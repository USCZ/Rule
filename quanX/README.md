# Quantumult X 自用规则集 · `quanX`

![Quantumult X](https://img.shields.io/badge/Quantumult%20X-rules-1E90FF)
![Last Commit](https://img.shields.io/github/last-commit/USCZ/Rule/main)
![Repo Size](https://img.shields.io/github/repo-size/USCZ/Rule)

> 面向中国大陆 / 香港用户的 **Quantumult X** 分流规则：港股/美股券商、Apple 推送（APNs）、Dola（豆包海外版）。
>
> 所有规则**已在文件内写好 `DIRECT` / `proxy` / `REJECT` 策略**，订阅后即生效，**请勿再设置 `force-policy`**（否则会覆盖文件内的混合策略）。

---

## 📑 目录

- [规则一览](#-规则一览)
- [1. HK_Finance.list — 港美股券商 + 香港金融](#1-hk_financelist--港美股券商--香港金融)
- [2. Apple_Push.list — Apple 推送（APNs）](#2-apple_pushlist--apple-推送apns)
- [3. Dola.list — Dola / Cici 豆包海外版](#3-dolalist--dola--cici-豆包海外版)
- [快速订阅 / 一键导入](#-快速订阅--一键导入)
- [各客户端配置](#-各客户端配置)
- [策略组要求](#-策略组要求)
- [更新日志](#-更新日志)
- [免责声明](#-免责声明)
- [参考来源](#-参考来源)

---

## 📦 规则一览

| 文件 | 用途 | 内置策略 | 分流要点 | 更新 |
| --- | --- | --- | --- | --- |
| [HK_Finance.list](HK_Finance.list) | 港股 / 美股券商 + 香港银行金融分流 | `proxy` / `DIRECT` | 券商 → `proxy`，银行 / 支付 / 监管 → `DIRECT` | 2026-06-16 |
| [Apple_Push.list](Apple_Push.list) | Apple 推送（APNs）走代理，恢复海外 App 通知 | `proxy` | 全部 → `proxy` | 2026-06-16 |
| [Dola.list](Dola.list) | Dola / Cici（豆包海外版）分流 + 追踪拦截 | `proxy` / `REJECT` | 核心 → `proxy`（新加坡），追踪 → `REJECT` | 2026-06-16 |

> ⚠️ 规则中的 `proxy` 是**自定义策略组名**，需在你的 Quantumult X 配置中存在名为 `proxy` 的 policy；`DIRECT` / `REJECT` 为内置策略。

---

## 1. HK_Finance.list — 港美股券商 + 香港金融

**Raw 地址**

```text
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/HK_Finance.list
```

**第一性原理（为什么券商要走代理）**

- 2026-05-22 中国证监会对 **富途控股 / 老虎证券 / 长桥证券** 境内外主体立案调查。
- 6/2 老虎、6/3 长桥、6/4 富途相继公告：自 **2026-06-12** 起暂停内地账户**买入 / 转入**，仅保留**卖出 + 出金**；八部门《方案》要求境外券商交易软件 / 服务器逐步关停。
- 实测：大陆**直连**券商 App、行情、登录接口受阻，**挂海外节点（代理）可正常访问**。

> ⟹ 规则原则：所有【券商交易 / 行情 / 登录 / 资金】流量一律走 `proxy`；银行 / 支付 / 监管官网走 `DIRECT`（不受此次整治影响，直连更稳更快）。

**文件结构（8 大分节，共 350 条：249 `proxy` / 101 `DIRECT`）**

| 分节 | 策略 | 覆盖 |
| --- | --- | --- |
| 一、中资跨境互联网券商 | `proxy` | 富途 / Futubull / moomoo、老虎 / TradeUp、长桥 Longbridge / Longport、微牛 Webull |
| 二、国际 / 美资券商 | `proxy` | 盈透 IBKR、嘉信 Schwab / TD、**Firstrade 第一证券**、SoFi、BBAE，及 Robinhood / Fidelity / E\*Trade / Vanguard / tastytrade / Merrill / Morgan Stanley |
| 三、香港本地券商 | `proxy` | 致富 + Megahub、耀才、辉立 Phillip/POEMS、华盛、uSmart、**艾德 Eddid、第一上海、海通国际、国泰君安国际、中银国际、富邦** |
| 四、基金 / 财富平台 | `proxy` | FSMOne / Fundsupermart / iFAST |
| 五、行情 / 数据 | `proxy` | AAStocks、ETNet、Megahub、投资全速易 i-Invest |
| 六、券商后端 IP 段 | `proxy` | 来自 Broker.list（2026-06-13）的 63 条 IP-CIDR（腾讯云港/新 + AWS，券商交易行情后端） |
| 七、银行 / 虚拟银行 / 支付 | `DIRECT` | ZA、Airstar、WeLab、Mox、livi、PAOb、Fusion、蚂蚁银行 / AlipayHK、汇丰 / 恒生、中银香港、花旗、渣打、星展、交行、大新、东亚、信银国际、建行亚洲、永隆、大众、华侨、AEON、八达通、摩根大通 |
| 八、交易所 / 监管 | `DIRECT` | HKEX、HKMA、SFC |

**说明**

- 文件内已混合写入 `DIRECT` / `proxy`，**不要**在 `[filter_remote]` 加 `force-policy`，否则会把银行的 `DIRECT` 一起覆盖。
- 第六节 IP 段以腾讯云香港 / 新加坡通用网段为主，范围较宽；若发现无关腾讯云服务被误代理，可删除对应段。
- 行情 / 交易场景建议在 `proxy` 组内选择**低延迟香港或新加坡节点**。

---

## 2. Apple_Push.list — Apple 推送（APNs）

**Raw 地址**

```text
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Apple_Push.list
```

**背景**：2026 年 5 月起，部分大陆网络环境下 iOS 对部分海外 App（**Telegram、X / Twitter** 等）的 APNs 推送被屏蔽 / 异常；将 APNs 走海外代理后可恢复推送。

**内容**

- `HOST-SUFFIX,push.apple.com` → `proxy`（承载规则，覆盖 `*-courier`、`init` 等所有子域）。
- Akamai 上的 **APNs 专用 CNAME**（`push-apple.com.akadns.net`、`courier-push-apple.com.akadns.net`、`init-*-lb.push-apple.com.akadns.net`）→ `proxy`，精确匹配，避免波及其他 Akamai 服务。
- APNs IPv4（`17.249.0.0/16`、`17.252.0.0/16`、`17.57.144.0/22`、`17.188.128.0/18`、`17.188.20.0/23`）与 IPv6 网段 → `proxy, no-resolve`。
- 宽泛的 `akadns.net` / `apple.com.edgekey.net` **默认注释关闭**（过宽会误伤其他 Akamai/Edgekey 服务）；如推送仍异常可手动解开。

**注意事项**

1. **切勿对 `push.apple.com` 启用 HTTPS 解密（MITM）**，否则推送 TLS 握手失败。
2. **蜂窝（移动数据）下需在代理工具中开启「包含 APNS / 全部网络」**，否则会间歇性失效；规则生效后开关一次飞行模式让长连接重连。
3. APNs 走代理后，**节点故障会导致所有推送（含国内）异常**；建议把 `proxy` 指向「稳定节点 + `DIRECT` 兜底」的策略组。

---

## 3. Dola.list — Dola / Cici 豆包海外版

**Raw 地址**

```text
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Dola.list
```

**应用**：Dola（原 **Cici**，包名 `com.larus.wolf`，开发商 **SPRING (SG) PTE. LTD.** / 字节跳动海外 AI 助手）。区域锁定、仅限海外，大陆直连会被区域限制拦截，**必须走海外节点**；开发主体在新加坡，**建议在 `proxy` 组内优先选择新加坡节点**。

**内容**

- **走 `proxy`**：
  - 核心服务 `dola.com`、`ciciai.com`
  - 字节海外基础设施（仅海外解析）`byteoversea.com`、`byteoversea.net`、`byteintl.net`、`ibytedtos.com`、`ibyteimg.com`、`bytefcdn-oversea.com`
  - 火山引擎媒体后端 `volcvideo.com`（仅放行媒体域，**不放行 `volces.com` 等火山主域**，避免影响国内火山服务）
- **走 `REJECT`**（追踪 / 分析 / 崩溃上报，不影响核心功能）：`appsflyersdk.com`、`app-analytics-services.com`、`ibytedapm.com`、`log-report.volcvideos.com`

> 文件内已分别写明 `proxy` / `REJECT`，**不要**加 `force-policy`，否则拦截规则会失效。

---

## 🚀 快速订阅 / 一键导入

> 「单个规则列表」的一键导入仅 **Quantumult X** 与 **Loon** 原生支持。
> **Shadowrocket / Clash** 的一键导入只针对完整配置 / 模块 / 订阅，**不支持裸 `.list` 规则**；
> 请在配置中用 `RULE-SET` / `rule-providers` 引用（见 [各客户端配置](#-各客户端配置)）。

**Raw 地址**

| 规则 | Raw 地址 |
| --- | --- |
| 港美股券商 + 香港金融 | [HK_Finance.list](https://raw.githubusercontent.com/USCZ/Rule/main/quanX/HK_Finance.list) |
| Apple Push | [Apple_Push.list](https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Apple_Push.list) |
| Dola / Cici AI | [Dola.list](https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Dola.list) |

### Quantumult X（原生一键 → `[filter_remote]`）

| 规则 | 一键导入 |
| --- | --- |
| 港美股券商 + 香港金融 | [导入 Quantumult X](https://quantumult.app/x/open-app/add-resource?remote-resource=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FHK_Finance.list) |
| Apple Push | [导入 Quantumult X](https://quantumult.app/x/open-app/add-resource?remote-resource=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FApple_Push.list) |
| Dola / Cici AI | [导入 Quantumult X](https://quantumult.app/x/open-app/add-resource?remote-resource=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FDola.list) |

网页不跳转时，把下面的 URL Scheme 复制到 Safari：

```text
quantumult-x:///add-resource?remote-resource=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FHK_Finance.list
quantumult-x:///add-resource?remote-resource=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FApple_Push.list
quantumult-x:///add-resource?remote-resource=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FDola.list
```

### Loon（原生一键 → `[Remote Rule]`）

| 规则 | 一键导入 |
| --- | --- |
| 港美股券商 + 香港金融 | [导入 Loon](https://www.nsloon.com/openloon/import?rules=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FHK_Finance.list) |
| Apple Push | [导入 Loon](https://www.nsloon.com/openloon/import?rules=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FApple_Push.list) |
| Dola / Cici AI | [导入 Loon](https://www.nsloon.com/openloon/import?rules=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FDola.list) |

网页不跳转时，把下面的 URL Scheme 复制到 Safari：

```text
loon://import?rules=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FHK_Finance.list
loon://import?rules=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FApple_Push.list
loon://import?rules=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FDola.list
```

> Loon 采用 Surge 系语法：本规则的 `HOST-SUFFIX` / `HOST-KEYWORD` 新版 Loon 多可识别，旧版需改 `DOMAIN-SUFFIX` / `DOMAIN-KEYWORD`；`Apple_Push.list` 里的 `IP6-CIDR` 在 Loon 写作 `IP-CIDR6`。

### Shadowrocket（无裸列表一键 → `[Rule]` 内用 `RULE-SET` 引用）

小火箭一键只支持模块 `shadowrocket://install?module=` / 配置 `shadowrocket://config/add/` / 订阅，**不支持直接导入规则列表**。小火箭兼容 `HOST-*` 语法，在配置 `[Rule]` 引用即可：

```ini
[Rule]
RULE-SET,https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Apple_Push.list,proxy
# 注意：RULE-SET 第三列会给整份列表套统一策略；
# HK_Finance.list / Dola.list 含 DIRECT / REJECT 混合策略，套单一策略会被覆盖——
# 请把这两份的规则展开到 [Rule] 并保留每行末尾的 proxy / DIRECT / REJECT。
```

### Clash / Mihomo / Stash（无裸列表一键 → `rule-providers`）

Clash 的一键 `clash://install-config?url=` 只针对完整 YAML 配置。规则列表需作为 `rule-providers` 写进配置，并把 `HOST-*` 转为 `DOMAIN-*`（`behavior: classical` 的 provider 同样是单一 outbound，混合策略文件需按策略拆分）：

```yaml
rule-providers:
  hk_finance:
    type: http
    behavior: classical
    url: https://raw.githubusercontent.com/USCZ/Rule/main/quanX/HK_Finance.list
    path: ./ruleset/hk_finance.list
    interval: 86400
rules:
  - RULE-SET,hk_finance,🚀 Proxy
```

> Stash 可用 `https://link.stash.ws/install-override/<path>`（`.stoverride`）实现一键，但需先把规则封装成 override 文件。
> 如需我额外生成 **Shadowrocket 模块 / Stash override / Clash 配置** 来实现这两类客户端的真正一键，请告诉我。

---

## ⚙️ 各客户端配置

Quantumult X — 在 `[filter_remote]` 中加入（**不要**加 `force-policy`）：

```ini
[filter_remote]
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/HK_Finance.list, tag=香港金融混合分流, update-interval=86400, opt-parser=false, enabled=true
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Apple_Push.list, tag=Apple Push, update-interval=86400, opt-parser=false, enabled=true
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Dola.list, tag=Dola Cici AI, update-interval=86400, opt-parser=false, enabled=true
```

| 客户端 | 适配状态 | 配置方法 | 注意事项 |
| --- | --- | --- | --- |
| **Quantumult X** | 原生支持 | 用上方一键导入，或把 Raw 地址加入 `[filter_remote]` | 三份文件均已内置策略，不要加 `force-policy`。 |
| **Loon** | 语义基本兼容 | 在 `[Remote Rule]` 添加 Raw 地址；若不识别 `HOST-*`，把 `HOST-SUFFIX` / `HOST-KEYWORD` 改为 `DOMAIN-SUFFIX` / `DOMAIN-KEYWORD` | 混合策略文件不要套统一 policy，否则 `REJECT` / `DIRECT` 被覆盖。 |
| **Shadowrocket** | 语义基本兼容 | 在 `[Rule]` 引用 Raw 地址，或从 App 规则 URL 导入 | 保留每行末尾的 `proxy` / `DIRECT` / `REJECT`，不要用单一策略包裹整份文件。 |
| **Clash / Mihomo** | 需转换 | 把 `HOST-*` 转为 `DOMAIN-*` / `IP-CIDR` 后放入 `rules:`，或按策略拆成多个 `rule-providers` | `RULE-SET,xxx,proxy` 会给整份 provider 套统一策略，不适合混合策略文件。 |
| **V2Ray / Xray** | 需转换 | 取规则后转换为 `routing.rules`，分别映射到 `proxy` / `direct` / `block` outboundTag | `Apple_Push.list` 全 `proxy` 最易转换；`HK_Finance.list` 含 `DIRECT`/`proxy` 混合。 |

Clash / Mihomo 转换示例（Apple Push 部分）：

```yaml
rules:
  - DOMAIN-SUFFIX,push.apple.com,proxy
  - DOMAIN,push-apple.com.akadns.net,proxy
  - DOMAIN,courier-push-apple.com.akadns.net,proxy
  - IP-CIDR,17.249.0.0/16,proxy,no-resolve
  # HK_Finance / Dola 建议把 HOST-* 转为 DOMAIN-* 后展开，或按 proxy/direct/reject 拆分 provider。
```

---

## 🧩 策略组要求

- `proxy`：**自定义代理策略组**，必须在你的 Quantumult X 配置 `[policy]` 中存在同名 policy。
- Apple Push 的 `proxy` 建议指向「稳定节点 + `DIRECT` 兜底」组（避免节点故障导致全部推送异常）。
- Dola 的 `proxy` 建议指向**新加坡**节点 / 策略组。
- `DIRECT` / `REJECT` 为 Quantumult X 内置策略，无需额外定义。

---

## 📝 更新日志

### 2026-06-16

- **HK_Finance.list**：依据 6·12 跨境券商整治第一性原理**重新设计**为 8 大分节；券商（中资跨境 / 美资 / 香港本地）全部走 `proxy`，新增 **Firstrade、艾德 Eddid、第一上海、海通国际、国泰君安国际、中银国际、华盛、tastytrade、Robinhood、Fidelity、E\*Trade、Vanguard、Merrill、Morgan Stanley** 等；合入 Broker.list（2026-06-13）后端 IP 段；银行 / 支付 / 监管保持 `DIRECT`。清理过宽的 `HOST-KEYWORD,invest`、typo `i-innvest.com`、无效 `za.group.com`。共 350 条（249 `proxy` / 101 `DIRECT`）。
- **Apple_Push.list**：改为 Quantumult X 语法并把 APNs（`push.apple.com` + 17.x 段 + IPv6）走 `proxy`；用精确的 Akamai APNs CNAME 取代宽泛的 `akadns.net`（后者降级为可选注释）；补充蜂窝「包含 APNS」、禁止 MITM、节点兜底等注意事项。
- **Dola.list**：补充字节跳动海外基础设施域名（`byteoversea` / `ibytedtos` / `ibyteimg` / `bytefcdn-oversea` / `byteintl`），仅放行 `volcvideo` 媒体域；新增 `ibytedapm` 拦截；标注应用为 Cici→Dola（`com.larus.wolf`，新加坡主体），建议新加坡节点。

---

## ⚠️ 免责声明

- 本仓库为**个人自用**的网络分流规则，仅用于技术研究与学习交流，**不构成任何投资建议**。
- 规则仅改变流量走向，**不提供任何代理服务 / 节点**，也不保证任何 App 或服务可用。
- 请在**遵守所在地法律法规**及各服务条款的前提下使用；因使用本规则产生的任何后果由使用者自行承担。
- 券商相关服务受监管政策影响，可达性可能随时变化，规则不保证时效性。

---

## 🔗 参考来源

- 证监会立案 / 三大券商限购时间表：[新浪财经](https://finance.sina.com.cn/jjxw/2026-06-05/doc-iniahnxe3313627.shtml) · [21 经济网](https://www.21jingji.com/article/20260605/herald/46c67c0f120bcfc59b77497e0791a21c.html)
- 券商域名 / IP 规则：Broker.list（`Allen2023/broker-rules`，2026-06-13）、[blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script)
- iOS 海外 App 推送异常与 APNs 代理：社区实测（V2EX / NodeSeek）、[Apple 官方推送排障 102266](https://support.apple.com/en-us/102266)
- 券商官网：[Eddid](https://www.eddid.com.hk/) · [第一上海](http://www.firstshanghai.com.hk/) · [海通国际](https://www.htisec.com) · [国泰君安国际](https://www.gtjai.com/sc) · [中银国际](http://www.bocichina.com) · [Firstrade](https://www.firstrade.com/)
