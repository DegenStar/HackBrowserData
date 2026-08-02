# HackBrowserData 极简使用指南

> 仅在你拥有授权的浏览器数据上使用。输出可能包含密码、Cookie 和会话数据，请妥善保管。

## macOS

1. 安装 Go 1.20+：`brew install go`
2. 构建：

```bash
git clone https://github.com/moonD4rk/HackBrowserData
cd HackBrowserData
go build ./cmd/hack-browser-data/
```

3. 查看浏览器：`./hack-browser-data list`
4. 导出书签和历史到 `results`：

```bash
./hack-browser-data dump -c bookmark,history
```

Safari 提取为空时，在“系统设置 → 隐私与安全性 → 完全磁盘访问权限”中授权终端。不要使用 `sudo`。

## Linux

1. 安装 Go 1.20+（例如 Debian/Ubuntu：`sudo apt install golang-go`）。
2. 构建：

```bash
git clone https://github.com/moonD4rk/HackBrowserData
cd HackBrowserData
go build ./cmd/hack-browser-data/
```

3. 使用：

```bash
./hack-browser-data list
./hack-browser-data dump -b chrome -c bookmark,history
```

若需读取受系统钥匙串保护的数据，请在正常桌面会话中运行，勿使用 `sudo`。

## Windows

1. 安装 Go 1.20+：<https://go.dev/dl/>。
2. 在 PowerShell 中构建：

```powershell
git clone https://github.com/moonD4rk/HackBrowserData
cd HackBrowserData
go build ./cmd/hack-browser-data/
```

3. 使用：

```powershell
.\hack-browser-data.exe list
.\hack-browser-data.exe dump -b chrome -c bookmark,history
```

不要以管理员身份运行。新版 Chrome Cookie 的 App-Bound Encryption 支持需要额外构建载荷，详见 [README-CN.md](README-CN.md)。

## WSL

WSL 中运行的程序通常无法直接读取 Windows 浏览器的 DPAPI/凭据数据。建议在 Windows PowerShell 中运行 Windows 版本。

若只处理已经复制到 WSL 的数据，可在 WSL 中构建 Linux 版本：

```bash
sudo apt update && sudo apt install -y golang-go git
git clone https://github.com/moonD4rk/HackBrowserData
cd HackBrowserData
go build ./cmd/hack-browser-data/
./hack-browser-data list
```

## 输出与清理

默认输出目录为 `results`。只导出需要的类别；完成后将结果移至加密存储或安全删除，切勿分享 `keys.json`、Cookie 或密码导出文件。
