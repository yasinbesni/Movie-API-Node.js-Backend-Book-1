# Bölüm 34 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# GET /movies/:movieId ile Tek Bir Filmi Nasıl Getiririz?

## Dune'a Tıkladık. Neden Bütün Filmler Tekrar Geliyor?

Bir önceki bölümde sonunda gerçek film listemizi aldık.

Postman'den:

```http
GET /movies
```

## Örnek 2

Request'ini gönderdiğimizde MongoDB'deki filmler Response içinde geliyordu:

```text
Interstellar
Dune
```

## Örnek 3

Bu, film listesini göstermek için tam istediğimiz şey.

Ama şimdi kullanıcı listede:

```text
Dune
```

## Örnek 4

filmine tıklasın.

Detay sayfasında yalnızca Dune'ı göstermek istiyoruz.

Başlığını.

Yayın yılını.

Puanını.

İleride ekleyeceğimiz diğer film bilgilerini.

Peki bunun için backend'e yine:

```http
GET /movies
```

## Örnek 5

mi diyeceğiz?

Bu durumda bütün filmleri tekrar getirip içlerinden Dune'ı seçmemiz gerekir.

İki film varken çok büyük bir sorun gibi görünmeyebilir.

Ama collection'da:

```text
10 film
100 film
10.000 film
```

## Örnek 6

olduğunu düşünün.

Kullanıcı tek bir filmi istiyor.

Biz neden bütün collection'ı getirelim?

Backend'e artık daha açık bir şey söylememiz gerekiyor:

> Bana filmleri değil,
> **şu filmi** getir.

Peki "şu film" hangisi?

İşte `_id` tam burada yeniden karşımıza çıkıyor.

---

## Dune'ın Zaten Bir Kimliği Var

33. bölümde filmleri getirirken Response'ta her film için `_id` alanını görmüştük.

Dune kabaca şöyle görünüyordu:

```json
{
  "_id": "...",
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8
}
```

## Örnek 7

Buradaki `_id` sizde farklı bir değer olacaktır.

Bu normal.

25 ve 26. bölümlerde `_id` ve ObjectId'nin neden var olduğunu öğrenmiştik.

O zaman soru şuydu:

```text
MongoDB bir document'ı
diğerinden nasıl ayırıyor?
```

## Örnek 8

Şimdi o bilgi ilk kez Route tasarımımızın parçası olacak.

Dune'ın `_id` değerini backend'e gönderirsek backend hangi filmi istediğimizi anlayabilir.

Örneğin gerçek bir `_id` değerimizin şöyle olduğunu düşünelim:

```text
68ac1234abcd5678ef901234
```

## Örnek 9

O zaman Request şöyle olabilir:

```http
GET /movies/68ac1234abcd5678ef901234
```

## Örnek 10

Artık URL bize iki şey söylüyor:

```text
/movies
↓
film kaynağıyla çalışıyoruz

68ac1234abcd5678ef901234
↓
hangi filmi istediğimizi söylüyor
```

## Örnek 11

Bu kez Request:

> Bana bütün filmleri getir.

demiyor.

Şunu diyor:

> Bana bu kimliğe sahip filmi getir.

---

## Her Film İçin Ayrı Route mu Yazacağız?

Dune'ın `_id` değeri için şöyle bir Route yazdığımızı düşünün:

```js
app.get("/movies/68ac1234abcd5678ef901234", (req, res) => {
});
```

## Örnek 12

Peki Interstellar için?

Başka bir Route mu?

```js
app.get("/movies/baska-bir-id", (req, res) => {
});
```

## Örnek 13

Yeni bir film eklediğimizde bir Route daha mı yazacağız?

Elbette hayır.

Bizim ihtiyacımız:

```text
/movies/
```

## Örnek 14

kısmı sabit kalsın,

sonundaki film kimliği ise Request'e göre değişsin.

Yani şöyle bir Route'a ihtiyacımız var:

```http
GET /movies/:movieId
```

## Örnek 15

Buradaki:

```text
:movieId
```

## Örnek 16

gerçek film kimliği değildir.

Değişebilen kısmın adıdır.

Express'te buna **Route Parameter** diyebiliriz.

Burada çok kolay karışabilecek bir nokta var.

```text
/movies/:movieId
```

## Örnek 17

bir **Route şablonudur**.

Postman veya frontend gerçek Request'i gönderirken URL'ye:

```text
:movieId
```

## Örnek 18

yazmaz.

Onun yerine gerçek değeri gönderir.

Örneğin:

```text
Route tanımı
↓
/movies/:movieId

Gerçek Request
↓
/movies/68ac1234abcd5678ef901234

Express'in yakaladığı değer
↓
req.params.movieId
↓
"68ac1234abcd5678ef901234"
```

## Örnek 19

Yani iki noktayı (`:`) yalnızca Express'e:

> Buradaki bölüm sabit değil, değişebilir.

demek için Route tanımında kullanıyoruz.

---

## `:movieId` Ne Anlama Geliyor?

Route'umuzu şöyle yazalım:

```js
app.get("/movies/:movieId", (req, res) => {
});
```

## Örnek 20

Buradaki iki nokta:

```text
:
```

## Örnek 21

önemlidir.

Şöyle okuyabiliriz:

```text
/movies/
↓
sabit bölüm

:movieId
↓
bu konumda gelecek değeri
movieId adıyla yakala
```

## Örnek 22

Örneğin Request:

```http
GET /movies/68ac1234abcd5678ef901234
```

## Örnek 23

olursa Express:

```text
68ac1234abcd5678ef901234
```

## Örnek 24

değerini yakalar.

Peki nereye koyar?

```js
req.params
```

## Örnek 25

içine.

Yani Route çalıştığında:

```js
req.params
```

## Örnek 26

kabaca şöyle olabilir:

```js
{
  movieId: "68ac1234abcd5678ef901234"
}
```

## Örnek 27

Artık URL'nin içindeki film kimliği backend'imizin elinde.

---

## Önce Gerçekten Görelim

Hemen MongoDB sorgusuna geçmeyelim.

Önce Express'in değeri gerçekten yakaladığını görelim.

`index.js` içine geçici olarak şu Route'u ekleyelim:

```js
app.get("/movies/:movieId", (req, res) => {
  console.log(req.params);

  res.json({
    params: req.params,
  });
});
```

## Örnek 28

Şimdi Dune'ın gerçek `_id` değerini 33. bölümdeki Response'tan veya Atlas Data Explorer'dan kopyalayın.

Örneğin sizin Dune `_id` değeriniz:

```text
BURAYA_KENDI_DUNE_ID_DEGERINIZ
```

## Örnek 29

olsun.

Postman'de şu Request'i gönderin:

```text
GET http://localhost:3000/movies/BURAYA_KENDI_DUNE_ID_DEGERINIZ
```

## Örnek 30

`Send` düğmesine bastığınızda Response kabaca şöyle olur:

```json
{
  "params": {
    "movieId": "BURAYA_KENDI_DUNE_ID_DEGERINIZ"
  }
}
```

## Örnek 31

Demek ki URL'nin sonundaki değer kaybolmadı.

Express onu yakaladı ve:

```js
req.params.movieId
```

## Örnek 32

üzerinden erişilebilir hâle getirdi.

Akış:

```text
GET /movies/GERCEK_ID
↓
:movieId
↓
Express değeri yakalar
↓
req.params.movieId
```

## Örnek 33

Şimdi bu kimliği Mongoose'a verebiliriz.

---

## req.params İçinden movieId'yi Alalım

32. bölümde `req.body` içinden alanları destructuring ile almıştık:

```js
const { title, releaseYear, voteAverage } = req.body;
```

## Örnek 34

Aynı JavaScript bilgisini burada da kullanabiliriz.

Bu kez veri:

```js
req.body
```

## Örnek 35

içinde değil,

```js
req.params
```

## Örnek 36

içinde.

Şöyle yazalım:

```js
const { movieId } = req.params;
```

## Örnek 37

Bunu okuyalım:

```text
req.params içindeki
movieId değerini al
↓
movieId değişkenine koy
```

## Örnek 38

Artık elimizde doğrudan:

```js
movieId
```

## Örnek 39

var.

Bu değer, kullanıcının URL içinde gönderdiği film kimliği.

Ama burada bir soru daha var:

> URL'nin sonunda gelen her şey gerçekten bir ObjectId olabilir mi?

Hayır.

Örneğin:

```http
GET /movies/abc
```

## Örnek 40

Request'inde:

```text
abc
```

## Örnek 41

değeri bir Route Parameter olarak yakalanabilir.

Express açısından bunda sorun yoktur.

Express yalnızca URL'deki parçayı alır:

```js
req.params.movieId
```

## Örnek 42

içine koyar.

Ama bizim MongoDB `_id` alanımız varsayılan olarak ObjectId kullandığı için Mongoose'a göndereceğimiz değerin uygun bir ObjectId gösterimi olup olmadığını kontrol etmek daha doğru olur.

Bunun için Mongoose'un:

```js
mongoose.isObjectIdOrHexString(movieId)
```

## Örnek 43

yardımcı metodunu kullanabiliriz.

Bu kontrolün çalışabilmesi için `index.js` dosyamızda Mongoose'u da import edeceğiz:

```js
import mongoose from "mongoose";
```

## Örnek 44

Ardından:

```js
if (!mongoose.isObjectIdOrHexString(movieId)) {
  return res.status(400).json({
    message: "Geçersiz film kimliği.",
  });
}
```

## Örnek 45

yazabiliriz.

Buradaki:

```text
400 Bad Request
```

## Örnek 46

şunu anlatır:

> Request sunucuya ulaştı,
> ancak gönderilen değer beklenen biçime uygun değil.

Yani üç farklı durumu birbirine karıştırmayacağız:

```text
movieId biçimi geçersiz
↓
400 Bad Request

movieId biçimi geçerli ama film yok
↓
404 Not Found

film bulundu
↓
200 OK
```

## Örnek 47

Şimdi Mongoose'a güvenli biçimde:

> Bu `_id` değerine sahip filmi bul.

diyebiliriz.

---

## Movie Model'e "Bu _id'yi Bul" Nasıl Deriz?

33. bölümde bütün filmleri almak için:

```js
Movie.find({})
```

## Örnek 48

kullanmıştık.

Ama şimdi bütün filmleri istemiyoruz.

Tek bir document istiyoruz.

Üstelik hangi document olduğunu biliyoruz:

```text
_id = movieId
```

## Örnek 49

Mongoose bunun için çok anlaşılır bir metoda sahip:

```js
findById()
```

## Örnek 50

Adını parçalayalım:

```text
find
↓
bul

by
↓
ile / göre

Id
↓
_id değeri
```

## Örnek 51

Yani:

```js
Movie.findById(movieId)
```

## Örnek 52

satırını şöyle okuyabiliriz:

> Movie kayıtları içinde
> bu `_id` değerine sahip
> tek filmi bul.

Mongoose burada bize kullanışlı bir kısa yol sunar.

Şu ifade:

```js
Movie.findById(movieId)
```

## Örnek 53

temelde şu aramayı anlatır:

```js
Movie.findOne({
  _id: movieId,
});
```

## Örnek 54

Yani `findById()` sihirli, bambaşka bir arama değildir.

Özellikle `_id` alanına göre **tek bir document** bulmak için hazırlanmış daha okunaklı bir yoldur.

Tam ihtiyacımız olan şey.

---

## Bütün Filmler Yerine Tek Bir Film

Şimdi sorgumuzu yazalım:

```js
const movie = await Movie.findById(movieId);
```

## Örnek 55

33. bölümde:

```js
const movies = await Movie.find({});
```

## Örnek 56

yazmıştık.

Dikkat edin:

```text
movies
↓
çoğul
↓
birden fazla film
↓
dizi
```

## Örnek 57

Şimdi ise:

```text
movie
↓
tekil
↓
tek film
```

## Örnek 58

kullanıyoruz.

Bu küçük isim farkı kodun ne yaptığını okumayı kolaylaştırır.

Karşılaştıralım:

```text
Movie.find({})
↓
birden fazla document
↓
movies

Movie.findById(movieId)
↓
tek bir document
↓
movie
```

## Örnek 59

Eğer Dune bulunursa `movie`, Dune'ı temsil eden tek bir Mongoose Document olur.

---

## Route Artık Dune'ı Bulabilir

Şu ana kadar elimizde iki satır var:

```js
const { movieId } = req.params;

const movie = await Movie.findById(movieId);
```

## Örnek 60

Bütün hikâye şimdilik:

```text
URL içindeki _id
↓
req.params.movieId
↓
movieId
↓
Movie.findById(movieId)
↓
tek film
```

## Örnek 61

Şimdi Dune bulunduysa kullanıcıya gönderebiliriz:

```js
res.status(200).json({
  message: "Film getirildi.",
  data: movie,
});
```

## Örnek 62

Böylece:

```text
GET /movies/DUNE_ID
↓
movieId alınır
↓
MongoDB'de aranır
↓
Dune bulunur
↓
200 OK
↓
Dune Response olarak gönderilir
```

## Örnek 63

Akış tamamlanmış gibi görünüyor.

Ama küçük bir problemimiz var.

---

## Ya Böyle Bir Film Yoksa?

Kullanıcı URL'ye MongoDB'de hiçbir document'a ait olmayan bir `_id` gönderirse ne olacak?

Örneğin biçim olarak geçerli bir ObjectId düşünelim ama collection'ımızda böyle bir kayıt bulunmasın.

Mongoose:

```js
await Movie.findById(movieId);
```

## Örnek 64

sorgusunu çalıştırır.

Ama eşleşen document yoksa sonuç:

```js
null
```

## Örnek 65

olabilir.

Yani:

```text
movie
↓
null
```

## Örnek 66

Bu durumda:

```js
res.status(200).json({
  message: "Film getirildi.",
  data: movie,
});
```

## Örnek 67

göndermek garip olur.

Çünkü aslında film bulunmadı.

Kullanıcıya doğru şeyi söylemeliyiz.

---

## 404 Not Found Burada Ne Anlama Geliyor?

`404 Not Found` ifadesini muhtemelen web sitelerinde daha önce gördünüz.

Genellikle:

```text
Sayfa bulunamadı.
```

## Örnek 68

mesajıyla karşılaşırız.

Ama `404` yalnızca HTML sayfaları için değildir.

HTTP açısından anlamı daha geneldir:

```text
İstenen kaynak bulunamadı.
```

## Örnek 69

Bizim kaynağımız bir film.

Kullanıcı:

```text
şu _id değerine sahip filmi getir
```

## Örnek 70

dedi.

MongoDB'de böyle bir film yoksa:

```text
404 Not Found
```

## Örnek 71

uygun bir Response olur.

Buradaki anlam:

```text
404
↓
HTTP durum kodu

Not Found
↓
istenen kaynak bulunamadı
```

## Örnek 72

Bizim örneğimizde:

```text
GET /movies/:movieId
↓
movieId ile film arandı
↓
film yok
↓
404 Not Found
```

## Örnek 73

---

## movie null ise Route'u Burada Bitirelim

Şöyle kontrol edebiliriz:

```js
if (movie === null) {
  return res.status(404).json({
    message: "Film bulunamadı.",
  });
}
```

## Örnek 74

Burada:

```js
movie === null
```

## Örnek 75

şunu sorar:

> Mongoose bu `_id` ile eşleşen
> hiçbir film bulamadı mı?

Cevap evetse `404` gönderiyoruz.

Peki neden başına:

```js
return
```

## Örnek 76

yazdık?

Çünkü Response'u gönderdikten sonra Route fonksiyonunun devam etmesini istemiyoruz.

Yoksa aşağıdaki başarılı Response satırına da ulaşmaya çalışabilir.

Akışı düşünün:

```text
film yok
↓
404 gönder
↓
Route'u bitir
```

## Örnek 77

`return` burada tam olarak bu son adımı sağlar.

---

## Film Varsa 200, Yoksa 404

Artık iki olasılığımız var.

Birinci olasılık:

```text
movie bulundu
↓
200 OK
↓
filmi gönder
```

## Örnek 78

İkinci olasılık:

```text
movie bulunamadı
↓
404 Not Found
↓
"Film bulunamadı."
```

## Örnek 79

Bu ayrım API'mizin daha anlamlı cevap vermesini sağlar.

Çünkü:

```text
Sorgu çalıştı ama film yok.
```

## Örnek 80

ile:

```text
Film bulundu.
```

## Örnek 81

aynı sonuç değildir.

---

## GET /movies/:movieId Route'unu Tamamlayalım

Şimdi parçaları birleştirelim:

```js
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
```

## Örnek 82

İlk bakışta birkaç parça var.

Ama hikâyesi oldukça sade:

```text
movieId'yi URL'den al
↓
ObjectId biçimini kontrol et
↓
biçim yanlışsa 400
↓
Movie.findById(movieId)
↓
film yoksa 404
↓
film varsa 200 + film
```

## Örnek 83

Bölümün merkezi artık üç adımdır:

```js
const { movieId } = req.params;

if (!mongoose.isObjectIdOrHexString(movieId)) {
  return res.status(400).json({
    message: "Geçersiz film kimliği.",
  });
}

const movie = await Movie.findById(movieId);
```

## Örnek 84

İlk satır Express tarafıdır.

Ortadaki kontrol, URL'den gelen değerin ObjectId biçimine uygun olup olmadığını denetler.

Son satır Mongoose tarafıdır.

Hepsinin ortasında aynı veri vardır:

```text
movieId
```

## Örnek 85

Yani yine Express ile Mongoose'u küçük bir veri parçasıyla birbirine bağlıyoruz.

---

## index.js Dosyamız Şimdi Nasıl Görünüyor?

Dosya sürekliliğini yine kaybetmeyelim.

33. bölümde:

```http
GET /movies
```

## Örnek 86

ile bütün filmleri getiriyorduk.

32. bölümde:

```http
POST /movies
```

## Örnek 87

ile yeni film oluşturuyorduk.

Şimdi bunların yanına:

```http
GET /movies/:movieId
```

## Örnek 88

ekliyoruz.

`index.js` dosyamızın ilgili hâli şöyle:

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

## Örnek 89

Artık üç gerçek davranışımız var:

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
```

## Örnek 90

Film API'mizin küçük ama gerçek bir omurgası oluşmaya başladı.

---

## Dune'ın Gerçek _id Değerini Alalım

Şimdi Route'u gerçekten deneyelim.

Önce:

```bash
npm start
```

## Örnek 91

ile sunucuyu çalıştırın.

Ardından Postman'de:

```http
GET http://localhost:3000/movies
```

## Örnek 92

Request'ini gönderin.

Response içindeki Dune kaydını bulun.

Örneğin:

```json
{
  "_id": "SIZIN_DUNE_ID_DEGERINIZ",
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8
}
```

## Örnek 93

Buradaki gerçek `_id` değerini kopyalayın.

Sonra yeni bir Request gönderin:

```text
GET http://localhost:3000/movies/SIZIN_DUNE_ID_DEGERINIZ
```

## Örnek 94

Bu kez Body göndermiyoruz.

Kimlik zaten URL'nin içinde.

`Send` düğmesine basalım.

---

## Bu Kez Dizi Değil, Tek Film Gelmeli

İşlem başarılıysa:

```text
200 OK
```

## Örnek 95

görmeliyiz.

Response Body şuna benzer olabilir:

```json
{
  "message": "Film getirildi.",
  "data": {
    "_id": "SIZIN_DUNE_ID_DEGERINIZ",
    "title": "Dune",
    "releaseYear": 2021,
    "voteAverage": 8
  }
}
```

## Örnek 96

33. bölümde Response içindeki:

```js
data
```

## Örnek 97

bir diziydi:

```js
[
  { ...Interstellar },
  { ...Dune }
]
```

## Örnek 98

Şimdi ise:

```js
data
```

## Örnek 99

tek bir film.

```text
GET /movies
↓
data = []

GET /movies/:movieId
↓
data = {}
```

## Örnek 100

Buradaki `{}` gösterimi tek bir nesneyi anlatmak için kullandığımız sade bir hatırlatmadır.

Artık kullanıcı Dune'ın detay sayfasına gittiğinde bütün filmleri almak zorunda değil.

Yalnızca Dune gelebilir.

---

## Olmayan Bir Film İstersek Ne Olur?

Şimdi ikinci yolu da düşünelim.

MongoDB'de bulunmayan ama ObjectId biçiminde bir değer gönderdiğimizi varsayalım.

Bu durumda:

```js
const movie = await Movie.findById(movieId);
```

## Örnek 101

sonucunda:

```js
movie === null
```

## Örnek 102

olabilir.

Route şu bölüme girer:

```js
if (movie === null) {
  return res.status(404).json({
    message: "Film bulunamadı.",
  });
}
```

## Örnek 103

Postman'de:

```text
404 Not Found
```

## Örnek 104

ve:

```json
{
  "message": "Film bulunamadı."
}
```

## Örnek 105

Response'unu görürüz.

Bu kez backend bize yalnızca:

```text
bir şeyler ters gitti
```

## Örnek 106

demiyor.

Daha anlamlı biçimde:

```text
İstediğin film burada yok.
```

## Örnek 107

diyor.

---

## Peki `abc` Gibi Bir movieId Gönderirsek?

Şimdi biraz önce eklediğimiz kontrolün neden önemli olduğunu deneyelim.

Postman'den:

```http
GET http://localhost:3000/movies/abc
```

## Örnek 108

Request'ini gönderelim.

Express:

```text
abc
```

## Örnek 109

değerini yine başarıyla yakalar.

Yani:

```js
req.params.movieId
```

## Örnek 110

değeri:

```text
abc
```

## Örnek 111

olur.

Fakat Route'umuz hemen şu kontrolü yapar:

```js
if (!mongoose.isObjectIdOrHexString(movieId)) {
  return res.status(400).json({
    message: "Geçersiz film kimliği.",
  });
}
```

## Örnek 112

`abc`, ObjectId'nin yaygın 24 karakterlik hexadecimal gösterimine uygun olmadığı için MongoDB sorgusuna geçmeden Response göndeririz.

Postman'de:

```text
400 Bad Request
```

## Örnek 113

ve:

```json
{
  "message": "Geçersiz film kimliği."
}
```

## Örnek 114

görürüz.

Bu ayrımı net tutalım:

```text
abc
↓
kimlik biçimi yanlış
↓
400 Bad Request

geçerli ObjectId biçimi
ama eşleşen film yok
↓
404 Not Found

geçerli ObjectId
ve film var
↓
200 OK
```

## Örnek 115

Böylece geçersiz bir kullanıcı girdisini yanlışlıkla:

```text
500 Internal Server Error
```

## Örnek 116

gibi bir sunucu hatasıymış gibi göstermemiş oluruz.

Tam validation konusuna ileride ayrıca döneceğiz.

Ama bu Route'un doğru davranması için gereken temel kimlik kontrolünü artık burada tamamlamış olduk.

---

## Neden title ile Aramadık?

Burada akla doğal bir soru gelebilir:

> Dune'ı neden `_id` ile arıyoruz?
> `title: "Dune"` desek olmaz mı?

Elbette Mongoose ile başka field'lara göre de arama yapabiliriz.

Ama 25. bölümde gördüğümüz problemi hatırlayın.

Aynı isimde birden fazla film olabilir:

```text
Dune — 1984

Dune — 2021
```

## Örnek 117

Başlık:

```text
film içeriğinin bir parçasıdır
```

## Örnek 118

ve değişebilir veya tekrar edebilir.

`_id` ise document'ı ayırt etmek için vardır.

Bu yüzden tek bir MongoDB document'ını hedefleyen Route'larda `_id` çok doğal bir seçimdir.

Şu bağlantı artık daha anlamlı:

```text
MongoDB document
↓
_id ile ayırt edilir

GET /movies/:movieId
↓
_id URL üzerinden taşınır

Movie.findById(movieId)
↓
o document bulunur
```

## Örnek 119

Daha önce öğrendiğimiz `_id` konusu artık API tasarımımızın içinde çalışıyor.

---

## `find({})` ile `findById()` Arasındaki Fark

İki bölümün en önemli farkını yan yana koyalım.

33. bölüm:

```js
const movies = await Movie.find({});
```

## Örnek 120

Amaç:

```text
bütün filmleri getir
```

## Örnek 121

Sonuç:

```text
dizi
```

## Örnek 122

34. bölüm:

```js
const movie = await Movie.findById(movieId);
```

## Örnek 123

Amaç:

```text
belirli _id değerine sahip
tek filmi getir
```

## Örnek 124

Sonuç:

```text
tek Document
veya
null
```

## Örnek 125

Kısaca:

```text
find({})
↓
çoklu sonuç

findById(id)
↓
tek sonuç
```

## Örnek 126

Mongoose Model'imizin neden önemli olduğunu artık daha fazla hissetmeye başlıyoruz.

Aynı `Movie` Model'i:

```text
bütün filmleri bulabiliyor

tek filmi bulabiliyor

yeni film oluşturabiliyor
```

## Örnek 127

---

## 🧠 Akılda Kalsın

Bu bölümün köprüsü:

```text
URL
↓
Route Parameter
↓
req.params
↓
movieId
↓
ObjectId biçim kontrolü
↓
Movie.findById(movieId)
↓
MongoDB
```

## Örnek 128

Route:

```js
app.get("/movies/:movieId", ...)
```

## Örnek 129

içindeki:

```text
:movieId
```

## Örnek 130

değişebilen URL parçasını yakalar.

Gerçek değer:

```js
req.params.movieId
```

## Örnek 131

üzerinden alınır.

Sonra:

```js
const movie = await Movie.findById(movieId);
```

## Örnek 132

ile `_id` değeri eşleşen film aranır.

Kimlik biçimi geçersizse:

```text
400 Bad Request
```

## Örnek 133

Film bulunursa:

```text
200 OK
```

## Örnek 134

Geçerli kimliğe rağmen film bulunamazsa:

```text
404 Not Found
```

## Örnek 135

göndeririz.

---

## Bölüm Özeti

Bu bölüme Dune'ın detay sayfasına tıklayan bir kullanıcıyla başladık.

`GET /movies` bütün filmleri getiriyordu.

Ama bize yalnızca:

```text
Dune
```

## Örnek 136

gerekiyordu.

Filmi ayırt etmek için daha önce öğrendiğimiz:

```text
_id
```

## Örnek 137

değerini kullandık.

Route'umuzu:

```js
app.get("/movies/:movieId", ...)
```

## Örnek 138

şeklinde oluşturduk.

`:movieId` kısmının gerçek bir id olmadığını, URL'deki değişken değeri yakalayan bir Route Parameter olduğunu gördük.

Express bu değeri:

```js
req.params
```

## Örnek 139

içine koydu.

Biz de:

```js
const { movieId } = req.params;
```

## Örnek 140

ile aldık.

Önce:

```js
mongoose.isObjectIdOrHexString(movieId)
```

## Örnek 141

ile kimliğin ObjectId biçimine uygun olup olmadığını kontrol ettik.

Biçim geçersizse:

```text
400 Bad Request
```

## Örnek 142

gönderdik.

Ardından:

```js
const movie = await Movie.findById(movieId);
```

## Örnek 143

ile MongoDB'de tek bir film aradık.

Film bulunduğunda:

```text
200 OK
```

## Örnek 144

ile filmi gönderdik.

Film bulunamadığında:

```text
404 Not Found
```

## Örnek 145

ile:

```json
{
  "message": "Film bulunamadı."
}
```

## Örnek 146

Response'unu gönderdik.

Artık API'miz:

```text
GET /movies
↓
bütün filmler

GET /movies/:movieId
↓
tek film

POST /movies
↓
yeni film
```

## Örnek 147

işlemlerini yapabiliyor.

---

## Sonraki Adım

Dune'ı bulabiliyoruz.

Peki Dune'ın puanı değişirse ne olacak?

Şu anda MongoDB'de:

```text
voteAverage: 8
```

## Örnek 148

olduğunu düşünün.

Bunu:

```text
8.3
```

## Örnek 149

yapmak istiyoruz.

Filmi nasıl bulacağımızı artık biliyoruz:

```text
/movies/:movieId
```
