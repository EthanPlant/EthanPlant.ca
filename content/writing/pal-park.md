+++
title = "How Does Pal Park Actually Work?"
subtitle = "Reverse engineering one of Pokemon's most interesting cartridge interactions"
date = 2026-11-25

[taxonomies]
tags = ["pokemon", "reverse-engineering", "systems-programming"]
+++
One of my favourite features of the original Nintendo DS was its ability to interact with Game Boy Advance cartridges. Although the GBA slot was primarily intended to provide backwards compatibility, developers could also access it from within their DS games. Pokemon made particularly extensive use of this functionality throughout Generation IV, allowing players to encounter certain Pokemon depending on which GBA cartridge was inserted and, more importantly, permanently migrate Pokemon from Generation III via Pal Park.

When Nintendo released the DSi in 2008, it removed the GBA slot. While the DSi remained perfectly capable of running the Generation IV games, it lost the ability to migrate Pokemon from their predecessors. This functionality was never officially restores, and even the 3DS, which otherwise provides a convenient way of playing most of the series' history, has no equivalent mechanism for performing these migrations.

I've been working on a project named Poke Transporter DSi, the eventual goal of which is to restore this functionality through [nds-bootstrap](https://github.com/DS-Homebrew/nds-bootstrap) by allowing Generation IV games to interact with GBA ROMs and saves stored on the DSi's SD card. What differentiates this project from an external save editor like PkHeX or PKSM is that I don't intend to implement any of the Pokemon migration logic itself. I want Pokemon Platinum to perform its own migration, using its original interface, its original validation logic, and its original conversion routines, entirely unaware that the cartridge it believes its communicating with doesn't physically exist.

Before implementing any of this, however, I needed to understand exactly what that communication entailed. Over the course of this investigation, I've worked through the Pokemon Platinum and NitroSDK decompilations, modified melonDS to instrument the GBA cartridge interface, captured nearly a million transactions during a successful migration, and reconstructed the contents of a Generation III save from nothing more than a log of memory accesses.

The results were considerably more interesting than I initially anticipated.

## Understanding the Nintendo DS Cartridge Interface
To understand how Pal Park works, we first need to understand how the Nintendo DS interacts with Game Boy Advance cartridges. The original DS has two cartridge interfaces, conventionally referred to as Slot-1 and Slot-2. Slot-1 is the interface used by Nintendo DS cartridges, while Slot-2 is the interface used by Game Boy Advance cartridges and various accessories, including the Rumble Pak and the memory expansion cartridge used by the Nintendo DS Browser.

The distinction between these interfaces is rather important from a software perspective. Nintendo DS cartridges are accessed through a command-oriented interface, whereas the GBA cartridge interface is exposed directly through the processor's memory map. Reading data from a GBA cartridge is therefore, under the appropriate conditions, conceptually equivalent to reading from a particular memory address.

The two regions we're primarily interested in are:

| Address range | Purpose |
| :--- | :--- |
| `0x08000000–0x09FFFFFF` | GBA cartridge ROM |
| `0x0A000000–0x0A00FFFF` | Relevant GBA backup memory window |

The first region provides a 32 MiB address space for the cartridge ROM, while the latter exposes the backup memory used to store persistent game data. The precise behaviour of that backup memory depends on the cartridge, as GBA games could use several different save technologies, including SRAM, EEPROM and Flash.

Complicating matters somewhat is the fact that the Nintendo DS contains two processors: an ARM946E-S, commonly referred to as ARM9, and an ARM7TDMI, commonly referred to as ARM7. Both processors can access Slot-2, but they cannot independently drive the cartridge bus at the same time. Ownership of the interface is controlled through bit 7 of the external memory control register, `EXMEMCNT`, located at `0x04000204`.

When this bit is cleared, ARM9 owns the GBA cartridge interface. When it is set, ARM7 owns it. The register also controls various timing parameters associated with cartridge accesses, meaning that interacting with a cartridge involves considerably more than simply dereferencing a pointer. Software needs to coordinate ownership, configure appropriate access timings, and ensure that the other processor isn't attempting to access the cartridge concurrently.

Nintendo's official development library, NitroSDK, abstracts much of this complexity through its cartridge library, conventionally prefixed with `CTRDG`. As it turns out, this abstraction is probably the single most useful thing for the purposes of Poke Transporter DSi.

## Finding Pal Park in Pokemon Platinum
One of the wonderful things about developing homebrew for older Nintendo hardware today is the amount of reverse engineering that has already been performed by the community. The [Pokemon Platinum decompilation project](https://github.com/pret/pokeplatinum) provides a human-readable reconstruction of the original game's source code, allowing me to investigate much of its behaviour without having to begin by manually disassembling hundreds of kilobytes of ARM instructions.

Most of the relevant code resides in ARM9 overlay 97, identified as main_menu in the decompilation. The original Nintendo DS only has 4 MiB of main RAM, and commercially released games frequently make use of overlays to avoid keeping their entire executable code resident in memory. Overlays contain code and data that can be loaded into RAM when needed and subsequently unloaded, allowing different parts of the game to reuse the same memory.

The particularly relevant source files within this overlay are `main_menu.c`, which determines whether the migration option should appear on the main menu; `gba_migrator.c`, which implements the application responsible for selecting and migrating Pokemon; and `ov97_02235D18.c`, which contains much of the game's implementation of the Generation III cartridge and save interface.

Even more conveniently, the Platinum decompilation depends on a reconstruction of NitroSDK. This means that we can follow the migration process all the way from the main menu's application logic down to the individual byte writes that issue commands to the GBA cartridge's Flash chip.

The relevant portions of the call graph look approximately like this:
```
MainMenu_Main
└── DetectGBAGame
    └── ov97_02235D2C
        ├── ov97_02235DC8
        │   ├── CTRDG_IsAgbCartridge
        │   ├── CTRDG_GetAgbGameCode
        │   ├── CTRDG_Enable
        │   └── CTRDG_CpuCopy32
        ├── Read GBA game metadata
        └── ov97_02235D18
            └── CTRDG_IdentifyAgbBackup
```

Once the player selects the migration option, the main menu exits and queues the application described by `gGBAMigratorAppTemplate`. This application is also part of overlay 97 and provides its own initialization, main-loop and cleanup functions.

One important consideration for Poke Transporter DSi is that overlay code is not necessarily resident throughout the lifetime of the game. Patching the migration application directly would consequently require accounting for the game's overlay-loading behaviour. Intercepting the resident NitroSDK functions, assuming they provide sufficient coverage of the cartridge interface, would potentially allow us to avoid much of this complexity.

## How Platinum Knows You Have Emerald Inserted
The first interaction with the GBA cartridge actually occurs considerably earlier than the main menu. During initialization, NitroSDK establishes information about the cartridge shared between ARM9 and ARM7, including whether a cartridge is present, whether it is a GBA cartridge, and its identifying information.

ARM9 reads the relevant portion of the cartridge header and initializes shared module information before communicating with ARM7 through the DS's interprocessor communication facilities, known within NitroSDK as PXI. ARM7 performs its own validation of the cartridge, including checking the Nintendo logo embedded within its ROM.

The two processors subsequently use this cached information to detect whether the cartridge has been removed or swapped out. This is particularly important because the cartridge interface is memory-mapped: unlike a conventional file descriptor, a pointer into cartridge memory doesn't automatically become invalid in a way that software can conveniently handle when the cartridge is removed.

The main menu then performs a separate, game-specific identification process. Platinum contains a table of 30 supported GBA game codes, accounting for the five Generation III games across their six supported languages. These identifiers are four-character sequences stored at offset `0xAC` within the standard GBA ROM header.

For the English releases, the identifiers are
| Game | Game Code|
| :--- | ---: |
| Pokemon Ruby | AXVE |
| Pokemon Sapphire | AXPE |
| Pokemon FireRed | BPRE |
| Pokemon LeafGreen | BPGE |
| Pokemon Emerald | BPEE |

Because the ARM processors operate in little-endian mode, the four bytes comprising Emerald's game code, `42 50 45 45`, can also be represented as the integer `0x45455042`.

Once Platinum has obtained the game code through `CTRDG_GetAgbGameCode()` and determined that it corresponds to a supported game, it enables cartridge access and copies the first 192 bytes of the ROM into RAM using NitroSDK:
```C
CTRDG_CpuCopy32(
    (void *)0x08000000,
    header,
    0xC0
);
```

The game then independently validates the cartridge header. It compares the 156-byte Nintendo logo at offsets `0x04–0x9F` against the expected contents and calculates the header complement, which is stored at offset `0xBD`.

The checksum algorithm is effectively equivalent to:
```C
uint8_t calculate_header_checksum(const uint8_t *header)
{
    uint8_t checksum = 0;

    for (unsigned i = 0xA0; i <= 0xBC; i++) {
        checksum -= header[i];
    }

    return (uint8_t)(checksum - 0x19);
}
```

At this point, Platinum has established that the cartridge presents itself as a supported Pokemon game and that its standard cartridge header is internally consistent. It hasn't yet established whether it can actually understand the game's save data, however, and this is where things begin to get particularly interesting.

## Game Freak Left Us an API?
If you open a Pokemon Emerald ROM in a hex editor and navigate to offset `0x100`, you'll find something that isn't part of the standard Game Boy Advance cartridge header. Game Freak embedded a structure describing various properties of the game directly into its ROM, effectively providing other software with information about the game's internal data layout. The relevant section from a hexdump of Pokemon Emerald is:
```
00000100: 0300 0000 0200 0000 706f 6b65 6d6f 6e20  ........pokemon
00000110: 656d 6572 616c 6420 7665 7273 696f 6e00  emerald version.
00000120: 0000 0000 0000 0000 8ca1 3008 b828 3008  ..........0..(0.
00000130: 7836 3008 3844 3008 a8bc 5708 88c3 5708  x60.8D0...W...W.
00000140: 40c5 5708 c885 3108 7c97 3108 085c 5a08  @.W...1.|.1..\Z.
00000150: 7012 0000 9c13 0000 1800 0000 8809 0000  p...............
00000160: 243b 0000 4600 0000 e408 0000 ac08 0000  $;..F...........
00000170: 8201 0000 070a 0a0a 0c0c 060c 0610 120c  ................
00000180: 0f0b 0108 0c00 0000 2c0f 0000 883d 0000  ........,....=..
00000190: 3402 0000 3802 0000 0900 0000 0a00 0000  4...8...........
000001a0: 0000 0000 0800 0000 a80c 0000 a80c 0000  ................
000001b0: c731 0000 b331 0000 0000 0000 cc03 3208  .1...1........2.
000001c0: dbb6 3108 d4ba 3108 a039 5808 98c8 3108  ..1...1..9X...1.
000001d0: 00c4 3208 60c4 3208 a800 0000 6408 0000  ..2.`.2.....d...
000001e0: 9b08 0000 1e1e 1040 2e32 0000 9804 0000  .......@.2......
000001f0: a831 0000 f831 0000 3400 0000 0000 0000  .1...1..4.......
```

The Platinum decompilation calls this structure `GBAPkmnGameHeader`. It contains information about the sizes of the game's save blocks, offsets into those blocks, the locations of trainer and party information, flags, variables, and pointers to various pieces of game data.

For Emerald, FireRed and LeafGreen, Platinum retrieves this information by copying the structure directly from the GBA cartridge:
```C
CTRDG_CpuCopy32(
    (void *)0x08000100,
    game_header,
    sizeof(GBAPkmnGameHeader)
);
```

Among the particularly useful fields are the sizes of the game's two main save structures. In the English Emerald ROM used for my reference capture, these values are:
| ROM offset | Field | Value |
| :--- | :---: | ---: |
| `0x188` | SaveBlock2 size | `0x0F2C`
| `0x18C` | SaveBlock1 size | `0x3D88`

These sizes matter because the game needs to know how much data belongs to each logical save section and, by extension, how much data should be included when validating that section's checksum.

Ruby and Sapphire are exceptions to this metadata-loading mechanism. Platinum instead supplies several hardcoded layout values for those games, which means that the migration code contains an explicit compatibility path for the earlier Generation III releases.

I find the existence of this structure fascinating. It's effectively a binary interface between games running on entirely different hardware, allowing one game to describe its internal data structures to another. Platinum doesn't need to execute any GBA code, invoke Emerald's save routines, or somehow communicate with a running instance of Emerald. It simply reads information describing the save layout and implements the necessary logic itself.

This is also the first indication that the amount of the original GBA ROM that Poke Transporter DSi actually needs to expose might be substantially smaller than I initially anticipated.

## Instrumenting melonDS
Static analysis is useful, but it only tells you what a program appears capable of doing. Particularly when working with reconstructed source code, I wanted to establish what the retail game actually does rather than relying exclusively on my interpretation of the decompilation.

To accomplish this, I checked out a specific revision of [melonDS](https://github.com/melonDS-emu/melonDS) and modified its GBA cartridge implementation to log every ROM and backup memory access. I also instrumented the external memory control register, allowing me to establish which processor owned the cartridge interface during each operation and how the game configured its access timings.

The relevant instrumentation points were the `ROMRead`, `ROMWrite`, `SRAMRead` and `SRAMWrite` functions within `GBACartSlot`, along with `NDS::SetExMemCnt`. Despite the latter pair of cartridge functions being named after SRAM, they provide the relevant interface for observing Flash accesses as well.

Each instrumentation record contains the following information:
```
M1,operation,cpu_or_owner,pc9,pc7,address,value,width,exmem9
```
For example, an actual pair of ROM accesses from the beginning of the migration looks like this:
```
M1,R_ROM,9,01FF8500,00001160,080000AC,00005042,16,680C
M1,R_ROM,9,01FF8500,00001160,080000AE,00004545,16,680C
```

These are two 16-bit reads of Emerald's game code, performed while ARM9 owns the cartridge. The recorded values, interpreted in little-endian order, produce the four ASCII characters `BPEE`.

There are a few important limitations to this instrumentation. The processor field for cartridge accesses represents the owner of the bus, which isn't necessarily the processor instruction that directly initiated a transfer. DMA, for example, can perform cartridge accesses independently of the processor's current instruction. Similarly, the recorded program counters are raw snapshots of the processors' R15 registers, not necessarily the exact addresses of the instructions responsible for each access.

The resulting EXMEM events also represent calls to the emulator's register setter rather than necessarily representing distinct changes to the physical register state. These distinctions are important when interpreting the trace, particularly when using it to identify candidate binary patch locations.

With the instrumentation in place, I booted an unmodified copy of Pokemon Platinum with Pokemon Emerald inserted into the emulated Slot-2, selected six Pokemon, confirmed their migration, and allowed the game to return to the menu.

The resulting log contained 952,678 instrumentation events, occupying approximately 51 MiB of disk space.

## The Smallest Pokemon Emerald ROM
The first thing I wanted to determine was exactly which regions of the Emerald ROM were accessed during the entire process. Since the GBA cartridge interface exposes up to 32 MiB of ROM address space, I was interested in whether Platinum followed any of the pointers contained in the game-specific metadata, or whether it accessed additional information elsewhere in the ROM.

After processing all 238,746 ROM read transactions, the answer was rather satisfying:
| ROM offsets | Unique bytes access |
| :--- | ---:
| `0x000–0x0BF` | 192 |
| `0x100-0x1FB` | 252 |
| Everything else | 0 |
| Total | 444 |

Throughout the entire captured migration, Pokemon Platinum accessed only 444 distinct bytes of Pokemon Emerald's 16 MiB ROM. Every access was contained within the first 512 bytes, and repeated reads of the same addresses consistently returned the same values.

The two complete header and metadata copies were also visible in the trace. Platinum first performs these reads while determining whether to display the migration option, then performs them again when the migration application initializes. Both passes are immediately followed by identification of the cartridge's backup memory.

There were no accesses through the metadata's pointers into the rest of the GBA ROM, no ROM writes, and no accesses to Emerald's RTC interface. Platinum never attempted to execute any code from the GBA cartridge.

This doesn't necessarily mean that 512 bytes are sufficient for every conceivable cartridge interaction. NitroSDK contains a conditional path that reads a module identifier from ROM offset `0x1FFFE`, for example, although that path was never exercised in this capture. Other games, cartridge revisions and failure paths may introduce additional requirements.

Nevertheless, for the successful Emerald-to-Platinum migration I'm investigating, we could retain the entire required ROM interface in a 512-byte buffer. Rather than keeping a 16 MiB ROM resident in the already rather limited memory available to DS software, we can read a tiny portion of it from the SD card and expose that information through our virtual cartridge interface.

The considerably larger problem is the cartridge's save data.

## Flash Memory Isn't RAM
Pokemon Emerald uses a 1 Mbit Flash chip for its persistent storage, providing 128 KiB of capacity. The storage is organized into 32 physical sectors of 4 KiB each, with the sectors divided between two banks of 64 KiB.

Because the relevant backup memory address window only exposes 64 KiB at a time, the cartridge cannot expose all of its save data simultaneously. Software must explicitly select the bank it wishes to access, after which reads and writes within the backup memory window correspond to that bank.

Given a physical sector number and an offset within the sector, the mapping can be expressed as:
```C
uint32_t bank = physical_sector >> 4;

uint32_t bus_address =
    0x0A000000
    + ((physical_sector & 0xF) << 12)
    + offset;

uint32_t file_offset =
    physical_sector * 0x1000
    + offset;
```

For example, physical sector 15 resides in bank zero at cartridge address `0x0A00F000`, whereas physical sector 16 resides in bank one at address `0x0A000000`. Although these sectors are adjacent within the save file, accessing them requires switching banks and wrapping around to the beginning of the memory-mapped address window.

Unlike ordinary RAM, Flash memory cannot generally be modified by arbitrarily overwriting bytes. Programming Flash clears bits from one to zero, while restoring bits to one requires an erase operation. These erase operations occur at a larger granularity, which in this case is an entire 4 KiB sector.

Flash chips therefore expose a command protocol. Depending on the current state of the device, a write to a particular address may represent an unlock operation, an instruction to erase storage, a bank selection, or a byte of actual save data.

The two addresses commonly used for issuing commands to the Flash device are:
```C
#define FLASH_BASE 0x0A000000
#define FLASH_U1   0x0A005555
#define FLASH_U2   0x0A002AAA
```
For example, selecting a bank requires the following sequence of byte writes:
```C
flash_write(FLASH_U1, 0xAA);
flash_write(FLASH_U2, 0x55);
flash_write(FLASH_U1, 0xB0);
flash_write(FLASH_BASE, bank);
```

The initial writes of `0xAA` and `0x55` constitute an unlock sequence. The subsequent write of `0xB0` instructs the Flash chip to interpret the following write as a bank number rather than as ordinary data.

The initial identification of the Flash chip uses a similar mechanism. After issuing the unlock sequence and command `0x90`, the chip enters an identification mode in which reads from the first two addresses return its manufacturer and device identifiers instead of its saved contents.

In my reference capture, melonDS reports manufacturer ID `0x62` and device ID `0x13`, corresponding to the Sanyo 1 Mbit Flash configuration. NitroSDK also contains support for the Macronix `0xC2`/`0x09` device.

After retrieving the identifiers, the SDK returns the chip to its normal read-array mode using a reset command. Across both identification passes, Platinum performs the following sequence:
```
WRITE  0A005555  AA
WRITE  0A002AAA  55
WRITE  0A005555  90

READ   0A000001  13
READ   0A000000  62

WRITE  0A005555  AA
WRITE  0A002AAA  55
WRITE  0A005555  F0
WRITE  0A005555  F0
```

Erasing a sector requires a second unlock sequence following the erase-setup command:
```C
flash_write(FLASH_U1, 0xAA);
flash_write(FLASH_U2, 0x55);
flash_write(FLASH_U1, 0x80);

flash_write(FLASH_U1, 0xAA);
flash_write(FLASH_U2, 0x55);
flash_write(sector_address, 0x30);
```

Programming an individual byte, meanwhile, requires four writes:
```C
flash_write(FLASH_U1, 0xAA);
flash_write(FLASH_U2, 0x55);
flash_write(FLASH_U1, 0xA0);
flash_write(target_address, data);
```

An important implementation detail is that these commands cannot be decoded by examining the value of an individual write in isolation. A value of `0xF0`, for example, may represent a command to reset the Flash chip, but it may also simply be a byte of Pokemon save data. Even the command addresses themselves can be valid programming destinations.

A correct low-level implementation consequently needs a state machine that tracks the unlock sequence, the currently selected command, the active bank and whether the next write represents a command argument or programming data.

Fortunately, most of the game never needs to concern itself with these details. NitroSDK provides considerably higher-level abstractions for identifying, reading, writing and verifying the cartridge's backup memory.

## How a Generation III Pokemon Save Actually Works
Generation III Pokemon games use a rotating save system intended to protect against interrupted writes. Rather than maintaining a single authoritative copy of the player's save, the game maintains two main save slots, each consisting of 14 physical Flash sectors. The remaining four sectors are used for additional game-specific persistent data outside the main rotating save.

An important distinction is that the two save slots do not correspond to the two Flash banks. Their physical layout is:
```
                 Bank 0                  Bank 1
Sector    0 ........ 13  14 15 | 16 ........ 27  28 .. 31
          [   Slot A   ][        Slot B        ][ Other ]
```

Slot A occupies sectors 0–13, while slot B occupies sectors 14–27. Because slot B crosses the boundary between the two physical Flash banks, any implementation that treats a Flash bank as synonymous with a Pokemon save slot is going to encounter problems very quickly.

The contents of these save slots are divided into 14 logical sections. Section zero contains SaveBlock2, which includes various pieces of player and trainer information. Sections one through four contain SaveBlock1, which includes much of the game's state, and sections five through thirteen contain the Pokemon storage system.

The physical position of a section within a save slot is not fixed. Each time the game produces a new save, the physical arrangement of the logical sections rotates. Consequently, determining what a particular sector contains requires examining its footer rather than simply assuming that physical sector zero contains logical section zero.

Each 4 KiB sector has the following layout:
| Offset | Size | Contents |
| :--- | ---: | ---: |
| `0x000-0xFF3 | 4084 bytes | Data |
| 0xFF4-0xFF5 | 2 bytes | Logical section ID |
| 0xFF6-0xFF7 | 2 bytes | Section checksum |
| 0xFF8-0xFFB | 4 bytes | Save signature |
| 0xFFC-0xFFF | 4 bytes | Save index

The signature used by these saves is `0x08012025`. The save index identifies the generation of the save to which a sector belongs, allowing software to distinguish between older and newer saves and identify certain interrupted-write conditions.

Although the physical space preceding the footer is 4084 bytes, the regular logical payload size is `0xF80`, or 3968 bytes. The final section of each logical block can contain less data, depending on the size of the structure being stored. This is one reason why the save block sizes contained in Emerald's ROM metadata are important.

The checksum algorithm sums the meaningful section data as little-endian 32-bit words, then adds the upper and lower halves of the resulting 32-bit accumulator and truncates the result to 16 bits. An illustrative implementation is:
```C
An illustrative implementation is:

uint16_t section_checksum(
    const uint8_t *data,
    size_t size
)
{
    uint32_t sum = 0;

    for (size_t i = 0; i < size; i += 4) {
        uint32_t word =
            (uint32_t)data[i]
            | ((uint32_t)data[i + 1] << 8)
            | ((uint32_t)data[i + 2] << 16)
            | ((uint32_t)data[i + 3] << 24);

        sum += word;
    }

    return (uint16_t)(
        (sum & 0xFFFF) + (sum >> 16)
    );
}
```

When loading a save, Platinum must reconstruct essentially the same logical representation that Emerald itself would use. It reads the sectors in both save slots, validates their signatures and checksums, determines whether the required logical sections are present, and examines their save indices to decide which slot is authoritative. The relevant functions in Platinum's decompilation are:
```
ov97_022360D8
├── ov97_02235FFC
│   ├── ov97_02235F4C (validate slot A)
│   │   ├── ov97_02235EAC
│   │   │   └── CTRDG_ReadAgbFlash
│   │   └── Validate sector checksum
│   ├── ov97_02235F4C (validate slot B)
│   └── Select active save slot
└── Reread and reconstruct active save
```

The save-selection logic also contains special handling for save-index rollover and certain damaged-save conditions. It isn't quite as simple as taking whichever slot has the numerically highest index, nor does the presence of one apparently valid slot guarantee that Platinum will accept every possible state of the other slot.

My trace confirmed the expected loading sequence exactly. Platinum first reads physical sectors 0–13, then sectors 14–27, and finally rereads sectors 0–13 to reconstruct the active save in RAM. Each read retrieves the entirety of a 4 KiB sector, and every byte returned by the second read of the active slot matches the original read. In total, Platinum retrieves 168 KiB of save data from a device that only contains 128 KiB of storage.

##  Why Both Processors Need to Believe in the Cartridge
After reconstructing Emerald's save, Platinum doesn't need to repeatedly access the GBA cartridge to display its Pokemon boxes. The migration interface works against the in-memory copy of Emerald's Pokemon storage, allowing the game to browse individual records and display their information without performing additional Flash reads.

The game does, however, need to ensure that the cartridge hasn't been removed while the player is selecting Pokemon. This becomes a somewhat more complicated problem when the cartridge doesn't actually exist.

NitroSDK's `CTRDG_IsExisting()` function repeatedly reads four halfwords from the cartridge ROM and compares their contents against its cached cartridge information:
| ROM address | Purpose |
| :--- | :--- |
| `0x080000B2` | ROM type |
| `0x080000BE` | Module identifier |
| `0x080000AC` | Game code, low halfword |
| `0x080000AE` | Game code, high halfword |

The trace establishes that both processors participate in these checks. During initialization, ARM7 reads all 156 bytes of the Nintendo logo, and throughout the migration it periodically performs the four-halfword cartridge-presence check.

The complete distribution of cartridge accesses between the processors is particularly revealing:
| Operation | ARM9 | ARM7 |
| :--- | :--- | ---:
ROM reads | 236,444 | 2302 |
Flash reads | 229,466 | 0 |
Flash writes | 229,814 | 0 |
EXMEM events | 252,427 | 2225 |

No Flash operations were attributed to ARM7 during the entire migration. Its involvement in the observed cartridge interface consists of cartridge initialization, presence detection and associated bus-control operations.

The enormous number of ROM reads performed by ARM9 is primarily a consequence of the Flash driver. After programming an individual byte, NitroSDK polls the Flash chip to determine whether the operation has completed. As part of this process, it calls `CTRDG_IsExisting()` to ensure that the cartridge remains inserted.

An individual programming operation therefore looks approximately like this when condensed to its relevant cartridge accesses:
```
WRITE FLASH  0A005555  AA
WRITE FLASH  0A002AAA  55
WRITE FLASH  0A005555  A0
WRITE FLASH  0A00F000  C8

READ FLASH   0A00F000  C8

READ ROM     080000B2  0096
READ ROM     080000BE  0000
READ ROM     080000AC  5042
READ ROM     080000AE  4545
```
The process then repeats for the next byte.

As we'll see shortly, Platinum programs 57,344 bytes during a successful migration. The presence checks associated with those individual programming operations alone account for 229,376 ROM halfword reads, or approximately 96% of all ROM reads in the capture.

There are two useful implications here. Firstly, replacing the higher-level Flash operations would eliminate the need to perform this enormous amount of low-level cartridge communication. Secondly, Poke Transporter DSi cannot simply patch the main menu into believing that Emerald is present and call it a day. The shared cartridge state and ARM7's presence-detection behaviour must also remain consistent, or we risk the game interpreting our virtual cartridge as having been removed.

## Selecting and Converting the Pokemon
Once the save has been reconstructed, Platinum performs a series of eligibility checks before allowing the migration to proceed. These include checking whether there are at least six Pokemon available to migrate, whether the destination game already contains a pending Pal Park transfer, and whether the relevant migration cooldown has elapsed.

The original Diamond, Pearl and Platinum impose a 24-hour migration restriction, which is associated with information about the source trainer and the DS system's clock. The migration code also records information about the console's MAC address and RTC offset, allowing it to detect certain changes to the system being used for migration. Pokemon HeartGold and SoulSilver later removed this restriction.

This actually caused a minor annoyance during my testing. Moving the Platinum save into melonDS triggered the game's clock-adjustment restriction, requiring me to establish a compatible emulator-side save state before capturing a successful migration. I deliberately avoided patching the restriction out of the game, since the purpose of the capture was to establish the behaviour of an unmodified retail executable.

The Pokemon themselves are also subject to additional restrictions, including whether they are eggs, whether they know prohibited HM moves, and whether their species and other relevant data can be handled by the migration code.

After the player has selected and confirmed six Pokemon, Platinum calls a function named `CopySelectedMonToPalParkTransfer`. This retrieves the selected Generation III boxed Pokemon records from the reconstructed storage and converts them into Generation IV Pokemon using the game's existing conversion routines. These routines are responsible for handling the differences between the two generations' Pokemon data structures, including the representations of species, moves, trainer information, met data and other persistent fields. The resulting Pokemon are placed in Platinum's dedicated migration storage rather than being deposited directly into the player's PC.

Platinum also modifies its in-memory copy of Emerald's Pokemon storage, setting the selected records' species fields to `SPECIES_NONE` through `GBABoxPokemon_SetData()`. At this stage, however, the changes only exist in RAM. The selected Pokemon have been prepared for inclusion in Platinum's save, and their removal from Emerald has been staged, but the migration transaction has not yet been committed to either cartridge.

This brings us to what I consider the most interesting part of the entire investigation: how Platinum coordinates the two independent save operations.

## Game Freak's Attempt at a Distributed Transaction
Migrating Pokemon between cartridges presents an interesting problem from a storage systems perspective. Platinum needs to remove six Pokemon from one persistent storage device and add them to another, but the two cartridges have no underlying mechanism for atomically committing those changes together. If Platinum were to save Emerald first and subsequently encounter a failure while saving its own data, the Pokemon could disappear from both games. Conversely, if Platinum were to save its own data first and subsequently fail to update Emerald, both games could retain copies of the Pokemon.

There's no obvious way to make this operation completely atomic across the two independent cartridges, particularly when the user can turn the console off at any point. Nevertheless, the game can arrange its writes in a way that makes certain failure modes substantially less likely.

The migration state machine responsible for coordinating these operations is currently named `ov97_02233B8C` in the Platinum decompilation. Following its execution reveals a rather deliberate ordering of the two save operations.

| State | Operation |
| :--- | :--- |
| 0 | Convert selected Pokemon, clear source species and record migration history |
| 1-2 | Variable delay before beginning save operations |
| 3 | Initialize Platinum's save operation |
| 4 | Advance Platinum's save to its finalization stage
| 5 | Read 16 bytes from the beginning of the GBA flash
| 6 | Initialize the inactive GBA save-slot rewrite
| 7 | Wait for the first 13 logical sections of the new GBA save
| 8 | Finalize Platinum's save and dispatch the final GBA section
| 9 | Await completion of the final GBA write

The destination save is initialized using `SaveData_SaveStateInit(saveData, 2)`. Platinum then repeatedly calls `SaveData_SaveStateMain()` until the save operation returns `SAVE_RESULT_PROCEED_FINAL`, at which point the destination save is deliberately paused.

The game proceeds to construct a new version of Emerald's save in its inactive save slot, but it only writes the first 13 of the 14 required logical sections. Once those writes have completed successfully, Platinum resumes its own save operation and continues until it receives `SAVE_RESULT_OK`. Only after Platinum's save has completed does the game dispatch the final write to Emerald.

The rotating save format is what makes this approach useful. While Platinum is constructing the new version of Emerald's save in the inactive slot, the previously active slot remains intact. The old slot still contains all six Pokemon, while the new slot is intended to represent the source game after their removal.

The ordering appears designed to favour avoiding Pokemon loss. If the operation is interrupted before the destination save has completed, the previous version of Emerald's save has not been overwritten. If the destination save completes but the final Emerald sector isn't successfully written, the two cartridges can still disagree about whether the migration occurred.

This is not an atomic distributed transaction, and the exact recovery behaviour depends on how each game handles the interrupted save. Nevertheless, it's a fairly interesting example of application-level coordination between independent storage devices, using the existing save format to improve the operation's resilience.

One peculiar detail I noticed is that, immediately before constructing the new Emerald save, Platinum reads 16 bytes from sector zero at offset zero. The resulting buffer is not subsequently used by the surrounding function, and I've confirmed that the read occurs in the actual trace. Whether this was intended as an additional cartridge check, a workaround for some hardware behaviour, or something else entirely remains unclear.

The DS save finalization sequence itself is established by the decompiled source. My current instrumentation only observes Slot-2, so it cannot independently establish the precise timing of the writes to Platinum's own cartridge.

## How Platinum Constructs the New Emerald Save
When Platinum begins rewriting Emerald's save, it first selects the inactive save slot and increments the save index. It then reconstructs each logical section into a temporary 4096-byte sector buffer, initially clearing the entire buffer to zero, copying the meaningful save data into it, and populating the footer with the logical section ID, calculated checksum, save signature and new save index.

The physical sector to which a logical section is written is calculated using the position of logical section zero in the previous active save slot:
```C
uint32_t physical_sector =
    ((logical_section + previous_section_zero + 1) % 14)
    + 14 * destination_slot;
```

This is what produces the rotation of logical sections within each successive save. The value `previous_section_zero` represents the position within the original slot, rather than an absolute physical sector number.

In my reference capture, the original active slot was slot A, with a save index of 1658. Logical section zero occupied the first physical sector of that slot, giving us a previous section-zero position of zero. Platinum therefore selected slot B as the destination, incremented the save index to 1659, and wrote the following sequence of physical sectors:
```
15, 16, 17, 18, 19, 20, 21,
22, 23, 24, 25, 26, 27, 14
```

The first write places logical section zero in physical sector 15, with the remaining logical sections following sequentially until logical section 13 wraps around to physical sector 14.

This is a particularly useful reference case for Poke Transporter DSi because it exercises both Flash banks, including switching from bank zero to bank one early in the save operation and subsequently switching back to bank zero for the final sector.

The actual sector write is dispatched through NitroSDK's asynchronous Flash interface:
```C
CTRDG_WriteAndVerifyAgbFlashAsync(
    physical_sector,
    sector_buffer,
    4,
    ov97_02236244
);
```

The callback inspects the result returned by the SDK's cartridge task and advances the state machine accordingly. A successful operation allows the migration code to prepare the next logical section, while a failure is propagated back through the migration application. 

One rather curious detail is the third argument supplied to the function. Despite the sector buffer containing 4096 bytes, Platinum passes a verification size of only four bytes.

The SDK still erases and programs the entire 4 KiB sector. Every programmed byte is individually polled, but the separate verification operation only compares the first four bytes of the completed sector. This behaviour is directly visible in the captured trace, which contains exactly 56 explicit verification reads across the 14 programmed sectors.

## Accounting for 952,678 Transactions
The complete migration trace contains the following distribution of instrumentation events:

| Operation | Count |
| :--- | ---: |
| ROM reads | 236,746 |
| Flash reads | 229,466 |
| Flash writes | 229,814 |
| EXMEM control events | 254,652|
| **Total** | 952,678 |

I wrote additional analysis tooling to classify the Flash command sequences, track the active bank, reconstruct the data returned by Flash reads, and associate each programming operation with its corresponding physical sector. The particularly satisfying result was that every single Flash write could be accounted for using the behaviour established through static analysis.

Platinum identifies the Flash chip twice, selects a bank 85 times, erases 14 sectors, and programs 57,344 bytes. The bank-selection count breaks down into 42 selections for the initial full-sector reads, one selection for the mysterious 16-byte read, and three selections for each sector being rewritten. NitroSDK independently selects the appropriate bank before erasing a sector, before programming its contents, and before performing its explicit verification pass, even if the correct bank is already selected.

Given the number of byte writes required by each Flash command, we can calculate the expected number of writes independently of the trace:
| Operation | Calculation | Writes |
| :--- | :---: | ---: |
| Flash identification | 2*7 | 14 |
| Bank selection | 85*4 | 340 |
| Sector erasure | 14*6 | 84 |
| Byte programming | 57,344*4 | 229,376 |
| **Total** | 229,814 |

The calculated number of writes is identical to the observed number of writes. There were no unidentified Flash commands, no ROM writes, no whole-chip erasures and no additional programming attempts indicative of retries. The Flash reads can be accounted for just as precisely. Every erase poll returned `0xFF`, every programmed-byte poll returned the expected value, and every explicit verification read matched the corresponding programmed data.

An interesting consequence of this is that melonDS completes the relevant Flash operations immediately from Platinum's perspective. The first polling read following each program or erase operation returns the expected result, and Platinum doesn't require any intermediate busy states to successfully complete the migration.

For Poke Transporter DSi, this means that the initial Flash backend can implement immediate command completion without needing to reproduce the physical programming delays of the original hardware. We still need to preserve the SDK's asynchronous task and callback semantics, however. Completing an internal storage operation synchronously is not necessarily equivalent to invoking an SDK completion callback synchronously.

These are the moments in reverse engineering that I find particularly rewarding. You begin with a large, seemingly incomprehensible collection of memory transactions, gradually establish a model of the system producing them, and eventually reach a point where you can predict exactly how many operations it will perform and why.

## The Most Expensive 23 Bytes in Pokemon
Because the trace records both the data returned by Flash reads and the data supplied to Flash writes, it contains enough information to reconstruct Emerald's entire main save area before and after the migration. The initial 28 full-sector reads expose both original save slots, giving us 112 KiB of the cartridge's persistent storage. By subsequently applying the recorded sector erasures and byte-programming operations to that reconstructed data, we can produce the corresponding post-migration representation.

The four additional sectors, comprising the remaining 16 KiB of the physical Flash chip, were never accessed during the capture. Their original contents consequently cannot be recovered from the trace, although we can establish that the migration never attempted to modify them.

I independently calculated the checksum of every logical section across the two original save slots and the newly constructed save slot. All 42 sector checksums matched their stored values, and every slot contained the complete set of logical section IDs with consistent save indices.

With both the original active save and the new active save reconstructed, I could align their contents by logical section ID rather than physical sector position and determine exactly what Platinum had changed. The answer was **23 bytes**.

Across all meaningful save data, excluding sector footers, save counters, padding and physical section rotation, Platinum modified only 23 bytes. Every modified byte was located within logical section 12, which contains part of Emerald's Pokemon storage.

Despite this, Platinum erased 14 entire sectors, programmed 57,344 bytes, issued 229,814 Flash writes, performed 229,466 Flash reads, and generated hundreds of thousands of additional ROM accesses and bus-control operations. This gives us a data-programming amplification factor of approximately 2,493, before we even begin accounting for the command sequences required to perform each individual programming operation.

This is not necessarily evidence of inefficiency on Game Freak's part. The amount of work performed is primarily a consequence of the physical properties of Flash memory and the game's choice to maintain two complete rotating save slots rather than modifying the currently active save in place. Preserving the old save until a replacement has been constructed provides considerably more resilience against interrupted writes. It also illustrates why treating a Pokemon save as an ordinary mutable file is a rather different abstraction from interacting with the original physical storage device.

## What Does Removinhe a Pokemon Actually Mean?
One might reasonably expect that when Platinum removes a Pokemon from Emerald, it would simply overwrite the entire corresponding Pokemon record with zeroes. Generation III boxed Pokemon occupy 80 bytes each, so completely clearing six records would involve modifying up to 480 bytes of the logical save data.

Instead, Platinum uses Emerald's existing boxed Pokemon representation to mark each record as unoccupied. 

A Generation III boxed Pokemon record contains unencrypted identifying information, various status flags, a checksum, and 48 bytes of encrypted Pokemon data. The encrypted data is divided into four 12-byte substructures whose ordering depends on the Pokemon's personality value. Their contents are encrypted using a key derived from the personality value and original trainer identifier. The key is effectively:
```C
uint32_t key = personality ^ original_trainer_id;
```

The encrypted region contains information such as the species, held item, experience, moves, EVs, IVs and various other attributes associated with the Pokemon. Because the order of the substructures depends on the personality value, modifying a particular field requires locating the correct substructure, decrypting the relevant data, updating the field, recalculating the Pokemon checksum, and restoring the encrypted representation.

Platinum's implementation of `GBABoxPokemon_SetData()` already handles these details. When the migration code sets the species to `SPECIES_NONE`, the relevant species-occupancy state is updated along with the internal checksum, but the remainder of the Pokemon record is not comprehensively erased.

This explains why so few bytes of the save changed. From Emerald's perspective, the selected box positions are now empty, even though much of the information previously associated with the Pokemon remains in their underlying records.

It also highlights one of the advantages of using Platinum's original migration implementation rather than attempting to reproduce its behaviour through an external save editor. The game already understands the peculiarities of the original Pokemon format, and I don't need to duplicate any of that logic within Poke Transporter DSi.

## Using the Trace to Find Binary Hook Locations
Although the decompilation provides us with a useful understanding of the cartridge interface, Poke Transporter DSi will ultimately need to modify the behaviour of the actual retail game. For that purpose, the raw program counter values recorded in the trace provide another useful source of information. For example, every byte-programming command in the captured migration originated from the same four raw ARM9 program counter values:
| Raw ARM9 PC | Operation |
| :--- | :--- |
| `0x020D297C` | Write first unlock byte |
| `0x020D2988` | Write second unlock byte |
| `0x020D2990` | Write programming command
| `0x020D297C` | Write actual programming data |

Similarly, every explicit sector verification read originates from raw PC `0x020D18EC`, while the bulk Flash reads originate from `0x020D1894`.

These patterns correspond closely to the SDK's implementation of functions such as `CTRDGi_ProgramFlashByteMX`, `CTRDGi_ReadAgbFlashCore` and `CTRDGi_VerifyFlashSectorCore`.

The cartridge-presence checks also produce consistent program counter values on both processors, giving us a starting point for identifying the ARM9 and ARM7 implementations of `CTRDG_IsExisting()`.

These values aren't directly usable as function entry points. ARM's architectural pipeline affects the value of R15, and the emulator's internal representation of the program counter introduces additional considerations. We also need to establish the instruction set in use, the surrounding instructions, and whether the relevant code is permanently resident or belongs to an overlay. Nevertheless, they provide concrete anchors for the next stage of investigation. Rather than attempting to blindly identify cartridge-related functions within the retail executable, we can begin at known access sites and work backwards to identify their enclosing functions.

The eventual patching mechanism will need to account for different regional releases and ROM revisions. I don't particularly want to distribute a collection of unexplained magic offsets that happen to work for the one ROM I tested, so establishing verifiable instruction signatures and expected original bytes will be important before the project expands beyond its initial target.

## What Happens at Pal Park Itself?
Something worth emphasizing is that the actual Catching Show at Pal Park is largely unrelated to the GBA cartridge. By the time Platinum reports that the migration has successfully completed, the six Pokemon have already been converted into their Generation IV representations and stored in Platinum's dedicated migration data. The GBA cartridge is no longer required to provide the Pokemon themselves.

The subsequent Catching Show constructs its encounters using the destination game's `MigratedPokemon`storage. Once the player has caught all six Pokemon, the game invokes the relevant routines to move the migrated Pokemon into the player's PC boxes, update the associated records, and clear the pending transfer data. The player still needs to save the resulting game state to make those changes persistent, but the original source cartridge no longer participates in the underlying Pokemon conversion or encounter generation.

This means that the portion of Pal Park that Poke Transporter DSi actually needs to restore is the migration application accessible from the main menu. The existing Catching Show should continue functioning without any Pokemon-specific modifications.

My current capture ends after the successful migration and return to the menu, so the behaviour of the Catching Show described here is established from the source code rather than from a separate runtime capture. Completing the Catching Show, saving, and reopening both games remains part of the eventual end-to-end validation.

## Building a Cartridge That Doesn't Exist
My initial concern when considering this project was that virtualizing Slot-2 might require intercepting arbitrary memory accesses to the GBA cartridge regions. The ARM946E-S contains a memory protection unit, but it does not provide the sort of virtual memory translation facilities that would allow us to simply map a buffer in main RAM to the address range ordinarily occupied by a GBA cartridge.

Attempting to emulate the interface at the individual memory-access level would therefore involve considerably more intrusive mechanisms. It would also require dealing with accesses originating from both processors and potentially from DMA, making it rather more complicated than intercepting a handful of ordinary function calls.

Fortunately, Platinum's extensive use of NitroSDK gives us a considerably more attractive alternative. Rather than reproducing the physical cartridge bus from the perspective of arbitrary code, we can investigate replacing the relevant functions within the SDK's cartridge interface. The initial candidate functions are:
```
CTRDG_Init
CTRDG_IsAgbCartridge
CTRDG_IsExisting
CTRDG_IsPulledOut
CTRDG_GetAgbGameCode
CTRDG_CpuCopy32
CTRDG_IdentifyAgbBackup
CTRDG_ReadAgbFlash
CTRDG_WriteAndVerifyAgbFlashAsync
```

There are additional considerations associated with ARM7 initialization, cartridge-removal notifications, shared cartridge state and the side effects of enabling cartridge access. The exact hook surface consequently remains subject to further investigation, but the trace has established the fundamental behaviour those hooks need to reproduce.

The backing data for the virtual cartridge could conceptually be represented as:
```C
#define GBA_ROM_HEADER_SIZE 0x200
#define GBA_FLASH_SIZE      0x20000
#define GBA_SECTOR_SIZE     0x1000

typedef struct {
    uint8_t rom_header[GBA_ROM_HEADER_SIZE];
    uint8_t flash[GBA_FLASH_SIZE];
} VirtualGBACart;
```

The two buffers occupy a combined 128.5 KiB, excluding the bookkeeping required by the backend. Although this is a meaningful amount of memory on the original Nintendo DS, it is considerably more manageable than retaining a complete GBA ROM.

The Flash backing store itself can expose direct sector-level operations to the SDK hooks:
```C
bool vcart_read_flash(
    VirtualGBACart *cart,
    uint32_t sector,
    uint32_t offset,
    void *destination,
    size_t size
);

bool vcart_write_sector(
    VirtualGBACart *cart,
    uint32_t sector,
    const uint8_t *source
);
```

Internally, these operations can work against a contiguous 128 KiB representation of the cartridge's save, translating the sector and offset into a backing-store offset. The production hook doesn't need to issue four separate virtual Flash writes for every byte it wants to program, since we're implementing an interface above that level of abstraction.

I nevertheless intend to provide a low-level Flash command interface within the host-testable backend. Apart from making the implementation more generally useful, this will allow us to validate its behaviour against the reference trace without needing to run the game itself.

The substantially more delicate part of the implementation will be persistence. Updating a 128 KiB buffer in RAM is trivial; ensuring that a successful migration doesn't leave the user's actual save files in an inconsistent or unrecoverable state is not. In particular, we need to account for the ordering established by Platinum's migration state machine. If our virtual cartridge reports successful completion of all of its sector writes while silently retaining the changes in volatile memory, a subsequent failure to persist that memory to the SD card could undermine the assumptions made by the original migration implementation.

We also need to preserve the asynchronous SDK contract, including the lifetime of the caller's sector buffer, the expected scheduling of completion callbacks, and the propagation of failures. A virtual storage operation shouldn't report success unless it has satisfied whatever durability guarantees the implementation establishes.

I intend to be considerably more conservative about this part of the project than I am about most of my emulator development. Crashing an emulator because I've implemented something incorrectly is annoying. Destroying someone's Pokemon save because I've implemented something incorrectly is an entirely different matter.

## Nearly Half a Million Regression Tests
One particularly nice consequence of capturing the complete migration is that I now have an exceptionally useful reference fixture for developing the virtual Flash backend. The capture contains 229,814 Flash writes and 229,466 Flash reads, all in their original order. Given the original Flash contents, I can replay these 459,280 transactions against a new implementation and compare every returned read value against the corresponding value returned by melonDS.

The replay can exercise the entire observed Flash command interface, including identification mode, reset commands, bank switching, sector erasure, byte programming and verification. At the end of the replay, the backing storage can be compared against the contents reconstructed from the original migration.

The basic test structure will resemble:
```C
The basic test structure will resemble:

for (size_t i = 0; i < transaction_count; i++) {
    const FlashTransaction *transaction = &transactions[i];

    switch (transaction->operation) {
    case FLASH_READ:
        assert(
            flash_read(&flash, transaction->address)
            == transaction->value
        );
        break;

    case FLASH_WRITE:
        flash_write(
            &flash,
            transaction->address,
            transaction->value
        );
        break;
    }
}

assert(memcmp(
    flash.storage,
    expected_save,
    FLASH_SIZE
) == 0);
```

The original 128 KiB save will be necessary for the full final comparison, since the trace alone cannot reconstruct sectors 28–31. The test will also explicitly assert that the previous active save slot and those four additional sectors remain unchanged.

This gives us a particularly appealing separation between the virtual cartridge's implementation and the platform-specific integration. The backend can be written as a portable C library, compiled using an ordinary host compiler, and tested on my desktop without requiring a DS emulator, an ARM cross-compiler or a physical DSi.

I'll also be creating synthetic fixtures for the public repository. The captured trace contains sufficient information to reconstruct portions of a commercial Pokemon ROM and virtually the entire main save area of my personal Emerald cartridge, so I don't intend to distribute it as part of the project's automated test suite.

Of course, passing this trace replay will not establish that the backend correctly implements every possible Flash operation. It represents one successful migration path, without physical programming delays, cartridge removal, invalid commands, write failures or interruptions to the save transaction. Those behaviours will require separate, deliberately constructed tests.

Nevertheless, having nearly half a million real cartridge transactions available as a regression fixture is a rather good starting point.

## Whats Next?
The first major reverse-engineering milestone of Poke Transporter DSi is now substantially complete. For the successful Emerald-to-Platinum migration captured in melonDS, I've mapped the cartridge identification process, established the exact regions of ROM that are accessed, identified the roles played by ARM9 and ARM7, accounted for every observed Flash command, reconstructed both generations of the source save, and established how the migration application coordinates its two save operations.

There is still work to do before any of this runs on physical hardware. I need to resolve the exact binary hook locations, establish how the patches will interact with nds-bootstrap, compare the observed behaviour against real cartridges, and develop appropriate persistence and recovery mechanisms. The completed migration also needs to be validated against the actual saved files and the subsequent Catching Show, rather than relying solely on the source code and Slot-2 trace.

The next development milestone, however, is considerably more straightforward than I initially expected. I'll be implementing the virtual GBA cartridge and Flash backend as an independently testable C library, using the captured migration as a reference for its behaviour. Once that implementation is sufficiently robust, I'll begin integrating it with nds-bootstrap and investigating the patches necessary to make Pokemon Platinum recognize a virtual Emerald cartridge on my DSi.

Perhaps the most satisfying result of this investigation is just how much functionality I don't need to implement. I don't need to emulate the GBA's processor, graphics hardware, audio hardware or peripherals. I don't need to implement the Generation III save format, recreate Pokemon's encryption algorithms, reproduce the conversion between Pokemon data structures, or implement Pal Park's migration restrictions.

Game Freak already implemented all of that nearly two decades ago. My job is to provide the interface that its existing code expects, preserve the semantics of that interface, and make the game believe it's communicating with a cartridge. The only slightly inconvenient detail is that the cartridge slot doesn't exist.

## References and Further Reading
For reproducibility, the source analysis in this devlog uses pinned revisions of the Platinum and NitroSDK decompilations rather than their respective development branches.
- [Pokemon Platinum decompilation](https://github.com/pret/pokeplatinum/tree/485c400f9d7bad10d906d556ba23e5e46d5b3e5e)
- [Platinum's main menu implementation](https://github.com/pret/pokeplatinum/blob/485c400f9d7bad10d906d556ba23e5e46d5b3e5e/src/main_menu/main_menu.c)
- [Platinum's GBA migration application](https://github.com/pret/pokeplatinum/blob/485c400f9d7bad10d906d556ba23e5e46d5b3e5e/src/main_menu/gba_migrator.c)
- [Platinum's GBA cartridge and save implementation](https://github.com/pret/pokeplatinum/blob/485c400f9d7bad10d906d556ba23e5e46d5b3e5e/src/main_menu/ov97_02235D18.c)
- [NitroSDK decompilation](https://github.com/ntrtwl/NitroSDK/tree/eaae40f199c0a8827947afbc33227b6ba2f9d45c)
- [NitroSDK's common Flash implementation](https://github.com/ntrtwl/NitroSDK/blob/eaae40f199c0a8827947afbc33227b6ba2f9d45c/libraries/ctrdg/src/ctrdg_flash_common.c)
- [melonDS's GBA cartridge implementation](https://github.com/melonDS-emu/melonDS/blob/ff972523f8a70e877dd054439876c3c8c6b79197/src/GBACart.cpp)
- [Bulbapedia -- Generation III Save Data Structure](https://bulbapedia.bulbagarden.net/wiki/Save_data_structure_(Generation_III))
- [GBATEK -- GBA and DS Technical Reference](https://problemkaputt.de/gbatek.htm)

A considerable amount of this investigation was made possible by the work of the Pokémon decompilation community, the NitroSDK reverse-engineering community, and the developers of melonDS. The existence of these projects dramatically reduces the barrier to understanding and extending decades-old commercial software, and Poke Transporter DSi would be a considerably more difficult undertaking without them.
