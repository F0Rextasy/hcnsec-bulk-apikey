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

**HCNSec 계정을 발급합니다 — 패널 자체 정책에 따라 실행당 하나씩 — 자동 생성된 API 키와 함께: OpenAI 호환 추론 + 9Router 커넥터.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-16%20served-412991?style=for-the-badge)](https://api.hcnsec.cn)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 빠른 시작](#-quick-start) · [🎁 무료 티어](#-free-tier--how-much-you-get) · [🤖 모델](#-models--all-16-of-them) · [🧪 API 사용법](#-how-to-use-the-api) · [✅ 검증됨](#-verified-working--every-key-is-tested) · [🧭 작동 방식](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ 옵션](#️-cli-options) · [🛰️ 프록시](#️-behind-a-proxy) · [🐞 이슈](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ 이 도구가 존재하는 이유

HCNSec (新疆幻城网安科技公益大模型安全网关)은 **16개의 모델 id**를 가진 New API 패널이며(2026-10-21 라이브 키에서 측정), 유난히 완전한 이메일 정책을 갖추고 있습니다: gmail / outlook / hotmail / 163 / qq / 126 / foxmail / icloud 모두 화이트리스트를 통과합니다. 가입은 Turnstile 뒤의 이메일 + 코드 방식이고, 로그인은 일반 비밀번호이며, 계정의 토큰은 기본 그룹에서 `unlimited_quota`를 가집니다.

Turnstile이 핵심 관문입니다: 그 토큰은 이를 해결한 출구에 묶여 있으므로, 이 도구는 검증된 하나의 출구를 통해 실제 Chrome을 구동하고 패널 자체의 위젯을 읽습니다 — 다른 곳에서 해결된 토큰은 `Turnstile 校验失败`로 응답합니다. 측정된 Chrome의 세부 사항 하나가 이 레일에 포함됩니다: DevTools 포트는 0이 아니어야 합니다(`--remote-debugging-port=0`은 `navigator.webdriver`를 true로 바꾸고 위젯이 오류 `600010`과 함께 죽습니다); CLI가 스스로 사용 가능한 0이 아닌 포트를 선택합니다.

> ⚠️ **운영자 주의 (측정됨, 공지 #53, 2026-09-30):** 패널은 한 사람이 여러 계정을 운영할 수 없다고 명시하며 연관된 계정 집합을 차단했다고 밝힙니다. 모든 계정을 위험 단위로 취급하십시오; 이 도구는 기본적으로 **실행당 하나의 계정**을 사용합니다.

## 🎁 무료 티어 — 얼마나 받을 수 있나

| 항목 | 값 |
| --- | --- |
| 계정 할당량 | `/api/user/self`에서 `999,999,999` (기본 그룹에서 무제한 표시) |
| `quota_per_unit` | 500,000 |
| 가입 | 이메일 + 6자리 코드 + Turnstile (단계당 일회용 토큰) |
| 이메일 정책 | gmail, outlook, hotmail, **163, qq, 126, foxmail**, icloud 허용; sina/aliyun/inboxbear 거부 (`EMAIL_ADDRESS_REJECTED` — inboxbear 2026-10-21 재측정) |
| 출석 체크 | `/api/user/checkin`이 존재하지만 Turnstile로 제한됨; 할당량 여유 덕분에 선택 사항 |
| 다중 계정 정책 | **공지 #53: 다중 계정 사용은 차단 대상** — 위의 운영자 주의 참조 |

> 💡 무료 티어는 HCNSec이 설정하며 변경될 수 있습니다. 실시간 수치는 [api.hcnsec.cn](https://api.hcnsec.cn)에서 확인하세요 — 그리고 도구 자체의 게이트가 판단하게 하세요:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 모델 — 16개 전부

`GET /v1/models`는 라이브 키에서 **16개의 id**를 반환했습니다(2026-10-21):

`auto` · `DeepSeek-V4-Flash` · `DeepSeek-V4-Pro` · `DeepSeek-V4.1-Flash` · `glm-5.3` · `glm-5.3-flash` · `longcat-2.5` · `MiMo-V2.6-Flash` · `MiniCPM-V-4.7-35B-A3B` · `MiniMax-M3.1-Flash` · `Qwen3-Embedding-8B` · `Qwen3.8-27B` · `Qwen3.8-Flash-Next` · `sensenova-6.8-flash-lite` · `sensenova-u1.5-lite` · `step-5-preview`

검증 모델에서 라이브 완성이 응답했습니다(`content: "ok"`):

| 모델 ID | 상태 |
| --- | --- |
| `MiMo-V2.6-Flash` | **200** — 도구의 기본 채팅 게이트 |
| `longcat-2.5` | **200** |
| `step-5-preview`, `Qwen3.8-Flash-Next`, `glm-5.3-flash`, `DeepSeek-V4-Flash` | **200** (이전 실행, 동일 패널 세대) |

```bash
curl https://api.hcnsec.cn/v1/chat/completions \
  -H "Authorization: Bearer Hjcop6…" -H "Content-Type: application/json" \
  -d '{"model":"MiMo-V2.6-Flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 API 사용법

Base URL **`https://api.hcnsec.cn/v1`** — OpenAI 호환. 키는 그대로도, `sk-` 접두사와 함께도 작동합니다(둘 다 `/v1/models`에서 200 테스트됨); 파일에는 접두사 없는 노출 형식이 담깁니다.

```python
from openai import OpenAI
c = OpenAI(base_url="https://api.hcnsec.cn/v1", api_key="Hjcop6…")
print(c.chat.completions.create(model="MiMo-V2.6-Flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 빠른 시작

```bash
cd hcnsec-bulk-apikey
printf 'socks5://127.0.0.1:1080\n' > proxies.txt   # one exit per line — see the proxy note
node src/launcher.js

# or directly:
node src/cli.js --count 1        # one account per run, by policy
node src/cli.js --verify-only    # re-test every key already in keys.txt
node src/cli.js --screen         # test the exit list against the panel
```

가입은 출구를 통해 구동되는 실제 Chrome에서 실행됩니다(Turnstile 토큰이 거기에 묶여 있음); `keys.txt`는 `GET /v1/models` + 실제 완성이 통과한 후에만 작성되며, `accounts.json`은 계정 상태를 보관합니다.

### 🖱️ 런처

런처는 프록시 목록을 요청하고, 이를 기억하며, CLI를 실행합니다 — 발급, 검증, 출구 스크리닝, 설정, 도움말, 9Router 연결.

## ✅ 검증됨 — 모든 키가 테스트됨

아래의 모든 검사는 **2026-10-21**에 배포된 CLI에서 재실행되었습니다(풀 출구: `socks5h://127.0.0.1:40000`을 통한 WARP).

| 검사 | 결과 |
| --- | --- |
| `--screen` (출구 검증) | **✔ 1.3 s** — `/api/status` 200 + 리다이렉트 포함 `challenges.cloudflare.com/turnstile/v0/api.js` 200 |
| 브라우저 레일 (실제 Chrome) | 인페이지 Turnstile 토큰 **752자**, `navigator.webdriver:false` — 그리고 패널이 이를 **수락**했습니다: `/api/verification`이 Turnstile 실패가 아닌 이메일 도메인 정책(`EMAIL_ADDRESS_REJECTED`)으로 응답했습니다 |
| `GET /api/status` | **200** — register on, e-mail verification on, turnstile on, checkin on |
| `POST /api/user/register` | **200** `{"message":"","success":true}` (이메일 + 코드 + 새 토큰) |
| `POST /api/user/login` | **200** (액세스 토큰; 일반 비밀번호) |
| `POST /api/token/` + reveal | **200** — 기본 그룹에서 `unlimited_quota` |
| `GET /api/user/self` | **200** `quota=999999999` 그룹 `default` |
| `GET /v1/models` | **200, 16개 모델** |
| `--verify-only` ×2 키 | 풀을 통해 `MiMo-V2.6-Flash`(`"ok"`)에서 **2/2 chat-200** |
| 플래그데이 실행 | `keys.txt`의 모든 키, 각각 자체 chat 200 이후에만 작성됨 |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

> **품질 게이트.** 여기의 모든 키는 `keys.txt`에 작성되기 전에 라이브 `chat-200`을 통과했습니다
> (죽은 키는 절대 배포되지 않습니다). 라우터 조합도 모델 수준에서 동일한 규칙을 따릅니다: 항목은
> **모델이 `chat-200`으로 응답하고 도구 호출이 가능한 경우에만** 추가됩니다 — 도구 지원이 없는 모델,
> 죽은 키 또는 죽은 제공자는 추가되지 않고 제거됩니다. 언제든지 검증 단계를 재실행하세요;
> 실패는 보고되며, 절대 숨겨지지 않습니다.

## 🧭 작동 방식

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: HCNSec page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.hcnsec.cn/v1/chat/completions → real content = the key is live
```

| 단계 | 호출 | 비고 |
| --- | --- | --- |
| 검증 | `GET /api/status` + Turnstile 스크립트 | `challenges.cloudflare.com`을 로드할 수 없는 출구는 다른 무엇보다 먼저 건너뜁니다 |
| 브라우저 | 출구를 통한 실제 Chrome에서 패널 `/register` | 인페이지 위젯이 700자 이상의 토큰으로 응답합니다; 토큰은 해당 출구의 IP에 묶입니다 |
| 코드 전송 | `GET /api/verification?email=…&turnstile=<fresh>` | 화이트리스트는 토큰 이후에 적용됩니다 |
| 가입 | `POST /api/user/register?turnstile=<fresh>` | 이메일 + 코드 + 비밀번호 |
| 로그인 | `POST /api/user/login?turnstile=<fresh>` | 일반 비밀번호 (암호화 꺼짐) |
| 키 | `POST /api/token/` (+ reveal 홉) | 디스크에 한 번 작성됨 |
| 증명 | `GET /v1/models` + 실제 완성 | 실패한 키는 절대 작성되지 않습니다 |

모든 패널 호출은 자체적인 새 일회용 토큰을 지닙니다; 제한된 전송은 65초 백오프하며, 이미 사용 중인 주소는 완전히 새로운 메일박스를 받습니다.

## ⚙️ CLI 옵션

```bash
node src/cli.js --count 1          # mint accounts (one per run, by policy)
node src/cli.js --verify-only      # re-verify every key in keys.txt
node src/cli.js --screen           # exit pool self-test (status + Turnstile gates)
node src/cli.js --connect-9router  # register the provider + a key on a local 9Router
node src/cli.js --help
```

| 플래그 | 기본값 | 의미 |
| --- | --- | --- |
| `--count N` | `1` | 발급할 계정 수, 순차 |
| `--model <id>` | `MiMo-V2.6-Flash` | 라이브 게이트에 사용되는 모델 |
| `--pool <file>` | `proxies.txt` | 출구 풀, 프록시당 한 줄 |
| `--tries N` | `3` | 계정당 검증할 출구 후보 |
| `--gap MS` | `4000` | 계정 간 일시 정지 |
| `--code-timeout S` | `300` | 이메일 코드를 기다리는 초 |
| `--keys <file>` | `keys.txt` | `--verify-only`용 키 파일 |
| `--debug-port N` | 사용 가능한 포트 | Chrome DevTools 포트 — 0이 아니어야 함 (이유 참조) |
| `--browser <exe>` | 자동 | Chrome 바이너리 경로 |

| 환경 변수 | 기본값 | 의미 |
| --- | --- | --- |
| `HCN_PROXIES` | `proxies.txt` | 출구 풀, 프록시당 한 줄 |
| `HCN_MODEL` | `MiMo-V2.6-Flash` | 라이브 검증용 모델 |
| `CHROME_PATH` | 자동 | Chrome 바이너리 경로 |

## 🛰️ 프록시 뒤에서

**강력히 권장합니다.** 이 도구는 네트워크가 허용하는 곳에서는 직접 작동하지만, 이 패널의 Turnstile은 건강한 출구 등급이 필요합니다(주거용 출구 / WARP가 엔드투엔드로 작동하는 것으로 측정됨; 수명이 짧은 무료 출구는 1분 안에 죽었습니다). `--pool`을 사용하면 모든 요청 — 브라우저 포함 — 이 목록의 출구를 통해 나가며, fail-closed 방식입니다: 각 후보는 먼저 검증되고(`/api/status` + 리다이렉트 포함 Turnstile 스크립트), 토큰은 이를 사용할 출구에서 생성됩니다. 풀이 없으면 실행은 직접 나가며 위젯은 보통 마운트되지 않습니다.

## 📄 파일

| 경로 | 내용 |
| --- | --- |
| `keys.txt` | 라이브 검증된 키 — git-ignored |
| `accounts.json` | 실패한 시도를 포함한 계정 상태 |
| `proxies.txt` | 사용자의 출구 — git-ignored |
| `out/` | 실행 로그, 실패 스크린샷, `attempts.jsonl` 원장 — git-ignored |

## 🔌 9Router에 연결

[9Router](https://9router.com)는 자체 호스팅, OpenAI 호환 라우터입니다:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

기본값: `--prefix hcn`, `--base-url https://api.hcnsec.cn/v1`, `--model MiMo-V2.6-Flash`. 모델은 `hcn/<model id>`로 제공됩니다. CLI는 한 번에 동일하게 수행할 수 있습니다: `node src/cli.js --connect-9router`.

## 🔌 OmniRoute에 연결

[OmniRoute](https://omniroute.online)는 자체 호스팅, OpenAI 호환 라우터입니다:

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

기본값: `--prefix hcn`, `--base-url https://api.hcnsec.cn/v1`, `--model MiMo-V2.6-Flash`. 모델은 `hcn/<model id>`로 제공됩니다. CLI는 한 번에 동일하게 수행할 수 있습니다: `node src/cli.js --connect-omniroute`.

## 🔑 키 하나로 얻는 것

| | |
| --- | --- |
| 🏢 제공자 | **HCNSec** — [api.hcnsec.cn](https://api.hcnsec.cn) |
| 🎁 제공자가 주는 것 | 기본 그룹에서 `unlimited_quota` 표시가 있는 계정, 16개 모델 |
| 🔑 키 하나로 얻는 것 | 자체 계정의 키 하나: `https://api.hcnsec.cn/v1`에서 검증된 id들 |
| 🌐 API 형태 | OpenAI 호환: `POST https://api.hcnsec.cn/v1/chat/completions` |
| 🔒 데이터 | `api.hcnsec.cn`과 사용자의 메일박스 백엔드만 접촉됩니다 |

## 🐞 이슈

HCNSec이 가입 게이트, Turnstile 사이트키 또는 이메일 화이트리스트를 변경하면, 도구는 그 지점에서 멈추고 그렇게 알립니다. 명령과 정확한 출력을 포함하여 **[이슈를 열어주세요](../../issues)**.

## 📄 라이선스

MIT © 2026 — [LICENSE](LICENSE) 참조.

<div align="center">

**[MIT](LICENSE)** © 2026 hcnsec-bulk-apikey contributors

</div>