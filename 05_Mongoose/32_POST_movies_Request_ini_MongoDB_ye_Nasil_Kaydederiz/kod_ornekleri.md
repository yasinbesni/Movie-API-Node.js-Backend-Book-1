# Bölüm 32 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# POST /movies Request'ini MongoDB'ye Nasıl Kaydederiz?

## Response Geldi... Peki Film Gerçekten Kaydedildi mi?

Bir önceki bölümde Interstellar'ı şu kodla MongoDB'ye kaydetmiştik:

```js
const movie = new Movie({
  title: "Interstellar",
  releaseYear: 2014,
  voteAverage: 8.7,
});

await movie.save();
```

## Örnek 2

Ama o film bilgisini biz kodun içine yazmıştık.

Gerçek bir film platformunda kullanıcı bizim dosyalarımızı açıp:

```js
new Movie(...)
```

## Örnek 3

yazmayacak.

Frontend bize bir Request gönderecek.

Örneğin:

```http
POST /movies
```

## Örnek 4

ve film bilgileri Request Body içinde gelecek.

Asıl soru şimdi başlıyor:

> `req.body` içine gelen film
> nasıl MongoDB'deki gerçek bir document'a dönüşecek?

Bunu anlamak için önce küçük bir deneme yapalım.

---

## Önce Eski POST Route'umuzu Geri Getirelim

29. bölümün sonunda uygulamamızın başlangıç dosyası:

```text
index.js
```

## Örnek 5

idi.

Bu dosyada MongoDB bağlantımız ve:

```js
app.use(express.json());
```

## Örnek 6

zaten bulunuyor.

Express bölümünde daha önce buna benzer bir POST Route yazmıştık:

```js
app.post("/movies", (req, res) => {
  res.json({
    message: "Film bilgileri alındı.",
    data: req.body,
  });
});
```

## Örnek 7

Şimdi bu Route'u `index.js` içine yeniden ekleyelim.

Henüz Mongoose kullanmıyoruz.

Amacımız önce bir şeyi görmek.

---

## Postman'den Dune'ı Gönderelim

Sunucumuzu:

```bash
npm start
```

## Örnek 8

ile çalıştıralım.

Postman'de:

```text
POST
```

## Örnek 9

yöntemini seçelim.

Adres:

```text
http://localhost:3000/movies
```

## Örnek 10

olsun.

Body bölümünde:

```text
raw
↓
JSON
```

## Örnek 11

seçip şu veriyi gönderelim:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8
}
```

## Örnek 12

`Send` düğmesine bastığımızda Response olarak şuna benzer bir sonuç görürüz:

```json
{
  "message": "Film bilgileri alındı.",
  "data": {
    "title": "Dune",
    "releaseYear": 2021,
    "voteAverage": 8
  }
}
```

## Örnek 13

Her şey çalışmış gibi görünüyor.

Dune backend'e geldi.

`req.body` içinde okundu.

Response olarak geri gönderildi.

Peki Atlas'a bakarsak?

```text
Data Explorer
↓
movieApp
↓
movies
```

## Örnek 14

Dune orada mı?

**Hayır.**

İşte bölümün asıl problemi bu.

---

## Response Görmek Kaydedildiği Anlamına Gelmiyor

Route'umuza yeniden bakalım:

```js
app.post("/movies", (req, res) => {
  res.json({
    message: "Film bilgileri alındı.",
    data: req.body,
  });
});
```

## Örnek 15

Bu kod ne yapıyor?

```text
Request'i alıyor
↓
req.body'yi okuyor
↓
aynı veriyi Response olarak geri gönderiyor
```

## Örnek 16

Peki nerede:

```text
Movie Model
```

## Örnek 17

var?

Yok.

Nerede:

```text
new Movie(...)
```

## Örnek 18

var?

Yok.

Nerede:

```text
save()
```

## Örnek 19

var?

O da yok.

Demek ki:

```text
Response'ta Dune'ı görmek
≠
Dune MongoDB'ye kaydedildi
```

## Örnek 20

Şu anda veri yalnızca backend'imize uğrayıp geri dönüyor:

```text
Postman
↓
Express
↓
req.body
↓
Response
↓
Postman
```

## Örnek 21

MongoDB bu yolculuğa hiç katılmıyor.

İşte şimdi eksik parçayı ekleyeceğiz.

---

## Express ile Mongoose Arasında Bir Köprü Kurmalıyız

Elimizde iki ayrı bilgi var.

Express bize kullanıcının gönderdiği filmi:

```js
req.body
```

## Örnek 22

ile veriyor.

Mongoose ise bir film Document'ını:

```js
new Movie(...)
```

## Örnek 23

ile oluşturabiliyor.

O hâlde yapmamız gereken şey aslında çok doğal:

```text
req.body
↓
Movie Model
↓
Mongoose Document
↓
save()
↓
MongoDB
```

## Örnek 24

Yani Request'ten gelen veriyi Mongoose'a vereceğiz.

---

## Ama Önce Movie Model'ini Bu Dosyaya Getirelim

Şu anda çalıştığımız dosya:

```text
index.js
```

## Örnek 25

`Movie` Model'imiz ise:

```text
src/db/models/movie.js
```

## Örnek 26

dosyasında.

30. bölümde `Movie` Model'ini oluşturmuş ve bu dosyadan dışarı aktarmıştık. Temel `mongoose.model(...)` kullanımıyla korumalı `mongoose.models.Movie || ...` kullanımının farkını da aynı bölümde açıklamıştık.

Burada Model'i yeniden tanımlamayacağız. Başka dosyada kullanacağımız için yalnızca import etmeliyiz.

`index.js` dosyasının üst kısmına:

```js
import { Movie } from "./src/db/models/movie.js";
```

## Örnek 27

ekleyelim.

Artık bağlantı hazır:

```text
src/db/models/movie.js
↓
export Movie

index.js
↓
import Movie
```

## Örnek 28

Bu noktadan sonra `index.js` içinde:

```js
Movie
```

## Örnek 29

kullanabiliriz.

---

## Şimdi req.body'den Bir Document Oluşturalım

Postman'den gelen veri:

```js
req.body
```

## Örnek 30

içinde.

Şuna benzer:

```js
{
  title: "Dune",
  releaseYear: 2021,
  voteAverage: 8
}
```

## Örnek 31

31. bölümde Document'ı sabit değerlerle oluşturmuştuk:

```js
const movie = new Movie({
  title: "Interstellar",
  releaseYear: 2014,
  voteAverage: 8.7,
});
```

## Örnek 32

Bu kez değerleri Request'ten alacağız.

Önce `req.body` içinden ihtiyacımız olan alanları çıkaralım:

```js
const { title, releaseYear, voteAverage } = req.body;
```

## Örnek 33

Bu satır **destructuring** kullanır.

Şöyle okuyabiliriz:

```text
req.body içindeki
title
releaseYear
voteAverage
alanlarını al
↓
aynı isimlerde değişkenler oluştur
```

## Örnek 34

Yani artık elimizde doğrudan:

```js
title
releaseYear
voteAverage
```

## Örnek 35

değişkenleri var.

Şimdi bunları `Movie` Model'ine verebiliriz:

```js
const movie = new Movie({
  title,
  releaseYear,
  voteAverage,
});
```

## Örnek 36

Buradaki kısa yazım da JavaScript'in property shorthand özelliğidir.

Örneğin:

```js
{
  title,
}
```

## Örnek 37

şunun kısa hâlidir:

```js
{
  title: title,
}
```

## Örnek 38

Böylece kodumuz hem daha temiz olur hem de verinin yolculuğu açık kalır:

```text
req.body
↓
destructuring
↓
title, releaseYear, voteAverage
↓
Movie Model
↓
Mongoose Document
```

## Örnek 39

Dune artık yalnızca `req.body` içindeki sıradan bir veri değil.

Mongoose'un oluşturduğu bir Document.

Ama burada durursak yine MongoDB'ye gitmez.

31. bölümden nedenini biliyoruz.

---

## Eksik Olan Tek Satır

Document'ımız oluştu:

```js
const { title, releaseYear, voteAverage } = req.body;

const movie = new Movie({
  title,
  releaseYear,
  voteAverage,
});
```

## Örnek 40

Fakat:

```text
Document oluşturmak
≠
Document'ı MongoDB'ye kaydetmek
```

## Örnek 41

Bu yüzden:

```js
await movie.save();
```

## Örnek 42

satırına ihtiyacımız var.

İşte MongoDB'yi yolculuğa gerçekten dahil eden satır bu.

```text
req.body
↓
new Movie(...)
↓
Mongoose Document
↓
movie.save()
↓
MongoDB
```

## Örnek 43

Artık Dune yalnızca gelip geri dönmeyecek.

Veritabanında kalacak.

---

## Route Neden async Olmalı?

Bir sorun kaldı.

Şu satırda:

```js
await movie.save();
```

## Örnek 44

kullanıyoruz.

`await` kullanabilmek için Route fonksiyonumuzu:

```js
async
```

## Örnek 45

yapmalıyız.

Yani:

```js
app.post("/movies", async (req, res) => {
});
```

## Örnek 46

şeklinde yazacağız.

Bunu daha önceki bilgilerimizle şöyle okuyabiliriz:

```text
POST /movies Request'i geldiğinde
↓
asenkron işlemler yapabilecek
bir Route fonksiyonu çalıştır
```

## Örnek 47

Buradaki asenkron işlemimiz:

```text
MongoDB'ye veri yazmak
```

## Örnek 48

tır.

---

## Başarılı Olursa Ne Döndürelim?

Bu kez yalnızca:

```text
Film bilgileri alındı.
```

## Örnek 49

demek istemiyoruz.

Çünkü Request yalnızca alınmadı.

Bu Request sonucunda MongoDB'de **yeni bir film kaydı oluşturuldu**.

HTTP'de sunucu, işlemin sonucunu bir **durum kodu (Status Code)** ile de bildirir.

Daha önce sıkça gördüğümüz:

```text
200 OK
```

## Örnek 50

genel olarak Request'in başarıyla işlendiğini anlatır.

Ama bizim şu anki işlemimiz daha özel:

```text
Yeni bir kaynak oluşturduk.
```

## Örnek 51

API açısından burada oluşturulan kaynak:

```text
Dune film kaydı
```

## Örnek 52

dır.

Böyle bir durumda:

```text
201 Created
```

## Örnek 53

durum kodunu kullanabiliriz.

Buradaki iki parçayı ayrı okuyalım:

```text
201
↓
HTTP durum kodu

Created
↓
Oluşturuldu
```

## Örnek 54

Yani `201 Created` bize şunu söyler:

> Request başarıyla işlendi ve sonucunda yeni bir kaynak oluşturuldu.

Bizim örneğimizde:

```text
POST /movies
↓
Dune MongoDB'ye kaydedildi
↓
yeni bir film kaydı oluştu
↓
201 Created
```

## Örnek 55

Bu nedenle Response'umuzu:

```js
res.status(201).json({
  message: "Film oluşturuldu.",
  data: movie,
});
```

## Örnek 56

şeklinde gönderiyoruz.

Şimdi bu kodu da okuyabiliriz:

```text
res.status(201)
↓
Response'un HTTP durum kodunu
201 Created olarak ayarla

.json(...)
↓
Response Body'yi JSON olarak gönder
```

## Örnek 57

Böylece kullanıcı yalnızca mesajdan değil, HTTP durum kodundan da yeni kaydın başarıyla oluşturulduğunu anlayabilir.

---

## Route'u Tamamlayalım

Şimdi parçaları birleştirelim.

`index.js` içindeki `POST /movies` Route'umuzu şu hâle getirelim:

```js
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
```

## Örnek 58

Bir anda uzun görünebilir.

Ama ortadaki asıl hikâye yalnızca şu:

```text
req.body
↓
destructuring
↓
new Movie(...)
↓
movie.save()
↓
201 Created
```

## Örnek 59

`try / catch` ise kaydetme sırasında beklenmeyen bir hata oluşursa Request'in cevapsız kalmaması için burada bulunuyor.

Şimdilik bütün hata türlerini ayırmıyoruz.

Örneğin kullanıcının yanlış veri göndermesi ile sunucuda oluşan başka bir problemi ileride aynı şekilde ele almayacağız.

Validation ve hata yönetimi bölümlerinde bunu daha doğru biçimde ayıracağız.

Bu bölümde odağımız:

```text
başarılı POST Request'i
↓
MongoDB kaydı
```

## Örnek 60

akışıdır.

---

## index.js Dosyamızın İlgili Tam Hâli

Şimdi dosya sürekliliğini kaybetmeden tamamını görelim.

```js
import "dotenv/config";
import express from "express";

import { initMongoConnection } from "./src/db/initMongoConnection.js";
import { Movie } from "./src/db/models/movie.js";

const app = express();

app.use(express.json());

app.get("/", (req, res) => {
  res.send("Merhaba Express!");
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

## Örnek 61

Burada artık hiçbir parça havada değil.

```text
Movie nereden geldi?
↓
import edildi

req.body nereden geldi?
↓
express.json() hazırladı

title, releaseYear ve voteAverage nereden geldi?
↓
req.body destructuring ile ayrıldı

movie nereden geldi?
↓
new Movie(...) oluşturdu

MongoDB'ye ne gönderdi?
↓
movie.save()

Response'u kim gönderdi?
↓
res.status(201).json(...)
```

## Örnek 62

---

## Aynı Dune Request'ini Tekrar Gönderelim

Sunucuyu:

```bash
npm start
```

## Örnek 63

ile çalıştıralım.

Postman'de yine:

```http
POST http://localhost:3000/movies
```

## Örnek 64

Request'ini gönderelim.

Body:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8
}
```

## Örnek 65

olsun.

`Send` düğmesine basalım.

Ama bu kez önceki denemeden farklı bir şey olacak.

İlk denemede yolumuz:

```text
Postman
↓
req.body
↓
Response
```

## Örnek 66

idi.

Şimdi:

```text
Postman
↓
req.body
↓
Movie
↓
Document
↓
save()
↓
MongoDB
↓
Response
```

## Örnek 67

olacak.

---

## Postman'de Ne Görmeliyiz?

İşlem başarılıysa Status Code:

```text
201 Created
```

## Örnek 68

olmalı.

Response Body ise şuna benzer olabilir:

```json
{
  "message": "Film oluşturuldu.",
  "data": {
    "title": "Dune",
    "releaseYear": 2021,
    "voteAverage": 8,
    "_id": "..."
  }
}
```

## Örnek 69

Mongoose ayarlarına göre Response içinde `__v` gibi ek bir alan da görebilirsiniz.

Bu bölümde onun ayrıntısına girmemize gerek yok.

Şu anda önemli olan iki şey:

```text
201 Created

ve

kaydedilen Dune verisi
```

## Örnek 70

---

## Şimdi Atlas'a Bakalım

Response'u gördük.

Ama artık biliyoruz:

```text
Response görmek
```

## Örnek 71

tek başına verinin MongoDB'ye kaydedildiğini kanıtlamaz.

Bölümün başında bunu özellikle yaşadık.

Bu yüzden Atlas'ın:

```text
Data Explorer
```

## Örnek 72

bölümüne geçelim.

Ardından:

```text
movieApp
↓
movies
```

## Örnek 73

collection'ını açalım.

Bu kez Dune orada olmalı.

31. bölümü uyguladıysanız collection'da artık kabaca:

```text
movies
│
├── Interstellar
└── Dune
```

## Örnek 74

görürüz.

Ve aradaki fark çok net:

```text
İlk Dune denemesi
↓
Response geldi
↓
MongoDB kaydı yok

İkinci Dune denemesi
↓
Movie oluşturuldu
↓
save() çalıştı
↓
MongoDB kaydı var
```

## Örnek 75

İşte bölümün bütün amacı buydu.

---

## Send'e Bastığımız Anda Gerçekte Ne Oldu?

Şimdi artık bütün yolculuğu okuyabiliriz.

Postman:

```text
POST /movies
```

## Örnek 76

Request'ini gönderdi.

Request Body:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8
}
```

## Örnek 77

Express'e ulaştı.

```js
app.use(express.json());
```

## Örnek 78

Body'yi işledi.

Film verisi:

```js
req.body
```

## Örnek 79

içine geldi.

Route:

```js
new Movie(...)
```

## Örnek 80

ile bu veriden bir Mongoose Document oluşturdu.

Sonra:

```js
await movie.save();
```

## Örnek 81

Document'ın verisini MongoDB'ye kaydetti.

Son olarak:

```js
res.status(201).json(...)
```

## Örnek 82

Response'u Postman'e gönderdi.

Tek çizgide:

```text
Postman
↓
POST /movies
↓
JSON Body
↓
express.json()
↓
req.body
↓
Movie Model
↓
Mongoose Document
↓
save()
↓
MongoDB
↓
201 Created
↓
Postman
```

## Örnek 83

Artık bu diyagram ezberlenecek bir şema değil.

Her adımını gerçekten çalıştırdık.

---

## Burada Büyük Bir Şey Değişti

Biraz önce Dune'ı eklemek için backend dosyasını değiştirmiyorduk.

Dune'ın adı artık kodun içinde değildi.

```text
"Dune"
```

## Örnek 84

Postman'den geldi.

Yarın:

```text
Arrival
```

## Örnek 85

gönderebiliriz.

Sonra:

```text
The Matrix
```

## Örnek 86

gönderebiliriz.

Route kodunu değiştirmemize gerek yok.

Çünkü Route artık belirli bir filme göre yazılmıyor.

Şuna göre çalışıyor:

```text
Request'ten hangi film gelirse
↓
onu Document'a dönüştür
↓
MongoDB'ye kaydet
```

## Örnek 87

İşte API yazmanın güçlü taraflarından biri burada görünmeye başlıyor.

Kodumuz:

```text
tek bir filmi
```

## Örnek 88

değil,

```text
film oluşturma davranışını
```

## Örnek 89

tanımlıyor.

---

## 🧠 Akılda Kalsın

Bu bölümün en önemli ayrımı:

```text
req.body'yi Response olarak göndermek
≠
req.body'deki veriyi MongoDB'ye kaydetmek
```

## Örnek 90

MongoDB kaydı için Mongoose'un gerçekten devreye girmesi gerekir.

Bizim akışımız:

```text
req.body
↓
new Movie(...)
↓
Mongoose Document
↓
movie.save()
↓
MongoDB
```

## Örnek 91

oldu.

Ve yalnızca kayıt başarılı olduktan sonra:

```text
201 Created
```

## Örnek 92

Response'u gönderdik.

---

## Bölüm Özeti

Bu bölüme küçük bir yanılgıyla başladık.

Postman'den Dune'ı gönderdik.

Backend veriyi aldı.

Response içinde Dune'ı gördük.

Ama Atlas'a baktığımızda:

```text
Dune yoktu.
```

## Örnek 93

Çünkü ilk Route yalnızca:

```text
req.body
↓
Response
```

## Örnek 94

akışını kuruyordu.

MongoDB'ye hiçbir şey yazmıyordu.

Sonra `Movie` Model'ini `index.js` içine:

```js
import { Movie } from "./src/db/models/movie.js";
```

## Örnek 95

ile getirdik.

Request'ten gelen veriyi önce:

```js
const { title, releaseYear, voteAverage } = req.body;
```

## Örnek 96

ile `req.body` içinden ayırdık.

Ardından:

```js
const movie = new Movie({
  title,
  releaseYear,
  voteAverage,
});
```

## Örnek 97

ile bir Mongoose Document oluşturduk.

Ardından:

```js
await movie.save();
```

## Örnek 98

ile Document'ın verisini MongoDB'ye kaydettik.

Başarılı işlem sonunda:

```js
res.status(201).json({
  message: "Film oluşturuldu.",
  data: movie,
});
```

## Örnek 99

ile Response gönderdik.

Sonunda Atlas'ta Dune'ı gördük.

Yani artık gerçek veri akışımız:

```text
POST /movies
↓
req.body
↓
Movie
↓
Document
↓
save()
↓
MongoDB
↓
201 Created
```

## Örnek 100

hâline geldi.

---

## Sonraki Adım

Artık MongoDB'ye film ekleyebiliyoruz.

Üstelik film adını kodun içine yazmamıza gerek yok.

Request'ten gelen her uygun film:

```text
POST /movies
```

## Örnek 101

üzerinden kaydedilebilir.

Ama film platformunun yalnızca veri eklemesi yetmez.

Kullanıcı siteyi açtığında kayıtlı filmleri de görmek isteyecek.

Şu anda MongoDB'de:

```text
Interstellar

Dune
```

## Örnek 102

var.

Peki bu kez veriyi ters yönde nasıl taşıyacağız?

```text
MongoDB
↓
filmleri bul
↓
Express
↓
Response
↓
Postman
```
