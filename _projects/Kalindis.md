---
name: Kalindis
tools: [Godot, Git]
image: /assets/InstaEscape.png
description: Nahar's first large-scale project, taking 6 months and 10 collaborating members to complete. 
---

# Kalindis

In Kalindis, you play as a cheerful jellyfish trying to escape the sea floor! You only have access to one jump which replenishes once you collect a STAR. Collecting MOONS will freeze the floor in place for you! Jumping into a STICKY WALL gives you some breathing room, letting you reangle and time your next jump! Hitting a BARREL will replenish your jump but knock you back down! Climb as high as possible!

<center>
<video src="/assets/Kalindis Gameplay.mp4" width="640" height="360" controls></video>
</center>

Platform: Android | Engine: Godot | Duration: 6 months | Team Size: 8 | Roles: Game Programming, Game Design, Project Management

<center>
<video src="/assets/kalindismvmtprototype.mp4" width="640" height="360" controls></video>
</center>


## Responsibilities 

- Designed, prototyped, and implemented player movement/basic mechanics (death, jumping, collecting stars and moons), environment interactions (sticky walls), obstacles (barrels, death walls), and UI

- Calculated and displayed player trajectory with projectile motion calculations

- Designed and implemented a gradual difficulty scaling system 

- Implemented Finite State Machine (FSM) to handle player animations, SFX, and environment interaction logic

- Integrated assets from artists, sound engineers, and UI designers 

- Implemented fully functional leaderboard system

- Polished gamefeel by implementing screenshake, particle effects, and responsive controls

## What I learned

A lot of the criticism we received from this game revolved around UX-related issues, with the camera and the player controller being the main ones. The camera is always centered on the player rather than looking upwards, which makes it more difficult to anticipate where the next star would be, and the player joystick was always fixed on one place, rather than moving to wherever the player tapped. This restricted how the player could play and didn't make use of the screen space you get from touchscreen devices. We also opted for a text-based tutorial, an approach that made no sense in retrospect. 

Enough with the bad though, this was our first major project and my first where I really felt like I was a gameplay programmer rather than just a coder making jam games. I gained a lot of useful knowledge about game feel, scope, and polish which I otherwise wouldn't have learned. Although there were flaws to it, for our first project we did pretty good and I'm happy with what we managed to achieve.  


For more detailed and general thoughts on what we learned as a team from this project, have a look at the [post-mortem](https://barwani.eu.org/blog/kalindis-postmortem/)

