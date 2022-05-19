---
title: 简介
type: docs
weight: 1
---

![intro](./intro.png)

## 什么是NES？

[NES](https://en.wikipedia.org/wiki/Nintendo\_Entertainment\_System) (Nintendo Entertainment System) 是八九十年代最流行的家用游戏主机之一。
截止到目前，NES仍然非常具有活力，许多NES平台的游戏就算是放到现在也仍然非常有趣。
从硬件角度来看，NES的硬件实现非常的简单，所以让新手来编写一个NES模拟器也不是很困难。

本系列的目的是创建一个可以运行和游玩第一代NES游戏的模拟器，这些游戏有：

- [吃豆人](https://en.wikipedia.org/wiki/Pac-Man)
- [大金刚](https://en.wikipedia.org/wiki/Donkey\_Kong)
- [敲冰块](https://en.wikipedia.org/wiki/Ice\_Climber)
- [马力欧兄弟](https://en.wikipedia.org/wiki/Super\_Mario\_Bros)
- 等等。。。

本文采用增量的方式，渐进的讲解模拟器的实现，然后逐步完善成一个全能的模拟器。
编写模拟器的一大难点是，我们必须编写一个完整的模拟器才能运行NES游戏，期间很难得到一些反馈，这会使整个过程变得无趣。
所以我们尽量将任务分成独立的小块，然后在组装到一起。
毕竟一切都是为了好玩！

## 为什么选择 Rust？

[Rust](https://www.rust-lang.org/) 是一个专注于安全的现代化系统级编程语言。

> 如果你想对 Rust 进行一个初步的了解，可以观看此影片 ["Consider Rust"](https://www.youtube.com/watch?v=DnT-LUQgc7s)

Rust 允许我们在硬件和内存方面进行相对底层的操作，这非常适合编写硬件模拟程序。
例如，NES的CPU支持的大多数操作都是对 8-bit 无符号整型的位运算。
而 Rust 提供了出色的功能来处理不同大小的有符号和无符号整数，而无需任何额外的开销。
此外，Rust 生态提供了大量的库，使处理位级数据变得尽可能方便。

我们的目标是在我们目前拥有的硬件上面运行NES游戏，所以我们必须对NES主机的硬件进行模拟，这无可避免的会造成一些性能损耗。
选择 Rust 可以使我们获得更强的性能，虽然对目前的硬件速度来讲，使用任何语言来模拟NES都不成问题，甚至可以使用JavaScript编写模拟器在浏览器上面运行。
但是选择性能更好的语言总是不会错的。

## 必要条件

我希望读者具备 Rust 语言的基本知识，并了解主要的语言结构和平台功能。我将在我们进行的过程中介绍一些功能，但其他功能必须在其他地方学习。

还假设读者对位算术、布尔逻辑以及二进制和十六进制编号系统的工作原理有基本的了解。

虽然 NES 是一个相对简单的平台，NES CPU 指令集小而简单，但需要对计算机系统有一些基本的了解。

## 相关资料

1. [Nesdev Wiki](http://wiki.nesdev.com/w/index.php/Nesdev\_Wiki) - nothing would be possible without it. The one-stop-shop.
2. [Nintendo Entertainment System Documentation](http://nesdev.com/NESDoc.pdf) - a short tutorial that covers pretty much everything about NES
3. [Nintendo Age Nerdy Nights](https://nerdy-nights.nes.science/) - a series to help people write games for the NES
4. [I.Am.Error](https://www.goodreads.com/book/show/23461364-i-am-error) - a book full of histories of the Nintendo Entertainment System platform
5. [The Elements of Computing Systems](https://www.goodreads.com/book/show/910789.The\_Elements\_of\_Computing\_Systems) - everything you need to know about computer systems, how to build Tetris starting from logic gates.
