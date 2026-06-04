# Quantumult X 规则集自用规则

这个目录用于存放 Quantumult X 自用分流规则。当前包含 Apple Push、Dola / Cici AI、香港金融服务混合分流三类规则。后续新增规则也默认按自用场景维护。

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

### 3. HK_Finance.list

用途：香港银行、券商、金融行情服务混合分流规则。银行、支付、监管公开站点按风控保守直连；富途/富途牛牛/Futubull/Moomoo 以及其他离岸券商、行情、交易接口按大陆访问稳定性走代理。

Raw 地址：

```text
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/HK_Finance.list
```

Quantumult X 示例：

```ini
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/HK_Finance.list, tag=香港金融混合分流, update-interval=86400, opt-parser=false, enabled=true
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
- 富途 / 富途牛牛 / Futubull / Moomoo
- 汇丰香港 HSBC HK
- 投资全速易 / i-Invest / AAStocks 相关服务
- 其他香港金融常见辅助域名

说明：

- 规则内已经混合写入 `DIRECT` 和 `proxy`，不要在 `[filter_remote]` 中添加 `force-policy`，否则会覆盖文件内策略。
- 银行/支付/监管公开站点使用 `DIRECT`，包括虚拟银行、汇丰/恒生/中银/渣打/花旗/星展/八达通等。
- 离岸券商、行情推送、交易执行和报价卡使用 `proxy`，包括富途/Moomoo、老虎、长桥、Webull、IBKR、Schwab、致富、FSMOne、AAStocks、ETNet 等。
- 富途相关主域包括 `futunn.com`、`moomoo.com`、`futuholdings.com`、`futuhk.com`、`futufin.com`；其中 `futunn.com` 后缀覆盖 `qtcard`、`openapi`、`api`、`api5`、`trade`、`support`、`download` 以及 WebSocket 子域。
- 建议使用低延迟香港或新加坡节点，尤其是行情和交易场景。
- 文件不再包含宽泛的云厂商 `IP-ASN` 规则，避免误伤非金融流量。

## 推荐配置示例

在 Quantumult X 配置的 `[filter_remote]` 中加入：

```ini
[filter_remote]
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Apple_Push.list, tag=Apple Push, force-policy=proxy, update-interval=86400, opt-parser=false, enabled=true
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Dola.list, tag=Dola Cici AI, update-interval=86400, opt-parser=false, enabled=true
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/HK_Finance.list, tag=香港金融混合分流, update-interval=86400, opt-parser=false, enabled=true
```

## 使用建议

- `Apple_Push.list`：建议放在较前位置，并使用 `force-policy=proxy`，让 Apple Push 默认走代理策略，用于改善 Telegram、X / Twitter 等 App 的推送问题。
- `Dola.list`：规则内已包含 `proxy` 和 `REJECT`，不要使用 `force-policy` 覆盖；其中 `proxy` 建议对应新加坡节点或新加坡策略组。
- `HK_Finance.list`：建议放在银行/金融相关规则优先级较高的位置；规则内已有 `DIRECT` / `proxy` 混合策略，不要使用 `force-policy` 覆盖。
- 如遇 App 域名变更，可通过 Quantumult X 日志或 MitM/抓包补充规则。
