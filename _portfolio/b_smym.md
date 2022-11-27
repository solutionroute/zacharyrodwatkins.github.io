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

There are two parts to the API: Command_Publisher and MemoryWatcher

#### Command_Publisher

This is simply a python wrapper around making system calls echoing to the fifo pipe. It provides functionality like simulating moving joysticks, presssing buttons, etc...

As an example, the below code snipping sets the joystick position, and pressed the 'B' button.
'''
import Command_Publisher

Move_Stick('0.5','0.5')
Press_Button('B')
''' 

#### MemoryWatcher 

Now this is really the core of this project. Once you know the adress and the data-type of an in game variable, MemoryWatcher lets you read that variable into your python script.

For instance, say you knew that the x-position of your character was represented as a float, and held at adress 0x80453090. You could then retrive that information in your python script as follows:

'''
p1x_add = 0x80453090

p1=MemoryWatcher(p1x_add,'f')

while(p1.getStatus()=='hooked'):

    p1x = float(p1.getMemory())
    print(p1x)
'''

With these two modules, the hope is that you could get as creative as you want. At the absolute simplest, check out the following script.

'''
#!/usr/bin/python3
from Command_Publisher import *
import sys
sys.path.insert(0, 'src/Our-Dolphin/Source/build')
from DolphinMemoryEngine import MemoryWatcher

p1x_add = 0x80453090
p2x_add = 0x80453F20

p1=MemoryWatcher(p1x_add,'f')
p2=MemoryWatcher(p2x_add, 'f')

while(p1.getStatus()=='hooked'):

    p1x = float(p1.getMemory())
    p2x = float(p2.getMemory())
 
    if abs(p1x-p2x)<10:
        Move_Stick('0.5','0.5')
        Press_Button('B') # attack!

    else:
        x_stick = 1 if p2x<p1x else 0
        x_stick = str(x_stick) 
        Move_Stick(x_stick, str(0.5)) # move towards the opponent

'''