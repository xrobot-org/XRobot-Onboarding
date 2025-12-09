## 本任务目标
- 在 `xr_hello_linux` 工程中加入 RamFS 和 `LibXR::Terminal`，在 Linux 控制台里体验和 MCU 路线同一套终端风格：`ls`、`cd`、历史记录等。
- 让终端独立跑在一个 LibXR 线程里，主线程不再不停打印 `[LED]`，避免输出互相打架。

后面章节有机会把当前的 `bool led_on` 挂到命令行上，用命令来控制“亮 / 灭”。这节先专注于让终端跑起来。

## 前置条件
- 已完成上一节“用一个布尔量当作“LED”：LibXR 上的第一个循环”：
  - 工程仍能在 VS Code 中通过 **Build** 成功构建。
  - `User/main.cpp` 里已经有 `LibXR::PlatformInit();`，并写过一个使用 `LibXR::Thread::Sleep` 的循环。

## 步骤 1：在 main.cpp 中引入 RamFS 和 Terminal
1. 打开 `User/main.cpp`。
2. 在已有的 `#include` 下方补上：
```cpp
#include "ramfs.hpp"
#include "terminal.hpp"
```

## 步骤 2：简化主循环，为终端腾出“干净输出”
为了让命令行输出更清晰，这一节先把之前不停刷 `[LED]` 的循环去掉，只保留一个“保持程序存活”的空循环。可以改成类似下面这样：

```cpp
#include "libxr.hpp"
#include "libxr_system.hpp"
#include "thread.hpp"
#include "ramfs.hpp"
#include "terminal.hpp"

int main(int, char**) {
  LibXR::PlatformInit();

  // 后面可以用它来表示“LED 当前状态”，本节先不用
  bool led_on = false;

  // 1. 创建一个内存文件系统
  LibXR::RamFS ramfs;

  // 2. 基于 RamFS 创建终端对象
  LibXR::Terminal<1024, 64, 16, 128> terminal(ramfs);

  // 3. 用一个线程跑终端
  LibXR::Thread term_thread;
  term_thread.Create(
      &terminal,
      LibXR::Terminal<1024, 64, 16, 128>::ThreadFun,
      "terminal",
      65536,
      LibXR::Thread::Priority::MEDIUM);

  // 主线程只需要保持活着即可
  while (true) {
    LibXR::Thread::Sleep(1000);
  }

  return 0;
}
```

要点：
- 现在“写很多东西到屏幕”的只有终端自己，之前那种一秒一行 `[LED]` 的打印先停掉，方便你看清命令行。
- `led_on` 先留在这里。以后可以通过终端命令去改它，这样“LED 的状态”就完全由命令行控制，而不是死循环。

## 步骤 3：在 VS Code 中构建
1. 点击 VS Code 底部的 **Build** 按钮。
2. 构建成功即可，无需手动运行 `cmake` / `make` 之类的命令。

若编译失败，优先检查：
- 是否已经添加了 `#include "ramfs.hpp"` 和 `#include "terminal.hpp"`。
- `CMakeLists.txt` 中是否仍然保留：`add_subdirectory(libxr)` 和 `target_link_libraries(... xr)`。

## 步骤 4：运行程序并体验终端
1. 在 VS Code 底部 CMake Tools 区域点击 **Run** 按钮，启动程序。
2. 程序跑起来后，你应该在下方的终端/输出中看到终端的提示符（具体长相以 LibXR 当前实现为准，通常会有一个代表当前目录的前缀和一个符号）。
3. 尝试输入几条命令并回车：
   - `ls` —— 查看当前 RamFS 中的目录内容。
   - `cd /`、`cd ..` —— 切换目录。
   - 使用键盘方向键上下翻动历史命令，体验行编辑和历史记录。

这一节只要求你熟悉终端“有这个东西”、“大致长什么样”，不需要自己写命令实现，也不需要去理解 RamFS 的内部结构。

## 完成标准
- 可以在 VS Code 中点击 **Build** 成功构建工程。
- 通过底部的 **Run** 按钮运行程序后，能够看到 LibXR 终端的提示符并与之交互。
- 能够输入 `ls`、`cd` 等基础命令，并看到合理的反馈。
- 你知道：
  - 终端实际跑在一个 LibXR 的线程里。
  - RamFS 是终端背后用的内存文件系统，本节只需要知道有这么一层即可。
  - 之前用来练手的 `bool led_on` 还在，以后可以通过命令行来控制它，而不再用死循环刷日志。
