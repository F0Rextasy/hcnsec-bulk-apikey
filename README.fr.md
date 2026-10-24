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

**Créez des comptes HCNSec — un par exécution, selon la propre politique du panel — avec des clés API générées automatiquement : inférence compatible OpenAI + connecteur 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-16%20served-412991?style=for-the-badge)](https://api.hcnsec.cn)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Démarrage rapide](#-quick-start) · [🎁 Niveau gratuit](#-free-tier--how-much-you-get) · [🤖 Modèles](#-models--all-16-of-them) · [🧪 Utiliser l'API](#-how-to-use-the-api) · [✅ Vérifié](#-verified-working--every-key-is-tested) · [🧭 Comment ça marche](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Options](#️-cli-options) · [🛰️ Proxys](#️-behind-a-proxy) · [🐞 Problèmes](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Pourquoi cela existe

HCNSec (新疆幻城网安科技公益大模型安全网关) est un panel New API avec **16 identifiants de modèles** (mesurés le 2026-10-21 sur une clé active) et une politique e-mail inhabituellement complète : gmail / outlook / hotmail / 163 / qq / 126 / foxmail / icloud passent tous sa liste blanche. L'inscription se fait par e-mail + code derrière un Turnstile, la connexion est un simple mot de passe, et le jeton du compte porte `unlimited_quota` sur le groupe par défaut.

Le Turnstile est la barrière porteuse : son jeton est lié à la sortie qui l'a résolu, donc l'outil pilote un vrai Chrome via une sortie validée et lit le widget du panel lui-même — un jeton résolu ailleurs répond `Turnstile 校验失败`. Un détail mesuré de Chrome fait partie du rail : le port DevTools doit être non nul (`--remote-debugging-port=0` fait basculer `navigator.webdriver` à true et le widget meurt alors avec l'erreur `600010`) ; le CLI choisit lui-même un port non nul libre.

> ⚠️ **Note pour l'opérateur (mesurée, annonce #53, 2026-09-30) :** le panel indique qu'une personne ne peut pas gérer plusieurs comptes et affirme avoir banni des ensembles de comptes liés. Considérez chaque compte comme une unité de risque ; cet outil utilise par défaut **un compte par exécution**.

## 🎁 Niveau gratuit — ce que vous obtenez

| Quoi | Valeur |
| --- | --- |
| Quota du compte | `999,999,999` sur `/api/user/self` (vue illimitée sur le groupe par défaut) |
| `quota_per_unit` | 500,000 |
| Inscription | e-mail + code à 6 caractères + Turnstile (jeton à usage unique par étape) |
| Politique e-mail | gmail, outlook, hotmail, **163, qq, 126, foxmail**, icloud acceptés ; sina/aliyun/inboxbear rejetés (`EMAIL_ADDRESS_REJECTED` — inboxbear re-mesuré le 2026-10-21) |
| Enregistrement | `/api/user/checkin` existe mais est protégé par Turnstile ; la marge de quota le rend facultatif |
| Politique multi-comptes | **annonce #53 : l'usage multi-comptes est bannissable** — voir la note pour l'opérateur ci-dessus |

> 💡 Le niveau gratuit est défini par HCNSec et peut changer. Consultez les chiffres en direct sur [api.hcnsec.cn](https://api.hcnsec.cn) — et laissez la propre barrière de l'outil décider :

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modèles — les 16 au complet

`GET /v1/models` a renvoyé **16 identifiants** sur une clé active (2026-10-21) :

`auto` · `DeepSeek-V4-Flash` · `DeepSeek-V4-Pro` · `DeepSeek-V4.1-Flash` · `glm-5.3` · `glm-5.3-flash` · `longcat-2.5` · `MiMo-V2.6-Flash` · `MiniCPM-V-4.7-35B-A3B` · `MiniMax-M3.1-Flash` · `Qwen3-Embedding-8B` · `Qwen3.8-27B` · `Qwen3.8-Flash-Next` · `sensenova-6.8-flash-lite` · `sensenova-u1.5-lite` · `step-5-preview`

Une complétion en direct a répondu sur les modèles de vérification (`content: "ok"`) :

| Identifiant du modèle | Statut |
| --- | --- |
| `MiMo-V2.6-Flash` | **200** — la barrière de chat par défaut de l'outil |
| `longcat-2.5` | **200** |
| `step-5-preview`, `Qwen3.8-Flash-Next`, `glm-5.3-flash`, `DeepSeek-V4-Flash` | **200** (exécution antérieure, même génération de panel) |

```bash
curl https://api.hcnsec.cn/v1/chat/completions \
  -H "Authorization: Bearer Hjcop6…" -H "Content-Type: application/json" \
  -d '{"model":"MiMo-V2.6-Flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 Comment utiliser l'API

URL de base **`https://api.hcnsec.cn/v1`** — compatible OpenAI. Les clés fonctionnent à la fois nues et avec un préfixe `sk-` (les deux testées 200 sur `/v1/models`) ; le fichier contient la forme révélée nue.

```python
from openai import OpenAI
c = OpenAI(base_url="https://api.hcnsec.cn/v1", api_key="Hjcop6…")
print(c.chat.completions.create(model="MiMo-V2.6-Flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 Démarrage rapide

```bash
cd hcnsec-bulk-apikey
printf 'socks5://127.0.0.1:1080\n' > proxies.txt   # one exit per line — see the proxy note
node src/launcher.js

# or directly:
node src/cli.js --count 1        # one account per run, by policy
node src/cli.js --verify-only    # re-test every key already in keys.txt
node src/cli.js --screen         # test the exit list against the panel
```

L'inscription s'exécute dans un vrai Chrome piloté via votre sortie (le jeton Turnstile y est lié) ; `keys.txt` n'est écrit qu'après `GET /v1/models` + une vraie complétion, et `accounts.json` conserve l'état du compte.

### 🖱️ Le lanceur

Le lanceur est le chemin en une commande : il demande combien de clés vous voulez (Entrée = `10`) et exécute le CLI avec `--auto` ; `proxies.txt` est pris en compte automatiquement lorsqu'il existe, sinon l'exécution se fait en direct. Il revérifie aussi chaque clé, teste votre liste de sorties, affiche les paramètres mémorisés, connecte les clés à 9Router / OmniRoute — et il vérifie Chrome au préalable, afin qu'un navigateur manquant soit signalé avant le début d'une exécution.

### 🚀 Une seule commande

```bash
node src/launcher.js       # menu → "1) Mint keys" → how many keys? [10]
node src/cli.js --count 10 # the same run, non-interactive
```

C'est tout l'outil : `--auto` est activé par défaut — le Turnstile est résolu dans le rail de navigateur intégré (un vrai Chrome via une sortie épinglée de `proxies.txt`, ou DIRECT lorsqu'il n'y en a pas) et une clé n'atteint `keys.txt` qu'après son propre chat 200 en direct. `--dry` affiche le plan résolu — nombre, sorties, solveur, modèle — et se termine sans toucher à un fichier ni au réseau :

```bash
node src/cli.js --count 10 --dry
```

> ⚠️ **Conditions et risque de compte.** Automatiser une inscription tierce est votre décision et votre responsabilité, pas celle de ce dépôt. Lisez les conditions de HCNSec avant d'exécuter ceci et respectez-les : l'usage multi-comptes y est explicitement bannissable (annonce #53), une flotte qui se fait remarquer peut être bannie en entier, et les barrières e-mail/captcha peuvent changer sans préavis. Ne créez que ce que vous utilisez réellement.

## ✅ Vérifié fonctionnel — chaque clé est testée

Toutes les vérifications ci-dessous ont été relancées le **2026-10-21** depuis le CLI livré (sortie du pool : WARP via `socks5h://127.0.0.1:40000`).

| Vérification | Résultat |
| --- | --- |
| `--screen` (validation de sortie) | **✔ 1.3 s** — `/api/status` 200 + `challenges.cloudflare.com/turnstile/v0/api.js` 200 avec redirections |
| rail de navigateur (vrai Chrome) | jeton Turnstile en page **752 caractères**, `navigator.webdriver:false` — et le panel l'a **accepté** : `/api/verification` a répondu selon la politique de domaine e-mail (`EMAIL_ADDRESS_REJECTED`), pas un échec Turnstile |
| `GET /api/status` | **200** — inscription activée, vérification e-mail activée, turnstile activé, checkin activé |
| `POST /api/user/register` | **200** `{"message":"","success":true}` (e-mail + code + jeton frais) |
| `POST /api/user/login` | **200** (jeton d'accès ; mot de passe en clair) |
| `POST /api/token/` + révélation | **200** — `unlimited_quota` sur le groupe par défaut |
| `GET /api/user/self` | **200** `quota=999999999` groupe `default` |
| `GET /v1/models` | **200, 16 modèles** |
| `--verify-only` ×2 clés | **2/2 chat-200** sur `MiMo-V2.6-Flash` (`"ok"`) via le pool |
| exécution de jour J | chaque clé dans `keys.txt`, chacune écrite uniquement après son propre chat 200 |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

> **Barrières de qualité.** Chaque clé ici a passé un `chat-200` en direct avant d'être écrite dans `keys.txt`
> (les clés mortes ne sont jamais livrées). Les combinaisons de routeur suivent la même règle au niveau du modèle : une entrée n'est
> ajoutée **que si le modèle répond `chat-200` et peut appeler des outils** — un modèle sans prise en charge des outils,
> une clé morte ou un fournisseur mort est écarté plutôt qu'ajouté. Relancez l'étape de vérification à tout moment ;
> les échecs sont signalés, jamais masqués.

## 🧭 Comment ça marche

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: HCNSec page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.hcnsec.cn/v1/chat/completions → real content = the key is live
```

| Étape | Appel | Remarques |
| --- | --- | --- |
| validation | `GET /api/status` + le script Turnstile | une sortie qui ne peut pas charger `challenges.cloudflare.com` est ignorée avant toute autre chose |
| navigateur | panel `/register` dans un vrai Chrome via la sortie | le widget en page répond avec un jeton de 700+ caractères ; le jeton est lié à l'IP de cette sortie |
| envoi du code | `GET /api/verification?email=…&turnstile=<fresh>` | la liste blanche s'applique après le jeton |
| inscription | `POST /api/user/register?turnstile=<fresh>` | e-mail + code + mot de passe |
| connexion | `POST /api/user/login?turnstile=<fresh>` | mot de passe en clair (chiffrement désactivé) |
| clé | `POST /api/token/` (+ étape de révélation) | écrite sur disque une seule fois |
| preuve | `GET /v1/models` + une vraie complétion | une clé qui échoue n'est jamais écrite |

Chaque appel au panel porte son propre jeton à usage unique frais ; un envoi limité en débit attend 65 s, et une adresse déjà utilisée obtient une toute nouvelle boîte mail.

## ⚙️ Options du CLI

```bash
node src/cli.js --count 1          # mint accounts (one per run, by policy)
node src/cli.js --verify-only      # re-verify every key in keys.txt
node src/cli.js --screen           # exit pool self-test (status + Turnstile gates)
node src/cli.js --connect-9router  # register the provider + a key on a local 9Router
node src/cli.js --help
```

| Option | Défaut | Signification |
| --- | --- | --- |
| `--count N` | `1` | comptes à créer, en série |
| `--model <id>` | `MiMo-V2.6-Flash` | modèle utilisé pour la barrière en direct |
| `--pool <file>` | `proxies.txt` | pool de sorties, une ligne par proxy |
| `--exits N` | `6` | candidats de sortie validés par compte |
| `--gap MS` | `4000` | pause entre les comptes |
| `--code-timeout S` | `300` | secondes d'attente du code e-mail |
| `--keys <file>` | `keys.txt` | fichier de clés pour `--verify-only` |
| `--debug-port N` | port libre | port Chrome DevTools — doit être non nul (voir la note explicative) |
| `--browser <exe>` | auto | chemin du binaire Chrome |
| `--auto` | `on` | résoudre le Turnstile dans le rail de navigateur intégré (par défaut) |
| `--dry` | `off` | afficher le plan résolu et quitter — aucune écriture de fichier, aucune requête |

| Env | Défaut | Signification |
| --- | --- | --- |
| `HCN_PROXIES` | `proxies.txt` | pool de sorties, une ligne par proxy |
| `HCN_MODEL` | `MiMo-V2.6-Flash` | modèle pour la vérification en direct |
| `CHROME_PATH` | auto | chemin du binaire Chrome |

## 🛰️ Derrière un proxy

**Fortement recommandé.** L'outil fonctionne en direct là où le réseau le permet, mais le Turnstile de ce panel nécessite une classe de sortie saine (une sortie résidentielle / WARP a été mesurée fonctionnelle de bout en bout ; les sorties gratuites éphémères mouraient en moins d'une minute). Avec `--pool`, chaque requête — le navigateur inclus — passe par une sortie de la liste, en échec fermé : chaque candidat est d'abord validé (`/api/status` + le script Turnstile avec redirections) et le jeton naît ensuite sur la sortie qui l'utilisera. Sans pool, l'exécution se fait en direct et le widget ne se monte généralement jamais.

## 📄 Fichiers

| Chemin | Contenu |
| --- | --- |
| `keys.txt` | clés vérifiées en direct — ignoré par git |
| `accounts.json` | état du compte, y compris les tentatives échouées |
| `proxies.txt` | vos sorties — ignoré par git |
| `keys-dead.txt` | clés qui ont cessé de répondre à `--verify-only` — ignoré par git |
| `out/` | journaux d'exécution, captures d'échec, registre `attempts.jsonl` — ignoré par git |

## 🔌 Connexion à 9Router

[9Router](https://9router.com) est un routeur auto-hébergé, compatible OpenAI :

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Défauts : `--prefix hcn`, `--base-url https://api.hcnsec.cn/v1`, `--model MiMo-V2.6-Flash`. Les modèles sont servis sous `hcn/<model id>`. Le CLI peut faire de même en une seule commande : `node src/cli.js --connect-9router`.

## 🔌 Connexion à OmniRoute

[OmniRoute](https://omniroute.online) est un routeur auto-hébergé, compatible OpenAI :

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

Défauts : `--prefix hcn`, `--base-url https://api.hcnsec.cn/v1`, `--model MiMo-V2.6-Flash`. Les modèles sont servis sous `hcn/<model id>`. Le CLI peut faire de même en une seule commande : `node src/cli.js --connect-omniroute`.

## 🔑 Ce qu'une clé vous apporte

| | |
| --- | --- |
| 🏢 Fournisseur | **HCNSec** — [api.hcnsec.cn](https://api.hcnsec.cn) |
| 🎁 Ce que le fournisseur donne | un compte avec vue `unlimited_quota` sur le groupe par défaut, 16 modèles |
| 🔑 Ce qu'une clé vous apporte | une clé sur son propre compte : les identifiants vérifiés sur `https://api.hcnsec.cn/v1` |
| 🌐 Forme de l'API | compatible OpenAI : `POST https://api.hcnsec.cn/v1/chat/completions` |
| 🔒 Vos données | seuls `api.hcnsec.cn` et votre backend de boîte mail sont contactés |

## 🐞 Problèmes

Si HCNSec modifie ses barrières d'inscription, la sitekey Turnstile ou la liste blanche e-mail, l'outil s'arrête là et le signale. **[Ouvrez un ticket](../../issues)** avec la commande et la sortie exacte.

## 📄 Licence

MIT © 2026 — voir [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 hcnsec-bulk-apikey contributors

</div>