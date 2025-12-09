## 本任务目标
- 回顾这一段 Linux · 新手路线目前为止做了哪些事情，从“只看概念”走到了“能在 VS Code 里调试一个真程序”。
- 把几件事串成一条清晰的整体脉络：环境 → 工程骨架 → 调试。
- 想一想以后要扩展功能（线程 / 同步 / 终端 / 数据库等）时，大概会改动哪些位置。

本节不需要写新代码，主要是回顾和梳理。如果愿意，可以顺手再跑一遍构建和调试，当作自查。

---

## 你现在已经具备的东西
可以先在脑子里过一遍自己已经做过的操作：

- **理解层面**：
  - 知道在 Linux 上用 LibXR，本质上是在写普通 C++ 程序，只是把 LibXR 当作一个跨平台 C++ 库来链接。
  - 知道这条路线只关注 C++ 版 LibXR 的用法，不依赖 CodeGenerator，也用不到 `pip install libxr`。

- **环境层面**：
  - 系统里已经有了 g++/clang、CMake、ninja、gdb、Python3、Git 等基础工具。
  - VS Code 可以正常启动。

- **工程层面**：
  - 有一个 `xr_hello_linux` 工程，目录结构大致为：
    - 顶层 `CMakeLists.txt`；
    - `User/main.cpp`（你的入口文件）；
    - `libxr/`（LibXR 源码）；
    - `build/`（由 CMake 生成）。
  - 能通过 CMake 把 LibXR 加进来，编译出一个可执行文件。
  - 程序运行时，会每秒打印一行 `Hello LibXR on Linux`。

- **调试层面**：
  - VS Code 装好了 clangd / CMake Tools / CodeLLDB 三个插件。
  - CMake 开启了 `-g` 调试信息。
  - 可以用 CMake Tools 在 VS Code 里完成 Configure + Build。
  - `.vscode/launch.json` 配好了 CodeLLDB，F5 能在 `main` 处停下，再继续运行程序。

---

## 把这几步串成一条线
试着用自己的话，把当前这条线描述一遍：

1. **环境准备**：
   - Linux 自己提供内核和 C/C++ 运行时；
   - 你通过包管理器装好了编译器、CMake、调试器等开发工具；
   - VS Code 装好，用来写代码和进行图形化构建/调试。

2. **接上 LibXR**：
   - 通过 `git clone` 把 LibXR 源码放在 `libxr/`；
   - 在顶层 `CMakeLists.txt` 里用 `add_subdirectory(libxr)` 加进来；
   - 在自己的可执行目标上 `target_link_libraries(... xr)`，并把 LibXR 的头文件路径加入 include 路径。

3. **写一个最小可运行示例**：
   - 在 `User/main.cpp` 中调用 `LibXR::PlatformInit()` 做平台初始化；
   - 在一个循环里用 `STDIO::Printf` 打印一行文本，用 `Thread::Sleep` 控制节奏；
   - 编译运行，确认“库 + 工程 + 代码”这条链路是通的。

4. **让它变成“可调试的程序”**：
   - 在 CMake 中打开 `-g`；
   - 用 VS Code 的 CMake Tools 进行 Configure + Build；
   - 用 CodeLLDB 配好启动配置，让调试器在 `main` 停下；
   - 按 F5，观察到可以单步 / 继续运行，看到输出。

如果你能不看文档，大致把这几步讲出来，说明当前这部分已经真正消化了。

---

## 遇到问题时，大概从哪几层检查？
后面你在 Linux 上继续用 LibXR 时，可以按照类似的顺序排查：

1. **环境/工具链层**：
   - 编译器 / CMake / 调试器是否还在（升级系统之后有时会少包）。
   - VS Code 插件是否正常工作（尤其是 clangd 和 CMake Tools）。

2. **构建配置层（CMake）**：
   - `add_subdirectory(libxr)` 是否还在。
   - 可执行目标是否仍然链接了 `xr` 库。
   - 是否忘记在新的目标里也加上 LibXR 的 include 路径。

3. **代码层（你的 main / 其他源文件）**：
   - 是否仍然在合适的位置调用了 `LibXR::PlatformInit()`。
   - 是否在运行前就崩溃，可以借助调试器在 `main` 开头打断点看调用栈。

4. **调试配置层（VS Code）**：
   - `launch.json` 中 `program` 路径是否和现在的构建产物一致。
   - CMake 当前构建类型是不是 Debug，是否带 `-g`。

---

## 接下来可以去做什么？
在这一节结束后，你已经有了一个能跑、能调试的最小 LibXR 工程，后续在 Linux · 新手 路线中，大概率会往这些方向扩展：

- 在现有工程中增加更多源文件，而不是只写一个 `main.cpp`；
- 开始使用 LibXR 的线程 / 同步原语，写出更像“真实服务”的结构；
- 尝试终端、日志、数据库等中间件能力，让这个小程序变得更“好用”和更好排查问题。

这些都可以在当前骨架的基础上逐步添加，而不需要推翻重来。

---

## 完成标准
- 你能用自己的话顺一遍：从环境安装、创建工程、接入 LibXR、编译运行，到在 VS Code 中单步调试 `main` 的完整流程。
- 遇到“编译不过 / 跑不起来 / 调不进来”时，能够大致判断是环境问题、构建问题、代码问题还是调试配置问题，而不是完全摸不着头脑。
- 心里接受这样一件事：后面要学的内容会更丰富，但都会建立在这个简单、清晰的工程骨架之上，而不是再从零搭一次。
