+++
title = "Molecule"
description = "A small Unix-like kernel experiment written in Rust."
date = 2026-05-16

[taxonomies]
tags = ["rust", "operating-systems", "kernel", "systems-programming"]
+++

[Molecule](https://github.com/EthanPlant/Molecule) is a small Unix-like kernel experiment written in Rust.

It is not a production operating system. It's not trying to be one. It's a project I built because I wanted to understand more of what happens below the software I normally work on.

Most software runs on top of a very large stack of assumptions. Files exist. Memory exists. Processes exist. Timers exist. The screen exists. Other CPUs exist. The machine boots and somehow hands control to your code.

A kernel project forces you to meet those assumptions one at a time.

## What it is

Molecule is a lightweight kernel experiment with pieces of a Unix-like system.

It includes work on:

- booting through UEFI with Limine
- basic console output
- memory management
- allocation
- scheduling
- ACPI
- SMP
- early system structure

The interesting part is not that any one of those pieces is especially novel. The interesting part is seeing how quickly “simple” things stop being simple once there is no operating system underneath you.

Printing text becomes a milestone.

Allocating memory becomes a design decision.

Scheduling becomes a negotiation with reality.

## Why I built it

I really like systems that become less mysterious when you take them apart.

Operating systems are one of the best examples of that. From the outside, they feel enormous and opaque. From the inside, they are still enormous, but the pieces become more understandable.

A kernel is a good antidote to magical thinking in software.

There is no framework to hide behind. No runtime quietly fixing things. No service you can call when the machine is not configured yet.

At some point, the CPU jumps to your code and your code either knows what to do next or it does not.

That is a useful kind of humility.

## What I learned

The biggest lesson was how much of modern software depends on layers of work we rarely think about.

A normal program begins with an absurd amount of help already in place. It has an address space, a stack, system calls, file descriptors, threads, memory allocation, timers, drivers, and a terminal or windowing system waiting for it.

Kernel work removes that comfort.

You start with almost nothing, then slowly build enough structure for the machine to feel usable.

That process makes familiar abstractions feel earned again.

It also makes you appreciate boring infrastructure. A scheduler that works. A bootloader that gives you the information you need. A memory allocator that does not quietly betray you. A console that prints what you asked it to print.

None of these things are glamorous. All of them matter.

## Why Rust

Rust is a good fit for this kind of project because it lets you work close to the machine while still having a language that pushes back against some of the easiest mistakes.

That doesn't make kernel work safe.

It does make certain kinds of unsafety more explicit.

In a kernel, that distinction matters. There are still raw pointers, architecture details, unsafe blocks, and places where the compiler cannot save you. But Rust encourages you to draw boundaries around those parts instead of letting them leak everywhere.

That is useful.

Not magic. Useful.

## Current state

Molecule is best understood as a learning project and systems programming artifact.

It is a record of trying to understand booting, memory, scheduling, and early kernel structure by building pieces of them directly.

That is why I still think it is worth keeping around.

Some projects are valuable because they become finished products.

Some are valuable because they permanently change how you see the systems underneath everything else.

Molecule is the second kind.
