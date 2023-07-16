# 13W3 Video Snake Oil

Do you have an old workstation with a 13W3-style video connector? Are you frustrated with adapters that claim to convert this to a 15-pin analog VGA, but don't seem to work? Then this project is for you.

![Adapter with 13W3 on one side and VGA at the other](https://github.com/schlae/13W3/blob/main/13W3.jpg)

[Schematic](https://github.com/schlae/13W3/blob/main/13W3.pdf)

[Fab files](https://github.com/schlae/13W3/blob/main/fab/13W3_Rev1.zip)


Board dimensions: 52mm x 53.6mm. 2-layers, standard board thickness. Fabricate it using whatever soldermask and silkscreen colors you prefer.

## Assembly

Unless you need the composite sync separator, assembly is easy: just install J1 (a 13W3 connector, Mouser 636-685M13W3103L461 or equivalent) and J2 (a high-density 15-pin D shell connector, Mouser 636-181-015-213R561 or equivalent). Be warned that the 13W3 connector is quite expensive, so you may want to look at online auction sites or surplus sources.

You will need to solder some jumper wires to configure the 13W3 adapter for the particular workstation you are using. Ground pads are located next to the pads that are marked with the pin number that they are connected to. Using the table below, connect the pads to ground, leave them floating, or jumper them to the H (horizontal sync) or V (vertical sync) pads.

| 13W3 pin | Sun SPARCstation | RS/6000 [1] | SGI [2]     |
|----------|--------------|-----------------|-------------|
| 1        | n/c (ser rd) | Gnd (ID-2)      | n/c (mon3)  |
| 2        | V            | n/c (ID-3)      | n/c (mon2)  |
| 3        | n/c (sense0) | Gnd (self test) | n/c (Csync) |
| 4        | Gnd          | Gnd             | H           |
| 5        | n/c (Csync)  | H               | V           |
| 6        | H            | Gnd (ID-0)      | n/c (mon1)  |
| 7        | n/c (ser wr) | n/c (ID-1)      | n/c (mon2)  |
| 8        | n/c (sense1) | n/c             | Gnd         |
| 9        | n/c (sense2) | V               | Gnd         |
| 10       | GND          | Gnd             | Gnd         |

Notes:
1. The RS/6000 is wired for monitor ID 1010.
2. The SGI wiring connections have not been tested but should work.

Once you finish the wiring, it's a good idea to mark the board with the name of the system it is wired for. The space underneath the silkscreen text "FOR BEST RESULTS USE WITH:" is provided for this purpose.

If you figure out the wiring for a system not in this table, feel free to file a bug report or submit a pull request.

### Composite Sync and Sync-On-Green

Some workstations output composite sync or sync-on-green. Many modern LCD monitors already support sync-on-green, so you may not need to wire up any sync signals at all. If yours doesn't support it, or if the workstation outputs a separate composite sync signal, then you'll need to install these components:

| Q | Designator | Description | Mouser part number |
|---|------------|-------------|--------------------|
| 2 | C1, C3     | 0.1uF X5R 50V capacitor, 0603 | |
| 1 | C2 | 560pF C0G 50V capacitor, 0603 | |
| 1 | R1 | 100 ohm 5% 0603 resistor | |
| 1 | R2 | 10.0K ohm 1% 0603 resistor | |
| 1 | U1 | LMH1980 sync separator | 926-LMH1980MM/NOPB |

To separate sync-on-green, install a jumper wire across JP11. For composite sync, run a wire from the JP11 pad nearest R1 (marked CSYNC) to the appropriate pin on the 13W3 connector.

For this circuit to work, it needs 5V power, provided by J5 on the PCB (marked "5V GND"). If your workstation does not provide 5V on the 13W3 connector, you'll need to get it from somewhere else (keyboard port, external DC power supply, etc).
