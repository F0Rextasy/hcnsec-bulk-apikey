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

**HCNSec アカウントを発行 — パネル自身のポリシーに従い、1回の実行につき1つ — 自動生成された API キー付き: OpenAI 互換の推論 + 9Router コネクタ。**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-16%20served-412991?style=for-the-badge)](https://api.hcnsec.cn)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 クイックスタート](#-quick-start) · [🎁 無料枠](#-free-tier--how-much-you-get) · [🤖 モデル](#-models--all-16-of-them) · [🧪 API の使い方](#-how-to-use-the-api) · [✅ 検証済み](#-verified-working--every-key-is-tested) · [🧭 仕組み](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ オプション](#️-cli-options) · [🛰️ プロキシ](#️-behind-a-proxy) · [🐞 問題報告](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ これが存在する理由

HCNSec（新疆幻城网安科技公益大模型安全网关）は **16 個のモデル ID** を持つ New API パネルであり（2026-10-21 に稼働中のキーで計測）、異例なほど完全なメールポリシーを備えています。gmail / outlook / hotmail / 163 / qq / 126 / foxmail / icloud はすべてホワイトリストを通過します。登録はメールアドレス + コードで、Turnstile の背後にあり、ログインは平文パスワード、そしてアカウントのトークンはデフォルトグループで `unlimited_quota` を持ちます。

Turnstile が要となるゲートです。そのトークンはそれを解いた出口に紐付けられるため、このツールは検証済みの1つの出口を介して実際の Chrome を操作し、パネル自身のウィジェットを読み取ります。他の場所で解かれたトークンは `Turnstile 校验失败` を返します。計測された Chrome の詳細の1つがこのレールの一部です。DevTools ポートは非ゼロでなければなりません（`--remote-debugging-port=0` は `navigator.webdriver` を true に反転させ、ウィジェットはエラー `600010` で死にます）。CLI は空いている非ゼロのポートを自身で選びます。

> ⚠️ **運用者向け注意（計測済み、お知らせ #53、2026-09-30）:** パネルは、1人が複数のアカウントを運用してはならないと述べており、紐付けられたアカウント群を禁止したとしています。すべてのアカウントをリスク単位として扱ってください。このツールはデフォルトで **1回の実行につき1アカウント** です。

## 🎁 無料枠 — どれだけ得られるか

| 項目 | 値 |
| --- | --- |
| アカウントクォータ | `/api/user/self` で `999,999,999`（デフォルトグループで無制限表示） |
| `quota_per_unit` | 500,000 |
| サインアップ | メールアドレス + 6文字コード + Turnstile（ステップごとに単回使用トークン） |
| メールポリシー | gmail、outlook、hotmail、**163、qq、126、foxmail**、icloud が受理。sina/aliyun/inboxbear は拒否（`EMAIL_ADDRESS_REJECTED` — inboxbear は 2026-10-21 に再計測） |
| チェックイン | `/api/user/checkin` は存在するが Turnstile でゲートされている。クォータの余裕があるため任意 |
| 複数アカウントポリシー | **お知らせ #53: 複数アカウントの使用は禁止対象** — 上記の運用者向け注意を参照 |

> 💡 無料枠は HCNSec が設定するもので、変更される可能性があります。最新の数値は [api.hcnsec.cn](https://api.hcnsec.cn) で確認してください — そしてツール自身のゲートに判断させてください:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 モデル — 全16個

`GET /v1/models` は稼働中のキーで **16 個の ID** を返しました（2026-10-21）:

`auto` · `DeepSeek-V4-Flash` · `DeepSeek-V4-Pro` · `DeepSeek-V4.1-Flash` · `glm-5.3` · `glm-5.3-flash` · `longcat-2.5` · `MiMo-V2.6-Flash` · `MiniCPM-V-4.7-35B-A3B` · `MiniMax-M3.1-Flash` · `Qwen3-Embedding-8B` · `Qwen3.8-27B` · `Qwen3.8-Flash-Next` · `sensenova-6.8-flash-lite` · `sensenova-u1.5-lite` · `step-5-preview`

稼働中の補完が検証モデルで応答しました（`content: "ok"`）:

| モデル ID | ステータス |
| --- | --- |
| `MiMo-V2.6-Flash` | **200** — ツールのデフォルトチャットゲート |
| `longcat-2.5` | **200** |
| `step-5-preview`、`Qwen3.8-Flash-Next`、`glm-5.3-flash`、`DeepSeek-V4-Flash` | **200**（以前の実行、同じパネル世代） |

```bash
curl https://api.hcnsec.cn/v1/chat/completions \
  -H "Authorization: Bearer Hjcop6…" -H "Content-Type: application/json" \
  -d '{"model":"MiMo-V2.6-Flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 API の使い方

ベース URL **`https://api.hcnsec.cn/v1`** — OpenAI 互換。キーは素のままでも `sk-` プレフィックス付きでも動作します（どちらも `/v1/models` で 200 を計測）。ファイルには素の公開形式が格納されます。

```python
from openai import OpenAI
c = OpenAI(base_url="https://api.hcnsec.cn/v1", api_key="Hjcop6…")
print(c.chat.completions.create(model="MiMo-V2.6-Flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 クイックスタート

```bash
cd hcnsec-bulk-apikey
printf 'socks5://127.0.0.1:1080\n' > proxies.txt   # one exit per line — see the proxy note
node src/launcher.js

# or directly:
node src/cli.js --count 1        # one account per run, by policy
node src/cli.js --verify-only    # re-test every key already in keys.txt
node src/cli.js --screen         # test the exit list against the panel
```

サインアップはあなたの出口を介して操作される実際の Chrome で実行されます（Turnstile トークンはそれに紐付けられます）。`keys.txt` は `GET /v1/models` + 実際の補完の後にのみ書き込まれ、`accounts.json` はアカウントの状態を保持します。

### 🖱️ ランチャー

ランチャーはプロキシリストを尋ね、それを記憶し、CLI を実行します — 発行、検証、出口のスクリーニング、設定、ヘルプ、9Router への接続。

## ✅ 検証済みで動作 — すべてのキーがテスト済み

以下のすべてのチェックは **2026-10-21** に、出荷された CLI から再実行されました（プール出口: `socks5h://127.0.0.1:40000` 経由の proxy）。

| チェック | 結果 |
| --- | --- |
| `--screen`（出口検証） | **✔ 1.3 秒** — `/api/status` 200 + `challenges.cloudflare.com/turnstile/v0/api.js` 200（リダイレクトあり） |
| ブラウザレール（実際の Chrome） | ページ内 Turnstile トークン **752 文字**、`navigator.webdriver:false` — そしてパネルはそれを **受理** しました: `/api/verification` は Turnstile の失敗ではなく、メールドメインポリシー（`EMAIL_ADDRESS_REJECTED`）を返しました |
| `GET /api/status` | **200** — 登録オン、メール検証オン、turnstile オン、チェックイン オン |
| `POST /api/user/register` | **200** `{"message":"","success":true}`（メールアドレス + コード + 新しいトークン） |
| `POST /api/user/login` | **200**（アクセストークン、平文パスワード） |
| `POST /api/token/` + 公開 | **200** — デフォルトグループで `unlimited_quota` |
| `GET /api/user/self` | **200** `quota=999999999` グループ `default` |
| `GET /v1/models` | **200、16 モデル** |
| `--verify-only` ×2 キー | プール経由で `MiMo-V2.6-Flash`（`"ok"`）に対して **2/2 チャット 200** |
| フラッグデイ実行 | `keys.txt` に **78 キー**、そのすべてが自身のチャット 200 の後にのみ書き込み |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 仕組み

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: HCNSec page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.hcnsec.cn/v1/chat/completions → real content = the key is live
```

| ステップ | 呼び出し | 備考 |
| --- | --- | --- |
| 検証 | `GET /api/status` + Turnstile スクリプト | `challenges.cloudflare.com` を読み込めない出口は、何よりも先にスキップされる |
| ブラウザ | 出口を介した実際の Chrome でのパネル `/register` | ページ内ウィジェットが 700 文字以上のトークンで応答する。トークンはその出口の IP に紐付けられる |
| コード送信 | `GET /api/verification?email=…&turnstile=<fresh>` | ホワイトリストはトークンの後に適用される |
| 登録 | `POST /api/user/register?turnstile=<fresh>` | メールアドレス + コード + パスワード |
| ログイン | `POST /api/user/login?turnstile=<fresh>` | 平文パスワード（暗号化オフ） |
| キー | `POST /api/token/`（+ 公開ホップ） | 一度だけディスクに書き込み |
| 証明 | `GET /v1/models` + 実際の補完 | 失敗したキーは決して書き込まれない |

すべてのパネル呼び出しは自身の新しい単回使用トークンを伴います。スロットルされた送信は 65 秒バックオフし、すでに使用中のアドレスには真新しいメールボックスが割り当てられます。

## ⚙️ CLI オプション

```bash
node src/cli.js --count 1          # mint accounts (one per run, by policy)
node src/cli.js --verify-only      # re-verify every key in keys.txt
node src/cli.js --screen           # exit pool self-test (status + Turnstile gates)
node src/cli.js --connect-9router  # register the provider + a key on a local 9Router
node src/cli.js --help
```

| フラグ | デフォルト | 意味 |
| --- | --- | --- |
| `--count N` | `1` | 発行するアカウント数、直列 |
| `--model <id>` | `MiMo-V2.6-Flash` | ライブゲートに使用するモデル |
| `--pool <file>` | `proxies.txt` | 出口プール、1行につき1プロキシ |
| `--tries N` | `3` | アカウントごとに検証する出口候補 |
| `--gap MS` | `4000` | アカウント間の一時停止 |
| `--code-timeout S` | `300` | メールコードを待つ秒数 |
| `--keys <file>` | `keys.txt` | `--verify-only` 用のキーファイル |
| `--debug-port N` | 空きポート | Chrome DevTools ポート — 非ゼロでなければならない（理由の注記を参照） |
| `--browser <exe>` | 自動 | Chrome バイナリのパス |

| 環境変数 | デフォルト | 意味 |
| --- | --- | --- |
| `HCN_PROXIES` | `proxies.txt` | 出口プール、1行につき1プロキシ |
| `HCN_MODEL` | `MiMo-V2.6-Flash` | ライブ検証用のモデル |
| `CHROME_PATH` | 自動 | Chrome バイナリのパス |

## 🛰️ プロキシの背後で

**強く推奨します。** このツールはネットワークが許す場所では直接動作しますが、このパネルの Turnstile は健全な出口クラスを必要とします（レジデンシャル出口 / proxy がエンドツーエンドで動作することが計測されました。短命な無料出口は1分以内に死にました）。`--pool` を使うと、すべてのリクエスト — ブラウザを含む — がリストからの出口を経由し、フェイルクローズドになります。各候補は最初に検証され（`/api/status` + リダイレクト付きの Turnstile スクリプト）、その後トークンはそれを使用する出口上で生まれます。プールなしの場合、実行は直接行われ、ウィジェットは通常マウントされません。

## 📄 ファイル

| パス | 内容 |
| --- | --- |
| `keys.txt` | ライブ検証済みキー — git で無視 |
| `accounts.json` | 失敗した試行を含むアカウント状態 |
| `proxies.txt` | あなたの出口 — git で無視 |
| `out/` | 実行ログ、失敗時のスクリーンショット、`attempts.jsonl` 台帳 — git で無視 |

## 🔌 9Router への接続

[9Router](https://9router.com) はセルフホスト型の OpenAI 互換ルーターです:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

デフォルト: `--prefix hcn`、`--base-url https://api.hcnsec.cn/v1`、`--model MiMo-V2.6-Flash`。モデルは `hcn/<model id>` として提供されます。CLI でも一度に同じことができます: `node src/cli.js --connect-9router`。

## 🔌 OmniRoute への接続

[OmniRoute](https://omniroute.online) はセルフホスト型の OpenAI 互換ルーターです:

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

デフォルト: `--prefix hcn`、`--base-url https://api.hcnsec.cn/v1`、`--model MiMo-V2.6-Flash`。モデルは `hcn/<model id>` として提供されます。CLI でも一度に同じことができます: `node src/cli.js --connect-omniroute`。
## 🔑 1つのキーで得られるもの

| | |
| --- | --- |
| 🏢 プロバイダー | **HCNSec** — [api.hcnsec.cn](https://api.hcnsec.cn) |
| 🎁 プロバイダーが提供するもの | デフォルトグループで `unlimited_quota` 表示のアカウント、16 モデル |
| 🔑 1つのキーで得られるもの | 独自のアカウント上の1つのキー: `https://api.hcnsec.cn/v1` 上の検証済み ID |
| 🌐 API の形式 | OpenAI 互換: `POST https://api.hcnsec.cn/v1/chat/completions` |
| 🔒 あなたのデータ | `api.hcnsec.cn` とあなたの受信トレイバックエンドのみが接触される |

## 🐞 問題報告

HCNSec が登録ゲート、Turnstile サイトキー、またはメールホワイトリストを変更した場合、ツールはそこで停止し、その旨を伝えます。コマンドと正確な出力を添えて **[issue を作成してください](../../issues)**。

## 📄 ライセンス

MIT © 2026 — [LICENSE](LICENSE) を参照。

<div align="center">

**[MIT](LICENSE)** © 2026 hcnsec-bulk-apikey contributors

</div>