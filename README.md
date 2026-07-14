# Xray VLESS + REALITY + Vision 一键安装脚本

适用于 Ubuntu 24.04 / 22.04 与 Debian 11/12 VPS 的安全加固与 Xray-core VLESS + REALITY + Vision 自动部署脚本。

## 功能特性

- VPS 基础安全加固（禁用 root SSH 登录、创建部署用户、开启公钥认证）
- UFW 防火墙自动配置
- Fail2ban 防暴力破解
- 自动安装 / 更新 Xray-core
- 自动生成 UUID、REALITY 密钥对、shortId
- 自动生成 VLESS 导入链接与二维码
- 网络参数调优（BBR、TCP Fast Open、MTU 探测）
- 安装失败自动回滚，避免半途而废导致服务器状态混乱
- 安装完成后，会在终端最后位置用**醒目颜色**单独展示订阅链接，避免被其他日志淹没

## 支持系统

- Ubuntu 24.04 LTS x64（推荐）
- Ubuntu 22.04 LTS x64
- Debian 11 / 12 x64
- 建议使用全新 VPS，或已确认状态干净的环境

## 一键安装

先切换到 root：

```bash
sudo -i
```

再执行安装：

```bash
apt update && apt install -y curl
bash <(curl -Ls https://raw.githubusercontent.com/hcloudlab/xray-vless-reality-vision/main/install.sh)
```

以上命令可直接复制粘贴到 VPS 终端执行，无需提前克隆仓库或下载任何文件。

## 自定义端口

默认入站端口为 `443/tcp`。Reality 用 443 最不容易被 GFW 阻断，推荐保持默认。

指定固定端口：

```bash
XRAY_PORT=2053 bash <(curl -Ls https://raw.githubusercontent.com/hcloudlab/xray-vless-reality-vision/main/install.sh)
```

随机端口（脚本自动挑选一个 20000-49999 的空闲端口）：

```bash
XRAY_PORT=random bash <(curl -Ls https://raw.githubusercontent.com/hcloudlab/xray-vless-reality-vision/main/install.sh)
```

> ⚠️ 随机端口是非 443 端口，Reality 在非 443 端口更容易被 GFW 阻断。如果随机端口连接超时，改用默认 443 重装即可。

## 自定义 REALITY SNI

默认 `REALITY_SERVER_NAME` 为 `speed.cloudflare.com`，默认 `REALITY_DEST` 为 `speed.cloudflare.com:443`。

```bash
REALITY_SERVER_NAME=www.apple.com REALITY_DEST=www.apple.com:443 bash <(curl -Ls https://raw.githubusercontent.com/hcloudlab/xray-vless-reality-vision/main/install.sh)
```

## 安装完成后

安装成功后，脚本会依次输出：

1. 完整客户端信息（服务器 IP、端口、UUID、REALITY 公钥、shortId 等）
2. 终端二维码，可直接扫码导入
3. **在最后位置用绿色/青色高亮单独重复显示订阅链接**，方便直接复制，不会被前面的安装日志淹没

客户端信息会永久保存在服务器本地：

```bash
cat /root/xray-reality-client.txt
```

二维码 PNG 文件保存在：

```bash
/root/xray-vless-reality-qr.png
```

扫码导入适用于以下客户端：

- Shadowrocket
- v2rayNG
- Hiddify
- NekoBox
- Clash / Clash Verge
- sing-box

## 状态检查

```bash
bash <(curl -Ls https://raw.githubusercontent.com/hcloudlab/xray-vless-reality-vision/main/status.sh)
```

会检查系统信息、Xray 版本、配置文件、服务状态、监听端口、防火墙、Fail2ban 状态等。

## 卸载

```bash
bash <(curl -Ls https://raw.githubusercontent.com/hcloudlab/xray-vless-reality-vision/main/uninstall.sh)
```

卸载脚本会逐项确认后再执行，默认不会改动 SSH / UFW / Fail2ban 配置，仅移除 Xray 本身与其配置。

## 常用命令

```bash
systemctl status xray
journalctl -u xray -e --no-pager
ufw status verbose
fail2ban-client status sshd
xray version
```

## 安全提示

- 本脚本采用保守的 SSH 加固策略：禁用 root 登录、启用公钥认证，但**默认不会强制关闭密码登录**，避免新用户被意外锁在门外。确认部署用户的 SSH 密钥登录可用后，建议手动关闭密码登录。
- 不要把 `/root/xray-reality-client.txt`、订阅链接或二维码对外公开分享。
- 如果修改了端口或 SNI，请确保客户端配置与服务器保持一致。
- 建议先备份 `/root/xray-reality-client.txt`。

## 故障排查

1. 先执行状态检查脚本查看 Xray 服务状态和日志（见上方"状态检查"）。
2. 确认入站端口（默认 `443/tcp`，或你自定义/随机的端口）已在 VPS 防火墙和云服务商安全组中放行。
3. 确认 REALITY SNI 域名（默认 `speed.cloudflare.com`）的 DNS 解析在服务器上正常。
4. 如果二维码无法显示，直接复制 `/root/xray-reality-client.txt` 里的原始 VLESS 链接手动导入。
5. 如果重新安装后链接失效，重新运行一键安装命令并检查终端最后输出的订阅链接。

## 项目结构

```text
xray-vless-reality-vision/
├── README.md
├── LICENSE
├── install.sh       # 一键安装（含加固、部署、订阅链接生成）
├── status.sh         # 状态检查
└── uninstall.sh      # 卸载
```

## License

MIT
