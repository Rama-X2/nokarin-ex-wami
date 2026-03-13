---

# nokarin-ex-wami

![OpenWrt](https://img.shields.io/badge/OpenWrt-compatible-green)
![OpenClash](https://img.shields.io/badge/OpenClash-supported-blue)
![Clash Meta](https://img.shields.io/badge/Core-Clash%20Meta-blue)
![Last Commit](https://img.shields.io/github/last-commit/Rama-X2/nokarin-ex-wami)
![Stars](https://img.shields.io/github/stars/Rama-X2/nokarin-ex-wami?style=social)

A curated collection of **OpenClash configuration files for OpenWrt routers**.

This repository provides ready-to-use configurations designed for **stable routing, DNS handling, and flexible proxy setups** using OpenClash.

This repository also contains configuration files that I personally use for routing traffic through OpenClash with stable DNS settings, optimized rules, and flexible routing behavior.

The goal of this project is to simplify the process of deploying OpenClash configurations without having to build everything from scratch.

---

# Overview

OpenClash is a powerful proxy client for OpenWrt that supports advanced traffic routing and multiple proxy protocols.

This repository contains configurations intended for:

* everyday network usage
* tunneling setups
* rule-based traffic routing
* DNS management

These configurations are designed to be **easy to modify and extend** depending on user needs.

---

# Features

* Pre-configured **OpenClash YAML files**
* Ready-to-use OpenClash configurations
* Optimized **rule-based routing**
* Stable **DNS configuration**
* Compatible with **Clash Meta Core**
* Compatible with multiple Clash protocols
* Multiple proxy protocol support
* Suitable for **tunneling environments**
* Designed for daily network usage
* Easy integration with existing OpenClash setups

---

# Requirements

Before using these configurations, ensure your router has:

* **OpenWrt installed**
* **luci-app-openclash**
* **Clash Core or Clash Meta Core**
* active internet connection

Install OpenClash if it is not available:

```
opkg update
opkg install luci-app-openclash
```

---

# Installation

### Clone repository

```
git clone https://github.com/Rama-X2/nokarin-ex-wami.git
```

or download it directly from GitHub.

---

### Upload configuration

Upload the configuration file to the OpenClash config directory:

```
/etc/openclash/config/
```

File transfer methods:

* SCP
* WinSCP
* SSH
* LuCI file upload
* LuCI file transfer

---

### Load configuration

Open the OpenClash interface:

```
LuCI → Services → OpenClash → Config
```

Select the uploaded configuration and start OpenClash.

---

# Recommended Settings

For optimal performance and stability:

| Setting      | Recommended Value   |
| ------------ | ------------------- |
| Mode         | TUN Mode            |
| Core         | Clash Meta          |
| DNS Mode     | Fake-IP             |
| Log Level    | Silent              |
| IPv6         | Disabled (optional) |
| Auto Restart | Enabled             |
| Dashboard    | Enabled             |

---

# Network Architecture

Example traffic flow when using OpenClash in **TUN mode**:

```
            Internet
               │
               │
         Proxy Server
               │
               │
        ┌─────────────┐
        │   OpenClash  │
        │   (Clash)    │
        └──────┬───────┘
               │
        Rule-based Routing
               │
     ┌─────────┼─────────┐
     │         │         │
   Proxy     Direct    Reject
     │         │         │
Blocked ISP  Local Net  Ads
```

Traffic is routed according to the rules defined in the configuration.

---

# Repository Structure

```
nokarin-ex-wami
│
├── config/
│   ├── main-config.yaml
│   └── example-config.yaml
│
├── rules/
│   ├── custom-rules.yaml
│   └── bypass-rules.yaml
│
└── README.md
```

---

# Example Configuration

Example snippet from a Clash configuration:

```yaml
mode: rule

dns:
  enable: true
  enhanced-mode: fake-ip
  listen: 0.0.0.0:7874

proxies:
  - name: example-vmess
    type: vmess
    server: example.com
    port: 443
```

---

# Proxy Configuration Templates

## VMess

```yaml
proxies:
  - name: vmess-example
    type: vmess
    server: example.com
    port: 443
    uuid: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    alterId: 0
    cipher: auto
    tls: true
```

---

## VLESS

```yaml
proxies:
  - name: vless-example
    type: vless
    server: example.com
    port: 443
    uuid: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    tls: true
    network: ws
```

---

## Trojan

```yaml
proxies:
  - name: trojan-example
    type: trojan
    server: example.com
    port: 443
    password: yourpassword
    tls: true
```

---

# Fake-IP Filter Example

When using Fake-IP mode, some services should bypass fake DNS responses.

```
dns:
  enable: true
  enhanced-mode: fake-ip

  fake-ip-filter:
    - "*.lan"
    - "*.local"
    - "*.bank"

    - "*.gojek.com"
    - "*.grab.com"
    - "*.dana.id"
    - "*.ovo.id"
    - "*.linkaja.id"
    - "*.seabank.co.id"
    - "*.shopee.co.id"
    - "*.tokopedia.com"
```

---

# Rule Provider Example

```
rule-providers:

  reject:
    type: http
    behavior: domain
    url: https://raw.githubusercontent.com/Loyalsoldier/clash-rules/release/reject.txt
    path: ./ruleset/reject.yaml
    interval: 86400

  direct:
    type: http
    behavior: domain
    url: https://raw.githubusercontent.com/Loyalsoldier/clash-rules/release/direct.txt
    path: ./ruleset/direct.yaml
    interval: 86400
```

---

# Example Rule Usage

```
rules:
  - RULE-SET,reject,REJECT
  - RULE-SET,direct,DIRECT
  - MATCH,Proxy
```

This configuration will:

1. block ads
2. bypass local traffic
3. route other traffic through proxy

---

# Wildcard / SNI Tunneling Example

Example configuration for tunneling environments:

```
proxies:
  - name: vmess-tunnel
    type: vmess
    server: bug.example.com
    port: 443
    uuid: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    tls: true
    servername: target.server.com
```

---

# Performance Notes

To maintain stable performance:

* avoid excessively large rule sets on low-RAM routers
* prefer rule providers over inline rules
* use Clash Meta core
* keep configurations clean and minimal

---

# Troubleshooting

### OpenClash cannot start

Possible causes:

* YAML syntax error
* incompatible core version
* insufficient router memory

---

### Some apps cannot connect

Possible fixes:

* add domains to `fake-ip-filter`
* create `DIRECT` rules
* disable IPv6 if not needed

---

### High latency

Possible solutions:

* change proxy nodes
* reduce rule complexity
* verify DNS configuration

---

# Notes

* Some rules may need adjustment depending on your ISP.
* If you are using **SNI bug or wildcard tunneling**, ensure your node configuration is correct.
* Not all nodes perform the same across different networks.

---

# Contributing

Contributions are welcome.

Feel free to open:

* **Pull Requests**
* **Issues**

to improve rules, configurations, or stability.

---

# Disclaimer

These configurations are provided for **educational and experimental purposes**.

Use them at your own risk. The author is not responsible for any misuse, network disruption, or policy violations resulting from the use of these configurations.

---

# Author

Maintained by

**Rama-X2**

GitHub
[https://github.com/Rama-X2](https://github.com/Rama-X2)

---
