# Quantumult X 自用规则集 · `quanX`

![Quantumult X](https://img.shields.io/badge/Quantumult%20X-rules-1E90FF)
![Last Commit](https://img.shields.io/github/last-commit/USCZ/Rule/main)
![Repo Size](https://img.shields.io/github/repo-size/USCZ/Rule)

> 面向中国大陆 / 香港用户的 **Quantumult X** 分流规则：港股/美股券商、Apple 推送（APNs）、Dola（豆包海外版）。
>
> 三份文件的策略**都已写在文件内**，订阅后即生效。**是否加 `force-policy` 要按文件区分**：
> - `Apple_Push.list` —— 全文 `proxy`，**加不加都安全**（加了就用你指定的策略组）；
> - `HK_Finance.list` / `Dola.list` —— `DIRECT` / `proxy` / `REJECT` **混用**，**不要加**，
>   否则文件内的 `DIRECT`（银行 / 交易所）与 `REJECT`（追踪拦截）会被一起覆盖。

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
| [HK_Finance.list](HK_Finance.list) | 港股 / 美股券商 + 香港银行金融分流 | `proxy` / `DIRECT` | 券商 → `proxy`；银行 / 支付 / 监管 / 致富证券 → `DIRECT` | 2026-09-29 |
| [Apple_Push.list](Apple_Push.list) | Apple 推送（APNs）走代理，恢复海外 App 通知 | `proxy` | 全部 → `proxy`（12 条） | 2026-09-29 |
| [Dola.list](Dola.list) | Dola / Cici（豆包海外版）分流 + 追踪拦截 | `proxy` / `REJECT` | 核心 → `proxy`（新加坡，10 条），追踪 → `REJECT`（4 条） | 2026-09-29 |

> ⚠️ 规则里的 `proxy` 是 **Quantumult X 内置策略**（不是自定义策略组名），含义是「走代理」，
> 具体出口由 **QX 主界面当前选中的节点 / 策略组**决定；`DIRECT` / `REJECT` 同样是内置策略。
> 官方原文：「Quantumult X 默认有 3 个自带策略：`DIRECT` 直连 / `PROXY` 代理 / `REJECT` 阻止」。
> 因此**不需要**在你的配置里额外定义名为 `proxy` 的策略组。

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
>
> **唯一例外 · 致富证券（Chief / 致富通）**：其 App 会主动检测 VPN 虚拟网卡接口，走 `proxy` 会导致「网络检测失败」或速度极慢，因此与银行同类**强制 `DIRECT`**，且放在文件最前面。

**文件结构（2 部分 · 31 个分节，共 338 条：196 `proxy` / 142 `DIRECT`）**

> 分节按「先直连、后代理」排列，与策略分布一致，便于审计。

**第一部分 · `DIRECT`（142 条）**

| 分节 | 条数 | 覆盖 |
| --- | --- | --- |
| 1-1 致富证券 Chief | 31 | 致富 / 致富通 Chief、Megahub（App 自检 VPN 接口，**硬直连**） |
| 1-2 香港虚拟银行 | 19 | ZA、Airstar 天星、WeLab 汇立、Mox、livi、PAOb、Fusion |
| 1-3 蚂蚁银行 + AlipayHK | 16 | Ant Bank HK、AlipayHK |
| 1-4 汇丰 / 恒生 | 23 | HSBC、Hang Seng |
| 1-5 其他在港银行与支付 | 37 | 中银香港、花旗、渣打、星展、交行、大新、东亚、信银国际、建行亚洲、永隆、大众、华侨、AEON、八达通、摩根大通 |
| 1-6 交易所 / 监管 | 5 | HKEX、HKMA、SFC |
| 1-7 大陆 A 股行情源 | 11 | 券商用的大陆行情接口（多已被 `geoip, cn` 覆盖，此处显式声明） |

**第二部分 · `proxy`（196 条）**

| 分节 | 条数 | 覆盖 |
| --- | --- | --- |
| 2-1 富途 Futu / Futubull / moomoo | 45 | 受 6·12 影响最大，务必走 `proxy` |
| 2-2 老虎 Tiger / 老虎国际 / TradeUp | 20 | |
| 2-3 长桥 Longbridge / LongPort | 14 | |
| 2-4 微牛 Webull | 5 | |
| 2-5 盈透证券 IBKR | 12 | |
| 2-6 嘉信 Schwab / TD Ameritrade | 12 | |
| 2-7 Firstrade 第一证券 | 3 | |
| 2-8 其他美资综合券商 | 12 | Robinhood / Fidelity / E\*Trade / Vanguard / tastytrade / Merrill / Morgan Stanley |
| 2-9 SoFi | 3 | |
| 2-10 BBAE 必贝 | 4 | |
| 2-11 耀才 Bright Smart | 3 | |
| 2-12 辉立 Phillip / POEMS | 2 | |
| 2-13 华盛 Valuable Capital | 2 | |
| 2-14 盈立 uSmart | 2 | |
| 2-15 艾德金融 Eddid | 5 | |
| 2-16 第一上海 First Shanghai | 2 | |
| 2-17 海通国际 Haitong International | 3 | |
| 2-18 国泰君安国际 GTJA International | 2 | |
| 2-19 中银国际证券 BOCI | 4 | 中银国际证券 + 母公司中银国际控股 |
| 2-20 宝盛 Monex BOOM | 3 | |
| 2-21 富邦证券（香港）Fubon | 1 | |
| 2-22 FSMOne / Fundsupermart / iFAST | 9 | |
| 2-23 行情 / 数据服务 | 8 | AAStocks、ETNet、Megahub、投资全速易 i-Invest |
| 2-24 券商后端 IP 段 | 20 | 仅 `/32` 精确主机（腾讯云港 / 新 + AWS） |

**说明**

- 文件内已混合写入 `DIRECT` / `proxy`，**不要**在 `[filter_remote]` 加 `force-policy`，否则会把银行的 `DIRECT` 一起覆盖。
- 第 2-24 节只保留 **20 条 `/32` 精确主机**。v1 曾有 43 条 `/23`、`/24` 网段，v2.0 已全部删除 —— 其中 13 条是**腾讯云大陆段**，一旦被搬进 `[filter_local]` 会先于 `geoip, cn` 命中，把大陆 IP 整体劫持进代理。详见文件末尾「附录 A」。
- 致富证券的 72 条显式子域规则已删除（被 `HOST-SUFFIX` 完整覆盖，QX 的 `HOST-SUFFIX` 匹配任意层级子域）；`HOST-KEYWORD` 兜底保留，因其匹配范围比后缀更宽、不是冗余。详见「附录 B」。
- 行情 / 交易场景建议在 `proxy` 组内选择**低延迟香港或新加坡节点**。

---

## 2. Apple_Push.list — Apple 推送（APNs）

**Raw 地址**

```text
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Apple_Push.list
```

**背景**：2026 年 5 月起，部分大陆网络环境下 iOS 对部分海外 App（**Telegram、X / Twitter** 等）的 APNs 推送被屏蔽 / 异常；将 APNs 走海外代理后可恢复推送。

**内容（v2.0，共 12 条，全部 `proxy`）**

- `HOST-SUFFIX,push.apple.com` → `proxy`。承载推送长连接的 `N-courier.push.apple.com` 就挂在它下面。
  注意 `push.apple.com` 本身**没有 A 记录**，只有 `N-courier.*` 这类子域有，所以必须用 `HOST-SUFFIX` 而不是 `HOST`。
- `HOST-SUFFIX,push-apple.com.akadns.net` → `proxy`。**v2.0 由 4 条 `HOST` 合并而来**（原为 apex + 3 条 `init-*-lb`），
  QX 的 `HOST-SUFFIX` 匹配任意层级子域，因此是**严格超集** —— 还能覆盖未来新增的 init 变体与 courier 的 CNAME 目标。
- `HOST-SUFFIX,courier-push-apple.com.akadns.net` → `proxy`（同理，覆盖 `1.courier-push-apple.com.akadns.net` 等实际名字）。
- **IPv4 5 条**（`17.249.0.0/16`、`17.252.0.0/16`、`17.57.144.0/22`、`17.188.128.0/18`、`17.188.20.0/23`）
  与 **IPv6 4 条** → `proxy, no-resolve`。**与 Apple 官方列表逐条核对，完全一致**（见「参考来源」）。
- 宽泛的 `akadns.net` / `apple.com.edgekey.net` **默认注释关闭**（过宽会误伤其他 Akamai / Edgekey 服务）；如推送仍异常可手动解开。

**为什么不用整个 `17.0.0.0/8`**

Apple 官方原话是「**最好**让设备访问整个 `17.0.0.0/8`（该网段已分配给 Apple）」，那 5 个 IPv4 段是官方给出的
「做不到 17/8 时的最小集合」。本文件采用最小集合，避免把 App Store 下载、iCloud、软件更新等 Apple 流量一并代理掉。

**域名规则与 IP 规则为什么要并存**

`no-resolve` 表示「不为了匹配本规则而额外做 DNS 解析」。所以：
设备用域名连 → 由 `HOST-SUFFIX` 命中；设备用缓存 IP 直连 → 由 `IP-CIDR` 命中。两条路径都覆盖，这正是两类规则并存的原因。

**注意事项**

1. **切勿对 `push.apple.com` 启用 HTTPS 解密（MITM）**，否则推送 TLS 握手失败。
   等价说法：不要在 `[mitm] hostname` 里写 `push.apple.com` 或 `*.apple.com`。
2. **端口**：设备侧 TCP **5223** 为主，**443 / 2197** 为回落。属运营商 / 防火墙层面，代理侧无需额外配置。
3. APNs 走代理后，**节点故障会导致所有推送（含国内）异常**；请把 `proxy` 指向**稳定节点**，不要用抖动大的自动测速组。
4. **本文件全 `proxy`，加 `force-policy` 是安全的**（v1 头部照抄了混合策略文件的模板，误写成「请勿设置」，v2.0 已改正）。

**一个需要实测的观察项**

实测（2026-09-29，DoH ECS = 中国大陆）：`init-*-lb.push-apple.com.akadns.net` 在大陆解析到 **金山云 CDN**：

```text
init-p01md-lb.push-apple.com.akadns.net
  → init-p01md-cn.push-apple.com.akadns.net
  → init-p01md.apple.com.download.ks-cdn.com
  → k128-mzstatic.gslb.ksyuncdn.com
```

而真正的推送长连接走 `N-courier.push.apple.com` → `17.57.145.x`（落在官方 `17.57.144.0/22` 内）。
也就是说 `init-*` 是 APNs 的**初始化资源**步骤，由 Apple 的**大陆 CDN** 承载、本身没被墙；把它也代理到海外，
理论上会让这一步改用海外 CDN、变慢。保留它是因为无法在无 iOS 设备的情况下验证移除是否影响推送
（按「不改无法验证的行为」原则）。若你实测推送正常但启动变慢，可注释掉 `push-apple.com.akadns.net` 那一行，
`N-courier.push.apple.com` 由第一条规则单独覆盖、不受影响。

---

## 3. Dola.list — Dola / Cici 豆包海外版

**Raw 地址**

```text
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Dola.list
```

**应用**：Dola（原 **Cici**，包名 `com.larus.wolf`，开发商 **SPRING (SG) PTE. LTD.** / 字节跳动海外 AI 助手）。区域锁定、仅限海外，大陆直连会被区域限制拦截，**必须走海外节点**；开发主体在新加坡，**建议在 `proxy` 组内优先选择新加坡节点**。

**内容（v2.0，共 14 条 = 10 `proxy` + 4 `REJECT`）**

- **走 `proxy`**：
  - 核心服务 `dola.com`、**`cici.com`（v2.0 新增）**、`ciciai.com`
  - 字节海外基础设施（仅海外解析）`byteoversea.com`、`byteoversea.net`、`byteintl.net`、`ibytedtos.com`、`ibyteimg.com`、`bytefcdn-oversea.com`
  - 火山引擎媒体后端 `volcvideo.com`（仅放行媒体域，**不放行 `volces.com` 等火山主域**，避免影响国内火山服务）
- **走 `REJECT`**（追踪 / 分析 / 崩溃上报，不影响核心功能）：`appsflyersdk.com`、`app-analytics-services.com`、`ibytedapm.com`、`log-report.volcvideos.com`

**v2.0 为什么补了 `cici.com`**

v1 只写了 `ciciai.com`，漏了 `cici.com`。实测（2026-09-29）：`cici.com` 与 `www.ciciai.com` 解析到**同一组 Akamai IP**
（`cici.com.edgesuite.net` → `a379.t.akamai.net` → `23.46.155.2xx`），且 `cici.com` 就是 Dola 当前的**官方站点**
（`https://www.cici.com/` 标题「Dola, your AI assistant.」）。只写 `ciciai.com` 会让访问 `cici.com` 的请求落到 `final`。

**⚠️ `REJECT` 段这四个都是「第三方通用」域名，不是 Dola 专属**

| 域名 | 归属 | 说明 |
| --- | --- | --- |
| `appsflyersdk.com` | AppsFlyer | 归因 SDK，被**大量** App 使用 |
| `app-analytics-services.com` | **Google**（Firebase Analytics） | 由 `firebase-ios-sdk` issue #12720 确认，**别被名字误导成 Apple 的** |
| `ibytedapm.com` | 字节 APM | 崩溃 / 性能上报 |
| `log-report.volcvideos.com` | 字节 | 日志上报。注意是 `volcvideos`（**复数**），与上面走代理的 `volcvideo`（单数）**不是同一个域名**；实测只有 `log-report` 这一个子域存在（`log.` / `report.` / `api.volcvideos.com` 均 NXDOMAIN），故用 `HOST` 精确匹配 |

拦截它们会**同时影响其它使用同一 SDK 的 App**。对本仓库的用途（去广告 / 隐私）而言这是期望行为；
若你依赖某个 App 的归因统计，把对应行改成 `proxy` 即可。

> **`force-policy` 的取舍**：文件内 `proxy` / `REJECT` 混用，**不要**加 `force-policy`，否则拦截会失效。
> 若你确实需要统一策略（例如固定走「特殊节点」），请**在本地 `[filter_local]` 把这 4 条补回来**（本地规则优先于远程）：
>
> ```ini
> host-suffix, appsflyersdk.com, reject
> host-suffix, app-analytics-services.com, reject
> host-suffix, ibytedapm.com, reject
> host, log-report.volcvideos.com, reject
> ```
>
> **✅ 已实测证实（2026-09-29）**：在 QX「分流规则」页搜 `appsflyersdk`，本文件那条显示为
> **「特殊节点」而非 `REJECT`** → `force-policy` 确实会**覆盖**规则集内部的策略。
> 官方文档「如果远程分流文件中已经指明则此处可忽略」的措辞有歧义，此处以实测为准。
> 反过来说：**只要使用者加了 `force-policy`，本文件下方那 4 条 `REJECT` 就是死规则**，
> 必须靠上面这段本地补回才能生效。

> **🔎 实测时的一个副产品（值得所有 QX 用户知悉）**：同一次搜索暴露出 `appsflyersdk` 在本机
> 共有 **3 个拦截源** —— 本地规则 / 本文件 / 第三方 **Loon 插件**（kelee `BlockAdvertisers.lpx`）。
> 最后那个的原理是：Loon 插件的 `[Rule]` 段会被 QX 的解析器（`opt-parser=true`）
> **转换成真正的分流规则**注入 QX。也就是说，**你以为只是「去广告重写」的资源也在改路由**，
> 而 `[rewrite_remote]` 条目**没有 `force-policy` 可用来约束它**（该字段只作用于 `[filter_remote]`）。
> 排查「某域名为什么走了奇怪的策略」时，请务必在「分流规则」页留意 tag 是插件名的条目。

**未收录的候选域名（观察项：已核查，但证据不足，故不加）**

- `dola.ai` —— HTTP 200 但只有 114 字节的占位页，无法确认归属
- `cici.ai` —— HTTP 436，无法确认归属
- `dola.com.cn` —— 域名已**过期**，解析到 `overdue.aliyun.com`（阿里云过期域名页）
- `byteintl.com` —— 域名存在（apex 无 A 记录），但与 `byteintl.net` 是否同用途未验证

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
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/HK_Finance.list, tag=港美股券商及香港银行, update-interval=86400, opt-parser=false, enabled=true
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

- `proxy`：**Quantumult X 内置策略**，含义是「走代理」，出口由 **QX 主界面当前选中的节点 / 策略组**决定。
  **不需要**（也不应该）在你的配置里定义名为 `proxy` 的策略组 —— 与内置策略重名行为不可预期。
  若想让某份规则固定走某个策略组，请用 `force-policy=<你的策略组>`（注意先看该文件是否混用策略）。
- `DIRECT` / `REJECT` 同样是 Quantumult X 内置策略，无需额外定义。
- Apple Push 的出口建议选**稳定节点**（避免节点故障导致全部推送异常）；
  Dola 建议选**新加坡**节点 —— 两者都可以通过 `force-policy` 指定专属策略组来实现。

---

## 📝 更新日志

### 2026-09-29

**HK_Finance.list v2.1** —— 全文件审计后重构，规则 451 → **338 条（142 `DIRECT` / 196 `proxy`）**：

- **删除 43 条 `/23`、`/24` IP 网段**，只保留 20 条 `/32` 精确主机。被删段中 13 条是**腾讯云大陆段**（`1.14.242.0/23`、`42.193.128.0/24`、`106.55.66.0/23` 等）——作为远程条目时会被本地 `geoip, cn, direct` 先行命中，属纯负担；但一旦被搬进 `[filter_local]` 就会抢先命中、把大陆 IP 劫持进代理，公共规则集必须消除这个隐患。
- **删除 73 条冗余 `HOST` 规则**：致富证券 72 条显式子域（`api.` / `cdn.` / `quote.` / `service.` / `toptrader.` …）全部被 `HOST-SUFFIX` 覆盖，另 1 条 `HOST,gator.uba.ap-southeast-1.volces.com`。
- **修正误判**：`HOST-SUFFIX,octopus.com,DIRECT` → **`octopus.com.hk`**（`octopus.com` 是英国 Octopus Energy / Octopus Deploy；八达通卡官网是 `octopus.com.hk`）。
- **补齐** `futu.hk`（`ibkr.com.hk` 上游已有，未重复添加）。
- **补齐中银国际 BOCI 的 3 个遗漏域名**：`bocichina.com.cn`（经 DNS 验证为 `bocichina.com` 的 CNAME 别名）、`bocichina.cn`（有 NS / SOA / SPF，同属该机构）、`boci.com.hk`（母公司中银国际控股，`www.boci.com.hk` 返回 200）。注意 `HOST-SUFFIX,bocichina.com` **不**覆盖 `bocichina.com.cn`（后缀匹配要求以 `.bocichina.com` 结尾），必须单独列出。
- **分节重排为「先直连、后代理」**，编号 1-1…1-7 / 2-1…2-24，与策略分布一致；全文件按「同类型 + 同值」去重。

**Apple_Push.list v2.0** —— 规则 15 → **12 条（全部 `proxy`）**：

- **合并 4 条 `HOST` 为 1 条 `HOST-SUFFIX`**：原写 apex `push-apple.com.akadns.net` + 3 条 `init-*-lb.push-apple.com.akadns.net`；QX 的 `HOST-SUFFIX` 匹配任意层级子域，合并后是**严格超集**（覆盖未来新增变体 + courier 的 CNAME 目标）。同理 `courier-push-apple.com.akadns.net` 由 `HOST` 改 `HOST-SUFFIX`。
- **9 个 IP 段与 Apple 官方列表逐条核对，完全一致**（IPv4 5 条 + IPv6 4 条）。
- **改正头部表述**：v1 直接写「请勿设置 force-policy」，那是照抄混合策略文件的模板 —— 本文件全 `proxy`，加 `force-policy` 是**安全**的，原表述会让使用者误以为必须放弃统一策略组。
- 补充端口（5223 主 / 443·2197 回落）、`no-resolve` 与域名规则并存的原因、以及「为何不用整个 `17.0.0.0/8`」。
- 记录一个**观察项**：`init-*-lb.push-apple.com.akadns.net` 在大陆解析到**金山云 CDN**（`*.apple.com.download.ks-cdn.com`），属 APNs 初始化步骤、本身没被墙；真正被墙的是 `N-courier.push.apple.com`。

**Dola.list v2.1** —— 规则条数不变（**14 条**），仅文档升级：

- 把「`force-policy` 会覆盖规则集内部策略」从「社区共识 / 官方措辞有歧义」**升级为已实测证实**：
  在 QX「分流规则」页搜 `appsflyersdk`，本文件那条显示为**「特殊节点」而非 `REJECT`**。
- 附带发现：同一次搜索暴露出 `appsflyersdk` 在本机共有 **3 个拦截源**，其中一个来自
  第三方 **Loon 插件**（`BlockAdvertisers.lpx`）—— 其 `[Rule]` 段被 QX 解析器**注入成分流规则**。
  即使本文件的 `REJECT` 被 `force-policy` 覆盖，拦截仍可能由其它来源兜住，**但那是巧合、不可依赖**。

**Dola.list v2.0** —— 规则 13 → **14 条（10 `proxy` + 4 `REJECT`）**：

- **补齐 `cici.com`**（v1 只写了 `ciciai.com`）。实测 `cici.com` 与 `www.ciciai.com` 解析到同一组 Akamai IP，且 `cici.com` 就是 Dola 当前官方站点（标题「Dola, your AI assistant.」）。
- **头部补齐「`force-policy` 会消灭 `REJECT` 时怎么办」** —— 给出在本地补回那 4 条的现成写法。
- **`REJECT` 段补充归属说明**：`app-analytics-services.com` 是 **Google Firebase Analytics**（不是 Apple 的，别被名字误导）；`log-report.volcvideos.com` 是 `volcvideos` **复数**，与走代理的 `volcvideo` 单数不是同一域名；这四个都是**第三方通用**域名，拦截会影响其它 App。
- 记录 4 个**观察项**（`dola.ai` / `cici.ai` / `dola.cn` 类域名 / `byteintl.com`）：已核查但证据不足，故不收录。

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
- iOS 海外 App 推送异常与 APNs 代理：社区实测（V2EX / NodeSeek）、[Apple 官方推送排障 102266](https://support.apple.com/en-us/102266)（本仓库 APNs 的 9 个 IP 段即出自该文档，2026-09-29 复核一致）
- Quantumult X 内置策略（`DIRECT` / `PROXY` / `REJECT`）与规则优先级：[DivineEngine《Quantumult X 入门：策略与分流》](https://divineengine.net/article/quantumult-x-filter-and-policy/)
- `app-analytics-services.com` 归属 Google Firebase Analytics：[firebase-ios-sdk issue #12720](https://github.com/firebase/firebase-ios-sdk/issues/12720)
- 券商官网：[Eddid](https://www.eddid.com.hk/) · [第一上海](http://www.firstshanghai.com.hk/) · [海通国际](https://www.htisec.com) · [国泰君安国际](https://www.gtjai.com/sc) · [中银国际](http://www.bocichina.com) · [Firstrade](https://www.firstrade.com/)
