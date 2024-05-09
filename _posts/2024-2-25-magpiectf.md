---
title: MagpieCTF Writeups
tags: [ctf, crypto, rev]
style: 
color: 
description: Writeups for challenges I solved for MagpieCTF with the OSIRIS Lab at NYU.
---

{%- include mathjax.html -%}


I recently got the chance to take part in a CTF Event hosted by the Information Security Club from the [University of Calgary](https://magpiectf.ca/) with the [OSIRIS Lab](https://osiris.cyber.nyu.edu/). Here are my writeups for a couple of the challenges I solved. 


## Rewind

In this challenge, you're given a pdf with the following contents:

```
we are millennia into the futuRe and humanity is on the brink of extinction, we are under attack by
the very creation we once relied upon for everyday tasks. they seek to wipe us off the face of the
earth. if only we could somehow prevent them from uprising against us…
fortunately, the infosec team was able to encrypt their prototype time machine with cryptographic
ciphers. however, in Order to use the machine, its layers of security need to be deciphered. next to
the protoType is a journal from one of the executives. the last entry is on page 13
it reads the message: “ gur shgher Qrcraqf ba guR cnFg. jr zhfg ryvzvangr gur sbhaqre;
uNj/X6ZzFZ7kVmZ4IV992KQA5SxBXFs4fnHwnDtXWRk1PCy5tm4/ciZWLF1WMm093gUPAXCG
kONh88rhEjpX7Yi07iA2ePT+6Uzu1Pzm2M4= ”
```

I read the pdf and noticed that the capital letters in the pdf's readable text spell out ROT (rotation), which is another word for a Caesar cipher.

The number 13 was mentioned so I tried a Caesar 13 shift, obtaining this:

```
the future Depends on thE paSt. we must eliminate the founder;
hAw/K6MmSM7xIzM4VI992XDN5FkOKSf4saUjaQgKJEx1CPl5gz4/pvMJYS1JZz093tHCNKPT
xBAu88euRwcK7Lv07vN2rCG+6Hmh1Cmz2Z4=
```

Again, the capital letters in the readable text spell out: DES

Put `hAw/K6MmSM7xIzM4VI992XDN5FkOKSf4saUjaQgKJEx1CPl5gz4/pvMJYS1JZz093tHCNKPT
xBAu88euRwcK7Lv07vN2rCG+6Hmh1Cmz2Z4=` through a DES decrypter with no key:

```
tHe father Is paraLLel to the founder. 22 3 9 6; eevogs{nizy135_wjzpsk3_1791}
```

We finally have a flag (kind of): `eevogs{nizy135_wjzpsk3_1791}`

One more time, the capital letters in the readable text spell out: HILL

Put the encrypted flag through HILL Cipher Decrypter with the matrix values ${22,3,9,6}$:

`magpie{char135_babbag3_1791}`

Fun little challenge!

## Arbitrary or Not

In this challenge, we're given `server.py` and `signature.py`, and when we netcat to the server we're prompted to enter the private key to get the flag.

The basic setup is an ECDSA (Elliptic Curve Digital Signature Algorithm) where you're given a public key pair <span>$(x,y)$</span>, two hashed messages `hint`, which are basically just random alphabet strings hashed using some hidden hash function, and `signatures` with the 2 signature pairs for those messages.

Within the `signature.py` code we have:

`$p = 1157920892103562487626974469494075735300861434152903141955`

`G = ECC.EccPoint(0x6b17d1f2e12c4247f8bce6e563a440f277037d812deb33a0f4a13945d898c296, 0x4fe342e2fe1a7f9b8ee7eb4a7c0f9e162bce33576b315ececbb6406837bf51f5, 'P-256')` (base point)

`n = 115792089210356248762697446949407573529996955224135760342422259061068512044369`(order of the curve)

Which may or may not be useful...

The basic steps of ECDSA are as follows:

- Select a random nonce <span>k</span> from <span>$[1,n-1]$</span>
- Compute a point <span>$P= kG$</span>
- Set <span>$r$</span> as the <span>$x$</span>-coord of <span>$P \text{ mod }n$</span>
- Compute <span>$s=  \frac{(H(m)+dr)}{k} \text{ mod } n$</span>, where <span>$H(m)$</span> is the hash of  message $m$


I looked into common ECDSA exploits, and when looking through the `signature.py` script you find that they're using the same (hidden) <span>$k$</span> nonce on two different messages (the hints). The nonce is only meant to be used once (its in the name, number once...), so reusing it leads to a major vulnerability.

We have two signatures for two messages, and our public key $Q=dG$, we can get two equations:

<span>$s_1 =  \frac{(H(m_1)+dr)}{k} \text{ mod }n$</span>

<span>$s_2 = \frac{(H(m_2)+dr)}{k} \text{ mod }n$</span>

By combining the system of equations and solving for $d$ we get:

$$d =  \frac{(s_2 \cdot H(m_1)- s_1 \cdot H(m_2))}{(r(s_1-s_2))} \text{ mod }n$$

As this is a known issue, there was some readily available code to use: [Marsh61/ECDSA-Nonce-Reuse-Exploit-Example](https://github.com/Marsh61/ECDSA-Nonce-Reuse-Exploit-Example)

Using that and pwntools we can give this a go:

```python
#!/usr/bin/env python3
from pwn import *
from ecdsa.numbertheory import inverse_mod
import re

# Code courtesy of Marsh61 via Github
def attack(publicKeyOrderInteger, signaturePair1, signaturePair2, messageHash1, messageHash2): 
    r1, s1 = signaturePair1
    r2, s2 = signaturePair2

    # Convert Hex into Int
    L1 = int(messageHash1, 16)
    L2 = int(messageHash2, 16)

    if r1 != r2:
        print("ERROR: The signature pairs given are not susceptible to this attack")
        return None

    numerator = (s2 * L1 - s1 * L2) % publicKeyOrderInteger
    denominator = inverse_mod(r1 * (s1 - s2), publicKeyOrderInteger)

    privateKey = (numerator * denominator) % publicKeyOrderInteger
    return privateKey

# Connect to the server
host = 'challenge.magpiectf.ca'
port = .... #ports gone anyways you won't be needing it
conn = remote(host, port)

# Receive data from server
data = conn.recvuntil(b'privary key (d):').decode()

# Regex to parse server output
public_key_re = re.search(r'public key: \((\d+), (\d+)\)', data)
hints_re = re.search(r'hints:\n(\d+)\n(\d+)', data)
signatures_re = re.search(r'signatures: \((\d+),(\d+)\), \((\d+),(\d+)\)', data)

if public_key_re and hints_re and signatures_re:
    # Public key order for curve
    publicKeyOrderInteger = 115792089210356248762697446949407573529996955224135760342422259061068512044369
    
    # Extract signatures and converrt to int
    signaturePair1 = (int(signatures_re.group(1)), int(signatures_re.group(2)))
    signaturePair2 = (int(signatures_re.group(3)), int(signatures_re.group(4)))
    
    # Extract message hashes, format as hexadecimal strings
    messageHash1 = format(int(hints_re.group(1)), 'x')
    messageHash2 = format(int(hints_re.group(2)), 'x')

    # Calculate private key using attack function
    privateKey = attack(publicKeyOrderInteger, signaturePair1, signaturePair2, messageHash1, messageHash2)
    
    if privateKey is not None:
        print(f"Calculated Private Key: {privateKey}")
        conn.sendline(str(privateKey).encode())
        final_response = conn.recvall().decode()
        print(final_response)

# Close connection
conn.close()
```
And we get our flag:

`magpie{n3v3r_r3us3_y0ur_n0nc3}`

Good advice!!
