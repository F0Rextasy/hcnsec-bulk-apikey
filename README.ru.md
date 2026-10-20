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

**Создавайте аккаунты HCNSec — по одному за запуск, согласно собственной политике панели — с автоматически сгенерированными API-ключами: OpenAI-совместимый инференс + коннектор 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-16%20served-412991?style=for-the-badge)](https://api.hcnsec.cn)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Быстрый старт](#-quick-start) · [🎁 Бесплатный тариф](#-free-tier--how-much-you-get) · [🤖 Модели](#-models--all-16-of-them) · [🧪 Использование API](#-how-to-use-the-api) · [✅ Проверено](#-verified-working--every-key-is-tested) · [🧭 Как это работает](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Опции](#️-cli-options) · [🛰️ Прокси](#️-behind-a-proxy) · [🐞 Проблемы](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Зачем это нужно

HCNSec (新疆幻城网安科技公益大模型安全网关) — это панель New API с **16 идентификаторами моделей** (измерено 2026-10-21 на живом ключе) и необычно полной политикой в отношении электронной почты: gmail / outlook / hotmail / 163 / qq / 126 / foxmail / icloud — все проходят её белый список. Регистрация — это электронная почта + код за Turnstile, вход — обычный пароль, а токен аккаунта несёт `unlimited_quota` в группе по умолчанию.

Turnstile — это несущие ворота: его токен привязан к выходу, который его решил, поэтому инструмент управляет настоящим Chrome через один проверенный выход и читает собственный виджет панели — токен, решённый где-либо ещё, отвечает `Turnstile 校验失败`. Одна измеренная деталь Chrome является частью рельса: порт DevTools должен быть ненулевым (`--remote-debugging-port=0` переключает `navigator.webdriver` в true, и виджет затем умирает с ошибкой `600010`); CLI сам выбирает свободный ненулевой порт.

> ⚠️ **Примечание оператора (измерено, объявление #53, 2026-09-30):** панель заявляет, что один человек не может запускать несколько аккаунтов, и говорит, что заблокировала связанные наборы аккаунтов. Рассматривайте каждый аккаунт как единицу риска; этот инструмент по умолчанию использует **один аккаунт за запуск**.

## 🎁 Бесплатный тариф — сколько вы получаете

| Что | Значение |
| --- | --- |
| Квота аккаунта | `999,999,999` на `/api/user/self` (неограниченный просмотр в группе по умолчанию) |
| `quota_per_unit` | 500,000 |
| Регистрация | электронная почта + 6-символьный код + Turnstile (одноразовый токен на каждый шаг) |
| Политика электронной почты | gmail, outlook, hotmail, **163, qq, 126, foxmail**, icloud принимаются; sina/aliyun/inboxbear отклоняются (`EMAIL_ADDRESS_REJECTED` — inboxbear переизмерен 2026-10-21) |
| Ежедневная отметка | `/api/user/checkin` существует, но защищён Turnstile; запаса квоты достаточно, чтобы это было необязательно |
| Политика мультиаккаунтинга | **объявление #53: использование нескольких аккаунтов ведёт к бану** — см. примечание оператора выше |

> 💡 Бесплатный тариф устанавливается HCNSec и может измениться. Проверяйте актуальные цифры на [api.hcnsec.cn](https://api.hcnsec.cn) — и пусть собственные ворота инструмента решают:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Модели — все 16 из них

`GET /v1/models` вернул **16 идентификаторов** на живом ключе (2026-10-21):

`auto` · `DeepSeek-V4-Flash` · `DeepSeek-V4-Pro` · `DeepSeek-V4.1-Flash` · `glm-5.3` · `glm-5.3-flash` · `longcat-2.5` · `MiMo-V2.6-Flash` · `MiniCPM-V-4.7-35B-A3B` · `MiniMax-M3.1-Flash` · `Qwen3-Embedding-8B` · `Qwen3.8-27B` · `Qwen3.8-Flash-Next` · `sensenova-6.8-flash-lite` · `sensenova-u1.5-lite` · `step-5-preview`

Живое завершение ответило на проверочных моделях (`content: "ok"`):

| ID модели | Статус |
| --- | --- |
| `MiMo-V2.6-Flash` | **200** — шлюз чата по умолчанию в инструменте |
| `longcat-2.5` | **200** |
| `step-5-preview`, `Qwen3.8-Flash-Next`, `glm-5.3-flash`, `DeepSeek-V4-Flash` | **200** (более ранний запуск, то же поколение панели) |

```bash
curl https://api.hcnsec.cn/v1/chat/completions \
  -H "Authorization: Bearer Hjcop6…" -H "Content-Type: application/json" \
  -d '{"model":"MiMo-V2.6-Flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 Как использовать API

Базовый URL **`https://api.hcnsec.cn/v1`** — OpenAI-совместимый. Ключи работают как в чистом виде, так и с префиксом `sk-` (оба протестированы 200 на `/v1/models`); в файле хранится форма без префикса.

```python
from openai import OpenAI
c = OpenAI(base_url="https://api.hcnsec.cn/v1", api_key="Hjcop6…")
print(c.chat.completions.create(model="MiMo-V2.6-Flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 Быстрый старт

```bash
cd hcnsec-bulk-apikey
printf 'socks5://127.0.0.1:1080\n' > proxies.txt   # one exit per line — see the proxy note
node src/launcher.js
# or directly:
node src/cli.js --count 1        # one account per run, by policy
node src/cli.js --verify-only    # re-test every key already in keys.txt
node src/cli.js --screen         # test the exit list against the panel
```

Регистрация выполняется в настоящем Chrome, управляемом через ваш выход (токен Turnstile привязан к нему); `keys.txt` записывается только после `GET /v1/models` + реального завершения, а `accounts.json` хранит состояние аккаунта.

### 🖱️ Лаунчер

Лаунчер запрашивает список прокси, запоминает его и запускает CLI — создание, проверка, отбор выходов, настройки, справка, подключение к 9Router.

## ✅ Проверено в работе — каждый ключ протестирован

Все проверки ниже были повторно выполнены **2026-10-21** из поставляемого CLI (выход пула: WARP через `socks5h://127.0.0.1:40000`).

| Проверка | Результат |
| --- | --- |
| `--screen` (отбор выходов) | **✔ 1.3 с** — `/api/status` 200 + `challenges.cloudflare.com/turnstile/v0/api.js` 200 с редиректами |
| браузерный рельс (настоящий Chrome) | внутристраничный токен Turnstile **752 символа**, `navigator.webdriver:false` — и панель его **приняла**: `/api/verification` ответил политикой домена электронной почты (`EMAIL_ADDRESS_REJECTED`), а не ошибкой Turnstile |
| `GET /api/status` | **200** — регистрация включена, проверка электронной почты включена, turnstile включён, checkin включён |
| `POST /api/user/register` | **200** `{"message":"","success":true}` (электронная почта + код + свежий токен) |
| `POST /api/user/login` | **200** (токен доступа; обычный пароль) |
| `POST /api/token/` + раскрытие | **200** — `unlimited_quota` в группе по умолчанию |
| `GET /api/user/self` | **200** `quota=999999999` группа `default` |
| `GET /v1/models` | **200, 16 моделей** |
| `--verify-only` ×2 ключа | **2/2 chat-200** на `MiMo-V2.6-Flash` (`"ok"`) через пул |
| запуск в день флагов | **78 ключей** в `keys.txt`, каждый записан только после собственного chat 200 |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 Как это работает

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: HCNSec page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.hcnsec.cn/v1/chat/completions → real content = the key is live
```

| Шаг | Вызов | Примечания |
| --- | --- | --- |
| отбор | `GET /api/status` + скрипт Turnstile | выход, который не может загрузить `challenges.cloudflare.com`, пропускается до всего остального |
| браузер | панель `/register` в настоящем Chrome через выход | внутристраничный виджет отвечает токеном длиной 700+ символов; токен привязан к IP этого выхода |
| отправка кода | `GET /api/verification?email=…&turnstile=<fresh>` | белый список применяется после токена |
| регистрация | `POST /api/user/register?turnstile=<fresh>` | электронная почта + код + пароль |
| вход | `POST /api/user/login?turnstile=<fresh>` | обычный пароль (шифрование отключено) |
| ключ | `POST /api/token/` (+ шаг раскрытия) | записывается на диск один раз |
| доказательство | `GET /v1/models` + реальное завершение | ключ, который не проходит, никогда не записывается |

Каждый вызов панели несёт собственный свежий одноразовый токен; при ограничении отправки выполняется откат на 65 с, а адрес, уже используемый, получает совершенно новый почтовый ящик.

## ⚙️ Опции CLI

```bash
node src/cli.js --count 1          # mint accounts (one per run, by policy)
node src/cli.js --verify-only      # re-verify every key in keys.txt
node src/cli.js --screen           # exit pool self-test (status + Turnstile gates)
node src/cli.js --connect-9router  # register the provider + a key on a local 9Router
node src/cli.js --help
```

| Флаг | По умолчанию | Значение |
| --- | --- | --- |
| `--count N` | `1` | количество создаваемых аккаунтов, последовательно |
| `--model <id>` | `MiMo-V2.6-Flash` | модель, используемая для живого шлюза |
| `--pool <file>` | `proxies.txt` | пул выходов, по одной строке на прокси |
| `--tries N` | `3` | кандидаты выходов, проверяемые на каждый аккаунт |
| `--gap MS` | `4000` | пауза между аккаунтами |
| `--code-timeout S` | `300` | секунды ожидания кода из электронной почты |
| `--keys <file>` | `keys.txt` | файл ключей для `--verify-only` |
| `--debug-port N` | свободный порт | порт Chrome DevTools — должен быть ненулевым (см. пояснение) |
| `--browser <exe>` | авто | путь к бинарнику Chrome |

| Переменная | По умолчанию | Значение |
| --- | --- | --- |
| `HCN_PROXIES` | `proxies.txt` | пул выходов, по одной строке на прокси |
| `HCN_MODEL` | `MiMo-V2.6-Flash` | модель для живой проверки |
| `CHROME_PATH` | авто | путь к бинарнику Chrome |

## 🛰️ За прокси

**Настоятельно рекомендуется.** Инструмент работает напрямую там, где это позволяет сеть, но Turnstile этой панели требует здорового класса выходов (резидентный выход / WARP был измерен как работающий от начала до конца; недолговечные бесплатные выходы умирали в течение минуты). С `--pool` каждый запрос — включая браузер — идёт через выход из списка, с отказом при сбое: каждый кандидат сначала проверяется (`/api/status` + скрипт Turnstile с редиректами), и затем токен рождается на том выходе, который будет его использовать. Без пула запуск идёт напрямую, и виджет обычно вообще не монтируется.

## 📄 Файлы

| Путь | Содержимое |
| --- | --- |
| `keys.txt` | живые проверенные ключи — игнорируется git |
| `accounts.json` | состояние аккаунта, включая неудачные попытки |
| `proxies.txt` | ваши выходы — игнорируется git |
| `out/` | журналы запусков, снимки сбоев, реестр `attempts.jsonl` — игнорируется git |

## 🔌 Подключение к 9Router

[9Router](https://9router.com) — это самостоятельно размещаемый OpenAI-совместимый роутер:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

По умолчанию: `--prefix hcn`, `--base-url https://api.hcnsec.cn/v1`, `--model MiMo-V2.6-Flash`. Модели обслуживаются как `hcn/<model id>`. CLI может сделать то же самое одним выстрелом: `node src/cli.js --connect-9router`.

## 🔑 Что даёт один ключ

| | |
| --- | --- |
| 🏢 Провайдер | **HCNSec** — [api.hcnsec.cn](https://api.hcnsec.cn) |
| 🎁 Что даёт провайдер | аккаунт с представлением `unlimited_quota` в группе по умолчанию, 16 моделей |
| 🔑 Что даёт один ключ | один ключ на собственном аккаунте: проверенные идентификаторы на `https://api.hcnsec.cn/v1` |
| 🌐 Форма API | OpenAI-совместимый: `POST https://api.hcnsec.cn/v1/chat/completions` |
| 🔒 Ваши данные | связь только с `api.hcnsec.cn` и вашим почтовым бэкендом |

## 🐞 Проблемы

Если HCNSec изменит свои шлюзы регистрации, sitekey Turnstile или белый список электронной почты, инструмент остановится там и сообщит об этом. **[Откройте issue](../../issues)** с командой и точным выводом.

## 📄 Лицензия

MIT © 2026 — см. [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 hcnsec-bulk-apikey contributors

</div>