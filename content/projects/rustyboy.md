+++
title = "RustyBoy"
description = "A Game Boy emulator written in Rust."
date = 2026-05-16

[taxonomies]
tags = ["rust", "systems-programming"]
+++

[RustyBoy](https://github.com/EthanPlant/RustyBoy) is a Game Boy emulator written in Rust.

![Screenshot of RustyBoy running Tetris](/images/rustyboy.png)

It's one of those projects that sounds simple until you start building it. Then you realize a handheld game console isn't one thing. It's a pile of small systems pretending to be one thing.

A CPU. Memory. Timers. Interrupts. Graphics. Input. Audio. Cartridges. Weird hardware behaviours. Decades of games accidentally depending on details nobody would design the same way twice.

That's what makes emulators interesting really interesting to me.

## What it is

RustyBoy emulates the original Game Boy.

The project is split into a core emulator and frontend code, so the actual emulation logic is separated from how the emulator is displayed or controlled.

It includes work on:

- CPU emulation
- memory mapping
- cartridge loading
- instruction decoding
- timers
- interrupts
- graphics
- input
- tests
- logging and debugging tools

The goal wasn't to make the most accurate emulator in the world. 

The goal was to build enough of the machine to understand how the pieces fit together.

## Why I built it

I like projects where the abstraction boundary is clear but unforgiving.

An emulator gives you a specification, a pile of test ROMs, and a very simple rule:

Either the game works or it doesn't.

There's not much room for pretending.

If the CPU flags are wrong, something breaks.  
If timing is wrong, something breaks.  
If memory is mapped incorrectly, something breaks.  
If interrupts fire at the wrong time, something breaks in a way that seems unrelated until you lose an evening to it.

That kind of feedback is frustrating, but useful.

It teaches you to respect the little details.

## What made it interesting

The Game Boy is small enough to fit in your head, but not so small that it becomes trivial.

That is a good size for a learning project.

You can understand the CPU. You can understand the memory map. You can understand the graphics pipeline. But you still have to deal with the fact that all of those parts interact.

The fun part is when a game first starts doing something recognizable.

A logo appears.  
Input works.  
A sprite moves.  
A menu opens.  
Tetris becomes Tetris.

Those moments feel absurdly rewarding because they are built out of many tiny correct decisions.

## What I learned

RustyBoy taught me that emulation is mostly about precision.

Not cleverness. Precision.

You spend a lot of time implementing tiny pieces of behaviour that seem too small to matter. Then a game depends on one of them, and suddenly it matters very much.

It also made me appreciate testing in a different way.

A normal test suite checks whether your code behaves the way you expect. Emulator tests often check whether your understanding of the hardware is wrong. That is a lot more humbling.

The machine doesn't care what your abstraction wanted to be.

## Why Rust

Rust worked well for this project because emulator code has a lot of state, a lot of byte-level work, and a lot of places where you want structure without giving up control.

The type system helps keep some of that state honest.

It does not make the emulator correct.

It does make certain mistakes harder to hide.

That is useful when you are passing bytes through a fake CPU and hoping a thirty-year-old game agrees with you.

## Current state

RustyBoy is archived now.

I still think it is worth keeping around because emulator projects leave behind a very specific kind of evidence. You can see the work on the screen.

When Tetris boots, it means a lot of small things are right at the same time. The CPU is close enough. The memory map is close enough. The timers, interrupts, input, and graphics are all cooperating well enough for an old game to believe it is running on the machine it was written for.

That's what I liked most about the project.

Progress wasn't abstract. It appeared as pixels.

A broken instruction might turn into a frozen logo. A timing bug might turn into a flickering sprite. A missing hardware detail might turn into a game that almost worked, which was somehow more annoying than one that did not work at all.

RustyBoy taught me to enjoy that kind of debugging.

Not because it was easy, but because the feedback was brutally honest.
