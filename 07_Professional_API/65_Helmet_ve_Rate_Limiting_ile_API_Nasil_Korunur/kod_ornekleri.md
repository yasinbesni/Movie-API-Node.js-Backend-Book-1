# Bölüm 65 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Helmet ve Rate Limiting ile API Nasıl Korunur?

## CORS'tan Sonra Hangi Güvenlik İhtiyaçları Kaldı?

64. bölümde CORS'u ekledik.

Artık browser'ın farklı bir origin'deki Movie API Response'unu JavaScript'e verip vermeyeceğini kontrol edebiliyoruz.

Ancak CORS'un çözdüğü problem oldukça belirgindir:

```text
browser
↓
başka bir origin'den Response geldi
↓
JavaScript bu Response'u okuyabilir mi?
```

## Örnek 2

CORS bu soruya cevap verir.

Fakat API'mizde bunun dışında iki ayrı ihtiyaç daha vardır.

Birincisi, HTTP Response'larımızın bazı güvenlik kararlarını header'lar üzerinden daha bilinçli taşımasıdır.

İkincisi ise aynı client'ın çok kısa sürede aşırı sayıda Request göndermesi durumunda uygulamanın buna sınırsız biçimde devam etmemesidir.

Bu iki ihtiyacın akışı farklıdır:

```text
Response güvenlik header'ları
↓
Helmet

çok sık Request
↓
Rate Limiting
```

## Örnek 3

Bu bölümde bu iki katmanı Movie API'ye ekleyeceğiz. Önce Response tarafına bakalım.

---

## Response Tarafındaki Güvenlik Kararlarını Helmet ile Toplayalım

Postman'de şu Request'i gönderelim:

```http
GET http://localhost:3000/movies
```

## Örnek 4

Genellikle ilk baktığımız yer Response Body'dir.

Ama bir HTTP Response yalnızca Body'den oluşmaz. Response ayrıca **header** adı verilen ek bilgiler taşır.

Örneğin daha önce şunları gördük:

```text
Content-Type
Access-Control-Allow-Origin
```

## Örnek 5

Bu header'lar client'a veya browser'a Response hakkında ek bilgi verir.

Bazı header'lar güvenlikle ilgili davranışları da etkiler. Örneğin browser'a içeriğin türünü tahmin etmemesini veya belirli browser özelliklerini hangi politika ile kullanacağını söyleyen header'lar vardır.

Bunların her birini `server.js` içinde elle yazmak mümkün olsa da kısa sürede dağınık bir yapı oluşabilir.

İhtiyacımız şu:

```text
yaygın güvenlik header kararları
↓
tek tek elle yazılmak yerine
↓
merkezi bir Middleware üzerinden yönetilsin
```

## Örnek 6

Bu ihtiyacı karşılamak için **Helmet**, Express uygulamalarında kullanabileceğimiz uygun ve yaygın bir çözümdür.

### Helmet Response'ta Tam Olarak Ne Değiştirir?

Bir HTTP Response yalnızca JSON Body'den oluşmaz. Response ile birlikte browser'a bazı ek bilgiler de gönderilir:

```text
Response
├─ Body
└─ Headers
```

## Örnek 7

Bu header'ların bir kısmı browser'a **Response'u nasıl ele alması gerektiğiyle ilgili güvenlik talimatları** verir.

Örneğin şu header'ı düşünelim:

```http
X-Content-Type-Options: nosniff
```

## Örnek 8

Bu header browser'a kabaca şunu söyler:

```text
Sunucunun bildirdiği Content-Type'a bağlı kal.
İçeriğin türünü kendin tahmin etmeye çalışma.
```

## Örnek 9

Bir başka örnek:

```http
Referrer-Policy: no-referrer
```

## Örnek 10

Bu tür bir politika ise başka bir adrese giderken mevcut sayfanın adresinden ne kadar bilginin `Referer` header'ında gönderileceğini sınırlar.

Helmet ile birlikte sık göreceğimiz bir başka isim de şudur:

```text
Content-Security-Policy
```

## Örnek 11

Kısaca **CSP** olarak da adlandırılan bu header, browser'a bir sayfanın hangi kaynaklardan `script`, stil, görsel veya benzeri içerikleri yükleyebileceğini söyleyen güvenlik politikasıdır.

Örneğin HTML sayfası sunan bir uygulamada CSP ile:

```text
script'ler yalnızca izin verilen kaynaklardan yüklensin
↓
izin verilmeyen bir kaynaktan gelen içerik çalıştırılmasın
```

## Örnek 12

gibi sınırlar belirlenebilir.

Movie API'miz şu anda JSON Response ürettiği için bu bölümde ayrıntılı bir CSP politikası tasarlamayacağız. Burada bilmemiz gereken nokta, Helmet'in bu tür browser güvenlik kurallarını da HTTP header'ları üzerinden merkezi biçimde yönetebilmesidir.

Yani güvenlik header'ları yalnızca Response'a eklenen isimler değildir. Browser'ın bazı davranışlarını daha kontrollü hâle getiren **kuralları Response üzerinden iletirler**.

Bunları Express'te tek tek kendimiz de yazabiliriz:

```js
app.use((req, res, next) => {
  res.setHeader(
    "X-Content-Type-Options",
    "nosniff",
  );

  next();
});
```

## Örnek 13

Fakat güvenlikle ilgili birçok header'ı tek tek eklemek, doğru değerleri takip etmek ve gerektiğinde güncellemek kısa sürede gereksiz bir bakım yükü oluşturur.

Helmet'in yaptığı iş burada somutlaşır:

```text
Request gelir
↓
Helmet Middleware çalışır
↓
Response'a uygun güvenlik header'ları eklenir
ve bazı gereksiz bilgi veren header'lar kaldırılır
↓
Response browser'a gönderilir
```

## Örnek 14

Örneğin Helmet kullanıldığında sürüme ve ayarlara bağlı olarak `X-Content-Type-Options`, `Referrer-Policy`, `Content-Security-Policy` gibi header'lar Response'ta yer alabilir.

Burada bir de farklı görevde olan şu header vardır:

```http
X-Powered-By: Express
```

## Örnek 15

Express bu header ile Response'u hangi framework'ün ürettiğine dair bilgi verebilir. Uygulamanın çalışması için bu bilginin client'a gönderilmesi gerekmez.

Helmet varsayılan kullanımında `X-Powered-By` header'ını kaldırır:

```text
X-Powered-By: Express
↓
Helmet
↓
Response'tan kaldırılır
```

## Örnek 16

Bu işlem tek başına bir saldırıyı engellemez; amacı Response üzerinden **gereksiz teknoloji bilgisinin paylaşılmasını azaltmaktır**.

Böylece Helmet'in iki farklı türde işlem yaptığını görebiliriz:

```text
güvenlik davranışı belirleyen header'lar
↓
eklenir veya uygun değerlerle yönetilir

uygulama hakkında gereksiz bilgi veren header'lar
↓
kaldırılabilir
```

## Örnek 17

Bu nedenle Helmet'i şöyle düşünmek daha doğru olur:

```text
Helmet
↓
Request Body'yi doğrulamaz
kimlik doğrulaması yapmaz
Request sayısını sınırlamaz
↓
ama
↓
Response'un browser'a taşıdığı
güvenlik odaklı HTTP header'larını
merkezi biçimde ayarlar
```

## Örnek 18

Helmet uygulamayı tek başına güvenli hâle getirmez. Onun görevi daha belirgindir: **browser'ın Response'u işlerken kullanacağı bazı güvenlik kurallarını HTTP header'ları üzerinden uygulamaya eklemek ve bunları tek bir Middleware altında yönetmek.**

### Helmet'i Ekleyelim ve Farkı Gözlemleyelim

Terminalde:

```bash
npm install helmet
```

## Örnek 19

çalıştıralım.

`server.js` içinde:

```js
import helmet from "helmet";
```

## Örnek 20

ve ardından:

```js
app.use(helmet());
```

## Örnek 21

ekleyelim.

İlgili bölüm şu şekilde olabilir:

```js
export const app = express();

app.use(helmet());

app.use(
  cors({
    origin: env.corsOrigin,
  }),
);

app.use(express.json());
```

## Örnek 22

Server'ı yeniden başlatıp tekrar:

```http
GET /movies
```

## Örnek 23

Request'ini gönderelim.

Bu kez Postman'de **Response Headers** bölümüne bakalım.

Kullandığınız Helmet sürümüne göre örneğin şu header'lardan bazılarını görebilirsiniz:

```text
X-Content-Type-Options
Referrer-Policy
Cross-Origin-Opener-Policy
Content-Security-Policy
```

## Örnek 24

Express'in framework bilgisini taşıyan:

```text
X-Powered-By
```

## Örnek 25

header'ının kaldırıldığını da görebilirsiniz.

Buradaki önemli nokta tek tek header isimlerini ezberlemek değildir. Değişen davranışı görmek daha önemlidir:

```text
önce
↓
Response güvenlik header kararları dağınık veya varsayılan durumda

Helmet'ten sonra
↓
yaygın güvenlik header'ları merkezi bir Middleware üzerinden yönetiliyor
```

## Örnek 26

Helmet'in varsayılan ayarları iyi bir başlangıçtır; ancak her proje aynı ihtiyaçlara sahip değildir. Özellikle Content Security Policy gibi bazı ayarlar frontend yapısına göre özelleştirilebilir.

Movie API'miz şu anda JSON API olduğu için burada:

```js
app.use(helmet());
```

## Örnek 27

ile temel davranışı kullanmamız yeterlidir.

Helmet'i ekledik. Şimdi Response tarafındaki bu korumadan farklı bir probleme geçelim: **aynı client'ın çok kısa sürede aşırı sayıda Request göndermesi**.

---

## Çok Sık Request Geldiğinde Ne Olur?

Bu bölümde **client**, API'ye Request gönderen taraf anlamına gelir. Örneğin:

```text
browser
Postman
mobil uygulama
başka bir server
```

## Örnek 28

birer client olabilir.

Bir client'ın:

```http
GET /movies
```

## Örnek 29

Request'ini birkaç kez göndermesi normaldir.

Ama aynı client bu Request'i birkaç saniye içinde yüzlerce veya binlerce kez gönderirse her Request normal uygulama akışını başlatabilir:

```text
Request
↓
Middleware'ler
↓
Router
↓
Controller
↓
Service
↓
MongoDB Query
↓
Response
```

## Örnek 30

Bir Request'in maliyeti küçük olabilir. Fakat aynı iş çok kısa sürede tekrar tekrar çalıştırıldığında CPU, memory, network ve veritabanı bağlantıları üzerindeki toplam yük büyür.

Bu nedenle API'nin şu kurala ihtiyacı olabilir:

```text
aynı client
↓
belirli bir zaman aralığında
↓
belirli sayının üzerinde Request gönderemesin
```

## Örnek 31

Bu yaklaşıma **rate limiting** denir.

### Rate Limiting'i Bir Sayaç Gibi Düşünelim

Örneğin şu kuralı belirleyelim:

```text
60 saniye
↓
en fazla 100 Request
```

## Örnek 32

Client'ın Request'leri aynı zaman aralığı içinde sayılır.

```text
1. Request   → izin ver
2. Request   → izin ver
...
100. Request → izin ver
101. Request → sınır aşıldı
```

## Örnek 33

Limit aşıldığında API:

```http
429 Too Many Requests
```

## Örnek 34

Response'u döndürebilir.

Rate limiting'in iki temel değeri vardır:

```text
window
↓
Request'leri hangi zaman aralığında sayacağız?

limit
↓
bu zaman aralığında en fazla kaç Request'e izin vereceğiz?
```

## Örnek 35

Bizim örneğimizde:

```text
window = 60 saniye
limit = 100 Request
```

## Örnek 36

olacak.

Artık ihtiyacı bildiğimize göre bunu Express'e ekleyebiliriz.

---

## Rate Limiter'ı Projeye Ekleyelim

Terminalde:

```bash
npm install express-rate-limit
```

## Örnek 37

çalıştıralım.

Rate limiter için yeni dosyamız:

```text
src/middlewares/rateLimiter.js
```

## Örnek 38

olacak.

Ancak Middleware'i yazmadan önce iki değerin kaynağını netleştirelim:

```text
60 saniye
100 Request
```

## Örnek 39

Bu değerler uygulamanın kod mantığına ait değişmez bilgiler değildir. Ortama göre değişebilir.

Örneğin development sırasında test etmek için:

```text
3 Request / 60 saniye
```

## Örnek 40

kullanabiliriz.

Production ortamında ise:

```text
100 Request / 60 saniye
```

## Örnek 41

kullanmak isteyebiliriz.

63. bölümde config yapısını tam da bu tür değerler için kurmuştuk. Bu yüzden rate limit değerlerini de environment config üzerinden yöneteceğiz.

### Rate Limit Ayarlarını Config'e Ekleyelim

`.env` dosyasına:

```env
MONGODB_URL=...
PORT=3000
CORS_ORIGIN=http://localhost:5173
RATE_LIMIT_WINDOW_MS=60000
RATE_LIMIT_LIMIT=100
```

## Örnek 42

ekleyelim.

Burada:

```text
60000 ms
↓
60 saniye
```

## Örnek 43

anlamına gelir.

`example.env` dosyamızı da güncelleyelim:

```env
MONGODB_URL=your_mongodb_connection_string
PORT=3000
CORS_ORIGIN=http://localhost:5173
RATE_LIMIT_WINDOW_MS=60000
RATE_LIMIT_LIMIT=100
```

## Örnek 44

63. bölümden bildiğimiz önemli ayrıntı burada tekrar karşımıza çıkar: environment variable'lar String gelir.

Yani:

```env
RATE_LIMIT_LIMIT=100
```

## Örnek 45

olsa bile:

```js
process.env.RATE_LIMIT_LIMIT
```

## Örnek 46

şu değeri verir:

```text
"100"
```

## Örnek 47

Bu değeri rate limiter'a vermeden önce Number'a dönüştürüp doğrulamalıyız.

`src/config/env.js` içine küçük bir helper ekleyelim:

```js
const getPositiveIntegerEnv = (name) => {
  const value = getRequiredEnv(name);
  const number = Number(value);

  if (
    !Number.isSafeInteger(number) ||
    number <= 0
  ) {
    throw new Error(
      `${name} pozitif tam sayı olmalıdır.`,
    );
  }

  return number;
};
```

## Örnek 48

Bu helper:

```text
"100"
↓
100
```

## Örnek 49

dönüşümünü yapar.

Ama:

```text
"abc"
0
-5
```

## Örnek 50

gibi değerleri kabul etmez.

Şimdi `env` nesnesini genişletebiliriz:

```js
export const env = {
  mongodbUrl: getRequiredEnv(
    "MONGODB_URL",
  ),
  port: parsePort(process.env.PORT),
  corsOrigin: getRequiredEnv(
    "CORS_ORIGIN",
  ),
  rateLimitWindowMs:
    getPositiveIntegerEnv(
      "RATE_LIMIT_WINDOW_MS",
    ),
  rateLimitLimit:
    getPositiveIntegerEnv(
      "RATE_LIMIT_LIMIT",
    ),
};
```

## Örnek 51

Artık `rateLimiter.js`, `process.env` ayrıntılarıyla uğraşmak zorunda kalmaz. Ona doğrudan doğrulanmış config değerleri ulaşır.

---

## Rate Limiter Middleware'ini Yazalım

`src/middlewares/rateLimiter.js`:

```js
import { rateLimit } from "express-rate-limit";

import { env } from "../config/env.js";

export const apiRateLimiter = rateLimit({
  windowMs: env.rateLimitWindowMs,
  limit: env.rateLimitLimit,
  standardHeaders: "draft-8",
  legacyHeaders: false,
  handler: (req, res) => {
    return res.status(429).json({
      message:
        "Çok fazla Request gönderildi. Lütfen daha sonra tekrar deneyin.",
    });
  },
});
```

## Örnek 52

İlk bakışta burada birkaç yeni seçenek var. Hepsini aynı anda ezberlemek yerine her birinin akışta hangi kararı verdiğine bakalım.

### `windowMs` ve `limit`

```js
windowMs: env.rateLimitWindowMs,
limit: env.rateLimitLimit,
```

## Örnek 53

şu kuralı oluşturur:

```text
windowMs
↓
Request'leri ne kadar süre aynı pencere içinde sayacağız?

limit
↓
o pencere içinde kaç Request'e kadar izin vereceğiz?
```

## Örnek 54

Örneğin:

```env
RATE_LIMIT_WINDOW_MS=60000
RATE_LIMIT_LIMIT=100
```

## Örnek 55

ise:

```text
60 saniye içinde
↓
en fazla 100 Request
```

## Örnek 56

kuralını uyguluyoruz.

Paket varsayılan kullanımda Request gönderen client'ları IP bilgisine göre ayıran bir anahtar kullanır.

Basit zihinsel model:

```text
IP A
↓
kendi sayacı

IP B
↓
kendi sayacı
```

## Örnek 57

Gerçek deployment ortamlarında proxy veya load balancer gibi ağ katmanları IP bilgisinin Express tarafından nasıl görüldüğünü etkileyebilir. Bu bölümde bu ileri ağ yapılandırmalarına girmeyeceğiz; burada temel rate limiting akışını kuruyoruz.

### Limit Aşılırsa Neden `429` Dönüyoruz?

Limit aşıldığında:

```http
429 Too Many Requests
```

## Örnek 58

kullanıyoruz.

61. bölümde Response yapımızı:

```json
{
  "message": "..."
}
```

## Örnek 59

şeklinde tutarlı hâle getirmiştik.

Bu nedenle `express-rate-limit` paketinin varsayılan Response'u yerine kendi `handler` fonksiyonumuzu yazıyoruz:

```js
handler: (req, res) => {
  return res.status(429).json({
    message:
      "Çok fazla Request gönderildi. Lütfen daha sonra tekrar deneyin.",
  });
},
```

## Örnek 60

Burada yeni bir Response biçimi icat etmiyoruz. Önceki bölümde oluşturduğumuz API sözleşmesini rate limit hatasında da koruyoruz.

### Rate Limit Bilgisini Header'larda da Taşıyalım

Şu iki seçenek:

```js
standardHeaders: "draft-8",
legacyHeaders: false,
```

## Örnek 61

rate limit bilgisinin modern RateLimit header formatıyla gönderilmesini ve eski `X-RateLimit-*` header'larının kullanılmamasını sağlar.

Postman'de paket sürümüne göre örneğin:

```text
RateLimit
RateLimit-Policy
```

## Örnek 62

gibi header'lar görebilirsiniz.

Buradaki amaç header formatının bütün ayrıntılarını ezberlemek değildir. Client'ın limit hakkında Response üzerinden bilgi alabileceğini görmek yeterlidir.

---

## Middleware'i Request Akışında Doğru Yere Koyalım

Rate limiter hazır. Şimdi `server.js` içinde nereye yerleşeceğine karar vermeliyiz.

İlgili import'lar:

```js
import cors from "cors";
import express from "express";
import helmet from "helmet";

import { env } from "./config/env.js";
import { errorHandler } from "./middlewares/errorHandler.js";
import { apiRateLimiter } from "./middlewares/rateLimiter.js";
import { requestLogger } from "./middlewares/requestLogger.js";
import moviesRouter from "./routers/movies.js";
```

## Örnek 63

Middleware akışını şu şekilde kurabiliriz:

```js
export const app = express();

app.use(helmet());

app.use(
  cors({
    origin: env.corsOrigin,
  }),
);

app.use(requestLogger);
app.use(apiRateLimiter);
app.use(express.json());

app.use("/movies", moviesRouter);

app.use(errorHandler);
```

## Örnek 64

Şimdi bu sırayı Request açısından okuyalım:

```text
Request gelir
↓
Helmet
Response güvenlik header'larını yönetir
↓
CORS
cross-origin Response erişimi için header kararlarını uygular
↓
requestLogger
Request'i kaydeder
↓
apiRateLimiter
Request sayısı limiti aştı mı kontrol eder
↓
express.json()
JSON Body'yi parse eder
↓
/movies Router
normal API akışı devam eder
↓
errorHandler
Route akışında oluşan hataları karşılar
```

## Örnek 65

Burada iki yerleşim kararı özellikle anlamlıdır.

İlki:

```text
requestLogger
↓
rate limiter'dan önce
```

## Örnek 66

olduğu için limit nedeniyle reddedilen Request'i de loglarda görebiliriz.

İkincisi:

```text
rate limiter
↓
express.json()'dan önce
```

## Örnek 67

olduğu için limiti zaten aşmış bir Request'i JSON Body parse aşamasına geçmeden durdurabiliriz.

Bu sıra her Express projesi için değişmez bir kural değildir. Bizim Movie API'mizde görmek istediğimiz davranışla uyumludur.

---

## Rate Limiting'i Gerçek Request'lerle Doğrulayalım

`100` Request göndererek test yapmak gereksiz yere uzun sürer.

Development ortamında geçici olarak:

```env
RATE_LIMIT_WINDOW_MS=60000
RATE_LIMIT_LIMIT=3
```

## Örnek 68

kullanalım.

Server'ı yeniden başlatalım ve aynı endpoint'e kısa sürede dört Request gönderelim:

```http
GET /movies
```

## Örnek 69

Beklediğimiz akış:

```text
1. Request
↓
başarılı

2. Request
↓
başarılı

3. Request
↓
başarılı

4. Request
↓
429 Too Many Requests
```

## Örnek 70

Dördüncü Request'in Response Body'si:

```json
{
  "message": "Çok fazla Request gönderildi. Lütfen daha sonra tekrar deneyin."
}
```

## Örnek 71

olmalıdır.

Response Headers bölümünde de rate limit bilgisini taşıyan header'ları inceleyebilirsiniz.

Test bittikten sonra normal değere dönelim:

```env
RATE_LIMIT_LIMIT=100
```

## Örnek 72

Burada yalnızca kodun çalıştığını değil, bütün akışı doğrulamış olduk:

```text
config
↓
rateLimiter Middleware
↓
Middleware sırası
↓
429 Response
↓
Response sözleşmesi
```

## Örnek 73

---

## Bu Koruma Katmanlarının Sınırını Doğru Çizelim

Helmet ve rate limiting yararlıdır; ancak ikisine de yapabileceklerinden daha büyük görevler yüklememeliyiz.

### Rate Limiting Tek Başına DDoS Koruması Değildir

DDoS, yani **Distributed Denial of Service**, çok sayıda kaynaktan aşırı trafik göndererek bir servisi kullanılamaz hâle getirmeyi amaçlayan saldırı türlerinden biridir.

Express içinde çalışan rate limiter tekrarlanan Request'leri sınırlamaya yardımcı olur.

Ancak çok büyük trafik daha Request uygulamamıza ulaşmadan önce network veya server altyapısını zorlayabilir.

Bu nedenle büyük ölçekli saldırılarda uygulama seviyesinin dışında çalışan ek altyapı korumaları da gerekebilir.

Bizim eklediğimiz katmanın görevi daha nettir:

```text
Express'e ulaşan Request'ler
↓
belirli bir zaman aralığında sayılır
↓
limit aşılırsa Request reddedilir
```

## Örnek 74

### Varsayılan MemoryStore Ne Anlama Geliyor?

`express-rate-limit`, sayaç bilgisini bir yerde tutmak zorundadır.

Varsayılan olarak **MemoryStore** kullanılır. Yani sayaçlar çalışan Node.js process'inin belleğinde tutulur.

Örneğin:

```text
Node.js process memory
↓
IP A → 12 Request
IP B → 4 Request
```

## Örnek 75

Bu yapı tek process ile çalışan uygulamamız için yeterlidir.

Ama iki önemli sonucu vardır.

Birincisi, uygulama yeniden başlatılırsa memory'deki sayaçlar sıfırlanır.

İkincisi, uygulamamız birden fazla process veya server üzerinde çalışırsa her instance kendi sayacını tutabilir.

```text
Server A
↓
kendi sayaçları

Server B
↓
kendi sayaçları
```

## Örnek 76

Böyle bir mimaride bütün instance'ların paylaştığı harici bir store kullanılabilir. Redis bu amaçla kullanılabilecek çözümlerden biridir.

Şimdiki Movie API'mizde harici store kurmamıza gerek yok; ancak varsayılan MemoryStore'un bu sınırını bilmek önemlidir.

---

## CORS, Helmet ve Rate Limiting Artık Nerede Duruyor?

64 ve 65. bölümlerde üç farklı koruma mekanizması ekledik.

Görevlerini tek akışta ayıralım:

```text
CORS
↓
browser'ın cross-origin Response'a erişimini yönetir

Helmet
↓
HTTP Response güvenlik header'larını yönetir

Rate Limiting
↓
Request sıklığına sınır koyar
```

## Örnek 77

Üçü de güvenlikle ilgilidir; fakat farklı noktalara müdahale eder.

Örneğin:

```text
CORS kullanmak
↓
client'ın kimliğini doğrulamaz

Helmet kullanmak
↓
çok sık Request'i sınırlamaz

Rate limiting kullanmak
↓
HTTP güvenlik header'larını oluşturmaz
```

## Örnek 78

Bu ayrım sayesinde güvenlik araçlarını isimlerine göre değil, **hangi problemi çözdüklerine göre** seçebiliriz.

---

## Proje Yapımızdaki Yeni Sorumluluk

Güncel yapımızda:

```text
src
├── config
│   └── env.js
├── constants
├── controllers
├── db
├── middlewares
│   ├── errorHandler.js
│   ├── rateLimiter.js
│   ├── requestLogger.js
│   └── validateBody.js
├── routers
├── services
├── utils
├── validation
├── index.js
└── server.js
```

## Örnek 79

`rateLimiter.js` Request akışında çalışan yeni bir Middleware sorumluluğudur.

Limit değerleri ise Middleware'in içine gömülmemiştir:

```text
.env
↓
config/env.js
↓
rateLimiter.js
```

## Örnek 80

Bu bağlantı 63. bölümde kurduğumuz config yapısının neden değerli olduğunu da gösterir. Ortama göre değişen değerleri tek tek Middleware dosyalarında aramak yerine merkezi config üzerinden yönetiyoruz.

---

## Bölüm Sonu Uygulaması

1. `helmet` ve `express-rate-limit` paketlerini kurun.
2. `app.use(helmet())` ekleyin ve Response Headers bölümündeki değişikliği gözlemleyin.
3. `.env` ve `example.env` dosyalarına `RATE_LIMIT_WINDOW_MS` ve `RATE_LIMIT_LIMIT` ekleyin.
4. Bu değerleri `env.js` içinde pozitif tam sayı olarak doğrulayın.
5. `src/middlewares/rateLimiter.js` dosyasını oluşturun.
6. Limit aşıldığında `429` ve `{ message }` biçiminde Response dönmesini sağlayın.
7. Rate limiter'ı `requestLogger` sonrasında, `express.json()` öncesinde çalıştırın.
8. Limiti development sırasında geçici olarak `3` yapıp dört hızlı Request ile davranışı doğrulayın.
9. Test bittikten sonra normal limit değerine geri dönün.
10. CORS, Helmet ve Rate Limiting'in Request/Response akışının hangi farklı noktalarına müdahale ettiğini açıklayın.

---

## 🧠 Akılda Kalsın

Helmet'in temel görevi:

```text
Response
↓
güvenlik odaklı HTTP header kararlarını yönetmek
```

## Örnek 81

Rate limiting'in temel görevi:

```text
client Request'leri
↓
belirli zaman aralığında say
↓
limit aşılırsa
429 Too Many Requests
```

## Örnek 82

Rate limiter için iki temel değer:

```text
windowMs
↓
zaman aralığı

limit
↓
izin verilen Request sayısı
```

## Örnek 83

Ve en önemli güvenlik ayrımı:

```text
CORS
Helmet
Rate Limiting
↓
aynı güvenlik problemini çözmez
```

## Örnek 84

Her biri Request/Response akışının farklı bir noktasını korur.

---

## Sonraki Adım

Movie API artık Request'i doğruluyor, merkezi Response yapısı kullanıyor, Service katmanına sahip, config değerlerini merkezden yönetiyor ve temel güvenlik katmanlarını uyguluyor.

Fakat uygulama çalışırken ne olduğunu hâlâ sınırlı biçimde görebiliyoruz.

38. bölümde yazdığımız `requestLogger` bize yalnızca örneğin:

```text
GET /movies
```

## Örnek 85

bilgisini verir.

Uygulama büyüdükçe şu bilgiler de önemli hâle gelir:

```text
hangi status döndü?
Request ne kadar sürdü?
hangi hata oluştu?
hangi Request sırasında oluştu?
```
