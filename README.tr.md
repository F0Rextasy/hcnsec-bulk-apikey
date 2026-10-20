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

**HCNSec hesapları oluşturun — panelin kendi politikasına göre her çalıştırmada bir tane — otomatik oluşturulan API anahtarlarıyla: OpenAI uyumlu çıkarım + 9Router bağlayıcısı.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-16%20served-412991?style=for-the-badge)](https://api.hcnsec.cn)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Hızlı başlangıç](#-quick-start) · [🎁 Ücretsiz katman](#-free-tier--how-much-you-get) · [🤖 Modeller](#-models--all-16-of-them) · [🧪 API'yi kullanma](#-how-to-use-the-api) · [✅ Doğrulandı](#-verified-working--every-key-is-tested) · [🧭 Nasıl çalışır](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Seçenekler](#️-cli-options) · [🛰️ Proxy'ler](#️-behind-a-proxy) · [🐞 Sorunlar](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Bu neden var

HCNSec (新疆幻城网安科技公益大模型安全网关), **16 model kimliğine** sahip bir New API panelidir (canlı bir anahtarda 2026-10-21 tarihinde ölçüldü) ve alışılmadık derecede eksiksiz bir e-posta politikasına sahiptir: gmail / outlook / hotmail / 163 / qq / 126 / foxmail / icloud hepsi beyaz listesinden geçer. Kayıt, bir Turnstile arkasında e-posta + koddur, giriş düz paroladır ve hesabın token'ı varsayılan grupta `unlimited_quota` taşır.

Turnstile yük taşıyan kapıdır: token'ı onu çözen çıkışa bağlıdır, bu yüzden araç doğrulanmış bir çıkış üzerinden gerçek bir Chrome'u çalıştırır ve panelin kendi widget'ını okur — başka bir yerde çözülen bir token `Turnstile 校验失败` yanıtı verir. Ölçülen bir Chrome ayrıntısı rayın bir parçasıdır: DevTools portu sıfırdan farklı olmalıdır (`--remote-debugging-port=0`, `navigator.webdriver` değerini true'ya çevirir ve widget ardından `600010` hatasıyla ölür); CLI kendisi boş, sıfırdan farklı bir port seçer.

> ⚠️ **Operatör notu (ölçüldü, duyuru #53, 2026-09-30):** panel, bir kişinin birden fazla hesap çalıştıramayacağını belirtir ve bağlantılı hesap gruplarını yasakladığını söyler. Her hesabı bir risk birimi olarak değerlendirin; bu araç varsayılan olarak **her çalıştırmada bir hesap** kullanır.

## 🎁 Ücretsiz katman — ne kadar alırsınız

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
node src/cli.js --verify-only     # keys.txt içindeki her anahtar gerçek bir sohbet çağrısıyla yeniden test edilir
```

## 🤖 Modeller — 16'sının tamamı

`GET /v1/models`, canlı bir anahtarda **16 kimlik** döndürdü (2026-10-21):

`auto` · `DeepSeek-V4-Flash` · `DeepSeek-V4-Pro` · `DeepSeek-V4.1-Flash` · `glm-5.3` · `glm-5.3-flash` · `longcat-2.5` · `MiMo-V2.6-Flash` · `MiniCPM-V-4.7-35B-A3B` · `MiniMax-M3.1-Flash` · `Qwen3-Embedding-8B` · `Qwen3.8-27B` · `Qwen3.8-Flash-Next` · `sensenova-6.8-flash-lite` · `sensenova-u1.5-lite` · `step-5-preview`

Canlı bir tamamlama, doğrulama modellerinde yanıt verdi (`content: "ok"`):

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
printf 'socks5://127.0.0.1:1080\n' > proxies.txt   # satır başına bir çıkış — proxy notuna bakın
node src/launcher.js
# veya doğrudan:
node src/cli.js --count 1        # politikaya göre her çalıştırmada bir hesap
node src/cli.js --verify-only    # keys.txt içinde zaten bulunan her anahtarı yeniden test et
node src/cli.js --screen         # çıkış listesini panele karşı test et
```

Kayıt, çıkışınız üzerinden yönetilen gerçek bir Chrome'da çalışır (Turnstile token'ı ona bağlıdır); `keys.txt` yalnızca `GET /v1/models` + gerçek bir tamamlamadan sonra yazılır ve `accounts.json` hesap durumunu saklar.

### 🖱️ Başlatıcı

Başlatıcı proxy listesini ister, onu hatırlar ve CLI'yi çalıştırır — oluştur, doğrula, çıkışları tara, ayarlar, yardım, 9Router'a Bağlan.

## ✅ Çalıştığı doğrulandı — her anahtar test edilir

Aşağıdaki tüm kontroller **2026-10-21** tarihinde gönderilen CLI'den yeniden çalıştırıldı (havuz çıkışı: `socks5h://127.0.0.1:40000` üzerinden WARP).

| Kontrol | Sonuç |
| --- | --- |
| `--screen` (çıkış doğrulama) | **✔ 1.3 s** — `/api/status` 200 + `challenges.cloudflare.com/turnstile/v0/api.js` yönlendirmelerle 200 |
| tarayıcı rayı (gerçek Chrome) | sayfa içi Turnstile token'ı **752 karakter**, `navigator.webdriver:false` — ve panel onu **kabul etti**: `/api/verification` Turnstile hatası değil, e-posta alan adı politikasını (`EMAIL_ADDRESS_REJECTED`) yanıtladı |
| `GET /api/status` | **200** — kayıt açık, e-posta doğrulama açık, turnstile açık, checkin açık |
| `POST /api/user/register` | **200** `{"message":"","success":true}` (e-posta + kod + taze token) |
| `POST /api/user/login` | **200** (erişim token'ı; düz parola) |
| `POST /api/token/` + açığa çıkarma | **200** — varsayılan grupta `unlimited_quota` |
| `GET /api/user/self` | **200** `quota=999999999` grup `default` |
| `GET /v1/models` | **200, 16 model** |
| `--verify-only` ×2 anahtar | havuz üzerinden `MiMo-V2.6-Flash` (`"ok"`) üzerinde **2/2 sohbet-200** |
| bayrak günü çalıştırması | `keys.txt` içinde **78 anahtar**, her biri yalnızca kendi sohbet 200'ünden sonra yazıldı |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 Nasıl çalışır

```
gelen kutusu (hesap başına bir adres)                            hesap başına taze bir adres
   │
   ├─▶ kayıt: HCNSec sayfası → e-posta adresi → gelen kutusundan kod / bağlantı
   ├─▶ oturum: hesabın çerezleri veya erişim token'ı elde
   ├─▶ anahtar oluşturma: hesabın anahtar uç noktası → düz metin anahtar bir kez döndürülür (hemen diske yazılır)
   └─▶ canlı kanıt: POST https://api.hcnsec.cn/v1/chat/completions → gerçek içerik = anahtar canlı
```

| Adım | Çağrı | Notlar |
| --- | --- | --- |
| doğrula | `GET /api/status` + Turnstile betiği | `challenges.cloudflare.com` yükleyemeyen bir çıkış, başka her şeyden önce atlanır |
| tarayıcı | çıkış üzerinden gerçek Chrome'da panel `/register` | sayfa içi widget 700+ karakterlik bir token ile yanıt verir; token o çıkışın IP'sine bağlıdır |
| kod gönder | `GET /api/verification?email=…&turnstile=<fresh>` | beyaz liste token'dan sonra uygulanır |
| kayıt | `POST /api/user/register?turnstile=<fresh>` | e-posta + kod + parola |
| giriş | `POST /api/user/login?turnstile=<fresh>` | düz parola (şifreleme kapalı) |
| anahtar | `POST /api/token/` (+ açığa çıkarma atlaması) | bir kez diske yazılır |
| kanıt | `GET /v1/models` + gerçek bir tamamlama | başarısız olan bir anahtar asla yazılmaz |

Her panel çağrısı kendi taze tek kullanımlık token'ını taşır; kısıtlanan bir gönderim 65 s geri çekilir ve zaten kullanımda olan bir adres yepyeni bir posta kutusu alır.

## ⚙️ CLI seçenekleri

```bash
node src/cli.js --count 1          # hesap oluştur (politikaya göre her çalıştırmada bir tane)
node src/cli.js --verify-only      # keys.txt içindeki her anahtarı yeniden doğrula
node src/cli.js --screen           # çıkış havuzu kendi kendini testi (durum + Turnstile kapıları)
node src/cli.js --connect-9router  # sağlayıcıyı + yerel bir 9Router üzerinde bir anahtar kaydet
node src/cli.js --help
```

| Bayrak | Varsayılan | Anlam |
| --- | --- | --- |
| `--count N` | `1` | oluşturulacak hesap sayısı, seri |
| `--model <id>` | `MiMo-V2.6-Flash` | canlı kapı için kullanılan model |
| `--pool <file>` | `proxies.txt` | çıkış havuzu, proxy başına bir satır |
| `--tries N` | `3` | hesap başına doğrulanan çıkış adayları |
| `--gap MS` | `4000` | hesaplar arası duraklama |
| `--code-timeout S` | `300` | e-posta kodunu bekleme saniyesi |
| `--keys <file>` | `keys.txt` | `--verify-only` için anahtar dosyası |
| `--debug-port N` | boş port | Chrome DevTools portu — sıfırdan farklı olmalıdır (neden notuna bakın) |
| `--browser <exe>` | otomatik | Chrome ikili dosya yolu |

| Ortam | Varsayılan | Anlam |
| --- | --- | --- |
| `HCN_PROXIES` | `proxies.txt` | çıkış havuzu, proxy başına bir satır |
| `HCN_MODEL` | `MiMo-V2.6-Flash` | canlı doğrulama için model |
| `CHROME_PATH` | otomatik | Chrome ikili dosya yolu |

## 🛰️ Proxy arkasında

**Şiddetle önerilir.** Araç, ağın izin verdiği yerlerde doğrudan çalışır, ancak bu panelin Turnstile'ı sağlıklı bir çıkış sınıfı gerektirir (bir konut çıkışı / WARP uçtan uca çalışır olarak ölçüldü; kısa ömürlü ücretsiz çıkışlar bir dakika içinde öldü). `--pool` ile her istek — tarayıcı dahil — listeden bir çıkış üzerinden gider, başarısızlıkta kapanır: her aday önce doğrulanır (`/api/status` + yönlendirmelerle Turnstile betiği) ve token ardından onu kullanacak çıkışta doğar. Havuz olmadan çalıştırma doğrudan gider ve widget genellikle hiç yüklenmez.

## 📄 Dosyalar

| Yol | İçerik |
| --- | --- |
| `keys.txt` | canlı doğrulanmış anahtarlar — git tarafından yok sayılır |
| `accounts.json` | başarısız denemeler dahil hesap durumu |
| `proxies.txt` | çıkışlarınız — git tarafından yok sayılır |
| `out/` | çalıştırma günlükleri, başarısızlık ekran görüntüleri, `attempts.jsonl` defteri — git tarafından yok sayılır |

## 🔌 9Router'a bağlan

[9Router](https://9router.com) kendi kendine barındırılan, OpenAI uyumlu bir yönlendiricidir:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Varsayılanlar: `--prefix hcn`, `--base-url https://api.hcnsec.cn/v1`, `--model MiMo-V2.6-Flash`. Modeller `hcn/<model id>` olarak sunulur. CLI aynısını tek seferde yapabilir: `node src/cli.js --connect-9router`.

## 🔑 Bir anahtar size ne kazandırır

| | |
| --- | --- |
| 🏢 Sağlayıcı | **HCNSec** — [api.hcnsec.cn](https://api.hcnsec.cn) |
| 🎁 Sağlayıcının verdikleri | varsayılan grupta `unlimited_quota` görünümüne sahip bir hesap, 16 model |
| 🔑 Bir anahtarın size kazandırdıkları | kendi hesabında bir anahtar: `https://api.hcnsec.cn/v1` üzerinde doğrulanmış kimlikler |
| 🌐 API biçimi | OpenAI uyumlu: `POST https://api.hcnsec.cn/v1/chat/completions` |
| 🔒 Verileriniz | yalnızca `api.hcnsec.cn` ve gelen kutusu arka ucunuzla iletişim kurulur |

## 🐞 Sorunlar

HCNSec kayıt kapılarını, Turnstile sitekey'ini veya e-posta beyaz listesini değiştirirse, araç orada durur ve bunu söyler. Komut ve tam çıktıyla birlikte **[bir sorun açın](../../issues)**.

## 📄 Lisans

MIT © 2026 — bkz. [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 hcnsec-bulk-apikey katkıda bulunanlar

</div>