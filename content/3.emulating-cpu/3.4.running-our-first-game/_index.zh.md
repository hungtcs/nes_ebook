---
title: 运行我们的第一个游戏
type: docs
weight: 4
---

![image_1_progress.png](image_1_progress.png)

太好了，到目前为止一切都很棒！
我们接下来要做的就是找点乐趣。贪吃蛇游戏在这篇文章中介绍过：[Easy 6502](https://skilldrick.github.io/easy6502/#snake)。
事实上，它并不是真正的NES游戏。它使用纯 6502 指令构建，并使用完全不同的内存映射。

然而，这是一种验证我们的 CPU 是否真正正常工作的有趣方式，并且玩第一款游戏也很有趣。

当我们将在 PPU 中实现渲染时，我们现在要实现的大部分逻辑都会以某种方式重用，因此不会浪费任何精力。

贪吃蛇的游戏源代码是：

```rust
let game_code = vec![
  0x20, 0x06, 0x06, 0x20, 0x38, 0x06, 0x20, 0x0d, 0x06, 0x20, 0x2a, 0x06, 0x60, 0xa9, 0x02, 0x85,
  0x02, 0xa9, 0x04, 0x85, 0x03, 0xa9, 0x11, 0x85, 0x10, 0xa9, 0x10, 0x85, 0x12, 0xa9, 0x0f, 0x85,
  0x14, 0xa9, 0x04, 0x85, 0x11, 0x85, 0x13, 0x85, 0x15, 0x60, 0xa5, 0xfe, 0x85, 0x00, 0xa5, 0xfe,
  0x29, 0x03, 0x18, 0x69, 0x02, 0x85, 0x01, 0x60, 0x20, 0x4d, 0x06, 0x20, 0x8d, 0x06, 0x20, 0xc3,
  0x06, 0x20, 0x19, 0x07, 0x20, 0x20, 0x07, 0x20, 0x2d, 0x07, 0x4c, 0x38, 0x06, 0xa5, 0xff, 0xc9,
  0x77, 0xf0, 0x0d, 0xc9, 0x64, 0xf0, 0x14, 0xc9, 0x73, 0xf0, 0x1b, 0xc9, 0x61, 0xf0, 0x22, 0x60,
  0xa9, 0x04, 0x24, 0x02, 0xd0, 0x26, 0xa9, 0x01, 0x85, 0x02, 0x60, 0xa9, 0x08, 0x24, 0x02, 0xd0,
  0x1b, 0xa9, 0x02, 0x85, 0x02, 0x60, 0xa9, 0x01, 0x24, 0x02, 0xd0, 0x10, 0xa9, 0x04, 0x85, 0x02,
  0x60, 0xa9, 0x02, 0x24, 0x02, 0xd0, 0x05, 0xa9, 0x08, 0x85, 0x02, 0x60, 0x60, 0x20, 0x94, 0x06,
  0x20, 0xa8, 0x06, 0x60, 0xa5, 0x00, 0xc5, 0x10, 0xd0, 0x0d, 0xa5, 0x01, 0xc5, 0x11, 0xd0, 0x07,
  0xe6, 0x03, 0xe6, 0x03, 0x20, 0x2a, 0x06, 0x60, 0xa2, 0x02, 0xb5, 0x10, 0xc5, 0x10, 0xd0, 0x06,
  0xb5, 0x11, 0xc5, 0x11, 0xf0, 0x09, 0xe8, 0xe8, 0xe4, 0x03, 0xf0, 0x06, 0x4c, 0xaa, 0x06, 0x4c,
  0x35, 0x07, 0x60, 0xa6, 0x03, 0xca, 0x8a, 0xb5, 0x10, 0x95, 0x12, 0xca, 0x10, 0xf9, 0xa5, 0x02,
  0x4a, 0xb0, 0x09, 0x4a, 0xb0, 0x19, 0x4a, 0xb0, 0x1f, 0x4a, 0xb0, 0x2f, 0xa5, 0x10, 0x38, 0xe9,
  0x20, 0x85, 0x10, 0x90, 0x01, 0x60, 0xc6, 0x11, 0xa9, 0x01, 0xc5, 0x11, 0xf0, 0x28, 0x60, 0xe6,
  0x10, 0xa9, 0x1f, 0x24, 0x10, 0xf0, 0x1f, 0x60, 0xa5, 0x10, 0x18, 0x69, 0x20, 0x85, 0x10, 0xb0,
  0x01, 0x60, 0xe6, 0x11, 0xa9, 0x06, 0xc5, 0x11, 0xf0, 0x0c, 0x60, 0xc6, 0x10, 0xa5, 0x10, 0x29,
  0x1f, 0xc9, 0x1f, 0xf0, 0x01, 0x60, 0x4c, 0x35, 0x07, 0xa0, 0x00, 0xa5, 0xfe, 0x91, 0x00, 0x60,
  0xa6, 0x03, 0xa9, 0x00, 0x81, 0x10, 0xa2, 0x00, 0xa9, 0x01, 0x81, 0x10, 0x60, 0xa2, 0x00, 0xea,
  0xea, 0xca, 0xd0, 0xfb, 0x60
];
```

您可以在[此处](https://gist.github.com/wkjagt/9043907)找到带有注释的汇编代码。

游戏使用的内存映射：

| Address space | Type  | Description  |
|---|---|---|
| **0xFE** | Input | 随机数发生器 |
| **0xFF** | Input | 按下的按钮 |
| **[0x0200..0x0600]**  | Output |  屏幕. 每个单元格代表 32x32 矩阵中像素的颜色。矩阵从左上角开始，比如 **0x0200** 代表 (0,0) 的像素颜色， **0x0201** 代表 (1,0)， **0x0220** 代表 (0,1) ![image_2_screen_matrix.png](image_2_screen_matrix.png) |

游戏执行标准游戏循环：

* 读取用户输入
* 计算游戏状态
* 渲染游戏状态到屏幕
* 重复以上步骤

我们需要拦截这个循环来 **获取用户输入** 到 **输入映射空间** 并 渲染屏幕的状态。
让我们修改我们的 CPU 运行周期：

```rust
impl CPU {
  // ...
  pub fn run(&mut self) {
    self.run_with_callback(|_| {});
  }

  pub fn run_with_callback<F>(&mut self, mut callback: F) where F: FnMut(&mut CPU), {
    let ref opcodes: HashMap<u8, &'static opcodes::OpCode> = *opcodes::OPCODES_MAP;

    loop {
      callback(self);
      //....
      match code {
        //...
      }
      // ..
    }
  }
}
```

现在，客户端代码可以提供一个回调，该回调将在每个操作码解释周期之前执行。

`main` 方法的主要代码：

```rust
fn main() {
  let game_code = vec![
    // ...
  ];

  //load the game
  let mut cpu = CPU::new();
  cpu.load(game_code);
  cpu.reset();

  // run the game cycle
  cpu.run_with_callback(move |cpu| {
    // TODO:
    // read user input and write it to mem[0xFF]
    // update mem[0xFE] with new Random Number
    // read mem mapped screen state
    // render screen state
  });
}
```

对于我们的输入/输出，我们将使用在游戏开发中流行的跨平台库，即 [Simple DirectMedia Layer library](https://www.libsdl.org/) 库。

幸运的是，有一个方便的 crate 库提供了针对 Rust 的绑定：[rust-sdl2](https://rust-sdl2.github.io/rust-sdl2/sdl2/)

让我们添加到 `Cargo.toml`：

```toml
# ...

[dependencies]
lazy_static = "1.4.0"
bitflags = "1.2.1"

sdl2 = "0.34.0"
rand = "=0.7.3"
```

1. 首先，我们需要初始化 SDL：

    ```rust
    use sdl2::event::Event;
    use sdl2::EventPump;
    use sdl2::keyboard::Keycode;
    use sdl2::pixels::Color;
    use sdl2::pixels::PixelFormatEnum;

    fn main() {
      // init sdl2
      let sdl_context = sdl2::init().unwrap();
      let video_subsystem = sdl_context.video().unwrap();
      let window = video_subsystem
          .window("Snake game", (32.0 * 10.0) as u32, (32.0 * 10.0) as u32)
          .position_centered()
          .build().unwrap();

      let mut canvas = window.into_canvas().present_vsync().build().unwrap();
      let mut event_pump = sdl_context.event_pump().unwrap();
      canvas.set_scale(10.0, 10.0).unwrap();

      //...
    }
    ```

    因为我们的游戏屏幕很小（32x32 像素），我们将 `scale factor` 设置为 10。

    > 在这里使用 `.unwrap()` 是合理的，因为它是我们应用程序的外层。
    > 没有其他层可以处理 Err 值并对其进行处理。

    接下来，我们将创建一个用于渲染的纹理：

    ```rust
    //...
    let creator = canvas.texture_creator();
    let mut texture = creator.create_texture_target(PixelFormatEnum::RGB24, 32, 32).unwrap();
    //...
    ```

    我们告诉 SDL，我们的纹理大小为 32x32，每个像素由 3 个字节表示（用于 R、G 和 B 颜色）。这意味着纹理将由 32x32x3 字节数组表示。

2. 处理用户输入很简单：

    ```rust
    fn handle_user_input(cpu: &mut CPU, event_pump: &mut EventPump) {
      for event in event_pump.poll_iter() {
        match event {
          Event::Quit { .. } | Event::KeyDown { keycode: Some(Keycode::Escape), .. } => {
            std::process::exit(0)
          },
          Event::KeyDown { keycode: Some(Keycode::W), .. } => {
            cpu.mem_write(0xff, 0x77);
          },
          Event::KeyDown { keycode: Some(Keycode::S), .. } => {
            cpu.mem_write(0xff, 0x73);
          },
          Event::KeyDown { keycode: Some(Keycode::A), .. } => {
            cpu.mem_write(0xff, 0x61);
          },
          Event::KeyDown { keycode: Some(Keycode::D), .. } => {
            cpu.mem_write(0xff, 0x64);
          }
          _ => {/* do nothing */}
        }
      }
    }
    ```

3. 渲染屏幕状态有点棘手。我们的程序假设每个像素有 1 个字节，而 SDL 需要 3 个字节。
   从游戏的角度来看，我们如何映射颜色并不重要，唯一重要的两个颜色映射是：

    * 0 - 黑色
    * 1 - 白色

    ```rust
    fn color(byte: u8) -> Color {
      match byte {
          0 => sdl2::pixels::Color::BLACK,
          1 => sdl2::pixels::Color::WHITE,
          2 | 9 => sdl2::pixels::Color::GREY,
          3 | 10 => sdl2::pixels::Color::RED,
          4 | 11 => sdl2::pixels::Color::GREEN,
          5 | 12 => sdl2::pixels::Color::BLUE,
          6 | 13 => sdl2::pixels::Color::MAGENTA,
          7 | 14 => sdl2::pixels::Color::YELLOW,
          _ => sdl2::pixels::Color::CYAN,
      }
    }
    ```

    现在我们可以将 CPU 屏幕映射转换为 3 个字节，如下所示：

    ```rust
    let color_idx = cpu.mem_read(i as u16);
    let (b1, b2, b3) = color(color_idx).rgb();
    ```

    需要注意的是，如果屏幕状态没有改变，我们不想强制更新 SDL 画布。
    请记住，CPU 会在每条指令之后调用我们的回调，而且大多数时候这些指令与屏幕无关。
    同时，更新画布是一项繁重的操作。

    我们可以通过创建将从屏幕状态填充的临时缓冲区来跟踪屏幕状态。
    只有在屏幕发生变化的情况下，我们才会更新 SDL 画布。

    ```rust
    fn read_screen_state(cpu: &CPU, frame: &mut [u8; 32 * 3 * 32]) -> bool {
      let mut frame_idx = 0;
      let mut update = false;
      for i in 0x0200..0x600 {
        let color_idx = cpu.mem_read(i as u16);
        let (b1, b2, b3) = color(color_idx).rgb();
        if frame[frame_idx] != b1 || frame[frame_idx + 1] != b2 || frame[frame_idx + 2] != b3 {
          frame[frame_idx] = b1;
          frame[frame_idx + 1] = b2;
          frame[frame_idx + 2] = b3;
          update = true;
        }
        frame_idx += 3;
      }
      update
    }
    ```

现在游戏的主循环变成了：

```rust
fn main() {
  // ...init sdl
  // ...load program

  let mut screen_state = [0 as u8; 32 * 3 * 32];
  let mut rng = rand::thread_rng();

  cpu.run_with_callback(move |cpu| {
    handle_user_input(cpu, &mut event_pump);
    cpu.mem_write(0xfe, rng.gen_range(1, 16));

    if read_screen_state(cpu, &mut screen_state) {
      texture.update(None, &screen_state, 32 * 3).unwrap();
      canvas.copy(&texture, None, None).unwrap();
      canvas.present();
    }

    ::std::thread::sleep(std::time::Duration::new(0, 70_000));
  });
}
```

添加了最后一个睡眠语句以减慢速度，以便游戏以可玩的速度运行。

这就是我们模拟器上运行的第一款游戏。

![snk_game.gif](../snk_game.gif)

------

> 本章节代码: [GitHub](https://github.com/bugzmanov/nes_ebook/tree/master/code/ch3.4)
