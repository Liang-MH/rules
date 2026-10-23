# 分流规则

本仓库提供 Clash / Mihomo 规则集，具体出口由客户端的策略组选择决定。

## Meta AI 分流调整（2026-10-23）

在 `AiDomain.list`（美国 AI 分流）新增 `#meta.ai` 段，域名复用以下已有配置（固定提交链接）：

| 来源 | 核实结果 |
| --- | --- |
| [v2fly/domain-list-community：data/category-ai-!cn](https://github.com/v2fly/domain-list-community/blob/597956ad5d1c063d80329d58a6aaadf6dcec43a7/data/category-ai-!cn) | `# Meta` 段列出 `meta.ai`、`metaaivm.com`、`muse.ai`。 |
| [dler-io/Rules：Clash/Provider/AI Suite.yaml](https://github.com/dler-io/Rules/blob/3ccf5720a78e34a71cf3b4f3abb8102e26e42aed/Clash/Provider/AI%20Suite.yaml) | `Meta AI / Muse` 段为 `DOMAIN-SUFFIX,meta.ai` 与 `DOMAIN-SUFFIX,muse.ai`。 |

本次采用两者并集，三条均写为 `DOMAIN-SUFFIX`。v2fly `data/meta` 中的 `meta.com`、`facebook` 等 Meta 全站域名未加入 AI 分流，避免把整个 Meta 生态划进 AI 出口。客户端刷新 AiDomain 规则集即可生效。

## PyPI 分流调整（2026-10-14）

### GitHub 来源核对

修改前查阅了以下已有配置，并用固定提交链接记录依据：

| 来源 | 核实结果 |
| --- | --- |
| [v2fly/domain-list-community：data/python](https://github.com/v2fly/domain-list-community/blob/d721e2be316bd1ab692b40246efbdcf863242a72/data/python) | Python 域名分类同时包含 `pypi.org` 和 `pythonhosted.org`。该项目提供域名分类，本身不规定直连或代理。 |
| [blackmatrix7/ios_rule_script：Clash/Python/Python.list](https://github.com/blackmatrix7/ios_rule_script/blob/0fa60782abfe70f58da510e90d9086136cf0d855/rule/Clash/Python/Python.list) | 已有 `DOMAIN-SUFFIX,pypi.org` 与 `DOMAIN-SUFFIX,pythonhosted.org`，可核对 Clash 规则写法。 |

两份配置均还包含 `pycon.org`、`pypa.io`、`pypi.io`、`python.org`。本次只采用与当前 PyPI 索引和包下载有关的两个域名，避免扩大到整个 Python 站点分类。

### 修改内容与边界

- 在 `Developer.list` 添加 `DOMAIN-SUFFIX,pypi.org`，覆盖包索引请求。
- 将已有 `DOMAIN-SUFFIX,files.pythonhosted.org` 扩为 `DOMAIN-SUFFIX,pythonhosted.org`，保留对包下载子域名的覆盖，并与上述已有规则一致。
- 两类请求均可命中现有 `RULE-SET,developer,👨‍💻 Developer`。实际路由仍取决于客户端模式、前置规则和策略组当前选择。
- 不修改代理组、节点、DNS、TUN 或 IPv6；“美国手动”的直连美国用途保持不变。
- 这项修改补齐分流覆盖，不代表已经验证某个节点或网络路径可正常访问 PyPI。

### 使用本仓库更新

GitHub 已将本仓库从 `KARENLHAO/rules` 迁移到 `Liang-MH/rules`；旧 Git 远端会重定向到新仓库。推送时已确认该迁移提示。客户端已有的以下 `rule-providers.developer.url` 无需更换：

```text
https://raw.githubusercontent.com/Liang-MH/rules/main/Developer.list
```

保留该 provider 的 `type: http`、`behavior: classical`、`format: text`、现有 `path` 和刷新间隔，以及已有的 Developer 路由规则，然后刷新该规则集。无需修改“美国手动”或其他策略组。

刷新后，可在客户端连接记录中核对 `pypi.org`、`files.pythonhosted.org` 的命中规则及出口，再重试 pip 安装。
