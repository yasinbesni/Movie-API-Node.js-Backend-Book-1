# Bölüm 51 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Searching ile Film Adına Göre Arama Nasıl Yapılır?

## Dune Yazınca Hangi Filmler Bulunmalı?

Film listemizi yıla ve puana göre daraltabiliyoruz. Ancak bir kullanıcı belirli bir filmi aradığında bu bilgiler her zaman yeterli olmaz.

Örneğin kullanıcı arama kutusuna **Dune** yazıyor. Yalnızca adı tam olarak “Dune” olan filmi değil, **Dune: Part Two** gibi bu kelimenin geçtiği diğer filmleri de görmek istiyor. Basit bir tam eşitlik kontrolü bu beklentiyi karşılamaz.

50. bölümde oluşturduğumuz `buildMovieFilter()` zaten hangi filmlerin listeleneceğini belirliyor. Şimdi aynı yapıya film adını da dahil edeceğiz.

Önce kullanıcının yazdığı arama metnini Request ile nasıl göndereceğimizi görelim.

---

## Arama Parametremiz `q` Olsun

Film adına göre arama için:

```text
q
```

## Örnek 2

adında bir query parameter kullanalım.

Örneğin:

```http
GET /movies?q=Dune
```

## Örnek 3

Buradaki:

```text
q
```

## Örnek 4

genellikle İngilizce *query* kelimesinin kısa kullanımıdır.

Express tarafında:

```js
req.query.q
```

## Örnek 5

ile değere ulaşabiliriz.

Ama 50. bölümde Controller'ın filtre ayrıntılarını doğrudan yönetmemesi için:

```text
buildMovieFilter()
```

## Örnek 6

yardımcısını oluşturmuştuk.

Aynı yapıyı devam ettireceğiz.

Yani Controller'a yeni bir arama bloğu eklemek yerine `buildMovieFilter()` fonksiyonunu genişleteceğiz.

---

## Tam Eşitlik Neden Yeterli Değil?

Şu filtre:

```js
{
  title: "Dune",
}
```

## Örnek 7

yalnızca:

```text
Dune
```

## Örnek 8

başlığıyla eşleşir.

Ama:

```text
Dune: Part Two
```

## Örnek 9

içinde de `"Dune"` kelimesi bulunur.

Bizim istediğimiz:

```text
title içinde
"Dune" metni geçiyor mu?
```

## Örnek 10

sorusunu sorabilmek.

MongoDB bu tür metin eşleşmeleri için:

```js
$regex
```

## Örnek 11

sorgu operatörünü kullanabilir.

---

## `$regex` Nedir?

`$regex`, MongoDB sorgularında String alanlarını bir **regular expression**, yani düzenli ifade deseniyle eşleştirmemizi sağlayan sorgu operatörüdür.

Basit bir örnek:

```js
{
  title: {
    $regex: "Dune",
  },
}
```

## Örnek 12

şunu söyler:

> `title` alanı `"Dune"` desenine uyan Document'ları seç.

Desenin başına veya sonuna özel bir sınır koymadığımız için `"Dune"` ifadesi başlığın herhangi bir yerinde bulunabilir.

Bu nedenle:

```text
Dune
Dune: Part Two
The Making of Dune
```

## Örnek 13

gibi başlıklar eşleşebilir.

Burada `$regex` veriyi değiştirmez.

> **Not:** Bu bölümde *searching* sözcüğünü Movie API'mizde film başlığı içinde metin arama davranışı için kullanıyoruz. Burada MongoDB'nin ayrı arama servislerini değil, `$regex` sorgu operatörünü kullanıyoruz.

50. bölümdeki `$gte` gibi bu da bir **sorgu operatörüdür**:

```text
$gte
↓
sayısal karşılaştırma

$regex
↓
metin deseniyle eşleştirme
```

## Örnek 14

---

## Büyük ve Küçük Harf Sorunu

Kullanıcı:

```http
GET /movies?q=dune
```

## Örnek 15

gönderebilir.

Veritabanındaki başlık ise:

```text
Dune
```

## Örnek 16

olabilir.

Aramamızın büyük-küçük harf duyarsız çalışmasını istiyorsak `$regex` yanında:

```js
$options: "i"
```

## Örnek 17

kullanabiliriz.

Örneğin:

```js
{
  title: {
    $regex: "Dune",
    $options: "i",
  },
}
```

## Örnek 18

Buradaki:

```text
i
↓
case-insensitive
↓
büyük-küçük harf duyarsız
```

## Örnek 19

anlamına gelir.

Böylece `"dune"`, `"Dune"` veya `"DUNE"` gibi yazımlar eşleşebilir.

---

## Kullanıcı Metnini Doğrudan `$regex` İçine Vermeli miyiz?

Şöyle yazmak çok kolay görünür:

```js
filter.title = {
  $regex: query.q,
  $options: "i",
};
```

## Örnek 20

Ama kullanıcı tarafından gönderilen bazı karakterlerin regular expression içinde özel anlamları vardır.

Örneğin:

```text
.
*
+
?
(
)
[
]
```

## Örnek 21

gibi karakterler yalnızca normal metin karakteri olarak değerlendirilmez.

Kullanıcı film adında gerçekten:

```text
Dune (2021)
```

## Örnek 22

aramak istiyorsa parantezlerin regular expression komutu gibi yorumlanmasını istemeyiz.

Bizim ihtiyacımız:

> Kullanıcının yazdığı metni normal metin olarak ara.

Bu nedenle arama değerindeki özel regex karakterlerini **escape**, yani özel anlamını etkisiz hâle getireceğiz.

---

## `escapeRegex()` Yardımcı Fonksiyonunu Oluşturalım

Bunu anlaşılır bir JavaScript akışıyla yapalım.

Önce özel karakterleri bir Array içinde tutalım:

```js
const regexSpecialChars = [
  "\\",
  ".",
  "*",
  "+",
  "?",
  "^",
  "$",
  "{",
  "}",
  "(",
  ")",
  "|",
  "[",
  "]",
];
```

## Örnek 23

Sonra:

```js
const escapeRegex = (value) => {
  let escapedValue = "";

  for (const char of value) {
    if (regexSpecialChars.includes(char)) {
      escapedValue += "\\";
    }

    escapedValue += char;
  }

  return escapedValue;
};
```

## Örnek 24

Akışı okuyalım.

`for...of` ile String içindeki karakterleri tek tek geziyoruz.

Örneğin:

```text
D
u
n
e
(
2
0
2
1
)
```

## Örnek 25

Her karakter için:

```js
regexSpecialChars.includes(char)
```

## Örnek 26

ile:

> Bu karakter regex açısından özel mi?

diye soruyoruz.

Normal karakterse olduğu gibi ekliyoruz.

Özel karakterse önce:

```text
\
```

## Örnek 27

ekleyip sonra karakterin kendisini ekliyoruz.

Örneğin:

```text
(
↓
\(
```

## Örnek 28

hâline gelir.

Böylece parantez regex komutu olarak değil, normal `(` karakteri olarak aranabilir.

Bu fonksiyon MongoDB sorgusu çalıştırmaz.

Yalnızca kullanıcı metnini `$regex` içinde daha güvenli kullanabileceğimiz hâle getirir.

---

## Arama Metnini Önce Kontrol Edelim

Kullanıcı:

```http
GET /movies?q=
```

## Örnek 29

gibi boş bir arama gönderebilir.

Boş aramayı bütün başlıklarla eşleşen anlamsız bir sorguya çevirmek istemiyoruz.

Bu nedenle:

```js
const search = query.q.trim();
```

## Örnek 30

sonrasında:

```js
if (
  search === "" ||
  search.length > 50
) {
```

## Örnek 31

kontrolünü kullanacağız.

Burada `50` MongoDB'nin zorunlu sınırı değildir.

Bizim API'mizin:

> Film arama metni en fazla 50 karakter olsun.

kararıdır.

Amaç hem API sözleşmesini açık tutmak hem de gereksiz derecede büyük arama desenlerini kabul etmemektir.

---

## `buildMovieFilter()` Fonksiyonunu Arama İçin Genişletelim

50. bölümde bu yardımcı fonksiyon zaten:

```text
year
minVoteAverage
```

## Örnek 32

filtrelerini oluşturuyordu.

Şimdi sonuna `q` desteğini ekleyeceğiz:

```js
if (query.q !== undefined) {
  if (typeof query.q !== "string") {
    return {
      filter: null,
      error:
        "q geçerli bir metin olmalıdır.",
    };
  }

  const search = query.q.trim();

  if (
    search === "" ||
    search.length > 50
  ) {
    return {
      filter: null,
      error:
        "q 1 ile 50 karakter arasında olmalıdır.",
    };
  }

  filter.title = {
    $regex: escapeRegex(search),
    $options: "i",
  };
}
```

## Örnek 33

Örneğin:

```http
GET /movies?q=Dune
```

## Örnek 34

geldiğinde filtreye:

```js
{
  title: {
    $regex: "Dune",
    $options: "i",
  },
}
```

## Örnek 35

eklenir.

---

## Güncel `buildMovieFilter.js`

Dosyamız:

```text
src/utils/buildMovieFilter.js
```

## Örnek 36

artık şöyle olur:

```js
const regexSpecialChars = [
  "\\",
  ".",
  "*",
  "+",
  "?",
  "^",
  "$",
  "{",
  "}",
  "(",
  ")",
  "|",
  "[",
  "]",
];

const escapeRegex = (value) => {
  let escapedValue = "";

  for (const char of value) {
    if (regexSpecialChars.includes(char)) {
      escapedValue += "\\";
    }

    escapedValue += char;
  }

  return escapedValue;
};

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

  if (query.q !== undefined) {
    if (typeof query.q !== "string") {
      return {
        filter: null,
        error:
          "q geçerli bir metin olmalıdır.",
      };
    }

    const search = query.q.trim();

    if (
      search === "" ||
      search.length > 50
    ) {
      return {
        filter: null,
        error:
          "q 1 ile 50 karakter arasında olmalıdır.",
      };
    }

    filter.title = {
      $regex: escapeRegex(search),
      $options: "i",
    };
  }

  return {
    filter,
    error: null,
  };
};
```

## Örnek 37

Burada önemli nokta şudur:

> 50. bölümdeki filtering yapısını silmedik; yalnızca yeni bir arama koşuluyla genişlettik.

---

## Controller Değişiyor mu?

Hayır.

50. bölümde Controller zaten:

```js
const {
  filter,
  error,
} = buildMovieFilter(req.query);
```

## Örnek 38

kullanıyordu.

`buildMovieFilter()` artık `q` değerini de bildiği için Controller'ın yeni arama ayrıntısını öğrenmesine gerek yok.

49 ve 50. bölümlerde kurduğumuz Query akışı aynı şekilde devam eder:

```js
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
```

## Örnek 39

Burada değişen şey Controller'ın yapısı değildir.

Değişen yalnızca `filter` nesnesinin artık gerektiğinde şu koşulu da taşıyabilmesidir:

```js
{
  title: {
    $regex: "Dune",
    $options: "i",
  },
}
```

## Örnek 40

Bu önemli bir mimari kazanımdır:

```text
Yeni arama davranışı eklendi.
↓
Controller büyümedi.
↓
buildMovieFilter() genişledi.
```

## Örnek 41

---

## Search ile Pagination Birlikte Çalışır mı?

Evet.

Örneğin:

```http
GET /movies?q=Dune&page=2&limit=10
```

## Örnek 42

gönderilebilir.

`buildMovieFilter()`:

```js
{
  title: {
    $regex: "Dune",
    $options: "i",
  },
}
```

## Örnek 43

oluşturur.

Sonra:

```js
Movie.countDocuments(filter);
```

## Örnek 44

yalnızca aramaya uyan filmleri sayar.

```js
Movie.find(filter);
```

## Örnek 45

de yalnızca aynı aramaya uyan filmleri getirir.

Pagination da bu arama sonucunun üzerinde çalışır.

---

## Search ile Filtering Birlikte Çalışır mı?

Evet.

Örneğin:

```http
GET /movies?q=Dune&year=2021&minVoteAverage=8
```

## Örnek 46

Request'inde filtre yaklaşık olarak:

```js
{
  releaseYear: 2021,
  voteAverage: {
    $gte: 8,
  },
  title: {
    $regex: "Dune",
    $options: "i",
  },
}
```

## Örnek 47

olur.

MongoDB aynı filtre nesnesindeki bütün bu farklı alan koşullarını birlikte değerlendirir:

```text
releaseYear = 2021
VE
voteAverage >= 8
VE
title içinde "Dune" geçiyor
```

## Örnek 48

Böylece searching, filtering sistemimizin ayrı bir rakibi değil; aynı filtre nesnesinin yeni bir koşuludur.

---

## Postman ile Deneyelim

Önce yalnızca arama:

```http
GET http://localhost:3000/movies?q=Dune
```

## Örnek 49

Sonra küçük harfle:

```http
GET http://localhost:3000/movies?q=dune
```

## Örnek 50

`$options: "i"` kullandığımız için büyük-küçük harf farkının aramayı engellememesini bekleriz.

Sonra filtreyle birlikte:

```http
GET http://localhost:3000/movies?q=Dune&year=2021
```

## Örnek 51

ve pagination ile:

```http
GET http://localhost:3000/movies?q=Dune&page=2&limit=5
```

## Örnek 52

deneyebiliriz.

Gerçek sonuçlar kendi MongoDB verilerinize bağlıdır.

---

## Aramaya Uyan Film Yoksa

Şu Request geçerli olabilir:

```http
GET /movies?q=BulunmayanBirFilm
```

## Örnek 53

Eşleşen Document yoksa:

```text
totalItems = 0
totalPages = 0
data = []
```

## Örnek 54

döner.

Bu yine `404 Not Found` olmak zorunda değildir.

Route vardır, arama parametresi geçerlidir; yalnızca sonuç bulunmamıştır.

---

## `$regex` ile Index Konusunda Küçük Bir Not

45. bölümde index'lerin sorgu performansına yardımcı olabileceğini öğrenmiştik.

Ancak:

```text
başlığın herhangi bir yerinde
büyük-küçük harf duyarsız
metin araması
```

## Örnek 55

gibi regex sorguları büyük veri kümelerinde daha fazla işlem gerektirebilir ve yavaşlayabilir.

Bu bölümde amacımız Movie API'mize temel bir başlık araması kazandırmaktır.

Çok büyük uygulamalarda gelişmiş metin araması için özel search çözümleri gerekebilir.

Şimdilik:

```text
doğru arama davranışını kur
↓
ölç
↓
gerektiğinde daha gelişmiş çözüm seç
```

## Örnek 56

yaklaşımını koruyoruz.

---

## 🧠 Akılda Kalsın

Tam eşitlik:

```js
{
  title: "Dune",
}
```

## Örnek 57

yalnızca tam değeri arar.

Metin içinde eşleşme için:

```js
{
  title: {
    $regex: "Dune",
    $options: "i",
  },
}
```

## Örnek 58

kullanabiliriz.

Burada:

```text
$regex
↓
String alanında desen eşleşmesi

$options: "i"
↓
büyük-küçük harf duyarsız arama
```

## Örnek 59

anlamına gelir.

Kullanıcı metnini doğrudan regex deseni olarak bırakmadık.

`escapeRegex()` ile özel karakterlerin regex komutu gibi yorumlanmasını engelledik.

En önemlisi, 50. bölümde oluşturduğumuz:

```js
buildMovieFilter()
```

## Örnek 60

yapısını yeniden kullandık.

Bu yüzden:

```text
pagination
filtering
searching
```

## Örnek 61

aynı `getMovies` akışında birlikte çalışabiliyor.

---

## Sonraki Adım

Artık kullanıcı:

```text
yıl
minimum puan
film adı
```

## Örnek 62

üzerinden listeyi daraltabiliyor.

Fakat Response'ta hâlâ Movie Document'ın bütün alanlarını gönderiyoruz.

Kullanıcıya her zaman:

```text
_id
title
releaseYear
voteAverage
createdAt
updatedAt
__v
```

## Örnek 63

alanlarının tamamı gerekli mi?

Bir sonraki bölümde:

```text
projection
select()
```
