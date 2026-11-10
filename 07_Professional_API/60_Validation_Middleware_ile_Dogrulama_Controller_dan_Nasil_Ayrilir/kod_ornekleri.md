# Bölüm 60 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Validation Middleware ile Doğrulama Controller'dan Nasıl Ayrılır?

## 59. Bölümde Çalışan Ama Tekrarlanan Bir Kod Bıraktık

59. bölümde Joi ile Request Body validation'ını çalıştırdık.

Artık `createMovie` Controller'ının başında şu kod var:

```js
const {
  error,
  value,
} = createMovieSchema.validate(
  req.body,
  {
    abortEarly: false,
    convert: false,
  },
);

if (error !== undefined) {
  return res.status(400).json({
    message: "Geçersiz Request Body.",
    errors: error.details.map(
      (detail) => detail.message,
    ),
  });
}
```

## Örnek 2

Buradaki:

```js
error.details.map(
  (detail) => detail.message,
)
```

## Örnek 3

yapısını 59. bölümde parçalara ayırmıştık: Joi'nin hata listesinden yalnızca `message` değerlerini seçiyoruz.

59. bölümde ayrıca iki farklı mesaj kaynağı görmüştük:

```text
Joi default mesajı
veya
.messages() ile yazdığımız custom mesaj
```

## Örnek 4

Middleware'in bunların hangisi olduğunu bilmesine gerek yoktur.

O yalnızca:

```js
detail.message
```

## Örnek 5

değerini Response'a taşır.

Yani sorumluluk ayrımı şöyle kalır:

```text
Joi Schema
↓
kural + gerekiyorsa custom hata mesajı

Validation Middleware
↓
Schema'yı çalıştır + oluşan mesajları HTTP Response'a taşı
```

## Örnek 6

Mesaj mekanizması değişmiyor. Önceki bölümde Joi Schema'ları Request Body'yi doğruluyordu; şimdi çözmemiz gereken problem, bu doğrulamayı çalıştıran aynı kodun birden fazla Controller'da tekrar etmesi.

`updateMovieById` Controller'ının başında da neredeyse aynı kod var.

Fark yalnızca kullanılan Schema:

```text
createMovie
↓
createMovieSchema
```

## Örnek 7



```text
updateMovieById
↓
updateMovieSchema
```

## Örnek 8

Şu anda kodumuz çalışıyor.

Ama Controller'lara baktığımızda asıl işi görmek zorlaşıyor.

Örneğin `createMovie` için görmek istediğimiz akış aslında çok basit:

```text
geçerli film verisini al
↓
Movie oluştur
↓
kaydet
↓
Response gönder
```

## Örnek 9

Fakat Controller'ın başında önce yaklaşık yirmi satırlık validation mekanizmasını okumamız gerekiyor.

Daha da önemlisi aynı mekanizma ikinci Controller'da tekrar ediyor.

Yeni problemimiz:

> Validation kuralları Joi Schema'larda duruyor. Peki validation'ı çalıştıran aynı kod neden her Controller'ın içinde tekrar ediyor?

Bu bölümde bu problemi adım adım çözeceğiz.

---

## Önce Sadece POST Validation Kodunu Controller'dan Çıkaralım

Hemen genel bir çözüm yazmayalım.

Önce yalnızca `POST /movies` için çalışan en basit Middleware'i oluşturalım.

Dosya:

```text
src/middlewares/validateBody.js
```

## Örnek 10

İlk denememiz şöyle olabilir:

```js
import {
  createMovieSchema,
} from "../validation/movieSchemas.js";

export const validateCreateMovieBody = (
  req,
  res,
  next,
) => {
  const {
    error,
    value,
  } = createMovieSchema.validate(
    req.body,
    {
      abortEarly: false,
      convert: false,
    },
  );

  if (error !== undefined) {
    return res.status(400).json({
      message: "Geçersiz Request Body.",
      errors: error.details.map(
        (detail) => detail.message,
      ),
    });
  }

  req.body = value;

  next();
};
```

## Örnek 11

Bu kodda yeni bir validation kuralı yok.

59. bölümde Controller'ın başında bulunan aynı kodu yalnızca başka bir yere taşıdık.

Neden bu dosya Middleware klasöründe?

Çünkü artık akış şöyle olsun istiyoruz:

```text
Request
↓
Validation
↓
Controller
```

## Örnek 12

Validation, Request ile Controller arasındaki bir adım hâline geliyor.

---

## Route'a Ekleyip Gerçekten Çalıştığını Görelim

Dosyamız:

```text
src/routers/movies.js
```

## Örnek 13

Önce:

```js
router.post(
  "/",
  createMovie,
);
```

## Örnek 14

vardı.

Şimdi:

```js
router.post(
  "/",
  validateCreateMovieBody,
  createMovie,
);
```

## Örnek 15

olur.

Bunu soldan sağa okuyalım:

```text
POST /movies
↓
validateCreateMovieBody
↓
createMovie
```

## Örnek 16

Geçerli Body gelirse Middleware:

```js
next();
```

## Örnek 17

çağırır ve Controller'a geçilir.

Geçersiz Body gelirse Middleware:

```js
return res.status(400).json(...);
```

## Örnek 18

ile Response'u gönderir.

Controller hiç çalışmaz.

---

## Akışı Terminalde Gözümüzle Görelim

Middleware öğrenirken yalnızca diyagram okumak yerine bir kez gerçekten gözlemleyelim.

Geçici olarak Middleware'e:

```js
console.log("1 - validation çalıştı");
```

## Örnek 19

Controller'ın başına da:

```js
console.log("2 - createMovie çalıştı");
```

## Örnek 20

ekleyelim.

### Geçersiz Request

```json
{
  "title": "   ",
  "releaseYear": "2014"
}
```

## Örnek 21

Gönderdiğimizde terminalde yalnızca:

```text
1 - validation çalıştı
```

## Örnek 22

bekleriz.

Neden ikinci satır yok?

Çünkü validation hata verdi ve Middleware Request'i burada bitirdi.

Akış:

```text
Request
↓
Validation Middleware
↓
hata
↓
400 Response
↓
DUR
```

## Örnek 23

### Geçerli Request

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "voteAverage": 8.7
}
```

## Örnek 24

Gönderdiğimizde:

```text
1 - validation çalıştı
2 - createMovie çalıştı
```

## Örnek 25

görürüz.

Bu küçük deney Middleware sırasını çok somut hâle getirir.

Deney bittikten sonra geçici `console.log()` satırlarını silebiliriz.

---

## Controller Şimdi Nasıl Görünüyor?

Validation kodunu çıkardıktan sonra `createMovie` tekrar asıl işine yaklaşır:

```js
export const createMovie = async (req, res) => {
  const {
    title,
    releaseYear,
    voteAverage,
  } = req.body;

  const movie = new Movie({
    title,
    releaseYear,
    voteAverage,
  });

  await movie.save();

  res.status(201).json({
    message: "Film oluşturuldu.",
    data: movie,
  });
};
```

## Örnek 26

Artık ilk bakışta Controller'ın işi görülebiliyor:

```text
Body'den film alanlarını al
↓
Movie oluştur
↓
kaydet
↓
Response gönder
```

## Örnek 27

Bu yalnızca daha kısa kod değildir.

Sorumluluk daha görünür hâle gelmiştir.

---

## Ama PATCH Geldiğinde Yeni Bir Sorun Çıkıyor

POST için:

```js
validateCreateMovieBody
```

## Örnek 28

yazdık.

Şimdi PATCH için de benzer bir Middleware yazabiliriz:

```js
validateUpdateMovieBody
```

## Örnek 29

Ama içindeki kod neredeyse tamamen aynı olur.

Tek fark:

```text
POST
↓
createMovieSchema

PATCH
↓
updateMovieSchema
```

## Örnek 30

olacaktır.

Yani Controller'daki tekrarı kaldırırken bu kez Middleware dosyasında yeni bir tekrar oluşturmuş oluruz.

Şunu yapmak istemiyoruz:

```text
validateCreateMovieBody
↓
20 satır validation kodu

validateUpdateMovieBody
↓
aynı 20 satır validation kodu
```

## Örnek 31

Asıl değişen yalnızca Schema ise, validation mekanizmasını bir kez yazıp Schema'yı dışarıdan verebiliriz.

İşte `validateBody(schema)` fikri burada doğuyor.

---

## `validateBody(schema)` ile Aynı Mekanizmayı Tek Yere Toplayalım

Final Middleware'imiz:

```js
export const validateBody = (schema) => {
  return (req, res, next) => {
    const {
      error,
      value,
    } = schema.validate(
      req.body,
      {
        abortEarly: false,
        convert: false,
      },
    );

    if (error !== undefined) {
      return res.status(400).json({
        message: "Geçersiz Request Body.",
        errors: error.details.map(
          (detail) => detail.message,
        ),
      });
    }

    req.body = value;

    next();
  };
};
```

## Örnek 32

İlk bakışta burada iki fonksiyon var.

Bu yapı yeni görünebilir.

Bu yüzden önce kullanımına bakalım:

```js
validateBody(createMovieSchema)
```

## Örnek 33

Bunu Türkçe olarak şöyle okuyabiliriz:

> `createMovieSchema` ile çalışan bir Body validation Middleware'i hazırla.

PATCH için:

```js
validateBody(updateMovieSchema)
```

## Örnek 34

şöyle okunur:

> `updateMovieSchema` ile çalışan bir Body validation Middleware'i hazırla.

Yani:

```text
validation mekanizması
↓
validateBody

hangi kurallar kullanılacak?
↓
verdiğimiz Joi Schema
```

## Örnek 35

---

## İki Fonksiyon Neden Var?

Şu kısım:

```js
export const validateBody = (schema) => {
```

## Örnek 36

Schema'yı alır.

Ama Express'in Route üzerinde çalıştıracağı Middleware şu imzaya sahip olmalıdır:

```js
(req, res, next) => {
  ...
}
```

## Örnek 37

Bu nedenle dış fonksiyon Schema'yı alıp bize gerçek Middleware fonksiyonunu döndürür.

Akış:

```text
validateBody(createMovieSchema)
↓
createMovieSchema bilgisi alınır
↓
(req, res, next) => { ... }
Middleware fonksiyonunu döndürür
```

## Örnek 38

Bunu bir fabrika gibi ezberlememize gerek yok.

Bizim için yeterli mental model:

```text
Schema ver
↓
o Schema ile çalışan Middleware'i al
```

## Örnek 39

olmalıdır.

---

## `validateBody(schema)` İki Farklı Zamanda Nasıl Çalışıyor?

Şu Route'u düşünelim:

```js
router.post(
  "/",
  validateBody(createMovieSchema),
  createMovie,
);
```

## Örnek 40

Uygulama Router'ı oluştururken:

```js
validateBody(createMovieSchema)
```

## Örnek 41

çağrılır.

Bu çağrı bir Middleware fonksiyonu üretir.

Sonra gerçek HTTP Request geldiğinde çalışan fonksiyon:

```js
(req, res, next) => {
  ...
}
```

## Örnek 42

kısmıdır.

İki zamanı ayıralım:

```text
Uygulama hazırlanırken
↓
validateBody(createMovieSchema)
↓
Middleware hazırlandı
```

## Örnek 43



```text
HTTP Request geldiğinde
↓
req.body doğrulandı
↓
400 veya next()
```

## Örnek 44

Bu ayrım, iki fonksiyonlu yapının neden var olduğunu anlamayı kolaylaştırır.

---

## POST ve PATCH Route'larını Final Hâline Getirelim

Dosya:

```text
src/routers/movies.js
```

## Örnek 45

Import'lar:

```js
import {
  validateBody,
} from "../middlewares/validateBody.js";

import {
  createMovieSchema,
  updateMovieSchema,
} from "../validation/movieSchemas.js";
```

## Örnek 46

POST:

```js
router.post(
  "/",
  validateBody(createMovieSchema),
  createMovie,
);
```

## Örnek 47

PATCH:

```js
router.patch(
  "/:movieId",
  validateBody(updateMovieSchema),
  updateMovieById,
);
```

## Örnek 48

Şimdi iki Route'u yan yana okuyalım:

```text
POST /movies
↓
createMovieSchema ile Body'yi doğrula
↓
createMovie
```

## Örnek 49



```text
PATCH /movies/:movieId
↓
updateMovieSchema ile Body'yi doğrula
↓
updateMovieById
```

## Örnek 50

Router validation'ın nasıl yapıldığını bilmiyor.

Yalnızca Request akışını sıraya koyuyor.

---

## `req.body = value` Neden Middleware'de?

59. bölümde Joi'nin:

```js
const {
  error,
  value,
} = schema.validate(...);
```

## Örnek 51

şeklinde sonuç verdiğini gördük.

Validation başarılı olduğunda Controller'ın Joi'nin doğrulamadan geçirdiği değerle devam etmesini istiyoruz.

Bu nedenle:

```js
req.body = value;
```

## Örnek 52

yazıyoruz.

Sonra:

```js
next();
```

## Örnek 53

ile sıradaki adıma geçiyoruz.

Akış:

```text
orijinal req.body
↓
Joi validation
↓
value
↓
req.body = value
↓
next()
↓
Controller
```

## Örnek 54

Express Middleware'lerinin `req` nesnesi üzerinde değişiklik yapabilmesi normaldir.

Burada bunu bilinçli kullanıyoruz.

---

## Validation Başarısızsa `next()` Neden Çalışmıyor?

Kodumuz:

```js
if (error !== undefined) {
  return res.status(400).json({
    message: "Geçersiz Request Body.",
    errors: error.details.map(
      (detail) => detail.message,
    ),
  });
}
```

## Örnek 55

şeklinde.

Burada:

```text
hata var
↓
400 Response gönder
↓
return
↓
Middleware biter
```

## Örnek 56

`next()` çağrılmaz.

Bu yüzden Controller çalışmaz.

Geçerli durumda ise:

```js
next();
```

## Örnek 57

çağrılır.

Bu kez Express sıradaki fonksiyona geçer.

Middleware sırası bu nedenle önemlidir.

---

## `createMovie` Controller'ında Önce ve Sonra

### Önce

Controller'ın başında:

```text
Joi Schema çalıştır
↓
options ver
↓
error kontrol et
↓
error.details dönüştür
↓
400 Response hazırla
↓
sonra filmi oluştur
```

## Örnek 58

vardı.

### Sonra

Controller:

```js
export const createMovie = async (req, res) => {
  const {
    title,
    releaseYear,
    voteAverage,
  } = req.body;

  const movie = new Movie({
    title,
    releaseYear,
    voteAverage,
  });

  await movie.save();

  res.status(201).json({
    message: "Film oluşturuldu.",
    data: movie,
  });
};
```

## Örnek 59

hâline gelir.

Artık validation mekanizması Controller'ın içinde görünmüyor.

Ama Request yine validation'dan geçiyor.

Bu önemli farktır:

```text
kodu Controller'dan çıkardık
≠
validation'ı kaldırdık
```

## Örnek 60

---

## PATCH Controller'da Ne Kayboluyor, Ne Kalıyor?

59. bölümde `updateMovieSchema` içine:

```js
.min(1)
```

## Örnek 61

eklemiştik.

Bu nedenle eski:

```js
const hasUpdate =
  title !== undefined ||
  releaseYear !== undefined ||
  voteAverage !== undefined;
```

## Örnek 62

kontrolünü artık Controller'da tutmamıza gerek yok.

Validation Middleware boş Body'yi Controller'a ulaştırmaz.

Ama şu kod kalır:

```js
const fieldsToSet = {};

if (title !== undefined) {
  fieldsToSet.title = title;
}

if (releaseYear !== undefined) {
  fieldsToSet.releaseYear = releaseYear;
}

if (voteAverage !== undefined) {
  fieldsToSet.voteAverage = voteAverage;
}
```

## Örnek 63

Çünkü bu kod validation yapmıyor.

Bu kod:

```text
hangi alanlar update sorgusuna girecek?
```

## Örnek 64

sorusunu cevaplıyor.

Aynı şekilde:

```js
runValidators: true
```

## Örnek 65

kalmaya devam eder.

Çünkü bu Mongoose update validation'ıdır.

---

## Doğrulama Akışındaki Üç Katmanın Görevi

Artık doğrulama akışında üç farklı sorumluluk yan yana bulunuyor.

### Joi Schema

```text
createMovieSchema
updateMovieSchema
```

## Örnek 66

Görevi:

```text
Request Body hangi kurallara uymalı?
```

## Örnek 67

### Express Validation Middleware

```text
validateBody(schema)
```

## Örnek 68

Görevi:

```text
Joi validation'ı Request ile Controller arasında çalıştır
```

## Örnek 69

### Mongoose Validation

```text
required
min
max
runValidators
```

## Örnek 70

Görevi:

```text
MongoDB'ye yazılacak Document kurallarını koru
```

## Örnek 71

Bunları tek çizgide görelim:

```text
HTTP Request
↓
validateBody(createMovieSchema)
↓
Controller
↓
Mongoose validation
↓
MongoDB
```

## Örnek 72

Bu diyagram 59 ve 60. bölümlerin birlikte oluşturduğu yapıdır.

---

## Express Validation Middleware ile Mongoose Middleware Nerede Çalışır?

54. bölümde Mongoose Query Middleware görmüştük:

```js
movieSchema.pre(
  "findOneAndUpdate",
  function () {
    ...
  },
);
```

## Örnek 73

60. bölümdeki:

```js
validateBody(schema)
```

## Örnek 74

ise Express Middleware'dir.

Fark:

```text
Express Middleware
↓
HTTP Request akışında çalışır
```

## Örnek 75



```text
Mongoose Middleware
↓
Mongoose işlemi çevresinde çalışır
```

## Örnek 76

Aynı "Middleware" kelimesi kullanılsa da çalışma katmanları farklıdır.

---

## Neden `validateBody` Joi Schema'larını Kendi İçinde Import Etmiyor?

Şöyle yazabilirdik:

```js
validateCreateMovieBody
validateUpdateMovieBody
```

## Örnek 77

ve her Middleware kendi Schema'sını import edebilirdi.

Ama o zaman validation mekanizması yeniden çoğalırdı.

Bizim çözümümüzde:

```js
validateBody(createMovieSchema)
```

## Örnek 78

ve:

```js
validateBody(updateMovieSchema)
```

## Örnek 79

kullanıyoruz.

Böylece:

```text
nasıl validation yapılır?
↓
validateBody.js

hangi alanlar geçerlidir?
↓
movieSchemas.js

hangi Route hangi Schema'yı kullanır?
↓
movies.js Router
```

## Örnek 80

sorumlulukları ayrılır.

Bu ayrım büyüyen projelerde kodu takip etmeyi kolaylaştırır.

---

## Postman ile Middleware Akışını Test Edelim

### Geçersiz POST

```http
POST http://localhost:3000/movies
```

## Örnek 81

Body:

```json
{
  "title": "   ",
  "releaseYear": "2014"
}
```

## Örnek 82

Beklenen akış:

```text
Request
↓
validateBody(createMovieSchema)
↓
hata
↓
400 Response
↓
createMovie ÇALIŞMAZ
```

## Örnek 83

### Geçerli POST

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3
}
```

## Örnek 84

Beklenen:

```text
validation başarılı
↓
next()
↓
createMovie
↓
201 Created
```

## Örnek 85

### Boş PATCH

```http
PATCH http://localhost:3000/movies/MOVIE_ID
```

## Örnek 86

Body:

```json
{}
```

## Örnek 87

Beklenen:

```text
updateMovieSchema.min(1)
↓
hata
↓
400
↓
updateMovieById çalışmaz
```

## Örnek 88

### Geçerli PATCH

```json
{
  "voteAverage": 9.1
}
```

## Örnek 89

Beklenen:

```text
validation başarılı
↓
next()
↓
updateMovieById
↓
findByIdAndUpdate
↓
Mongoose runValidators
↓
200 OK
```

## Örnek 90

---

## Proje Yapımız Şimdi Ne Anlatıyor?

Bu noktada `src` klasörümüzün ilgili kısmı şöyle düşünülebilir:

```text
src
├── constants
│   ├── movie.js
│   └── pagination.js
├── controllers
│   └── movies.js
├── db
│   └── models
│       ├── director.js
│       └── movie.js
├── middlewares
│   ├── errorHandler.js
│   ├── requestLogger.js
│   └── validateBody.js
├── routers
│   └── movies.js
├── utils
├── validation
│   └── movieSchemas.js
├── index.js
└── server.js
```

## Örnek 91

Burada klasörler yalnızca düzen olsun diye yok.

Her birinin artık gerçek bir sorumluluğu var:

```text
validation
↓
Request kuralları

middlewares
↓
Request akışındaki ortak işlemler

controllers
↓
endpoint'in asıl işi

constants
↓
uygulamanın isim verilmiş ortak kararları
```

## Örnek 92

Bu, 37. bölümde konuştuğumuz proje mimarisinin ihtiyaç çıktıkça büyümesine iyi bir örnektir.

---

## Validation Middleware Şu Anda Neyi Kontrol Ediyor?

Bu bölümde oluşturduğumuz `validateBody(schema)` Middleware'i yalnızca:

```text
req.body
```

## Örnek 93

üzerindeki veriyi doğrular.

Örneğin POST ve PATCH isteklerinde gönderilen Movie alanları aynı doğrulama akışından geçebilir. Böylece Controller içinde tekrar eden Body kontrollerini ayrı ayrı yazmak zorunda kalmayız.

`req.params` ve `req.query` ise farklı kaynaklardan gelir:

```text
req.params
↓
URL içindeki değişken parçalar

req.query
↓
URL sonundaki query parametreleri
```

## Örnek 94

asında benzer bir tekrar oluştuğunda onları da kendi ihtiyaçlarına uygun biçimde ele alabiliriz.

---

## Bölüm Sonu Uygulaması

1. Önce `validateCreateMovieBody` gibi yalnızca POST için çalışan basit Middleware'i geçici olarak yazın.
2. Route'a ekleyip geçerli ve geçersiz Request'lerde Controller'ın çalışıp çalışmadığını `console.log()` ile gözlemleyin.
3. PATCH için aynı kodu yazmak üzereyken oluşacak tekrarı fark edin.
4. Basit Middleware'i `validateBody(schema)` yapısına dönüştürün.
5. POST Route'unda `createMovieSchema`, PATCH Route'unda `updateMovieSchema` kullanın.
6. Controller'lardaki Joi validation bloklarını kaldırın.
7. PATCH Controller'dan eski `hasUpdate` kontrolünü kaldırın fakat `fieldsToSet` whitelist'ini koruyun.
8. `runValidators: true` seçeneğini kaldırmayın.
9. Geçersiz POST, geçerli POST, boş PATCH ve geçerli PATCH senaryolarını Postman ile tekrar test edin.

---

## 🧠 Akılda Kalsın

Önceki bölümde Joi ile Request Body kurallarını tanımladık ve validation'ı Controller içinde çalıştırdık.

Bu bölümde ise aynı validation kodunun `createMovie` ve `updateMovieById` Controller'larında tekrar ettiğini gördük; tekrar eden kısmı Validation Middleware'e taşıdık.

Önce:

```text
Controller
↓
validation
↓
asıl kayıt oluşturacağım / güncelleyeceğim?
```

## Örnek 95

vardı.

Şimdi:

```text
Request
↓
Validation Middleware
↓
Controller
```

## Örnek 96

var.

`validateBody(schema)` şu fikri temsil eder:

```text
validation mekanizması aynı
↓
Schema değişebilir
```

## Örnek 97

Geçersiz Request:

```text
Middleware'de biter
↓
Controller çalışmaz
```

## Örnek 98

Geçerli Request:

```text
req.body = value
↓
next()
↓
Controller
```

## Örnek 99

Ve en önemli ayrım:

```text
Joi Schema
↓
kurallar

validateBody
↓
kuralları Request akışında çalıştıran Middleware

Mongoose validation
↓
Document kuralları
```

## Örnek 100

---

## Sonraki Adım

Artık API'ye gelen Body için daha düzenli bir giriş katmanımız var.

Fakat Postman'de farklı endpoint'lerin Response'larına baktığımızda başka bir problem görmeye başlayabiliriz.

Örneğin bir yerde:

```json
{
  "message": "Film bulunamadı."
}
```

## Örnek 101

başka bir yerde:

```json
{
  "message": "Geçersiz Request Body.",
  "errors": []
}
```

## Örnek 102

liste endpoint'inde ise:

```json
{
  "message": "Filmler getirildi.",
  "page": 1,
  "limit": 10,
  "totalItems": 42,
  "data": []
}
```
