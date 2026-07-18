# Xray VLESS + REALITY + Vision 一键安装脚本

适用于 Ubuntu 24.04 / 22.04 与 Debian 11/12 VPS 的 Xray-core VLESS + REALITY + Vision 一键安装脚本。

## 功能特性

- 自动安装 / 更新 Xray-core
- 自动生成 UUID、REALITY 密钥对、shortId
- 自动生成 VLESS 导入链接与二维码
- 网络参数调优（BBR、TCP Fast Open、MTU 探测）
- 如果 UFW 已启用，自动补充 Xray 入站端口规则；不会重置或启用 UFW
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

默认 `REALITY_SERVER_NAME` 为 `www.cloudflare.com`，默认 `REALITY_DEST` 为 `www.cloudflare.com:443`。

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

会检查系统信息、Xray 版本、配置文件、服务状态、监听端口、防火墙状态等。

## 卸载

```bash
bash <(curl -Ls https://raw.githubusercontent.com/hcloudlab/xray-vless-reality-vision/main/uninstall.sh)
```

卸载脚本会逐项确认后再执行，默认不会改动 SSH 或无关防火墙配置，仅移除 Xray 本身与其配置。

## 常用命令

```bash
systemctl status xray
journalctl -u xray -e --no-pager
ufw status verbose
xray version
```

## 安全提示

- 不要把 `/root/xray-reality-client.txt`、订阅链接或二维码对外公开分享。
- 如果修改了端口或 SNI，请确保客户端配置与服务器保持一致。
- 建议先备份 `/root/xray-reality-client.txt`。

## 故障排查

1. 先执行状态检查脚本查看 Xray 服务状态和日志（见上方"状态检查"）。
2. 确认入站端口（默认 `443/tcp`，或你自定义/随机的端口）已在 VPS 防火墙和云服务商安全组中放行。
3. 确认 REALITY SNI 域名（默认 `www.cloudflare.com`）的 DNS 解析在服务器上正常。
4. 如果二维码无法显示，直接复制 `/root/xray-reality-client.txt` 里的原始 VLESS 链接手动导入。
5. 如果重新安装后链接失效，重新运行一键安装命令并检查终端最后输出的订阅链接。

## 已知问题：Xray-core 26.7.11 起旧客户端无法连接

上游从 26.7.11 起将 REALITY 的默认 `minClientVer` 收紧为 `26.3.27`（有意为之的默认值变更，不是 bug，不会有"修复版本"）。服务端 core 为 26.7.11 及以上时，基于旧 core 的客户端（Shadowrocket、mihomo 等）会被直接拒绝，服务端日志报：

```text
REALITY: processed invalid connection ... authentication failed or validation criteria not met
```

该报错与"客户端参数填错"无法区分，请先按下面方法自查。

**本脚本当前锁定安装 Xray-core 26.6.27**（收紧前的已验证可用版本），以兼容存量客户端。如需安装其他版本，用环境变量覆盖：

```bash
XRAY_VERSION=26.7.11 bash <(curl -Ls https://raw.githubusercontent.com/hcloudlab/xray-vless-reality-vision/main/install.sh)
```

**存量用户自查：**

```bash
# 查看服务端 core 版本
xray version
/usr/local/x-ui/bin/xray-linux-amd64 version   # 如果用 3x-ui
```

若版本为 26.7.11 及以上且客户端连不上、日志报 authentication failed，说明客户端 core 旧于 26.3.27。三种处理方式：

1. **升级客户端**（推荐）：更新 Shadowrocket / mihomo 等到基于新 core 的版本，一劳永逸。
2. **降级服务端**：重新运行本脚本即可装回 26.6.27。临时方案，旧版本不会获得上游后续修复。
3. **显式放开 `minClientVer`**：在服务端 REALITY 配置中手动设置 `"minClientVer": "0.0.0"`。上游对该默认值的注释是 "change it at your own risk"，说明有安全考量；本脚本**不会**代替你写入这个字段，是否放开请自行判断并自担风险。

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
