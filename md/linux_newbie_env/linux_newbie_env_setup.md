## 本任务目标
- 在当前 Linux 上准备好后续开发要用的基础工具：gcc/g++、CMake、ninja、gdb、Python3、pip/pipx、Git。
- 安装 VS Code，并确认可以正常启动。
- 先只把环境打底，不拉 LibXR、不创建工程。

## 步骤 1：安装系统级开发工具（以 Ubuntu/Debian 为例）
在终端执行：
```bash
sudo apt update
sudo apt install -y \
  git \
  gcc g++ \
  cmake ninja-build \
  gdb \
  python3 python3-pip pipx \
  tar xz-utils wget \
  libudev-dev libnm-dev libwpa-client-dev
```
安装完成后，用下面这些命令简单检查是否可用（只要能看到版本号即可）：
```bash
g++ --version
cmake --version
ninja --version
gdb --version
python3 --version
pip3 --version
pipx --version
git --version
```
> 说明：这里安装的是通用开发环境，本路线当前阶段不会用 `pip install libxr`，因为那是 CodeGenerator 的 Python 包。

## 步骤 2：安装 VS Code
- 从 VS Code 官网下载适合你发行版的安装包（例如 `.deb` 或 `.rpm`）。
- 使用系统包管理器安装（例如在 `.deb` 所在目录执行：`sudo apt install ./code_*.deb`）。
- 安装完成后，在终端输入 `code`，确认 VS Code 能正常启动即可，本节不强制安装任何扩展。

## 步骤 3：配置 Git 基本信息
在终端执行：
```bash
git config --global user.name "你的名字"
git config --global user.email "your_email@example.com"
```
然后用下面命令确认设置生效：
```bash
git config --list
```
看到刚设置的 user.name / user.email 即可。

## 完成标准
- 终端中 `g++ --version`、`cmake --version`、`ninja --version`、`gdb --version`、`python3 --version`、`git --version` 都能正常输出版本号。
- 可以启动 VS Code（无论是终端输入 `code` 还是用图形界面打开）。
- Git 已设置好全局用户名和邮箱。
- 尚未拉取 LibXR 代码、也未创建任何 CMake 工程，只是把环境准备好，为后续任务做铺垫。
