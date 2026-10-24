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

**Mint HCNSec accounts — one per run, by the panel's own policy — with auto-generated API keys: OpenAI-compatible inference + 9Router connector.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-16%20served-412991?style=for-the-badge)](https://api.hcnsec.cn)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Quick start](#-quick-start) · [🎁 Free tier](#-free-tier--how-much-you-get) · [🤖 Models](#-models--all-16-of-them) · [🧪 Use the API](#-how-to-use-the-api) · [✅ Verified](#-verified-working--every-key-is-tested) · [🧭 How it works](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Options](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Issues](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Why this exists

HCNSec (新疆幻城网安科技公益大模型安全网关) is a New API panel with **16 model ids** (measured 2026-10-21 on a live key) and an unusually complete e-mail policy: gmail / outlook / hotmail / 163 / qq / 126 / foxmail / icloud all pass its whitelist. Registration is e-mail + code behind a Turnstile, login is plain password, and the account's token carries `unlimited_quota` on the default group.

The Turnstile is the load-bearing gate: its token is bound to the exit that solved it, so the tool drives a real Chrome over one vetted exit and reads the panel's own widget — a token solved anywhere else answers `Turnstile 校验失败`. One measured Chrome detail is part of the rail: the DevTools port must be nonzero (`--remote-debugging-port=0` flips `navigator.webdriver` to true and the widget then dies with error `600010`); the CLI picks a free nonzero port itself.

> ⚠️ **Operator note (measured, announcement #53, 2026-09-30):** the panel states one person may not run multiple accounts and says it has banned linked account sets. Treat every account as a risk unit; this tool defaults to **one account per run**.

## 🎁 Free tier — how much you get

| What | Value |
| --- | --- |
| Account quota | `999,999,999` on `/api/user/self` (unlimited view on the default group) |
| `quota_per_unit` | 500,000 |
| Signup | e-mail + 6-char code + Turnstile (single-use token per step) |
| E-mail policy | gmail, outlook, hotmail, **163, qq, 126, foxmail**, icloud accepted; sina/aliyun/inboxbear rejected (`EMAIL_ADDRESS_REJECTED` — inboxbear re-measured 2026-10-21) |
| Check-in | `/api/user/checkin` exists but is Turnstile-gated; the quota headroom makes it optional |
| Multi-account policy | **announcement #53: multi-account use is bannable** — see the operator note above |

> 💡 The free tier is set by HCNSec and can change. Check the live numbers at [api.hcnsec.cn](https://api.hcnsec.cn) — and let the tool's own gate decide:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Models — all 16 of them

`GET /v1/models` returned **16 ids** on a live key (2026-10-21):

`auto` · `DeepSeek-V4-Flash` · `DeepSeek-V4-Pro` · `DeepSeek-V4.1-Flash` · `glm-5.3` · `glm-5.3-flash` · `longcat-2.5` · `MiMo-V2.6-Flash` · `MiniCPM-V-4.7-35B-A3B` · `MiniMax-M3.1-Flash` · `Qwen3-Embedding-8B` · `Qwen3.8-27B` · `Qwen3.8-Flash-Next` · `sensenova-6.8-flash-lite` · `sensenova-u1.5-lite` · `step-5-preview`

A live completion answered on the verification models (`content: "ok"`):

| Model ID | Status |
| --- | --- |
| `MiMo-V2.6-Flash` | **200** — the tool's default chat gate |
| `longcat-2.5` | **200** |
| `step-5-preview`, `Qwen3.8-Flash-Next`, `glm-5.3-flash`, `DeepSeek-V4-Flash` | **200** (earlier run, same panel generation) |

```bash
curl https://api.hcnsec.cn/v1/chat/completions \
  -H "Authorization: Bearer Hjcop6…" -H "Content-Type: application/json" \
  -d '{"model":"MiMo-V2.6-Flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 How to use the API

Base URL **`https://api.hcnsec.cn/v1`** — OpenAI-compatible. Keys work both bare and with an `sk-` prefix (both tested 200 on `/v1/models`); the file carries the bare reveal form.

```python
from openai import OpenAI
c = OpenAI(base_url="https://api.hcnsec.cn/v1", api_key="Hjcop6…")
print(c.chat.completions.create(model="MiMo-V2.6-Flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 Quick start

```bash
cd hcnsec-bulk-apikey
printf 'socks5://127.0.0.1:1080\n' > proxies.txt   # one exit per line — see the proxy note
node src/launcher.js
# or directly:
node src/cli.js --count 1        # one account per run, by policy
node src/cli.js --verify-only    # re-test every key already in keys.txt
node src/cli.js --screen         # test the exit list against the panel
```

The signup runs in a real Chrome driven over your exit (the Turnstile token is bound to it); `keys.txt` is written only after `GET /v1/models` + a real completion, and `accounts.json` keeps the account state.

### 🖱️ The launcher

The launcher is the one-command path: it asks how many keys you want (Enter = `10`) and runs the CLI with `--auto`; `proxies.txt` is picked up automatically when it exists, otherwise the run goes direct. It also re-checks every key, screens your exit list, shows the remembered settings, connects keys to 9Router / OmniRoute — and it checks for Chrome up front, so a missing browser is reported before a run starts.

### 🚀 One command

```bash
node src/launcher.js       # menu → "1) Mint keys" → how many keys? [10]
node src/cli.js --count 10 # the same run, non-interactive
```

That is the whole tool: `--auto` is on by default — the Turnstile is solved in the built-in browser rail (a real Chrome over one pinned exit from `proxies.txt`, or DIRECT when there is none) and a key reaches `keys.txt` only after its own live chat 200. `--dry` prints the resolved plan — count, exits, solver, model — and exits without touching a file or the network:

```bash
node src/cli.js --count 10 --dry
```

> ⚠️ **Terms & account risk.** Automating a third-party sign-up is your call and your responsibility, not this repository's. Read HCNSec's terms before you run this and respect them: multi-account use is explicitly bannable there (announcement #53), a fleet that gets noticed can be banned whole, and the e-mail/captcha gates can change without notice. Mint only what you actually use.

## ✅ Verified working — every key is tested

All checks below were re-run on **2026-10-21** from the shipped CLI (pool exit: WARP over `socks5h://127.0.0.1:40000`).

| Check | Result |
| --- | --- |
| `--screen` (exit vet) | **✔ 1.3 s** — `/api/status` 200 + `challenges.cloudflare.com/turnstile/v0/api.js` 200 with redirects |
| browser rail (real Chrome) | in-page Turnstile token **752 chars**, `navigator.webdriver:false` — and the panel **accepted** it: `/api/verification` answered the e-mail-domain policy (`EMAIL_ADDRESS_REJECTED`), not a Turnstile failure |
| `GET /api/status` | **200** — register on, e-mail verification on, turnstile on, checkin on |
| `POST /api/user/register` | **200** `{"message":"","success":true}` (e-mail + code + fresh token) |
| `POST /api/user/login` | **200** (access token; plain password) |
| `POST /api/token/` + reveal | **200** — `unlimited_quota` on the default group |
| `GET /api/user/self` | **200** `quota=999999999` group `default` |
| `GET /v1/models` | **200, 16 models** |
| `--verify-only` ×2 keys | **2/2 chat-200** on `MiMo-V2.6-Flash` (`"ok"`) through the pool |
| flag-day run | every key in `keys.txt`, every one written only after its own chat 200 |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

> **Quality gates.** Every key here passed a live `chat-200` before it was written to `keys.txt`
> (dead keys are never shipped). Router combos follow the same rule at the model level: an entry is
> added **only if the model answers `chat-200` and can call tools** — a model without tool support,
> a dead key or a dead provider is dropped instead of added. Re-run the verify step any time;
> failures are reported, never hidden.

## 🧭 How it works

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: HCNSec page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.hcnsec.cn/v1/chat/completions → real content = the key is live
```

| Step | Call | Notes |
| --- | --- | --- |
| vet | `GET /api/status` + the Turnstile script | an exit that cannot load `challenges.cloudflare.com` is skipped before anything else |
| browser | panel `/register` in real Chrome over the exit | the in-page widget answers with a 700-char+ token; the token is bound to that exit's IP |
| send code | `GET /api/verification?email=…&turnstile=<fresh>` | whitelist applies after the token |
| register | `POST /api/user/register?turnstile=<fresh>` | e-mail + code + password |
| login | `POST /api/user/login?turnstile=<fresh>` | plain password (encryption off) |
| key | `POST /api/token/` (+ reveal hop) | written to disk once |
| proof | `GET /v1/models` + a real completion | a key that fails is never written |

Every panel call carries its own fresh single-use token; a throttled send backs off 65 s, and an address already in use gets a brand-new mailbox.

## ⚙️ CLI options

```bash
node src/cli.js --count 1          # mint accounts (one per run, by policy)
node src/cli.js --verify-only      # re-verify every key in keys.txt
node src/cli.js --screen           # exit pool self-test (status + Turnstile gates)
node src/cli.js --connect-9router  # register the provider + a key on a local 9Router
node src/cli.js --help
```

| Flag | Default | Meaning |
| --- | --- | --- |
| `--count N` | `1` | accounts to mint, serial |
| `--model <id>` | `MiMo-V2.6-Flash` | model used for the live gate |
| `--pool <file>` | `proxies.txt` | exit pool, one line per proxy |
| `--exits N` | `6` | exit candidates vetted per account |
| `--gap MS` | `4000` | pause between accounts |
| `--code-timeout S` | `300` | seconds to wait for the e-mail code |
| `--keys <file>` | `keys.txt` | key file for `--verify-only` |
| `--debug-port N` | free port | Chrome DevTools port — must be nonzero (see the why note) |
| `--browser <exe>` | auto | Chrome binary path |
| `--auto` | `on` | solve the Turnstile in the built-in browser rail (default) |
| `--dry` | `off` | print the resolved plan and exit — no file writes, no requests |

| Env | Default | Meaning |
| --- | --- | --- |
| `HCN_PROXIES` | `proxies.txt` | exit pool, one line per proxy |
| `HCN_MODEL` | `MiMo-V2.6-Flash` | model for the live verification |
| `CHROME_PATH` | auto | Chrome binary path |

## 🛰️ Behind a proxy

**Strongly recommended.** The tool works direct where the network allows, but this panel's Turnstile needs a healthy exit class (a residential exit / WARP was measured working end-to-end; short-lived free exits died inside a minute). With `--pool` every request — the browser included — goes through an exit from the list, fail-closed: each candidate is vetted first (`/api/status` + the Turnstile script with redirects) and the token is then born on the exit that will use it. Without a pool the run goes direct and the widget usually never mounts.

## 📄 Files

| Path | Contents |
| --- | --- |
| `keys.txt` | live-verified keys — git-ignored |
| `accounts.json` | account state incl. failed attempts |
| `proxies.txt` | your exits — git-ignored |
| `keys-dead.txt` | keys that stopped answering `--verify-only` — git-ignored |
| `out/` | run logs, failure shots, `attempts.jsonl` ledger — git-ignored |

## 🔌 Connect to 9Router

[9Router](https://9router.com) is a self-hosted, OpenAI-compatible router:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Defaults: `--prefix hcn`, `--base-url https://api.hcnsec.cn/v1`, `--model MiMo-V2.6-Flash`. Models are served as `hcn/<model id>`. The CLI can do the same in one shot: `node src/cli.js --connect-9router`.

## 🔌 Connect to OmniRoute

[OmniRoute](https://omniroute.online) is a self-hosted, OpenAI-compatible router:

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

Defaults: `--prefix hcn`, `--base-url https://api.hcnsec.cn/v1`, `--model MiMo-V2.6-Flash`. Models are served as `hcn/<model id>`. The CLI can do the same in one shot: `node src/cli.js --connect-omniroute`.

## 🔑 What one key gets you

| | |
| --- | --- |
| 🏢 Provider | **HCNSec** — [api.hcnsec.cn](https://api.hcnsec.cn) |
| 🎁 What the provider gives | an account with `unlimited_quota` view on the default group, 16 models |
| 🔑 What one key gets you | one key on its own account: the verified ids on `https://api.hcnsec.cn/v1` |
| 🌐 API shape | OpenAI-compatible: `POST https://api.hcnsec.cn/v1/chat/completions` |
| 🔒 Your data | only `api.hcnsec.cn` and your inbox backend are contacted |

## 🐞 Issues

If HCNSec changes its register gates, the Turnstile sitekey or the e-mail whitelist, the tool stops there and says so. **[Open an issue](../../issues)** with the command and the exact output.

## 📄 License

MIT © 2026 — see [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 hcnsec-bulk-apikey contributors

</div>
