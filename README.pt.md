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

**Crie contas HCNSec — uma por execução, conforme a política do próprio painel — com chaves de API geradas automaticamente: inferência compatível com OpenAI + conector 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-16%20served-412991?style=for-the-badge)](https://api.hcnsec.cn)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Início rápido](#-quick-start) · [🎁 Nível gratuito](#-free-tier--how-much-you-get) · [🤖 Modelos](#-models--all-16-of-them) · [🧪 Use a API](#-how-to-use-the-api) · [✅ Verificado](#-verified-working--every-key-is-tested) · [🧭 Como funciona](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Opções](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Problemas](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Por que isto existe

HCNSec (新疆幻城网安科技公益大模型安全网关) é um painel New API com **16 ids de modelo** (medidos em 2026-10-21 em uma chave ativa) e uma política de e-mail incomumente completa: gmail / outlook / hotmail / 163 / qq / 126 / foxmail / icloud passam todos pela sua lista de permissões. O cadastro é e-mail + código atrás de um Turnstile, o login é senha simples, e o token da conta carrega `unlimited_quota` no grupo padrão.

O Turnstile é o portão crítico: seu token está vinculado à saída que o resolveu, então a ferramenta conduz um Chrome real através de uma saída verificada e lê o próprio widget do painel — um token resolvido em qualquer outro lugar responde `Turnstile 校验失败`. Um detalhe medido do Chrome faz parte do trilho: a porta do DevTools deve ser diferente de zero (`--remote-debugging-port=0` muda `navigator.webdriver` para true e o widget então morre com erro `600010`); a CLI escolhe uma porta livre diferente de zero por conta própria.

> ⚠️ **Nota do operador (medida, anúncio #53, 2026-09-30):** o painel afirma que uma pessoa não pode executar múltiplas contas e diz que baniu conjuntos de contas vinculadas. Trate cada conta como uma unidade de risco; esta ferramenta usa por padrão **uma conta por execução**.

## 🎁 Nível gratuito — quanto você recebe

| O quê | Valor |
| --- | --- |
| Cota da conta | `999,999,999` em `/api/user/self` (visão ilimitada no grupo padrão) |
| `quota_per_unit` | 500,000 |
| Cadastro | e-mail + código de 6 caracteres + Turnstile (token de uso único por etapa) |
| Política de e-mail | gmail, outlook, hotmail, **163, qq, 126, foxmail**, icloud aceitos; sina/aliyun/inboxbear rejeitados (`EMAIL_ADDRESS_REJECTED` — inboxbear remedido em 2026-10-21) |
| Check-in | `/api/user/checkin` existe mas é protegido por Turnstile; a folga de cota o torna opcional |
| Política de múltiplas contas | **anúncio #53: o uso de múltiplas contas pode resultar em banimento** — veja a nota do operador acima |

> 💡 O nível gratuito é definido pela HCNSec e pode mudar. Confira os números ao vivo em [api.hcnsec.cn](https://api.hcnsec.cn) — e deixe o próprio portão da ferramenta decidir:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modelos — todos os 16

`GET /v1/models` retornou **16 ids** em uma chave ativa (2026-10-21):

`auto` · `DeepSeek-V4-Flash` · `DeepSeek-V4-Pro` · `DeepSeek-V4.1-Flash` · `glm-5.3` · `glm-5.3-flash` · `longcat-2.5` · `MiMo-V2.6-Flash` · `MiniCPM-V-4.7-35B-A3B` · `MiniMax-M3.1-Flash` · `Qwen3-Embedding-8B` · `Qwen3.8-27B` · `Qwen3.8-Flash-Next` · `sensenova-6.8-flash-lite` · `sensenova-u1.5-lite` · `step-5-preview`

Uma conclusão ao vivo respondeu nos modelos de verificação (`content: "ok"`):

| ID do modelo | Status |
| --- | --- |
| `MiMo-V2.6-Flash` | **200** — o portão de chat padrão da ferramenta |
| `longcat-2.5` | **200** |
| `step-5-preview`, `Qwen3.8-Flash-Next`, `glm-5.3-flash`, `DeepSeek-V4-Flash` | **200** (execução anterior, mesma geração de painel) |

```bash
curl https://api.hcnsec.cn/v1/chat/completions \
  -H "Authorization: Bearer Hjcop6…" -H "Content-Type: application/json" \
  -d '{"model":"MiMo-V2.6-Flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 Como usar a API

URL base **`https://api.hcnsec.cn/v1`** — compatível com OpenAI. As chaves funcionam tanto sem prefixo quanto com o prefixo `sk-` (ambos testados 200 em `/v1/models`); o arquivo carrega a forma revelada sem prefixo.

```python
from openai import OpenAI
c = OpenAI(base_url="https://api.hcnsec.cn/v1", api_key="Hjcop6…")
print(c.chat.completions.create(model="MiMo-V2.6-Flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 Início rápido

```bash
cd hcnsec-bulk-apikey
printf 'socks5://127.0.0.1:1080\n' > proxies.txt   # one exit per line — see the proxy note
node src/launcher.js

# or directly:
node src/cli.js --count 1        # one account per run, by policy
node src/cli.js --verify-only    # re-test every key already in keys.txt
node src/cli.js --screen         # test the exit list against the panel
```

O cadastro roda em um Chrome real conduzido através da sua saída (o token do Turnstile está vinculado a ela); `keys.txt` só é escrito após `GET /v1/models` + uma conclusão real, e `accounts.json` mantém o estado da conta.

### 🖱️ O launcher

O launcher é o caminho de um comando só: ele pergunta quantas chaves você quer (Enter = `10`) e executa a CLI com `--auto`; `proxies.txt` é detectado automaticamente quando existe, caso contrário a execução vai direto. Ele também reverifica cada chave, testa sua lista de saídas, mostra as configurações memorizadas, conecta chaves ao 9Router / OmniRoute — e verifica o Chrome de antemão, para que um navegador ausente seja reportado antes de uma execução começar.

### 🚀 Um comando

```bash
node src/launcher.js       # menu → "1) Mint keys" → how many keys? [10]
node src/cli.js --count 10 # the same run, non-interactive
```

Essa é a ferramenta inteira: `--auto` está ativado por padrão — o Turnstile é resolvido no trilho de navegador embutido (um Chrome real através de uma saída fixada de `proxies.txt`, ou DIRECT quando não há nenhuma) e uma chave só chega a `keys.txt` após seu próprio chat ao vivo 200. `--dry` imprime o plano resolvido — contagem, saídas, solucionador, modelo — e sai sem tocar em um arquivo ou na rede:

```bash
node src/cli.js --count 10 --dry
```

> ⚠️ **Termos e risco de conta.** Automatizar um cadastro de terceiros é sua decisão e sua responsabilidade, não deste repositório. Leia os termos da HCNSec antes de executar isto e respeite-os: o uso de múltiplas contas é explicitamente passível de banimento lá (anúncio #53), uma frota que chame atenção pode ser banida por inteiro, e os portões de e-mail/captcha podem mudar sem aviso. Crie apenas o que você realmente usa.

## ✅ Verificado e funcionando — cada chave é testada

Todas as verificações abaixo foram reexecutadas em **2026-10-21** a partir da CLI distribuída (saída do pool: WARP sobre `socks5h://127.0.0.1:40000`).

| Verificação | Resultado |
| --- | --- |
| `--screen` (verificação de saída) | **✔ 1.3 s** — `/api/status` 200 + `challenges.cloudflare.com/turnstile/v0/api.js` 200 com redirecionamentos |
| trilho de navegador (Chrome real) | token Turnstile na página **752 caracteres**, `navigator.webdriver:false` — e o painel **aceitou** ele: `/api/verification` respondeu a política de domínio de e-mail (`EMAIL_ADDRESS_REJECTED`), não uma falha de Turnstile |
| `GET /api/status` | **200** — registro ativado, verificação de e-mail ativada, turnstile ativado, checkin ativado |
| `POST /api/user/register` | **200** `{"message":"","success":true}` (e-mail + código + token novo) |
| `POST /api/user/login` | **200** (token de acesso; senha simples) |
| `POST /api/token/` + revelação | **200** — `unlimited_quota` no grupo padrão |
| `GET /api/user/self` | **200** `quota=999999999` grupo `default` |
| `GET /v1/models` | **200, 16 modelos** |
| `--verify-only` ×2 chaves | **2/2 chat-200** em `MiMo-V2.6-Flash` (`"ok"`) através do pool |
| execução de dia de mudança | cada chave em `keys.txt`, cada uma escrita apenas após seu próprio chat 200 |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

> **Portões de qualidade.** Cada chave aqui passou por um `chat-200` ao vivo antes de ser escrita em `keys.txt`
> (chaves mortas nunca são entregues). As combinações de roteador seguem a mesma regra no nível do modelo: uma entrada é
> adicionada **apenas se o modelo responder `chat-200` e puder chamar ferramentas** — um modelo sem suporte a ferramentas,
> uma chave morta ou um provedor morto é descartado em vez de adicionado. Reexecute a etapa de verificação a qualquer momento;
> as falhas são reportadas, nunca escondidas.

## 🧭 Como funciona

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: HCNSec page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.hcnsec.cn/v1/chat/completions → real content = the key is live
```

| Etapa | Chamada | Notas |
| --- | --- | --- |
| verificação | `GET /api/status` + o script do Turnstile | uma saída que não consegue carregar `challenges.cloudflare.com` é ignorada antes de qualquer outra coisa |
| navegador | painel `/register` em Chrome real através da saída | o widget na página responde com um token de 700+ caracteres; o token está vinculado ao IP dessa saída |
| enviar código | `GET /api/verification?email=…&turnstile=<fresh>` | a lista de permissões se aplica após o token |
| registrar | `POST /api/user/register?turnstile=<fresh>` | e-mail + código + senha |
| login | `POST /api/user/login?turnstile=<fresh>` | senha simples (criptografia desativada) |
| chave | `POST /api/token/` (+ salto de revelação) | escrita em disco uma vez |
| prova | `GET /v1/models` + uma conclusão real | uma chave que falha nunca é escrita |

Cada chamada ao painel carrega seu próprio token de uso único novo; um envio limitado por taxa recua 65 s, e um endereço já em uso recebe uma caixa de correio totalmente nova.

## ⚙️ Opções da CLI

```bash
node src/cli.js --count 1          # mint accounts (one per run, by policy)
node src/cli.js --verify-only      # re-verify every key in keys.txt
node src/cli.js --screen           # exit pool self-test (status + Turnstile gates)
node src/cli.js --connect-9router  # register the provider + a key on a local 9Router
node src/cli.js --help
```

| Flag | Padrão | Significado |
| --- | --- | --- |
| `--count N` | `1` | contas a criar, em série |
| `--model <id>` | `MiMo-V2.6-Flash` | modelo usado para o portão ao vivo |
| `--pool <file>` | `proxies.txt` | pool de saídas, uma linha por proxy |
| `--tries N` | `3` | candidatos de saída verificados por conta |
| `--gap MS` | `4000` | pausa entre contas |
| `--code-timeout S` | `300` | segundos para esperar pelo código de e-mail |
| `--keys <file>` | `keys.txt` | arquivo de chaves para `--verify-only` |
| `--debug-port N` | porta livre | porta do Chrome DevTools — deve ser diferente de zero (veja a nota do porquê) |
| `--browser <exe>` | auto | caminho do binário do Chrome |
| `--auto` | `on` | resolve o Turnstile no trilho de navegador embutido (padrão) |
| `--dry` | `off` | imprime o plano resolvido e sai — sem gravação de arquivos, sem requisições |

| Env | Padrão | Significado |
| --- | --- | --- |
| `HCN_PROXIES` | `proxies.txt` | pool de saídas, uma linha por proxy |
| `HCN_MODEL` | `MiMo-V2.6-Flash` | modelo para a verificação ao vivo |
| `CHROME_PATH` | auto | caminho do binário do Chrome |

## 🛰️ Atrás de um proxy

**Fortemente recomendado.** A ferramenta funciona direto onde a rede permite, mas o Turnstile deste painel precisa de uma classe de saída saudável (uma saída residencial / WARP foi medida funcionando de ponta a ponta; saídas gratuitas de curta duração morreram em menos de um minuto). Com `--pool` cada requisição — incluindo o navegador — passa por uma saída da lista, fail-closed: cada candidato é verificado primeiro (`/api/status` + o script do Turnstile com redirecionamentos) e o token então nasce na saída que o usará. Sem um pool, a execução vai direto e o widget geralmente nunca monta.

## 📄 Arquivos

| Caminho | Conteúdo |
| --- | --- |
| `keys.txt` | chaves verificadas ao vivo — ignorado pelo git |
| `accounts.json` | estado da conta, incluindo tentativas falhas |
| `proxies.txt` | suas saídas — ignorado pelo git |
| `keys-dead.txt` | chaves que pararam de responder a `--verify-only` — ignorado pelo git |
| `out/` | logs de execução, capturas de falhas, registro `attempts.jsonl` — ignorado pelo git |

## 🔌 Conectar ao 9Router

[9Router](https://9router.com) é um roteador auto-hospedado, compatível com OpenAI:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Padrões: `--prefix hcn`, `--base-url https://api.hcnsec.cn/v1`, `--model MiMo-V2.6-Flash`. Os modelos são servidos como `hcn/<model id>`. A CLI pode fazer o mesmo em um único comando: `node src/cli.js --connect-9router`.

## 🔌 Conectar ao OmniRoute

[OmniRoute](https://omniroute.online) é um roteador auto-hospedado, compatível com OpenAI:

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

Padrões: `--prefix hcn`, `--base-url https://api.hcnsec.cn/v1`, `--model MiMo-V2.6-Flash`. Os modelos são servidos como `hcn/<model id>`. A CLI pode fazer o mesmo em um único comando: `node src/cli.js --connect-omniroute`.

## 🔑 O que uma chave te dá

| | |
| --- | --- |
| 🏢 Provedor | **HCNSec** — [api.hcnsec.cn](https://api.hcnsec.cn) |
| 🎁 O que o provedor dá | uma conta com visão `unlimited_quota` no grupo padrão, 16 modelos |
| 🔑 O que uma chave te dá | uma chave em sua própria conta: os ids verificados em `https://api.hcnsec.cn/v1` |
| 🌐 Formato da API | compatível com OpenAI: `POST https://api.hcnsec.cn/v1/chat/completions` |
| 🔒 Seus dados | apenas `api.hcnsec.cn` e seu backend de caixa de correio são contatados |

## 🐞 Problemas

Se a HCNSec mudar seus portões de registro, a sitekey do Turnstile ou a lista de permissões de e-mail, a ferramenta para ali e avisa. **[Abra uma issue](../../issues)** com o comando e a saída exata.

## 📄 Licença

MIT © 2026 — veja [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 hcnsec-bulk-apikey contributors

</div>