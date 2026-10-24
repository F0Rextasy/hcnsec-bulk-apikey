<div align="center">

```
██╗  ██╗ ██████╗███╗   ██╗███████╗███████╗ ██████╗
██║  ██║██╔════╝████╗  ██║██╔════╝██╔════╝██╔════╝
███████║██║     ██╔██╗ ██║███████╗█████╗  ██║
██╔══██║██║     ██║╚██╗██║╚════██║██╔══╝  ██║
██║  ██║╚██████╗██║ ╚████║███████║███████╗╚██████╗
╚═╝  ╚═╝ ╚═════╝╚═╝  ╚═══╝╚══════╝╚══════╝ ╚═════╝
```

# hcnsec-bulk-apikey

**批量铸造 HCNSec 账号——按面板自身政策，每次运行一个账号——并自动生成 API 密钥：兼容 OpenAI 的推理 + 9Router 连接器。**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-16%20served-412991?style=for-the-badge)](https://api.hcnsec.cn)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 快速开始](#-quick-start) · [🎁 免费额度](#-free-tier--how-much-you-get) · [🤖 模型](#-models--all-16-of-them) · [🧪 使用 API](#-how-to-use-the-api) · [✅ 已验证](#-verified-working--every-key-is-tested) · [🧭 工作原理](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ 选项](#️-cli-options) · [🛰️ 代理](#️-behind-a-proxy) · [🐞 问题反馈](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ 为什么有这个项目

HCNSec（新疆幻城网安科技公益大模型安全网关）是一个 New API 面板，拥有 **16 个模型 id**（2026-10-21 在一个有效密钥上实测），并且有一套异常完整的电子邮件政策：gmail / outlook / hotmail / 163 / qq / 126 / foxmail / icloud 都能通过其白名单。注册是电子邮件 + 验证码，背后有 Turnstile 把关；登录是普通密码；账号的令牌在默认分组上带有 `unlimited_quota`。

Turnstile 是关键关卡：它的令牌绑定到解决它的出口，因此本工具通过一个经过审查的出口驱动真实的 Chrome，并读取面板自身的组件——在其他任何地方解决的令牌都会返回 `Turnstile 校验失败`。一个实测的 Chrome 细节也是这条轨道的一部分：DevTools 端口必须非零（`--remote-debugging-port=0` 会把 `navigator.webdriver` 翻转为 true，组件随后以错误 `600010` 挂掉）；CLI 会自行选择一个空闲的非零端口。

> ⚠️ **运营者须知（实测，公告 #53，2026-09-30）：** 面板声明一个人不得运行多个账号，并表示已封禁关联账号组。把每个账号视为一个风险单元；本工具默认 **每次运行一个账号**。

## 🎁 免费额度——你能得到多少

| 项目 | 值 |
| --- | --- |
| 账号配额 | `/api/user/self` 上为 `999,999,999`（默认分组上显示为无限） |
| `quota_per_unit` | 500,000 |
| 注册 | 电子邮件 + 6 位验证码 + Turnstile（每步一次性令牌） |
| 电子邮件政策 | 接受 gmail、outlook、hotmail、**163、qq、126、foxmail**、icloud；拒绝 sina/aliyun/inboxbear（`EMAIL_ADDRESS_REJECTED`——inboxbear 于 2026-10-21 重新实测） |
| 签到 | `/api/user/checkin` 存在但受 Turnstile 把关；配额余量使其可有可无 |
| 多账号政策 | **公告 #53：多账号使用会被封禁**——见上方运营者须知 |

> 💡 免费额度由 HCNSec 设定，可能变化。在 [api.hcnsec.cn](https://api.hcnsec.cn) 查看实时数字——并让工具自身的关卡来判定：

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 模型——全部 16 个

`GET /v1/models` 在一个有效密钥上返回了 **16 个 id**（2026-10-21）：

`auto` · `DeepSeek-V4-Flash` · `DeepSeek-V4-Pro` · `DeepSeek-V4.1-Flash` · `glm-5.3` · `glm-5.3-flash` · `longcat-2.5` · `MiMo-V2.6-Flash` · `MiniCPM-V-4.7-35B-A3B` · `MiniMax-M3.1-Flash` · `Qwen3-Embedding-8B` · `Qwen3.8-27B` · `Qwen3.8-Flash-Next` · `sensenova-6.8-flash-lite` · `sensenova-u1.5-lite` · `step-5-preview`

一次实时补全在验证模型上返回了应答（`content: "ok"`）：

| 模型 ID | 状态 |
| --- | --- |
| `MiMo-V2.6-Flash` | **200**——本工具的默认聊天关卡 |
| `longcat-2.5` | **200** |
| `step-5-preview`、`Qwen3.8-Flash-Next`、`glm-5.3-flash`、`DeepSeek-V4-Flash` | **200**（更早的一次运行，同一代面板） |

```bash
curl https://api.hcnsec.cn/v1/chat/completions \
  -H "Authorization: Bearer Hjcop6…" -H "Content-Type: application/json" \
  -d '{"model":"MiMo-V2.6-Flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 如何使用 API

基础 URL **`https://api.hcnsec.cn/v1`**——兼容 OpenAI。密钥裸用和带 `sk-` 前缀都可用（两者在 `/v1/models` 上均实测 200）；文件中保存的是裸密钥形式。

```python
from openai import OpenAI
c = OpenAI(base_url="https://api.hcnsec.cn/v1", api_key="Hjcop6…")
print(c.chat.completions.create(model="MiMo-V2.6-Flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 快速开始

```bash
cd hcnsec-bulk-apikey
printf 'socks5://127.0.0.1:1080\n' > proxies.txt   # one exit per line — see the proxy note
node src/launcher.js

# or directly:
node src/cli.js --count 1        # one account per run, by policy
node src/cli.js --verify-only    # re-test every key already in keys.txt
node src/cli.js --screen         # test the exit list against the panel
```

注册在你的出口上驱动的真实 Chrome 中运行（Turnstile 令牌绑定到它）；`keys.txt` 仅在 `GET /v1/models` + 一次真实补全之后才写入，`accounts.json` 保存账号状态。

### 🖱️ 启动器

启动器是一条命令的路径：它询问你想要多少个密钥（回车 = `10`）并以 `--auto` 运行 CLI；`proxies.txt` 存在时会自动被采用，否则运行将直连。它还会重新检查每个密钥、筛查你的出口列表、显示记住的设置、将密钥连接到 9Router / OmniRoute——并且它会预先检查 Chrome，因此缺失浏览器会在运行开始前被报告。

### 🚀 一条命令

```bash
node src/launcher.js       # menu → "1) Mint keys" → how many keys? [10]
node src/cli.js --count 10 # the same run, non-interactive
```

这就是整个工具：`--auto` 默认开启——Turnstile 在内置浏览器轨道中解决（一个真实 Chrome 通过 `proxies.txt` 中一个固定的出口，若没有则 DIRECT），密钥只有在其自身的实时聊天返回 200 之后才会进入 `keys.txt`。`--dry` 打印解析后的计划——数量、出口、求解器、模型——然后退出，不触碰任何文件或网络：

```bash
node src/cli.js --count 10 --dry
```

> ⚠️ **条款与账号风险。** 自动化第三方注册是你的决定和你的责任，而非本仓库的。运行前请阅读 HCNSec 的条款并遵守它们：多账号使用在那里明确会被封禁（公告 #53），一个被注意到的账号群可能被整体封禁，电子邮件/验证码关卡可能随时变化。只铸造你实际使用的量。

## ✅ 已验证可用——每个密钥都经过测试

以下所有检查均于 **2026-10-21** 从随附的 CLI 重新运行（池出口：通过 `socks5h://127.0.0.1:40000` 的 WARP）。

| 检查 | 结果 |
| --- | --- |
| `--screen`（出口审查） | **✔ 1.3 秒**——`/api/status` 200 + `challenges.cloudflare.com/turnstile/v0/api.js` 200（含重定向） |
| 浏览器轨道（真实 Chrome） | 页内 Turnstile 令牌 **752 字符**，`navigator.webdriver:false`——并且面板 **接受** 了它：`/api/verification` 返回的是电子邮件域名政策（`EMAIL_ADDRESS_REJECTED`），而非 Turnstile 失败 |
| `GET /api/status` | **200**——注册开启、电子邮件验证开启、turnstile 开启、签到开启 |
| `POST /api/user/register` | **200** `{"message":"","success":true}`（电子邮件 + 验证码 + 全新令牌） |
| `POST /api/user/login` | **200**（访问令牌；普通密码） |
| `POST /api/token/` + reveal | **200**——默认分组上的 `unlimited_quota` |
| `GET /api/user/self` | **200** `quota=999999999` 分组 `default` |
| `GET /v1/models` | **200，16 个模型** |
| `--verify-only` ×2 密钥 | 通过池在 `MiMo-V2.6-Flash` 上 **2/2 chat-200**（`"ok"`） |
| flag-day 运行 | `keys.txt` 中的每个密钥，每一个都仅在其自身聊天 200 之后才写入 |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

> **质量关卡。** 这里的每个密钥在写入 `keys.txt` 之前都通过了实时 `chat-200`
> （死密钥绝不随附）。路由器组合在模型层面遵循同样的规则：一个条目
> **仅当该模型应答 `chat-200` 且能调用工具时** 才会被添加——不支持工具的模型、
> 死密钥或死提供方会被丢弃而非添加。随时重新运行验证步骤；
> 失败会被报告，绝不隐藏。

## 🧭 工作原理

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: HCNSec page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.hcnsec.cn/v1/chat/completions → real content = the key is live
```

| 步骤 | 调用 | 说明 |
| --- | --- | --- |
| 审查 | `GET /api/status` + Turnstile 脚本 | 无法加载 `challenges.cloudflare.com` 的出口会在其他一切之前被跳过 |
| 浏览器 | 通过出口在真实 Chrome 中打开面板 `/register` | 页内组件返回一个 700+ 字符的令牌；令牌绑定到该出口的 IP |
| 发送验证码 | `GET /api/verification?email=…&turnstile=<fresh>` | 白名单在令牌之后生效 |
| 注册 | `POST /api/user/register?turnstile=<fresh>` | 电子邮件 + 验证码 + 密码 |
| 登录 | `POST /api/user/login?turnstile=<fresh>` | 普通密码（加密关闭） |
| 密钥 | `POST /api/token/`（+ reveal 跳转） | 一次性写入磁盘 |
| 证明 | `GET /v1/models` + 一次真实补全 | 失败的密钥绝不写入 |

每次面板调用都携带自己的全新一次性令牌；被限流的发送会退避 65 秒，已被使用的地址会获得一个全新的邮箱。

## ⚙️ CLI 选项

```bash
node src/cli.js --count 1          # mint accounts (one per run, by policy)
node src/cli.js --verify-only      # re-verify every key in keys.txt
node src/cli.js --screen           # exit pool self-test (status + Turnstile gates)
node src/cli.js --connect-9router  # register the provider + a key on a local 9Router
node src/cli.js --help
```

| 标志 | 默认值 | 含义 |
| --- | --- | --- |
| `--count N` | `1` | 要铸造的账号数，串行 |
| `--model <id>` | `MiMo-V2.6-Flash` | 用于实时关卡的模型 |
| `--pool <file>` | `proxies.txt` | 出口池，每行一个代理 |
| `--exits N` | `6` | 每个账号审查的出口候选数 |
| `--gap MS` | `4000` | 账号之间的暂停 |
| `--code-timeout S` | `300` | 等待电子邮件验证码的秒数 |
| `--keys <file>` | `keys.txt` | 用于 `--verify-only` 的密钥文件 |
| `--debug-port N` | 空闲端口 | Chrome DevTools 端口——必须非零（见原因说明） |
| `--browser <exe>` | 自动 | Chrome 二进制路径 |
| `--auto` | `开启` | 在内置浏览器轨道中解决 Turnstile（默认） |
| `--dry` | `关闭` | 打印解析后的计划并退出——不写文件，不发请求 |

| 环境变量 | 默认值 | 含义 |
| --- | --- | --- |
| `HCN_PROXIES` | `proxies.txt` | 出口池，每行一个代理 |
| `HCN_MODEL` | `MiMo-V2.6-Flash` | 用于实时验证的模型 |
| `CHROME_PATH` | 自动 | Chrome 二进制路径 |

## 🛰️ 在代理之后

**强烈推荐。** 在网络允许的地方本工具可直连工作，但此面板的 Turnstile 需要健康的出口类别（住宅出口 / WARP 实测端到端可用；短命的免费出口在一分钟内就死了）。使用 `--pool` 时，每个请求——包括浏览器——都通过列表中的一个出口，故障关闭：每个候选先被审查（`/api/status` + 含重定向的 Turnstile 脚本），然后令牌在将使用它的出口上诞生。没有池时运行将直连，组件通常根本无法挂载。

## 📄 文件

| 路径 | 内容 |
| --- | --- |
| `keys.txt` | 实时验证的密钥——已被 git 忽略 |
| `accounts.json` | 账号状态，含失败尝试 |
| `proxies.txt` | 你的出口——已被 git 忽略 |
| `keys-dead.txt` | 停止应答 `--verify-only` 的密钥——已被 git 忽略 |
| `out/` | 运行日志、失败截图、`attempts.jsonl` 账本——已被 git 忽略 |

## 🔌 连接到 9Router

[9Router](https://9router.com) 是一个自托管、兼容 OpenAI 的路由器：

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

默认值：`--prefix hcn`、`--base-url https://api.hcnsec.cn/v1`、`--model MiMo-V2.6-Flash`。模型以 `hcn/<model id>` 形式提供。CLI 可以一条命令完成同样的事：`node src/cli.js --connect-9router`。

## 🔌 连接到 OmniRoute

[OmniRoute](https://omniroute.online) 是一个自托管、兼容 OpenAI 的路由器：

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

默认值：`--prefix hcn`、`--base-url https://api.hcnsec.cn/v1`、`--model MiMo-V2.6-Flash`。模型以 `hcn/<model id>` 形式提供。CLI 可以一条命令完成同样的事：`node src/cli.js --connect-omniroute`。

## 🔑 一个密钥能给你什么

| | |
| --- | --- |
| 🏢 提供方 | **HCNSec**——[api.hcnsec.cn](https://api.hcnsec.cn) |
| 🎁 提供方给予什么 | 一个在默认分组上显示 `unlimited_quota` 的账号，16 个模型 |
| 🔑 一个密钥能给你什么 | 在其自己账号上的一个密钥：`https://api.hcnsec.cn/v1` 上已验证的 id |
| 🌐 API 形态 | 兼容 OpenAI：`POST https://api.hcnsec.cn/v1/chat/completions` |
| 🔒 你的数据 | 仅联系 `api.hcnsec.cn` 和你的收件箱后端 |

## 🐞 问题反馈

如果 HCNSec 更改其注册关卡、Turnstile sitekey 或电子邮件白名单，本工具会停在那里并说明原因。**[提交 issue](../../issues)**，附上命令和确切输出。

## 📄 许可证

MIT © 2026——见 [LICENSE](LICENSE)。

<div align="center">

**[MIT](LICENSE)** © 2026 hcnsec-bulk-apikey 贡献者

</div>