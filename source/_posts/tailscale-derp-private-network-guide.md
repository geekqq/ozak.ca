---
title: 完全免费！从零开始搭建你的私人网络：Tailscale + 国内DERP免费中继完全指南
date: 2026-03-15 08:30:00
tags:
  - Tailscale
  - DERP
  - 内网穿透
  - WireGuard
  - Homelab
categories:
  - blog
---

写在前面：为什么你需要这个？

你有没有遇到过这样的场景：在公司突然想起家里电脑上有个重要文件忘记带了？或者出差在外，想远程连接家里的 NAS 下载点东西？又或者你养了一只“大龙虾”（OpenClaw 项目），需要在不同设备之间无缝切换工作环境？

传统的解决方案要么复杂（配置 VPN、端口转发），要么贵（购买商业远程桌面服务），要么不安全（直接暴露公网 IP）。但今天要分享的这个方案，基本可以同时解决这几个问题：**完全免费、安全、简单，而且跨平台。**

这套方案的核心就是：

- **Tailscale**：把你的设备拉进一个私人的虚拟局域网
- **国内 DERP 中继**：在某些网络环境下优化连接体验

## 什么是 Tailscale？简单说就是“私人专属网络”

你可以把 Tailscale 理解成一个“帮你自动组网的工具”。

想象一下，你的手机、平板、家里的电脑、公司的电脑，甚至树莓派和 NAS，都像在同一个局域网里一样，可以互相访问：

- 不需要记复杂的公网 IP
- 不需要折腾端口转发
- 不需要公网 IP
- 甚至不需要你真的很懂网络

Tailscale 基于 **WireGuard** 协议，而 WireGuard 本身就是目前非常快、非常现代、也非常安全的 VPN 技术之一。Tailscale 的价值在于，它把大部分复杂配置都藏起来了，你只需要登录、授权、安装客户端，就能把设备连起来。

更重要的是，**Tailscale 个人使用免费版支持最多 100 台设备**，对于绝大多数个人用户、家庭用户、小型 homelab 玩家来说，基本够用了。

## 第一步：注册和安装 Tailscale

### 注册账号

先访问 Tailscale 官网：

- https://tailscale.com

点击 **Get Started** 注册账号。可以直接使用这些账号登录：

- Google
- GitHub
- Microsoft

这一步很顺滑，几乎没有门槛。

### 安装客户端

Tailscale 几乎支持所有主流平台。

#### Windows

下载安装包：

- https://tailscale.com/download/windows

安装完成后，点击系统托盘的 Tailscale 图标，选择 **Log in**，浏览器会自动打开授权页面。

#### macOS

可以直接从 Mac App Store 安装，也可以下载安装包。

安装后同样登录授权即可。

#### Linux

以 Ubuntu 为例：

```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```

运行之后会给你一个授权链接，复制到浏览器里完成授权即可。

#### iPhone / Android

在 App Store 或 Google Play 搜索 **Tailscale**，下载安装后登录你的账号就行。

#### NAS / 路由器

如果你用的是这些设备，也可以直接接入：

- 群晖 NAS
- 威联通 NAS
- OpenWrt 路由器
- 梅林固件路由器

### 第一次连接

当你在第一台设备上安装并登录之后，Tailscale 会自动给这台设备分配一个 `100.x.x.x` 的内网地址。

接着你在第二台设备上也安装并登录同一个账号，这两台机器就已经在一个 tailnet 里了。

你可以直接测试：

- `ping` 对方的 Tailscale IP
- Windows 文件共享访问 `\\100.x.x.x`
- Mac 访问 `smb://100.x.x.x`

到这里，最基础的私人网络其实已经搭起来了。

## 第二步：实际应用场景

### 场景一：远程桌面连接

Tailscale 配好之后，你就可以直接用系统自带的远程桌面功能连回家里的电脑。

比如在 Windows 上：

```text
Win + R → mstsc
```

然后输入目标设备的 Tailscale IP 就行。

不需要开端口，不需要 DDNS，也不用担心把远程桌面直接暴露到公网。

### 场景二：访问家里的 NAS 或文件服务器

如果家里有 NAS，安装 Tailscale 之后，在外面也能像在家里一样访问文件。

对于经常在外、但又需要调资料的人来说，这个体验真的很舒服。

### 场景三：开发和测试环境

如果你平时会折腾服务器、树莓派、小服务、开发环境，那 Tailscale 更方便：

- 在公司访问家里的测试环境
- 远程连树莓派
- 远程看日志、改配置
- 临时接入某台 Linux 机器做排查

### 场景四：手机和平板无缝接入

这是很多人低估的一个点。

在手机上装好 Tailscale 后，你可以：

- 访问家里的文件
- 远程控制电脑
- 打开 NAS 上的内容
- 检查 homelab 服务状态

对于在外面临时救火特别有用。

## 第三步：理解 DERP 中继服务器

虽然 Tailscale 会尽量让设备之间直接连接（P2P），但在现实里并不总是能成功。

一些常见原因包括：

- NAT 类型复杂
- 公司网络限制严格
- 防火墙策略较多
- 某些运营商网络环境不利于直连

这时候就需要 **DERP（Designated Encrypted Relay for Packets）** 中继服务器来转发流量。

Tailscale 官方在全球有很多 DERP 节点，但在国内访问这些节点时，有时体验并不理想，可能会出现：

- 延迟高
- 速度慢
- 偶尔不稳定

所以如果你主要在国内网络环境下使用，配置更合适的 DERP 节点会对体验有帮助。

## 第四步：使用 FOFA DERP Scanner 找到更合适的中继节点

这里要感谢 GitHub 上的项目：

- **fofa-derp-scanner**
- 项目地址：https://github.com/wlzh/fofa-derp-scanner

它的作用是：

1. 从 FOFA 上找公开可用的 DERP 节点
2. 进行可用性测试
3. 帮你筛出更合适的节点

### 准备工作

你需要准备：

- 一个 FOFA 账号：https://fofa.info
- 一台装了 Python 3 的电脑
- 一台可以运行 Docker 的电脑

### 第一步：在 FOFA 上搜索 DERP 节点

登录 FOFA 后，在搜索框输入：

```text
body="Tailscale" && body="DERP server" && country="CN"
```

这个搜索条件会找出中国境内公开可见的 Tailscale DERP 节点。

然后点击导出，选择 **JSON** 格式，保存成：

```text
fofa_assets.json
```

### 第二步：克隆工具并转换格式

在终端执行：

```bash
git clone https://github.com/wlzh/fofa-derp-scanner.git
cd fofa-derp-scanner
python3 scripts/convert_assets.py --input /path/to/fofa_assets.json --output config/derp.json --start 900
```

这个脚本会把 FOFA 导出的结果转换成测试工具需要的格式，并给这些节点分配一个从 `900` 开始的 ID。

### 第三步：测试节点可用性

接下来用 Docker 跑测试：

```bash
docker-compose up -d
```

这个命令会启动测试容器，并自动测试这些节点的连通性。

测试通常需要几分钟，完成后在浏览器打开：

```text
http://localhost:8030/
```

然后进入 **success** 页面，这里会列出测试成功的节点。

建议用浏览器插件（比如 **SingleFile**）把这个页面保存为：

```text
success_report.html
```

### 第四步：提取成功节点

运行提取脚本：

```bash
python3 scripts/extract_success.py \
  --html /path/to/success_report.html \
  --json config/derp.json \
  --output config/derp-success-prober.json \
  --start 900 \
  --end 999
```

这个命令会从测试报告中提取成功节点，并生成最终配置文件。

### 第五步：应用到 Tailscale

打开 Tailscale ACL 配置页面：

- https://login.tailscale.com/admin/acls/file

然后在配置文件中加入或修改 `derpMap`：

```json
{
  "derpMap": {
    "OmitDefaultRegions": false,
    "Regions": {
      // 把 derp-success-prober.json 里的 Regions 内容复制到这里
    }
  }
}
```

这里有个小建议：

- 如果你想只使用自定义 DERP，可以把 `OmitDefaultRegions` 设为 `true`
- 但更稳妥的方式通常是先保持 `false`

这样你的自定义节点和官方节点都能同时存在，官方节点可以作为备份。

保存之后，Tailscale 设备通常会在几秒内自动刷新配置。

## 第五步：验证和优化

### 检查连接状态

在任意一台设备上执行：

```bash
tailscale status
```

你会看到：

- 所有已连接设备
- 它们之间是 **direct** 还是 **relay**

如果是 Windows，也可以用 PowerShell 看当前连接情况，例如：

```powershell
(Get-NetTCPConnection -OwningProcess (Get-Process tailscaled | Select-Object -Last 1).Id -State Established).RemoteAddress
```

### 优化节点列表

如果你发现某个 DERP 节点连接不稳定，或者速度明显偏慢，可以回到 Tailscale ACL 配置页面，找到对应 IP 的节点配置，把它删掉再保存。

Tailscale 会自动切换到其他可用节点。

另外也建议定期重新运行 **FOFA DERP Scanner**，更新节点列表，因为公开节点是会变化的。

## 进阶技巧

### 1）设置 Exit Node（出口节点）

如果你想让手机或笔记本的所有流量都通过家里的电脑转发，比如：

- 访问家里的局域网设备
- 使用家里的网络环境
- 在外获得一个稳定的家庭网络出口

可以在家里的电脑上运行：

```bash
sudo tailscale up --advertise-exit-node
```

然后去管理后台启用这台设备的 **Exit Node** 功能。

之后在手机或其他设备上打开 Tailscale，选择 **Use Exit Node**，把流量通过这台机器转发即可。

### 2）子网路由（Subnet Router）

如果你想让 Tailscale 网络中的设备访问家里的其他局域网设备，比如：

- 打印机
- 智能家居设备
- 没安装 Tailscale 的 NAS
- 其他局域网机器

假设你的局域网是：

```text
192.168.1.0/24
```

那么在已接入 Tailscale 的设备上运行：

```bash
sudo tailscale up --advertise-routes=192.168.1.0/24
```

然后在管理后台启用这条路由。这样其他 Tailscale 设备就能访问整个内网。

### 3）MagicDNS

Tailscale 的 **MagicDNS** 也特别实用。

启用后你不用再记住设备 IP，直接用设备名就可以访问。

比如：

```bash
ping my-desktop
```

这对设备比较多的人来说，非常省心。

## 常见问题解答

### Q：Tailscale 免费版有什么限制？

A：免费版支持最多 100 台设备、1 个用户。对个人用户来说通常够用。如果你需要多人协作或者更复杂的组织能力，可以考虑付费版。

### Q：使用公开 DERP 节点安全吗？

A：相对安全。因为 Tailscale 使用端到端加密，中继服务器只能转发加密流量，看不到明文内容。但如果你处理的是特别敏感的数据，最好还是自建私有 DERP。

### Q：为什么有时候连接很慢？

A：通常是当前 DERP 节点质量一般。可以按上面的方法检查当前连接的节点，删除表现差的节点配置，让 Tailscale 切换到更合适的节点。

### Q：可以在公司网络里使用吗？

A：大多数情况下可以。Tailscale 本身就是为复杂网络环境设计的，在很多公司网络、防火墙环境里都能正常工作。不过还是那句话：请遵守公司的网络使用政策。

### Q：手机上使用会不会很耗电？

A：通常不会太夸张。Tailscale 在移动端做了不少优化，待机时功耗不高，实际传输数据时才会有更明显的电量消耗。

## 总结

通过 **Tailscale + 国内 DERP 中继** 的组合，你可以免费搭建一个属于自己的、安全的、跨平台的虚拟局域网。

它适合这些事情：

- 远程办公
- 访问家里的设备
- 远程桌面
- 访问 NAS
- 连接 Homelab
- 在多个设备之间无缝切换

看起来步骤有点多，但其实大多数只需要配置一次。后面用起来会非常省心。

如果你还在为远程访问设备而烦恼，这套方案确实值得试试。

## 相关链接

- Tailscale 官网：https://tailscale.com/
- Tailscale 下载页面：https://tailscale.com/download
- Tailscale 管理后台：https://login.tailscale.com/admin
- FOFA 搜索引擎：https://fofa.info/
- FOFA DERP Scanner 项目：https://github.com/wlzh/fofa-derp-scanner
- Tailscale DERP Prober 项目：https://github.com/helloworlde/tailscale-derpprober
- SingleFile 浏览器插件：https://chrome.google.com/webstore/detail/singlefile/mpiodijhokgodhhofbcjdecpffjipkle

## 出处

本文根据 X 平台原文整理改写，原始链接如下：

- https://x.com/gxjdian/status/2031248060035055823?s=46
