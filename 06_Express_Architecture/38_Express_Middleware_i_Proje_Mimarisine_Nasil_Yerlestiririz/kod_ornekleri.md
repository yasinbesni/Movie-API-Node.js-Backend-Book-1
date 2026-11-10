# Bölüm 38 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Express Middleware'i Proje Mimarisine Nasıl Yerleştiririz?

## Middleware'i Biliyoruz. Ama Artık Projemiz Değişti

19. bölümde middleware'in temel mantığını öğrenmiştik.

O zamanki akışımızı hatırlayalım:

```text
Request
↓
middleware
↓
next()
↓
sonraki işlem
```

## Örnek 2

Ayrıca:

```js
app.use(express.json());
```

## Örnek 3

satırının da hazır bir Express middleware'i olduğunu görmüştük.

Ama o günlerde projemiz bugünkü kadar parçalı değildi.

37. bölümde kodumuzu ayırdık:

```text
server.js
↓
Router
↓
Controller
↓
Movie Model
↓
MongoDB
```

## Örnek 4

Şimdi middleware için yeni bir soru doğuyor:

> Request akışında arada çalışan ortak kodları bu yapıda nereye koyacağız?

Bu bölümde middleware'i yeniden tanımlamayacağız.

Onu gerçek proje mimarimize yerleştireceğiz.

---

## Aynı İşi Her Controller'a Yazmak İster miyiz?

Her Request geldiğinde terminale kısa bir bilgi yazmak istediğimizi düşünün.

Örneğin:

```text
GET /movies
POST /movies
PATCH /movies/...
```

## Örnek 5

Bunu her Controller'ın içine ayrı ayrı yazabiliriz.

Ama bir süre sonra loglama biçimini değiştirmek istersek her Controller'daki kopyayı ayrı ayrı düzeltmemiz gerekir. Birini gözden kaçırmak farklı yollarda farklı kayıtlar üretilmesine yol açar. Ortak middleware'de tek değişiklik yapmak, hem bakım yükünü azaltır hem de aynı davranışın bütün bağlı yollarda uygulanmasını kolaylaştırır.

Ama o zaman:

```text
getMovies
↓
log yaz

createMovie
↓
log yaz

updateMovieById
↓
log yaz
```

## Örnek 6

şeklinde aynı iş tekrar eder.

Bu işlem:

```text
filmi getir
filmi oluştur
filmi güncelle
```

## Örnek 7

gibi Controller'ın asıl görevi değildir.

Request yolculuğunun **arasında** yapılacak ortak bir iştir.

Yani tam middleware'lik bir ihtiyaçtır.

---

## requestLogger Middleware'ini Ayrı Bir Dosyaya Taşıyalım

Bu kez middleware kodunu `server.js` içinde bırakmayacağız.

Çünkü artık proje yapımızda tekrar kullanılabilir ortak işlerin kendi yeri olmasını istiyoruz.

Yeni klasör:

```text
src/middlewares
```

## Örnek 8

Yeni dosya:

```text
src/middlewares/requestLogger.js
```

## Örnek 9

Dosyaya şunu yazalım:

```js
export const requestLogger = (
  req,
  res,
  next,
) => {
  console.log(`${req.method} ${req.path}`);

  next();
};
```

## Örnek 10

Burada middleware'in çalışma mantığı değişmedi.

19. bölümde gördüğümüz yapı yine aynı:

```text
ortak işi yap
↓
next()
↓
akış devam etsin
```

## Örnek 11

Değişen şey middleware'in artık proje içinde **nerede yaşadığıdır**.

---

## requestLogger'ı server.js İçinde Kullanalım

Dosya:

```text
src/server.js
```

## Örnek 12

Önce import edelim:

```js
import { requestLogger }
  from "./middlewares/requestLogger.js";
```

## Örnek 13

Sonra Request akışına ekleyelim:

```js
app.use(requestLogger);
```

## Örnek 14

Film Router'ından önce kullanalım:

```js
import express from "express";

import { requestLogger }
  from "./middlewares/requestLogger.js";
import moviesRouter from "./routers/movies.js";

export const app = express();

app.use(express.json());
app.use(requestLogger);

app.get("/", (req, res) => {
  res.send("Merhaba Express!");
});

app.use("/movies", moviesRouter);
```

## Örnek 15

Şimdi `GET /movies` geldiğinde akışımız:

```text
GET /movies
↓
express.json()
↓
requestLogger
↓
next()
↓
moviesRouter
↓
getMovies Controller
↓
MongoDB
↓
Response
```

## Örnek 16

olur.

---

## Neden requestLogger Controller'ın İçinde Değil?

Çünkü Controller'ın sorusu:

```text
Bu Route'un asıl işi ne?
```

## Örnek 17

Örneğin:

```text
GET /movies
↓
filmleri getir
```

## Örnek 18

Middleware'in sorusu ise farklıdır:

```text
Request yolculuğu sırasında
hangi ortak iş yapılacak?
```

## Örnek 19

Bizim örneğimizde:

```text
Request geldi
↓
log yaz
↓
devam et
```

## Örnek 20

Bu yüzden:

```text
Controller
↓
asıl işlem yapılacak?
```

## Örnek 21

ile:

```text
Middleware
↓
arada hangi ortak iş yapılacak?
```

## Örnek 22

aynı görev değildir.

---

## Router'ın Görevi de Değişmedi

Router hâlâ:

```text
HTTP yöntemi + yol
↓
hangi Controller?
```

## Örnek 23

sorusuna cevap verir.

Örneğin:

```js
router.get("/", getMovies);
```

## Örnek 24

şunu söyler:

```text
GET /
↓
getMovies
```

## Örnek 25

Middleware ise bu yolculuğun arasına girebilir.

Üçünü birlikte düşünelim:

```text
MIDDLEWARE
↓
arada ortak iş yap

ROUTER
↓
hedef Controller'ı seç

CONTROLLER
↓
asıl işlemi yürüt
```

## Örnek 26

---

## Middleware Her Request'te Çalışmak Zorunda mı?

Hayır.

Burada 19. bölümdeki temel bilgiyi proje mimarimiz üzerinde genişletiyoruz.

Middleware'i ne kadar geniş bir alanda kullanmak istediğimize göre farklı yerlere bağlayabiliriz.

---

## 1. Uygulama Genelinde Kullanmak

Şu kullanım:

```js
app.use(requestLogger);
```

## Örnek 27

middleware'i genel Request akışına ekler.

Kabaca:

```text
uygulamaya gelen uygun Request'ler
↓
requestLogger
```

## Örnek 28

Bizim mevcut kullanımımız budur.

---

## 2. Belirli Bir Başlangıç Yolunda Kullanmak

Yalnızca `/movies` ile başlayan Request'lerde çalışmasını istersek:

```js
app.use("/movies", requestLogger);
```

## Örnek 29

kullanabiliriz.

Örneğin:

```text
GET /movies
PATCH /movies/:movieId
DELETE /movies/:movieId
↓
requestLogger çalışabilir
```

## Örnek 30

Ama:

```text
GET /
```

## Örnek 31

aynı başlangıç yoluna sahip değildir.

---

## 3. Yalnızca Belirli Bir Route'ta Kullanmak

Middleware'i doğrudan Route üzerinde de kullanabiliriz.

Dosya:

```text
src/routers/movies.js
```

## Örnek 32

Önce import:

```js
import { requestLogger }
  from "../middlewares/requestLogger.js";
```

## Örnek 33

Sonra:

```js
router.get(
  "/",
  requestLogger,
  getMovies,
);
```

## Örnek 34

Akış:

```text
GET /
↓
requestLogger
↓
next()
↓
getMovies
```

## Örnek 35

Buradaki sıra önemlidir.

Express önce:

```text
requestLogger
```

## Örnek 36

sonra:

```text
getMovies
```

## Örnek 37

fonksiyonunu çalıştırır.

---

## Üç Kullanımı Tek Bakışta Görelim

```text
app.use(requestLogger)
↓
uygulama genelindeki akış
```

## Örnek 38



```text
app.use("/movies", requestLogger)
↓
/movies ile başlayan akış
```

## Örnek 39



```text
router.get("/", requestLogger, getMovies)
↓
yalnızca belirli Route
```

## Örnek 40

Yani middleware'i:

```text
nerede kullanacağız?
```

## Örnek 41

sorusu, middleware'in kendisinden ayrı bir tasarım kararıdır.

---

## Sıra Neden Hâlâ Önemli?

Express, Request ile eşleşen middleware ve Route işlemlerini tanımlandıkları sıraya göre değerlendirir.

Şu sıra:

```js
app.use(requestLogger);
app.use("/movies", moviesRouter);
```

## Örnek 42

bize:

```text
Request
↓
requestLogger
↓
moviesRouter
```

## Örnek 43

akışını verir.

Ama Router önce gelirse:

```js
app.use("/movies", moviesRouter);
app.use(requestLogger);
```

## Örnek 44

ve Router Request'i Response ile tamamlarsa alttaki `requestLogger` o Request için çalışmayabilir.

Bu yüzden middleware eklerken yalnızca:

```text
hangi middleware?
```

## Örnek 45

değil:

```text
hangi sırada?
```

## Örnek 46

sorusunu da sorarız.

---

## Response Gönderilirse Ne Oluyordu?

19. bölümden kısa bir hatırlatma yeterli.

Middleware iki temel yoldan biriyle ilerleyebilir:

```text
işini yap
↓
next()
↓
devam et
```

## Örnek 47

veya:

```text
Response gönder
↓
akışı burada bitir
```

## Örnek 48

Bu yüzden Response gönderilmiş bir middleware'in ardından normal akışı sürdürmek için ayrıca `next()` çağırmak istemeyiz.

Bu konuyu yeniden açmıyoruz; yalnızca 39. bölümde hata akışına geçerken ihtiyaç duyacağımız sınırı hatırlıyoruz.

---

## Proje Yapımız Şimdi Nasıl?

`requestLogger` ile birlikte proje yapımıza yeni bir klasör eklendi: `src/middlewares`.

```text
src
├── controllers
│   └── movies.js
├── db
│   ├── initMongoConnection.js
│   └── models
│       └── movie.js
├── middlewares
│   └── requestLogger.js
├── routers
│   └── movies.js
├── index.js
└── server.js
```

## Örnek 49

`src/middlewares` klasörünü yalnızca:

```text
profesyonel projelerde olur
```

## Örnek 50

diye baştan eklemedik.

Önce `requestLogger` gibi tekrar kullanılabilir ortak bir middleware ihtiyacı doğdu.

Sonra bu ortak kodun yaşayacağı yer olarak `src/middlewares` klasörünü oluşturduk.

---

## GET /movies Artık Hangi Yoldan Geçiyor?

```text
GET /movies
↓
src/server.js
↓
express.json()
↓
requestLogger
↓
next()
↓
src/routers/movies.js
↓
router.get("/", getMovies)
↓
src/controllers/movies.js
↓
getMovies
↓
Movie Model
↓
MongoDB
↓
200 Response
```

## Örnek 51

Middleware'in proje mimarisindeki yeri artık görünür.

---

## Peki try/catch Tekrarı Ne Oldu?

37. bölümün sonunda bizi buraya getiren problem hâlâ duruyor.

Controller dosyamızda:

```text
getMovies
↓
try/catch

getMovieById
↓
try/catch

createMovie
↓
try/catch

updateMovieById
↓
try/catch

deleteMovieById
↓
try/catch
```

## Örnek 52

tekrarı var.

Artık middleware'in:

```text
Request akışının arasında
çalışabildiğini
```

## Örnek 53

ve proje içinde ayrı bir dosyada yaşayabildiğini biliyoruz.

Şimdi daha güçlü bir soru sorabiliriz:

> Beklenmeyen bir hata oluştuğunda bu hata da ortak bir middleware'e gönderilebilir mi?

Ve:

> Her Controller'da aynı `try/catch` yapısını tekrar etmek zorunda mıyız?

İşte sıradaki bölümün konusu bu.

---

## 🧠 Akılda Kalsın

Middleware'i 19. bölümde öğrendik.

Bu bölümde yeni öğrendiğimiz şey onun **mimarideki yeri** oldu.

```text
Middleware
↓
Request akışında ortak iş
```

## Örnek 54



```text
Router
↓
hedef Controller'ı seç
```

## Örnek 55



```text
Controller
↓
asıl işlemi yürüt
```

## Örnek 56

Middleware farklı kapsamlarla bağlanabilir:

```text
app.use(middleware)
↓
genel
```

## Örnek 57



```text
app.use("/movies", middleware)
↓
belirli başlangıç yolu
```

## Örnek 58



```text
router.get("/", middleware, controller)
↓
belirli Route
```

## Örnek 59

---

## Sonraki Adım

Normal Request akışını artık hem temel düzeyde hem de proje mimarimiz içinde biliyoruz.

Sırada normal akıştan farklı bir yol var:

```text
Request
↓
Controller
↓
beklenmeyen hata
↓
?
```
