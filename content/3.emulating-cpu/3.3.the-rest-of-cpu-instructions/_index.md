---
title: Implementing The Rest Of Cpu Instructions
type: docs
---

 <div style="text-align:center"><img src="./images/ch3.3/image_1_how_to_draw_owl.png" width="60%"/></div>

Implementing the rest of the 6502 CPU instructions should be relatively straightforward. I won't go into detail for all of them.

Just some remarks:
* **ADC** is perhaps the most complicated instruction from a logic flow perspective. Note that the spec contains details regarding decimal mode that can be entirely skipped because the Ricoh modification of the chip didn't support decimal mode.
> This article goes into a detailed overview of how binary arithmetic is implemented in 6502: [The 6502 overflow flag explained mathematically ](http://www.righto.com/2012/12/the-6502-overflow-flag-explained.html)
>
>For the curious and brave souls: [The 6502 CPU's overflow flag explained at the silicon level ](http://www.righto.com/2013/01/a-small-part-of-6502-chip-explained.html)

* After ADC is implemented, implementing **SBC** becomes trivial as
`A - B = A + (-B)`.
And `-B = !B + 1`

* **PHP**, **PLP** and **RTI** have to deal with [2 bit B-flag](http://wiki.nesdev.com/w/index.php/Status_flags#The_B_flag). Except for interrupts execution, those are the only commands that directly influence (or being directly influenced by) the 5th bit of **Status register P**

* The majority of the branching and jumping operations can be implemented by simply modifying the **program_counter** register. However, be careful not to increment the register within the same instruction interpret cycle.

If you get stuck, you can always look up the implementation of 6502 instruction set here: <link to code>


<br/>

------

> The full source code for this chapter: <a href="https://github.com/bugzmanov/nes_ebook/tree/master/code/ch3.3" target="_blank">GitHub</a>
