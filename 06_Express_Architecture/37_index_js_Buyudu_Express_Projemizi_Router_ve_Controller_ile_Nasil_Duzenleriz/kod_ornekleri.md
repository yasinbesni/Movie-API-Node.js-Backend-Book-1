# Bölüm 37 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# index.js Büyüdü: Express Projemizi Router ve Controller ile Nasıl Düzenleriz?

## CRUD Tamamlandı. Ama `index.js` Dosyasını Açtığımızda Ne Görüyoruz?

Yeni bir film eklemek, güncellemek veya silmek artık Movie API'mizin bildiği işler. Fakat şimdi küçük bir değişiklik yapmak için `index.js` dosyasını açtığımızı düşünün. Sunucu ayarları, MongoDB bağlantısı ve bütün HTTP yolları aynı dosyada. Aradığımız birkaç satıra ulaşmak bile eskisinden uzun sürüyor.

İşlemler çalışıyor; sorun onların yanlış olması değil, kod büyüdükçe kimin ne yaptığını izlemenin zorlaşması.

36. bölümde DELETE işlemini de tamamladık. 

Artık API'miz:

```text
POST
↓
film oluşturuyor

GET
↓
film okuyor

PATCH
↓
film güncelliyor

DELETE
↓
film siliyor
```

## Örnek 2

Yani ilk CRUD döngümüz çalışıyor.

Fakat 36. bölümün sonunda başka bir şey dikkatimizi çekti.

Bütün Route'larımız hâlâ:

```text
index.js
```

## Örnek 3

dosyasının içinde.

Şu anda aynı dosyada:

```text
Express uygulamasının oluşturulması

express.json()

GET /

GET /movies

GET /movies/:movieId

POST /movies

PATCH /movies/:movieId

DELETE /movies/:movieId

MongoDB bağlantısı

sunucunun başlatılması
```

## Örnek 4

gibi farklı görevler bulunuyor.

Kodumuz yanlış değil.

Çalışıyor.

Ama uygulama büyüdükçe şu soru daha önemli hâle gelecek:

> Hangi kodun görevi ne ve o kod hangi dosyada bulunmalı?

Bu bölümde yeni bir özellik eklemeyeceğiz.

Çalışan kodumuzu daha anlaşılır bir düzene taşıyacağız.

---

## Bu Bölümde Neyi Ayıracağız?

Şimdilik yalnızca bildiğimiz kavramlarla ilerleyeceğiz.

Yeni yapımızın merkezinde dört parça olacak:

```text
routers
controllers
server.js
index.js
```

## Örnek 5

Ve zaten var olan:

```text
db
```

## Örnek 6

klasörümüzü kullanmaya devam edeceğiz.

Bölümün sonunda proje yapımız kabaca şöyle olacak:

```text
project
├── src
│   ├── controllers
│   │   └── movies.js
│   ├── db
│   │   ├── initMongoConnection.js
│   │   └── models
│   │       └── movie.js
│   ├── routers
│   │   └── movies.js
│   ├── index.js
│   └── server.js
├── .env
├── .gitignore
├── package.json
└── package-lock.json
```

## Örnek 7

Bu bölümde yalnızca mevcut kodumuzun gerçekten ihtiyaç duyduğu parçaları oluşturacağız.

Önce elimizdeki gerçek problemi çözeceğiz:

```text
Route tanımı
ile
Route çalışınca yapılacak işlem
aynı yerde duruyor
```

## Örnek 8

Yani proje yapısını bir anda büyütmek yerine, her dosyayı yaptığı işi anlayarak oluşturacağız.

---

## Router ve Controller Neden Ayrı?

Şu anki `index.js` içinden bir Route'a bakalım:

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

## Örnek 9

Bu kod tek parça gibi görünüyor.

Ama aslında iki farklı soruya cevap veriyor.

Birinci soru:

```text
Hangi Request?
```

## Örnek 10

Cevap:

```text
GET /movies
```

## Örnek 11

İkinci soru:

```text
Bu Request geldiğinde ne yapılacak?
```

## Örnek 12

Cevap:

```text
Movie.find({})
↓
filmleri getir
↓
Response gönder
```

## Örnek 13

İşte bu iki sorumluluğu ayıracağız.

---

## Router'ın Görevi Nedir?

Router'ın görevi çok net:

> Hangi HTTP yöntemi ve hangi yol geldiğinde hangi Controller çalışacak?

Yani Router şu eşleştirmeyi bilir:

```text
GET /
↓
getMovies

POST /
↓
createMovie

PATCH /:movieId
↓
updateMovieById
```

## Örnek 14

Router'ın işi:

```text
MongoDB'den film getir
```

## Örnek 15

değildir.

Router'ın işi:

```text
Bu Request'i
hangi fonksiyona göndereceğim?
```

## Örnek 16

sorusunu cevaplamaktır.

En kısa hâliyle:

```text
Router
↓
YÖNLENDİRİR
```

## Örnek 17

---

## Controller'ın Görevi Nedir?

Controller ise Router'ın yönlendirdiği Request'i işler.

Controller'ın görevi:

```text
Request'ten gereken bilgiyi al

MongoDB işlemini yap

gerekli kontrolü yap

uygun HTTP Response'u gönder
```

## Örnek 18

Örneğin tek film Controller'ı şunlarla ilgilenebilir:

```text
req.params.movieId

ObjectId biçimi doğru mu?

Movie.findById(movieId)

film yoksa 404

film varsa 200
```

## Örnek 19

Dikkat edin.

Controller:

```text
GET /movies/:movieId
```

## Örnek 20

adresini tanımlamaz.

O adres Router'ın sorumluluğudur.

Controller ise:

```text
Bu Route beni çağırdı.
Şimdi ne yapmalıyım?
```

## Örnek 21

sorusunu cevaplar.

En kısa hâliyle:

```text
Controller
↓
İŞLER
```

## Örnek 22

---

## Router ve Controller'ı Yan Yana Koyalım

```text
ROUTER

Hangi HTTP yöntemi?
Hangi yol?
Hangi Controller?

↓

YÖNLENDİRİR
```

## Örnek 23



```text
CONTROLLER

Request'ten ne lazım?
Hangi MongoDB işlemi yapılacak?
Hangi status code dönecek?
Response ne olacak?

↓

İŞLER
```

## Örnek 24

Örneğin:

```text
GET /movies
↓
Router
↓
getMovies Controller
↓
Movie.find({})
↓
MongoDB
↓
200 Response
```

## Örnek 25

Bu akış mevcut uygulamamız için yeterli ve takip edilebilir.

![Router ve Controller katmanları](images/37-katmanli-mimari-baski.svg)

---

## İlk Controller Dosyamızı Oluşturalım

Şimdi gerçekten dosyaları oluşturmaya başlayalım.

Oluşturacağımız klasör:

```text
src/controllers
```

## Örnek 26

Dosya:

```text
src/controllers/movies.js
```

## Örnek 27

Tam yol:

```text
project
└── src
    └── controllers
        └── movies.js
```

## Örnek 28

Bu dosyada film Route'larımız çalıştığında yapılacak işlemler bulunacak.

---

### Controller Hangi Dosyayı Kullanacak?

Film işlemlerimiz MongoDB'ye:

```text
Movie
```

## Örnek 29

Model'i üzerinden ulaşıyordu.

Model'imiz zaten:

```text
src/db/models/movie.js
```

## Örnek 30

dosyasında.

Controller dosyamız:

```text
src/controllers/movies.js
```

## Örnek 31

olduğu için import yolu:

```js
import { Movie } from "../db/models/movie.js";
```

## Örnek 32

olacak.

Buradaki relative path'i bir kez daha net okuyalım:

```text
src/controllers/movies.js
↓
../
↓
src
↓
db/models/movie.js
```

## Örnek 33

Yani burada:

```text
../
```

## Örnek 34

ile `controllers` klasöründen bir seviye yukarı çıkar,

ardından:

```text
db/models/movie.js
```

## Örnek 35

yoluna ilerleriz.

Dosyamızın adı:

```text
movie.js
```

## Örnek 36

olduğu için import yolunda da aynı büyük-küçük harf kullanımını koruyoruz.

Ayrıca `_id` biçimini kontrol etmek için:

```js
import mongoose from "mongoose";
```

## Örnek 37

kullanıyoruz.

Dosyanın başlangıcı:

```js
import mongoose from "mongoose";

import { Movie } from "../db/models/movie.js";
```

## Örnek 38

olur.

---

## GET /movies İşlemini Controller'a Taşıyalım

Eski `index.js` içinde `GET /movies` Route'u hem yolu hem de film getirme işlemini aynı yerde taşıyordu.

Şimdi Route'un içindeki işlem mantığını ayrı bir fonksiyona taşıyoruz.

Dosya:

```text
src/controllers/movies.js
```

## Örnek 39

Kod:

```js
export const getMovies = async (req, res) => {
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
};
```

## Örnek 40

Burada:

```js
export
```

## Örnek 41

kullandık.

Çünkü birazdan Router dosyamız:

```js
getMovies
```

## Örnek 42

fonksiyonunu import edecek.

---

### Neden Controller'da `/movies` Yazmıyor?

Dikkat edin.

Controller artık `getMovies` adlı ayrı bir fonksiyon olarak çalışıyor.

Route yolu ise Router dosyasında kalıyor.

Burada:

```text
GET
```

## Örnek 43

veya:

```text
/movies
```

## Örnek 44

yazmıyor.

Çünkü bunlar Controller'ın görevi değil.

Controller yalnızca:

```text
Beni çağırdın.
Filmleri getirip Response göndereyim.
```

## Örnek 45

diyor.

HTTP yöntemi ve yol eşleştirmesini birazdan Router yapacak.

---

## GET /movies/:movieId Controller'ı

Aynı dosyada devam ediyoruz:

```text
src/controllers/movies.js
```

## Örnek 46

Kod:

```js
export const getMovieById = async (req, res) => {
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
};
```

## Örnek 47

Burada önceki bölümlerde öğrendiğimiz hiçbir davranışı değiştirmedik.

Sadece kodun bulunduğu dosyayı değiştirdik.

Akış hâlâ:

```text
req.params.movieId
↓
ObjectId kontrolü
↓
Movie.findById(movieId)
↓
404 veya 200
```

## Örnek 48

---

## POST /movies Controller'ı

Aynı dosya:

```text
src/controllers/movies.js
```

## Örnek 49

Kod:

```js
export const createMovie = async (req, res) => {
  try {
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
  } catch (error) {
    console.error(error);

    res.status(500).json({
      message: "Film oluşturulamadı.",
    });
  }
};
```

## Örnek 50

Burada da:

```text
req.body
↓
new Movie(...)
↓
save()
↓
201 Created
```

## Örnek 51

akışı aynı kaldı.

---

## PATCH /movies/:movieId Controller'ı

Dosya hâlâ:

```text
src/controllers/movies.js
```

## Örnek 52

Kod:

```js
export const updateMovieById = async (req, res) => {
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
};
```

## Örnek 53

35. bölümde kullandığımız ana yöntem yine aynı:

```text
findById()
↓
Document'ı değiştir
↓
save()
```

## Örnek 54

Bu bölümde update yöntemini değiştirmiyoruz.

Dosya sorumluluklarını düzenliyoruz.

---

## DELETE /movies/:movieId Controller'ı

Aynı dosyada son işlemimiz:

```js
export const deleteMovieById = async (req, res) => {
  try {
    const { movieId } = req.params;

    if (!mongoose.isObjectIdOrHexString(movieId)) {
      return res.status(400).json({
        message: "Geçersiz film kimliği.",
      });
    }

    const deletedMovie =
      await Movie.findByIdAndDelete(movieId);

    if (deletedMovie === null) {
      return res.status(404).json({
        message: "Film bulunamadı.",
      });
    }

    res.status(200).json({
      message: "Film silindi.",
      data: deletedMovie,
    });
  } catch (error) {
    console.error(error);

    res.status(500).json({
      message: "Film silinemedi.",
    });
  }
};
```

## Örnek 55

Artık film işlemlerinin hepsi:

```text
src/controllers/movies.js
```

## Örnek 56

dosyasında bulunuyor.

---

## controllers/movies.js Dosyasının Tam Hâli

Dosya:

```text
src/controllers/movies.js
```

## Örnek 57



```js
import mongoose from "mongoose";

import { Movie } from "../db/models/movie.js";

export const getMovies = async (req, res) => {
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
};

export const getMovieById = async (req, res) => {
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
};

export const createMovie = async (req, res) => {
  try {
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
  } catch (error) {
    console.error(error);

    res.status(500).json({
      message: "Film oluşturulamadı.",
    });
  }
};

export const updateMovieById = async (req, res) => {
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
};

export const deleteMovieById = async (req, res) => {
  try {
    const { movieId } = req.params;

    if (!mongoose.isObjectIdOrHexString(movieId)) {
      return res.status(400).json({
        message: "Geçersiz film kimliği.",
      });
    }

    const deletedMovie =
      await Movie.findByIdAndDelete(movieId);

    if (deletedMovie === null) {
      return res.status(404).json({
        message: "Film bulunamadı.",
      });
    }

    res.status(200).json({
      message: "Film silindi.",
      data: deletedMovie,
    });
  } catch (error) {
    console.error(error);

    res.status(500).json({
      message: "Film silinemedi.",
    });
  }
};
```

## Örnek 58

Şimdi Controller'larımız hazır.

Peki onları kim çağıracak?

İşte sıra Router'da.

---

## Router Dosyamızı Oluşturalım

Oluşturacağımız klasör:

```text
src/routers
```

## Örnek 59

Dosya:

```text
src/routers/movies.js
```

## Örnek 60

Tam yol:

```text
project
└── src
    └── routers
        └── movies.js
```

## Örnek 61

---

### Controller'ları Router'a Import Edelim

Router'ın hangi fonksiyonu çalıştıracağını bilmesi gerekiyor.

Bu nedenle:

```js
import {
  createMovie,
  deleteMovieById,
  getMovieById,
  getMovies,
  updateMovieById,
} from "../controllers/movies.js";
```

## Örnek 62

yazıyoruz.

Dosya ilişkisi:

```text
src/routers/movies.js
↓
src/controllers/movies.js
```

## Örnek 63

---

## express.Router() Nedir?

Şimdiye kadar Route'ları:

```js
app.get(...)
app.post(...)
app.patch(...)
app.delete(...)
```

## Örnek 64

şeklinde doğrudan Express uygulamasına ekliyorduk.

Artık film Route'larını ayrı bir dosyada toplamak istiyoruz.

Bunun için:

```js
express.Router()
```

## Örnek 65

kullanacağız.

Önce Express'i import ederiz:

```js
import express from "express";
```

## Örnek 66

Sonra:

```js
const router = express.Router();
```

## Örnek 67

yazarız.

Şöyle düşünebiliriz:

```text
app
↓
bütün Express uygulaması

router
↓
belirli bir Route grubunu
ayrı dosyada toplamak için
kullandığımız yapı
```

## Örnek 68

Bizim Router'ımız:

```text
movies
```

## Örnek 69

Route grubunu taşıyacak.

---

## İlk Route Eşleştirmemizi Yapalım

Bütün filmleri getiren Controller:

```js
getMovies
```

## Örnek 70

idi.

Router:

```js
router.get("/", getMovies);
```

## Örnek 71

şeklinde yazılabilir.

Bunu şöyle okuyun:

```text
GET /
geldiğinde
↓
getMovies çalıştır
```

## Örnek 72

Ama burada bir soru var.

Bizim gerçek adresimiz:

```text
/movies
```

## Örnek 73

değil miydi?

Neden Router içinde yalnızca:

```text
/
```

## Örnek 74

yazdık?

Birazdan `server.js` içinde bunun nasıl `/movies` olduğuna geleceğiz.

Şimdilik Router dosyasını tamamlayalım.

---

## Diğer Film Route'larını Ekleyelim

Tek filmi getirmek:

```js
router.get("/:movieId", getMovieById);
```

## Örnek 75

Film oluşturmak:

```js
router.post("/", createMovie);
```

## Örnek 76

Film güncellemek:

```js
router.patch("/:movieId", updateMovieById);
```

## Örnek 77

Film silmek:

```js
router.delete("/:movieId", deleteMovieById);
```

## Örnek 78

Dikkat edin.

Router dosyasında:

```text
Movie.find()

new Movie()

save()

res.status()
```

## Örnek 79

gibi işlem ayrıntıları yok.

Yalnızca:

```text
HTTP yöntemi
+
yol
+
Controller
```

## Örnek 80

eşleşmesi var.

İşte Router'ın görevi bu.

---

## Router'ı Export Edelim

Dosyanın sonunda:

```js
export default router;
```

## Örnek 81

yazalım.

Çünkü birazdan `server.js` bu Router'ı import edecek.

---

## routers/movies.js Dosyasının Tam Hâli

Dosya:

```text
src/routers/movies.js
```

## Örnek 82



```js
import express from "express";

import {
  createMovie,
  deleteMovieById,
  getMovieById,
  getMovies,
  updateMovieById,
} from "../controllers/movies.js";

const router = express.Router();

router.get("/", getMovies);

router.get("/:movieId", getMovieById);

router.post("/", createMovie);

router.patch("/:movieId", updateMovieById);

router.delete("/:movieId", deleteMovieById);

export default router;
```

## Örnek 83

Dosyaya yalnızca gözümüzle bakarak bile şunları görebiliyoruz:

```text
hangi Route'lar var?

hangi HTTP yöntemlerini kullanıyorlar?

hangi Controller'ları çalıştırıyorlar?
```

## Örnek 84

Bu, Router dosyasının iyi yaptığı iştir.

---

## Named Export ve Default Export'u Kısaca Hatırlayalım

Controller fonksiyonlarımızı named export ile dışarı açtık:

```js
export const getMovies = async (req, res) => {
  const movies = await Movie.find({});

  res.status(200).json({
    message: "Filmler getirildi.",
    data: movies,
  });
};
```

## Örnek 85

Bu bir **named export** olduğu için Router dosyasında süslü parantezle import ediyoruz:

```js
import {
  getMovies,
} from "../controllers/movies.js";
```

## Örnek 86

Kısaca:

```text
export const getMovies = ...
↓
import { getMovies } from ...
```

## Örnek 87

Router dosyasının sonunda ise:

```js
export default router;
```

## Örnek 88

yazdık.

Bu bir **default export** olduğu için birazdan `server.js` içinde süslü parantez kullanmadan import edeceğiz:

```js
import moviesRouter from "./routers/movies.js";
```

## Örnek 89

Kısaca:

```text
export default router
↓
import moviesRouter from ...
```

## Örnek 90

Burada bir ayrıntı daha var.

Default export edilen değer:

```js
export default router;
```

## Örnek 91

olmasına rağmen import ederken:

```js
moviesRouter
```

## Örnek 92

adını verebildik.

Default export'ta import eden dosya değere kendi seçtiği adı verebilir.

Named export'ta ise:

```js
export const getMovies = ...
```

## Örnek 93

şeklinde dışarı açılan değeri:

```js
import { getMovies } from "...";
```

## Örnek 94

şeklinde adıyla alıyoruz.

---

## Şimdi server.js Dosyasını Oluşturalım

Şu ana kadar Express uygulamasını:

```text
index.js
```

## Örnek 95

içinde oluşturuyorduk.

Örneğin:

```js
const app = express();

app.use(express.json());
```

## Örnek 96

ve Route'larımız aynı dosyadaydı.

Şimdi Express uygulamasını kuran kısmı ayrı bir dosyaya taşıyacağız.

Dosya:

```text
src/server.js
```

## Örnek 97

Tam yol:

```text
project
└── src
    └── server.js
```

## Örnek 98

---

## server.js Ne İşe Yarayacak?

Şimdilik görevi:

```text
Express uygulamasını oluştur

uygulamanın genel ayarlarını ekle

Router'ları uygulamaya bağla

app'i export et
```

## Örnek 99

olacak.

Burada henüz yeni bir kavram öğretmeye çalışmıyoruz.

Önceden kullandığımız:

```js
app.use(express.json());
```

## Örnek 100

satırını da buraya taşıyacağız.

Şimdilik bu satırı daha önce kullandığımız hâliyle koruyoruz.

Request akışının nasıl ilerlediğini incelerken `app.use()` satırına yeniden döneceğiz.

---

## server.js Dosyasını Yazalım

Dosya:

```text
src/server.js
```

## Örnek 101

İlk olarak:

```js
import express from "express";

import moviesRouter from "./routers/movies.js";
```

## Örnek 102

yazalım.

Sonra:

```js
export const app = express();
```

## Örnek 103

ile Express uygulamasını oluşturalım ve export edelim.

Ardından:

```js
app.use(express.json());
```

## Örnek 104

satırımızı ekleyelim.

Ana Route'umuzu da burada tutabiliriz:

```js
app.get("/", (req, res) => {
  res.send("Merhaba Express!");
});
```

## Örnek 105

Şimdi film Router'ımızı uygulamaya bağlayacağız.

---

## app.use("/movies", moviesRouter) Ne Demek?

Şu satırı yazalım:

```js
app.use("/movies", moviesRouter);
```

## Örnek 106

Bunu şimdilik şöyle okuyabiliriz:

> `/movies` ile başlayan film Request'lerini `moviesRouter` içine gönder.

Router dosyamızda:

```js
router.get("/", getMovies);
```

## Örnek 107

vardı.

Server'da ise:

```js
app.use("/movies", moviesRouter);
```

## Örnek 108

var.

İkisini birleştirelim:

```text
/movies
+
/
↓
/movies
```

## Örnek 109

Yani:

```js
app.use("/movies", moviesRouter);
```

## Örnek 110

ile:

```js
router.get("/", getMovies);
```

## Örnek 111

birlikte:

```http
GET /movies
```

## Örnek 112

Route'unu oluşturur.

---

## :movieId Nasıl Birleşiyor?

Router'da:

```js
router.get("/:movieId", getMovieById);
```

## Örnek 113

var.

Server'da:

```js
app.use("/movies", moviesRouter);
```

## Örnek 114

var.

Birleştirirsek:

```text
/movies
+
/:movieId
↓
/movies/:movieId
```

## Örnek 115

olur.

Örneğin:

```http
GET /movies/68ac1234abcd5678ef901234
```

## Örnek 116

Request'i geldiğinde:

```text
/movies
↓
moviesRouter

/:movieId
↓
getMovieById
```

## Örnek 117

eşleşmesi oluşur.

---

## Bütün Route'ların Yeni Akışı

Artık:

```text
GET /movies
↓
getMovies

GET /movies/:movieId
↓
getMovieById

POST /movies
↓
createMovie

PATCH /movies/:movieId
↓
updateMovieById

DELETE /movies/:movieId
↓
deleteMovieById
```

## Örnek 118

Route'larımızın dışarıdan görünen adresleri değişmedi.

Sadece kodun hangi dosyada bulunduğu değişti.

Bu çok önemli:

```text
API davranışı aynı
↓
kod düzeni değişti
```

## Örnek 119

---

## server.js Dosyasının Tam Hâli

Dosya:

```text
src/server.js
```

## Örnek 120



```js
import express from "express";

import moviesRouter from "./routers/movies.js";

export const app = express();

app.use(express.json());

app.get("/", (req, res) => {
  res.send("Merhaba Express!");
});

app.use("/movies", moviesRouter);
```

## Örnek 121

Bu dosyanın görevi artık daha net:

```text
Express uygulamasını kur
↓
film Router'ını bağla
↓
app'i export et
```

## Örnek 122

---

## Peki index.js Ne Yapacak?

Şimdi eski büyük `index.js` dosyamıza dönelim.

Route kodlarını Controller ve Router'a taşıdık.

Express uygulamasını da:

```text
server.js
```

## Örnek 123

içinde oluşturduk.

O zaman `index.js` için geriye kalan asıl görev:

```text
ortam değişkenlerini yükle

MongoDB'ye bağlan

Express uygulamasını başlat
```

## Örnek 124

oldu.

Bu ayrım önemli:

```text
server.js
↓
uygulamayı KUR

index.js
↓
uygulamayı BAŞLAT
```

## Örnek 125

---

## index.js Dosyasını src İçine Taşıyalım

Şimdi proje kökündeki:

```text
index.js
```

## Örnek 126

dosyasını:

```text
src/index.js
```

## Örnek 127

konumuna taşıyalım.

Tam yol:

```text
project
└── src
    └── index.js
```

## Örnek 128

Dosyanın yeni içeriği:

```js
import "dotenv/config";

import { initMongoConnection } from "./db/initMongoConnection.js";
import { app } from "./server.js";

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

## Örnek 129

---

## Import Yolu Neden Değişti?

Eski root `index.js` dosyasında:

```js
import { initMongoConnection }
  from "./src/db/initMongoConnection.js";
```

## Örnek 130

kullanıyorduk.

Ama `index.js` artık:

```text
src/index.js
```

## Örnek 131

konumunda.

`db` klasörü de aynı `src` klasörünün içinde.

Bu yüzden yeni yol:

```js
import { initMongoConnection }
  from "./db/initMongoConnection.js";
```

## Örnek 132

oldu.

Aynı şekilde `app`:

```js
import { app } from "./server.js";
```

## Örnek 133

ile geliyor.

Dosyanın yeri değişince göreli import yolu da değişebilir.

Bunu her dosya taşıdığımızda kontrol edeceğiz.

---

## server.js ile index.js'i Karıştırmayalım

`server.js`:

```text
Express app oluşturur

express.json() ekler

Router'ları bağlar

app'i export eder
```

## Örnek 134

`index.js`:

```text
.env değerlerini yükler

MongoDB bağlantısını başlatır

app.listen() ile sunucuyu çalıştırır
```

## Örnek 135

Kısaca:

```text
server.js
↓
UYGULAMAYI KUR

index.js
↓
UYGULAMAYI BAŞLAT
```

## Örnek 136

---

## package.json da Yeni Dosya Yolunu Bilmelidir

Burada küçük ama önemli bir devamlılık noktası var.

Daha önce giriş dosyamız:

```text
index.js
```

## Örnek 137

idi.

Şimdi:

```text
src/index.js
```

## Örnek 138

oldu.

Eğer `package.json` içinde:

```json
{
  "scripts": {
    "start": "node index.js"
  }
}
```

## Örnek 139

varsa bunu:

```json
{
  "scripts": {
    "start": "node src/index.js"
  }
}
```

## Örnek 140

şeklinde güncellemeliyiz.

Eğer daha önce bir `dev` komutunuz da varsa onun da yeni yolu göstermesi gerekir.

Örneğin:

```json
{
  "scripts": {
    "start": "node src/index.js",
    "dev": "nodemon src/index.js"
  }
}
```

## Örnek 141

Burada yeni bir npm konusu öğrenmiyoruz.

Yalnızca çalıştırılan dosyanın konumu değiştiği için komutu da güncelliyoruz.

---

## .env Dosyasını src İçine Taşıyor muyuz?

Hayır.

Şimdilik:

```text
.env
.gitignore
package.json
package-lock.json
```

## Örnek 142

gibi proje dosyaları kökte kalabilir.

Biz JavaScript uygulama kodumuzu:

```text
src
```

## Örnek 143

altında düzenliyoruz.

Son görünüm:

```text
project
├── src
│   ├── controllers
│   │   └── movies.js
│   ├── db
│   │   ├── initMongoConnection.js
│   │   └── models
│   │       └── movie.js
│   ├── routers
│   │   └── movies.js
│   ├── index.js
│   └── server.js
├── .env
├── .gitignore
├── package.json
└── package-lock.json
```

## Örnek 144

---

## Bir GET Request Artık Hangi Dosyalardan Geçiyor?

Şimdi en önemli resmi görelim.

Postman'den:

```http
GET /movies
```

## Örnek 145

Request'i geldi.

Akış:

```text
Request
↓
src/server.js
↓
app.use("/movies", moviesRouter)
↓
src/routers/movies.js
↓
router.get("/", getMovies)
↓
src/controllers/movies.js
↓
getMovies()
↓
Movie.find({})
↓
MongoDB
↓
200 Response
```

## Örnek 146

Dosya isimlerini ezberlemek yerine bu yolculuğu anlamaya çalışın.

---

## Tek Film Request'i Nasıl İlerliyor?

```http
GET /movies/DUNE_ID
```

## Örnek 147

Akış:

```text
src/server.js
↓
/movies

src/routers/movies.js
↓
GET /:movieId

src/controllers/movies.js
↓
getMovieById

req.params.movieId
↓
Movie.findById(movieId)

MongoDB
↓
404 veya 200
```

## Örnek 148

Artık:

```text
hangi Route?
```

## Örnek 149

sorusu Router'da,

```text
Route çalışınca ne olacak?
```

## Örnek 150

sorusu Controller'da cevaplanıyor.

---

## POST Request'i Nasıl İlerliyor?

```http
POST /movies
```

## Örnek 151

Akış:

```text
src/server.js
↓
moviesRouter

src/routers/movies.js
↓
POST /

src/controllers/movies.js
↓
createMovie

req.body
↓
new Movie(...)
↓
save()
↓
MongoDB
↓
201 Created
```

## Örnek 152

Önceki bölümlerde öğrendiğimiz davranış aynen korunuyor.

Sadece kodun adresi artık belli.

---

## Daha Fazla Dosya Daha Fazla Karmaşa mı?

İlk bakışta:

```text
önce
↓
tek index.js

şimdi
↓
birkaç dosya
```

## Örnek 153

olduğu için yapı daha karmaşıkmış gibi görünebilir.

Ama önemli olan dosya sayısı değildir.

Aradığımız kodun nerede olduğunu tahmin edebilmemizdir.

Örneğin:

```text
Film Route'ları nerede?
↓
src/routers/movies.js

Film Request'i geldiğinde yapılan işlem nerede?
↓
src/controllers/movies.js

Movie Model nerede?
↓
src/db/models/movie.js

Express uygulaması nerede kuruluyor?
↓
src/server.js

Uygulama nerede başlıyor?
↓
src/index.js
```

## Örnek 154

Dosya adı artık bize sorumluluk hakkında ipucu veriyor.

---

## Peki try/catch'ler Neden Hâlâ Controller'larda?

Controller dosyasına baktığınızda bir şey dikkatinizi çekmiş olabilir.

Neredeyse bütün fonksiyonlarda aynı desen tekrar ediyor:

```text
try
↓
asıl işlem
↓
catch
↓
hatayı karşıla
```

## Örnek 155

Örneğin:

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

## Örnek 156

Bu tekrar gerçekten var.

Fakat bu bölümde onu kaldırmayacağız.

Çünkü bu tekrarı doğru biçimde ortaklaştırmadan önce Express'in Request akışında bir işlemin diğerine nasıl geçtiğini anlamamız gerekiyor.

Şimdilik Controller'ların davranışı açık ve tanıdık kalsın.

Bu noktada yalnızca tekrarı fark etmemiz yeterli. Henüz onu bir problem olarak çözmeye çalışmayacağız.

38. bölümde önce middleware'in büyüyen proje mimarimizde nerede durduğunu ve Request akışına nasıl katıldığını netleştireceğiz. Bu temel hazır olduğunda, 39. bölümde Controller'lardaki `try/catch` tekrarına geri dönüp merkezi hata yönetimini kuracağız.

---

## Şimdi Uygulamayı Çalıştıralım

`package.json` giriş yolunu güncelledikten sonra:

```bash
npm start
```

## Örnek 157

çalıştırın.

Ardından daha önce kullandığımız Request'leri tekrar deneyin:

```http
GET http://localhost:3000/movies
```

## Örnek 158



```http
GET http://localhost:3000/movies/SIZIN_FILM_ID_DEGERINIZ
```

## Örnek 159



```http
POST http://localhost:3000/movies
```

## Örnek 160



```http
PATCH http://localhost:3000/movies/SIZIN_FILM_ID_DEGERINIZ
```

## Örnek 161



```http
DELETE http://localhost:3000/movies/SILINECEK_FILM_ID_DEGERINIZ
```

## Örnek 162

Adresler değişmedi.

Response davranışları da değişmemeli.

Yani:

```text
kodun düzenini değiştirdik
↓
API'nin dışarıdan görünen davranışını bozmadık
```

## Örnek 163

---

## Import Hatası Alırsak Nereye Bakacağız?

Bu bölümde dosya taşıdığımız için en kolay hata kaynaklarından biri import yollarıdır.

Tek tek kontrol edelim.

`src/controllers/movies.js`:

```js
import { Movie } from "../db/models/movie.js";
```

## Örnek 164

`src/routers/movies.js`:

```js
import {
  createMovie,
  deleteMovieById,
  getMovieById,
  getMovies,
  updateMovieById,
} from "../controllers/movies.js";
```

## Örnek 165

`src/server.js`:

```js
import moviesRouter from "./routers/movies.js";
```

## Örnek 166

`src/index.js`:

```js
import { initMongoConnection } from "./db/initMongoConnection.js";
import { app } from "./server.js";
```

## Örnek 167

Bir dosyanın konumu değiştiğinde import yolunu da o dosyanın bulunduğu yere göre düşünmeliyiz.

Bir de dosya adını kontrol ederiz:

```text
src/db/models/movie.js
```

## Örnek 168

ile:

```text
src/db/models/Movie.js
```

## Örnek 169

görsel olarak çok benzer olsa da büyük-küçük harf kullanımları aynı değildir.

Kitap boyunca kullandığımız gerçek dosya adı:

```text
movie.js
```

## Örnek 170

olacak.

Git kullanan bir projede dosyayı daha önce `Movie.js` adıyla oluşturduysanız ve yalnızca harf boyutunu değiştirdiyseniz Git bu değişikliği bazı sistemlerde hemen fark etmeyebilir.

Böyle bir durumda dosya adını önce geçici başka bir ada, sonra tekrar `movie.js` adına çevirmek sorunu net biçimde çözer.

Örneğin Git Bash'te:

```bash
git mv src/db/models/Movie.js src/db/models/movie-temp.js
git mv src/db/models/movie-temp.js src/db/models/movie.js
```

## Örnek 171

Böylece Git de gerçek dosya adının artık:

```text
movie.js
```

## Örnek 172

olduğunu kaydeder.

---

## 🧠 Akılda Kalsın

Bu bölümün merkezindeki akış:

```text
Request
↓
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

## Örnek 173

Görevler:

```text
Router
↓
hangi Request
hangi Controller?

Controller
↓
Request geldiğinde
ne yapılacak?

Movie Model
↓
MongoDB işlemleri

server.js
↓
Express uygulamasını kur

index.js
↓
MongoDB'ye bağlan
ve uygulamayı başlat
```

## Örnek 174

En kısa ayrım:

```text
Router
↓
YÖNLENDİR

Controller
↓
İŞLE
```

## Örnek 175

---

## Bölüm Özeti

CRUD işlemlerimizi tamamladığımızda bütün kodumuzun:

```text
index.js
```

## Örnek 176

içinde birikmeye başladığını fark ettik.

İlk olarak Route tanımı ile Route'un yaptığı işlemin aynı sorumluluk olmadığını gördük.

Bu nedenle:

```text
src/routers/movies.js
```

## Örnek 177

dosyasını oluşturduk.

Router'a:

```text
HTTP yöntemi
+
Route
+
Controller
```

## Örnek 178

eşleştirmelerini verdik.

Film işlemlerini ise:

```text
src/controllers/movies.js
```

## Örnek 179

dosyasına taşıdık.

Express uygulamasının kurulmasını:

```text
src/server.js
```

## Örnek 180

dosyasına ayırdık.

Uygulamanın başlangıcını:

```text
src/index.js
```

## Örnek 181

içinde tuttuk.

Son yapımız:

```text
src
├── controllers
│   └── movies.js
├── db
│   ├── initMongoConnection.js
│   └── models
│       └── movie.js
├── routers
│   └── movies.js
├── index.js
└── server.js
```

## Örnek 182

oldu.

Bu klasör ağacını projenin değişmez son hâli gibi düşünmeyin.

Uygulama büyüdükçe yeni bir sorumluluk gerçekten ortaya çıkarsa yeni klasörler ekleyeceğiz. Örneğin ortak sabit değerler için `constants`, Request doğrulama kuralları için `validation` veya tekrar kullanılabilir Middleware'ler için `middlewares` klasörü ancak ihtiyaç doğduğunda projeye girecek.

Amacımız baştan çok sayıda boş klasör oluşturmak değil; her klasöre gerçek bir sorumluluk vermektir.

Ve en önemlisi Request'in yolunu artık görebiliyoruz:

```text
Request
↓
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

## Örnek 183

---

## Sonraki Adım

Kodumuz artık daha düzenli.

Controller dosyasına tekrar baktığımızda aynı yapı hâlâ birkaç kez karşımıza çıkıyor:

```text
try/catch
try/catch
try/catch
try/catch
try/catch
```

## Örnek 184

Bu tekrarı fark ettik; fakat bir sonraki bölümün amacı onu hemen ortadan kaldırmak değil. Önce ortak bir işlemin Request akışına nasıl yerleştiğini yeniden görmemiz gerekiyor.

19. bölümde middleware'in temel mantığını görmüştük:

```text
Request
↓
middleware
↓
next()
↓
sonraki işlem
```

## Örnek 185

O zaman projemiz çok daha küçüktü ve Route'larımız henüz Router ile Controller dosyalarına ayrılmamıştı.

Şimdi ise elimizde:

```text
server.js
↓
Router
↓
Controller
```
