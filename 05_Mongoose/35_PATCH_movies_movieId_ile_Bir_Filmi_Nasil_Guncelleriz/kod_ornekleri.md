# Bölüm 35 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# PATCH /movies/:movieId ile Bir Filmi Nasıl Güncelleriz?

## Dune Doğru Film. Ama Puanı Artık Doğru Değil.

Bir önceki bölümde tek bir filmi `_id` değeriyle bulmayı başardık.

Dune'ın MongoDB'deki kaydı şöyle olsun:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8
}
```

## Örnek 2

Şimdi puanı:

```text
8
↓
8.3
```

## Örnek 3

olarak güncellememiz gerekiyor.

Başlık doğru.

Yayın yılı doğru.

Film doğru.

Değişmesini istediğimiz yalnızca:

```text
voteAverage
```

## Örnek 4

alanı.

Filmi silip yeniden oluşturmayacağız.

Mevcut document'ı bulup yalnızca gereken alanı değiştireceğiz.

İlk kez bir kaydı:

```text
okumak
```

## Örnek 5

yerine:

```text
güncelleyeceğiz.
```

## Örnek 6

---

## Bu İş İçin Neden PATCH?

HTTP yöntemlerini öğrenirken `PUT` ile `PATCH` arasındaki farkı görmüştük.

Kısaca hatırlayalım:

```text
PUT
↓
mevcut kaynağın tamamını
güncelleme fikri

PATCH
↓
mevcut kaynağın
belirli alanlarını güncelleme fikri
```

## Örnek 7

Biz Dune'ın tamamını değiştirmiyoruz.

Yalnızca puanını değiştiriyoruz.

Bu yüzden Request'imiz:

```http
PATCH /movies/:movieId
```

## Örnek 8

olacak.

Gerçek Request'te `:movieId` yerine Dune'ın gerçek `_id` değeri bulunur:

```text
PATCH http://localhost:3000/movies/SIZIN_DUNE_ID_DEGERINIZ
```

## Örnek 9

---

## URL Bir Şeyi, Body Başka Bir Şeyi Söylüyor

Bir güncelleme Request'inde backend'in iki şeyi bilmesi gerekir:

```text
1. Hangi film değişecek?

2. Filmde ne değişecek?
```

## Örnek 10

Birincisinin cevabı URL'de:

```text
/movies/SIZIN_DUNE_ID_DEGERINIZ
```

## Örnek 11

İkincisinin cevabı Request Body'de:

```json
{
  "voteAverage": 8.3
}
```

## Örnek 12

Yani:

```text
URL
↓
hangi film?

Request Body
↓
hangi alan değişecek?
```

## Örnek 13

Bu bölümün merkezindeki fikir tam olarak budur.

---

## Hangi Dosyada Çalışıyoruz?

Yine:

```text
index.js
```

## Örnek 14

dosyasındayız.

34. bölümün sonunda burada zaten:

```text
GET /movies
GET /movies/:movieId
POST /movies
```

## Örnek 15

Route'larımız vardı.

Ayrıca:

```js
import mongoose from "mongoose";
```

## Örnek 16

ve:

```js
import { Movie } from "./src/db/models/movie.js";
```

## Örnek 17

import'ları da hazır.

Yeni bir dosya oluşturmuyoruz.

API'mize yalnızca yeni bir davranış ekleyeceğiz:

```http
PATCH /movies/:movieId
```

## Örnek 18

---

## Önce Route'un İskeletini Kuralım

`index.js` içine:

```js
app.patch("/movies/:movieId", async (req, res) => {
});
```

## Örnek 19

ekleyelim.

Bu Route'ta iki eski bilgi aynı yerde buluşacak:

```text
req.params
↓
hangi film?

req.body
↓
hangi değişiklik?
```

## Örnek 20

Örneğin:

```text
req.params
↓
{
  movieId: "DUNE_ID"
}

req.body
↓
{
  voteAverage: 8.3
}
```

## Örnek 21

---

## Önce Hangi Filmi Güncelleyeceğimizi Bulalım

34. bölümde URL'deki `movieId` değerini şöyle almıştık:

```js
const { movieId } = req.params;
```

## Örnek 22

Aynı ObjectId kontrolünü burada da kullanacağız:

```js
if (!mongoose.isObjectIdOrHexString(movieId)) {
  return res.status(400).json({
    message: "Geçersiz film kimliği.",
  });
}
```

## Örnek 23

Bu kontrol artık yeni değil.

API'mizin alışkanlığı hâline geliyor:

```text
movieId biçimi yanlış
↓
400 Bad Request
```

## Örnek 24

Kimlik uygunsa filmi bulabiliriz.

---

## Dune'ı Bulalım

Filmi bulmayı zaten biliyoruz:

```js
const movie = await Movie.findById(movieId);
```

## Örnek 25

Film yoksa:

```js
movie === null
```

## Örnek 26

olur.

Bu durumda:

```js
if (movie === null) {
  return res.status(404).json({
    message: "Film bulunamadı.",
  });
}
```

## Örnek 27

kullanacağız.

Yani:

```text
geçerli kimlik
↓
ama film yok
↓
404 Not Found
```

## Örnek 28

Şimdi Dune bulundu.

Asıl yeni noktaya geldik.

---

## Dune'ı Bulduk. Puanını Değiştirince MongoDB de Değişir mi?

Biraz önce şu kodla Dune'ı bulduk:

```js
const movie = await Movie.findById(movieId);
```

## Örnek 29

Bu işlemden sonra `movie` değişkeninin içinde Dune'ı temsil eden bir Mongoose Document vardır.

Şu anda puanı:

```js
movie.voteAverage
```

## Örnek 30

üzerinden okuyabiliriz.

Örneğin:

```text
8
```

## Örnek 31

olsun.

Şimdi şu satırı yazalım:

```js
movie.voteAverage = 8.3;
```

## Örnek 32

Bu satır ne yaptı?

Dune'ı temsil eden **Mongoose Document'ın içindeki** `voteAverage` değerini değiştirdi.

Yani uygulamamızın içinde artık:

```text
movie.voteAverage
↓
8.3
```

## Örnek 33

var.

Fakat MongoDB'deki kayıt hâlâ:

```text
voteAverage
↓
8
```

## Örnek 34

olabilir.

Çünkü şu ana kadar MongoDB'ye:

```text
Bu değişikliği kaydet.
```

## Örnek 35

demedik.

İki tarafı yan yana görelim:

```text
Node.js uygulamasındaki Mongoose Document
voteAverage: 8.3

MongoDB'deki kayıt
voteAverage: 8
```

## Örnek 36

Yani şu satır:

```js
movie.voteAverage = 8.3;
```

## Örnek 37

yalnızca elimizdeki Document'ı değiştirir.

**MongoDB'ye otomatik olarak güncelleme göndermez.**

Değişikliğin MongoDB'de de kalıcı olması için birazdan:

```js
await movie.save();
```

## Örnek 38

yazacağız.

Akışımız şu olacak:

```text
Dune'ı bul
↓
movie.voteAverage = 8.3
↓
Document değişti
↓
movie.save()
↓
MongoDB'deki kayıt da güncellendi
```

## Örnek 39

---

## Eski Dostumuz save() Geri Döndü

31. bölümde ilk filmimizi kaydederken:

```js
await movie.save();
```

## Örnek 40

kullanmıştık.

Aynı `save()` mevcut bir Mongoose Document üzerinde yaptığımız değişiklikleri de MongoDB'ye kaydedebilir.

```js
movie.voteAverage = 8.3;

await movie.save();
```

## Örnek 41

Akış:

```text
Dune bulundu
↓
movie.voteAverage değişti
↓
Document değişikliği taşıyor
↓
movie.save()
↓
MongoDB güncellendi
```

## Örnek 42

Böylece daha önce öğrendiğimiz `save()` yeni bir anlam kazanıyor.

---

## Ama 8.3 Değerini Kodun İçine Yazmayacağız

Şu satır yalnızca fikri gösteriyordu:

```js
movie.voteAverage = 8.3;
```

## Örnek 43

Gerçek API'de yeni değer Request'ten gelecek.

Body:

```json
{
  "voteAverage": 8.3
}
```

## Örnek 44

olduğuna göre değer:

```js
req.body.voteAverage
```

## Örnek 45

içinde.

Fakat PATCH'in önemli bir özelliğini korumamız gerekiyor.

---

## PATCH Demek Gönderilmeyen Alanlara Dokunmamak Demektir

Dune:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8
}
```

## Örnek 46

olsun.

Biz yalnızca:

```json
{
  "voteAverage": 8.3
}
```

## Örnek 47

gönderiyoruz.

Beklediğimiz sonuç:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3
}
```

## Örnek 48

Yani:

```text
title
↓
dokunma

releaseYear
↓
dokunma

voteAverage
↓
güncelle
```

## Örnek 49

---

## Body'deki Alanları Alalım

Destructuring bilgisini yine kullanabiliriz:

```js
const {
  title,
  releaseYear,
  voteAverage,
} = req.body;
```

## Örnek 50

PATCH'te bu alanların hepsinin gelmesini beklemiyoruz.

Yalnızca puan geldiyse:

```text
title
↓
undefined

releaseYear
↓
undefined

voteAverage
↓
8.3
```

## Örnek 51

olabilir.

Bu yüzden yalnızca gerçekten gönderilen alanları değiştireceğiz.

---

## Neden `if (voteAverage)` Yazmıyoruz?

İlk akla gelen kod şu olabilir:

```js
if (voteAverage) {
  movie.voteAverage = voteAverage;
}
```

## Örnek 52

Ama bu her zaman doğru değildir.

Çünkü JavaScript'te:

```text
0
```

## Örnek 53

falsy bir değerdir.

Bir gün gerçekten:

```json
{
  "voteAverage": 0
}
```

## Örnek 54

gönderirsek `if (voteAverage)` çalışmaz.

Oysa `0` geçerli bir değerdir.

Bizim sormak istediğimiz soru:

> Bu alan gönderildi mi?

Bu yüzden:

```js
if (voteAverage !== undefined) {
  movie.voteAverage = voteAverage;
}
```

## Örnek 55

daha doğru bir kontroldür.

---

## Yalnızca Gönderilen Alanları Değiştirelim

Şöyle yazabiliriz:

```js
if (title !== undefined) {
  movie.title = title;
}

if (releaseYear !== undefined) {
  movie.releaseYear = releaseYear;
}

if (voteAverage !== undefined) {
  movie.voteAverage = voteAverage;
}
```

## Örnek 56

Body yalnızca:

```json
{
  "voteAverage": 8.3
}
```

## Örnek 57

ise yalnızca `voteAverage` değişir.

Diğer alanlar olduğu gibi kalır.

---

## Hiç Güncellenecek Alan Gönderilmezse?

Şöyle bir Body düşünün:

```json
{}
```

## Örnek 58

Film bulunabilir.

Ama değiştirecek hiçbir şey yok.

Bunun için:

```js
const hasUpdate =
  title !== undefined ||
  releaseYear !== undefined ||
  voteAverage !== undefined;

if (!hasUpdate) {
  return res.status(400).json({
    message: "Güncellenecek film bilgisi gönderilmedi.",
  });
}
```

## Örnek 59

yazabiliriz.

Akış:

```text
Body'de desteklediğimiz hiçbir alan yok
↓
400 Bad Request
```

## Örnek 60

---

## Değişiklikleri MongoDB'ye Kaydedelim

Alanları değiştirdikten sonra:

```js
await movie.save();
```

## Örnek 61

çalıştıracağız.

Bu satırdan sonra Document üzerindeki değişiklik MongoDB'ye yazılır.

`save()` sırasında Mongoose'un Schema kuralları da devreye girebilir.

Validation konusunu ileride daha ayrıntılı inceleyeceğiz.

Şimdilik bağlantımız:

```text
Document'ı değiştir
↓
save()
↓
MongoDB'ye yaz
```

## Örnek 62

---

## Başarılı Güncellemede Hangi Status Code?

Yeni film oluşturmuyoruz.

Bu yüzden:

```text
201 Created
```

## Örnek 63

kullanmıyoruz.

Mevcut filmi başarıyla güncelliyoruz ve güncel hâlini Response içinde gönderiyoruz.

Bu nedenle:

```text
200 OK
```

## Örnek 64

uygun bir seçimdir.

```js
res.status(200).json({
  message: "Film güncellendi.",
  data: movie,
});
```

## Örnek 65

`movie`, `save()` tamamlandıktan sonraki güncel Document'tır.

---

## PATCH Route'unu Tamamlayalım

Şimdi parçaları birleştirelim:

```js
app.patch("/movies/:movieId", async (req, res) => {
  try {
    const { movieId } = req.params;

    if (!mongoose.isObjectIdOrHexString(movieId)) {
      return res.status(400).json({
        message: "Geçersiz film kimliği.",
      });
    }

    const movie = await Movie.findById(movieId);

    if (movie === null) {
      return res.status(404).json({
        message: "Film bulunamadı.",
      });
    }

    const {
      title,
      releaseYear,
      voteAverage,
    } = req.body;

    const hasUpdate =
      title !== undefined ||
      releaseYear !== undefined ||
      voteAverage !== undefined;

    if (!hasUpdate) {
      return res.status(400).json({
        message: "Güncellenecek film bilgisi gönderilmedi.",
      });
    }

    if (title !== undefined) {
      movie.title = title;
    }

    if (releaseYear !== undefined) {
      movie.releaseYear = releaseYear;
    }

    if (voteAverage !== undefined) {
      movie.voteAverage = voteAverage;
    }

    await movie.save();

    res.status(200).json({
      message: "Film güncellendi.",
      data: movie,
    });
  } catch (error) {
    console.error(error);

    res.status(500).json({
      message: "Film güncellenemedi.",
    });
  }
});
```

## Örnek 66

Kod uzun görünebilir.

Ama yeni olan fikir kısa:

```text
hangi film?
↓
movieId

hangi alanlar değişecek?
↓
req.body

film bulundu
↓
yalnızca gönderilen alanları değiştir
↓
save()
```

## Örnek 67

---

## Şimdi Postman'de Deneyelim

Sunucuyu:

```bash
npm start
```

## Örnek 68

ile çalıştırın.

Dune'ın `_id` değerini bilmiyorsanız:

```http
GET http://localhost:3000/movies
```

## Örnek 69

Request'iyle tekrar görebilirsiniz.

Postman'de HTTP yöntemini:

```text
PATCH
```

## Örnek 70

olarak seçin.

Adres:

```text
http://localhost:3000/movies/SIZIN_DUNE_ID_DEGERINIZ
```

## Örnek 71

Body:

```json
{
  "voteAverage": 8.3
}
```

## Örnek 72

olsun.

`Send` düğmesine basalım.

---

## Response'ta Ne Görmeliyiz?

İşlem başarılıysa:

```text
200 OK
```

## Örnek 73

görmeliyiz.

Response Body şuna benzer olabilir:

```json
{
  "message": "Film güncellendi.",
  "data": {
    "_id": "SIZIN_DUNE_ID_DEGERINIZ",
    "title": "Dune",
    "releaseYear": 2021,
    "voteAverage": 8.3
  }
}
```

## Örnek 74

Body'de yalnızca:

```json
{
  "voteAverage": 8.3
}
```

## Örnek 75

gönderdik.

Ama `title` ve `releaseYear` hâlâ duruyor.

Çünkü onlara dokunmadık.

PATCH'in davranışı tam olarak buydu.

---

## Gerçekten MongoDB'de Değişti mi?

Response görmekle yetinmeyelim.

Tekrar:

```http
GET http://localhost:3000/movies/SIZIN_DUNE_ID_DEGERINIZ
```

## Örnek 76

Request'ini gönderelim.

Bu kez:

```json
{
  "voteAverage": 8.3
}
```

## Örnek 77

görmeliyiz.

İstersek Atlas Data Explorer'dan da kontrol edebiliriz.

Artık değişiklik yalnızca uygulamanın belleğinde değil.

MongoDB'de kalıcı.

---

## Aynı Route ile Başlığı da Değiştirebilir miyiz?

Evet.

Örneğin:

```json
{
  "title": "Dune: Çöl Gezegeni"
}
```

## Örnek 78

gönderirsek yalnızca `title` değişir.

Birden fazla alan da gönderebiliriz:

```json
{
  "title": "Dune: Çöl Gezegeni",
  "voteAverage": 8.3
}
```

## Örnek 79

Bu kez:

```text
title
↓
değişir

voteAverage
↓
değişir

releaseYear
↓
aynı kalır
```

## Örnek 80

Route'u yeniden yazmamıza gerek yok.

---

## Mongoose'ta Başka Güncelleme Yöntemleri de Var mı?

Evet.

Bu bölümde güncellemeyi bilinçli olarak şu akışla yaptık:

```text
findById()
↓
Mongoose Document'ı bul
↓
yalnızca gönderilen alanları değiştir
↓
save()
```

## Örnek 81

Çünkü ilk kez bir Document'ı güncellerken değişikliğin hangi aşamalardan geçtiğini açıkça görmek istiyoruz.

Mongoose, bir Document'ı önce uygulamaya alıp ardından `save()` çağırmadan da doğrudan update sorguları çalıştırabilir.

Fakat bu yöntemler farklı Query davranışları ve farklı validation ayrıntıları getirir. Bu nedenle onları burada araya sıkıştırmayacağız.

53. bölümde doğrudan update sorgularına geri döneceğiz; hangi durumda Document üzerinde çalışmanın, hangi durumda update Query'si kullanmanın daha uygun olduğunu ayrı bir konu olarak inceleyeceğiz.

---

## Neden req.body'yi Olduğu Gibi Document'a Vermedik?

Daha kısa bir kod yazabilirdik.

Ama şu anda API'mizin hangi alanların güncellenmesine izin verdiğini açıkça görmek istiyoruz.

Bizim izin verdiğimiz alanlar:

```text
title
releaseYear
voteAverage
```

## Örnek 82

Bu yüzden onları açıkça kontrol ettik.

İleride validation ve Request doğrulama konularında bu yapıyı daha düzenli hâle getireceğiz.

---

## index.js Dosyamızın İlgili Tam Hâli

Dosya sürekliliğini koruyalım.

```js
import "dotenv/config";
import express from "express";
import mongoose from "mongoose";

import { initMongoConnection } from "./src/db/initMongoConnection.js";
import { Movie } from "./src/db/models/movie.js";

const app = express();

app.use(express.json());

app.get("/", (req, res) => {
  res.send("Merhaba Express!");
});

app.get("/movies", async (req, res) => {
  try {
    const movies = await Movie.find({});

    res.status(200).json({
      message: "Filmler getirildi.",
      data: movies,
    });
  } catch (error) {
    console.error(error);

    res.status(500).json({
      message: "Filmler getirilemedi.",
    });
  }
});

app.get("/movies/:movieId", async (req, res) => {
  try {
    const { movieId } = req.params;

    if (!mongoose.isObjectIdOrHexString(movieId)) {
      return res.status(400).json({
        message: "Geçersiz film kimliği.",
      });
    }

    const movie = await Movie.findById(movieId);

    if (movie === null) {
      return res.status(404).json({
        message: "Film bulunamadı.",
      });
    }

    res.status(200).json({
      message: "Film getirildi.",
      data: movie,
    });
  } catch (error) {
    console.error(error);

    res.status(500).json({
      message: "Film getirilemedi.",
    });
  }
});

app.post("/movies", async (req, res) => {
  try {
    const { title, releaseYear, voteAverage } = req.body;

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
  } catch (error) {
    console.error(error);

    res.status(500).json({
      message: "Film oluşturulamadı.",
    });
  }
});

app.patch("/movies/:movieId", async (req, res) => {
  try {
    const { movieId } = req.params;

    if (!mongoose.isObjectIdOrHexString(movieId)) {
      return res.status(400).json({
        message: "Geçersiz film kimliği.",
      });
    }

    const movie = await Movie.findById(movieId);

    if (movie === null) {
      return res.status(404).json({
        message: "Film bulunamadı.",
      });
    }

    const {
      title,
      releaseYear,
      voteAverage,
    } = req.body;

    const hasUpdate =
      title !== undefined ||
      releaseYear !== undefined ||
      voteAverage !== undefined;

    if (!hasUpdate) {
      return res.status(400).json({
        message: "Güncellenecek film bilgisi gönderilmedi.",
      });
    }

    if (title !== undefined) {
      movie.title = title;
    }

    if (releaseYear !== undefined) {
      movie.releaseYear = releaseYear;
    }

    if (voteAverage !== undefined) {
      movie.voteAverage = voteAverage;
    }

    await movie.save();

    res.status(200).json({
      message: "Film güncellendi.",
      data: movie,
    });
  } catch (error) {
    console.error(error);

    res.status(500).json({
      message: "Film güncellenemedi.",
    });
  }
});

const startServer = async () => {
  try {
    await initMongoConnection();

    app.listen(3000, () => {
      console.log("Server 3000 portunda çalışıyor.");
    });
  } catch (error) {
    console.error("Uygulama başlatılamadı.");
  }
};

startServer();
```

## Örnek 83

Artık API'miz dört gerçek davranışa sahip:

```text
GET /movies
↓
bütün filmleri getir

GET /movies/:movieId
↓
tek filmi getir

POST /movies
↓
yeni film oluştur

PATCH /movies/:movieId
↓
mevcut filmin belirli alanlarını güncelle
```

## Örnek 84

---

## 🧠 Akılda Kalsın

Bu bölümün merkezinde iki soru var:

```text
URL
↓
HANGİ film?

Body
↓
NEYİ değiştir?
```

## Örnek 85

Akışımız:

```text
PATCH /movies/:movieId
↓
req.params.movieId
↓
Movie.findById(movieId)
↓
Document'ı bul
↓
req.body
↓
yalnızca gönderilen alanları değiştir
↓
movie.save()
↓
MongoDB
↓
200 OK
```

## Örnek 86

---

## Bölüm Özeti

Bu bölüme Dune'ın puanını:

```text
8
↓
8.3
```

## Örnek 87

yapmak isteyerek başladık.

Yalnızca belirli alanları değiştirmek istediğimiz için:

```http
PATCH /movies/:movieId
```

## Örnek 88

Route'unu kullandık.

URL hangi filmi değiştireceğimizi, Request Body ise hangi alanların değişeceğini söyledi.

Filmi:

```js
const movie = await Movie.findById(movieId);
```

## Örnek 89

ile bulduk.

Yalnızca Body'de gerçekten gönderilen alanları değiştirdik.

Ardından:

```js
await movie.save();
```

## Örnek 90

ile değişikliği MongoDB'ye kaydettik.

Başarılı güncellemede:

```text
200 OK
```

## Örnek 91

ile güncel filmi Response olarak gönderdik.

---

## Sonraki Adım

Dune'ı oluşturabiliyoruz.

Bulabiliyoruz.

Güncelleyebiliyoruz.

Peki yanlışlıkla eklenen bir film kaydı artık sistemde bulunmamalıysa?

Bu kez document'ın bir alanını değiştirmek istemiyoruz.

Document'ın kendisini MongoDB'den kaldırmak istiyoruz.

Elimizde yine:

```text
movieId
```
