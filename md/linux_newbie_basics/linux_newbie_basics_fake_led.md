## 本任务目标
- 在现有 `xr_hello_linux` 工程里，用一个 `bool` 变量当作“LED 状态”。
- 写一个简单循环：每隔一段时间翻转一次这个状态，并用 `LibXR::STDIO::Printf` 打印出来。
- 先熟悉“状态 + 循环 + Sleep”这一组最基础的用法，后面再上更复杂的东西。

> 说明：LibXR 里面 GPIO 有自己的抽象基类，但对 Linux 新手来说一次性上手会有点重，这一节故意只用一个 `bool`，把注意力放在“写 C++ + 用 LibXR 线程睡眠”上。

## 前置条件
- 已完成前一节 “Hello LibXR（Linux）”：
  - 工程目录为 `xr_hello_linux/`。
  - 顶层 `CMakeLists.txt` 已经引入 `libxr/` 子目录并链接 `xr` 库。
  - 可以在 VS Code 里用底部的 **Build** 按钮成功构建工程。

## 步骤 1：在 VS Code 中打开工程和 main.cpp
1. 终端进入工程根目录（路径按你自己的为准）：
```bash
cd ~/dev/xr_hello_linux
code .
```
2. 在 VS Code 左侧资源管理器中展开 `User/`，打开 `main.cpp`。

## 步骤 2：改造 main()，加入一个简单的“LED 状态”
假设已有的 `main` 只是在循环里打印“Hello LibXR on Linux”，现在我们把它替换成下面这种写法：

```cpp
#include "libxr.hpp"
#include "libxr_system.hpp"
#include "thread.hpp"

int main(int, char**) {
  LibXR::PlatformInit();

  bool led_on = false;  // 用一个布尔量代表“灯是亮还是灭”

  while (true) {
    led_on = !led_on;  // 每次循环翻转一次

    LibXR::STDIO::Printf("[LED] %s\n", led_on ? "ON" : "OFF");

    LibXR::Thread::Sleep(500);  // 约 500 ms，可以按喜好改长 / 改短
  }

  return 0;
}
```

要点：
- 不需要任何额外成员、结构体，一个 `bool` 就够用。
- 真正控制节奏的是 `LibXR::Thread::Sleep`，以后你在任何平台上写循环时，都可以用同样的接口来让线程“睡一会儿”。

## 步骤 3：在 VS Code 中构建并运行
1. 在 VS Code 底部状态栏找到 CMake Tools 区域：
   - 点击 **Build**（或“生成”）按钮，等待构建完成即可。
2. 构建成功后：
   - 仍然在底部 CMake Tools 区域，点击 **Run**（运行）按钮直接启动刚才构建出来的程序。
3. 观察 VS Code 下方“终端 / 输出”区域的程序输出，大致会是：
```text
[LED] ON
[LED] OFF
[LED] ON
[LED] OFF
...
```

## 完成标准
- 能在 VS Code 中点击 **Build** 顺利构建工程，无编译错误。
- 能通过底部的 **Run** 按钮启动程序，在输出窗口里看到 `[LED] ON` / `[LED] OFF` 有节奏地交替打印。
- 你知道这个“LED”只是一个布尔状态，用来练手，真正操作硬件 GPIO 会在 MCU 路线里单独讲。下节开始会在这个工程里继续加东西。
