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
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Apple_Push.list, tag=Apple Push, update-interval=86400, opt-parser=false, enabled=true
```

说明：

- 包含 `push.apple.com`、`push-apple.com.akadns.net`、`apple.com.edgekey.net` 等 APNs 相关域名规则。
- 包含 Apple 官方 APNs 文档列出的常见 IPv4 / IPv6 网段，并在规则内显式写入 `proxy`。
- 本自用规则建议 Apple Push 走代理策略，而不是直连。
- 近期部分 App，例如 Telegram、X / Twitter，在直连状态下可能出现推送不稳定或无推送；通过代理策略转发 Apple Push 连接，有助于恢复通知。
- 规则文件内部已经写明 `proxy`，一般不需要在 `[filter_remote]` 中额外添加 `force-policy=proxy`。

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

- `dola.com`、`cici.com`、`ciciai.com`、`ciciaicdn.com` 以及 Dola / Cici 当前使用的新加坡区 ByteDance/Cici CDN、API、鉴权和签名媒体域名走 `proxy`。
- Dola / Cici AI 的代理策略建议落到新加坡节点；可将你的 `proxy` 策略组固定选择新加坡节点，或把 `Dola.list` 文件中的 `proxy` 改成你配置里的新加坡策略组名称。
- `appsflyersdk.com`、`app-analytics-services.com`、`log-report.volcvideos.com` 走 `REJECT`。
- 该规则文件内部已经分别写明 `proxy` 和 `REJECT` 策略，不建议在 `[filter_remote]` 中添加 `force-policy`，否则可能覆盖文件内策略，导致追踪/分析拦截规则失效。

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

### 一键导入 / Raw 链接

| 规则 | Raw 地址 | Quantumult X 一键导入 |
| --- | --- | --- |
| Apple Push | [Apple_Push.list](https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Apple_Push.list) | [导入 QX](https://quantumult.app/x/open-app/add-resource?remote-resource=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FApple_Push.list) |
| Dola / Cici AI | [Dola.list](https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Dola.list) | [导入 QX](https://quantumult.app/x/open-app/add-resource?remote-resource=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FDola.list) |
| 香港金融混合分流 | [HK_Finance.list](https://raw.githubusercontent.com/USCZ/Rule/main/quanX/HK_Finance.list) | [导入 QX](https://quantumult.app/x/open-app/add-resource?remote-resource=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FHK_Finance.list) |

如果 GitHub App 或浏览器不唤起 Quantumult X，可复制下面的 URL Scheme 到 Safari 打开：

```text
quantumult-x:///add-resource?remote-resource=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FApple_Push.list
quantumult-x:///add-resource?remote-resource=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FDola.list
quantumult-x:///add-resource?remote-resource=https%3A%2F%2Fraw.githubusercontent.com%2FUSCZ%2FRule%2Fmain%2FquanX%2FHK_Finance.list
```

### 客户端配置方法

| 客户端 | 适配状态 | 配置方法 | 注意事项 |
| --- | --- | --- | --- |
| Quantumult X | 原生支持 | 使用上方一键导入，或把 Raw 地址加入 `[filter_remote]` | `Dola.list` 和 `HK_Finance.list` 已内置混合策略，不要再加 `force-policy`。 |
| Loon | 规则语义基本兼容 | 在 `[Remote Rule]` 中添加 Raw 地址；若当前版本不识别 `HOST-*`，把 `HOST-SUFFIX` / `HOST-KEYWORD` 改为 `DOMAIN-SUFFIX` / `DOMAIN-KEYWORD` 后使用 | 对混合策略文件不要套统一 policy，否则 `REJECT` / `DIRECT` 会被覆盖。 |
| Shadowrocket | 规则语义基本兼容 | 在配置的 `[Rule]` 中引用 Raw 地址，或从 App 的规则 URL 导入入口添加 | 对 `Dola.list`、`HK_Finance.list` 保留文件内第三列策略；不要用单一代理策略包裹整份文件。 |
| V2Ray / Xray / v2rayN / v2rayNG | 需要转换 | 不能直接导入 QuanX/Surge 风格 list；按表中 Raw 地址取规则后转换为 `routing.rules`，分别映射到 `proxy`、`direct`、`block` outboundTag | `Apple_Push.list` 全部走 `proxy`，最容易转换；`Dola.list` 有 `REJECT`，`HK_Finance.list` 有 `DIRECT` / `proxy` 混合策略。 |
| Clash / Mihomo | 需要转换或拆分 | 推荐把规则转换为 `DOMAIN-SUFFIX` / `DOMAIN-KEYWORD` / `IP-CIDR` 后放入 `rules:`，或按策略拆成多个 `rule-providers` | `RULE-SET,xxx,proxy` 会给整份 provider 套统一策略，不适合直接套在 Dola/HK 混合策略文件上。 |

在 Quantumult X 配置的 `[filter_remote]` 中加入：

```ini
[filter_remote]
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Apple_Push.list, tag=Apple Push, update-interval=86400, opt-parser=false, enabled=true
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Dola.list, tag=Dola Cici AI, update-interval=86400, opt-parser=false, enabled=true
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/HK_Finance.list, tag=香港金融混合分流, update-interval=86400, opt-parser=false, enabled=true
```

Loon 可参考：

```ini
[Remote Rule]
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Apple_Push.list, tag=Apple Push, enabled=true
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Dola.list, tag=Dola Cici AI, enabled=true
https://raw.githubusercontent.com/USCZ/Rule/main/quanX/HK_Finance.list, tag=香港金融混合分流, enabled=true
```

Shadowrocket 可参考：

```ini
[Rule]
RULE-SET,https://raw.githubusercontent.com/USCZ/Rule/main/quanX/Apple_Push.list,proxy

# Dola.list / HK_Finance.list 是混合策略文件，优先使用 App 的规则 URL 导入入口，
# 或将规则内容展开到 [Rule]，保留每行末尾的 proxy / DIRECT / REJECT。
```

V2Ray / Xray 转换思路：

```json
{
  "routing": {
    "domainStrategy": "AsIs",
    "rules": [
      {
        "type": "field",
        "domain": ["domain:push.apple.com"],
        "outboundTag": "proxy"
      },
      {
        "type": "field",
        "ip": ["17.249.0.0/16", "17.252.0.0/16"],
        "outboundTag": "proxy"
      }
    ]
  }
}
```

Clash / Mihomo 可参考：

```yaml
rules:
  # Apple Push 全部走 proxy，可直接转换后放在前面。
  - DOMAIN-SUFFIX,push.apple.com,proxy
  - DOMAIN-SUFFIX,push-apple.com.akadns.net,proxy
  - DOMAIN-KEYWORD,apple.com.edgekey.net,proxy
  - IP-CIDR,17.249.0.0/16,proxy,no-resolve

  # Dola / HK_Finance 建议转换 HOST-* 为 DOMAIN-* 后展开到 rules，
  # 或先按 proxy / DIRECT / REJECT 拆成多个 provider。
```

## 使用建议

- `Apple_Push.list`：建议放在较前位置；规则内已写入 `proxy`，用于改善 Telegram、X / Twitter 等 App 的推送问题。
- `Dola.list`：规则内已包含 `proxy` 和 `REJECT`，不要使用 `force-policy` 覆盖；其中 `proxy` 建议对应新加坡节点或新加坡策略组。
- `HK_Finance.list`：建议放在银行/金融相关规则优先级较高的位置；规则内已有 `DIRECT` / `proxy` 混合策略，不要使用 `force-policy` 覆盖。
- 如遇 App 域名变更，可通过 Quantumult X 日志或 MitM/抓包补充规则。
