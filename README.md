# 轩哥 / Nexus

**CS student @ 武汉科技大学 · 汽车安全 CTF（UDS / CAN / V2X / ECU 固件）+ AI agent / 供应链安全工具链**

I write **zero-dependency, deterministic security tools** — the kind you can drop
into a scratch CI container with no network and no toolchain. Every finding
carries the evidence (`file:line`, byte offset, packet number) instead of a
vibe, because a scanner you cannot verify is a scanner you will switch off.

> 中文优先，代码注释与文档都是「能复核」优先；所有工具都在 Windows 主力机上实测过。

---

## AI agent security（当前主线 · 2026 最大热点）

| Repo | What it is | Why it exists |
|---|---|---|
| [**civet**](https://github.com/xuange-hu/civet) | GitHub Actions 工作流审计器 · **36 规则 · 零依赖** | zizmor 6.7k★ 是 Rust、actionlint 4.3k★ 是 Go；零依赖 Python 赛道空着 |
| [**mcplock**](https://github.com/xuange-hu/mcplock) | MCP 服务器**能力锁文件** + 漂移检测 | MCP 工具集会静默变化；官方 inspector 11k★ 但是 GUI，CI 里跑不了 |
| [**skillvet**](https://github.com/xuange-hu/skillvet) | Agent skill / MCP 配置的静态安全审查 | 确定性规则，不调 LLM；含 Trojan Source 检测 + SARIF |
| [**agent-scan**](https://github.com/xuange-hu/agent-scan) | JS/Node 版 agent 生态扫描器（MCP / Skills / AGENTS.md） | 30+ 规则，零依赖 |

## Supply chain & infrastructure

| Repo | What it is | Why it exists |
|---|---|---|
| [**kubeguard**](https://github.com/xuange-hu/kubeguard) | Kubernetes 清单审计 · **37 规则 · 跨资源分析** | kube-linter 3.5k★ 是 Go 且只看单对象；K8s 默认全通，风险在对象之间 |
| [**layerlint**](https://github.com/xuange-hu/layerlint) | Dockerfile 审计 · **唯一建模 stage→layer** 的那个 | hadolint 12.5k★ 只看指令清单，`RUN rm -rf` 删不掉前一层 |
| [**docklint**](https://github.com/xuange-hu/docklint) | 容器构建审计 · **三遍扫描：Dockerfile + compose + .dockerignore** · 38 规则 | layerlint 看的是层叠上去的东西（`RUN rm` 删不掉前一层）；docklint 看的是三个文件之间的缝隙：被 `.dockerignore` 排掉的 COPY、compose 用 `user: root` 撤销的降权、烤进 layer 又被挂载盖住的密钥。在 docker/awesome-compose 的 74 个文件上校准，因此删过自己的规则 |
| [**pcaplens**](https://github.com/xuange-hu/pcaplens) | pcap / pcapng 解析 + CLI + MCP | 不用 tshark；上下文预算一等公民，每条结论带报文号与字节偏移 |
| [**sbomkit**](https://github.com/xuange-hu/sbomkit) | 供应链体检 · SBOM + **SPDX 许可证表达式求解** + PR 门禁 diff | 许可证是表达式不是字符串：`(MIT OR GPL-3.0-only)` 可以选 MIT，子串匹配的工具会误报 |
| [**secretsweep**](https://github.com/xuange-hu/secretsweep) | 凭据泄露扫描 · **86 规则 · 占位符分级 · 基线门禁 · GitHub Action** | gitleaks 30k★ / trufflehog 28k★ 都是 Go，Python 侧 detect-secrets 带依赖；示例密钥不等于真泄露；Action 一行接入、不装任何依赖 |
| [**tfguard**](https://github.com/xuange-hu/tfguard) | Terraform / OpenTofu 审计 · **40 规则 · 自写 HCL2 解析器 · 凭据溯源 · 跨块关系** | tfsec 7k★ / terrascan 5.2k★ 是 Go，checkov 9.1k★ 是 Python 但带一车依赖；零依赖赛道空着。`password = var.db_password` 的密钥其实在变量那一行 |

## Cloud identity security

| Repo | What it is | Why it exists |
|---|---|---|
| [**iamlint**](https://github.com/xuange-hu/iamlint) | AWS IAM 策略审计 · **49 规则 · 两遍扫描** · 自带 lab | parliament 1.2k★ / cloudsploit 3.6k★ 都是单条 statement 视角；真正丢账号的那条结论活在两条 statement **之间**（`iam:CreateAccessKey` + `iam:AttachUserPolicy`）。`--bundle` 跨文件合并成一个 principal |

## Transport & certificate security

| Repo | What it is | Why it exists |
|---|---|---|
| [**tlslint**](https://github.com/xuange-hu/tlslint) | TLS / X.509 审计 · **43 规则 · 自写 DER 解析器 · 本地实验室服务器** | testssl.sh 9.3k★ 是 shell、sslyze 3.8k★ 要 cryptography + nassl、sslscan 2.6k★ 是 C；零依赖 Python 赛道空着。每条结论带字段与值；`tlslint demo` 不联网跑出 37/43 条规则 |
| [**headervet**](https://github.com/xuange-hu/headervet) | HTTP 安全响应头审计 · **36 规则 · 三遍扫描 · 配置 + dump + live 三种输入** | securityheaders.com 只给一个响应打分；真正出事的两种情况它都看不见——`proxy_hide_header` 把应用设的头剥了（HDV203）、nginx location 里第一个 `add_header` 让继承的安全头全部失效（HDV201）。nginx / Apache / Caddy / Express 全读，零依赖，SARIF + MCP |

## API contract security

| Repo | What it is | Why it exists |
|---|---|---|
| [**specvet**](https://github.com/xuange-hu/specvet) | OpenAPI / Swagger **安全审查** · 36 规则 · 全部映射 OWASP API Top 10 2023 | Spectral 3.2k★ / Redocly / vacuum 管的是风格；第二遍扫描看的是关系：某个 path 的 GET 有认证而 DELETE 没有 |

## 输入层 / 解析层安全

| Repo | What it is | Why it exists |
|---|---|---|
| [**redoslint**](https://github.com/xuange-hu/redoslint) | ReDoS 审计 · **9 规则 · 自写正则解析器 · 字符集代数** · CLI + SARIF + 6 个 MCP 工具 | regexploit 是手工 REPL、recheck 要 JVM/Node、rxxr2 已停更、npm 的 `redos` 只读 JS；零依赖 Python 扫描器赛道空着。形状匹配会同时报 `(a+)+` 和线性的 `(ab+)+`，这里比的是**字符集能不能重叠** |

## Vehicle security（起点项目 · 1★）

| Repo | What it is |
|---|---|
| [**vehsec**](https://github.com/xuange-hu/vehsec) | CAN · UDS · Seed-Key · V2X · DoIP · J1939 · DBC → **CLI + TUI + 31 个 MCP 工具**，5 个 Docker CTF Lab，零硬件可跑 |
| [awesome-vehicle-security](https://github.com/xuange-hu/vehsec#awesome-vehicle-security精选清单) | 车载安全精选清单（在 vehsec 仓库内） |

## Other

| Repo | What it is |
|---|---|
| [**firmprint**](https://github.com/xuange-hu/firmprint) | Cortex-M 固件识别，证据式（带偏移与字节） |
| [**winproof**](https://github.com/xuange-hu/winproof) | Windows 只读取证（浏览器劫持 / 启动项），PowerShell 零依赖 |
| [**pickword-ime**](https://github.com/xuange-hu/pickword-ime) | 打字学外语 Android 输入法（基于同文 Trime） |
| [**screen-translator**](https://github.com/xuange-hu/screen-translator) | 截图 → OCR → LLM 翻译 |
| [**NexusAgent**](https://github.com/xuange-hu/NexusAgent) | 轻量 agent 框架：memory tools + 可插拔 LLM 适配 |
| [**vibe-debt**](https://github.com/xuange-hu/vibe-debt) | AI 代码债评分 CLI |
| [**bilibili-video-crawler**](https://github.com/xuange-hu/bilibili-video-crawler) | B 站爬虫（WBI 签名 + 限速重试） |

---

## 设计信条

1. **证据式输出** —— 每条结论必须给报文号 / 文件行号 / 原始字节。结论可复核，而不是被信任。
2. **关系比单点更值钱** —— 单看一个对象看不出的风险，第三遍扫描才看得出来：安全组对外开放，且公网实例正落在它里面。
3. **零依赖优先** —— 能在标准库里做的解析（DBC、X.509、pcap、YAML、**HCL2**、正则）就不引第三方包。
4. **确定性** —— 能写规则就不调 LLM；同样的输入必须给同样的结论。
5. **CI 一等公民** —— 每个工具都有 `--fail-on` 退出码 + SARIF，进 GitHub Code Scanning。

## Contact

- GitHub: [@xuange-hu](https://github.com/xuange-hu)
- 方向：汽车安全 CTF（UDS/CAN/V2X/ECU 固件）、AI agent 安全、供应链安全
- 装备：Kali + 水星 MW150US V4.0（RTL8188CUS，可监听抓包、不支持注入）

<sub>最后更新：2026-11-10 · 25 repositories</sub>
