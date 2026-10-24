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

**HCNSecアカウントを作成 — パネル自身のポリシーに従い、1回の実行につき1つ — 自動生成されたAPIキー付き: OpenAI互換の推論 + 9Routerコネクタ。**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-16%20served-412991?style=for-the-badge)](https://api.hcnsec.cn)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 クイックスタート](#-quick-start) · [🎁 無料枠](#-free-tier--how-much-you-get) · [🤖 モデル](#-models--all-16-of-them) · [🧪 APIの使い方](#-how-to-use-the-api) · [✅ 検証済み](#-verified-working--every-key-is-tested) · [🧭 仕組み](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ オプション](#️-cli-options) · [🛰️ プロキシ](#️-behind-a-proxy) · [🐞 問題報告](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ これが存在する理由

HCNSec (新疆幻城网安科技公益大模型安全网关) は **16個のモデルID** を持つNew APIパネルであり (2026-10-21にライブキーで計測)、異常なほど完全なメールポリシーを備えています: gmail / outlook / hotmail / 163 / qq / 126 / foxmail / icloud はすべてホワイトリストを通過します。登録はメール + コードでTurnstileの背後にあり、ログインは平文パスワードで、アカウントのトークンはデフォルトグループで `unlimited_quota` を持ちます。

Turnstileが要となるゲートです: そのトークンはそれを解いた出口に紐付けられているため、ツールは1つの検証済み出口を介して実際のChromeを操作し、パネル自身のウィジェットを読み取ります — 他の場所で解かれたトークンは `Turnstile 校验失败` を返します。計測されたChromeの詳細の1つがレールの一部です: DevToolsポートは非ゼロでなければなりません (`--remote-debugging-port=0` は `navigator.webdriver` をtrueに反転させ、ウィジェットはエラー `600010` で死にます); CLIは空いている非ゼロポートを自身で選びます。

> ⚠️ **運用者向け注意 (計測済み、お知らせ #53、2026-09-30):** パネルは1人が複数のアカウントを運用してはならないと述べており、リンクされたアカウント群をBANしたと述べています。すべてのアカウントをリスク単位として扱ってください; このツールはデフォルトで **1回の実行につき1アカウント** です。

## 🎁 無料枠 — どれだけ得られるか

| 項目 | 値 |
| --- | --- |
| アカウントクォータ | `/api/user/self` で `999,999,999` (デフォルトグループで無制限表示) |
| `quota_per_unit` | 500,000 |
| 登録 | メール + 6文字コード + Turnstile (ステップごとに単一使用トークン) |
| メールポリシー | gmail、outlook、hotmail、**163、qq、126、foxmail**、icloudが許可; sina/aliyun/inboxbearは拒否 (`EMAIL_ADDRESS_REJECTED` — inboxbearは2026-10-21に再計測) |
| チェックイン | `/api/user/checkin` は存在するがTurnstileでゲートされている; クォータの余裕により任意 |
| 複数アカウントポリシー | **お知らせ #53: 複数アカウントの使用はBAN対象** — 上記の運用者向け注意を参照 |

> 💡 無料枠はHCNSecによって設定され、変更される可能性があります。最新の数値は [api.hcnsec.cn](https://api.hcnsec.cn) で確認してください — そしてツール自身のゲートに判断を委ねてください:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 モデル — 全16個

`GET /v1/models` はライブキーで **16個のID** を返しました (2026-10-21):

`auto` · `DeepSeek-V4-Flash` · `DeepSeek-V4-Pro` · `DeepSeek-V4.1-Flash` · `glm-5.3` · `glm-5.3-flash` · `longcat-2.5` · `MiMo-V2.6-Flash` · `MiniCPM-V-4.7-35B-A3B` · `MiniMax-M3.1-Flash` · `Qwen3-Embedding-8B` · `Qwen3.8-27B` · `Qwen3.8-Flash-Next` · `sensenova-6.8-flash-lite` · `sensenova-u1.5-lite` · `step-5-preview`

ライブの補完が検証モデルで応答しました (`content: "ok"`):

| モデルID | ステータス |
| --- | --- |
| `MiMo-V2.6-Flash` | **200** — ツールのデフォルトチャットゲート |
| `longcat-2.5` | **200** |
| `step-5-preview`、`Qwen3.8-Flash-Next`、`glm-5.3-flash`、`DeepSeek-V4-Flash` | **200** (以前の実行、同じパネル世代) |

```bash
curl https://api.hcnsec.cn/v1/chat/completions \
  -H "Authorization: Bearer Hjcop6…" -H "Content-Type: application/json" \
  -d '{"model":"MiMo-V2.6-Flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 APIの使い方

ベースURL **`https://api.hcnsec.cn/v1`** — OpenAI互換。キーはベア形式でも `sk-` プレフィックス付きでも動作します (どちらも `/v1/models` で200を確認済み); ファイルにはベアの公開形式が格納されます。

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

登録はあなたの出口を介して操作される実際のChromeで実行されます (Turnstileトークンはそれに紐付けられています); `keys.txt` は `GET /v1/models` + 実際の補完の後にのみ書き込まれ、`accounts.json` はアカウントの状態を保持します。

### 🖱️ ランチャー

ランチャーはワンコマンドの経路です: いくつのキーが欲しいか尋ね (Enter = `10`)、`--auto` でCLIを実行します; `proxies.txt` が存在すれば自動的に読み込まれ、そうでなければ実行は直接接続になります。また、すべてのキーを再チェックし、出口リストをスクリーニングし、記憶された設定を表示し、キーを9Router / OmniRouteに接続します — そして事前にChromeをチェックするので、ブラウザの欠落は実行開始前に報告されます。

### 🚀 ワンコマンド

```bash
node src/launcher.js       # menu → "1) Mint keys" → how many keys? [10]
node src/cli.js --count 10 # the same run, non-interactive
```

これがツールのすべてです: `--auto` はデフォルトでオン — Turnstileは組み込みブラウザレールで解かれ (`proxies.txt` からの1つの固定された出口を介した実際のChrome、または存在しない場合はDIRECT)、キーは自身のライブチャット200の後にのみ `keys.txt` に到達します。`--dry` は解決されたプラン — カウント、出口、ソルバー、モデル — を出力し、ファイルやネットワークに触れることなく終了します:

```bash
node src/cli.js --count 10 --dry
```

> ⚠️ **利用規約とアカウントリスク。** 第三者のサインアップを自動化するのはあなたの判断と責任であり、このリポジトリのものではありません。これを実行する前にHCNSecの利用規約を読み、尊重してください: 複数アカウントの使用は明示的にBAN対象であり (お知らせ #53)、目をつけられた一群は丸ごとBANされる可能性があり、メール/キャプチャのゲートは予告なく変更される可能性があります。実際に使用する分だけを作成してください。

## ✅ 検証済みで動作 — すべてのキーがテストされています

以下のすべてのチェックは **2026-10-21** に出荷されたCLIから再実行されました (プール出口: `socks5h://127.0.0.1:40000` 経由のWARP)。

| チェック | 結果 |
| --- | --- |
| `--screen` (出口検証) | **✔ 1.3 s** — `/api/status` 200 + `challenges.cloudflare.com/turnstile/v0/api.js` 200 (リダイレクトあり) |
| ブラウザレール (実際のChrome) | ページ内Turnstileトークン **752文字**、`navigator.webdriver:false` — そしてパネルはそれを **受け入れました**: `/api/verification` はTurnstile失敗ではなく、メールドメインポリシー (`EMAIL_ADDRESS_REJECTED`) を返しました |
| `GET /api/status` | **200** — 登録オン、メール検証オン、turnstileオン、チェックインオン |
| `POST /api/user/register` | **200** `{"message":"","success":true}` (メール + コード + 新しいトークン) |
| `POST /api/user/login` | **200** (アクセストークン; 平文パスワード) |
| `POST /api/token/` + reveal | **200** — デフォルトグループで `unlimited_quota` |
| `GET /api/user/self` | **200** `quota=999999999` グループ `default` |
| `GET /v1/models` | **200、16モデル** |
| `--verify-only` ×2キー | プール経由で `MiMo-V2.6-Flash` (`"ok"`) で **2/2 chat-200** |
| フラッグデイ実行 | `keys.txt` 内のすべてのキー、それぞれ自身のチャット200の後にのみ書き込み |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

> **品質ゲート。** ここにあるすべてのキーは `keys.txt` に書き込まれる前にライブの `chat-200` を通過しました
> (死んだキーは決して出荷されません)。ルーターのコンボもモデルレベルで同じルールに従います: エントリは
> **モデルが `chat-200` を返し、ツールを呼び出せる場合にのみ** 追加されます — ツールサポートのないモデル、
> 死んだキー、死んだプロバイダは追加される代わりに削除されます。検証ステップはいつでも再実行してください;
> 失敗は報告され、決して隠されません。

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
| 検証 | `GET /api/status` + Turnstileスクリプト | `challenges.cloudflare.com` を読み込めない出口は他の何よりも前にスキップされます |
| ブラウザ | 出口を介した実際のChromeでパネル `/register` | ページ内ウィジェットが700文字以上のトークンで応答します; トークンはその出口のIPに紐付けられます |
| コード送信 | `GET /api/verification?email=…&turnstile=<fresh>` | ホワイトリストはトークンの後に適用されます |
| 登録 | `POST /api/user/register?turnstile=<fresh>` | メール + コード + パスワード |
| ログイン | `POST /api/user/login?turnstile=<fresh>` | 平文パスワード (暗号化オフ) |
| キー | `POST /api/token/` (+ revealホップ) | 一度だけディスクに書き込み |
| 証明 | `GET /v1/models` + 実際の補完 | 失敗したキーは決して書き込まれません |

すべてのパネル呼び出しは自身の新しい単一使用トークンを運びます; スロットルされた送信は65秒バックオフし、既に使用中のアドレスは真新しいメールボックスを取得します。

## ⚙️ CLIオプション

```bash
node src/cli.js --count 1          # mint accounts (one per run, by policy)
node src/cli.js --verify-only      # re-verify every key in keys.txt
node src/cli.js --screen           # exit pool self-test (status + Turnstile gates)
node src/cli.js --connect-9router  # register the provider + a key on a local 9Router
node src/cli.js --help
```

| フラグ | デフォルト | 意味 |
| --- | --- | --- |
| `--count N` | `1` | 作成するアカウント数、直列 |
| `--model <id>` | `MiMo-V2.6-Flash` | ライブゲートに使用するモデル |
| `--pool <file>` | `proxies.txt` | 出口プール、プロキシごとに1行 |
| `--exits N` | `6` | アカウントごとに検証する出口候補 |
| `--gap MS` | `4000` | アカウント間の一時停止 |
| `--code-timeout S` | `300` | メールコードを待つ秒数 |
| `--keys <file>` | `keys.txt` | `--verify-only` 用のキーファイル |
| `--debug-port N` | 空きポート | Chrome DevToolsポート — 非ゼロでなければなりません (理由の注記を参照) |
| `--browser <exe>` | 自動 | Chromeバイナリパス |
| `--auto` | `on` | 組み込みブラウザレールでTurnstileを解く (デフォルト) |
| `--dry` | `off` | 解決されたプランを出力して終了 — ファイル書き込みなし、リクエストなし |

| 環境変数 | デフォルト | 意味 |
| --- | --- | --- |
| `HCN_PROXIES` | `proxies.txt` | 出口プール、プロキシごとに1行 |
| `HCN_MODEL` | `MiMo-V2.6-Flash` | ライブ検証用のモデル |
| `CHROME_PATH` | 自動 | Chromeバイナリパス |

## 🛰️ プロキシの背後で

**強く推奨します。** ツールはネットワークが許す場所では直接動作しますが、このパネルのTurnstileは健全な出口クラスを必要とします (レジデンシャル出口 / WARPがエンドツーエンドで動作することが計測されました; 短命の無料出口は1分以内に死にました)。`--pool` を使用すると、すべてのリクエスト — ブラウザを含む — がリストからの出口を介して、フェイルクローズドで送られます: 各候補は最初に検証され (`/api/status` + リダイレクト付きのTurnstileスクリプト)、トークンはそれを使用する出口で生まれます。プールがない場合、実行は直接接続になり、ウィジェットは通常マウントされません。

## 📄 ファイル

| パス | 内容 |
| --- | --- |
| `keys.txt` | ライブ検証済みキー — git無視 |
| `accounts.json` | 失敗した試行を含むアカウント状態 |
| `proxies.txt` | あなたの出口 — git無視 |
| `keys-dead.txt` | `--verify-only` に応答しなくなったキー — git無視 |
| `out/` | 実行ログ、失敗時のスクリーンショット、`attempts.jsonl` 台帳 — git無視 |

## 🔌 9Routerへの接続

[9Router](https://9router.com) はセルフホストのOpenAI互換ルーターです:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

デフォルト: `--prefix hcn`、`--base-url https://api.hcnsec.cn/v1`、`--model MiMo-V2.6-Flash`。モデルは `hcn/<model id>` として提供されます。CLIでもワンショットで同じことができます: `node src/cli.js --connect-9router`。

## 🔌 OmniRouteへの接続

[OmniRoute](https://omniroute.online) はセルフホストのOpenAI互換ルーターです:

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

デフォルト: `--prefix hcn`、`--base-url https://api.hcnsec.cn/v1`、`--model MiMo-V2.6-Flash`。モデルは `hcn/<model id>` として提供されます。CLIでもワンショットで同じことができます: `node src/cli.js --connect-omniroute`。

## 🔑 1つのキーで得られるもの

| | |
| --- | --- |
| 🏢 プロバイダ | **HCNSec** — [api.hcnsec.cn](https://api.hcnsec.cn) |
| 🎁 プロバイダが提供するもの | デフォルトグループで `unlimited_quota` 表示のアカウント、16モデル |
| 🔑 1つのキーで得られるもの | 自身のアカウント上の1つのキー: `https://api.hcnsec.cn/v1` 上の検証済みID |
| 🌐 API形式 | OpenAI互換: `POST https://api.hcnsec.cn/v1/chat/completions` |
| 🔒 あなたのデータ | `api.hcnsec.cn` とあなたの受信トレイバックエンドのみが接触されます |

## 🐞 問題報告

HCNSecが登録ゲート、Turnstileサイトキー、またはメールホワイトリストを変更した場合、ツールはそこで停止し、その旨を伝えます。コマンドと正確な出力を添えて **[issueを開いてください](../../issues)**。

## 📄 ライセンス

MIT © 2026 — [LICENSE](LICENSE) を参照。

<div align="center">

**[MIT](LICENSE)** © 2026 hcnsec-bulk-apikey contributors

</div>