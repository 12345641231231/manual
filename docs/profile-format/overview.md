---
sidebar_position: 0
---

#概述

本页面提供了冲浪板配置文件格式的全面概述。冲浪板的配置系统旨在实现灵活性和强大功能，使用户能够定义复杂的代理规则和网络行为。

:::tip
冲浪板关注[Surge](https://nssurge.com/)的个人资料格式。

可查看Surge的配置文档[此处](https://manual.nssurge.com/).
:::

以下示例展示了完整的配置文件结构，涵盖主要部分：`[常规]`, `[主机]`, `[代理]`, `[代理组]`, `[规则]`,以及`[面板]`.

这是
#!MANAGED-CONFIG http://test.com/surfboard.conf interval=60 strict=true # 带自动更新间隔的远程配置订阅，间隔以分钟为单位


# DNS服务器配置。'system' 使用设备的默认DNS。
dns-server = system, 8.8.8.8, 8.8.4.4, 9.9.9.9:9953

# 用于加密DNS查询的DNS over HTTPS (DoH)服务器。
doh-server = https://9.9.9.9/dns-query, https://1.1.1.1/dns-query

绕过代理的域名或IP地址范围列表。


用于测试代理连接延迟的URL。
代理测试网址 = http://www.gstatic.com/generate_204


互联网测试网址 = http://www.gstatic.cn/generate_204

# 连接测试的超时时间，单位为秒。
test-timeout = 5

#应始终解析为真实IP的域名，绕过虚假IP机制。
always-real-ip = *.srv.nintendo.net, *.stun.playstation.net, xbox.*.microsoft.com, *.xboxlive.com

#内部HTTP代理服务器地址和端口。
http-listen = 0.0.0.0:1234

#内部SOCKS5代理服务器地址和端口。
socks5-listen = 127.0.0.1:1235

#当代理不支持UDP中继时的行为（DIRECT或REJECT）。
udp-policy-not-supported-behaviour = DIRECT

[主机]
#针对特定域名的静态IP映射。
abc.com = 1.2.3.4

#通配符域名映射。
*.dev = 6.7.8.9

#DNS别名（CNAME）映射。
foo.com = bar.com

#针对特定域名的自定义DNS服务器。
bar.com = server:8.8.8.8

]
#内置策略。
开启 = 直接
关闭 = 拒绝

#HTTP代理配置。
ProxyHTTP = http, 1.2.3.4, 443, username, password

#带有TLS设置的HTTPS代理。
ProxyHTTPS = https, 1.2.3.4, 443, username, password, skip-cert-verify=true, sni=www.google.com

#SOCKS5代理配置。
ProxySOCKS5 = socks5, 1.2.3.4, 443, username, password, udp-relay=false

#通过TLS的SOCKS5，以增强安全性。
ProxySOCKS5TLS = socks5-tls, 1.2.3.4, 443, 用户名, 密码, skip-cert-verify=true, sni=www.google.com

#带可选混淆功能的Shadowsocks代理。
ProxySS = ss, 1.2.3.4, 8000, encrypt-method=chacha20-ietf-poly1305, password=abcd1234, udp-relay=false, obfs=http, obfs-host=www.google.com, obfs-uri=/

V2Ray协议的VMess代理。
ProxyVMess = vmess, 1.2.3.4, 8000, username=0233d11c-15a4-47d3-ade3-48ffca0ce119, udp-relay=false, ws=true, tls=true, ws-path=/v2, ws-headers=X-Header-1:value|X-Header-2:value, skip-cert-verify=true, sni=www.google.com, vmess-aead=true

#木马代理配置。
代理木马 = 木马, 192.168.20.6, 443, 密码=密码1, udp中继=false, 跳过证书验证=true, sni=www.google.com

#WireGuard VPN 集成。
ProxyWireguard = wireguard, section-name = HomeServer

[WireGuard 家庭服务器]
#特定的WireGuard接口和对等体配置。
私钥 = sDEZLACT3zgNCS0CyClgcBC2eYROqYrwLT4wdtAJj3s=
本机IP = 10.0.2.2
DNS服务器 = 8.8.8.8
MTU = 1280
对等方 = (公钥 = fWO8XS9/nwUQcqnkfBpKeqIqbzclQ6EKP20Pgvzwclg=, 允许的IP = 0.0.0.0/0, 端点 = 192.168.20.6:51820, 心跳 = 30)

[代理组]
#手动选择组。
选择组 = 选择，代理HTTP，代理HTTPS，直接，拒绝

#基于延迟的自动选择。
自动测试组 = url-测试，代理SOCKS5，代理SOCKS5TLS，url=http://www.gstatic.com/generate_204，间隔=600，容差=100，超时=5，隐藏=true

#使用外部策略列表的组。
ExternalGroup = select, policy-path=https://example.com/nodes.txt, policy-regex-filter=HK-.*

#包含所有已定义代理的组。
AllProxies = 选择，include-all-proxies = true

#用于分发流量的负载均衡组。
LoadBalanceGroup = 负载均衡，ProxyHTTP，ProxyHTTPS

#回退组：如果当前代理失败，则切换到下一个代理。
回退组 = 回退，代理SOCKS5，代理SOCKS5TLS，url=http://www.gstatic.com/generate_204，间隔=600，超时=5

[Rule]
# Direct domain matching.
DOMAIN,www.apple.com,ProxyHTTP

#域名后缀匹配（涵盖所有子域名）。
DOMAIN-SUFFIX,apple.com,代理,强制使用远程DNS

#域名关键词匹配。
DOMAIN-KEYWORD,google,Proxy,增强模式

#使用CIDR表示法进行IP地址范围匹配。
IP-CIDR，192.168.0.0/16，DIRECT

#基于地理位置的匹配（例如，ISO国家代码）。
GEOIP，US，REJECT

#按进程/软件包名称进行的应用程序匹配。
进程名,com.android.vending,代理

#根据外部规则集对流量进行规则匹配。
规则集，https://example.com/ruleset.conf，ProxyVMess

#基于SSID/BSSID的特定网络规则。
子网,类型:WIFI,直接

# Protocol-specific rules.
PROTOCOL,QUIC,REJECT

# Default rule for traffic matching no other rules.
FINAL,ProxyTrojan

[Panel]
# Custom UI panel for displaying information or status.
PanelA = title="Status Panel", content="System Online\nAll services operational", style=info
```

You can read on for detailed definitions of different sections.
```

You can read on for detailed definitions of different sections.
