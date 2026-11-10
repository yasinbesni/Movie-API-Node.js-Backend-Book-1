# Bölüm 66 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Logging ile Sunucuda Gerçekleşen İşlemler ve Hatalar Nasıl İzlenir?

## Bir Request'in Yalnızca Geldiğini Bilmek Artık Yetmiyor

38. bölümde basit bir `requestLogger` Middleware'i oluşturmuştuk:

```js
console.log(
  `${req.method} ${req.path}`,
);
```

## Örnek 2

Bu kod sayesinde terminalde örneğin:

```text
GET /movies
```

## Örnek 3

görebiliyorduk.

Bu bilgi başlangıçta yeterliydi. Çünkü amacımız bir Request'in Middleware'den geçtiğini görmekti.

Fakat Movie API büyüdükçe tek başına:

```text
GET /movies
```

## Örnek 4

kaydı bize yeterince şey söylemez.

Bir kullanıcı:

> `GET /movies` bazen çok yavaş çalışıyor.

 dediğinde şu bilgileri de görmek isteriz:

```text
hangi Request geldi?
↓
hangi status code ile tamamlandı?
↓
ne kadar sürdü?
↓
hata oluştu mu?
```

## Örnek 5

Örneğin şu iki Request'e bakalım:

```text
GET /movies
status: 200
süre: 18 ms
```

## Örnek 6

ve:

```text
GET /movies
status: 200
süre: 1850 ms
```

## Örnek 7

İkisi de `200` ile tamamlanmıştır. Ancak ikinci Request'in çok daha yavaş olduğunu artık açıkça görebiliyoruz.

Uygulamada gerçekleşen olayları sonradan inceleyebilmek için tuttuğumuz bu kayıtlara **log** denir.

Bu bölümde amacımız terminale daha fazla yazı basmak değildir. Amacımız, bir sorun çıktığında:

```text
ne oldu?
↓
hangi Request sırasında oldu?
↓
Request nasıl tamamlandı?
```

## Örnek 8

sorularına cevap verecek kayıtlar bırakmaktır.

---

## Önce En Basit Logu Yazalım

Node.js'te `console.log()` ile de log yazabiliriz. Ancak büyüyen bir uygulamada logların seviyesini, yapısını ve HTTP bilgilerini daha düzenli yönetmek isteriz.

Bu bölümde bunun için **Pino** kullanacağız.

Önce paketleri kuralım:

```bash
npm install pino pino-http
```

## Örnek 9

Şimdilik yalnızca `pino` ile başlayacağız. `pino-http` biraz sonra, HTTP Request'lerini otomatik izleme ihtiyacı doğduğunda kullanılacak.

Yeni dosyamız:

```text
src/logging/logger.js
```

## Örnek 10

olsun:

```js
import pino from "pino";

export const logger = pino();
```

## Örnek 11

Burada yeni olan iki parça var.

```js
pino();
```

## Örnek 12

Pino tarafından bir **logger nesnesi** oluşturur.

Biz bu nesneyi:

```js
logger
```

## Örnek 13

adındaki değişkende tutuyoruz.

Yani:

```text
pino()
↓
log yazabilen bir nesne oluşturur
↓
logger değişkenine atanır
```

## Örnek 14

Artık `logger`ın nereden geldiğini biliyoruz.

Örneğin:

```js
logger.info(
  "Server çalışmaya başladı.",
);
```

## Örnek 15

yazabiliriz.

Buradaki:

```js
.info(...)
```

## Örnek 16

`logger` nesnesinin bir metodudur ve **normal bir bilgi kaydı** oluşturur.

Kavramsal olarak:

```text
logger
↓
Pino ile oluşturduğumuz nesne

logger.info(...)
↓
normal bir bilgi logu yaz
```

## Örnek 17

Pino bu kaydı JSON biçiminde gösterebilir. Örneğin çıktı buna benzeyebilir:

```json
{
  "level": 30,
  "time": 1760000000000,
  "msg": "Server çalışmaya başladı."
}
```

## Örnek 18

Şimdilik burada yalnızca şu ilişkiyi bilmemiz yeterlidir:

```text
msg
↓
yazdığımız mesaj

level
↓
logun önem seviyesi

time
↓
logun oluşturulduğu zaman
```

## Örnek 19

`level: 30` değerinin neden sayı olduğunu biraz sonra log seviyelerini ele alırken açıklayacağız.

---

## Pino ile Şu Anda Neyi Yapabiliyoruz?

Şu anda uygulamanın genel bir olayını kaydedebiliriz:

```js
logger.info(
  "Server çalışmaya başladı.",
);
```

## Örnek 20

Ama bir HTTP Request için şu bilgileri henüz otomatik toplamıyoruz:

```text
GET /movies geldi
↓
Response 200 ile tamamlandı
↓
18 ms sürdü
```

## Örnek 21

Bunları yalnızca `pino` kullanarak kendimiz yazmaya kalkarsak her Request için:

```text
methodu al
↓
URL'yi al
↓
başlangıç zamanını kaydet
↓
Response'un bitmesini bekle
↓
status code'u al
↓
geçen süreyi hesapla
↓
log yaz
```

## Örnek 22

gibi işleri tekrar tekrar yapmamız gerekir.

Bu iş `getMovies`, `createMovie` veya `updateMovieById` gibi belirli bir Controller'a ait değildir.

Bütün HTTP Request'lerini ilgilendirir.

Bu nedenle burada ortak bir **Middleware** kullanmak daha uygundur.

İkinci paketimiz olan **`pino-http`** tam bu işi kolaylaştırır.

---

## `pino` ile `pino-http` Arasındaki Fark

İsimleri benzer olduğu için önce görevlerini ayıralım:

```text
pino
↓
log kayıtlarını yazan ana logger'ı oluşturur
```

## Örnek 23



```text
pino-http
↓
HTTP Request ve Response akışını izleyen
Express Middleware'ini oluşturur
```

## Örnek 24

Yani `pino-http`, Pino'nun yerine geçmez.

Bizim oluşturduğumuz ana `logger`ı kullanarak HTTP Request'leri hakkında log üretir.

---

## Eski `requestLogger` Middleware'imizi Güncelleyelim

38. bölümden beri şu dosyamız vardı:

```text
src/middlewares/requestLogger.js
```

## Örnek 25

Artık içindeki basit `console.log()` yerine `pino-http` kullanacağız:

```js
import pinoHttp from "pino-http";

import { logger } from "../logging/logger.js";

export const requestLogger = pinoHttp({
  logger,
});
```

## Örnek 26

Bu kodu adım adım okuyalım.

Önce:

```js
import { logger } from "../logging/logger.js";
```

## Örnek 27

ile az önce oluşturduğumuz ana Pino logger'ını alıyoruz.

Sonra:

```js
pinoHttp({
  logger,
});
```

## Örnek 28

ile `pino-http`a şunu söylüyoruz:

> HTTP loglarını üretirken bu Pino logger'ını kullan.

`pinoHttp(...)` çağrısının bize verdiği sonuç ise bir Express Middleware'dir.

Bu Middleware'i:

```js
requestLogger
```

## Örnek 29

adında tutuyoruz.

Artık üç adı karıştırmayalım:

```text
pino
↓
kütüphane
↓
logger nesnesini oluşturur
```

## Örnek 30



```text
pino-http
↓
kütüphane
↓
HTTP logging Middleware'i oluşturur
```

## Örnek 31



```text
requestLogger
↓
bizim Express'e ekleyeceğimiz Middleware
```

## Örnek 32

---

## `requestLogger` Express Akışına Nasıl Giriyor?

`server.js` içinde:

```js
app.use(requestLogger);
```

## Örnek 33

yazıyoruz.

Artık bir Request geldiğinde akış şöyle başlar:

```text
GET /movies gelir
↓
requestLogger çalışır
↓
pino-http Request'i izlemeye başlar
↓
Request diğer Middleware ve Route'lara devam eder
↓
Response tamamlanır
↓
pino-http HTTP sonucunu loglar
```

## Örnek 34

Örneğin `server.js` içindeki sıra şöyle olabilir:

```js
const app = express();

app.use(requestLogger);
app.use(helmet());

app.use(
  cors({
    origin: env.corsOrigin,
  }),
);

app.use(apiRateLimiter);
app.use(express.json());

app.use("/movies", moviesRouter);

app.use(errorHandler);
```

## Örnek 35

`requestLogger` başa yakın olduğu için Request daha Router'a ulaşmadan izlenmeye başlanır.

Bu sayede yalnızca Controller'ın çalıştığı kısmı değil, Request'in sunucu içindeki daha geniş akışını gözlemleyebiliriz.

---

## İlk HTTP Logunu Okuyalım

Server'ı başlatalım ve Postman'den:

```http
GET http://localhost:3000/movies
```

## Örnek 36

gönderelim.

İsteği gönderdikten sonra **Node.js sunucusunu başlattığımız terminale** bakalım. `pino-http`, Response tamamlandığında HTTP isteğine ait bilgileri otomatik olarak loglar. Varsayılan kurulumda bu kayıt terminalin standart çıktısına yazılır; ayrıca `console.log()` çağırmamız gerekmez.

Örneğin terminalde aşağıdakine benzer bir JSON log kaydı görebiliriz:

```json
{
  "level": 30,
  "req": {
    "method": "GET",
    "url": "/movies"
  },
  "res": {
    "statusCode": 200
  },
  "responseTime": 18,
  "msg": "request completed"
}
```

## Örnek 37

Bu JSON, okunmasını kolaylaştırmak için sadeleştirilmiş bir örnektir; gerçek terminal çıktısında ek alanlar bulunabilir. `level: 30`, `info` log seviyesini; `msg` ise kaydın kısa açıklamasını belirtir. Şimdi isteğin sonucunu anlamak için dört alana odaklanalım:

```text
req.method
↓
GET
```

## Örnek 38



```text
req.url
↓
/movies
```

## Örnek 39



```text
res.statusCode
↓
200
```

## Örnek 40



```text
responseTime
↓
18 ms
```

## Örnek 41

Böylece eski logger'ımızdaki:

```text
GET /movies
```

## Örnek 42

bilgisi artık şu anlamlı kayda dönüşür:

```text
GET /movies
↓
200 ile tamamlandı
↓
18 ms sürdü
```

## Örnek 43

Buradaki `responseTime`, kullanıcının telefonundan veya bilgisayarından yaptığı toplam internet yolculuğunu ölçmez.

Yaklaşık olarak Request'in `pino-http` Middleware'ine girdikten sonra Response tamamlanana kadar geçen **sunucu tarafındaki süreyi** gösterir.

---

## `req.log` Nereden Geliyor?

`pino-http` bize yalnızca otomatik HTTP logu üretmez.

Request işlenirken kullanabileceğimiz Request'e özel bir logger da ekler:

```js
req.log
```

## Örnek 44

Önemli nokta şudur:

```text
Express'in normal req nesnesi
↓
req.params
req.query
req.body
```

## Örnek 45

`requestLogger` Middleware'inden geçtikten sonra:

```text
req.log
```

## Örnek 46

de kullanılabilir hâle gelir.

Biz `req.log` değerini elle oluşturmuyoruz.

Onu:

```text
requestLogger
↓
pino-http
↓
req.log
```

## Örnek 47

zinciri oluşturur.

Örneğin belirli bir film Request'i sırasında:

```js
req.log.info(
  {
    movieId: req.params.movieId,
  },
  "Movie getiriliyor.",
);
```

## Örnek 48

yazabiliriz.

Burada artık log belirli bir HTTP Request'in bağlamına aittir.

---

## `logger` ile `req.log` Ne Zaman Kullanılır?

Şimdi ikisini kolayca ayırabiliriz.

### Belirli bir Request yoksa: `logger`

Örneğin server başlarken:

```js
logger.info(
  "Server çalışmaya başladı.",
);
```

## Örnek 49

Burada henüz belirli bir:

```text
GET /movies
POST /movies
```

## Örnek 50

Request'i yoktur.

Bu nedenle ana:

```js
logger
```

## Örnek 51

uygundur.

### Belirli bir Request içindeysek: `req.log`

Örneğin bir Request işlenirken hata oluştuğunda:

```js
req.log.error(
  {
    err: error,
  },
  "Request işlenirken hata oluştu.",
);
```

## Örnek 52

kullanabiliriz.

Kısa ayrım:

```text
uygulamanın genel olayı
↓
logger
```

## Örnek 53



```text
belirli bir HTTP Request'e ait olay
↓
req.log
```

## Örnek 54

---

## Merkezi `errorHandler` İçinde Log Yazalım

39. bölümde merkezi `errorHandler` oluşturmuştuk.

Artık beklenmeyen hatayı yalnızca kullanıcıya Response olarak göndermek yerine, server tarafında teknik ayrıntısıyla da kaydedebiliriz:

```js
export const errorHandler = (
  error,
  req,
  res,
  next,
) => {
  req.log.error(
    {
      err: error,
    },
    "Request işlenirken beklenmeyen hata oluştu.",
  );

  return res.status(500).json({
    message:
      "Beklenmeyen bir hata oluştu.",
  });
};
```

## Örnek 55

Burada iki farklı bilgi iki farklı yere gider.

Client'a:

```json
{
  "message": "Beklenmeyen bir hata oluştu."
}
```

## Örnek 56

Server loguna ise geliştiricinin inceleyebileceği hata bilgisi gider:

```text
hangi Request sırasında oldu?
↓
hata nesnesi ne?
↓
stack bilgisi ne?
```

## Örnek 57

Bu ayrım önemlidir.

API kullanıcısına her teknik ayrıntıyı göndermek istemeyiz. Ama problemi incelemek için server tarafında yeterli iz bırakmak isteriz.

Aynı hatalı Request için iki log görmek de normaldir:

```text
req.log.error(...)
↓
hatanın kendisini anlatır
```

## Örnek 58



```text
pino-http otomatik HTTP logu
↓
Request'in hangi status ile
ve ne kadar sürede tamamlandığını anlatır
```

## Örnek 59

Bunlar aynı kaydın gereksiz tekrarı değildir; farklı sorulara cevap verirler.

---

## Structured Logging Ne Demek?

Şöyle bir metin yazabiliriz:

```text
GET /movies 200 18ms
```

## Örnek 60

İnsan gözü bunu okuyabilir.

Ama Pino bilgileri ayrı alanlar hâlinde tutabilir:

```json
{
  "method": "GET",
  "url": "/movies",
  "statusCode": 200,
  "responseTime": 18
}
```

## Örnek 61

Bu yapıya **structured logging** diyebiliriz.

Avantajı şudur:

```text
statusCode = 500 olan kayıtları bul
```

## Örnek 62

veya:

```text
/movies Request'lerini ayır
```

## Örnek 63

veya:

```text
responseTime değeri yüksek kayıtları bul
```

## Örnek 64

şeklinde logları daha kolay filtreleyebiliriz.

Yani log artık yalnızca okunacak bir cümle değil, alanlarına göre işlenebilecek bir veri hâline gelir.

---

## Log Seviyesi Nedir?

Her log aynı önemde değildir.

Örneğin:

```text
Server başladı
```

## Örnek 65

normal bir bilgidir.

Ama:

```text
MongoDB bağlantısı kurulamadı
```

## Örnek 66

bir hatadır.

Bu nedenle logger'larda **log seviyeleri** kullanılır.

Başlangıç için şu dört seviyeyi bilmek yeterlidir:

| Seviye | Ne zaman kullanılır? |
|---|---|
| `debug` | Geliştirme sırasında ayrıntılı takip bilgisi |
| `info` | Normal çalışma bilgisi |
| `warn` | Sistem çalışıyor ama dikkat edilmesi gereken durum |
| `error` | Bir işlemde hata oluştu |

Örneğin:

```js
logger.info(
  "Server çalışmaya başladı.",
);
```

## Örnek 67

normal bir olaydır.

```js
logger.error(
  {
    err: error,
  },
  "MongoDB bağlantısı kurulamadı.",
);
```

## Örnek 68

ise hata kaydıdır.

Pino'nun JSON çıktısında seviye sayı olarak görünebilir. Örneğin:

```text
level: 30
↓
info
```

## Örnek 69

Kod yazarken bu sayıyı kullanmayız ve ezberlememiz gerekmez.

Biz:

```js
logger.info()
logger.warn()
logger.error()
```

## Örnek 70

gibi anlamlı metot isimlerini kullanırız.

---

## Hangi Seviyelerin Yazılacağını Ayarlanabilir Yapalım

Şu anda logger'ı:

```js
export const logger = pino();
```

## Örnek 71

şeklinde oluşturuyoruz.

Geliştirme ortamında daha ayrıntılı log, production ortamında ise daha sınırlı log görmek isteyebiliriz.

Bu ayar ortama göre değişebileceği için 63. bölümde kurduğumuz environment config yapısına uygundur.

Önce `.env` dosyasına açıkça şu değeri ekleyelim:

```env
LOG_LEVEL=info
```

## Örnek 72

Bu satırın anlamı şudur:

```text
LOG_LEVEL
↓
logger'ın kullanacağı minimum log seviyesi

info
↓
bu örnekte seçtiğimiz değer
```

## Örnek 73

`process.env` değerlerinin String geldiğini 63. bölümden biliyoruz. Dolayısıyla:

```js
process.env.LOG_LEVEL
```

## Örnek 74

şu değeri taşır:

```js
"info"
```

## Örnek 75

Şimdi `src/config/env.js` içinde bu değeri kontrol edip `env` nesnesine ekleyelim.

Önce izin vereceğimiz değerleri tanımlayalım:

```js
const LOG_LEVELS = [
  "fatal",
  "error",
  "warn",
  "info",
  "debug",
  "trace",
  "silent",
];
```

## Örnek 76

Sonra küçük bir dönüştürme ve kontrol fonksiyonu yazalım:

```js
const parseLogLevel = (value) => {
  const logLevel =
    (value ?? "info")
      .trim()
      .toLowerCase();

  if (!LOG_LEVELS.includes(logLevel)) {
    throw new Error(
      "LOG_LEVEL geçerli bir log seviyesi olmalıdır.",
    );
  }

  return logLevel;
};
```

## Örnek 77

Bu fonksiyonun yaptığı iş basittir:

```text
LOG_LEVEL verilmemişse
↓
"info" kullan

LOG_LEVEL verilmişse
↓
küçük harfe çevir
↓
geçerli seviyeler arasında mı kontrol et
↓
geçerliyse geri döndür
```

## Örnek 78

Şimdi `env` nesnesine ekleyebiliriz:

```js
export const env = {
  mongodbUrl: getRequiredEnv(
    "MONGODB_URL",
  ),
  port: parsePort(process.env.PORT),
  logLevel: parseLogLevel(
    process.env.LOG_LEVEL,
  ),
};
```

## Örnek 79

`.env` dosyamızda:

```env
LOG_LEVEL=info
```

## Örnek 80

olduğuna göre zincir artık şöyledir:

```text
.env
↓
LOG_LEVEL=info
↓
process.env.LOG_LEVEL
↓
"info"
↓
parseLogLevel("info")
↓
env.logLevel
↓
"info"
```

## Örnek 81

Yani bu noktada:

```js
env.logLevel === "info";
```

## Örnek 82

olur.

`env.logLevel` artık nereden geldiği bilinmeyen bir değer değildir; `.env` içindeki `LOG_LEVEL` değerinin doğrulanmış hâlidir.

---

## Logger'ı `env.logLevel` ile Güncelleyelim

`env.logLevel` artık gerçekten var olduğuna göre `logger.js` dosyamızda kullanabiliriz:

```js
import pino from "pino";

import { env } from "../config/env.js";

export const logger = pino({
  level: env.logLevel,
});
```

## Örnek 83

Bizim mevcut `.env` değerimiz:

```env
LOG_LEVEL=info
```

## Örnek 84

olduğu için bu kod çalışırken kavramsal olarak şuna dönüşür:

```js
export const logger = pino({
  level: "info",
});
```

## Örnek 85

`level: "info"` şu anlama gelir:

```text
debug
↓
gösterilmez

info
warn
error
fatal
↓
gösterilir
```

## Örnek 86

Yani `level`, **hangi seviyeden itibaren logların yazılacağını** belirler.

Geçici olarak:

```env
LOG_LEVEL=error
```

## Örnek 87

yaparsak `info` kayıtları görünmez; `error` ve daha önemli kayıtlar görünür.

Denemeden sonra tekrar:

```env
LOG_LEVEL=info
```

## Örnek 88

değerine dönebiliriz.

---

## Request Body'yi Neden Olduğu Gibi Loglamıyoruz?

`req.log` kullanabildiğimizi gördükten sonra şunu yapmak cazip gelebilir:

```js
req.log.info({
  body: req.body,
});
```

## Örnek 89

Ancak Request Body ileride şunları içerebilir:

```text
password
access token
kişisel bilgi
özel uygulama verisi
```

## Örnek 90

Bu nedenle iyi logging:

```text
mümkün olan her şeyi kaydetmek
```

## Örnek 91

değildir.

Daha doğru soru şudur:

> Bu bilgi, problemi daha sonra anlamak için gerçekten gerekli mi?

Gerekli değilse loga eklememek daha güvenlidir.

---

## Logging Parçaları Projede Nerede Duruyor?

Artık proje içindeki parçaların görevlerini biliyoruz:

```text
src
├── config
│   └── env.js
├── logging
│   └── logger.js
├── middlewares
│   ├── errorHandler.js
│   └── requestLogger.js
├── routers
├── controllers
├── services
├── index.js
└── server.js
```

## Örnek 92

Bağlantı şu şekildedir:

```text
.env
↓
config/env.js
↓
env.logLevel
↓
logging/logger.js
↓
logger
```

## Örnek 93

Sonra:

```text
logger
↓
requestLogger.js
↓
pino-http
↓
requestLogger Middleware'i
↓
app.use(requestLogger)
```

## Örnek 94

Bir HTTP Request geldiğinde ise:

```text
Request
↓
requestLogger
↓
req.log kullanılabilir olur
↓
Router / Controller / Service çalışır
↓
Response tamamlanır
↓
pino-http HTTP özet logunu yazar
```

## Örnek 95

Bu zincir, bölümde oluşturduğumuz bütün parçaları birbirine bağlar.

---

## Baştan Sona Bir Request'i Takip Edelim

Postman'den:

```http
GET /movies
```

## Örnek 96

gönderiyoruz.

### 1. Request Express'e gelir

```text
GET /movies
```

## Örnek 97

### 2. `requestLogger` Request'i görür

```text
requestLogger
↓
pino-http tarafından oluşturulmuş Middleware
```

## Örnek 98

### 3. `req.log` kullanılabilir hâle gelir

Artık Request akışındaki kodlar gerekirse:

```js
req.log.info(...);
req.log.error(...);
```

## Örnek 99

kullanabilir.

### 4. Normal API akışı devam eder

```text
Rate Limiter
↓
JSON Parser
↓
Router
↓
Validation Middleware
↓
Controller
↓
Service
↓
MongoDB
```

## Örnek 100

### 5. Response tamamlanır

Örneğin:

```text
200 OK
```

## Örnek 101

### 6. `pino-http` HTTP sonucunu loglar

Örneğin:

```text
GET /movies
↓
200
↓
18 ms
```

## Örnek 102

sonuçlandığını da izleyebiliriz.

---

## Bölüm Sonu Uygulaması

1. `pino` ve `pino-http` paketlerini kurun.
2. `src/logging/logger.js` dosyasında önce `pino()` ile temel `logger` nesnesini oluşturun.
3. `logger.info("Server çalışmaya başladı.")` kullanıp ilk Pino logunu görün.
4. `requestLogger.js` içindeki eski `console.log()` Middleware'ini `pinoHttp({ logger })` ile değiştirin.
5. `app.use(requestLogger)` satırını Request akışının başına yakın yerleştirin.
6. `GET /movies` gönderip `method`, `url`, `statusCode` ve `responseTime` alanlarını bulun.
7. `req.log` değerinin `pino-http` tarafından eklendiğini doğrulayın.
8. `errorHandler` içinde `req.log.error({ err: error }, ...)` kullanın.
9. `.env` ve `.env.example` içine `LOG_LEVEL=info` ekleyin.
10. `config/env.js` içinde değeri doğrulayıp `env.logLevel` oluşturun.
11. `logger.js` içinde `level: env.logLevel` kullanın.
12. Geçici olarak `LOG_LEVEL=error` deneyip `info` loglarının filtrelendiğini gözlemleyin; sonra tekrar `info` değerine dönün.
13. Hassas veya gereksiz Request verilerini loglamadığınızdan emin olun.

---

## 🧠 Akılda Kalsın

Bu bölümde dört ismi doğru yere koyabilirsek logging yapısı büyük ölçüde anlaşılmış olur:

```text
pino
↓
ana logging kütüphanesi
```

## Örnek 103



```text
logger
↓
pino() ile bizim oluşturduğumuz ana logger nesnesi
```

## Örnek 104



```text
pino-http
↓
HTTP Request / Response akışını izleyen Middleware'i üretir
```

## Örnek 105



```text
req.log
↓
pino-http'ın belirli bir Request için kullanılabilir hâle getirdiği logger
```

## Örnek 106

Ve config zinciri:

```text
LOG_LEVEL=info
↓
process.env.LOG_LEVEL = "info"
↓
env.logLevel = "info"
↓
pino({ level: "info" })
```

## Örnek 107

Logging'in temel amacı şudur:

```text
bir problem oldu
↓
uygulamada ne yaşandığını tahmin etmek zorunda kalmayalım
↓
loglardan geriye dönüp inceleyebilelim
```

## Örnek 108

---

## Sonraki Adım

Artık bir Request'in:

```text
geldiğini
↓
nasıl tamamlandığını
↓
ne kadar sürdüğünü
↓
hata oluştuysa hata bilgisini
```
