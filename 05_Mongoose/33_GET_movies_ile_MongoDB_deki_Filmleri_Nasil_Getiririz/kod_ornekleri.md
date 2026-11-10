# Bölüm 33 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# GET /movies ile MongoDB'deki Filmleri Nasıl Getiririz?

## Bu Route Bir Süredir Rol Yapıyordu

Express'i ilk öğrenirken şöyle bir Route yazmıştık:

```js
app.get("/movies", (req, res) => {
  res.send("Filmler görüntüleniyor.");
});
```

## Örnek 2

O gün için görevini yapıyordu.

`GET /movies` Request'i geliyordu ve Express bir Response gönderiyordu.

Ama kabul edelim:

```text
Filmler görüntüleniyor.
```

## Örnek 3

derken ortada gerçekten görüntülenen hiçbir film yoktu.

Henüz MongoDB yoktu.

`movies` collection'ı yoktu.

Interstellar yoktu.

Dune yoktu.

Route biraz rol yapıyordu.

Şimdi durum değişti.

Bir önceki bölümlerde MongoDB'ye gerçekten iki film kaydettik:

```text
movies
│
├── Interstellar
└── Dune
```

## Örnek 4

Artık şu cümlenin hakkını verebiliriz:

```text
Filmler görüntüleniyor.
```

## Örnek 5

Bu kez gerçekten filmleri getireceğiz.

---

## Kullanıcı Atlas'ı Açmak Zorunda mı?

Biz geliştirici olarak Atlas'a girip:

```text
Data Explorer
↓
movieApp
↓
movies
```

## Örnek 6

yolundan kayıtları görebiliyoruz.

Ama film platformunu kullanan biri neden Atlas'a girsin?

Kullanıcı yalnızca uygulamayı açar.

Frontend de backend'e şöyle der:

```http
GET /movies
```

## Örnek 7

Yani:

> Bana filmleri gönder.

Backend'in yapması gereken şey artık ters yönde bir yolculuk kurmak:

```text
MongoDB
↓
filmleri bul
↓
Express
↓
Response
↓
kullanıcı
```

## Örnek 8

32. bölümde veri:

```text
kullanıcıdan MongoDB'ye
```

## Örnek 9

gidiyordu.

Bu bölümde:

```text
MongoDB'den kullanıcıya
```

## Örnek 10

gelecek.

---

## Hangi Dosyada Çalışıyoruz?

Yeni bir dosya açmıyoruz.

Yine:

```text
index.js
```

## Örnek 11

dosyasındayız.

32. bölümde `Movie` Model'ini zaten bu dosyaya import etmiştik:

```js
import { Movie } from "./src/db/models/movie.js";
```

## Örnek 12

Bu yüzden tekrar yeni bir import eklememize gerek yok.

Elimizde zaten şu bağlantı var:

```text
index.js
↓
Movie Model
↓
movies collection
```

## Örnek 13

Şimdi tek ihtiyacımız:

```text
Movie Model'e
"filmleri getir"
demek.
```

## Örnek 14

Peki bunu Mongoose'ta nasıl söyleyeceğiz?

---

## Önce GET /movies Route'unu Oluşturalım

`index.js` içine şu Route'u ekleyelim:

```js
app.get("/movies", async (req, res) => {
});
```

## Örnek 15

Burada `async` kullanmamızın nedeni birazdan MongoDB'den veri bekleyecek olmamız.

Ama Route şu anda boş.

Express `GET /movies` Request'ini karşılayabilir.

Fakat MongoDB'ye henüz hiçbir soru sormuyor.

Şimdi eksik olan cümleyi Mongoose'a söylemeliyiz:

> `movies` collection'ındaki filmleri bul.

---

## Movie Model'e "Filmleri Bul" Nasıl Deriz?

Mongoose Model'lerinde document aramak için:

```js
find()
```

## Örnek 16

metodu bulunur.

Bizim Model'imiz:

```js
Movie
```

## Örnek 17

olduğuna göre şu kodu yazabiliriz:

```js
Movie.find({})
```

## Örnek 18

Buradaki:

```text
Movie
```

## Örnek 19

hangi collection ile çalışacağımızı biliyor.

Bizim örneğimizde:

```text
Movie Model
↓
movies collection
```

## Örnek 20

ilişkisi vardı.

`find` ise:

```text
bul
```

## Örnek 21

anlamına geliyor.

Peki parantezin içindeki:

```js
{}
```

## Örnek 22

ne?

Bu boş nesne:

```text
özel bir filtre vermiyorum
```

## Örnek 23

demektir.

Yani:

```js
Movie.find({})
```

## Örnek 24

satırını şimdilik şöyle okuyabiliriz:

> `movies` collection'ındaki bütün document'ları bul.

Tam da istediğimiz şey.

---

## Peki Sonuç Nereye Gelecek?

MongoDB'ye:

```js
Movie.find({})
```

## Örnek 25

dedik.

Ama filmleri bir yerde tutmamız gerekiyor.

Şöyle yazalım:

```js
const movies = await Movie.find({});
```

## Örnek 26

Bu satır bölümün merkezidir.

Birlikte okuyalım:

```text
Movie
↓
movies collection ile çalış

find({})
↓
filtre vermeden document'ları ara

await
↓
sorgunun sonucunu bekle

movies
↓
bulunan filmleri burada tut
```

## Örnek 27

Eğer collection'ımızda:

```text
Interstellar
Dune
```

## Örnek 28

varsa `movies` değişkeni kabaca şöyle düşünülebilir:

```js
[
  {
    title: "Interstellar",
    releaseYear: 2014,
    voteAverage: 8.7,
  },
  {
    title: "Dune",
    releaseYear: 2021,
    voteAverage: 8,
  },
]
```

## Örnek 29

Dikkatinizi çeken bir şey oldu mu?

Sonuç tek bir nesne değil.

Bir:

```text
dizi
```

## Örnek 30

---

## Neden Sonuç Bir Dizi?

Çünkü:

```js
find({})
```

## Örnek 31

tek bir film aramıyor.

Koşula uyan:

```text
bütün document'ları
```

## Örnek 32

arıyor.

Bizim collection'ımızda iki film olduğu için sonuç iki elemanlı bir dizi olabilir:

```text
[
  Interstellar,
  Dune
]
```

## Örnek 33

Yarın 50 film olursa dizi 50 eleman içerebilir.

Hiç film yoksa?

Sonuç:

```js
[]
```

## Örnek 34

olur.

Yani `find()` ile çoklu sonuç beklediğimiz için:

```text
movies
↓
dizi
```

## Örnek 35

olarak düşünmek doğru olur.

Bu ayrım, 34. bölümde tek bir filmi `_id` üzerinden aramaya başladığımızda daha da anlamlı hâle gelecek.

---

## Küçük Ama Önemli Bir Ayrıntı: find() Hemen Diziyi mi Döndürüyor?

Burada teknik olarak küçük bir ayrıntı var.

Şu çağrı:

```js
Movie.find({})
```

## Örnek 36

Mongoose tarafında bir:

```text
Query
```

## Örnek 37

yani sorgu nesnesi oluşturur.

Bu kelime sizi durdurmasın.

Şimdilik şöyle düşünmeniz yeterli:

```text
Movie.find({})
↓
MongoDB'ye sorulacak sorgu hazırlanır

await
↓
sorgunun çalışıp sonucunun gelmesi beklenir

movies
↓
bulunan Document'lar
```

## Örnek 38

Bu nedenle:

```js
const movies = await Movie.find({});
```

## Örnek 39

yazıyoruz.

`await` tamamlandıktan sonra `movies`, bulunan Mongoose Document'larından oluşan bir dizi olur.

Query konusunu ileride daha ayrıntılı inceleyeceğiz.

Bu bölümde asıl ihtiyacımız:

```text
MongoDB'deki filmleri almak.
```

## Örnek 40

---

## Filmleri Bulduk. Ama Kullanıcı Henüz Görmedi.

Şu satır çalıştı:

```js
const movies = await Movie.find({});
```

## Örnek 41

Artık filmler backend'imizin içinde.

Ama Postman henüz onları göremez.

Çünkü Request'in bir de Response tarafı var.

32. bölümde yeni bir kayıt oluşturduğumuz için:

```text
201 Created
```

## Örnek 42

kullanmıştık.

Bu kez yeni bir kaynak oluşturmuyoruz.

Var olan filmleri başarıyla getiriyoruz.

Bu durumda:

```text
200 OK
```

## Örnek 43

durum kodunu kullanabiliriz.

Buradaki anlam:

```text
200
↓
HTTP durum kodu

OK
↓
Request başarıyla işlendi
```

## Örnek 44

Yani:

```text
GET /movies
↓
filmler başarıyla bulundu
↓
200 OK
```

## Örnek 45

---

## Filmleri Response Olarak Gönderelim

Artık elimizde:

```js
movies
```

## Örnek 46

değişkeni var.

Bunu Response Body içinde kullanıcıya gönderebiliriz:

```js
res.status(200).json({
  message: "Filmler getirildi.",
  data: movies,
});
```

## Örnek 47

Böylece yol tamamlanır:

```text
MongoDB
↓
Movie.find({})
↓
movies
↓
200 OK
↓
JSON Response
↓
Postman
```

## Örnek 48

32. bölümde:

```text
Request Body → MongoDB
```

## Örnek 49

köprüsünü kurmuştuk.

Şimdi:

```text
MongoDB → Response Body
```

## Örnek 50

köprüsünü kuruyoruz.

---

## GET /movies Route'unun Tam Hâli

Şimdi parçaları birleştirelim:

```js
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
```

## Örnek 51

Bu Route'u bir hikâye gibi okuyabiliriz:

```text
GET /movies geldi
↓
MongoDB'deki filmleri bul
↓
sonucu movies içine al
↓
200 OK ile JSON olarak gönder
```

## Örnek 52

Asıl işin kalbi yalnızca şu satır:

```js
const movies = await Movie.find({});
```

## Örnek 53

Geri kalan kod Request'i karşılıyor, başarılı sonucu gönderiyor ve beklenmeyen bir hata olursa Response'un cevapsız kalmasını engelliyor.

---

## index.js Şimdi Nasıl Görünüyor?

Dosya sürekliliğini koruyalım.

32. bölümdeki `POST /movies` Route'umuzu silmiyoruz.

Yanına `GET /movies` Route'unu ekliyoruz.

`index.js` dosyamızın ilgili hâli:

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

## Örnek 54

Şimdi aynı yol üzerinde iki farklı davranışımız var:

```text
GET /movies
↓
filmleri getir

POST /movies
↓
yeni film oluştur
```

## Örnek 55

Yol aynı:

```text
/movies
```

## Örnek 56

Ama HTTP yöntemi farklı olduğu için Express farklı Route'u çalıştırıyor.

Daha önce öğrendiğimiz GET ve POST farkı artık gerçek MongoDB işlemleri yapıyor.

---

## Şimdi Gerçekten Deneyelim

Sunucuyu çalıştıralım:

```bash
npm start
```

## Örnek 57

Bağlantı başarılıysa terminalde:

```text
MongoDB bağlantısı başarılı.
Server 3000 portunda çalışıyor.
```

## Örnek 58

mesajlarını görmeliyiz.

Şimdi Postman'e geçelim.

HTTP yöntemini:

```text
GET
```

## Örnek 59

seçelim.

Adres:

```text
http://localhost:3000/movies
```

## Örnek 60

olsun.

Bu Request için Body göndermemize gerek yok.

Çünkü backend'e yeni bir film vermiyoruz.

Backend'den var olan filmleri istiyoruz.

`Send` düğmesine basalım.

---

## Bu Kez Response'ta Gerçek Filmler Var

İşlem başarılıysa:

```text
200 OK
```

## Örnek 61

görmeliyiz.

Response Body ise şuna benzer olabilir:

```json
{
  "message": "Filmler getirildi.",
  "data": [
    {
      "_id": "...",
      "title": "Interstellar",
      "releaseYear": 2014,
      "voteAverage": 8.7
    },
    {
      "_id": "...",
      "title": "Dune",
      "releaseYear": 2021,
      "voteAverage": 8
    }
  ]
}
```

## Örnek 62

Mongoose'un eklediği bazı alanları da Response içinde görebilirsiniz.

Ayrıca filmlerin sırası sizde farklı olabilir.

Çünkü henüz MongoDB'ye:

```text
önce şu filmi,
sonra bunu getir
```

## Örnek 63

gibi bir sıralama kuralı vermedik.

Şimdilik önemli olan:

```text
Interstellar
ve
Dune
```

## Örnek 64

verilerinin gerçekten MongoDB'den gelip Response'a ulaşması.

---

## Bir Zamanlar Sadece "Filmler Görüntüleniyor" Diyorduk

Bölümün başındaki eski Route'u hatırlayın:

```js
app.get("/movies", (req, res) => {
  res.send("Filmler görüntüleniyor.");
});
```

## Örnek 65

O Route yalnızca bir cümle gönderiyordu.

Şimdiki Route ise:

```js
app.get("/movies", async (req, res) => {
  const movies = await Movie.find({});

  res.status(200).json({
    message: "Filmler getirildi.",
    data: movies,
  });
});
```

## Örnek 66

gerçekten veritabanına gidiyor.

Aradaki fark küçük görünse de backend açısından büyük:

```text
ESKİ
GET /movies
↓
hazır bir metin
↓
Response

ŞİMDİ
GET /movies
↓
MongoDB sorgusu
↓
gerçek film verileri
↓
Response
```

## Örnek 67

Artık API'miz yalnızca ne yapacağını söylemiyor.

Gerçekten yapıyor.

---

## POST ve GET Artık Birbirini Tamamlıyor

Şimdi film platformumuzun iki temel davranışı var.

Bir film eklemek istersek:

```http
POST /movies
```

## Örnek 68

Akış:

```text
Request Body
↓
Movie Document
↓
save()
↓
MongoDB
```

## Örnek 69

Filmleri görmek istersek:

```http
GET /movies
```

## Örnek 70

Akış:

```text
MongoDB
↓
Movie.find({})
↓
movies
↓
Response
```

## Örnek 71

Biri veriyi içeri taşıyor.

Diğeri dışarı çıkarıyor.

```text
POST
→ MongoDB'ye doğru

GET
← MongoDB'den dışarı
```

## Örnek 72

Backend'in veri akışı artık iki yönlü çalışıyor.

---

## Peki Collection Boş Olsaydı?

Şu anda iki filmimiz olduğu için Response'ta iki kayıt görüyoruz.

Ama `movies` collection'ı boş olsaydı:

```js
const movies = await Movie.find({});
```

## Örnek 73

yine çalışırdı.

Bu kez `movies`:

```js
[]
```

## Örnek 74

olurdu.

Response da örneğin:

```json
{
  "message": "Filmler getirildi.",
  "data": []
}
```

## Örnek 75

şeklinde dönebilirdi.

Bu önemli bir ayrım.

```text
Hiç film bulunmaması
```

## Örnek 76

ile:

```text
sunucuda hata oluşması
```

## Örnek 77

aynı şey değildir.

Collection boşsa sorgu başarılı olabilir; yalnızca sonuç dizisi boştur.

Hata oluşursa ise `catch` bloğuna geçeriz.

Bu ayrım ileride API Response'larımızı tasarlarken sık sık karşımıza çıkacak.

---

## 🧠 Akılda Kalsın

Bu bölümün merkezindeki satır:

```js
const movies = await Movie.find({});
```

## Örnek 78

Şöyle okunabilir:

```text
Movie Model
↓
movies collection

find({})
↓
bütün filmleri ara

await
↓
sonucu bekle

movies
↓
bulunan film Document'larından oluşan dizi
```

## Örnek 79

Sonra:

```js
res.status(200).json({
  message: "Filmler getirildi.",
  data: movies,
});
```

## Örnek 80

ile bu diziyi kullanıcıya gönderiyoruz.

Kısacası:

```text
GET /movies
↓
Movie.find({})
↓
MongoDB
↓
movies[]
↓
200 OK
↓
JSON Response
```

## Örnek 81

---

## Bölüm Özeti

Bu bölümde yıllar önce değil, yalnızca birkaç bölüm önce yazdığımız şu Route'un hakkını sonunda verdik:

```text
GET /movies
```

## Örnek 82

Artık bu Route yalnızca:

```text
Filmler görüntüleniyor.
```

## Örnek 83

mesajı göndermiyor.

Gerçekten MongoDB'deki filmleri getiriyor.

Bunun için:

```js
const movies = await Movie.find({});
```

## Örnek 84

kullandık.

`Movie` Model'inin `movies` collection ile çalıştığını, `find({})` ile bütün document'ları aradığımızı gördük.

Sonucun bir dizi olduğunu öğrendik:

```text
Movie.find({})
↓
[
  Interstellar,
  Dune
]
```

## Örnek 85

Başarılı GET Request'i için:

```text
200 OK
```

## Örnek 86

durum kodunu kullandık.

Ve filmleri:

```js
res.status(200).json({
  message: "Filmler getirildi.",
  data: movies,
});
```

## Örnek 87

ile JSON Response olarak gönderdik.

Artık backend'imiz:

```text
POST /movies
↓
film ekleyebiliyor

GET /movies
↓
filmleri getirebiliyor
```

## Örnek 88

---

## Sonraki Adım

Şimdi kullanıcı bütün filmleri görebiliyor.

Ama film platformunda bir kullanıcı Dune'ın detay sayfasına tıklarsa ne olacak?

Bize:

```text
bütün filmler
```

## Örnek 89

gerekmiyor.

Yalnızca:

```text
tek bir film
```

## Örnek 90

gerekiyor.

Üstelik hangi filmi istediğimizi de bir şekilde Route'a söylemeliyiz.

Örneğin:

```http
GET /movies/:movieId
```
