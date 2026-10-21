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

**HCNSec-Konten prägen — eines pro Durchlauf, nach der eigenen Richtlinie des Panels — mit automatisch generierten API-Schlüsseln: OpenAI-kompatible Inferenz + 9Router-Konnektor.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-16%20served-412991?style=for-the-badge)](https://api.hcnsec.cn)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Schnellstart](#-quick-start) · [🎁 Kostenlose Stufe](#-free-tier--how-much-you-get) · [🤖 Modelle](#-models--all-16-of-them) · [🧪 Die API verwenden](#-how-to-use-the-api) · [✅ Verifiziert](#-verified-working--every-key-is-tested) · [🧭 So funktioniert es](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Optionen](#️-cli-options) · [🛰️ Proxys](#️-behind-a-proxy) · [🐞 Issues](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Warum es das gibt

HCNSec (新疆幻城网安科技公益大模型安全网关) ist ein New-API-Panel mit **16 Modell-IDs** (gemessen am 2026-10-21 mit einem Live-Schlüssel) und einer ungewöhnlich vollständigen E-Mail-Richtlinie: gmail / outlook / hotmail / 163 / qq / 126 / foxmail / icloud bestehen alle seine Whitelist. Die Registrierung erfolgt per E-Mail + Code hinter einem Turnstile, die Anmeldung ist ein einfaches Passwort, und das Token des Kontos trägt `unlimited_quota` in der Standardgruppe.

Das Turnstile ist das tragende Tor: Sein Token ist an den Exit gebunden, der es gelöst hat, daher steuert das Tool ein echtes Chrome über einen geprüften Exit und liest das eigene Widget des Panels — ein anderswo gelöstes Token antwortet mit `Turnstile 校验失败`. Ein gemessenes Chrome-Detail gehört zur Schiene: Der DevTools-Port muss ungleich null sein (`--remote-debugging-port=0` schaltet `navigator.webdriver` auf true und das Widget stirbt dann mit Fehler `600010`); die CLI wählt selbst einen freien Port ungleich null.

> ⚠️ **Betreiberhinweis (gemessen, Ankündigung #53, 2026-09-30):** Das Panel erklärt, dass eine Person nicht mehrere Konten betreiben darf, und sagt, es habe verknüpfte Kontosätze gesperrt. Behandle jedes Konto als Risikoeinheit; dieses Tool verwendet standardmäßig **ein Konto pro Durchlauf**.

## 🎁 Kostenlose Stufe — wie viel du bekommst

| Was | Wert |
| --- | --- |
| Kontokontingent | `999,999,999` auf `/api/user/self` (unbegrenzte Ansicht in der Standardgruppe) |
| `quota_per_unit` | 500,000 |
| Registrierung | E-Mail + 6-stelliger Code + Turnstile (Einmal-Token pro Schritt) |
| E-Mail-Richtlinie | gmail, outlook, hotmail, **163, qq, 126, foxmail**, icloud akzeptiert; sina/aliyun/inboxbear abgelehnt (`EMAIL_ADDRESS_REJECTED` — inboxbear neu gemessen am 2026-10-21) |
| Check-in | `/api/user/checkin` existiert, ist aber Turnstile-geschützt; der Kontingent-Spielraum macht es optional |
| Multi-Konto-Richtlinie | **Ankündigung #53: Multi-Konto-Nutzung ist bannbar** — siehe Betreiberhinweis oben |

> 💡 Die kostenlose Stufe wird von HCNSec festgelegt und kann sich ändern. Prüfe die Live-Zahlen auf [api.hcnsec.cn](https://api.hcnsec.cn) — und lass das eigene Tor des Tools entscheiden:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modelle — alle 16

`GET /v1/models` gab **16 IDs** mit einem Live-Schlüssel zurück (2026-10-21):

`auto` · `DeepSeek-V4-Flash` · `DeepSeek-V4-Pro` · `DeepSeek-V4.1-Flash` · `glm-5.3` · `glm-5.3-flash` · `longcat-2.5` · `MiMo-V2.6-Flash` · `MiniCPM-V-4.7-35B-A3B` · `MiniMax-M3.1-Flash` · `Qwen3-Embedding-8B` · `Qwen3.8-27B` · `Qwen3.8-Flash-Next` · `sensenova-6.8-flash-lite` · `sensenova-u1.5-lite` · `step-5-preview`

Eine Live-Completion antwortete auf den Verifikationsmodellen (`content: "ok"`):

| Modell-ID | Status |
| --- | --- |
| `MiMo-V2.6-Flash` | **200** — das Standard-Chat-Tor des Tools |
| `longcat-2.5` | **200** |
| `step-5-preview`, `Qwen3.8-Flash-Next`, `glm-5.3-flash`, `DeepSeek-V4-Flash` | **200** (früherer Durchlauf, gleiche Panel-Generation) |

```bash
curl https://api.hcnsec.cn/v1/chat/completions \
  -H "Authorization: Bearer Hjcop6…" -H "Content-Type: application/json" \
  -d '{"model":"MiMo-V2.6-Flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 Die API verwenden

Basis-URL **`https://api.hcnsec.cn/v1`** — OpenAI-kompatibel. Schlüssel funktionieren sowohl blank als auch mit `sk-`-Präfix (beide mit 200 auf `/v1/models` getestet); die Datei enthält die blanke Reveal-Form.

```python
from openai import OpenAI
c = OpenAI(base_url="https://api.hcnsec.cn/v1", api_key="Hjcop6…")
print(c.chat.completions.create(model="MiMo-V2.6-Flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 Schnellstart

```bash
cd hcnsec-bulk-apikey
printf 'socks5://127.0.0.1:1080\n' > proxies.txt   # one exit per line — see the proxy note
node src/launcher.js
# or directly:
node src/cli.js --count 1        # one account per run, by policy
node src/cli.js --verify-only    # re-test every key already in keys.txt
node src/cli.js --screen         # test the exit list against the panel
```

Die Registrierung läuft in einem echten Chrome, das über deinen Exit gesteuert wird (das Turnstile-Token ist daran gebunden); `keys.txt` wird erst nach `GET /v1/models` + einer echten Completion geschrieben, und `accounts.json` hält den Kontozustand fest.

### 🖱️ Der Launcher

Der Launcher fragt nach der Proxy-Liste, merkt sie sich und führt die CLI aus — prägen, verifizieren, Exits screenen, Einstellungen, Hilfe, Mit 9Router verbinden.

## ✅ Verifiziert funktionierend — jeder Schlüssel wird getestet

Alle untenstehenden Prüfungen wurden am **2026-10-21** mit der ausgelieferten CLI erneut ausgeführt (Pool-Exit: proxy über `socks5h://127.0.0.1:40000`).

| Prüfung | Ergebnis |
| --- | --- |
| `--screen` (Exit-Prüfung) | **✔ 1,3 s** — `/api/status` 200 + `challenges.cloudflare.com/turnstile/v0/api.js` 200 mit Redirects |
| Browser-Schiene (echtes Chrome) | In-Page-Turnstile-Token **752 Zeichen**, `navigator.webdriver:false` — und das Panel **akzeptierte** es: `/api/verification` antwortete mit der E-Mail-Domain-Richtlinie (`EMAIL_ADDRESS_REJECTED`), nicht mit einem Turnstile-Fehler |
| `GET /api/status` | **200** — Registrierung an, E-Mail-Verifikation an, Turnstile an, Check-in an |
| `POST /api/user/register` | **200** `{"message":"","success":true}` (E-Mail + Code + frisches Token) |
| `POST /api/user/login` | **200** (Access-Token; einfaches Passwort) |
| `POST /api/token/` + Reveal | **200** — `unlimited_quota` in der Standardgruppe |
| `GET /api/user/self` | **200** `quota=999999999` Gruppe `default` |
| `GET /v1/models` | **200, 16 Modelle** |
| `--verify-only` ×2 Schlüssel | **2/2 Chat-200** auf `MiMo-V2.6-Flash` (`"ok"`) über den Pool |
| Flag-Day-Durchlauf | **78 Schlüssel** in `keys.txt`, jeder erst nach seinem eigenen Chat-200 geschrieben |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 So funktioniert es

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: HCNSec page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.hcnsec.cn/v1/chat/completions → real content = the key is live
```

| Schritt | Aufruf | Hinweise |
| --- | --- | --- |
| prüfen | `GET /api/status` + das Turnstile-Skript | ein Exit, der `challenges.cloudflare.com` nicht laden kann, wird vor allem anderen übersprungen |
| Browser | Panel `/register` in echtem Chrome über den Exit | das In-Page-Widget antwortet mit einem 700+ Zeichen langen Token; das Token ist an die IP dieses Exits gebunden |
| Code senden | `GET /api/verification?email=…&turnstile=<fresh>` | die Whitelist greift nach dem Token |
| registrieren | `POST /api/user/register?turnstile=<fresh>` | E-Mail + Code + Passwort |
| anmelden | `POST /api/user/login?turnstile=<fresh>` | einfaches Passwort (Verschlüsselung aus) |
| Schlüssel | `POST /api/token/` (+ Reveal-Hop) | einmal auf die Festplatte geschrieben |
| Nachweis | `GET /v1/models` + eine echte Completion | ein Schlüssel, der fehlschlägt, wird nie geschrieben |

Jeder Panel-Aufruf trägt sein eigenes frisches Einmal-Token; ein gedrosselter Versand wartet 65 s ab, und eine bereits verwendete Adresse bekommt ein brandneues Postfach.

## ⚙️ CLI-Optionen

```bash
node src/cli.js --count 1          # mint accounts (one per run, by policy)
node src/cli.js --verify-only      # re-verify every key in keys.txt
node src/cli.js --screen           # exit pool self-test (status + Turnstile gates)
node src/cli.js --connect-9router  # register the provider + a key on a local 9Router
node src/cli.js --help
```

| Flag | Standard | Bedeutung |
| --- | --- | --- |
| `--count N` | `1` | zu prägende Konten, seriell |
| `--model <id>` | `MiMo-V2.6-Flash` | für das Live-Tor verwendetes Modell |
| `--pool <file>` | `proxies.txt` | Exit-Pool, eine Zeile pro Proxy |
| `--tries N` | `3` | pro Konto geprüfte Exit-Kandidaten |
| `--gap MS` | `4000` | Pause zwischen Konten |
| `--code-timeout S` | `300` | Sekunden Wartezeit auf den E-Mail-Code |
| `--keys <file>` | `keys.txt` | Schlüsseldatei für `--verify-only` |
| `--debug-port N` | freier Port | Chrome-DevTools-Port — muss ungleich null sein (siehe Warum-Hinweis) |
| `--browser <exe>` | auto | Pfad zur Chrome-Binary |

| Env | Standard | Bedeutung |
| --- | --- | --- |
| `HCN_PROXIES` | `proxies.txt` | Exit-Pool, eine Zeile pro Proxy |
| `HCN_MODEL` | `MiMo-V2.6-Flash` | Modell für die Live-Verifikation |
| `CHROME_PATH` | auto | Pfad zur Chrome-Binary |

## 🛰️ Hinter einem Proxy

**Dringend empfohlen.** Das Tool funktioniert direkt, wo das Netzwerk es zulässt, aber das Turnstile dieses Panels benötigt eine gesunde Exit-Klasse (ein Residential-Exit / proxy wurde durchgängig funktionierend gemessen; kurzlebige kostenlose Exits starben innerhalb einer Minute). Mit `--pool` geht jede Anfrage — auch der Browser — über einen Exit aus der Liste, fail-closed: jeder Kandidat wird zuerst geprüft (`/api/status` + das Turnstile-Skript mit Redirects) und das Token wird dann auf dem Exit geboren, der es verwenden wird. Ohne Pool läuft der Durchlauf direkt und das Widget mountet meist nie.

## 📄 Dateien

| Pfad | Inhalt |
| --- | --- |
| `keys.txt` | live-verifizierte Schlüssel — von git ignoriert |
| `accounts.json` | Kontozustand inkl. fehlgeschlagener Versuche |
| `proxies.txt` | deine Exits — von git ignoriert |
| `out/` | Lauf-Logs, Fehler-Screenshots, `attempts.jsonl`-Ledger — von git ignoriert |

## 🔌 Mit 9Router verbinden

[9Router](https://9router.com) ist ein selbst gehosteter, OpenAI-kompatibler Router:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Standardwerte: `--prefix hcn`, `--base-url https://api.hcnsec.cn/v1`, `--model MiMo-V2.6-Flash`. Modelle werden als `hcn/<model id>` bereitgestellt. Die CLI kann dasselbe in einem Schritt tun: `node src/cli.js --connect-9router`.

## 🔑 Was ein Schlüssel dir bringt

| | |
| --- | --- |
| 🏢 Anbieter | **HCNSec** — [api.hcnsec.cn](https://api.hcnsec.cn) |
| 🎁 Was der Anbieter gibt | ein Konto mit `unlimited_quota`-Ansicht in der Standardgruppe, 16 Modelle |
| 🔑 Was ein Schlüssel dir bringt | einen Schlüssel auf seinem eigenen Konto: die verifizierten IDs auf `https://api.hcnsec.cn/v1` |
| 🌐 API-Form | OpenAI-kompatibel: `POST https://api.hcnsec.cn/v1/chat/completions` |
| 🔒 Deine Daten | nur `api.hcnsec.cn` und dein Inbox-Backend werden kontaktiert |

## 🐞 Issues

Wenn HCNSec seine Registrierungs-Tore, den Turnstile-Sitekey oder die E-Mail-Whitelist ändert, stoppt das Tool dort und sagt es. **[Öffne ein Issue](../../issues)** mit dem Befehl und der exakten Ausgabe.

## 📄 Lizenz

MIT © 2026 — siehe [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 hcnsec-bulk-apikey contributors

</div>