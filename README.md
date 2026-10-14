# 分流规则

本仓库提供 Clash / Mihomo 规则集，具体出口由客户端的策略组选择决定。

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

若客户端仍引用 `Liang-MH/rules/main/Developer.list`，本仓库的更新不会自动应用。仅将已有 `rule-providers.developer.url` 改为：

```text
https://raw.githubusercontent.com/KARENLHAO/rules/main/Developer.list
```

保留该 provider 的 `type: http`、`behavior: classical`、`format: text`、现有 `path` 和刷新间隔，以及已有的 Developer 路由规则，然后刷新该规则集。无需修改“美国手动”或其他策略组。

刷新后，可在客户端连接记录中核对 `pypi.org`、`files.pythonhosted.org` 的命中规则及出口，再重试 pip 安装。
