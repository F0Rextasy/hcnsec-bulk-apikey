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

**Crea cuentas de HCNSec — una por ejecución, según la propia política del panel — con claves API autogeneradas: inferencia compatible con OpenAI + conector 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-16%20served-412991?style=for-the-badge)](https://api.hcnsec.cn)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Inicio rápido](#-quick-start) · [🎁 Nivel gratuito](#-free-tier--how-much-you-get) · [🤖 Modelos](#-models--all-16-of-them) · [🧪 Usar la API](#-how-to-use-the-api) · [✅ Verificado](#-verified-working--every-key-is-tested) · [🧭 Cómo funciona](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Opciones](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Problemas](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Por qué existe esto

HCNSec (新疆幻城网安科技公益大模型安全网关) es un panel de New API con **16 ids de modelos** (medidos el 2026-10-21 en una clave activa) y una política de correo electrónico inusualmente completa: gmail / outlook / hotmail / 163 / qq / 126 / foxmail / icloud pasan todos su lista blanca. El registro es correo electrónico + código detrás de un Turnstile, el inicio de sesión es con contraseña simple, y el token de la cuenta lleva `unlimited_quota` en el grupo predeterminado.

El Turnstile es la puerta crítica: su token está vinculado a la salida que lo resolvió, por lo que la herramienta controla un Chrome real a través de una salida verificada y lee el propio widget del panel — un token resuelto en cualquier otro lugar responde `Turnstile 校验失败`. Un detalle medido de Chrome es parte del riel: el puerto de DevTools debe ser distinto de cero (`--remote-debugging-port=0` cambia `navigator.webdriver` a true y el widget entonces muere con el error `600010`); la CLI elige por sí misma un puerto libre distinto de cero.

> ⚠️ **Nota del operador (medida, anuncio #53, 2026-09-30):** el panel declara que una persona no puede ejecutar múltiples cuentas y dice que ha prohibido conjuntos de cuentas vinculadas. Trata cada cuenta como una unidad de riesgo; esta herramienta por defecto usa **una cuenta por ejecución**.

## 🎁 Nivel gratuito — cuánto obtienes

| Qué | Valor |
| --- | --- |
| Cuota de la cuenta | `999,999,999` en `/api/user/self` (vista ilimitada en el grupo predeterminado) |
| `quota_per_unit` | 500,000 |
| Registro | correo electrónico + código de 6 caracteres + Turnstile (token de un solo uso por paso) |
| Política de correo | gmail, outlook, hotmail, **163, qq, 126, foxmail**, icloud aceptados; sina/aliyun/inboxbear rechazados (`EMAIL_ADDRESS_REJECTED` — inboxbear re-medido el 2026-10-21) |
| Check-in | `/api/user/checkin` existe pero está protegido por Turnstile; el margen de cuota lo hace opcional |
| Política multicuenta | **anuncio #53: el uso multicuenta es motivo de prohibición** — ver la nota del operador arriba |

> 💡 El nivel gratuito lo establece HCNSec y puede cambiar. Consulta los números en vivo en [api.hcnsec.cn](https://api.hcnsec.cn) — y deja que la propia puerta de la herramienta decida:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modelos — los 16

`GET /v1/models` devolvió **16 ids** en una clave activa (2026-10-21):

`auto` · `DeepSeek-V4-Flash` · `DeepSeek-V4-Pro` · `DeepSeek-V4.1-Flash` · `glm-5.3` · `glm-5.3-flash` · `longcat-2.5` · `MiMo-V2.6-Flash` · `MiniCPM-V-4.7-35B-A3B` · `MiniMax-M3.1-Flash` · `Qwen3-Embedding-8B` · `Qwen3.8-27B` · `Qwen3.8-Flash-Next` · `sensenova-6.8-flash-lite` · `sensenova-u1.5-lite` · `step-5-preview`

Una completion en vivo respondió en los modelos de verificación (`content: "ok"`):

| ID del modelo | Estado |
| --- | --- |
| `MiMo-V2.6-Flash` | **200** — la puerta de chat predeterminada de la herramienta |
| `longcat-2.5` | **200** |
| `step-5-preview`, `Qwen3.8-Flash-Next`, `glm-5.3-flash`, `DeepSeek-V4-Flash` | **200** (ejecución anterior, misma generación del panel) |

```bash
curl https://api.hcnsec.cn/v1/chat/completions \
  -H "Authorization: Bearer Hjcop6…" -H "Content-Type: application/json" \
  -d '{"model":"MiMo-V2.6-Flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 Cómo usar la API

URL base **`https://api.hcnsec.cn/v1`** — compatible con OpenAI. Las claves funcionan tanto sin prefijo como con el prefijo `sk-` (ambas probadas con 200 en `/v1/models`); el archivo contiene la forma revelada sin prefijo.

```python
from openai import OpenAI
c = OpenAI(base_url="https://api.hcnsec.cn/v1", api_key="Hjcop6…")
print(c.chat.completions.create(model="MiMo-V2.6-Flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 Inicio rápido

```bash
cd hcnsec-bulk-apikey
printf 'socks5://127.0.0.1:1080\n' > proxies.txt   # one exit per line — see the proxy note
node src/launcher.js
# or directly:
node src/cli.js --count 1        # one account per run, by policy
node src/cli.js --verify-only    # re-test every key already in keys.txt
node src/cli.js --screen         # test the exit list against the panel
```

El registro se ejecuta en un Chrome real controlado a través de tu salida (el token de Turnstile está vinculado a ella); `keys.txt` se escribe solo después de `GET /v1/models` + una completion real, y `accounts.json` conserva el estado de la cuenta.

### 🖱️ El lanzador

El lanzador pide la lista de proxies, la recuerda y ejecuta la CLI — crear, verificar, examinar salidas, ajustes, ayuda, Conectar a 9Router.

## ✅ Verificado y funcionando — cada clave se prueba

Todas las comprobaciones siguientes se volvieron a ejecutar el **2026-10-21** desde la CLI distribuida (salida del pool: proxy sobre `socks5h://127.0.0.1:40000`).

| Comprobación | Resultado |
| --- | --- |
| `--screen` (verificación de salida) | **✔ 1.3 s** — `/api/status` 200 + `challenges.cloudflare.com/turnstile/v0/api.js` 200 con redirecciones |
| riel del navegador (Chrome real) | token de Turnstile en la página **752 caracteres**, `navigator.webdriver:false` — y el panel lo **aceptó**: `/api/verification` respondió con la política de dominio de correo (`EMAIL_ADDRESS_REJECTED`), no con un fallo de Turnstile |
| `GET /api/status` | **200** — registro activado, verificación de correo activada, turnstile activado, checkin activado |
| `POST /api/user/register` | **200** `{"message":"","success":true}` (correo electrónico + código + token nuevo) |
| `POST /api/user/login` | **200** (token de acceso; contraseña simple) |
| `POST /api/token/` + revelar | **200** — `unlimited_quota` en el grupo predeterminado |
| `GET /api/user/self` | **200** `quota=999999999` grupo `default` |
| `GET /v1/models` | **200, 16 modelos** |
| `--verify-only` ×2 claves | **2/2 chat-200** en `MiMo-V2.6-Flash` (`"ok"`) a través del pool |
| ejecución de día de bandera | **78 claves** en `keys.txt`, cada una escrita solo después de su propio chat 200 |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 Cómo funciona

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: HCNSec page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.hcnsec.cn/v1/chat/completions → real content = the key is live
```

| Paso | Llamada | Notas |
| --- | --- | --- |
| verificar | `GET /api/status` + el script de Turnstile | una salida que no puede cargar `challenges.cloudflare.com` se omite antes que nada |
| navegador | `/register` del panel en Chrome real a través de la salida | el widget en la página responde con un token de más de 700 caracteres; el token está vinculado a la IP de esa salida |
| enviar código | `GET /api/verification?email=…&turnstile=<fresh>` | la lista blanca se aplica después del token |
| registrar | `POST /api/user/register?turnstile=<fresh>` | correo electrónico + código + contraseña |
| iniciar sesión | `POST /api/user/login?turnstile=<fresh>` | contraseña simple (cifrado desactivado) |
| clave | `POST /api/token/` (+ salto de revelado) | escrita en disco una vez |
| prueba | `GET /v1/models` + una completion real | una clave que falla nunca se escribe |

Cada llamada al panel lleva su propio token de un solo uso nuevo; un envío limitado espera 65 s, y una dirección ya en uso obtiene un buzón completamente nuevo.

## ⚙️ Opciones de la CLI

```bash
node src/cli.js --count 1          # mint accounts (one per run, by policy)
node src/cli.js --verify-only      # re-verify every key in keys.txt
node src/cli.js --screen           # exit pool self-test (status + Turnstile gates)
node src/cli.js --connect-9router  # register the provider + a key on a local 9Router
node src/cli.js --help
```

| Opción | Predeterminado | Significado |
| --- | --- | --- |
| `--count N` | `1` | cuentas a crear, en serie |
| `--model <id>` | `MiMo-V2.6-Flash` | modelo usado para la puerta en vivo |
| `--pool <file>` | `proxies.txt` | pool de salidas, una línea por proxy |
| `--tries N` | `3` | salidas candidatas verificadas por cuenta |
| `--gap MS` | `4000` | pausa entre cuentas |
| `--code-timeout S` | `300` | segundos de espera para el código de correo |
| `--keys <file>` | `keys.txt` | archivo de claves para `--verify-only` |
| `--debug-port N` | puerto libre | puerto de Chrome DevTools — debe ser distinto de cero (ver la nota del porqué) |
| `--browser <exe>` | auto | ruta del binario de Chrome |

| Variable de entorno | Predeterminado | Significado |
| --- | --- | --- |
| `HCN_PROXIES` | `proxies.txt` | pool de salidas, una línea por proxy |
| `HCN_MODEL` | `MiMo-V2.6-Flash` | modelo para la verificación en vivo |
| `CHROME_PATH` | auto | ruta del binario de Chrome |

## 🛰️ Detrás de un proxy

**Muy recomendado.** La herramienta funciona directo donde la red lo permite, pero el Turnstile de este panel necesita una clase de salida saludable (una salida residencial / proxy se midió funcionando de extremo a extremo; las salidas gratuitas de corta duración murieron en menos de un minuto). Con `--pool` cada solicitud — incluido el navegador — pasa por una salida de la lista, con fallo cerrado: cada candidata se verifica primero (`/api/status` + el script de Turnstile con redirecciones) y luego el token nace en la salida que lo usará. Sin un pool, la ejecución va directo y el widget normalmente nunca se monta.

## 📄 Archivos

| Ruta | Contenido |
| --- | --- |
| `keys.txt` | claves verificadas en vivo — ignoradas por git |
| `accounts.json` | estado de la cuenta, incluidos los intentos fallidos |
| `proxies.txt` | tus salidas — ignoradas por git |
| `out/` | registros de ejecución, capturas de fallos, libro mayor `attempts.jsonl` — ignorados por git |

## 🔌 Conectar a 9Router

[9Router](https://9router.com) es un router autoalojado compatible con OpenAI:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Predeterminados: `--prefix hcn`, `--base-url https://api.hcnsec.cn/v1`, `--model MiMo-V2.6-Flash`. Los modelos se sirven como `hcn/<model id>`. La CLI puede hacer lo mismo en un solo paso: `node src/cli.js --connect-9router`.

## 🔑 Qué te da una clave

| | |
| --- | --- |
| 🏢 Proveedor | **HCNSec** — [api.hcnsec.cn](https://api.hcnsec.cn) |
| 🎁 Qué da el proveedor | una cuenta con vista `unlimited_quota` en el grupo predeterminado, 16 modelos |
| 🔑 Qué te da una clave | una clave en su propia cuenta: los ids verificados en `https://api.hcnsec.cn/v1` |
| 🌐 Forma de la API | compatible con OpenAI: `POST https://api.hcnsec.cn/v1/chat/completions` |
| 🔒 Tus datos | solo se contacta con `api.hcnsec.cn` y el backend de tu buzón |

## 🐞 Problemas

Si HCNSec cambia sus puertas de registro, la sitekey de Turnstile o la lista blanca de correo, la herramienta se detiene ahí y lo indica. **[Abre un issue](../../issues)** con el comando y la salida exacta.

## 📄 Licencia

MIT © 2026 — ver [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 hcnsec-bulk-apikey contributors

</div>