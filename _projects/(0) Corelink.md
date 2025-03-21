---
name: Corelink
tools: [C++, Unreal, Godot]
image: /assets/corelink.png
description: Corelink is a real time low latency network framework that facilitates connecting users, data, and applications with computational resources.
---


# Corelink

Corelink is a real time low latency network framework that facilitates connecting users, data, and applications with computational resources. Corelink software is open source and licensed under the MIT license.

The Corelink distributed stream management platform allows users to define, send and receive real-time data in virtually any format - audio, motion capture, video, floorplans, maps, facial capture, virtual reality tracking, and many more.

For more information, click [here](https://corelink.hsrn.nyu.edu/)

<center>
<img src="/assets/corelink.png" width="759" height="501">
</center>



## Responsibilities

- Specializing in CoreLink Unreal Engine 5 and C++ Clients

- Working to improve and implement functionality, as well as rewrite existing documentation/writing new documentation

- Working on integrating the Corelink API into Unreal Engine 5

- Heading a prospective Godot Engine Corelink Client/Plugin, leading preliminary research and development.

- Collaborating with researchers and students and implementing desired changes, fixes, and functionality, as well as assisting API usage.


## Tasks

- Completely rewrote outdated C++ client documentation to represent most recent version of Corelink (DONE)

- Integrating Corelink API into UE5 (In Progress)

- Drafting plans for Godot Engine Client/Plugin (In Progress)


### Integrating Corelink API into UE5

Tasks:

Phase 1:

- Rewrite classes to be structured in an Unreal-compliant manner (STL Smart Pointers/Memory -> Unreal Smart Pointers/Memory, base class inheritance changes)
- Set up build files to compile Corelink API on runtime as DLL
 
Phase 2:

- Replace Unreal Engine netcode with Corelink for plug-and-play usage 
- Expose Corelink API to Blueprint for any required basic logic modifications

Phase 3:

- Document changes and draft usage documentation
- Develop a small example game using Corelink within UE5
- Publish plugin to UE marketplace


#### Plan for Replacing UE5 Netcode

- **Implement `UCorelinkSubsystem` (`UWorldSubsystem`)**
  - Create a `UWorldSubsystem` to manage Corelink globally, ensuring a centralized communication layer.
  - Handle game-related network data, such as movement, state synchronization, and player interactions.
  - Manages control channels and authentication.
  - Provides a centralized API for Corelink functionality.
- **Implement `UCorelinkNetDriver` to Replace Unreal’s Netcode**
  - Subclass `UIpNetDriver` to create `UCorelinkNetDriver`, integrating Corelink as Unreal’s primary networking layer.
  - Replace Unreal’s default `NetDriver` with Corelink in `DefaultEngine.ini`, allowing seamless multiplayer functionality.
  - Manages network channels.
  - Sends and receives Unreal network packets.
  - Does **not** handle authentication (relies on the subsystem).
  - Handles serialization/deserialization of Unreal network packets.
  - Manages network channels for Unreal replication and RPCs.
- **Expose to Blueprints** (Unreal Engine Visual Scripting)
  - Provide `UCorelinkReplicationComponent` as a Blueprint-enabled component for easy integration.
  - Enable developers to attach network synchronization to actors for position updates, animations, and other replicated properties without writing C++.

This basically makes it a plug-and-play swap between Unreal's default networking API and Corelink, so users would do almost the exact same thing they'd do for standard Unreal networking setups, and if any additional functionality is needed, they could implement it without needing to write code. They can also of course disable all of this and implement whatever they'd like on their own.
