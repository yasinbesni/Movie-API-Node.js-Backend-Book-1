# Bölüm 49 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# countDocuments() ile Toplam Kayıt Sayısını Nasıl Bulur ve Pagination Bilgilerini Response'a Nasıl Ekleriz?

## Sonraki Sayfa Var mı?

Artık kullanıcı Movie API'mizden istediği sayfayı isteyebiliyor:

```http
GET /movies?page=3&limit=10
```

## Örnek 2

48. bölümde `req.query` içinden gelen bu değerleri `parsePaginationParams()` ile hazırlamıştık. Ardından `skip()` ve `limit()` ile o sayfanın filmlerini getiriyoruz.

Ama listeyi kullanan kişinin bilmediği bir şey var: **Üçüncü sayfadan sonra başka film kaldı mı?**

Diyelim ki ekranda on film görünüyor. Bu, veritabanında yalnızca on film olduğu anlamına gelmez. Belki onuncu filmin ardından onlarca film daha var; belki de listenin sonuna geldik.

Sadece o sayfadaki filmleri göndermek, bu soruyu cevaplamıyor.

Listeyi kullanışlı hâle getirmek için toplam kaç film olduğunu, buradan da kaç sayfa oluştuğunu bulmalıyız.

---

## `movies.length` Neden Yeterli Değil?

Şu sorguyu düşünelim:

```js
const movies = await Movie.find({})
  .sort({
    voteAverage: -1,
    releaseYear: -1,
    _id: 1,
  })
  .skip(skip)
  .limit(limit);
```

## Örnek 3

`limit` değeri `10` ise `movies` dizisinde en fazla 10 film bulunur.

Ama collection'da:

```text
93 film
```

## Örnek 4

olabilir.

Bu nedenle:

```js
movies.length
```

## Örnek 5

yalnızca **o sayfada kaç kayıt geldiğini** söyler.

Pagination için ihtiyacımız ise:

```text
bütün listenin toplam kayıt sayısı
```

## Örnek 6

bilgisidir.

Bu nedenle ayrı bir sayım sorgusu gerekir.

---

## `countDocuments()` Ne Yapar?

Mongoose'taki:

```js
countDocuments()
```

## Örnek 7

belirlediğimiz koşula uyan Document'ların sayısını bulur.

Şimdilik herhangi bir filtre kullanmadığımız için:

```js
Movie.countDocuments({});
```

## Örnek 8

yazabiliriz.

Buradaki:

```js
{}
```

## Örnek 9

boş filtre nesnesidir.

Yani:

```text
belirli bir koşul yok
↓
bütün Movie Document'larını say
```

## Örnek 10

demektir.

Collection'da 93 Movie Document varsa sonuç:

```js
93
```

## Örnek 11

olacaktır.

Fakat burada önemli bir ayrıntı var.

`Movie.countDocuments({})` yazdığımız anda elimizde henüz doğrudan `93` sayısı bulunmaz.

Mongoose önce bir **Query** oluşturur.

Peki neden?

---

## Mongoose Query'ye Neden İhtiyaç Duyuyoruz?

Bu kavramı tanımdan değil, ihtiyaçtan çıkaralım.

Şu sorguyu daha önce kullanmıştık:

```js
Movie.find({})
  .sort({
    voteAverage: -1,
  })
  .skip(20)
  .limit(10);
```

## Örnek 12

Burada tek bir işlem yazıyor gibi görünsek de sorguyu adım adım hazırlıyoruz:

```text
Movie.find({})
↓
hangi kayıtlar?

.sort(...)
↓
hangi sırada?

.skip(20)
↓
kaç kayıt geçilecek?

.limit(10)
↓
en fazla kaç kayıt getirilecek?
```

## Örnek 13

Mongoose'un bütün bu bilgileri, sorgu çalışmadan önce bir yerde tutması gerekir.

İşte **Query nesnesi** bunun için vardır.

Query'yi şöyle düşünebiliriz:

> **Veritabanında yapılacak işlemi ve bu işlem çalışmadan önce eklediğimiz sorgu ayarlarını taşıyan Mongoose nesnesi.**

Yani Query, sorgunun sonucu değildir.

Query:

```text
henüz çalıştırılmamış sorgunun hazırlanmış hâli
```

## Örnek 14

gibi düşünülebilir.

---

## Query'yi Gerçekten Kullanalım

Zincirleme yazmak yerine önce Query'yi bir değişkende tutalım:

```js
const moviesQuery = Movie.find({});
```

## Örnek 15

Bu noktada:

```js
moviesQuery
```

## Örnek 16

bir film dizisi değildir.

Mongoose Query nesnesidir.

Şimdi sorguya yeni ayarlar ekleyebiliriz:

```js
moviesQuery.sort({
  voteAverage: -1,
  releaseYear: -1,
  _id: 1,
});

moviesQuery.skip(skip);

moviesQuery.limit(limit);
```

## Örnek 17

Buradaki önemli fikir:

```text
Query oluşturuldu
↓
henüz sonuç alınmadı
↓
Query geliştirildi
↓
sort eklendi
↓
skip eklendi
↓
limit eklendi
```

## Örnek 18

İstersek aynı şeyi zincirleme biçimde de yazabiliriz:

```js
const moviesQuery = Movie.find({})
  .sort({
    voteAverage: -1,
    releaseYear: -1,
    _id: 1,
  })
  .skip(skip)
  .limit(limit);
```

## Örnek 19

İki yazımın da oluşturduğu şey:

```text
hazırlanmış bir Mongoose Query
```

## Örnek 20

olur.

---

## Hazırladığımız Query Ne Zaman Çalışacak?

Artık sorgumuz hazır.

Sonucu almak için:

```js
const movies = await moviesQuery.exec();
```

## Örnek 21

yazabiliriz.

Buradaki:

```js
.exec()
```

## Örnek 22

Mongoose Query'yi çalıştırır.

Akış:

```text
Movie.find({})
↓
Query oluştur

.sort()
.skip()
.limit()
↓
Query'yi hazırla

.exec()
↓
Query'yi çalıştır

await
↓
sonucun gelmesini bekle

movies
↓
gerçek sonuç
```

## Örnek 23

Bu bölümde `await` konusunu yeniden öğrenmiyoruz.

Yeni olan nokta şudur:

> **Önce Query'yi hazırlayabilir, sonra `exec()` ile çalıştırabiliriz.**

Mongoose Query'leri `await` ile doğrudan da kullanılabilir. Ancak burada Query ile sonucu birbirinden daha net ayırmak için `exec()` kullanacağız.

---

## `countDocuments()` da Query Oluşturur

Aynı mantık:

```js
Movie.countDocuments({});
```

## Örnek 24

için de geçerlidir.

İki aşamada yazalım:

```js
const countQuery = Movie.countDocuments({});
```

## Örnek 25

Buradaki:

```js
countQuery
```

## Örnek 26

henüz sayı değildir.

Şu sorguyu temsil eder:

```text
Movie Document'larını say
```

## Örnek 27

Sonra:

```js
const totalItems = await countQuery.exec();
```

## Örnek 28

ile Query'yi çalıştırırız.

Artık:

```js
totalItems
```

## Örnek 29

örneğin:

```js
93
```

## Örnek 30

olabilir.

Farkı netleştirelim:

```text
countQuery
↓
sayım işlemini temsil eden Query

totalItems
↓
sayım tamamlandıktan sonra gelen gerçek sayı
```

## Örnek 31

---

## Neden Tek Query'yi Hem Saymak Hem Listelemek İçin Kullanmıyoruz?

Pagination'da iki farklı veritabanı işlemi yapıyoruz:

```text
1. Toplam kaç kayıt var?

2. Bu sayfada hangi kayıtlar var?
```

## Örnek 32

Bunlar aynı şey değildir.

Birincisi:

```js
Movie.countDocuments({});
```

## Örnek 33

ikincisi:

```js
Movie.find({});
```

## Örnek 34

ile başlar.

Dolayısıyla iki ayrı Query oluşturmak doğaldır:

```text
countQuery
↓
sayım işlemi

moviesQuery
↓
Document getirme işlemi
```

## Örnek 35

Aynı Query'yi önce çalıştırıp sonra farklı bir işlem için tekrar kullanmaya çalışmak doğru yaklaşım değildir.

Query'yi:

```text
bir kez hazırlanan
ve belirli bir veritabanı işlemini temsil eden nesne
```

## Örnek 36

olarak düşünmek daha sağlıklıdır.

---

## Sayım ve Liste Sorgusu Neden Ayrı?

Pagination yaparken aslında iki farklı soruya cevap veririz:

```text
toplam kaç film var?

bu sayfada hangi filmler gösterilecek?
```

## Örnek 37

Bu iki sorunun görevi farklı olduğu için iki ayrı sorgu kullanırız.

Toplam kayıt sayısını bulmak için:

```js
const totalItems = await Movie
  .countDocuments({})
  .exec();
```

## Örnek 38

Bu sorgu yalnızca **kaç kayıt olduğunu** hesaplar. Burada `sort`, `skip` veya `limit` kullanmayız; çünkü toplam sayıyı yalnızca o anki sayfaya göre daraltmak istemeyiz.

Sayfada gösterilecek filmleri almak için ise ayrı bir sorgu çalıştırırız:

```js
const movies = await Movie
  .find({})
  .sort({
    voteAverage: -1,
  })
  .skip(skip)
  .limit(limit)
  .exec();
```

## Örnek 39

Bu ikinci sorgunun görevi farklıdır:

```text
find({})
↓
filmleri seç

sort(...)
↓
sırala

skip(skip)
↓
önceki sayfalardaki kayıtları geç

limit(limit)
↓
bu sayfada dönecek kayıt sayısını sınırla
```

## Örnek 40

Kısacası:

```text
countDocuments()
↓
toplam kayıt sayısı

find() + sort() + skip() + limit()
↓
bu sayfada gösterilecek kayıtlar
```

## Örnek 41

elde edilir.

---

## Aynı Sorgu Koşullarını Tekrar Yazmadan Kullanmak: `.merge()`

Buraya kadar iki ayrı sorgunun iki ayrı görevi olduğunu gördük:

```text
countDocuments()
↓
toplam kayıt sayısını hesaplar

find()
↓
listeye girecek kayıtları getirir
```

## Örnek 42

Peki her iki sorgunun da **aynı filtre koşullarını** kullanması gerektiğinde ne yapacağız? Aynı koşulları iki farklı yerde yeniden yazabiliriz; ancak bu, sorgu büyüdükçe gereksiz tekrar oluşturur ve iki sorgunun zamanla birbirinden farklılaşmasına yol açabilir.

Tam bu noktada `.merge()` işimize yarar. Bir Query üzerinde hazırladığımız ortak sorgu bilgilerini başka bir Query'ye aktararak aynı koşulları yeniden kullanabiliriz.

Önce yalnızca ortak sorgu koşullarını taşıyan bir Query oluşturalım:

```js
const moviesQuery = Movie.find({});
```

## Örnek 43

Buradaki `moviesQuery` henüz çalışmadı. Elimizde film listesi değil, **hangi kayıtların sorguya dahil olacağını tarif eden bir Query** var.

Şimdi toplam kayıt sayısını hesaplarken bu Query'deki koşulları sayım sorgusuna aktarabiliriz:

```js
const totalItems = await Movie
  .countDocuments({})
  .merge(moviesQuery)
  .exec();
```

## Örnek 44

Burada adım adım olan şudur:

```text
Movie.countDocuments({})
↓
yeni bir sayım Query'si oluştur

.merge(moviesQuery)
↓
moviesQuery'deki ortak sorgu bilgilerini
bu Query'ye aktar

.exec()
↓
sayım sorgusunu çalıştır

sonuç
↓
totalItems
```

## Örnek 45

Önemli nokta şu:

`.merge(moviesQuery)`, `moviesQuery` sonucundaki filmleri sayım sorgusuna eklemez ve `moviesQuery`'yi çalıştırmaz. Buradaki amaç, hazırlanmış Query'nin taşıdığı sorgu bilgilerini başka bir Query üzerinde yeniden kullanmaktır.

Listeyi almak istediğimizde ise asıl `moviesQuery` üzerine yalnızca listelemeye ait ayarları ekleriz:

```js
const movies = await moviesQuery
  .sort({
    voteAverage: -1,
  })
  .skip(skip)
  .limit(limit)
  .exec();
```

## Örnek 46

Böylece görevler birbirinden ayrılır:

```text
moviesQuery
↓
ortak kayıt koşulları

countDocuments().merge(moviesQuery)
↓
aynı koşullara uyan toplam kayıt sayısı

moviesQuery.sort().skip().limit()
↓
aynı koşullardan yalnızca bu sayfada gösterilecek kayıtlar
```

## Örnek 47

Neden `sort`, `skip` ve `limit` işlemlerini `.merge()` işleminden **sonra** `moviesQuery` üzerine ekledik?

Çünkü bunlar toplam kayıt sayısını belirleyen koşullar değildir. Bunlar yalnızca liste sonucunun hangi sırada ve hangi sayfa aralığında döneceğini belirler.

Bu nedenle akışı şöyle düşünmek daha kolaydır:

```text
önce ortak Query'yi hazırla
↓
sayım sorgusuna merge et
↓
toplamı bul
↓
sonra liste Query'sine
sort + skip + limit ekle
↓
bu sayfanın filmlerini getir
```

## Örnek 48

Şu anda filtre kullanmadığımız için `moviesQuery` içindeki koşul `{}`'dır. Bu nedenle `.merge()` kullanmak zorunlu değildir; burada amacı Query bilgisinin başka bir sorguda nasıl yeniden kullanılabildiğini göstermek için kullanıyoruz. Filtreleme eklendiğinde aynı yaklaşımın neden yararlı olduğu daha görünür hâle gelecektir.

---

## Şimdiki Pagination Kodumuza Dönelim

Bu bölümde henüz filtre yok. Bu yüzden önce ortak Query'yi oluşturabiliriz:

```js
const moviesQuery = Movie.find({});
```

## Örnek 49

Toplam kayıt sayısı:

```js
const totalItems = await Movie
  .countDocuments({})
  .merge(moviesQuery)
  .exec();
```

## Örnek 50

Bu satırdan sonra `totalItems` artık Query değildir; gerçek bir sayıdır.

Ardından aynı `moviesQuery` üzerine yalnızca listeleme adımlarını ekleriz:

```js
const movies = await moviesQuery
  .sort({
    voteAverage: -1,
    releaseYear: -1,
    _id: 1,
  })
  .skip(skip)
  .limit(limit)
  .exec();
```

## Örnek 51

Bu kodu iki ayrı görev olarak okuyabiliriz:

```text
1. görev
aynı koşullara uyan toplam kayıt sayısını bul
↓
totalItems

2. görev
aynı koşullardaki kayıtları sırala
ve yalnızca istenen sayfayı getir
↓
movies
```

## Örnek 52

Bu ayrımı gördüğümüzde `.merge()` satırı da daha anlamlı hâle gelir: **ortak Query bilgisini sayım sorgusuna taşır; pagination işlemlerini ise liste sorgusunda bırakır.**

---

## Toplam Sayfa Sayısını Hesaplayalım

Elimizde:

```text
totalItems = 93
limit = 10
```

## Örnek 53

olsun.

Hesap:

```text
93 / 10
↓
9.3
```

## Örnek 54

çıkar.

Ama kalan 3 film için bir sayfa daha gerekir.

Bu nedenle:

```js
const totalPages = Math.ceil(totalItems / limit);
```

## Örnek 55

kullanırız.

Sonuç:

```text
totalPages = 10
```

## Örnek 56

olur.

---

## Sonraki ve Önceki Sayfa Bilgileri

Sonraki sayfa için:

```js
const hasNext = page < totalPages;
```

## Örnek 57

kullanabiliriz.

Örneğin:

```text
page = 3
totalPages = 10
↓
hasNext = true
```

## Örnek 58

Son sayfada:

```text
page = 10
totalPages = 10
↓
hasNext = false
```

## Örnek 59

Önceki sayfa için:

```js
const hasPrevious =
  page > 1 &&
  page <= totalPages;
```

## Örnek 60

kullanıyoruz.

Bu kitapta liste aralığının dışındaki:

```http
GET /movies?page=999
```

## Örnek 61

gibi Request'lerde de tutarlı metadata üretmek istiyoruz.

---

## Pagination Hesaplarını Yardımcı Fonksiyona Taşıyalım

48. bölümde:

```js
parsePaginationParams()
```

## Örnek 62

yardımcısıyla pagination girişlerini Controller'dan ayırmıştık.

Şimdi:

```text
totalPages
hasNext
hasPrevious
```

## Örnek 63

hesaplarını da ayrı bir yardımcıya taşıyalım.

Yeni dosyamız:

```text
src/utils/calculatePaginationData.js
```

## Örnek 64

olsun.

```js
export const calculatePaginationData = (totalItems, limit, page) => {
  const totalPages = Math.ceil(totalItems / limit);

  const hasNext = page < totalPages;

  const hasPrevious =
    page > 1 &&
    page <= totalPages;

  return {
    totalPages,
    hasNext,
    hasPrevious,
  };
};
```

## Örnek 65

Bu fonksiyon MongoDB sorgusu yapmaz.

Yalnızca aldığı sayılardan pagination metadata üretir.

---

## Neden `Boolean(totalPages - page)` Kullanmıyoruz?

Şöyle bir kullanım görebiliriz:

```js
const hasNext = Boolean(totalPages - page);
```

## Örnek 66

Ama:

```text
totalPages = 10
page = 999
```

## Örnek 67

olursa:

```text
10 - 999
↓
-989
```

## Örnek 68

çıkar.

JavaScript'te sıfır dışındaki sayılar truthy olduğu için:

```js
Boolean(-989);
// true
```

## Örnek 69

olur.

Oysa geçerli bir sonraki sayfa yoktur.

Bu nedenle:

```js
page < totalPages
```

## Örnek 70

ifadesi niyetimizi daha doğru anlatır.

---

## `getMovies` Controller'ının Güncel Hâli

```js
import {
  parsePaginationParams,
} from "../utils/parsePaginationParams.js";

import {
  calculatePaginationData,
} from "../utils/calculatePaginationData.js";

export const getMovies = async (req, res) => {
  const {
    page,
    limit,
  } = parsePaginationParams(req.query);

  const skip = (page - 1) * limit;

  const totalItems =
    await Movie.countDocuments({})
      .exec();

  const moviesQuery = Movie.find({})
    .sort({
      voteAverage: -1,
      releaseYear: -1,
      _id: 1,
    })
    .skip(skip)
    .limit(limit);

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

## Örnek 71

Artık Controller'daki Query akışı görünür durumda:

```text
countDocuments()
↓
sayım Query'si
↓
exec()
↓
totalItems

Movie.find()
↓
moviesQuery
↓
sort + skip + limit
↓
exec()
↓
movies
```

## Örnek 72

Böylece Query yalnızca isim olarak kalmaz; `countDocuments()` ile toplam kayıt sayısının, `Movie.find()` ile sayfadaki kayıtların neden ayrı sorgularla üretildiği akış üzerinde açıkça görülür.

---

## Bir Request'i Baştan Sona İzleyelim

Kullanıcı:

```http
GET /movies?page=3&limit=10
```

## Örnek 73

göndersin.

`parsePaginationParams()`:

```text
page = 3
limit = 10
```

## Örnek 74

üretir.

Controller:

```text
skip = 20
```

## Örnek 75

hesaplar.

Sayım Query'si:

```text
totalItems = 93
```

## Örnek 76

sonucunu versin.

`moviesQuery` ise:

```text
bütün Movie kayıtlarından başla
↓
sırala
↓
20 kayıt geç
↓
en fazla 10 kayıt getir
```

## Örnek 77

şeklinde hazırlanır.

`exec()` çalıştıktan sonra üçüncü sayfanın filmlerini elde ederiz.

Pagination yardımcısı da:

```text
totalPages = 10
hasNext = true
hasPrevious = true
```

## Örnek 78

hesaplar.

---

## Hiç Film Yoksa

Collection boşsa:

```text
totalItems = 0
totalPages = 0
hasNext = false
hasPrevious = false
data = []
```

## Örnek 79

olur.

Geçerli bir liste Request'inin boş sonuç döndürmesi hata olmak zorunda değildir.

---

## Küçük Bir Teknik Sınır

Sayım ve listeleme için iki ayrı Query çalıştırıyoruz:

```js
Movie.countDocuments({});

Movie.find({});
```

## Örnek 80

İlk sorgudan sonra başka bir işlem yeni film ekler veya film silerse ikinci sorgu farklı bir veritabanı durumunu görebilir.

Bu bölümde standart pagination akışını kuruyoruz.

Daha ileri düzey snapshot veya transaction ihtiyaçlarını burada ele almıyoruz.

---

## 🧠 Akılda Kalsın

Pagination için iki farklı sorguya ihtiyacımız vardır:

```text
kaç kayıt var?
↓
countDocuments()

hangi kayıtlar bu sayfada?
↓
find()
```

## Örnek 81

Mongoose **Query** nesnesinin önemli tarafı şudur:

```text
sorguyu hemen sonuç olarak almak yerine
↓
önce hazırlayabiliriz
↓
sort ekleyebiliriz
↓
skip ekleyebiliriz
↓
limit ekleyebiliriz
↓
sonra exec() ile çalıştırabiliriz
```

## Örnek 82

Yani Query:

> **Veritabanında yapılacak işlemi ve işlem çalışmadan önce eklediğimiz sorgu ayarlarını taşıyan Mongoose nesnesidir.**

Pagination açısından önemli olan, toplam sayıyı hesaplayan sorgu ile sayfadaki kayıtları getiren sorgunun aynı filtre koşullarını kullanmasıdır.

Mevcut akışta filtre olmadığı için her iki sorgu da `{}` ile çalışır. Filtreleme eklendiğinde aynı `filter` nesnesi iki sorguya da verilecektir.

---

## Sonraki Adım

Artık pagination akışımızda:

```text
Query oluşturma
Query hazırlama
Query çalıştırma
toplam kayıt sayısı
pagination metadata
```

## Örnek 83

arasındaki ilişkiyi biliyoruz.

Şimdi hâlâ:

```js
Movie.find({});
```
