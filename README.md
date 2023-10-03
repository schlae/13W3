# 13W3 Video Snake Oil

Do you have an old workstation with a 13W3-style video connector? Are you frustrated with adapters that claim to convert this to a 15-pin analog VGA, but don't seem to work? Then this project is for you.

![Adapter with 13W3 on one side and VGA at the other](https://github.com/schlae/13W3/blob/main/13W3.jpg)

[Schematic](https://github.com/schlae/13W3/blob/main/13W3.pdf)

[Fab files](https://github.com/schlae/13W3/blob/main/fab/13W3_Rev1.zip)


Board dimensions: 52mm x 53.6mm. 2-layers, standard board thickness. Fabricate it using whatever soldermask and silkscreen colors you prefer.

## Assembly

Unless you need the composite sync separator, assembly is easy: just install J1 (a 13W3 connector, Mouser 636-685M13W3103L461 or equivalent) and J2 (a high-density 15-pin D shell connector, Mouser 636-181-015-213R561 or equivalent). Be warned that the 13W3 connector is quite expensive, so you may want to look at online auction sites or surplus sources.

You will need to solder some jumper wires to configure the 13W3 adapter for the particular workstation you are using. Ground pads are located next to the pads that are marked with the pin number that they are connected to. Using the table below, connect the pads to ground, leave them floating, or jumper them to the H (horizontal sync) or V (vertical sync) pads.

| 13W3 pin | Sun (old)          | Sun (DDC)        | RS/6000 [1]     | SGI (old) [2]    | SGI (DDC) [2]   |
|----------|--------------------|------------------|-----------------|------------------|-----------------|
| 1        | n/c (serial read)  | n/c (DDC SCL)    | Gnd (ID-2)      | n/c (id3)        | n/c (DDC SCL)   |
| 2        | n/c (Vsync) [3]    | n/c (DDC 5V in)  | n/c (ID-3)      | n/c (id0)        | n/c (DDC SDA)   |
| 3        | n/c (sense2) [6]   | n/c (sense2) [6] | Gnd (self test) | n/c (Csync)  [5] | n/c (Csync)     |
| 4        | Gnd (for sense)    | Gnd (for DDC)    | Gnd             | n/c (Hdrive) [5] | H               |
| 5        | H (Csync) [3]      | H (Csync) [4]    | H               | n/c (Vdrive) [5] | V               |
| 6        | n/c (Hsync) [3]    | n/c (DDC SDA)    | Gnd (ID-0)      | n/c (id1)        | n/c (DDC 5V in) |
| 7        | n/c (serial write) | n/c (Vsync) [4]  | n/c (ID-1)      | n/c (id2)        | Gnd (for DDC)   |
| 8        | n/c (sense1) [6]   | n/c (sense1) [6] | n/c             | Gnd              | Gnd             |
| 9        | n/c (sense0) [6]   | n/c (sense0) [6] | V               | Gnd              | Gnd             |
| 10       | Gnd (for sync)     | Gnd (for sync)   | Gnd             | Gnd              | Gnd             |

Notes:
1. The RS/6000 is wired for monitor ID 1010.
2. The SGI wiring connections have not been tested but should work.
3. Some video cards with Sun's old pinout lack Hsync and Vsync, so we connect Csync to the H pad, but see [§ Composite Sync](#composite-sync-and-sync-on-green) for more details.
4. All video cards with Sun's DDC pinout lack Hsync, so we connect Csync to the H pad, but see [§ Composite Sync](#composite-sync-and-sync-on-green) for more details.
5. Hdrive and Vdrive are not equivalent to Hsync and Vsync, so we connect Csync to the H pad, but see [§ Composite Sync](#composite-sync-and-sync-on-green) for more details.
6. Sense codes control the default resolution and refresh rate, unless DDC is supported and connected to your VGA monitor.
    * default sense code 000<sub>2</sub> is 1152x900@66
    * jump pins 3 and 9 to Gnd (101<sub>2</sub>) for 1280x1024@76
    * jump pin 8 to Gnd (010<sub>2</sub>) for 1024x768@60
    * for other sense codes, see [TurboGX/TurboGXplus Hardware Installation Guide](https://docs.oracle.com/cd/E19957-01/801-5399-10/801-5399-10.pdf), Table C-3
    * not all sense codes are supported by all video cards, see the Sun [Frame Buffer FAQ](http://www.sunhelp.org/faq/FrameBuffer.html#5)

Once you finish the wiring, it's a good idea to mark the board with the name of the system it is wired for. The space underneath the silkscreen text "FOR BEST RESULTS USE WITH:" is provided for this purpose.

If you figure out the wiring for a system not in this table, feel free to file a bug report or submit a pull request.

### Composite Sync and Sync-On-Green

Some workstations output composite sync (Csync) or sync-on-green but not separate sync (Hsync and Vsync), such as the SPARCstation 5 with TurboGX.

Many modern LCD monitors already support one or both of these. In the case of sync-on-green, you may not need to wire up any sync signals at all, whereas for composite sync, it may be enough to connect your Csync pin to the H pad.

If yours doesn't support those, then you'll need to install these components:

| Q | Designator | Description | Mouser part number |
|---|------------|-------------|--------------------|
| 2 | C1, C3     | 0.1uF X5R 50V capacitor, 0603 | |
| 1 | C2 | 560pF C0G 50V capacitor, 0603 | |
| 1 | R1 | 100 ohm 5% 0603 resistor | |
| 1 | R2 | 10.0K ohm 1% 0603 resistor | |
| 1 | U1 | LMH1980 sync separator | 926-LMH1980MM/NOPB |

To separate sync-on-green, install a jumper wire across JP11. For composite sync, run a wire from the JP11 pad nearest R1 (marked CSYNC) to the appropriate pin on the 13W3 connector. Be sure to disconnect your Csync pin from the H pad, if you connected it earlier.

For this circuit to work, it needs 5V power, provided by J5 on the PCB (marked "5V GND"). If your workstation does not provide 5V on the 13W3 connector, you'll need to get it from somewhere else (keyboard port, external DC power supply, etc).
