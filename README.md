# KC Game Framework

一个极简的「框架式游戏」实现 —— 用 C 写核心框架，Python/Java 写玩法扩展，打包成 `.KCbox` 即插即用。

## ✨ 特性

- **C 语言核心**：扩展包扫描、解压、资源加载、控制台渲染循环
- **Python 脚本**：扩展包玩法逻辑入口，灵活易写
- **Java 算法模块（可选）**：扩展包内可嵌入 Java 算法包
- **KCbox 扩展格式**：本质是 zip，打包代码 + 资源，放入 `KC/` 目录即加载
- **零依赖渲染**：纯控制台 PPM 像素渲染，不需要图形库
- **自带 TCC 编译器**（完整版）：Windows 下无需安装 gcc，开箱即编译

## 📁 目录结构

```
KCGameFramework/
├── core/                 # C 语言核心框架源码
│   ├── main.c            # 程序入口
│   ├── kcbox.c / .h      # KCbox 扫描、解压、执行
│   ├── game.c / .h       # 游戏主循环
│   ├── render.c / .h     # 控制台渲染
│   ├── input.c / .h      # 输入处理
│   ├── world.c / .h      # 世界数据
│   └── ppm.c / .h        # PPM 图片读取
├── tools/                # Python 工具脚本
│   ├── gen_image.py      # 生成 PPM 测试图片
│   └── pack_box.py       # 打包 KCbox 扩展包
├── example_box/          # 示例扩展包（Python + Java）
│   ├── manifest.json     # 扩展包配置
│   ├── game.py           # Python 玩法脚本
│   ├── Algo.java         # Java 算法源码
│   └── assets/           # 资源目录（PPM 图片、数据文件）
├── example_sandbox/      # 示例沙盒扩展包（世界生成）
│   ├── manifest.json
│   ├── world_gen.py
│   └── assets/
├── KC/                   # 扩展包目录 —— 放入 .KCbox 即加载
│   └── KCDemo.KCbox
├── mods/                 # Mods 目录（预留）
├── tcc/                  # TCC 微型 C 编译器（仅完整版自带）
├── build.bat             # Windows 一键构建脚本
├── Makefile              # Linux / macOS / MinGW 构建
├── kcgame.exe            # 预编译主程序（仅完整版自带）
└── README.md             # 本文件
```

## 🚀 快速开始

### 下载发行包

| 包名 | 说明 |
|------|------|
| `KCGameFramework-src.zip` | 纯源代码，需要自备 gcc / Python |
| `KCGameFramework-full.zip` | 完整版，自带 TCC 编译器 + 预编译 exe，开箱即用 |

### 环境要求

- **Windows / Linux / macOS**
- **Python 3**（运行扩展包脚本必需）
- **C 编译器**：gcc / MinGW / TCC（完整版已自带 TCC）
- **Java JDK**（可选，仅编译带 Java 算法的扩展包时需要）

### 构建 & 运行

#### Windows（完整版，推荐）

直接双击运行 `kcgame.exe` 即可，无需编译。

如需重新构建：

```bat
build.bat
```

脚本会自动使用自带的 TCC 编译 `core/` 源码，并打包示例扩展包。

#### Windows（源码版）

确保已安装 gcc / MinGW 并加入 PATH：

```bat
build.bat
```

#### Linux / macOS

```bash
make
./kcgame
```

### 运行效果

程序启动后会：

1. 扫描 `KC/` 目录下所有 `.KCbox` 文件
2. 解压第一个扩展包到临时目录
3. 读取 `manifest.json` 显示扩展包信息
4. 在控制台渲染 `assets/logo.ppm` 像素图
5. 依次执行 Python 入口脚本和 Java 算法包

## 📦 制作你自己的 KCbox 扩展包

### 1. 基于示例修改

复制 `example_box/` 目录，改你自己的内容：

| 文件 | 作用 |
|------|------|
| `manifest.json` | 扩展包元数据配置 |
| `game.py` | Python 玩法主逻辑（入口脚本） |
| `Algo.java` | Java 算法模块（可选） |
| `assets/` | 资源目录，放 PPM 图片、txt 数据等 |

### 2. 配置 manifest.json

```json
{
  "name": "我的扩展包",
  "version": "1.0.0",
  "author": "你的名字",
  "assets": "assets",
  "entry": "game.py",
  "algo": "algo.jar"
}
```

| 字段 | 说明 |
|------|------|
| `name` | 扩展包名称 |
| `version` | 版本号 |
| `author` | 作者名 |
| `assets` | 资源目录名，默认 `assets` |
| `entry` | Python 入口脚本文件名 |
| `algo` | Java 算法 jar 包名（可选，没有就去掉） |

### 3. 打包

```bat
python tools\pack_box.py example_box -n 我的扩展包
```

生成的 `我的扩展包.KCbox` 放到 `KC/` 目录下，重启 `kcgame.exe` 即可加载。

> 💡 **小知识**：`.KCbox` 本质就是 zip 文件，手动把 zip 重命名成 `.KCbox` 也能用。

## 🎨 生成 PPM 图片

框架自带 PPM 图片生成工具：

```bat
python tools\gen_image.py -o logo.ppm -t smiley -W 128 -H 128
```

支持的图片类型：
- `gradient` — 渐变
- `checkerboard` — 棋盘格
- `smiley` — 笑脸
- `noise` — 噪点

## 🔧 核心模块说明

### core/ — C 语言核心

| 文件 | 职责 |
|------|------|
| `main.c` | 程序入口，初始化与主循环 |
| `kcbox.c` | KCbox 扫描、zip 解压、manifest 解析 |
| `game.c` | 游戏状态管理 |
| `render.c` | 控制台字符渲染（基于 PPM 像素） |
| `input.c` | 键盘输入处理 |
| `world.c` | 世界/地图数据结构 |
| `ppm.c` | PPM P6 格式图片解码 |

### tools/ — Python 工具

- `gen_image.py`：生成测试用 PPM 图片
- `pack_box.py`：将目录打包为 `.KCbox` 格式

## 💡 提示

- 框架默认加载 `KC/` 目录下**第一个**找到的 `.KCbox` 文件
- 扩展包解压到 `KC/_extracted/`，运行时自动生成，可安全删除
- 项目使用相对路径，整个文件夹可随意移动位置
- 想做多个扩展包？多复制几份 `example_box` 改就行

## 📄 许可证

本项目采用 [GNU General Public License v3.0](LICENSE) (GPL-3.0) 开源。

- ✅ 你可以自由使用、修改、分发
- ✅ 你可以商用
- ⚠️ **如果你修改了代码并分发，必须同样以 GPL-3.0 开源**
- ⚠️ **必须保留版权声明和许可证副本** 📄 许可证



## 🤝 贡献

欢迎提交 Issue 和 PR！有好点子尽管来。
