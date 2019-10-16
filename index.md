# ALERT

**THIS DOCUMENT IS ONLY THE GBA PORTION OF GBATEK**

* The [full markdown fork](https://mgba-emu.github.io/gbatek/) has the entire document in markdown form.
* The [full original version](https://problemkaputt.de/gbatek.htm) is in HTML form.

I started with the full markdown and then just deleted sections that were
obviously not needed for GBA development. The goal is to make a smaller document
that's easier for browsers to render and search through. There's diminishing
returns, so mostly I just cut the DS and DSi stuff and called it good.

[PRs welcome](https://github.com/rust-console/gbatek-gbaonly/pulls) if you want
to improve this document further.

<a name="contents"></a><a name="gbatek"></a>
# GBATEK

Gameboy Advance Technical Info - Extracted from no$gba version 2.9b

[About this Document](#aboutthisdocument)

## GBA Reference

### Overview

- [GBA Technical Data](#gbatechnicaldata)
- [GBA Memory Map](#gbamemorymap)
- [GBA I/O Map](#gbaiomap)

### Hardware Programming

- [GBA LCD Video Controller](#gbalcdvideocontroller)
- [GBA Sound Controller](#gbasoundcontroller)
- [GBA Timers](#gbatimers)
- [GBA DMA Transfers](#gbadmatransfers)
- [GBA Communication Ports](#gbacommunicationports)
- [GBA Keypad Input](#gbakeypadinput)
- [GBA Interrupt Control](#gbainterruptcontrol)
- [GBA System Control](#gbasystemcontrol)
- [GBA Cartridges](#gbacartridges)
- [GBA Unpredictable Things](#gbaunpredictablethings)

### Other

- [ARM CPU Reference](#armcpureference)
- [BIOS Functions](#biosfunctions)
- [External Connectors](#externalconnectors)

## CPU Reference

### General ARM7TDMI Information

- [ARM CPU Overview](#armcpuoverview)
- [ARM CPU Register Set](#armcpuregisterset)
- [ARM CPU Flags & Condition Field (cond)](#armcpuflagsconditionfieldcond)
- [ARM CPU 26bit Memory Interface](#armcpu26bitmemoryinterface)
- [ARM CPU Exceptions](#armcpuexceptions)
- [ARM CPU Memory Alignments](#armcpumemoryalignments)

### Further Information

- [ARM Pseudo Instructions and Directives](#armpseudoinstructionsanddirectives)
- [ARM CP15 System Control Coprocessor](#armcp15systemcontrolcoprocessor)
- [ARM CPU Instruction Cycle Times](#armcpuinstructioncycletimes)
- [ARM CPU Versions](#armcpuversions)
- [ARM CPU Data Sheet](#armcpudatasheet)

## CPU 32bit ARM Mode

### ARM 32bit Opcodes (ARM Code)

- [ARM Instruction Summary](#arminstructionsummary)
- [ARM Branch and Branch with Link (B, BL, BX, BLX, SWI, BKPT)](#armopcodesbranchandbranchwithlinkbblbxblxswibkpt)
- [ARM Data Processing (ALU)](#armopcodesdataprocessingalu)
- [ARM Multiply and Multiply-Accumulate (MUL, MLA)](#armopcodesmultiplyandmultiplyaccumulatemulmla)
- [ARM Special ARM9 Instructions (CLZ, QADD/QSUB)](#armopcodesspecialarm9instructionsclzqaddqsub)
- [ARM PSR Transfer (MRS, MSR)](#armopcodespsrtransfermrsmsr)
- [ARM Memory: Single Data Transfer (LDR, STR, PLD)](#armopcodesmemorysingledatatransferldrstrpld)
- [ARM Memory: Halfword, Doubleword, and Signed Data Transfer](#armopcodesmemoryhalfworddoublewordandsigneddatatransfer)
- [ARM Memory: Block Data Transfer (LDM, STM)](#armopcodesmemoryblockdatatransferldmstm)
- [ARM Memory: Single Data Swap (SWP)](#armopcodesmemorysingledataswapswp)
- [ARM Coprocessor (MRC/MCR, LDC/STC, CDP, MCRR/MRRC)](#armopcodescoprocessorinstructionsmrcmcrldcstccdpmcrrmrrc)

## CPU 16bit THUMB Mode

### ARM 16bit Opcodes (THUMB Code)

- When operating in THUMB state, cut-down 16bit opcodes are used.
- THUMB is supported on T-variants of ARMv4 and up, ie. ARMv4T, ARMv5T, etc.
- [THUMB Instruction Summary](#thumbinstructionsummary)
- [THUMB Register Operations (ALU, BX)](#thumbopcodesregisteroperationsalubx)
- [THUMB Memory Load/Store (LDR/STR)](#thumbopcodesmemoryloadstoreldrstr)
- [THUMB Memory Addressing (ADD PC/SP)](#thumbopcodesmemoryaddressingaddpcsp)
- [THUMB Memory Multiple Load/Store (PUSH/POP and LDM/STM)](#thumbopcodesmemorymultipleloadstorepushpopandldmstm)
- [THUMB Jumps and Calls](#thumbopcodesjumpsandcalls)

## <a name="gbareference"></a>  GBA Reference

### Overview

- [GBA Technical Data](#gbatechnicaldata)
- [GBA Memory Map](#gbamemorymap)
- [GBA I/O Map](#gbaiomap)

### Hardware Programming

- [GBA LCD Video Controller](#gbalcdvideocontroller)
- [GBA Sound Controller](#gbasoundcontroller)
- [GBA Timers](#gbatimers)
- [GBA DMA Transfers](#gbadmatransfers)
- [GBA Communication Ports](#gbacommunicationports)
- [GBA Keypad Input](#gbakeypadinput)
- [GBA Interrupt Control](#gbainterruptcontrol)
- [GBA System Control](#gbasystemcontrol)
- [GBA Cartridges](#gbacartridges)
- [GBA Unpredictable Things](#gbaunpredictablethings)

### Other

- [ARM CPU Reference](#armcpureference)
- [BIOS Functions](#biosfunctions)
- [External Connectors](#externalconnectors)

## <a name="gbatechnicaldata"></a>  GBA Technical Data

### CPU Modes

```
  ARM Mode     ARM7TDMI 32bit RISC CPU, 16.78MHz, 32bit opcodes (GBA)
  THUMB Mode   ARM7TDMI 32bit RISC CPU, 16.78MHz, 16bit opcodes (GBA)
  CGB Mode     Z80/8080-style 8bit CPU, 4.2MHz or 8.4MHz  (CGB compatibility)
  DMG Mode     Z80/8080-style 8bit CPU, 4.2MHz (monochrome gameboy compatib.)
```

### Internal Memory

```
  BIOS ROM     16 KBytes
  Work RAM     288 KBytes (Fast 32K on-chip, plus Slow 256K on-board)
  VRAM         96 KBytes
  OAM          1 KByte (128 OBJs 3x16bit, 32 OBJ-Rotation/Scalings 4x16bit)
  Palette RAM  1 KByte (256 BG colors, 256 OBJ colors)
```

### Video

```
  Display      240x160 pixels (2.9 inch TFT color LCD display)
  BG layers    4 background layers
  BG types     Tile/map based, or Bitmap based
  BG colors    256 colors, or 16 colors/16 palettes, or 32768 colors
  OBJ colors   256 colors, or 16 colors/16 palettes
  OBJ size     12 types (in range 8x8 up to 64x64 dots)
  OBJs/Screen  max. 128 OBJs of any size (up to 64x64 dots each)
  OBJs/Line    max. 128 OBJs of 8x8 dots size (under best circumstances)
  Priorities   OBJ/OBJ: 0-127, OBJ/BG: 0-3, BG/BG: 0-3
  Effects      Rotation/Scaling, alpha blending, fade-in/out, mosaic, window
  Backlight    GBA SP only (optionally by light on/off toggle button)
```

### Sound

```
  Analogue     4 channel CGB compatible (3x square wave, 1x noise)
  Digital      2 DMA sound channels
  Output       Built-in speaker (mono), or headphones socket (stereo)
```

### Controls

```
  Gamepad      4 Direction Keys, 6 Buttons
```

### Communication Ports

```
  Serial Port  Various transfer modes, 4-Player Link, Single Game Pak play
```

### External Memory

```
  GBA Game Pak max. 32MB ROM or flash ROM + max 64K SRAM
  CGB Game Pak max. 32KB ROM + 8KB SRAM (more memory requires banking)
```

### Case Dimensions

```
  Size (mm)    GBA: 145x81x25 - GBA SP: 82x82x24 (closed), 155x82x24 (stretch)
```

### Power Supply

```
  Battery GBA  GBA: 2x1.5V DC (AA), Life-time approx. 15 hours
  Battery SP   GBA SP: Built-in rechargeable Lithium ion battery, 3.7V 600mAh
  External     GBA: 3.3V DC 350mA - GBA SP: 5.2V DC 320mA
```

- ----------------------------------------------------------------------------

### Original Gameboy Advance (GBA)

```
        ____._____________...___.____
   ____/    :  CARTRIDGE  SIO   :    \____
  | L       _____________________  LED  R |
  |        |                     |        |
  |  _||_  |   2.9" TFT SCREEN   |    (A) |
  | |_  _| | 240x160pix  61x40mm | (B)    |
  |   ||   |    NO BACKLIGHT     |  ::::  |
  |        |                     | SPEAKR |
  | STRT() |_____________________|  ::::  |
  | SLCT()     GAME BOY ADVANCE    VOLUME |
  |____  OFF-ON  BATTERY 2xAA PHONES  _==_|
       \__.##.__________________,,___/
```

### GBA SP (GBA SP)

```
   _______________________                                 _
  | _____________________ |                               / /
  ||                     ||                              / /
  ||   2.9" TFT SCREEN   ||                             / /
  || 240x160pix  61x40mm ||                            / /
  ||   WITH BACKLIGHT    ||                           / /
  ||                     ||     GBA SP SIDE VIEWS    / /
  ||_____________________||                         / /
  |  GAME BOY ADVANCE SP  |   _____________________(_)
  |_______________________|  |. . . . . . . .'.'.   _|
  |_|________|________|_|_|  |_CARTRIDGE_:_BATT._:_|_| <-- EXT1/EXT2
  |L    EXT1     EXT2    R|
  |          (*)      LEDSo   _____________________ _
  (VOL_||_           (A)  o  |_____________________(_)
  |  |_  _| ,,,,,(B)      |  |. . . . . . . .'.'.   _|
  |    ||   ;SPK;         |  |_CARTRIDGE_:_BATT._:_|_| <-- EXT1/EXT2
  |         '''''      ON #                         _ _____________________
  |       SLCT STRT    OFF#   _____________________(_)_____________________|
  | CART.  ()   ()        |  |. . . . . . . .'.'.   _|
  |_:___________________:_|  |_CARTRIDGE_:_BATT._:_|_| <-- EXT1/EXT2
```

### Gameboy Micro (GBA Micro)

```
      ________________SIO_______________
     | L      __________________      R |
     |       |     GBA-MICRO    |       |
     | _||_  |  2.0" TFT SCREEN |    (A)| +
     ||_  _| |240x160pix 42x28mm| (B)   |VOL
     |  ||   |     BACKLIGHT    |       | -
     |       |__________________|  ...  |
     |___________SELECT__START__________|
       PWR   <--- CARTRIDGE SLOT ---> PHONES
```

### Nintendo DS (NDS)

```
     _____________________________________
    |        _____________________        |
    |       |                     |       |
    |       |    3" TFT SCREEN    |       |
    |       | 256x192pix  61x46mm |       |
    |       |      BACKLIGHT      |       |
    | ::::: |    Original NDS     | ::::: |
    | ::::: |_____________________| ::::: |
   _|        _          ______   _        |_  <-- gap between screens: 22mm
  |L|_______| |________|      |_| |_______|R|     (equivalent to 90 pixels)
  |_______   _____________________   _______|
  |  PWR  | |                     | |SEL STA|
  |   _   | |    3" TFT SCREEN    | |       |
  | _| |_ | | 256x192pix  61x46mm | |   X   |
  ||_   _|| |      BACKLIGHT      | | Y   A |
  |  |_|  | |    TOUCH SCREEN     | |   B   |
  |       | |_____________________| |       |
  |_______|             NintendoDS  |_______|
  |         MIC                LEDS         |
  |_________________________________________|
       VOL        SLOT2(GBA)     MIC/PHONES
```

### Nintendo DS Lite (NDS-Lite)

```
     _____________________________________
    |        _____________________        |
    |       |                     |       |
    |       |    3" TFT SCREEN    |       |
    |  ...  | 256x192pix  61x46mm |  ...  |
    |  ...  |      BACKLIGHT      |  ...  |
    |       |      NDS-LITE       |       |
    |       |_____________________|       |
    |___  _ _ _ _ _ _ _ _ _ _ _ _ _ _ ____|   <-- gap between screens: 23mm
   L| _ |_____________MIC____________|LEDS|R
    |   _    _____________________        |
    | _| |_ |                     |   X   |
    ||_   _||    3" TFT SCREEN    | Y   A |PWR
    |  |_|  | 256x192pix  61x46mm |   B   |
    |       |      BACKLIGHT      |       |
    |       |    TOUCH SCREEN     |oSTART |
    |       |_____________________|oSELECT|
    |_____________________________________|
       VOL        SLOT2(GBA)     MIC/PHONES
```

### Nintendo DSi (DSi)

```
     _____________________________________
    |        _____________________        |
    |       |                     |   O o | <-- CAM (O) and LED (o)
    |       |   3.25" TFT SCREEN  |       |     (on backside)
    |       | 256x192pix  66x50mm |       |
    |       |      BACKLIGHT      |       |
    |  __   |         DSi         |   __  |
    | (__)  |_____________________|  (__) |
    |___  _ _ _ _ _ _ _ _ _ _ _ _ _ _ ____|  <-- gap between screens: 23mm
   L|LEDS|__________CAM__MIC_________| __ |R                   (88 pixels)
  + |   _    _____________________        |
 VOL| _| |_ |                     |   X   | <-- SD Card Slot
  - ||_   _||   3.25" TFT SCREEN  | Y   A |
    |  |_|  | 256x192pix  66x50mm |   B   |
    |       |      BACKLIGHT      |       |
    |       |    TOUCH SCREEN     |oSTART |
    | POWERo|_____________________|oSELECT|
    |_____________________________________|
                                 MIC/PHONES
```

### Nintendo DSi XL

```
  As DSi, but bigger case, and bigger 4.2" screens
```

### Gameboy Player (Gamecube Joypad) (GBA Player)

```
                   _________
       L____-------         -------____R
       /   ___   \           /   (Y)   \Z
      /   / O \   | (START) |        (X)\   Z      = Gameboy Player Menu
     |    \___/    \_______/      (A)    |  X or Y = Select button
     |\         _   \     /    (B)      /|
     | \___   _| |_  \   /   ___    ___/ |  optionally X/Y can be
     |    |\ |_   _| /   \  / C \  /|    |  swapped with L/R (?)
     |    | \  |_|  /     \ \___/ / |    |
     |    |  \_____/       \_____/  |    |  analogue sticks = ?
      \__/                           \__/
```

### Gameboy Player (Gamecube Bongos) (GBA Player)

```
       _______     _______
      /   Y   \   /   X   \   Y/B = left bongo rear/front side
     | . . . . |_| . . . . |  X/A = right bongo rear/front side
     |    B    |R|    A    |  S   = start/pause button
     |\_______/|_|\_______/|  R   = microphone (triggers R button)
     |\_______/|S|\_______/|
     |         |_|         |  (the X/Y inputs can be assigned to
     |\_______/| |\_______/|  GBA R/L inputs in GBA player setup)
      \_______/   \_______/
```

The GBA's separate 8bit/32bit CPU modes cannot be operated simultaneously.
Switching is allowed between ARM and THUMB modes only (that are the two GBA
modes).

This manual does not describe CGB and DMG modes, both are completely different
than GBA modes, and both cannot be accessed from inside of GBA modes anyways.

### Gameboy Player

An GBA Adapter for the Gamecube console; allowing to play GBA games on a
television set.

- [GBA Gameboy Player](#gbagameboyplayer)

### GBA SP Notes

Deluxe version of the original GBA. With backlight, new folded laptop-style
case, and built-in rechargeable battery. Appears to be 100% compatible with
GBA, there seems to be no way to detect SPs by software.

### Gameboy Micro (GBA Micro)

Minituarized GBA. Supports 32bit GBA games only (no 8bit DMG/CGB games). The
256K Main RAM is a bit slower than usually (cannot be "overclocked via port
4000800h).

### Nintendo DS (Dual Screen) Notes

New handheld with two screens, backwards compatible with GBA games, it is NOT
backwards compatible with older 8bit games (mono/color gameboys) though..

Also, the DS has no link port, so that GBA games will thus work only in single
player mode, link-port accessoires like printers cannot be used, and most
unfortunately multiboot won't work (trying to press Select+Start at powerup
will just lock up the DS).

### iQue Notes

iQue is a brand name used by Nintendo in China, iQue GBA and iQue DS are
essentially same as Nintendo GBA and Nintendo DS.

The iQue DS contains a larger firmware chip (the charset additionally contains
about 6700 simplified chinese characters), the bootmenu still allows to select
(only) six languages (japanese has been replaced by chinese). The iQue DS can
play normal international NDS games, plus chinese dedicated games. The latter
ones won't work on normal NDS consoles (that, reportedly simply due to a
firmware-version check contained in chinese dedicated games, aside from that
check, the games should be fully compatible with NDS consoles).

## <a name="gbamemorymap"></a>  GBA Memory Map

### General Internal Memory

```
  00000000-00003FFF   BIOS - System ROM         (16 KBytes)
  00004000-01FFFFFF   Not used
  02000000-0203FFFF   WRAM - On-board Work RAM  (256 KBytes) 2 Wait
  02040000-02FFFFFF   Not used
  03000000-03007FFF   WRAM - On-chip Work RAM   (32 KBytes)
  03008000-03FFFFFF   Not used
  04000000-040003FE   I/O Registers
  04000400-04FFFFFF   Not used
```

### Internal Display Memory

```
  05000000-050003FF   BG/OBJ Palette RAM        (1 Kbyte)
  05000400-05FFFFFF   Not used
  06000000-06017FFF   VRAM - Video RAM          (96 KBytes)
  06018000-06FFFFFF   Not used
  07000000-070003FF   OAM - OBJ Attributes      (1 Kbyte)
  07000400-07FFFFFF   Not used
```

### External Memory (Game Pak)

```
  08000000-09FFFFFF   Game Pak ROM/FlashROM (max 32MB) - Wait State 0
  0A000000-0BFFFFFF   Game Pak ROM/FlashROM (max 32MB) - Wait State 1
  0C000000-0DFFFFFF   Game Pak ROM/FlashROM (max 32MB) - Wait State 2
  0E000000-0E00FFFF   Game Pak SRAM    (max 64 KBytes) - 8bit Bus width
  0E010000-0FFFFFFF   Not used
```

### Unused Memory Area

```
  10000000-FFFFFFFF   Not used (upper 4bits of address bus unused)
```

### Default WRAM Usage

By default, the 256 bytes at 03007F00h-03007FFFh in Work RAM are reserved for
Interrupt vector, Interrupt Stack, and BIOS Call Stack. The remaining WRAM is
free for whatever use (including User Stack, which is initially located at
03007F00h).

### Address Bus Width and CPU Read/Write Access Widths

Shows the Bus-Width, supported read and write widths, and the clock cycles for
8/16/32bit accesses.

```
  Region        Bus   Read      Write     Cycles
  BIOS ROM      32    8/16/32   -         1/1/1
  Work RAM 32K  32    8/16/32   8/16/32   1/1/1
  I/O           32    8/16/32   8/16/32   1/1/1
  OAM           32    8/16/32   16/32     1/1/1 *
  Work RAM 256K 16    8/16/32   8/16/32   3/3/6 **
  Palette RAM   16    8/16/32   16/32     1/1/2 *
  VRAM          16    8/16/32   16/32     1/1/2 *
  GamePak ROM   16    8/16/32   -         5/5/8 **/***
  GamePak Flash 16    8/16/32   16/32     5/5/8 **/***
  GamePak SRAM  8     8         8         5     **
```

Timing Notes:

```
  *   Plus 1 cycle if GBA accesses video memory at the same time.
  **  Default waitstate settings, see System Control chapter.
  *** Separate timings for sequential, and non-sequential accesses.
  One cycle equals approx. 59.59ns (ie. 16.78MHz clock).
```

All memory (except GamePak SRAM) can be accessed by 16bit and 32bit DMA.

### GamePak Memory

Only DMA3 (and the CPU of course) may access GamePak ROM. GamePak SRAM can be
accessed by the CPU only - restricted to bytewise 8bit transfers. The SRAM
region is supposed for as external FLASH backup memory, or for battery-backed
SRAM.

For details about configuration of GamePak Waitstates, see:

- [GBA System Control](#gbasystemcontrol)

### VRAM, OAM, and Palette RAM Access

These memory regions can be accessed during H-Blank or V-Blank only (unless
display is disabled by Forced Blank bit in DISPCNT register).

There is an additional restriction for OAM memory: Accesses during H-Blank are
allowed only if 'H-Blank Interval Free' in DISPCNT is set (which'd reduce
number of display-able OBJs though).

The CPU appears to be able to access VRAM/OAM/Palette at any time, a waitstate
(one clock cycle) being inserted automatically in case that the display
controller was accessing memory simultaneously. (Ie. unlike as in old 8bit
gameboy, the data will not get lost.)

### CPU Mode Performance

Note that the GamePak ROM bus is limited to 16bits, thus executing ARM
instructions (32bit opcodes) from inside of GamePak ROM would result in a not
so good performance. So, it'd be more recommended to use THUMB instruction
(16bit opcodes) which'd allow each opcode to be read at once.

(ARM instructions can be used at best performance by copying code from GamePak
ROM into internal Work RAM)

### Data Format

Even though the ARM CPU itself would allow to select between Little-Endian and
Big-Endian format by using an external circuit, in the GBA no such circuit
exists, and the data format is always Little-Endian. That is, when accessing
16bit or 32bit data in memory, the least significant bits are stored in the
first byte (smallest address), and the most significant bits in the last byte.
(Ie. same as for 80x86 and Z80 CPUs.)

## <a name="gbaiomap"></a>  GBA I/O Map

### LCD I/O Registers

```
  4000000h  2    R/W  DISPCNT   LCD Control
  4000002h  2    R/W  -         Undocumented - Green Swap
  4000004h  2    R/W  DISPSTAT  General LCD Status (STAT,LYC)
  4000006h  2    R    VCOUNT    Vertical Counter (LY)
  4000008h  2    R/W  BG0CNT    BG0 Control
  400000Ah  2    R/W  BG1CNT    BG1 Control
  400000Ch  2    R/W  BG2CNT    BG2 Control
  400000Eh  2    R/W  BG3CNT    BG3 Control
  4000010h  2    W    BG0HOFS   BG0 X-Offset
  4000012h  2    W    BG0VOFS   BG0 Y-Offset
  4000014h  2    W    BG1HOFS   BG1 X-Offset
  4000016h  2    W    BG1VOFS   BG1 Y-Offset
  4000018h  2    W    BG2HOFS   BG2 X-Offset
  400001Ah  2    W    BG2VOFS   BG2 Y-Offset
  400001Ch  2    W    BG3HOFS   BG3 X-Offset
  400001Eh  2    W    BG3VOFS   BG3 Y-Offset
  4000020h  2    W    BG2PA     BG2 Rotation/Scaling Parameter A (dx)
  4000022h  2    W    BG2PB     BG2 Rotation/Scaling Parameter B (dmx)
  4000024h  2    W    BG2PC     BG2 Rotation/Scaling Parameter C (dy)
  4000026h  2    W    BG2PD     BG2 Rotation/Scaling Parameter D (dmy)
  4000028h  4    W    BG2X      BG2 Reference Point X-Coordinate
  400002Ch  4    W    BG2Y      BG2 Reference Point Y-Coordinate
  4000030h  2    W    BG3PA     BG3 Rotation/Scaling Parameter A (dx)
  4000032h  2    W    BG3PB     BG3 Rotation/Scaling Parameter B (dmx)
  4000034h  2    W    BG3PC     BG3 Rotation/Scaling Parameter C (dy)
  4000036h  2    W    BG3PD     BG3 Rotation/Scaling Parameter D (dmy)
  4000038h  4    W    BG3X      BG3 Reference Point X-Coordinate
  400003Ch  4    W    BG3Y      BG3 Reference Point Y-Coordinate
  4000040h  2    W    WIN0H     Window 0 Horizontal Dimensions
  4000042h  2    W    WIN1H     Window 1 Horizontal Dimensions
  4000044h  2    W    WIN0V     Window 0 Vertical Dimensions
  4000046h  2    W    WIN1V     Window 1 Vertical Dimensions
  4000048h  2    R/W  WININ     Inside of Window 0 and 1
  400004Ah  2    R/W  WINOUT    Inside of OBJ Window & Outside of Windows
  400004Ch  2    W    MOSAIC    Mosaic Size
  400004Eh       -    -         Not used
  4000050h  2    R/W  BLDCNT    Color Special Effects Selection
  4000052h  2    R/W  BLDALPHA  Alpha Blending Coefficients
  4000054h  2    W    BLDY      Brightness (Fade-In/Out) Coefficient
  4000056h       -    -         Not used
```

### Sound Registers

```
  4000060h  2  R/W  SOUND1CNT_L Channel 1 Sweep register       (NR10)
  4000062h  2  R/W  SOUND1CNT_H Channel 1 Duty/Length/Envelope (NR11, NR12)
  4000064h  2  R/W  SOUND1CNT_X Channel 1 Frequency/Control    (NR13, NR14)
  4000066h     -    -           Not used
  4000068h  2  R/W  SOUND2CNT_L Channel 2 Duty/Length/Envelope (NR21, NR22)
  400006Ah     -    -           Not used
  400006Ch  2  R/W  SOUND2CNT_H Channel 2 Frequency/Control    (NR23, NR24)
  400006Eh     -    -           Not used
  4000070h  2  R/W  SOUND3CNT_L Channel 3 Stop/Wave RAM select (NR30)
  4000072h  2  R/W  SOUND3CNT_H Channel 3 Length/Volume        (NR31, NR32)
  4000074h  2  R/W  SOUND3CNT_X Channel 3 Frequency/Control    (NR33, NR34)
  4000076h     -    -           Not used
  4000078h  2  R/W  SOUND4CNT_L Channel 4 Length/Envelope      (NR41, NR42)
  400007Ah     -    -           Not used
  400007Ch  2  R/W  SOUND4CNT_H Channel 4 Frequency/Control    (NR43, NR44)
  400007Eh     -    -           Not used
  4000080h  2  R/W  SOUNDCNT_L  Control Stereo/Volume/Enable   (NR50, NR51)
  4000082h  2  R/W  SOUNDCNT_H  Control Mixing/DMA Control
  4000084h  2  R/W  SOUNDCNT_X  Control Sound on/off           (NR52)
  4000086h     -    -           Not used
  4000088h  2  BIOS SOUNDBIAS   Sound PWM Control
  400008Ah  ..   -    -         Not used
  4000090h 2x10h R/W  WAVE_RAM  Channel 3 Wave Pattern RAM (2 banks!!)
  40000A0h  4    W    FIFO_A    Channel A FIFO, Data 0-3
  40000A4h  4    W    FIFO_B    Channel B FIFO, Data 0-3
  40000A8h       -    -         Not used
```

### DMA Transfer Channels

```
  40000B0h  4    W    DMA0SAD   DMA 0 Source Address
  40000B4h  4    W    DMA0DAD   DMA 0 Destination Address
  40000B8h  2    W    DMA0CNT_L DMA 0 Word Count
  40000BAh  2    R/W  DMA0CNT_H DMA 0 Control
  40000BCh  4    W    DMA1SAD   DMA 1 Source Address
  40000C0h  4    W    DMA1DAD   DMA 1 Destination Address
  40000C4h  2    W    DMA1CNT_L DMA 1 Word Count
  40000C6h  2    R/W  DMA1CNT_H DMA 1 Control
  40000C8h  4    W    DMA2SAD   DMA 2 Source Address
  40000CCh  4    W    DMA2DAD   DMA 2 Destination Address
  40000D0h  2    W    DMA2CNT_L DMA 2 Word Count
  40000D2h  2    R/W  DMA2CNT_H DMA 2 Control
  40000D4h  4    W    DMA3SAD   DMA 3 Source Address
  40000D8h  4    W    DMA3DAD   DMA 3 Destination Address
  40000DCh  2    W    DMA3CNT_L DMA 3 Word Count
  40000DEh  2    R/W  DMA3CNT_H DMA 3 Control
  40000E0h       -    -         Not used
```

### Timer Registers

```
  4000100h  2    R/W  TM0CNT_L  Timer 0 Counter/Reload
  4000102h  2    R/W  TM0CNT_H  Timer 0 Control
  4000104h  2    R/W  TM1CNT_L  Timer 1 Counter/Reload
  4000106h  2    R/W  TM1CNT_H  Timer 1 Control
  4000108h  2    R/W  TM2CNT_L  Timer 2 Counter/Reload
  400010Ah  2    R/W  TM2CNT_H  Timer 2 Control
  400010Ch  2    R/W  TM3CNT_L  Timer 3 Counter/Reload
  400010Eh  2    R/W  TM3CNT_H  Timer 3 Control
  4000110h       -    -         Not used
```

### Serial Communication (1)

```
  4000120h  4    R/W  SIODATA32 SIO Data (Normal-32bit Mode; shared with below)
  4000120h  2    R/W  SIOMULTI0 SIO Data 0 (Parent)    (Multi-Player Mode)
  4000122h  2    R/W  SIOMULTI1 SIO Data 1 (1st Child) (Multi-Player Mode)
  4000124h  2    R/W  SIOMULTI2 SIO Data 2 (2nd Child) (Multi-Player Mode)
  4000126h  2    R/W  SIOMULTI3 SIO Data 3 (3rd Child) (Multi-Player Mode)
  4000128h  2    R/W  SIOCNT    SIO Control Register
  400012Ah  2    R/W  SIOMLT_SEND SIO Data (Local of MultiPlayer; shared below)
  400012Ah  2    R/W  SIODATA8  SIO Data (Normal-8bit and UART Mode)
  400012Ch       -    -         Not used
```

### Keypad Input

```
  4000130h  2    R    KEYINPUT  Key Status
  4000132h  2    R/W  KEYCNT    Key Interrupt Control
```

### Serial Communication (2)

```
  4000134h  2    R/W  RCNT      SIO Mode Select/General Purpose Data
  4000136h  -    -    IR        Ancient - Infrared Register (Prototypes only)
  4000138h       -    -         Not used
  4000140h  2    R/W  JOYCNT    SIO JOY Bus Control
  4000142h       -    -         Not used
  4000150h  4    R/W  JOY_RECV  SIO JOY Bus Receive Data
  4000154h  4    R/W  JOY_TRANS SIO JOY Bus Transmit Data
  4000158h  2    R/?  JOYSTAT   SIO JOY Bus Receive Status
  400015Ah       -    -         Not used
```

### Interrupt, Waitstate, and Power-Down Control

```
  4000200h  2    R/W  IE        Interrupt Enable Register
  4000202h  2    R/W  IF        Interrupt Request Flags / IRQ Acknowledge
  4000204h  2    R/W  WAITCNT   Game Pak Waitstate Control
  4000206h       -    -         Not used
  4000208h  2    R/W  IME       Interrupt Master Enable Register
  400020Ah       -    -         Not used
  4000300h  1    R/W  POSTFLG   Undocumented - Post Boot Flag
  4000301h  1    W    HALTCNT   Undocumented - Power Down Control
  4000302h       -    -         Not used
  4000410h  ?    ?    ?         Undocumented - Purpose Unknown / Bug ??? 0FFh
  4000411h       -    -         Not used
  4000800h  4    R/W  ?         Undocumented - Internal Memory Control (R/W)
  4000804h       -    -         Not used
  4xx0800h  4    R/W  ?         Mirrors of 4000800h (repeated each 64K)
```

All further addresses at 4XXXXXXh are unused and do not contain mirrors of the
I/O area, with the only exception that 4000800h is repeated each 64K (ie.
mirrored at 4010800h, 4020800h, etc.)

## <a name="gbalcdvideocontroller"></a>  GBA LCD Video Controller

### Registers

- [LCD I/O Display Control](#lcdiodisplaycontrol)
- [LCD I/O Interrupts and Status](#lcdiointerruptsandstatus)
- [LCD I/O BG Control](#lcdiobgcontrol)
- [LCD I/O BG Scrolling](#lcdiobgscrolling)
- [LCD I/O BG Rotation/Scaling](#lcdiobgrotationscaling)
- [LCD I/O Window Feature](#lcdiowindowfeature)
- [LCD I/O Mosaic Function](#lcdiomosaicfunction)
- [LCD I/O Color Special Effects](#lcdiocolorspecialeffects)

### VRAM

- [LCD VRAM Overview](#lcdvramoverview)
- [LCD VRAM Character Data](#lcdvramcharacterdata)
- [LCD VRAM BG Screen Data Format (BG Map)](#lcdvrambgscreendataformatbgmap)
- [LCD VRAM Bitmap BG Modes](#lcdvrambitmapbgmodes)

### Sprites

- [LCD OBJ - Overview](#lcdobjoverview)
- [LCD OBJ - OAM Attributes](#lcdobjoamattributes)
- [LCD OBJ - OAM Rotation/Scaling Parameters](#lcdobjoamrotationscalingparameters)
- [LCD OBJ - VRAM Character (Tile) Mapping](#lcdobjvramcharactertilemapping)

### Other

- [LCD Color Palettes](#lcdcolorpalettes)
- [LCD Dimensions and Timings](#lcddimensionsandtimings)

## <a name="lcdiodisplaycontrol"></a>  LCD I/O Display Control

### 4000000h - DISPCNT - LCD Control (Read/Write)

```
  Bit   Expl.
  0-2   BG Mode                (0-5=Video Mode 0-5, 6-7=Prohibited)
  3     Reserved / CGB Mode    (0=GBA, 1=CGB; can be set only by BIOS opcodes)
  4     Display Frame Select   (0-1=Frame 0-1) (for BG Modes 4,5 only)
  5     H-Blank Interval Free  (1=Allow access to OAM during H-Blank)
  6     OBJ Character VRAM Mapping (0=Two dimensional, 1=One dimensional)
  7     Forced Blank           (1=Allow FAST access to VRAM,Palette,OAM)
  8     Screen Display BG0  (0=Off, 1=On)
  9     Screen Display BG1  (0=Off, 1=On)
  10    Screen Display BG2  (0=Off, 1=On)
  11    Screen Display BG3  (0=Off, 1=On)
  12    Screen Display OBJ  (0=Off, 1=On)
  13    Window 0 Display Flag   (0=Off, 1=On)
  14    Window 1 Display Flag   (0=Off, 1=On)
  15    OBJ Window Display Flag (0=Off, 1=On)
```

The table summarizes the facilities of the separate BG modes (video modes).

```
  Mode  Rot/Scal Layers Size               Tiles Colors       Features
  0     No       0123   256x256..512x515   1024  16/16..256/1 SFMABP
  1     Mixed    012-   (BG0,BG1 as above Mode 0, BG2 as below Mode 2)
  2     Yes      --23   128x128..1024x1024 256   256/1        S-MABP
  3     Yes      --2-   240x160            1     32768        --MABP
  4     Yes      --2-   240x160            2     256/1        --MABP
  5     Yes      --2-   160x128            2     32768        --MABP
```

Features: S)crolling, F)lip, M)osaic, A)lphaBlending, B)rightness, P)riority.

BG Modes 0-2 are Tile/Map-based. BG Modes 3-5 are Bitmap-based, in these modes
1 or 2 Frames (ie. bitmaps, or 'full screen tiles') exists, if two frames
exist, either one can be displayed, and the other one can be redrawn in
background.

### Blanking Bits

Setting Forced Blank (Bit 7) causes the video controller to display white
lines, and all VRAM, Palette RAM, and OAM may be accessed.

"When the internal HV synchronous counter cancels a forced blank during a
display period, the display begins from the beginning, following the display of
two vertical lines." What ?

Setting H-Blank Interval Free (Bit 5) allows to access OAM during H-Blank time
- using this feature reduces the number of sprites that can be displayed per
line.

### Display Enable Bits

By default, BG0-3 and OBJ Display Flags (Bit 8-12) are used to enable/disable
BGs and OBJ. When enabling Window 0 and/or 1 (Bit 13-14), color special effects
may be used, and BG0-3 and OBJ are controlled by the window(s).

### Frame Selection

In BG Modes 4 and 5 (Bitmap modes), either one of the two bitmaps/frames may be
displayed (Bit 4), allowing the user to update the other (invisible) frame in
background. In BG Mode 3, only one frame exists.

In BG Modes 0-2 (Tile/Map based modes), a similar effect may be gained by
altering the base address(es) of BG Map and/or BG Character data.

### 4000002h - Undocumented - Green Swap (R/W)

Normally, red green blue intensities for a group of two pixels is output as
BGRbgr (uppercase for left pixel at even xloc, lowercase for right pixel at odd
xloc). When the Green Swap bit is set, each pixel group is output as BgRbGr
(ie. green intensity of each two pixels exchanged).

```
  Bit   Expl.
  0     Green Swap  (0=Normal, 1=Swap)
  1-15  Not used
```

This feature appears to be applied to the final picture (ie. after mixing the
separate BG and OBJ layers). Eventually intended for other display types (with
other pin-outs). With normal GBA hardware it is just producing an interesting
dirt effect.

The NDS DISPCNT registers are 32bit (4000000h..4000003h), so Green Swap doesn't
exist in NDS mode, however, the NDS does support Green Swap in GBA mode.

## <a name="lcdiointerruptsandstatus"></a>  LCD I/O Interrupts and Status

### 4000004h - DISPSTAT - General LCD Status (Read/Write)

Display status and Interrupt control. The H-Blank conditions are generated once
per scanline, including for the 'hidden' scanlines during V-Blank.

```
  Bit   Expl.
  0     V-Blank flag   (Read only) (1=VBlank) (set in line 160..226; not 227)
  1     H-Blank flag   (Read only) (1=HBlank) (toggled in all lines, 0..227)
  2     V-Counter flag (Read only) (1=Match)  (set in selected line)     (R)
  3     V-Blank IRQ Enable         (1=Enable)                          (R/W)
  4     H-Blank IRQ Enable         (1=Enable)                          (R/W)
  5     V-Counter IRQ Enable       (1=Enable)                          (R/W)
  6     Not used (0) / DSi: LCD Initialization Ready (0=Busy, 1=Ready)   (R)
  7     Not used (0) / NDS: MSB of V-Vcount Setting (LYC.Bit8) (0..262)(R/W)
  8-15  V-Count Setting (LYC)      (0..227)                            (R/W)
```

The V-Count-Setting value is much the same as LYC of older gameboys, when its
value is identical to the content of the VCOUNT register then the V-Counter
flag is set (Bit 2), and (if enabled in Bit 5) an interrupt is requested.

Although the drawing time is only 960 cycles (240\*4), the H-Blank flag is "0"
for a total of 1006 cycles.

### 4000006h - VCOUNT - Vertical Counter (Read only)

Indicates the currently drawn scanline, values in range from 160..227 indicate
'hidden' scanlines within VBlank area.

```
  Bit   Expl.
  0-7   Current Scanline (LY)      (0..227)                              (R)
  8     Not used (0) / NDS: MSB of Current Scanline (LY.Bit8) (0..262)   (R)
  9-15  Not Used (0)
```

Note: This is much the same than the 'LY' register of older gameboys.

## <a name="lcdiobgcontrol"></a>  LCD I/O BG Control

### 4000008h - BG0CNT - BG0 Control (R/W) (BG Modes 0,1 only)

### 400000Ah - BG1CNT - BG1 Control (R/W) (BG Modes 0,1 only)

### 400000Ch - BG2CNT - BG2 Control (R/W) (BG Modes 0,1,2 only)

### 400000Eh - BG3CNT - BG3 Control (R/W) (BG Modes 0,2 only)

```
  Bit   Expl.
  0-1   BG Priority           (0-3, 0=Highest)
  2-3   Character Base Block  (0-3, in units of 16 KBytes) (=BG Tile Data)
  4-5   Not used (must be zero) (except in NDS mode: MSBs of char base)
  6     Mosaic                (0=Disable, 1=Enable)
  7     Colors/Palettes       (0=16/16, 1=256/1)
  8-12  Screen Base Block     (0-31, in units of 2 KBytes) (=BG Map Data)
  13    BG0/BG1: Not used (except in NDS mode: Ext Palette Slot for BG0/BG1)
  13    BG2/BG3: Display Area Overflow (0=Transparent, 1=Wraparound)
  14-15 Screen Size (0-3)
```

Internal Screen Size (dots) and size of BG Map (bytes):

```
  Value  Text Mode      Rotation/Scaling Mode
  0      256x256 (2K)   128x128   (256 bytes)
  1      512x256 (4K)   256x256   (1K)
  2      256x512 (4K)   512x512   (4K)
  3      512x512 (8K)   1024x1024 (16K)
```

In case that some or all BGs are set to same priority then BG0 is having the
highest, and BG3 the lowest priority.

In 'Text Modes', the screen size is organized as follows: The screen consists
of one or more 256x256 pixel (32x32 tiles) areas. When Size=0: only 1 area
(SC0), when Size=1 or Size=2: two areas (SC0,SC1 either horizontally or
vertically arranged next to each other), when Size=3: four areas (SC0,SC1 in
upper row, SC2,SC3 in lower row). Whereas SC0 is defined by the normal BG Map
base address (Bit 8-12 of BGxCNT), SC1 uses same address +2K, SC2 address +4K,
SC3 address +6K. When the screen is scrolled it'll always wraparound.

In 'Rotation/Scaling Modes', the screen size is organized as follows, only one
area (SC0) of variable size 128x128..1024x1024 pixels (16x16..128x128 tiles)
exists. When the screen is rotated/scaled (or scrolled?) so that the LCD
viewport reaches outside of the background/screen area, then BG may be either
displayed as transparent or wraparound (Bit 13 of BGxCNT).

## <a name="lcdiobgscrolling"></a>  LCD I/O BG Scrolling

### 4000010h - BG0HOFS - BG0 X-Offset (W)

### 4000012h - BG0VOFS - BG0 Y-Offset (W)

```
  Bit   Expl.
  0-8   Offset (0-511)
  9-15  Not used
```

Specifies the coordinate of the upperleft first visible dot of BG0 background
layer, ie. used to scroll the BG0 area.

### 4000014h - BG1HOFS - BG1 X-Offset (W)

### 4000016h - BG1VOFS - BG1 Y-Offset (W)

Same as above BG0HOFS and BG0VOFS for BG1 respectively.

### 4000018h - BG2HOFS - BG2 X-Offset (W)

### 400001Ah - BG2VOFS - BG2 Y-Offset (W)

Same as above BG0HOFS and BG0VOFS for BG2 respectively.

### 400001Ch - BG3HOFS - BG3 X-Offset (W)

### 400001Eh - BG3VOFS - BG3 Y-Offset (W)

Same as above BG0HOFS and BG0VOFS for BG3 respectively.

The above BG scrolling registers are exclusively used in Text modes, ie. for
all layers in BG Mode 0, and for the first two layers in BG mode 1.

In other BG modes (Rotation/Scaling and Bitmap modes) above registers are
ignored. Instead, the screen may be scrolled by modifying the BG
Rotation/Scaling Reference Point registers.

## <a name="lcdiobgrotationscaling"></a>  LCD I/O BG Rotation/Scaling

### 4000028h - BG2X\_L - BG2 Reference Point X-Coordinate, lower 16 bit (W)

### 400002Ah - BG2X\_H - BG2 Reference Point X-Coordinate, upper 12 bit (W)

### 400002Ch - BG2Y\_L - BG2 Reference Point Y-Coordinate, lower 16 bit (W)

### 400002Eh - BG2Y\_H - BG2 Reference Point Y-Coordinate, upper 12 bit (W)

These registers are replacing the BG scrolling registers which are used for
Text mode, ie. the X/Y coordinates specify the source position from inside of
the BG Map/Bitmap of the pixel to be displayed at upper left of the GBA
display. The normal BG scrolling registers are ignored in Rotation/Scaling and
Bitmap modes.

```
  Bit   Expl.
  0-7   Fractional portion (8 bits)
  8-26  Integer portion    (19 bits)
  27    Sign               (1 bit)
  28-31 Not used
```

Because values are shifted left by eight, fractional portions may be specified
in steps of 1/256 pixels (this would be relevant only if the screen is actually
rotated or scaled). Normal signed 32bit values may be written to above
registers (the most significant bits will be ignored and the value will be
cut-down to 28bits, but this is no actual problem because signed values have
set all MSBs to the same value).

### Internal Reference Point Registers

The above reference points are automatically copied to internal registers
during each vblank, specifying the origin for the first scanline. The internal
registers are then incremented by dmx and dmy after each scanline.

Caution: Writing to a reference point register by software outside of the
Vblank period does immediately copy the new value to the corresponding internal
register, that means: in the current frame, the new value specifies the origin
of the \<current> scanline (instead of the topmost scanline).

### 4000020h - BG2PA - BG2 Rotation/Scaling Parameter A (alias dx) (W)

### 4000022h - BG2PB - BG2 Rotation/Scaling Parameter B (alias dmx) (W)

### 4000024h - BG2PC - BG2 Rotation/Scaling Parameter C (alias dy)  (W)

### 4000026h - BG2PD - BG2 Rotation/Scaling Parameter D (alias dmy) (W)

```
  Bit   Expl.
  0-7   Fractional portion (8 bits)
  8-14  Integer portion    (7 bits)
  15    Sign               (1 bit)
```

See below for details.

### 400003Xh - BG3X\_L/H, BG3Y\_L/H, BG3PA-D - BG3 Rotation/Scaling Parameters

Same as above BG2 Reference Point, and Rotation/Scaling Parameters, for BG3
respectively.

### dx (PA) and dy (PC)

When transforming a horizontal line, dx and dy specify the resulting gradient
and magnification for that line. For example:

Horizontal line, length=100, dx=1, and dy=1. The resulting line would be drawn
at 45 degrees, f(y)=1/1\*x. Note that this would involve that line is magnified,
the new length is SQR(100^2+100^2)=141.42. Yup, exactly - that's the old a^2 +
b^2 = c^2 formula.

### dmx (PB) and dmy (PD)

These values define the resulting gradient and magnification for transformation
of vertical lines. However, when rotating a square area (which is surrounded by
horizontal and vertical lines), then the desired result should be usually a
rotated \<square> area (ie. not a parallelogram, for example).

Thus, dmx and dmy must be defined in direct relationship to dx and dy, taking
the example above, we'd have to set dmx=-1, and dmy=1, f(x)=-1/1\*y.

### Area Overflow

In result of rotation/scaling it may often happen that areas outside of the
actual BG area become moved into the LCD viewport. Depending of the Area
Overflow bit (BG2CNT and BG3CNT, Bit 13) these areas may be either displayed
(by wrapping the BG area), or may be displayed transparent.

This works only in BG modes 1 and 2. The area overflow is ignored in Bitmap
modes (BG modes 3-5), the outside of the Bitmaps is always transparent.

--- more details and confusing or helpful formulas ---

### The following parameters are required for Rotation/Scaling

```
  Rotation Center X and Y Coordinates (x0,y0)
  Rotation Angle                      (alpha)
  Magnification X and Y Values        (xMag,yMag)
```

The display is rotated by 'alpha' degrees around the center.

The displayed picture is magnified by 'xMag' along x-Axis (Y=y0) and 'yMag'
along y-Axis (X=x0).

### Calculating Rotation/Scaling Parameters A-D

```
  A = Cos (alpha) / xMag    ;distance moved in direction x, same line
  B = Sin (alpha) / xMag    ;distance moved in direction x, next line
  C = Sin (alpha) / yMag    ;distance moved in direction y, same line
  D = Cos (alpha) / yMag    ;distance moved in direction y, next line
```

### Calculating the position of a rotated/scaled dot

Using the following expressions,

```
  x0,y0    Rotation Center
  x1,y1    Old Position of a pixel (before rotation/scaling)
  x2,y2    New position of above pixel (after rotation scaling)
  A,B,C,D  BG2PA-BG2PD Parameters (as calculated above)
```

the following formula can be used to calculate x2,y2:

```
  x2 = A(x1-x0) + B(y1-y0) + x0
  y2 = C(x1-x0) + D(y1-y0) + y0
```

## <a name="lcdiowindowfeature"></a>  LCD I/O Window Feature

The Window Feature may be used to split the screen into four regions. The
BG0-3,OBJ layers and Color Special Effects can be separately enabled or
disabled in each of these regions.

### The DISPCNT Register

DISPCNT Bits 13-15 are used to enable Window 0, Window 1, and/or OBJ Window
regions, if any of these regions is enabled then the "Outside of Windows"
region is automatically enabled, too.

DISPCNT Bits 8-12 are kept used as master enable bits for the BG0-3,OBJ layers,
a layer is displayed only if both DISPCNT and WININ/OUT enable bits are set.

### 4000040h - WIN0H - Window 0 Horizontal Dimensions (W)

### 4000042h - WIN1H - Window 1 Horizontal Dimensions (W)

```
  Bit   Expl.
  0-7   X2, Rightmost coordinate of window, plus 1
  8-15  X1, Leftmost coordinate of window
```

Garbage values of X2>240 or X1>X2 are interpreted as X2=240.

### 4000044h - WIN0V - Window 0 Vertical Dimensions (W)

### 4000046h - WIN1V - Window 1 Vertical Dimensions (W)

```
  Bit   Expl.
  0-7   Y2, Bottom-most coordinate of window, plus 1
  8-15  Y1, Top-most coordinate of window
```

Garbage values of Y2>160 or Y1>Y2 are interpreted as Y2=160.

### 4000048h - WININ - Control of Inside of Window(s) (R/W)

```
  Bit   Expl.
  0-3   Window 0 BG0-BG3 Enable Bits     (0=No Display, 1=Display)
  4     Window 0 OBJ Enable Bit          (0=No Display, 1=Display)
  5     Window 0 Color Special Effect    (0=Disable, 1=Enable)
  6-7   Not used
  8-11  Window 1 BG0-BG3 Enable Bits     (0=No Display, 1=Display)
  12    Window 1 OBJ Enable Bit          (0=No Display, 1=Display)
  13    Window 1 Color Special Effect    (0=Disable, 1=Enable)
  14-15 Not used
```

### 400004Ah - WINOUT - Control of Outside of Windows & Inside of OBJ Window (R/W)

```
  Bit   Expl.
  0-3   Outside BG0-BG3 Enable Bits      (0=No Display, 1=Display)
  4     Outside OBJ Enable Bit           (0=No Display, 1=Display)
  5     Outside Color Special Effect     (0=Disable, 1=Enable)
  6-7   Not used
  8-11  OBJ Window BG0-BG3 Enable Bits   (0=No Display, 1=Display)
  12    OBJ Window OBJ Enable Bit        (0=No Display, 1=Display)
  13    OBJ Window Color Special Effect  (0=Disable, 1=Enable)
  14-15 Not used
```

### The OBJ Window

The dimension of the OBJ Window is specified by OBJs which are having the "OBJ
Mode" attribute being set to "OBJ Window". Any non-transparent dots of any such
OBJs are marked as OBJ Window area. The OBJ itself is not displayed.

The color, palette, and display priority of these OBJs are ignored. Both
DISPCNT Bits 12 and 15 must be set when defining OBJ Window region(s).

### Window Priority

In case that more than one window is enabled, and that these windows do
overlap, Window 0 is having highest priority, Window 1 medium, and Obj Window
lowest priority. Outside of Window is having zero priority, it is used for all
dots which are not inside of any window region.

## <a name="lcdiomosaicfunction"></a>  LCD I/O Mosaic Function

### 400004Ch - MOSAIC - Mosaic Size (W)

The Mosaic function can be separately enabled/disabled for BG0-BG3 by
BG0CNT-BG3CNT Registers, as well as for each OBJ0-127 by OBJ attributes in OAM
memory. Also, setting all of the bits below to zero effectively disables the
mosaic function.

```
  Bit   Expl.
  0-3   BG Mosaic H-Size  (minus 1)
  4-7   BG Mosaic V-Size  (minus 1)
  8-11  OBJ Mosaic H-Size (minus 1)
  12-15 OBJ Mosaic V-Size (minus 1)
  16-31 Not used
```

Example: When setting H-Size to 5, then pixels 0-5 of each display row are
colorized as pixel 0, pixels 6-11 as pixel 6, pixels 12-17 as pixel 12, and so
on.

Normally, a 'mosaic-pixel' is colorized by the color of the upperleft covered
pixel. In many cases it might be more desireful to use the color of the pixel
in the center of the covered area - this effect may be gained by scrolling the
background (or by adjusting the OBJ position, as far as upper/left rows/columns
of OBJ are transparent).

## <a name="lcdiocolorspecialeffects"></a>  LCD I/O Color Special Effects

Two types of Special Effects are supported: Alpha Blending (Semi-Transparency)
allows to combine colors of two selected surfaces. Brightness Increase/Decrease
adjust the brightness of the selected surface.

### 4000050h - BLDCNT - Color Special Effects Selection (R/W)

```
  Bit   Expl.
  0     BG0 1st Target Pixel (Background 0)
  1     BG1 1st Target Pixel (Background 1)
  2     BG2 1st Target Pixel (Background 2)
  3     BG3 1st Target Pixel (Background 3)
  4     OBJ 1st Target Pixel (Top-most OBJ pixel)
  5     BD  1st Target Pixel (Backdrop)
  6-7   Color Special Effect (0-3, see below)
         0 = None                (Special effects disabled)
         1 = Alpha Blending      (1st+2nd Target mixed)
         2 = Brightness Increase (1st Target becomes whiter)
         3 = Brightness Decrease (1st Target becomes blacker)
  8     BG0 2nd Target Pixel (Background 0)
  9     BG1 2nd Target Pixel (Background 1)
  10    BG2 2nd Target Pixel (Background 2)
  11    BG3 2nd Target Pixel (Background 3)
  12    OBJ 2nd Target Pixel (Top-most OBJ pixel)
  13    BD  2nd Target Pixel (Backdrop)
  14-15 Not used
```

Selects the 1st Target layer(s) for special effects. For Alpha
Blending/Semi-Transparency, it does also select the 2nd Target layer(s), which
should have next lower display priority as the 1st Target.

However, any combinations are possible, including that all layers may be
selected as both 1st+2nd target, in that case the top-most pixel will be used
as 1st target, and the next lower pixel as 2nd target.

### 4000052h - BLDALPHA - Alpha Blending Coefficients (R/W) (not W)

Used for Color Special Effects Mode 1, and for Semi-Transparent OBJs.

```
  Bit   Expl.
  0-4   EVA Coefficient (1st Target) (0..16 = 0/16..16/16, 17..31=16/16)
  5-7   Not used
  8-12  EVB Coefficient (2nd Target) (0..16 = 0/16..16/16, 17..31=16/16)
  13-15 Not used
```

For this effect, the top-most non-transparent pixel must be selected as 1st
Target, and the next-lower non-transparent pixel must be selected as 2nd
Target, if so - and only if so, then color intensities of 1st and 2nd Target
are mixed together by using the parameters in BLDALPHA register, for each pixel
each R, G, B intensities are calculated separately:

```
  I = MIN ( 31, I1st*EVA + I2nd*EVB )
```

Otherwise - for example, if only one target exists, or if a non-transparent
non-2nd-target pixel is moved between the two targets, or if 2nd target has
higher display priority than 1st target - then only the top-most pixel is
displayed (at normal intensity, regardless of BLDALPHA).

### 4000054h - BLDY - Brightness (Fade-In/Out) Coefficient (W) (not R/W)

Used for Color Special Effects Modes 2 and 3.

```
  Bit   Expl.
  0-4   EVY Coefficient (Brightness) (0..16 = 0/16..16/16, 17..31=16/16)
  5-31  Not used
```

For each pixel each R, G, B intensities are calculated separately:

```
  I = I1st + (31-I1st)*EVY   ;For Brightness Increase
  I = I1st - (I1st)*EVY      ;For Brightness Decrease
```

The color intensities of any selected 1st target surface(s) are increased or
decreased by using the parameter in BLDY register.

### Semi-Transparent OBJs

OBJs that are defined as 'Semi-Transparent' in OAM memory are always selected
as 1st Target (regardless of BLDCNT Bit 4), and are always using Alpha Blending
mode (regardless of BLDCNT Bit 6-7).

The BLDCNT register may be used to perform Brightness effects on the OBJ
(and/or other BG/BD layers). However, if a semi-transparent OBJ pixel does
overlap a 2nd target pixel, then semi-transparency becomes priority, and the
brightness effect will not take place (neither on 1st, nor 2nd target).

### The OBJ Layer

Before special effects are applied, the display controller computes the OBJ
priority ordering, and isolates the top-most OBJ pixel. In result, only the
top-most OBJ pixel is recursed at the time when processing special effects. Ie.
alpha blending and semi-transparency can be used for OBJ-to-BG or BG-to-OBJ ,
but not for OBJ-to-OBJ.

## <a name="lcdvramoverview"></a>  LCD VRAM Overview

The GBA contains 96 Kbytes VRAM built-in, located at address 06000000-06017FFF,
depending on the BG Mode used as follows:

### BG Mode 0,1,2 (Tile/Map based Modes)

```
  06000000-0600FFFF  64 KBytes shared for BG Map and Tiles
  06010000-06017FFF  32 KBytes OBJ Tiles
```

The shared 64K area can be split into BG Map area(s), and BG Tiles area(s), the
respective addresses for Map and Tile areas are set up by BG0CNT-BG3CNT
registers. The Map address may be specified in units of 2K (steps of 800h), the
Tile address in units of 16K (steps of 4000h).

### BG Mode 0,1 (Tile/Map based Text mode)

The tiles may have 4bit or 8bit color depth, minimum map size is 32x32 tiles,
maximum is 64x64 tiles, up to 1024 tiles can be used per map.

```
  Item        Depth     Required Memory
  One Tile    4bit      20h bytes
  One Tile    8bit      40h bytes
  1024 Tiles  4bit      8000h (32K)
  1024 Tiles  8bit      10000h (64K) - excluding some bytes for BG map
  BG Map      32x32     800h (2K)
  BG Map      64x64     2000h (8K)
```

### BG Mode 1,2 (Tile/Map based Rotation/Scaling mode)

The tiles may have 8bit color depth only, minimum map size is 16x16 tiles,
maximum is 128x128 tiles, up to 256 tiles can be used per map.

```
  Item        Depth     Required Memory
  One Tile    8bit      40h bytes
  256  Tiles  8bit      4000h (16K)
  BG Map      16x16     100h bytes
  BG Map      128x128   4000h (16K)
```

### BG Mode 3 (Bitmap based Mode for still images)

```
  06000000-06013FFF  80 KBytes Frame 0 buffer (only 75K actually used)
  06014000-06017FFF  16 KBytes OBJ Tiles
```

### BG Mode 4,5 (Bitmap based Modes)

```
  06000000-06009FFF  40 KBytes Frame 0 buffer (only 37.5K used in Mode 4)
  0600A000-06013FFF  40 KBytes Frame 1 buffer (only 37.5K used in Mode 4)
  06014000-06017FFF  16 KBytes OBJ Tiles
```

### Note

Additionally to the above VRAM, the GBA also contains 1 KByte Palette RAM (at
05000000h) and 1 KByte OAM (at 07000000h) which are both used by the display
controller as well.

## <a name="lcdvramcharacterdata"></a>  LCD VRAM Character Data

Each character (tile) consists of 8x8 dots (64 dots in total). The color depth
may be either 4bit or 8bit (see BG0CNT-BG3CNT).

### 4bit depth (16 colors, 16 palettes)

Each tile occupies 32 bytes of memory, the first 4 bytes for the topmost row of
the tile, and so on. Each byte representing two dots, the lower 4 bits define
the color for the left (!) dot, the upper 4 bits the color for the right dot.

### 8bit depth (256 colors, 1 palette)

Each tile occupies 64 bytes of memory, the first 8 bytes for the topmost row of
the tile, and so on. Each byte selects the palette entry for each dot.

## <a name="lcdvrambgscreendataformatbgmap"></a>  LCD VRAM BG Screen Data Format (BG Map)

The display background consists of 8x8 dot tiles, the arrangement of these
tiles is specified by the BG Screen Data (BG Map). The separate entries in this
map are as follows:

### Text BG Screen (2 bytes per entry)

Specifies the tile number and attributes. Note that BG tile numbers are always
specified in steps of 1 (unlike OBJ tile numbers which are using steps of two
in 256 color/1 palette mode).

```
  Bit   Expl.
  0-9   Tile Number     (0-1023) (a bit less in 256 color mode, because
                           there'd be otherwise no room for the bg map)
  10    Horizontal Flip (0=Normal, 1=Mirrored)
  11    Vertical Flip   (0=Normal, 1=Mirrored)
  12-15 Palette Number  (0-15)    (Not used in 256 color/1 palette mode)
```

A Text BG Map always consists of 32x32 entries (256x256 pixels), 400h entries =
800h bytes. However, depending on the BG Size, one, two, or four of these Maps
may be used together, allowing to create backgrounds of 256x256, 512x256,
256x512, or 512x512 pixels, if so, the first map (SC0) is located at base+0,
the next map (SC1) at base+800h, and so on.

### Rotation/Scaling BG Screen (1 byte per entry)

In this mode, only 256 tiles can be used. There are no x/y-flip attributes, the
color depth is always 256 colors/1 palette.

```
  Bit   Expl.
  0-7   Tile Number     (0-255)
```

The dimensions of Rotation/Scaling BG Maps depend on the BG size. For size 0-3
that are: 16x16 tiles (128x128 pixels), 32x32 tiles (256x256 pixels), 64x64
tiles (512x512 pixels), or 128x128 tiles (1024x1024 pixels).

The size and VRAM base address of the separate BG maps for BG0-3 are set up by
BG0CNT-BG3CNT registers.

## <a name="lcdvrambitmapbgmodes"></a>  LCD VRAM Bitmap BG Modes

In BG Modes 3-5 the background is defined in form of a bitmap (unlike as for
Tile/Map based BG modes). Bitmaps are implemented as BG2, with Rotation/Scaling
support. As bitmap modes are occupying 80KBytes of BG memory, only 16KBytes of
VRAM can be used for OBJ tiles.

### BG Mode 3 - 240x160 pixels, 32768 colors

Two bytes are associated to each pixel, directly defining one of the 32768
colors (without using palette data, and thus not supporting a 'transparent' BG
color).

```
  Bit   Expl.
  0-4   Red Intensity   (0-31)
  5-9   Green Intensity (0-31)
  10-14 Blue Intensity  (0-31)
  15    Not used in GBA Mode (in NDS Mode: Alpha=0=Transparent, Alpha=1=Normal)
```

The first 480 bytes define the topmost line, the next 480 the next line, and so
on. The background occupies 75 KBytes (06000000-06012BFF), most of the 80
Kbytes BG area, not allowing to redraw an invisible second frame in background,
so this mode is mostly recommended for still images only.

### BG Mode 4 - 240x160 pixels, 256 colors (out of 32768 colors)

One byte is associated to each pixel, selecting one of the 256 palette entries.
Color 0 (backdrop) is transparent, and OBJs may be displayed behind the bitmap.

The first 240 bytes define the topmost line, the next 240 the next line, and so
on. The background occupies 37.5 KBytes, allowing two frames to be used
(06000000-060095FF for Frame 0, and 0600A000-060135FF for Frame 1).

### BG Mode 5 - 160x128 pixels, 32768 colors

Colors are defined as for Mode 3 (see above), but horizontal and vertical size
are cut down to 160x128 pixels only - smaller than the physical dimensions of
the LCD screen.

The background occupies exactly 40 KBytes, so that BG VRAM may be split into
two frames (06000000-06009FFF for Frame 0, and 0600A000-06013FFF for Frame 1).

In BG modes 4,5, one Frame may be displayed (selected by DISPCNT Bit 4), the
other Frame is invisible and may be redrawn in background.

## <a name="lcdobjoverview"></a>  LCD OBJ - Overview

### General

Objects (OBJs) are moveable sprites. Up to 128 OBJs (of any size, up to 64x64
dots each) can be displayed per screen, and under best circumstances up to 128
OBJs (of small 8x8 dots size) can be displayed per horizontal display line.

### Maximum Number of Sprites per Line

The total available OBJ rendering cycles per line are

```
  1210  (=304*4-6)   If "H-Blank Interval Free" bit in DISPCNT register is 0
  954   (=240*4-6)   If "H-Blank Interval Free" bit in DISPCNT register is 1
```

The required rendering cycles are (depending on horizontal OBJ size)

```
  Cycles per <n> Pixels    OBJ Type              OBJ Type Screen Pixel Range
  n*1 cycles               Normal OBJs           8..64 pixels
  10+n*2 cycles            Rotation/Scaling OBJs 8..64 pixels   (area clipped)
  10+n*2 cycles            Rotation/Scaling OBJs 16..128 pixels (double size)
```

Caution:

The maximum number of OBJs per line is also affected by undisplayed (offscreen)
OBJs which are having higher priority than displayed OBJs.

To avoid this, move displayed OBJs to the begin of OAM memory (ie. OBJ0 has
highest priority, OBJ127 lowest).

Otherwise (in case that the program logic expects OBJs at fixed positions in
OAM) at least take care to set the OBJ size of undisplayed OBJs to 8x8 with
Rotation/Scaling disabled (this reduces the overload).

Does the above also apply for VERTICALLY OFFSCREEN (or VERTICALLY not on
CURRENT LINE) sprites ?

### VRAM - Character Data

OBJs are always combined of one or more 8x8 pixel Tiles (much like BG Tiles in
BG Modes 0-2). However, OBJ Tiles are stored in a separate area in VRAM:
06010000-06017FFF (32 KBytes) in BG Mode 0-2, or 06014000-06017FFF (16 KBytes)
in BG Mode 3-5.

Depending on the size of the above area (16K or 32K), and on the OBJ color
depth (4bit or 8bit), 256-1024 8x8 dots OBJ Tiles can be defined.

### OAM - Object Attribute Memory

This memory area contains Attributes which specify position, size, color depth,
etc. appearance for each of the 128 OBJs. Additionally, it contains 32 OBJ
Rotation/Scaling Parameter groups. OAM is located at 07000000-070003FF (sized 1
KByte).

## <a name="lcdobjoamattributes"></a>  LCD OBJ - OAM Attributes

### OBJ Attributes

There are 128 entries in OAM for each OBJ0-OBJ127. Each entry consists of 6
bytes (three 16bit Attributes). Attributes for OBJ0 are located at 07000000,
for OBJ1 at 07000008, OBJ2 at 07000010, and so on.

As you can see, there are blank spaces at 07000006, 0700000E, 07000016, etc. -
these 16bit values are used for OBJ Rotation/Scaling (as described in the next
chapter) - they are not directly related to the separate OBJs.

### OBJ Attribute 0 (R/W)

```
  Bit   Expl.
  0-7   Y-Coordinate           (0-255)
  8     Rotation/Scaling Flag  (0=Off, 1=On)
  When Rotation/Scaling used (Attribute 0, bit 8 set):
    9     Double-Size Flag     (0=Normal, 1=Double)
  When Rotation/Scaling not used (Attribute 0, bit 8 cleared):
    9     OBJ Disable          (0=Normal, 1=Not displayed)
  10-11 OBJ Mode  (0=Normal, 1=Semi-Transparent, 2=OBJ Window, 3=Prohibited)
  12    OBJ Mosaic             (0=Off, 1=On)
  13    Colors/Palettes        (0=16/16, 1=256/1)
  14-15 OBJ Shape              (0=Square,1=Horizontal,2=Vertical,3=Prohibited)
```

Caution: A very large OBJ (of 128 pixels vertically, ie. a 64 pixels OBJ in a
Double Size area) located at Y>128 will be treated as at Y>-128, the OBJ
is then displayed parts offscreen at the TOP of the display, it is then NOT
displayed at the bottom.

### OBJ Attribute 1 (R/W)

```
  Bit   Expl.
  0-8   X-Coordinate           (0-511)
  When Rotation/Scaling used (Attribute 0, bit 8 set):
    9-13  Rotation/Scaling Parameter Selection (0-31)
          (Selects one of the 32 Rotation/Scaling Parameters that
          can be defined in OAM, for details read next chapter.)
  When Rotation/Scaling not used (Attribute 0, bit 8 cleared):
    9-11  Not used
    12    Horizontal Flip      (0=Normal, 1=Mirrored)
    13    Vertical Flip        (0=Normal, 1=Mirrored)
  14-15 OBJ Size               (0..3, depends on OBJ Shape, see Attr 0)
          Size  Square   Horizontal  Vertical
          0     8x8      16x8        8x16
          1     16x16    32x8        8x32
          2     32x32    32x16       16x32
          3     64x64    64x32       32x64
```

### OBJ Attribute 2 (R/W)

```
  Bit   Expl.
  0-9   Character Name          (0-1023=Tile Number)
  10-11 Priority relative to BG (0-3; 0=Highest)
  12-15 Palette Number   (0-15) (Not used in 256 color/1 palette mode)
```

### Notes:

### OBJ Mode

The OBJ Mode may be Normal, Semi-Transparent, or OBJ Window.

Semi-Transparent means that the OBJ is used as 'Alpha Blending 1st Target'
(regardless of BLDCNT register, for details see chapter about Color Special
Effects).

OBJ Window means that the OBJ is not displayed, instead, dots with non-zero
color are used as mask for the OBJ Window, see DISPCNT and WINOUT for details.

### OBJ Tile Number

There are two situations which may divide the amount of available tiles by two
(by four if both situations apply):

1. When using the 256 Colors/1 Palette mode, only each second tile may be used,
the lower bit of the tile number should be zero (in 2-dimensional mapping mode,
the bit is completely ignored).

2. When using BG Mode 3-5 (Bitmap Modes), only tile numbers 512-1023 may be
used. That is because lower 16K of OBJ memory are used for BG. Attempts to use
tiles 0-511 are ignored (not displayed).

### Priority

In case that the 'Priority relative to BG' is the same than the priority of one
of the background layers, then the OBJ becomes higher priority and is displayed
on top of that BG layer.

Caution: Take care not to mess up BG Priority and OBJ priority. For example,
the following would cause garbage to be displayed:

```
  OBJ No. 0 with Priority relative to BG=1   ;hi OBJ prio, lo BG prio
  OBJ No. 1 with Priority relative to BG=0   ;lo OBJ prio, hi BG prio
```

That is, OBJ0 is always having priority above OBJ1-127, so assigning a lower BG
Priority to OBJ0 than for OBJ1-127 would be a bad idea.

## <a name="lcdobjoamrotationscalingparameters"></a>  LCD OBJ - OAM Rotation/Scaling Parameters

As described in the previous chapter, there are blank spaces between each of
the 128 OBJ Attribute Fields in OAM memory. These 128 16bit gaps are used to
store OBJ Rotation/Scaling Parameters.

### Location of Rotation/Scaling Parameters in OAM

Four 16bit parameters (PA,PB,PC,PD) are required to define a complete group of
Rotation/Scaling data. These are spread across OAM as such:

```
  1st Group - PA=07000006, PB=0700000E, PC=07000016, PD=0700001E
  2nd Group - PA=07000026, PB=0700002E, PC=07000036, PD=0700003E
  etc.
```

By using all blank space (128 x 16bit), up to 32 of these groups (4 x 16bit
each) can be defined in OAM.

### OBJ Rotation/Scaling PA,PB,PC,PD Parameters (R/W)

Each OBJ that uses Rotation/Scaling may select between any of the above 32
parameter groups. For details, refer to the previous chapter about OBJ
Attributes.

The meaning of the separate PA,PB,PC,PD values is identical as for BG, for
details read the chapter about BG Rotation/Scaling.

### OBJ Reference Point & Rotation Center

The OBJ Reference Point is the upper left of the OBJ, ie. OBJ X/Y coordinates:
X+0, Y+0.

The OBJ Rotation Center is always (or should be usually?) in the middle of the
object, ie. for a 8x32 pixel OBJ, this would be at the OBJ X/Y coordinates:
X+4, and Y+16.

### OBJ Double-Size Bit (for OBJs that use Rotation/Scaling)

When Double-Size is zero: The sprite is rotated, and then display inside of the
normal-sized (not rotated) rectangular area - the edges of the rotated sprite
will become invisible if they reach outside of that area.

When Double-Size is set: The sprite is rotated, and then display inside of the
double-sized (not rotated) rectangular area - this ensures that the edges of
the rotated sprite remain visible even if they would reach outside of the
normal-sized area. (Except that, for example, rotating a 8x32 pixel sprite by
90 degrees would still cut off parts of the sprite as the double-size area
isn't large enough.)

## <a name="lcdobjvramcharactertilemapping"></a>  LCD OBJ - VRAM Character (Tile) Mapping

Each OBJ tile consists of 8x8 dots, however, bigger OBJs can be displayed by
combining several 8x8 tiles. The horizontal and vertical size for each OBJ may
be separately defined in OAM, possible H/V sizes are 8,16,32,64 dots - allowing
'square' OBJs to be used (such like 8x8, 16x16, etc) as well as 'rectangular'
OBJs (such like 8x32, 64x16, etc.)

When displaying an OBJ that contains of more than one 8x8 tile, one of the
following two mapping modes can be used. In either case, the tile number of the
upperleft tile must be specified in OAM memory.

### Two Dimensional Character Mapping (DISPCNT Bit 6 cleared)

This mapping mode assumes that the 1024 OBJ tiles are arranged as a matrix of
32x32 tiles / 256x256 pixels (In 256 color mode: 16x32 tiles / 128x256 pixels).
Ie. the upper row of this matrix contains tiles 00h-1Fh, the next row tiles
20h-3Fh, and so on.

For example, when displaying a 16x16 pixel OBJ, with tile number set to 04h;
The upper row of the OBJ will consist of tile 04h and 05h, the next row of 24h
and 25h. (In 256 color mode: 04h and 06h, 24h and 26h.)

### One Dimensional Character Mapping (DISPCNT Bit 6 set)

In this mode, tiles are mapped each after each other from 00h-3FFh.

Using the same example as above, the upper row of the OBJ will consist of tile
04h and 05h, the next row of tile 06h and 07h. (In 256 color mode: 04h and 06h,
08h and 0Ah.)

## <a name="lcdcolorpalettes"></a>  LCD Color Palettes

### Color Palette RAM

BG and OBJ palettes are using separate memory regions:

```
  05000000-050001FF - BG Palette RAM (512 bytes, 256 colors)
  05000200-050003FF - OBJ Palette RAM (512 bytes, 256 colors)
```

Each BG and OBJ palette RAM may be either split into 16 palettes with 16 colors
each, or may be used as a single palette with 256 colors.

Note that some OBJs may access palette RAM in 16 color mode, while other OBJs
may use 256 color mode at the same time. Same for BG0-BG3 layers.

### Transparent Colors

Color 0 of all BG and OBJ palettes is transparent. Even though palettes are
described as 16 (256) color palettes, only 15 (255) colors are actually
visible.

### Backdrop Color

Color 0 of BG Palette 0 is used as backdrop color. This color is displayed if
an area of the screen is not covered by any non-transparent BG or OBJ dots.

### Color Definitions

Each color occupies two bytes (same as for 32768 color BG modes):

```
  Bit   Expl.
  0-4   Red Intensity   (0-31)
  5-9   Green Intensity (0-31)
  10-14 Blue Intensity  (0-31)
  15    Not used
```

### Intensities

Under normal circumstances (light source/viewing angle), the intensities 0-14
are practically all black, and only intensities 15-31 are resulting in visible
medium..bright colors.

Note: The intensity problem appears in the 8bit CGB "compatibility" mode
either. The original CGB display produced the opposite effect: Intensities 0-14
resulted in dark..medium colors, and intensities 15-31 resulted in bright
colors. Any "medium" colors of CGB games will appear invisible/black on GBA
hardware, and only very bright colors will be visible.

## <a name="lcddimensionsandtimings"></a>  LCD Dimensions and Timings

### Horizontal Dimensions

The drawing time for each dot is 4 CPU cycles.

```
  Visible     240 dots,  57.221 us,    960 cycles - 78% of h-time
  H-Blanking   68 dots,  16.212 us,    272 cycles - 22% of h-time
  Total       308 dots,  73.433 us,   1232 cycles - ca. 13.620 kHz
```

VRAM and Palette RAM may be accessed during H-Blanking. OAM can accessed only
if "H-Blank Interval Free" bit in DISPCNT register is set.

### Vertical Dimensions

```
  Visible (*) 160 lines, 11.749 ms, 197120 cycles - 70% of v-time
  V-Blanking   68 lines,  4.994 ms,  83776 cycles - 30% of v-time
  Total       228 lines, 16.743 ms, 280896 cycles - ca. 59.737 Hz
```

All VRAM, OAM, and Palette RAM may be accessed during V-Blanking.

Note that no H-Blank interrupts are generated within V-Blank period.

### System Clock

The system clock is 16.78MHz (16\*1024\*1024 Hz), one cycle is thus approx.
59.59ns.

(\*) Even though vertical screen size is 160 lines, the upper 8 lines are not
\<really> visible, these lines are covered by a shadow when holding the
GBA orientated towards a light source, the lines are effectively black - and
should not be used to display important information.

### Interlace

The LCD display is using some sort of interlace in which even scanlines are
dimmed in each second frame, and odd scanlines are dimmed in each other frame
(it does always render ALL lines in ALL frames, but half of them are dimmed).

The effect can be seen when displaying some horizontal lines in each second
frame, and hiding them in each other frame: the hardware will randomly show the
lines in dimmed or non-dimmed form (depending on whether the test was started
in an even or odd frame).

Unknown if it's possible to determine the even/off frame state by software (or
possibly to reset the hardware to this or that state by software).

Note: The NDS is applying some sort of frameskip to GBA games, about every 3
seconds there will by a missing (or maybe: inserted) frame, ie. a GBA game that
is updating the display in sync with GBA interlace will get offsync on NDS
consoles.

## <a name="gbasoundcontroller"></a>  GBA Sound Controller

The GBA supplies four 'analogue' sound channels for Tone and Noise (mostly
compatible to CGB sound), as well as two 'digital' sound channels (which can be
used to replay 8bit DMA sample data).

- [GBA Sound Channel 1 - Tone & Sweep](#gbasoundchannel1tonesweep)
- [GBA Sound Channel 2 - Tone](#gbasoundchannel2tone)
- [GBA Sound Channel 3 - Wave Output](#gbasoundchannel3waveoutput)
- [GBA Sound Channel 4 - Noise](#gbasoundchannel4noise)
- [GBA Sound Channel A and B - DMA Sound](#gbasoundchannelaandbdmasound)

- [GBA Sound Control Registers](#gbasoundcontrolregisters)
- [GBA Comparison of CGB and GBA Sound](#gbacomparisonofcgbandgbasound)

The GBA includes only a single (mono) speaker built-in, each channel may be
output to either left and/or right channels by using the external line-out
connector (for stereo headphones, etc).

## <a name="gbasoundchannel1tonesweep"></a>  GBA Sound Channel 1 - Tone & Sweep

### 4000060h - SOUND1CNT\_L (NR10) - Channel 1 Sweep register (R/W)

```
  Bit        Expl.
  0-2   R/W  Number of sweep shift      (n=0-7)
  3     R/W  Sweep Frequency Direction  (0=Increase, 1=Decrease)
  4-6   R/W  Sweep Time; units of 7.8ms (0-7, min=7.8ms, max=54.7ms)
  7-15  -    Not used
```

Sweep is disabled by setting Sweep Time to zero, if so, the direction bit
should be set.

The change of frequency (NR13,NR14) at each shift is calculated by the
following formula where X(0) is initial freq & X(t-1) is last freq:

```
  X(t) = X(t-1) +/- X(t-1)/2^n
```

### 4000062h - SOUND1CNT\_H (NR11, NR12) - Channel 1 Duty/Len/Envelope (R/W)

```
  Bit        Expl.
  0-5   W    Sound length; units of (64-n)/256s  (0-63)
  6-7   R/W  Wave Pattern Duty                   (0-3, see below)
  8-10  R/W  Envelope Step-Time; units of n/64s  (1-7, 0=No Envelope)
  11    R/W  Envelope Direction                  (0=Decrease, 1=Increase)
  12-15 R/W  Initial Volume of envelope          (1-15, 0=No Sound)
```

Wave Duty:

```
  0: 12.5% ( -_______-_______-_______ )
  1: 25%   ( --______--______--______ )
  2: 50%   ( ----____----____----____ ) (normal)
  3: 75%   ( ------__------__------__ )
```

The Length value is used only if Bit 6 in NR14 is set.

### 4000064h - SOUND1CNT\_X (NR13, NR14) - Channel 1 Frequency/Control (R/W)

```
  Bit        Expl.
  0-10  W    Frequency; 131072/(2048-n)Hz  (0-2047)
  11-13 -    Not used
  14    R/W  Length Flag  (1=Stop output when length in NR11 expires)
  15    W    Initial      (1=Restart Sound)
  16-31 -    Not used
```

## <a name="gbasoundchannel2tone"></a>  GBA Sound Channel 2 - Tone

This sound channel works exactly as channel 1, except that it doesn't have a
Tone Envelope/Sweep Register.

### 4000068h - SOUND2CNT\_L (NR21, NR22) - Channel 2 Duty/Length/Envelope (R/W)

### 400006Ah - Not used

### 400006Ch - SOUND2CNT\_H (NR23, NR24) - Channel 2 Frequency/Control (R/W)

For details, refer to channel 1 description.

## <a name="gbasoundchannel3waveoutput"></a>  GBA Sound Channel 3 - Wave Output

This channel can be used to output digital sound, the length of the sample
buffer (Wave RAM) can be either 32 or 64 digits (4bit samples). This sound
channel can be also used to output normal tones when initializing the Wave RAM
by a square wave. This channel doesn't have a volume envelope register.

### 4000070h - SOUND3CNT\_L (NR30) - Channel 3 Stop/Wave RAM select (R/W)

```
  Bit        Expl.
  0-4   -    Not used
  5     R/W  Wave RAM Dimension   (0=One bank/32 digits, 1=Two banks/64 digits)
  6     R/W  Wave RAM Bank Number (0-1, see below)
  7     R/W  Sound Channel 3 Off  (0=Stop, 1=Playback)
  8-15  -    Not used
```

The currently selected Bank Number (Bit 6) will be played back, while
reading/writing to/from wave RAM will address the other (not selected) bank.
When dimension is set to two banks, output will start by replaying the
currently selected bank.

### 4000072h - SOUND3CNT\_H (NR31, NR32) - Channel 3 Length/Volume (R/W)

```
  Bit        Expl.
  0-7   W    Sound length; units of (256-n)/256s  (0-255)
  8-12  -    Not used.
  13-14 R/W  Sound Volume  (0=Mute/Zero, 1=100%, 2=50%, 3=25%)
  15    R/W  Force Volume  (0=Use above, 1=Force 75% regardless of above)
```

The Length value is used only if Bit 6 in NR34 is set.

### 4000074h - SOUND3CNT\_X (NR33, NR34) - Channel 3 Frequency/Control (R/W)

```
  Bit        Expl.
  0-10  W    Sample Rate; 2097152/(2048-n) Hz   (0-2047)
  11-13 -    Not used
  14    R/W  Length Flag  (1=Stop output when length in NR31 expires)
  15    W    Initial      (1=Restart Sound)
  16-31 -    Not used
```

The above sample rate specifies the number of wave RAM digits per second, the
actual tone frequency depends on the wave RAM content, for example:

```
  Wave RAM, single bank 32 digits   Tone Frequency
  FFFFFFFFFFFFFFFF0000000000000000  65536/(2048-n) Hz
  FFFFFFFF00000000FFFFFFFF00000000  131072/(2048-n) Hz
  FFFF0000FFFF0000FFFF0000FFFF0000  262144/(2048-n) Hz
  FF00FF00FF00FF00FF00FF00FF00FF00  524288/(2048-n) Hz
  F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0  1048576/(2048-n) Hz
```

### 4000090h - WAVE\_RAM0\_L - Channel 3 Wave Pattern RAM (W/R)

### 4000092h - WAVE\_RAM0\_H - Channel 3 Wave Pattern RAM (W/R)

### 4000094h - WAVE\_RAM1\_L - Channel 3 Wave Pattern RAM (W/R)

### 4000096h - WAVE\_RAM1\_H - Channel 3 Wave Pattern RAM (W/R)

### 4000098h - WAVE\_RAM2\_L - Channel 3 Wave Pattern RAM (W/R)

### 400009Ah - WAVE\_RAM2\_H - Channel 3 Wave Pattern RAM (W/R)

### 400009Ch - WAVE\_RAM3\_L - Channel 3 Wave Pattern RAM (W/R)

### 400009Eh - WAVE\_RAM3\_H - Channel 3 Wave Pattern RAM (W/R)

This area contains 16 bytes (32 x 4bits) Wave Pattern data which is output by
channel 3. Data is played back ordered as follows: MSBs of 1st byte, followed
by LSBs of 1st byte, followed by MSBs of 2nd byte, and so on - this results in
a confusing ordering when filling Wave RAM in units of 16bit data - ie. samples
would be then located in Bits 4-7, 0-3, 12-15, 8-11.

In the GBA, two Wave Patterns exists (each 32 x 4bits), either one may be
played (as selected in NR30 register), the other bank may be accessed by the
users. After all 32 samples have been played, output of the same bank (or other
bank, as specified in NR30) will be automatically restarted.

Internally, Wave RAM is a giant shift-register, there is no pointer which is
addressing the currently played digit. Instead, the entire 128 bits are
shifted, and the 4 least significant bits are output.

Thus, when reading from Wave RAM, data might have changed its position. And,
when writing to Wave RAM all data should be updated (it'd be no good idea to
assume that old data is still located at the same position where it has been
written to previously).

## <a name="gbasoundchannel4noise"></a>  GBA Sound Channel 4 - Noise

This channel is used to output white noise. This is done by randomly switching
the amplitude between high and low at a given frequency. Depending on the
frequency the noise will appear 'harder' or 'softer'.

It is also possible to influence the function of the random generator, so the
that the output becomes more regular, resulting in a limited ability to output
Tone instead of Noise.

### 4000078h - SOUND4CNT\_L (NR41, NR42) - Channel 4 Length/Envelope (R/W)

```
  Bit        Expl.
  0-5   W    Sound length; units of (64-n)/256s  (0-63)
  6-7   -    Not used
  8-10  R/W  Envelope Step-Time; units of n/64s  (1-7, 0=No Envelope)
  11    R/W  Envelope Direction                  (0=Decrease, 1=Increase)
  12-15 R/W  Initial Volume of envelope          (1-15, 0=No Sound)
  16-31 -    Not used
```

The Length value is used only if Bit 6 in NR44 is set.

### 400007Ch - SOUND4CNT\_H (NR43, NR44) - Channel 4 Frequency/Control (R/W)

The amplitude is randomly switched between high and low at the given frequency.
A higher frequency will make the noise to appear 'softer'.

When Bit 3 is set, the output will become more regular, and some frequencies
will sound more like Tone than Noise.

```
  Bit        Expl.
  0-2   R/W  Dividing Ratio of Frequencies (r)
  3     R/W  Counter Step/Width (0=15 bits, 1=7 bits)
  4-7   R/W  Shift Clock Frequency (s)
  8-13  -    Not used
  14    R/W  Length Flag  (1=Stop output when length in NR41 expires)
  15    W    Initial      (1=Restart Sound)
  16-31 -    Not used
```

Frequency = 524288 Hz / r / 2^(s+1)     ;For r=0 assume r=0.5 instead

### Noise Random Generator (aka Polynomial Counter)

Noise randomly switches between HIGH and LOW levels, the output levels are
calculated by a shift register (X), at the selected frequency, as such:

```
  7bit:  X=X SHR 1, IF carry THEN Out=HIGH, X=X XOR 60h ELSE Out=LOW
  15bit: X=X SHR 1, IF carry THEN Out=HIGH, X=X XOR 6000h ELSE Out=LOW
```

The initial value when (re-)starting the sound is X=40h (7bit) or X=4000h
(15bit). The data stream repeats after 7Fh (7bit) or 7FFFh (15bit) steps.

## <a name="gbasoundchannelaandbdmasound"></a>  GBA Sound Channel A and B - DMA Sound

The GBA contains two DMA sound channels (A and B), each allowing to replay
digital sound (signed 8bit data, ie. -128..+127). Data can be transferred from
INTERNAL memory (not sure if EXTERNAL memory works also ?) to FIFO by using DMA
channel 1 or 2, the sample rate is generated by using one of the Timers.

### 40000A0h - FIFO\_A\_L - Sound A FIFO, Data 0 and Data 1 (W)

### 40000A2h - FIFO\_A\_H - Sound A FIFO, Data 2 and Data 3 (W)

These two registers may receive 32bit (4 bytes) of audio data (Data 0-3, Data 0
being located in least significant byte which is replayed first).

Internally, the capacity of the FIFO is 8 x 32bit (32 bytes), allowing to
buffer a small amount of samples. As the name says (First In First Out), oldest
data is replayed first.

### 40000A4h - FIFO\_B\_L - Sound B FIFO, Data 0 and Data 1 (W)

### 40000A6h - FIFO\_B\_H - Sound B FIFO, Data 2 and Data 3 (W)

Same as above, for Sound B.

### Initializing DMA-Sound Playback

- Select Timer 0 or 1 in SOUNDCNT\_H control register.

- Clear the FIFO.

- Manually write a sample byte to the FIFO.

- Initialize transfer mode for DMA 1 or 2.

- Initialize DMA Sound settings in sound control register.

- Start the timer.

### DMA-Sound Playback Procedure

The pseudo-procedure below is automatically repeated.

```
  If Timer overflows then
    Move 8bit data from FIFO to sound circuit.
    If FIFO contains only 4 x 32bits (16 bytes) then
      Request more data per DMA
      Receive 4 x 32bit (16 bytes) per DMA
    Endif
  Endif
```

This playback mechanism will be repeated forever, regardless of the actual
length of the sample buffer.

### Synchronizing Sample Buffers

The buffer-end may be determined by counting sound Timer IRQs (each sample
byte), or sound DMA IRQs (each 16th sample byte). Both methods would require a
lot of CPU time (IRQ processing), and both would fail if interrupts are
disabled for a longer period.

Better solutions would be to synchronize the sample rate/buffer length with
V-blanks, or to use a second timer (in count up/slave mode) which produces an
IRQ after the desired number of samples.

### The Sample Rate

The GBA hardware does internally re-sample all sound output to 32.768kHz
(default SOUNDBIAS setting). It'd thus do not make much sense to use higher
DMA/Timer rates. Best re-sampling accuracy can be gained by using DMA/Timer
rates of 32.768kHz, 16.384kHz, or 8.192kHz (ie. fragments of the physical
output rate).

## <a name="gbasoundcontrolregisters"></a>  GBA Sound Control Registers

### 4000080h - SOUNDCNT\_L (NR50, NR51) - Channel L/R Volume/Enable (R/W)

```
  Bit        Expl.
  0-2   R/W  Sound 1-4 Master Volume RIGHT (0-7)
  3     -    Not used
  4-6   R/W  Sound 1-4 Master Volume LEFT (0-7)
  7     -    Not used
  8-11  R/W  Sound 1-4 Enable Flags RIGHT (each Bit 8-11, 0=Disable, 1=Enable)
  12-15 R/W  Sound 1-4 Enable Flags LEFT (each Bit 12-15, 0=Disable, 1=Enable)
```

### 4000082h - SOUNDCNT\_H (GBA only) - DMA Sound Control/Mixing (R/W)

```
  Bit        Expl.
  0-1   R/W  Sound # 1-4 Volume   (0=25%, 1=50%, 2=100%, 3=Prohibited)
  2     R/W  DMA Sound A Volume   (0=50%, 1=100%)
  3     R/W  DMA Sound B Volume   (0=50%, 1=100%)
  4-7   -    Not used
  8     R/W  DMA Sound A Enable RIGHT (0=Disable, 1=Enable)
  9     R/W  DMA Sound A Enable LEFT  (0=Disable, 1=Enable)
  10    R/W  DMA Sound A Timer Select (0=Timer 0, 1=Timer 1)
  11    W?   DMA Sound A Reset FIFO   (1=Reset)
  12    R/W  DMA Sound B Enable RIGHT (0=Disable, 1=Enable)
  13    R/W  DMA Sound B Enable LEFT  (0=Disable, 1=Enable)
  14    R/W  DMA Sound B Timer Select (0=Timer 0, 1=Timer 1)
  15    W?   DMA Sound B Reset FIFO   (1=Reset)
```

### 4000084h - SOUNDCNT\_X (NR52) - Sound on/off (R/W)

Bits 0-3 are automatically set when starting sound output, and are
automatically cleared when a sound ends. (Ie. when the length expires, as far
as length is enabled. The bits are NOT reset when an volume envelope ends.)

```
  Bit        Expl.
  0     R    Sound 1 ON flag (Read Only)
  1     R    Sound 2 ON flag (Read Only)
  2     R    Sound 3 ON flag (Read Only)
  3     R    Sound 4 ON flag (Read Only)
  4-6   -    Not used
  7     R/W  PSG/FIFO Master Enable (0=Disable, 1=Enable) (Read/Write)
  8-31  -    Not used
```

While Bit 7 is cleared, both PSG and FIFO sounds are disabled, and all PSG
registers at 4000060h..4000081h are reset to zero (and must be re-initialized
after re-enabling sound). However, registers 4000082h and 4000088h are kept
read/write-able (of which, 4000082h has no function when sound is off, whilst
4000088h does work even when sound is off).

### 4000088h - SOUNDBIAS - Sound PWM Control (R/W, see below)

This register controls the final sound output. The default setting is 0200h, it
is normally not required to change this value.

```
  Bit        Expl.
  0     -    Not used
  1-9   R/W  Bias Level (Default=100h, converting signed samples into unsigned)
  10-13 -    Not used
  14-15 R/W  Amplitude Resolution/Sampling Cycle (Default=0, see below)
  16-31 -    Not used
```

Amplitude Resolution/Sampling Cycle (0-3):

```
  0  9bit / 32.768kHz   (Default, best for DMA channels A,B)
  1  8bit / 65.536kHz
  2  7bit / 131.072kHz
  3  6bit / 262.144kHz  (Best for PSG channels 1-4)
```

For more information on this register, read the descriptions below.

### 400008Ch - Not used

### 400008Eh - Not used

### Max Output Levels (with max volume settings)

Each of the two FIFOs can span the FULL output range (+/-200h).

Each of the four PSGs can span one QUARTER of the output range (+/-80h).

The current output levels of all six channels are added together by hardware.

So together, the FIFOs and PSGs, could reach THRICE the range (+/-600h).

The BIAS value is added to that signed value. With default BIAS (200h), the
possible range becomes -400h..+800h, however, values that exceed the unsigned
10bit output range of 0..3FFh are clipped to MinMax(0,3FFh).

### Resampling to 32.768kHz / 9bit (default)

The PSG channels 1-4 are internally generated at 262.144kHz, and DMA sound A-B
could be theoretically generated at timer rates up to 16.78MHz. However, the
final sound output is resampled to a rate of 32.768kHz, at 9bit depth (the
above 10bit value, divided by two). If necessary, rates higher than 32.768kHz
can be selected in the SOUNDBIAS register, that would result in a depth smaller
than 9bit though.

### PWM (Pulse Width Modulation) Output 16.78MHz / 1bit

Okay, now comes the actual output. The GBA can output only two voltages (low
and high), these 'bits' are output at system clock speed (16.78MHz). If using
the default 32.768kHz sampling rate, then 512 bits are output per sample
(512\*32K=16M). Each sample value (9bit range, N=0..511), would be then output
as N low bits, followed by 512-N high bits. The resulting 'noise' is smoothed
down by capacitors, by the speaker, and by human hearing, so that it will
effectively sound like clean D/A converted 9bit voltages at 32kHz sampling
rate.

### Changing the BIAS Level

Normally use 200h for clean sound output. A value of 000h might make sense
during periods when no sound is output (causing the PWM circuit to output
low-bits only, which is eventually reducing the power consumption, and/or
preventing 32KHz noise). Note: Using the SoundBias function (SWI 19h) allows to
change the level by slowly incrementing or decrementing it (without hard
scratch noise).

### Low Power Mode

When not using sound output, power consumption can be reduced by setting both
4000084h (PSG/FIFO) and 4000088h (BIAS) to zero.

## <a name="gbacomparisonofcgbandgbasound"></a>  GBA Comparison of CGB and GBA Sound

The GBA sound controller is mostly the same than that of older monochrome
gameboy and CGB. The following changes have been done:

### New Sound Channels

Two new sound channels have been added that may be used to replay 8bit digital
sound. Sample rate and sample data must be supplied by using a Timer and a DMA
channel.

### New Control Registers

The SOUNDCNT\_H register controls the new DMA channels - as well as mixing with
the four old channels. The SOUNDBIAS register controls the final sound output.

### Sound Channel 3 Changes

The length of the Wave RAM is doubled by dividing it into two banks of 32
digits each, either one or both banks may be replayed (one after each other),
for details check NR30 Bit 5-6. Optionally, the sound may be output at 75%
volume, for details check NR32 Bit 7.

### Changed Control Registers

NR50 is not supporting Vin signals (that's been an external sound input from
cartridge).

### Changed I/O Addresses

The GBAs sound register are located at 04000060-040000AE instead of at
FF10-FF3F as in CGB and monochrome gameboy. However, note that there have been
new blank spaces inserted between some of the separate registers - therefore it
is NOT possible to port CGB software to GBA just by changing the sound base
address.

### Accessing I/O Registers

In some cases two of the old 8bit registers are packed into a 16bit register
and may be accessed as such.

## <a name="gbatimers"></a>  GBA Timers

The GBA includes four incrementing 16bit timers.

Timer 0 and 1 can be used to supply the sample rate for DMA sound channel A
and/or B.

### 4000100h - TM0CNT\_L - Timer 0 Counter/Reload (R/W)

### 4000104h - TM1CNT\_L - Timer 1 Counter/Reload (R/W)

### 4000108h - TM2CNT\_L - Timer 2 Counter/Reload (R/W)

### 400010Ch - TM3CNT\_L - Timer 3 Counter/Reload (R/W)

Writing to these registers initializes the \<reload> value (but does not
directly affect the current counter value). Reading returns the current
\<counter> value (or the recent/frozen counter value if the timer has been
stopped).

The reload value is copied into the counter only upon following two situations:
Automatically upon timer overflows, or when the timer start bit becomes changed
from 0 to 1.

Note: When simultaneously changing the start bit from 0 to 1, and setting the
reload value at the same time (by a single 32bit I/O operation), then the newly
written reload value is recognized as new counter value.

### 4000102h - TM0CNT\_H - Timer 0 Control (R/W)

### 4000106h - TM1CNT\_H - Timer 1 Control (R/W)

### 400010Ah - TM2CNT\_H - Timer 2 Control (R/W)

### 400010Eh - TM3CNT\_H - Timer 3 Control (R/W)

```
  Bit   Expl.
  0-1   Prescaler Selection (0=F/1, 1=F/64, 2=F/256, 3=F/1024)
  2     Count-up Timing   (0=Normal, 1=See below)  ;Not used in TM0CNT_H
  3-5   Not used
  6     Timer IRQ Enable  (0=Disable, 1=IRQ on Timer overflow)
  7     Timer Start/Stop  (0=Stop, 1=Operate)
  8-15  Not used
```

When Count-up Timing is enabled, the prescaler value is ignored, instead the
time is incremented each time when the previous counter overflows. This
function cannot be used for Timer 0 (as it is the first timer).

F = System Clock (16.78MHz).

## <a name="gbadmatransfers"></a>  GBA DMA Transfers

### Overview

The GBA includes four DMA channels, the highest priority is assigned to DMA0,
followed by DMA1, DMA2, and DMA3. DMA Channels with lower priority are paused
until channels with higher priority have completed.

The CPU is paused when DMA transfers are active, however, the CPU is operating
during the periods when Sound/Blanking DMA transfers are paused.

### Special features of the separate DMA channels

DMA0 - highest priority, best for timing critical transfers (eg. HBlank DMA).

DMA1 and DMA2 - can be used to feed digital sample data to the Sound FIFOs.

DMA3 - can be used to write to Game Pak ROM/FlashROM (but not GamePak SRAM).

Beside for that, each DMA 0-3 may be used for whatever general purposes.

### 40000B0h,0B2h - DMA0SAD - DMA 0 Source Address (W) (internal memory)

### 40000BCh,0BEh - DMA1SAD - DMA 1 Source Address (W) (any memory)

### 40000C8h,0CAh - DMA2SAD - DMA 2 Source Address (W) (any memory)

### 40000D4h,0D6h - DMA3SAD - DMA 3 Source Address (W) (any memory)

The most significant address bits are ignored, only the least significant 27 or
28 bits are used (max 07FFFFFFh internal memory, or max 0FFFFFFFh any memory -
except SRAM ?!).

### 40000B4h,0B6h - DMA0DAD - DMA 0 Destination Address (W) (internal memory)

### 40000C0h,0C2h - DMA1DAD - DMA 1 Destination Address (W) (internal memory)

### 40000CCh,0CEh - DMA2DAD - DMA 2 Destination Address (W) (internal memory)

### 40000D8h,0DAh - DMA3DAD - DMA 3 Destination Address (W) (any memory)

The most significant address bits are ignored, only the least significant 27 or
28 bits are used (max. 07FFFFFFh internal memory or 0FFFFFFFh any memory -
except SRAM ?!).

### 40000B8h - DMA0CNT\_L - DMA 0 Word Count (W) (14 bit, 1..4000h)

### 40000C4h - DMA1CNT\_L - DMA 1 Word Count (W) (14 bit, 1..4000h)

### 40000D0h - DMA2CNT\_L - DMA 2 Word Count (W) (14 bit, 1..4000h)

### 40000DCh - DMA3CNT\_L - DMA 3 Word Count (W) (16 bit, 1..10000h)

Specifies the number of data units to be transferred, each unit is 16bit or
32bit depending on the transfer type, a value of zero is treated as max length
(ie. 4000h, or 10000h for DMA3).

### 40000BAh - DMA0CNT\_H - DMA 0 Control (R/W)

### 40000C6h - DMA1CNT\_H - DMA 1 Control (R/W)

### 40000D2h - DMA2CNT\_H - DMA 2 Control (R/W)

### 40000DEh - DMA3CNT\_H - DMA 3 Control (R/W)

```
  Bit   Expl.
  0-4   Not used
  5-6   Dest Addr Control  (0=Increment,1=Decrement,2=Fixed,3=Increment/Reload)
  7-8   Source Adr Control (0=Increment,1=Decrement,2=Fixed,3=Prohibited)
  9     DMA Repeat                   (0=Off, 1=On) (Must be zero if Bit 11 set)
  10    DMA Transfer Type            (0=16bit, 1=32bit)
  11    Game Pak DRQ  - DMA3 only -  (0=Normal, 1=DRQ <from> Game Pak, DMA3)
  12-13 DMA Start Timing  (0=Immediately, 1=VBlank, 2=HBlank, 3=Special)
          The 'Special' setting (Start Timing=3) depends on the DMA channel:
          DMA0=Prohibited, DMA1/DMA2=Sound FIFO, DMA3=Video Capture
  14    IRQ upon end of Word Count   (0=Disable, 1=Enable)
  15    DMA Enable                   (0=Off, 1=On)
```

After changing the Enable bit from 0 to 1, wait 2 clock cycles before accessing
any DMA related registers.

When accessing OAM (7000000h) or OBJ VRAM (6010000h) by HBlank Timing, then the
"H-Blank Interval Free" bit in DISPCNT register must be set.

### Source and Destination Address and Word Count Registers

The SAD, DAD, and CNT\_L registers are holding the initial start addresses, and
initial length. The hardware does NOT change the content of these registers
during or after the transfer.

The actual transfer takes place by using internal pointer/counter registers.
The initial values are copied into internal regs under the following
circumstances:

Upon DMA Enable (Bit 15) changing from 0 to 1: Reloads SAD, DAD, CNT\_L.

Upon Repeat: Reloads CNT\_L, and optionally DAD (Increment+Reload).

### DMA Repeat bit

If the Repeat bit is cleared: The Enable bit is automatically cleared after the
specified number of data units has been transferred.

If the Repeat bit is set: The Enable bit remains set after the transfer, and
the transfer will be restarted each time when the Start condition (eg. HBlank,
Fifo) becomes true. The specified number of data units is transferred
\<each> time when the transfer is (re-)started. The transfer will be
repeated forever, until it gets stopped by software.

### Sound DMA (FIFO Timing Mode) (DMA1 and DMA2 only)

In this mode, the DMA Repeat bit must be set, and the destination address must
be FIFO\_A (040000A0h) or FIFO\_B (040000A4h).

Upon DMA request from sound controller, 4 units of 32bits (16 bytes) are
transferred (both Word Count register and DMA Transfer Type bit are ignored).
The destination address will not be incremented in FIFO mode.

Keep in mind that DMA channels of higher priority may offhold sound DMA. For
example, when using a 64 kHz sample rate, 16 bytes of sound DMA data are
requested each 0.25ms (4 kHz), at this time another 16 bytes are still in the
FIFO so that there's still 0.25ms time to satisfy the DMA request. Thus DMAs
with higher priority should not be operated for longer than 0.25ms. (This
problem does not arise for HBlank transfers as HBlank time is limited to
16.212us.)

### Game Pak DMA

Only DMA 3 may be used to transfer data to/from Game Pak ROM or Flash ROM - it
cannot access Game Pak SRAM though (as SRAM data bus is limited to 8bit units).
In normal mode, DMA is requested as long until Word Count becomes zero. When
setting the 'Game Pack DRQ' bit, then the cartridge must contain an external
circuit which outputs a /DREQ signal. Note that there is only one pin for /DREQ
and /IREQ, thus the cartridge may not supply /IREQs while using DRQ mode.

### Video Capture Mode (DMA3 only)

Intended to copy a bitmap from memory (or from external hardware/camera) to
VRAM. When using this transfer mode, set the repeat bit, and write the number
of data units (per scanline) to the word count register. Capture works similar
like HBlank DMA, however, the transfer is started when VCOUNT=2, it is then
repeated each scanline, and it gets stopped when VCOUNT=162.

### Transfer End

The DMA Enable flag (Bit 15) is automatically cleared upon completion of the
transfer. The user may also clear this bit manually in order to stop the
transfer (obviously this is possible for Sound/Blanking DMAs only, in all other
cases the CPU is stopped until the transfer completes by itself).

### Transfer Rate/Timing

Except for the first data unit, all units are transferred by sequential reads
and writes. For n data units, the DMA transfer time is:

```
  2N+2(n-1)S+xI
```

Of which, 1N+(n-1)S are read cycles, and the other 1N+(n-1)S are write cycles,
actual number of cycles depends on the waitstates and bus-width of the source
and destination areas (as described in CPU Instruction Cycle Times chapter).
Internal time for DMA processing is 2I (normally), or 4I (if both source and
destination are in gamepak memory area).

DMA lockup when stopping while starting ???

Capture delayed, Capture Enable=AutoCleared ???

## <a name="gbacommunicationports"></a>  GBA Communication Ports

The GBAs Serial Port may be used in various different communication modes.
Normal mode may exchange data between two GBAs (or to transfer data from master
GBA to several slave GBAs in one-way direction).

Multi-player mode may exchange data between up to four GBAs. UART mode works
much like a RS232 interface. JOY Bus mode uses a standardized Nintendo
protocol. And General Purpose mode allows to mis-use the 'serial' port as
bi-directional 4bit parallel port.

Note: The Nintendo DS does not include a Serial Port.

- [SIO Normal Mode](#sionormalmode)
- [SIO Multi-Player Mode](#siomultiplayermode)
- [SIO UART Mode](#siouartmode)
- [SIO JOY BUS Mode](#siojoybusmode)
- [SIO General-Purpose Mode](#siogeneralpurposemode)
- [SIO Control Registers Summary](#siocontrolregisterssummary)

### Wireless Adapter

- [GBA Wireless Adapter](#gbawirelessadapter)

### Infrared Communication Adapters

Even though early GBA prototypes have been intended to support IR
communication, this feature has been removed.

However, Nintendo is apparently considering to provide an external IR adapter
(to be connected to the SIO connector, being accessed in General Purpose mode).

Also, it'd be theoretically possible to include IR ports built-in in game
cartridges (as done for some older 8bit/monochrome Hudson games).

## <a name="sionormalmode"></a>  SIO Normal Mode

This mode is used to communicate between two units.

Transfer rates of 256Kbit/s or 2Mbit/s can be selected, however, the fast
2Mbit/s is intended ONLY for special hardware expansions that are DIRECTLY
connected to the GBA link port (ie. without a cable being located between the
GBA and expansion hardware). In normal cases, always use 256Kbit/s transfer
rate which provides stable results.

Transfer lengths of 8bit or 32bit may be used, the 8bit mode is the same as for
older DMG/CGB gameboys, however, the voltages for "GBA cartridges in GBAs" are
different as for "DMG/CGB cartridges in DMG/CGB/GBAs", ie. it is not possible
to communicate between DMG/CGB games and GBA games.

### 4000134h - RCNT (R) - Mode Selection, in Normal/Multiplayer/UART modes (R/W)

```
  Bit   Expl.
  0-3   Undocumented (current SC,SD,SI,SO state, as for General Purpose mode)
  4-8   Not used     (Should be 0, bits are read/write-able though)
  9-13  Not used     (Always 0, read only)
  14    Not used     (Should be 0, bit is read/write-able though)
  15    Must be zero (0) for Normal/Multiplayer/UART modes
```

### 4000128h - SIOCNT - SIO Control, usage in NORMAL Mode (R/W)

```
  Bit   Expl.
  0     Shift Clock (SC)        (0=External, 1=Internal)
  1     Internal Shift Clock    (0=256KHz, 1=2MHz)
  2     SI State (opponents SO) (0=Low, 1=High/None) --- (Read Only)
  3     SO during inactivity    (0=Low, 1=High) (applied ONLY when Bit7=0)
  4-6   Not used                (Read only, always 0 ?)
  7     Start Bit               (0=Inactive/Ready, 1=Start/Active)
  8-11  Not used                (R/W, should be 0)
  12    Transfer Length         (0=8bit, 1=32bit)
  13    Must be "0" for Normal Mode
  14    IRQ Enable              (0=Disable, 1=Want IRQ upon completion)
  15    Not used                (Read only, always 0)
```

The Start bit is automatically reset when the transfer completes, ie. when all
8 or 32 bits are transferred, at that time an IRQ may be generated.

### 400012Ah - SIODATA8 - SIO Normal Communication 8bit Data (R/W)

For 8bit normal mode. Contains 8bit data (only lower 8bit are used). Outgoing
data should be written to this register before starting the transfer. During
transfer, transmitted bits are shifted-out (MSB first), and received bits are
shifted-in simultaneously. Upon transfer completion, the register contains the
received 8bit value.

### 4000120h - SIODATA32\_L - SIO Normal Communication lower 16bit data (R/W)

### 4000122h - SIODATA32\_H - SIO Normal Communication upper 16bit data (R/W)

Same as above SIODATA8, for 32bit normal transfer mode respectively.

SIOCNT/RCNT must be set to 32bit normal mode \<before> writing to
SIODATA32.

### Initialization

First, initialize RCNT register. Second, set mode/clock bits in SIOCNT with
startbit cleared. For master: select internal clock, and (in most cases)
specify 256KHz as transfer rate. For slave: select external clock, the local
transfer rate selection is then ignored, as the transfer rate is supplied by
the remote GBA (or other computer, which might supply custom transfer rates).

Third, set the startbit in SIOCNT with mode/clock bits unchanged.

### Recommended Communication Procedure for SLAVE unit (external clock)

- Initialize data which is to be sent to master.

- Set Start flag.

- Set SO to LOW to indicate that master may start now.

- Wait for IRQ (or for Start bit to become zero). (Check timeout here!)

- Set SO to HIGH to indicate that we are not ready.

- Process received data.

- Repeat procedure if more data is to be transferred.

(or is so=high done automatically? would be fine - more stable - otherwise
master may still need delay)

### Recommended Communication Procedure for SLAVE unit (external clock)

- Initialize data which is to be sent to master.

- Set Start=0 and SO=0 (SO=LOW indicates that slave is (almost) ready).

- Set Start=1 and SO=1 (SO=HIGH indicates not ready, applied after transfer).

```
  (Expl. Old SO=LOW kept output until 1st clock bit received).
  (Expl. New SO=HIGH is automatically output at transfer completion).
```

- Set SO to LOW to indicate that master may start now.

- Wait for IRQ (or for Start bit to become zero). (Check timeout here!)

- Process received data.

- Repeat procedure if more data is to be transferred.

### Recommended Communication Procedure for MASTER unit (internal clock)

- Initialize data which is to be sent to slave.

- Wait for SI to become LOW (slave ready). (Check timeout here!)

- Set Start flag.

- Wait for IRQ (or for Start bit to become zero).

- Process received data.

- Repeat procedure if more data is to be transferred.

### Cable Protocol

During inactive transfer, the shift clock (SC) is high. The transmit (SO) and
receive (SI) data lines may be manually controlled as described above.

When master sends SC=LOW, each master and slave must output the next outgoing
data bit to SO. When master sends SC=HIGH, each master and slave must read out
the opponents data bit from SI. This is repeated for each of the 8 or 32 bits,
and when completed SC will be kept high again.

### Transfer Rates

Either 256KHz or 2MHz rates can be selected for SC, so max 32KBytes (256Kbit)
or 128KBytes (2Mbit) can be transferred per second. However, the software must
process each 8bit or 32bit of transmitted data separately, so the actual
transfer rate will be reduced by the time spent on handling each data unit.

Only 256KHz provides stable results in most cases (such like when linking
between two GBAs). The 2MHz rate is intended for special expansion hardware
(with very short wires) only.

### Using Normal mode for One-Way Multiplayer communication

When using normal mode with multiplay-cables, data isn't exchanged between
first and second GBA as usually. Instead, data is shifted from first to last
GBA (the first GBA receives zero, because master SI is shortcut to GND).

This behaviour may be used for fast ONE-WAY data transfer from master to all
other GBAs. For example (3 GBAs linked):

```
  Step         Sender      1st Recipient   2nd Recipient
  Transfer 1:  DATA #0 --> UNDEF      -->  UNDEF     -->
  Transfer 2:  DATA #1 --> DATA #0    -->  UNDEF     -->
  Transfer 3:  DATA #2 --> DATA #1    -->  DATA #0   -->
  Transfer 4:  DATA #3 --> DATA #2    -->  DATA #1   -->
```

The recipients should not output any own data, instead they should forward the
previously received data to the next recipient during next transfer (just keep
the incoming data unmodified in the data register).

Due to the delayed forwarding, 2nd recipient should ignore the first incoming
data. After the last transfer, the sender must send one (or more) dummy data
unit(s), so that the last data is forwarded to the 2nd (or further)
recipient(s).

## <a name="siomultiplayermode"></a>  SIO Multi-Player Mode

Multi-Player mode can be used to communicate between up to 4 units.

### 4000134h - RCNT (R) - Mode Selection, in Normal/Multiplayer/UART modes (R/W)

```
  Bit   Expl.
  0-3   Undocumented (current SC,SD,SI,SO state, as for General Purpose mode)
  4-8   Not used     (Should be 0, bits are read/write-able though)
  9-13  Not used     (Always 0, read only)
  14    Not used     (Should be 0, bit is read/write-able though)
  15    Must be zero (0) for Normal/Multiplayer/UART modes
```

Note: Even though undocumented, many Nintendo games are using Bit 0 to test
current SC state in multiplay mode.

### 4000128h - SIOCNT - SIO Control, usage in MULTI-PLAYER Mode (R/W)

```
  Bit   Expl.
  0-1   Baud Rate     (0-3: 9600,38400,57600,115200 bps)
  2     SI-Terminal   (0=Parent, 1=Child)                  (Read Only)
  3     SD-Terminal   (0=Bad connection, 1=All GBAs Ready) (Read Only)
  4-5   Multi-Player ID     (0=Parent, 1-3=1st-3rd child)  (Read Only)
  6     Multi-Player Error  (0=Normal, 1=Error)            (Read Only)
  7     Start/Busy Bit      (0=Inactive, 1=Start/Busy) (Read Only for Slaves)
  8-11  Not used            (R/W, should be 0)
  12    Must be "0" for Multi-Player mode
  13    Must be "1" for Multi-Player mode
  14    IRQ Enable          (0=Disable, 1=Want IRQ upon completion)
  15    Not used            (Read only, always 0)
```

The ID Bits are undefined until the first transfer has completed.

### 400012Ah - SIOMLT\_SEND - Data Send Register (R/W)

Outgoing data (16 bit) which is to be sent to the other GBAs.

### 4000120h - SIOMULTI0 - SIO Multi-Player Data 0 (Parent) (R/W)

### 4000122h - SIOMULTI1 - SIO Multi-Player Data 1 (1st child) (R/W)

### 4000124h - SIOMULTI2 - SIO Multi-Player Data 2 (2nd child) (R/W)

### 4000126h - SIOMULTI3 - SIO Multi-Player Data 3 (3rd child) (R/W)

These registers are automatically reset to FFFFh upon transfer start.

After transfer, these registers contain incoming data (16bit each) from all
remote GBAs (if any / otherwise still FFFFh), as well as the local outgoing
SIOMLT\_SEND data.

Ie. after the transfer, all connected GBAs will contain the same values in
their SIOMULTI0-3 registers.

### Initialization

- Initialize RCNT Bit 14-15 and SIOCNT Bit 12-13 to select Multi-Player mode.

- Read SIOCNT Bit 3 to verify that all GBAs are in Multi-Player mode.

- Read SIOCNT Bit 2 to detect whether this is the Parent/Master unit.

### Recommended Transmission Procedure

- Write outgoing data to SIODATA\_SEND.

- Master must set Start bit.

- All units must process received data in SIOMULTI0-3 when transfer completed.

- After the first successful transfer, ID Bits in SIOCNT are valid.

- If more data is to be transferred, repeat procedure.

The parent unit blindly sends data regardless of whether childs have already
processed old data/supplied new data. So, parent unit might be required to
insert delays between each transfer, and/or perform error checking.

Also, slave units may signalize that they are not ready by temporarily
switching into another communication mode (which does not output SD High, as
Multi-Player mode does during inactivity).

### Transfer Protocol

Beginning

- The masters SI pin is always LOW.

- When all GBAs are in Multiplayer mode (ready) SD is HIGH.

- When master starts the transfer, it sets SC=LOW, slaves receive Busy bit.

Step A

- ID Bits in master unit are set to 0.

- Master outputs Startbit (LOW), 16bit Data, Stopbit (HIGH) through SD.

- This data is written to SIOMULTI0 of all GBAs (including master).

- Master forwards LOW from its SO to 1st childs SI.

- Transfer ends if next child does not output data after certain time.

Step B

- ID Bits in 1st child unit are set to 1.

- 1st Child outputs Startbit (LOW), 16bit Data, Stopbit (HIGH) through SD.

- This data is written to SIOMULTI1 of all GBAs (including 1st child).

- 1st child forwards LOW from its SO to 2nd childs SI.

- Transfer ends if next child does not output data after certain time.

Step C

- ID Bits in 2nd child unit are set to 2.

- 2nd Child outputs Startbit (LOW), 16bit Data, Stopbit (HIGH) through SD.

- This data is written to SIOMULTI2 of all GBAs (including 2nd child).

- 2nd child forwards LOW from its SO to 3rd childs SI.

- Transfer ends if next child does not output data after certain time.

Step D

- ID Bits in 3rd child unit are set to 3.

- 3rd Child outputs Startbit (LOW), 16bit Data, Stopbit (HIGH) through SD.

- This data is written to SIOMULTI3 of all GBAs (including 3rd child).

- Transfer ends (this was the last child).

Transfer end

- Master sets SC=HIGH, all GBAs set SO=HIGH.

- The Start/Busy bits of all GBAs are automatically cleared.

- Interrupts are requested in all GBAs (as far as enabled).

### Error Bit

This bit is set when a slave did not receive SI=LOW even though SC=LOW
signalized a transfer (this might happen when connecting more than 4 GBAs, or
when the previous child is not connected). Also, the bit is set when a Stopbit
wasn't HIGH.

The error bit may be undefined during active transfer - read only after
transfer completion (the transfer continues and completes as normal even if
errors have occurred for some or all GBAs).

Don't know: The bit is automatically reset/initialized with each transfer, or
must be manually reset?

### Transmission Time

The transmission time depends on the selected Baud rate. And on the amount of
Bits (16 data bits plus start/stop bits for each GBA), delays between data for
each GBA, plus final timeout (if less than 4 GBAs). That is, depending on the
number of connected GBAs:

```
  GBAs    Bits    Delays   Timeout
  1       18      None     Yes
  2       36      1        Yes
  3       54      2        Yes
  4       72      3        None
```

(The average Delay and Timeout periods are unknown?)

Above is not counting the additional CPU time that must be spent on initiating
and processing each transfer.

### Fast One-Way Transmission

Beside for the actual SIO Multiplayer mode, you can also use SIO Normal mode
for fast one-way data transfer from Master unit to all Child unit(s). See
chapter about SIO Normal mode for details.

## <a name="siouartmode"></a>  SIO UART Mode

This mode works much like a RS232 port, however, the voltages are unknown,
probably 0/3V rather than +/-12V ?. SI and SO are data lines (with crossed
wires), SC and SD signalize Clear to Send (with crossed wires also, which
requires special cable when linking between two GBAs ?)

### 4000134h - RCNT (R) - Mode Selection, in Normal/Multiplayer/UART modes (R/W)

```
  Bit   Expl.
  0-3   Undocumented (current SC,SD,SI,SO state, as for General Purpose mode)
  4-8   Not used     (Should be 0, bits are read/write-able though)
  9-13  Not used     (Always 0, read only)
  14    Not used     (Should be 0, bit is read/write-able though)
  15    Must be zero (0) for Normal/Multiplayer/UART modes
```

### 4000128h - SCCNT\_L - SIO Control, usage in UART Mode (R/W)

```
  Bit   Expl.
  0-1   Baud Rate  (0-3: 9600,38400,57600,115200 bps)
  2     CTS Flag   (0=Send always/blindly, 1=Send only when SC=LOW)
  3     Parity Control (0=Even, 1=Odd)
  4     Send Data Flag      (0=Not Full,  1=Full)    (Read Only)
  5     Receive Data Flag   (0=Not Empty, 1=Empty)   (Read Only)
  6     Error Flag          (0=No Error,  1=Error)   (Read Only)
  7     Data Length         (0=7bits,   1=8bits)
  8     FIFO Enable Flag    (0=Disable, 1=Enable)
  9     Parity Enable Flag  (0=Disable, 1=Enable)
  10    Send Enable Flag    (0=Disable, 1=Enable)
  11    Receive Enable Flag (0=Disable, 1=Enable)
  12    Must be "1" for UART mode
  13    Must be "1" for UART mode
  14    IRQ Enable          (0=Disable, 1=IRQ when any Bit 4/5/6 become set)
  15    Not used            (Read only, always 0)
```

### 400012Ah - SIODATA8 - usage in UART Mode (R/W)

Addresses the send/receive shift register, or (when FIFO is used) the
send/receive FIFO. In either case only the lower 8bit of SIODATA8 are used, the
upper 8bit are not used.

The send/receive FIFO may store up to four 8bit data units each. For example,
while 1 unit is still transferred from the send shift register, it is possible
to deposit another 4 units in the send FIFO, which are then automatically moved
to the send shift register one after each other.

### Send/Receive Enable, CTS Feedback

The receiver outputs SD=LOW (which is input as SC=LOW at the remote side) when
it is ready to receive data (that is, when Receive Enable is set, and the
Receive shift register (or receive FIFO) isn't full.

When CTS flag is set to always/blindly, then the sender transmits data
immediately when Send Enable is set, otherwise data is transmitted only when
Send Enable is set and SC is LOW.

### Error Flag

The error flag is set when a bad stop bit has been received (stop bit must be
0), when a parity error has occurred (if enabled), or when new data has been
completely received while the receive data register (or receive FIFO) is
already full.

The error flag is automatically reset when reading from SIOCNT register.

### Init & Initback

The content of the FIFO is reset when FIFO is disabled in UART mode, thus, when
entering UART mode initially set FIFO=disabled.

The Send/Receive enable bits must be reset before switching from UART mode into
another SIO mode!

## <a name="siojoybusmode"></a>  SIO JOY BUS Mode

This communication mode uses Nintendo's standardized JOY Bus protocol. When
using this communication mode, the GBA is always operated as SLAVE!

In this mode, SI and SO pins are data lines (apparently synchronized by
Start/Stop bits?), SC and SD are set to low (including during active
transfer?), the transfer rate is unknown?

### 4000134h - RCNT (R) - Mode Selection, in JOY BUS mode (R/W)

```
  Bit   Expl.
  0-3   Undocumented (current SC,SD,SI,SO state, as for General Purpose mode)
  4-8   Not used     (Should be 0, bits are read/write-able though)
  9-13  Not used     (Always 0, read only)
  14    Must be "1" for JOY BUS Mode
  15    Must be "1" for JOY BUS Mode
```

### 4000128h - SIOCNT - SIO Control, not used in JOY BUS Mode

This register is not used in JOY BUS mode.

### 4000140h - JOYCNT - JOY BUS Control Register (R/W)

```
  Bit   Expl.
  0     Device Reset Flag     (Command FFh)          (Read/Acknowledge)
  1     Receive Complete Flag (Command 14h or 15h?)  (Read/Acknowledge)
  2     Send Complete Flag    (Command 15h or 14h?)  (Read/Acknowledge)
  3-5   Not used
  6     IRQ when receiving a Device Reset Command  (0=Disable, 1=Enable)
  7-31  Not used
```

Bit 0-2 are working much like the bits in the IF register: Write a "1" bit to
reset (acknowledge) the respective bit.

UNCLEAR: Interrupts can be requested for Send/Receive commands also?

### 4000150h - JOY\_RECV\_L - Receive Data Register low (R/W)

### 4000152h - JOY\_RECV\_H - Receive Data Register high (R/W)

### 4000154h - JOY\_TRANS\_L - Send Data Register low (R/W)

### 4000156h - JOY\_TRANS\_H - Send Data Register high (R/W)

Send/receive data registers.

### 4000158h - JOYSTAT - Receive Status Register (R/W)

```
  Bit   Expl.
  0     Not used
  1     Receive Status Flag   (0=Remote GBA is/was receiving) (Read Only?)
  2     Not used
  3     Send Status Flag      (1=Remote GBA is/was sending)   (Read Only?)
  4-5   General Purpose Flag  (Not assigned, may be used for whatever purpose)
  6-31  Not used
```

Bit 1 is automatically set when writing to local JOY\_TRANS.

Bit 3 is automatically reset when reading from local JOY\_RECV.

Below are the four possible commands which can be received by the GBA. Note
that the GBA (slave) cannot send any commands itself, all it can do is to read
incoming data, and to provide 'reply' data which may (or may not) be read out
by the master unit.

### Command FFh - Device Reset

```
  Receive FFh (Command)
  Send    00h (GBA Type number LSB (or MSB?))
  Send    04h (GBA Type number MSB (or LSB?))
  Send    XXh (lower 8bits of SIOSTAT register)
```

### Command 00h - Type/Status Data Request

```
  Receive 00h (Command)
  Send    00h (GBA Type number LSB (or MSB?))
  Send    04h (GBA Type number MSB (or LSB?))
  Send    XXh (lower 8bits of SIOSTAT register)
```

### Command 15h - GBA Data Write (to GBA)

```
  Receive 15h (Command)
  Receive XXh (Lower 8bits of JOY_RECV_L)
  Receive XXh (Upper 8bits of JOY_RECV_L)
  Receive XXh (Lower 8bits of JOY_RECV_H)
  Receive XXh (Upper 8bits of JOY_RECV_H)
  Send    XXh (lower 8bits of SIOSTAT register)
```

### Command 14h - GBA Data Read (from GBA)

```
  Receive 14h (Command)
  Send    XXh (Lower 8bits of JOY_TRANS_L)
  Send    XXh (Upper 8bits of JOY_TRANS_L)
  Send    XXh (Lower 8bits of JOY_TRANS_H)
  Send    XXh (Upper 8bits of JOY_TRANS_H)
  Send    XXh (lower 8bits of SIOSTAT register)
```

## <a name="siogeneralpurposemode"></a>  SIO General-Purpose Mode

In this mode, the SIO is 'misused' as a 4bit bi-directional parallel port, each
of the SI,SO,SC,SD pins may be directly controlled, each can be separately
declared as input (with internal pull-up) or as output signal.

### 4000134h - RCNT (R) - SIO Mode, usage in GENERAL-PURPOSE Mode (R/W)

Interrupts can be requested when SI changes from HIGH to LOW, as General
Purpose mode does not require a serial shift clock, this interrupt may be
produced even when the GBA is in Stop (low power standby) state.

```
  Bit   Expl.
  0     SC Data Bit         (0=Low, 1=High)
  1     SD Data Bit         (0=Low, 1=High)
  2     SI Data Bit         (0=Low, 1=High)
  3     SO Data Bit         (0=Low, 1=High)
  4     SC Direction        (0=Input, 1=Output)
  5     SD Direction        (0=Input, 1=Output)
  6     SI Direction        (0=Input, 1=Output, but see below)
  7     SO Direction        (0=Input, 1=Output)
  8     SI Interrupt Enable (0=Disable, 1=Enable)
  9-13  Not used
  14    Must be "0" for General-Purpose Mode
  15    Must be "1" for General-Purpose or JOYBUS Mode
```

SI should be always used as Input to avoid problems with other hardware which
does not expect data to be output there.

### 4000128h - SIOCNT - SIO Control, not used in GENERAL-PURPOSE Mode

This register is not used in general purpose mode. That is, the separate bits
of SIOCNT still exist and are read- and/or write-able in the same manner as for
Normal, Multiplay, or UART mode (depending on SIOCNT Bit 12,13), but are having
no effect on data being output to the link port.

## <a name="siocontrolregisterssummary"></a>  SIO Control Registers Summary

### Mode Selection (by RCNT.15-14 and SIOCNT.13-12)

```
  R.15 R.14 S.13 S.12 Mode
  0    x    0    0    Normal 8bit
  0    x    0    1    Normal 32bit
  0    x    1    0    Multiplay 16bit
  0    x    1    1    UART (RS232)
  1    0    x    x    General Purpose
  1    1    x    x    JOY BUS
```

### SIOCNT

```
  Bit    0      1    2     3      4 5 6   7     8    9      10   11
  Normal Master Rate SI/In SO/Out - - -   Start -    -      -    -
  Multi  Baud   Baud SI/In SD/In  ID# Err Start -    -      -    -
  UART   Baud   Baud CTS   Parity S R Err Bits  FIFO Parity Send Recv
```

## <a name="gbawirelessadapter"></a>  GBA Wireless Adapter

### GBA Wireless Adapter (AGB-015 or OXY-004)

- [GBA Wireless Adapter Games](#gbawirelessadaptergames)
- [GBA Wireless Adapter Login](#gbawirelessadapterlogin)
- [GBA Wireless Adapter Commands](#gbawirelessadaptercommands)
- [GBA Wireless Adapter Component Lists](#gbawirelessadaptercomponentlists)

## <a name="gbawirelessadaptergames"></a>  GBA Wireless Adapter Games

### GBA Wireless Adapter compatible Games

```
  bit Generations series (Japan only)
  Boktai 2: Solar Boy Django (Konami)
  Boktai 3: Sabata's Counterattack
  Classic NES Series: Donkey Kong
  Classic NES Series: Dr. Mario
  Classic NES Series: Ice Climber
  Classic NES Series: Pac-Man
  Classic NES Series: Super Mario Bros.
  Classic NES Series: Xevious
  Digimon Racing (Bandai) (No Wireless Adapter support in European release)
  Dragon Ball Z: Buu's Fury (Atari)
  Famicom Mini Series: #13 Balloon Fight
  Famicom Mini Series: #12 Clu Clu Land
  Famicom Mini Series: #16 Dig Dug
  Famicom Mini Series: #02 Donkey Kong
  Famicom Mini Series: #15 Dr. Mario
  Famicom Mini Series: #03 Ice Climber
  Famicom Mini Series: #18 Makaimura
  Famicom Mini Series: #08 Mappy
  Famicom Mini Series: #11 Mario Bros.
  Famicom Mini Series: #06 Pac-Man
  Famicom Mini Series: #30 SD Gundam World Scramble Wars
  Famicom Mini Series: #01 Super Mario Bros.
  Famicom Mini Series: #21 Super Mario Bros.
  Famicom Mini Series: #19 Twin Bee
  Famicom Mini Series: #14 Wrecking Crew
  Famicom Mini Series: #07 Xevious
  Hamtaro: Ham-Ham Games (Nintendo)
  Lord of the Rings: The Third Age, The (EA Games)
  Mario Golf: Advance Tour (Nintendo)
  Mario Tennis: Power Tour (Nintendo)
  Mega Man Battle Network 5: Team Protoman (Capcom)
  Mega Man Battle Network 5: Team Colonel (Capcom)
  Mega Man Battle Network 6: Cybeast Falzar
  Mega Man Battle Network 6: Cybeast Gregar
  Momotaro Dentetsu G: Make a Gold Deck! (Japan only)
  Pokemon Emerald (Nintendo)
  Pokemon FireRed (Nintendo)
  Pokemon LeafGreen (Nintendo)
  Sennen Kazoku (Japan only)
  Shrek SuperSlam
  Sonic Advance 3
```

## <a name="gbawirelessadapterlogin"></a>  GBA Wireless Adapter Login

### GBA Wireless Adapter Login

```
  rcnt=8000h    ;\
  rcnt=80A0h    ;
  rcnt=80A2h    ; reset adapter or so
  wait          ;
  rcnt=80A0h    ;/
  siocnt=5003h  ;\set 32bit normal mode, 2MHz internal clock
  rcnt=0000h    ;/
  passes=0, index=0
 @@lop:
  passes=passes+1, if passes>32 then ERROR  ;give up (usually only 10 passses)
  recv.lo=siodata AND FFFFh    ;response from adapter
  recv.hi=siodata/10000h       ;adapter's own "NI" data
  if send.hi<>recv.lo then index=0, goto @@stuck  ;<-- fallback to index=0
  if (send.lo XOR FFFFh)<>recv.lo then goto @@stuck
  if (send.hi XOR FFFFh)<>recv.hi then goto @@stuck
  index=index+1
 @@stuck:
  send.lo=halfword[@@key_string+index*2]
  send.hi=recv.hi XOR FFFFh
  siodata=send.lo+(send.hi*10000h)
  siocnt.bit7=1                        ;<-- start transmission
  if index<4 then goto @@lop
  ret
 @@key_string db 'NINTENDO',01h,80h    ;10 bytes (5 halfwords; index=0..4)
```

### Data exchanged during Login

```
               GBA                         ADAPTER
               xxxx494E ;\     <-->        xxxxxxxx
               xxxx494E ; "NI" <--> "NI"/; 494EB6B1 ;\
  NOT("NI") /; B6B1494E ;/     <-->     \; 494EB6B1 ; NOT("NI")
            \; B6B1544E ;\"NT" <--> "NT"/; 544EB6B1 ;/
  NOT("NT") /; ABB1544E ;/     <-->     \; 544EABB1 ;\NOT("NT")
            \; ABB14E45 ;\"EN" <--> "EN"/; 4E45ABB1 ;/
  NOT("EN") /; B1BA4E45 ;/     <-->     \; 4E45B1BA ;\NOT("EN")
            \; B1BA4F44 ;\"DO" <--> "DO"/; 4F44B1BA ;/
  NOT("DO") /; B0BB4F44 ;/     <-->     \; 4F44B0BB ;\NOT("DO")
            \; B0BB8001 ;-fin  <-->  fin-; 8001B0BB ;/
                 \   \                      \   \
                  \   LSBs=Own               \   LSBs=Inverse of
                   \   Data.From.Gba          \   Prev.Data.From.Gba
                    \                          \
                     MSBs=Inverse of            MSBs=Own
                      Prev.Data.From.Adapter     Data.From.Adapter
```

## <a name="gbawirelessadaptercommands"></a>  GBA Wireless Adapter Commands

### Wireless Command/Parameter Transmission

```
  GBA       Adapter
  9966ppcch 80000000h   ;-send command (cc), and num param_words (pp)
  <param01> 80000000h   ;\
  <param02> 80000000h   ; send "pp" parameter word(s), if any
  ...       ...         ;/
  80000000h 9966rraah   ;-recv ack (aa=cc+80h), and num response_words (rr)
  80000000? <reply01>   ;\
  80000000? <reply02>   ; recv "rr" response word(s), if any
  ...       ...         ;/
```

Wireless 32bit Transfers

```
  wait until [4000128h].Bit2=0  ;want SI=0
  set [4000128h].Bit3=1         ;set SO=1
  wait until [4000128h].Bit2=1  ;want SI=1
  set [4000128h].Bit3=0,Bit7=1  ;set SO=0 and start 32bit transfer
```

All command/param/reply transfers should be done at Internal Clock (except,
Response Words for command 25h,27h,35h,37h should use External Clock).

### Wireless Commands

```
  Cmd Para Reply Name
  10h -    -     Hello (send immediately after login)
  11h -    1     Good/Bad response to cmd 16h ?
  12h
  13h -    1
  14h
  15h
  16h 6    -     Introduce (send game/user name)
  17h 1    -     Config (send after Hello) (eg. param=003C0420h or 003C043Ch)
  18h
  19h
  1Ah
  1Bh
  1Ch -    -
  1Dh -    NN    Get Directory? (receive list of game/user names?)
  1Eh -    NN    Get Directory? (receive list of game/user names?)
  1Fh 1    -     Select Game for Download (send 16bit Game_ID)
```

```
  20h -    1
  21h -    1     Good/Bad response to cmd 1Fh ?
  22h
  23h
  24h -    -
  25h                                       ;use EXT clock!
  26h -    -
  27h -    -     Begin Download ?           ;use EXT clock!
  28h
  29h
  2Ah
  2Bh
  2Ch
  2Dh
  2Eh
  2Fh
```

```
  30h 1    -
  31h
  32h
  33h
  34h
  35h                                       ;use EXT clock!
  36h
  37h                                       ;use EXT clock!
  38h
  39h
  3Ah
  3Bh
  3Ch
  3Dh -    -     Bye (return to language select)
  3Eh
  3Fh
```

Special Response 996601EEh for error or so? (only at software side?)

## <a name="gbawirelessadaptercomponentlists"></a>  GBA Wireless Adapter Component Lists

Main Chipset

```
  U1 32pin Freescale MC13190 (2.4 GHz ISM band transceiver)
  U2 48pin Freescale CT3000 or CT3001 (depending on adapter version)
  X3  2pin 9.5MHz crystal
```

The MC13190 is a Short-Range, Low-Power 2.4 GHz ISM band transceiver.

The processor is Motorola's 32-bit M-Core RISC engine. (?) MCT3000 (?)

See also: http://www.eetimes.com/document.asp?doc\_id=1271943

Version with GERMAN Postal Code on sticker:

```
  Sticker on Case:
    "GAME BOY advance, WIRELESS ADAPTER"
    "Pat.Pend.Made in Philipines, CE0125(!)B"
    "MODEL NO./MODELE NO.AGB-015 D-63760 Grossosteim P/AGB-A-WA-EUR-2 E3"
  PCB: "19-C046-04, A-7" (top side) and "B-7" and Microchip ",\\" (bottom side)
  PCB: white stamp "3104, 94V-0, RU, TW-15"
  PCB: black stamp "22FDE"
  U1 32pin "Freescale 13190, 4WFQ" (MC13190) (2.4 GHz ISM band transceiver)
  U2 48pin "Freescale CT3001, XAC0445"  (bottom side)
  X3  2pin "D959L4I" (9.5MHz)           (top side) (ca. 19 clks per 2us)
```

Further components... top side (A-7)

```
  D1   5pin "D6F, 44"   (top side, below X3)
  U71  6pin ".., () 2"  (top side, right of X3, tiny black chip)
  B71  6pin "[]"        (top side, right of X3, small white chip)
  ANT  2pin on-board copper wings
  Q?   3pin             (top side, above CN1)
  Q?   3pin             (top side, above CN1)
  D?   2pin "72"        (top side, above CN1)
  D3   2pin "F2"        (top side, above CN1)
  U200 4pin "MSV"       (top side, above CN1)
  U202 5pin "LXKA"      (top side, right of CN1)
  U203 4pin "M6H"       (top side, right of CN1)
  CN1  6pin connector to GBA link port (top side)
```

Further components... bottom side (B-7)

```
  U201 5pin "LXVB"      (bottom side, near CN1)
  U72  4pin "BMs"       (bottom side, near ANT, tiny black chip)
  FL70 ?pin "[] o26"    (bottom side, near ANT, bigger white chip)
  B70  6pin "[]"        (bottom side, near ANT, small white chip)
```

Plus, resistors and capacitors (without any markings).

Version WITHOUT sticker:

```
  Sticker on Case: N/A
  PCB: "19-C046-03, A-1" (top side) and "B-1" and Microchip ",\\" (bottom side)
  PCB: white stamp "3204, TW-15, RU, 94V-0"
  PCB: black stamp "23MN" or "23NH" or so (smeared)
  U1 32pin "Freescale 13190, 4FGD"      (top side)
  U2 48pin "Freescale CT3000, XAB0425"  (bottom side) ;CT3000 (not CT3001)
  X3  2pin "9.5SKSS4GT"                 (top side)
```

Further components... top side (A-1)

```
  D1   5pin "D6F, 31"   (top side, below X3)
  U71  6pin "P3, () 2"  (top side, right of X3, tiny black chip)
  B71  6pin "[]"        (top side, right of X3, small white chip)
  ANT  2pin on-board copper wings
  Q70  3pin             (top side, above CN1)
  D?   2pin "72"        (top side, above CN1)
  D3   2pin "F2"        (top side, above CN1)
  U200 4pin "MSV"       (top side, above CN1)
  U202 5pin "LXKH"      (top side, right of CN1)
  U203 4pin "M6H"       (top side, right of CN1)
  CN1  6pin connector to GBA link port (top side)
```

Further components... bottom side (B-1)

```
  U201 5pin "LXV2"      (bottom side, near CN1)
  U70  6pin "AAG"       (bottom side, near ANT, tiny black chip)
  FL70 ?pin "[] o26"    (bottom side, near ANT, bigger white chip)
  B70  6pin "[]"        (bottom side, near ANT, small white chip)
```

Plus, resistors and capacitors (without any markings).

Major Differences

```
  Sticker      "N/A"                     vs "Grossosteim P/AGB-A-WA-EUR-2 E3"
  PCB-markings "19-C046-03, A-1, 3204"   vs "19-C046-04, A-7, 3104"
  U1           "CT3000, XAB0425"         vs "CT3001, XAC0445"
  Transistors  One transistor (Q70)      vs Two transistors (both nameless)
  U70/U72      U70 "AAG" (6pin)          vs U72 "BMs" (4pin)
```

Purpose of the changes is unknown (either older/newer revisions, or different
regions with different FCC regulations).

## <a name="gbainfraredcommunication"></a>  GBA Infrared Communication

Early GBA prototypes have been intended to include a built-in IR port for
sending and receiving IR signals. Among others, this port could have been used
to communicate with other GBAs, or older CGB models, or TV Remote Controls,
etc.

[ THE INFRARED COMMUNICATION FEATURE IS -NOT- SUPPORTED ANYMORE ]

Anyways, the prototype specifications have been as shown below...

Keep in mind that the IR signal may be interrupted by whatever objects moved
between sender and receiver - the IR port isn't recommended for programs that
require realtime data exchange (such like action games).

### 4000136h - IR - Infrared Register (R/W)

```
  Bit   Expl.
  0     Transmission Data  (0=LED Off, 1=LED On)
  1     READ Enable        (0=Disable, 1=Enable)
  2     Reception Data     (0=None, 1=Signal received) (Read only)
  3     AMP Operation      (0=Off, 1=On)
  4     IRQ Enable Flag    (0=Disable, 1=Enable)
  5-15  Not used
```

When IRQ is enabled, an interrupt is requested if the incoming signal was
0.119us Off (2 cycles), followed by 0.536us On (9 cycles) - minimum timing
periods each.

### Transmission Notes

When transmitting an IR signal, note that it'd be not a good idea to keep the
LED turned On for a very long period (such like sending a 1 second
synchronization pulse). The recipient's circuit would treat such a long signal
as "normal IR pollution which is in the air" after a while, and thus ignore the
signal.

### Reception Notes

Received data is internally latched. Latched data may be read out by setting
both READ and AMP bits.

Note: Provided that you don't want to receive your own IR signal, be sure to
set Bit 0 to zero before attempting to receive data.

### Power-consumption

After using the IR port, be sure to reset the register to zero in order to
reduce battery power consumption.

## <a name="gbakeypadinput"></a>  GBA Keypad Input

The built-in GBA gamepad has 4 direction keys, and 6 buttons.

### 4000130h - KEYINPUT - Key Status (R)

```
  Bit   Expl.
  0     Button A        (0=Pressed, 1=Released)
  1     Button B        (etc.)
  2     Select          (etc.)
  3     Start           (etc.)
  4     Right           (etc.)
  5     Left            (etc.)
  6     Up              (etc.)
  7     Down            (etc.)
  8     Button R        (etc.)
  9     Button L        (etc.)
  10-15 Not used
```

It'd be usually recommended to read-out this register only once per frame, and
to store the current state in memory. As a side effect, this method avoids
problems caused by switch bounce when a key is newly released or pressed.

### 4000132h - KEYCNT - Key Interrupt Control (R/W)

The keypad IRQ function is intended to terminate the very-low-power Stop mode,
it is not suitable for processing normal user input, to do this, most programs
are invoking their keypad handlers from within VBlank IRQ.

```
  Bit   Expl.
  0     Button A        (0=Ignore, 1=Select)
  1     Button B        (etc.)
  2     Select          (etc.)
  3     Start           (etc.)
  4     Right           (etc.)
  5     Left            (etc.)
  6     Up              (etc.)
  7     Down            (etc.)
  8     Button R        (etc.)
  9     Button L        (etc.)
  10-13 Not used
  14    IRQ Enable Flag (0=Disable, 1=Enable)
  15    IRQ Condition   (0=Logical OR, 1=Logical AND)
```

In logical OR mode, an interrupt is requested when at least one of the selected
buttons is pressed.

In logical AND mode, an interrupt is requested when ALL of the selected buttons
are pressed.

### Notes

In 8bit gameboy compatibility mode, L and R Buttons are used to toggle the
screen size between normal 160x144 pixels and stretched 240x144 pixels.

The GBA SP is additionally having a \* Button used to toggle the backlight on
and off (controlled by separate hardware logic, there's no way to detect or
change the current backlight state by software).

## <a name="gbainterruptcontrol"></a>  GBA Interrupt Control

### 4000208h - IME - Interrupt Master Enable Register (R/W)

```
  Bit   Expl.
  0     Disable all interrupts         (0=Disable All, 1=See IE register)
  1-31  Not used
```

### 4000200h - IE - Interrupt Enable Register (R/W)

```
  Bit   Expl.
  0     LCD V-Blank                    (0=Disable)
  1     LCD H-Blank                    (etc.)
  2     LCD V-Counter Match            (etc.)
  3     Timer 0 Overflow               (etc.)
  4     Timer 1 Overflow               (etc.)
  5     Timer 2 Overflow               (etc.)
  6     Timer 3 Overflow               (etc.)
  7     Serial Communication           (etc.)
  8     DMA 0                          (etc.)
  9     DMA 1                          (etc.)
  10    DMA 2                          (etc.)
  11    DMA 3                          (etc.)
  12    Keypad                         (etc.)
  13    Game Pak (external IRQ source) (etc.)
  14-15 Not used
```

Note that there is another 'master enable flag' directly in the CPUs Status
Register (CPSR) accessible in privileged modes, see CPU reference for details.

### 4000202h - IF - Interrupt Request Flags / IRQ Acknowledge (R/W, see below)

```
  Bit   Expl.
  0     LCD V-Blank                    (1=Request Interrupt)
  1     LCD H-Blank                    (etc.)
  2     LCD V-Counter Match            (etc.)
  3     Timer 0 Overflow               (etc.)
  4     Timer 1 Overflow               (etc.)
  5     Timer 2 Overflow               (etc.)
  6     Timer 3 Overflow               (etc.)
  7     Serial Communication           (etc.)
  8     DMA 0                          (etc.)
  9     DMA 1                          (etc.)
  10    DMA 2                          (etc.)
  11    DMA 3                          (etc.)
  12    Keypad                         (etc.)
  13    Game Pak (external IRQ source) (etc.)
  14-15 Not used
```

Interrupts must be manually acknowledged by writing a "1" to one of the IRQ
bits, the IRQ bit will then be cleared.

"[Cautions regarding clearing IME and IE]

A corresponding interrupt could occur even while a command to clear IME or each
flag of the IE register is being executed. When clearing a flag of IE, you need
to clear IME in advance so that mismatching of interrupt checks will not
occur." ?

"[When multiple interrupts are used]

When the timing of clearing of IME and the timing of an interrupt agree,
multiple interrupts will not occur during that interrupt. Therefore, set
(enable) IME after saving IME during the interrupt routine." ?

### BIOS Interrupt handling

Upon interrupt execution, the CPU is switched into IRQ mode, and the physical
interrupt vector is called - as this address is located in BIOS ROM, the BIOS
will always execute the following code before it forwards control to the user
handler:

```
  00000018  b      128h                ;IRQ vector: jump to actual BIOS handler
  00000128  stmfd  r13!,r0-r3,r12,r14  ;save registers to SP_irq
  0000012C  mov    r0,4000000h         ;ptr+4 to 03FFFFFC (mirror of 03007FFC)
  00000130  add    r14,r15,0h          ;retadr for USER handler $+8=138h
  00000134  ldr    r15,[r0,-4h]        ;jump to [03FFFFFC] USER handler
  00000138  ldmfd  r13!,r0-r3,r12,r14  ;restore registers from SP_irq
  0000013C  subs   r15,r14,4h          ;return from IRQ (PC=LR-4, CPSR=SPSR)
```

As shown above, a pointer to the 32bit/ARM-code user handler must be setup in
[03007FFCh]. By default, 160 bytes of memory are reserved for interrupt stack
at 03007F00h-03007F9Fh.

### Recommended User Interrupt handling

- If necessary switch to THUMB state manually (handler is called in ARM state)

- Determine reason(s) of interrupt by examining IF register

- User program may freely assign priority to each reason by own logic

- Process the most important reason of your choice

- User MUST manually acknowledge by writing to IF register

- If user wants to allow nested interrupts, save SPSR\_irq, then enable IRQs.

- If using other registers than BIOS-pushed R0-R3, manually save R4-R11 also.

- Note that Interrupt Stack is used (which may have limited size)

- So, for memory consuming stack operations use system mode (=user stack).

- When calling subroutines in system mode, save LSR\_usr also.

- Restore SPSR\_irq and/or R4-R11 if you've saved them above.

- Finally, return to BIOS handler by BX LR (R14\_irq) instruction.

### Default memory usage at 03007FXX (and mirrored to 03FFFFXX)

```
  Addr.    Size Expl.
  3007FFCh 4    Pointer to user IRQ handler (32bit ARM code)
  3007FF8h 2    Interrupt Check Flag (for IntrWait/VBlankIntrWait functions)
  3007FF4h 4    Allocated Area
  3007FF0h 4    Pointer to Sound Buffer
  3007FE0h 16   Allocated Area
  3007FA0h 64   Default area for SP_svc Supervisor Stack (4 words/time)
  3007F00h 160  Default area for SP_irq Interrupt Stack (6 words/time)
```

Memory below 7F00h is free for User Stack and user data. The three stack
pointers are initially initialized at the TOP of the respective areas:

```
  SP_svc=03007FE0h
  SP_irq=03007FA0h
  SP_usr=03007F00h
```

The user may redefine these addresses and move stacks into other locations,
however, the addresses for system data at 7FE0h-7FFFh are fixed.

### Not sure, is following free for user ?

Registers R8-R12\_fiq, R13\_fiq, R14\_fiq, SPSR\_fiq

Registers R13-R14\_abt, SPSR\_abt

Registers R13-R14\_und, SPSR\_und

### Fast Interrupt (FIQ)

The ARM CPU provides two interrupt sources, IRQ and FIQ. In the GBA only IRQ is
used. In normal GBAs, the FIQ signal is shortcut to VDD35, ie. the signal is
always high, and there is no way to generate a FIQ by hardware. The registers
R8..12\_fiq could be used by software (when switching into FIQ mode by writing
to CPSR) - however, this might make the game incompatible with hardware
debuggers (which are reportedly using FIQs for debugging purposes).

## <a name="gbasystemcontrol"></a>  GBA System Control

### 4000204h - WAITCNT - Waitstate Control (R/W)

This register is used to configure game pak access timings. The game pak ROM is
mirrored to three address regions at 08000000h, 0A000000h, and 0C000000h, these
areas are called Wait State 0-2. Different access timings may be assigned to
each area (this might be useful in case that a game pak contains several ROM
chips with different access times each).

```
  Bit   Expl.
  0-1   SRAM Wait Control          (0..3 = 4,3,2,8 cycles)
  2-3   Wait State 0 First Access  (0..3 = 4,3,2,8 cycles)
  4     Wait State 0 Second Access (0..1 = 2,1 cycles)
  5-6   Wait State 1 First Access  (0..3 = 4,3,2,8 cycles)
  7     Wait State 1 Second Access (0..1 = 4,1 cycles; unlike above WS0)
  8-9   Wait State 2 First Access  (0..3 = 4,3,2,8 cycles)
  10    Wait State 2 Second Access (0..1 = 8,1 cycles; unlike above WS0,WS1)
  11-12 PHI Terminal Output        (0..3 = Disable, 4.19MHz, 8.38MHz, 16.78MHz)
  13    Not used
  14    Game Pak Prefetch Buffer (Pipe) (0=Disable, 1=Enable)
  15    Game Pak Type Flag  (Read Only) (0=GBA, 1=CGB) (IN35 signal)
  16-31 Not used
```

At startup, the default setting is 0000h. Currently manufactured cartridges are
using the following settings: WS0/ROM=3,1 clks; SRAM=8 clks; WS2/EEPROM: 8,8
clks; prefetch enabled; that is, WAITCNT=4317h, for more info see "GBA
Cartridges" chapter.

First Access (Non-sequential) and Second Access (Sequential) define the
waitstates for N and S cycles, the actual access time is 1 clock cycle PLUS the
number of waitstates.

GamePak uses 16bit data bus, so that a 32bit access is split into TWO 16bit
accesses (of which, the second fragment is always sequential, even if the first
fragment was non-sequential).

- [GBA GamePak Prefetch](#gbagamepakprefetch)

NOTES:

The GBA forcefully uses non-sequential timing at the beginning of each
128K-block of gamepak ROM, eg. "LDMIA [801fff8h],r0-r7" will have
non-sequential timing at 8020000h.

The PHI Terminal output (PHI Pin of Gamepak Bus) should be disabled.

### 4000300h - POSTFLG - BYTE - Undocumented - Post Boot / Debug Control (R/W)

After initial reset, the GBA BIOS initializes the register to 01h, and any
further execution of the Reset vector (00000000h) will pass control to the
Debug vector (0000001Ch) when sensing the register to be still set to 01h.

```
  Bit   Expl.
  0     Undocumented. First Boot Flag  (0=First, 1=Further)
  1-7   Undocumented. Not used.
```

Normally the debug handler rejects control unless it detects Debug flags in
cartridge header, in that case it may redirect to a cut-down boot procedure
(bypassing Nintendo logo and boot delays, much like nocash burst boot for
multiboot software). I am not sure if it is possible to reset the GBA
externally without automatically resetting register 300h though.

### 4000301h - HALTCNT - BYTE - Undocumented - Low Power Mode Control (W)

Writing to this register switches the GBA into battery saving mode.

In Halt mode, the CPU is paused as long as (IE AND IF)=0, this should be used
to reduce power-consumption during periods when the CPU is waiting for
interrupt events.

In Stop mode, most of the hardware including sound and video are paused, this
very-low-power mode could be used much like a screensaver.

```
  Bit   Expl.
  0-6   Undocumented. Not used.
  7     Undocumented. Power Down Mode  (0=Halt, 1=Stop)
```

The current GBA BIOS addresses only the upper eight bits of this register (by
writing 00h or 80h to address 04000301h), however, as the register isn't
officially documented, some or all of the bits might have different meanings in
future GBA models.

For best forwards compatibility, it'd generally be more recommended to use the
BIOS Functions SWI 2 (Halt) or SWI 3 (Stop) rather than writing to this
register directly.

### 4000410h - Undocumented - Purpose Unknown ? 8bit (W)

The BIOS writes the 8bit value 0FFh to this address. Purpose Unknown.

Probably just another bug in the BIOS.

### 4000800h - 32bit - Undocumented - Internal Memory Control (R/W)

Supported by GBA and GBA SP only - NOT supported by DS (even in GBA mode).

Also supported by GBA Micro - but crashes on "overclocked" WRAM setting.

Initialized to 0D000020h (by hardware). Unlike all other I/O registers, this
register is mirrored across the whole I/O area (in increments of 64K, ie. at
4000800h, 4010800h, 4020800h, ..., 4FF0800h)

```
  Bit   Expl.
  0     Disable 32K+256K WRAM (0=Normal, 1=Disable) (when off: empty/prefetch)
  1-3   Unknown          (Read/Write-able)
  4     Unknown          (Always zero, not used or write only)
  5     Enable 256K WRAM (0=Disable, 1=Normal) (when off: mirror of 32K WRAM)
  6-23  Unknown          (Always zero, not used or write only)
  24-27 Wait Control WRAM 256K (0-14 = 15..1 Waitstates, 15=Lockup)
  28-31 Unknown          (Read/Write-able)
```

The default value 0Dh in Bits 24-27 selects 2 waitstates for 256K WRAM (ie.
3/3/6 cycles 8/16/32bit accesses). The fastest possible setting would be 0Eh (1
waitstate, 2/2/4 cycles for 8/16/32bit), that works on GBA and GBA SP only, the
GBA Micro locks up with that setting (it's on-chip RAM is too slow, and works
only with 2 or more waitstates).

Note: One cycle equals approx. 59.59ns (ie. 16.78MHz clock).

## <a name="gbagamepakprefetch"></a>  GBA GamePak Prefetch

GamePak Prefetch can be enabled in WAITCNT register. When prefetch buffer is
enabled, the GBA attempts to read opcodes from Game Pak ROM during periods when
the CPU is not using the bus (if any). Memory access is then performed with 0
Waits if the CPU requests data which is already stored in the buffer. The
prefetch buffer stores up to eight 16bit values.

### GamePak ROM Opcodes

The prefetch feature works only with \<opcodes> fetched from GamePak ROM.
Opcodes executed in RAM or BIOS are not affected by the prefetch feature (even
if that opcodes read \<data> from GamePak ROM).

### Prefetch Enable

For GamePak ROM opcodes, prefetch may occur in two situations:

```
  1) opcodes with internal cycles (I) which do not change R15, shift/rotate
     register-by-register, load opcodes (ldr,ldm,pop,swp), multiply opcodes
  2) opcodes that load/store memory (ldr,str,ldm,stm,etc.)
```

### Prefetch Disable Bug

When Prefetch is disabled, the Prefetch Disable Bug will occur for all

```
  "Opcodes in GamePak ROM with Internal Cycles which do not change R15"
```

for those opcodes, the bug changes the opcode fetch time from 1S to 1N.

Note: Affected opcodes (with I cycles) are: Shift/rotate register-by-register
opcodes, multiply opcodes, and load opcodes (ldr,ldm,pop,swp).

## <a name="gbacartridges"></a>  GBA Cartridges

### ROM

- [GBA Cartridge Header](#gbacartridgeheader)
- [GBA Cartridge ROM](#gbacartridgerom)

### Backup Media

Aside from ROM, cartridges may also include one of the following backup medias,
used to store game positions, highscore tables, options, or other data.

- [GBA Cart Backup IDs](#gbacartbackupids)
- [GBA Cart Backup SRAM/FRAM](#gbacartbackupsramfram)
- [GBA Cart Backup EEPROM](#gbacartbackupeeprom)
- [GBA Cart Backup Flash ROM](#gbacartbackupflashrom)
- [GBA Cart Backup DACS](#gbacartbackupdacs)

### Add-Ons

- [GBA Cart I/O Port (GPIO)](#gbacartioportgpio)
- [GBA Cart Real-Time Clock (RTC)](#gbacartrealtimeclockrtc)
- [GBA Cart Solar Sensor](#gbacartsolarsensor)
- [GBA Cart Tilt Sensor](#gbacarttiltsensor)
- [GBA Cart Gyro Sensor](#gbacartgyrosensor)
- [GBA Cart Rumble](#gbacartrumble)
- [GBA Cart e-Reader](#gbacartereader)
- [GBA Cart Unknown Devices](#gbacartunknowndevices)
- [GBA Cart Protections](#gbacartprotections)

### Other Accessoires

- [GBA Flashcards](#gbaflashcards)
- [GBA Cheat Devices](#gbacheatdevices)

## <a name="gbacartridgeheader"></a>  GBA Cartridge Header

The first 192 bytes at 8000000h-80000BFh in ROM are used as cartridge header.
The same header is also used for Multiboot images at 2000000h-20000BFh (plus
some additional multiboot entries at 20000C0h and up).

### Header Overview

```
  Address Bytes Expl.
  000h    4     ROM Entry Point  (32bit ARM branch opcode, eg. "B rom_start")
  004h    156   Nintendo Logo    (compressed bitmap, required!)
  0A0h    12    Game Title       (uppercase ascii, max 12 characters)
  0ACh    4     Game Code        (uppercase ascii, 4 characters)
  0B0h    2     Maker Code       (uppercase ascii, 2 characters)
  0B2h    1     Fixed value      (must be 96h, required!)
  0B3h    1     Main unit code   (00h for current GBA models)
  0B4h    1     Device type      (usually 00h) (bit7=DACS/debug related)
  0B5h    7     Reserved Area    (should be zero filled)
  0BCh    1     Software version (usually 00h)
  0BDh    1     Complement check (header checksum, required!)
  0BEh    2     Reserved Area    (should be zero filled)
  --- Additional Multiboot Header Entries ---
  0C0h    4     RAM Entry Point  (32bit ARM branch opcode, eg. "B ram_start")
  0C4h    1     Boot mode        (init as 00h - BIOS overwrites this value!)
  0C5h    1     Slave ID Number  (init as 00h - BIOS overwrites this value!)
  0C6h    26    Not used         (seems to be unused)
  0E0h    4     JOYBUS Entry Pt. (32bit ARM branch opcode, eg. "B joy_start")
```

Note: With all entry points, the CPU is initially set into system mode.

### 000h - Entry Point, 4 Bytes

Space for a single 32bit ARM opcode that redirects to the actual startaddress
of the cartridge, this should be usually a "B \<start>" instruction.

Note: This entry is ignored by Multiboot slave GBAs (in fact, the entry is then
overwritten and redirected to a separate Multiboot Entry Point, as described
below).

### 004h..09Fh - Nintendo Logo, 156 Bytes

Contains the Nintendo logo which is displayed during the boot procedure.
Cartridge won't work if this data is missing or modified.

In detail: This area contains Huffman compression data (but excluding the
compression header which is hardcoded in the BIOS, so that it'd be probably not
possible to hack the GBA by producing de-compression buffer overflows).

A copy of the compression data is stored in the BIOS, the GBA will compare this
data and lock-up itself if the BIOS data isn't exactly the same as in the
cartridge (or multiboot header). The only exception are the two entries below
which are allowed to have variable settings in some bits.

### 09Ch Bit 2,7 - Debugging Enable

This is part of the above Nintendo Logo area, and must be commonly set to 21h,
however, Bit 2 and Bit 7 may be set to other values.

When both bits are set (ie. A5h), the FIQ/Undefined Instruction handler in the
BIOS becomes unlocked, the handler then forwards these exceptions to the user
handler in cartridge ROM (entry point defined in 80000B4h, see below).

Other bit combinations currently do not seem to have special functions.

### 09Eh Bit 0,1 - Cartridge Key Number MSBs

This is part of the above Nintendo Logo area, and must be commonly set to F8h,
however, Bit 0-1 may be set to other values.

During startup, the BIOS performs some dummy-reads from a stream of pre-defined
addresses, even though these reads seem to be meaningless, they might be
intended to unlock a read-protection inside of commercial cartridge. There are
16 pre-defined address streams - selected by a 4bit key number - of which the
upper two bits are gained from 800009Eh Bit 0-1, and the lower two bits from a
checksum across header bytes 09Dh..0B7h (bytewise XORed, divided by 40h).

### 0A0h - Game Title, Uppercase Ascii, max 12 characters

Space for the game title, padded with 00h (if less than 12 chars).

### 0ACh - Game Code, Uppercase Ascii, 4 characters

This is the same code as the AGB-UTTD code which is printed on the package and
sticker on (commercial) cartridges (excluding the leading "AGB-" part).

```
  U  Unique Code          (usually "A" or "B" or special meaning)
  TT Short Title          (eg. "PM" for Pac Man)
  D  Destination/Language (usually "J" or "E" or "P" or specific language)
```

The first character (U) is usually "A" or "B", in detail:

```
  A  Normal game; Older titles (mainly 2001..2003)
  B  Normal game; Newer titles (2003..)
  C  Normal game; Not used yet, but might be used for even newer titles
  F  Famicom/Classic NES Series (software emulated NES games)
  K  Yoshi and Koro Koro Puzzle (acceleration sensor)
  P  e-Reader (dot-code scanner)
  R  Warioware Twisted (cartridge with rumble and z-axis gyro sensor)
  U  Boktai 1 and 2 (cartridge with RTC and solar sensor)
  V  Drill Dozer (cartridge with rumble)
```

The second/third characters (TT) are:

```
  Usually an abbreviation of the game title (eg. "PM" for "Pac Man") (unless
  that gamecode was already used for another game, then TT is just random)
```

The fourth character (D) indicates Destination/Language:

```
  J  Japan             P  Europe/Elsewhere   F  French          S  Spanish
  E  USA/English       D  German             I  Italian
```

### 0B0h - Maker code, Uppercase Ascii, 2 characters

Identifies the (commercial) developer. For example, "01"=Nintendo.

### 0B2h - Fixed value, 1 Byte

Must be 96h.

### 0B3h - Main unit code, 1 Byte

Identifies the required hardware. Should be 00h for current GBA models.

### 0B4h - Device type, 1 Byte

Normally, this entry should be zero. With Nintendo's hardware debugger Bit 7
identifies the debugging handlers entry point and size of DACS (Debugging And
Communication System) memory: Bit7=0: 9FFC000h/8MBIT DACS, Bit7=1:
9FE2000h/1MBIT DACS. The debugging handler can be enabled in 800009Ch (see
above), normal cartridges do not have any memory (nor any mirrors) at these
addresses though.

### 0B5h - Reserved Area, 7 Bytes

Reserved, zero filled.

### 0BCh - Software version number

Version number of the game. Usually zero.

### 0BDh - Complement check, 1 Byte

Header checksum, cartridge won't work if incorrect. Calculate as such:

chk=0:for i=0A0h to 0BCh:chk=chk-[i]:next:chk=(chk-19h) and 0FFh

### 0BEh - Reserved Area, 2 Bytes

Reserved, zero filled.

Below required for Multiboot/slave programs only. For Multiboot, the above 192
bytes are required to be transferred as header-block (loaded to
2000000h-20000BFh), and some additional header-information must be located at
the beginning of the actual program/data-block (loaded to 20000C0h and up).
This extended header consists of Multiboot Entry point(s) which must be set up
correctly, and of two reserved bytes which are overwritten by the boot
procedure:

### 0C0h - Normal/Multiplay mode Entry Point

This entry is used only if the GBA has been booted by using Normal or Multiplay
transfer mode (but not by Joybus mode).

Typically deposit a ARM-32bit "B \<start>" branch opcode at this location,
which is pointing to your actual initialization procedure.

### 0C4h (BYTE) - Boot mode

The slave GBA download procedure overwrites this byte by a value which is
indicating the used multiboot transfer mode.

```
  Value  Expl.
  01h    Joybus mode
  02h    Normal mode
  03h    Multiplay mode
```

Typically set this byte to zero by inserting DCB 00h in your source.

Be sure that your uploaded program does not contain important program code or
data at this location, or at the ID-byte location below.

### 0C5h (BYTE) - Slave ID Number

If the GBA has been booted in Normal or Multiplay mode, this byte becomes
overwritten by the slave ID number of the local GBA (that'd be always 01h for
normal mode).

```
  Value  Expl.
  01h    Slave #1
  02h    Slave #2
  03h    Slave #3
```

Typically set this byte to zero by inserting DCB 00h in your source.

When booted in Joybus mode, the value is NOT changed and remains the same as
uploaded from the master GBA.

### 0C6h..0DFh - Not used

Appears to be unused.

### 0E0h - Joybus mode Entry Point

If the GBA has been booted by using Joybus transfer mode, then the entry point
is located at this address rather than at 20000C0h. Either put your
initialization procedure directly at this address, or redirect to the actual
boot procedure by depositing a "B \<start>" opcode here (either one using
32bit ARM code). Or, if you are not intending to support joybus mode (which is
probably rarely used), ignore this entry.

## <a name="gbacartridgerom"></a>  GBA Cartridge ROM

### ROM Size

The games F-ZERO and Super Mario Advance use ROMs of 4 MBytes each. Zelda uses
8 MBytes. Not sure if other sizes are manufactured.

### ROM Waitstates

The GBA starts the cartridge with 4,2 waitstates (N,S) and prefetch disabled.
The program may change these settings by writing to WAITCNT, the games F-ZERO
and Super Mario Advance use 3,1 waitstates (N,S) each, with prefetch enabled.

Third-party flashcards are reportedly running unstable with these settings.
Also, prefetch and shorter waitstates are allowing to read more data and
opcodes from ROM is less time, the downside is that it increases the power
consumption.

### ROM Chip

Because of how 24bit addresses are squeezed through the Gampak bus, the
cartridge must include a circuit that latches the lower 16 address bits on
non-sequential access, and that increments these bits on sequential access.
Nintendo includes this circuit directly in the ROM chip.

Also, the ROM must have 16bit data bus (or a circuit which converts two 8bit
data units into one 16bit unit - by not exceeding the waitstate timings).

## <a name="gbacartbackupids"></a>  GBA Cart Backup IDs

Nintendo didn't include a backup-type entry in the ROM header, however, the
required type can be detected by ID strings in the ROM-image. Nintendo's tools
are automatically inserting these strings (as part of their library headers).
When using other tools, you may insert ID strings by hand.

### ID Strings

The ID string must be located at a word-aligned memory location, the string
length should be a multiple of 4 bytes (padded with zero's).

```
  EEPROM_Vnnn    EEPROM 512 bytes or 8 Kbytes (4Kbit or 64Kbit)
  SRAM_Vnnn      SRAM 32 Kbytes (256Kbit)
  FLASH_Vnnn     FLASH 64 Kbytes (512Kbit) (ID used in older files)
  FLASH512_Vnnn  FLASH 64 Kbytes (512Kbit) (ID used in newer files)
  FLASH1M_Vnnn   FLASH 128 Kbytes (1Mbit)
```

For Nintendo's tools, "nnn" is a 3-digit library version number. When using
other tools, best keep it set to "nnn" rather than inserting numeric digits.

### Notes

No$gba does auto-detect most backup types, even without ID strings, except for
128K FLASH (without ID "FLASH1M\_Vnnn", the FLASH size defaults to 64K).
Ideally, for faster detection, the ID should be put into the first some bytes
of the ROM-image (ie. somewhere right after the ROM header).

## <a name="gbacartbackupsramfram"></a>  GBA Cart Backup SRAM/FRAM

SRAM - 32 KBytes (256Kbit) Lifetime: Depends on back-up battery

FRAM - 32 KBytes (256Kbit) Lifetime: 10,000,000,000 read/write per bit

Hyundai GM76V256CLLFW10 SRAM (Static RAM) (eg. F-Zero)

Fujitsu MB85R256 FRAM (Ferroelectric RAM) (eg. Warioware Twisted)

### Addressing and Waitstates

SRAM/FRAM is mapped to E000000h-E007FFFh, it should be accessed with 8
waitstates (write a value of 3 into Bit0-1 of WAITCNT).

### Databus Width

The SRAM/FRAM databus is restricted to 8 bits, it should be accessed by LDRB,
LDRSB, and STRB opcodes only.

### Reading and Writing

Reading from SRAM/FRAM should be performed by code executed in WRAM only (but
not by code executed in ROM). There is no such restriction for writing.

### Preventing Data Loss

The GBA SRAM/FRAM carts do not include a write-protect function (unlike older
8bit gameboy carts). This seems to be a problem and may cause data loss when a
cartridge is removed or inserted while the GBA is still turned on. As far as I
understand, this is not so much a hardware problem, but rather a software
problem, ie. theoretically you could remove/insert the cartridge as many times
as you want, but you should take care that your program does not crash (and
write blindly into memory).

### Recommended Workaround

Enable the Gamepak Interrupt (it'll most likely get triggered when removing the
cartridge), and hang-up the GBA in an endless loop when your interrupt handler
senses a Gamepak IRQ. For obvious reason, your interrupt handler should be
located in WRAM, ie. not in the (removed) ROM cartridge. The handler should
process Gamepak IRQs at highest priority. Periods during which interrupts are
disabled should be kept as short as possible, if necessary allow nested
interrupts.

### When to use the above Workaround

A program that relies wholly on code and data in WRAM, and that does not crash
even when ROM is removed, may keep operating without having to use the above
mechanism.

Do NOT use the workaround for programs that run without a cartridge inserted
(ie. single gamepak/multiboot slaves), or for programs that use Gamepak IRQ/DMA
for other purposes.

All other programs should use it. It'd be eventually a good idea to include it
even in programs that do not use SRAM/FRAM themselves (eg. otherwise removing a
SRAM/FRAM-less cartridge may lock up the GBA, and may cause it to destroy
backup data when inserting a SRAM/FRAM cartridge).

### SRAM vs FRAM

FRAM (Ferroelectric RAM) is a newer technology, used in newer GBA carts, unlike
SRAM (Static RAM), it doesn't require a battery to hold the data. At software
side, it is accessed exactly like SRAM, ie. unlike EEPROM/FLASH, it doesn't
require any Write/Erase commands/delays.

### Note

In SRAM/FRAM cartridges, the /REQ pin (Pin 31 of Gamepak bus) should be a
little bit shorter as than the other pins; when removing the cartridge, this
causes the gamepak IRQ signal to get triggered before the other pins are
disconnected.

## <a name="gbacartbackupeeprom"></a>  GBA Cart Backup EEPROM

9853 - EEPROM 512 Bytes (0200h) (4Kbit) (eg. used by Super Mario Advance)

9854 - EEPROM 8 KBytes (2000h) (64Kbit) (eg. used by Boktai)

Lifetime: 100,000 writes per address

### Addressing and Waitstates

The eeprom is connected to Bit0 of the data bus, and to the upper 1 bit (or
upper 17 bits in case of large 32MB ROM) of the cartridge ROM address bus,
communication with the chip takes place serially.

The eeprom must be used with 8 waitstates (set WAITCNT=X3XXh; 8,8 clks in WS2
area), the eeprom can be then addressed at DFFFF00h..DFFFFFFh.

Respectively, with eeprom, ROM is restricted to 8000000h-9FFFeFFh (max.
1FFFF00h bytes = 32MB minus 256 bytes). On carts with 16MB or smaller ROM,
eeprom can be alternately accessed anywhere at D000000h-DFFFFFFh.

### Data and Address Width

Data can be read from (or written to) the EEPROM in units of 64bits (8 bytes).
Writing automatically erases the old 64bits of data. Addressing works in units
of 64bits respectively, that is, for 512 Bytes EEPROMS: an address range of
0-3Fh, 6bit bus width; and for 8KByte EEPROMs: a range of 0-3FFh, 14bit bus
width (only the lower 10 address bits are used, upper 4 bits should be zero).

### Set Address (For Reading)

Prepare the following bitstream in memory:

```
  2 bits "11" (Read Request)
  n bits eeprom address (MSB first, 6 or 14 bits, depending on EEPROM)
  1 bit "0"
```

Then transfer the stream to eeprom by using DMA.

### Read Data

Read a stream of 68 bits from EEPROM by using DMA,

then decipher the received data as follows:

```
  4 bits - ignore these
 64 bits - data (conventionally MSB first)
```

### Write Data to Address

Prepare the following bitstream in memory, then transfer the stream to eeprom
by using DMA, it'll take ca. 108368 clock cycles (ca. 6.5ms) until the old data
is erased and new data is programmed.

```
  2 bits "10" (Write Request)
  n bits eeprom address (MSB first, 6 or 14 bits, depending on EEPROM)
 64 bits data (conventionally MSB first)
  1 bit "0"
```

After the DMA, keep reading from the chip, by normal LDRH [DFFFF00h], until Bit
0 of the returned data becomes "1" (Ready). To prevent your program from
locking up in case of malfunction, generate a timeout if the chip does not
reply after 10ms or longer.

### Using DMA

Transferring a bitstream to/from the EEPROM by LDRH/STRH opcodes does not work,
this might be because of timing problems, or because how the GBA squeezes
non-sequential memory addresses through the external address/data bus.

For this reason, a buffer in memory must be used (that buffer would be
typically allocated temporarily on stack, one halfword for each bit, bit1-15 of
the halfwords are don't care, only bit0 is of interest).

The buffer must be transfered as a whole to/from EEPROM by using DMA3 (only DMA
3 is valid to read & write external memory), use 16bit transfer mode, both
source and destination address incrementing (ie. DMA3CNT=80000000h+length).

DMA channels of higher priority should be disabled during the transfer (ie.
H/V-Blank or Sound FIFO DMAs). And, of course any interrupts that might mess
with DMA registers should be disabled.

### Pin-Outs

The EEPROM chips are having only 8 pins, these are connected, Pin 1..8, to
ROMCS, RD, WR, AD0, GND, GND, A23, VDD of the GamePak bus. Carts with 32MB ROM
must have A7..A22 logically ANDed with A23.

### Notes

There seems to be no autodection mechanism, so that a hardcoded bus width must
be used.

## <a name="gbacartbackupflashrom"></a>  GBA Cart Backup Flash ROM

64 KBytes - 512Kbits Flash ROM - Lifetime: 10,000 writes per sector

128 KBytes - 1Mbit Flash ROM - Lifetime: ??? writes per sector

### Chip Identification (all device types)

```
  [E005555h]=AAh, [E002AAAh]=55h, [E005555h]=90h  (enter ID mode)
  dev=[E000001h], man=[E000000h]                  (get device & manufacturer)
  [E005555h]=AAh, [E002AAAh]=55h, [E005555h]=F0h  (terminate ID mode)
```

Used to detect the type (and presence) of FLASH chips. See Device Types below.

### Reading Data Bytes (all device types)

```
  dat=[E00xxxxh]                                  (read byte from address xxxx)
```

### Erase Entire Chip (all device types)

```
  [E005555h]=AAh, [E002AAAh]=55h, [E005555h]=80h  (erase command)
  [E005555h]=AAh, [E002AAAh]=55h, [E005555h]=10h  (erase entire chip)
  wait until [E000000h]=FFh (or timeout)
```

Erases all memory in chip, erased memory is FFh-filled.

### Erase 4Kbyte Sector (all device types, except Atmel)

```
  [E005555h]=AAh, [E002AAAh]=55h, [E005555h]=80h  (erase command)
  [E005555h]=AAh, [E002AAAh]=55h, [E00n000h]=30h  (erase sector n)
  wait until [E00n000h]=FFh (or timeout)
```

Erases memory at E00n000h..E00nFFFh, erased memory is FFh-filled.

### Erase-and-Write 128 Bytes Sector (only Atmel devices)

```
  old=IME, IME=0                                  (disable interrupts)
  [E005555h]=AAh, [E002AAAh]=55h, [E005555h]=A0h  (erase/write sector command)
  [E00xxxxh+00h..7Fh]=dat[00h..7Fh]               (write 128 bytes)
  IME=old                                         (restore old IME state)
  wait until [E00xxxxh+7Fh]=dat[7Fh] (or timeout)
```

Interrupts (and DMAs) should be disabled during command/write phase. Target
address must be a multiple of 80h.

### Write Single Data Byte (all device types, except Atmel)

```
  [E005555h]=AAh, [E002AAAh]=55h, [E005555h]=A0h  (write byte command)
  [E00xxxxh]=dat                                  (write byte to address xxxx)
  wait until [E00xxxxh]=dat (or timeout)
```

The target memory location must have been previously erased.

### Terminate Command after Timeout (only Macronix devices, ID=1CC2h)

```
  [E005555h]=F0h                            (force end of write/erase command)
```

Use if timeout occurred during "wait until" periods, for Macronix devices only.

### Bank Switching (devices bigger than 64K only)

```
  [E005555h]=AAh, [E002AAAh]=55h, [E005555h]=B0h  (select bank command)
  [E000000h]=bnk                                  (write bank number 0..1)
```

Specifies 64K bank number for read/write/erase operations.

Required because gamepak flash/sram addressbus is limited to 16bit width.

### Device Types

Nintendo puts different FLASH chips in commercial game cartridges. Developers
should thus detect & support all chip types. For Atmel chips it'd be
recommended to simulate 4K sectors by software, though reportedly Nintendo
doesn't use Atmel chips in newer games anymore. Also mind that different
timings should not disturb compatibility and performance.

```
  ID     Name       Size  Sectors  AverageTimings  Timeouts/ms   Waits
  D4BFh  SST        64K   16x4K    20us?,?,?       10,  40, 200  3,2
  1CC2h  Macronix   64K   16x4K    ?,?,?           10,2000,2000  8,3
  1B32h  Panasonic  64K   16x4K    ?,?,?           10, 500, 500  4,2
  3D1Fh  Atmel      64K   512x128  ?,?,?           ...40..,  40  8,8
  1362h  Sanyo      128K  ?        ?,?,?           ?    ?    ?    ?
  09C2h  Macronix   128K  ?        ?,?,?           ?    ?    ?    ?
```

Identification Codes MSB=Device Type, LSB=Manufacturer.

Size in bytes, and numbers of sectors \* sector size in bytes.

Average medium Write, Erase Sector, Erase Chips timings are unknown?

Timeouts in milliseconds for Write, Erase Sector, Erase Chips.

Waitstates for Writes, and Reads in clock cycles.

### Accessing FLASH Memory

FLASH memory is located in the "SRAM" area at E000000h..E00FFFFh, which is
restricted to 16bit address and 8bit data buswidths. Respectively, the memory
can be accessed \<only> by 8bit read/write LDRB/STRB opcodes.

Also, reading anything (data or status/busy information) can be done
\<only> by opcodes executed in WRAM (not from opcodes in ROM) (there's no
such restriction for writing).

### FLASH Waitstates

Use 8 clk waitstates for initial detection (WAITCNT Bits 0,1 both set). After
detection of certain device types smaller wait values may be used for
write/erase, and even smaller wait values for raw reading, see Device Types
table.

In practice, games seem to use smaller values only for write/erase (even though
those operations are slow anyways), whilst raw reads are always done at 8 clk
waits (even though reads could actually benefit slightly from smaller wait
values).

### Verify Write/Erase and Retry

Even though device signalizes the completion of write/erase operations, it'd be
recommended to read/confirm the content of the changed memory area by software.
In practice, Nintendo's "erase-write-verify-retry" function typically repeats
the operation up to three times in case of errors.

Also, for SST devices only, the "erase-write" and "erase-write-verify-retry"
functions repeat the erase command up to 80 times, additionally followed by one
further erase command if no retries were needed, otherwise followed by six
further erase commands.

### Note

FLASH (64Kbytes) is used by the game Sonic Advance, and possibly others.

## <a name="gbacartbackupdacs"></a>  GBA Cart Backup DACS

128 KBytes - 1Mbit DACS - Lifetime: 100,000 writes.

1024 KBytes - 8Mbit DACS - Lifetime: 100,000 writes.

DACS (Debugging And Communication System) is used in Nintendo's hardware
debugger only, DACS is NOT used in normal game cartridges.

Parts of DACS memory is used to store the debugging exception handlers (entry
point/size defined in cartridge header), the remaining memory could be used to
store game positions or other data. The address space is the upper end of the
32MB ROM area, the memory can be read directly by the CPU, including for
ability to execute program code in this area.

## <a name="gbacartioportgpio"></a>  GBA Cart I/O Port (GPIO)

4bit General Purpose I/O Port (GPIO) - contained in the ROM-chip

Used by Boktai for RTC and Solar Sensor:

- [GBA Cart Real-Time Clock (RTC)](#gbacartrealtimeclockrtc)
- [GBA Cart Solar Sensor](#gbacartsolarsensor)
And by Warioware Twisted for Rumble and Z-Axis Sensor:

- [GBA Cart Rumble](#gbacartrumble)
- [GBA Cart Gyro Sensor](#gbacartgyrosensor)
Might be also used by other games for other purposes, such like other sensors,
or SRAM bank switching, etc.

The I/O registers are mapped to a 6-byte region in the ROM-area at 80000C4h,
the 6-byte region should be zero-filled in the ROM-image. In Boktai, the size
of the zero-filled region is 0E0h bytes - that probably due to an incorrect
definition (the additional bytes do not contain any extra ports, nor mirrors of
the ports in the 6-byte region). Observe that ROM-bus writes are limited to
16bit/32bit access (STRB opcodes are ignored; that, only in DS mode?).

### 80000C4h - I/O Port Data (selectable W or R/W)

```
  bit0-3  Data Bits 0..3 (0=Low, 1=High)
  bit4-15 not used (0)
```

### 80000C6h - I/O Port Direction (for above Data Port) (selectable W or R/W)

```
  bit0-3  Direction for Data Port Bits 0..3 (0=In, 1=Out)
  bit4-15 not used (0)
```

### 80000C8h - I/O Port Control (selectable W or R/W)

```
  bit0    Register 80000C4h..80000C8h Control (0=Write-Only, 1=Read/Write)
  bit1-15 not used (0)
```

In write-only mode, reads return 00h (or possible other data, if the rom
contains non-zero data at that location).

### Connection Examples

```
  GPIO       | Boktai  | Wario
  Bit Pin    | RTC SOL | GYR RBL
  -----------+---------+---------
  0   ROM.1  | SCK CLK | RES -
  1   ROM.2  | SIO RST | CLK -
  2   ROM.21 | CS  -   | DTA -
  3   ROM.22 | -   FLG | -   MOT
  -----------+---------+---------
  IRQ ROM.43 | IRQ -   | -   -
```

Aside from the I/O Port, the ROM-chip also includes an inverter (used for
inverting the RTC /IRQ signal), and some sort of an (unused) address decoder
output (which appears to be equal or related to A23 signal) (ie. reacting on
ROM A23, or SRAM D7, which share the same pin on GBA slot).

## <a name="gbacartrealtimeclockrtc"></a>  GBA Cart Real-Time Clock (RTC)

S3511 - 8pin RTC with 3-wire serial bus (used in Boktai)

The RTC chip is (almost) the same as used in NDS consoles:

- [DS Real-Time Clock (RTC)](#dsrealtimeclockrtc)
The chip is accessed via 4bit I/O port (only 3bits are used for RTC):

- [GBA Cart I/O Port (GPIO)](#gbacartioportgpio)

### Comparision of RTC Registers

```
  NDS_________GBA_________GBA/Params___
  stat2       control     (1-byte)
  datetime    datetime    (7-byte)
  time        time        (3-byte)
  stat1       force reset (0-byte)
  clkadjust   force irq   (0-byte)
  alarm1/int1 always FFh  (boktai contains code for writing 1-byte to it)
  alarm2      always FFh  (unused)
  free        always FFh  (unused)
```

### Control Register

```
  Bit Dir Expl.
  0   -   Not used
  1   R/W IRQ duty/hold related?
  2   -   Not used
  3   R/W Per Minute IRQ (30s duty)        (0=Disable, 1=Enable)
  4   -   Not used
  5   R/W Unknown?
  6   R/W 12/24-hour Mode                  (0=12h, 1=24h) (usually 1)
  7   R   Power-Off (auto cleared on read) (0=Normal, 1=Failure)
```

Setting after Battery-Shortcut is 82h. Setting after Force-Reset is 00h.

Unused bits seem to be always zero, but might be read-only or write-only?

### Datetime and Time Registers

Same as NDS, except AM/PM flag moved from hour.bit6 (NDS) to hour.bit7 (GBA).

### Force Reset/Irq Registers

Used to reset all RTC registers (all used registers become 00h, except
day/month which become 01h), or to drag the IRQ output LOW for a short moment.
These registers are strobed by ANY access to them, ie. by both writing to, as
well as reading from these registers.

### Pin-Outs / IRQ Signal

The package has identical pin-outs as in NDS, although it is slightly larger
than the miniature chip in the DS.

For whatever reason, the RTC's /IRQ output is passed through an inverter
(contained in the ROM-chip), the inverted signal is then passed to the /IRQ pin
on the cartridge slot. So, IRQ's will be triggered on the "wrong" edge -
possible somehow in relation with detecting cartridge-removal IRQs?

## <a name="gbacartsolarsensor"></a>  GBA Cart Solar Sensor

Uses a Photo Diode as Solar Sensor (used in Boktai, allowing to defeat vampires
when the cartridge is exposed to sunlight). The cartridge comes in transparent
case, and it's slightly longer than normal carts, so the sensor reaches out of
the cartridge slot. According to the manual, the sensor works only with
sunlight, but actually it works with any strong light source (eg. a 100 Watt
bulb at 1-2 centimeters distance). The sensor is accessed via 4bit I/O port
(only 3bits used), which is contained in the ROM-chip.

- [GBA Cart I/O Port (GPIO)](#gbacartioportgpio)

### A/D Conversion

The cartridge uses a self-made A/D converter, which is (eventually) better than
measuring a capacitor charge-up time, and/or less expensive than a real
ADC-chip:

It contains a 74LV4040 12bit binary counter (clocked by CPU via the I/O port),
of which only the lower 8bit are used, which are passed to a resistor
ladder-type D/A converter, which is generating a linear increasing voltage,
which is passed to a TLV272 voltage comparator, which is passing a signal to
the I/O port when the counter voltage becomes greater than the sensor voltage.

### Example Code

```
  strh  0001h,[80000c8h] ;-enable R/W mode
  strh  0007h,[80000c6h] ;-init I/O direction
  strh  0002h,[80000c4h] ;-reset counter to zero (high=reset) (I/O bit0)
  strh  0000h,[80000c4h] ;-clear reset (low=normal)
  mov   r0,0             ;-initial level
 @@lop:
  strh  0001h,[80000c4h] ;-clock high ;\increase counter      (I/O bit1)
  strh  0000h,[80000c4h] ;-clock low  ;/
  ldrh  r1,[80000c4h]    ;-read port                          (I/O bit3)
  tst   r1,08h           ;\
  addeq r0,1             ; loop until voltage match (exit with r0=00h..FFh),
  tsteq r0,100h          ; or until failure/timeout (exit with r0=100h)
  beq   @@lop            ;/
```

The results vary depending on the clock rate used. In above example, ensure
that IRQs or DMAs do not interrupt the function. Alternately, use a super-slow
clock rate (eg. like 666Hz used in Boktai) so that additional small IRQ/DMA
delays have little effect on the overall timing. Results should be somewhat:

```
  E8h  total darkness (including daylight on rainy days)
  Dxh  close to a 100 Watt Bulb
  5xh  reaches max level in boktai's solar gauge
  00h  close to a tactical nuclear bomb dropped on your city
```

The exact values may change from cartridge to cartridge, so it'd be recommened
to include a darkness calibration function, prompting the user to cover the
sensor for a moment.

## <a name="gbacarttiltsensor"></a>  GBA Cart Tilt Sensor

Yoshi's Universal Gravitation / Yoshi Topsy Turvy (X/Y-Axis)

Koro Koro Puzzle (probably same as Yoshi, X/Y-Axis, too) (?)

### Yoshi-Type (X/Y-Axis)

All of the registers are one byte wide, mapped into the top "half" of the SRAM
memory range.

```
  E008000h (W) Write 55h to start sampling
  E008100h (W) Write AAh to start sampling
  E008200h (R) Lower 8 bits of X axis
  E008300h (R) Upper 4 bits of X axis, and Bit7: ADC Status (0=Busy, 1=Ready)
  E008400h (R) Lower 8 bits of Y axis
  E008500h (R) Upper 4 bits of Y axis
```

You must set SRAM wait control to 8 clocks to access it correctly.

You must also set the cartridge PHI terminal to 4 MHz to make it work.

Sampling routine (typically executed once a frame during VBlank):

```
  wait until [E008300h].Bit7=1 or until timeout ;wait ready
  x = ([E008300h] AND 0Fh)*100h + [E008200h]    ;get x
  y = ([E008500h] AND 0Fh)*100h + [E008400h]    ;get y
  [E008000h]=55h, [E008100h]=AAh                ;start next conversion
```

Example values (may vary on different carts and on temperature, etc):

```
  X ranged between 0x2AF to 0x477, center at 0x392.    Huh?
  Y ranged between 0x2C3 to 0x480, center at 0x3A0.    Huh?
```

Thanks to Flubba for Yoshi-Type information.

Unknown if the Yoshi-Type sensors are sensing rotation, or orientation, or
motion, or something else? In case of rotation, rotation around X-axis would
result in motion in Y-direction, so not too sure whether X and Y have which
meaning?

Most probably, the sensors are measuring (both) static acceleration (gravity),
and dynamic acceleration (eg. shaking the device left/right).

The X/Y values are likely to be mirrored depending on using a back-loading
cartridge slot (original GBA), or front-loading cartridge slot (newer GBA SP,
and NDS, and NDS-Lite).

## <a name="gbacartgyrosensor"></a>  GBA Cart Gyro Sensor

Warioware Twisted (Z-Axis Gyro Sensor, plus Rumble)

### Wario-Type (Z-Axis)

Uses a single-axis sensor, which senses rotation around the Z-axis. The sensor
is connected to an analogue-in, serial-out ADC chip, which is accessed via
lower 3 bits of the GPIO,

- [GBA Cart I/O Port (GPIO)](#gbacartioportgpio)
The four I/O Lines are connected like so,

```
  GPIO.Bit0 (W) Start Conversion
  GPIO.Bit1 (W) Serial Clock
  GPIO.Bit2 (R) Serial Data
  GPIO.Bit3 (W) Used for Rumble (not gyro related)
```

There should be at least \<three sequential 32bit ARM opcodes executed in WS0
region> between the STRH opcodes which toggle the CLK signal. Wario uses
WAITCNT=45B7h (SRAM=8clks, WS0/WS1/WS2=3,1clks, Prefetch=On, PHI=Off).

The data stream consists of: 4 dummy bits (usually zero), followed by 12 data
bits, followed by endless unused bits (usually zero).

```
 read_gyro:
  mov  r1,8000000h      ;-cartridge base address
  mov  r0,01h           ;\enable R/W access
  strh r0,[r1,0c8h]     ;/
  mov  r0,0bh           ;\init direction (gpio2=input, others=output)
  strh r0,[r1,0c6h]     ;/
  ldrh r2,[r1,0c4h]     ;-get current state (for keeping gpio3=rumble)
  orr  r2,3                     ;\
  strh r2,[r1,0c4h] ;gpio0=1    ; start ADC conversion
  bic  r2,1                     ;
  strh r2,[r1,0c4h] ;gpio0=0    ;/
  mov  r0,00010000h ;stop-bit           ;\
  bic  r2,2                             ;
 @@lop:                                 ;
  ldrh r3,[r1,0c4h] ;get gpio2=data     ; read 16 bits
  strh r2,[r1,0c4h] ;gpio1=0=clk=low    ; (4 dummy bits, plus 12 data bits)
  movs r3,r3,lsr 3  ;gpio2 to cy=data   ;
  adcs r0,r0,r0     ;merge data, cy=done;
  orr  r3,r2,2      ;set bit1 and delay ;
  strh r3,[r1,0c4h] ;gpio1=1=clk=high   ;
  bcc  @@lop                            ;/
  bic  r0,0f000h                 ;-strip upper 4 dummy bits (isolate 12bit adc)
  bx   lr
```

Example values (may vary on different carts, battery charge, temperature, etc):

```
  354h  rotated in anti-clockwise direction (shock-speed)
  64Dh  rotated in anti-clockwise direction (normal fast)
  6A3h  rotated in anti-clockwise direction (slow)
  6C0h  no rotation                         (stopped)
  6DAh  rotation in clockwise direction     (slow)
  73Ah  rotation in clockwise direction     (normal fast)
  9E3h  rotation in clockwise direction     (shock-speed)
```

For detection, values 000h and FFFh would indicate that there's no sensor.

The Z-axis always points into same direction; no matter of frontloading or
backloading cartridge slots.

Thanks to Momo Vampire for contributing a Wario cartridge.

### X/Y/Z-Axes

X-Axis and Y-Axis are meant to be following the screens X and Y coordinates, so
the Z-Axis would point into the screens depth direction.

### DSi Cameras

DSi consoles can mis-use the built-in cameras as Gyro sensor (as done by the
System Flaw DSi game).

## <a name="gbacartrumble"></a>  GBA Cart Rumble

Warioware Twisted (Rumble, plus Z-Axis Gyro Sensor)

Drill Dozer (Rumble only) \<-- and ALSO supports Gameboy Player rumble?

GBA Rumble Carts are containing a small motor, which is causing some vibration
when/while it is switched on (that, unlike DS Rumble, which must be repeatedly
toggled on/off).

In Warioware Twisted, rumble is controlled via GPIO.Bit3 (Data 0=Low=Off,
1=High=On) (and Direction 1=Output), the other GPIO Bits are used for the gyro
sensor.

- [GBA Cart I/O Port (GPIO)](#gbacartioportgpio)
Note: GPIO3 is connected to an external pulldown resistor (so the HighZ level
gets dragged to Low=Off when direction is set to Input).

Unknown if Drill Dozer is controlled via GPIO.Bit3, too?

### DS Rumble Pak

Additionally, there's a Rumble Pak for the NDS, which connects to the GBA slot,
so it can be used also for GBA games (provided that the game doesn't require
the GBA slot, eg. GBA multiboot games).

- [DS Cart Rumble Pak](#dscartrumblepak)

### Gamecube Rumble

Moreover, GBA games that are running on a Gameboy Player are having access to
the Rumble function of Gamecube joypads.

- [GBA Gameboy Player](#gbagameboyplayer)

## <a name="gbacartereader"></a>  GBA Cart e-Reader

- [GBA Cart e-Reader Overview](#gbacartereaderoverview)
- [GBA Cart e-Reader I/O Ports](#gbacartereaderioports)
- [GBA Cart e-Reader Dotcode Format](#gbacartereaderdotcodeformat)
- [GBA Cart e-Reader Data Format](#gbacartereaderdataformat)
- [GBA Cart e-Reader Program Code](#gbacartereaderprogramcode)
- [GBA Cart e-Reader API Functions](#gbacartereaderapifunctions)
- [GBA Cart e-Reader VPK Decompression](#gbacartereadervpkdecompression)
- [GBA Cart e-Reader Error Correction](#gbacartereadererrorcorrection)
- [GBA Cart e-Reader File Formats](#gbacartereaderfileformats)

```
   ________________
  |   ShortStrip   |
  |L              L|
  |o    Center    o|
  |n    Region    n|
  |g              g|
  |  may contain   |
  |S   pictures,  S|
  |t instructions t|
  |r     etc.     r|
  |i              i|
  |p              p|
  |___ShortStrip___|
```

## <a name="gbacartereaderoverview"></a>  GBA Cart e-Reader Overview

The e-Reader is a large GBA cartridge (about as big as the GBA console), with
built-in dotcode scanning hardware. Dotcodes are tiny strips of black and white
pixels printed on the edges of cardboard cards. The cards have to be pulled
through a slot on the e-Reader, which is giving it a feeling like using a
magnet card reader. The binary data on the dotcodes contains small games,
either in native GBA code (ARM/THUMB), or in software emulated 8bit Z80 or
NES/Famicom (6502) code.

### The e-Reader Hardware

The hardware consists of regular 8MByte ROM and 128KByte FLASH chips, two link
ports, a custom PGA chip, the camera module (with two red LEDs, used as light
source), and some analogue components for generating the LED voltages, etc. The
camera supports 402x302 pixels with 7bit monochrome color depth, but the PGA
clips it to max 320 pixels per scanline with 1bit color depth.

### Link Port Plug/Socket

The e-Reader's two link ports are simply interconnected with each other;
without connection to the rest of the e-Reader hardware. These ports are used
only on the original GBA (where the large e-Reader cartridge would be covering
the GBA's link socket). When trying to insert the e-Reader into an original NDS
(or GBA-Micro), then the e-Reader's link plug will hit against the case of the
NDS, so it works only with some minor modification to the hardware. There's no
such problem with GBA-SP and NDS-Lite.

### Region/Version

There are 3 different e-Reader's: Japanese/Original, Japanese/Plus, and
Non-Japanese. The Original version has only 64K FLASH, no Link Port, and
reportedly supports only Z80 code, but no NES/GBA code. The Plus and
Non-Japanese versions should be almost identical, except that they reject cards
from the wrong region, and that the title strings aren't ASCII in Japan, the
Plus version should be backwards compatible to the Original one.

### The Problem

Nintendo's current programmers are definetly unable to squeeze a Pac-Man style
game into less than 4MBytes. Their solution has been: MORE memory. That is,
they've put a whopping 8MByte BIOS ROM into the e-Reader, which contains the
User Interface, and software emulation for running some of their 20 years old
8bit NES and Game&Watch titles, which do fit on a few dotcode strips.

## <a name="gbacartereaderioports"></a>  GBA Cart e-Reader I/O Ports

### DF80000h Useless Register (R/W)

```
  0     Output to PGA.Pin93 (which seems to be not connected to anything)
  1-3   Unknown, read/write-able (not used by e-Reader BIOS)
  4-15  Always zero (0)
```

### DFA0000h Reset Register (R/W)

```
  0    Always zero              (0)
  1    Reset Something?         (0=Normal, 1=Reset)
  2    Unknown, always set      (1)
  3    Unknown, read/write-able (not used by e-Reader BIOS)
  4-7  Always zero              (0)
  8    Unknown, read/write-able (not used by e-Reader BIOS)
  9-15 Always zero              (0)
```

### DFC0000h..DFC0027h Scanline Data (R)

Scanline data (40 bytes, for 320 pixels, 1bit per pixel, 0=black, 1=white).

The first (leftmost) pixel is located in the LSB of the LAST byte.

Port E00FFB1h.Bit1 (and [4000202h].Bit13) indicates when a new scanline is
present, the data should be then transferred to RAM via DMA3 (SAD=DFC0000h,
DAD=buf+y\*28h, CNT=80000014h; a slower non-DMA transfer method would result in
missed scanlines). After the DMA, software must reset E00FFB1h.Bit1.

Note: The scanning resolution is 1000 DPI.

### DFC0028h+(0..2Fh\*2) Brightest Pixels of 8x6 Blocks (R)

```
  0-6  Max Brightness (00h..7Fh; 00h=All black, 7Fh=One or more white)
  7-15 Always zero
```

Can be used to adjust the Port E00FF80h..E00FFAFh settings.

### DFC0088h Darkest Pixel of whole Image (R)

```
  0-7  Max Darkness   (00h..7Fh; 00h=One or more black, 7Fh=All white)
  8-15 Always zero
```

Can be used to adjust the Port E00FF80h..E00FFAFh settings.

### E00FF80h..E00FFAFh Intensity Boundaries for 8x6 Blocks (R/W)

The 320x246 pixel camera input is split into 8x6 blocks (40x41 pixels each),
with Block00h=Upper-right, Block07h=Upper-left, ..., Block27h=Lower-left. The
boundary values for the separate blocks are used for 128-grayscale to 2-color
conversion, probably done like "IF Pixel>Boundary THEN white ELSE black".

```
  0-6  Block Intensity Boundaries (0..7Fh; 7Fh=Whole block gets black)
  7    Always zero
```

The default boundary values are stored in FLASH memory, the values are
typically ranging from 28h (outer edges) to 34h (center image), that in respect
to the light source (the two LEDs are emitting more light to the center
region).

### E00FFB0h Control Register 0 (R/W)

```
  0    Serial Data       (Low/High)
  1    Serial Clock      (Low/High)
  2    Serial Direction  (0=Input, 1=Output)
  3    Led/Irq Enable    (0=Off, 1=On; Enable LED and Gamepak IRQ)
  4    Start Scan        (0=Off, 1=Start) (0-to-1 --> Resync line 0)
  5    Phi 16MHz Output  (0=Off, 1=On; Enable Clock for Camera, and for LED)
  6    Power 3V Enable   (0=Off, 1=On; Enable 3V Supply for Camera)
  7    Not used          (always 0) (sometimes 1) (Read only)
```

### E00FFB1h Control Register 1 (R/W)

```
  0    Not used          (always 0)
  1    Scanline Flag     (1=Scanline Received, 0=Acknowledge)
  2-3  Not used          (always 0)
  4    Strange Bit       (0=Normal, 1=Force Resync/Line0 on certain interval?)
  5    LED Anode Voltage (0=3.0V, 1=5.1V; requires E00FFB0h.Bit3+5 to be set)
  6    Not used          (always 0)
  7    Input from PGA.Pin22, always high (not used by e-Reader) (Read Only)
```

Bit1 can be SET by hardware only, software can only RESET that bit, the Gamepak
IRQ flag (Port 4000202h.Bit13) becomes set on 0-to-1 transitions.

### E00FFB2h Light Source LED Kathode Duration (LSB) (R/W)

### E00FFB3h Light Source LED Kathode Duration (MSB) (R/W)

Selects the LED Kathode=LOW Duration, aka the LED=ON Duration. That does act as
pulse width modulated LED brightness selection (the camera seems to react
slowly enough to view the light as being dimmed to medium, rather than seeing
the actual light ON and OFF states). The PWM timer seems to be clocked at 8MHz.
The hardware clips timer values 2000h..FFFFh to max 2000h (=1ms). Additionally,
the e-Reader BIOS clips values to max 11B3h. Default setting is found in FLASH
calibration data. A value of 0000h disables the LED.

### Serial Port Registers (Camera Type 1) (DV488800) (calib\_data[3Ch]=1)

All 16bit values are ordered MSB,LSB. All registers are whole 8bit
Read/Write-able, except 00h,57h-5Ah (read only), and 53h-55h (2bit only).

```
  Port     Expl.               (e-Reader Setting)
  00h      Maybe Chip ID (12h) (not used by e-Reader BIOS) (Read Only)
  01h                          (05h)    ;-Bit0: 1=auto-repeat scanning?
  02h                          (0Eh)
  10h-11h  Vertical Scroll     (calib_data[30h]+7)
  12h-13h  Horizontal Scroll   (0030h)
  14h-15h  Vertical Size       (00F6h=246)
  16h-17h  Horizontal Size     (0140h=320)
  20h-21h  H-Blank Duration    (00C4h)
  22h-23h                      (0400h)  ;-Upper-Blanking in dot-clock units?
  25h                          (var)    ;-bit1: 0=enable [57h..5Ah] ?
  26h                          (var)    ;\maybe a 16bit value
  27h                          (var)    ;/
  28h                          (00h)
  30h      Brightness/contrast (calib_data[31h]+/-nn)
  31h-33h                      (014h,014h,014h)
  34h      Brightness/contrast (02h)
  50h-52h  8bit Read/Write     (not used by e-Reader BIOS)
  53h-55h  2bit Read/Write     (not used by e-Reader BIOS)
  56h      8bit Read/Write     (not used by e-Reader BIOS)
  57h-58h  16bit value, used to autodetect/adjust register[30h] (Read Only)
  59h-5Ah  16bit value, used to autodetect/adjust register[30h] (Read Only)
  80h-FFh  Mirrors of 00h..7Fh (not used by e-Reader BIOS)
```

All other ports are unused, writes to those ports are ignored, and reads are
returning data mirrored from other ports; that is typically data from 2 or more
ports, ORed together.

### Serial Port Registers (Camera Type 2) (calib\_data[3Ch]=2)

All 16bit values are using more conventional LSB,MSB ordering, and port numbers
are arranged in a more reasonable way. The e-Reader BIOS doesn't support (or
doesn't require) brightness adjustment for this camera module.

```
  Port     Expl.             (e-Reader Setting)
  00h                        (22h)
  01h                        (50h)
  02h-03h  Vertical Scroll   (calib_data[30h]+28h)
  04h-05h  Horizontal Scroll (001Eh)
  06h-07h  Vertical Size     (00F6h)    ;=246
  08h-09h  Horizontal Size   (0140h)    ;=320
  0Ah-0Ch                    (not used by e-Reader BIOS)
  0Dh                        (01h)
  0Eh-0Fh                    (01EAh)    ;=245*2
  10h-11h                    (00F5h)    ;=245
  12h-13h                    (20h,F0h)  ;maybe min/max values?
  14h-15h                    (31h,C0h)  ;maybe min/max values?
  16h                        (00h)
  17h-18h                    (77h,77h)
  19h-1Ch                    (30h,30h,30h,30h)
  1Dh-20h                    (80h,80h,80h,80h)
  21h-FFh                    (not used by e-Reader BIOS)
```

This appears to be a Micron (aka Aptina) camera (resembling the DSi cameras).

My own e-Reader uses a Type 1 camera module. Not sure if Nintendo has ever
manufactured any e-Readers with Type 2 cameras?

### Calibration Data in FLASH Memory (Bank 0, Sector 0Dh)

```
  E00D000 14h  ID String ('Card-E Reader 2001',0,0)
  E00D014 2    Sector Checksum (NOT(x+x/10000h); x=sum of all other halfwords)
```

Begin of actual data (40h bytes)

```
  E00D016 8x6  [00h] Intensity Boundaries for 8x6 blocks ;see E00FF80h..AFh
  E00D046 1    [30h] Vertical scroll (0..36h)  ;see type1.reg10h/type2.reg02h
  E00D047 1    [31h] Brightness or contrast    ;see type1.reg30h
  E00D048 2    [32h] LED Duration              ;see E00FFB2h..B3h
  E00D04A 2    [34h] Not used?   (0000h)
  E00D04C 2    [36h] Signed value, related to adjusting the 8x6 blocks
  E00D04E 4    [38h] Not used?   (00000077h)
  E00D052 4    [3Ch] Camera Type (0=none,1=DV488800,2=Whatever?)
```

Remaining bytes in this Sector...

```
  E00D056 FAAh Not used (zerofilled) (included in above checksum)
```

### Flowchart for Overall Camera Access

ereader\_scan\_camera:

```
 call ereader_power_on
 call ereader_initialize
 for z=1 to number_of_frames
  for y=0 to 245
   Wait until E00FFB1h.Bit1 gets set by hardware (can be handled by IRQ)
   Copy 14h halfwords from DFC0000h to buf+y*28h via DMA3
   Reset E00FFB1h.Bit1 by software
  next y
  ;(could now check DFC0028h..DFC0086h/DFC0088h for adjusting E00FF00h..2Fh)
  ;(could now show image on screen, that may require to stop/pause scanning)
 next z
 call ereader_power_off
 Ret
```

ereader\_power\_on:

```
 [4000204h]=5803h   ;Init waitstates, and enable Phi 16MHz
 [DFA0000h].Bit1=1
 Wait(10ms)
 [E00FFB0h]=40h     ;Enable Power3V and reset other bits
 [DFA0000h].Bit1=0
 [E00FFB1h]=20h     ;Enable Power5V and reset other bits
 Wait(40ms)
 [E00FFB1h].Bit4=0  ;...should be already 0 ?
 [E00FFB0h]=40h+27h ;Phi16MHz=On, SioDtaClkDir=HighHighOut
 Ret
```

ereader\_power\_off:

```
 [E00FFB0h]=04h    ;Power3V=Off, Disable Everything, SioDtaClkDir=LowLowOut
 [DFA0000h].Bit1=0 ;...should be already 0
 [E00FFB1h].Bit5=0 ;Power5V=Off
 Ret
```

ereader\_initialize:

```
 IF calib_data[3Ch] AND 03h = 1 THEN init_camera_type1
 [E00FFB0h].Bit4=1 ;ScanStart
 IF calib_data[3Ch] AND 03h = 2 THEN init_camera_type2
 Copy calib_data[00h..2Fh] to [E00FF80h+00h..2Fh]  ;Intensity Boundaries
 Copy calib_data[32h..33h] to [E00FFB2h+00h..01h]  ;LED Duration LSB,MSB
 [E00FFB0h].Bit3=1                                 ;LedIrqOn
 Ret
```

init\_camera\_type1:

```
 x=MIN(0,calib_data[31h]-0Bh)
 Set Sio Registers (as shown for Camera Type 1, except below values...)
 Set Sio Registers [30h]=x [25h]=04h, [26h]=58h, [27h]=6Ch
 ;(could now detect/adjust <x> based on Sio Registers [57h..5Ah])
 Set Sio Registers [30h]=x [25h]=06h, [26h]=E8h, [27h]=6Ch
 Ret
```

init\_camera\_type2:

- Wait(0.5ms)
- Set Sio Registers (as shown for Camera Type 2)
- Ret

### Accessing Serial Registers via E00FFB0h

```
      Begin   Write(A) Write(B) Read(C) Read(D) End     Idle    PwrOff
  Dir ooooooo ooooooo  ooooooo  iiiiiii iiiiiii ooooooo ooooooo ooooooo
  Dta ---____ AAAAAAA  BBBBBBB  xxxxxCx xxxxxDx ______- ------- _______
  Clk ------_ ___---_  ___---_  ___---_ ___---_ ___---- ------- _______
```

### Flowchart for accessing Serial Registers via E00FFB0h (looks like I2C bus)

- Delay:
  - Wait circa 2.5us, Ret
- SioBegin:
  - SioDta=1, SioDir=Out, SioClk=1, Delay, SioDta=0, Delay, SioClk=0, Ret
- SioEnd:
  - SioDta=0, SioDir=Out, Delay, SioClk=1, Delay, SioDta=1, Ret
- SioRead1bit:   ;out: databit
  - SioDir=In, Delay, SioClk=1, Delay, databit=SioDta, SioClk=0, Ret
- SioWrite1bit:  ;in: databit
  - SioDta=databit, SioDir=Out, Delay, SioClk=1, Delay, SioClk=0, Ret
- SioReadByte:   ;in: endflag - out: data
  - for i=7 to 0, data.bit\<i>=SioRead1bit, next i, SioWrite1bit(endflag), Ret
- SioWriteByte:  ;in: data - out: errorflag
  - for i=7 to 0, Delay(huh/why?), SioWrite1bit(data.bit\<i>), next i
  - errorflag=SioRead1bit, SioDir=Out(huh/why?), Ret
- SioWriteRegisters:  ;in: index, len, buffer
  - SioBegin
  - SioWriteByte(22h)        ;command (set\_index) (and write\_data)
  - SioWriteByte(index)      ;index
  - for i=0 to len-1
    - SioWriteByte(buffer[i]) ;write data (and auto-increment index)
  - next
  - SioEnd
  - ret
- SioReadRegisters:   ;in: index, len - out: buffer
  - SioBegin
  - SioWriteByte(22h)        ;command (set\_index) (without any write\_data here)
  - SioWriteByte(index)      ;index
  - SioBegin
  - SioWriteByte(23h)        ;command (read\_data) (using above index)
  - for i=0 to len-1
    - if i=len-1 then endflag=1 else endflag=0
    - buffer[i]=SioReadByte(endflag)  ;read data (and auto-increment index)
  - next
  - SioEnd
  - Ret

Caution: Accessing the SIO registers appears highly unstable, and seems to
require error handling with retries. Not sure what is causing that problem,
possibly the registers cannot be accessed during camera-data-scans...?

### WAITCNT

The e-Reader BIOS uses WAITCNT [4000204h]=5803h when accessing the PGA, that
is, gamepak 16.78MHz phi output (bit11-12=3), 8 waits for SRAM region
(bit0-1=3), gamepak prefetch enabled (bit14=1), also sets WS0 to 4,2 waits
(bit2-4=0), and sets WS2 to odd 4,8 waits (bit8-10=0). The WS2 (probably WS0
too) settings are nonsense, and should work with faster timings (the e-Reader
can be accessed in NDS mode, which doesn't support that slow timings).

### e-Reader Memory and I/O Map (with all used/unused/mirrored regions)

```
  C000000h-C7FFFFFh  ROM (8MB)
  C800000h-DF7FFFFh  Open Bus
  DF80000h-DF80001h  Useless Register (R/W)
  DF80002h-DF9FFFFh  Mirrors of DF80000h-DF80001h
  DFA0000h-DFA0001h  Reset Register (R/W)
  DFA0002h-DFBFFFFh  Mirrors of DFA0000h-DFA0001h
  DFC0000h-DFC0027h  Scanline Data (320 Pixels) (R)
  DFC0028h-DFC0087h  Brightest Pixels of 8x6 Blocks (R)
  DFC0088h           Darkest Pixel of whole Image (R)
  DFC0089h-DFC00FFh  Always zero
  DFC0100h-DFDFFFFh  Mirrors of DFC0000h-DFC00FFh
  DFE0000h-DFFFFFFh  Open Bus
  E000000h-E00CFFFh  FLASH Bank 0 - Data
  E00D000h-E00DFFFh  FLASH Bank 0 - Calibration Data
  E00E000h-E00EFFFh  FLASH Bank 0 - Copy of Calibration Data
  E00F000h-E00FF7Fh  FLASH Bank 0 - Unused region
  E000000h-E00EFFFh  FLASH Bank 1 - Data
  E00F000h-E00FF7Fh  FLASH Bank 1 - Unused region
  E00FF80h-E00FFAFh  Intensity Boundaries for 8x6 Blocks (R/W)
  E00FFB0h           Control Register 0 (R/W)
  E00FFB1h           Control Register 1 (R/W)
  E00FFB2h-E00FFB3h  LED Duration (16bit) (R/W)
  E00FFB4h-E00FFBFh  Always zero
  E00FFC0h-E00FFFFh  Mirror of E00FF80h-E00FFBFh
```

Mind that WS2 should be accessed by LDRH/STRH, and SRAM region by LDRB/STRB.

Additionally about 32 serial bus registers are contained in the camera module.

### Camera Module Notes

The Type 1 initial setting on power-on is 402x302 pixels, the e-Reader uses
only 320x246 pixels. The full vertical resolution could be probably used
without problems. Port DFC0000h-DFC0027h are restricted to 320 pixels, so
larger horizontal resolutions could be probably obtained only by changing the
horizontal scroll offset on each 2nd scan.

The camera output is 128 grayscales (via parallel 7bit databus), but the PGA
converts it to 2 colors (1bit depth). For still images, it might be possible to
get 4 grayshades via 3 scans with different block intensity boundary settings.

No idea if the camera supports serial commands other than 22h and 23h. Namely,
it \<would> be a quite obvious and basic feature to allow to receive the
bitmap via the 2-wire serial bus (alternately to the 7bit databus), if
supported, it'd allow to get 7bit images, bypassing 1bit PGA conversion.

When used as actual camera (by cutting an opening in the case), the main
problem is the 1bit color depth, which allows only black and white schemes,
when/if solving that problem, focusing might be also a problem.

Either the camera or the PGA seem to have a problem on white-to-black
transitions in vertical direction, the upper some black pixels are sorts of
getting striped or dithered. For example, scanning the large sync marks appears
as:

```
  Actual Shape    Scanned Shape
     XXXXX            X  X
    XXXXXXX           X  X X
   XXXXXXXXX        X X  X XX
   XXXXXXXXX        X X  X XX
    XXXXXXX          XXXXXXX
     XXXXX            XXXXX
```

That appears only on large black shapes (the smaller data dots look better).
Probably the image is scanned from bottom upwards (and the camera senses only
the initial transition at the bottom, and then looses track of what it is
doing).

## <a name="gbacartereaderdotcodeformat"></a>  GBA Cart e-Reader Dotcode Format

Resolution is 342.39 DPI (almost 10 blocks per inch).

Resolution is 134.8 dots/cm (almost 4 blocks per centimeter).

The width and height of each block, and the spacing to the bottom edge of the
card is ca. 1/10 inch, or ca. 4 millimeters.

```
   XXX            BLOCK 1             XXX            BLOCK 2             XXX
  XXXXX                              XXXXX                              XXXXX
  XXXXX   X X X X X X  X X X X X X   XXXXX   X X X X X X  X X X X X X   XXXXX
  XXXXX                              XXXXX                              XXXXX
   XXX   HHHHHHHHHHHHHHHHHHHH......   XXX   HHHHHHHHHHHHHHHHHHHH......   XXX
         ..........................         ..........................
         ...... 3 short lines .....         ..........................
    A..................................A..................................A..
    A....      26 long lines       ....A........ X = Sync Marks   ........A..
    A....  (each 34 data dots)     ....A........ H = Block Header ........A..
    A....(not all lines shown here)....A........ . = Data Bits    ........A..
    A..................................A........ A = Address Bits ........A..
         ...... 3 short lines .....         ..........................
         ...(each 26 data dots)....         ..........................
   XXX   ..........................   XXX   ..........................   XXX
  XXXXX                              XXXXX                              XXXXX
  XXXXX   X X X X X X  X X X X X X   XXXXX   X X X X X X  X X X X X X   XXXXX
  XXXXX                              XXXXX                              XXXXX
   XXX                                XXX                                XXX
             <ca. 35 blank lines>
  ___Snip____________________________________________________________________
```

### Address Columns

Each Column consists of 26 dots. From top to bottom: 1 black dot, 8 blank dots,
16 address dots (MSB topmost), and 1 blank dot. The 16bit address values can be
calculated as:

```
  addr[0] = 03FFh
  for i = 1 to 53
    addr[i] = addr[i-1] xor ((i and (-i)) * 769h)
    if (i and 07h)=0 then addr[i] = addr[i] xor (769h)
    if (i and 0Fh)=0 then addr[i] = addr[i] xor (769h*2)
    if (i and 1Fh)=0 then addr[i] = addr[i] xor (769h*4) xor (769h)
  next i
```

Short strips use addr[1..19], long strips use addr[25..53], left to right.

### Block Header

The 18h-byte Block Header is taken from the 1st two bytes (20 dots) of the 1st
0Ch blocks (and is then repeated in the 1st two bytes of further blocks).

```
  00h      Unknown              (00h)
  01h      Dotcode type         (02h=Short, 03h=Long)
  02h      Unknown              (00h)
  03h      Address of 1st Block (01h=Short, 19h=Long)
  04h      Total Fragment Size  (40h) ;64 bytes per fragment, of which,
                                      ;48 bytes are actual data, the remaining
  05h      Error-Info Size      (10h) ;16 bytes are error-info
  06h      Unknown              (00h)
  07h      Interleave Value     (1Ch=Short, 2Ch=Long)
  08h..17h 16 bytes Reed-solomon error correction info for Block Header
```

### Data 4-Bit to 5-bit Conversion

In the Block Header (HHHHH), and Data Region (.....), each 4bit are expanded to
5bit, so one byte occupies 10 dots, and each block (1040 data dots) contains
104 bytes.

```
  4bit  00h 01h 02h 03h 04h 05h 06h 07h 08h 09h 0Ah 0Bh 0Ch 0Dh 0Eh 0Fh
  5bit  00h 01h 02h 12h 04h 05h 06h 16h 08h 09h 0Ah 14h 0Ch 0Dh 11h 10h
```

That formatting ensures that there are no more than two continous black dots
(in horizontal direction), neither inside of a 5bit value, nor between two 5bit
values, however, the address bars are violating that rule, and up to 5
continous black dots can appear at the (..A..) block boundaries.

### Data Order

Data starts with the upper bit of the 5bit value for the upper 4bit of the
first byte, which is located at the leftmost dot of the upper line of the
leftmost block, it does then extend towards rightmost dot of that block, and
does then continue in the next line, until reaching the bottom of the block,
and does then continue in the next block. The 1st two bytes of each block
contain a portion of the Block Header, the remaining 102 bytes in each block
contain data.

### Data Size

A long strip consists of 28 blocks (28\*104 = 2912 bytes), a short strip of 18
blocks (18\*104 = 1872 bytes). Of which, less than 75% can be actually used for
program code, the remaining data contains error correction info, and various
headers. See Data Format for more info.

### Interleaved Fragments

The Interleave Value (I) specifies the number of fragments, and does also
specify the step to the next byte inside of a fragment; except that, at the
block boundaries (every 104 bytes), the step is 2 bigger (for skipping the next
two Block Header bytes).

```
  RAW Offset  Content
  000h..001h  1st 2 bytes of RAW Header
  002h        1st byte of 1st fragment
  003h        1st byte of 2nd fragment
  ...         ...
  002h+I-1    1st byte of last fragment
  002h+I      2nd byte of 1st fragment
  003h+I      2nd byte of 2nd fragment
  ...         ...
  002h+I*2-1  2nd byte of last fragment
  ...         ...
```

Each fragment consists of 48 actual data bytes, followed by 16 error correction
bytes, followed by 0..2 unused bytes (since I\*40h doesn't exactly match
num\_blocks\*102).

## <a name="gbacartereaderdataformat"></a>  GBA Cart e-Reader Data Format

### Data Strip Format

The size of the data region is I\*48 bytes (I=Interleave Value, see Dotcode
Format), the first 48-byte fragment contains the Data Header, the remaining
(I-1) fragments are Data Fragments (which contain title(s), and VPK compressed
program code).

### First Strip

```
  Data Header  (48 bytes)
  Main-Title   (17 bytes, or 33 bytes)
  Sub-Title(s) (3+18 bytes, or 33 bytes) (for each strip) (optional)
  VPK Size     (2 byte value, total length of VPK Data in ALL strips)
  NULL Value   (4 bytes, contained ONLY in 1st strip of GBA strips)
  VPK Data     (length as defined in VPK Size entry, see above)
```

### Further Strip(s)

```
  Data Header  (48 bytes)
  Main-Title   (17 bytes, or 33 bytes)
  Sub-Title(s) (3+18 bytes, or 33 bytes) (for each strip) (optional)
  VPK Data     (continued from previous strip)
```

### Data Header (30h bytes) (1st fragment)

```
  00h-01h  Fixed         (00h,30h)
  02h      Fixed         (01h)     ;01h="Do not calculate Global Checksum" ?
  03h      Primary Type  (see below)
  04h-05h  Fixed         (00h,01h) (don't care)
  06h-07h  Strip Size    (0510h=Short, 0810h=Long Strip) ((I-1)*30h) (MSB,LSB)
  08h-0Bh  Fixed         (00h,00h,10h,12h)
  0Ch-0Dh  Region/Type   (see below)
  0Eh      Strip Type    (02h=Short Strip, 01h=Long Strip) (don't care)
  0Fh      Fixed         (00h) (don't care)
  10h-11h  Unknown       (whatever) (don't care)
  12h      Fixed         (10h)     ;10h="Do calculate Data Checksum" ?
  13h-14h  Data Checksum (see below) (MSB,LSB)
  15h-19h  Fixed         (19h,00h,00h,00h,08h)
  1Ah-21h  ID String     ('NINTENDO')
  22h-25h  Fixed         (00h,22h,00h,09h)
  26h-29h  Size Info     (see below)
  2Ah-2Dh  Flags         (see below)
  2Eh      Header Checksum (entries [0Ch-0Dh,10h-11h,26h-2Dh] XORed together)
  2Fh      Global Checksum (see below)
```

Primary Type [03h] is 8bit,

```
  0      Card Type (upper bit) (see below)
  1      Unknown (usually opposite of Bit0) (don't care)
  2-7    Unknown (usually zero)
```

Region/Type [0Ch..0Dh] is 16bit,

```
  0-3    Unknown (don't care)
  4-7    Card Type (lower bits) (see below)
  8-11   Region/Version (0=Japan/Original, 1=Non-japan, 2=Japan/Plus)
  12-15  Unknown (don't care)
```

Size Info [26h-29h] is 32bit,

```
  0      Unknown            (don't care)
  1-4    Strip Number       (01h..Number of strips)
  5-8    Number of Strips   (01h..0Ch) (01h..08h for Japan/Original version)
  9-23   Size of all Strips (excluding Headers and Main/Sub-Titles)
         (same as "VPK Size", but also including the 2-byte "VPK Size" value,
         plus the 4-byte NULL value; if it is present)
  24-31  Fixed              (02h) (don't care)
```

Flags [2Ah-2Dh] is 32bit,

```
  0      Permission to save (0=Start Immediately, 1=Prompt for FLASH Saving)
  1      Sub-Title Flag     (0=Yes, 1=None)    (Japan/Original: always 0=Yes)
  2      Application Type   (0=GBA/Z80, 1=NES) (Japan/Original: always 0=Z80)
  3-31   Zero (0) (don't care)
```

Data Checksum [13h-14h] is the complement (NOT) of the sum of all halfwords in
all Data Fragments, however, it's all done in reversed byte order: checksum is
calculated with halfwords that are read in MSB,LSB order, and the resulting
checksum is stored in MSB,LSB order in the Header Fragment.

Global Checksum [2Fh] is the complement (NOT) of the sum of the first 2Fh bytes
in the Data Header plus the sum of all Data Fragment checksums; the Data
Fragment checksums are all 30h bytes in a fragment XORed with each other.

### Titles (3+N bytes, or N bytes)

Titles can be 33 bytes for both Main and Sub (Format 0Eh), or Main=17 bytes and
Sub=3+18 bytes (Formats 02h..05h). In the 3+N bytes form, the first 3 bytes
(24bit) are are used to display "stats" information in form of "HP: h1 ID:
i1-i2-i3", defined as:

```
  Bit    Expl.
  0-3    h1, values 1..15 shown as "10..150", value 0 is not displayed
  4-6    i3, values 0..7 shown as "A..G,#"
  7-13   i2, values 0..98 shown as "01..99" values 99..127 as "A0..C8"
  14-18  i1, values 0..31 shown as "A..Z,-,_,{HP},.,{ID?},:"
  19-22  Unknown
  23     Disable stats (0=Show as "HP: h1 ID: i1-i2-i3", 1=Don't show it)
```

The N bytes portion contains the actual title, which must be terminated by 00h
(so the max length is N-1 characters, if it is shorter than N-1, then the
unused bytes are padded by further 00h's). The character set is normal ASCII
for non-Japan (see Region/Version entry in header), and 2-byte SHIFT-JIS for
Japanese long-titles (=max 16 2-byte chars) with values as so:

```
  00h          --> end-byte
  81h,40h      --> SPC
  81h,43h..97h --> punctuation marks
  82h,4Fh..58h --> "0..9"
  82h,60h..79h --> "A..Z"
  82h,81h..9Ah --> "a..z"
```

And 1-byte chars for Japanese short-titles,

```
  00     = end-byte
  01     = spc
  02..0B = 0..9
  0C..AF = japanese
  B0..B4 = dash, male, female, comma, round-dot
  B5..C0 = !"%&~?/+-:.'
  C1..DA = A..Z
  DB..DF = unused (blank)
  E0..E5 = japanese
  E6..FF = a..z
  N/A    = #$()*;<=>@[\]^_`{|}
```

Additionally to the Main-Title, optional Sub-Titles for each strip can be
included (see Sub-Title Flag in header). If enabled, then ALL strip titles are
included in each strip (allowing to show a preview of which strips have/haven't
been scanned yet).

The e-Reader can display maximum of 8 sub-titles, if the data consists of more
than 8 strips, then sub-titles aren't displayed (so it'd be waste of space to
include them in the dotcodes).

The Main Title gets clipped to 128 pixels width (that are, circa 22
characters), and, the e-Reader BIOS acts confused on multi-strip games with
Main Titles longer than 26 characters (so the full 33 bytes may be used only in
Japan; with 16bit charset).

If the title is empty (00h-filled), and there is only one card in the
application, then the application is started immediately. That, without
allowing the user to save it in FLASH memory.

Caution: Although shorter Titles do save memory, they do act unpleasant: the
text "(C) P-Letter" will be displayed at the bottom of the loading screen.

On Japanese/Original, 8bit sub-titles can be up to 18 characters (without any
end-byte) (or less when stats are enabled, due to limited screen width).

### Card Types (Primary Type.Bit0 and Region/Type.Bit12-15)

```
  00h..01h  Blank Screen (?)
  02h..03h  Dotcode Application with 17byte-title, with stats, load music A
  04h..05h  Dotcode Application with 17byte-title, with stats, load music B
  06h..07h  P-Letter Attacks
  08h..09h  Construction Escape
  0Ah..0Bh  Construction Action
  0Ch..0Dh  Construction Melody Box
  0Eh       Dotcode Application with 33byte-title, without stats, load music A
  0Fh       Game specific cards
  10h..1Dh  P-Letter Viewer
  1Eh..1Fh  Same as 0Eh and 0Fh (see above)
```

The 'Application' types are meant to be executable GBA/Z80/NES programs.

## <a name="gbacartereaderprogramcode"></a>  GBA Cart e-Reader Program Code

The GBA/Z80/NES program code is stored in the VPK compressed area.

NES-type is indicated by header [2Ah].Bit2, GBA-type is indicated by the NULL
value inserted between VPK Size and VPK Data, otherwise Z80-type is used.

### GBA Format

Load Address and Entrypoint are at 2000000h (in ARM state). The 32bit word at
2000008h is eventually destroyed by the e-Reader. Namely,

```
  IF e-Reader is Non-Japanese,
  AND [2000008h] is outside of range of 2000000h..20000E3h,
  AND only if booted from camera (not when booted from FLASH?),
  THEN [2000008h]=[2000008h]-0001610Ch ELSE [2000008h] kept intact
```

Existing multiboot-able GBA binaries can be converted to e-Reader format by,

```
  Store "B 20000C0h" at 2000000h   ;redirect to RAM-entrypoint
  Zerofill 2000004h..20000BFh      ;erase header (for better compression rate)
  Store 01h,01h at 20000C4h        ;indicate RAM boot
```

The GBA code has full access to the GBA hardware, and may additionally use
whatever API functions contained in the e-Reader BIOS. With the incoming LR
register value, "mov r0,N, bx lr" returns to the e-Reader BIOS (with N being
0=Restart, or 2=To\_Menu). No idea if it's necessary to preserve portions of RAM
when returning to the e-Reader BIOS?

Caution: Unlike for normal GBA cartridges/multiboot files, the hardware is left
uninitialized when booting dotcodes (among others: sound DMA is active, and
brightness is set to zero), use "mov r0,0feh, swi 010000h" to get the normal
settings.

### NES Format

Emulates a NES (Nintendo Entertainment System) console (aka Family Computer).

The visible 240x224 pixel NES/NTSC screen resolution is resampled to 240x160 to
match the smaller vertical resolution of the GBA hardware. So, writing e-Reader
games in NES format will result in blurred screen output. The
screen/sound/joypad is accessed via emulated NES I/O ports, program code is
running on an emulated 6502 8bit CPU, for more info on the NES hardware, see
no$nes debugger specifications, or

```
  http://problemkaputt.de/everynes.htm
```

The e-Reader's NES emulator supports only 16K PRG ROM, followed by 8K VROM. The
emulation accuracy is very low, barely working with some of Nintendo's own NES
titles; running the no$nes diagnostics program on it has successfully failed on
ALL hardware tests ;-)

The load address for the 16K PRG-ROM is C000h, the 16bit NMI vector at [FFFAh]
is encrypted like so:

```
  for i=17h to 0
   for j=07h to 0, nmi = nmi shr 1, if carry then nmi = nmi xor 8646h, next j
   nmi = nmi xor (byte[dmca_data+i] shl 8)
  next i
  dmca_data: db 0,0,'DMCA NINTENDO E-READER'
```

The 16bit reset vector at [FFFCh] contains:

```
  Bit0-14  Lower bits of Entrypoint (0..7FFFh = Address 8000h..FFFFh)
  Bit15    Nametable Mode (0=Vertical Mirroring, 1=Horizontal Mirroring)
```

reportedly,

```
   (NES limitations, 1 16K program rom + 1-2 8K CHR rom, mapper 0 and 1)
   ines mapper 1 would be MMC1, rather than CNROM (ines mapper 3)?
   but, there are more or less NONE games that have 16K PRG ROM + 16K VROM?
```

The L+R Button key-combination allows to reset the NES, however, there seems to
be no way to return to the e-Reader BIOS.

### Z80/8080 Format

The e-Reader doesn't support the following Z80 opcodes:

```
  CB [Prefix]     E0 RET PO   E2 JP PO,nn   E4 CALL PO,nn   27 DAA    76 HALT
  ED [Prefix]     E8 RET PE   EA JP PE,nn   EC CALL PE,nn   D3 OUT (n),A
  DD [IX Prefix]  F3 DI       08 EX AF,AF'  F4 CALL P,nn    DB IN A,(n)
  FD [IY Prefix]  FB EI       D9 EXX        FC CALL M,nn    xx RST 00h..38h
```

That is leaving not more than six supported Z80 opcodes (DJNZ, JR, JR
c/nc/z/nz), everything else are 8080 opcodes. Custom opcodes are:

```
  76 WAIT A frames, D3 WAIT n frames, and C7/CF RST 0/8 used for API calls.
```

The load address and entrypoint are at 0100h in the emulated Z80 address space.
The Z80 doesn't have direct access to the GBA hardware, instead
video/sound/joypad are accessed via API functions, invoked via RST 0 and RST 8
opcodes, followed by an 8bit data byte, and with parameters in the Z80 CPU
registers. For example, "ld a,02h, rst 8, db 00h" does return to the e-Reader
BIOS.

The Z80/8080 emulation is incredibly inefficient, written in HLL code,
developed by somebody whom knew nothing about emulation nor about ARM nor about
Z80/8080 processors.

### Running GBA-code on Japanese/Original e-Reader

Original e-Reader supports Z80 code only, but can be tweaked to run GBA-code:

```
  retry:
   ld bc,data // ld hl,00c8h      ;src/dst
  lop:
   ld a,[bc] // inc bc // ld e,a  ;lsb
   ld a,[bc] // inc bc // ld d,a  ;msb
   dw 0bcfh ;aka rst 8 // db 0bh  ;[4000000h+hl]=de (DMA registers)
   inc hl // inc  hl // ld a,l
   cp a,0dch // jr nz,lop
  mod1 equ $+1
   dw 37cfh ;aka rst 8 // db 37h  ;bx 3E700F0h
  ;below executed only on jap/plus... on jap/plus, above 37cfh is hl=[400010Ch]
   ld a,3Ah // ld [mod1],a                  ;bx 3E700F0h (3Ah instead 37h)
   ld hl,1 // ld [mod2],hl // ld [mod3],hl  ;base (0200010Ch instead 0201610Ch)
   jr retry
  data:
  mod2 equ $+1
   dd loader         ;40000C8h dma2sad (loader)            ;\
   dd 030000F0h      ;40000CCh dma2dad (mirrored 3E700F0h) ; relocate loader
   dd 8000000ah      ;40000D0h dma2cnt (copy 0Ah x 16bit)  ;/
  mod3 equ $+1
   dd main           ;40000D4h dma3sad (main)              ;\prepare main reloc
   dd 02000000h      ;40000D8h dma3dad (2000000h)          ;/dma3cnt see loader
   .align 2          ;alignment for 16bit-halfword
  org $+201600ch     ;jap/plus: adjusted to org $+200000ch
  loader:
   mov r0,80000000h  ;(dma3cnt, copy 10000h x 16bit)
   mov r1,04000000h  ;i/o base
   strb r1,[r1,208h] ;ime=0 (better disable ime before moving ram)
   str r0,[r1,0DCh]  ;dma3cnt (relocate to 2000000h)
   mov r15,2000000h  ;start relocated code at 2000000h in ARM state
  main:
   ;...insert/append whatever ARM code here...
   end
```

## <a name="gbacartereaderapifunctions"></a>  GBA Cart e-Reader API Functions

### Z80 Interface (Special Opcodes)

```
  db 76h       ;Wait8bit A
  db D3h,xxh   ;Wait8bit xxh
  db C7h,xxh   ;RST0_xxh
  db CFh,xxh   ;RST8_xxh
  ld r,[00xxh]       ;get system values (addresses differ on jap/ori)
  ld r,[00C2h..C3h]  ;GetKeyStateSticky (jap/ori: 9F02h..9F03h)
  ld r,[00C4h..C5h]  ;GetKeyStateRaw    (jap/ori: 9F04h..9F05h)
  ld r,[00C0h..C1h]  ;see Exit and ExitRestart
  ld r,[00D0h..D3h]  ;see Mul16bit
```

For jap/ori, 9Fxxh isn't forwards compatible with jap/plus, so it'd be better
to check joypad via IoRead.

### GBA Interface

```
  bx [30075FCh] ;ApiVector ;in: r0=func_no,r1,r2,r3,[sp+0],[sp+4],[sp+8]=params
  bx lr         ;Exit      ;in: r0 (0=Restart, 2=To_Menu)
```

### Wait8bit/Wait16bit

The various Wait opcodes and functions are waiting as many frames as specified.
Many API functions have no effect until the next Wait occurs.

### Z80 RST0\_xxh Functions / GBA Functions 02xxh

```
  RST0_00h FadeIn, A speed, number of frames (0..x)
  RST0_01h FadeOut
  RST0_02h BlinkWhite
  RST0_03h  (?)
  RST0_04h  (?) blend_func_unk1
  RST0_05h  (?)
  RST0_06h  (?)
  RST0_07h  (?)
  RST0_08h  (?)
  RST0_09h  (?) _020264CC_check
  RST0_0Ah  (?) _020264CC_free
  RST0_0Bh N/A (bx 0)
  RST0_0Ch N/A (bx 0)
  RST0_0Dh N/A (bx 0)
  RST0_0Eh N/A (bx 0)
  RST0_0Fh N/A (bx 0)
  RST0_10h LoadSystemBackground, A number of background (1..101), E bg# (0..3)
  RST0_11h SetBackgroundOffset, A=bg# (0..3), DE=X, BC=Y
  RST0_12h SetBackgroundAutoScroll
  RST0_13h SetBackgroundMirrorToggle
  RST0_14h  (?)
  RST0_15h  (?)
  RST0_16h  (?) write_000000FF_to_02029494_
  RST0_17h  (?)
  RST0_18h  (?)
  RST0_19h SetBackgroundMode, A=mode (0..2)
  RST0_1Ah  (?)
  RST0_1Bh  (?)
  RST0_1Ch  (?)
  RST0_1Dh  (?)
  RST0_1Eh  (?)
  RST0_1Fh  (?)
  RST0_20h LayerShow
  RST0_21h LayerHide
  RST0_22h  (?)
  RST0_23h  (?)
  RST0_24h ... [20264DCh+A*20h+1Ah]=DE, [20264DCh+A*20h+1Ch]=BC
  RST0_25h  (?)
  RST0_26h  (?)
  RST0_27h  (?)
  RST0_28h  (?)
  RST0_29h  (?)
  RST0_2Ah  (?)
  RST0_2Bh  (?)
  RST0_2Ch  (?)
  RST0_2Dh LoadCustomBackground, A bg# (0..3), DE pointer to struct_background,
           max. tile data size = 3000h bytes, max. map data size = 1000h bytes
  RST0_2Eh GBA: N/A - Z80: (?)
  RST0_2Fh  (?)
  RST0_30h CreateSystemSprite, - -   (what "- -" ???)
  RST0_31h SpriteFree, HL sprite handle
  RST0_32h SetSpritePos, HL=sprite handle, DE=X, BC=Y
  RST0_33h  (?) sprite_unk2
  RST0_34h SpriteFrameNext
  RST0_35h SpriteFramePrev
  RST0_36h SetSpriteFrame, HL=sprite handle, E=frame number (0..x)
  RST0_37h  (?) sprite_unk3
  RST0_38h  (?) sprite_unk4
  RST0_39h SetSpriteAutoMove, HL=sprite handle, DE=X, BC=Y
  RST0_3Ah  (?) sprite_unk5
  RST0_3Bh  (?) sprite_unk6
  RST0_3Ch SpriteAutoAnimate
  RST0_3Dh  (?) sprite_unk7
  RST0_3Eh SpriteAutoRotateUntilAngle
  RST0_3Fh SpriteAutoRotateByAngle
  RST0_40h SpriteAutoRotateByTime
  RST0_41h  (?) sprite_unk8
  RST0_42h SetSpriteAutoMoveHorizontal
  RST0_43h SetSpriteAutoMoveVertical
  RST0_44h  (?) sprite_unk9
  RST0_45h SpriteDrawOnBackground
  RST0_46h SpriteShow, HL=sprite handle
  RST0_47h SpriteHide, HL=sprite handle
  RST0_48h SpriteMirrorToggle
  RST0_49h  (?) sprite_unk10
  RST0_4Ah  (?) sprite_unk11
  RST0_4Bh  (?) sprite_unk12
  RST0_4Ch GetSpritePos
  RST0_4Dh CreateCustomSprite
  RST0_4Eh  (?)
  RST0_4Fh  (?) sprite_unk14
  RST0_50h  (?) sprite_unk15
  RST0_51h  (?) sprite_unk16
  RST0_52h  (?) sprite_unk17
  RST0_53h  (?) sprite_unk18
  RST0_54h  (?)
  RST0_55h  (?) sprite_unk20
  RST0_56h  (?)
  RST0_57h SpriteMove
  RST0_58h  (?) sprite_unk22
  RST0_59h  (?) sprite_unk23
  RST0_5Ah  (?) sprite_unk24
  RST0_5Bh SpriteAutoScaleUntilSize, C=speed (higher value is slower),
           HL=sprite handle, DE=size (0100h = normal size,
           lower value = larger, higher value = smaller)
  RST0_5Ch SpriteAutoScaleBySize
  RST0_5Dh SpriteAutoScaleWidthUntilSize
  RST0_5Eh SpriteAutoScaleHeightBySize
  RST0_5Fh  (?)
  RST0_60h  (?)
  RST0_61h  (?)
  RST0_62h  (?)
  RST0_63h  (?)
  RST0_64h hl=[[2024D28h+a*4]+12h]
  RST0_65h  (?) sprite_unk25
  RST0_66h SetSpriteVisible, HL=sprite handle, E=(0=not visible, 1=visible)
  RST0_67h  (?) sprite_unk26
  RST0_68h  (?) set_sprite_unk27
  RST0_69h  (?) get_sprite_unk27
  RST0_6Ah  (?)
  RST0_6Bh  (?)
  RST0_6Ch  (?)
  RST0_6Dh  (?)
  RST0_6Eh hl=[hl+000Ah]  ;r0=[r1+0Ah]
  RST0_6Fh  (?)
  RST0_70h  (?)
  RST0_71h  (?)
  RST0_72h  (?)
  RST0_73h  (?)
  RST0_74h  (?)
  RST0_75h  (?)
  RST0_76h  (?)
  RST0_77h  (?)
  RST0_78h  (?)
  RST0_79h  (?)
  RST0_7Ah  (?)
  RST0_7Bh  (?)
  RST0_7Ch  (?) _0202FD2C_unk12
  RST0_7Dh Wait16bit ;HL=num_frames (16bit variant of Wait8bit opcode/function)
  RST0_7Eh SetBackgroundPalette, HL=src_addr, DE=offset, C=num_colors (1..x)
  RST0_7Fh GetBackgroundPalette(a,b,c)
  RST0_80h SetSpritePalette, HL=src_addr, DE=offset, C=num_colors (1..x)
  RST0_81h GetSpritePalette(a,b,c)
  RST0_82h ClearPalette
  RST0_83h  (?) _0202FD2C_unk11
  RST0_84h  (?)
  RST0_85h  (?)
  RST0_86h  (?)
  RST0_87h  (?) _0202FD2C_unk8
  RST0_88h  (?) _0202FD2C_unk7
  RST0_89h  (?)
  RST0_8Ah  (?) _0202FD2C_unk6
  RST0_8Bh  (?) _0202FD2C_unk5
  RST0_8Ch GBA: N/A - Z80: (?)
  RST0_8Dh GBA: N/A - Z80: (?)
  RST0_8Eh  (?)
  RST0_8Fh WindowHide
  RST0_90h CreateRegion, H=bg# (0..3), L=palbank# (0..15),
           D,E,B,C=x1,y1,cx,cy (in tiles), return: n/a (no$note: n/a ???)
  RST0_91h SetRegionColor
  RST0_92h ClearRegion
  RST0_93h SetPixel
  RST0_94h GetPixel
  RST0_95h DrawLine
  RST0_96h DrawRect
  RST0_97h  (?) _0202FD2C_unk4
  RST0_98h SetTextColor, A=region handle, D=color foreground (0..15),
           E=color background (0..15)
  RST0_99h DrawText, A=region handle, BC=pointer to text, D=X, E=Y
           (non-japan uses ASCII text, but japanese e-reader's use STH ELSE?)
  RST0_9Ah SetTextSize
  RST0_9Bh  (?) RegionUnk7
  RST0_9Ch  (?) _0202FD2C_unk3
  RST0_9Dh  (?) _0202FD2C_unk2
  RST0_9Eh  (?) _0202FD2C_unk1
  RST0_9Fh Z80: (?) - GBA: SetBackgroundModeRaw
  RST0_A0h  (?)
  RST0_A1h  (?)
  RST0_A2h  (?) RegionUnk6
  RST0_A3h GBA: N/A - Z80: (?)
  RST0_A4h GBA: N/A - Z80: (?)
  RST0_A5h  (?)
  RST0_A6h  (?)
  RST0_A7h  (?)
  RST0_A8h  (?)
  RST0_A9h  (?)
  RST0_AAh  (?)
  RST0_ABh  (?)
  RST0_ACh  (?)
  RST0_ADh  (?) RegionUnk5
  RST0_AEh [202FD2Ch+122h]=A
  RST0_AFh [202FD2Ch+123h]=A
  RST0_B0h [202FD2Ch+124h]=A
  RST0_B1h  (?)
  RST0_B2h  (?)
  RST0_B3h GBA: N/A - Z80: Sqrt   ;hl=sqrt(hl)
  RST0_B4h GBA: N/A - Z80: ArcTan ;hl=ArcTan2(hl,de)
  RST0_B5h Sine                   ;hl=sin(a)*de
  RST0_B6h Cosine                 ;hl=cos(a)*de
  RST0_B7h  (?)
  RST0_B8h  (?)
  RST0_B9h N/A (bx 0)
  RST0_BAh N/A (bx 0)
  RST0_BBh N/A (bx 0)
  RST0_BCh N/A (bx 0)
  RST0_BDh N/A (bx 0)
  RST0_BEh N/A (bx 0)
  RST0_BFh N/A (bx 0)
  Below Non-Japan and Japan/Plus only (not Japan/Ori)
  RST0_C0h GetTextWidth(a,b)
  RST0_C1h GetTextWidthEx(a,b,c)
  RST0_C2h  (?)
  RST0_C3h Z80: N/A (bx 0) - GBA: (?)
  RST0_C4h  (?)
  RST0_C5h  (?)
  RST0_C6h  (?)
  RST0_C7h  (?)
  RST0_C8h  (?)
  RST0_C9h  (?)
  RST0_CAh  (?)
  RST0_CBh  (?)
  RST0_CCh  (?)
  RST0_CDh N/A (bx lr)
  RST0_CEh ;same as RST0_3Bh, but with 16bit mask
  RST0_CFh ;same as RST0_3Eh, but with 16bit de
  RST0_D0h ;same as RST0_3Fh, but with 16bit de
  RST0_D1h ;same as RST0_5Bh, but with 16bit de
  RST0_D2h ;same as RST0_5Ch, but with 16bit de
  RST0_D3h ;same as RST0_5Dh, but with 16bit de
  RST0_D4h ;same as RST0_5Eh, but with 16bit de
  RST0_D5h  (?)
  RST0_D6h  (?)
  RST0_D7h ;[202FD2Ch+125h]=A
  RST0_D8h  (?)
  RST0_D9h  (?)
  RST0_DAh  (?)
  RST0_DBh ;A=[3003E51h]
  RST0_DCh ;[3004658h]=01h
  RST0_DDh DecompressVPKorNonVPK
  RST0_DEh FlashWriteSectorSingle(a,b)
  RST0_DFh FlashReadSectorSingle(a,b)
  RST0_E0h SoftReset
  RST0_E1h GetCartridgeHeader     ;[hl+0..BFh]=[8000000h..80000BFh]
  RST0_E2h GBA: N/A - Z80: bx hl  ;in: hl=addr, af,bc,de,sp=param, out: a
  RST0_E3h Z80: N/A (bx 0) - GBA: (?)
  RST0_E4h  (?)
  RST0_E5h  (?)
  RST0_E6h  (?)
  RST0_E7h  (?)
  RST0_E8h  (?)
  RST0_E9h ;[2029498h]=0000h
  RST0_EAh Z80: N/A (bx 0) - GBA: InitMemory(a)
  RST0_EBh  (?) BL_irq_sio_dma3
  RST0_ECh ;hl = [3003E30h]*100h + [3003E34h]
  RST0_EDh FlashWriteSectorMulti(a,b,c)
  RST0_EEh FlashReadPart(a,b,c)
  RST0_EFh ;A=((-([2029416h] xor 1)) OR (+([2029416h] xor 1))) SHR 31
  RST0_F0h  (?) _unk1
  RST0_F1h RandomInit     ;in: hl=random_seed
  RST0_F2h                         (?)
  Below Japan/Plus only
  RST0_F3h  (?)
  RST0_F4h  (?)
  RST0_F5h  (?)
  RST0_F6h  (?)
  RST0_F7h GBA: N/A - Z80: (?)
  Below is undefined/garbage (values as so in Z80 mode)
  Jap/Ori: RST0_C0h      N/A (bx 0)
  Jap/Ori: RST0_C1h..FFh Overlaps RST8 jump list
  Non-Jap: RST0_F3h..FFh Overlaps RST8 jump list
  Jap/Pls: RST0_F8h..FFh Overlaps RST8 jump list
```

### Z80 RST8\_xxh Functions / GBA Functions 01xxh

```
  RST8_00h GBA: N/A - Z80: Exit       ;[00C0h]=a ;(1=restart, 2=exit)
  RST8_01h GBA: N/A - Z80: Mul8bit    ;hl=a*e
  RST8_02h GBA: N/A - Z80: Mul16bit   ;hl=hl*de, s32[00D0h]=hl*de
  RST8_03h Div                        ;hl=hl/de
  RST8_04h DivRem                     ;hl=hl mod de
  RST8_05h PlaySystemSound            ;in: hl=sound_number
  RST8_06h  (?) sound_unk1
  RST8_07h Random8bit                 ;a=random(0..FFh)
  RST8_08h SetSoundVolume
  RST8_09h BcdTime                    ;[de+0..5]=hhmmss(hl*bc)
  RST8_0Ah BcdNumber                  ;[de+0..4]=BCD(hl), [de+5]=00h
  RST8_0Bh IoWrite                    ;[4000000h+hl]=de
  RST8_0Ch IoRead                     ;de=[4000000h+hl]
  RST8_0Dh GBA: N/A - Z80:  (?)
  RST8_0Eh GBA: N/A - Z80:  (?)
  RST8_0Fh GBA: N/A - Z80:  (?)
  RST8_10h GBA: N/A - Z80:  (?)
  RST8_11h DivSigned                  ;hl=hl/de, signed
  RST8_12h RandomMax                  ;a=random(0..a-1)
  RST8_13h SetSoundSpeed
  RST8_14h  hl=[202FD20h]=[2024CACh]
  RST8_15h  hl=[2024CACh]-[202FD20h]
  RST8_16h SoundPause
  RST8_17h SoundResume
  RST8_18h PlaySystemSoundEx
  RST8_19h IsSoundPlaying
  RST8_1Ah  (?)
  RST8_1Bh  (?)
  RST8_1Ch  (?)
  RST8_1Dh GetExitCount               ;a=[2032D34h]
  RST8_1Eh Permille                   ;hl=de*1000/hl
  RST8_1Fh GBA: N/A - Z80: ExitRestart;[2032D38h]=a, [00C0h]=0001h  ;a=?
  RST8_20h GBA: N/A - Z80: WaitJoypad ;wait until joypad<>0, set hl=joypad
  RST8_21h GBA: N/A - Z80:  (?)
  RST8_22h  (?) _sound_unk7
  RST8_23h  (?) _sound_unk8
  RST8_24h  (?) _sound_unk9
  RST8_25h  (?) _sound_unk10
  RST8_26h Mosaic     ;bg<n>cnt.bit6=a.bit<n>, [400004Ch]=de
  RST8_27h  (?)
  RST8_28h  (?)
  RST8_29h  (?)
  RST8_2Ah  (?) get_8bit_from_2030110h
  RST8_2Bh  (?)
  RST8_2Ch  (?) get_16bit_from_2030112h ;jap/ori: hl=[20077B2h]
  RST8_2Dh  (?) get_16bit_from_2030114h ;jap/ori: hl=[20077B4h]
  RST8_2Eh  (?)
  RST8_2Fh PlayCustomSound(a,b)
  Below not for Japanese/Original
  (the renumbered functions can be theoretically used on japanese/original)
  (but, doing so would blow forwards compatibility with japanese/plus)
  RST8_30h (ori: none)      GBA: N/A - Z80: (?)
  RST8_31h (ori: none)      PlayCustomSoundEx(a,b,c)
  RST8_32h (ori: RST8_30h)  BrightnessHalf   ;[4000050h]=00FFh,[4000054h]=0008h
  RST8_33h (ori: RST8_31h)  BrightnessNormal ;[4000050h]=0000h
  RST8_34h (ori: RST8_32h)  N/A (bx lr)
  RST8_35h (ori: RST8_33h)   (?)
  RST8_36h (ori: RST8_34h)  ResetTimer ;[400010Ch]=00000000h, [400010Eh]=A+80h
  RST8_37h (ori: RST8_35h)  GetTimer   ;hl=[400010Ch]
  RST8_38h (ori: none)      GBA: N/A - Z80:  (?)
  Below is undefined/reserved/garbage (values as so in Z80 mode)
  (can be used to tweak jap/ori to start GBA-code from inside of Z80-code)
  (that, after relocating code to 3000xxxh via DMA via IoWrite function)
  RST8_39h (ori: RST8_36h)  bx 0140014h
  RST8_3Ah (ori: RST8_37h)  bx 3E700F0h
  RST8_3Bh (ori: RST8_38h)  bx 3E70000h+1
  RST8_3Ch (ori: RST8_39h)  bx 3E703E6h+1
  RST8_3Dh (ori: RST8_3Ah)  bx 3E703E6h+1
  RST8_3Eh (ori: RST8_3Bh)  bx 3E703E6h+1
  RST8_3Fh (ori: RST8_3Ch)  bx 3E703E6h+1
  40h-FFh  (ori: 3Dh-FFh)   bx ...
```

### GBA Functions 03xxh (none such in Z80 mode)

```
  RSTX_00h Wait8bit  ;for 16bit: RST0_7Dh
  RSTX_01h GetKeyStateSticky()
  RSTX_02h GetKeyStateRaw()
  RSTX_03h  (?)
  RSTX_04h  (?)
```

## <a name="gbacartereadervpkdecompression"></a>  GBA Cart e-Reader VPK Decompression

### vpk\_decompress(src,dest)

```
  collected32bit=80000000h  ;initially empty (endflag in bit31)
  for i=0 to 3, id[i]=read_bits(8), next i, if id[0..3]<>'vpk0' then error
  dest_end=dest+read_bits(32)     ;size of decompressed data (of all strips)
  method=read_bits(8), if method>1 then error
  tree_index=0, read_huffman_tree, disproot=tree_index
  tree_index=tree_index+1, read_huffman_tree, lenroot=tree_index
  ;above stuff is contained only in the first strip. below loop starts at
  ;current location in first strip, and does then continue in further strips.
 decompress_loop:
  if read_bits(1)=0 then                   ;copy one uncompressed data byte,
    [dest]=read_bits(8), dest=dest+1       ;does work without huffman trees
  else
    if disproot=-1 or lenroot=-1 then error  ;compression does require trees
    disp=read_tree(disproot)
    if method=1   ;disp*4 is good for 32bit ARM opcodes
      if disp>2 then disp=disp*4-8 else disp=disp+4*read_tree(disproot)-7
    len=read_tree(lenroot)
    if len=0 or disp<=0 or dest+len-1>dest_end then error ;whoops
    for j=1 to len, [dest]=[dest-disp], dest=dest+1, next j
  if dest<dest_end then decompress_loop
  ret
```

### read\_bits(num)

```
  mov  data=0
  for i=1 to num
    shl collected32bit,1   ;move next bit to carry, or set zeroflag if empty
    if zeroflag
      collected32bit=[src+0]*1000000h+[src+1]*10000h+[src+2]*100h+[src+3]
      src=src+4            ;read data in 32bit units, in reversed byte-order
      carryflag=1          ;endbit
      rcl collected32bit,1 ;move bit31 to carry (and endbit to bit0)
    rcl data,1             ;move carry to data
  next i
  ret(data)
```

### read\_tree(root\_index)

```
  i=root_index
  while node[i].right<>-1  ;loop until reaching data node
    if read_bits(1)=1 then i=node[i].right else i=node[i].left
  i=node[i].left           ;get number of bits
  i=read_bits(i)           ;read that number of bits
  ret(i)                   ;return that value
```

### load\_huffman\_tree

```
  stacktop=sp
  if read_bits(1)=1 then tree_index=-1, ret  ;exit (empty)
  node[tree_index].right=-1                  ;indicate data node
  node[tree_index].left=read_bits(8)         ;store data value
  if read_bits(1)=1 then ret                 ;exit (only 1 data node at root)
  push tree_index                     ;save previous (child) node
  tree_index=tree_index+1
  jmp data_injump
 load_loop:
  push tree_index                     ;save previous (child) node
  tree_index=tree_index+1
  if read_bits(1)=1 then parent_node
 data_injump:
  node[tree_index].right=-1           ;indicate data node
  node[tree_index].left=read_bits(8)  ;store data value
  jmp load_loop
 parent_node:
  pop node[tree_index].right          ;store 1st child
  pop node[tree_index].left           ;store 2nd child
  if sp<>stacktop then jmp load_loop
  if read_bits(1)=0 then error        ;end bit (must be 1)
  ret
```

The best values for the huffman trees that I've found are 6,9,12-bit
displacements for method 0 (best for NES/Z80 code), and two less for method 1,
ie. 4,7,10-bit (best for GBA code). And 2,4,10-bit for the length values. The
smallest value in node 0, and the other values in node 10 and 11.

### Notes

The decompression works similar to the GBA BIOS'es LZ77 decompression function,
but without using fixed bit-widths of length=4bit and displacement=12bit,
instead, the bit-widths are read from huffman trees (which can also define
fixed bit-widths; if data is located directly in the root node).

Unlike the GBA BIOS'es Huffman decompression function, the trees are starting
with data entries, end are ending with the root entry. The above load function
deciphers the data, and returns the root index.

With the variable bit-widths, the VPK compression rate is quite good, only,
it's a pity that the length/disp values are zero-based, eg. for 2bit and 4bit
lengths, it'd be much better to assign 2bit as 2..5, and 4bit as 6..21.

### Non-VPK

The e-Reader additionally supports an alternate decompression function,
indicated by the absence of the "vpk0" ID, which supports compression of
increasing byte-values, which isn't useful for program code.

Bit15 of the VPK Size value seems to disable (de-)compression, the VPK Data
field is then containing plain uncompressed data.

## <a name="gbacartereadererrorcorrection"></a>  GBA Cart e-Reader Error Correction

The Error Correction Information that is appended at the end of the Block
Header & Data Fragments consists of standard Reed-Solomon codes, which are
also used for CD/DVD disks, DSL modems, and digital DVB television signals.
That info allows to locate and repair a number of invalid data bytes.

Below code shows how to create and verify error-info (but not how to do the
actual error correction). The dtalen,errlen values should be 18h,10h for the
Block Header, and 40h,10h for Data Fragments; the latter settings might be
possible to get changed to other values though?

### append\_error\_info(data,dtalen,errlen)

```
  reverse_byte_order(data,dtalen)
  zerofill_error_bytes(data,errlen)
  for i=dtalen-1 to errlen  ;loop across data portion
    z = rev[ data[i] xor data[errlen-1] ] ;
    for j=errlen-1 to 0     ;loop across error-info portion
    if j=0 then x=00h else x=data[j-1]
      if z<>FFh then
        y=gg[j], if y<>FFh then
          y=y+z, if y>=FFh then y=y-FFh
          x=x xor pow[y]
      data[j]=x
    next j
  next i
  invert_error_bytes(data,errlen)
  reverse_byte_order(data,dtalen)
```

### verify\_error\_info(data,dtalen,errlen)

```
  reverse_byte_order(data,dtalen)
  invert_error_bytes(data,errlen)
  make_rev(data,dtalen)
  for i=78h to 78h+errlen-1
    x=0, z=0
    for j=0 to dtalen-1
      y=data[j]
      if y<>FFh then
        y=y+z, if y>=FFh then y=y-FFh
        x=x xor pow[y]
      z=z+i, if z>=FFh then z=z-FFh
    next j
    if x<>0 then error
  next i
  ;(if errors occured, could correct them now)
  make_pow(data,dtalen)
  invert_error_bytes(data,errlen)
  reverse_byte_order(data,dtalen)
```

### make\_rev(data,len)

```
  for i=0 to len-1, data[i]=rev[data[i]], next i
```

### make\_pow(data,len)

```
  for i=0 to len-1, data[i]=pow[data[i]], next i
```

### invert\_error\_bytes(data,len)

```
  for i=0 to len-1, data[i]=data[i] xor FFh, next i
```

### zerofill\_error\_bytes(data,len)

```
  for i=0 to len-1, data[i]=00h, next i
```

### reverse\_byte\_order(data,len)

```
  for i=0 to (len-1)/2, x=data[i], data[i]=data[len-i], data[len-i]=x, next i
```

### create\_pow\_and\_rev\_tables

```
  x=01h, pow[FFh]=00h, rev[00h]=FFh
  for i=00h to FEh
    pow[i]=x, rev[x]=i, x=x*2, if x>=100h then x=x xor 187h
  next i
```

### create\_gg\_table

```
  gg[0]=pow[78h]
  for i=1 to errlen-1
    gg[i]=01h
    for j=i downto 0
      if j=0 then y=00h else y=gg[j-1]
      x=gg[j], if x<>00h then
        x=rev[x]+78h+i, if x>=FFh then x=x-FFh
        y=y xor pow[x]
      gg[j]=y
    next j
  next i
  make_rev(gg,errlen)
```

With above value of 78h, and errlen=10h, gg[00h..0Fh] will be always:

```
  00h,4Bh,EBh,D5h,EFh,4Ch,71h,00h,F4h,00h,71h,4Ch,EFh,D5h,EBh,4Bh
```

So using a hardcoded table should take up less memory than calculating it.

### Notes

The actual error correction should be able to fix up to "errlen" errors at
known locations (eg. data from blocks that haven't been scanned, or whose
5bit-to-4bit conversion had failed due to an invalid 5bit value), or up to
"errlen/2" errors at unknown locations. The corrected data isn't guaranteed to
be correct (even if it looks okay to the "verify" function), so the Data Header
checksums should be checked, too.

### More Info

For more info, I've found Reed-Solomon source code from Simon Rockliff, and an
updated version from Robert Morelos-Zaragoza and Hari Thirumoorthy to be
useful. For getting started with that source, some important relationships
& differences are:

```
  pow = alpha_to, but generated as shown above
  rev = index_of, dito
  b0  = 78h
  nn  = dtalen
  kk  = dtalen-errlen
  %nn = MOD FFh (for the ereader that isn't MOD dtalen)
  -1  = FFh
```

And, the ereader processes data/errinfo backwards, starting at the last byte.

## <a name="gbacartereaderfileformats"></a>  GBA Cart e-Reader File Formats

### .BMP Files (homebrew 300 DPI strips)

Contains a picture of the whole dotcode strip with address bars and sync marks
(see Dotcode chapter) in Microsoft's Bitmap format. The image is conventionally
surrounded by a blank 2-pixel border, resulting in a size of 989x44 pixels for
long strips. The file should should have 1bit color depth. The pixels per meter
entry should match the desired printing resolution, either 300 DPI or 360 DPI.
But, resolution of printer hardware is typically specified in inch rather than
in meters, so an exact match isn't supported by Microsoft. Most homebrew .BMP
files contain nonsense resolutions like 200 DPI, or 300 dots per meter (ca. 8
DPI).

### .JPG Files (scanned 1200 DPI strips)

Same as BMP, but should contain a dotcode scanned at 1200 DPI, with correct
orientation (the card-edge side at the bottom of the image), and containing
only the dotcode (not the whole card), so the JPG size should be about 3450x155
pixels for long strips.

No$gba currently doesn't work with progressive JPGs. Scans with white
background can be saved as monochrome JPG. Scans with red/yellow background
should contain a correct RED layer (due to the red LED light source) (the
brightness of the green/blue layers can be set to zero for better compression).

### .RAW Files

Contains the "raw" information from the BMP format, that is, 2-byte block
header, 102-byte data, 2-byte block header, 102-byte data, etc. The data
portion is interleaved, and includes the full 48-byte data header, titles, vpk
compressed data, error-info, and unused bytes. RAW files are excluding Address
Bars, Sync Marks, and 4bit-to-5bit encoding.

Each RAW file contains one or more strip(s), so the RAW filesize is either
18\*104 bytes (short strip), or 28\*104 bytes (long strip), or a multiple thereof
(if it contains more than one strip) (although multi-strip games are often
stored in separate files for each strip; named file1.raw, file2.raw, etc).

### .BIN Files

Filesize should be I\*30h, with I=1Ch for short strips, and I=2Ch for long
strips, or a multiple thereof (if it contains more than one strip). Each strip
consists of the 48-byte Data Header, followed by title(s), and vpk compressed
data. Unlike .RAW files, .BIN files aren't interleaved, and do not contain
Block Headers, nor error-info, nor unused bytes (in last block). The files do
contain padding bytes to match a full strip-size of I\*30h.

Caution: Older .BIN files have been using a size-reduced 12-byte header (taken
from entries 0Dh, 0Ch, 10h-11h, 26h-2Dh of the 48-byte Data Header; in that
order), that files have never contained more than one strip per file, so the
filesize should be exactly I\*30h-36, the size-reduced header doesn't contain a
Primary Type entry, so it's everyone's bet which Card Type is to be used (hint:
the 12-byte headers were based on the assumption that Primary Type would be
always 01h on Short Strips, and 02h on Long Strips).

### .SAV Files

Contains a copy of the e-Reader's 128Kbyte FLASH memory. With the saved
e-Reader application being located in the 2nd 64K-bank, the data consists of a
header with title and gba/nes/z80 format info, followed by the vpk compressed
data. The FLASH memory does also contain e-Reader calibration settings, the
remaining 100Kbytes are typically FFh-filled.

## <a name="gbacartunknowndevices"></a>  GBA Cart Unknown Devices

### GBA Infra-Red Port (AGB-006)

No info?

## <a name="gbacartprotections"></a>  GBA Cart Protections

### Classic NES Series

These are some NES/Famicom games ported or emulated to work on GBA. The games
are doing some uncommon stuff that can cause compatibility problems when not
using original GBA consoles or cartridges.

- CPU pipeline (selfmodifying code that shall NOT affect prefetched opcodes)

- STMDA write to I/O ports (writes in INCREASING order, not DECREASING order)

- SRAM detection (refuses to run if SRAM exists; the games do contain EEPROM)

- ROM mirrors (instead of the usual increasing numbers in unused ROM area)

- RAM mirrors (eg. main RAM accessed at 2F00000h instead of 2000000h)

Note: These games can be detected by checking [80000ACh]="F" (ie. game
code="Fxxx").

## <a name="gbaflashcards"></a>  GBA Flashcards

Flashcards are re-writable cartridges using FLASH memory, allowing to test even
multiboot-incompatible GBA software on real hardware, providing a good
development environment when used in combination with a reasonable software
debugger.

The carts can be written to from external tools, or directly from GBA programs.

Below are pseudo code flowcharts for detect, erase, and write operations.

All flash reads/writes are meant to be 16bit (ldrh/strh) memory accesses.

### detect\_flashcard:

```
 configure_flashcard(9E2468Ah,9413h)    ;unlock flash advance cards
 turbo=1, send_command(8000000h,90h)    ;enter ID mode (both chips, if any)
 maker=[8000000h], device=[8000000h+2]
 IF maker=device THEN device=[8000000h+4] ELSE turbo=0
 flashcard_read_mode                    ;exit ID mode
 search (maker+device*10000h) in device_list
 total/erase/write_block_size = list_entry SHL turbo
```

### flashcard\_erase(dest,len):

```
 FOR x=1 to len/erase_block_size
  send_command(dest,20h)        ;erase sector command
  send_command(dest,D0h)        ;confirm erase sector
  dest=dest+erase_block_size
 IF wait_busy=okay THEN NEXT x
 enter_read_mode                ;exit erase/status mode
```

### flashcard\_write(src,dest,len):

```
 siz=write_block_size
 FOR x=1 to len/siz
  IF siz=2 THEN send_command(dest,10h)  ;write halfword command
  IF siz>2 THEN send_command(dest,E8h)  ;write to buffer command
  IF siz>2 THEN send_command(dest,16-1) ;buffer size 16 halfwords (per chip)
  FOR y=1 TO siz/2
   [dest]=[src], dest=dest+2, src=src+2 ;write data to buffer
  NEXT y
  IF siz>2 THEN send_command(dest,D0h)  ;confirm write to buffer
 IF wait_busy=okay THEN NEXT x
 enter_read_mode                        ;exit write/status mode
```

### send\_command(adr,val):

- [adr]=val
- IF turbo THEN [adr+2]=val

### enter\_read\_mode:

```
 send_command(8000000h,FFh)     ;exit status mode
 send_command(8000000h,FFh)     ;again maybe more stable (as in jeff's source)
```

### flashcard\_wait\_busy:

- start=time
- REPEAT
  - stat=[8000000h] XOR 80h
  - IF turbo THEN stat=stat OR ([8000000h+2] XOR 80h)
  - IF (stat AND 7Fh)>0 THEN error
  - IF (stat AND 80h)=0 THEN ready
  - IF time-start>5secs THEN timeout
- UNTIL ready OR error OR timeout
- IF error OR timeout THEN send\_command(8000000h,50h)    ;clear status

### configure\_flashcard(adr,val):   ;required for Flash Advance cards only

- [930ECA8h]=5354h
- [802468Ah]=1234h, repeated 500 times
- [800ECA8h]=5354h
- [802468Ah]=5354h
- [802468Ah]=5678h, repeated 500 times
- [930ECA8h]=5354h
- [802468Ah]=5354h
- [8ECA800h]=5678h
- [80268A0h]=1234h
- [802468Ah]=ABCDh, repeated 500 times
- [930ECA8h]=5354h
- [adr]=val

### init\_backup:  ;no info how to use that exactly

```
 configure_flashcard(942468Ah,???)
```

### device\_list:  (id code, total/erase/write sizes in bytes)

```
  ID Code    Total   Erase  Write  Name
  -??-00DCh      ?       ?      ?  Hudson Cart (???)
  00160089h     4M    128K     32  Intel i28F320J3A (Flash Advance)
  00170089h     8M    128K     32  Intel i28F640J3A (Flash Advance)
  00180089h    16M    128K     32  Intel i28F128J3A (Flash Advance)
  00E200B0h      ?     64K      2  Sharp LH28F320BJE ? (Nintendo)
```

### Notes

All flashcards should work at 4,2 waitstates (power on default), most
commercial games change waits to 3,1 which may work unstable with some/older FA
flashcards. Intel FLASH specified to have a lifetime of 100,000 erases, and
average block erase time 1 second (up to 5 second in worst cases).

Aside from the main FLASH memory, Flash Advance (FA) (aka Visoly) cards
additionally contain battery buffered SRAM backup, and FLASH backup, and in
some cases also EEPROM backup.

Turbo FA cards are containing two chips interlaced (at odd/even halfword
addresses), allowing to write/erase both chips simultaneously, resulting in
twice as fast programming time.

Standard Nintendo flash carts have to be modified before you can actually write
to them. This is done by removing resistor R7 and putting it at empty location
R8.

Mind that write/erase/detect modes output status information in ROM area, so
that in that modes all GBA program code (and any interrupt handlers) must be
executed in WRAM, not in ROM.

Thanks to Jeff Frohwein for his FAQ and CARTLIB sample in FLGBA at devrs.com

## <a name="gbacheatdevices"></a>  GBA Cheat Devices

Codebreaker (US) aka Xploder (EUR).

Gameshark (US) aka Action Replay (EUR).

- [GBA Cheat Codes - General Info](#gbacheatcodesgeneralinfo)
- [GBA Cheat Codes - Codebreaker/Xploder](#gbacheatcodescodebreakerxploder)
- [GBA Cheat Codes - Gameshark/Action Replay V1/V2](#gbacheatcodesgamesharkactionreplayv1v2)
- [GBA Cheat Codes - Pro Action Replay V3](#gbacheatcodesproactionreplayv3)

## <a name="gbacheatcodesgeneralinfo"></a>  GBA Cheat Codes - General Info

Cheat devices are external adapters, connected between the GBA and the game
cartridge. The devices include a BIOS ROM which is, among others, used to
prompt the user to enter cheat codes.

These codes are used to patch specified memory locations for a certain GBA
game, allowing the user to gain goodies such like Infinite sex, 255 Cigarettes,
etc.

### ROM and RAM Patches

For ROM Patches, the device watches the address bus, if it matches a specified
address then it outputs a patched value to the data bus, that mechanism is
implemented by hardware, aside from the Hook Enable Code some devices also
allow a limited number of cheats to use ROM patches.

Most cheat codes are RAM patches, each time when the hook procedure is executed
it will process all codes and overwrite the specified addresses in RAM (or VRAM
or I/O area) by the desired values.

### Enable Codes (Must Be On)

Enable codes usually consist of the Game ID, Hook Address, and eventually a
third code used to encrypt all following codes. The Game ID is used to confirm
that the correct cartridge is inserted, just a verification, though the device
may insist on the ID code.

The Hook Address specifies an address in cartridge ROM, and should point to an
opcode which is executed several times per second (eg. once per frame, many
codes place the hook in the joypad handler). At the hook address, the device
redirects to its own BIOS, processes the RAM patches, and does then return
control to the game cartridge.

Note: The hook address should not point to opcodes with relative addressing
(eg. B, BL, LDR Rd,=Imm, ADD Rd,=Imm opcodes - which are all relative to PC
program counter register).

### Alignment

Addresses for 16bit or 32bit values should be properly aligned.

## <a name="gbacheatcodescodebreakerxploder"></a>  GBA Cheat Codes - Codebreaker/Xploder

### Codebreaker Codes

```
  0000xxxx 000y  Enable Code 1 - Game ID
  1aaaaaaa 000z  Enable Code 2 - Hook Address
  2aaaaaaa yyyy  [aaaaaaa]=[aaaaaaa] OR yyyy
  3aaaaaaa 00yy  [aaaaaaa]=yy
  4aaaaaaa yyyy  [aaaaaaa+0..(cccc-1)*ssss]=yyyy+0..(cccc-1)*ssss
  iiiicccc ssss  parameters for above code
  5aaaaaaa cccc  [aaaaaaa+0..(cccc-1)]=11,22,33,44,etc.
  11223344 5566  parameter bytes 1..6 for above code (example)
  77880000 0000  parameter bytes 7..8 for above code (padded with zero)
  6aaaaaaa yyyy  [aaaaaaa]=[aaaaaaa] AND yyyy
  7aaaaaaa yyyy  IF [aaaaaaa]=yyyy THEN (next code)
  8aaaaaaa yyyy  [aaaaaaa]=yyyy
  9xyyxxxx xxxx  Enable Code 0 - Encrypt all following codes (optional)
  Aaaaaaaa yyyy  IF [aaaaaaa]<>yyyy THEN (next code)
  Baaaaaaa yyyy  IF [aaaaaaa]>yyyy THEN (next code) (signed comparison)
  Caaaaaaa yyyy  IF [aaaaaaa]<yyyy THEN (next code) (signed comparison)
  D0000020 yyyy  IF [joypad] AND yyyy = 0 THEN (next code)
  Eaaaaaaa yyyy  [aaaaaaa]=[aaaaaaa]+yyyy
  Faaaaaaa yyyy  IF [aaaaaaa] AND yyyy THEN (next code)
```

### Codebreaker Enable Codes

Hook Address 'aaaaaaa' is a 25bit offset in ROM-image (0-1FFFFFFh).

Flag byte 'y' (usually 0Ah), Bit1=Disable IRQs, Bit3=CRC Exists.

Code Handler Store Address 'z' (0-7, usually 7) (8000100h+z\*400000h).

Checksum 'xxxx' for first 64Kbytes of cartridge (no$gba pads by FFh if ROM is
smaller than 64K). Calculated, by using unsigned 16bit values, as such:

```
  crc=FFFFh
  for i=0 to FFFFh
   x=byte[i] xor (crc/100h)
   x=x xor (x/10h)
   crc=(crc*100h) xor (x*1001h) xor (x*20h)
  next i
```

### Codebreaker Encryption

codebreaker\_change\_encryption:

Encryption can be (optionally) activated by code "9xyyxxxx xxxx",

```
  for i=0 to 2Fh, swaplist[i]=i, next i
  randomizer = 1111h xor byte[code+4]                              ;LSB value
  for i=0 to 4Fh
    exchange swaplist[random MOD 30h] with swaplist[random MOD 30h]
  next i
  halfword[seedlist+0] = halfword[code+0]                          ;LSW address
  randomizer = 4EFAD1C3h
  for i=0 to byte[code+3]-91h, randomizer=random, next i           ;MSB address
  word[seedlist+2]=random, halfword[seedlist+6]=random
  randomizer = F254h xor byte[code+5]                              ;MSB value
  for i=0 to byte[code+5]-01h, randomizer=random, next i           ;MSB value
  word[seedlist+8]=random, halfword[seedlist+12]=random
  ;note: byte[code+2] = don't care
  ret
```

The above random function works like so:

```
  randomizer=randomizer*41C64E6Dh+3039h, x=(randomizer SHL 14 AND C0000000h)
  randomizer=randomizer*41C64E6Dh+3039h, x=(randomizer SHR 1  AND 3FFF8000h)+x
  randomizer=randomizer*41C64E6Dh+3039h, x=(randomizer SHR 16 AND 00007FFFh)+x
  return(x)
```

Once when encryption is activated, all following codes are decrypted like so:

```
  for i=2Fh to 0
    j=swaplist[i]
    bitno1=(i AND 7), index1=xlatlist[i/8]
    bitno2=(j AND 7), index2=xlatlist[j/8]
    exchange [code+index1].bitno1 with [code+index2].bitno2
  next i
  word[code+0] = word[code+0] xor word[seedlist+8]
  i = (byte[code+3]*1010000h + byte[code+0]*100h + byte[code+5])
  i = (halfword[code+1]*10001h) xor (word[seedlist+2]) xor i
  i = (byte[seedlist+0]*1010101h) xor (byte[seedlist+1]*1000000h) xor i
  j = (byte[code+5] + (byte[code+0] xor byte[code+4])*100h)
  j = (byte[seedlist+0]*101h) xor halfword[seedlist+6] xor j
  word[code+0] = i, halfword[code+4] = j
```

The above xlatlist is fixed: xlatlist[0..5] = 3,2,1,0,5,4

## <a name="gbacheatcodesgamesharkactionreplayv1v2"></a>  GBA Cheat Codes - Gameshark/Action Replay V1/V2

### Gameshark RAW Codes (These codes must be encrypted before using them)

```
  0aaaaaaa 000000xx  [aaaaaaa]=xx
  1aaaaaaa 0000xxxx  [aaaaaaa]=xxxx
  2aaaaaaa xxxxxxxx  [aaaaaaa]=xxxxxxxx
  3000cccc xxxxxxxx  write xxxxxxxx to (cccc-1) addresses (list in next codes)
  aaaaaaaa aaaaaaaa  parameter for above code, containing two addresses each
  aaaaaaaa 00000000  last parameter for above, zero-padded if only one address
  60aaaaaa y000xxxx  [8000000h+aaaaaa*2]=xxxx (ROM Patch)
  8a1aaaaa 000000xx  IF GS_Button_Down THEN [a0aaaaa]=xx
  8a2aaaaa 0000xxxx  IF GS_Button_Down THEN [a0aaaaa]=xxxx
  80F00000 0000xxxx  IF GS_Button_Down THEN slowdown xxxx * ? cycles per hook
  Daaaaaaa 0000xxxx  IF [aaaaaaa]=xxxx THEN (next code)
  E0zzxxxx 0aaaaaaa  IF [aaaaaaa]=xxxx THEN (next 'zz' codes)
  Faaaaaaa 00000x0y  Enable Code - Hook Routine
  xxxxxxxx 001DC0DE  Enable Code - Game Code ID (value at [0ACh] in cartridge)
  DEADFACE 0000xxyy  Change Encryption Seeds
```

### Enable Code - Hook Routine

Hook Address 'aaaaaaa' is a 28bit ROM address (8FFFFFFh-9FFFFFFh).

Used to insert the GS code handler routine where it will be executed at

least 20 times per second. Without this code, GSA can not write to RAM.

- y=1 - Executes code handler without backing up the LR register.
- y=2 - Executes code handler and backs up the LR register.
- y=3 - Replaces a 32-bit pointer used for long-branches.
- x=0 - Must turn GSA off before loading game.
- x=1 - Must not do that.

### ROM Patch

This type allows GSA to intercept ROM reads and returns the value xxxx.

- y=0 wait for the code handler to enable the patch
- y=1 patch is enabled before the game starts
- y=2 unknown ?

Note: V1/V2 hardware can only have up to 1 user-defined rom patch max. V3 can
have up to 4. Some enable code types can shorten the amount of user-defined rom
patches available.

### Gameshark Encryption

A=Left half, and V=Right half of code.

```
  FOR I=1 TO 32
    A=A + (V*16+S0) XOR (V+I*9E3779B9h) XOR (V/32+S1)
    V=V + (A*16+S2) XOR (A+I*9E3779B9h) XOR (A/32+S3)
  NEXT I
```

Upon startup, the initial encryption seeds are:

```
  S0=09F4FBBDh S1=9681884Ah S2=352027E9h S3=F3DEE5A7h
```

Upon DEADFACE 0000xxyy, the S0..S3 seeds are changed like so:

```
  FOR y=0 TO 3
   FOR x=0 TO 3
    z = T1[(xx+x) AND FFh] + T2[(yy+y) AND FFh]
    Sy = Sy*100h + (z AND FFh)
   NEXT x
  NEXT y
```

All calculations truncated to unsigned 32bit integer values.

T1 and T2 are translation tables contained in the gameshark cartridge.

## <a name="gbacheatcodesproactionreplayv3"></a>  GBA Cheat Codes - Pro Action Replay V3

### Pro Action Replay V3 - RAW Codes

```
  C4aaaaaa 0000yyyy  Enable Code - Hook Routine at [8aaaaaa]
  xxxxxxxx 001DC0DE  Enable Code - ID Code [080000AC]
  DEADFACE 0000xxxx  Enable Code - Change Encryption Seeds
  00aaaaaa xxxxxxyy  [a0aaaaa..a0aaaaa+xxxxxx]=yy
  02aaaaaa xxxxyyyy  [a0aaaaa..a0aaaaa+xxxx*2]=yyyy
  04aaaaaa yyyyyyyy  [a0aaaaa]=yyyyyyyy
  40aaaaaa xxxxxxyy  [ [a0aaaaa] + xxxxxx ]=yy   (Indirect)
  42aaaaaa xxxxyyyy  [ [a0aaaaa] + xxxx*2 ]=yyyy (Indirect)
  44aaaaaa yyyyyyyy  [ [a0aaaaa] ]=yyyyyyyy      (Indirect)
  80aaaaaa 000000yy  [a0aaaaa]=[a0aaaaa]+yy
  82aaaaaa 0000yyyy  [a0aaaaa]=[a0aaaaa]+yyyy
  84aaaaaa yyyyyyyy  [a0aaaaa]=[a0aaaaa]+yyyyyyyy
  C6aaaaaa 0000yyyy  [4aaaaaa]=yyyy              (I/O Area)
  C7aaaaaa yyyyyyyy  [4aaaaaa]=yyyyyyyy          (I/O Area)
  iiaaaaaa yyyyyyyy  IF [a0aaaaa] <cond> <value> THEN <action>
  00000000 60000000  ELSE (?)
  00000000 40000000  ENDIF (?)
  00000000 0800xx00  AR Slowdown : loops the AR xx times
  00000000 00000000  End of the code list
  00000000 10aaaaaa 000000zz 00000000  IF AR_BUTTON THEN [a0aaaaa]=zz
  00000000 12aaaaaa 0000zzzz 00000000  IF AR_BUTTON THEN [a0aaaaa]=zzzz
  00000000 14aaaaaa zzzzzzzz 00000000  IF AR_BUTTON THEN [a0aaaaa]=zzzzzzzz
  00000000 18aaaaaa 0000zzzz 00000000  [8000000+aaaaaa*2]=zzzz  (ROM Patch 1)
  00000000 1Aaaaaaa 0000zzzz 00000000  [8000000+aaaaaa*2]=zzzz  (ROM Patch 2)
  00000000 1Caaaaaa 0000zzzz 00000000  [8000000+aaaaaa*2]=zzzz  (ROM Patch 3)
  00000000 1Eaaaaaa 0000zzzz 00000000  [8000000+aaaaaa*2]=zzzz  (ROM Patch 4)
```

```
  00000000 80aaaaaa 000000yy ssccssss  repeat cc times [a0aaaaa]=yy
   (with yy=yy+ss, a0aaaaa=a0aaaaa+ssss after each step)
```

```
  00000000 82aaaaaa 0000yyyy ssccssss  repeat cc times [a0aaaaa]=yyyy
   (with yyyy=yyyy+ss, a0aaaaa=a0aaaaa+ssss*2 after each step)
```

```
  00000000 84aaaaaa yyyyyyyy ssccssss  repeat cc times [a0aaaaa]=yyyyyyyy
   (with yyyy=yyyy+ss, a0aaaaa=a0aaaaa+ssss*4 after each step)
```

Warning: There is a bug on the real AR (v2 upgraded to v3, and maybe on real
v3) with the 32bit Increment Slide code. You HAVE to add a code (best choice is
80000000 00000000 : add 0 to value at address 0) right after it, else the AR
will erase the 2 last 8 digits lines of the 32 Bits Inc. Slide code when you
enter it !!!

Final Notes

The 'turn off all codes' makes an infinite loop (that can't be broken, unless
the condition becomes True). - How? By Interrupt? Huh?

ROM Patch1 works on real V3 and, on V1/V2 upgraded to V3.

ROM Patch2,3,4 work on real V3 hardware only.

### Pro Action Replay V3 Conditional Codes - iiaaaaaa yyyyyyyy

The 'ii' is composed of \<cond> + \<value> + \<action>.

```
  <cond>           <value>            <action>
  08 Equal =       00 8bit zz         00 execute next code
  10 Not equal <>  02 16bit zzzz      40 execute next two codes
  18 Signed <      04 32bit zzzzzzzz  80 execute all following
  20 Signed >      06 (always false)     codes until ELSE or ENDIF
  28 Unsigned <                       C0 normal ELSE turn off all codes
  30 Unsigned >
  38 Logical AND
```

For example, ii=18h+02h+40h=5Ah, produces IF [a0aaaaa]\<zzzz THEN next 2
codes.

Always... Codes

```
  For the "Always..." codes:
  - XXXXXXXX can be any authorised address except 00000000 (eg. use 02000000).
  - ZZZZZZZZ can be anything.
  - The "y" in the code data must be in the [1-7] range (which means not 0).
  typ=y,sub=0,siz=3   Always skip next line.
  typ=y,sub=1,siz=3   Always skip next 2 lines.
  typ=y,sub=2,siz=3   Always Stops executing all the codes below.
  typ=y,sub=3,siz=3   Always turn off all codes.
```

### Code Format (ttaaaaaa xxxxyyzz)

- adr mask = 003FFFFF
- n/a mask = 00C00000 ;not used
- xtr mask = 01000000 ;used only by I/O write, and MSB of Hook
- siz mask = 06000000
- typ mask = 38000000 ;0=normal, other=conditional
- sub mask = C0000000

### Pro Action Replay V3 Encryption

Works exactly as for Gameshark Encryption, but with different initial seeds,

```
  S0=7AA9648Fh S1=7FAE6994h S2=C0EFAAD5h S3=42712C57h
```

And, the T1 and T2 translation tables are different, too.

## <a name="gbagameboyplayer"></a>  GBA Gameboy Player

The Gameboy Player is an "adapter" for the Gamecube console. It's basicly is a
GBA in a black box without LCD screen and without buttons, connected to an
expansion port at the bottom of the Gamecube. The Gamecube is then capturing
the GBA video output (and passing it to the television set), and in the other
direction, passing the Gamecube joypad input to the GBA inputs.

### Unlocking and Detecting Gameboy Player Functions

Both unlocking and detection requires to display the 240x160 pixel Gameboy
Player logo (44 colors) for a number of frames... maybe at least 3-4 frames?
not sure if it checks the color of the logo... so maybe it can be hidden by
using dark gray on black background?

While displaying this logo, the joypad data will switch between values 03FFh (2
frames duration) and 030Fh (1 frame duration). The latter value (left, right,
up, down all pressed) indicates that it's a Gameboy Player.

### Palette

Knowing Nintendo, they've probably not reproduced the blurred GBA colors (?),
so the games won't look as desired on the TV screen. Unless the game does
detect the Gameboy Player, and adjust the colors accordingly by software.

### Rumble

The only known existing special function is the joypad rumble function,
controlled by sending data through the serial port (the normal GBA port, even
though it also has the connectors).

The Game Boy Player added a rumble feature to certain Game Boy Advance games
when played with a GameCube controller. Those games included:

- Drill Dozer (supports BOTH handheld-rumble and GBP-rumble?)
- Mario & Luigi: Superstar Saga
- Pokemon Pinball: Ruby & Sapphire
- Shikakui Atama wo Marukusuru Advance: Kokugo Sansu Rika Shakai
- Shikakui Atama wo Marukusuru Advance: Kanji Keisan
- Summon Night Craft Sword Monogatari: Hajimari no Ishi
- Super Mario Advance 4: Super Mario Bros. 3

Fredrik Olsson (aka Flubba) has implemented rumble in 3 applications now
RumblePong (FluBBA) (homebrew)

```
  Remudvance (FluBBA) (homebrew)
  Goomba (FluBBA) (8bit Gameboy Color Emulator for 32bit GBA) (homebrew)
  and, supposedly in "Tetanus on Drugs" (Tepples) (homebrew)
```

The GBP can also use some of the extra controllers for the GC like the Bongas

from Donkey Konga.

The logo requires at least 256 colors, it doesn't matter if you use a tiled

screen mode or a bitmapped one, the logo can be ripped from either

"Pokemon Pinball" or "Super Mario Advance 4".

### Rumble

After detecting/unlocking the Gameboy Player, init RCNT and SIOCNT to 32bit
normal mode, external clock, SO=high, with IRQ enabled, and set the transfer
start bit. You should then receive the following sequence (about once per
frame), and your serial IRQ handler should send responses accordingly:

```
  Receive  Response
  0000494E 494EB6B1
  xxxx494E 494EB6B1
  B6B1494E 544EB6B1
  B6B1544E 544EABB1
  ABB1544E 4E45ABB1
  ABB14E45 4E45B1BA
  B1BA4E45 4F44B1BA
  B1BA4F44 4F44B0BB
  B0BB4F44 8000B0BB
  B0BB8002 10000010
  10000010 20000013
  20000013 40000004
  30000003 40000004
  30000003 40000004
  30000003 40000004
  30000003 400000yy
  30000003 40000004
```

The first part of the transfer just contains the string "NINTENDO" split into
16bit fragments, and bitwise inversions thereof (eg. 494Eh="NI", and B6B1h=NOT
494Eh). In the second part, \<yy> should be 04h=RumbleOff, or
26h=RumbleOn.

### Note

If it's having a similar range of functions as the 8bit Super Gameboy, then the
Gameboy Player might be also able to access analogue joypad input, and to
access other features of the Gamecube hardware, up to possibly executing code
on the Gamecube CPU...?

## <a name="gbaunpredictablethings"></a>  GBA Unpredictable Things

### Forward

Most of the below is caused by 'traces' from previous operations which have
used the databus. No promises that the results are stable on all current or
future GBA models, and/or under all temperature and interference circumstances.

Also, below specifies 32bit data accesses only. When reading units less than
32bit, data is rotated depending on the alignment of the originally specified
address, and 8bit or 16bit are then isolated from the 32bit value as usually.

### Reading from BIOS Memory (00000000-00003FFF)

The BIOS memory is protected against reading, the GBA allows to read opcodes or
data only if the program counter is located inside of the BIOS area. If the
program counter is not in the BIOS area, reading will return the most recent
successfully fetched BIOS opcode (eg. the opcode at [00DCh+8] after startup and
SoftReset, the opcode at [0134h+8] during IRQ execution, and opcode at
[013Ch+8] after IRQ execution, and opcode at [0188h+8] after SWI execution).

### Reading from Unused Memory (00004000-01FFFFFF,10000000-FFFFFFFF)

Accessing unused memory at 00004000h-01FFFFFFh, and 10000000h-FFFFFFFFh (and
02000000h-03FFFFFFh when RAM is disabled via Port 4000800h) returns the
recently pre-fetched opcode. For ARM code this is simply:

```
  WORD = [$+8]
```

For THUMB code the result consists of two 16bit fragments and depends on the
address area and alignment where the opcode was stored.

For THUMB code in Main RAM, Palette Memory, VRAM, and Cartridge ROM this is:

```
  LSW = [$+4], MSW = [$+4]
```

For THUMB code in BIOS or OAM (and in 32K-WRAM on Original-NDS (in GBA mode)):

```
  LSW = [$+4], MSW = [$+6]   ;for opcodes at 4-byte aligned locations
  LSW = [$+2], MSW = [$+4]   ;for opcodes at non-4-byte aligned locations
```

For THUMB code in 32K-WRAM on GBA, GBA SP, GBA Micro, NDS-Lite (but not NDS):

```
  LSW = [$+4], MSW = OldHI   ;for opcodes at 4-byte aligned locations
  LSW = OldLO, MSW = [$+4]   ;for opcodes at non-4-byte aligned locations
```

Whereas OldLO/OldHI are usually:

```
  OldLO=[$+2], OldHI=[$+2]
```

Unless the previous opcode's prefetch was overwritten; that can happen if the
previous opcode was itself an LDR opcode, ie. if it was itself reading data:

```
  OldLO=LSW(data), OldHI=MSW(data)
  Theoretically, this might also change if a DMA transfer occurs.
```

Note: Additionally, as usually, the 32bit data value will be rotated if the
data address wasn't 4-byte aligned, and the upper bits of the 32bit value will
be masked in case of LDRB/LDRH reads.

Note: The opcode prefetch is caused by the prefetch pipeline in the CPU itself,
not by the external gamepak prefetch, ie. it works for code in ROM and RAM as
well.

### Reading from Unused or Write-Only I/O Ports

Works like above Unused Memory when the entire 32bit memory fragment is Unused
(eg. 0E0h) and/or Write-Only (eg. DMA0SAD). And otherwise, returns zero if the
lower 16bit fragment is readable (eg. 04Ch=MOSAIC, 04Eh=NOTUSED/ZERO).

### Reading from GamePak ROM when no Cartridge is inserted

Because Gamepak uses the same signal-lines for both 16bit data and for lower
16bit halfword address, the entire gamepak ROM area is effectively filled by
incrementing 16bit values (Address/2 AND FFFFh).

### Memory Mirrors

Most internal memory is mirrored across the whole 24bit/16MB address space in
which it is located: Slow On-board RAM at 2XXXXXX, Fast On-Chip RAM at
3XXXXXXh, Palette RAM at 5XXXXXXh, VRAM at 6XXXXXXh, and OAM at 7XXXXXXh. Even
though VRAM is sized 96K (64K+32K), it is repeated in steps of 128K
(64K+32K+32K, the two 32K blocks itself being mirrors of each other).

BIOS ROM, Normal ROM Cartridges, and I/O area are NOT mirrored, the only
exception is the undocumented I/O port at 4000800h (repeated each 64K).

The 64K SRAM area is mirrored across the whole 32MB area at E000000h-FFFFFFFh,
also, inside of the 64K SRAM field, 32K SRAM chips are repeated twice.

### Writing 8bit Data to Video Memory

Video Memory (BG, OBJ, OAM, Palette) can be written to in 16bit and 32bit units
only. Attempts to write 8bit data (by STRB opcode) won't work:

Writes to OBJ (6010000h-6017FFFh) (or 6014000h-6017FFFh in Bitmap mode) and to
OAM (7000000h-70003FFh) are ignored, the memory content remains unchanged.

Writes to BG (6000000h-600FFFFh) (or 6000000h-6013FFFh in Bitmap mode) and to
Palette (5000000h-50003FFh) are writing the new 8bit value to BOTH upper and
lower 8bits of the addressed halfword, ie. "[addr AND NOT 1]=data\*101h".

### Using Invalid Tile Numbers

In Text mode, large tile numbers (combined with a non-zero character base
setting in BGnCNT register) may exceed the available 64K of BG VRAM.

On GBA and GBA SP, such invalid tiles are displayed as if the character data is
filled by the 16bit BG Map entry value (ie. as vertically striped tiles). Above
applies only if there is only one BG layer enabled, with two or more layers,
things are getting much more complicated: tile-data is then somehow derived
from the other layers, depending on their priority order and scrolling offsets.

On NDS (in GBA mode), such invalid tiles are displayed as if the character data
is zero-filled (ie. as invisible/transparent tiles).

### Accessing SRAM Area by 16bit/32bit

Reading retrieves 8bit value from specified address, multiplied by 0101h (LDRH)
or by 01010101h (LDR). Writing changes the 8bit value at the specified address
only, being set to LSB of (source\_data ROR (address\*8)).

## <a name="armcpureference"></a>  ARM CPU Reference

### General ARM7TDMI Information

- [ARM CPU Overview](#armcpuoverview)
- [ARM CPU Register Set](#armcpuregisterset)
- [ARM CPU Flags & Condition Field (cond)](#armcpuflagsconditionfieldcond)
- [ARM CPU 26bit Memory Interface](#armcpu26bitmemoryinterface)
- [ARM CPU Exceptions](#armcpuexceptions)
- [ARM CPU Memory Alignments](#armcpumemoryalignments)

### ARM 32bit Instruction Set (ARM Code)

- [ARM Instruction Summary](#arminstructionsummary)
- [ARM Opcodes: Branch and Branch with Link (B, BL, BX, BLX, SWI, BKPT)](#armopcodesbranchandbranchwithlinkbblbxblxswibkpt)
- [ARM Opcodes: Data Processing (ALU)](#armopcodesdataprocessingalu)
- [ARM Opcodes: Multiply and Multiply-Accumulate (MUL, MLA)](#armopcodesmultiplyandmultiplyaccumulatemulmla)
- [ARM Opcodes: Special ARM9 Instructions (CLZ, QADD/QSUB)](#armopcodesspecialarm9instructionsclzqaddqsub)
- [ARM Opcodes: PSR Transfer (MRS, MSR)](#armopcodespsrtransfermrsmsr)
- [ARM Opcodes: Memory: Single Data Transfer (LDR, STR, PLD)](#armopcodesmemorysingledatatransferldrstrpld)
- [ARM Opcodes: Memory: Halfword, Doubleword, and Signed Data Transfer](#armopcodesmemoryhalfworddoublewordandsigneddatatransfer)
- [ARM Opcodes: Memory: Block Data Transfer (LDM, STM)](#armopcodesmemoryblockdatatransferldmstm)
- [ARM Opcodes: Memory: Single Data Swap (SWP)](#armopcodesmemorysingledataswapswp)
- [ARM Opcodes: Coprocessor Instructions (MRC/MCR, LDC/STC, CDP, MCRR/MRRC)](#armopcodescoprocessorinstructionsmrcmcrldcstccdpmcrrmrrc)

### ARM 16bit Instruction Set (THUMB Code)

When operating in THUMB state, cut-down 16bit opcodes are used.

THUMB is supported on T-variants of ARMv4 and up, ie. ARMv4T, ARMv5T, etc.

- [THUMB Instruction Summary](#thumbinstructionsummary)
- [THUMB Opcodes: Register Operations (ALU, BX)](#thumbopcodesregisteroperationsalubx)
- [THUMB Opcodes: Memory Load/Store (LDR/STR)](#thumbopcodesmemoryloadstoreldrstr)
- [THUMB Opcodes: Memory Addressing (ADD PC/SP)](#thumbopcodesmemoryaddressingaddpcsp)
- [THUMB Opcodes: Memory Multiple Load/Store (PUSH/POP and LDM/STM)](#thumbopcodesmemorymultipleloadstorepushpopandldmstm)
- [THUMB Opcodes: Jumps and Calls](#thumbopcodesjumpsandcalls)

### Note

Switching between ARM and THUMB state can be done by using the Branch and
Exchange (BX) instruction.

### Further Information

- [ARM Pseudo Instructions and Directives](#armpseudoinstructionsanddirectives)
- [ARM CP15 System Control Coprocessor](#armcp15systemcontrolcoprocessor)
- [ARM CPU Instruction Cycle Times](#armcpuinstructioncycletimes)
- [ARM CPU Versions](#armcpuversions)
- [ARM CPU Data Sheet](#armcpudatasheet)

## <a name="armcpuoverview"></a>  ARM CPU Overview

The ARM7TDMI is a 32bit RISC (Reduced Instruction Set Computer) CPU, designed
by ARM (Advanced RISC Machines), and designed for both high performance and low
power consumption.

### Fast Execution

Depending on the CPU state, all opcodes are sized 32bit or 16bit (that's
counting both the opcode bits and its parameters bits) providing fast decoding
and execution. Additionally, pipelining allows - (a) one instruction to be
executed while (b) the next instruction is decoded and (c) the next instruction
is fetched from memory - all at the same time.

### Data Formats

The CPU manages to deal with 8bit, 16bit, and 32bit data, that are called:

```
   8bit - Byte
  16bit - Halfword
  32bit - Word
```

### The two CPU states

As mentioned above, two CPU states exist:

- ARM state: Uses the full 32bit instruction set (32bit opcodes)

- THUMB state: Uses a cutdown 16bit instruction set (16bit opcodes)

Regardless of the opcode-width, both states are using 32bit registers, allowing
32bit memory addressing as well as 32bit arithmetic/logical operations.

### When to use ARM state

Basically, there are two advantages in ARM state:

- - Each single opcode provides more functionality, resulting
    - in faster execution when using a 32bit bus memory system
    - (such like opcodes stored in GBA Work RAM).
- - All registers R0-R15 can be accessed directly.

The downsides are:

- - Not so fast when using 16bit memory system
    - (but it still works though).
- - Program code occupies more memory space.

### When to use THUMB state

There are two major advantages in THUMB state:

- - Faster execution up to approx 160% when using a 16bit bus
    - memory system (such like opcodes stored in GBA GamePak ROM).
- - Reduces code size, decreases memory overload down to approx 65%.

The disadvantages are:

- - Not as multi-functional opcodes as in ARM state, so it will
    - be sometimes required use more than one opcode to gain a
    - similar result as for a single opcode in ARM state.
- - Most opcodes allow only registers R0-R7 to be used directly.

### Combining ARM and THUMB state

Switching between ARM and THUMB state is done by a normal branch (BX)
instruction which takes only a handful of cycles to execute (allowing to change
states as often as desired - with almost no overload).

Also, as both ARM and THUMB are using the same register set, it is possible to
pass data between ARM and THUMB mode very easily.

The best memory & execution performance can be gained by combining both
states: THUMB for normal program code, and ARM code for timing critical
subroutines (such like interrupt handlers, or complicated algorithms).

Note: ARM and THUMB code cannot be executed simultaneously.

### Automatic state changes

Beside for the above manual state switching by using BX instructions, the
following situations involve automatic state changes:

- CPU switches to ARM state when executing an exception

- User switches back to old state when leaving an exception

## <a name="armcpuregisterset"></a>  ARM CPU Register Set

### Overview

The following table shows the ARM7TDMI register set which is available in each
mode. There's a total of 37 registers (32bit each), 31 general registers (Rxx)
and 6 status registers (xPSR).

Note that only some registers are 'banked', for example, each mode has it's own
R14 register: called R14, R14\_fiq, R14\_svc, etc. for each mode respectively.

However, other registers are not banked, for example, each mode is using the
same R0 register, so writing to R0 will always affect the content of R0 in
other modes also.

```
  System/User FIQ       Supervisor Abort     IRQ       Undefined
  --------------------------------------------------------------
  R0          R0        R0         R0        R0        R0
  R1          R1        R1         R1        R1        R1
  R2          R2        R2         R2        R2        R2
  R3          R3        R3         R3        R3        R3
  R4          R4        R4         R4        R4        R4
  R5          R5        R5         R5        R5        R5
  R6          R6        R6         R6        R6        R6
  R7          R7        R7         R7        R7        R7
  --------------------------------------------------------------
  R8          R8_fiq    R8         R8        R8        R8
  R9          R9_fiq    R9         R9        R9        R9
  R10         R10_fiq   R10        R10       R10       R10
  R11         R11_fiq   R11        R11       R11       R11
  R12         R12_fiq   R12        R12       R12       R12
  R13 (SP)    R13_fiq   R13_svc    R13_abt   R13_irq   R13_und
  R14 (LR)    R14_fiq   R14_svc    R14_abt   R14_irq   R14_und
  R15 (PC)    R15       R15        R15       R15       R15
  --------------------------------------------------------------
  CPSR        CPSR      CPSR       CPSR      CPSR      CPSR
  --          SPSR_fiq  SPSR_svc   SPSR_abt  SPSR_irq  SPSR_und
  --------------------------------------------------------------
```

### R0-R12 Registers (General Purpose Registers)

These thirteen registers may be used for whatever general purposes. Basically,
each is having same functionality and performance, ie. there is no 'fast
accumulator' for arithmetic operations, and no 'special pointer register' for
memory addressing.

However, in THUMB mode only R0-R7 (Lo registers) may be accessed freely, while
R8-R12 and up (Hi registers) can be accessed only by some instructions.

### R13 Register (SP)

This register is used as Stack Pointer (SP) in THUMB state. While in ARM state
the user may decided to use R13 and/or other register(s) as stack pointer(s),
or as general purpose register.

As shown in the table above, there's a separate R13 register in each mode, and
(when used as SP) each exception handler may (and MUST!) use its own stack.

### R14 Register (LR)

This register is used as Link Register (LR). That is, when calling to a
sub-routine by a Branch with Link (BL) instruction, then the return address
(ie. old value of PC) is saved in this register.

Storing the return address in the LR register is obviously faster than pushing
it into memory, however, as there's only one LR register for each mode, the
user must manually push its content before issuing 'nested' subroutines.

Same happens when an exception is called, PC is saved in LR of new mode.

Note: In ARM mode, R14 may be used as general purpose register also, provided
that above usage as LR register isn't required.

### R15 Register (PC)

R15 is always used as program counter (PC). Note that when reading R15, this
will usually return a value of PC+nn because of read-ahead (pipelining),
whereas 'nn' depends on the instruction and on the CPU state (ARM or THUMB).

### CPSR and SPSR (Program Status Registers) (ARMv3 and up)

The current condition codes (flags) and CPU control bits are stored in the CPSR
register. When an exception arises, the old CPSR is saved in the SPSR of the
respective exception-mode (much like PC is saved in LR).

For details refer to chapter about CPU Flags.

## <a name="armcpuflagsconditionfieldcond"></a>  ARM CPU Flags & Condition Field (cond)

### ARM Condition Field {cond}

The opcode {cond} suffixes can be used for conditionally executed code based on
the C,N,Z,V flags in CPSR register. For example: BEQ = Branch if Equal, MOVMI =
Move if Signed.

In ARM mode, {cond} can be used with all opcodes (except for a few newer ARMv5
instructions: BKPT, PLD, CDP2, LDC2, MCR2, MRC2, STC2, and BLX\_imm are
nonconditional; however BLX\_reg can be conditional).

In THUMB mode, {cond} can be used only for branch opcodes.

```
  Code Suffix Flags         Meaning
  0:   EQ     Z=1           equal (zero) (same)
  1:   NE     Z=0           not equal (nonzero) (not same)
  2:   CS/HS  C=1           unsigned higher or same (carry set)
  3:   CC/LO  C=0           unsigned lower (carry cleared)
  4:   MI     N=1           negative (minus)
  5:   PL     N=0           positive or zero (plus)
  6:   VS     V=1           overflow (V set)
  7:   VC     V=0           no overflow (V cleared)
  8:   HI     C=1 and Z=0   unsigned higher
  9:   LS     C=0 or Z=1    unsigned lower or same
  A:   GE     N=V           greater or equal
  B:   LT     N<>V          less than
  C:   GT     Z=0 and N=V   greater than
  D:   LE     Z=1 or N<>V   less or equal
  E:   AL     -             always (the "AL" suffix can be omitted)
  F:   NV     -             never (ARMv1,v2 only) (Reserved ARMv3 and up)
```

Execution Time: If condition=false: 1S cycle. Otherwise: as specified for the
respective opcode.

### Current Program Status Register (CPSR)

```
  Bit   Expl.
  31    N - Sign Flag       (0=Not Signed, 1=Signed)               ;\
  30    Z - Zero Flag       (0=Not Zero, 1=Zero)                   ; Condition
  29    C - Carry Flag      (0=Borrow/No Carry, 1=Carry/No Borrow) ; Code Flags
  28    V - Overflow Flag   (0=No Overflow, 1=Overflow)            ;/
  27    Q - Sticky Overflow (1=Sticky Overflow, ARMv5TE and up only)
  26-8  Reserved            (For future use) - Do not change manually!
  7     I - IRQ disable     (0=Enable, 1=Disable)                     ;\
  6     F - FIQ disable     (0=Enable, 1=Disable)                     ; Control
  5     T - State Bit       (0=ARM, 1=THUMB) - Do not change manually!; Bits
  4-0   M4-M0 - Mode Bits   (See below)                               ;/
```

### Bit 31-28: Condition Code Flags (N,Z,C,V)

These bits reflect results of logical or arithmetic instructions. In ARM mode,
it is often optionally whether an instruction should modify flags or not, for
example, it is possible to execute a SUB instruction that does NOT modify the
condition flags.

In ARM state, all instructions can be executed conditionally depending on the
settings of the flags, such like MOVEQ (Move if Z=1). While In THUMB state,
only Branch instructions (jumps) can be made conditionally.

### Bit 27: Sticky Overflow Flag (Q) - ARMv5TE and ARMv5TExP and up only

Used by QADD, QSUB, QDADD, QDSUB, SMLAxy, and SMLAWy only. These opcodes set
the Q-flag in case of overflows, but leave it unchanged otherwise. The Q-flag
can be tested/reset by MSR/MRS opcodes only.

### Bit 27-8: Reserved Bits (except Bit 27 on ARMv5TE and up, see above)

These bits are reserved for possible future implementations. For best forwards
compatibility, the user should never change the state of these bits, and should
not expect these bits to be set to a specific value.

### Bit 7-0: Control Bits (I,F,T,M4-M0)

These bits may change when an exception occurs. In privileged modes (non-user
modes) they may be also changed manually.

The interrupt bits I and F are used to disable IRQ and FIQ interrupts
respectively (a setting of "1" means disabled).

The T Bit signalizes the current state of the CPU (0=ARM, 1=THUMB), this bit
should never be changed manually - instead, changing between ARM and THUMB
state must be done by BX instructions.

The Mode Bits M4-M0 contain the current operating mode.

```
  Binary Hex Dec  Expl.
  0xx00b 00h 0  - Old User       ;\26bit Backward Compatibility modes
  0xx01b 01h 1  - Old FIQ        ; (supported only on ARMv3, except ARMv3G,
  0xx10b 02h 2  - Old IRQ        ; and on some non-T variants of ARMv4)
  0xx11b 03h 3  - Old Supervisor ;/
  10000b 10h 16 - User (non-privileged)
  10001b 11h 17 - FIQ
  10010b 12h 18 - IRQ
  10011b 13h 19 - Supervisor (SWI)
  10111b 17h 23 - Abort
  11011b 1Bh 27 - Undefined
  11111b 1Fh 31 - System (privileged 'User' mode) (ARMv4 and up)
```

Writing any other values into the Mode bits is not allowed.

### Saved Program Status Registers (SPSR\_\<mode>)

Additionally to above CPSR, five Saved Program Status Registers exist:

SPSR\_fiq, SPSR\_svc, SPSR\_abt, SPSR\_irq, SPSR\_und

Whenever the CPU enters an exception, the current status register (CPSR) is
copied to the respective SPSR\_\<mode> register. Note that there is only
one SPSR for each mode, so nested exceptions inside of the same mode are
allowed only if the exception handler saves the content of SPSR in memory.

For example, for an IRQ exception: IRQ-mode is entered, and CPSR is copied to
SPSR\_irq. If the interrupt handler wants to enable nested IRQs, then it must
first push SPSR\_irq before doing so.

## <a name="armcpu26bitmemoryinterface"></a>  ARM CPU 26bit Memory Interface

The 26bit Memory Interface was used by ARMv1 and ARMv2. The 32bit interface is
used by ARMv3 and newer, however, 26bit backward compatibility was included in
all ARMv3 (except ARMv3G), and optionally in some non-T variants of ARMv4.

### Format of R15 in 26bit Mode (Program Counter Register)

```
  Bit   Name     Expl.
  31-28 N,Z,C,V  Flags (Sign, Zero, Carry, Overflow)
  27-26 I,F      Interrupt Disable bits (IRQ, FIQ) (1=Disable)
  25-2  PC       Program Counter, 24bit, Step 4 (64M range)
  1-0   M1,M0    Mode (0=User, 1=FIQ, 2=IRQ, 3=Supervisor)
```

Branches with +/-32M range wrap the PC register, and can reach all 64M memory.

### Reading from R15

If R15 is specified in bit16-19 of an opcode, then NZCVIF and M0,1 are masked
(zero), otherwise the full 32bits are used.

### Writing to R15

ALU opcodes with S=1, and LDM opcodes with PSR=1 can write to all 32bits in R15
(in 26bit mode, that is allowed even in user mode, though it does then affect
only NZCF, not the write protected IFMM bits ???), other opcodes which write to
R15 will modify only the program counter bits. Also, special CMP/CMN/TST/TEQ{P}
opcodes can be used to write to the PSR bits in R15 without modifying the PC
bits.

### Exceptions

SWIs, Reset, Data/Prefetch Aborts and Undefined instructions enter Supervisor
mode. Interrupts enter IRQ and FIQ mode. Additionally, a special 26bit Address
Exception exists, which enters Supervisor mode on accesses to memory
addresses>=64M as follows:

```
  R14_svc = PC ($+8, including old PSR bits)
  M1,M0 = 11b = supervisor mode, F=same, I=1, PC=14h,
  to continue at the fault location, return by SUBS PC,LR,8.
```

32bit CPUs with 26bit compatibility mode can be configured to switch into 32bit
mode when encountering exceptions.

## <a name="armcpuexceptions"></a>  ARM CPU Exceptions

### Exception Vectors

The following are the exception vectors in memory. That is, when an exception
arises, CPU is switched into ARM state, and the program counter (PC) is loaded
by the respective address.

```
  Address  Prio  Exception                  Mode on Entry      Interrupt Flags
  BASE+00h 1     Reset                      Supervisor (_svc)  I=1, F=1
  BASE+04h 7     Undefined Instruction      Undefined  (_und)  I=1, F=unchanged
  BASE+08h 6     Software Interrupt (SWI)   Supervisor (_svc)  I=1, F=unchanged
  BASE+0Ch 5     Prefetch Abort             Abort      (_abt)  I=1, F=unchanged
  BASE+10h 2     Data Abort                 Abort      (_abt)  I=1, F=unchanged
  BASE+14h ??    Address Exceeds 26bit      Supervisor (_svc)  I=1, F=unchanged
  BASE+18h 4     Normal Interrupt (IRQ)     IRQ        (_irq)  I=1, F=unchanged
  BASE+1Ch 3     Fast Interrupt (FIQ)       FIQ        (_fiq)  I=1, F=1
```

BASE is normally 00000000h, but may be optionally FFFF0000h in some ARM CPUs.
Priority for simultaneously occuring exceptions ranges from Prio=1=Highest to
Prio=7=Lowest.

As there's only space for one ARM opcode at each of the above addresses, it'd
be usually recommended to deposit a Branch opcode into each vector, which'd
then redirect to the actual exception handlers address.

### Actions performed by CPU when entering an exception

```
  - R14_<new mode>=PC+nn   ;save old PC, ie. return address
  - SPSR_<new mode>=CPSR   ;save old flags
  - CPSR new T,M bits      ;set to T=0 (ARM state), and M4-0=new mode
  - CPSR new I bit         ;IRQs disabled (I=1), done by ALL exceptions
  - CPSR new F bit         ;FIQs disabled (F=1), done by Reset and FIQ only
  - PC=exception_vector    ;see table above
```

Above "PC+nn" depends on the type of exception. Basically, in ARM state that
nn-offset is caused by pipelining, and in THUMB state an identical ARM-style
'offset' is generated (even though the 'base address' may be only
halfword-aligned).

### Required user-handler actions when returning from an exception

Restore any general registers (R0-R14) which might have been modified by the
exception handler. Use return-instruction as listed in the respective
descriptions below, this will both restore PC and CPSR - that automatically
involves that the old CPU state (THUMB or ARM) as well as old state of FIQ and
IRQ disable flags are restored.

As mentioned above (see action on entering...), the return address is always
saved in ARM-style format, so that exception handler may use the same
return-instruction, regardless of whether the exception has been generated from
inside of ARM or THUMB state.

### FIQ (Fast Interrupt Request)

This interrupt is generated by a LOW level on the nFIQ input. It is supposed to
process timing critical interrupts at a high priority, as fast as possible.

Additionally to the common banked registers (R13\_fiq,R14\_fiq), five extra
banked registers (R8\_fiq-R12\_fiq) are available in FIQ mode. The exception
handler may freely access these registers without modifying the main programs
R8-R12 registers (and without having to save that registers on stack).

In privileged (non-user) modes, FIQs may be also manually disabled by setting
the F Bit in CPSR.

### IRQ (Normal Interrupt Request)

This interrupt is generated by a LOW level on the nIRQ input. Unlike FIQ, the
IRQ mode is not having its own banked R8-R12 registers.

IRQ is having lower priority than FIQ, and IRQs are automatically disabled when
a FIQ exception becomes executed. In privileged (non-user) modes, IRQs may be
also manually disabled by setting the I Bit in CPSR.

To return from IRQ Mode (continuing at following opcode):

```
  SUBS PC,R14,4   ;both PC=R14_irq-4, and CPSR=SPSR_irq
```

### Software Interrupt

Generated by a software interrupt instruction (SWI). Recommended to request a
supervisor (operating system) function. The SWI instruction may also contain a
parameter in the 'comment field' of the opcode:

In case that your main program issues SWIs from both inside of THUMB and ARM
states, then your exception handler must separate between 24bit comment fields
in ARM opcodes, and 8bit comment fields in THUMB opcodes (if necessary
determine old state by examining T Bit in SPSR\_svc); However, in Little Endian
mode, you could use only the most significant 8bits of the 24bit ARM comment
field (as done in the GBA, for example) - the exception handler could then
process the BYTE at [R14-2], regardless of whether it's been called from ARM or
THUMB state.

To return from Supervisor Mode (continuing at following opcode):

```
  MOVS PC,R14   ;both PC=R14_svc, and CPSR=SPSR_svc
```

Note: Like all other exceptions, SWIs are always executed in ARM state, no
matter whether it's been caused by an ARM or THUMB state SWI instruction.

### Undefined Instruction Exception (supported by ARMv3 and up)

This exception is generated when the CPU comes across an instruction which it
cannot handle. Most likely signalizing that the program has locked up, and that
an errormessage should be displayed.

However, it might be also used to emulate custom functions, ie. as an
additional 'SWI' instruction (which'd use R14\_und and SPSR\_und though, and it'd
thus allow to execute the Undefined Instruction handler from inside of
Supervisor mode without having to save R14\_svc and SPSR\_svc).

To return from Undefined Mode (continuing at following opcode):

```
  MOVS PC,R14   ;both PC=R14_und, and CPSR=SPSR_und
```

Note that not all unused opcodes are necessarily producing an exception, for
example, an ARM state Multiply instruction with Bit6=1 would be blindly
accepted as 'legal' opcode.

### Abort (supported by ARMv3 and up)

Aborts (page faults) are mostly supposed for virtual memory systems (ie. not
used in GBA, as far as I know), otherwise they might be used just to display an
error message. Two types of aborts exists:

- Prefetch Abort (occurs during an instruction prefetch)

- Prefetch Abort (also occurs on BKPT opcodes, ARMv5 and up)

- Data Abort (occurs during a data access)

A virtual memory systems abort handler would then most likely determine the
fault address: For prefetch abort that's just "R14\_abt-4". For Data abort, the
THUMB or ARM instruction at "R14\_abt-8" needs to be 'disassembled' in order to
determine the addressed data in memory.

The handler would then fix the error by loading the respective memory page into
physical memory, and then retry to execute the SAME instruction again, by
returning as follows:

```
  prefetch abort: SUBS PC,R14,#4   ;PC=R14_abt-4, and CPSR=SPSR_abt
  data abort:     SUBS PC,R14,#8   ;PC=R14_abt-8, and CPSR=SPSR_abt
```

Separate exception vectors for prefetch/data abort exists, each should use the
respective return instruction as shown above.

### Address Exceeds 26bit

This exception can occur only on old ARM CPUs with 26bit address scheme (or in
26bit backwards compatibility mode).

### Reset

Forces PC=VVVV0000h, and forces control bits of CPSR to T=0 (ARM state), F=1
and I=1 (disable FIQ and IRQ), and M4-0=10011b (Supervisor mode).

## <a name="armcpumemoryalignments"></a>  ARM CPU Memory Alignments

The CPU does NOT support accessing mis-aligned addresses (which would be rather
slow because it'd have to merge/split that data into two accesses).

When reading/writing code/data to/from memory, Words and Halfwords must be
located at well-aligned memory address, ie. 32bit words aligned by 4, and 16bit
halfwords aligned by 2.

### Mis-aligned STR,STRH,STM,LDM,LDRD,STRD,PUSH,POP (forced align)

The mis-aligned low bit(s) are ignored, the memory access goes to a forcibly
aligned (rounded-down) memory address.

For LDRD/STRD, it isn't clearly defined if the address must be aligned by 8 (on
the NDS, align-4 seems to be okay) (align-8 may be required on other CPUs with
64bit databus).

### Mis-aligned LDR,SWP (rotated read)

Reads from forcibly aligned address "addr AND (NOT 3)", and does then rotate
the data as "ROR (addr AND 3)\*8". That effect is internally used by LDRB and
LDRH opcodes (which do then mask-out the unused bits).

The SWP opcode works like a combination of LDR and STR, that means, it does
read-rotated, but does write-unrotated.

### Mis-aligned LDRH,LDRSH (does or does not do strange things)

On ARM9 aka ARMv5 aka NDS9:

```
  LDRH Rd,[odd]   -->  LDRH Rd,[odd-1]        ;forced align
  LDRSH Rd,[odd]  -->  LDRSH Rd,[odd-1]       ;forced align
```

On ARM7 aka ARMv4 aka NDS7/GBA:

```
  LDRH Rd,[odd]   -->  LDRH Rd,[odd-1] ROR 8  ;read to bit0-7 and bit24-31
  LDRSH Rd,[odd]  -->  LDRSB Rd,[odd]         ;sign-expand BYTE value
```

### Mis-aligned PC/R15 (branch opcodes, or MOV/ALU/LDR with Rd=R15)

For ARM code, the low bits of the target address should be usually zero,
otherwise, R15 is forcibly aligned by clearing the lower two bits.

For THUMB code, the low bit of the target address may/should/must be set, the
bit is (or is not) interpreted as thumb-bit (depending on the opcode), and R15
is then forcibly aligned by clearing the lower bit.

In short, R15 will be always forcibly aligned, so mis-aligned branches won't
have effect on subsequent opcodes that use R15, or [R15+disp] as operand.

## <a name="arminstructionsummary"></a>  ARM Instruction Summary

Modification of CPSR flags is optional for all {S} instructions.

### Logical ALU Operations

```
  Instruction                      Cycles   Flags  Expl.
  MOV{cond}{S} Rd,Op2              1S+x+y    NZc-  Rd = Op2
  MVN{cond}{S} Rd,Op2              1S+x+y    NZc-  Rd = NOT Op2
  ORR{cond}{S} Rd,Rn,Op2           1S+x+y    NZc-  Rd = Rn OR Op2
  EOR{cond}{S} Rd,Rn,Op2           1S+x+y    NZc-  Rd = Rn XOR Op2
  AND{cond}{S} Rd,Rn,Op2           1S+x+y    NZc-  Rd = Rn AND Op2
  BIC{cond}{S} Rd,Rn,Op2           1S+x+y    NZc-  Rd = Rn AND NOT Op2
  TST{cond}{P}    Rn,Op2           1S+x      NZc-  Void = Rn AND Op2
  TEQ{cond}{P}    Rn,Op2           1S+x      NZc-  Void = Rn XOR Op2
```

Add x=1I cycles if Op2 shifted-by-register. Add y=1S+1N cycles if Rd=R15.

Carry flag affected only if Op2 contains a non-zero shift amount.

### Arithmetic ALU Operations

```
  Instruction                      Cycles   Flags  Expl.
  ADD{cond}{S} Rd,Rn,Op2           1S+x+y    NZCV  Rd = Rn+Op2
  ADC{cond}{S} Rd,Rn,Op2           1S+x+y    NZCV  Rd = Rn+Op2+Cy
  SUB{cond}{S} Rd,Rn,Op2           1S+x+y    NZCV  Rd = Rn-Op2
  SBC{cond}{S} Rd,Rn,Op2           1S+x+y    NZCV  Rd = Rn-Op2+Cy-1
  RSB{cond}{S} Rd,Rn,Op2           1S+x+y    NZCV  Rd = Op2-Rn
  RSC{cond}{S} Rd,Rn,Op2           1S+x+y    NZCV  Rd = Op2-Rn+Cy-1
  CMP{cond}{P}    Rn,Op2           1S+x      NZCV  Void = Rn-Op2
  CMN{cond}{P}    Rn,Op2           1S+x      NZCV  Void = Rn+Op2
```

Add x=1I cycles if Op2 shifted-by-register. Add y=1S+1N cycles if Rd=R15.

### Multiply

```
  Instruction                      Cycles   Flags  Expl.
  MUL{cond}{S} Rd,Rm,Rs            1S+mI     NZx-  Rd = Rm*Rs
  MLA{cond}{S} Rd,Rm,Rs,Rn         1S+mI+1I  NZx-  Rd = Rm*Rs+Rn
  UMULL{cond}{S} RdLo,RdHi,Rm,Rs   1S+mI+1I  NZx-  RdHiLo = Rm*Rs
  UMLAL{cond}{S} RdLo,RdHi,Rm,Rs   1S+mI+2I  NZx-  RdHiLo = Rm*Rs+RdHiLo
  SMULL{cond}{S} RdLo,RdHi,Rm,Rs   1S+mI+1I  NZx-  RdHiLo = Rm*Rs
  SMLAL{cond}{S} RdLo,RdHi,Rm,Rs   1S+mI+2I  NZx-  RdHiLo = Rm*Rs+RdHiLo
  SMLAxy{cond}  Rd,Rm,Rs,Rn     ARMv5TE(xP)  ----q Rd=HalfRm*HalfRs+Rn
  SMLAWy{cond}  Rd,Rm,Rs,Rn     ARMv5TE(xP)  ----q Rd=(Rm*HalfRs)/10000h+Rn
  SMULWy{cond}  Rd,Rm,Rs        ARMv5TE(xP)  ----  Rd=(Rm*HalfRs)/10000h
  SMLALxy{cond} RdLo,RdHi,Rm,Rs ARMv5TE(xP)  ----  RdHiLo=RdHiLo+HalfRm*HalfRs
  SMULxy{cond}  Rd,Rm,Rs        ARMv5TE(xP)  ----  Rd=HalfRm*HalfRs
```

### Memory Load/Store

```
  Instruction                      Cycles    Flags  Expl.
  LDR{cond}{B}{T} Rd,<Address>     1S+1N+1I+y ----  Rd=[Rn+/-<offset>]
  LDR{cond}H      Rd,<Address>     1S+1N+1I+y ----  Load Unsigned halfword
  LDR{cond}D      Rd,<Address>                ----  Load Dword ARMv5TE
  LDR{cond}SB     Rd,<Address>     1S+1N+1I+y ----  Load Signed byte
  LDR{cond}SH     Rd,<Address>     1S+1N+1I+y ----  Load Signed halfword
  LDM{cond}{amod} Rn{!},<Rlist>{^} nS+1N+1I+y ----  Load Multiple
  STR{cond}{B}{T} Rd,<Address>     2N         ----  [Rn+/-<offset>]=Rd
  STR{cond}H      Rd,<Address>     2N         ----  Store halfword
  STR{cond}D      Rd,<Address>                ----  Store Dword ARMv5TE
  STM{cond}{amod} Rn{!},<Rlist>{^} (n-1)S+2N  ----  Store Multiple
  SWP{cond}{B}    Rd,Rm,[Rn]       1S+2N+1I   ----  Rd=[Rn], [Rn]=Rm
  PLD             <Address>        1S         ----  Prepare Cache ARMv5TE
```

For LDR/LDM, add y=1S+1N if Rd=R15, or if R15 in Rlist.

### Jumps, Calls, CPSR Mode, and others

```
  Instruction                  Cycles  Flags  Expl.
  B{cond}   label              2S+1N    ----  PC=$+8+/-32M
  BL{cond}  label              2S+1N    ----  PC=$+8+/-32M, LR=$+4
  BX{cond}  Rn                 2S+1N    ----  PC=Rn, T=Rn.0 (THUMB/ARM)
  BLX{cond} Rn                 2S+1N    ----  PC=Rn, T=Rn.0, LR=PC+4, ARM9
  BLX       label              2S+1N    ----  PC=PC+$+/-32M, LR=$+4, T=1, ARM9
  MRS{cond} Rd,Psr             1S       ----  Rd=Psr
  MSR{cond} Psr{_field},Op     1S      (psr)  Psr[field]=Op
  SWI{cond} Imm24bit           2S+1N    ----  PC=8, ARM Svc mode, LR=$+4
  BKPT      Imm16bit           ???      ----  PC=C, ARM Abt mode, LR=$+4 ARM9
  The Undefined Instruction    2S+1I+1N ----  PC=4, ARM Und mode, LR=$+4
  cond=false                   1S       ----  Any opcode with condition=false
  NOP                          1S       ----  R0=R0
```

```
  CLZ{cond} Rd,Rm              ???      ----    Count Leading Zeros ARMv5
  QADD{cond} Rd,Rm,Rn                   ----q   Rd=Rm+Rn       ARMv5TE(xP)
  QSUB{cond} Rd,Rm,Rn                   ----q   Rd=Rm-Rn       ARMv5TE(xP)
  QDADD{cond} Rd,Rm,Rn                  ----q   Rd=Rm+Rn*2     ARMv5TE(xP)
  QDSUB{cond} Rd,Rm,Rn                  ----q   Rd=Rm-Rn*2     ARMv5TE(xP)
```

### Coprocessor Functions (if any)

```
  Instruction                         Cycles  Flags  Expl.
  CDP{cond} Pn,<cpopc>,Cd,Cn,Cm{,<cp>} 1S+bI   ----  Coprocessor specific
  STC{cond}{L} Pn,Cd,<Address>         (n-1)S+2N+bI  [address] = CRd
  LDC{cond}{L} Pn,Cd,<Address>         (n-1)S+2N+bI  CRd = [address]
  MCR{cond} Pn,<cpopc>,Rd,Cn,Cm{,<cp>} 1S+bI+1C      CRn = Rn {<op> CRm}
  MRC{cond} Pn,<cpopc>,Rd,Cn,Cm{,<cp>} 1S+(b+1)I+1C  Rn = CRn {<op> CRm}
  CDP2,STC2,LDC2,MCR2,MRC2 - ARMv5 Extensions similar above, without {cond}
  MCRR{cond} Pn,<cpopc>,Rd,Rn,Cm  ;write Rd,Rn to coproc ARMv5TE
  MRRC{cond} Pn,<cpopc>,Rd,Rn,Cm  ;read Rd,Rn from coproc ARMv5TE
```

### ARM Binary Opcode Format

```
  |..3 ..................2 ..................1 ..................0|
  |1_0_9_8_7_6_5_4_3_2_1_0_9_8_7_6_5_4_3_2_1_0_9_8_7_6_5_4_3_2_1_0|
  |_Cond__|0_0_0|___Op__|S|__Rn___|__Rd___|__Shift__|Typ|0|__Rm___| DataProc
  |_Cond__|0_0_0|___Op__|S|__Rn___|__Rd___|__Rs___|0|Typ|1|__Rm___| DataProc
  |_Cond__|0_0_1|___Op__|S|__Rn___|__Rd___|_Shift_|___Immediate___| DataProc
  |_Cond__|0_0_1_1_0|P|1|0|_Field_|__Rd___|_Shift_|___Immediate___| PSR Imm
  |_Cond__|0_0_0_1_0|P|L|0|_Field_|__Rd___|0_0_0_0|0_0_0_0|__Rm___| PSR Reg
  |_Cond__|0_0_0_1_0_0_1_0_1_1_1_1_1_1_1_1_1_1_1_1|0_0|L|1|__Rn___| BX,BLX
  |1_1_1_0|0_0_0_1_0_0_1_0|_____immediate_________|0_1_1_1|_immed_| BKPT ARM9
  |_Cond__|0_0_0_1_0_1_1_0_1_1_1_1|__Rd___|1_1_1_1|0_0_0_1|__Rm___| CLZ  ARM9
  |_Cond__|0_0_0_1_0|Op_|0|__Rn___|__Rd___|0_0_0_0|0_1_0_1|__Rm___| QALU ARM9
  |_Cond__|0_0_0_0_0_0|A|S|__Rd___|__Rn___|__Rs___|1_0_0_1|__Rm___| Multiply
  |_Cond__|0_0_0_0_1|U|A|S|_RdHi__|_RdLo__|__Rs___|1_0_0_1|__Rm___| MulLong
  |_Cond__|0_0_0_1_0|Op_|0|Rd/RdHi|Rn/RdLo|__Rs___|1|y|x|0|__Rm___| MulHalfARM9
  |_Cond__|0_0_0_1_0|B|0_0|__Rn___|__Rd___|0_0_0_0|1_0_0_1|__Rm___| TransSwp12
  |_Cond__|0_0_0|P|U|0|W|L|__Rn___|__Rd___|0_0_0_0|1|S|H|1|__Rm___| TransReg10
  |_Cond__|0_0_0|P|U|1|W|L|__Rn___|__Rd___|OffsetH|1|S|H|1|OffsetL| TransImm10
  |_Cond__|0_1_0|P|U|B|W|L|__Rn___|__Rd___|_________Offset________| TransImm9
  |_Cond__|0_1_1|P|U|B|W|L|__Rn___|__Rd___|__Shift__|Typ|0|__Rm___| TransReg9
  |_Cond__|0_1_1|________________xxx____________________|1|__xxx__| Undefined
  |_Cond__|1_0_0|P|U|S|W|L|__Rn___|__________Register_List________| BlockTrans
  |_Cond__|1_0_1|L|___________________Offset______________________| B,BL,BLX
  |_Cond__|1_1_0|P|U|N|W|L|__Rn___|__CRd__|__CP#__|____Offset_____| CoDataTrans
  |_Cond__|1_1_0_0_0_1_0|L|__Rn___|__Rd___|__CP#__|_CPopc_|__CRm__| CoRR ARM9
  |_Cond__|1_1_1_0|_CPopc_|__CRn__|__CRd__|__CP#__|_CP__|0|__CRm__| CoDataOp
  |_Cond__|1_1_1_0|CPopc|L|__CRn__|__Rd___|__CP#__|_CP__|1|__CRm__| CoRegTrans
  |_Cond__|1_1_1_1|_____________Ignored_by_Processor______________| SWI
```

## <a name="armopcodesbranchandbranchwithlinkbblbxblxswibkpt"></a>  ARM Opcodes: Branch and Branch with Link (B, BL, BX, BLX, SWI, BKPT)

### Branch and Branch with Link (B, BL, BLX\_imm)

Branch (B) is supposed to jump to a subroutine. Branch with Link is meant to be
used to call to a subroutine, return address is then saved in R14.

```
  Bit    Expl.
  31-28  Condition (must be 1111b for BLX)
  27-25  Must be "101" for this instruction
  24     Opcode (0-1) (or Halfword Offset for BLX)
          0: B{cond} label    ;branch            PC=PC+8+nn*4
          1: BL{cond} label   ;branch/link       PC=PC+8+nn*4, LR=PC+4
          H: BLX label ;ARM9  ;branch/link/thumb PC=PC+8+nn*4+H*2, LR=PC+4, T=1
  23-0   nn - Signed Offset, step 4      (-32M..+32M in steps of 4)
```

Branch with Link can be used to 'call' to a sub-routine, which may then
'return' by MOV PC,R14 for example.

Execution Time: 2S + 1N

Return: No flags affected.

### Branch and Exchange (BX, BLX\_reg)

```
  Bit    Expl.
  31-28  Condition
  27-8   Must be "0001.0010.1111.1111.1111" for this instruction
  7-4    Opcode
          0001b: BX{cond}  Rn    ;PC=Rn, T=Rn.0  (ARMv4T and ARMv5 and up)
          0011b: BLX{cond} Rn    ;PC=Rn, T=Rn.0, LR=PC+4    (ARMv5 and up)
  3-0    Rn - Operand Register  (R0-R14)
```

Switching to THUMB Mode: Set Bit 0 of the value in Rn to 1, program continues
then at Rn-1 in THUMB mode.

Results in undefined behaviour if using R15 (PC+8 itself) as operand. Using BLX
R14 is possible (sets PC=Old\_LR, and New\_LR=retadr).

Execution Time: 2S + 1N

Return: No flags affected.

### Branch via ALU, LDR, LDM

Most ALU, LDR, LDM opcodes can also change PC/R15.

### Software Interrupt (SWI/BKPT) (svc/abt exceptions)

SWI supposed for calls to the operating system - Enter Supervisor mode (SVC) in
ARM state. BKPT intended for debugging - enters Abort mode in ARM state via
Prefetch Abort vector.

```
  Bit    Expl.
  31-28  Condition (must be 1110b for BKPT, ie. Condition=always)
  27-24  Opcode
          1111b: SWI{cond} nn   ;software interrupt
          0001b: BKPT      nn   ;breakpoint (ARMv5 and up)
  For SWI:
   23-0   nn - Comment Field, ignored by processor (24bit value)
  For BKPT:
   23-20  Must be 0010b for BKPT
   19-8   nn - upper 12bits of comment field, ignored by processor
   7-4    Must be 0111b for BKPT
   3-0    nn - lower 4bits of comment field, ignored by processor
```

Execution Time: 2S+1N

The exception handler may interprete the SWI Comment Field by examining the
lower 24bit of the 32bit opcode opcode at [R14\_svc-4]. If your are also using
SWI's from inside of THUMB, then the SWI handler must examine the T Bit
SPSR\_svc in order to determine whether it's been a THUMB SWI - and if so,
examine the lower 8bit of the 16bit opcode opcode at [R14\_svc-2].

For Returning from SWI use "MOVS PC,R14", that instruction does restore both PC
and CPSR, ie. PC=R14\_svc, and CPSR=SPSR\_svc.

Nesting SWIs: SPSR\_svc and R14\_svc should be saved on stack before either
invoking nested SWIs, or (if the IRQ handler uses SWIs) before enabling IRQs.

Execution SWI/BKPT:

```
  R14_svc=PC+4     R14_abt=PC+4   ;save return address
  SPSR_svc=CPSR    SPSR_abt=CPSR  ;save CPSR flags
  CPSR=<changed>   CPSR=<changed> ;Enter svc/abt, ARM state, IRQs disabled
  PC=VVVV0008h     PC=VVVV000Ch   ;jump to SWI/PrefetchAbort vector address
```

### Undefined Instruction (und exception)

```
  Bit    Expl.
  31-28  Condition
  27-25  Must be 011b for this instruction
  24-5   Reserved for future use
  4      Must be 1b for this instruction
  3-0    Reserved for future use
```

No assembler mnemonic exists, following bitstreams are (not) reserved.

```
  cond011xxxxxxxxxxxxxxxxxxxx1xxxx - reserved for future use (except below).
  cond01111111xxxxxxxxxxxx1111xxxx - free for user.
```

Execution time: 2S+1I+1N.

## <a name="armopcodesdataprocessingalu"></a>  ARM Opcodes: Data Processing (ALU)

### Opcode Format

```
  Bit    Expl.
  31-28  Condition
  27-26  Must be 00b for this instruction
  25     I - Immediate 2nd Operand Flag (0=Register, 1=Immediate)
  24-21  Opcode (0-Fh)               ;*=Arithmetic, otherwise Logical
           0: AND{cond}{S} Rd,Rn,Op2    ;AND logical       Rd = Rn AND Op2
           1: EOR{cond}{S} Rd,Rn,Op2    ;XOR logical       Rd = Rn XOR Op2
           2: SUB{cond}{S} Rd,Rn,Op2 ;* ;subtract          Rd = Rn-Op2
           3: RSB{cond}{S} Rd,Rn,Op2 ;* ;subtract reversed Rd = Op2-Rn
           4: ADD{cond}{S} Rd,Rn,Op2 ;* ;add               Rd = Rn+Op2
           5: ADC{cond}{S} Rd,Rn,Op2 ;* ;add with carry    Rd = Rn+Op2+Cy
           6: SBC{cond}{S} Rd,Rn,Op2 ;* ;sub with carry    Rd = Rn-Op2+Cy-1
           7: RSC{cond}{S} Rd,Rn,Op2 ;* ;sub cy. reversed  Rd = Op2-Rn+Cy-1
           8: TST{cond}{P}    Rn,Op2    ;test            Void = Rn AND Op2
           9: TEQ{cond}{P}    Rn,Op2    ;test exclusive  Void = Rn XOR Op2
           A: CMP{cond}{P}    Rn,Op2 ;* ;compare         Void = Rn-Op2
           B: CMN{cond}{P}    Rn,Op2 ;* ;compare neg.    Void = Rn+Op2
           C: ORR{cond}{S} Rd,Rn,Op2    ;OR logical        Rd = Rn OR Op2
           D: MOV{cond}{S} Rd,Op2       ;move              Rd = Op2
           E: BIC{cond}{S} Rd,Rn,Op2    ;bit clear         Rd = Rn AND NOT Op2
           F: MVN{cond}{S} Rd,Op2       ;not               Rd = NOT Op2
  20     S - Set Condition Codes (0=No, 1=Yes) (Must be 1 for opcode 8-B)
  19-16  Rn - 1st Operand Register (R0..R15) (including PC=R15)
              Must be 0000b for MOV/MVN.
  15-12  Rd - Destination Register (R0..R15) (including PC=R15)
              Must be 0000b (or 1111b) for CMP/CMN/TST/TEQ{P}.
  When above Bit 25 I=0 (Register as 2nd Operand)
    When below Bit 4 R=0 - Shift by Immediate
      11-7   Is - Shift amount   (1-31, 0=Special/See below)
    When below Bit 4 R=1 - Shift by Register
      11-8   Rs - Shift register (R0-R14) - only lower 8bit 0-255 used
      7      Reserved, must be zero  (otherwise multiply or undefined opcode)
    6-5    Shift Type (0=LSL, 1=LSR, 2=ASR, 3=ROR)
    4      R - Shift by Register Flag (0=Immediate, 1=Register)
    3-0    Rm - 2nd Operand Register (R0..R15) (including PC=R15)
  When above Bit 25 I=1 (Immediate as 2nd Operand)
    11-8   Is - ROR-Shift applied to nn (0-30, in steps of 2)
    7-0    nn - 2nd Operand Unsigned 8bit Immediate
```

### Second Operand (Op2)

This may be a shifted register, or a shifted immediate. See Bit 25 and 11-0.

Unshifted Register: Specify Op2 as "Rm", assembler converts to "Rm,LSL#0".

Shifted Register: Specify as "Rm,SSS#Is" or "Rm,SSS Rs" (SSS=LSL/LSR/ASR/ROR).

Immediate: Specify as 32bit value, for example: "#000NN000h", assembler should
automatically convert into "#0NNh,ROR#0ssh" as far as possible (ie. as far as a
section of not more than 8bits of the immediate is non-zero).

### Zero Shift Amount (Shift Register by Immediate, with Immediate=0)

```
  LSL#0: No shift performed, ie. directly Op2=Rm, the C flag is NOT affected.
  LSR#0: Interpreted as LSR#32, ie. Op2 becomes zero, C becomes Bit 31 of Rm.
  ASR#0: Interpreted as ASR#32, ie. Op2 and C are filled by Bit 31 of Rm.
  ROR#0: Interpreted as RRX#1 (RCR), like ROR#1, but Op2 Bit 31 set to old C.
```

In source code, LSR#32, ASR#32, and RRX#1 should be specified as such -
attempts to specify LSR#0, ASR#0, or ROR#0 will be internally converted to
LSL#0 by the assembler.

### Using R15 (PC)

When using R15 as Destination (Rd), note below CPSR description and Execution
time description.

When using R15 as operand (Rm or Rn), the returned value depends on the
instruction: PC+12 if I=0,R=1 (shift by register), otherwise PC+8 (shift by
immediate).

### Returned CPSR Flags

If S=1, Rd\<>R15, logical operations (AND,EOR,TST,TEQ,ORR,MOV,BIC,MVN):

```
  V=not affected
  C=carryflag of shift operation (not affected if LSL#0 or Rs=00h)
  Z=zeroflag of result
  N=signflag of result (result bit 31)
```

If S=1, Rd\<>R15, arithmetic operations (SUB,RSB,ADD,ADC,SBC,RSC,CMP,CMN):

```
  V=overflowflag of result
  C=carryflag of result
  Z=zeroflag of result
  N=signflag of result (result bit 31)
```

IF S=1, with unused Rd bits=1111b, {P} opcodes (CMPP/CMNP/TSTP/TEQP):

```
  R15=result  ;modify PSR bits in R15, ARMv2 and below only.
  In user mode only N,Z,C,V bits of R15 can be changed.
  In other modes additionally I,F,M1,M0 can be changed.
  The PC bits in R15 are left unchanged in all modes.
```

If S=1, Rd=R15; should not be used in user mode:

```
  CPSR = SPSR_<current mode>
  PC = result
  For example: MOVS PC,R14  ;return from SWI (PC=R14_svc, CPSR=SPSR_svc).
```

If S=0: Flags are not affected (not allowed for CMP,CMN,TEQ,TST).

The instruction "MOV R0,R0" is used as "NOP" opcode in 32bit ARM state.

Execution Time: (1+p)S+rI+pN. Whereas r=1 if I=0 and R=1 (ie. shift by
register); otherwise r=0. And p=1 if Rd=R15; otherwise p=0.

## <a name="armopcodesmultiplyandmultiplyaccumulatemulmla"></a>  ARM Opcodes: Multiply and Multiply-Accumulate (MUL, MLA)

### Opcode Format

```
  Bit    Expl.
  31-28  Condition
  27-25  Must be 000b for this instruction
  24-21  Opcode
          0000b: MUL{cond}{S}   Rd,Rm,Rs        ;multiply   Rd = Rm*Rs
          0001b: MLA{cond}{S}   Rd,Rm,Rs,Rn     ;mul.& accumulate Rd = Rm*Rs+Rn
          0100b: UMULL{cond}{S} RdLo,RdHi,Rm,Rs ;multiply   RdHiLo=Rm*Rs
          0101b: UMLAL{cond}{S} RdLo,RdHi,Rm,Rs ;mul.& acc. RdHiLo=Rm*Rs+RdHiLo
          0110b: SMULL{cond}{S} RdLo,RdHi,Rm,Rs ;sign.mul.  RdHiLo=Rm*Rs
          0111b: SMLAL{cond}{S} RdLo,RdHi,Rm,Rs ;sign.m&a.  RdHiLo=Rm*Rs+RdHiLo
          1000b: SMLAxy{cond}   Rd,Rm,Rs,Rn     ;Rd=HalfRm*HalfRs+Rn
          1001b: SMLAWy{cond}   Rd,Rm,Rs,Rn     ;Rd=(Rm*HalfRs)/10000h+Rn
          1001b: SMULWy{cond}   Rd,Rm,Rs        ;Rd=(Rm*HalfRs)/10000h
          1010b: SMLALxy{cond}  RdLo,RdHi,Rm,Rs ;RdHiLo=RdHiLo+HalfRm*HalfRs
          1011b: SMULxy{cond}   Rd,Rm,Rs        ;Rd=HalfRm*HalfRs
  20     S - Set Condition Codes (0=No, 1=Yes) (Must be 0 for Halfword mul)
  19-16  Rd (or RdHi) - Destination Register (R0-R14)
  15-12  Rn (or RdLo) - Accumulate Register  (R0-R14) (Set to 0000b if unused)
  11-8   Rs - Operand Register               (R0-R14)
  For Non-Halfword Multiplies
    7-4  Must be 1001b for these instructions
  For Halfword Multiplies
    7    Must be 1 for these instructions
    6    y - Rs Top/Bottom flag (0=B=Lower 16bit, 1=T=Upper 16bit)
    5    x - Rm Top/Bottom flag (as above), or 0 for SMLAW, or 1 for SMULW
    4    Must be 0 for these instructions
  3-0    Rm - Operand Register               (R0-R14)
```

### Multiply and Multiply-Accumulate (MUL, MLA)

Restrictions: Rd may not be same as Rm. Rd,Rn,Rs,Rm may not be R15.

Note: Only the lower 32bit of the internal 64bit result are stored in Rd, thus
no sign/zero extension is required and MUL and MLA can be used for both signed
and unsigned calculations!

Execution Time: 1S+mI for MUL, and 1S+(m+1)I for MLA. Whereas 'm' depends on
whether/how many most significant bits of Rs are all zero or all one. That is
m=1 for Bit 31-8, m=2 for Bit 31-16, m=3 for Bit 31-24, and m=4 otherwise.

Flags (if S=1): Z=zeroflag, N=signflag, C=destroyed (ARMv4 and below) or C=not
affected (ARMv5 and up), V=not affected. MUL/MLA supported by ARMv2 and up.

### Multiply Long and Multiply-Accumulate Long (MULL, MLAL)

Optionally supported, INCLUDED in ARMv3M, EXCLUDED in ARMv4xM/ARMv5xM.

Restrictions: RdHi,RdLo,Rm must be different registers. R15 may not be used.

Execution Time: 1S+(m+1)I for MULL, and 1S+(m+2)I for MLAL. Whereas 'm' depends
on whether/how many most significant bits of Rs are "all zero" (UMULL/UMLAL) or
"all zero or all one" (SMULL,SMLAL). That is m=1 for Bit31-8, m=2 for Bit31-16,
m=3 for Bit31-24, and m=4 otherwise.

Flags (if S=1): Z=zeroflag, N=signflag, C=destroyed (ARMv4 and below) or C=not
affected (ARMv5 and up), V=destroyed??? (ARMv4 and below???) or V=not affected
(ARMv5 and up).

### Signed Halfword Multiply (SMLAxy,SMLAWy,SMLALxy,SMULxy,SMULWy)

Supported by E variants of ARMv5 and up, ie. ARMv5TE(xP).

Q-flag gets set on 32bit SMLAxy/SMLAWy addition overflows, however, the result
is NOT truncated (as it'd be done with QADD opcodes).

Q-flag is NOT affected on (rare) 64bit SMLALxy addition overflows.

SMULxy/SMULWy cannot overflow, and thus leave Q-flag unchanged as well.

NZCV-flags are not affected by Halfword multiplies.

Execution Time: 1S+Interlock (SMULxy,SMLAxy,SMULWx,SMLAWx)

Execution Time: 1S+1I+Interlock (SMLALxy)

## <a name="armopcodesspecialarm9instructionsclzqaddqsub"></a>  ARM Opcodes: Special ARM9 Instructions (CLZ, QADD/QSUB)

### Count Leading Zeros (CLZ)

```
  Bit    Expl.
  31-28  Condition
  27-16  Must be 0001.0110.1111b for this instruction
         Opcode (fixed)
           CLZ{cond} Rd,Rm  ;Rd=Number of leading zeros in Rm
  15-12  Rd - Destination Register              (R0-R14)
  11-4   Must be 1111.0001b for this instruction
  3-0    Rm - Source Register                   (R0-R14)
```

CLZ supported by ARMv5 and up. Execution time: 1S.

Return: No Flags affected. Rd=0..32.

### Opcode Format (QADD/QSUB)

```
  Bit    Expl.
  31-28  Condition
  27-24  Must be 0001b for this instruction
  23-20  Opcode
          0000b: QADD{cond}  Rd,Rm,Rn    ;Rd=Rm+Rn
          0010b: QSUB{cond}  Rd,Rm,Rn    ;Rd=Rm-Rn
          0100b: QDADD{cond} Rd,Rm,Rn    ;Rd=Rm+Rn*2 (doubled)
          0110b: QDSUB{cond} Rd,Rm,Rn    ;Rd=Rm-Rn*2 (doubled)
  19-16  Rn - Second Source Register            (R0-R14)
  15-12  Rd - Destination Register              (R0-R14)
  11-4   Must be 00000101b for this instruction
  3-0    Rm - First Source Register             (R0-R14)
```

Supported by E variants of ARMv5 and up, ie. ARMv5TE(xP).

Execution time: 1S+Interlock.

Results truncated to signed 32bit range in case of overflows, with the Q-flag
being set (and being left unchanged otherwise). NZCV flags are not affected.

Note: Rn\*2 is internally processed first, and may get truncated - even if the
final result would fit into range.

## <a name="armopcodespsrtransfermrsmsr"></a>  ARM Opcodes: PSR Transfer (MRS, MSR)

### Opcode Format

These instructions occupy an unused area (TEQ,TST,CMP,CMN with S=0) of ALU
opcodes.

```
  Bit    Expl.
  31-28  Condition
  27-26  Must be 00b for this instruction
  25     I - Immediate Operand Flag  (0=Register, 1=Immediate) (Zero for MRS)
  24-23  Must be 10b for this instruction
  22     Psr - Source/Destination PSR  (0=CPSR, 1=SPSR_<current mode>)
  21     Opcode
           0: MRS{cond} Rd,Psr          ;Rd = Psr
           1: MSR{cond} Psr{_field},Op  ;Psr[field] = Op
  20     Must be 0b for this instruction (otherwise TST,TEQ,CMP,CMN)
  For MRS:
    19-16   Must be 1111b for this instruction (otherwise SWP)
    15-12   Rd - Destination Register  (R0-R14)
    11-0    Not used, must be zero.
  For MSR:
    19      f  write to flags field     Bit 31-24 (aka _flg)
    18      s  write to status field    Bit 23-16 (reserved, don't change)
    17      x  write to extension field Bit 15-8  (reserved, don't change)
    16      c  write to control field   Bit 7-0   (aka _ctl)
    15-12   Not used, must be 1111b.
  For MSR Psr,Rm (I=0)
    11-4    Not used, must be zero. (otherwise BX)
    3-0     Rm - Source Register <op>  (R0-R14)
  For MSR Psr,Imm (I=1)
    11-8    Shift applied to Imm   (ROR in steps of two 0-30)
    7-0     Imm - Unsigned 8bit Immediate
    In source code, a 32bit immediate should be specified as operand.
    The assembler should then convert that into a shifted 8bit value.
```

MSR/MRS and CPSR/SPSR supported by ARMv3 and up.

ARMv2 and below contained PSR flags in R15, accessed by CMP/CMN/TST/TEQ{P}.

The field mask bits specify which bits of the destination Psr are write-able
(or write-protected), one or more of these bits should be set, for example,
CPSR\_fsxc (aka CPSR aka CPSR\_all) unlocks all bits (see below user mode
restriction though).

Restrictions:

In non-privileged mode (user mode): only condition code bits of CPSR can be
changed, control bits can't.

Only the SPSR of the current mode can be accessed; In User and System modes no
SPSR exists.

The T-bit may not be changed; for THUMB/ARM switching use BX instruction.

Unused Bits in CPSR are reserved for future use and should never be changed
(except for unused bits in the flags field).

Execution Time: 1S.

Note: The A22i assembler recognizes MOV as alias for both MSR and MRS because
it is practically not possible to remember whether MSR or MRS was the load or
store opcode, and/or whether it does load to or from the Psr register.

## <a name="armopcodesmemorysingledatatransferldrstrpld"></a>  ARM Opcodes: Memory: Single Data Transfer (LDR, STR, PLD)

### Opcode Format

```
  Bit    Expl.
  31-28  Condition (Must be 1111b for PLD)
  27-26  Must be 01b for this instruction
  25     I - Immediate Offset Flag (0=Immediate, 1=Shifted Register)
  24     P - Pre/Post (0=post; add offset after transfer, 1=pre; before trans.)
  23     U - Up/Down Bit (0=down; subtract offset from base, 1=up; add to base)
  22     B - Byte/Word bit (0=transfer 32bit/word, 1=transfer 8bit/byte)
  When above Bit 24 P=0 (Post-indexing, write-back is ALWAYS enabled):
    21     T - Memory Management (0=Normal, 1=Force non-privileged access)
  When above Bit 24 P=1 (Pre-indexing, write-back is optional):
    21     W - Write-back bit (0=no write-back, 1=write address into base)
  20     L - Load/Store bit (0=Store to memory, 1=Load from memory)
          0: STR{cond}{B}{T} Rd,<Address>   ;[Rn+/-<offset>]=Rd
          1: LDR{cond}{B}{T} Rd,<Address>   ;Rd=[Rn+/-<offset>]
         (1: PLD <Address> ;Prepare Cache for Load, see notes below)
          Whereas, B=Byte, T=Force User Mode (only for POST-Indexing)
  19-16  Rn - Base register               (R0..R15) (including R15=PC+8)
  15-12  Rd - Source/Destination Register (R0..R15) (including R15=PC+12)
  When above I=0 (Immediate as Offset)
    11-0   Unsigned 12bit Immediate Offset (0-4095, steps of 1)
  When above I=1 (Register shifted by Immediate as Offset)
    11-7   Is - Shift amount      (1-31, 0=Special/See below)
    6-5    Shift Type             (0=LSL, 1=LSR, 2=ASR, 3=ROR)
    4      Must be 0 (Reserved, see The Undefined Instruction)
    3-0    Rm - Offset Register   (R0..R14) (not including PC=R15)
```

### Instruction Formats for \<Address>

An expression which generates an address:

```
  <expression>                  ;an immediate used as address
  ;*** restriction: must be located in range PC+/-4095+8, if so,
  ;*** assembler will calculate offset and use PC (R15) as base.
```

Pre-indexed addressing specification:

```
  [Rn]                          ;offset = zero
  [Rn, <#{+/-}expression>]{!}   ;offset = immediate
  [Rn, {+/-}Rm{,<shift>} ]{!}   ;offset = register shifted by immediate
```

Post-indexed addressing specification:

```
  [Rn], <#{+/-}expression>      ;offset = immediate
  [Rn], {+/-}Rm{,<shift>}       ;offset = register shifted by immediate
```

Whereas...

```
  <shift>  immediate shift such like LSL#4, ROR#2, etc. (see ALU opcodes).
  {!}      exclamation mark ("!") indicates write-back (Rn will be updated).
```

### Notes

Shift amount 0 has special meaning, as described for ALU opcodes.

When writing a word (32bit) to memory, the address should be word-aligned.

When reading a byte from memory, upper 24 bits of Rd are zero-extended.

LDR PC,\<op> on ARMv4 leaves CPSR.T unchanged.

LDR PC,\<op> on ARMv5 sets CPSR.T to \<op> Bit0, (1=Switch to Thumb).

When reading a word from a halfword-aligned address (which is located in the
middle between two word-aligned addresses), the lower 16bit of Rd will contain
[address] ie. the addressed halfword, and the upper 16bit of Rd will contain
[Rd-2] ie. more or less unwanted garbage. However, by isolating lower bits this
may be used to read a halfword from memory. (Above applies to little endian
mode, as used in GBA.)

In a virtual memory based environment (ie. not in the GBA), aborts (ie. page
faults) may take place during execution, if so, Rm and Rn should not specify
the same register when post-indexing is used, as the abort-handler might have
problems to reconstruct the original value of the register.

Return: CPSR flags are not affected.

Execution Time: For normal LDR: 1S+1N+1I. For LDR PC: 2S+2N+1I. For STR: 2N.

### PLD \<Address> ;Prepare Cache for Load

PLD must use following settings cond=1111b, P=1, B=1, W=0, L=1, Rd=1111b, the
address may not use post-indexing, and may not use writeback, the opcode is
encoded identical as LDRNVB R15,\<Address>.

PLD signalizes to the memory system that a specific memory address will be soon
accessed, the memory system may use this hint to prepare caching/pipelining,
aside from that, PLD does not have any affect to the program logic, and behaves
identical as NOP.

PLD supported by ARMv5TE only, not ARMv5, not ARMv5TExP.

## <a name="armopcodesmemoryhalfworddoublewordandsigneddatatransfer"></a>  ARM Opcodes: Memory: Halfword, Doubleword, and Signed Data Transfer

### Opcode Format

```
  Bit    Expl.
  31-28  Condition
  27-25  Must be 000b for this instruction
  24     P - Pre/Post (0=post; add offset after transfer, 1=pre; before trans.)
  23     U - Up/Down Bit (0=down; subtract offset from base, 1=up; add to base)
  22     I - Immediate Offset Flag (0=Register Offset, 1=Immediate Offset)
  When above Bit 24 P=0 (Post-indexing, write-back is ALWAYS enabled):
    21     Not used, must be zero (0)
  When above Bit 24 P=1 (Pre-indexing, write-back is optional):
    21     W - Write-back bit (0=no write-back, 1=write address into base)
  20     L - Load/Store bit (0=Store to memory, 1=Load from memory)
  19-16  Rn - Base register                (R0-R15) (Including R15=PC+8)
  15-12  Rd - Source/Destination Register  (R0-R15) (Including R15=PC+12)
  11-8   When above Bit 22 I=0 (Register as Offset):
           Not used. Must be 0000b
         When above Bit 22 I=1 (immediate as Offset):
           Immediate Offset (upper 4bits)
  7      Reserved, must be set (1)
  6-5    Opcode (0-3)
         When Bit 20 L=0 (Store) (and Doubleword Load/Store):
          0: Reserved for SWP instruction
          1: STR{cond}H  Rd,<Address>  ;Store halfword   [a]=Rd
          2: LDR{cond}D  Rd,<Address>  ;Load Doubleword  R(d)=[a], R(d+1)=[a+4]
          3: STR{cond}D  Rd,<Address>  ;Store Doubleword [a]=R(d), [a+4]=R(d+1)
         When Bit 20 L=1 (Load):
          0: Reserved.
          1: LDR{cond}H  Rd,<Address>  ;Load Unsigned halfword (zero-extended)
          2: LDR{cond}SB Rd,<Address>  ;Load Signed byte (sign extended)
          3: LDR{cond}SH Rd,<Address>  ;Load Signed halfword (sign extended)
  4      Reserved, must be set (1)
  3-0    When above Bit 22 I=0:
           Rm - Offset Register            (R0-R14) (not including R15)
         When above Bit 22 I=1:
           Immediate Offset (lower 4bits)  (0-255, together with upper bits)
```

STRH,LDRH,LDRSB,LDRSH supported on ARMv4 and up.

STRD/LDRD supported on ARMv5TE only, not ARMv5, not ARMv5TExP.

STRD/LDRD: base writeback: Rn should not be same as R(d) or R(d+1).

STRD: index register: Rm should not be same as R(d) or R(d+1).

STRD/LDRD: Rd must be an even numbered register (R0,R2,R4,R6,R8,R10,R12).

STRD/LDRD: Address must be double-word aligned (multiple of eight).

### Instruction Formats for \<Address>

An expression which generates an address:

```
  <expression>                  ;an immediate used as address
  ;*** restriction: must be located in range PC+/-255+8, if so,
  ;*** assembler will calculate offset and use PC (R15) as base.
```

Pre-indexed addressing specification:

```
  [Rn]                          ;offset = zero
  [Rn, <#{+/-}expression>]{!}   ;offset = immediate
  [Rn, {+/-}Rm]{!}              ;offset = register
```

Post-indexed addressing specification:

```
  [Rn], <#{+/-}expression>      ;offset = immediate
  [Rn], {+/-}Rm                 ;offset = register
```

Whereas...

```
  {!}      exclamation mark ("!") indicates write-back (Rn will be updated).
```

Return: No Flags affected.

Execution Time: For Normal LDR, 1S+1N+1I. For LDR PC, 2S+2N+1I. For STRH 2N.

## <a name="armopcodesmemoryblockdatatransferldmstm"></a>  ARM Opcodes: Memory: Block Data Transfer (LDM, STM)

### Opcode Format

```
  Bit    Expl.
  31-28  Condition
  27-25  Must be 100b for this instruction
  24     P - Pre/Post (0=post; add offset after transfer, 1=pre; before trans.)
  23     U - Up/Down Bit (0=down; subtract offset from base, 1=up; add to base)
  22     S - PSR & force user bit (0=No, 1=load PSR or force user mode)
  21     W - Write-back bit (0=no write-back, 1=write address into base)
  20     L - Load/Store bit (0=Store to memory, 1=Load from memory)
          0: STM{cond}{amod} Rn{!},<Rlist>{^}  ;Store (Push)
          1: LDM{cond}{amod} Rn{!},<Rlist>{^}  ;Load  (Pop)
          Whereas, {!}=Write-Back (W), and {^}=PSR/User Mode (S)
  19-16  Rn - Base register                (R0-R14) (not including R15)
  15-0   Rlist - Register List
  (Above 'offset' is meant to be the number of words specified in Rlist.)
```

Return: No Flags affected.

Execution Time: For normal LDM, nS+1N+1I. For LDM PC, (n+1)S+2N+1I. For STM
(n-1)S+2N. Where n is the number of words transferred.

### Addressing Modes {amod}

The IB,IA,DB,DA suffixes directly specify the desired U and P bits:

```
  IB  increment before          ;P=1, U=1
  IA  increment after           ;P=0, U=1
  DB  decrement before          ;P=1, U=0
  DA  decrement after           ;P=0, U=0
```

Alternately, FD,ED,FA,EA could be used, mostly to simplify mnemonics for stack
transfers.

```
  ED  empty stack, descending   ;LDM: P=1, U=1  ;STM: P=0, U=0
  FD  full stack,  descending   ;     P=0, U=1  ;     P=1, U=0
  EA  empty stack, ascending    ;     P=1, U=0  ;     P=0, U=1
  FA  full stack,  ascending    ;     P=0, U=0  ;     P=1, U=1
```

Ie. the following expressions are aliases for each other:

```
  STMFD=STMDB=PUSH   STMED=STMDA   STMFA=STMIB   STMEA=STMIA
  LDMFD=LDMIA=POP    LDMED=LDMIB   LDMFA=LDMDA   LDMEA=LDMDB
```

Note: The equivalent THUMB functions use fixed organization:

```
  PUSH/POP: full descending     ;base register SP (R13)
  LDM/STM:  increment after     ;base register R0..R7
```

Descending is common stack organization as used in 80x86 and Z80 CPUs, SP is
decremented when pushing/storing data, and incremented when popping/loading
data.

### When S Bit is set (S=1)

If instruction is LDM and R15 is in the list: (Mode Changes)

```
  While R15 loaded, additionally: CPSR=SPSR_<current mode>
```

Otherwise: (User bank transfer)

```
  Rlist is referring to User Bank Registers, R0-R15 (rather than
  register related to the current mode, such like R14_svc etc.)
  Base write-back should not be used for User bank transfer.
  Caution - When instruction is LDM:
  If the following instruction reads from a banked register (eg. R14_svc),
  then CPU might still read R14 instead; if necessary insert a dummy NOP.
```

### Notes

The base address should be usually word-aligned.

LDM Rn,...,PC on ARMv4 leaves CPSR.T unchanged.

LDR Rn,...,PC on ARMv5 sets CPSR.T to \<op> Bit0, (1=Switch to Thumb).

### Transfer Order

The lowest Register in Rlist (R0 if its in the list) will be loaded/stored
to/from the lowest memory address.

Internally, the rlist register are always processed with INCREASING addresses
(ie. for DECREASING addressing modes, the CPU does first calculate the lowest
address, and does then process rlist with increasing addresses; this detail can
be important when accessing memory mapped I/O ports).

### Strange Effects on Invalid Rlist's

Empty Rlist: R15 loaded/stored (ARMv4 only), and Rb=Rb+/-40h (ARMv4-v5).

Writeback with Rb included in Rlist: Store OLD base if Rb is FIRST entry in
Rlist, otherwise store NEW base (STM/ARMv4), always store OLD base (STM/ARMv5),
no writeback (LDM/ARMv4), writeback if Rb is "the ONLY register, or NOT the
LAST register" in Rlist (LDM/ARMv5).

## <a name="armopcodesmemorysingledataswapswp"></a>  ARM Opcodes: Memory: Single Data Swap (SWP)

### Opcode Format

```
  Bit    Expl.
  31-28  Condition
  27-23  Must be 00010b for this instruction
         Opcode (fixed)
           SWP{cond}{B} Rd,Rm,[Rn]      ;Rd=[Rn], [Rn]=Rm
  22     B - Byte/Word bit (0=swap 32bit/word, 1=swap 8bit/byte)
  21-20  Must be 00b for this instruction
  19-16  Rn - Base register                     (R0-R14)
  15-12  Rd - Destination Register              (R0-R14)
  11-4   Must be 00001001b for this instruction
  3-0    Rm - Source Register                   (R0-R14)
```

SWP/SWPB supported by ARMv2a and up.

Swap works properly including if Rm and Rn specify the same register.

R15 may not be used for either Rn,Rd,Rm. (Rn=R15 would be MRS opcode).

Upper bits of Rd are zero-expanded when using Byte quantity. For info about
byte and word data memory addressing, read LDR and STR opcode description.

Execution Time: 1S+2N+1I. That is, 2N data cycles, 1S code cycle, plus 1I.

## <a name="armopcodescoprocessorinstructionsmrcmcrldcstccdpmcrrmrrc"></a>  ARM Opcodes: Coprocessor Instructions (MRC/MCR, LDC/STC, CDP, MCRR/MRRC)

### Coprocessor Register Transfers (MRC, MCR) (with ARM Register read/write)

```
  Bit    Expl.
  31-28  Condition (or 1111b for MRC2/MCR2 opcodes on ARMv5 and up)
  27-24  Must be 1110b for this instruction
  23-21  CP Opc - Coprocessor operation code         (0-7)
  20     ARM-Opcode (0-1)
          0: MCR{cond} Pn,<cpopc>,Rd,Cn,Cm{,<cp>}   ;move from ARM to CoPro
          0: MCR2      Pn,<cpopc>,Rd,Cn,Cm{,<cp>}   ;move from ARM to CoPro
          1: MRC{cond} Pn,<cpopc>,Rd,Cn,Cm{,<cp>}   ;move from CoPro to ARM
          1: MRC2      Pn,<cpopc>,Rd,Cn,Cm{,<cp>}   ;move from CoPro to ARM
  19-16  Cn     - Coprocessor source/dest. Register  (C0-C15)
  15-12  Rd     - ARM source/destination Register    (R0-R15)
  11-8   Pn     - Coprocessor number                 (P0-P15)
  7-5    CP     - Coprocessor information            (0-7)
  4      Reserved, must be one (1) (otherwise CDP opcode)
  3-0    Cm     - Coprocessor operand Register       (C0-C15)
```

MCR/MRC supported by ARMv2 and up, MCR2/MRC2 by ARMv5 and up.

A22i syntax allows to use MOV with Rd specified as first (dest), or last
(source) operand. Native MCR/MRC syntax uses Rd as middle operand, \<cp>
can be ommited if \<cp> is zero.

When using MCR with R15: Coprocessor will receive a data value of PC+12.

When using MRC with R15: Bit 31-28 of data are copied to Bit 31-28 of CPSR (ie.
N,Z,C,V flags), other data bits are ignored, CPSR Bit 27-0 are not affected,
R15 (PC) is not affected.

Execution time: 1S+bI+1C for MCR, 1S+(b+1)I+1C for MRC.

Return: For MRC only: Either R0-R14 modified, or flags affected (see above).

For details refer to original ARM docs. The opcodes irrelevant for GBA/NDS7
because no coprocessor exists (except for a dummy CP14 unit). However, NDS9
includes a working CP15 unit.

- [ARM CP14 ICEbreaker Debug Communications Channel](#armcp14icebreakerdebugcommunicationschannel)
- [ARM CP15 System Control Coprocessor](#armcp15systemcontrolcoprocessor)

### Coprocessor Data Transfers (LDC, STC) (with Memory read/write)

```
  Bit    Expl.
  31-28  Condition (or 1111b for LDC2/STC2 opcodes on ARMv5 and up)
  27-25  Must be 110b for this instruction
  24     P - Pre/Post (0=post; add offset after transfer, 1=pre; before trans.)
  23     U - Up/Down Bit (0=down; subtract offset from base, 1=up; add to base)
  22     N - Transfer length (0-1, interpretation depends on co-processor)
  21     W - Write-back bit (0=no write-back, 1=write address into base)
  20     Opcode (0-1)
          0: STC{cond}{L} Pn,Cd,<Address>  ;Store to memory (from coprocessor)
          0: STC2{L}      Pn,Cd,<Address>  ;Store to memory (from coprocessor)
          1: LDC{cond}{L} Pn,Cd,<Address>  ;Read from memory (to coprocessor)
          1: LDC2{L}      Pn,Cd,<Address>  ;Read from memory (to coprocessor)
          whereas {L} indicates long transfer (Bit 22: N=1)
  19-16  Rn     - ARM Base Register              (R0-R15)     (R15=PC+8)
  15-12  Cd     - Coprocessor src/dest Register  (C0-C15)
  11-8   Pn     - Coprocessor number             (P0-P15)
  7-0    Offset - Unsigned Immediate, step 4     (0-1020, in steps of 4)
```

LDC/STC supported by ARMv2 and up, LDC2/STC2 by ARMv5 and up.

Execution time: (n-1)S+2N+bI, n=number of words transferred.

For details refer to original ARM docs, irrelevant in GBA because no
coprocessor exists.

### Coprocessor Data Operations (CDP) (without Memory or ARM Register operand)

```
  Bit    Expl.
  31-28  Condition (or 1111b for CDP2 opcode on ARMv5 and up)
  27-24  Must be 1110b for this instruction
         ARM-Opcode (fixed)
           CDP{cond} Pn,<cpopc>,Cd,Cn,Cm{,<cp>}
           CDP2      Pn,<cpopc>,Cd,Cn,Cm{,<cp>}
  23-20  CP Opc - Coprocessor operation code       (0-15)
  19-16  Cn     - Coprocessor operand Register     (C0-C15)
  15-12  Cd     - Coprocessor destination Register (C0-C15)
  11-8   Pn     - Coprocessor number               (P0-P15)
  7-5    CP     - Coprocessor information          (0-7)
  4      Reserved, must be zero (otherwise MCR/MRC opcode)
  3-0    Cm     - Coprocessor operand Register     (C0-C15)
```

CDP supported by ARMv2 and up, CDP2 by ARMv5 and up.

Execution time: 1S+bI, b=number of cycles in coprocessor busy-wait loop.

Return: No flags affected, no ARM-registers used/modified.

For details refer to original ARM docs, irrelevant in GBA because no
coprocessor exists.

### Coprocessor Double-Register Transfer (MCRR, MRRC) - ARMv5TE only

```
  Bit    Expl.
  31-28  Condition
  27-21  Must be 1100010b for this instruction
  20     L - Opcode (Load/Store)
          0: MCRR{cond} Pn,opcode,Rd,Rn,Cm  ;write Rd,Rn to coproc
          1: MRRC{cond} Pn,opcode,Rd,Rn,Cm  ;read Rd,Rn from coproc
  19-16  Rn - Second source/dest register      (R0-R14)
  15-12  Rd - First source/dest register       (R0-R14)
  11-8   Pn     - Coprocessor number           (P0-P15)
  7-4    CP Opc - Coprocessor operation code   (0-15)
  3-0    Cm     - Coprocessor operand Register (C0-C15)
```

Supported by ARMv5TE only, not ARMv5, not ARMv5TExP.

## <a name="thumbinstructionsummary"></a>  THUMB Instruction Summary

The table below lists all THUMB mode instructions with clock cycles, affected
CPSR flags, Format/chapter number, and description.

Only register R0..R7 can be used in thumb mode (unless R8-15,SP,PC are
explicitly mentioned).

### Logical Operations

```
  Instruction        Cycles Flags Format Expl.
  MOV Rd,Imm8bit      1S     NZ--  3   Rd=nn
  MOV Rd,Rs           1S     NZ00  2   Rd=Rs+0
  MOV R0..14,R8..15   1S     ----  5   Rd=Rs
  MOV R8..14,R0..15   1S     ----  5   Rd=Rs
  MOV R15,R0..15      2S+1N  ----  5   PC=Rs
  MVN Rd,Rs           1S     NZ--  4   Rd=NOT Rs
  AND Rd,Rs           1S     NZ--  4   Rd=Rd AND Rs
  TST Rd,Rs           1S     NZ--  4 Void=Rd AND Rs
  BIC Rd,Rs           1S     NZ--  4   Rd=Rd AND NOT Rs
  ORR Rd,Rs           1S     NZ--  4   Rd=Rd OR Rs
  EOR Rd,Rs           1S     NZ--  4   Rd=Rd XOR Rs
  LSL Rd,Rs,Imm5bit   1S     NZc-  1   Rd=Rs SHL nn
  LSL Rd,Rs           1S+1I  NZc-  4   Rd=Rd SHL (Rs AND 0FFh)
  LSR Rd,Rs,Imm5bit   1S     NZc-  1   Rd=Rs SHR nn
  LSR Rd,Rs           1S+1I  NZc-  4   Rd=Rd SHR (Rs AND 0FFh)
  ASR Rd,Rs,Imm5bit   1S     NZc-  1   Rd=Rs SAR nn
  ASR Rd,Rs           1S+1I  NZc-  4   Rd=Rd SAR (Rs AND 0FFh)
  ROR Rd,Rs           1S+1I  NZc-  4   Rd=Rd ROR (Rs AND 0FFh)
  NOP                 1S     ----  5   R8=R8
```

Carry flag affected only if shift amount is non-zero.

### Arithmetic Operations and Multiply

```
  Instruction        Cycles Flags Format Expl.
  ADD Rd,Rs,Imm3bit   1S     NZCV  2   Rd=Rs+nn
  ADD Rd,Imm8bit      1S     NZCV  3   Rd=Rd+nn
  ADD Rd,Rs,Rn        1S     NZCV  2   Rd=Rs+Rn
  ADD R0..14,R8..15   1S     ----  5   Rd=Rd+Rs
  ADD R8..14,R0..15   1S     ----  5   Rd=Rd+Rs
  ADD R15,R0..15      2S+1N  ----  5   PC=Rd+Rs
  ADD Rd,PC,Imm8bit*4 1S     ---- 12   Rd=(($+4) AND NOT 2)+nn
  ADD Rd,SP,Imm8bit*4 1S     ---- 12   Rd=SP+nn
  ADD SP,Imm7bit*4    1S     ---- 13   SP=SP+nn
  ADD SP,-Imm7bit*4   1S     ---- 13   SP=SP-nn
  ADC Rd,Rs           1S     NZCV  4   Rd=Rd+Rs+Cy
  SUB Rd,Rs,Imm3Bit   1S     NZCV  2   Rd=Rs-nn
  SUB Rd,Imm8bit      1S     NZCV  3   Rd=Rd-nn
  SUB Rd,Rs,Rn        1S     NZCV  2   Rd=Rs-Rn
  SBC Rd,Rs           1S     NZCV  4   Rd=Rd-Rs-NOT Cy
  NEG Rd,Rs           1S     NZCV  4   Rd=0-Rs
  CMP Rd,Imm8bit      1S     NZCV  3 Void=Rd-nn
  CMP Rd,Rs           1S     NZCV  4 Void=Rd-Rs
  CMP R0-15,R8-15     1S     NZCV  5 Void=Rd-Rs
  CMP R8-15,R0-15     1S     NZCV  5 Void=Rd-Rs
  CMN Rd,Rs           1S     NZCV  4 Void=Rd+Rs
  MUL Rd,Rs           1S+mI  NZx-  4   Rd=Rd*Rs
```

### Jumps and Calls

```
  Instruction        Cycles    Flags Format Expl.
  B disp              2S+1N     ---- 18  PC=$+/-2048
  BL disp             3S+1N     ---- 19  PC=$+/-4M, LR=$+5
  B{cond=true} disp   2S+1N     ---- 16  PC=$+/-0..256
  B{cond=false} disp  1S        ---- 16  N/A
  BX R0..15           2S+1N     ----  5  PC=Rs, ARM/THUMB (Rs bit0)
  SWI Imm8bit         2S+1N     ---- 17  PC=8, ARM SVC mode, LR=$+2
  BKPT Imm8bit        ???       ---- 17  ??? ARM9 Prefetch Abort
  BLX disp            ???       ---- ??? ??? ARM9
  BLX R0..R14         ???       ---- ??? ??? ARM9
  POP {Rlist,}PC   (n+1)S+2N+1I ---- 14
  MOV R15,R0..15      2S+1N     ----  5  PC=Rs
  ADD R15,R0..15      2S+1N     ----  5  PC=Rd+Rs
```

The thumb BL instruction occupies two 16bit opcodes, 32bit in total.

### Memory Load/Store

```
  Instruction        Cycles    Flags Format Expl.
  LDR  Rd,[Rb,5bit*4] 1S+1N+1I  ----  9  Rd = WORD[Rb+nn]
  LDR  Rd,[PC,8bit*4] 1S+1N+1I  ----  6  Rd = WORD[PC+nn]
  LDR  Rd,[SP,8bit*4] 1S+1N+1I  ---- 11  Rd = WORD[SP+nn]
  LDR  Rd,[Rb,Ro]     1S+1N+1I  ----  7  Rd = WORD[Rb+Ro]
  LDRB Rd,[Rb,5bit*1] 1S+1N+1I  ----  9  Rd = BYTE[Rb+nn]
  LDRB Rd,[Rb,Ro]     1S+1N+1I  ----  7  Rd = BYTE[Rb+Ro]
  LDRH Rd,[Rb,5bit*2] 1S+1N+1I  ---- 10  Rd = HALFWORD[Rb+nn]
  LDRH Rd,[Rb,Ro]     1S+1N+1I  ----  8  Rd = HALFWORD[Rb+Ro]
  LDSB Rd,[Rb,Ro]     1S+1N+1I  ----  8  Rd = SIGNED_BYTE[Rb+Ro]
  LDSH Rd,[Rb,Ro]     1S+1N+1I  ----  8  Rd = SIGNED_HALFWORD[Rb+Ro]
  STR  Rd,[Rb,5bit*4] 2N        ----  9  WORD[Rb+nn] = Rd
  STR  Rd,[SP,8bit*4] 2N        ---- 11  WORD[SP+nn] = Rd
  STR  Rd,[Rb,Ro]     2N        ----  7  WORD[Rb+Ro] = Rd
  STRB Rd,[Rb,5bit*1] 2N        ----  9  BYTE[Rb+nn] = Rd
  STRB Rd,[Rb,Ro]     2N        ----  7  BYTE[Rb+Ro] = Rd
  STRH Rd,[Rb,5bit*2] 2N        ---- 10  HALFWORD[Rb+nn] = Rd
  STRH Rd,[Rb,Ro]     2N        ----  8  HALFWORD[Rb+Ro]=Rd
  PUSH {Rlist}{LR}    (n-1)S+2N ---- 14
  POP  {Rlist}{PC}              ---- 14  (ARM9: with mode switch)
  STMIA Rb!,{Rlist}   (n-1)S+2N ---- 15
  LDMIA Rb!,{Rlist}   nS+1N+1I  ---- 15
```

### THUMB Binary Opcode Format

This table summarizes the position of opcode/parameter bits for THUMB mode
instructions, Format 1-19.

```
 Form|_15|_14|_13|_12|_11|_10|_9_|_8_|_7_|_6_|_5_|_4_|_3_|_2_|_1_|_0_|
 __1_|_0___0___0_|__Op___|_______Offset______|____Rs_____|____Rd_____|Shifted
 __2_|_0___0___0___1___1_|_I,_Op_|___Rn/nn___|____Rs_____|____Rd_____|ADD/SUB
 __3_|_0___0___1_|__Op___|____Rd_____|_____________Offset____________|Immedi.
 __4_|_0___1___0___0___0___0_|______Op_______|____Rs_____|____Rd_____|AluOp
 __5_|_0___1___0___0___0___1_|__Op___|Hd_|Hs_|____Rs_____|____Rd_____|HiReg/BX
 __6_|_0___1___0___0___1_|____Rd_____|_____________Word______________|LDR PC
 __7_|_0___1___0___1_|__Op___|_0_|___Ro______|____Rb_____|____Rd_____|LDR/STR
 __8_|_0___1___0___1_|__Op___|_1_|___Ro______|____Rb_____|____Rd_____|""H/SB/SH
 __9_|_0___1___1_|__Op___|_______Offset______|____Rb_____|____Rd_____|""{B}
 _10_|_1___0___0___0_|Op_|_______Offset______|____Rb_____|____Rd_____|""H
 _11_|_1___0___0___1_|Op_|____Rd_____|_____________Word______________|"" SP
 _12_|_1___0___1___0_|Op_|____Rd_____|_____________Word______________|ADD PC/SP
 _13_|_1___0___1___1___0___0___0___0_|_S_|___________Word____________|ADD SP,nn
 _14_|_1___0___1___1_|Op_|_1___0_|_R_|____________Rlist______________|PUSH/POP
 _17_|_1___0___1___1___1___1___1___0_|___________User_Data___________|BKPT ARM9
 _15_|_1___1___0___0_|Op_|____Rb_____|____________Rlist______________|STM/LDM
 _16_|_1___1___0___1_|_____Cond______|_________Signed_Offset_________|B{cond}
 _U__|_1___1___0___1___1___1___1___0_|_____________var_______________|UndefARM9
 _17_|_1___1___0___1___1___1___1___1_|___________User_Data___________|SWI
 _18_|_1___1___1___0___0_|________________Offset_____________________|B
 _19_|_1___1___1___0___1_|_________________________var___________|_0_|BLX.ARM9
 _U__|_1___1___1___0___1_|_________________________var___________|_1_|UndefARM9
 _19_|_1___1___1___1_|_H_|______________Offset_Low/High______________|BL,BLX
```

Further UNDEFS ??? ARM9?

- 1011 0001 xxxxxxxx (reserved)
- 1011 0x1x xxxxxxxx (reserved)
- 1011 10xx xxxxxxxx (reserved)
- 1011 1111 xxxxxxxx (reserved)
- 1101 1110 xxxxxxxx (free for user)

## <a name="thumbopcodesregisteroperationsalubx"></a>  THUMB Opcodes: Register Operations (ALU, BX)

### THUMB.1: move shifted register

```
  15-13  Must be 000b for 'move shifted register' instructions
  12-11  Opcode
           00b: LSL Rd,Rs,#Offset   (logical/arithmetic shift left)
           01b: LSR Rd,Rs,#Offset   (logical    shift right)
           10b: ASR Rd,Rs,#Offset   (arithmetic shift right)
           11b: Reserved (used for add/subtract instructions)
  10-6   Offset                     (0-31)
  5-3    Rs - Source register       (R0..R7)
  2-0    Rd - Destination register  (R0..R7)
```

Example: LSL Rd,Rs,#nn ; Rd = Rs \<\< nn ; ARM equivalent: MOVS Rd,Rs,LSL
#nn

Zero shift amount is having special meaning (same as for ARM shifts), LSL#0
performs no shift (the carry flag remains unchanged), LSR/ASR#0 are interpreted
as LSR/ASR#32. Attempts to specify LSR/ASR#0 in source code are automatically
redirected as LSL#0, and source LSR/ASR#32 is redirected as opcode LSR/ASR#0.

Execution Time: 1S

Flags: Z=zeroflag, N=sign, C=carry (except LSL#0: C=unchanged), V=unchanged.

### THUMB.2: add/subtract

```
  15-11  Must be 00011b for 'add/subtract' instructions
  10-9   Opcode (0-3)
           0: ADD Rd,Rs,Rn   ;add register        Rd=Rs+Rn
           1: SUB Rd,Rs,Rn   ;subtract register   Rd=Rs-Rn
           2: ADD Rd,Rs,#nn  ;add immediate       Rd=Rs+nn
           3: SUB Rd,Rs,#nn  ;subtract immediate  Rd=Rs-nn
         Pseudo/alias opcode with Imm=0:
           2: MOV Rd,Rs      ;move (affects cpsr) Rd=Rs+0
  8-6    For Register Operand:
           Rn - Register Operand (R0..R7)
         For Immediate Operand:
           nn - Immediate Value  (0-7)
  5-3    Rs - Source register       (R0..R7)
  2-0    Rd - Destination register  (R0..R7)
```

Return: Rd contains result, N,Z,C,V affected (including MOV).

Execution Time: 1S

### THUMB.3: move/compare/add/subtract immediate

```
  15-13  Must be 001b for this type of instructions
  12-11  Opcode
           00b: MOV Rd,#nn      ;move     Rd   = #nn
           01b: CMP Rd,#nn      ;compare  Void = Rd - #nn
           10b: ADD Rd,#nn      ;add      Rd   = Rd + #nn
           11b: SUB Rd,#nn      ;subtract Rd   = Rd - #nn
  10-8   Rd - Destination Register  (R0..R7)
  7-0    nn - Unsigned Immediate    (0-255)
```

ARM equivalents for MOV/CMP/ADD/SUB are MOVS/CMP/ADDS/SUBS same format.

Execution Time: 1S

Return: Rd contains result (except CMP), N,Z,C,V affected (for MOV only N,Z).

### THUMB.4: ALU operations

```
  15-10  Must be 010000b for this type of instructions
  9-6    Opcode (0-Fh)
           0: AND Rd,Rs     ;AND logical       Rd = Rd AND Rs
           1: EOR Rd,Rs     ;XOR logical       Rd = Rd XOR Rs
           2: LSL Rd,Rs     ;log. shift left   Rd = Rd << (Rs AND 0FFh)
           3: LSR Rd,Rs     ;log. shift right  Rd = Rd >> (Rs AND 0FFh)
           4: ASR Rd,Rs     ;arit shift right  Rd = Rd SAR (Rs AND 0FFh)
           5: ADC Rd,Rs     ;add with carry    Rd = Rd + Rs + Cy
           6: SBC Rd,Rs     ;sub with carry    Rd = Rd - Rs - NOT Cy
           7: ROR Rd,Rs     ;rotate right      Rd = Rd ROR (Rs AND 0FFh)
           8: TST Rd,Rs     ;test            Void = Rd AND Rs
           9: NEG Rd,Rs     ;negate            Rd = 0 - Rs
           A: CMP Rd,Rs     ;compare         Void = Rd - Rs
           B: CMN Rd,Rs     ;neg.compare     Void = Rd + Rs
           C: ORR Rd,Rs     ;OR logical        Rd = Rd OR Rs
           D: MUL Rd,Rs     ;multiply          Rd = Rd * Rs
           E: BIC Rd,Rs     ;bit clear         Rd = Rd AND NOT Rs
           F: MVN Rd,Rs     ;not               Rd = NOT Rs
  5-3    Rs - Source Register       (R0..R7)
  2-0    Rd - Destination Register  (R0..R7)
```

ARM equivalent for NEG would be RSBS.

Return: Rd contains result (except TST,CMP,CMN),

Affected Flags:

```
  N,Z,C,V for  ADC,SBC,NEG,CMP,CMN
  N,Z,C   for  LSL,LSR,ASR,ROR (carry flag unchanged if zero shift amount)
  N,Z,C   for  MUL on ARMv4 and below: carry flag destroyed
  N,Z     for  MUL on ARMv5 and above: carry flag unchanged
  N,Z     for  AND,EOR,TST,ORR,BIC,MVN
```

Execution Time:

```
  1S      for  AND,EOR,ADC,SBC,TST,NEG,CMP,CMN,ORR,BIC,MVN
  1S+1I   for  LSL,LSR,ASR,ROR
  1S+mI   for  MUL on ARMv4 (m=1..4; depending on MSBs of incoming Rd value)
  1S+mI   for  MUL on ARMv5 (m=3; fucking slow, no matter of MSBs of Rd value)
```

### THUMB.5: Hi register operations/branch exchange

```
  15-10  Must be 010001b for this type of instructions
  9-8    Opcode (0-3)
           0: ADD Rd,Rs   ;add        Rd = Rd+Rs
           1: CMP Rd,Rs   ;compare  Void = Rd-Rs  ;CPSR affected
           2: MOV Rd,Rs   ;move       Rd = Rs
           2: NOP         ;nop        R8 = R8
           3: BX  Rs      ;jump       PC = Rs     ;may switch THUMB/ARM
           3: BLX Rs      ;call       PC = Rs     ;may switch THUMB/ARM (ARM9)
  7      MSBd - Destination Register most significant bit (or BL/BLX flag)
  6      MSBs - Source Register most significant bit
  5-3    Rs - Source Register        (together with MSBs: R0..R15)
  2-0    Rd - Destination Register   (together with MSBd: R0..R15)
```

Restrictions: For ADD/CMP/MOV, MSBs and/or MSBd must be set, ie. it is not
allowed that both are cleared.

When using R15 (PC) as operand, the value will be the address of the
instruction plus 4 (ie. $+4). Except for BX R15: CPU switches to ARM state, and
PC is auto-aligned as (($+4) AND NOT 2).

For BX, MSBs may be 0 or 1, MSBd must be zero, Rd is not used/zero.

For BLX, MSBs may be 0 or 1, MSBd must be set, Rd is not used/zero.

For BX/BLX, when Bit 0 of the value in Rs is zero:

```
  Processor will be switched into ARM mode!
  If so, Bit 1 of Rs must be cleared (32bit word aligned).
  Thus, BX PC (switch to ARM) may be issued from word-aligned address
  only, the destination is PC+4 (ie. the following halfword is skipped).
```

BLX may not use R15. BLX saves the return address as LR=PC+3 (with thumb bit).

Using BLX R14 is possible (sets PC=Old\_LR, and New\_LR=retadr).

Assemblers/Disassemblers should use MOV R8,R8 as NOP (in THUMB mode).

Return: Only CMP affects CPSR condition flags!

Execution Time:

```
 1S     for ADD/MOV/CMP
 2S+1N  for ADD/MOV with Rd=R15, and for BX
```

## <a name="thumbopcodesmemoryloadstoreldrstr"></a>  THUMB Opcodes: Memory Load/Store (LDR/STR)

### THUMB.6: load PC-relative (for loading immediates from literal pool)

```
  15-11  Must be 01001b for this type of instructions
  N/A    Opcode (fixed)
           LDR Rd,[PC,#nn]      ;load 32bit    Rd = WORD[PC+nn]
  10-8   Rd - Destination Register   (R0..R7)
  7-0    nn - Unsigned offset        (0-1020 in steps of 4)
```

The value of PC will be interpreted as (($+4) AND NOT 2).

Return: No flags affected, data loaded into Rd.

Execution Time: 1S+1N+1I

### THUMB.7: load/store with register offset

```
  15-12  Must be 0101b for this type of instructions
  11-10  Opcode (0-3)
          0: STR  Rd,[Rb,Ro]   ;store 32bit data  WORD[Rb+Ro] = Rd
          1: STRB Rd,[Rb,Ro]   ;store  8bit data  BYTE[Rb+Ro] = Rd
          2: LDR  Rd,[Rb,Ro]   ;load  32bit data  Rd = WORD[Rb+Ro]
          3: LDRB Rd,[Rb,Ro]   ;load   8bit data  Rd = BYTE[Rb+Ro]
  9      Must be zero (0) for this type of instructions
  8-6    Ro - Offset Register              (R0..R7)
  5-3    Rb - Base Register                (R0..R7)
  2-0    Rd - Source/Destination Register  (R0..R7)
```

Return: No flags affected, data loaded either into Rd or into memory.

Execution Time: 1S+1N+1I for LDR, or 2N for STR

### THUMB.8: load/store sign-extended byte/halfword

```
  15-12  Must be 0101b for this type of instructions
  11-10  Opcode (0-3)
          0: STRH Rd,[Rb,Ro]  ;store 16bit data          HALFWORD[Rb+Ro] = Rd
          1: LDSB Rd,[Rb,Ro]  ;load sign-extended 8bit   Rd = BYTE[Rb+Ro]
          2: LDRH Rd,[Rb,Ro]  ;load zero-extended 16bit  Rd = HALFWORD[Rb+Ro]
          3: LDSH Rd,[Rb,Ro]  ;load sign-extended 16bit  Rd = HALFWORD[Rb+Ro]
  9      Must be set (1) for this type of instructions
  8-6    Ro - Offset Register              (R0..R7)
  5-3    Rb - Base Register                (R0..R7)
  2-0    Rd - Source/Destination Register  (R0..R7)
```

Return: No flags affected, data loaded either into Rd or into memory.

Execution Time: 1S+1N+1I for LDR, or 2N for STR

### THUMB.9: load/store with immediate offset

```
  15-13  Must be 011b for this type of instructions
  12-11  Opcode (0-3)
          0: STR  Rd,[Rb,#nn]  ;store 32bit data   WORD[Rb+nn] = Rd
          1: LDR  Rd,[Rb,#nn]  ;load  32bit data   Rd = WORD[Rb+nn]
          2: STRB Rd,[Rb,#nn]  ;store  8bit data   BYTE[Rb+nn] = Rd
          3: LDRB Rd,[Rb,#nn]  ;load   8bit data   Rd = BYTE[Rb+nn]
  10-6   nn - Unsigned Offset              (0-31 for BYTE, 0-124 for WORD)
  5-3    Rb - Base Register                (R0..R7)
  2-0    Rd - Source/Destination Register  (R0..R7)
```

Return: No flags affected, data loaded either into Rd or into memory.

Execution Time: 1S+1N+1I for LDR, or 2N for STR

### THUMB.10: load/store halfword

```
  15-12  Must be 1000b for this type of instructions
  11     Opcode (0-1)
          0: STRH Rd,[Rb,#nn]  ;store 16bit data   HALFWORD[Rb+nn] = Rd
          1: LDRH Rd,[Rb,#nn]  ;load  16bit data   Rd = HALFWORD[Rb+nn]
  10-6   nn - Unsigned Offset              (0-62, step 2)
  5-3    Rb - Base Register                (R0..R7)
  2-0    Rd - Source/Destination Register  (R0..R7)
```

Return: No flags affected, data loaded either into Rd or into memory.

Execution Time: 1S+1N+1I for LDR, or 2N for STR

### THUMB.11: load/store SP-relative

```
  15-12  Must be 1001b for this type of instructions
  11     Opcode (0-1)
          0: STR  Rd,[SP,#nn]  ;store 32bit data   WORD[SP+nn] = Rd
          1: LDR  Rd,[SP,#nn]  ;load  32bit data   Rd = WORD[SP+nn]
  10-8   Rd - Source/Destination Register  (R0..R7)
  7-0    nn - Unsigned Offset              (0-1020, step 4)
```

Return: No flags affected, data loaded either into Rd or into memory.

Execution Time: 1S+1N+1I for LDR, or 2N for STR

## <a name="thumbopcodesmemoryaddressingaddpcsp"></a>  THUMB Opcodes: Memory Addressing (ADD PC/SP)

### THUMB.12: get relative address

```
  15-12  Must be 1010b for this type of instructions
  11     Opcode/Source Register (0-1)
          0: ADD  Rd,PC,#nn    ;Rd = (($+4) AND NOT 2) + nn
          1: ADD  Rd,SP,#nn    ;Rd = SP + nn
  10-8   Rd - Destination Register         (R0..R7)
  7-0    nn - Unsigned Offset              (0-1020, step 4)
```

Return: No flags affected, result in Rd.

Execution Time: 1S

### THUMB.13: add offset to stack pointer

```
  15-8   Must be 10110000b for this type of instructions
  7      Opcode/Sign
          0: ADD  SP,#nn       ;SP = SP + nn
          1: ADD  SP,#-nn      ;SP = SP - nn
  6-0    nn - Unsigned Offset    (0-508, step 4)
```

Return: No flags affected, SP adjusted.

Execution Time: 1S

## <a name="thumbopcodesmemorymultipleloadstorepushpopandldmstm"></a>  THUMB Opcodes: Memory Multiple Load/Store (PUSH/POP and LDM/STM)

### THUMB.14: push/pop registers

```
  15-12  Must be 1011b for this type of instructions
  11     Opcode (0-1)
          0: PUSH {Rlist}{LR}   ;store in memory, decrements SP (R13)
          1: POP  {Rlist}{PC}   ;load from memory, increments SP (R13)
  10-9   Must be 10b for this type of instructions
  8      PC/LR Bit (0-1)
          0: No
          1: PUSH LR (R14), or POP PC (R15)
  7-0    Rlist - List of Registers (R7..R0)
```

In THUMB mode stack is always meant to be 'full descending', ie. PUSH is
equivalent to 'STMFD/STMDB' and POP to 'LDMFD/LDMIA' in ARM mode.

Examples:

```
 PUSH {R0-R3}     ;push R0,R1,R2,R3
 PUSH {R0,R2,LR}  ;push R0,R2,LR
 POP  {R4,R7}     ;pop R4,R7
 POP  {R2-R4,PC}  ;pop R2,R3,R4,PC
```

Note: When calling to a sub-routine, the return address is stored in LR
register, when calling further sub-routines, PUSH {LR} must be used to save
higher return address on stack. If so, POP {PC} can be later used to return
from the sub-routine.

POP {PC} ignores the least significant bit of the return address (processor
remains in thumb state even if bit0 was cleared), when intending to return with
optional mode switch, use a POP/BX combination (eg. POP {R3} / BX R3).

ARM9: POP {PC} copies the LSB to thumb bit (switches to ARM if bit0=0).

Return: No flags affected, SP adjusted, registers loaded/stored.

Execution Time: nS+1N+1I (POP), (n+1)S+2N+1I (POP PC), or (n-1)S+2N (PUSH).

### THUMB.15: multiple load/store

```
  15-12  Must be 1100b for this type of instructions
  11     Opcode (0-1)
          0: STMIA Rb!,{Rlist}   ;store in memory, increments Rb
          1: LDMIA Rb!,{Rlist}   ;load from memory, increments Rb
  10-8   Rb - Base register (modified) (R0-R7)
  7-0    Rlist - List of Registers     (R7..R0)
```

Both STM and LDM are incrementing the Base Register.

The lowest register in the list (ie. R0, if it's in the list) is stored/loaded
at the lowest memory address.

Examples:

```
 STMIA R7!,{R0-R2}  ;store R0,R1,R2
 LDMIA R0!,{R1,R5}  ;store R1,R5
```

Return: No flags affected, Rb adjusted, registers loaded/stored.

Execution Time: nS+1N+1I for LDM, or (n-1)S+2N for STM.

### Strange Effects on Invalid Rlist's

Empty Rlist: R15 loaded/stored (ARMv4 only), and Rb=Rb+40h (ARMv4-v5).

Writeback with Rb included in Rlist: Store OLD base if Rb is FIRST entry in
Rlist, otherwise store NEW base (STM/ARMv4), always store OLD base (STM/ARMv5),
no writeback (LDM/ARMv4/ARMv5; at this point, THUMB opcodes work different than
ARM opcodes).

## <a name="thumbopcodesjumpsandcalls"></a>  THUMB Opcodes: Jumps and Calls

### THUMB.16: conditional branch

```
  15-12  Must be 1101b for this type of instructions
  11-8   Opcode/Condition (0-Fh)
          0: BEQ label        ;Z=1         ;equal (zero) (same)
          1: BNE label        ;Z=0         ;not equal (nonzero) (not same)
          2: BCS/BHS label    ;C=1         ;unsigned higher or same (carry set)
          3: BCC/BLO label    ;C=0         ;unsigned lower (carry cleared)
          4: BMI label        ;N=1         ;negative (minus)
          5: BPL label        ;N=0         ;positive or zero (plus)
          6: BVS label        ;V=1         ;overflow (V set)
          7: BVC label        ;V=0         ;no overflow (V cleared)
          8: BHI label        ;C=1 and Z=0 ;unsigned higher
          9: BLS label        ;C=0 or Z=1  ;unsigned lower or same
          A: BGE label        ;N=V         ;greater or equal
          B: BLT label        ;N<>V        ;less than
          C: BGT label        ;Z=0 and N=V ;greater than
          D: BLE label        ;Z=1 or N<>V ;less or equal
          E: Undefined, should not be used
          F: Reserved for SWI instruction (see SWI opcode)
  7-0    Signed Offset, step 2 ($+4-256..$+4+254)
```

Destination address must by halfword aligned (ie. bit 0 cleared)

Return: No flags affected, PC adjusted if condition true

Execution Time:

```
  2S+1N   if condition true (jump executed)
  1S      if condition false
```

### BX and ADD/MOV PC

See also THUMB.5: BX Rs, and ADD/MOV PC,Rs.

### THUMB.18: unconditional branch

```
  15-11  Must be 11100b for this type of instructions
  N/A    Opcode (fixed)
          B label   ;branch (jump)
  10-0   Signed Offset, step 2 ($+4-2048..$+4+2046)
```

Return: No flags affected, PC adjusted.

Execution Time: 2S+1N

### THUMB.19: long branch with link

This may be used to call (or jump) to a subroutine, return address is saved in
LR (R14).

Unlike all other THUMB mode instructions, this instruction occupies 32bit of
memory which are split into two 16bit THUMB opcodes.

- First Instruction - LR = PC+4+(nn SHL 12)
  - 15-11  Must be 11110b for BL/BLX type of instructions
  - 10-0   nn - Upper 11 bits of Target Address
- Second Instruction - PC = LR + (nn SHL 1), and LR = PC+2 OR 1 (and BLX: T=0)
  - 15-11  Opcode
                  - 11111b: BL label   ;branch long with link
                  - 11101b: BLX label  ;branch long with link switch to ARM mode (ARM9)
  - 10-0   nn - Lower 11 bits of Target Address (BLX: Bit0 Must be zero)

The destination address range is (PC+4)-400000h..+3FFFFEh, ie. PC+/-4M.

Target must be halfword-aligned. As Bit 0 in LR is set, it may be used to
return by a BX LR instruction (keeping CPU in THUMB mode).

Return: No flags affected, PC adjusted, return address in LR.

Execution Time: 3S+1N (first opcode 1S, second opcode 2S+1N).

Note: Exceptions may or may not occur between first and second opcode, this is
"implementation defined" (unknown how this is implemented in GBA and NDS).

Using only the 2nd half of BL as "BL LR+imm" is possible (for example, Mario
Golf Advance Tour for GBA uses opcode F800h as "BL LR+0").

### THUMB.17: software interrupt and breakpoint

SWI supposed for calls to the operating system - Enter Supervisor mode (SVC) in
ARM state. BKPT intended for debugging - enters Abort mode in ARM state via
Prefetch Abort vector.

```
  15-8   Opcode
          11011111b: SWI nn   ;software interrupt
          10111110b: BKPT nn  ;software breakpoint (ARMv5 and up)
  7-0    nn - Comment Field, ignored by processor (8bit value) (0-255)
```

Execution Time: 2S+1N

The exception handler may interprete the SWI Comment Field by examining the
lower 8bit of the 16bit opcode opcode at [R14\_svc-2].

If your are also using SWI's from inside of ARM mode, then the SWI handler must
examine the T Bit SPSR\_svc in order to determine whether it's been a ARM SWI -
and if so, examine the lower 24bit of the 32bit opcode opcode at [R14\_svc-4].

For Returning from SWI use "MOVS PC,R14", that instruction does restore both PC
and CPSR, ie. PC=R14\_svc, and CPSR=SPSR\_svc, and (as called from THUMB mode),
it'll also restore THUMB mode.

Nesting SWIs: SPSR\_svc and R14\_svc should be saved on stack before either
invoking nested SWIs, or (if the IRQ handler uses SWIs) before enabling IRQs.

Execution SWI/BKPT:

```
  R14_svc=PC+2     R14_abt=PC+4   ;save return address
  SPSR_svc=CPSR    SPSR_abt=CPSR  ;save CPSR flags
  CPSR=<changed>   CPSR=<changed> ;Enter svc/abt, ARM state, IRQs disabled
  PC=VVVV0008h     PC=VVVV000Ch   ;jump to SWI/PrefetchAbort vector address
```

## <a name="armpseudoinstructionsanddirectives"></a>  ARM Pseudo Instructions and Directives

### ARM Pseudo Instructions

```
  nop              mov r0,r0
  ldr Rd,=Imm      ldr Rd,[r15,disp] ;use .pool as parameter field
  add Rd,=addr     add/sub Rd,r15,disp
  adr Rd,addr      add/sub Rd,r15,disp
  adrl Rd,addr     two add/sub opcodes with disp=xx00h+00yyh
  mov Rd,Imm       mvn Rd,NOT Imm    ;or vice-versa
  and Rd,Rn,Imm    bic Rd,Rn,NOT Imm ;or vice-versa
  cmp Rd,Rn,Imm    cmn Rd,Rn,-Imm    ;or vice-versa
  add Rd,Rn,Imm    sub Rd,Rn,-Imm    ;or vice-versa
```

All above opcodes may be made conditional by specifying a {cond} field.

### THUMB Pseudo Instructions

```
  nop              mov r8,r8
  ldr Rd,=Imm      ldr Rd,[r15,disp] ;use .pool as parameter field
  add Rd,=addr     add Rd,r15,disp
  adr Rd,addr      add Rd,r15,disp
  mov Rd,Rs        add Rd,Rs,0       ;with Rd,Rs in range r0-r7 each
```

### A22i Directives

```
  org  adr     assume following code from this address on
  .gba         indicate GBA program
  .nds         indicate NDS program
  .dsi         indicate DSi program
  .fix         fix GBA/NDS/DSi header checksum
  .ereader_create_bmp      create GBA e-Reader dotcode .BMP file(s) (bitmaps)
  .ereader_create_raw      create GBA e-Reader dotcode .RAW file (useless)
  .ereader_create_bin      create GBA e-Reader dotcode .BIN file (smallest)
  .ereader_japan_plus      japanese/plus     (default is non-japanese)
  .ereader_japan_original  japanese/original (with Z80-stub for GBA-code)
  .title 'Txt' defines a title (used for e-Reader dotcodes)
  .teak        select TeakLiteII instruction set (for DSi DSP)
  .xtensa      select Xtensa instruction set (for DSi Atheros Wifi)
  .norewrite   do not delete existing output file (keep following data in file)
  .data?       following defines RAM data structure (assembled to nowhere)
  .code        following is normal ROM code/data (assembled to ROM image)
  .include     includes specified source code file (no nesting/error handling)
  .import      imports specified binary file (optional parameters: ,begin,len)
  .radix nn    changes default numeric format (nn=2,8,10,16 = bin/oct/dec/hex)
  .errif expr  generates an error message if expression is nonzero
  .if expr     assembles following code only if expression is nonzero
  .else        invert previous .if condition
  .endif       terminate .if/.ifdef/.ifndef
  .ifdef sym   assemble following only if symbol is defined
  .ifndef sym  assemble following only if symbol is not defined
  .align nn    aligns to an address divisible-by-nn, inserts 00's
  .msg         defines a no$gba debugmessage string, such like .msg 'Init Okay'
  .brk         defines a no$gba source code break opcode
  l equ n      l=n
  l:   [cmd]   l=$   (global label)
  @@l: [cmd]   @@l=$ (local label, all locals are reset at next global label)
  end          end of source code
  db ...       define 8bit data (bytes)
  dw ...       define 16bit data (halfwords)
  dd ...       define 32bit data (words)
  defs nn      define nn bytes space (zero-filled)
  ;...         defines a comment (ignored by the assembler)
  //           alias for CRLF, eg. allows <db 'Text',0 // dw addr> in one line
```

### A22i Alias Directives (for compatibility with other assemblers)

```
  align        .align 4          code16    .thumb
  align nn     .align nn         .code 16  .thumb
  % nn         defs nn           code32    .arm
  .space nn    defs nn           .code 32  .arm
  ..ds nn      defs nn           ltorg     .pool
  x=n          x equ n           .ltorg    .pool
  .equ x,n     x equ n           ..ltorg   .pool
  .define x n  x equ n           dcb       db (8bit data)
  incbin       .import           defb      db (8bit data)
  @@@...       ;comment          .byte     db (8bit data)
  @ ...        ;comment          .ascii    db (8bit string)
  @*...        ;comment          dcw       dw (16bit data)
  @...         ;comment          defw      dw (16bit data)
  .text        .code             .hword    dw (16bit data)
  .bss         .data?            dcd       dd (32bit data)
  .global      (ignored)         defd      dd (32bit data)
  .extern      (ignored)         .long     dd (32bit data)
  .thumb_func  (ignored)         .word     dw/dd, don't use
  #directive   .directive        .end      end
  .fill nn,1,0 defs nn
```

### Alias Conditions, Opcodes, Operands

```
  hs   cs   ;condition higher or same = carry set
  lo   cc   ;condition lower = carry cleared
  asl  lsl  ;arithmetic shift left = logical shift left
```

### A22i Numeric Formats & Dialects

```
  Type          Normal       Alias
  Decimal       85           #85  &d85
  Hexadecimal   55h          #55h  0x55  #0x55  $55  &h55
  Octal         125o         0o125  &o125
  Ascii         'U'          "U"
  Binary        01010101b    %01010101  0b01010101  &b01010101
  Roman         &rLXXXV      (very useful for arrays of kings and chapters)
```

Note: The default numeric format can be changed by the .radix directive
(usually 10=decimal). For example, with radix 16, values like "85" and "0101b"
are treated as hexadecimal numbers (in that case, decimal and binary numbers
can be still defined with prefixes &d and &b).

### A22i Numeric Operators Priority

```
  Prio  Operator           Aliases
  8     (,) brackets
  7     +,- sign
  6     *,/,MOD,SHL,SHR    MUL,DIV,<<,>>
  5     +,- operation
  4     EQ,GE,GT,LE,LT,NE  =,>=,>,<=,<,<>,==,!=
  3     NOT
  2     AND
  1     OR,XOR             EOR
```

Operators of same priority are processed from left to right.

Boolean operators (priority 4) return 1=TRUE, 0=FALSE.

### A22i Nocash Syntax

Even though A22i does recognize the official ARM syntax, it's also allowing to
use friendly code:

```
  mov   r0,0ffh         ;no C64-style "#", and no C-style "0x" required
  stmia [r7]!,r0,r4-r5  ;square [base] brackets, no fancy {rlist} brackets
  mov   r0,cpsr         ;no confusing MSR and MRS (whatever which is which)
  mov   r0,p0,0,c0,c0,0 ;no confusing MCR and MRC (whatever which is which)
  ldr   r0,[score]      ;allows to use clean brackets for relative addresses
  push  rlist           ;alias for stmfd [r13]!,rlist (and same for pop/ldmfd)
  label:                ;label definitions recommended to use ":" colons
```

[A22i is the no$gba debug version's built-in source code assembler.]










## <a name="armcpuinstructioncycletimes"></a>  ARM CPU Instruction Cycle Times

Instruction Cycle Summary

```
  Instruction      Cycles      Additional
  ---------------------------------------------------------------------
  ALU              1S          +1S+1N if R15 loaded, +1I if SHIFT(Rs)
  MSR,MRS          1S
  LDR              1S+1N+1I    +1S+1N if R15 loaded
  STR              2N
  LDM              nS+1N+1I    +1S+1N if R15 loaded
  STM              (n-1)S+2N
  SWP              1S+2N+1I
  BL (THUMB)       3S+1N
  B,BL             2S+1N
  SWI,trap         2S+1N
  MUL              1S+ml
  MLA              1S+(m+1)I
  MULL             1S+(m+1)I
  MLAL             1S+(m+2)I
  CDP              1S+bI
  LDC,STC          (n-1)S+2N+bI
  MCR              1N+bI+1C
  MRC              1S+(b+1)I+1C
  {cond} false     1S
```

ARM9:

```
  Q{D}ADD/SUB      1S+Interlock.
  CLZ              1S.
  LDR              1S+1N+1L
  LDRB,LDRH,LDRmis 1S+1N+2L
  LDR PC ...
  STR              1S+1N        (not 2N, and both in parallel)
```

Execution Time: 1S+Interlock    (SMULxy,SMLAxy,SMULWx,SMLAWx)

Execution Time: 1S+1I+Interlock (SMLALxy)

Whereas,

```
  n = number of words transferred
  b = number of cycles spent in coprocessor busy-wait loop
  m = depends on most significant byte(s) of multiplier operand
```

Above 'trap' is meant to be the execution time for exceptions. And '{cond}
false' is meant to be the execution time for conditional instructions which
haven't been actually executed because the condition has been false.

The separate meaning of the N,S,I,C cycles is:

### N - Non-sequential cycle

Requests a transfer to/from an address which is NOT related to the address used
in the previous cycle. (Called 1st Access in GBA language).

The execution time for 1N is 1 clock cycle (plus non-sequential access
waitstates).

### S - Sequential cycle

Requests a transfer to/from an address which is located directly after the
address used in the previous cycle. Ie. for 16bit or 32bit accesses at
incrementing addresses, the first access is Non-sequential, the following
accesses are sequential. (Called 2nd Access in GBA language).

The execution time for 1S is 1 clock cycle (plus sequential access waitstates).

### I - Internal Cycle

CPU is just too busy, not even requesting a memory transfer for now.

The execution time for 1I is 1 clock cycle (without any waitstates).

### C - Coprocessor Cycle

The CPU uses the data bus to communicate with the coprocessor (if any), but no
memory transfers are requested.

### Memory Waitstates

Ideally, memory may be accessed free of waitstates (1N and 1S are then equal to
1 clock cycle each). However, a memory system may generate waitstates for
several reasons: The memory may be just too slow. Memory is currently accessed
by DMA, eg. sound, video, memory transfers, etc. Or when data is squeezed
through a 16bit data bus (in that special case, 32bit access may have more
waitstates than 8bit and 16bit accesses). Also, the memory system may separate
between S and N cycles (if so, S cycles would be typically faster than N
cycles).

### Memory Waitstates for Different Memory Areas

Different memory areas (eg. ROM and RAM) may have different waitstates. When
executing code in one area which accesses data in another area, then the S+N
cycles must be split into code and data accesses: 1N is used for data access,
plus (n-1)S for LDM/STM, the remaining S+N are code access. If an instruction
jumps to a different memory area, then all code cycles for that opcode are
having waitstate characteristics of the NEW memory area (except Thumb BL which
still executes 1S in OLD area).

## <a name="armcpuversions"></a>  ARM CPU Versions

### Version Numbers

ARM CPUs are distributed by name ARM#, and are described as ARMv# in
specifications, whereas "#" is NOT the same than "v#", for example, ARM7TDMI is
ARMv4TM. That is so confusing, that ARM didn't even attempt to clarify the
relationship between the various "#" and "v#" values.

### Version Variants

Suffixes like "M" (long multiply), "T" (Thumb support), "E" (Enhanced DSP)
indicate presence of special features, additionally to the standard instruction
set of a given version, or, when preceded by an "x", indicate the absence of
that features.

### ARMv1 aka ARM1

Some sort of a beta version, according to ARM never been used in any commercial
products.

### ARMv2 and up

MUL,MLA

CDP,LDC,MCR,MRC,STC

SWP/SWPB (ARMv2a and up only)

Two new FIQ registers

### ARMv3 and up

MRS,MSR opcodes (instead CMP/CMN/TST/TEQ{P} opcodes)

CPSR,SPSR registers (instead PSR bits in R15)

Removed never condition, cond=NV no longer valid

32bit addressing (instead 26bit addressing in older versions)

26bit addressing backwards comptibility mode (except v3G)

Abt and Und modes (instead handling aborts/undefined in Svc mode)

SMLAL,SMULL,UMLAL,UMULL (optionally, INCLUDED in v3M, EXCLUDED in v4xM/v5xM)

### ARMv4 aka ARM7 and up

LDRH,LDRSB,LDRSH,STRH

Sys mode (privileged user mode)

BX (only ARMv4T, and any ARMv5 or ARMv5T and up)

THUMB code (only T variants, ie. ARMv4T, ARMv5T)

### ARMv5 aka ARM9 and up

BKPT,BLX,CLZ (BKPT,BLX also in THUMB mode)

LDM/LDR/POP PC with mode switch (POP PC also in THUMB mode)

CDP2,LDC2,MCR2,MRC2,STC2 (new coprocessor opcodes)

C-flag unchanged by MUL (instead undefined flag value)

changed instruction cycle timings / interlock ??? or not ???

QADD,QDADD,QDSUB,QSUB opcodes, CPSR.Q flag (v5TE and V5TExP only)

SMLAxy,SMLALxy,SMLAWy,SMULxy,SMULWy (v5TE and V5TExP only)

LDRD,STRD,PLD,MCRR,MRRC (v5TE only, not v5, not v5TExP)

### ARMv6

No public specifications available.

### A Milestone in Computer History

Original ARMv2 has been used in the relative rare and expensive Archimedes
deluxe home computers in the late eighties, the Archimedes has caught a lot of
attention, particularly for being the first home computer that used a BIOS
being programmed in BASIC language - which has been a absolutely revolutionary
decadency at that time.

Inspired, programmers all over the world have successfully developed even
slower and much more inefficient programming languages, which are nowadays
consequently used by nearly all ARM programmers, and by most non-ARM
programmers as well.

## <a name="armcpudatasheet"></a>  ARM CPU Data Sheet

This present document is an attempt to supply a brief ARM7TDMI reference,
hopefully including all information which is relevant for programmers.

Some details that I have treated as meaningless for GBA programming aren't
included - such like Big Endian format, and Virtual Memory data aborts, and
most of the chapters listed below.

Have a look at the complete data sheet (URL see below) for more detailed
verbose information about ARM7TDMI instructions. That document also includes:

- Signal Description

```
  Pins of the original CPU, probably other for GBA.
```

- Memory Interface

```
  Optional virtual memory circuits, etc. not for GBA.
```

- Coprocessor Interface

```
  As far as I know, none such in GBA.
```

- Debug Interface

```
  For external hardware-based debugging.
```

- ICEBreaker Module

```
  For external hardware-based debugging also.
```

- Instruction Cycle Operations

```
  Detailed: What happens during each cycle of each instruction.
```

- DC Parameters (Power supply)

- AC Parameters (Signal timings)

The official ARM7TDMI data sheet can be downloaded from ARMs webpage,

```
  http://www.arm.com/Documentation/UserMans/PDF/ARM7TDMI.html
```

Be prepared for bloated PDF Format, approx 1.3 MB, about 200 pages.

## <a name="biosfunctions"></a>  BIOS Functions

The BIOS includes several System Call Functions which can be accessed by SWI
instructions. Incoming parameters are usually passed through registers
R0,R1,R2,R3. Outgoing registers R0,R1,R3 are typically containing either
garbage, or return value(s). All other registers (R2,R4-R14) are kept
unchanged.

### Caution

When invoking SWIs from inside of ARM state specify SWI NN\*10000h, instead of
SWI NN as in THUMB state.

### Overview

- [BIOS Function Summary](#biosfunctionsummary)
- [BIOS Differences between GBA and NDS functions](#biosdifferencesbetweengbaandndsfunctions)

### All Functions Described

- [BIOS Arithmetic Functions](#biosarithmeticfunctions)
- [BIOS Rotation/Scaling Functions](#biosrotationscalingfunctions)
- [BIOS Decompression Functions](#biosdecompressionfunctions)
- [BIOS Memory Copy](#biosmemorycopy)
- [BIOS Halt Functions](#bioshaltfunctions)
- [BIOS Reset Functions](#biosresetfunctions)
- [BIOS Misc Functions](#biosmiscfunctions)
- [BIOS Multi Boot (Single Game Pak)](#biosmultibootsinglegamepak)
- [BIOS Sound Functions](#biossoundfunctions)
- [BIOS SHA1 Functions (DSi only)](#biossha1functionsdsionly)
- [BIOS RSA Functions (DSi only)](#biosrsafunctionsdsionly)

### RAM Usage, BIOS Dumps

- [BIOS RAM Usage](#biosramusage)
- [BIOS Dumping](#biosdumping)

### How BIOS Processes SWIs

SWIs can be called from both within THUMB and ARM mode. In ARM mode, only the
upper 8bit of the 24bit comment field are interpreted.

Each time when calling a BIOS function 4 words (SPSR, R11, R12, R14) are saved
on Supervisor stack (\_svc). Once it has saved that data, the SWI handler
switches into System mode, so that all further stack operations are using user
stack.

In some cases the BIOS may allow interrupts to be executed from inside of the
SWI procedure. If so, and if the interrupt handler calls further SWIs, then
care should be taken that the Supervisor Stack does not overflow.

## <a name="biosfunctionsummary"></a>  BIOS Function Summary

```
  GBA  NDS7 NDS9 DSi7 DSi9 Basic Functions
  00h  00h  00h  -    -    SoftReset
  01h  -    -    -    -    RegisterRamReset
  02h  06h  06h  06h  06h  Halt
  03h  07h  -    07h  -    Stop/Sleep
  04h  04h  04h  04h  04h  IntrWait       ;DSi7/DSi9: both bugged?
  05h  05h  05h  05h  05h  VBlankIntrWait ;DSi7/DSi9: both bugged?
  06h  09h  09h  09h  09h  Div
  07h  -    -    -    -    DivArm
  08h  0Dh  0Dh  0Dh  0Dh  Sqrt
  09h  -    -    -    -    ArcTan
  0Ah  -    -    -    -    ArcTan2
  0Bh  0Bh  0Bh  0Bh  0Bh  CpuSet
  0Ch  0Ch  0Ch  0Ch  0Ch  CpuFastSet
  0Dh  -    -    -    -    GetBiosChecksum
  0Eh  -    -    -    -    BgAffineSet
  0Fh  -    -    -    -    ObjAffineSet
  GBA  NDS7 NDS9 DSi7 DSi9 Decompression Functions
  10h  10h  10h  10h  10h  BitUnPack
  11h  11h  11h  11h  11h  LZ77UnCompReadNormalWrite8bit   ;"Wram"
  12h  -    -    -    -    LZ77UnCompReadNormalWrite16bit  ;"Vram"
  -    -    -    01h  01h  LZ77UnCompReadByCallbackWrite8bit
  -    12h  12h  02h  02h  LZ77UnCompReadByCallbackWrite16bit
  -    -    -    19h  19h  LZ77UnCompReadByCallbackWrite16bit (same as above)
  13h  -    -    -    -    HuffUnCompReadNormal
  -    13h  13h  13h  13h  HuffUnCompReadByCallback
  14h  14h  14h  14h  14h  RLUnCompReadNormalWrite8bit     ;"Wram"
  15h  -    -    -    -    RLUnCompReadNormalWrite16bit    ;"Vram"
  -    15h  15h  15h  15h  RLUnCompReadByCallbackWrite16bit
  16h  -    16h  -    16h  Diff8bitUnFilterWrite8bit       ;"Wram"
  17h  -    -    -    -    Diff8bitUnFilterWrite16bit      ;"Vram"
  18h  -    18h  -    18h  Diff16bitUnFilter
  GBA  NDS7 NDS9 DSi7 DSi9 Sound (and Multiboot/HardReset/CustomHalt)
  19h  08h  -    08h  -    SoundBias
  1Ah  -    -    -    -    SoundDriverInit
  1Bh  -    -    -    -    SoundDriverMode
  1Ch  -    -    -    -    SoundDriverMain
  1Dh  -    -    -    -    SoundDriverVSync
  1Eh  -    -    -    -    SoundChannelClear
  1Fh  -    -    -    -    MidiKey2Freq
  20h  -    -    -    -    SoundWhatever0
  21h  -    -    -    -    SoundWhatever1
  22h  -    -    -    -    SoundWhatever2
  23h  -    -    -    -    SoundWhatever3
  24h  -    -    -    -    SoundWhatever4
  25h  -    -    -    -    MultiBoot
  26h  -    -    -    -    HardReset
  27h  1Fh  -    1Fh  -    CustomHalt
  28h  -    -    -    -    SoundDriverVSyncOff
  29h  -    -    -    -    SoundDriverVSyncOn
  2Ah  -    -    -    -    SoundGetJumpList
  GBA  NDS7 NDS9 DSi7 DSi9 New NDS Functions
  -    03h  03h  03h  03h  WaitByLoop
  -    0Eh  0Eh  0Eh  0Eh  GetCRC16
  -    0Fh  0Fh  -    -    IsDebugger
  -    1Ah  -    1Ah  -    GetSineTable
  -    1Bh  -    1Bh  -    GetPitchTable (DSi7: bugged)
  -    1Ch  -    1Ch  -    GetVolumeTable
  -    1Dh  -    1Dh  -    GetBootProcs (DSi7: only 1 proc)
  -    -    1Fh  -    1Fh  CustomPost
  GBA  NDS7 NDS9 DSi7 DSi9 New DSi Functions (RSA/SHA1)
  -    -    -    20h  20h  RSA_Init_crypto_heap
  -    -    -    21h  21h  RSA_Decrypt
  -    -    -    22h  22h  RSA_Decrypt_Unpad
  -    -    -    23h  23h  RSA_Decrypt_Unpad_OpenPGP_SHA1
  -    -    -    24h  24h  SHA1_Init
  -    -    -    25h  25h  SHA1_Update
  -    -    -    26h  26h  SHA1_Finish
  -    -    -    27h  27h  SHA1_Init_update_fin
  -    -    -    28h  28h  SHA1_Compare_20_bytes
  -    -    -    29h  29h  SHA1_Random_maybe
  GBA  NDS7 NDS9 DSi7 DSi9 Invalid Functions
  2Bh+ 20h+ 20h+ -    -    Crash (SWI xxh..FFh do jump to garbage addresses)
  -    xxh  xxh  -    -    Jump to 0   (on any SWI numbers not listed above)
  -    -    -    12h  12h  No function (ignored)
  -    -    -    2Bh  2Bh  No function (ignored)
  -    -    -    40h+ 40h+ Mirror      (SWI 40h..FFh mirror to 00h..3Fh)
  -    -    -    xxh  xxh  Hang        (on any SWI numbers not listed above)
```

Invalid NDS functions: NDS7 SWI 01h, 02h, 0Ah, 16h-19h, 1Eh, and NDS9 SWI 01h,
02h, 07h, 08h, 0Ah, 17h, 19h-1Eh will jump to zero (ie. to the NDS7 reset
vector, or to NDS9 unused (usually PU-locked ITCM) memory, which will be both
redirected to the debug handler, if any).

Invalid DSi functions: DSi9 SWI 00h, 07h-08h, 0Ah, 0Fh, 17h, 1Ah-1Eh, 2Ah,
2Ch-3Fh do hang in endless loop.

## <a name="biosdifferencesbetweengbaandndsfunctions"></a>  BIOS Differences between GBA and NDS functions

### Differences between GBA and NDS BIOS functions

- SoftReset uses different addresses

- SWI numbers for Halt, Stop/Sleep, Div, Sqrt have changed

- Halt destroys r0 on NDS9, IntrWait bugged on NDS9

- CpuFastSet allows 4-byte blocks (nice), but...

- CpuFastSet works very SLOW because of a programming bug (uncool)

- Some of the decompression functions are now using callbacks

- SoundBias uses new delay parameter

And, a number of GBA functions have been removed, and some new NDS functions
have been added, see:

- [BIOS Function Summary](#biosfunctionsummary)

## <a name="biosarithmeticfunctions"></a>  BIOS Arithmetic Functions

Div

DivArm

Sqrt

ArcTan

ArcTan2

### SWI 06h (GBA) or SWI 09h (NDS7/NDS9/DSi7/DSi9) - Div

Signed Division, r0/r1.

```
  r0  signed 32bit Number
  r1  signed 32bit Denom
```

Return:

```
  r0  Number DIV Denom ;signed
  r1  Number MOD Denom ;signed
  r3  ABS (Number DIV Denom) ;unsigned
```

For example, incoming -1234, 10 should return -123, -4, +123.

The function usually gets caught in an endless loop upon division by zero.

Note: The NDS9 and DSi9 additionally support hardware division, by math
coprocessor, accessed via I/O Ports, however, the SWI function is a raw
software division.

### SWI 07h (GBA) - DivArm

Same as above (SWI 06h Div), but incoming parameters are exchanged, r1/r0
(r0=Denom, r1=number). For compatibility with ARM's library. Slightly slower (3
clock cycles) than SWI 06h.

### SWI 08h (GBA) or SWI 0Dh (NDS7/NDS9/DSi7/DSi9) - Sqrt

Calculate square root.

```
  r0   unsigned 32bit number
```

Return:

```
  r0   unsigned 16bit number
```

The result is an integer value, so Sqrt(2) would return 1, to avoid this
inaccuracy, shift left incoming number by 2\*N as much as possible (the result
is then shifted left by 1\*N). Ie. Sqrt(2 shl 30) would return 1.41421 shl 15.

Note: The NDS9 and DSi9 additionally support hardware square root calculation,
by math coprocessor, accessed via I/O Ports, however, the SWI function is a raw
software calculation.

### SWI 09h (GBA) - ArcTan

Calculates the arc tangent.

```
  r0   Tan, 16bit (1bit sign, 1bit integral part, 14bit decimal part)
```

Return:

```
  r0   "-PI/2<THETA/<PI/2" in a range of C000h-4000h.
```

Note: there is a problem in accuracy with "THETA\<-PI/4, PI/4\<THETA".

### SWI 0Ah (GBA) - ArcTan2

Calculates the arc tangent after correction processing.

Use this in normal situations.

```
  r0   X, 16bit (1bit sign, 1bit integral part, 14bit decimal part)
  r1   Y, 16bit (1bit sign, 1bit integral part, 14bit decimal part)
```

Return:

```
  r0   0000h-FFFFh for 0<=THETA<2PI.
```

## <a name="biosrotationscalingfunctions"></a>  BIOS Rotation/Scaling Functions

BgAffineSet

ObjAffineSet

### SWI 0Eh (GBA) - BgAffineSet

Used to calculate BG Rotation/Scaling parameters.

```
  r0   Pointer to Source Data Field with entries as follows:
        s32  Original data's center X coordinate (8bit fractional portion)
        s32  Original data's center Y coordinate (8bit fractional portion)
        s16  Display's center X coordinate
        s16  Display's center Y coordinate
        s16  Scaling ratio in X direction (8bit fractional portion)
        s16  Scaling ratio in Y direction (8bit fractional portion)
        u16  Angle of rotation (8bit fractional portion) Effective Range 0-FFFF
  r1   Pointer to Destination Data Field with entries as follows:
        s16  Difference in X coordinate along same line
        s16  Difference in X coordinate along next line
        s16  Difference in Y coordinate along same line
        s16  Difference in Y coordinate along next line
        s32  Start X coordinate
        s32  Start Y coordinate
  r2   Number of Calculations
```

Return: No return value, Data written to destination address.

### SWI 0Fh (GBA) - ObjAffineSet

Calculates and sets the OBJ's affine parameters from the scaling ratio and
angle of rotation.

The affine parameters are calculated from the parameters set in Srcp.

The four affine parameters are set every Offset bytes, starting from the Destp
address.

If the Offset value is 2, the parameters are stored contiguously. If the value
is 8, they match the structure of OAM.

When Srcp is arrayed, the calculation can be performed continuously by
specifying Num.

```
  r0   Source Address, pointing to data structure as such:
        s16  Scaling ratio in X direction (8bit fractional portion)
        s16  Scaling ratio in Y direction (8bit fractional portion)
        u16  Angle of rotation (8bit fractional portion) Effective Range 0-FFFF
  r1   Destination Address, pointing to data structure as such:
        s16  Difference in X coordinate along same line
        s16  Difference in X coordinate along next line
        s16  Difference in Y coordinate along same line
        s16  Difference in Y coordinate along next line
  r2   Number of calculations
  r3   Offset in bytes for parameter addresses (2=continuous, 8=OAM)
```

Return: No return value, Data written to destination address.

For both Bg- and ObjAffineSet, Rotation angles are specified as 0-FFFFh
(covering a range of 360 degrees), however, the GBA BIOS recurses only the
upper 8bit; the lower 8bit may contain a fractional portion, but it is ignored
by the BIOS.

## <a name="biosdecompressionfunctions"></a>  BIOS Decompression Functions

BitUnPack

Diff8bitUnFilter

HuffUnComp

LZ77UnComp

RLUnComp

### Decompression Read/Write Variants

```
  ReadNormal:      Fast (src must be memory mapped)
  ReadByCallback:  Slow (src can be non-memory, eg. serial Firmware SPI bus)
  Write8bitUnits:  Fast (dest must support 8bit writes, eg. not VRAM)
  Write16bitUnits: Slow (dest must be halfword-aligned) (for VRAM)
```

### BitUnPack - SWI 10h (GBA/NDS7/NDS9/DSi7/DSi9)

Used to increase the color depth of bitmaps or tile data. For example, to
convert a 1bit monochrome font into 4bit or 8bit GBA tiles. The Unpack Info is
specified separately, allowing to convert the same source data into different
formats.

```
  r0  Source Address      (no alignment required)
  r1  Destination Address (must be 32bit-word aligned)
  r2  Pointer to UnPack information:
       16bit  Length of Source Data in bytes     (0-FFFFh)
       8bit   Width of Source Units in bits      (only 1,2,4,8 supported)
       8bit   Width of Destination Units in bits (only 1,2,4,8,16,32 supported)
       32bit  Data Offset (Bit 0-30), and Zero Data Flag (Bit 31)
      The Data Offset is always added to all non-zero source units.
      If the Zero Data Flag was set, it is also added to zero units.
```

Data is written in 32bit units, Destination can be Wram or Vram. The size of
unpacked data must be a multiple of 4 bytes. The width of source units (plus
the offset) should not exceed the destination width.

Return: No return value, Data written to destination address.

### Diff8bitUnFilterWrite8bit (Wram) - SWI 16h (GBA/NDS9/DSi9)

### Diff8bitUnFilterWrite16bit (Vram) - SWI 17h (GBA)

### Diff16bitUnFilter - SWI 18h (GBA/NDS9/DSi9)

These aren't actually real decompression functions, destination data will have
exactly the same size as source data. However, assume a bitmap or wave form to
contain a stream of increasing numbers such like 10..19, the
filtered/unfiltered data would be:

```
  unfiltered:   10  11  12  13  14  15  16  17  18  19
  filtered:     10  +1  +1  +1  +1  +1  +1  +1  +1  +1
```

In this case using filtered data (combined with actual compression algorithms)
will obviously produce better compression results.

Data units may be either 8bit or 16bit used with Diff8bit or Diff16bit
functions respectively.

```
  r0  Source address (must be aligned by 4) pointing to data as follows:
       Data Header (32bit)
         Bit 0-3   Data size (must be 1 for Diff8bit, 2 for Diff16bit)
         Bit 4-7   Type (must be 8 for DiffFiltered)
         Bit 8-31  24bit size after decompression
       Data Units (each 8bit or 16bit depending on used SWI function)
         Data0          ;original data
         Data1-Data0    ;difference data
         Data2-Data1    ;...
         Data3-Data2
         ...
  r1  Destination address
```

Return: No return value, Data written to destination address.

### HuffUnCompReadNormal - SWI 13h (GBA)

### HuffUnCompReadByCallback - SWI 13h (NDS/DSi)

The decoder starts in root node, the separate bits in the bitstream specify if
the next node is node0 or node1, if that node is a data node, then the data is
stored in memory, and the decoder is reset to the root node. The most often
used data should be as close to the root node as possible. For example, the
4-byte string "Huff" could be compressed to 6 bits: 10-11-0-0, with root.0
pointing directly to data "f", and root.1 pointing to a child node, whose nodes
point to data "H" and data "u".

Data is written in units of 32bits, if the size of the compressed data is not a
multiple of 4, please adjust it as much as possible by padding with 0.

Align the source address to a 4Byte boundary.

```
  r0  Source Address, aligned by 4, pointing to:
       Data Header (32bit)
         Bit0-3   Data size in bit units (normally 4 or 8)
         Bit4-7   Compressed type (must be 2 for Huffman)
         Bit8-31  24bit size of decompressed data in bytes
       Tree Size (8bit)
         Bit0-7   Size of Tree Table/2-1 (ie. Offset to Compressed Bitstream)
       Tree Table (list of 8bit nodes, starting with the root node)
        Root Node and Non-Data-Child Nodes are:
         Bit0-5   Offset to next child node,
                  Next child node0 is at (CurrentAddr AND NOT 1)+Offset*2+2
                  Next child node1 is at (CurrentAddr AND NOT 1)+Offset*2+2+1
         Bit6     Node1 End Flag (1=Next child node is data)
         Bit7     Node0 End Flag (1=Next child node is data)
        Data nodes are (when End Flag was set in parent node):
         Bit0-7   Data (upper bits should be zero if Data Size is less than 8)
       Compressed Bitstream (stored in units of 32bits)
         Bit0-31  Node Bits (Bit31=First Bit)  (0=Node0, 1=Node1)
  r1  Destination Address
  r2  Callback temp buffer      ;\for NDS/DSi "ReadByCallback" variants only
  r3  Callback structure        ;/(see Callback notes below)
```

Return: No return value, Data written to destination address.

### LZ77UnCompReadNormalWrite8bit (Wram) - SWI 11h (GBA/NDS7/NDS9/DSi7/DSi9)

### LZ77UnCompReadNormalWrite16bit (Vram) - SWI 12h (GBA)

### LZ77UnCompReadByCallbackWrite8bit - SWI 01h (DSi7/DSi9)

### LZ77UnCompReadByCallbackWrite16bit - SWI 12h (NDS), SWI 02h or 19h (DSi)

Expands LZ77-compressed data. The Wram function is faster, and writes in units
of 8bits. For the Vram function the destination must be halfword aligned, data
is written in units of 16bits.

CAUTION: Writing 16bit units to [dest-1] instead of 8bit units to [dest] means
that reading from [dest-1] won't work, ie. the "Vram" function works only with
disp=001h..FFFh, but not with disp=000h.

If the size of the compressed data is not a multiple of 4, please adjust it as
much as possible by padding with 0. Align the source address to a 4-Byte
boundary.

```
  r0  Source address, pointing to data as such:
       Data header (32bit)
         Bit 0-3   Reserved
         Bit 4-7   Compressed type (must be 1 for LZ77)
         Bit 8-31  Size of decompressed data
       Repeat below. Each Flag Byte followed by eight Blocks.
       Flag data (8bit)
         Bit 0-7   Type Flags for next 8 Blocks, MSB first
       Block Type 0 - Uncompressed - Copy 1 Byte from Source to Dest
         Bit 0-7   One data byte to be copied to dest
       Block Type 1 - Compressed - Copy N+3 Bytes from Dest-Disp-1 to Dest
         Bit 0-3   Disp MSBs
         Bit 4-7   Number of bytes to copy (minus 3)
         Bit 8-15  Disp LSBs
  r1  Destination address
  r2  Callback parameter        ;\for NDS/DSi "ReadByCallback" variants only
  r3  Callback structure        ;/(see Callback notes below)
```

Return: No return value.

### RLUnCompReadNormalWrite8bit (Wram) - SWI 14h (GBA/NDS7/NDS9/DSi7/DSi9)

### RLUnCompReadNormalWrite16bit (Vram) - SWI 15h (GBA)

### RLUnCompReadByCallbackWrite16bit - SWI 15h (NDS7/NDS9/DSi7/DSi9)

Expands run-length compressed data. The Wram function is faster, and writes in
units of 8bits. For the Vram function the destination must be halfword aligned,
data is written in units of 16bits.

If the size of the compressed data is not a multiple of 4, please adjust it as
much as possible by padding with 0. Align the source address to a 4Byte
boundary.

```
  r0  Source Address, pointing to data as such:
       Data header (32bit)
         Bit 0-3   Reserved
         Bit 4-7   Compressed type (must be 3 for run-length)
         Bit 8-31  Size of decompressed data
       Repeat below. Each Flag Byte followed by one or more Data Bytes.
       Flag data (8bit)
         Bit 0-6   Expanded Data Length (uncompressed N-1, compressed N-3)
         Bit 7     Flag (0=uncompressed, 1=compressed)
       Data Byte(s) - N uncompressed bytes, or 1 byte repeated N times
  r1  Destination Address
  r2  Callback parameter        ;\for NDS/DSi "ReadByCallback" variants only
  r3  Callback structure        ;/(see Callback notes below)
```

Return: No return value, Data written to destination address.

### NDS/DSi Decompression Callbacks

On NDS and DSi, the "ReadByCallback" variants are reading source data from
callback functions (rather than directly from memory). The callback functions
may read normal data from memory, or from other devices, such like directly
from the gamepak bus, without storing the source data in memory. The downside
is that the callback mechanism makes the function very slow, furthermore,
NDS7/NDS9 SWI 12h, 13h, 15h are using THUMB code, and variables on stack,
alltogether that makes the whole shit very-very-very slow.

```
  r2 = user defined callback parameter (passed on to Open function)
        (or, for Huffman: pointer to temp buffer, max 200h bytes needed)
  r3 = pointer to callback structure
```

Callback structure (five 32bit pointers to callback functions)

```
  Open_and_get_32bit (eg. LDR r0,[r0], get header)
  Close              (optional, 0=none)
  Get_8bit           (eg. LDRB r0,[r0])
  Get_16bit          (not used)
  Get_32bit          (used by Huffman only)
```

All functions may use ARM or THUMB code (indicated by address bit0). The
current source address (r0) is passed to all callback functions. Additionally,
the initial destination address (r1), and a user defined parameter (r2) are
passed to the Open function. For Huffman r2 must point to a temp buffer (max
200h bytes needed, internally used by the SWI function to make a copy of the
huffman tree; needed for random-access to the tree, which wouldn't work with
the sequentially reading callbacks).

All functions have return values in r0. The Open function normally returns the
first word (containing positive length and type), alternatively it may return a
negative error code to abort/reject decompression. The Close function, if it is
defined, should return zero (or any positive value), or a negative errorcode.
The other functions return raw data, without errorcodes. The SWI returns the
length of decompressed data, or the signed errorcode from the Open/Close
functions.

## <a name="biosmemorycopy"></a>  BIOS Memory Copy

CpuFastSet

CpuSet

### SWI 0Ch (GBA/NDS7/NDS9/DSi7/DSi9) - CpuFastSet

Memory copy/fill in units of 32 bytes. Memcopy is implemented as repeated
LDMIA/STMIA [Rb]!,r2-r9 instructions. Memfill as single LDR followed by
repeated STMIA [Rb]!,r2-r9.

After processing all 32-byte-blocks, the NDS/DSi additonally processes the
remaining words as 4-byte blocks. BUG: The NDS/DSi uses the fast 32-byte-block
processing only for the first N bytes (not for the first N words), so only the
first quarter of the memory block is FAST, the remaining three quarters are
SLOWLY copied word-by-word.

The length is specifed as wordcount, ie. the number of bytes divided by 4.

On the GBA, the length should be a multiple of 8 words (32 bytes) (otherwise
the GBA is forcefully rounding-up the length). On NDS/DSi, the length may be
any number of words (4 bytes).

```
  r0    Source address        (must be aligned by 4)
  r1    Destination address   (must be aligned by 4)
  r2    Length/Mode
          Bit 0-20  Wordcount (GBA: rounded-up to multiple of 8 words)
          Bit 24    Fixed Source Address (0=Copy, 1=Fill by WORD[r0])
```

Return: No return value, Data written to destination address.

### SWI 0Bh (GBA/NDS7/NDS9/DSi7/DSi9) - CpuSet

Memory copy/fill in units of 4 bytes or 2 bytes. Memcopy is implemented as
repeated LDMIA/STMIA [Rb]!,r3 or LDRH/STRH r3,[r0,r5] instructions. Memfill as
single LDMIA or LDRH followed by repeated STMIA [Rb]!,r3 or STRH r3,[r0,r5].

The length must be a multiple of 4 bytes (32bit mode) or 2 bytes (16bit mode).
The (half)wordcount in r2 must be length/4 (32bit mode) or length/2 (16bit
mode), ie. length in word/halfword units rather than byte units.

```
  r0    Source address        (must be aligned by 4 for 32bit, by 2 for 16bit)
  r1    Destination address   (must be aligned by 4 for 32bit, by 2 for 16bit)
  r2    Length/Mode
          Bit 0-20  Wordcount (for 32bit), or Halfwordcount (for 16bit)
          Bit 24    Fixed Source Address (0=Copy, 1=Fill by {HALF}WORD[r0])
          Bit 26    Datasize (0=16bit, 1=32bit)
```

Return: No return value, Data written to destination address.

Note: On GBA, NDS7 and DSi7, these two functions will silently reject to do
anything if the source start or end addresses are reaching into the BIOS area.
The NDS9 and DSi9 don't have such read-proctections.

## <a name="bioshaltfunctions"></a>  BIOS Halt Functions

Halt

IntrWait

VBlankIntrWait

Stop/Sleep

CustomHalt

### SWI 02h (GBA) or SWI 06h (NDS7/NDS9/DSi7/DSi9) - Halt

Halts the CPU until an interrupt request occurs. The CPU is switched into
low-power mode, all other circuits (video, sound, timers, serial, keypad,
system clock) are kept operating.

Halt mode is terminated when any enabled interrupts are requested, that is when
(IE AND IF) is not zero, the GBA locks up if that condition doesn't get true.
However, the state of CPUs IRQ disable bit in CPSR register, and the IME
register are don't care, Halt passes through even if either one has disabled
interrupts.

On GBA and NDS7/DSi7, Halt is implemented by writing to HALTCNT, Port 4000301h.
On NDS9/DSi9, Halt is implemted by writing to System Control Coprocessor (mov
p15,0,c7,c0,4,r0 opcode), this opcode hangs if IME=0.

No parameters, no return value.

(GBA/NDS7/DSi7: all registers unchanged, NDS9/DSi9: R0 destroyed)

### SWI 04h (GBA/NDS7/NDS9/DSi7/DSi9) - IntrWait ;DSi7/DSi9=bugged?

Continues to wait in Halt state until one (or more) of the specified
interrupt(s) do occur. The function forcefully sets IME=1. When using multiple
interrupts at the same time, this function is having less overhead than
repeatedly calling the Halt function.

```
  r0    0=Return immediately if an old flag was already set (NDS9: bugged!)
        1=Discard old flags, wait until a NEW flag becomes set
  r1    Interrupt flag(s) to wait for (same format as IE/IF registers)
  r2    DSi7 only: Extra flags (same format as DSi7's IE2/IF2 registers)
```

Caution: When using IntrWait or VBlankIntrWait, the user interrupt handler MUST
update the BIOS Interrupt Flags value in RAM; when acknowleding processed
interrupt(s) by writing a value to the IF register, the same value should be
also ORed to the BIOS Interrupt Flags value, at following memory location:

```
  Host     GBA (16bit)  NDS7 (32bit)  NDS9 (32bit)  DSi7-IF2 (32bit)
  Address  [3007FF8h]   [380FFF8h]    [DTCM+3FF8h]  [380FFC0h]
```

NDS9: BUG: No Discard (r0=0) doesn't work. The function always waits for at
least one IRQ to occur (no matter which, including IRQs that are not selected
in r1), even if the desired flag was already set. NB. the same bug is also
found in the GBA/NDS7 functions, but it's compensated by a second bug, ie. the
GBA/NDS7 functions are working okay because their "bug doesn't work".

Return: No return value, the selected flag(s) are automatically reset in BIOS
Interrupt Flags value in RAM upon return.

DSi9: BUG: The function tries to enter Halt state via Port 4000301h (which
would be okay on ARM7, but it's probably ignored on ARM9, which should normally
use CP15 to enter Halt state; if Port 4000301h is really ignored, then the
function will "successfully" wait for interrupts, but without actually entering
any kind of low power mode).

DSi7: BUG: The function tries to wait for IF and IF2 interrupts, but it does
accidently ignore the old IF interrupts, and works only with new IF2 ones.

### SWI 05h (GBA/NDS7/NDS9/DSi7/DSi9) - VBlankIntrWait ;DSi7/DSi9=bugged?

Continues to wait in Halt status until a new V-Blank interrupt occurs.

The function sets r0=1 and r1=1 (plus r2=0 on DSi7) and does then execute
IntrWait (SWI 04h), see IntrWait for details.

No parameters, no return value.

### SWI 03h (GBA) - Stop

Switches the GBA into very low power mode (to be used similar as a
screen-saver). The CPU, System Clock, Sound, Video, SIO-Shift Clock, DMAs, and
Timers are stopped.

Stop state can be terminated by the following interrupts only (as far as
enabled in IE register): Joypad, Game Pak, or General-Purpose-SIO.

"The system clock is stopped so the IF flag is not set."

Preparation for Stop:

Disable Video before implementing Stop (otherwise Video just freezes, but still
keeps consuming battery power). Possibly required to disable Sound also?
Obviously, it'd be also recommended to disable any external hardware (such like
Rumble or Infra-Red) as far as possible.

No parameters, no return value.

### SWI 07h (NDS7/DSi7) - Sleep

No info, probably similar as GBA SWI 03h (Stop). Sleep is implemented for ARM7
only, not for ARM9. But maybe the ARM7 function does stop \<both> ARM7 and
ARM9 (?)

### SWI 27h (GBA) or SWI 1Fh (NDS7/DSi7) - CustomHalt (Undocumented)

Writes the 8bit parameter value to HALTCNT, below values are equivalent to Halt
and Stop/Sleep functions, other values reserved, purpose unknown.

```
  r2  8bit parameter (GBA: 00h=Halt, 80h=Stop) (NDS7/DSi7: 80h=Halt, C0h=Sleep)
```

No return value.

## <a name="biosresetfunctions"></a>  BIOS Reset Functions

SoftReset

RegisterRamReset

HardReset

### SWI 00h (GBA/NDS7/NDS9) - SoftReset

Clears 200h bytes of RAM (containing stacks, and BIOS IRQ vector/flags),
initializes system, supervisor, and irq stack pointers, sets R0-R12, LR\_svc,
SPSR\_svc, LR\_irq, and SPSR\_irq to zero, and enters system mode.

Note that the NDS9 stack registers are hardcoded (the DTCM base should be set
to the default setting of 0800000h). The NDS9 function additionally flushes
caches and write buffer, and sets the CP15 control register to 12078h.

```
  Host  sp_svc    sp_irq    sp_sys    zerofilled area       return address
  GBA   3007FE0h  3007FA0h  3007F00h  [3007E00h..3007FFFh]  Flag[3007FFAh]
  NDS7  380FFDCh  380FFB0h  380FF00h  [380FE00h..380FFFFh]  Addr[27FFE34h]
  NDS9  0803FC0h  0803FA0h  0803EC0h  [DTCM+3E00h..3FFFh]   Addr[27FFE24h]
```

The NDS7/NDS9 return addresses at [27FFE34h/27FFE24h] are usually containing
copies of Cartridge Header [034h/024h] entry points, which may select ARM/THUMB
state via bit0. The GBA return address 8bit flag is interpreted as 00h=8000000h
(ROM), or 01h-FFh=2000000h (RAM), entered in ARM state.

Note: The reset is applied only to the CPU that has executed the SWI (ie. on
the NDS, the other CPU will remain unaffected).

Return: Does not return to calling procedure, instead, loads the above return
address into R14, and then jumps to that address by a "BX R14" opcode.

### SWI 01h (GBA) - RegisterRamReset

Resets the I/O registers and RAM specified in ResetFlags. However, it does not
clear the CPU internal RAM area from 3007E00h-3007FFFh.

```
  r0  ResetFlags
       Bit   Expl.
       0     Clear 256K on-board WRAM  ;-don't use when returning to WRAM
       1     Clear 32K on-chip WRAM    ;-excluding last 200h bytes
       2     Clear Palette
       3     Clear VRAM
       4     Clear OAM              ;-zerofilled! does NOT disable OBJs!
       5     Reset SIO registers    ;-switches to general purpose mode!
       6     Reset Sound registers
       7     Reset all other registers (except SIO, Sound)
```

Return: No return value.

Bug: LSBs of SIODATA32 are always destroyed, even if Bit5 of R0 was cleared.

The function always switches the screen into forced blank by setting
DISPCNT=0080h (regardless of incoming R0, screen becomes white).

### SWI 26h (GBA) - HardReset (Undocumented)

This function reboots the GBA (including for getting through the time-consuming
nintendo intro, which is making the function particularly useless and
annoying).

Parameters: None. Return: Never/Reboot.

Execution Time: About 2 seconds (!)

## <a name="biosmiscfunctions"></a>  BIOS Misc Functions

GetBiosChecksum

WaitByLoop

GetCRC16

IsDebugger

GetSineTable

GetPitchTable

GetVolumeTable

CustomPost

GetBootProcs

### SWI 0Dh (GBA) - GetBiosChecksum (Undocumented)

Calculates the checksum of the BIOS ROM (by reading in 32bit units, and adding
up these values). IRQ and FIQ are disabled during execution.

The checksum is BAAE187Fh (GBA and GBA SP), or BAAE1880h (DS in GBA mode,
whereas the only difference is that the byte at [3F0Ch] is changed from 00h to
01h, otherwise the BIOS is 1:1 same as GBA BIOS, it does even include multiboot
code).

Parameters: None. Return: r0=Checksum.

### SWI 03h (NDS7/NDS9/DSi7/DSi9) - WaitByLoop

Performs a "LOP: SUB R0,1 / BGT LOP" wait loop, the loop is executed in BIOS
memory, which provides reliable timings (regardless of the memory waitstates
& cache state of the calling procedure). Intended only for short delays
(eg. flash memory programming cycles).

```
  r0  Delay value (should be in range 1..7FFFFFFFh)
```

Execution time varies for ARM7 vs ARM9. On ARM9 it does also depend on whether
ROM is cached, and on DSi it does further depended on the ARM9 CPU clock, and
on whether using NDS or DSi BIOS ROM (NDS uses faster THUMB code, whilst DSi
uses ARM code, which is slow on uncached ARM9 ROM reads). For example, to get a
1 millisecond delay, use following values:

```
  CPU  Clock     Cache   BIOS     Value for 1ms
  ARM7 33.51MHz  none    NDS/DSi  r0=20BAh    ;=20BAh  ;-ARM7
  ARM9 67.03MHz  on      NDS/DSi  r0=20BAh*2  ;=4174h  ;\ARM9 with cache
  ARM9 134.06MHz on      DSi      r0=20BAh*4  ;=82E8h  ;/
  ARM9 67.03MHz  off     NDS      r0=20BAh/2  ;=105Dh  ;\
  ARM9 67.03MHz  off     DSi      r0=20BAh/4  ;=082Eh  ; ARM9 without cache
  ARM9 134.06MHz off     DSi      r0=20BAh/3  ;=0AE8h  ;/
```

Return: No return value.

### SWI 0Eh (NDS7/NDS9/DSi7/DSi9) - GetCRC16

```
  r0  Initial CRC value (16bit, usually FFFFh)
  r1  Start Address   (must be aligned by 2)
  r2  Length in bytes (must be aligned by 2)
```

CRC16 checksums can be calculated as such:

```
  val[0..7] = C0C1h,C181h,C301h,C601h,CC01h,D801h,F001h,A001h
  for i=start to end
    crc=crc xor byte[i]
    for j=0 to 7
      crc=crc shr 1:if carry then crc=crc xor (val[j] shl (7-j))
    next j
  next i
```

Return:

```
  r0  Calculated 16bit CRC Value
```

Additionally, if the length is nonzero, r3 contains the last processed halfword
at [addr+len-2]. Unlike most other NDS7/DSi7 SWI functions (which do reject
reading from BIOS memory), this allows to dump the NDS7/DSi7 BIOS (except for
the memory region that is locked via BIOSPROT Port 4000308h).

### SWI 0Fh (NDS7/NDS9) - IsDebugger

Detects if 4MB (normal) or 8MB (debug version) Main RAM installed.

Caution: Fails on ARM9 when cache is enabled (always returns 8MB state).

Return: r0 = result (0=normal console 4MB, 1=debug version 8MB)

Destroys halfword at [27FFFFAh] (NDS7) or [27FFFF8h] (NDS9)!

The SWI 0Fh function doesn't work stable if it gets interrupted by an interrupt
which is calling SWI 0Fh, which would destroy the above halfword scratch value
(unless the IRQ handler has saved/restored the halfword).

### SWI 1Ah (NDS7/DSi7) - GetSineTable

```
  r0  Index (0..3Fh) (must be in that range, otherwise returns garbage)
```

Return: r0 = Desired Entry (0000h..7FF5h) ;SIN(0 .. 88.6 degrees)\*8000h

### SWI 1Bh (NDS7/DSi7) - GetPitchTable (DSi7: bugged)

```
  r0  Index (0..2FFh) (must be in that range, otherwise returns garbage)
```

BUG: DSi7 accidently reads from SineTable instead of PitchTable, as workaround
for obtaining PitchTable values, one can set "r0=(0..2FFh)-46Ah" on DSi.

Return: r0 = Desired Entry (0000h..FF8Ah) (unsigned)

### SWI 1Ch (NDS7/DSi7) - GetVolumeTable

```
  r0  Index (0..2D3h) (must be in that range, otherwise returns garbage)
```

Return: r0 = Desired Entry (00h..7Fh) (unsigned)

### SWI 1Fh (NDS9/DSi9) - CustomPost

Writes to the POSTFLG register, probably for use by Firmware boot procedure.

```
  r0  32bit value, to be written to POSTFLG, Port 4000300h
```

Return: No return value.

### SWI 1Dh (NDS7/DSi7) - GetBootProcs

Returns addresses of Gamecart boot procedure/interrupt handler, probably for
use by Firmware boot procedure. Most of the returned NDS7 functions won't work
if the POSTFLG register is set.

The return values are somewhat XORed by each other (on DSi7 most of the values
are zero; which does rather negate the XORing effect, and, as a special
gimmick, one of the zero values is XORed by incoming r2).

## <a name="biosmultibootsinglegamepak"></a>  BIOS Multi Boot (Single Game Pak)

MultiBoot

### SWI 25h (GBA) - MultiBoot

This function uploads & starts program code to slave GBAs, allowing to
launch programs on slave units even if no cartridge is inserted into the slaves
(this works because all GBA BIOSes contain built-in download procedures in
ROM).

However, the SWI 25h BIOS upload function covers only 45% of the required
Transmission Protocol, the other 55% must be coded in the master cartridge (see
Transmission Protocol below).

```
  r0  Pointer to MultiBootParam structure
  r1  Transfer Mode (undocumented)
       0=256KHz, 32bit, Normal mode    (fast and stable)
       1=115KHz, 16bit, MultiPlay mode (default, slow, up to three slaves)
       2=2MHz,   32bit, Normal mode    (fastest but maybe unstable)
  Note: HLL-programmers that are using the MultiBoot(param_ptr) macro cannot
  specify the transfer mode and will be forcefully using MultiPlay mode.
```

Return:

```
  r0  0=okay, 1=failed
```

See below for more details.

### Multiboot Parameter Structure

Size of parameter structure should be 4Ch bytes (the current GBA BIOS uses only
first 44h bytes though). The following entries must be set before calling SWI
25h:

```
  Addr Size Name/Expl.
  14h  1    handshake_data (entry used for normal mode only)
  19h  3    client_data[1,2,3]
  1Ch  1    palette_data
  1Eh  1    client_bit (Bit 1-3 set if child 1-3 detected)
  20h  4    boot_srcp  (typically 8000000h+0C0h)
  24h  4    boot_endp  (typically 8000000h+0C0h+length)
```

The transfer length (excluding header data) should be a multiple of 10h,
minimum length 100h, max 3FF40h (ca. 256KBytes). Set palette\_data as
"81h+color\*10h+direction\*8+speed\*2", or as "0f1h+color\*2" for fixed palette,
whereas color=0..6, speed=0..3, direction=0..1. The other entries
(handshake\_data, client\_data[1-3], and client\_bit) must be same as specified in
Transmission Protocol (see below hh,cc,y).

### Multiboot Transfer Protocol

Below describes the complete transfer protocol, normally only the Initiation
part must be programmed in the master cartridge, the main data transfer can be
then performed by calling SWI 25h, the slave program is started after SWI 25h
completion.

The ending handshake is normally not required, when using it, note that you
will need custom code in BOTH master and slave programs.

```
  Times  Send   Receive  Expl.
  -----------------------Required Transfer Initiation in master program
  ...    6200   FFFF     Slave not in multiplay/normal mode yet
  1      6200   0000     Slave entered correct mode now
  15     6200   720x     Repeat 15 times, if failed: delay 1/16s and restart
  1      610y   720x     Recognition okay, exchange master/slave info
  60h    xxxx   NN0x     Transfer C0h bytes header data in units of 16bits
  1      6200   000x     Transfer of header data completed
  1      620y   720x     Exchange master/slave info again
  ...    63pp   720x     Wait until all slaves reply 73cc instead 720x
  1      63pp   73cc     Send palette_data and receive client_data[1-3]
  1      64hh   73uu     Send handshake_data for final transfer completion
  -----------------------Below is SWI 25h MultiBoot handler in BIOS
  DELAY  -      -        Wait 1/16 seconds at master side
  1      llll   73rr     Send length information and receive random data[1-3]
  LEN    yyyy   nnnn     Transfer main data block in units of 16 or 32 bits
  1      0065   nnnn     Transfer of main data block completed, request CRC
  ...    0065   0074     Wait until all slaves reply 0075 instead 0074
  1      0065   0075     All slaves ready for CRC transfer
  1      0066   0075     Signalize that transfer of CRC follows
  1      zzzz   zzzz     Exchange CRC must be same for master and slaves
  -----------------------Optional Handshake (NOT part of master/slave BIOS)
  ...    ....   ....     Exchange whatever custom data
```

Legend for above Protocol

```
  y     client_bit, bit(s) 1-3 set if slave(s) 1-3 detected
  x     bit 1,2,or 3 set if slave 1,2,or 3
  xxxx  header data, transferred in 16bit (!) units (even in 32bit normal mode)
  nn    response value for header transfer, decreasing 60h..01h
  pp    palette_data
  cc    random client_data[1..3] from slave 1-3, FFh if slave not exists
  hh    handshake_data, 11h+client_data[1]+client_data[2]+client_data[3]
  uu    random data, not used, ignore this value
```

Below automatically calculated by SWI 25h BIOS function (don't care about)

```
  llll  download length/4-34h
  rr    random data from each slave for encryption, FFh if slave not exists
  yyyy  encoded data in 16bit (multiplay) or 32bit (normal mode) units
  nnnn  response value, lower 16bit of destadr in GBA memory (00C0h and up)
  zzzz  16bit download CRC value, must be same for master and slaves
```

Pseudo Code for SWI 25h Transfer with Checksum and Encryption calculations

```
  if normal_mode    then c=C387h:x=C37Bh:k=43202F2Fh
  if multiplay_mode then c=FFF8h:x=A517h:k=6465646Fh
  m=dword(pp,cc,cc,cc):f=dword(hh,rr,rr,rr)
  for ptr=000000C0h to (file_size-4) step 4
    c=c xor data[ptr]:for i=1 to 32:c=c shr 1:if carry then c=c xor x:next
    m=(6F646573h*m)+1
    send_32_or_2x16 (data[ptr] xor (-2000000h-ptr) xor m xor k)
  next
  c=c xor f:for i=1 to 32:c=c shr 1:if carry then c=c xor x:next
  wait_all_units_ready_for_checksum:send_32_or_1x16 (c)
```

Whereas, explained: c=chksum,x=chkxor,f=chkfin,k=keyxor,m=keymul

### Multiboot Communication

In Multiplay mode, master sends 16bit data, and receives 16bit data from each
slave (or FFFFh if none). In Normal mode, master sends 32bit data (upper 16bit
zero, lower 16bit as for multiplay mode), and receives 32bit data (upper 16bit
as for multiplay mode, and lower 16bit same as lower 16bit previously sent by
master). Because SIODATA32 occupies same addresses as SIOMULTI0-1, the same
transfer code can be used for both multiplay and normal mode (in normal mode
SIOMULTI2-3 should be forced to FFFFh though). After each transfer, master
should wait for Start bit cleared in SIOCNT register, followed by a 36us delay.

Note: The multiboot slave would also recognize data being sent in Joybus mode,
however, master GBAs cannot use joybus mode (because GBA hardware cannot act as
master in joybus mode).

### Multiboot Slave Header

The transferred Header block is written to 2000000-20000BFh in slave RAM, the
header must contain valid data (identically as for normal ROM-cartridge
headers, including a copy of the Nintendo logo, correct header CRC, etc.), in
most cases it'd be recommended just to transfer a copy of the master cartridges
header from 8000000h-80000BFh.

### Multiboot Slave Program/Data

The transferred main program/data block is written to 20000C0h and up (max
203FFFFh) in slave RAM, note that absolute addresses in the program must be
then originated at 2000000h rather than 8000000h. In case that the master
cartridge is 256K or less, it could just transfer a copy of the whole cartridge
at 80000C0h and up, the master should then copy & execute its own ROM data
into RAM as well.

### Multiboot Slave Extended Header

For Multiboot slaves, separate Entry Point(s) must be defined at the beginning
of the Program/Data block (the Entry Point in the normal header is ignored),
also some reserved bytes in this section are overwritten by the Multiboot
procedure. For more information see chapter about Cartridge Header.

### Multiboot Slave with Cartridge

Beside for slaves without cartridge, multiboot can be also used for slaves
which do have a cartridge inserted, if so, SELECT and START must be kept held
down during power-on in order to switch the slave GBA into Multiboot mode (ie.
to prevent it from starting the cartridge as normally).

The general idea is to enable newer programs to link to any existing older GBA
programs, even if these older programs originally didn't have been intended to
support linking.

The uploaded program may access the slaves SRAM, Flash ROM, or EEPROM (if any,
allowing to read out or modify slave game positions), as well as cartridge ROM
at 80000A0h-8000FFFh (the first 4KBytes, excluding the nintendo logo, allowing
to read out the cartridge name from the header, for example).

The main part of the cartridge ROM is meant to be locked out in order to
prevent software pirates from uploading "intruder" programs which would send
back a copy of the whole cartridge to the master, however, for good or evil, at
present time, current GBA models and GBA carts do not seem to contain any such
protection.

### Uploading Programs from PC

Beside for the ability to upload a program from one GBA to another, this
feature can be also used to upload small programs from a PC to a GBA. For more
information see chapter about External Connectors.

### Nintendo DS

The GBA multiboot function requires a link port, and so, works on GBA and GBA
SP only. The Nintendo DS in GBA mode does include the multiboot BIOS function,
but it won't be of any use as the DS doesn't have a link port.

## <a name="biossoundfunctions"></a>  BIOS Sound Functions

MidiKey2Freq

SoundBias

SoundChannelClear

SoundDriverInit

SoundDriverMain

SoundDriverMode

SoundDriverVSync

SoundDriverVSyncOff

SoundDriverVSyncOn

SoundWhatever0..4

SoundGetJumpList

### SWI 1Fh (GBA) - MidiKey2Freq

Calculates the value of the assignment to ((SoundArea)sa).vchn[x].fr when
playing the wave data, wa, with the interval (MIDI KEY) mk and the fine
adjustment value (halftones=256) fp.

```
  r0  WaveData* wa
  r1  u8 mk
  r2  u8 fp
```

Return:

```
  r0  u32
```

This function is particularly popular because it allows to read from BIOS
memory without copy protection range checks. The formula to read one byte (a)
from address (i, 0..3FFF) is:

a = (MidiKey2Freq(i-(((i AND 3)+1)OR 3), 168, 0) \* 2) SHR 24

### SWI 19h (GBA) or SWI 08h (NDS7/DSi7) - SoundBias

Increments or decrements the current level of the SOUNDBIAS register (with
short delays) until reaching the desired new level. The upper bits of the
register are kept unchanged.

```
  r0   BIAS level (0=Level 000h, any other value=Level 200h)
  r1   Delay Count (NDS/DSi only) (GBA uses a fixed delay count of 8)
```

Return: No return value.

### SWI 1Eh (GBA) - SoundChannelClear

Clears all direct sound channels and stops the sound.

This function may not operate properly when the library which expands the sound
driver feature is combined afterwards. In this case, do not use it.

No parameters, no return value.

### SWI 1Ah (GBA) - SoundDriverInit

Initializes the sound driver. Call this only once when the game starts up.

It is essential that the work area already be secured at the time this function
is called.

You cannot execute this driver multiple times, even if separate work areas have
been prepared.

```
  r0  Pointer to work area for sound driver, SoundArea structure as follows:
       SoundArea (sa) Structure
        u32    ident      Flag the system checks to see whether the
                          work area has been initialized and whether it
                          is currently being accessed.
        vu8    DmaCount   User access prohibited
        u8     reverb     Variable for applying reverb effects to direct sound
        u16    d1         User access prohibited
        void   (*func)()  User access prohibited
        int    intp       User access prohibited
        void*  NoUse      User access prohibited
        SndCh  vchn[MAX]  The structure array for controlling the direct
                          sound channels (currently 8 channels are
                          available). The term "channel" here does
                          not refer to hardware channels, but rather to
                          virtual constructs inside the sound driver.
        s8     pcmbuf[PCM_BF*2]
       SoundChannel Structure
        u8         sf     The flag indicating the status of this channel.
                          When 0 sound is stopped.
                          To start sound, set other parameters and
                          then write 80h to here.
                          To stop sound, logical OR 40h for a
                          release-attached off (key-off), or write zero
                          for a pause. The use of other bits is
                          prohibited.
        u8         r1     User access prohibited
        u8         rv     Sound volume output to right side
        u8         lv     Sound volume output to left side
        u8         at     The attack value of the envelope. When the
                          sound starts, the volume begins at zero and
                          increases every 1/60 second. When it
                          reaches 255, the process moves on to the
                          next decay value.
        u8         de     The decay value of the envelope. It is
                          multiplied by "this value/256" every 1/60
                          sec. and when sustain value is reached, the
                          process moves to the sustain condition.
        u8         su     The sustain value of the envelope. The
                          sound is sustained by this amount.
                          (Actually, multiplied by rv/256, lv/256 and
                          output left and right.)
        u8         re     The release value of the envelope. Key-off
                          (logical OR 40h in sf) to enter this state.
                          The value is multiplied by "this value/256"
                          every 1/60 sec. and when it reaches zero,
                          this channel is completely stopped.
        u8         r2[4]  User access prohibited
        u32        fr     The frequency of the produced sound.
                          Write the value obtained with the
                          MidiKey2Freq function here.
        WaveData*  wp     Pointer to the sound's waveform data. The waveform
                          data can be generated automatically from the AIFF
                          file using the tool (aif2agb.exe), so users normally
                          do not need to create this themselves.
        u32        r3[6]  User access prohibited
        u8         r4[4]  User access prohibited
       WaveData Structure
        u16   type    Indicates the data type. This is currently not used.
        u16   stat    At the present time, non-looped (1 shot) waveform
                      is 0000h and forward loop is 4000h.
        u32   freq    This value is used to calculate the frequency.
                      It is obtained using the following formula:
                      sampling rate x 2^((180-original MIDI key)/12)
        u32   loop    Loop pointer (start of loop)
        u32   size    Number of samples (end position)
        s8    data[]  The actual waveform data. Takes (number of samples+1)
                      bytes of 8bit signed linear uncompressed data. The last
                      byte is zero for a non-looped waveform, and the same
                      value as the loop pointer data for a looped waveform.
```

Return: No return value.

### SWI 1Ch (GBA) - SoundDriverMain

Main of the sound driver.

Call every 1/60 of a second. The flow of the process is to call
SoundDriverVSync, which is explained later, immediately after the V-Blank
interrupt.

After that, this routine is called after BG and OBJ processing is executed.

No parameters, no return value.

### SWI 1Bh (GBA) - SoundDriverMode

Sets the sound driver operation mode.

```
  r0  Sound driver operation mode
       Bit    Expl.
       0-6    Direct Sound Reverb value (0-127, default=0) (ignored if Bit7=0)
       7      Direct Sound Reverb set (0=ignore, 1=apply reverb value)
       8-11   Direct Sound Simultaneously-produced (1-12 channels, default 8)
       12-15  Direct Sound Master volume (1-15, default 15)
       16-19  Direct Sound Playback Frequency (1-12 = 5734,7884,10512,13379,
              15768,18157,21024,26758,31536,36314,40137,42048, def 4=13379 Hz)
       20-23  Final number of D/A converter bits (8-11 = 9-6bits, def. 9=8bits)
       24-31  Not used.
```

Return: No return value.

### SWI 1Dh (GBA) - SoundDriverVSync

An extremely short system call that resets the sound DMA. The timing is
extremely critical, so call this function immediately after the V-Blank
interrupt every 1/60 second.

No parameters, no return value.

### SWI 28h (GBA) - SoundDriverVSyncOff

Due to problems with the main program if the V-Blank interrupts are stopped,
and SoundDriverVSync cannot be called every 1/60 a second, this function must
be used to stop sound DMA.

Otherwise, even if you exceed the limit of the buffer the DMA will not stop and
noise will result.

No parameters, no return value.

### SWI 29h (GBA) - SoundDriverVSyncOn

This function restarts the sound DMA stopped with the previously described
SoundDriverVSyncOff.

After calling this function, have a V-Blank occur within 2/60 of a second and
call SoundDriverVSync.

No parameters, no return value.

### SWI 20h..24h (GBA) - SoundWhatever0..4 (Undocumented)

Whatever undocumented sound-related BIOS functions.

### SWI 2Ah (GBA) - SoundGetJumpList (Undocumented)

Receives pointers to 36 additional sound-related BIOS functions.

```
  r0  Destination address (must be aligned by 4) (120h bytes buffer)
```



## <a name="biosrsabasics"></a>  BIOS RSA Basics

### RSA Basics

The RSA formulas are quite simple: Applying an exponent and modulus to the
source data. There are two formulas used for encryption/decryption. The first
formula requires only the Public Key (and an exponent, which is usually some
fixed constant; on the DSi it's always 10001h aka 65537 decimal). The second
formula is almost same, but requires the Private Key instead of the constant
exponent (and also requires the Public Key as modulus):

```
  Public Key formula:      dest = src^10001h mod pubkey
  Private Key formula:     dest = src^prvkey mod pubkey
```

That formulas can be used for encrypting secret messages, as so:

```
  Recipient's Public Key   --> Encrypt a message
  Recipient's Private Key  --> Decrypt a message
```

Or, using the formulas the other way around, to create digital signatures:

```
  Sender's Private Key     --> Encrypt/create a signature
  Sender's Public Key      --> Decrypt/verify a signature
```

The overall idea is that only the owner of the Private Key can decrypt
messages, or create signatures. The Public Key can be shared freely, so that
everybody can encrypt messages, or verify signatures.

### RSA Big Number Maths

The exponent/modulus can be implemented with simple unsigned multiply/divide
operations. However, RSA requires dealing with big 1024bit integers (or even
bigger numbers when using larger keys), this does usually require some software
functions since regular CPUs cannot directly deal with such large numbers.

### RSA Byte Order

The DSi is storing all RSA keys and signatures in Big-Endian format, so one
will need to reverse the byte order before doing the actual maths on
Little-Endian CPUs.

### RSA Signatures (used on DSi)

Digital signatures can be used for signing documents or other binaries. The
signature does usually consist of a secure checksum (SHA-1, MD5, SHA256, etc.)
computed on the document/binary, and then encrypted via the RSA Private Key
formula.

The checksum can be then decrypted via Public Key, if the decrypted checksum
does match up, then one can be sure that the document/binary hasn't been
modified, and that it was really created by the Private Key owner.

### RSA Encrypted Messages (not used on DSi)

Encrypted RSA messages are restricted to the size of the Public Key (eg. with a
1024bit key, the message should be smaller than 128 bytes). For bigger
messages, one could either split the message into smaller snippets, or, one
could combine RSA with some other encryption mechanism (eg. store an AES key in
the RSA message, and decrypt the actual document via AES; that would add
private/public key security to AES).

### RSA Padding

RSA can be weak if the message is a small number (especially very small values
like "0" or "1" obviously wouldn't work well with the "msg^exp" maths; other
small values can be also weak, eg. with the common/small public exponent
10001h). To avoid that problem, the MSBs of the message should be padded with
nonzero bytes, typically as so (as defined in RFC 2313):

```
  00h   1      "00" Leading zero (00h)
  01h   1      "BT" Block type (always 01h on DSi)
  02h   8+n    "PS" Padding (FFh-filled, min 8 bytes, usually 69h bytes on DSi)
  0Ah+n 1      "00" Padding end (00h)
  0Bh+n 75h-n  "D"  Data (max 75h bytes, usually a 14h-byte SHA1 value on DSi)
```

That, for 80h-byte messages. For other sizes replace "75h" by "F5h, 1F5h, etc."

### RSA Key Generation

Generating a RSA key pair is more difficult than the encryption/decryption
part. First of, one needs two unsigned random prime numbers; for a 1024bit key,
that would be usually two large 512bit prime numbers (whereas, finding real
prime numbers is complicated, and it's more common to use values that have a
"high probability" of being prime numbers).

The public key is then simply generated by multiplying the two prime numbers (P
and Q) with each other:

```
  pubkey = P * Q
```

The private key is also based on the same prime numbers, but the maths there
are more complicated (and not described here).

When knowing one prime number, one could theoretically compute the other as
"Q=pubkey/P", however, prime numbers aren't as rare as one might think, and
it's quite impossible to guess (or brute-force) one of the prime numbers.

## <a name="biosrsapseudocode"></a>  BIOS RSA Pseudo Code

### rsa\_mpi\_pow\_mod(dst,src,pubkey,exp,num\_exp\_bits) ;[dst]=[src]^[exp] mod [key]

```
  base(rsa__number_size), bigbuf(rsa_number_size*2)
  [base]=[src], [dst]=1, pow8bit=01h                ;-init base, result, powbit
  for i=1 to num_exp_bits
    if [exp] AND pow8bit then rsa_mpi_mul_mod(dst,base)   ;-mul result
    rsa_mpi_mul_mod(base,base)                            ;-square base
    pow8bit=pow8bit ROL 1, exp=exp+carry                  ;-next exp bit
  next i
  return
```

This is the RSA main function. The exponent is applied by squaring the "src"
several times, and, if the corresponding exponent bit is set, multiplying the
result by the squared value. To avoid the numbers to become incredible large,
the modulus is applied after each multiplication (rather than applying it only
on the final result).

```
  For the Private Key formula: Use exp=prvkey, num_exp_bits=rsa_number_size*8
  For the Public Key formula:  Use exp=ptr_to_10001h, num_exp_bits=17
```

The parameters and result for "rsa\_mpi\_pow\_mod" must be in little-endian. Ie.
for DSi, reverse byte the byte order of the incoming/outgoing values. And, on
DSi, use rsa\_number\_size=80h (aka 128 bytes, aka for 1024bit RSA).

### rsa\_mpi\_mul\_mod(dst,src):

```
  rsa_mpi_mul(bigbuf,dst,src)           ;-multiply
  rsa_mpi_mod(bigbuf,pubkey)            ;-modulus
  [dst]=[bigbuf+0..rsa_number_size-1]   ;-copy to dst
  return
```

### rsa\_mpi\_mul(dst,src1,src2):    ;[dst]=[src1]\*[src2]

```
  [dst+0]=0, oldmsw=0                   ;-init first word and oldmsw
  for i=0 to rsa_number_size-4 step 4   ;\
    call @@inner_loop                   ; compute LSWs of destination
    src2=src2+4                         ;
  next i                                ;/
  src2=src2-4
  for i=rsa_number_size-8 to 0 step -4  ;\
    src1=src1+4                         ; compute MSWs of destination
    call @@inner_loop                   ;
  next i                                ;/
  return
 ;---
 @@inner_loop:
  [dst+4]=oldmsw, oldmsw=0
  for j=0 to i step 4
    msw:lsw = [src1+j]*[src2-j]
    [dst+0]=[dst+0]+lsw
    [dst+4]=[dst+4]+msw+cy
    oldmsw=oldmsw+cy
  next j
  dst=dst+4
  ret
```

### rsa\_mpi\_mod(dst,src):   ;[dst]=[dst] mod [src]  ;aka division remainder

;Double/Single -> Single modulo division (mpi/mpi)

;Divisor's MSW must be >= 80000000h

```
  ebx=rsa_number_size, dst=dst+ebx, i=ebx+4
 @@type0_lop:                                                           ;\
  if [dst+ebx-4]=0 then goto @@type0_next                               ;
  rsa_mpi_cmp(dst,src), if borrow then goto @@type1_next                ; type0
  rsa_mpi_sub(dst,src), if [dst+ebx-4]<>0 then goto @@type1_next        ; loop
 @@type0_next:                                                          ;
  dst=dst-4, i=i-4, if i>0 then goto @@type0_lop                        ;/
  goto @@done
 ;--- --- ---
 @@type1_lop:                                                           ;\
  lsw=[dst+ebx-4], msw=[dst+ebx-0]                                      ;
  if msw>=[src+ebx-4] then fac=FFFFFFFFh else fac=msw:lsw / [src+ebx-4] ;
  rsa_mpi_mulsub(dst,src,fac), if carry=0 then goto @@skip_add          ; type1
 @@add_more:                                                            ; loop
  rsa_mpi_add(dst,src)                                                  ;
  [dst+ebx]=[dst+ebx]+carry, if carry=0 then goto @@add_more            ;
 @@skip_add:                                                            ;
  if [dst+ebx-4]=0 then goto @@type0_next                               ;
 @@type1_next:                                                          ;
  dst=dst-4, i=i-4, if i>0 then goto @@type1_lop                        ;/
 @@done:
  return
```

### rsa\_mpi\_mulsub(dst,src,fac):    ;[dst]=[dst]-[src]\*fac

```
  oldborrow=0, oldmsw=0                                       ;\
  for i=0 to rsa_number_size-4 step 4                         ; process
    msw:lsw = [src+i]*fac, lsw=lsw+oldmsw, oldmsw=msw+carry   ; rsa_number_size
    [dst+i]=[dst+i]-lsw-oldborrow, oldborrow=borrow           ; bytes, plus...
  next i                                                      ;/
  [dst+rsa_number_size]=[dst+rsa_number_size]-oldmsw-oldborrow ;-one extra word
  return borrow   ;(unlike "rsa_embedded" which returns INVERTED borrow)
```

### rsa\_mpi\_add(dst,src):   ;out: [dst]=[dst]+[src], carry

```
  carry = 0
  for i=0 to rsa_number_size-4 step 4
    [dst+i]=[dst+i]+[src+i]+carry
  next i
  return carry
```

### rsa\_mpi\_sub(dst,src):   ;out: [dst]=[dst]-[src], borrow/unused

```
  borrow = 0
  for i=0 to rsa_number_size-4 step 4
    [dst+i]=[dst+i]-[src+i]-borrow
  next i
  return borrow
```

### rsa\_mpi\_cmp[dst,src]:   ;compare [dst]-[src], out: borrow

```
  for i=rsa_number_size-4 to 0 step -4
    temp=[dst+i]-[src+i], if not equal then return borrow
  next i
  return borrow
```

This is about same as "sub", but faster (because it can abort the loop upon
first difference).

## <a name="biosramusage"></a>  BIOS RAM Usage

Below contains info about RAM contents at cartridge boot time (as initialized
by the BIOS/Firmware), plus info about RAM locations used by IRQ handlers and
SWI functions.

### GBA BIOS RAM Usage

Below memory at 3007Fxxh is often accessed directly, or via mirrors at
3FFFFxxh.

```
  3000000h 7F00h User Memory and User Stack              (sp_usr=3007F00h)
  3007F00h A0h   Default Interrupt Stack (6 words/time)  (sp_irq=3007FA0h)
  3007FA0h 40h   Default Supervisor Stack (4 words/time) (sp_svc=3007FE0h)
  3007FE0h 10h   Debug Exception Stack (4 words/time)    (sp_xxx=3007FF0h)
  3007FF0h 4     Pointer to Sound Buffer (for SWI Sound functions)
  3007FF4h 3     Reserved (unused)
  3007FF7h 1     Reserved (intro/nintendo logo related)
  3007FF8h 2     IRQ IF Check Flags (for SWI IntrWait/VBlankIntrWait functions)
  3007FFAh 1     Soft Reset Re-entry Flag (for SWI SoftReset function)
  3007FFBh 1     Reserved (intro/multiboot slave related)
  3007FFCh 4     Pointer to user IRQ handler (to 32bit ARM code)
```

### NDS BIOS RAM Usage

Below memory at 27FFxxxh is mirrored to 23FFxxxh (on retail consoles with 4MB
RAM), however, it should be accessed via address 27FFxxxh (for compatibility
with debug consoles with 8MB RAM). Accessing it via mirrors at 2FFFxxxh is also
valid (this is done by DSi enhanced games; even when running in non-DSi mode;
this allows DSi games to use the same memory addresses in NDS and DSi mode).

```
  2000000h ...   ARM7 and ARM9 bootcode can be loaded here (2000000h..23BFDFFh)
  2400000h ...   Debug bootcode can be loaded here (2400000h..27BFDFFh)
  23FEE00h 168h  Fragments of NDS9 firmware boot code
  27FF800h 4     NDS Gamecart Chip ID 1
  27FF804h 4     NDS Gamecart Chip ID 2
  27FF808h 2     NDS Cart Header CRC (verified)            ;hdr[15Eh]
  27FF80Ah 2     NDS Cart Secure Area CRC (not verified ?) ;hdr[06Ch]
  27FF80Ch 2     NDS Cart Missing/Bad CRC (0=Okay, 1=Missing/Bad)
  27FF80Eh 2     NDS Cart Secure Area Bad (0=Okay, 1=Bad)
  27FF810h 2     Boot handler task number (usually FFFFh at cart boot time)
  27FF812h 2     Secure disable (0=Normal, 1=Disable; Cart[078h]=BIOS[1088h])
  27FF814h 2     SIO Debug Connection Exists (0=No, 1=Yes)
  27FF816h 2     RTC Status?                 (0=Okay, 1=Bad)
  27FF818h 1     Random RTC    ;random LSB from SIO debug detect handshake
  27FF819h 37h   Zerofilled by firmware
  27FF850h 2     NDS7 BIOS CRC (5835h)
  27FF860h 4     Somewhat copy of Cart[038h], nds7 ram addr (?)
  27FF864h 4     Wifi FLASH User Settings Bad (0=Okay, 1=Bad)
  27FF868h 4     Wifi FLASH User Settings FLASH Address (fmw[20h]*8)
                   maybe recommended to use above RAM cell instead FLASH entry?
  27FF86Ch 4     Whatever (seems to be zero at cart boot time)
  27FF870h 4     Whatever (seems to be zero at cart boot time)
  27FF874h 2     Wifi FLASH firmware part5 crc16 (359Ah) (fmw[026h])
  27FF876h 2     Wifi FLASH firmware part3/part4 crc16 (fmw[004h] or ZERO)
                   Above is usually ZERO at cart boot (set to fmw[004h] only
                   when running pictochat, or maybe also when changing user
                   settings)
  27FF878h 08h   Not used
  27FF880h 4     Message from NDS9 to NDS7  (=7 at cart boot time)
  27FF884h 4     NDS7 Boot Task (also checked by NDS9) (=6 at cart boot time)
  27FF888h ..    Whatever (seems to be zero at cart boot time)
  27FF890h 4     Somewhat boot flags (somewhat B0002A22h)
                   bit10 part3/part4 loaded/decoded (bit3 set if bad crc)
                   bit28 part5 loaded/decoded with good crc
  27FF894h 36Ch  Not used (zero)
  27FFC00h 4     NDS Gamecart Chip ID 1   (copy of 27FF800h)
  27FFC04h 4     NDS Gamecart Chip ID 2   (copy of 27FF804h)
  27FFC08h 2     NDS Cart Header CRC      (copy of 27FF808h)
  27FFC0Ah 2     NDS Cart Secure Area CRC (copy of 27FF80Ah)
  27FFC0Ch 2     NDS Cart Missing/Bad CRC (copy of 27FF80Ch)
  27FFC0Eh 2     NDS Cart Secure Area Bad (copy of 27FF80Eh)
  27FFC10h 2     NDS7 BIOS CRC (5835h)    (copy of <27FF850h>)
  27FFC12h 2     Secure Disable           (copy of 27FF812h)
  27FFC14h 2     SIO Debug Exist          (copy of 27FF814h)
  27FFC16h 1     RTC Status?              (<8bit> copy of 27FF816h)
  27FFC17h 1     Random 8bit              (copy of <27FF818h>)
  27FFC18h 18h   Not used (zero)
  27FFC30h 2     GBA Cartridge Header[BEh], Reserved
  27FFC32h 3     GBA Cartridge Header[B5h..B7h], Reserved
  27FFC35h 1     Whatever flags ?
  27FFC36h 2     GBA Cartridge Header[B0h], Maker Code
  27FFC38h 4     GBA Cartridge Header[ACh], Gamecode
  27FFC3Ch 4     Frame Counter (eg. 00000332h in no$gba with original firmware)
  27FFC40h 2     Boot Indicator (0001h=normal; required for some NDS games)
  27FFC42h 3Eh   Not used (zero)
  27FFC80h 70h   Wifi FLASH User Settings (fmw[newest_user_settings])
  27FFCF0h 10h   Not used (zero)
  27FFDxxh ..    NDS9 Debug Exception Stack (stacktop=27FFD9Ch)
  27FFD9Ch 4     NDS9 Debug Exception Vector (0=None)
  27FFDA0h ..    ...
  27FFE00h 170h  NDS Cart Header at 27FFE00h+0..16Fh
  27FFF70h ..    Not used (zerofilled at cart boot time)
  27FFFF8h 2     NDS9 Scratch addr for SWI IsDebugger check
  27FFFFAh 2     NDS7 Scratch addr for SWI IsDebugger check
  27FFFFCh ..    ...
  27FFFFEh 2     Main Memory Control (on-chip power-down I/O port)
  DTCM+3FF8h 4   NDS9 IRQ IF Check Bits (hardcoded RAM address)
  DTCM+3FFCh 4   NDS9 IRQ Handler (hardcoded RAM address)
  37F8000h FE00h ARM7 bootcode can be loaded here (37F8000h..3807DFFh)
  380F700h 1D4h  Fragments of NDS7 firmware boot code
  380F980h 4     Unknown/garbage (set to FBDD37BBh, purpose unknown)
                   NOTE: Cooking Coach is doing similar crap at 37FCF1Ch ?!?!
  380FFC0h 4     DSi7 IRQ IF2 Check Bits (hardcoded RAM address) (DSi only)
  380FFDCh ..    NDS7 Debug Stacktop / Debug Vector (0=None)
  380FFF8h 4     NDS7 IRQ IF Check Bits (hardcoded RAM address)
  380FFFCh 4     NDS7 IRQ Handler (hardcoded RAM address)
  ---
  summary of nds memory used at cartridge boot time:
  (all other memory zero-filled unless containing cartridge data)
  37F8000h..3807E00h  ;cartridge area (nds7 only)
  2000000h..23BFE00h  ;cartridge area (nds9 and nds7)
  2400000h..27BFE00h  ;cartridge area (debug ver)
  23FEE00h..23FEF68h  ;fragments of NDS9 firmware boot code
  27FF800h..27FF85Fh  ;various values (from BIOS boot code)
  27FF860h..27FF893h  ;various values (from Firmware boot code)
  27FFC00h..27FFC41h  ;various values (from Firmware boot code)
  27FFC80h..27FFCE6h  ;firmware user settings
  27FFE00h..27FFF6Fh  ;cart header
  380F700h..380F8D4h  ;fragments of NDS7 firmware boot code
  380F980h            ;set to FBDD37BBh
  ---
  register settings at cartridge boot time:
  nds9 r0..r11     = zero
  nds9 r12,r14,r15 = entrypoint
  nds9 r13         = 3002F7Ch (!)
  nds9 r13_irq     = 3003F80h
  nds9 r13_svc     = 3003FC0h
  nds9 r14/spsr_irq= zero
  nds9 r14/spsr_svc= zero
  ---
  nds7 r0..r11     = zero
  nds7 r12,r14,r15 = entrypoint
  nds7 r13         = 380FD80h
  nds7 r13_irq     = 380FF80h
  nds7 r13_svc     = 380FFC0h
  nds7 r14/spsr_irq= zero
  nds7 r14/spsr_svc= zero
  ---
  Observe that SWI SoftReset applies different stack pointers:
  Host  sp_svc    sp_irq    sp_sys    zerofilled area       return address
  NDS7  380FFDCh  380FFB0h  380FF00h  [380FE00h..380FFFFh]  Addr[27FFE34h]
  NDS9  0803FC0h  0803FA0h  0803EC0h  [DTCM+3E00h..3FFFh]   Addr[27FFE24h]
```

### DSi BIOS RAM

```
  2000000h 8    Whatever Title ID    ;carthdr[230h]
  2000008h 1    Whatever Unknown/Unused
  2000009h 1    Whatever Flags (03h=Stuff is used?)
  200000Ah 2    Whatever Maker code  ;carthdr[010h]
  200000Ch 2    Whatever Unknown  ;\counter/length/indices/whatever?
  200000Eh 2    Whatever Unknown  ;/
  2000010h 2    Whatever CRC16 [2000000h..20002FFh] ini=FFFFh,[2000010h]=0000h
  2000012h 2    Whatever Unknown/Unused
  2000014h 2ECh Whatever Unknown... some buffer... string maybe?
  2000300h 5   Warmboot ID ("TLNC",00h) (also requires BPTWL[70h]=01h)
  2000305h 1   Warmboot Length of data at 2000308h (01h..18h, for CRC)
  2000306h 2   Warmboot CRC16 of data at 2000308h (with initial value FFFFh)
  2000308h 8   Warmboot Unknown                     ;-rarely used
  2000310h 8   Warmboot Title ID                    ;-often used
  2000318h 4   Warmboot Flags (bit0, 1-3, 4, 5,6,7) ;-usually 16bit, once 32bit
  200031Ch 4   Warmboot Unused
  2000400h 128h  System Settings from TWLCFGn.dat file (bytes 088h..1AFh)
  20005E0h 1     WlFirm Type (1=DWM-W015, 2=DWM-W024) (as wifi_flash[1FDh])
  20005E1h 1     WlFirm Unknown (zero)
  20005E2h 2     WlFirm CRC16 with initial value FFFFh on [20005E4h..20005EFh]
  20005E4h 4     WlFirm RAM vars (500400h)  ;\
  20005E8h 4     WlFirm RAM base (500000h)  ; as from "Wifi Firmware" file
  20005ECh 4     WlFirm RAM size (02E000h)  ;/
  20005F0h 10h   WlFirm Unknown (zero)
  2000600h 14h   Hexvalues from HWINFO_N.dat
  23FEE00h 200h  DSi9 bootstrap relict
  ---
  2FEE120h 4     "nand"   <--- passed as so to launcher
  2FF80xxh
  2FF82xxh
  2FF83xxh
  2FF89xxh
  2FF8Axxh
  2FF8Bxxh
  2FF8Cxxh
  2FF8Dxxh       ... Wifi MAC address, channel mask, etc.
  2FF8Fxxh
  2FF90xxh
  2FF91xxh
  2FF9208h       FBDD37BBh (that odd "garbage" value occurs also on NDS)
  2FFA1xxh
  2FFA2xxh
  2FFA5xxh
  2FFA6xxh
  2FFA680h 12    02FD4D80h,00000000h,00001980h
  2FFA68Ch ..    Zerofilled
  ---
  2FFC000h 1000h Full Cart Header (as at 2FFE000h, but, FOR NDS ROM CARTRIDGE)
  2FFD000h 7B0h  Zerofilled
  2FFD7B0h 8+1   Version Data Filename (eg. 30,30,30,30,30,30,30,34,00)
  2FFD7B9h 1     Version Data Region   (eg. 50h="P"=Europe)
  2FFD7BAh 1     Unknown  (00)   ;bit0 = warmboot-flag-related
  2FFD7BBh 1     Unknown  (00)
  2FFD7BCh 15+1  eMMC CID (dd,ss,ss,ss,ss,03,4D,30,30,46,50,41,00,00,15), 00
  2FFD7CCh 15+1  eMMC CSD (40,40,96,E9,7F,DB,F6,DF,01,59,0F,2A,01,26,90), 00
  2FFD7DCh 4     eMMC OCR (80,80,FF,80)                                  ;20h
  2FFD7E0h 8     eMMC SCR (00,04,00,00,00,00,00,00) (for MMC: dummy/4bit);24h
  2FFD7E8h 2     eMMC RCA (01,00)                                        ;2Ch
  2FFD7EAh 2     eMMC Typ (01,00) (0=SD Card, 1=MMC Card)                ;2Eh
  2FFD7ECh 2     eMMC HCS (00,00) ;copy of OCR.bit30 (sector addressing) ;30h
  2FFD7EEh 2     eMMC ?   (00,00)                                        ;32h
  2FFD7F0h 4     eMMC ?   (00,00,00,00)                                  ;34h
  2FFD7F4h 4     eMMC CSR (00,09,00,00)   ;card status (state=tran)      ;38h
  2FFD7F8h 2     eMMC Port 4004824h setting  (00,01)  ;SD_CARD_CLK_CTL   ;3Ch
  2FFD7FAh 2     eMMC Port 4004828h setting  (E0,40)  ;SD_CARD_OPTION    ;3Eh
  2FFD7FCh 2     eMMC ?   (00,00)                                        ;40h
  2FFD7FEh 2     eMMC Device (usually 0001h=eMMC) (0000h=SD/MMC Slot?)   ;42h
  2FFD800h 1     Unknown 05h (maybe number of IDs at 2FFD850h?)
  2FFD801h 2Fh   Zerofilled
  2FFD830h 1     Unknown 1Fh
  2FFD831h 1Fh   Zerofilled
  2FFD850h 5x8   Five Title IDs (ROM Cart, and HNBP, HNDA, HNEA, HNGP) why?
  2FFD878h 788h  Zerofilled
  2FFE000h 1000h DSi Full Cart Header (additionally to short headers)
  2FFF000h 0Ch   Zerofilled
  2FFF00Ch 4     ? 0000007Fh
  2FFF010h 4     ? 550E25B8h
  2FFF014h 4     ? 02FF4000h
  2FFF018h A68h  Zerofilled
  2FFFA80h 160h  Short Cart header (as at 2FFFE00h, but, FOR NDS ROM CARTRIDGE)
  2FFFBE0h 20h   Zerofilled
```

Below resembles NDS area at 27FFC00h (with added/removed stuff)...

```
  2FFFC00h 4     NDS Gamecart Chip ID
  2FFFC04h 20h   Zerofilled
  2FFFC24h 5     ? (04 00 73 01 03)
  2FFFC29h 7     Zerofilled
  2FFFC30h 12    GBA Cartridge Header (FF FF FF FF FF 00 FF FF FF FF FF FF)
  2FFFC3Ch 4     Frame Counter maybe? (eg. 1F 01 00 00 in cooking coach)
  2FFFC40h 2     Boot Indicator (0001h=normal; required for some NDS games)
  2FFFC42h 3Eh   Not used (zero)
  2FFFC80h 70h   Wifi FLASH User Settings (fmw[newest_user_settings])
  2FFFCF0h 4     ?           (3D 00 01 6E) (update counter and crc16 ?)
  2FFFCF4h 6     Wifi MAC Address (00 23 CC xx xx xx)              (fmw[036h])
  2FFFCFAh 2     Wifi Channels (usually 1041h = ch1+7+13) (based on fmw[03Ch])
  2FFFCFCh 4     Zero
  2FFFD00h 68h   Zerofilled
  2FFFD68h 4     Bitmask for Supported Languages (3Eh for Europe);\
  2FFFD6Ch 4     Unknown (00,00,00,00)                           ; from
  2FFFD70h 1     Console Region (0=JP,1=US,2=EU,3=AU,4=CHN,5=KOR); HWINFO_S.dat
  2FFFD71h 12    Serial/Barcode (ASCII, 11-12 characters)        ;
  2FFFD7Dh 3     ? (00 00 3C)                                    ;/
  2FFFD80h 0Ch   Zerofilled
  2FFFD8Ch 10h   ARM9 debug exception stack (stacktop 2FFFD9Ch)
  2FFFD9Ch 4     ARM9 debug exception vector (020D3E64h)
  2FFFDA0h 4     02F80000h                                ;\
  2FFFDA4h 4     02FFA674h                                ;
  2FFFDA8h 4     00000000h zero                           ; start addresses?
  2FFFDACh 4     01FF86E0h itcm?                          ;
  2FFFDB0h 4     027C00C0h                                ;
  2FFFDB4h 4     02FFF000h                                ;
  2FFFDB8h 4     03040000h wram?                          ;
  2FFFDBCh 4     03800000h wram?                          ;
  2FFFDC0h 4     0380C3B4h wram?                          ;/
  2FFFDC4h 4     02F80000h                                ;\
  2FFFDC8h 4     02FFC000h ptr to DSi Full Cart Header    ;
  2FFFDCCh 4     00000000h zero                           ; end addresses?
  2FFFDD0h 4     02000000h ram bottom?                    ; (for above nine
  2FFFDD4h 4     027C0780h                                ; start addresses)
  2FFFDD8h 4     02FFF680h                                ;
  2FFFDDCh 4     03040000h wram?                          ;
  2FFFDE0h 4     03800000h wram?                          ;
  2FFFDE4h 4     0380F780h wram?                          ;/
  2FFFDE8h 4     RTC Date at Boot (BCD) (yy,mm,dd,XX) (XX=maybe day-of-week?)
  2FFFDECh 4     RTC Time at Boot (BCD) (hh,ss,mm,0) (hh.bit6=maybe PM or 24h?)
  2FFFDF0h 4     Initial ARM7 Port 4004008h bits (13FBFB06h) (SCFG_EXT)
  2FFFDF4h 1     Initial ARM7 Port 40040xxh bits (C4h)       (SCFG_xxx)
  2FFFDF5h 1     Initial ARM7 Port 400400xh bits (F0h)       (SCFG_xxx)
  2FFFDF6h 2+2   Zerofilled
  2FFFDFAh 1     Warmboot Flag (bptwl[70h] OR 80h, ie. 80h=cold or 81h=warm)
  2FFFDFBh 1     01h
  2FFFDFCh 4     Pointer to TWLCFGn.dat (usually 2000400h) (or 0=2000400h)
  2FFFE00h 160h  Short Cart header (unlike NDS, only 160h, not 170h)
  2FFFF60h A0h   Zerofilled
 37FA414h       "nand:/title/....app"  <-- [1D4h]+3C0h  (without Device List!)
 380C400h 22E4h BIOS Keys (as from Boot Stage 1, see there)
  380F010h 10h   AES key for dev.kp (E5,CC,5A,8B,...) (optional/for launcher)
  380F600h 200h  DSi7 bootstrap relict (at 3FFF600h aka mirrored to 380F600h)
  380FFC0h 4     DSi7 IRQ IF2 Check Bits (hardcoded RAM address) (DSi only)
  380FFC4h 4     DSi7 SCFG_EXT setting
  380FFC8h 2     DSi7 SCFG_misc bits
  380FFDCh ..    DSi7 Debug Stacktop / Debug Vector (0=None)
  380FFF8h 4     DSi7 IRQ IF Check Bits (hardcoded RAM address)
  380FFFCh 4     DSi7 IRQ Handler (hardcoded RAM address)
  xxxxxxxh ?     ARM7i and ARM9 bootcode can be loaded WHERE and WHERE?
  cart_header[1D4h] 400h SD/MMC Device List ARM7 RAM; initialized by firmware
```

Initial state after DSi BIOS ROM bootcode (when starting eMMC bootcode)
requires only a few memory blocks in ITCM, ARM7 WRAM, and AES keyslots:

```
  1FFC400h 400h  BIOS Keys from FFFF87F4h (C3 02 93 DE ..) Whatever, 8x80h RSA?
  1FFC800h 80h   BIOS Keys from FFFF9920h (30 33 26 D5 ..) Whatever
  1FFC880h 14h   Whatever, should/may be zerofilled?
  1FFC894h 1048h BIOS Keys from FFFF99A0h (99 D5 20 5F ..) Blowfish/NDS-mode
  1FFD8DCh 1048h BIOS Keys from FFFFA9E8h (D8 18 FA BF ..) Blowfish/unused?
  3FFC400h 200h  BIOS Keys from 00008188h (CA 13 31 79 ..) Whatever, 32x10h AES?
  3FFC600h 40h   BIOS Keys from 0000B5D8h (AF 1B F5 16 ..) Whatever, AES?
  3FFC640h 14h   Whatever, must be zerofilled
  3FFC654h 1048h BIOS Keys from 0000C6D0h (59 AA 56 8E ..) Blowfish/DSi-mode
  3FFD69Ch 1048h BIOS Keys from 0000D718h (54 86 13 3B ..) Blowfish/unused?
  3FFE6E4h 44h   eMMC Info (to be relocated to 2FFD7BCh, see there for details)
  4004450h 8     AES Key0.X ("Nintendo") for modcrypt
  4004480h 10h   AES Key1.X (CPU/Console ID and constants) for dev.kp and Tad
  40044xxh ?     AES Key2... (?)
  40044E0h 1Ch   AES Key3.X/Y (CPU/Console ID and constants) for eMMC
  2000300h 20h   Warmboot Info (optional, passed on to Launcher)
```

## <a name="biosdumping"></a>  BIOS Dumping

### BIOSes

```
  GBA BIOS 16K  (fully dumpable)
  NDS7 BIOS 16K (fully dumpable)
  NDS9 BIOS 4K  (fully dumpable)
  DSi7 BIOS 64K (about 41K dumpable)
  DSi9 BIOS 64K (about 41K dumpable)
  DSiWifi BIOS 80K on older DSi (fully dumpable)
  DSiWifi BIOS Unknown size on newer DSi (probably fully dumpable)
  3DSWifi BIOS Unknown size on 3DS (probably fully dumpable)
```

### GBA BIOS

Contains SWI Functions and Bootcode (for starting cartridges, or booting via
Serial Port). The GBA BIOS can be read only by opcodes executed in BIOS area,
for example, via the MidiKey2Freq function (most other SWI Functions (like
CpuSet) are refusing source addresses within BIOS area).

### NDS BIOSes

Contains SWI Functions and Bootcode (for booting from SPI Bus Firmware FLASH
memory). The NDS9 BIOS can be dumped without restrictions (eg. via CpuSet, or
via LDR opcodes in RAM). The NDS7 BIOS has same restrictions as GBA, ie.
reading works only by BIOS opcodes, and not by functions like CpuSet. The
GetCRC16 functions does work though (at least for memory at 1204h..3FFFh). As
an additional obstacle, memory at 0000h..1203h can be dumped only by opcodes
within 0000h..1203h (that memory does mainly contain data, but some of the data
values can serve as THUMB LDR opcodes). For details see:

- [DS Memory Control - BIOS](#dsmemorycontrolbios)
Note: DSi consoles are containing a copy of the NDS BIOSes, but with BIOSPROT
set to 0020h (even when running in NDS mode), so the first 20h bytes of the
DSi's NDS7 BIOS aren't dumpable (except via tracing, see below), that 20h bytes
should be just same as on original NDS7 though.

### DSi BIOSes - Lower 32K-halves (SWI Functions)

The lower 32K of DSi9 doesn't have any restricions. The lower 32K of DSi7 has
similar restrictions as NDS7, but with BIOSPROT set to 0020h (instead of
1204h), this is making it more easy to dump memory at 0020h..7FFFh (eg. via
GetCRC16), but makes it impossible to dump the exception vectors at
0000h..001Fh, however, they can be deduced by tracing (with timer IRQs):

```
  ROM:00000000h  EA000006  b 20h   ;dsi7_reset_vector
  ROM:00000004h  EA000006  b 24h   ;dsi7_undef_handler
  ROM:00000008h  EA00001F  b 8Ch   ;dsi7_swi_handler
  ROM:0000000Ch  EA000004  b 24h   ;dsi7_prefetch_abort_handler
  ROM:00000010h  EA000003  b 24h   ;dsi7_data_abort_handler
  ROM:00000014h  EAFFFFFE  b 14h   ;reserved_vector
  ROM:00000018h  EA000013  b 6Ch   ;dsi7_irq_handler
  ROM:0000001Ch  EA000000  b 24h   ;dsi7_fiq_handler
```

Aside from branch opcodes, above could theoretically contain ALU opcodes that
modify R15 (but that would be very unlikely, and would make no difference).

### DSi BIOSes - Upper 32K-halves (Bootcode, for booting from eMMC memory)

The upper 32K of the DSi9 and DSi7 BIOSes are locked at some point during
booting, and there's no known way to dump them directly. However, portions of
that memory are relocated to RAM/TCM before locking, and that relocated copies
can be dumped.

On a DSi, the following DSi ROM data can be dumped (originally done via Main
Memory hacks, ie. with complex external hardware soldered to the mainboard, but
it's now also possible via Unlaunch.dsi exploit):

```
  ROM:FFFF87F4h / TCM:1FFC400h (400h)  (C3 02 93 DE ..) Whatever, 8x80h RSA?
  ROM:FFFF9920h / TCM:1FFC800h (80h)   (30 33 26 D5 ..) Whatever
  ROM:FFFF99A0h / TCM:1FFC894h (1048h) (99 D5 20 5F ..) Blowfish/NDS-mode
  ROM:FFFFA9E8h / TCM:1FFD8DCh (1048h) (D8 18 FA BF ..) Blowfish/unused?
  ROM:00008188h / RAM:3FFC400h (200h)  (CA 13 31 79 ..) Whatever, 32x10h AES?
  ROM:0000B5D8h / RAM:3FFC600h (40h)   (AF 1B F5 16 ..) Whatever, "common key"?
  ROM:0000C6D0h / RAM:3FFC654h (1048h) (59 AA 56 8E ..) Blowfish/DSi-mode
  ROM:0000D718h / RAM:3FFD69Ch (1048h) (54 86 13 3B ..) Blowfish/unused?
```

On a 3DS, the following "DSi ROM data" can be dumped from the 2470h-byte DSi
key area in 3DS memory at ARM9 ITCM 01FFD000h..01FFF46F (via 3DS exploits that
are capable of executing code on ARM9 side):

```
  ROM:FFFF87F4h / 3DS:01FFD000h  200h  RSA key 0..3
  ROM:00008308h / 3DS:01FFD200h  80h   some AES keys
  ROM:FFFF9920h / 3DS:01FFD280h  80h   whatever
  ROM:0000B5D8h / 3DS:01FFD300h  40h   AES keys and values (common etc)
  ROM:?         / 3DS:01FFD340h  A0h   misc "Nintendo" string etc.
  ROM:0000C6D0h / 3DS:01FFD3E0h  1048h Blowfish for DSi-mode
  ROM:FFFF99A0h / 3DS:01FFE428h  1048h Blowfish for DS-mode
```

The 3DS does have only half of the DSi keys (the extra keys might be used for
DSi debug version, but aren't needed for normal DSi software).

The 40h-byte area for ROM:0000B5D8h can be fully dumped from 3DS ITCM, the same
vales should also exist in DSi ITCM, but the DSi zerofills a 10h-byte fraction
of that area after initialization, and it doesn't seem be possible to read that
values via Main Memory hacks (most of that erased values can be found in AES
keyslots though).

The A0h-byte area is found only in 3DS ITCM, it should also exist somewhere in
DSi ROM, but isn't relocated to DSi ITCM (however, the relevant values can be
found in AES keyslots, eg. the "Nintendo" string).

### Checksums for BiosDSi.rom (20000h bytes)

```
  Offset Size  CRC32
  00000h 8000h 5434691Dh                ;\
  08000h 188h  ?                        ;
  08188h 180h  E5632151h  (not 3ds)     ;
  08308h 80h   64515306h                ;
  08388h 3250h ?                        ;
  0B5D8h 20h   85BE2749h                ; ARM7
  0B5F8h 10h   25A46A54h  (3ds only)    ;
  0B608h 10h   E882B9A9h                ;
  0B618h 10B8h ?                        ;
  0C6D0h 1048h 3B5CDF06h                ;
  0D718h 1048h 5AC363F9h  (not 3ds)     ;
  0E860h 18A0h ?                        ;/
  10000h 8000h 11E7C1EAh                ;\
  18000h 7F4h  ?                        ;
  187F4h 200h  4405D4BAh                ;
  189F4h 200h  2A32F2E7h  (not 3ds)     ;
  18BF4h D2Ch  ?                        ; ARM9
  19920h 80h   2699A10Fh                ;
  199A0h 1048h A8F58AE7h                ;
  1A9E8h 1048h E94759ACh  (not 3ds)     ;
  1BA30h 45D0h ?                        ;/
  ?      A0h   180DF59Bh  (3ds only)    ;-whatever, "Nintendo" string etc.
  ?      80h   ........h  (TWL-FIRM)    ;-RSA key for eMMC boot info
```

Checksums for the 'whole' 20000h-byte file (with unknown/missing areas
zerofilled):

```
  180DF59Bh (tcm/ram dump)  (missing 10h bytes)
  03A21235h (3ds dump)      (missing 180h+200h+1048h+1048h bytes)
  CDAA8FF6h (combined dump) (missing only the unknown "?" areas)
```

### DSiWifi BIOS

The Wifi BIOS can be dumped by using the WINDOW\_DATA register via SDIO CMD53.

### Further DSi BIOSes

The DSi cameras and several other I2C/SPI devices are probably having BIOS
ROMs, too. Unknown if/how that ROMs are dumpable.

### DSi BIOS Dumping via voltage errors

Lowering VDD12 for a moment does work quite reliable for crashing the ARM9 and
trapping the 2FFFD9Ch vector in Main RAM. The problem is that Main RAM seems to
be disabled during bootstage 1 (it gets enabled at begin of bootstage 2 via
EXMEMCNT, that is, shortly after the upper BIOS 32Kbyte areas are disabled).
More on that here:

```
  http://4dsdev.kuribo64.net/thread.php?id=130
```

One theory/idea (from dark\_samus) is that EXMEMCNT controls the CE2 pin on the
Main RAM chip, so one could try to rewire that pin to get Main RAM enabled
regardless of EXMEMCNT, if that's actually working, then trapping the 2FFFD9Ch
vector should work even while BIOS ROMs are fully readable.

## <a name="externalconnectors"></a>  External Connectors

### External Connectors

- [AUX GBA Game Pak Bus](#auxgbagamepakbus)
- [AUX DS Game Card Slot](#auxdsgamecardslot)
- [AUX Link Port](#auxlinkport)
- [AUX Sound/Headphone Socket and Battery/Power Supply](#auxsoundheadphonesocketandbatterypowersupply)
- [AUX DSi SD/MMC Pin-Outs](#auxdsisdmmcpinouts)

### Getting access to Internal Pins

- [AUX Opening the GBA](#auxopeningthegba)
- [AUX Mainboard](#auxmainboard)
- [AUX DSi Component Lists](#auxdsicomponentlists)
- [AUX DSi Internal Connectors](#auxdsiinternalconnectors)
- [AUX DSi Chipset Pinouts](#auxdsichipsetpinouts)

### More Internal Stuff

- [Pinouts - CPU - Signal Summary](#pinoutscpusignalsummary)
- [Pinouts - CPU - Pinouts](#pinoutscpupinouts)
- [Pinouts - Audio Amplifiers](#pinoutsaudioamplifiers)
- [Pinouts - LCD Cables](#pinoutslcdcables)
- [Pinouts - Power Switches, DC/DC Converters, Reset Generators](#pinoutspowerswitchesdcdcconvertersresetgenerators)
- [Pinouts - Wifi](#pinoutswifi)
- [Pinouts - Various](#pinoutsvarious)

### Xboo Multiboot Cable

- [AUX Xboo PC-to-GBA Multiboot Cable](#auxxboopctogbamultibootcable)
- [AUX Xboo Flashcard Upload](#auxxbooflashcardupload)
- [AUX Xboo Burst Boot Backdoor](#auxxbooburstbootbackdoor)
- [DS Xboo](#dsxboo)

## <a name="auxgbagamepakbus"></a>  AUX GBA Game Pak Bus

### Game Pak Bus - 32pin cartridge slot

The cartridge bus may be used for both CGB and GBA game paks. In GBA mode, it
is used as follows:

```
 Pin    Name    Dir  Expl.
 1      VDD     O    Power Supply 3.3V DC
 2      PHI     O    System Clock (selectable none, 4.19MHz, 8.38MHz, 16.78MHz)
 3      /WR     O    Write Select    ;\latched address to be incremented on
 4      /RD     O    Read Select     ;/rising edges of /RD or /WR signals
 5      /CS     O    ROM Chip Select ;-A0..A15 to be latched on falling edge
 6-21   AD0-15  I/O  lower 16bit Address    and/or  16bit ROM-data (see below)
 22-29  A16-23  I/O  upper 8bit ROM-Address   or    8bit SRAM-data (see below)
 30     /CS2    O    SRAM Chip Select
 31     /REQ    I    Interrupt request (/IREQ) or DMA request (/DREQ)
 32     GND     O    Ground 0V
```

When accessing game pak SRAM, a 16bit address is output through AD0-AD15, then
8bit of data are transferred through A16-A23.

When accessing game pak ROM, a 24bit address is output through AD0-AD15 and
A16-A23, then 16bit of data are transferred through AD0-AD15.

The 24bit address is formed from the actual 25bit memory address (byte-steps),
divided by two (halfword-steps).

Pin Pitch is 1.5mm.

### 8bit-Gamepak-Switch (GBA, GBA SP only) (not DS)

A small switch is located inside of the cartridge slot, the switch is pushed
down when an 8bit cartridge is inserted, it is released when a GBA cartridge is
inserted (or if no cartridge is inserted).

The switch mechanically controls whether VDD3 or VDD5 are output at VDD35; ie.
in GBA mode 3V power supply/signals are used for the cartridge slot and link
port, while in 8bit mode 5V are used.

The switch additionally drags IN35 to 3V when an 8bit cart is inserted, the
current state of IN35 can be determined in GBA mode via Port 4000204h
(WAITCNT), if the switch is pushed, then CGB mode can be activated via Port
4000000h (DISPCNT.3), this bit can be set ONLY by opcodes in BIOS region (eg.
via CpuSet SWI function).

In 8bit mode, the cartridge bus works much like for GBA SRAM, however, the 8bit
/CS signal is expected at Pin 5, while GBA SRAM /CS2 at Pin 30 is interpreted
as /RESET signal by the 8bit MBC chip (if any). In practice, this appears to
result in 00h being received as data when attempting to read-out 8bit
cartridges from inside of GBA mode.


## <a name="auxlinkport"></a>  AUX Link Port

### Serial Link Port Pin-Out (GBA:"EXT" - GBA SP:"EXT.1")

```
  Pin  Name  Cable
  1    VDD35 N/A       GBA Socket     GBA Plug   Old "8bit" Plug
  2    SO    Red       ___________    _________    ___________
  3    SI    Orange   |  2  4  6  |  / 2  4  6 \  |  2  4  6  |
  4    SD    Brown     \_1_ 3 _5_/   \_1_ 3 _5_/   \_1__3__5_/
  5    SC    Green         '-'           '-'
  6    GND   Blue      Socket Outside View / Plug Inside View
  Shield     Shield
```

Note: The pin numbers and names are printed on the GBA mainboard, colors as
used in Nintendo's AGB-005 and older 8bit cables.

### Serial Link/Power Supply Port (GBA-Micro: "EXT.")

```
  1  In  DC (Supply 5.2VDC)  ___________________
  2  Out V3 (SIO 3.3VDC)    |  1 2 3 4 5 6 7 8  |
  3  I/O SO (SIO RCNT.3)    | ================= |
  4  I/O SI (SIO RCNT.2)     \_________________/
  5  I/O SD (SIO RCNT.1)
  6  I/O SC (SIO RCNT.0)
  7  OUT DG (SIO GROUND)
  8  In  DG (Supply GROUND)
  -  -   -  (Shield not connected)
```

### Cable Diagrams (Left: GBA Cable, Right: 8bit Gameboy Cable)

```
  Big Plug  Middle Socket  Small Plug    Plug 1         Plug 2
   SI _________________     ____ SI       SI ______  ______SI
   SO ____________SO   |__ | ___ SO       SO ______><______SO
   GND____________GND______|____GND       GND_____________GND
   SD ____________SD____________ SD       SD               SD
   SC ____________SC____________ SC       SC _____________ SC
   Shield_______Shield_______Shield       Shield_______Shield
```

### Normal Connection

Just connect the plugs to the two GBAs and leave the Middle Socket
disconnected, in this mode both GBAs may behave as master or slave, regardless
of whether using big or small plugs.

The GBA is (NOT ???) able to communicate in Normal mode with MultiPlay cables
which do not have crossed SI/SO lines.

### Multi-Play Connection

Connect two GBAs as normal, for each further GBAs connect an additional cable
to the Middle socket of the first (or further) cable(s), up to four GBAs may be
connected by using up to three cables.

The GBA which is connected to a Small Plug is master, the slaves are all
connected to Large Plugs. (Only small plugs fit into the Middle Socket, so it's
not possible to mess up something here).

### Multi-Boot Connection

MultiBoot (SingleGamepak) is typically using Multi-Play communication, in this
case it is important that the Small plug is connected to the master/sender (ie.
to the GBA that contains the cartridge).

### Non-GBA Mode Connection

First of all, it is not possible to link between 32bit GBA games and 8bit
games, parts because of different cable protocol, and parts because of
different signal voltages.

However, when a 8bit cartridge is inserted (the GBA is switched into 8bit
compatibility mode) it may be connected to other 8bit games (monochrome
gameboys, CGBs, or to other GBAs which are in 8bit mode also, but not to GBAs
in 32bit mode).

When using 8bit link mode, an 8bit link cable must be used. The GBA link cables
won't work, see below modification though.

### Using a GBA 32bit cable for 8bit communication

Open the middle socket, and disconnect Small Plugs SI from GND, and connect SI
to Large Plugs SO instead. You may also want to install a switch that allows to
switch between SO and GND, the GND signal should be required for MultiPlay
communication only though.

Also, cut off the plastic ledge from the plugs so that they fit into 8bit
gameboy sockets.

### Using a GBA 8bit cable for 32bit communication

The cable should theoretically work as is, as the grounded SI would be required
for MultiPlay communication only. However, software that uses SD for
Slave-Ready detection won't work unless when adding a SD-to-SD connection (the
8bit plugs probably do not even contain SD pins though).

## <a name="auxsoundheadphonesocketandbatterypowersupply"></a>  AUX Sound/Headphone Socket and Battery/Power Supply

### GBA, GBA-Micro, NDS, and NDS-Lite: Stereo Sound Connector (3.5mm, female)

```
  Tip     Audio Left         ___ ___ _____+-----------+
  Middle  Audio Right       (___|___|_____|           |
  Base    Ground              L   R   GND +-----------+
```

The NDS socket doesn't fully match regular 3.5mm plugs, one needs to cut-off a
portion of the DS case to be able to fully insert the plug, which still
requires a lot of pressure, furthermore, when fully inserted, left/right become
shortcut to mono, so one needs to pull-back the plug a bit to gain stereo
output.

### GBA SP and NDS - Power/Headphone Socket (EXT.2)

```
  Pin SP   NDS  Expl.
  1   P31  SL   Audio LOUT                          _____________
  2   P32  VIN  Supply Input (DC 5.2V)           SW| 5   ___   1 |SL
  3   P33  SR   Audio ROUT                         | ----   ---- |
  4   P34  SG   Audio GND (via 100uF to GND)       |_6__4   3__2_|
  5   P35  SW   Audio Speaker Disable (GND=Dis)    GND SG\_/SR VIN
  6        GND  Supply GND
  Shield        GND
```

External power input is used to charge the built-in battery, it cannot be used
to run the SP without that battery.

### NDS-Lite - Power Socket

```
  Pin  Expl.                                          __________
  1    Supply Input (DC 5.2V)                        /  ======  \
  2    Supply GND                                GND |___2__1___| VIN
```

### GBA-Micro - Power Socket

Uses an 8pin socket (which combines SIO and Power), for pin-outs, see

- [AUX Link Port](#auxlinkport)

### External Power Supply

GBA: DC 3.3V (no separate power socket, requires 2xAA-battery-shaped adapter)

GBA-SP/NDS: DC 5.2V (or DC 5V) (special connector on power/headphone socket)

NDS-Lite: DC 5.2V (or DC 5V) (another special connector on power socket)

### Internal Battery Supply

GBA: 2xAA (3V)

GBA-SP: Li-ion 3.7V, 600mAh (built-in, recharge-able)

GBA-Micro: Li-ion 3.8V, 460mAh (built-in, recharge-able)

NDS: Li-ion 3.7V, 850mAh (built-in, recharge-able)

NDS-Lite: Li-ion 3.7V, 1000mAh (built-in, recharge-able)

### Using PC +5V DC as Power Supply

Developers whom are using a PC for GBA programming will probably want to use
the PC power supply (gained from disk drive power supply cable) for the GBA as
well rather than dealing with batteries or external power supplies.

GBA: To lower the voltage to approximately 3 Volts use two diodes, type 1N 4004
or similar, the ring printed onto the diodes points towards the GBA side,
connected as such:

```
  PC +5V (red)   --------|>|---|>|--------  GBA BT+
  PC GND (black) -------------------------  GBA BT-
```

GBA SP, GBA Micro, NDS, and NDS-Lite: Works directly at +5V connected to EXT.2
socket (not to the internal battery pins), without any diodes.


## <a name="auxopeningthegba"></a>  AUX Opening the GBA

Since Nintendo uses special screws with Y-shaped heads to seal the GBA (as well
as older 8bit gameboys), it's always a bit difficult to loosen these screws.

### Using Screwdrivers

One possible method is to use a small flat screwdriver, which might work, even
though it'll most likely damage the screwdriver.

Reportedly, special Y-shaped screwdrivers for gameboys are available for sale
somewhere (probably not at your local dealer, but you might find some in the
internet or elsewhere).

### Destroying the Screws

A more violent method is to take an electric drill, and drill-off the screw
heads, this might also slightly damage the GBA plastic chase, also take care
that the metal spoons from the destroyed screws don't produce shortcuts on the
GBA mainboard.

### Using a selfmade Screwdriver

A possible method is to take a larger screw (with a normal I-shaped, or
X-shaped head), and to cut the screw-tip into Y-shape, you'll then end up with
an "adapter" which can be placed in the middle between a normal screwdriver and
gameboy screws.

Preferably, first cut the screw-tip into a shape like a "sharp three sided
pyramid", next cut notches into each side. Access to a grinding-machine will be
a great benefit, but you might get it working by using a normal metal-file as
well.

### Opening the GBA Micro

- open the case with appropriate screwdriver or drilling machine or whatever

- remove the plastic front-plate (there are two snap-ins inside at ONE side)

- remove the mainboard and screen and plastic skeleton from the metal case

- remove the start/select daughter-board from the plastic skeleton

- remove the plastic skeleton (move the screen through the skeleton)

- remove the screen (lift lcd socket front-side, backlight socket rear-side)

### Opening the NDS-Lite

- open the case with appropriate screwdriver or drilling machine or whatever

- remove the RFU unit, and the 4-pin touch-screen cable (under the RFU unit)

- remove the mainboard together with the lower screen

- remove the upper/lower screen cables (on the rear-side of the mainboard)

## <a name="auxmainboard"></a>  AUX Mainboard

Other possibly useful signals on the mainboard...

### FIQ Signal

The FIQ (Fast Interrupt) signal (labeled FIQ on the mainboard) could be used as
external interrupt (or debugging break) signal.

Caution: By default, the FIQ input is directly shortcut to VDD35 (+3V or +5V
power supply voltage), this can be healed by scratching off the CL1 connection
located close to the FIQ pin (FIQ still appears to have an internal pull-up, so
that an external resistor is not required).

The GBA BIOS rejects FIQs if using normal ROM cartridge headers (or when no
cartridge is inserted). When using a FIQ-compatible ROM header, Fast Interrupts
can be then requested by pulling FIQ to ground, either by a push button, or by
remote controlled signals.

### RESET Signal

The RESET signal (found on the mainboard) could be used to reset the GBA by
pulling the signal to ground for a few microseconds (or longer). The signal can
be directly used (it is not shortcut to VDD35, unlike FIQ).

Note: A reset always launches Nintendo's time-consuming and annoying boot/logo
procedure, so that it'd be recommend to avoid this "feature" when possible.

### Joypad Signals

The 10 direction/button signals are each directly shortcut to ground when
pressed, and pulled up high otherwise (unlike 8bit gameboys which used a 2x4
keyboard matrix), it'd be thus easy to connect a remote keyboard, keypad,
joypad, or read-only 12bit parallel port.




## <a name="pinoutscpusignalsummary"></a>  Pinouts - CPU - Signal Summary

### Advance Gameboy CPU Signal Summary

Cart Bus: D0-D7, A0-A15, /CS, /RD, /WR (different usage in GBA/DMG mode)

WRAM Bus: WA0-WA16, WD0-WD15, /WLB, /WUB, /WWE, /WOE (used in GBA mode only)

LCD Bus : LDR1-5, LDG1-5, LDB1-5, DCK, LP, PS, SPL, CLS, SPS, MOD, REVC

Joypad: TP0-3 (Buttons), TP4-7 (Directions), TP8-9 (L/R-Buttons, via R43/R44)

Serial Link: SC, SD (aka P14?), SI, SO - Audio: SO1-2, Vin

Other: CK1-2, PHI, IN35, VCNT5, /FIQ (via CL1 to VDD3), /RESET (IN), /RES (OUT)

Supply: VDD35, VDD3, VDD2, GND (some are probably undoc inputs)

GBA SP: Same as GBA, plus VDD1, plus duplicated supply pins, plus pin 152.

## <a name="pinoutscpupinouts"></a>  Pinouts - CPU - Pinouts

### Advance Gameboy CPU Pinouts (CPU AGB)

```
  1 VDD3  17 D0   33 A0    49 WA4   65 VDD2  81 WD9   97 LDB5   113 CK1
  2 IN35  18 A15  34 /CS   50 WA5   66 WD5   82 WD1   98 LDB4   114 CK2
  3 TP8   19 A14  35 /RD   51 WA6   67 WD13  83 /WOE  99 LDB3   115 VDD2
  4 TP0   20 A13  36 /WR   52 WA7   68 WD6   84 DCK   100 LDB2  116 GND
  5 TP1   21 A12  37 PHI   53 /WLB  69 WD14  85 LP    101 LDB1  117 VDD2
  6 SO1   22 A11  38 VDD35 54 /WUB  70 WD7   86 PS    102 GND   118 VCNT5
  7 SO2   23 A10  39 GND   55 /WWE  71 WD15  87 LDR5  103 VDD3  119 TP9
  8 Vin   24 A9   40 SC    56 WA8   72 WD8   88 LDR4  104 SPL   120 TP6
  9 /RES  25 A8   41 SD    57 WA9   73 WD16  89 LDR3  105 CLS   121 TP5
  10 D7   26 A7   42 SI    58 WA10  74 WA16  90 LDR2  106 SPS   122 TP7
  11 D6   27 A6   43 SO    59 WA11  75 WD12  91 LDR1  107 MOD   123 TP4
  12 D5   28 A5   44 VDD2  60 WA12  76 WD4   92 LDG5  108 REVC  124 /FIQ
  13 D4   29 A4   45 WA0   61 WA13  77 WD11  93 LDG4  109 GNDed 125 /RESET
  14 D3   30 A3   46 WA1   62 WA14  78 WD3   94 LDG3  110 GNDed 126 TP2
  15 D2   31 A2   47 WA2   63 WA15  79 WD10  95 LDG2  111 GNDed 127 TP3
  16 D1   32 A1   48 WA3   64 GND   80 WD2   96 LDG1  112 GNDed 128 GND
```

### GBA SP CPU Pinouts (CPU AGB B)

```
  1 IN35   21 D0    41 A0    61 WA4   81 WD13  101 GND   121 LDB4  141 GND
  2 TP8    22 A15   42 /CS   62 WA5   82 WD6   102 VDD1  122 LDB3  142 VDD3
  3 TP0    23 A14   43 /RD   63 WA6   83 WD14  103 GND   123 LDB2  143 GND
  4 TP1    24 A13   44 /WR   64 WA7   84 WD7   104 VDD3  124 LDB1  144 VCNT5
  5 SO1    25 A12   45 PHI   65 /WLB  85 WD15  105 DCK   125 GND   145 TP9
  6 SO2    26 A11   46 VDD35 66 /WUB  86 WD8   106 LP    126 VDD3  146 TP6
  7 Vin    27 GND   47 GND   67 GND   87 WD16  107 PS    127 SPL   147 TP5
  8 VDD1   28 VDD35 48 SC    68 VDD2  88 WA16  108 LDR5  128 CLS   148 TP7
  9 GND    29 A10   49 SD    69 /WWE  89 VDD2  109 LDR4  129 SPS   149 TP4
  10 VDD35 30 A9    50 SI    70 WA8   90 GND   110 LDR3  130 MOD   150 /FIQ
  11 /RES  31 A8    51 SO    71 WA9   91 WD12  111 LDR2  131 REVC  151 /RESET
  12 D7    32 A7    52 VDD35 72 WA10  92 WD4   112 LDR1  132 GND   152 ?
  13 D6    33 A6    53 GND   73 WA11  93 WD11  113 LDG5  133 GND   153 TP3
  14 D5    34 A5    54 VDD1  74 WA12  94 WD3   114 LDG4  134 GND   154 TP2
  15 D4    35 A4    55 GND   75 WA13  95 WD10  115 LDG3  135 GND   155 VDD3
  16 D3    36 GND   56 VDD2  76 WA14  96 WD2   116 LDG2  136 VDD1  156 GND
  17 D2    37 VDD35 57 WA0   77 WA15  97 WD9   117 LDG1  137 GND
  18 GND   38 A3    58 WA1   78 GND   98 WD1   118 GND   138 CK1
  19 VDD35 39 A2    59 WA2   79 VDD2  99 /WOE  119 VDD3  139 CK2
  20 D1    40 A1    60 WA3   80 WD5   100 VDD2 120 LDB5  140 VDD2
```

Pin 152 seems to be not connected on the mainboard, maybe an undoc output.

### GBA-Micro, NDS, NDS-Lite, and DSi CPU Pinouts

Unknown. The CPU Pins are hidden underneath of the CPU. And, in NDS and
NDS-Lite, the CPU itself hides underneath of the DS Cartridge Slot. In the DSi
it's hidden underneath of a shielding plate (which is itself underneath of the
removeable wifi daughterboard).

## <a name="pinoutsaudioamplifiers"></a>  Pinouts - Audio Amplifiers

### Advance Gameboy Audio Amplifier (AMP AGB IR3R60N) (U6)

```
  1   2   3   4   5   6   7   8   9   10  11  12   13   14   15   16   17  18
  C38 FR1 FR2 FL1 FL2 GND RIN LIN C39 VOL SW  VDD5 LOUT VCC3 ROUT VCC3 SP  GND
```

SW=Headphone Switch (grounded when none connected).

### GBA SP Audio Amplifier (uses AMB AGB IR3R60N, too) (U3)

Same connection as in GBA, except that pin14/16 connect to VR21 (instead VCC3),
and pin1/9 connect to different capacitors.

### NDS - National Semiconductor LM4880M Dual 250mW Audio Power Amplifier (U12)

```
  1-OUT A  2-IN A  3-BYPASS  4-GND  5-SHUTDOWN  6-IN B  7-OUT A  8-VDD.VQ5
```

NDS-Lite: No external amplifier (Mitsumi 3205B Powermanagment Device contains
internal amplifier).

## <a name="pinoutslcdcables"></a>  Pinouts - LCD Cables

### Advance Gameboy Display Socket

```
  1 ?     6 GND    11 LDR2   16 LDG2   21 LDB3   26 SPS     31 P2-VSS  36 V4
  2 VSHD  7 VSHD   12 LDR1   17 LDG1   22 LDB2   27 ?       32 P2-VCC  37 V3
  3 DCK   8 LDR5   13 LDG5   18 GND    23 LDB1   28 MOD     33 ?       38 V2
  4 LP    9 LDR4   14 LDG4   19 LDB5   24 SPL    29 VCOM    34 VDD5    39 V1
  5 PS    10 LDR3  15 LDG3   20 LDB4   25 CLS    30 P2-VEE  35 GND     40 V0
```

### GBA SP Display Socket

```
  1 VSHD 5 VSHD  9 LDR3   13 LDG4  17 GND   21 LDB2  25 SPS   29 P2VSS 33 U83
  2 DCK  6 GND   10 LDR2  14 LDG3  18 LDB5  22 LDB1  26 MOD   30 COM   34 VDD5
  3 LP   7 LDR5  11 LDR1  15 LDG2  19 LDB4  23 SPL   27 REVC  31 VDD5
  4 PS   8 LDR4  12 LDG5  16 LDG1  20 LDB3  24 CLS   28 P2VDD 32 GND
```

### GBA Micro Display Sockets

```
  __GBA Mirco display socket (P1)____________________________________
  1-PS    6-5bit  11-MD    16-5bit  21-5bit  26-CL             31-GND
  2-RV    7-5bit  12-SL    17-5bit  22-5bit  27-SS             32-GND
  3-GND   8-5bit  13-CK    18-5bit  23-5bit  28-via C5 to VR1  33-V10
  4-5bit  9-LP    14-GND   19-5bit  24-5bit  29-V5             34-V-5
  5-5bit  10-VD   15-5bit  20-GND   25-5bit  30-to VR1
  __GBA Mirco backlight socket (P3)__________________________________
  1-LC  2-LC  3-LA  4-LA
```

### NDS Upper/Lower Display Sockets

```
  ___NDS upper screen/upper backlight/speakers socket (P3)_____________________
  1-SPLO 7-PS2   13-LDR2  19-GND   25-LDG2  31-LDB2  37-MOD2  43-VDD15  49-SPRO
  2-SPLO 8-REV2  14-LDR1  20-DCLK2 26-LDG1  32-LDB1  38-GND   44-VDD-5  50-GND
  3-SSC2 9-GND   15-LDR0  21-GND   27-LDG0  33-LDB0  39-VDD5  45-VDD-10 51-GND
  4-ASC2 10-LDR5 16-LS2   22-LDG5  28-LDB5  34-GCK2  40-VDD10 46-LEDC2
  5-GND  11-LDR4 17-VSHD  23-LDG4  29-LDB4  35-GSP2  41-COM2  47-LEDA2
  6-SPL2 12-LDR3 18-DISP1 24-LDG3  30-LDB3  36-GND   42-GND   48-SPRO
  ___NDS lower screen socket (P4)______________________________________________
  1-SSC1 6-REV1  11-LDR2  16-DISP0 21-LDG4  26-LDB5  31-LDB0  36-GND  41-VDD15
  2-ASC1 7-GND   12-LDR1  17-SPL1  22-LDG3  27-LDB4  32-GCK1  37-?    42-VDD10
  3-GND  8-LDR5  13-LDR0  18-DCLK1 23-LDG2  28-LDB3  33-GSP1  38-VDD5 43-GND
  4-?    9-LDR4  14-LS1   19-GND   24-LDG1  29-LDB2  34-VSHD  39-COM1 44-VDD-5
  5-PS1  10-LDR3 15-VSHD  20-LDG5  25-LDG0  30-LDB1  35-MOD1  40-GND  45-VDD-10
  ___NDS lower backlight socket (P5)____   ___NDS touchscreen socket (P6)______
  1:LEDA1  2:LEDA1  3:LEDC1  4:LEDC1       1:Y-    2:X-    3:Y+    4:X+
```

### NDS-Lite Upper/Lower Display Sockets

```
  ___NDS-Lite upper screen/upper backlight/speakers socket (P3)________________
  1-VDD-5 6-MOD    11-LD2xx 16-LD2xx 21-LD2xx 26-LD2xx 31-LS   36-GND   41-SPRO
  2-VDD10 7-GSP    12-LD2xx 17-LD2xx 22-LD2xx 27-LD2xx 32-VSHD 37-COM2  42-SG
  3-VDD5  8-GCK    13-LD2xx 18-GND   23-LD2xx 28-GND   33-GND  38-LEDA2 43-SG
  4-GND   9-LD2xx  14-LD2xx 19-LD2xx 24-LD2xx 29-DCLK  34-xx2? 39-LEDC2 44-SPLO
  5-VSHD  10-LD2xx 15-LD2xx 20-LD2xx 25-LD2xx 30-SPL   35-REV  40-SPRO  45-SPLO
  ___NDS-Lite lower screen/lower backlight (P4)________________________________
  1-VDD-5 6-MOD    11-LD1xx 16-LD1xx 21-LD1xx 26-LD1xx 31-LS   36-GND
  2-VDD10 7-GSP    12-LD1xx 17-LD1xx 22-LD1xx 27-LD1xx 32-VSHD 37-COM1
  3-VDD5  8-GCK    13-LD1xx 18-GND   23-LD1xx 28-GND   33-GND  38-LEDA1
  4-GND   9-LD1xx  14-LD1xx 19-LD1xx 24-LD1xx 29-DCLK  34-xx1? 39-LEDC1
  5-VSHD  10-LD1xx 15-LD1xx 20-LD1xx 25-LD1xx 30-SPL   35-REV
  ___NDS-Lite touchscreen socket (P6)______   ___NDS-Lite white coax (P12)_____
  1:X-  2:Y-  3:X+  4:Y+                      Center:MICIN  Shield:GND
```

## <a name="pinoutspowerswitchesdcdcconvertersresetgenerators"></a>  Pinouts - Power Switches, DC/DC Converters, Reset Generators

### Advance Gameboy Power Switch (2-position slider, with two common pins)

### GBA SP Power Switch (same as GBA)

```
  1 via resistor to GND (OFF)
  2 VS (BT+) (ON)
  C VCC (to board)
```

### GBA Micro Power Switch

Same as GBA and GBA SP, but Pin 1 and 2 exchanged.

### Advance Gameboy Cartridge Slot Switch (integrated 4pin micro switch)

### GBA SP Cartridge Slot Switch (separate 4pin micro switch)

```
  C1 VDD35  (to S2 when PRESSED, to S1 when RELEASED)
  S1 VDD3   (to C2 when PRESSED, to C1 when RELEASED)
  C2 IN35   (to S1 when PRESSED)
  S2 VDD5   (to C1 when PRESSED)
```

Pressed=8bit DMG/MGB/CGB cart, Released=32bit GBA cart (or no cart inserted)

GBA: switch integrated in cart socket, GBA-SP: separate switch next to socket.

### Advance Gameboy Power Controller (M 121 514X) (U4)

```
  1-VIN    2-VOUT5  3-CSS5    4-VDRV5  5-GND    6-VDRV3    7-CSS3    8-VOUT3
  9-VCNT5  10-CSCP  11-REGEXT 12-VDD3  13-VDD2  14-/RESET  15-LOWBAT 16-VDD13
```

/RESET is passed to the CPU, and then forwarded to /RES pin on cart slot.

### Advance Gameboy LCD Regulator (AGB-REG IR3E09N) (U3)

```
  1  2  3    4      5   6   7   8   9   10   11  12  13  14  15  16   17     18
  ?  ?  REVC U3-COM V0  V1  ?   ?   ?   GND  ?   V2  ?   V3  V4  VDD5 U3-VDD ?
```

### GBA SP Power Controller 1 (S6403 AU227 9276) (U4)

```
  1-VCC    2-SCP1   3-SCP2 4-VDRV3 5-VOUT3/VDD3 6-VDD2  7-VOUT1/VDD1  8-VDRV1
  9-LOWBAT 10-VCNT5 11-LS5 12-?    13-GND       14-?    15-VOUT5/VDD5 16-VDRV5
```

### GBA SP Power Controller 2 (2253B 2808) (U5)

```
  1-TIN     2-U5C3     3-ADJ  4-U5VDD  5-VIN   6-?    7-U57  8-?
  9-to-C29  10-to-C30  11-?   12-GND   13-VS   14-S-  15-S+  16-U5OUT
```

### GBA Micro - Power Managment Device (U2)

- 1 via C43 to GND
- 2 via R24 to C34 to R25 back to U2.2
- 3 via C35 to GND
- 4 via C36 to GND
- 5
- 6 audio.in ? (see BP)
- 7   via C48 to GND
- 8   via R21 to C46 to C47 to C38 to R23 to phones
- 9  VL (to U4)
- 10  via R27 to C33 to C44 to C49 to R22 to phones
- 11  via C45 to GND
- 12 audio.in ? (see BP)
- 13  via C41 to GND
- 14 phones (switch)
- 15 phones (tip via R22)
- 16 phones (mid via R23)
- 17 VCS
- 18 SP
- 19 GND
- 20 LB
- 21 via C52 to GND
- 22 via C53 to GND
- 23 RS         (looks like RESET output)
- 24 to R37/C56 (looks like RESET input)
- 25
- 26
- 27 via C54 to V3
- 28 V3
- 29 GND
- 30 V3
- 31 VC
- 32 to C58
- 33 to R41/C58
- 34 GND
- 35
- 36 VC
- 37 VC
- 38
- 39 V5
- 40 GND
- 41 GND
- 42
- 43
- 44
- 45 B+
- 46 S-
- 47 S+
- 48

### GBA Micro - Volume/Backlight Level Up/Down Controller (U5)

```
  1-     5-GND  9-      13-XD      17-     21-     25-    29-
  2-     6-GND  10-     14-to U4.7 18-XR   22-CN   26-    30-
  3-     7-     11-XC   15-        19-V+   23-CNS  27-    31-BP
  4-LN   8-     12-GND  16-        20-V-   24-     28-V3  32-
```

### NDS Powermanagment Device (Mitsumi 3152A) (U3)

```
  1 R50-EXTB+        17               33 LEDC1           49 VCNT5
  2 R39-ORANGE       18               34 GND             50
  3 GND              19 VQ5           35 LEDC2           51 RST
  4                  20               36                 52
  5 Rxx-Q4           21               37 U10-LEDA2       53
  6 INS+             22 GND           38                 54
  7 INS-             23 VQ5           39 MIC.C53-AIN     55 VQ5
  8                  24               40 MIC.TSC.AUX     56 R24-SR
  9 VDET             25 VDD3.3        41 GND             57
  10 PVDD            26 GND           42 R38-RED         58 R22-SL
  11                 27 CL60-VDD3.3   43 R37-GREEN       59 GND
  12 PWSW            28 VSHD          44 VDD3.3          60 VR3.PIN2
  13                 29               45 PWM.SPI.CLK     61
  14 GND             30 VDD5          46 PWM.SPI.D       62
  15 GND             31 U9-LEDA1      47 PWM.SPI.Q       63
  16 VQ5             32               48 PWM.SPI.SEL     64 GND
```

### NDS-LITE Powermanagment Device (Mitsumi 3205B) (U3)

```
  1 SW               17               33 LEDC1           49 VCNT5
  2 R50-EXTB+        18               34 GND             50
  3 R39-ORANGE       19 VQ5           35 LEDC2           51 RST
  4 GND              20               36                 52
  5                  21               37 U10-LEDA2       53
  6 R30-Q4           22 GND           38                 54
  7 INS+             23 VQ5           39 MIC.C53-AIN     55 CL63-VQ5
  8 INS-             24               40 MIC.TSC.AUX     56 R24-SR
  9 VDET             25 VDD3.3        41 GND             57 SPRO
  10 PVDD            26 GND           42 R38-RED         58 SPLO
  11                 27 CL60-VDD3.3   43 R37-GREEN       59 R22-SL
  12 PWSW            28 VSHD          44 VDD3.3          60 GND
  13 GND             29               45 PWM.SPI.CLK     61 R79-VR3.PIN2
  14 GND             30 VDD5          46 PWM.SPI.D       62
  15 GND             31 U9-LEDA1      47 PWM.SPI.Q       63
  16 VQ5             32               48 PWM.SPI.SEL     64
```

### NDS-LITE Power Switch

```
  1 PWSW (grounded when switch is pulled)
  2 GND
  3 GND
  4 NC? (grounded when switch is not pulled)
```


## <a name="pinoutsvarious"></a>  Pinouts - Various

### Advance Gameboy 256Kbytes RAM 128Kx16bit (NEC D442012LGY-B85x-MJH) (wide)

### GBA SP 256Kbytes RAM 128Kx16bit (F 82D12160-10FN) (square)

```
  1 A15   7 A9    13 IC    19 A6   25 A0   31 D2   37 VCC  43 D15
  2 A14   8 A8    14 /UB   20 A5   26 /CE1 32 D10  38 D5   44 D8
  3 A13   9 NC    15 /LB   21 A4   27 GND  33 D3   39 D13  45 D16
  4 A12   10 NC   16 NC    22 A3   28 /OE  34 D11  40 D6   46 GND
  5 A11   11 /WE  17 NC    23 A2   29 D1   35 D4   41 D14  47 NC
  6 A10   12 CE2  18 A7    24 A1   30 D9   36 D12  42 D7   48 A16
```

Connection in GBA and GBA SP: IC-GND, /CE1-GND, CE2-VDD2, VCC-VDD2, Pin16-VDD2,
the other NC pins seem to be actually not connected, all other pins connect to
the corresponding Wxx CPU pins. Note: Both GBA and GBA SP have soldering points
for wide (12x18mm) and square (12x14mm) RAMs, so either could be used.

The GBA additionally contains 32K built-in WRAM, and built-in VRAM, so the
above 256K RAM chip is probably not used in 8bit classic/color gameboy mode.

Note: In the GBA Micro, the 256K RAM are contained on-chip in the CPU.

### Advance Gameboy Schematic Fragments

P2-VSS = VDD-15

VIN = VCC3 via R33

REGEXT (on my modified board, REGEXT underneath of my diodes)

/RES (OUT) (via R40)

/CS (via R39)

/WR (via R38)

SC (via Rxx)

SD (via Rxx)

SI (via Rxx)

SO (via Rxx)

DCK (via R36)

A-GND via CP4 (100uF) to GND (used speaker, and on headphone socket)

### GBA SP Schematic Fragments

P2VDD = VDD13

P2VSS = VDD15

/RES via R46

/CS via R45

/WR via R44

DCK via R20

VS=BT+

In my repaired GBA-SP: CK1 test-point is disconnected (instead GND'ed).

In my repaired GBA-SP: broken oscillator replaced

In my repaired GBA-SP: broken r1 1mOhm replaced (near oscillator)

In my repaired GBA-SP: broken EXT2 socket metal-spring/snapper removed

CL1 FIQ (near SW4)

CL2 ?

CL3 ?

CL4 VOUT1/VDD1 (near U4)

CL5 VOUT3/VDD3 (near U4)

CL6 VOUT5/VDD5 (near U4)

DL1-red (power low) ---R32--Q4--R6--

DL2-green (power good) ---Q6--LOWBAT/R34-VDD3

DL3-orange (charge) --R24--Q2--VIN/U57

P2VDD--VDD13

P2VSS--VDD15

S+ and S- are (almost) shortcut by R23 (1.0 ohm)

S+ via Q1 to VIN

VS via D1 to S-

A-GND via CP1 (100uF) to GND

U4 pin 12 to r6 (towards red led)

U4 pin 14 to D6---to U7

SC (CPU pin48) with R7 100K ohm pullup to VDD35

P35 via Q11 to SW (speaker disable)

### GBA SP Backlight-Button Schematic (U6,U8,Q12)

```
        ______                _____
  GND--|1 U8 6|-- U85        |     |--VDD5
  U82--|2    5|-- U85    U61-| Q12 |         U83  ------> to display
  U83--|3____4|-- U82        |_____|--Q12B   Q12B <------ from button
  U61--|1 U6 8|--VDD5    (X)---R51--VDD5    (X)---C70--GND
  U62--|2    7|--VDD5    U62---R49--VDD5    U61---R40--GND
  U62--|3    6|--(X)     Q12B--R39--VDD5    U82---R38--GND
  GND--|4____5|--NC?     Q12B--C69--VDD5    U85---R50--U62
```

## <a name="auxxboopctogbamultibootcable"></a>  AUX Xboo PC-to-GBA Multiboot Cable

Below describes how to connect a PC parallel port to the GBA link port,
allowing to upload small programs (max 256 KBytes) from no$gba's Utility menu
into real GBAs.

This is possible because the GBA BIOS includes a built-in function for
downloading & executing program code even when no cartridge is inserted.
The program is loaded to 2000000h and up in GBA memory, and must contain
cartridge header information just as for normal ROM cartridges (nintendo logo,
checksum, etc., plus some additional multiboot info).

### Basic Cable Connection

The general connection is very simple (only needs four wires), the only problem
is that you need a special GBA plug or otherwise need to solder wires directly
to the GBA mainboard (see Examples below).

```
  GBA  Name  Color                 SUBD CNTR Name
  2    SO    Red     ------------- 10   10   /ACK
  3    SI    Orange  ------------- 14   14   /AUTOLF
  5    SC    Green   ------------- 1    1    /STROBE
  6    GND   Blue    ------------- 19   19   GND
```

Optionally, also connect the following signals (see notes below):

```
  4    SD    Brown   ------------- 17   36   /SELECT  (double speed burst)
  3    SI    Orange  ----[===]---- 2..9 2..9 D0..7    (pull-up, 560 Ohm)
  5    SC    Green   ----[===]---- 2..9 2..9 D0..7    (pull-up, 560 Ohm)
  4    SD    Brown   ----[===]---- 2..9 2..9 D0..7    (pull-up, 560 Ohm)
  START  (mainboard) -----|>|----- 16   31   /INIT    (auto-reset, 1N4148)
  SELECT (mainboard) -----|>|----- 16   31   /INIT    (auto-reset, 1N4148)
  RESET  (mainboard) -----||------ 16   31   /INIT    (auto-reset, 300nF)
```

Notes: The GBA Pins are arranged from left to right as 2,4,6 in upper row, and
1,3,5 in lower row; outside view of GBA socket; flat side of socket upside. The
above "Colors" are as used in most or all standard Nintendo link cables, note
that Red/Orange will be exchanged at one end in cables with crossed SO/SI
lines. At the PC side, use the SUBD pin numbers when connecting to a 25-pin
SUBD plug, or CNTR pin numbers for 36-pin Centronics plug.

### Optional SD Connection (Double Speed Burst)

The SD line is used for Double Speed Burst transfers only, in case that you are
using a gameboy link plug for the connection, and if that plug does not have a
SD-pin (such like from older 8bit gameboy cables), then you may leave out this
connection. Burst Boot will then only work half as fast though.

### Optional Pull-Ups (Improves Low-to-High Transition Speed)

If your parallel port works only with medium or slow delay settings, try to
connect 560 Ohm resistors to SI/SC/SD inputs each, and the other resistor pin
to any or all of the parallel port data lines (no$gba outputs high to pins
2..9).

### Optional Reset Connection (CAUTION: Connection changed September 2004)

The Reset connection allows to automatically reset & upload data even if a
program in the GBA has locked up (or if you've loaded a program that does not
support nocash burst boot), without having to reset the GBA manually by
switching it off and on (and without having to press Start+Select if a
cartridge is inserted).

The two diodes should be 1N4148 or similar, the capacitor should be 300nF (eg.
three 100nF capacitors in parallel). The signals are labeled on the mainboard,
and can be found at following names / CPU pin numbers: RESET/CPU.125,
SELECT/TP2/CPU.126, START/TP3/CPU.127.

### Optional Power Supply Connection

Also, you may want to connect the power supply to parallel port data lines, see
chapter Power Supply for details.

### Transmission Speed

The first transfer will be very slow, and the GBA BIOS will display the boot
logo for at least 4 seconds, even if the transfer has completed in less time.
Once when you have uploaded a program with burst boot backdoor, further
transfers will be ways faster. The table below shows transfer times for 0KByte
- 256KByte files:

```
  Boot Mode_____Delay 0_______Delay 1_______Delay 2_____
  Double Burst  0.1s - 1.8s   0.1s - 3.7s   0.1s - 5.3s
  Single Burst  0.1s - 3.6s   0.1s - 7.1s   0.1s - 10.6s
  Normal Bios   4.0s - 9.0s   4.0s - 12.7s  4.0s - 16.3s
```

All timings measured on a 66MHz computer, best possible transmission speed
should be 150KBytes/second. Timings might slightly vary depending on the CPU
speed and/or operating system. Synchronization is done by I/O waitstates, that
should work even on faster computers. Non-zero delays are eventually required
for cables without pull-ups.

### Requirements

Beside for the cable and plugs, no special requirements.

The cable should work with all parallel ports, including old-fashioned
one-directional printer ports, as well as modern bi-directional EPP ports.
Transfer timings should work stable regardless of the PCs CPU speed (see above
though), and regardless of multitasking interruptions.

Both no$gba and the actual transmission procedure are using some 32bit code, so
that either one currently requires 80386SX CPUs or above.

### Connection Examples

As far as I can imagine, there are four possible methods how to connect the
cable to the GBA. The first two methods don't require to open the GBA, and the
other methods also allow to connect optional power supply and reset signal.

```
  1) Connect it to the GBA link port. Advantage: No need to
     open/modify the GBA. Disadvantage: You need a special plug,
     (typically gained by removing it from a gameboy link cable).
  2) Solder the cable directly to the GBA link port pins. Advantages:
     No plug required & no need to open the GBA. Disadvantages:
     You can't remove the cable, and the link port becomes unusable.
  3) Solder the cable directly to the GBA mainboard. Advantage: No
     plug required at the GBA side. Disadvantage: You'll always
     have a cable leaping out of the GBA even when not using it,
     unless you put a small standard plug between GBA and cable.
  4) Install a Centronics socket in the GBA (between power switch
     and headphone socket). Advantage: You can use a standard
     printer cable. Disadvantages: You need to cut a big hole into
     the GBAs battery box (which cannot be used anymore), the big
     cable might be a bit uncomfortable when holding the GBA.
```

Personally, I've decided to use the lastmost method as I don't like ending up
with hundreds of special cables for different purposes, and asides, it's been
fun to damage the GAB as much as possible.

### Note

The above used PC parallel port signals are typically using 5V=HIGH while GBA
link ports deal with 3V=HIGH. From my experiences, the different voltages do
not cause communication problems (and do not damage the GBA and/or PC
hardware), and after all real men don't care about a handful of volts, however,
use at own risk.

## <a name="auxxbooflashcardupload"></a>  AUX Xboo Flashcard Upload

### Flashcard Upload

Allows to write data to flashcards which are plugged into GBA cartridge slot,
cartridge is automatically started after writing. On initial power-up, hold
down START+SELECT to prevent the GBA from booting the old program in the
flashcard.

The Upload function in Utility menu uses flashcard mode for files bigger than
256KB (otherwise uses multiboot mode automatically). Also, there's a separate
Upload to Flashcard function in Remote Access submenu, allowing to write files
of 256KB or less to flashcard if that should be desired.

### Supported Flashcards

Function currently tested with Visoly Flash Advance (FA) 256Mbit (32MB) Turbo
cartridge. Should also work with older FA versions. Please let me know if you
are using other flashcards which aren't yet supported.

### Flashcard Performance

Writing to flashcards may become potentially slow because of chip erase/write
times, cable transmission time, and the sheer size of larger ROM-images.
However, developers whom are testing different builts of their project usually
won't need to rewrite the complete flashcard, Xboo uses a highspeed checksum
mechanism (16MB/sec) to determine which flashcard sector(s) have changed, and
does then re-write only these sector(s).

To eliminate transmission time, data transfer takes place in the erase phases.
Erase/write time depends on the flashcard type, should be circa 1-2 seconds per
256KB sector. Because the cartridge is programmed directly in the GBA there's
no need to remove it from the GBA when writing to it.

### Developers Advice

Locate your program fragments at fixed addresses, for example, code and data
blocks each aligned to 64K memory boundaries, so that data remains at the same
location even when the size of code changes. Fill any blank spaces by value FFh
for faster write time. Reduce the size of your ROM-image by efficient memory
use (except for above alignment trick). Include the burst boot backdoor in your
program, allowing to re-write the flashcard directly without resetting the GBA.

### Lamers Advice

Xboo Flashcard support does not mean to get lame & to drop normal multiboot
support, if your program fits into 256KB then make it \<both> flashcard
\<and> multiboot compatible - multiboot reduces upload time, increases
your flashcard lifetime, and will also work for people whom don't own
flashcards.

## <a name="auxxbooburstbootbackdoor"></a>  AUX Xboo Burst Boot Backdoor

When writing Xboo compatible programs, always include a burst boot "backdoor",
this will allow yourself (and other people) to upload programs much faster as
when using the normal GBA BIOS multiboot function. Aside from the improved
transmission speed, there's no need to reset the GBA each time (eventually
manually if you do not have reset connect), without having to press
Start+Select (if cartridge inserted), and, most important, the time-consuming
nintendo-logo intro is bypassed.

### The Burst Boot Protocol

In your programs IRQ handler, add some code that watches out for burst boot IRQ
requests. When sensing a burst boot request, download the actual boot
procedure, and pass control to that procedure.

```
  Send (PC)    Reply (GBA)
  "BRST"       "BOOT"        ;request burst, and reply <prepared> for boot
  <wait 1/16s> <process IRQ> ;long delay, allow slave to enter IRQ handler
  llllllll     "OKAY"        ;send length in bytes, reply <ready> to boot
  dddddddd     --------      ;send data in 32bit units, reply don't care
  cccccccc     cccccccc      ;exchange crc (all data units added together)
```

Use normal mode, 32bit, external clock for all transfers. The received
highspeed loader (currently approx. 180h bytes) is to be loaded to and started
at 3000000h, which will then handle the actual download operation.

Below is an example program which works with multiboot, burstboot, and as
normal rom/flashcard. The source can be assembled with a22i (the no$gba
built-in assembler, see no$gba utility menu). When using other/mainstream
assemblers, you'll eventually have to change some directives, convert numbers
from NNNh into 0xNNN format, and define the origin somewhere in linker/makefile
instead of in source code.

```
 .arm            ;select 32bit ARM instruction set
 .gba            ;indicate that it's a gameboy advance program
 .fix            ;automatically fix the cartridge header checksum
 org 2000000h    ;origin in RAM for multiboot-cable/no$gba-cutdown programs
 ;------------------
 ;cartridge header/multiboot header
  b     rom_start                ;-rom entry point
  dcb   ...insert logo here...   ;-nintento logo (156 bytes)
  dcb   'XBOO SAMPLE '           ;-title (12 bytes)
  dcb   0,0,0,0,  0,0            ;-game code (4 bytes), maker code (2 bytes)
  dcb   96h,0,0                  ;-fixed value 96h, main unit code, device type
  dcb   0,0,0,0,0,0,0            ;-reserved (7 bytes)
  dcb   0                        ;-software version number
  dcb   0                        ;-header checksum (set by .fix)
  dcb   0,0                      ;-reserved (2 bytes)
  b     ram_start                ;-multiboot ram entry point
  dcb   0,0                      ;-multiboot reserved bytes (destroyed by BIOS)
  dcb   0,0                      ;-blank padded (32bit alignment)
 ;------------------
 irq_handler:  ;interrupt handler (note: r0-r3 are pushed by BIOS)
  mov    r1,4000000h             ;\get I/O base address,
  ldr    r0,[r1,200h] ;IE/IF     ; read IE and IF,
  and    r0,r0,r0,lsr 16         ; isolate occurred AND enabled irqs,
  add    r3,r1,200h   ;IF        ; and acknowledge these in IF
  strh   r0,[r3,2]               ;/
  ldrh   r3,[r1,-8]              ;\mix up with BIOS irq flags at 3007FF8h,
  orr    r3,r3,r0                ; aka mirrored at 3FFFFF8h, this is required
  strh   r3,[r1,-8]              ;/when using the (VBlank-)IntrWait functions
  and    r3,r0,80h ;IE/IF.7 SIO  ;\
  cmp    r3,80h                  ; check if it's a burst boot interrupt
  ldreq  r2,[r1,120h] ;SIODATA32 ; (if interrupt caused by serial transfer,
  ldreq  r3,[msg_brst]           ; and if received data is "BRST",
  cmpeq  r2,r3                   ; then jump to burst boot)
  beq    burst_boot              ;/
  ;... insert your own interrupt handler code here ...
  bx     lr                      ;-return to the BIOS interrupt handler
 ;------------------
 burst_boot:     ;requires incoming r1=4000000h
  ;... if your program uses DMA, disable any active DMA transfers here ...
  ldr   r4,[msg_okay]            ;\
  bl    sio_transfer             ; receive transfer length/bytes & reply "OKAY"
  mov   r2,r0 ;len               ;/
  mov   r3,3000000h   ;dst       ;\
  mov   r4,0  ;crc               ;
 @@lop:                          ;
  bl    sio_transfer             ; download burst loader to 3000000h and up
  stmia [r3]!,r0      ;dst       ;
  add   r4,r4,r0      ;crc       ;
  subs  r2,r2,4       ;len       ;
  bhi   @@lop                    ;/
  bl    sio_transfer             ;-send crc value to master
  b     3000000h  ;ARM state!    ;-launch actual transfer / start the loader
 ;------------------
 sio_transfer:  ;serial transfer subroutine, 32bit normal mode, external clock
  str   r4,[r1,120h]  ;siodata32 ;-set reply/send data
  ldr   r0,[r1,128h]  ;siocnt    ;\
  orr   r0,r0,80h                ; activate slave transfer
  str   r0,[r1,128h]  ;siocnt    ;/
 @@wait:                         ;\
  ldr   r0,[r1,128h]  ;siocnt    ; wait until transfer completed
  tst   r0,80h                   ;
  bne   @@wait                   ;/
  ldr   r0,[r1,120h]  ;siodata32 ;-get received data
  bx    lr
 ;---
 msg_boot dcb 'BOOT'     ;\
 msg_okay dcb "OKAY"     ; ID codes for the burstboot protocol
 msg_brst dcb "BRST"     ;/
 ;------------------
 download_rom_to_ram:
  mov  r0,8000000h  ;src/rom     ;\
  mov  r1,2000000h  ;dst/ram     ;
  mov  r2,40000h/16 ;length      ; transfer the ROM content
 @@lop:                          ; into RAM (done in units of 4 words/16 bytes)
  ldmia [r0]!,r4,r5,r6,r7        ; currently fills whole 256K of RAM,
  stmia [r1]!,r4,r5,r6,r7        ; even though the proggy is smaller
  subs  r2,r2,1                  ;
  bne   @@lop                    ;/
  sub   r15,lr,8000000h-2000000h ;-return (retadr rom/8000XXXh -> ram/2000XXXh)
 ;------------------
 init_interrupts:
  mov  r4,4000000h               ;-base address for below I/O registers
  ldr  r0,=irq_handler           ;\install IRQ handler address
  str  r0,[r4,-4]   ;IRQ HANDLER ;/at 3FFFFFC aka 3007FFC
  mov  r0,0008h                  ;\enable generating vblank irqs
  strh r0,[r4,4h]   ;DISPSTAT    ;/
  mrs  r0,cpsr                   ;\
  bic  r0,r0,80h                 ; cpu interrupt enable (clear i-flag)
  msr  cpsr,r0                   ;/
  mov  r0,0                      ;\
  str  r0,[r4,134h] ;RCNT        ; init SIO normal mode, external clock,
  ldr  r0,=5080h                 ; 32bit, IRQ enable, transfer started
  str  r0,[r4,128h] ;SIOCNT      ; output "BOOT" (indicate burst boot prepared)
  ldr  r0,[msg_boot]             ;
  str  r0,[r4,120h] ;SIODATA32   ;/
  mov  r0,1                      ;\interrupt master enable
  str  r0,[r4,208h] ;IME=1       ;/
  mov  r0,81h                    ;\enable execution of vblank IRQs,
  str  r0,[r4,200h] ;IE=81h      ;/and of SIO IRQs (burst boot)
  bx   lr
 ;------------------
 rom_start:   ;entry point when booted from flashcart/rom
  bl   download_rom_to_ram       ;-download ROM to RAM (returns to ram_start)
 ram_start:   ;entry point for multiboot/burstboot
  mov  r0,0feh                   ;\reset all registers, and clear all memory
  swi  10000h ;RegisterRamReset  ;/(except program code in wram at 2000000h)
  bl   init_interrupts           ;-install burst boot irq handler
  mov  r4,4000000h               ;\enable video,
  strh r4,[r4,000h] ;DISPCNT     ;/by clearing the forced blank bit
 @@mainloop:
  swi  50000h ;VBlankIntrWait    ;-wait one frame (cpu in low power mode)
  mov  r5,5000000h               ;\increment the backdrop palette color
  str  r8,[r5]                   ; (ie. display a blinking screen)
  add  r8,r8,1                   ;/
  b    @@mainloop
 ;------------------
 .pool
 end
```

## <a name="aboutthisdocument"></a>  About this Document

### About

GBATEK written 2001-2014 by Martin Korth, programming specs for the GBA and NDS
hardware, I've been trying to keep the specs both as short as possible, and as
complete as possible. The document is part of the no$gba debuggers built-in
help text.

### Updates

The standalone docs in TXT and HTM format are updated when having added any
major changes to the document. The no$gba built-in version will be updated more
regularly, including for minor changes, along with all no$gba updates.

### Homepage

http://problemkaputt.de/gba.htm - no$gba emulator homepage (freeware)

http://problemkaputt.de/gba-dev.htm - no$gba debugger homepage

http://problemkaputt.de/gbapics.htm - no$gba debugger screenshots

http://problemkaputt.de/gbatek.htm - gbatek html version

http://problemkaputt.de/gbatek.txt - gbatek text version

### Feedback

If you find any information in this document to be misleading, incomplete, or
incorrect, please say something! My spam-shielded email address is found at:

http://problemkaputt.de/email.htm - contact

Mail from programmers only, please. No gaming questions, thanks.

### Credits

Thanks for GBATEK fixes, and for info about GBA and NDS hardware,

- Jasper Vijn

- Remi Veilleux (DS video details)

- Randy Linden

- Sebastian Rasmussen

- Stephen Stair (DS Wifi)

- Cue (DS Firmware bits and bytes)

- Tim Seidel (DS Wifi RF2958 datasheet)

- Damien Good (DS Bios Dumping, and lots of e-Reader info)

- Kenobi and Dualscreenman (lots of ARDS/CBDS cheat info)

- Flubba (GBA X/Y-Axis tilt sensor, and GBA Gameboy Player info)

- DarkFader (DS Key2)

- Dstek by neimod (DS Sound)

- Christian Auby

- Jeff Frohwein

- NDSTech Wiki, http://www.bottledlight.com/ds/ (lots of DS info)

### Formatting

TXT is 80 columns, TXT is 80 columns, TXT is 80 columns.

Don't trust anything else. Never.

## <a name="index"></a>  Index

- [Contents](#contents)
- [GBA Reference](#gbareference)
- [GBA Technical Data](#gbatechnicaldata)
- [GBA Memory Map](#gbamemorymap)
- [GBA I/O Map](#gbaiomap)
- [GBA LCD Video Controller](#gbalcdvideocontroller)
- [LCD I/O Display Control](#lcdiodisplaycontrol)
- [LCD I/O Interrupts and Status](#lcdiointerruptsandstatus)
- [LCD I/O BG Control](#lcdiobgcontrol)
- [LCD I/O BG Scrolling](#lcdiobgscrolling)
- [LCD I/O BG Rotation/Scaling](#lcdiobgrotationscaling)
- [LCD I/O Window Feature](#lcdiowindowfeature)
- [LCD I/O Mosaic Function](#lcdiomosaicfunction)
- [LCD I/O Color Special Effects](#lcdiocolorspecialeffects)
- [LCD VRAM Overview](#lcdvramoverview)
- [LCD VRAM Character Data](#lcdvramcharacterdata)
- [LCD VRAM BG Screen Data Format (BG Map)](#lcdvrambgscreendataformatbgmap)
- [LCD VRAM Bitmap BG Modes](#lcdvrambitmapbgmodes)
- [LCD OBJ - Overview](#lcdobjoverview)
- [LCD OBJ - OAM Attributes](#lcdobjoamattributes)
- [LCD OBJ - OAM Rotation/Scaling Parameters](#lcdobjoamrotationscalingparameters)
- [LCD OBJ - VRAM Character (Tile) Mapping](#lcdobjvramcharactertilemapping)
- [LCD Color Palettes](#lcdcolorpalettes)
- [LCD Dimensions and Timings](#lcddimensionsandtimings)
- [GBA Sound Controller](#gbasoundcontroller)
- [GBA Sound Channel 1 - Tone & Sweep](#gbasoundchannel1tonesweep)
- [GBA Sound Channel 2 - Tone](#gbasoundchannel2tone)
- [GBA Sound Channel 3 - Wave Output](#gbasoundchannel3waveoutput)
- [GBA Sound Channel 4 - Noise](#gbasoundchannel4noise)
- [GBA Sound Channel A and B - DMA Sound](#gbasoundchannelaandbdmasound)
- [GBA Sound Control Registers](#gbasoundcontrolregisters)
- [GBA Comparison of CGB and GBA Sound](#gbacomparisonofcgbandgbasound)
- [GBA Timers](#gbatimers)
- [GBA DMA Transfers](#gbadmatransfers)
- [GBA Communication Ports](#gbacommunicationports)
- [SIO Normal Mode](#sionormalmode)
- [SIO Multi-Player Mode](#siomultiplayermode)
- [SIO UART Mode](#siouartmode)
- [SIO JOY BUS Mode](#siojoybusmode)
- [SIO General-Purpose Mode](#siogeneralpurposemode)
- [SIO Control Registers Summary](#siocontrolregisterssummary)
- [GBA Wireless Adapter](#gbawirelessadapter)
- [GBA Wireless Adapter Games](#gbawirelessadaptergames)
- [GBA Wireless Adapter Login](#gbawirelessadapterlogin)
- [GBA Wireless Adapter Commands](#gbawirelessadaptercommands)
- [GBA Wireless Adapter Component Lists](#gbawirelessadaptercomponentlists)
- [GBA Infrared Communication](#gbainfraredcommunication)
- [GBA Keypad Input](#gbakeypadinput)
- [GBA Interrupt Control](#gbainterruptcontrol)
- [GBA System Control](#gbasystemcontrol)
- [GBA GamePak Prefetch](#gbagamepakprefetch)
- [GBA Cartridges](#gbacartridges)
- [GBA Cartridge Header](#gbacartridgeheader)
- [GBA Cartridge ROM](#gbacartridgerom)
- [GBA Cart Backup IDs](#gbacartbackupids)
- [GBA Cart Backup SRAM/FRAM](#gbacartbackupsramfram)
- [GBA Cart Backup EEPROM](#gbacartbackupeeprom)
- [GBA Cart Backup Flash ROM](#gbacartbackupflashrom)
- [GBA Cart Backup DACS](#gbacartbackupdacs)
- [GBA Cart I/O Port (GPIO)](#gbacartioportgpio)
- [GBA Cart Real-Time Clock (RTC)](#gbacartrealtimeclockrtc)
- [GBA Cart Solar Sensor](#gbacartsolarsensor)
- [GBA Cart Tilt Sensor](#gbacarttiltsensor)
- [GBA Cart Gyro Sensor](#gbacartgyrosensor)
- [GBA Cart Rumble](#gbacartrumble)
- [GBA Cart e-Reader](#gbacartereader)
- [GBA Cart e-Reader Overview](#gbacartereaderoverview)
- [GBA Cart e-Reader I/O Ports](#gbacartereaderioports)
- [GBA Cart e-Reader Dotcode Format](#gbacartereaderdotcodeformat)
- [GBA Cart e-Reader Data Format](#gbacartereaderdataformat)
- [GBA Cart e-Reader Program Code](#gbacartereaderprogramcode)
- [GBA Cart e-Reader API Functions](#gbacartereaderapifunctions)
- [GBA Cart e-Reader VPK Decompression](#gbacartereadervpkdecompression)
- [GBA Cart e-Reader Error Correction](#gbacartereadererrorcorrection)
- [GBA Cart e-Reader File Formats](#gbacartereaderfileformats)
- [GBA Cart Unknown Devices](#gbacartunknowndevices)
- [GBA Cart Protections](#gbacartprotections)
- [GBA Flashcards](#gbaflashcards)
- [GBA Cheat Devices](#gbacheatdevices)
- [GBA Cheat Codes - General Info](#gbacheatcodesgeneralinfo)
- [GBA Cheat Codes - Codebreaker/Xploder](#gbacheatcodescodebreakerxploder)
- [GBA Cheat Codes - Gameshark/Action Replay V1/V2](#gbacheatcodesgamesharkactionreplayv1v2)
- [GBA Cheat Codes - Pro Action Replay V3](#gbacheatcodesproactionreplayv3)
- [GBA Gameboy Player](#gbagameboyplayer)
- [GBA Unpredictable Things](#gbaunpredictablethings)
- [LZ Decompression Functions](#lzdecompressionfunctions)
- [ARM CPU Reference](#armcpureference)
- [ARM CPU Overview](#armcpuoverview)
- [ARM CPU Register Set](#armcpuregisterset)
- [ARM CPU Flags & Condition Field (cond)](#armcpuflagsconditionfieldcond)
- [ARM CPU 26bit Memory Interface](#armcpu26bitmemoryinterface)
- [ARM CPU Exceptions](#armcpuexceptions)
- [ARM CPU Memory Alignments](#armcpumemoryalignments)
- [ARM Instruction Summary](#arminstructionsummary)
- [ARM Opcodes: Branch and Branch with Link (B, BL, BX, BLX, SWI, BKPT)](#armopcodesbranchandbranchwithlinkbblbxblxswibkpt)
- [ARM Opcodes: Data Processing (ALU)](#armopcodesdataprocessingalu)
- [ARM Opcodes: Multiply and Multiply-Accumulate (MUL, MLA)](#armopcodesmultiplyandmultiplyaccumulatemulmla)
- [ARM Opcodes: Special ARM9 Instructions (CLZ, QADD/QSUB)](#armopcodesspecialarm9instructionsclzqaddqsub)
- [ARM Opcodes: PSR Transfer (MRS, MSR)](#armopcodespsrtransfermrsmsr)
- [ARM Opcodes: Memory: Single Data Transfer (LDR, STR, PLD)](#armopcodesmemorysingledatatransferldrstrpld)
- [ARM Opcodes: Memory: Halfword, Doubleword, and Signed Data Transfer](#armopcodesmemoryhalfworddoublewordandsigneddatatransfer)
- [ARM Opcodes: Memory: Block Data Transfer (LDM, STM)](#armopcodesmemoryblockdatatransferldmstm)
- [ARM Opcodes: Memory: Single Data Swap (SWP)](#armopcodesmemorysingledataswapswp)
- [ARM Opcodes: Coprocessor Instructions (MRC/MCR, LDC/STC, CDP, MCRR/MRRC)](#armopcodescoprocessorinstructionsmrcmcrldcstccdpmcrrmrrc)
- [THUMB Instruction Summary](#thumbinstructionsummary)
- [THUMB Opcodes: Register Operations (ALU, BX)](#thumbopcodesregisteroperationsalubx)
- [THUMB Opcodes: Memory Load/Store (LDR/STR)](#thumbopcodesmemoryloadstoreldrstr)
- [THUMB Opcodes: Memory Addressing (ADD PC/SP)](#thumbopcodesmemoryaddressingaddpcsp)
- [THUMB Opcodes: Memory Multiple Load/Store (PUSH/POP and LDM/STM)](#thumbopcodesmemorymultipleloadstorepushpopandldmstm)
- [THUMB Opcodes: Jumps and Calls](#thumbopcodesjumpsandcalls)
- [ARM Pseudo Instructions and Directives](#armpseudoinstructionsanddirectives)
- [ARM CP14 ICEbreaker Debug Communications Channel](#armcp14icebreakerdebugcommunicationschannel)
- [ARM CP15 System Control Coprocessor](#armcp15systemcontrolcoprocessor)
- [ARM CP15 Overview](#armcp15overview)
- [ARM CP15 ID Codes](#armcp15idcodes)
- [ARM CP15 Control Register](#armcp15controlregister)
- [ARM CP15 Memory Managment Unit (MMU)](#armcp15memorymanagmentunitmmu)
- [ARM CP15 Protection Unit (PU)](#armcp15protectionunitpu)
- [ARM CP15 Cache Control](#armcp15cachecontrol)
- [ARM CP15 Tightly Coupled Memory (TCM)](#armcp15tightlycoupledmemorytcm)
- [ARM CP15 Misc](#armcp15misc)
- [ARM CPU Instruction Cycle Times](#armcpuinstructioncycletimes)
- [ARM CPU Versions](#armcpuversions)
- [ARM CPU Data Sheet](#armcpudatasheet)
- [BIOS Functions](#biosfunctions)
- [BIOS Function Summary](#biosfunctionsummary)
- [BIOS Differences between GBA and NDS functions](#biosdifferencesbetweengbaandndsfunctions)
- [BIOS Arithmetic Functions](#biosarithmeticfunctions)
- [BIOS Rotation/Scaling Functions](#biosrotationscalingfunctions)
- [BIOS Decompression Functions](#biosdecompressionfunctions)
- [BIOS Memory Copy](#biosmemorycopy)
- [BIOS Halt Functions](#bioshaltfunctions)
- [BIOS Reset Functions](#biosresetfunctions)
- [BIOS Misc Functions](#biosmiscfunctions)
- [BIOS Multi Boot (Single Game Pak)](#biosmultibootsinglegamepak)
- [BIOS Sound Functions](#biossoundfunctions)
- [BIOS SHA1 Functions (DSi only)](#biossha1functionsdsionly)
- [BIOS RSA Functions (DSi only)](#biosrsafunctionsdsionly)
- [BIOS RSA Basics](#biosrsabasics)
- [BIOS RSA Pseudo Code](#biosrsapseudocode)
- [BIOS RAM Usage](#biosramusage)
- [BIOS Dumping](#biosdumping)
- [External Connectors](#externalconnectors)
- [AUX GBA Game Pak Bus](#auxgbagamepakbus)
- [AUX DS Game Card Slot](#auxdsgamecardslot)
- [AUX Link Port](#auxlinkport)
- [AUX Sound/Headphone Socket and Battery/Power Supply](#auxsoundheadphonesocketandbatterypowersupply)
- [AUX DSi SD/MMC Pin-Outs](#auxdsisdmmcpinouts)
- [AUX Opening the GBA](#auxopeningthegba)
- [AUX Mainboard](#auxmainboard)
- [AUX DSi Component Lists](#auxdsicomponentlists)
- [AUX DSi Internal Connectors](#auxdsiinternalconnectors)
- [AUX DSi Chipset Pinouts](#auxdsichipsetpinouts)
- [Pinouts - CPU - Signal Summary](#pinoutscpusignalsummary)
- [Pinouts - CPU - Pinouts](#pinoutscpupinouts)
- [Pinouts - Audio Amplifiers](#pinoutsaudioamplifiers)
- [Pinouts - LCD Cables](#pinoutslcdcables)
- [Pinouts - Power Switches, DC/DC Converters, Reset Generators](#pinoutspowerswitchesdcdcconvertersresetgenerators)
- [Pinouts - Wifi](#pinoutswifi)
- [Pinouts - Various](#pinoutsvarious)
- [AUX Xboo PC-to-GBA Multiboot Cable](#auxxboopctogbamultibootcable)
- [AUX Xboo Flashcard Upload](#auxxbooflashcardupload)
- [AUX Xboo Burst Boot Backdoor](#auxxbooburstbootbackdoor)
- [About this Document](#aboutthisdocument)
