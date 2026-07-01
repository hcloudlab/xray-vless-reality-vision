# Secure VPS Xray Reality Installer

Automated Ubuntu 24.04 VPS hardening and Xray-core VLESS + REALITY + Vision deployment script.

适用于 Ubuntu 24.04 VPS 的安全加固与 Xray-core VLESS + Reality + Vision 自动部署脚本。

## Features

- Ubuntu 24.04 VPS security hardening
- UFW firewall configuration
- Fail2ban SSH protection
- Xray-core installation
- VLESS + REALITY + Vision configuration
- Automatic UUID generation
- Automatic REALITY key pair generation
- Automatic shortId generation
- Shadowrocket / v2rayN compatible VLESS link output
- One-command deployment

## Supported System

- Ubuntu 24.04 LTS x64
- Fresh VPS recommended

## Quick Start

```bash
sudo -i
apt update && apt install -y curl
bash <(curl -Ls https://raw.githubusercontent.com/hexa46656-creator/secure-vps-xray-reality-installer/main/install.sh)
```

## Custom Port

Default Xray inbound port is `8443`.

```bash
XRAY_PORT=8443 bash <(curl -Ls https://raw.githubusercontent.com/hexa46656-creator/secure-vps-xray-reality-installer/main/install.sh)
```

## Custom REALITY SNI

Default SNI is `www.microsoft.com`.

```bash
REALITY_SERVER_NAME=www.apple.com REALITY_DEST=www.apple.com:443 bash <(curl -Ls https://raw.githubusercontent.com/hexa46656-creator/secure-vps-xray-reality-installer/main/install.sh)
```

## Client Info

After installation, client information is saved at:

```bash
/root/xray-reality-client.txt
```

View it:

```bash
cat /root/xray-reality-client.txt
```

## Useful Commands

```bash
systemctl status xray
journalctl -u xray -e --no-pager
ufw status verbose
fail2ban-client status sshd
xray version
```

## Project Structure

```text
secure-vps-xray-reality-installer/
├── README.md
├── LICENSE
├── .gitignore
├── install.sh
├── uninstall.sh
├── update.sh
├── status.sh
├── reset-client.sh
└── docs/
    ├── shadowrocket.md
    └── troubleshooting.md
```

## Installation Output

After successful installation, the script will generate:

- Server IP
- Port
- UUID
- REALITY public key
- REALITY shortId
- VLESS import link

The client information will be saved to:

```bash
/root/xray-reality-client.txt
```

## Security Notes

This script uses a conservative SSH hardening strategy:

- Root SSH login is disabled.
- Public key authentication is enabled.
- Password authentication is not forcibly disabled by default to avoid locking new users out.

After confirming your SSH key login works, you may manually disable SSH password login.

## Common Commands

Check Xray:

```bash
systemctl status xray
```

View Xray logs:

```bash
journalctl -u xray -e --no-pager
```

Check firewall:

```bash
ufw status verbose
```

Check Fail2ban:

```bash
fail2ban-client status sshd
```

## Update

```bash
bash update.sh
```

## Uninstall

```bash
bash uninstall.sh
```

## License

MIT

## 中文说明

### 项目简介

本项目用于在 Ubuntu VPS 上一键部署 Xray-core VLESS + REALITY + Vision。安装完成后，脚本会输出客户端链接、订阅链接，并把客户端信息保存到本机。

### 支持系统

- Ubuntu 24.04 LTS x64
- 建议使用全新 VPS 或已确认可用的干净环境

### 一键安装命令

```bash
sudo -i
apt update && apt install -y curl
bash <(curl -Ls https://raw.githubusercontent.com/hexa46656-creator/xray-vless-reality-vision/main/install.sh)
```

### 默认端口

- 默认入站端口：`8443/tcp`

### 默认 SNI

- 默认 `REALITY_SERVER_NAME`：`speed.cloudflare.com`
- 默认 `REALITY_DEST`：`speed.cloudflare.com:443`
- 如果你手动传入参数，脚本会保留你的自定义值

### 安装完成后的客户端链接

- 客户端信息会保存到：`/root/xray-reality-client.txt`
- 安装完成后，脚本会在终端显示原始 VLESS 链接和订阅链接
- 你可以直接复制链接导入，也可以使用下方二维码扫码导入

### 二维码扫码导入

安装完成后，脚本会同时显示二维码并保存 PNG 文件。

- 二维码内容优先使用脚本最终生成的订阅链接
- 如果订阅链接不可用，会回退到原始 `vless://` 链接
- PNG 文件保存路径：`/root/xray-vless-reality-qr.png`

扫码导入适用于以下客户端：

- Shadowrocket
- v2rayNG
- Hiddify
- NekoBox
- Clash / Clash Verge

### 状态检查命令

```bash
bash status.sh
```

### 卸载命令

```bash
bash uninstall.sh
```

### 安全提示

- 不要把客户端信息公开分享
- 如果你修改了端口或 SNI，请重新确认服务器配置和客户端配置一致
- 建议先备份 `/root/xray-reality-client.txt`

### 故障排查

1. 先执行 `bash status.sh` 查看 Xray 服务状态和日志
2. 确认 `8443/tcp` 已在 VPS 防火墙和云安全组中放行
3. 确认 `speed.cloudflare.com` 的 DNS 解析正常
4. 如果二维码无法显示，直接复制 `/root/xray-reality-client.txt` 里的原始链接手动导入
5. 如果订阅或客户端链接失效，重新运行安装脚本并检查生成输出
