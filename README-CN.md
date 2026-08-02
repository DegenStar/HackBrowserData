<div align="center">
<img src="LOGO.png" alt="hack-browser-data logo" width="440px" />
</div>

# HackBrowserData

[English README](README.md)

[![Lint](https://github.com/moonD4rk/HackBrowserData/actions/workflows/lint.yml/badge.svg)](https://github.com/moonD4rk/HackBrowserData/actions/workflows/lint.yml) [![Build](https://github.com/moonD4rk/HackBrowserData/actions/workflows/build.yml/badge.svg)](https://github.com/moonD4rk/HackBrowserData/actions/workflows/build.yml) [![Release](https://github.com/moonD4rk/HackBrowserData/actions/workflows/release.yml/badge.svg)](https://github.com/moonD4rk/HackBrowserData/actions/workflows/release.yml) [![Tests](https://github.com/moonD4rk/HackBrowserData/actions/workflows/test.yml/badge.svg?branch=main)](https://github.com/moonD4rk/HackBrowserData/actions/workflows/test.yml) [![codecov](https://codecov.io/gh/moonD4rk/HackBrowserData/branch/main/graph/badge.svg?token=KWJCN38657)](https://codecov.io/gh/moonD4rk/HackBrowserData)

`HackBrowserData` 是一款命令行工具，用于从浏览器解密并导出浏览器数据：密码、历史记录、Cookie、书签、信用卡、下载记录、localStorage、sessionStorage 和扩展。它支持 Windows、macOS、Linux 上主流的 Chromium 系浏览器和 Firefox，以及 macOS 上的 Safari。

它还可**跨机器、跨操作系统**解密数据：在源主机导出主密钥，然后在其他主机离线解密数据副本——即使分析主机的操作系统无法运行相应浏览器也可使用。

> 免责声明：本工具仅用于安全研究。用户须自行承担使用本工具产生的全部法律及相关责任，原作者不承担任何法律责任。

## 支持的数据类别

| 类别 | Chromium 系 | Firefox | Safari |
|:---|:---:|:---:|:---:|
| 密码 | ✅ | ✅ | ✅ |
| Cookie | ✅ | ✅ | ✅ |
| 书签 | ✅ | ✅ | ✅ |
| 历史记录 | ✅ | ✅ | ✅ |
| 下载记录 | ✅ | ✅ | ✅ |
| 信用卡 | ✅ | - | - |
| 扩展 | ✅ | ✅ | ✅ |
| LocalStorage | ✅ | ✅ | ✅ |
| SessionStorage | ✅ | - | - |

## 支持的浏览器

> 在 macOS 上，部分 Chromium 系浏览器解密时**需要当前用户密码**。
>
> macOS 26.4 或更高版本可能无法解密密码。

| 浏览器 | Windows | macOS | Linux |
|:---|:---:|:---:|:---:|
| Chrome | ✅² | ✅ | ✅ |
| Chrome Beta | ✅² | ✅ | ✅ |
| Chromium | ✅ | ✅ | ✅ |
| Edge | ✅² | ✅ | ✅ |
| Brave | ✅² | ✅ | ✅ |
| Opera | ✅ | ✅ | ✅ |
| OperaGX | ✅ | ✅ | - |
| Vivaldi | ✅ | ✅ | ✅ |
| Yandex | ✅ | ✅ | - |
| CocCoc | ✅² | ✅ | - |
| Arc | ✅ | ✅ | - |
| DuckDuckGo³ | ✅ | - | - |
| QQ³ | ✅ | - | - |
| 360 ChromeX³ | ✅ | - | - |
| 360 Chrome³ | ✅ | - | - |
| DC Browser³ | ✅ | - | - |
| Sogou Explorer³ | ✅ | - | - |
| Firefox | ✅ | ✅ | ✅ |
| Safari¹ | - | ✅ | - |

> ¹ Safari 需要“完全磁盘访问权限”；若提取结果为空，请在“系统设置 → 隐私与安全性 → 完全磁盘访问权限”中启用。
>
> ² 在 Windows 上，解密 Chromium 127+ 的 Cookie（Chrome / Chrome Beta / Edge / Brave / CocCoc）需要通过 `make build-windows` 构建 App-Bound Encryption 载荷。
>
> ³ 这些浏览器仅在 Windows 上发布，但其数据可在**任意操作系统**上解密：用 `archive` 获取文件、用 `dumpkeys` 导出密钥，再用 macOS 或 Linux 上的 `restore` 解密。

## 快速开始

### 安装

下载适用于你系统的[发布版本](https://github.com/moonD4rk/HackBrowserData/releases)并运行二进制文件。

> 某些情况下，Windows Defender 或其他杀毒软件会将此安全工具识别为病毒而阻止执行。全部代码均为开源，可自行修改和编译。

### 从源码构建

需要 `Go 1.20+`。

```bash
git clone https://github.com/moonD4rk/HackBrowserData
cd HackBrowserData
go build ./cmd/hack-browser-data/
```

#### 跨平台构建

```bash
# For Windows (standard build, no Chromium 127+ ABE cookie support)
GOOS=windows GOARCH=amd64 go build ./cmd/hack-browser-data/

# For Linux
GOOS=linux GOARCH=amd64 go build ./cmd/hack-browser-data/
```

#### 使用 App-Bound Encryption 的 Windows 构建（可选）

Chrome / Chrome Beta / Edge / Brave / CocCoc 127+ 使用 App-Bound Encryption 保护 Cookie。解密需要一个小型 C 载荷；推荐使用 [Zig](https://ziglang.org/)（0.13+）工具链（Makefile 会调用 `zig cc`）。也可手动使用 MinGW-w64 `gcc` 构建源文件。

```bash
# 1. Install Zig
brew install zig                 # macOS
scoop install zig                # Windows (scoop)
# or download from https://ziglang.org/download/

# 2. Build the payload (outputs crypto/windows/payload/abe_extractor_amd64.bin)
make payload

# 3. Build hack-browser-data.exe with the ABE payload embedded
make build-windows
```

生成的 `hack-browser-data.exe` 支持完整解密 Chromium 127+ 的 ABE Cookie。

## 用法

```
$ hack-browser-data -h
hack-browser-data decrypts and exports browser data from Chromium-based
browsers and Firefox on Windows, macOS, and Linux.

GitHub: https://github.com/moonD4rk/HackBrowserData

Usage:
  hack-browser-data [flags]
  hack-browser-data [command]

Available Commands:
  archive     将解密相关的配置文件打包成 zip 文件，以便跨主机恢复
  dump        提取并解密浏览器数据（默认命令）
  dumpkeys    将 Chromium 主密钥导出为 JSON，以便跨主机解密
  help        关于任意命令的帮助
  list        列出检测到的浏览器和配置文件
  restore     使用导出的主密钥解密已复制的配置文件数据
  version     打印版本信息

Flags:
  -b, --browser string        target browser: all|chrome|firefox|edge|... (default "all")
  -c, --category string       data categories (comma-separated): all|password,cookie,... (default "all")
  -d, --dir string            output directory (default "results")
  -f, --format string         output format: csv|json|cookie-editor (default "json")
  -h, --help                  help for hack-browser-data
      --keychain-pw string    macOS keychain password
  -p, --profile-path string   custom profile dir path, get with chrome://version
  -v, --verbose               enable debug logging
      --zip                   compress output to zip

Use "hack-browser-data [command] --help" for more information about a command.
```

### `dump`：提取并解密浏览器数据（默认命令）

不带子命令运行 `hack-browser-data` 时，默认执行 `dump`。

| 参数 | 简写 | 默认值 | 说明 |
|---|---|---|---|
| `--browser` | `-b` | `all` | 目标浏览器：all\|chrome\|firefox\|edge\|... |
| `--category` | `-c` | `all` | 数据类别，逗号分隔：all\|password\|cookie\|bookmark\|history\|download\|creditcard\|extension\|localstorage\|sessionstorage |
| `--format` | `-f` | `json` | 输出格式：csv\|json\|cookie-editor |
| `--dir` | `-d` | `results` | 输出目录 |
| `--profile-path` | `-p` | | 自定义配置目录路径，可通过 chrome://version 获取 |
| `--keychain-pw` | | | macOS 钥匙串密码 |
| `--zip` | | `false` | 将输出压缩为 zip |

> `--format cookie-editor` **仅写入 Cookie**，输出为与 Cookie-Editor 浏览器扩展导入格式一致的 JSON 数组；非 Cookie 类别会被跳过。

### 跨主机解密

可在**分析主机**上解密来自另一台**源主机**的数据，包括分析主机操作系统甚至无法安装的浏览器（例如在 macOS 上解密 Sogou 或 QQ Browser 数据）。平台绑定内容（DPAPI、macOS Keychain、Chrome App-Bound Encryption）无需离开源主机：主密钥只导出一次，之后可对数据副本完全离线解密。

流程使用三个命令和两个可传输制品：

| 步骤 | 主机 | 命令 | 产物 |
|---|---|---|---|
| 1 | 源主机 | `dumpkeys` | `keys.json`：可携带主密钥 |
| 2 | 源主机 | `archive` | `browser-data.zip`：解密所需文件 |
| 3 | 分析主机 | `restore` | 解密后的输出（csv / json / cookie-editor） |

```bash
# On the origin host (any OS) — export the keys and pack the data
hack-browser-data dumpkeys -o keys.json
hack-browser-data archive  -o browser-data.zip

# Copy keys.json + browser-data.zip to the analyst host, then decrypt offline
hack-browser-data restore --keys keys.json --data-zip browser-data.zip
```

> `keys.json` 包含明文主密钥，应按秘密数据处理。`dumpkeys -o` 会使用 `0600` 权限写入；建议通过安全通道流式传输，而非保留在磁盘上。

#### `dumpkeys`：导出用于跨主机解密的主密钥

在源主机派生每个 Chromium 安装的主密钥并写为 JSON（Firefox / Safari 没有可携带密钥，因此会跳过）。默认输出到 stdout，可通过 SSH 管道传输。

| 参数 | 简写 | 默认值 | 说明 |
|---|---|---|---|
| `--browser` | `-b` | `all` | 目标浏览器：all\|chrome\|edge\|... |
| `--output` | `-o` | *stdout* | 输出文件（以 `0600` 写入）；省略则输出到 stdout |
| `--keychain-pw` | | | macOS 钥匙串密码 |

#### `archive`：打包用于传输的解密相关文件

仅收集恢复实际需要的文件（Cookie、登录数据、历史记录等），并使用与提取相同的锁定文件绕过机制，因此 Windows 上可安全读取正在使用的 SQLite 文件。ZIP 的布局为 `<browser-key>/<User Data layout>`，可在一个归档中携带多个浏览器且恢复过程保持明确。条目名始终使用正斜杠，因此 Windows 生成的归档可在 macOS / Linux 上恢复。

| 参数 | 简写 | 默认值 | 说明 |
|---|---|---|---|
| `--browser` | `-b` | `all` | 目标浏览器：all\|chrome\|edge\|... |
| `--category` | `-c` | `all` | 数据类别，逗号分隔 |
| `--output` | `-o` | `browser-data.zip` | 输出归档路径 |

#### `restore`：用导出的主密钥解密已复制的配置数据

直接根据 `keys.json` 重建每个 Chromium 引擎并解密所提供的数据；不会查询分析主机的本地浏览器表，因此**可恢复的浏览器恰好是 `keys.json` 中的 vault**。数据只能以下列两种方式之一提供：

- `--data-zip`：由 `archive` 生成的 ZIP；会解压到临时目录并在结束后删除。
- `--data-dir`：目录。可以是 `archive` 布局（`<browser-key>/...`，多个浏览器），也可以是单一浏览器手动复制的 `User Data` 根目录；后者需搭配 `-b` 才不会有歧义。

`-b` 是对 dump 中 vault 的**可选筛选条件**，并非必需选择器。

| 参数 | 简写 | 默认值 | 说明 |
|---|---|---|---|
| `--keys` | | *required* | 来自 `dumpkeys` 的密钥文件（用 `-` 表示 stdin） |
| `--data-zip` | | | 来自 `archive` 的 ZIP（与 `--data-dir` 互斥） |
| `--data-dir` | | | 已复制的数据目录（与 `--data-zip` 互斥） |
| `--browser` | `-b` | | 仅恢复此浏览器；必须匹配 `--keys` 中的 vault |
| `--category` | `-c` | `all` | 数据类别，逗号分隔 |
| `--format` | `-f` | `json` | 输出格式：csv\|json\|cookie-editor |
| `--dir` | `-d` | `results` | 输出目录 |
| `--zip` | | `false` | 将输出压缩为 zip |

#### 跨主机示例

```bash
# Stream keys over SSH (no keys.json on disk), data copied separately
ssh origin "hack-browser-data dumpkeys" | \
  hack-browser-data restore --keys - --data-zip browser-data.zip

# Restore one browser from a hand-copied User Data folder (no archive)
hack-browser-data restore --keys keys.json --data-dir ./chrome-userdata -b chrome
```

### `list`：列出检测到的浏览器和配置文件

| 参数 | 默认值 | 说明 |
|---|---|---|
| `--detail` | `false` | 显示各类别的条目数量 |

### `version`：打印版本信息

```bash
hack-browser-data version
```

### 全局参数

| 参数 | 简写 | 说明 |
|---|---|---|
| `--verbose` | `-v` | 启用调试日志 |

### 示例

```bash
# Extract all data from all browsers (default)
hack-browser-data

# Extract specific browser and categories
hack-browser-data dump -b chrome -c password,cookie

# Export in CSV format to a custom directory (JSON is the default)
hack-browser-data dump -b chrome -f csv -d output

# Export cookies in CookieEditor format
hack-browser-data dump -f cookie-editor

# Compress output to zip
hack-browser-data dump --zip

# List detected browsers and profiles
hack-browser-data list

# List with per-category entry counts
hack-browser-data list --detail

# Use custom profile path
hack-browser-data dump -b chrome -p "/path/to/User Data/Default"
```

## 参与贡献

欢迎并感谢社区通过 GitHub issue、pull request、邮件反馈等方式作出的贡献。

贡献前请阅读[贡献指南](CONTRIBUTING.md)。

## 贡献者

贡献者名单由自动化 HTML 块维护；请参阅英文 README 中的完整名单：

<!-- readme: collaborators,contributors -start -->
请见 [README.md 的 Contributors](README.md#contributors)。
<!-- readme: collaborators,contributors -end -->

## Stargazers over time

[![Star History Chart](https://api.star-history.com/svg?repos=moond4rk/hackbrowserdata&type=Date)](https://github.com/moond4rk/HackBrowserData)

## 404StarLink 2.0 - Galaxy

`HackBrowserData` 是 404Team [StarLink-Galaxy](https://github.com/knownsec/404StarLink2.0-Galaxy) 的一部分。如对 `HackBrowserData` 有疑问，或希望寻找交流伙伴，请参阅 [Starlink group](https://github.com/knownsec/404StarLink2.0-Galaxy#community)。

<a href="https://github.com/knownsec/404StarLink2.0-Galaxy" target="_blank"><img src="https://raw.githubusercontent.com/knownsec/404StarLink-Project/master/logo.png" align="middle"/></a>

## JetBrains 开源许可证

`HackBrowserData` 在 JetBrains s.r.o. 授予的**免费 JetBrains 开源许可证**下，使用 `GoLand` IDE 开发；在此表示感谢。

<a href="https://www.jetbrains.com/?from=HackBrowserData" target="_blank"><img src="https://raw.githubusercontent.com/moonD4rk/staticfiles/master/picture/jetbrains-variant-4.png" width="256" align="middle"/></a>
