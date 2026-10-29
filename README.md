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
| [**pcaplens**](https://github.com/xuange-hu/pcaplens) | pcap / pcapng 解析 + CLI + MCP | 不用 tshark；上下文预算一等公民，每条结论带报文号与字节偏移 |
| [**sbomkit**](https://github.com/xuange-hu/sbomkit) | 供应链体检 · SBOM + **SPDX 许可证表达式求解** + PR 门禁 diff | 许可证是表达式不是字符串：`(MIT OR GPL-3.0-only)` 可以选 MIT，子串匹配的工具会误报 |

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
2. **零依赖优先** —— 能在标准库里做的解析（DBC、X.509、pcap、YAML）就不引第三方包。
3. **确定性** —— 能写规则就不调 LLM；同样的输入必须给同样的结论。
4. **CI 一等公民** —— 每个工具都有 `--fail-on` 退出码 + SARIF，进 GitHub Code Scanning。

## Contact

- GitHub: [@xuange-hu](https://github.com/xuange-hu)
- 方向：汽车安全 CTF（UDS/CAN/V2X/ECU 固件）、AI agent 安全、供应链安全
- 装备：Kali + 水星 MW150US V4.0（RTL8188CUS，可监听抓包、不支持注入）

<sub>最后更新：2026-10-29 · 14 repositories</sub>
