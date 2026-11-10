# Bölüm 19 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Middleware ile Request Akışını Yönetmek

## Request Route'a Ulaşmadan Önce Ne Olur?

Önceki bölümde şu satırı kullandık:

```js
app.use(express.json());
```

## Örnek 2

Bu satır sayesinde

Request Body içinde gönderilen

JSON verisine:

```js
req.body
```

## Örnek 3

üzerinden ulaşabildik.

Akışımız şöyleydi:

```text
Postman
↓
JSON Body içeren Request
↓
express.json()
↓
req.body
↓
Route
↓
Response
```

## Örnek 4

Burada önemli bir ayrıntı vardır.

Request,

eşleşen Route'un koduna ulaşmadan önce

başka bir işlemden geçti.

Bu işlem:

```js
express.json()
```

## Örnek 5

tarafından gerçekleştirildi.

Peki Route'tan önce

çalışabilen bu yapı nedir?

Express'te bu tür yapılara:

```text
middleware
```

## Örnek 6

denir.

## Middleware Nedir?

Middleware,

bir Request'in

uygulamadaki yolculuğu sırasında

çalışabilen bir fonksiyondur.

Başlangıç düzeyinde

şöyle düşünebiliriz:

```text
Request gelir.
↓
Middleware çalışır.
↓
Request üzerinde bir işlem yapılabilir.
↓
Akış devam eder.
↓
Route çalışır.
↓
Response gönderilir.
```

## Örnek 7

Bir middleware:

```text
Request hakkında bilgi okuyabilir,

Request üzerinde işlem yapabilir,

bazı kontroller gerçekleştirebilir,

Response gönderebilir,

veya akışı sonraki adıma bırakabilir.
```

## Örnek 8

Middleware kavramını

bir sinemanın girişindeki

ara kontrol noktaları gibi düşünebiliriz.

İzleyici doğrudan salona gitmeden önce:

```text
bilet kontrolünden,

güvenlik kontrolünden,

ve yönlendirme noktasından
```

## Örnek 9

geçebilir.

Request de

Route'a ulaşmadan önce

bir veya daha fazla middleware'den geçebilir.

Ancak bu benzetmede

önemli olan kontrolün kendisi değil,

**akışın sırasıdır**:

```text
Request
↓
Middleware
↓
Middleware
↓
Route
↓
Response
```

## Örnek 10

🧠 Akılda Kalsın

> Middleware,
> Request ile Response arasındaki akışta
> çalışabilen ara fonksiyonlardan biridir.

![Express middleware istek akışı](images/19-middleware-akisi-baski.svg)

## İlk Middleware'imizi Oluşturalım

Şimdi kendi middleware'imizi yazalım.

`index.js` dosyamızda

Route'lardan önce şu kodu ekleyelim:

```js
app.use((req, res, next) => {
  console.log("Yeni bir Request geldi.");

  next();
});
```

## Örnek 11

Dosyamızın ilgili bölümü

şuna benzer görünebilir:

```js
import express from "express";

const app = express();

app.use((req, res, next) => {
  console.log("Yeni bir Request geldi.");

  next();
});

app.get("/movies", (req, res) => {
  res.send("Filmler");
});

app.listen(3000, () => {
  console.log("Server 3000 portunda çalışıyor.");
});
```

## Örnek 12

Uygulamayı:

```bash
npm start
```

## Örnek 13

ile çalıştıralım.

Ardından tarayıcıdan:

```text
http://localhost:3000/movies
```

## Örnek 14

adresine gidelim.

Terminalde:

```text
Yeni bir Request geldi.
```

## Örnek 15

mesajını görürüz.

Tarayıcıda ise:

```text
Filmler
```

## Örnek 16

Response'u görürüz.

Demek ki aynı Request sırasında

iki farklı işlem gerçekleşti:

```text
Request
↓
Middleware çalıştı.
↓
Terminale mesaj yazıldı.
↓
Route çalıştı.
↓
Response gönderildi.
```

## Örnek 17

Buradaki yeni parça:

```js
next
```

## Örnek 18

parametresidir.

Şimdi onun görevini inceleyelim.

## next() Ne İşe Yarar?

Middleware fonksiyonumuz:

```js
(req, res, next) => {
  console.log("Yeni bir Request geldi.");

  next();
}
```

## Örnek 19

şeklindeydi.

Burada:

```js
req
```

## Örnek 20

Request'i,

```js
res
```

## Örnek 21

Response'u temsil eder.

Üçüncü parametre olan:

```js
next
```

## Örnek 22

ise Express'in

akışı bir sonraki uygun adıma

devam ettirmesini sağlayan

bir fonksiyondur.

```js
next();
```

## Örnek 23

çalıştığında Express:

```text
Bu middleware'in işi tamamlandı.
↓
Akışa devam et.
```

## Örnek 24

mantığıyla ilerler.

Bir sonraki adım:

```text
başka bir middleware

veya

eşleşen Route
```

## Örnek 25

olabilir.

Örneğimizde:

```text
GET /movies
↓
Middleware
↓
next()
↓
app.get("/movies")
↓
Response
```

## Örnek 26

akışı oluşur.

Bu nedenle `next()`:

> Her zaman doğrudan Route'a git

anlamına gelmez.

Daha doğru ifade şudur:

> Uygulamadaki sıraya göre
> bir sonraki uygun middleware veya Route handler'a geç.

Bu ayrım,

birden fazla middleware kullandığımızda

daha anlamlı hâle gelecektir.

## next() Çağrılmazsa Ne Olur?

Şimdi middleware'imizi

şöyle yazdığımızı düşünelim:

```js
app.use((req, res, next) => {
  console.log("Yeni bir Request geldi.");
});
```

## Örnek 27

Dikkat edersek:

```js
next();
```

## Örnek 28

yok.

Middleware ayrıca:

```js
res.send()
```

## Örnek 29

gibi bir yöntemle

Response da göndermiyor.

Bu durumda Request:

```text
Request
↓
Middleware
↓
Akış burada kalır.
```

## Örnek 30

Route'a devam edemez.

İstemci de

beklediği Response'u alamaz

ve Request beklemede kalabilir.

Bu nedenle bir middleware

genellikle iki yoldan biriyle

işini tamamlar:

```text
1
next()
↓
Akışı devam ettir.

veya

2
Response gönder.
↓
Request'i burada sonuçlandır.
```

## Örnek 31

Örneğin middleware'in kendisi

Response gönderebilir:

```js
app.use((req, res, next) => {
  res.send("Request burada sonlandırıldı.");
});
```

## Örnek 32

Bu durumda:

```js
next();
```

## Örnek 33

çağırmamamız normaldir.

Çünkü Response zaten gönderilmiştir.

🧠 Akılda Kalsın

```text
Middleware
↓
next()
↓
Akış devam eder.
```

## Örnek 34

veya:

```text
Middleware
↓
Response gönderir.
↓
Akış burada sonuçlanır.
```

## Örnek 35

Fakat middleware:

```text
ne next() çağırır

ne de Response gönderirse
```

## Örnek 36

Request'in yolculuğu tamamlanmayabilir.

## Middleware'lerin Sırası Neden Önemlidir?

Express uygulamasında

middleware ve Route'ları

belirli bir sırayla yazarız.

Bu sıra önemlidir.

Örneğin:

```js
app.use((req, res, next) => {
  console.log("Middleware çalıştı.");
  next();
});

app.get("/movies", (req, res) => {
  res.send("Filmler");
});
```

## Örnek 37

kodunda middleware

Route'tan önce tanımlanmıştır.

Bu nedenle:

```text
Request
↓
Middleware
↓
Route
↓
Response
```

## Örnek 38

akışı oluşur.

Peki sıralamayı değiştirirsek?

```js
app.get("/movies", (req, res) => {
  res.send("Filmler");
});

app.use((req, res, next) => {
  console.log("Middleware çalıştı.");
  next();
});
```

## Örnek 39

`GET /movies` Request'i geldiğinde

ilk Route eşleşir

ve:

```js
res.send("Filmler");
```

## Örnek 40

ile Response'u gönderir.

Bu Route akışı

sonraki adıma bırakmadığı için

alttaki middleware

bu Request için çalışmaz.

```text
Kod sırası
↓
Request'in hangi yapılardan
geçeceğini etkileyebilir.
```

## Örnek 41

Bu konu özellikle

`express.json()` için önemlidir.

JSON Body'yi Route içinde

kullanmak istiyorsak:

```js
app.use(express.json());
```

## Örnek 42

satırını

ilgili Route'lardan önce

tanımlamalıyız.

## Bir Middleware Bütün Request'lerde Çalışabilir mi?

Evet.

Şu kullanım:

```js
app.use((req, res, next) => {
  console.log(`${req.method} ${req.path}`);

  next();
});
```

## Örnek 43

uygun Request'lerde

genel bir middleware olarak

akışa katılır.

Örneğin:

```text
GET /movies
POST /movies
GET /about
```

## Örnek 44

Request'leri geldiğinde

terminalde şuna benzer çıktılar görebiliriz:

```text
GET /movies
POST /movies
GET /about
```

## Örnek 45

Bu sayede aynı kodu

her Route'un içine

tekrar tekrar yazmamıza gerek kalmaz.

Şöyle bir tekrar yerine:

```js
app.get("/movies", (req, res) => {
  console.log("Request geldi.");
  res.send("Filmler");
});

app.get("/about", (req, res) => {
  console.log("Request geldi.");
  res.send("Hakkında");
});
```

## Örnek 46

ortak işlemi middleware'e taşıyabiliriz:

```js
app.use((req, res, next) => {
  console.log("Request geldi.");
  next();
});
```

## Örnek 47

Ardından Route'larımız

yalnızca kendi görevlerine odaklanabilir:

```js
app.get("/movies", (req, res) => {
  res.send("Filmler");
});

app.get("/about", (req, res) => {
  res.send("Hakkında");
});
```

## Örnek 48

Bu yaklaşım,

uygulama büyüdükçe

tekrarı azaltmamıza yardımcı olur.

## Middleware'i Belirli Bir Route'ta Kullanabilir miyiz?

Evet.

Bir middleware'in

bütün Request'lerde çalışması

zorunlu değildir.

Önce middleware fonksiyonumuzu

bir değişkende oluşturalım:

```js
const logMovieRequest = (req, res, next) => {
  console.log("Movies Route'una Request geldi.");

  next();
};
```

## Örnek 49

Ardından onu

yalnızca belirli bir Route'ta kullanabiliriz:

```js
app.get(
  "/movies",
  logMovieRequest,
  (req, res) => {
    res.send("Filmler");
  },
);
```

## Örnek 50

Bu Request geldiğinde:

```text
GET /movies
↓
logMovieRequest
↓
next()
↓
Route handler
↓
Response
```

## Örnek 51

akışı gerçekleşir.

Başka bir Route:

```js
app.get("/about", (req, res) => {
  res.send("Hakkında");
});
```

## Örnek 52

bu middleware'i kullanmadığı için

`logMovieRequest`

bu Route için çalışmaz.

Şimdilik iki temel kullanım biçimini

ayırt etmemiz yeterlidir:

```text
app.use(middleware)
↓
Middleware'i genel akışa ekleyebilir.

Route içinde middleware
↓
Belirli bir Route'un akışına eklenebilir.
```

## Örnek 53

İlerleyen bölümlerde

authentication,

validation

ve hata yönetimi gibi konularda

middleware yapılarını

çok daha fazla kullanacağız.

## express.json() Neden Bir Middleware'dir?

Artık önceki bölümde kullandığımız:

```js
app.use(express.json());
```

## Örnek 54

satırını daha iyi anlayabiliriz.

Buradaki:

```js
express.json()
```

## Örnek 55

Express'in bize sunduğu

hazır bir middleware'dir.

Uygun JSON içeriği taşıyan

Request'lerin Body bölümünü işler

ve ayrıştırılan veriyi:

```js
req.body
```

## Örnek 56

üzerinden kullanabilmemizi sağlar.

Akış şöyle gerçekleşir:

```text
JSON Body içeren Request
↓
express.json()
↓
Body işlenir.
↓
req.body hazırlanır.
↓
next adım
↓
Route
```

## Örnek 57

Bu nedenle:

```js
app.use(express.json());

app.post("/movies", (req, res) => {
  console.log(req.body);

  res.json({
    message: "Film bilgileri alındı.",
    data: req.body,
  });
});
```

## Örnek 58

sıralaması anlamlıdır.

`express.json()`:

```text
Route'un işi değildir.
```

## Örnek 59

Request Route'a ulaşmadan önce

Body üzerinde yapılması gereken

ortak bir işlemdir.

Bu nedenle middleware olarak

akışa eklenir.

Şimdi 18. bölümde kullandığımız satırı

daha doğru okuyabiliriz:

```js
app.use(express.json());
```

## Örnek 60



```text
app.use(...)
↓
Middleware'i uygulama akışına ekle.

express.json()
↓
JSON Body'yi işleyen hazır middleware.
```

## Örnek 61

🧠 Akılda Kalsın

```text
Request
↓
Middleware
↓
Route
↓
Response
```

## Örnek 62

Middleware:

```text
Request üzerinde işlem yapabilir,

akışı next() ile devam ettirebilir,

veya gerektiğinde Response göndererek
akışı sonlandırabilir.
```

## Örnek 63

## Bölüm Özeti

Bu bölümde

middleware'in

Request akışında çalışan

ara fonksiyonlardan biri olduğunu öğrendik.

İlk middleware'imizi:

```js
app.use((req, res, next) => {
  console.log("Yeni bir Request geldi.");

  next();
});
```

## Örnek 64

şeklinde oluşturduk.

`next()` fonksiyonunun:

```text
Akışı bir sonraki uygun
middleware veya Route handler'a
devam ettirdiğini
```

## Örnek 65

gördük.

Middleware'in:

```text
next() çağırabileceğini

veya

Response göndererek
Request'i sonuçlandırabileceğini
```

## Örnek 66

ayırt ettik.

Ayrıca kod sırasının

Request akışını etkileyebildiğini gördük.

```text
Request
↓
Kodda önce karşılaştığı uygun yapı
↓
Sonraki uygun yapı
↓
Response
```

## Örnek 67

Genel middleware kullanımını:

```js
app.use(middleware);
```

## Örnek 68

ve belirli bir Route'a middleware eklemeyi

uygulamalı olarak inceledik.

Son olarak:

```js
express.json()
```

## Örnek 69

yapısının

Express'in hazır middleware'lerinden biri olduğunu

ve JSON Body'yi işleyerek:

```js
req.body
```

## Örnek 70

üzerinden kullanılabilir hâle getirdiğini gördük.

## Sonraki Adım

Artık Express uygulamamızın

Request akışını daha iyi anlayabiliyoruz.

```text
Request
↓
Middleware
↓
Route
↓
Response
```

## Örnek 71

Fakat uygulamamızda

hâlâ önemli bir eksik var.

Şu ana kadar film bilgilerimizi

ya kodun içine yazdık

ya da Request Body üzerinden aldık.

Örneğin bir kullanıcı bize:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014
}
```

## Örnek 72

verisini gönderdiğinde

bu bilgiye `req.body` üzerinden ulaşabiliyoruz.

Ama Request bittikten sonra

bu filmi kalıcı olarak nerede tutacağız?

Sunucuyu kapatıp yeniden açtığımızda

verilerimizin kaybolmamasını

nasıl sağlayacağız?

```text
Request ile veri gelir.
↓
Backend veriyi işler.
↓
?
↓
Veri daha sonra tekrar kullanılabilmeli.
```

## Örnek 73

Eksik olan parça:

```text
Veritabanı
```
