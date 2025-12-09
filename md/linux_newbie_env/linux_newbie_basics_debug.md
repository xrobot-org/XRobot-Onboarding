## 本任务目标
- 使用 VS Code 打开上一节创建的 `xr_hello_linux` 工程。
- 安装并启用 clangd / CMake Tools / CodeLLDB 三个插件。
- 在 CMake 中打开调试信息（`-g`），用 VS Code 的 CMake Tools 完成编译。
- 使用 CodeLLDB 调试器，按 F5 启动调试，让程序在 `main` 函数处停下。

本节开始之后，构建流程统一通过 **VS Code + CMake Tools** 完成，不再使用命令行手动调用 `cmake` / `cmake --build`。

---

## 前置条件
- 已完成 “Hello LibXR（Linux）” 任务，工程目录结构为：
  - `xr_hello_linux/`
    - `CMakeLists.txt`
    - `User/main.cpp`
    - `libxr/`
    - `build/`（之前已经成功编译过一次）。

---

## 步骤 1：在终端安装 clangd
在终端中执行（以 Ubuntu/Debian 为例）：
```bash
sudo apt update
sudo apt install -y clangd
```
安装完成后可简单检查：
```bash
clangd --version
```
看到版本号即可。

---

## 步骤 2：用 VS Code 打开工程并安装插件
1. 在终端进入工程根目录：
```bash
cd ~/dev/xr_hello_linux   # 按你的实际路径调整
code .
```

2. 在 VS Code 左侧「扩展」(Extensions) 面板中搜索并安装：
- **clangd**（C/C++ 语言服务）
- **CMake Tools**
- **CodeLLDB**

3. 安装完成后 **重启 VS Code**，再次用 `code .` 打开 `xr_hello_linux` 目录。

4. 打开任意 `.cpp` 文件，确认：
- 代码有高亮、跳转和补全（clangd 生效）；
- 窗口底部状态栏出现 CMake 相关区域（例如「Configure」「Build」按钮或齿轮图标）。

---

## 步骤 3：第一次用 CMake Tools 配置工程
1. 在 VS Code 底部状态栏找到 CMake Tools 区域（通常有一个显示当前 Kit/工具链的按钮）。
2. 第一次使用时会提示选择编译工具链（Kit），例如：
- `GCC ...` 或 `Clang ...` 之类的选项。
选择你系统中可用的编译器即可（一般选 GCC）。

3. 选择好 Kit 后，点击状态栏中的 **Configure**（配置）按钮：
- CMake Tools 会自动调用 CMake，在工程中生成或更新 `build` 目录；
- 过程完成后，状态栏会显示当前的构建类型（例如 Debug）。

4. 点击状态栏中的 **Build**（构建）按钮：
- CMake Tools 会在 `build/` 目录中编译工程；
- 终端面板中可以看到构建输出。

> 之后如果只需要“重新编译”，只要点击状态栏的 Build 按钮即可，不再手动敲命令行。

---

## 步骤 4：为调试启用 `-g` 并通过 VS Code 重新构建
1. 在 VS Code 中打开工程根目录下的 `CMakeLists.txt`。
2. 在 `set(CMAKE_CXX_STANDARD ...)` 附近添加一行编译选项：
```cmake
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_compile_options(-g)
```

3. 保存 `CMakeLists.txt`。

4. 回到底部状态栏：
- 先点击 **Configure** 让 CMake 重新配置工程；
- 再点击 **Build** 重新构建。

这样生成的 `build/xr_hello_linux` 就带有调试信息，可用于断点调试。

---

## 步骤 5：用 CodeLLDB 生成 launch.json
1. 在 VS Code 左侧点击「运行和调试」(Run and Debug) 图标。
2. 如果还没有调试配置，点击「运行和调试」按钮，在弹出的选择中选 **CodeLLDB**：
- VS Code 会在工程根目录下自动生成 `.vscode/launch.json` 文件。

3. 打开 `.vscode/launch.json`，找到 CodeLLDB 对应的配置项（`"type": "lldb"`）。

4. 把 `program` 字段改为指向我们编译出来的可执行文件：
```jsonc
"program": "${workspaceRoot}/build/xr_hello_linux",
```

5. 在同一个配置中加入以下字段，确保启动调试时会在 `main` 函数处自动停下：
```jsonc
"initCommands": [
  "breakpoint set -n main -N entry"
],
"exitCommands": [
  "breakpoint delete entry"
]
```

一个典型配置片段示例（只示意关键字段）：
```jsonc
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug xr_hello_linux (CodeLLDB)",
      "type": "lldb",
      "request": "launch",
      "program": "${workspaceRoot}/build/xr_hello_linux",
      "cwd": "${workspaceRoot}",
      "initCommands": [
        "breakpoint set -n main -N entry"
      ],
      "exitCommands": [
        "breakpoint delete entry"
      ]
    }
  ]
}
```

保存 `launch.json`。

---

## 步骤 6：在 VS Code 中启动调试
1. 确认刚才使用 CMake Tools 的 Build 已经成功（底部状态栏无错误提示）。
2. 打开「运行和调试」侧栏，在上方下拉列表中选择：
- `Debug xr_hello_linux (CodeLLDB)`（或你刚才看到的配置名称）。

3. 按下 **F5**，或点击绿色三角形按钮启动调试：
- 调试器启动后，会先加载程序，然后在 `main` 函数处自动停下；
- 你应该在编辑器里看到 `main` 的第一行被高亮，左侧可单步执行（F10/F11），下方可以查看变量和调用栈。

4. 点击「继续」（F5），程序会开始运行，终端中每秒打印一行 `Hello LibXR on Linux`。

---

## 完成标准
- VS Code 已安装并启用 clangd / CMake Tools / CodeLLDB，C++ 代码有补全和跳转，底部状态栏可以看到 CMake 工具栏和 Build 按钮。
- `CMakeLists.txt` 中添加了 `add_compile_options(-g)`，通过 VS Code 的 CMake Tools 完成 Configure + Build。
- `.vscode/launch.json` 中的 `program` 指向 `${workspaceRoot}/build/xr_hello_linux`，并配置了 `initCommands` / `exitCommands` 以在 `main` 停下。
- 在 VS Code 中按 F5 能启动调试，程序在 `main` 函数第一行暂停，之后可以单步或继续运行。

