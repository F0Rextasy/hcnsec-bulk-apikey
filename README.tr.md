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

**HCNSec hesapları oluşturun — panelin kendi politikasına göre, her çalıştırmada bir tane — otomatik oluşturulan API anahtarlarıyla: OpenAI uyumlu çıkarım + 9Router bağlayıcısı.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-16%20served-412991?style=for-the-badge)](https://api.hcnsec.cn)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Hızlı başlangıç](#-quick-start) · [🎁 Ücretsiz katman](#-free-tier--how-much-you-get) · [🤖 Modeller](#-models--all-16-of-them) · [🧪 API'yi kullanma](#-how-to-use-the-api) · [✅ Doğrulandı](#-verified-working--every-key-is-tested) · [🧭 Nasıl çalışır](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Seçenekler](#️-cli-options) · [🛰️ Proxy'ler](#️-behind-a-proxy) · [🐞 Sorunlar](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Bu neden var

HCNSec (新疆幻城网安科技公益大模型安全网关), **16 model kimliğine** sahip bir New API panelidir (canlı bir anahtarda 2026-10-21 tarihinde ölçüldü) ve alışılmadık derecede eksiksiz bir e-posta politikasına sahiptir: gmail / outlook / hotmail / 163 / qq / 126 / foxmail / icloud hepsi beyaz listesinden geçer. Kayıt, bir Turnstile arkasında e-posta + koddur, giriş düz paroladır ve hesabın token'ı varsayılan grupta `unlimited_quota` taşır.

Turnstile yük taşıyan kapıdır: token'ı onu çözen çıkışa bağlıdır, bu yüzden araç gerçek bir Chrome'u doğrulanmış tek bir çıkış üzerinden sürer ve panelin kendi widget'ını okur — başka bir yerde çözülen bir token `Turnstile 校验失败` yanıtı verir. Ölçülen bir Chrome ayrıntısı rayın parçasıdır: DevTools portu sıfırdan farklı olmalıdır (`--remote-debugging-port=0`, `navigator.webdriver` değerini true'ya çevirir ve widget ardından `600010` hatasıyla ölür); CLI kendisi boş, sıfırdan farklı bir port seçer.

> ⚠️ **Operatör notu (ölçüldü, duyuru #53, 2026-09-30):** panel, bir kişinin birden fazla hesap çalıştıramayacağını belirtir ve bağlantılı hesap kümelerini yasakladığını söyler. Her hesabı bir risk birimi olarak değerlendirin; bu araç varsayılan olarak **her çalıştırmada bir hesap** kullanır.

## 🎁 Ücretsiz katman — ne kadar elde edersiniz

| Ne | Değer |
| --- | --- |
| Hesap kotası | `/api/user/self` üzerinde `999,999,999` (varsayılan grupta sınırsız görünüm) |
| `quota_per_unit` | 500,000 |
| Kayıt | e-posta + 6 karakterli kod + Turnstile (her adım için tek kullanımlık token) |
| E-posta politikası | gmail, outlook, hotmail, **163, qq, 126, foxmail**, icloud kabul edilir; sina/aliyun/inboxbear reddedilir (`EMAIL_ADDRESS_REJECTED` — inboxbear 2026-10-21 tarihinde yeniden ölçüldü) |
| Giriş (check-in) | `/api/user/checkin` mevcut ancak Turnstile korumalı; kota boşluğu bunu isteğe bağlı kılar |
| Çoklu hesap politikası | **duyuru #53: çoklu hesap kullanımı yasaklanabilir** — yukarıdaki operatör notuna bakın |

> 💡 Ücretsiz katman HCNSec tarafından belirlenir ve değişebilir. Canlı sayıları [api.hcnsec.cn](https://api.hcnsec.cn) adresinden kontrol edin — ve kararı aracın kendi kapısına bırakın:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modeller — 16'sının tamamı

`GET /v1/models`, canlı bir anahtarda **16 kimlik** döndürdü (2026-10-21):

`auto` · `DeepSeek-V4-Flash` · `DeepSeek-V4-Pro` · `DeepSeek-V4.1-Flash` · `glm-5.3` · `glm-5.3-flash` · `longcat-2.5` · `MiMo-V2.6-Flash` · `MiniCPM-V-4.7-35B-A3B` · `MiniMax-M3.1-Flash` · `Qwen3-Embedding-8B` · `Qwen3.8-27B` · `Qwen3.8-Flash-Next` · `sensenova-6.8-flash-lite` · `sensenova-u1.5-lite` · `step-5-preview`

Doğrulama modellerinde canlı bir tamamlama yanıt verdi (`content: "ok"`):

| Model Kimliği | Durum |
| --- | --- |
| `MiMo-V2.6-Flash` | **200** — aracın varsayılan sohbet kapısı |
| `longcat-2.5` | **200** |
| `step-5-preview`, `Qwen3.8-Flash-Next`, `glm-5.3-flash`, `DeepSeek-V4-Flash` | **200** (daha önceki çalıştırma, aynı panel nesli) |

```bash
curl https://api.hcnsec.cn/v1/chat/completions \
  -H "Authorization: Bearer Hjcop6…" -H "Content-Type: application/json" \
  -d '{"model":"MiMo-V2.6-Flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 API nasıl kullanılır

Temel URL **`https://api.hcnsec.cn/v1`** — OpenAI uyumlu. Anahtarlar hem çıplak hem de `sk-` önekiyle çalışır (her ikisi de `/v1/models` üzerinde 200 olarak test edildi); dosya çıplak açığa çıkarma biçimini taşır.

```python
from openai import OpenAI
c = OpenAI(base_url="https://api.hcnsec.cn/v1", api_key="Hjcop6…")
print(c.chat.completions.create(model="MiMo-V2.6-Flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 Hızlı başlangıç

```bash
cd hcnsec-bulk-apikey
printf 'socks5://127.0.0.1:1080\n' > proxies.txt   # one exit per line — see the proxy note
node src/launcher.js

# or directly:
node src/cli.js --count 1        # one account per run, by policy
node src/cli.js --verify-only    # re-test every key already in keys.txt
node src/cli.js --screen         # test the exit list against the panel
```

Kayıt, çıkışınız üzerinden sürülen gerçek bir Chrome'da çalışır (Turnstile token'ı ona bağlıdır); `keys.txt` yalnızca `GET /v1/models` + gerçek bir tamamlamadan sonra yazılır ve `accounts.json` hesap durumunu saklar.

### 🖱️ Başlatıcı

Başlatıcı tek komutlu yoldur: kaç anahtar istediğinizi sorar (Enter = `10`) ve CLI'yi `--auto` ile çalıştırır; `proxies.txt` mevcut olduğunda otomatik olarak alınır, aksi halde çalıştırma doğrudan gider. Ayrıca her anahtarı yeniden kontrol eder, çıkış listenizi tarar, hatırlanan ayarları gösterir, anahtarları 9Router / OmniRoute'a bağlar — ve baştan Chrome'u kontrol eder, böylece eksik bir tarayıcı bir çalıştırma başlamadan önce bildirilir.

### 🚀 Tek komut

```bash
node src/launcher.js       # menu → "1) Mint keys" → how many keys? [10]
node src/cli.js --count 10 # the same run, non-interactive
```

Aracın tamamı bu: `--auto` varsayılan olarak açıktır — Turnstile yerleşik tarayıcı rayında çözülür (`proxies.txt` içinden sabitlenmiş tek bir çıkış üzerinden gerçek bir Chrome veya hiç yoksa DIRECT) ve bir anahtar `keys.txt` dosyasına yalnızca kendi canlı sohbet 200'ünden sonra ulaşır. `--dry`, çözümlenen planı yazdırır — sayı, çıkışlar, çözücü, model — ve bir dosyaya veya ağa dokunmadan çıkar:

```bash
node src/cli.js --count 10 --dry
```

> ⚠️ **Koşullar ve hesap riski.** Üçüncü taraf bir kaydı otomatikleştirmek sizin kararınız ve sorumluluğunuzdur, bu deponun değil. Bunu çalıştırmadan önce HCNSec'in koşullarını okuyun ve bunlara uyun: orada çoklu hesap kullanımı açıkça yasaklanabilir (duyuru #53), fark edilen bir filo topluca yasaklanabilir ve e-posta/captcha kapıları önceden bildirilmeden değişebilir. Yalnızca gerçekten kullandığınız kadarını oluşturun.

## ✅ Çalıştığı doğrulandı — her anahtar test edilir

Aşağıdaki tüm kontroller **2026-10-21** tarihinde gönderilen CLI'dan yeniden çalıştırıldı (havuz çıkışı: `socks5h://127.0.0.1:40000` üzerinden WARP).

| Kontrol | Sonuç |
| --- | --- |
| `--screen` (çıkış denetimi) | **✔ 1.3 s** — `/api/status` 200 + `challenges.cloudflare.com/turnstile/v0/api.js` yönlendirmelerle 200 |
| tarayıcı rayı (gerçek Chrome) | sayfa içi Turnstile token'ı **752 karakter**, `navigator.webdriver:false` — ve panel bunu **kabul etti**: `/api/verification` bir Turnstile hatası değil, e-posta alan adı politikasını (`EMAIL_ADDRESS_REJECTED`) yanıtladı |
| `GET /api/status` | **200** — kayıt açık, e-posta doğrulama açık, turnstile açık, checkin açık |
| `POST /api/user/register` | **200** `{"message":"","success":true}` (e-posta + kod + taze token) |
| `POST /api/user/login` | **200** (erişim token'ı; düz parola) |
| `POST /api/token/` + açığa çıkarma | **200** — varsayılan grupta `unlimited_quota` |
| `GET /api/user/self` | **200** `quota=999999999` grup `default` |
| `GET /v1/models` | **200, 16 model** |
| `--verify-only` ×2 anahtar | havuz üzerinden `MiMo-V2.6-Flash` üzerinde **2/2 sohbet-200** (`"ok"`) |
| bayrak günü çalıştırması | `keys.txt` içindeki her anahtar, her biri yalnızca kendi sohbet 200'ünden sonra yazıldı |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

> **Kalite kapıları.** Buradaki her anahtar, `keys.txt` dosyasına yazılmadan önce canlı bir `chat-200` geçti
> (ölü anahtarlar asla gönderilmez). Yönlendirici kombinasyonları model düzeyinde aynı kuralı izler: bir giriş
> **yalnızca model `chat-200` yanıtlıyorsa ve araçları çağırabiliyorsa** eklenir — araç desteği olmayan bir model,
> ölü bir anahtar veya ölü bir sağlayıcı eklenmek yerine çıkarılır. Doğrulama adımını istediğiniz zaman yeniden çalıştırın;
> hatalar bildirilir, asla gizlenmez.

## 🧭 Nasıl çalışır

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: HCNSec page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.hcnsec.cn/v1/chat/completions → real content = the key is live
```

| Adım | Çağrı | Notlar |
| --- | --- | --- |
| denetim | `GET /api/status` + Turnstile betiği | `challenges.cloudflare.com` yükleyemeyen bir çıkış, başka her şeyden önce atlanır |
| tarayıcı | çıkış üzerinden gerçek Chrome'da panel `/register` | sayfa içi widget 700+ karakterlik bir token ile yanıt verir; token o çıkışın IP'sine bağlıdır |
| kod gönder | `GET /api/verification?email=…&turnstile=<fresh>` | beyaz liste token'dan sonra uygulanır |
| kayıt | `POST /api/user/register?turnstile=<fresh>` | e-posta + kod + parola |
| giriş | `POST /api/user/login?turnstile=<fresh>` | düz parola (şifreleme kapalı) |
| anahtar | `POST /api/token/` (+ açığa çıkarma atlaması) | diske bir kez yazılır |
| kanıt | `GET /v1/models` + gerçek bir tamamlama | başarısız olan bir anahtar asla yazılmaz |

Her panel çağrısı kendi taze tek kullanımlık token'ını taşır; kısıtlanan bir gönderim 65 s geri çekilir ve zaten kullanımda olan bir adres yepyeni bir posta kutusu alır.

## ⚙️ CLI seçenekleri

```bash
node src/cli.js --count 1          # mint accounts (one per run, by policy)
node src/cli.js --verify-only      # re-verify every key in keys.txt
node src/cli.js --screen           # exit pool self-test (status + Turnstile gates)
node src/cli.js --connect-9router  # register the provider + a key on a local 9Router
node src/cli.js --help
```

| Bayrak | Varsayılan | Anlam |
| --- | --- | --- |
| `--count N` | `1` | oluşturulacak hesap sayısı, seri |
| `--model <id>` | `MiMo-V2.6-Flash` | canlı kapı için kullanılan model |
| `--pool <file>` | `proxies.txt` | çıkış havuzu, proxy başına bir satır |
| `--tries N` | `3` | hesap başına denetlenen çıkış adayı |
| `--gap MS` | `4000` | hesaplar arası duraklama |
| `--code-timeout S` | `300` | e-posta kodunu bekleme süresi (saniye) |
| `--keys <file>` | `keys.txt` | `--verify-only` için anahtar dosyası |
| `--debug-port N` | boş port | Chrome DevTools portu — sıfırdan farklı olmalıdır (neden notuna bakın) |
| `--browser <exe>` | otomatik | Chrome ikili dosya yolu |
| `--auto` | `on` | Turnstile'ı yerleşik tarayıcı rayında çöz (varsayılan) |
| `--dry` | `off` | çözümlenen planı yazdır ve çık — dosya yazımı yok, istek yok |

| Ortam değişkeni | Varsayılan | Anlam |
| --- | --- | --- |
| `HCN_PROXIES` | `proxies.txt` | çıkış havuzu, proxy başına bir satır |
| `HCN_MODEL` | `MiMo-V2.6-Flash` | canlı doğrulama için model |
| `CHROME_PATH` | otomatik | Chrome ikili dosya yolu |

## 🛰️ Proxy arkasında

**Şiddetle önerilir.** Araç, ağın izin verdiği yerlerde doğrudan çalışır, ancak bu panelin Turnstile'ı sağlıklı bir çıkış sınıfı gerektirir (bir konut çıkışı / WARP uçtan uca çalışır olarak ölçüldü; kısa ömürlü ücretsiz çıkışlar bir dakika içinde öldü). `--pool` ile her istek — tarayıcı dahil — listeden bir çıkış üzerinden gider, hataya kapalı: her aday önce denetlenir (`/api/status` + yönlendirmelerle Turnstile betiği) ve token ardından onu kullanacak çıkışta doğar. Havuz olmadan çalıştırma doğrudan gider ve widget genellikle hiç bağlanmaz.

## 📄 Dosyalar

| Yol | İçerik |
| --- | --- |
| `keys.txt` | canlı doğrulanmış anahtarlar — git tarafından yok sayılır |
| `accounts.json` | başarısız denemeler dahil hesap durumu |
| `proxies.txt` | çıkışlarınız — git tarafından yok sayılır |
| `keys-dead.txt` | `--verify-only` yanıtını durduran anahtarlar — git tarafından yok sayılır |
| `out/` | çalıştırma günlükleri, hata görüntüleri, `attempts.jsonl` defteri — git tarafından yok sayılır |

## 🔌 9Router'a bağlan

[9Router](https://9router.com) kendi kendine barındırılan, OpenAI uyumlu bir yönlendiricidir:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Varsayılanlar: `--prefix hcn`, `--base-url https://api.hcnsec.cn/v1`, `--model MiMo-V2.6-Flash`. Modeller `hcn/<model id>` olarak sunulur. CLI aynısını tek seferde yapabilir: `node src/cli.js --connect-9router`.

## 🔌 OmniRoute'a bağlan

[OmniRoute](https://omniroute.online) kendi kendine barındırılan, OpenAI uyumlu bir yönlendiricidir:

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

Varsayılanlar: `--prefix hcn`, `--base-url https://api.hcnsec.cn/v1`, `--model MiMo-V2.6-Flash`. Modeller `hcn/<model id>` olarak sunulur. CLI aynısını tek seferde yapabilir: `node src/cli.js --connect-omniroute`.

## 🔑 Bir anahtar size ne kazandırır

| | |
| --- | --- |
| 🏢 Sağlayıcı | **HCNSec** — [api.hcnsec.cn](https://api.hcnsec.cn) |
| 🎁 Sağlayıcının verdikleri | varsayılan grupta `unlimited_quota` görünümlü bir hesap, 16 model |
| 🔑 Bir anahtarın size kazandırdıkları | kendi hesabında bir anahtar: `https://api.hcnsec.cn/v1` üzerinde doğrulanmış kimlikler |
| 🌐 API biçimi | OpenAI uyumlu: `POST https://api.hcnsec.cn/v1/chat/completions` |
| 🔒 Verileriniz | yalnızca `api.hcnsec.cn` ve gelen kutunuzun arka ucuyla iletişim kurulur |

## 🐞 Sorunlar

HCNSec kayıt kapılarını, Turnstile sitekey'ini veya e-posta beyaz listesini değiştirirse, araç orada durur ve bunu bildirir. Komut ve tam çıktıyla birlikte **[bir sorun açın](../../issues)**.

## 📄 Lisans

MIT © 2026 — bkz. [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 hcnsec-bulk-apikey katkıda bulunanlar

</div>