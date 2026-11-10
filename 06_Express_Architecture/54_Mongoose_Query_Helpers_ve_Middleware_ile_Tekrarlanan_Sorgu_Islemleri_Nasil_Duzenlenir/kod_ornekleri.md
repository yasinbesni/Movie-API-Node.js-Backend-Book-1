# Bölüm 54 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Mongoose Query Helpers ve Middleware ile Tekrarlanan Sorgu İşlemleri Nasıl Düzenlenir?

## Aynı Sorgu Kuralları Birçok Yerde Tekrarlanırsa

53. bölümde Movie API'mizin güncelleme tarafını geliştirdik.

Artık iki farklı güncelleme yaklaşımını biliyoruz:

```text
Document'ı bul
↓
alanlarını değiştir
↓
save()
```

## Örnek 2

ve:

```text
doğrudan update Query'si oluştur
↓
findByIdAndUpdate() / updateOne()
↓
MongoDB güncellemeyi uygulasın
```

## Örnek 3

Ayrıca `$set`, `$inc` ve `$unset` gibi update operatörlerini kullandık.

Listeleme tarafında da Query'miz zamanla büyüdü.

52. bölümün sonunda `GET /movies` için şu Query'yi kullanıyorduk:

```js
const moviesQuery = Movie.find(filter)
  .select({
    title: 1,
    releaseYear: 1,
    voteAverage: 1,
  })
  .sort({
    voteAverage: -1,
    releaseYear: -1,
    _id: 1,
  })
  .skip(skip)
  .limit(limit)
  .lean();
```

## Örnek 4

Bu kod yanlış değildir.

Hatta artık her satırın ne yaptığını biliyoruz.

Bu bölümde yeni bir problem oluşturmak yerine, önce iki **somut tekrar ihtiyacına** bakacağız.

Birincisi:

> Aynı `select()` ve `sort()` davranışına başka bir Movie Query'sinde de ihtiyaç duyarsak ne yapacağız?

İkincisi:

> Doğrudan update Query'lerinde `runValidators: true` kuralını her seferinde yazmak zorunda mıyız?

Bu iki problem bizi iki farklı Mongoose aracına götürecek:

```text
Query Helper
↓
açıkça çağırdığımız ortak Query davranışı

Query Middleware
↓
belirli Query'lerde otomatik çalışan ortak kural
```

## Örnek 5

Önce isimleri ezberlemeyelim.

Önce problemleri görelim.

---

## Birinci Problem: Aynı `select()` ve `sort()` Kodunu Tekrar Yazmak

Şu anda Movie listemizin ortak iki kararı var:

```text
hangi alanlar dönecek?
↓
select()

hangi sırada dönecek?
↓
sort()
```

## Örnek 6

Mevcut liste Query'mizde bunları açıkça yazıyoruz:

```js
Movie.find(filter)
  .select({
    title: 1,
    releaseYear: 1,
    voteAverage: 1,
  })
  .sort({
    voteAverage: -1,
    releaseYear: -1,
    _id: 1,
  });
```

## Örnek 7

Şu anda bu kodu yalnızca bir yerde kullanıyor olsaydık, mutlaka başka bir yapıya taşımamız gerekmezdi.

Fakat tekrarın nasıl oluştuğunu somut görmek için ikinci bir Query düşünelim.

Örneğin uygulamanın başka bir yerinde yalnızca puanı 8 veya daha yüksek filmleri almak istediğimizi varsayalım:

```js
const highRatedMoviesQuery = Movie.find({
  voteAverage: {
    $gte: 8,
  },
})
  .select({
    title: 1,
    releaseYear: 1,
    voteAverage: 1,
  })
  .sort({
    voteAverage: -1,
    releaseYear: -1,
    _id: 1,
  })
  .lean();
```

## Örnek 8

Şimdi iki Query'yi yan yana düşünelim.

İlk Query:

```js
Movie.find(filter)
  .select({
    title: 1,
    releaseYear: 1,
    voteAverage: 1,
  })
  .sort({
    voteAverage: -1,
    releaseYear: -1,
    _id: 1,
  });
```

## Örnek 9

İkinci Query:

```js
Movie.find({
  voteAverage: {
    $gte: 8,
  },
})
  .select({
    title: 1,
    releaseYear: 1,
    voteAverage: 1,
  })
  .sort({
    voteAverage: -1,
    releaseYear: -1,
    _id: 1,
  });
```

## Örnek 10

Filtreler farklıdır.

Ama şu bölüm aynıdır:

```js
.select({
  title: 1,
  releaseYear: 1,
  voteAverage: 1,
})
.sort({
  voteAverage: -1,
  releaseYear: -1,
  _id: 1,
})
```

## Örnek 11

İşte tekrar burada ortaya çıktı.

Şimdi daha anlamlı bir soru sorabiliriz:

> Movie listesi için kullandığımız bu ortak `select + sort` davranışına bir isim verip farklı Query'lerde kullanabilir miyiz?

Evet.

Mongoose'taki **Query Helper** tam olarak bunun için kullanılabilir.

---

## Query Helper'ı Kendi Query Metodumuz Gibi Düşünelim

Şimdiye kadar Mongoose'un hazır Query metotlarını kullandık:

```js
find()
select()
sort()
skip()
limit()
lean()
```

## Örnek 12

Query Helper ile bu zincire kendi metodumuzu ekleyebiliriz.

Örneğin Movie listesi için:

```js
forMovieList()
```

## Örnek 13

adında bir metot oluşturabiliriz.

Sonra şu kodu:

```js
Movie.find(filter)
  .select({
    title: 1,
    releaseYear: 1,
    voteAverage: 1,
  })
  .sort({
    voteAverage: -1,
    releaseYear: -1,
    _id: 1,
  });
```

## Örnek 14

şöyle yazabiliriz:

```js
Movie.find(filter).forMovieList();
```

## Örnek 15

Burada önemli nokta şudur:

```js
forMovieList()
```

## Örnek 16

Mongoose'un hazır bir metodu değildir.

Bu adı ve davranışı biz oluşturacağız.

---

## `forMovieList()` Query Helper'ını Oluşturalım

Çalıştığımız dosya:

```text
src/db/models/movie.js
```

## Örnek 17

Schema tanımından sonra, Model oluşturulmadan önce şu kodu ekleyelim:

```js
movieSchema.query.forMovieList = function () {
  return this
    .select({
      title: 1,
      releaseYear: 1,
      voteAverage: 1,
    })
    .sort({
      voteAverage: -1,
      releaseYear: -1,
      _id: 1,
    });
};
```

## Örnek 18

Bu kodu ilk bakışta tek parça okumaya çalışmayalım.

Önce:

```js
movieSchema.query.forMovieList
```

## Örnek 19

kısmını okuyalım:

```text
Movie Query'lerine
↓
forMovieList adında
↓
kendi metodumuzu ekle
```

## Örnek 20

Şimdi fonksiyonun içindeki:

```js
this
```

## Örnek 21

değerine bakalım.

Şu kodu çalıştırdığımızı düşünelim:

```js
Movie.find(filter).forMovieList();
```

## Örnek 22

Önce:

```js
Movie.find(filter)
```

## Örnek 23

bir Mongoose Query oluşturur.

Ardından:

```js
.forMovieList()
```

## Örnek 24

çağrılır.

Helper içindeki `this`, işte bu Query'yi temsil eder.

Bunu şöyle düşünebiliriz:

```text
Movie.find(filter)
↓
mevcut Query
↓
forMovieList()
↓
this = mevcut Query
↓
select() ekle
↓
sort() ekle
↓
Query'yi geri döndür
```

## Örnek 25

Bu nedenle:

```js
return this
  .select(...)
  .sort(...);
```

## Örnek 26

yazabiliyoruz.

### Neden Arrow Function Kullanmadık?

Helper içindeki `this` değerine ihtiyacımız var.

Bu nedenle:

```js
function () {
```

## Örnek 27

kullanıyoruz.

Şunu kullanmıyoruz:

```js
() => {
```

## Örnek 28

Çünkü arrow function kendi `this` bağlamını oluşturmaz.

Bu bölüm için akılda tutmamız gereken basit kural:

```text
Mongoose bize this verecekse
↓
normal function kullan
```

## Örnek 29

---

## Tekrar Eden İki Query Şimdi Nasıl Görünüyor?

Biraz önce iki yerde aynı `select + sort` kodunu yazmıştık.

Artık ilk Query:

```js
Movie.find(filter).forMovieList();
```

## Örnek 30

olabilir.

Yüksek puanlı filmler için verdiğimiz ikinci örnek de:

```js
Movie.find({
  voteAverage: {
    $gte: 8,
  },
}).forMovieList();
```

## Örnek 31

olabilir.

Artık ortak davranış tek yerde tanımlıdır:

```js
movieSchema.query.forMovieList = function () {
  return this
    .select({
      title: 1,
      releaseYear: 1,
      voteAverage: 1,
    })
    .sort({
      voteAverage: -1,
      releaseYear: -1,
      _id: 1,
    });
};
```

## Örnek 32

İki Query'nin filtreleri farklı olabilir.

Ama Movie listesine ait ortak:

```text
alan seçimi
+
sıralama
```

## Örnek 33

aynı helper üzerinden gelir.

Query Helper'ın ne işe yaradığını artık daha somut okuyabiliriz:

> Tekrar kullanılabilecek bir Query davranışına isim verip onu Query zincirinde açıkça çağırırız.

---

## Mevcut `getMovies` Query'mizi Güncelleyelim

52. bölümün sonunda:

```js
const moviesQuery = Movie.find(filter)
  .select({
    title: 1,
    releaseYear: 1,
    voteAverage: 1,
  })
  .sort({
    voteAverage: -1,
    releaseYear: -1,
    _id: 1,
  })
  .skip(skip)
  .limit(limit)
  .lean();
```

## Örnek 34

kullanıyorduk.

Şimdi:

```js
const moviesQuery = Movie.find(filter)
  .forMovieList()
  .skip(skip)
  .limit(limit)
  .lean();
```

## Örnek 35

kullanabiliriz.

Burada her şeyi helper'ın içine taşımadık.

Helper içinde yalnızca:

```text
select
+
sort
```

## Örnek 36

var.

`skip()` ve `limit()` hâlâ Controller'da görünür.

Çünkü bunların değerleri Request'ten gelen `page` ve `limit` bilgilerine göre değişiyor.

`lean()` da görünür kalıyor.

Böylece:

```js
forMovieList()
```

## Örnek 37

adı gerçekten yalnızca Movie listesinin ortak görünümünü temsil ediyor.

Query Helper Query'yi çalıştırmaz.

Şu kod:

```js
const moviesQuery = Movie.find(filter)
  .forMovieList()
  .skip(skip)
  .limit(limit)
  .lean();
```

## Örnek 38

hâlâ yalnızca Query'yi hazırlar.

Sonucu yine:

```js
const movies = await moviesQuery.exec();
```

## Örnek 39

ile alırız.

---

## İkinci Problem: `runValidators: true` Değerini Unutmak

Şimdi güncelleme tarafına dönelim.

53. bölümde PATCH Controller'ımızda şu Query'yi kullanmıştık:

```js
const updateMovieQuery = Movie.findByIdAndUpdate(
  movieId,
  {
    $set: fieldsToSet,
  },
  {
    returnDocument: "after",
    runValidators: true,
  },
);
```

## Örnek 40

Buradaki:

```js
runValidators: true
```

## Örnek 41

neden vardı?

Çünkü doğrudan update Query'lerinde update validator'larının çalışmasını istiyoruz.

Şimdi aynı projede başka bir yerde yine `findOneAndUpdate()` kullandığımızı düşünelim:

```js
const movie = await Movie.findOneAndUpdate(
  {
    title: "Dune",
  },
  {
    $set: {
      voteAverage: 8.5,
    },
  },
  {
    returnDocument: "after",
    runValidators: true,
  },
).exec();
```

## Örnek 42

Burada da aynı seçenek tekrarlandı:

```js
runValidators: true
```

## Örnek 43

Şimdi asıl risk ortaya çıkıyor.

Bir geliştirici yeni bir update Query'si yazıp şunu unutabilir:

```js
runValidators: true
```

## Örnek 44

Örneğin:

```js
Movie.findOneAndUpdate(
  { title: "Dune" },
  {
    $set: {
      voteAverage: 11,
    },
  },
  {
    returnDocument: "after",
  },
);
```

## Örnek 45

Movie Schema'mızda:

```text
voteAverage <= 10
```

## Örnek 46

kuralı var.

Ama doğrudan update validator'ları varsayılan olarak açık değildir.

Eğer projemizde şu kararı veriyorsak:

> `findOneAndUpdate()` ailesindeki güncellemelerde validation her zaman çalışmalı.

artık bu yalnızca tek bir Controller'a ait seçenek değildir.

Bu, aynı Query türü için ortak bir proje kuralıdır.

İşte burada **Query Middleware** devreye girebilir.

---

## Middleware'i “Araya Giren Otomatik Kod” Olarak Düşünelim

Express Middleware'i daha önce görmüştük.

Orada kabaca:

```text
Request
↓
middleware
↓
Controller
```

## Örnek 47

akışı vardı.

Mongoose Query Middleware'de ise HTTP Request'in arasına girmiyoruz.

Bir Mongoose Query'sinin çalışmasına bağlanıyoruz.

Bizim örneğimizde istediğimiz akış şu:

```text
findOneAndUpdate Query'si
↓
Query çalışmadan hemen önce
↓
runValidators: true ekle
↓
Query çalışsın
```

## Örnek 48

Mongoose'ta işlemden önce çalışan middleware için:

```js
pre()
```

## Örnek 49

kullanılır.

`pre` kelimesini burada:

```text
önce
```

## Örnek 50

olarak okuyabiliriz.

---

## `findOneAndUpdate` İçin Middleware Ekleyelim

Yine:

```text
src/db/models/movie.js
```

## Örnek 51

dosyasındayız.

Query Helper'ın altına şu kodu ekleyelim:

```js
movieSchema.pre(
  "findOneAndUpdate",
  function () {
    this.setOptions({
      runValidators: true,
    });
  },
);
```

## Örnek 52

Şimdi bunu da parçalara ayıralım.

```js
movieSchema.pre(
```

## Örnek 53

şunu anlatır:

```text
bir Mongoose işlemi çalışmadan önce
↓
bu kodu çalıştır
```

## Örnek 54

Hangi işlemden önce?

```js
"findOneAndUpdate"
```

## Örnek 55

Yani:

```text
findOneAndUpdate Query'si
↓
pre middleware
↓
runValidators: true
↓
Query çalışır
```

## Örnek 56

Helper'da olduğu gibi burada da:

```js
this
```

## Örnek 57

kullanıyoruz.

Fakat `this` güncellenecek Movie Document'ı değildir.

`this`, çalışmak üzere olan Mongoose Query'sidir.

Bu nedenle:

```js
this.setOptions({
  runValidators: true,
});
```

## Örnek 58

ile o Query'nin seçeneklerine `runValidators` ekleyebiliyoruz.

---

## Middleware'in Gerçekten Çalıştığını Terminalde Görelim

Bu noktayı yalnızca okuyup geçmeyelim.

Middleware'i ilk kez denerken geçici olarak iki `console.log()` ekleyebiliriz:

```js
movieSchema.pre(
  "findOneAndUpdate",
  function () {
    console.log(
      "Middleware öncesi:",
      this.getOptions().runValidators,
    );

    this.setOptions({
      runValidators: true,
    });

    console.log(
      "Middleware sonrası:",
      this.getOptions().runValidators,
    );
  },
);
```

## Örnek 59

PATCH Controller'ından artık açıkça:

```js
runValidators: true
```

## Örnek 60

seçeneğini kaldıralım.

Controller'daki Query:

```js
const updateMovieQuery = Movie.findByIdAndUpdate(
  movieId,
  {
    $set: fieldsToSet,
  },
  {
    returnDocument: "after",
  },
);
```

## Örnek 61

hâline gelsin.

Şimdi geçerli bir PATCH Request gönderelim:

```http
PATCH http://localhost:3000/movies/MOVIE_ID
```

## Örnek 62

Body:

```json
{
  "voteAverage": 8.7
}
```

## Örnek 63

Terminalde yaklaşık olarak şunu görmeliyiz:

```text
Middleware öncesi: undefined
Middleware sonrası: true
```

## Örnek 64

Bu küçük deney önemli.

Çünkü middleware'in ne yaptığını artık yalnızca teorik olarak söylemiyoruz.

Gerçekten:

```text
Query geldi
↓
runValidators henüz yoktu
↓
middleware çalıştı
↓
runValidators true oldu
```

## Örnek 65

akışını görüyoruz.

Deney bittikten sonra `console.log()` satırlarını silebiliriz.

Proje kodunda kalacak asıl middleware:

```js
movieSchema.pre(
  "findOneAndUpdate",
  function () {
    this.setOptions({
      runValidators: true,
    });
  },
);
```

## Örnek 66

olur.

---

## Peki Biz `findByIdAndUpdate()` Kullanıyorduk

PATCH Controller'ımızda:

```js
Movie.findByIdAndUpdate(...)
```

## Örnek 67

kullanıyoruz.

Ama middleware'i:

```js
"findOneAndUpdate"
```

## Örnek 68

adıyla yazdık.

Bu ilk bakışta garip görünebilir.

Mongoose'ta:

```js
findByIdAndUpdate(id, update, options)
```

## Örnek 69

`_id` üzerinden çalışan bir `findOneAndUpdate()` kısa yoludur.

Bu yüzden `findByIdAndUpdate()` çağrısı da:

```js
pre("findOneAndUpdate")
```

## Örnek 70

middleware'ini tetikler.

Akışı şöyle okuyabiliriz:

```text
findByIdAndUpdate(movieId, ...)
↓
_id ile findOneAndUpdate
↓
pre("findOneAndUpdate")
↓
runValidators: true
↓
update Query'si çalışır
```

## Örnek 71

---

## Validation'ı Postman ile de Doğrulayalım

Şimdi middleware'in yalnızca terminalde çalıştığını değil, gerçek davranışı koruduğunu da görelim.

PATCH Controller'ında artık açıkça:

```js
runValidators: true
```

## Örnek 72

yok.

Buna rağmen şu Request'i gönderelim:

```http
PATCH http://localhost:3000/movies/MOVIE_ID
```

## Örnek 73

Body:

```json
{
  "voteAverage": 11
}
```

## Örnek 74

Movie Schema'mızda `voteAverage` için üst sınır `10` olduğu için Request yine validation hatasıyla reddedilmelidir.

Yani artık:

```text
Controller
↓
findByIdAndUpdate()
↓
Query Middleware
↓
runValidators: true
↓
Schema validation
↓
11 geçersiz
```

## Örnek 75

akışı çalışır.

Buradaki önemli fark şudur:

> `runValidators: true` kuralı artık Controller'ın hatırlaması gereken bir ayrıntı değildir; `findOneAndUpdate` Query'leri için Schema seviyesinde uygulanır.

---

## Query Helper ile Query Middleware'i Yan Yana Koyalım

Artık farkı tanımdan değil, yaptığımız iki örnekten çıkarabiliriz.

Query Helper'da:

```js
Movie.find(filter).forMovieList();
```

## Örnek 76

biz özellikle:

```js
.forMovieList()
```

## Örnek 77

çağrısını yazdık.

Yazmazsak helper çalışmaz.

Query Middleware'de ise Controller'da:

```js
.pre(...)
```

## Örnek 78

çağrısı yoktur.

Controller yalnızca:

```js
Movie.findByIdAndUpdate(...)
```

## Örnek 79

çalıştırır.

Middleware ilgili Query türünü gördüğünde otomatik devreye girer.

En kısa ayrım:

| Yapı | Biz ne yaptık? | Nasıl devreye girdi? |
|---|---|---|
| Query Helper | `select + sort` davranışına `forMovieList()` adını verdik | Query zincirinde `.forMovieList()` yazarak açıkça çağırdık |
| Query Middleware | `findOneAndUpdate` işlemlerinde validation'ı ortak kural yaptık | İlgili Query çalışınca otomatik devreye girdi |

Bunu daha da kısaltırsak:

```text
Query Helper
↓
ben çağırırım

Query Middleware
↓
ilgili Query çalışınca otomatik çalışır
```

## Örnek 80

---

## Her Tekrarı Helper veya Middleware'e Taşımayalım

Burada önemli bir sınır vardır.

Bir şeyi yapabiliyor olmamız, her yerde yapmamız gerektiği anlamına gelmez.

Örneğin yalnızca bir yerde kullanılan:

```js
.sort({ releaseYear: -1 })
```

## Örnek 81

satırı için hemen Query Helper oluşturmak gereksiz olabilir.

Aynı şekilde yalnızca tek bir update işleminde gerekli olan özel bir option'ı middleware ile bütün Query'lere zorla uygulamak da doğru olmayabilir.

Daha iyi sorular şunlardır:

Query Helper için:

> Bu Query davranışının tekrar kullanılabilecek, anlamlı bir adı var mı?

Query Middleware için:

> Bu davranış gerçekten bu Query türü için genel bir kural mı?

Bizim örneklerimizde:

```text
forMovieList()
↓
Movie listesine ait ortak alan + sıralama davranışı
```

## Örnek 82

ve:

```text
pre("findOneAndUpdate")
↓
findOneAndUpdate ailesinde update validation'ı açık tut
```

## Örnek 83

kararlarını verdik.

---

## Schema İçindeki Sıra Nasıl Olmalı?

Query Helper ve Middleware'i Movie Schema üzerinde tanımlıyoruz.

Bunları Model oluşturulmadan önce eklemeliyiz.

Dosyanın genel sırası:

```text
Schema'yı oluştur
↓
Query Helper'ı ekle
↓
Query Middleware'i ekle
↓
Model'ı oluştur
```

## Örnek 84

Yani:

```js
const movieSchema = new mongoose.Schema(
  {
    // alanlar
  },
  {
    timestamps: true,
  },
);

movieSchema.query.forMovieList = function () {
  // ortak Query davranışı
};

movieSchema.pre(
  "findOneAndUpdate",
  function () {
    // otomatik Query davranışı
  },
);

export const Movie =
  mongoose.models.Movie ||
  mongoose.model("Movie", movieSchema);
```

## Örnek 85

şeklinde düşünebiliriz.

---

## `movie.js` Dosyasının Güncel Hâli

Bu bölümde eklediğimiz parçalarla ilgili kısım şöyle olur:

```js
import mongoose from "mongoose";

const movieSchema = new mongoose.Schema(
  {
    title: {
      type: String,
      required: [
        true,
        "Film adı zorunludur.",
      ],
    },
    releaseYear: {
      type: Number,
      min: [
        1888,
        "Film yılı 1888'den küçük olamaz.",
      ],
    },
    voteAverage: {
      type: Number,
      min: [
        0,
        "Film puanı 0'dan küçük olamaz.",
      ],
      max: [
        10,
        "Film puanı 10'dan büyük olamaz.",
      ],
    },
  },
  {
    timestamps: true,
  },
);

movieSchema.query.forMovieList = function () {
  return this
    .select({
      title: 1,
      releaseYear: 1,
      voteAverage: 1,
    })
    .sort({
      voteAverage: -1,
      releaseYear: -1,
      _id: 1,
    });
};

movieSchema.pre(
  "findOneAndUpdate",
  function () {
    this.setOptions({
      runValidators: true,
    });
  },
);

export const Movie =
  mongoose.models.Movie ||
  mongoose.model("Movie", movieSchema);
```

## Örnek 86

Burada artık Schema dosyasında üç farklı sorumluluk görüyoruz:

```text
Schema alanları
↓
Movie verisinin yapısı ve validation kuralları

Query Helper
↓
isteyerek çağırdığımız ortak Query davranışı

Query Middleware
↓
belirli Query'lerde otomatik uygulanan ortak kural
```

## Örnek 87

---

## Controller Tarafındaki Değişiklikleri Birlikte Görelim

Bu bölümde Controller tarafında iki farklı sadeleşme oldu.

Liste Query'sinde daha önce açıkça yazdığımız:

```text
select()
+
sort()
```

## Örnek 88

ortak davranışını artık:

```js
.forMovieList()
```

## Örnek 89

ile çağırıyoruz. `skip()`, `limit()` ve `lean()` ise değişken veya Query'ye özgü kararlar oldukları için Controller'da görünür kalıyor.

PATCH tarafında ise daha önce Query seçenekleri içinde açıkça bulunan:

```js
runValidators: true
```

## Örnek 90

artık Controller'da görünmüyor. Çünkü `pre("findOneAndUpdate")` Middleware'i bu seçeneği ilgili Query çalışırken otomatik olarak ekliyor.

İki değişiklik aynı tür değildir:

```text
forMovieList()
↓
Controller açıkça çağırır

runValidators
↓
Query Middleware otomatik ekler
```

## Örnek 91

Bu ayrım, bölümün en önemli noktasıdır.

---

## Bölüm Sonu Uygulaması

Bu bölümü yalnızca okuyarak geçmeyin.

### 1. Query Helper'ı Deneyin

`movie.js` içine:

```js
movieSchema.query.forMovieList = function () {
  return this
    .select({
      title: 1,
      releaseYear: 1,
      voteAverage: 1,
    })
    .sort({
      voteAverage: -1,
      releaseYear: -1,
      _id: 1,
    });
};
```

## Örnek 92

ekleyin.

`getMovies` Query'sini:

```js
const moviesQuery = Movie.find(filter)
  .forMovieList()
  .skip(skip)
  .limit(limit)
  .lean();
```

## Örnek 93

hâline getirin.

Postman'de:

```http
GET http://localhost:3000/movies?page=1&limit=5
```

## Örnek 94

Request'ini gönderin.

Response'ta yine yalnızca:

```text
_id
title
releaseYear
voteAverage
```

## Örnek 95

alanlarının geldiğini ve sıralamanın değişmediğini doğrulayın.

### 2. Middleware'in Query'yi Değiştirdiğini Görün

Middleware'e geçici `console.log()` satırlarını ekleyin:

```js
movieSchema.pre(
  "findOneAndUpdate",
  function () {
    console.log(
      "Middleware öncesi:",
      this.getOptions().runValidators,
    );

    this.setOptions({
      runValidators: true,
    });

    console.log(
      "Middleware sonrası:",
      this.getOptions().runValidators,
    );
  },
);
```

## Örnek 96

PATCH Controller'ından açık:

```js
runValidators: true
```

## Örnek 97

seçeneğini kaldırın.

Geçerli bir PATCH Request gönderin ve terminalde:

```text
Middleware öncesi: undefined
Middleware sonrası: true
```

## Örnek 98

benzeri çıktıyı görün.

Ardından Body'yi:

```json
{
  "voteAverage": 11
}
```

## Örnek 99

olarak gönderin.

Schema validation'ın Request'i yine reddettiğini doğrulayın.

Son olarak geçici `console.log()` satırlarını kaldırın.

Bu uygulamanın sonunda şu iki cümleyi kendi kelimelerinizle açıklayabiliyor olmalısınız:

```text
Query Helper'ı Query zincirinde ben çağırırım.

Query Middleware ilgili Query işlemi çalıştığında otomatik devreye girer.
```

## Örnek 100

---

## 🧠 Akılda Kalsın

Query Helper:

```text
tekrar kullanılabilir Query davranışı
↓
anlamlı bir isim ver
↓
Query zincirinde açıkça çağır
```

## Örnek 101

Örneğin:

```js
Movie.find(filter).forMovieList();
```

## Örnek 102

Query Helper içindeki `this`:

```text
mevcut Mongoose Query'si
```

## Örnek 103

olur.

Query Middleware:

```text
belirli Query işlemi
↓
öncesinde veya sonrasında
↓
otomatik davranış çalıştır
```

## Örnek 104

Bu bölümde:

```js
pre("findOneAndUpdate")
```

## Örnek 105

ile update validation seçeneğini otomatik ekledik.

En önemli ayrım:

```text
Query Helper
↓
ben çağırırım

Query Middleware
↓
ilgili Query çalışınca otomatik çalışır
```

## Örnek 106

Her iki yapıda da `this` değerini kullandığımız için normal `function` tercih ettik.

---

## Sonraki Adım

Movie API'mizde artık yalnızca Query yazmıyoruz.

Aynı zamanda Query davranışlarını nerede tutacağımızı da düşünmeye başladık.

Fakat gerçek uygulamalarda başka bir problem daha vardır.

Movie Document'larımız şu ana kadar çoğunlukla kendi alanlarını taşıdı:

```text
title
releaseYear
voteAverage
```

## Örnek 107

Gerçek bir film uygulamasında ise:

```text
yönetmen
oyuncular
türler
```

## Örnek 108

gibi başka veriler de bulunabilir.

Bu noktada yeni bir veri modelleme sorusu doğar:

> İlişkili veriyi aynı Document'ın içine mi koymalıyız, yoksa başka bir Document'a referans mı vermeliyiz?

55. bölümde MongoDB'deki:

```text
embedding
referencing
```
