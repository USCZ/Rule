# Quantumult X 规则集自用规则

这个目录用于存放 Quantumult X 自用分流规则。当前包含 Apple Push、Dola / Cici AI、香港银行券商直连三类规则。后续新增规则也默认按自用场景维护。

## 规则列表

### 1. Apple_Push.list

用途：Apple Push 通知相关规则。

Raw 地址：

```text
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Apple_Push.list
```

Quantumult X 示例：

```ini
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Apple_Push.list, tag=Apple Push, force-policy=proxy, update-interval=86400, opt-parser=false, enabled=true
```

说明：

- 包含 `push.apple.com`、`akadns.net`、`apple.com.edgekey.net` 等域名规则。
- 包含 Apple Push 常见 IPv4 / IPv6 网段。
- 本自用规则建议 Apple Push 走代理策略，而不是直连。
- 近期部分 App，例如 Telegram、X / Twitter，在直连状态下可能出现推送不稳定或无推送；通过代理策略转发 Apple Push 连接，有助于恢复通知。
- 如果你的主代理策略组名称不是 `proxy`，请把示例中的 `force-policy=proxy` 改成你实际使用的代理策略组名称。

---

### 2. Dola.list

用途：Dola / Cici AI 相关服务分流与追踪拦截。

Raw 地址：

```text
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Dola.list
```

Quantumult X 示例：

```ini
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Dola.list, tag=Dola Cici AI, update-interval=86400, opt-parser=false, enabled=true
```

说明：

- `dola.com`、`ciciai.com`、`volcvideo.com`、`ibyteimg.com` 走 `proxy`。
- `appsflyersdk.com`、`app-analytics-services.com`、`log-report.volcvideos.com` 走 `REJECT`。
- 该规则文件内部已经分别写明 `proxy` 和 `REJECT` 策略，不建议在 `[filter_remote]` 中添加 `force-policy`，否则可能覆盖文件内策略，导致追踪/分析拦截规则失效。
- Dola / Cici AI 的代理策略建议落到新加坡节点；可将你的 `proxy` 策略组固定选择新加坡节点，或把 `Dola.list` 文件中的 `proxy` 改成你配置里的新加坡策略组名称。

---

### 3. HK_Finance_Direct.list

用途：香港银行、券商、金融行情服务直连规则。

Raw 地址：

```text
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/HK_Finance_Direct.list
```

Quantumult X 示例：

```ini
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/HK_Finance_Direct.list, tag=港股银行券商直连, force-policy=DIRECT, update-interval=86400, opt-parser=false, enabled=true
```

覆盖 App / 服务：

- 众安银行 ZA Bank
- 天星银行 Airstar Bank
- 汇立银行 WeLab Bank
- 致富证券 Chief Securities
- 盈透证券 Interactive Brokers / IBKR
- 嘉信理财 Charles Schwab
- 蚂蚁银行 Ant Bank HK
- FSMOne / Fundsupermart
- BBAE
- 汇丰香港 HSBC HK
- 投资全速易 / i-Invest / AAStocks 相关服务
- 其他香港金融常见辅助域名

说明：

- 规则内策略统一为 `DIRECT`。
- 建议放在代理规则之前，避免银行、券商 App 因代理 IP、风控、地区判断导致登录或交易异常。
- 文件中包含少量 `IP-ASN` 辅助规则；如果你的 Quantumult X 版本不支持，可删除对应行。

## 推荐配置示例

在 Quantumult X 配置的 `[filter_remote]` 中加入：

```ini
[filter_remote]
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Apple_Push.list, tag=Apple Push, force-policy=proxy, update-interval=86400, opt-parser=false, enabled=true
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Dola.list, tag=Dola Cici AI, update-interval=86400, opt-parser=false, enabled=true
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/HK_Finance_Direct.list, tag=港股银行券商直连, force-policy=DIRECT, update-interval=86400, opt-parser=false, enabled=true
```

## 使用建议

- `Apple_Push.list`：建议放在较前位置，并使用 `force-policy=proxy`，让 Apple Push 默认走代理策略，用于改善 Telegram、X / Twitter 等 App 的推送问题。
- `Dola.list`：规则内已包含 `proxy` 和 `REJECT`，不要使用 `force-policy` 覆盖；其中 `proxy` 建议对应新加坡节点或新加坡策略组。
- `HK_Finance_Direct.list`：建议放在银行/金融相关规则优先级较高的位置，并强制 `DIRECT`。
- 如遇 App 域名变更，可通过 Quantumult X 日志或 MitM/抓包补充规则。
