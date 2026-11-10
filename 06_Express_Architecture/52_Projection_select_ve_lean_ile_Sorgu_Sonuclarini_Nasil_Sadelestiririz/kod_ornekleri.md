# Bölüm 52 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Projection, select() ve lean() ile Sorgu Sonuçlarını Nasıl Sadeleştiririz?

## Film Kartında Her Bilgiyi Göstermek Zorunda mıyız?

Bu aşamadaki HTTP yanıtı ve sorgu örneklerini mevcut `src/controllers/movies.js` dosyasındaki Movie Controller üzerinden takip edeceğiz. Yeni dosya oluşturmadıkça aynı dosyanın ilgili fonksiyonunu güncelliyoruz.

Movie API'mizde artık film arayabiliyor, sonuçları filtreleyebiliyor ve sayfalara bölebiliyoruz.

Şimdi bu sonuçların bir film listesindeki kartlarda gösterileceğini düşünün. Kartta filmin **adı, yılı ve puanı** var. Kullanıcı bu üç bilgiye bakarak hangi filmin ayrıntılarını açacağına karar verecek.

Fakat veritabanındaki Movie Document'ında başka alanlar da bulunuyor. Liste ekranı bunları kullanmayacaksa her filmle birlikte göndermemiz gerçekten gerekli mi?

51. bölümde aradığımız filmi **hangi koşullarla bulacağımızı** belirlemiştik. Bu kez daha farklı bir soru soruyoruz:

> Bulduğumuz film hakkında **hangi bilgileri göndermeliyiz?**

Bu sorunun cevabı bizi sorgu sonucundaki alanları seçmeye götürüyor.

---

## Neden Projection'a İhtiyaç Duyuyoruz?

Bir Movie Document'ımız şöyle olsun:

```json
{
  "_id": "66d5f6c3e33c4f0012345678",
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3,
  "createdAt": "2026-09-02T05:30:00.000Z",
  "updatedAt": "2026-09-02T08:15:00.000Z",
  "__v": 0
}
```

## Örnek 2

`GET /movies` gibi bir liste endpoint'inde her alanı istemciye göndermemiz gerekmeyebilir.

Örneğin liste ekranı yalnızca şu alanları kullanıyor olabilir:

```text
_id
title
releaseYear
voteAverage
```

## Örnek 3

Bu durumda `createdAt`, `updatedAt` ve `__v` gibi alanları her film için Response'a eklemek gereksizdir.

İhtiyacımız şudur:

```text
Document'ları bul
↓
ama Response'a yalnızca gerekli alanları koy
```

## Örnek 4

MongoDB'de bu alan seçme işlemine **projection** denir.

---

## Projection Nedir?

Projection, bir sorgu sonucundaki Document'larda **hangi alanların yer alacağını** belirlememizi sağlar.

Örneğin yalnızca:

```text
title
releaseYear
voteAverage
```

## Örnek 5

alanlarını istiyorsak şöyle bir projection tanımlayabiliriz:

```js
{
  title: 1,
  releaseYear: 1,
  voteAverage: 1,
}
```

## Örnek 6

Buradaki `1` şu anlama gelir:

```text
1
↓
bu alanı sonuca dahil et
```

## Örnek 7

Bu projection sonucunda bir film yaklaşık olarak şöyle dönebilir:

```json
{
  "_id": "66d5f6c3e33c4f0012345678",
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3
}
```

## Örnek 8

Projection içinde `_id` yazmadığımız hâlde `_id` yine geldi.

Bunun nedeni, inclusion projection kullanıldığında MongoDB'nin `_id` alanını varsayılan olarak sonuca dahil etmesidir.

İstersek `_id` alanını açıkça hariç tutabiliriz:

```js
{
  title: 1,
  releaseYear: 1,
  voteAverage: 1,
  _id: 0,
}
```

## Örnek 9

Fakat Movie API'mizin liste sonucunda `_id` değerini koruyacağız. Çünkü istemci daha sonra belirli bir filme:

```http
GET /movies/:movieId
```

## Örnek 10

ile ulaşmak isteyebilir.

---

## Filtering ile Projection'ın Görevleri Farklıdır

Projection'ın ne yaptığını gördüğümüze göre, şimdi onu daha önce kullandığımız filtering ile ayıralım.

Filtering şu soruya cevap verir:

```text
hangi Document'lar sonuca girecek?
```

## Örnek 11

Örneğin:

```js
{
  releaseYear: 2021,
}
```

## Örnek 12

şunu söyler:

> `releaseYear` değeri 2021 olan Movie Document'larını getir.

Projection ise farklı bir soruya cevap verir:

```text
getirilen Document'ların hangi alanları sonuçta yer alacak?
```

## Örnek 13

Kısa ayrım:

```text
filter
↓
hangi Document'lar?

projection
↓
o Document'ların hangi alanları?
```

## Örnek 14

Örneğin filtering ile yalnızca 2021 filmlerini seçebilir, projection ile de bu filmlerden yalnızca `title`, `releaseYear` ve `voteAverage` alanlarını döndürebiliriz.

Yani filtering ile projection birbirinin yerine kullanılan iki özellik değildir; sorgunun farklı aşamalarını kontrol ederler.

---

## Projection'da İki Yol: Alan Seçmek veya Alan Çıkarmak

Projection kullanırken iki farklı yaklaşımımız vardır.

Örneğin veritabanındaki bir Movie Document'ının şu alanlara sahip olduğunu düşünelim:

```js
{
  _id: "66d5f6c3e33c4f0012345678",
  title: "Dune",
  releaseYear: 2021,
  voteAverage: 8.3,
  __v: 0,
  createdAt: "...",
  updatedAt: "...",
}
```

## Örnek 15

### 1. Sadece istediğimiz alanları seçmek

Şöyle bir projection yazarsak:

```js
{
  title: 1,
  releaseYear: 1,
  voteAverage: 1,
}
```

## Örnek 16

MongoDB'ye şunu söylemiş oluruz:

> Sonuçta yalnızca bu alanları tut.

Sonuç yaklaşık olarak şöyle görünür:

```js
{
  _id: "66d5f6c3e33c4f0012345678",
  title: "Dune",
  releaseYear: 2021,
  voteAverage: 8.3,
}
```

## Örnek 17

Buradaki `1`, alanın sonuca **dahil edilmesini** ister. Bu yaklaşıma **inclusion projection** denir.

### 2. Sadece istemediğimiz alanları çıkarmak

Bazen de hangi alanları göstereceğimizi tek tek saymak yerine, yalnızca istemediğimiz alanları çıkarabiliriz:

```js
{
  __v: 0,
  createdAt: 0,
  updatedAt: 0,
}
```

## Örnek 18

Bu kez MongoDB'ye şunu söyleriz:

> Bu alanları çıkar, diğer alanları bırak.

Sonuç:

```js
{
  _id: "66d5f6c3e33c4f0012345678",
  title: "Dune",
  releaseYear: 2021,
  voteAverage: 8.3,
}
```

## Örnek 19

Buradaki `0`, alanın sonuçtan **hariç tutulmasını** ister. Bu yaklaşıma **exclusion projection** denir.

### Neden `1` ve `0` kullanımını karıştırmıyoruz?

Bir projection'da genel olarak tek bir yaklaşım seçeriz:

```text
1 kullanıyorsak
↓
hangi alanların kalacağını söylüyoruz

0 kullanıyorsak
↓
hangi alanların çıkarılacağını söylüyoruz
```

## Örnek 20

Bu nedenle şu kullanım uygun değildir:

```js
{
  title: 1,
  releaseYear: 0,
}
```

## Örnek 21

Çünkü aynı projection içinde bir yandan **yalnızca `title` alanını dahil et**, diğer yandan **`releaseYear` alanını çıkar** şeklinde iki farklı yaklaşım birlikte kullanılmış olur.

Burada önemli bir istisna vardır: `_id`.

Inclusion projection kullansak bile MongoDB `_id` alanını varsayılan olarak sonuca ekler. `_id` alanını istemiyorsak bunu ayrıca kapatabiliriz:

```js
{
  title: 1,
  releaseYear: 1,
  _id: 0,
}
```

## Örnek 22

Bu kullanım geçerlidir. Sonuçta yalnızca:

```js
{
  title: "Dune",
  releaseYear: 2021,
}
```

## Örnek 23

kalır.

Movie API'mizde response'ta hangi alanların yer alacağını açıkça görmek istediğimiz için inclusion yaklaşımını kullanacağız.

---

## Mongoose'ta Projection'ı `select()` ile Tanımlayalım

Projection MongoDB tarafındaki kavramdır.

Mongoose Query üzerinde projection tanımlamak için:

```js
select()
```

## Örnek 24

metodunu kullanabiliriz.

Örneğin:

```js
Movie.find(filter).select({
  title: 1,
  releaseYear: 1,
  voteAverage: 1,
});
```

## Örnek 25

Bunu şöyle okuyabiliriz:

```text
find(filter)
↓
hangi Movie Document'ları?

select(...)
↓
bu Document'ların hangi alanları?
```

## Örnek 26

`select()` Query'yi hemen çalıştırmaz.

49. bölümden beri kullandığımız mantık değişmez:

```text
Query'yi hazırla
↓
metotlarla geliştir
↓
exec() ile çalıştır
```

## Örnek 27

> **Bilgi Notu:** Mongoose `select("title releaseYear voteAverage")` gibi String kullanımına da izin verir. Bu kitapta hangi alanları seçtiğimizi daha açık görmek için nesne biçimini kullanacağız.

---

## Liste Query'sine `select()` Ekleyelim

51. bölümün sonunda liste Query'miz şöyleydi:

```js
const moviesQuery = Movie.find(filter)
  .sort({
    voteAverage: -1,
    releaseYear: -1,
    _id: 1,
  })
  .skip(skip)
  .limit(limit);
```

## Örnek 28

Şimdi yalnızca ihtiyaç duyduğumuz alanları seçelim:

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
  .limit(limit);
```

## Örnek 29

Burada `_id` yazmadık.

Çünkü inclusion projection'da `_id` varsayılan olarak sonuçta kalır.

`countDocuments(filter)` tarafında ise `select()` kullanmamıza gerek yoktur.

Çünkü:

```js
const countQuery = Movie.countDocuments(filter);
```

## Örnek 30

bize Movie Document'larını değil, yalnızca filtreye uyan kayıt sayısını verir.

Projection bu nedenle `moviesQuery` için anlamlıdır.

---

## Postman'da Projection Farkını Görelim

Örneğin:

```http
GET http://localhost:3000/movies?q=Dune&page=1&limit=5
```

## Örnek 31

Request'ini gönderelim.

Projection eklenmeden önce `data` içindeki bir film şöyle görünebilir:

```json
{
  "_id": "66d5f6c3e33c4f0012345678",
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3,
  "createdAt": "2026-09-02T05:30:00.000Z",
  "updatedAt": "2026-09-02T08:15:00.000Z",
  "__v": 0
}
```

## Örnek 32

`select()` ekledikten sonra:

```json
{
  "_id": "66d5f6c3e33c4f0012345678",
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3
}
```

## Örnek 33

şeklinde dönebilir.

Burada projection'ın etkisini doğrudan Response üzerinde görebiliyoruz:

```text
createdAt
updatedAt
__v
```

## Örnek 34

artık liste sonucunda yoktur.

Projection'ın temel görevi budur:

> **Sorgu sonucunda hangi alanların bulunacağını belirlemek.**

Biz Movie API'mizde bu özelliği liste Response'unu yalnızca ihtiyaç duyduğumuz alanlarla sınırlamak için kullanıyoruz.

---

## Alanları Azalttık, Peki Elimizdeki Sonuç Tam Olarak Nedir?

Projection ile artık Response'ta hangi alanların bulunacağını belirleyebiliyoruz.

Örneğin Query sonucunda ilk film şu alanlarla gelsin:

```js
{
  title: "Dune",
  releaseYear: 2021,
  voteAverage: 8.1,
}
```

## Örnek 35

İlk bakışta bu değer sıradan bir JavaScript nesnesi gibi görünür.

Ama `find()` Query'sini normal şekilde çalıştırdığımızda Mongoose, MongoDB'den gelen sonucu yalnızca alanları olan sade bir nesne olarak bırakmaz. Onu kendi **Document** yapısına dönüştürür.

Bu yüzden:

```js
const movies = await Movie.find(filter)
  .select({
    title: 1,
    releaseYear: 1,
    voteAverage: 1,
  })
  .exec();
```

## Örnek 36

sonrasında:

```js
movies[0]
```

## Örnek 37

veri olarak şuna benzer görünse de:

```js
{
  title: "Dune",
  releaseYear: 2021,
  voteAverage: 8.1,
}
```

## Örnek 38

arka planda bir **Mongoose Document**'tır.

Bunu daha önce kullandığımız şu davranıştan tanıyoruz:

```js
document.save();
```

## Örnek 39

Sıradan bir JavaScript nesnesinde `save()` diye bir Mongoose metodu yoktur. Mongoose Document'ta ise bu tür Document özellikleri bulunabilir.

Yani normal akış kabaca şöyledir:

```text
MongoDB sonucu
↓
Mongoose sonucu Document'a dönüştürür
↓
uygulama Mongoose Document alır
```

## Örnek 40

Burada yeni bir ihtiyaç doğuyor.

`GET /movies` Route'unda bu filmleri yalnızca **okuyoruz** ve Response olarak gönderiyoruz. Bu sonuçlar üzerinde:

```js
document.save();
```

## Örnek 41

gibi Document davranışlarını kullanmayacağız.

Öyleyse yalnızca okunacak bir liste için her sonucu Mongoose Document olarak oluşturmamız şart mı?

Hayır. Böyle bir durumda sonucu daha sade bir JavaScript nesnesi olarak alabiliriz.

İşte `lean()` bu ihtiyacı karşılar.

---

## `lean()` Nedir?

`lean()`, Mongoose'a şu tercihi bildirir:

```text
Bu sonucu Mongoose Document'a dönüştürme.
Bana sade JavaScript nesneleri ver.
```

## Örnek 42

Normal Query:

```js
const movies = await Movie.find(filter).exec();
```

## Örnek 43

şu akışı kullanır:

```text
MongoDB sonucu
↓
hydration
↓
Mongoose Document
```

## Örnek 44

Buradaki **hydration**, veritabanından gelen ham sonucun Mongoose Document'a dönüştürülmesi adımıdır.

`lean()` eklediğimizde:

```js
const movies = await Movie.find(filter)
  .lean()
  .exec();
```

## Örnek 45

akış sadeleşir:

```text
MongoDB sonucu
↓
Document'a dönüştürme adımını atla
↓
sade JavaScript nesnesi
```

## Örnek 46

Örneğin her iki durumda da Response'ta şu veriyi görebiliriz:

```js
{
  title: "Dune",
  releaseYear: 2021,
  voteAverage: 8.1,
}
```

## Örnek 47

Fark, alanların görünüşünde değil; uygulamanın elindeki nesnenin türündedir.

Normal Query'de:

```text
movies[0]
↓
Mongoose Document
↓
Document davranışları kullanılabilir
```

## Örnek 48

`lean()` ile:

```text
movies[0]
↓
sade JavaScript nesnesi
↓
Mongoose Document davranışları yoktur
```

## Örnek 49

Bu nedenle `lean()`, özellikle veriyi yalnızca okuyup Response olarak gönderdiğimiz sorgularda yararlı olabilir. Mongoose'un Document oluşturmak için yaptığı ek işlemleri atladığı için uygulama tarafındaki işlem ve bellek kullanımını da azaltabilir.

Bunun karşılığında `save()` gibi Document davranışlarını kullanamayız. Değişiklik takibi, getter/setter ve virtual gibi bazı Mongoose Document özellikleri de aynı şekilde kullanılamaz.

Şimdilik karar verirken şu soruyu sormamız yeterlidir:

```text
Bu sonucu yalnızca okuyacak mıyım,
yoksa Mongoose Document davranışlarına ihtiyacım var mı?
```

## Örnek 50

---

## `lean()`ın Farkını Gerçekten Görelim

Burada önemli bir nokta vardır.

Projection'ın etkisini Postman Response'unda hemen gördük.

Fakat `lean()` eklediğimizde JSON Response çoğu durumda aynı görünebilir.

Çünkü `lean()`:

```text
hangi alanların döndüğünü
```

## Örnek 51

değil:

```text
Mongoose'un uygulamaya hangi tür nesne verdiğini
```

## Örnek 52

değiştirir.

Bunu küçük bir deneyle görelim.

Önce `moviesQuery` içinde henüz `lean()` kullanmayalım.

Query çalıştıktan sonra geçici olarak şu satırı ekleyin:

```js
const movies = await moviesQuery.exec();

console.log(
  typeof movies[0]?.save,
);
```

## Örnek 53

Veritabanında en az bir film varsa terminalde yaklaşık olarak:

```text
function
```

## Örnek 54

görebiliriz.

Çünkü `movies[0]` bir Mongoose Document'tır ve `save()` davranışına sahiptir.

Şimdi Query'nin sonuna:

```js
.lean()
```

## Örnek 55

ekleyelim:

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

## Örnek 56

Aynı `console.log()` bu kez:

```text
undefined
```

## Örnek 57

yazabilir.

Çünkü artık:

```text
movies[0]
↓
Mongoose Document değil
↓
sade JavaScript nesnesi
```

## Örnek 58

hâline gelmiştir.

İşte `lean()`ın asıl farkı budur.

Postman'daki JSON aynı görünebilir; ancak uygulamanın elindeki değer artık Mongoose Document değil, sade bir JavaScript nesnesidir.

Deney tamamlandıktan sonra geçici `console.log()` satırını silebiliriz.

---

## Sorguyu Üç Aşamada Şekillendirmek

Buraya kadar kullandığımız araçlar sorgunun farklı bir kararını etkiliyor. Hepsini tek yerde görelim:

| Araç | Cevap verdiği soru |
|---|---|
| `filter` | Hangi Document'lar seçilecek? |
| projection / `select()` | Seçilen Document'ların hangi alanları dönecek? |
| `lean()` | Sonuç Mongoose Document mı, sade JavaScript nesnesi mi olacak? |

Bu nedenle `select()` ve `lean()` birlikte kullanılabilir.

```text
select()
↓
hangi alanlar?

lean()
↓
hangi tür nesne?
```

## Örnek 59

`lean()` tek başına:

```text
createdAt
updatedAt
__v
```

## Örnek 60

alanlarını kaldırmaz.

Bu alanları azaltan şey projection'dır.

---

## `getMovies` Controller'ının Güncel Hâli

Artık liste Query'mizi iki yeni kararla geliştirebiliriz:

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

  const totalItems = await countQuery.exec();

  const movies = await moviesQuery.exec();

  const {
    totalPages,
    hasNext,
    hasPrevious,
  } = calculatePaginationData(
    totalItems,
    limit,
    page,
  );

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

## Örnek 61

Controller'ın temel sorumluluğu değişmedi.

Yalnızca `moviesQuery` artık şunları da söylüyor:

```text
select(...)
↓
hangi alanlar dönecek?

lean()
↓
sonuç Mongoose Document mı, sade JavaScript nesnesi mi olacak?
```

## Örnek 62

---

## Bütün Listeleme Akışını Birlikte Okuyalım

Artık `GET /movies` Route'unda öğrendiğimiz kavramların her biri farklı bir sorumluluk taşıyor:

```text
req.query
↓
Request'ten listeleme seçeneklerini al

parsePaginationParams()
↓
page + limit

buildMovieFilter()
↓
year + minVoteAverage + q

countDocuments(filter)
↓
filtreye uyan toplam kayıt sayısı

find(filter)
↓
hangi Movie Document'ları?

select(...)
↓
hangi alanlar?

sort(...)
↓
hangi sıra?

skip(...) + limit(...)
↓
hangi sayfa?

lean()
↓
Mongoose Document yerine sade JavaScript nesnesi

exec()
↓
Query'yi çalıştır
```

## Örnek 63

Zincir uzadı ama her parçanın görevi ayrıdır.

Bu ayrımı kurabildiğimizde uzun Query zincirlerini okumak daha kolay hâle gelir.

---

## Performans Hakkında Doğru Cümleyi Kuralım

Projection'ın temel görevi şudur:

```text
sorgu sonucunda hangi alanların bulunacağını belirlemek
```

## Örnek 64

Movie API'mizde bu sayede ihtiyaç duymadığımız alanları Response'a taşımıyoruz.

Bu, MongoDB'den uygulamaya taşınan veri miktarını azaltabilir.

Fakat:

> **Projection ekledim, dolayısıyla her sorgu otomatik olarak daha hızlıdır.**

şeklinde bir kural kurmamalıyız.

`lean()` ise farklı bir katmanda çalışır.

Mongoose'un Query sonucunu Document'a dönüştürme adımını atlar. Böylece uygulama tarafındaki işlem ve bellek kullanımını azaltabilir.

Yani:

```text
projection
↓
hangi veri alanları?

lean()
↓
Mongoose sonucu Document'a dönüştürecek mi?
```

## Örnek 65

Performans konusunda 45. bölümdeki yaklaşımımız değişmez:

```text
ihtiyacı doğru tasarla
↓
ölç
↓
sonuca göre optimize et
```

## Örnek 66

---

## Bölüm Sonu Uygulaması

Bu bölümü yalnızca okuyarak geçmeyin.

İki farkı da kendi uygulamanız üzerinde görün.

### 1. Projection'ı Gözlemleyin

`moviesQuery` içine:

```js
.select({
  title: 1,
  releaseYear: 1,
  voteAverage: 1,
})
```

## Örnek 67

ekleyin.

Ardından Postman'da:

```http
GET http://localhost:3000/movies
```

## Örnek 68



```http
GET http://localhost:3000/movies?year=2021
```

## Örnek 69



```http
GET http://localhost:3000/movies?q=Dune&page=1&limit=5
```

## Örnek 70

Request'lerini deneyin.

`data` içindeki Movie nesnelerinde şu alanların bulunmadığını doğrulayın:

```text
createdAt
updatedAt
__v
```

## Örnek 71

Sonra `select()` satırını geçici olarak kaldırıp aynı Request'i yeniden gönderin.

Response farkını kendiniz görün.

Ardından `select()` satırını geri ekleyin.

### 2. `lean()` Farkını Gözlemleyin

Önce Query'den `lean()` satırını geçici olarak kaldırın.

`movies` sonucunu aldıktan sonra:

```js
console.log(
  typeof movies[0]?.save,
);
```

## Örnek 72

ile terminal çıktısını inceleyin.

Sonra Query'ye tekrar:

```js
.lean()
```

## Örnek 73

ekleyin ve aynı Request'i gönderin.

Şu farkı kendiniz doğrulayın:

```text
lean() yok
↓
save → function

lean() var
↓
save → undefined
```

## Örnek 74

Bu deney bittikten sonra `console.log()` satırını silin ve proje kodunu bölümdeki güncel hâline döndürün.

Bu uygulamanın sonunda şu iki cümleyi kendi kelimelerinizle açıklayabiliyor olmalısınız:

```text
select() hangi alanların döneceğini değiştirir.

lean() Mongoose'un bize Mongoose Document yerine sade bir JavaScript nesnesi vermesini sağlar.
```

## Örnek 75

---

## 🧠 Akılda Kalsın

Filtering:

```text
hangi Document'lar?
```

## Örnek 76

Projection:

```text
hangi alanlar?
```

## Örnek 77

Mongoose'ta projection için:

```js
.select({
  title: 1,
  releaseYear: 1,
  voteAverage: 1,
})
```

## Örnek 78

kullanabiliriz.

Inclusion projection'da `_id` varsayılan olarak sonuçta kalır.

`lean()` ise:

```text
Mongoose Document'a dönüştürme
↓
atla
↓
sade JavaScript nesnesi döndür
```

## Örnek 79

mantığıyla çalışır.

En önemli ayrım:

```text
select()
↓
hangi alanlar?

lean()
↓
hangi tür nesne?
```

## Örnek 80

`lean()` kullandığımızda `save()` gibi Mongoose Document davranışlarını beklememeliyiz.

---

## Sonraki Adım

Movie API'mizin listeleme tarafı artık oldukça olgunlaştı.

Kullanıcı:

```text
sayfalayabiliyor
filtreleyebiliyor
arayabiliyor
```

## Örnek 81

API ise:

```text
hangi alanları döndüreceğini seçebiliyor
okuma sonuçlarını sade JavaScript nesneleri olarak alabiliyor
```

## Örnek 82

Şimdi yeniden güncelleme tarafına döneceğiz.

35. bölümde bir Movie Document'ını önce bulup alanlarını değiştirdikten sonra:

```js
movie.save()
```

## Örnek 83

kullanmıştık.

Fakat MongoDB ve Mongoose, Document'ı önce uygulamaya getirip sonra kaydetmek zorunda kalmadan doğrudan update sorguları da çalıştırabilir.

53. bölümde:

```text
updateOne()
findByIdAndUpdate()
$set
$inc
$unset
```
