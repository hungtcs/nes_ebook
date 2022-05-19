---
title: Introduction
type: docs
weight: 1
---

![intro](./intro.png)

## Why NES?

The [NES](https://en.wikipedia.org/wiki/Nintendo\_Entertainment\_System) (Nintendo Entertainment System) was one of the most popular gaming platforms throughout the 80s and the 90s. The platform and the emergent ecosystem was and still is a huge cultural phenomenon. The device itself had relatively simple hardware (judging from the modern days), and it's incredible how much was made out of it.

This series is about creating an emulator capable of running and playing first-gen NES games, such as:

* [PacMan](https://en.wikipedia.org/wiki/Pac-Man)
* [Donkey Kong](https://en.wikipedia.org/wiki/Donkey\_Kong)
* [Ice Climber](https://en.wikipedia.org/wiki/Ice\_Climber)
* [Super Mario Bros](https://en.wikipedia.org/wiki/Super\_Mario\_Bros)
* etc.

We would go with incremental updates, with potentially enjoyable milestones, gradually building a fully capable platform. One of the problems with writing an emulator is that you don't get any feedback until the very end when the whole thing is done, and that's no fun. I've tried to break the entire exercise into small pieces with visible and playable goals. After all, it's all about having a good time.

## Why Rust?

Rust is a modern language with modern expression capabilities and impressive performance characteristics.

> For an overview of the language, I recommend watching ["Consider Rust"](https://www.youtube.com/watch?v=DnT-LUQgc7s) presentation by Jon Gjengse.

The Rust programming language allows us to go as low-level as needed in terms of hardware and memory management, which is a good fit for the problem of hardware simulation. For example, NES has a Central Processing Unit (CPU), and the majority of supported operations are dealing with unsigned 8-bit arithmetic and bit manipulation. Rust provides excellent capabilities for working with signed and unsigned numbers of different sizes without any overhead. In addition, the Rust ecosystem offers a plethora of libraries that make working on bit-level data as convenient as it gets.

The goal is to play NES games on the hardware that we have, meaning we have to simulate NES hardware. The process of simulation alone means that we are introducing significant performance overhead in comparison to running native applications. By choosing rust, we hope to get some additional performance budget for our needs. NES hardware specs are pretty modest by today's standards. For example, the NES CPU is about 3000 times slower than modern CPUs. Emulating that in any language should not be a problem. Some folks were able to get playable performance on an emulator written in Python. But it is still nice to have extra power for free.

## Prerequisites

I expect the reader to have basic knowledge of the Rust language and understanding primary language constructs and platform capabilities. I'll introduce some features as we go, but others have to be learned elsewhere.

It's also assumed that the reader has a basic understanding of bit arithmetic, boolean logic, and how binary and hexadecimal numbering systems work. Again, NES is a relatively simple platform, and the NES CPU instructions set is small and straightforward, but some basic understanding of computer systems is required.

## References

1. [Nesdev Wiki](http://wiki.nesdev.com/w/index.php/Nesdev\_Wiki) - nothing would be possible without it. The one-stop-shop.
2. [Nintendo Entertainment System Documentation](http://nesdev.com/NESDoc.pdf) - a short tutorial that covers pretty much everything about NES
3. [Nintendo Age Nerdy Nights](https://nerdy-nights.nes.science/) - a series to help people write games for the NES
4. [I.Am.Error](https://www.goodreads.com/book/show/23461364-i-am-error) - a book full of histories of the Nintendo Entertainment System platform
5. [The Elements of Computing Systems](https://www.goodreads.com/book/show/910789.The\_Elements\_of\_Computing\_Systems) - everything you need to know about computer systems, how to build Tetris starting from logic gates.

***

Created by [@bugzmanov](http://twitter.com/bugzmanov), 2020

_Special thanks to_ [_Spencer Burris_](https://github.com/sburris0) _and_ [_Kirill Gusakov_](https://github.com/kgusakov/) _for reviews, edits and helpfull suggestions!_
