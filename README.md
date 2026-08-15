# 🖥️ 2026年软路由与OpenWrt部署完全指南

> 本仓库专注于将机场订阅配置到软路由和OpenWrt系统，实现全屋设备自动代理。无需在每台设备单独配置，所有连接路由器的设备都能享受科学上网体验。

## 📋 目录导航

- [为什么选择路由器部署](#为什么选择路由器部署)
- [软路由硬件选择指南](#软路由硬件选择指南)
- [OpenWrt 安装与配置](#openwrt-安装与配置)
- [旁路路由部署方案](#旁路路由部署方案)
- [节点配置与优化](#节点配置与优化)
- [全屋设备分流策略](#全屋设备分流策略)
- [性能监控与故障排查](#性能监控与故障排查)
- [高级功能配置](#高级功能配置)

---

## 🌟 为什么选择路由器部署

### 路由器部署的核心优势

**1. 全设备覆盖**

| 设备类型 | 单独配置 | 路由器部署 |
|---------|---------|-----------|
| 电脑 (Windows/Mac) | ✅ 需安装客户端 | ✅ 自动代理 |
| 手机 (iOS/Android) | ✅ 需安装客户端 | ✅ 自动代理 |
| 平板 | ✅ 需安装客户端 | ✅ 自动代理 |
| 智能电视 | ❌ 配置复杂 | ✅ 自动代理 |
| 游戏主机 | ❌ 配置复杂 | ✅ 自动代理 |
| IoT 设备 | ❌ 无法配置 | ✅ 自动代理 |
| 访客设备 | ❌ 无法配置 | ✅ 按规则分流 |

**2. 一劳永逸**

- 路由器一次配置，所有设备永久使用
- 新设备连接即可使用，无需安装任何软件
- 出门在外回家自动连接，无需手动操作
- 家庭成员无需任何技术知识

**3. 性能优势**

- 专用处理器处理加密流量
- 不占用设备算力和电池
- 支持更多设备同时在线
- 可以跑满带宽上限

**4. 特殊场景支持**

- Apple TV、Chromecast 等电视设备
- PlayStation、Xbox、Switch 游戏主机
- 只能指定DNS的IoT设备
- 无法安装代理应用的老旧设备

### 部署方式对比

| 部署方式 | 复杂度 | 成本 | 适用场景 | 推荐指数 |
|---------|--------|------|---------|---------|
| 原生 OpenWrt | 高 | 低 | 技术用户 | ⭐⭐⭐⭐ |
| 旁路路由 | 中 | 中 | 普通用户 | ⭐⭐⭐⭐⭐ |
| 软路由 | 中 | 高 | 高性能需求 | ⭐⭐⭐⭐⭐ |
| Docker 旁路 | 低 | 低 | 进阶用户 | ⭐⭐⭐⭐ |

---

## 🔧 软路由硬件选择指南

### 入门级推荐

#### 友善之臂 NanoPi R2S/R4S

**NanoPi R2S 规格**

| 项目 | 规格 |
|------|------|
| CPU | RK3328 (四核 1.5GHz) |
| 内存 | 1GB DDR4 |
| 网口 | 1× 千兆 + 1× 千兆 |
| USB | 1× USB 3.0 |
| 功耗 | 约 5W |
| 价格 | 约 200-300 元 |

**适用场景**
- 带宽 500Mbps 以下
- 10 台以下设备
- 轻度科学上网需求
- 家庭入门级使用

**NanoPi R4S 规格**

| 项目 | 规格 |
|------|------|
| CPU | RK3399 (六核 1.8GHz) |
| 内存 | 4GB DDR4 |
| 网口 | 1× 2.5GbE + 1× 1GbE |
| USB | 2× USB 3.0 |
| 功耗 | 约 8W |
| 价格 | 约 500-700 元 |

**适用场景**
- 带宽 1Gbps 以下
- 30 台以下设备
- 中度科学上网需求
- 4K 视频需求

### 中高端推荐

#### x86 软路由

**入门 x86 配置**

| 项目 | 推荐配置 |
|------|---------|
| CPU | Intel Celeron J4125/J5105 |
| 内存 | 4-8GB DDR4 |
| 网口 | 至少 2× 千兆 |
| 存储 | 64GB+ SSD |
| 功耗 | 15-30W |
| 价格 | 500-1500 元 |

**进阶 x86 配置**

| 项目 | 推荐配置 |
|------|---------|
| CPU | Intel Core i3/i5 低功耗版 |
| 内存 | 8-16GB DDR4 |
| 网口 | 2× 2.5GbE 或更高 |
| 存储 | 128GB+ NVMe SSD |
| 功耗 | 30-60W |
| 价格 | 1500-3000 元 |

**适用场景**
- 带宽 1Gbps 以上
- 50+ 设备同时在线
- 跑满千兆带宽
- 企业级使用需求

### 路由器推荐（直接刷 OpenWrt）

| 型号 | CPU | 内存 | Flash | 推荐场景 |
|------|-----|------|-------|---------|
| 红米 AX6 | IPQ8071A | 512MB | 128MB | 入门推荐 |
| 小米 AX3600 | IPQ8071A | 512MB | 256MB | 性价比首选 |
| 红米 AX6000 | MT7986A | 512MB | 128MB | WiFi6 推荐 |
| TP-LINK XDR6080 | IPQ8072A | 1GB | 128MB | 高端选择 |

---

## 🛠️ OpenWrt 安装与配置

### OpenWrt 固件下载

#### 官方固件 vs 第三方固件

| 固件类型 | 来源 | 优点 | 缺点 |
|---------|------|------|------|
| 官方 OpenWrt | openwrt.org | 原生、稳定、更新及时 | 功能较少 |
| ImmortalWrt | immortalwrt.org | 国内优化、插件丰富 | 社区维护 |
| Lean 大源码 | GitHub | 功能最全、性能优化 | 更新较慢 |
| Lienol 固件 | GitHub | 插件丰富 | 稳定性一般 |

#### 下载地址

**NanoPi R2S/R4S**
- [ImmortalWrt 下载页](https://固件.immortalwrt.org)
- [友善之臂官方](https://www.friendlyarm.com)

**x86 软路由**
- [OpenWrt x86 固件](https://downloads.openwrt.org)
- [ImmortalWrt x86](https://固件.immortalwrt.org)

**小米/红米路由器**
- [Xiaomi Router OpenWrt](https://github.com/mv装裹王/OpenWrt)
- [Padavan 固件](https://opt.cn2qq.com/padavan)

### OpenWrt 基础配置

#### 首次登录配置

**连接管理**

1. 使用网线连接电脑到路由器 LAN 口
2. 浏览器访问 `192.168.1.1`
3. 默认账号：`root`，无密码
4. 首次登录后设置 root 密码

**网络配置**

```bash
# SSH 登录路由器
ssh root@192.168.1.1

# 查看网络接口
cat /etc/config/network

# 配置 LAN 口
vi /etc/config/network
```

```lua
config interface 'lan'
    option device 'br-lan'
    option proto 'static'
    option ipaddr '192.168.1.1'
    option netmask '255.255.255.0'
    option ip6assign '60'

config interface 'wan'
    option device 'eth0'
    option proto 'dhcp'

config interface 'wan6'
    option device 'eth0'
    option proto 'dhcpv6'
```

#### 安装必要插件

**通过 OPKG 安装**

```bash
# 更新软件包列表
opkg update

# 安装基础工具
opkg install curl wget luci-app-openvpn

# 安装 Clash 相关
opkg install luci-app-clash
opkg install luci-i18n-base-zh-cn

# 安装其他常用插件
opkg install luci-app-unblockmusic  # 解锁网易云灰色歌曲
opkg install luci-app-smartdns      # DNS 优化
opkg install luci-app-adguardhome   # 广告拦截
```

**通过 LuCI Web 界面安装**

1. 访问 LuCI：`http://192.168.1.1/cgi-bin/luci`
2. 进入「系统」>「软件包」
3. 点击「更新列表」
4. 搜索并安装所需插件

### OpenClash 安装与配置

#### OpenClash 简介

OpenClash 是 OpenWrt 上的 Clash 客户端，具有以下特点：
- 完整的 Clash 核心支持
- 友好的 Web 配置界面
- 支持订阅自动更新
- 多种代理协议支持
- 灵活的规则配置

#### 安装 OpenClash

**方法一：LuCI 在线安装**

1. 进入 LuCI 界面
2. 「系统」>「软件包」
3. 搜索 `luci-app-openclash`
4. 点击「安装」

**方法二：手动安装**

```bash
# 下载 OpenClash
cd /tmp
wget https://github.com/vernesong/OpenClash/releases/download/v0.45.140/OpenClash-stable.tar.gz

# 解压安装
tar -zxvf OpenClash-stable.tar.gz -C /
```

**方法三：ShellClash 一键安装**

```bash
# SSH 登录后执行
sh -c "$(curl -fsSL https://github.com/jamesstatd/james/raw/main/shellclash/install.sh)"
```

#### OpenClash 配置

**基础配置步骤**

1. **访问 OpenClash 界面**
   - LuCI > 服务 > OpenClash
   - 首次进入会提示安装 Clash 核心

2. **配置订阅**
   ```
   界面路径：配置管理 > 订阅设置
   
   操作：
   1. 点击「添加」
   2. 粘贴订阅链接（从 ClashVIP 获取）
   3. 设置订阅名称
   4. 点击「保存并应用」
   ```

3. **配置节点**
   ```
   界面路径：配置管理 > 节点管理
   
   操作：
   1. 选择订阅源
   2. 点击「同步」
   3. 选择要使用的节点
   ```

4. **配置规则**
   ```
   界面路径：配置管理 > 规则设置
   
   推荐规则：
   - 游戏模式：Game Rules
   - 视频模式：Video Rules  
   - 回国模式：China Rules
   - 完整模式：Rule Provider
   ```

#### OpenClash 配置文件示例

```yaml
# OpenClash 配置文件模板
port: 7890
socks-port: 7891
redir-port: 7892
allow-lan: true
bind-address: "*"
mode: rule
log-level: info
external-controller: 0.0.0.0:9090
secret: "your-secret"

dns:
  enable: true
  listen: 0.0.0.0:53
  ipv6: false
  enhanced-mode: fake-ip
  fake-ip-range: 198.18.0.1/16
  nameserver:
    - 223.5.5.5
    - 119.29.29.29
  fallback:
    - 8.8.8.8
    - 1.1.1.1

# 代理节点（由订阅自动生成）
proxies: []

# 代理组
proxy-groups:
  - name: Proxy
    type: url-test
    proxies:
      - 香港节点
      - 日本节点
    url: "http://www.gstatic.com/generate_204"
    interval: 300

# 分流规则
rules:
  - DOMAIN-SUFFIX,clashvip.net,DIRECT
  - DOMAIN-SUFFIX,nav.clashvip.net,DIRECT
  - DOMAIN-SUFFIX,clashhub.net,DIRECT
  - DOMAIN-SUFFIX,bbs.clashhub.net,DIRECT
  - DOMAIN-SUFFIX,vpsvip.net,DIRECT
  - DOMAIN-SUFFIX,clash-for-windows.net,DIRECT
  - DOMAIN-SUFFIX,apple.com,DIRECT
  - DOMAIN-SUFFIX,baidu.com,DIRECT
  - DOMAIN-SUFFIX,taobao.com,DIRECT
  - DOMAIN-SUFFIX,alipay.com,DIRECT
  - DOMAIN-SUFFIX,tencent.com,DIRECT
  - GEOIP,CN,DIRECT
  - MATCH,Proxy
```

---

## 🔀 旁路路由部署方案

### 旁路路由原理

旁路路由（旁网关）是一种特殊的部署方式：
- 主路由保持原有配置不变
- 旁路设备负责透明代理
- 通过网关设置引导流量
- 不影响原有网络结构

### 适用场景

| 场景 | 推荐方案 | 说明 |
|------|---------|------|
| 不想改动主路由 | 旁路路由 | 仅旁路设备透明代理 |
| 多设备多平台 | 主路由部署 | 所有设备自动代理 |
| 访客隔离需求 | 主路由+旁路 | 主路由访客网，旁路给固定设备 |
| 游戏主机专用 | 旁路路由 | 只代理游戏设备 |

### 旁路路由配置步骤

#### 方案一：OpenClash 旁路模式

**1. 基础设置**

```
OpenClash > 全局设置 > 运营模式
选择：Fake-IP (TUN) 模式

然后：
1. 勾选「绕过中国大陆」
2. 设置 LAN 桥接
3. 保存并应用
```

**2. 客户端网关设置**

在需要代理的设备上设置网关：
- IP 地址：旁路路由 IP（如 192.168.1.254）
- DNS：旁路路由 IP 或 223.5.5.5

#### 方案二：PassWall 旁路模式

PassWall 也支持旁路模式：

```
PassWall > 基本设置 > 旁路路由
1. 启用旁路模式
2. 设置旁路 IP 地址
3. 选择节点
4. 保存并应用
```

### 软路由完整配置示例

#### 网络拓扑

```
光猫 (192.168.1.1)
    |
    ↓ (桥接模式)
软路由 OpenWrt (192.168.1.2)
    |
    ├── LAN 1 → 交换机 → 电脑、手机、平板
    |
    ├── LAN 2 → IPTV 专用
    |
    └── Wi-Fi → 无线设备
```

#### OpenWrt 网络配置

```bash
# /etc/config/network

config interface 'loopback'
    option device 'lo'
    option proto 'static'
    option ipaddr '127.0.0.1'
    option netmask '255.0.0.0'

config interface 'lan'
    option device 'br-lan'
    option proto 'static'
    option ipaddr '192.168.1.2'
    option netmask '255.255.255.0'
    option ip6assign '60'
    list dns '223.5.5.5'

config interface 'wan'
    option device 'eth0'
    option proto 'dhcp'
    option metric '100'

config interface 'wan6'
    option device 'eth0'
    option proto 'dhcpv6'

config device
    option name 'br-lan'
    option type 'bridge'
    list ports 'lan1'
    list ports 'lan2'
    list ports 'lan3'
    list ports 'lan4'
```

#### DHCP 配置

```bash
# /etc/config/dhcp

config dnsmasq
    option domainneeded '1'
    option localise_queries '1'
    option rebind_protection '1'
    option rebind_localhost '1'
    option local '/lan/'
    option domain 'lan'
    option expandhosts '1'
    option authoritative '1'
    option readethers '1'
    option leasefile '/tmp/dhcp.leases'
    list server '127.0.0.1#5335'
    list server '223.5.5.5'
    list server '119.29.29.29'

config dhcp 'lan'
    option interface 'lan'
    option start '100'
    option limit '150'
    option leasetime '12h'

config dhcp 'wan'
    option interface 'wan'
    option ignore '1'
```

---

## 🔗 节点配置与优化

### 订阅管理

#### ClashVIP 订阅获取

1. 访问 [ClashVIP 官网](https://clashvip.net)
2. 注册并登录账号
3. 进入「订阅管理」
4. 复制 Clash 订阅链接
5. 粘贴到 OpenClash 配置中

#### 自动更新配置

```
OpenClash > 配置管理 > 订阅设置

推荐配置：
- 自动更新：✅ 启用
- 更新周期：每 6 小时
- 更新日期：每天
- 通知：✅ 启用
```

### 节点选择策略

#### 节点类型对比

| 节点类型 | 延迟 | 带宽 | 稳定性 | 适用场景 |
|---------|------|------|--------|---------|
| 香港 | 低 | 高 | 优 | 通用推荐 |
| 台湾 | 低 | 高 | 优 | 视频、游戏 |
| 日本 | 中 | 高 | 优 | 游戏加速 |
| 新加坡 | 中 | 中 | 优 | 东南亚 |
| 美国 | 高 | 高 | 良 | 特殊需求 |

#### 智能节点切换

```yaml
# 代理组配置
proxy-groups:
  - name: Proxy
    type: url-test
    proxies:
      - 香港-01
      - 香港-02
      - 台湾-01
      - 日本-01
      - 新加坡-01
    url: "http://www.gstatic.com/generate_204"
    interval: 300
    tolerance: 50

  - name: 游戏
    type: url-test
    proxies:
      - 日本-游戏专线
      - 香港-游戏专线
      - 台湾-游戏专线
    url: "http://www.gstatic.com/generate_204"
    interval: 180
    tolerance: 30

  - name: 视频
    type: fallback
    proxies:
      - 香港-01
      - 台湾-01
      - 日本-01
    url: "http://www.gstatic.com/generate_204"
    interval: 300
```

### 性能优化

#### DNS 优化

```bash
# 使用 SmartDNS 优化 DNS 解析
opkg install luci-app-smartdns

# SmartDNS 配置
config smartdns server
    option enabled '1'
    list server '223.5.5.5'
    list server '119.29.29.29'
    option port '5335'
    
config smartdns server
    option enabled '1'
    list server '8.8.8.8'
    list server '1.1.1.1'
    option type 'tcp'
    option port '53'
```

#### 带宽优化

```
OpenClash > 流量控制

推荐设置：
- 并发连接数：不限制
- 连接超时：60秒
- UDP 超时：300秒
- 本地 DNS：启用
- 域名缓存：启用
```

---

## 🌐 全屋设备分流策略

### 分流规则设计

#### 基础分流原则

| 流量类型 | 路由策略 | 规则示例 |
|---------|---------|---------|
| 国内网站 | 直连 (DIRECT) | GEOIP,CN |
| 国内应用 | 直连 (DIRECT) | DOMAIN-SUFFIX,baidu.com |
| Google 全家桶 | 代理 (Proxy) | DOMAIN-KEYWORD,google |
| 社交媒体 | 代理 (Proxy) | DOMAIN-SUFFIX,facebook.com |
| 视频流媒体 | 代理 (Proxy) | DOMAIN-SUFFIX,netflix.com |
| 游戏 | 游戏代理组 | 自定义规则 |

### 分流规则配置

#### OpenClash 规则设置

```
界面路径：OpenClash > 配置管理 > 规则设置

推荐配置策略：

1. 选择规则提供源
   - 推荐：lhie1/Dreamacro 规则
   - 或使用：ACL4SSR 规则

2. 自定义规则
   添加以下规则到规则列表顶部：
   
   - DOMAIN-SUFFIX,clashvip.net,DIRECT
   - DOMAIN-SUFFIX,nav.clashvip.net,DIRECT
   - DOMAIN-SUFFIX,clashhub.net,DIRECT
   - DOMAIN-SUFFIX,bbs.clashhub.net,DIRECT
   - DOMAIN-SUFFIX,vpsvip.net,DIRECT
   - DOMAIN-SUFFIX,clash-for-windows.net,DIRECT
```

#### 按设备分流

```bash
# 使用防火墙规则按 IP 分流

# 设置特定 IP 走代理
iptables -t nat -A PREROUTING -s 192.168.1.100 -j REDIRECT

# 设置特定 IP 直连
iptables -t nat -A PREROUTING -s 192.168.1.101 -j ACCEPT
```

### 特殊设备配置

#### Apple TV 配置

```bash
# Apple TV 专用规则
# 确保 Apple TV 使用代理访问所有内容

config rule
    option name 'Apple-TV'
    option src 'lan'
    option dest 'wan'
    option dest_ip '192.168.1.200'
    option target 'REDIRECT'
    option dest_port '80,443'
```

#### 游戏主机配置

```yaml
# 游戏主机分流规则
rules:
  # PlayStation
  - DOMAIN-SUFFIX,playstation.net,游戏
  - DOMAIN-SUFFIX,sony.com,游戏
  - DOMAIN-KEYWORD,ps,游戏
  
  # Xbox
  - DOMAIN-SUFFIX,xbox.com,游戏
  - DOMAIN-SUFFIX,xboxlive.com,游戏
  
  # Nintendo Switch
  - DOMAIN-SUFFIX,nintendo.com,游戏
  - DOMAIN-SUFFIX,nintendo.net,游戏
```

---

## 📊 性能监控与故障排查

### OpenClash 日志查看

#### 实时日志

```
LuCI > 服务 > OpenClash > 日志页面

查看内容：
- 启动日志
- 运行日志
- 错误日志
- 节点状态
```

#### SSH 查看日志

```bash
# 查看 Clash 日志
logread | grep clash

# 查看系统日志
logread | tail -100

# 查看内核日志
dmesg | tail -50
```

### 常见故障排查

#### 问题一：所有设备无法上网

**排查步骤：**

1. 检查 OpenClash 是否运行
   ```
   /etc/init.d/openclash status
   ```

2. 检查节点是否可用
   ```
   curl -I https://clashvip.net
   ```

3. 检查 DNS 设置
   ```
   cat /etc/resolv.conf
   ```

4. 重启服务
   ```
   /etc/init.d/openclash restart
   /etc/init.d/network restart
   ```

#### 问题二：部分设备无法上网

**排查步骤：**

1. 检查设备 IP 配置
   ```
   IP: 应为 192.168.1.x
   网关: 应为 OpenWrt IP (192.168.1.2)
   DNS: 应为 OpenWrt IP 或 223.5.5.5
   ```

2. 检查 DHCP 分配
   ```
   cat /tmp/dhcp.leases
   ```

3. 检查防火墙规则
   ```
   iptables -t nat -L -n
   ```

#### 问题三：速度很慢

**排查步骤：**

1. 测试节点延迟
   ```
   ping -c 5 hk.clashvip.net
   ```

2. 测试带宽
   ```
   curl -o /dev/null -s -w "%{speed_download}" http://speedtest.file
   ```

3. 更换节点测试

4. 检查是否跑满带宽

#### 问题四：TUN 模式不工作

**排查步骤：**

1. 检查 TUN 状态
   ```
   ip addr | grep tun
   ```

2. 检查权限
   ```
   ls -l /dev/net/tun
   ```

3. 尝试切换到系统代理模式

### 性能监控脚本

```bash
#!/bin/bash
# OpenWrt 性能监控脚本

echo "=== OpenClash 状态监控 ==="
echo "服务状态：$(/etc/init.d/openclash status | grep running)"
echo "运行时间：$(uptime | awk '{print $3,$4}')"

echo ""
echo "=== 网络流量 ==="
cat /proc/net/dev | grep eth0

echo ""
echo "=== 内存使用 ==="
free -m

echo ""
echo "=== Clash 进程 ==="
ps | grep clash | grep -v grep

echo ""
echo "=== 节点延迟 ==="
curl -s -o /dev/null -w "香港: %{time_total}s\n" --max-time 5 https://hk.clashvip.net
curl -s -o /dev/null -w "日本: %{time_total}s\n" --max-time 5 https://jp.clashvip.net
```

---

## 🚀 高级功能配置

### 广告拦截

#### AdGuard Home 配置

```bash
# 安装 AdGuard Home
opkg install luci-app-adguardhome

# 配置
# LuCI > 服务 > AdGuard Home
# 
# 启用广告拦截
# 上游 DNS: 223.5.5.5, 119.29.29.29
# Bootstrap DNS: 8.8.8.8
```

#### 自定义广告规则

```ini
# 广告拦截规则
||doubleclick.net^
||googlesyndication.com^
||googleadservices.com^
||ads.google.com^
||facebook.com/tr^
||analytics.google.com^
||adservice.google.com^
```

### 网易云音乐解锁

#### 安装解锁插件

```bash
# 安装依赖
opkg update
opkg install luci-app-unblockmusic
opkg install UnblockNeteaseMusic

# 配置
# LuCI > 服务 > 网易云音乐解锁
# 
# 启用服务
# 选择音乐源
# 保存并应用
```

### 科学上网回国

#### 回国模式配置

有些机场支持「回国节点」，用于海外用户访问国内内容：

```yaml
# 回国代理组
proxy-groups:
  - name: 回国
    type: select
    proxies:
      - DIRECT
      - 北京-回国
      - 上海-回国
      - 广州-回国

# 回国规则
rules:
  - DOMAIN-SUFFIX,bilibili.com,回国
  - DOMAIN-SUFFIX,qq.com,回国
  - DOMAIN-SUFFIX,tencent.com,回国
  - DOMAIN-SUFFIX,baidu.com,回国
  - DOMAIN-SUFFIX,taobao.com,回国
  - DOMAIN-SUFFIX,tmall.com,回国
  - DOMAIN-SUFFIX,jd.com,回国
```

---

## 📥 相关资源

| 资源 | 链接 |
|------|------|
| ClashVIP 官网 | https://clashvip.net |
| 机场导航 | https://nav.clashvip.net |
| Clash 教程 | https://clashhub.net |
| 用户社区 | https://bbs.clashhub.net |
| OpenClash 项目 | https://github.com/vernesong/OpenClash |
| OpenWrt 官方 | https://openwrt.org |
| ImmortalWrt | https://immortalwrt.org |

---

## ⚠️ 免责声明

1. 本仓库仅提供技术教程，不参与任何商业活动
2. 请遵守当地法律法规使用网络服务
3. 机场服务可能受地区政策影响
4. 购买前请仔细阅读服务商服务条款
5. 请勿用于任何违法用途

---

**最后更新：2026-08-15**

**更新内容：**
- 新增软路由硬件选择完整指南
- 添加 OpenWrt 安装与配置详解
- 增加 OpenClash 旁路路由部署方案
- 完善全屋设备分流策略
- 增加高级功能配置（广告拦截、回国模式等）
