# Bölüm 57 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Mongoose Virtuals ile Veritabanında Saklanmayan Alanlar Nasıl Oluşturulur?

## Saklanan Veriden Yeni Bir Bilgi Hesaplamak

56. bölümde Movie ile Director arasındaki referansı `populate()` ile kullanmaya başladık.

Artık tek bir filmi getirirken Director bilgisini de Response'a ekleyebiliyoruz:

```js
const movieQuery = Movie.findById(movieId)
  .populate({
    path: "director",
    select: {
      name: 1,
      birthYear: 1,
      country: 1,
      _id: 0,
    },
  });
```

## Örnek 2

Director sonucumuz kabaca şöyle olabilir:

```json
{
  "name": "Christopher Nolan",
  "birthYear": 1970,
  "country": "United Kingdom"
}
```

## Örnek 3

Burada `birthYear` veritabanında saklanan gerçek bir alandır.

Fakat API'de yönetmenin yaşını da göstermek istediğimizi düşünelim.

Elimizde yalnızca doğum yılı olduğu için şimdilik yıl bazında basit bir hesap yapabiliriz:

```text
mevcut yıl - doğum yılı
```

## Örnek 4

Örneğin mevcut yılın 2026 olduğunu düşünürsek:

```text
2026 - 1970
↓
56
```

## Örnek 5

Bu değeri doğrudan MongoDB'ye:

```js
{
  age: 56,
}
```

## Örnek 6

şeklinde kaydetmek ilk bakışta kolay görünebilir.

Ama bir yıl sonra hesap:

```text
2027 - 1970
↓
57
```

## Örnek 7

olacaktır.

MongoDB'deki:

```text
age: 56
```

## Örnek 8

ise kendiliğinden `57` olmaz.

Bu durumda her yıl bütün Director Document'larındaki `age` alanını ayrıca güncellememiz gerekirdi.

Aslında buna ihtiyacımız yok.

Çünkü yaş bilgisini hesaplamak için gerekli kaynak bilgi zaten elimizde:

```text
birthYear
↓
1970
```

## Örnek 9

Yeni problemimiz şu:

> Veritabanında yalnızca kaynak bilgiyi saklayıp, ondan hesaplanan başka bir değeri Mongoose Document'ı üzerinde normal bir alan gibi kullanabilir miyiz?

Evet.

Mongoose'ta bu tür alanlar için **Virtual** kullanabiliriz.

---

## Önce Kaynak Bilgi ile Türetilmiş Bilgiyi Ayıralım

Bu bölümün en önemli fikri Virtual'ın sözdizimi değildir.

Önce hangi bilgiyi veritabanında saklamamız gerektiğini anlamalıyız.

Bizim örneğimizde:

```text
birthYear
↓
Director'ın kaynak bilgisi
↓
MongoDB'de saklanır
```

## Örnek 10

Ama:

```text
age
↓
birthYear + mevcut yıldan hesaplanabilir
↓
her yıl değişebilir
```

## Örnek 11

Bu nedenle `age` ayrı bir MongoDB alanı olmak zorunda değildir.

Bunu şöyle düşünebiliriz:

```text
MongoDB
↓
birthYear: 1970

Mongoose Document
↓
birthYear: 1970
↓
hesapla
↓
age: 56
```

## Örnek 12

Buradaki `age`, saklanan değil **hesaplanan** bir değerdir.

> Not: Elimizde yalnızca `birthYear` bulunduğu için burada hesapladığımız değer yaklaşık, yıl bazlı bir yaştır. Gün ve ay bilgisine göre kesin yaş hesaplamak isteseydik yalnızca doğum yılı yerine doğum tarihi gibi daha ayrıntılı bir kaynak bilgi saklamamız gerekirdi.

---

## Virtual Nedir?

Mongoose Virtual, MongoDB'de doğrudan saklanmayan fakat Mongoose Document'ı üzerinden bir özellik gibi okunabilen değerdir.

Örneğin gerçek Director Document'ımız MongoDB'de şöyle olabilir:

```json
{
  "_id": "DIRECTOR_ID",
  "name": "Christopher Nolan",
  "birthYear": 1970,
  "country": "United Kingdom"
}
```

## Örnek 13

Burada:

```text
age
```

## Örnek 14

alanı yoktur.

Ama Mongoose tarafında bir Virtual tanımlarsak:

```js
director.age
```

## Örnek 15

şeklinde bir değer okuyabiliriz.

Kısa fikir:

```text
MongoDB'de saklanan alan
↓
birthYear

Mongoose'un hesapladığı Virtual
↓
age
```

## Örnek 16

Virtual, MongoDB'ye yeni bir alan eklemez.

Mongoose Document'ına hesaplanan bir özellik kazandırır.

---

## İlk Virtual'ımızı Oluşturalım

Çalıştığımız dosya:

```text
src/db/models/director.js
```

## Örnek 17

55. bölümde Director Schema'mızı şöyle oluşturmuştuk:

```js
import mongoose from "mongoose";

const directorSchema = new mongoose.Schema(
  {
    name: {
      type: String,
      required: [
        true,
        "Yönetmen adı zorunludur.",
      ],
    },
    birthYear: {
      type: Number,
    },
    country: {
      type: String,
    },
  },
  {
    timestamps: true,
  },
);
```

## Örnek 18

Şimdi Schema oluşturulduktan sonra, Model oluşturulmadan önce şu Virtual'ı ekleyelim:

```js
directorSchema.virtual("age").get(function () {
  if (
    this.birthYear === undefined ||
    this.birthYear === null
  ) {
    return null;
  }

  const currentYear = new Date().getFullYear();

  return currentYear - this.birthYear;
});
```

## Örnek 19

Kodun tamamını bir anda okumayalım.

Önce:

```js
directorSchema.virtual("age")
```

## Örnek 20

kısmı şunu söyler:

```text
Director Document'larında
↓
age adında bir Virtual tanımla
```

## Örnek 21

Ardından:

```js
.get(function () {
  ...
})
```

## Örnek 22

ile bu değer **okunduğunda** ne hesaplanacağını belirliyoruz.

Yani:

```js
director.age
```

## Örnek 23

okununca Mongoose Virtual'ın getter fonksiyonunu çalıştırır.

---

### Virtual İçindeki `this` Neyi Gösterir?

Virtual getter içinde:

```js
this.birthYear
```

## Örnek 24

kullanıyoruz.

Buradaki `this`, ilgili Director Document'ını temsil eder.

Örneğin Document:

```js
{
  name: "Christopher Nolan",
  birthYear: 1970,
}
```

## Örnek 25

ise Virtual içindeki:

```js
this.birthYear
```

## Örnek 26

şu değeri verir:

```text
1970
```

## Örnek 27

Ardından:

```js
const currentYear = new Date().getFullYear();
```

## Örnek 28

mevcut yılı alır.

Son olarak:

```js
return currentYear - this.birthYear;
```

## Örnek 29

ile yıl bazlı yaş değerini döndürürüz.

Akış:

```text
director.age okunur
↓
Virtual getter çalışır
↓
this.birthYear alınır
↓
mevcut yıl alınır
↓
ikisi çıkarılır
↓
hesaplanan değer döner
```

## Örnek 30

### Neden Normal `function` Kullandık?

54. bölümde Query Helper ve Query Middleware yazarken `this` değerini kullanabilmek için normal `function` kullandığımızı görmüştük.

Virtual getter'da da aynı ihtiyaç vardır.

Bu nedenle:

```js
function () {
```

## Örnek 31

kullanıyoruz.

Şunu kullanmıyoruz:

```js
() => {
```

## Örnek 32

Çünkü Virtual'ın üzerinde çalıştığı Director Document'ına `this` üzerinden ulaşmak istiyoruz.

---

### `birthYear` Yoksa Ne Olacak?

Schema'mızda `birthYear` şu anda zorunlu değildir.

Yani bazı Director Document'larında:

```js
birthYear
```

## Örnek 33

bulunmayabilir.

Bu nedenle Virtual'ın başında `birthYear` değerinin eksik olup olmadığını kontrol ediyoruz:

```js
if (
  this.birthYear === undefined ||
  this.birthYear === null
) {
  return null;
}
```

## Örnek 34

Böylece hem `undefined` hem de `null` durumunda hesaplamayı durduruyoruz.

Böylece eksik bir değerde:

```text
mevcut yıl - undefined
```

## Örnek 35

gibi anlamsız bir hesap yapmak yerine:

```js
age: null
```

## Örnek 36

mantığına ulaşabiliriz.

Bu bölümde amacımız `birthYear` alanını zorunlu hâle getirmek değildir.

Amacımız Virtual'ın mevcut Document verisinden nasıl hesaplandığını görmek.

---

## Virtual Gerçekten Çalışıyor mu?

Bunu önce Response'tan bağımsız olarak görelim.

Bir Director Document'ını aldığımızı düşünelim:

```js
const director = await Director.findById(
  directorId,
).exec();
```

## Örnek 37

Ardından:

```js
console.log(director.birthYear);
console.log(director.age);
```

## Örnek 38

çalıştırabiliriz.

Örneğin mevcut yıl 2026 ise terminalde yaklaşık olarak:

```text
1970
56
```

## Örnek 39

görebiliriz.

Buradaki önemli nokta şudur:

```text
birthYear
↓
MongoDB'den geldi

age
↓
MongoDB'den gelmedi
↓
Mongoose Virtual tarafından hesaplandı
```

## Örnek 40

MongoDB Compass veya Atlas'ta Director Document'ına baktığımızda hâlâ:

```json
{
  "name": "Christopher Nolan",
  "birthYear": 1970,
  "country": "United Kingdom"
}
```

## Örnek 41

görürüz.

Şöyle bir alan oluşmaz:

```js
age: 56
```

## Örnek 42

Çünkü Virtual veritabanına kaydedilmez.

---

## Virtual'ı Response'ta Nasıl Gösteririz?

Artık Director Document'ı üzerinden:

```js
director.age
```

## Örnek 43

değerine ulaşabiliyoruz.

Örneğin:

```js
console.log(director.age);
```

## Örnek 44

ile hesaplanan yaş değerini görebiliriz.

Movie API'mizde ise bu bilgiyi yalnızca kod içinde kullanmak istemiyoruz.

Director bilgisi Response'a gönderildiğinde `age` alanının da görünmesini istiyoruz:

```json
{
  "name": "Christopher Nolan",
  "birthYear": 1970,
  "age": 56
}
```

## Örnek 45

Bunun için Director Schema'nın JSON çıktısına Virtual alanları da dahil etmesini söylememiz gerekir.

Schema options bölümünü şöyle güncelleyelim:

```js
{
  timestamps: true,
  id: false,
  toJSON: {
    virtuals: true,
  },
}
```

## Örnek 46

Buradaki yeni bölüm:

```js
toJSON: {
  virtuals: true,
}
```

## Örnek 47

şunu söyler:

```text
Director Document'ı
↓
JSON çıktısına dönüştürülürken
↓
Virtual alanları da dahil et
```

## Örnek 48

Böylece Director Document'ı:

```js
res.status(200).json({
  data: director,
});
```

## Örnek 49

ile Response'a gönderildiğinde `age` Virtual'ı da JSON çıktısında yer alabilir.

Önemli ayrım şudur:

```text
MongoDB'de saklanan
↓
birthYear

MongoDB'de saklanmayan
↓
age

Response hazırlanırken
↓
age hesaplanır ve JSON çıktısına eklenir
```

## Örnek 50

Yani `age` hâlâ veritabanında saklanan gerçek bir alan değildir.

Director Document'ındaki mevcut bilgilerden hesaplanır ve ihtiyaç duyduğumuz çıktıda gösterilir.

> **Kısa not:** Mongoose, Schema'lara varsayılan olarak `_id` değerinin String karşılığını veren `id` adlı hazır bir Virtual ekler. `toJSON: { virtuals: true }` kullandığımızda bu Virtual da JSON çıktısında görünebilir. Response'u sade tutmak için `id: false` kullanıyoruz. Bu ayar MongoDB'deki gerçek `_id` alanını kaldırmaz; yalnızca Mongoose'un hazır `id` Virtual'ını kapatır.

---

## Populate Edilen Director'da Virtual'ı Görelim

Bölümün başında 56. bölümden devraldığımız populate Query'sini görmüştük. Burada kritik ayrıntı, Director projection'ında `birthYear` alanının seçili olmasıdır:

```js
.populate({
  path: "director",
  select: {
    name: 1,
    birthYear: 1,
    country: 1,
    _id: 0,
  },
})
```

## Örnek 51

Burada `birthYear` özellikle önemlidir.

Çünkü `age` Virtual'ı şu bilgiyi kullanıyor:

```js
this.birthYear
```

## Örnek 52

Populate edilen Director sonucunda `birthYear` varsa Virtual hesaplanabilir.

`toJSON.virtuals` seçeneğini de açtıktan sonra Response kabaca şöyle olabilir:

```json
{
  "message": "Film getirildi.",
  "data": {
    "title": "Interstellar",
    "releaseYear": 2014,
    "voteAverage": 8.7,
    "director": {
      "name": "Christopher Nolan",
      "birthYear": 1970,
      "country": "United Kingdom",
      "age": 56
    }
  }
}
```

## Örnek 53

Burada:

```text
name
birthYear
country
↓
Director Document'ının gerçek alanları
```

## Örnek 54

ama:

```text
age
↓
Virtual
↓
Response hazırlanırken hesaplanan değer
```

## Örnek 55

şeklinde düşünmeliyiz.

---

### Virtual İçin Gerekli Alanı Projection'dan Çıkarmayalım

Burada 52 ve 56. bölümlerle önemli bir bağlantı vardır.

Virtual:

```js
this.birthYear
```

## Örnek 56

üzerinden hesap yapıyor.

Eğer populate sırasında yalnızca:

```js
select: {
  name: 1,
  _id: 0,
}
```

## Örnek 57

alırsak `birthYear` Director sonucunda bulunmaz.

Bu durumda Virtual'ın hesaplamak için kullandığı bilgi de yoktur.

Bu nedenle `age` göstermek istediğimiz Response'ta:

```js
select: {
  name: 1,
  birthYear: 1,
  _id: 0,
}
```

## Örnek 58

gibi Virtual'ın ihtiyaç duyduğu kaynak alanı da seçmeliyiz.

Buradaki genel düşünce:

```text
Virtual başka bir alandan hesaplanıyor
↓
o alan Query sonucunda mevcut olmalı
```

## Örnek 59

olmalıdır.

---

## Virtual ile `lean()` Birlikte Nasıl Davranır?

52. bölümde `lean()` kullanmanın Query sonucunu nasıl değiştirdiğini görmüştük.

Normal bir Mongoose Query:

```text
Query
↓
Mongoose Document
```

## Örnek 60

döndürürken, `lean()` kullandığımızda:

```text
Query
↓
sade JavaScript object
```

## Örnek 61

elde ediyorduk.

Şimdi bunun Virtual'lar açısından önemli bir sonucu olduğunu görelim.

Önce Director'ı normal şekilde getirelim:

```js
const director = await Director.findById(
  directorId,
).exec();

console.log(director.age);
```

## Örnek 62

Burada `director` bir Mongoose Document'ıdır.

Bu nedenle Schema üzerinde tanımladığımız `age` Virtual'ına ulaşabiliriz.

Örneğin 2026 yılında terminalde:

```text
56
```

## Örnek 63

gibi yıl bazlı hesaplanmış bir değer görebiliriz.

Şimdi aynı Query'ye `lean()` ekleyelim:

```js
const director = await Director.findById(
  directorId,
)
  .lean()
  .exec();

console.log(director.age);
```

## Örnek 64

Bu kez sonuç farklıdır.

`lean()` bize Mongoose Document yerine sade bir JavaScript object verdiği için Schema üzerinde tanımladığımız `age` Virtual'ı bu object üzerinde bulunmaz.

Bu nedenle:

```text
undefined
```

## Örnek 65

görebiliriz.

İki durumu yan yana koyalım:

```text
Normal Query
↓
Mongoose Document
↓
Schema üzerindeki Virtual kullanılabilir
↓
director.age
↓
56
```

## Örnek 66



```text
lean() kullanılan Query
↓
sade JavaScript object
↓
normal Mongoose Virtual'ı yok
↓
director.age
↓
undefined
```

## Örnek 67

Buradaki önemli nokta şudur:

`lean()` Virtual'ı veritabanından silmez.

Zaten `age` veritabanında saklanan bir alan değildir.

Asıl fark Query sonucunun türüdür:

```text
Mongoose Document
↓
Virtual kullanılabilir

sade JavaScript object
↓
normal Mongoose Virtual'ı bulunmaz
```

## Örnek 68

Bu yüzden `lean()` kullanmak yanlış değildir.

Yalnızca Query sonucunda Mongoose Document özelliklerine ihtiyacımız olup olmadığını bilmemiz gerekir.

Artık bir Query tasarlarken yalnızca:

> Hangi veriyi getireceğim?

sorusunu değil, gerektiğinde:

> Getirdiğim sonuç üzerinde Mongoose Document davranışlarına ihtiyacım var mı?

sorusunu da düşünebiliriz.

### 56'daki Liste Query'miz Ne Olacak?

56. bölümde `GET /movies` tarafında şu yapıyı kullanmıştık:

```js
const moviesQuery = Movie.find(filter)
  .forMovieList()
  .populate({
    path: "director",
    select: {
      name: 1,
      _id: 0,
    },
  })
  .skip(skip)
  .limit(limit)
  .lean();
```

## Örnek 69

Buradaki `lean()` populate edilen sonuçlara da etki eder.

Bu nedenle Director Schema'da `age` Virtual'ımız olsa bile bu liste Query'sinde `age` otomatik görünmez.

Şimdilik bunu değiştirmeyeceğiz.

Buradaki önemli ayrım şu: Virtual normal Mongoose Document üzerinde kullanılabilir; `lean()` kullanılan liste Query'sinde ise sonuçlar plain JavaScript object olarak döndüğü için aynı Virtual otomatik olarak görünmez.

Bu farkı net görmek için tek Movie getiren ve `lean()` kullanmayan:

```http
GET /movies/:movieId
```

## Örnek 70

Route'unda Virtual'ı gösterebiliriz.

Liste Route'unda ise mevcut `lean()` kararımızı koruyabiliriz.

İleride gerçekten:

```text
hem lean()
hem Virtual
```

## Örnek 71

aynı sonuçta gerekli olursa bunun için farklı çözümler değerlendirilebilir.

Bu bölümde yeni bir paket veya ek soyutlama eklemeyeceğiz.

---

## Virtual ile MongoDB'de Arama Yapabilir miyiz?

Hayır.

Bu sınır çok önemlidir.

`age` MongoDB'de saklanan gerçek bir alan değildir.

Bu nedenle şöyle bir sorguyu:

```js
Director.find({
  age: 56,
});
```

## Örnek 72

normal bir MongoDB alanı sorgusu gibi düşünmemeliyiz.

MongoDB Document'ında:

```text
age
```

## Örnek 73

alanı yoktur.

Yalnızca:

```text
birthYear
```

## Örnek 74

vardır.

Virtual daha sonra Mongoose Document'ı üzerinde hesaplanır.

Akış:

```text
MongoDB Query
↓
saklanan alanlarla çalışır

Mongoose Virtual
↓
Document elde edildikten sonra hesaplanır
```

## Örnek 75

Dolayısıyla Virtual'ı:

```text
veritabanında yeni bir alan oluşturdum
```

## Örnek 76

gibi düşünmemeliyiz.

---

## Virtual Hangi Durumlarda Anlamlıdır?

Bizim örneğimiz:

```text
birthYear
↓
age
```

## Örnek 77

idi.

Başka örnekler de düşünülebilir.

Bir kullanıcının:

```text
firstName
lastName
```

## Örnek 78

alanlarından:

```text
fullName
```

## Örnek 79

hesaplanabilir.

Bir ürünün:

```text
price
vatRate
```

## Örnek 80

alanlarından:

```text
priceWithVat
```

## Örnek 81

türetilmek istenebilir.

Burada ortak fikir şudur:

```text
kaynak alanlar zaten var
↓
yeni değer bu alanlardan hesaplanabiliyor
↓
yeni değeri ayrıca saklamak zorunda değiliz
```

## Örnek 82

Fakat her hesaplanan değer mutlaka Virtual olmak zorunda değildir.

Eğer değer yalnızca tek Controller'da, tek bir durumda kullanılacaksa doğrudan o noktada hesaplamak daha basit olabilir.

Virtual özellikle:

```text
Document'a ait anlamlı bir özellik gibi davranan
ve tekrar kullanılabilecek
hesaplanmış değerlerde
```

## Örnek 83

anlamlıdır.

---

## `director.js` Dosyasının Güncel Hâli

Bu bölümde eklediğimiz parçalarla Director Model dosyamız şöyle olabilir:

```js
import mongoose from "mongoose";

const directorSchema = new mongoose.Schema(
  {
    name: {
      type: String,
      required: [
        true,
        "Yönetmen adı zorunludur.",
      ],
    },
    birthYear: {
      type: Number,
    },
    country: {
      type: String,
    },
  },
  {
    timestamps: true,
    id: false,
    toJSON: {
      virtuals: true,
    },
  },
);

directorSchema.virtual("age").get(function () {
  if (
    this.birthYear === undefined ||
    this.birthYear === null
  ) {
    return null;
  }

  const currentYear = new Date().getFullYear();

  return currentYear - this.birthYear;
});

export const Director =
  mongoose.models.Director ||
  mongoose.model("Director", directorSchema);
```

## Örnek 84

Dosyadaki sorumlulukları ayıralım:

```text
Schema alanları
↓
name, birthYear, country
↓
MongoDB'de saklanan bilgiler

Virtual
↓
age
↓
Document üzerinde hesaplanan bilgi

Model
↓
Director
```

## Örnek 85

---

## Postman ile Sonucu Görelim

Aynı `GET /movies/:movieId` Request'ini yeniden gönderelim. Query'de Director için `birthYear` seçili ve Director Schema'sında `toJSON.virtuals` açık olduğu için artık Response'ta `age` değerini de görebiliriz.

Postman'de:

```http
GET http://localhost:3000/movies/MOVIE_ID
```

## Örnek 86

Request'ini gönderelim.

Response'taki Director bölümünde kabaca:

```json
{
  "name": "Christopher Nolan",
  "birthYear": 1970,
  "country": "United Kingdom",
  "age": 56
}
```

## Örnek 87

görebiliriz.

Ardından MongoDB Atlas veya Compass'taki Director Document'ını kontrol edelim.

Orada hâlâ:

```json
{
  "name": "Christopher Nolan",
  "birthYear": 1970,
  "country": "United Kingdom"
}
```

## Örnek 88

vardır.

`age` yoktur.

Bu karşılaştırma Virtual'ın temel fikrini gösterir:

```text
Response'ta var
↓
MongoDB'de yok
↓
Mongoose hesapladı
```

## Örnek 89

---

## Bölüm Sonu Uygulaması

Bu bölümü yalnızca okuyarak geçmeyin.

### 1. `age` Virtual'ını Ekleyin

`director.js` içine:

```js
directorSchema.virtual("age").get(function () {
  if (
    this.birthYear === undefined ||
    this.birthYear === null
  ) {
    return null;
  }

  const currentYear = new Date().getFullYear();

  return currentYear - this.birthYear;
});
```

## Örnek 90

ekleyin.

### 2. Önce Document Üzerinde Test Edin

Geçici olarak bir Director Document'ını alın ve:

```js
console.log(director.birthYear);
console.log(director.age);
```

## Örnek 91

ile saklanan ve hesaplanan değeri yan yana görün.

### 3. Virtual'ı Response'a Ekleyin

Director Schema options içine:

```js
id: false,
toJSON: {
  virtuals: true,
},
```

## Örnek 92

ekleyin.

Ardından `GET /movies/:movieId` Request'ini gönderin.

Populate edilen Director içinde `age` alanının geldiğini doğrulayın.

### 4. MongoDB'de `age` Olmadığını Kontrol Edin

Atlas veya Compass üzerinden aynı Director Document'ına bakın.

Şu alanların saklandığını:

```text
name
birthYear
country
```

## Örnek 93

görün.

Ama:

```text
age
```

## Örnek 94

alanının saklanmadığını doğrulayın.

### 5. `lean()` Farkını Deneyin

Geçici olarak bir Director Query'sini:

```js
const director = await Director.findById(
  directorId,
)
  .lean()
  .exec();
```

## Örnek 95

şeklinde çalıştırın.

Ardından:

```js
console.log(director.age);
```

## Örnek 96

sonucunu gözlemleyin.

Virtual'ın neden görünmediğini şu cümleyle açıklayabiliyor olmalısınız:

```text
lean()
↓
Mongoose Document yerine sade object
↓
Mongoose Document Virtual'ı çalışmaz
```

## Örnek 97

### 6. Virtual ile Query Yazmayı Denemeyin, Nedenini Açıklayın

Şu kodun neden doğru bir yaklaşım olmadığını kendi kelimelerinizle açıklayın:

```js
Director.find({
  age: 56,
});
```

## Örnek 98

Ana neden:

```text
age MongoDB'de saklanan alan değildir
```

## Örnek 99

olmalıdır.

---

## 🧠 Akılda Kalsın

Virtual:

```text
MongoDB'de saklanmayan
↓
Mongoose Document üzerinde hesaplanan
↓
bir özellik
```

## Örnek 100

olarak düşünülebilir.

Bu bölümde:

```text
birthYear
↓
saklanan kaynak bilgi

age
↓
hesaplanan Virtual
```

## Örnek 101

ayrımını yaptık.

Virtual tanımı:

```js
directorSchema.virtual("age").get(function () {
  ...
});
```

## Örnek 102

şeklindedir.

Virtual getter içindeki:

```js
this
```

## Örnek 103

ilgili Mongoose Document'ını temsil eder.

Virtual'ın Express Response'unda görünmesi için Schema'da:

```js
toJSON: {
  virtuals: true,
}
```

## Örnek 104

kullanabiliriz.

Ama Virtual:

```text
MongoDB'ye kaydedilmez
MongoDB alanı gibi sorgulanamaz
normal lean() sonucunda gelmez
```

## Örnek 105

En önemli düşünce:

```text
kaynak bilgiyi sakla
↓
gerektiğinde türetilmiş bilgiyi hesapla
```

## Örnek 106

olmalıdır.

---

## Sonraki Adım

Bu bölümde bir Mongoose Document'ına **hesaplanan bir özellik** ekledik:

```js
director.age;
```

## Örnek 107

Burada `age` bir metot değildir. `birthYear` değerinden hesaplanan ve özellik gibi okunan bir Virtual'dır.

Şimdi farklı bir ihtiyaca geçelim.

Bir Movie Document'ının kendi verisine bakıp şu soruyu cevaplamasını istediğimizi düşünelim:

```js
movie.isHighRated();
```

## Örnek 108

Bu kez istediğimiz şey hesaplanan bir alan değil, **çağrılabilen bir davranıştır**.

Başka bir durumda soru tek bir Movie'ye değil, bütün Movie Model'ına ait olabilir:

```js
Movie.countHighRated();
```

## Örnek 109

Bu iki çağrının çalıştığı seviye farklıdır:

```text
movie.isHighRated()
↓
tek bir Document

Movie.countHighRated()
↓
Model / collection seviyesi
```
