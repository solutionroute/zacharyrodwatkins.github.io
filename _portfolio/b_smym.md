---
title: "Show Me Your Moves"
excerpt: "A Python API for creating Super Smash Bros. Melee AIs. <br/><img src='/images/melee.png'>"
collection: portfolio
---
 
<br/><img src='/images/melee.png'>

Show me Your Moves is a Python API for creating custom Super Smash Bros. AIs. 
We run Super Smash Bros. Melee in an emulated environment and read memory address directly from RAM. With the backend written in C++, we provide a Python API for reading various in-game parameters (e.g. health, player location, etc...), as well as inputting commands (e.g. jump, move, attack, etc...)
 
Checkout the source-code [here](https://github.com/zacharyrodwatkins/ShowMeYourMoves)

## Setup 

### Prerequisites

This project is based off of the dolphin emulator. You can install that [here](https://dolphin-emu.org/)

Also, huge shoutout to the [Dolphin Memory Engine](https://github.com/aldelaro5/Dolphin-memory-engine) repo. This tool lets you parse through dolphin's emulated RAM, and made finding all relavent memory adressesses possible. 

You also need a melee iso. Download the one we used [here](https://vimm.net/vault/7818), to make sure all adressesses actually correct.

Finally, to input commands we need to set up a fifo pipe. Instructions can be found [here](https://wiki.dolphin-emu.org/index.php?title=Pipe_Input). Make sure you create the pipe in ~/.local/share/dolphin-emu/Pipes 

### Using the API

There are two parts to the API: Command_Publisher and Memory_Watcher

#### Command_Publisher

Comman