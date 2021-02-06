# PI Pico - how to unbrick from infinite loop in python

## The problem

As a genius in programming I was interrested in how PI Pico will deal with infinite loop of some math (may to be hot?), so I made a "code" with `While True :`. Right after I press "Run" in Thonny, it saves program into memory, so a second after Pico **disappear** from device list. BOOM.

You can put any new .uf2 sketch and it will works fine. You can start in BOOTSEL mode, but here is no "wrong" file - just initial files with help, which doesn't help much. As soon as you put python' .uf2 back (say instead of blink), it returns to infinite loop.

This wasn't my plan.

## The Idea

Well, if Pico works with **any** other program, the thing is in code of python interpretator, which runs `main.py` right after start. This file doesn't exists in "file" folder, so here have to be other one - probably virtual - disk with files visible from Thonny (which can't be used now as soon as Pico cannot communicate via virtual comport).

So how does Pico stores files for python?

## Tools

1. Go to [picotool](https://github.com/raspberrypi/picotool) and install it (you can find detailed instruction in [Pico books](https://www.raspberrypi.org/documentation/pico/getting-started/))
2. Find what HEX-editor is suitable for you, in my case it is "[Sublime Text](https://www.sublimetext.com)" and viewer (in my case MC)
3. A terminal window

## How to

1. Put python' .uf2 back to Pico using BOOTSEL mode. Pico will restart and hangs, but in our case it is OK
2. Reboot Pico in BOOTSEL mode again to operate with picotool
3. Use `picotool info -a` and find address info of `embedded drive`, in my case `embedded drive:  0x100a0000-0x10200000 (1408K): MicroPython`
4. Lets dump it using `picotool save -r 0x100a0000 0x10200000 ~/Desktop/pico.bin` this will give us 1441792 bytes file
5. Make copy: `cp ~/Desktop/pico.bin ~/Desktop/pico.bak` just for a case
6. Open .bin file in HEX viewer to be sure you see something like 6C69 7474 6C65 6673 2FE0 (which is `littlefs/` [btw](https://github.com/littlefs-project/littlefs))
7. Scroll through file to find **your wrong** code
8. Simple edit your code, may be overwriting first letters of each row with hash (# is 23 in HEX) OR do as I did: put FF instead whole code
9. Save binary file and check its size (must be same!)
10. Now it is time to return file to Pico: `picotool load ~/Desktop/pico.bin -t bin -o 0x100a0000`
11. Reboot: `picotool reboot`

## It was fun

Well, I make it brick right in 11th minute of the very first try. I tried to find a solution in internet but wasn't succeed. So I've documented what found in next 40 minutes. It was FUN!

## The ticket

[Posted here](https://github.com/micropython/micropython/issues/6853)
