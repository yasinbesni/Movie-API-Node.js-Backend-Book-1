# Bölüm 50 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Filtering ile Filmleri Alanlarına Göre Nasıl Süzebiliriz?

## Yalnızca 2021 Yılındaki Filmleri Görmek İstiyoruz

Kullanıcı film kataloğuna girip “Yalnızca 2021 yılında çıkan filmleri görmek istiyorum” dediğinde yalnızca sonraki sayfaya geçmek işe yaramaz. Önce **hangi filmlerin listede yer alacağını** seçmemiz gerekir.

49. bölümde listeyi sayfalara ayırdık. Şimdi aynı Movie Controller'a bu seçimi yapabilmesi için bir koşul ekleyeceğiz.

Artık Controller:

```text
parsePaginationParams()
↓
page ve limit değerlerini al

countDocuments()
↓
sayım Query'sini oluştur

find()
↓
liste Query'sini oluştur

sort + skip + limit
↓
liste Query'sini hazırla

exec()
↓
Query'leri çalıştır

calculatePaginationData()
↓
totalPages, hasNext ve hasPrevious hesapla
```

## Örnek 2

akışıyla çalışıyor.

Fakat bütün bu işlemler hâlâ:

```js
Movie.find({});
```

## Örnek 3

üzerinden yapılıyor.

Yani kullanıcı hangi sayfayı isterse istesin, başlangıç noktası bütün filmler.

Şimdi yeni bir ihtiyaç doğuyor:

> **Sadece 2021 yılında yayımlanan filmleri görmek istiyorum.**

Bu artık pagination problemi değildir.

Bu, **filtering**, yani filtreleme problemidir.

---

## Filtreleme Nedir?

Filtreleme, sorgu sonucuna hangi Document'ların gireceğini bir veya daha fazla koşulla belirlemektir.

Örnek verimiz şöyle olsun:

| Film | releaseYear | voteAverage |
|---|---:|---:|
| Dune | 2021 | 8.3 |
| Interstellar | 2014 | 8.6 |
| Film A | 2021 | 7.2 |
| Film B | 2021 | 9.0 |
| Film C | 2022 | 8.5 |
| Film D | 2021 | 6.8 |

Şu koşulu uygularsak:

```text
releaseYear = 2021
```

## Örnek 4

sonuçta:

```text
Dune
Film A
Film B
Film D
```

## Örnek 5

kalır.

Filtreleme:

```text
Document oluşturmaz
Document güncellemez
Document silmez
```

## Örnek 6

Yalnızca:

```text
hangi Document'lar sorgu sonucuna girecek?
```

## Örnek 7

sorusunu cevaplar.

---

## `find({})` İçindeki Boş Nesne Ne Anlatıyordu?

Şimdiye kadar:

```js
Movie.find({});
```

## Örnek 8

kullanıyorduk.

Buradaki:

```js
{}
```

## Örnek 9

boş filtre nesnesidir.

Belirli bir koşul vermediğimiz için bütün Movie Document'ları sorgunun sonucuna girebilir.

Yalnızca 2021 filmlerini istersek:

```js
Movie.find({
  releaseYear: 2021,
});
```

## Örnek 10

yazabiliriz.

Bu filtreyi şöyle okuyalım:

```text
releaseYear
↓
kontrol edilen alan

2021
↓
aranan değer
```

## Örnek 11

Yani:

> `releaseYear` değeri 2021 olan Movie Document'larını getir.

---

## Bu Bir Eşitlik Filtresidir

Şu nesne:

```js
{
  releaseYear: 2021,
}
```

## Örnek 12

şu anlama gelir:

```text
releaseYear = 2021
```

## Örnek 13

Burada JavaScript'teki:

```js
===
```

## Örnek 14

operatörünü yazmıyoruz.

MongoDB sorgusunda alanı ve aranan değeri yazarak eşitlik koşulu oluşturuyoruz.

Örneğin:

```js
Movie.find({
  title: "Dune",
});
```

## Örnek 15

başlığı tam olarak `"Dune"` olan Document'ları arar.

Başlığın yalnızca bir bölümünü aramak ise farklı bir ihtiyaçtır. Onu 51. bölümde ele alacağız.

---

## Kullanıcı Yılı Query Parameter Olarak Göndersin

48. bölümde query parameter kullanımını öğrenmiştik.

Pagination için:

```http
GET /movies?page=2&limit=10
```

## Örnek 16

kullanıyorduk.

Şimdi filtre bilgisi de query üzerinden gelebilir:

```http
GET /movies?year=2021
```

## Örnek 17

Express tarafında değere:

```js
req.query.year
```

## Örnek 18

ile ulaşabiliriz.

Burada iki isim görüyoruz:

```text
Query parameter
↓
year

Movie alanı
↓
releaseYear
```

## Örnek 19

Bunların aynı isimde olması zorunlu değildir.

Controller veya yardımcı fonksiyonumuz:

```text
year
↓
releaseYear filtresi
```

## Örnek 20

bağlantısını kurabilir.

---

## Filtre Kurallarını da Controller'a Yığmayalım

48. bölümde pagination değerlerini hazırlama işini:

```text
src/utils/parsePaginationParams.js
```

## Örnek 21

dosyasına taşımıştık.

49. bölümde pagination hesaplarını:

```text
src/utils/calculatePaginationData.js
```

## Örnek 22

dosyasına taşımıştık.

Filtering için de aynı düşünceyi sürdürelim.

Yeni yardımcı dosyamız:

```text
src/utils/buildMovieFilter.js
```

## Örnek 23

olsun.

Bu dosyanın görevi:

```text
filter query'lerini oku
↓
değerleri kontrol et
↓
Mongoose'un kullanacağı filter nesnesini oluştur
```

## Örnek 24

olacak.

Controller böylece filtre değerlerinin nasıl dönüştürüldüğünü tek tek bilmek zorunda kalmayacak.

---

## Önce Sayısal Filtre Değerini Hazırlayan Küçük Fonksiyon

Aynı dosyanın içine:

```js
const parseFilterNumber = (value) => {
  if (
    typeof value !== "string" ||
    value.trim() === ""
  ) {
    return null;
  }

  const number = Number(value);

  if (!Number.isFinite(number)) {
    return null;
  }

  return number;
};
```

## Örnek 25

yazalım.

Bu fonksiyon:

```text
"2021"
↓
2021

"8.3"
↓
8.3
```

## Örnek 26

gibi değerleri sayıya dönüştürebilir.

Burada ilk kez kullandığımız şu kontrol önemli:

```js
Number.isFinite(number)
```

## Örnek 27

`Number.isFinite()` kendisine verilen değerin **gerçek ve sonlu bir sayı** olup olmadığını kontrol eder.

Örneğin:

```js
Number.isFinite(2021); // true
Number.isFinite(8.3);  // true
Number.isFinite(NaN);  // false
Number.isFinite(Infinity); // false
```

## Örnek 28

Biz önce:

```js
const number = Number(value);
```

## Örnek 29

ile query değerini sayıya çevirmeye çalışıyoruz. Ardından `Number.isFinite(number)` ile bu dönüşümün kullanılabilir bir sayı üretip üretmediğini kontrol ediyoruz.

Burada `Number.isFinite()` ile birazdan `year` filtresinde kullanacağımız `Number.isSafeInteger()` aynı işi yapmaz:

```text
Number.isFinite()
↓
Değer geçerli ve sonlu bir sayı mı?
↓
8.3 gibi ondalıklı sayıları da kabul eder.

Number.isSafeInteger()
↓
Değer güvenli bir tam sayı mı?
↓
8.3 gibi ondalıklı sayıları kabul etmez.
```

## Örnek 30

Bu ayrım filtrelerimiz için önemlidir. `voteAverage` değeri `8.3` olabilir; ama `releaseYear` için `2021.5` gibi bir değer istemeyiz.

Fonksiyon bu nedenle sayı olarak kullanamayacağımız değerlerde:

```js
null
```

## Örnek 31

döndürür.

Örneğin `"abc"` sayıya çevrildiğinde `NaN` oluşur ve `Number.isFinite(NaN)` sonucu `false` olduğu için değer reddedilir.

Buradaki:

```js
value.trim() === ""
```

## Örnek 32

kontrolü özellikle boş String'i reddeder.

Çünkü:

```js
Number("");
```

## Örnek 33

sonucu JavaScript'te `0` olabilir.

Biz boş query değerini gerçek bir `0` değeri gibi kabul etmek istemiyoruz.

---

## `year` Filtresini Oluşturalım

Şimdi asıl yardımcı fonksiyonumuza başlayalım:

```js
export const buildMovieFilter = (query) => {
  const filter = {};

  if (query.year !== undefined) {
    const year = parseFilterNumber(query.year);

    if (
      !Number.isSafeInteger(year) ||
      year < 1888
    ) {
      return {
        filter: null,
        error:
          "year 1888 veya daha büyük tam sayı olmalıdır.",
      };
    }

    filter.releaseYear = year;
  }

  return {
    filter,
    error: null,
  };
};
```

## Örnek 34

Burada önemli bir tasarım kararı var: `buildMovieFilter()` yalnızca filtreyi değil, olası hata bilgisini de aynı nesnenin içinde döndürüyor. Böylece fonksiyonu kullanan Controller her durumda aynı iki alanı bekleyebilir:

```text
başarılıysa
filter → kullanılabilir filtre
error  → null

başarısızsa
filter → null
error  → kullanıcıya gönderilecek hata mesajı
```

## Örnek 35

Yani `error: null`, “bir hata oluştu” anlamına gelmez. Tam tersine, **bu filtre hazırlanırken hata oluşmadı** demektir. Teknik olarak bu alanı hiç döndürmemek de mümkün olurdu; burada dönüş yapısını tutarlı tutmak için bilinçli olarak `null` kullanıyoruz.

Burada önce:

```js
const filter = {};
```

## Örnek 36

oluşturuyoruz.

Kullanıcı `year` göndermediyse boş filtre olduğu gibi kalır.

Ama:

```http
GET /movies?year=2021
```

## Örnek 37

geldiyse:

```js
filter.releaseYear = year;
```

## Örnek 38

sonrasında filtre:

```js
{
  releaseYear: 2021,
}
```

## Örnek 39

olur.

Az önce `Number.isFinite()` ile farkını gördüğümüz `Number.isSafeInteger(year)` burada özellikle yıl için kullanılıyor. Böylece `2021` kabul edilirken `2021.5` gibi ondalıklı bir değer reddedilir.

`1888` alt sınırını da 40. bölümdeki Movie Schema kuralımızla tutarlı şekilde kullanıyoruz.

---

## Peki “Puanı En Az 8 Olanlar” Nasıl Seçilir?

Kullanıcı bu kez şöyle desin:

> **Puanı en az 8 olan filmleri görmek istiyorum.**

Burada yalnızca:

```text
voteAverage = 8
```

## Örnek 40

demek yeterli değildir.

Çünkü:

```text
8.0
8.3
8.7
9.0
```

## Örnek 41

değerlerinin tamamı koşula uymalıdır.

45. bölümde index anlatımı sırasında:

```js
$gte
```

## Örnek 42

operatörüyle tanışmıştık.

`$gte`:

```text
greater than or equal
↓
büyük veya eşit
```

## Örnek 43

anlamına gelir.

Şu filtre:

```js
{
  voteAverage: {
    $gte: 8,
  },
}
```

## Örnek 44

şunu söyler:

> `voteAverage` değeri 8'e eşit veya 8'den büyük olan Document'ları seç.

| voteAverage | Sonuca girer mi? |
|---:|---|
| 7.9 | Hayır |
| 8.0 | Evet |
| 8.3 | Evet |
| 9.0 | Evet |

`$gte`, JavaScript operatörü değildir.

MongoDB'nin sorgu operatörlerinden biridir ve veriyi değiştirmez.

---

## Minimum Puanı da Yardımcı Fonksiyona Ekleyelim

Query parameter adımız:

```text
minVoteAverage
```

## Örnek 45

olsun.

Örneğin:

```http
GET /movies?minVoteAverage=8
```

## Örnek 46

veya:

```http
GET /movies?year=2021&minVoteAverage=8
```

## Örnek 47

gönderilebilir.

`buildMovieFilter()` içine ikinci koşulu ekleyelim:

```js
if (query.minVoteAverage !== undefined) {
  const minVoteAverage = parseFilterNumber(query.minVoteAverage);

  if (
    minVoteAverage === null ||
    minVoteAverage < 0 ||
    minVoteAverage > 10
  ) {
    return {
      filter: null,
      error:
        "minVoteAverage 0 ile 10 arasında sayı olmalıdır.",
    };
  }

  filter.voteAverage = {
    $gte: minVoteAverage,
  };
}
```

## Örnek 48

Burada puanın tam sayı olmasını istemiyoruz.

Çünkü:

```text
8.3
8.75
```

## Örnek 49

gibi değerler geçerlidir. Bu nedenle ilk başta hazırladığımız `parseFilterNumber()` fonksiyonunun `Number.isFinite()` kontrolü bu filtre için uygundur; sayı geçerli olduğu sürece ondalıklı olmasına izin verir.

Ardından Movie Schema'mızdaki puan aralığıyla uyumlu olarak:

```text
0 ile 10
```

## Örnek 50

sınırını uyguluyoruz.

---

## `buildMovieFilter.js` Dosyasının Tam Hâli

```text
src/utils/buildMovieFilter.js
```

## Örnek 51

dosyamız:

```js
const parseFilterNumber = (value) => {
  if (
    typeof value !== "string" ||
    value.trim() === ""
  ) {
    return null;
  }

  const number = Number(value);

  if (!Number.isFinite(number)) {
    return null;
  }

  return number;
};

export const buildMovieFilter = (query) => {
  const filter = {};

  if (query.year !== undefined) {
    const year = parseFilterNumber(query.year);

    if (
      !Number.isSafeInteger(year) ||
      year < 1888
    ) {
      return {
        filter: null,
        error:
          "year 1888 veya daha büyük tam sayı olmalıdır.",
      };
    }

    filter.releaseYear = year;
  }

  if (query.minVoteAverage !== undefined) {
    const minVoteAverage = parseFilterNumber(query.minVoteAverage);

    if (
      minVoteAverage === null ||
      minVoteAverage < 0 ||
      minVoteAverage > 10
    ) {
      return {
        filter: null,
        error:
          "minVoteAverage 0 ile 10 arasında sayı olmalıdır.",
      };
    }

    filter.voteAverage = {
      $gte: minVoteAverage,
    };
  }

  return {
    filter,
    error: null,
  };
};
```

## Örnek 52

İlk `year` örneğinde belirlediğimiz dönüş sözleşmesini burada da koruyoruz: `filter` Mongoose sorgularında kullanılacak koşulları taşır; `error` ise yalnızca filtre değeri geçersizse bir hata mesajı içerir. Geçerli Request'te `error` yine `null` olur.

---

## Filtre Nesnesi Hangi Hâllere Gelebilir?

Hiç filtre gönderilmezse:

```http
GET /movies
```

## Örnek 53

sonuç:

```js
{}
```

## Örnek 54

olur.

Yalnızca yıl:

```http
GET /movies?year=2021
```

## Örnek 55

için:

```js
{
  releaseYear: 2021,
}
```

## Örnek 56

oluşur.

Yalnızca minimum puan:

```http
GET /movies?minVoteAverage=8
```

## Örnek 57

için:

```js
{
  voteAverage: {
    $gte: 8,
  },
}
```

## Örnek 58

oluşur.

İkisi birlikte:

```http
GET /movies?year=2021&minVoteAverage=8
```

## Örnek 59

için:

```js
{
  releaseYear: 2021,
  voteAverage: {
    $gte: 8,
  },
}
```

## Örnek 60

oluşur.

Aynı filtre nesnesindeki bu iki farklı alan koşulunun ikisinin de sağlanması gerekir:

```text
releaseYear = 2021
VE
voteAverage >= 8
```

## Örnek 61

---

## `countDocuments()` da Aynı Filtreyi Kullanmalı

49. bölümde:

```js
await Movie.countDocuments({});
```

## Örnek 62

ile bütün filmleri sayıyorduk.

Ama kullanıcı:

```http
GET /movies?year=2021
```

## Örnek 63

gönderdiyse artık bütün collection'ın toplamını istemiyoruz.

Filtreye uyan toplamı istiyoruz.

Yanlış yaklaşım:

```js
const totalItems = await Movie.countDocuments({});

const movies = await Movie.find(filter);
```

## Örnek 64

Burada `totalItems` bütün filmleri, `movies` ise filtrelenmiş filmleri temsil eder.

Doğru yaklaşımda aynı `filter` nesnesini iki ayrı Query'ye veririz:

```js
const countQuery = Movie.countDocuments(filter);

const moviesQuery = Movie.find(filter);
```

## Örnek 65

Böylece:

```text
filter
↓
aynı koşullar

countQuery
↓
filtreye uyan kaç film var?

moviesQuery
↓
filtreye uyan hangi filmler var?
```

## Örnek 66

sorularını aynı filtre üzerinden hazırlarız.

49. bölümde öğrendiğimiz gibi Query henüz sonuç değildir. Sonuçları gerektiği yerde `exec()` ile alacağız.

---

## Controller'da Önceki Yardımcıları Yeniden Kullanıyoruz

Artık görevlerimiz daha açık ayrılıyor:

```text
parsePaginationParams()
↓
page ve limit

buildMovieFilter()
↓
iki Query'nin de kullanacağı filter

countDocuments(filter)
↓
sayım Query'si

find(filter)
↓
liste Query'si

calculatePaginationData()
↓
pagination metadata
```

## Örnek 67

49. bölümde öğrendiğimiz Query mantığını bırakmıyoruz. Bu bölümde yalnızca Query'lerin başlangıç koşulunu artık boş `{}` yerine `filter` nesnesinden alıyoruz.

Çalıştığımız dosya:

```text
src/controllers/movies.js
```

## Örnek 68

içinde ilgili import'lar:

```js
import {
  parsePaginationParams,
} from "../utils/parsePaginationParams.js";

import {
  calculatePaginationData,
} from "../utils/calculatePaginationData.js";

import {
  buildMovieFilter,
} from "../utils/buildMovieFilter.js";
```

## Örnek 69

olur.

---

## `getMovies` Controller'ının Güncel Hâli

```js
import {
  parsePaginationParams,
} from "../utils/parsePaginationParams.js";

import {
  calculatePaginationData,
} from "../utils/calculatePaginationData.js";

import {
  buildMovieFilter,
} from "../utils/buildMovieFilter.js";

export const getMovies = async (req, res) => {
  const {
    page,
    limit,
  } = parsePaginationParams(req.query);

  const {
    filter,
    error,
  } = buildMovieFilter(req.query);

  if (error !== null) {
    return res.status(400).json({
      message: error,
    });
  }

  const skip = (page - 1) * limit;

  const countQuery = Movie.countDocuments(filter);

  const moviesQuery = Movie.find(filter)
    .sort({
      voteAverage: -1,
      releaseYear: -1,
      _id: 1,
    })
    .skip(skip)
    .limit(limit);

  const totalItems = await countQuery.exec();

  const movies = await moviesQuery.exec();

  const {
    totalPages,
    hasNext,
    hasPrevious,
  } = calculatePaginationData(totalItems, limit, page);

  res.status(200).json({
    message: "Filmler getirildi.",
    page,
    limit,
    totalItems,
    totalPages,
    hasNext,
    hasPrevious,
    data: movies,
  });
};
```

## Örnek 70

Dikkat edersek 48 ve 49. bölümlerde oluşturduğumuz yapı bozulmadı.

Bu bölümde yeni olan şey, iki Query'nin de aynı `filter` nesnesinden başlamasıdır:

```text
filter
↙          ↘
countQuery  moviesQuery
```

## Örnek 71

Sonra 49. bölümdeki gibi:

```text
Query'leri hazırla
↓
exec() ile çalıştır
↓
sonuçları al
```

## Örnek 72

akışı devam eder.

Yani filtering, Query mantığının yerine geçmedi. Query'lere **hangi kayıtların dahil olacağını** ekledi.

---

## Postman ile Deneyelim

Yalnızca yıl:

```http
GET http://localhost:3000/movies?year=2021
```

## Örnek 73

Yalnızca minimum puan:

```http
GET http://localhost:3000/movies?minVoteAverage=8
```

## Örnek 74

İkisi birlikte:

```http
GET http://localhost:3000/movies?year=2021&minVoteAverage=8
```

## Örnek 75

Pagination ile birlikte:

```http
GET http://localhost:3000/movies?year=2021&minVoteAverage=8&page=2&limit=10
```

## Örnek 76

kullanılabilir.

Filtre parametreleri değişse bile pagination yardımcılarımız aynı şekilde çalışmaya devam eder.

---

## Filtreye Uyan Film Yoksa 404 mü?

Hayır.

Geçerli bir filtre gönderilmiş fakat hiçbir Movie Document koşula uymamış olabilir.

Bu durumda:

```text
totalItems = 0
totalPages = 0
hasNext = false
hasPrevious = false
data = []
```

## Örnek 77

dönebilir.

Liste Route'u vardır ve Request geçerlidir.

Yalnızca filtre sonucunda kayıt bulunmamıştır.

Bu nedenle boş listeyi hata olarak değerlendirmiyoruz.

---

## Geçersiz Filtre Değeri Ne Olur?

Örneğin:

```http
GET /movies?year=abc
```

## Örnek 78

gönderilirse:

```js
buildMovieFilter()
```

## Örnek 79

geçerli bir filtre oluşturamaz.

Controller:

```http
400 Bad Request
```

## Örnek 80

döndürür.

Aynı şekilde:

```http
GET /movies?minVoteAverage=11
```

## Örnek 81

0–10 aralığının dışındadır ve reddedilir.

Burada pagination ile filtering için farklı kararlar aldığımızı fark edin.

48. bölümde geçersiz pagination değerinde:

```text
default değere dön
```

## Örnek 82

kararı vermiştik.

Bu bölümde geçersiz filtre değerinde:

```text
400 Bad Request
```

## Örnek 83

dönüyoruz.

Her query parameter'ın aynı davranışı kullanması gerekmez.

---

## Neden `Movie.find(req.query)` Yazmıyoruz?

Şöyle bir kullanım kısa görünebilir:

```js
Movie.find(req.query);
```

## Örnek 84

Ama kullanmayacağız.

Çünkü:

```http
GET /movies?year=2021&minVoteAverage=8&page=2&limit=10
```

## Örnek 85

Request'inde `req.query` içinde:

```text
year
minVoteAverage
page
limit
```

## Örnek 86

bulunabilir.

Bunların hepsi Movie alanı değildir.

Ayrıca `req.query` kullanıcı tarafından gönderilen veridir.

Biz yalnızca desteklediğimiz alanları okuyup kendi:

```js
filter
```

## Örnek 87

nesnemizi oluşturuyoruz.

Böylece veritabanına hangi koşulların ulaşacağını uygulamamız kontrol eder.

---

## 🧠 Akılda Kalsın

Filtering:

```text
hangi Document'lar sorgu sonucuna girecek?
```

## Örnek 88

sorusunu cevaplar.

Eşitlik filtresi:

```js
{
  releaseYear: 2021,
}
```

## Örnek 89

Minimum puan filtresi:

```js
{
  voteAverage: {
    $gte: 8,
  },
}
```

## Örnek 90

şeklindedir.

Bu bölümde:

```text
src/utils/buildMovieFilter.js
```

## Örnek 91

oluşturduk.

Böylece görevlerimiz artık:

```text
parsePaginationParams()
↓
page + limit

buildMovieFilter()
↓
filter

calculatePaginationData()
↓
pagination metadata
```

## Örnek 92

şeklinde ayrıldı.

En önemli bağlantı, aynı filtreyi iki ayrı Query'de kullanmamızdır:

```js
const countQuery = Movie.countDocuments(filter);

const moviesQuery = Movie.find(filter);
```

## Örnek 93

Ardından Query'leri 49. bölümde öğrendiğimiz şekilde:

```js
const totalItems = await countQuery.exec();

const movies = await moviesQuery.exec();
```

## Örnek 94

ile çalıştırırız.

---

## Sonraki Adım

Artık kullanıcı:

```text
yıl
minimum puan
```

## Örnek 95

gibi bilinen alanlarla film listesini daraltabiliyor.

Ama kullanıcı bazen yalnızca film adının bir kısmını hatırlar:

> **Adında “Dune” geçen filmleri bul.**

Bu kez:

```js
{
  title: "Dune",
}
```

## Örnek 96

gibi tam eşitlik filtresi yeterli olmayabilir.

51. bölümde MongoDB'nin:

```js
$regex
```

## Örnek 97

sorgu operatörünü kullanarak film adına göre arama yapacağız.

Üstelik 50. bölümde oluşturduğumuz:

```text
buildMovieFilter()
```
