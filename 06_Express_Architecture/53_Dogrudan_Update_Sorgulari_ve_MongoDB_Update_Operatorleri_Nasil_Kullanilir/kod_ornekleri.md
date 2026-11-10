# Bölüm 53 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Doğrudan Update Sorguları ve MongoDB Update Operatörleri Nasıl Kullanılır?

## Listeyi Düzenledik, Şimdi Film Kaydını Güncelleyeceğiz

52. bölümde Movie API'mizin listeleme tarafını geliştirdik.

Artık `GET /movies` tarafında:

```text
filtering
searching
sorting
pagination
projection
lean()
```

## Örnek 2

birlikte çalışabiliyor.

Listeleme tarafında hangi kayıtların geleceğini, hangi sırada döneceğini ve Response'ta hangi alanların bulunacağını daha kontrollü hâle getirdik.

Şimdi yeniden güncelleme tarafına dönelim.

35. bölümde `PATCH /movies/:movieId` Route'unu oluştururken güncelleme işlemini özellikle Mongoose Document üzerinden kurmuştuk:

```text
findById()
↓
Mongoose Document'ı bul
↓
gönderilen alanları Document üzerinde değiştir
↓
save()
↓
MongoDB'ye kaydet
```

## Örnek 3

Bu yöntem sayesinde ilk güncelleme işlemimizi adım adım görebildik.

35. bölümün sonunda Mongoose'un, Document'ı önce uygulamaya almadan doğrudan update sorguları da çalıştırabildiğini yalnızca not etmiş ve ayrıntılarını sonraya bırakmıştık.

40. bölümde validation anlatırken de bu sorgularla ilgili küçük bir fark görmüştük:

```text
Document.save()
↓
validation otomatik çalışır

doğrudan update sorgusu
↓
update validator'larını ayrıca etkinleştirmek gerekebilir
```

## Örnek 4

Şimdi o ertelediğimiz konuya dönüyoruz.

Bu bölümde sorumuz artık:

> **Bir Document'ı önce `findById()` ile uygulamaya almadan, yapılacak değişikliği doğrudan bir update Query'si içinde nasıl ifade ederiz?**

Bu geçişte Mongoose'un doğrudan update metotlarını ve MongoDB'nin:

```js
$set
$inc
$unset
```

## Örnek 5

update operatörlerini birlikte inceleyeceğiz.

---

## Önce İki Güncelleme Yaklaşımını Ayıralım

Mevcut PATCH Controller'ımızda yaptığımız işlem:

```js
const movie = await Movie.findById(movieId);

if (voteAverage !== undefined) {
  movie.voteAverage = voteAverage;
}

await movie.save();
```

## Örnek 6

şeklindeydi.

Burada önce bir Mongoose Document elde ediyoruz.

Sonra Document üzerinde değişiklik yapıyoruz.

Son olarak `save()` çağırıyoruz.

Kavramsal olarak:

```text
MongoDB
↓
Document'ı getir
↓
Node.js uygulamasındaki Mongoose Document
↓
alanı değiştir
↓
save()
↓
MongoDB'yi güncelle
```

## Örnek 7

Doğrudan update sorgusunda ise düşünce farklıdır:

```text
hangi Document?
+
hangi değişiklik?
↓
MongoDB'ye update sorgusu gönder
```

## Örnek 8

Örneğin:

```js
Movie.updateOne(
  { _id: movieId },
  {
    $set: {
      voteAverage: 8.3,
    },
  },
);
```

## Örnek 9

Burada önce filmi Mongoose Document olarak almak zorunda değiliz.

MongoDB'ye doğrudan şunu söylüyoruz:

```text
_id değeri movieId olan Document'ı bul
↓
voteAverage alanını 8.3 yap
```

## Örnek 10

İşte bu bölümün temel farkı budur.

---

## `updateOne()` Ne Döndürür?

Önce en sade doğrudan update örneğine bakalım:

```js
const result = await Movie.updateOne(
  { _id: movieId },
  {
    $set: {
      voteAverage: 8.3,
    },
  },
).exec();
```

## Örnek 11

`updateOne()` güncellenmiş Movie Document'ını döndürmez.

Bunun yerine güncelleme işlemi hakkında bir sonuç verir.

Örneğin:

```js
{
  acknowledged: true,
  matchedCount: 1,
  modifiedCount: 1,
}
```

## Örnek 12

Buradaki değerleri ayrı okuyalım.

```text
acknowledged: true
↓
MongoDB işlem isteğini kabul edip sonuç üretti
```

## Örnek 13

Ama bu değer tek başına:

```text
Document bulundu
```

## Örnek 14

anlamına gelmez.

Bunun için:

```text
matchedCount
```

## Örnek 15

alanına bakarız.

```text
matchedCount: 1
↓
filtreye uyan bir Document bulundu
```

## Örnek 16

`modifiedCount` ise **kaç Document üzerinde gerçek bir değişiklik oluştuğunu** gösterir. Burada sayılan şey değişen alanların sayısı değildir.

Örneğin aynı Movie Document içinde hem `title` hem de `voteAverage` alanını değiştirsek bile:

```text
1 Document değişti
↓
modifiedCount: 1
```

## Örnek 17

İki alan değişti diye `modifiedCount: 2` olmaz. `updateOne()` yalnızca tek bir Document'i güncellemeyi hedeflediği için bu değer pratikte `0` veya `1` olur.

```text
modifiedCount: 1
↓
eşleşen Document üzerinde en az bir değişiklik oluştu

modifiedCount: 0
↓
eşleşen Document üzerinde değişiklik oluşmadı
veya hiç Document eşleşmedi
```

## Örnek 18

Hangi durumun yaşandığını anlamak için `modifiedCount` değerini tek başına değil, `matchedCount` ile birlikte okuruz.

Fakat bizim Movie Schema'mızda:

```js
timestamps: true
```

## Örnek 19

kullanıyoruz. Mongoose doğrudan update sorgularında da `updatedAt` alanını güncellediği için, aynı alan değerini yeniden gönderdiğimiz bazı durumlarda yalnızca `updatedAt` değişse bile `modifiedCount` değeri `1` olabilir.

Bu nedenle bizim projemizde `modifiedCount` değerini:

```text
filmdeki iş alanlarından biri kesin değişti
```

## Örnek 20

şeklinde tek başına yorumlamayacağız.

Film hiç bulunamazsa:

```text
matchedCount: 0
modifiedCount: 0
```

## Örnek 21

olabilir.

Bu nokta birazdan ana PATCH Route'umuz için hangi metodu seçeceğimizi belirlerken önemli olacak.

---

## `$set` Nedir?

Biraz önce update bilgisini şöyle yazdık:

```js
{
  $set: {
    voteAverage: 8.3,
  },
}
```

## Örnek 22

Buradaki `$set`, MongoDB'nin update operatörlerinden biridir.

Görevi:

```text
bir alanın değerini belirle
```

## Örnek 23

şeklinde düşünülebilir.

Örneğin:

```js
{
  $set: {
    title: "Dune: Part Two",
    voteAverage: 8.6,
  },
}
```

## Örnek 24

şunu söyler:

```text
title
↓
"Dune: Part Two" yap

voteAverage
↓
8.6 yap
```

## Örnek 25

Diğer alanlara dokunmayız.

Bu davranış PATCH mantığımızla oldukça uyumludur.

Çünkü PATCH Request'inde zaten:

```text
yalnızca gönderilen alanları değiştir
```

## Örnek 26

istiyoruz.

---

### Update Metodu Sorguyu Çalıştırır, Update Operatörü Değişikliği Tarif Eder

Bu iki yapı güncellemenin farklı parçalarını üstlenir.

```text
updateOne()
findByIdAndUpdate()
↓
Mongoose update metotları
```

## Örnek 27

Ama:

```text
$set
$inc
$unset
↓
MongoDB update operatörleri
```

## Örnek 28

Yani şu kodda:

```js
Movie.updateOne(
  { _id: movieId },
  {
    $set: {
      voteAverage: 8.3,
    },
  },
);
```

## Örnek 29

iki ayrı karar vardır:

```text
updateOne()
↓
hangi update yöntemini kullanıyoruz?

$set
↓
alan üzerinde nasıl bir değişiklik yapıyoruz?
```

## Örnek 30

Bu ayrım önemlidir.

Çünkü aynı `$set` operatörünü başka bir update metodunda da kullanabiliriz:

```js
Movie.findByIdAndUpdate(
  movieId,
  {
    $set: {
      voteAverage: 8.3,
    },
  },
);
```

## Örnek 31

Operatör değişmedi.

Yalnızca kullandığımız Mongoose metodu değişti.

---

## PATCH İçin Güvenli `$set` Verisini Oluşturalım

İlk bakışta şu kullanım kısa görünebilir:

```js
Movie.findByIdAndUpdate(
  movieId,
  {
    $set: req.body,
  },
);
```

## Örnek 32

Fakat bunu kullanmayacağız.

Çünkü `req.body` kullanıcıdan gelir.

Bizim Movie API'mizde güncellenmesine izin verdiğimiz alanlar şunlardır:

```text
title
releaseYear
voteAverage
```

## Örnek 33

Bu nedenle hangi alanların update sorgusuna gireceğini yine uygulamamız belirlemelidir.

Mevcut Controller'ımızdaki yaklaşımı koruyabiliriz:

```js
const {
  title,
  releaseYear,
  voteAverage,
} = req.body;
```

## Örnek 34

Sonra yalnızca gönderilen alanlardan bir update nesnesi oluşturabiliriz.

---

### `$set` İçeriğini Request'e Göre Oluşturalım

Başlangıçta boş bir nesne oluşturalım:

```js
const fieldsToSet = {};
```

## Örnek 35

`title` gönderildiyse:

```js
if (title !== undefined) {
  fieldsToSet.title = title;
}
```

## Örnek 36

`releaseYear` gönderildiyse:

```js
if (releaseYear !== undefined) {
  fieldsToSet.releaseYear = releaseYear;
}
```

## Örnek 37

`voteAverage` gönderildiyse:

```js
if (voteAverage !== undefined) {
  fieldsToSet.voteAverage = voteAverage;
}
```

## Örnek 38

Örneğin Request Body:

```json
{
  "voteAverage": 8.3
}
```

## Örnek 39

ise `fieldsToSet` nesnesi:

```js
{
  voteAverage: 8.3,
}
```

## Örnek 40

olur.

Birden fazla alan gönderilirse:

```json
{
  "title": "Dune: Part Two",
  "voteAverage": 8.6
}
```

## Örnek 41

sonuç:

```js
{
  title: "Dune: Part Two",
  voteAverage: 8.6,
}
```

## Örnek 42

olur.

Sonra bunu:

```js
{
  $set: fieldsToSet,
}
```

## Örnek 43

şeklinde update sorgusuna verebiliriz.

---

### Boş PATCH Request'ini Yine Reddetmeliyiz

Önceki Controller'ımızda şu kontrol vardı:

```js
const hasUpdate =
  title !== undefined ||
  releaseYear !== undefined ||
  voteAverage !== undefined;
```

## Örnek 44

Bunu koruyabiliriz.

Çünkü kullanıcı:

```json
{}
```

## Örnek 45

veya yalnızca desteklemediğimiz alanları gönderirse güncellenecek Movie bilgisi oluşmaz.

Bu durumda:

```js
if (!hasUpdate) {
  return res.status(400).json({
    message: "Güncellenecek film bilgisi gönderilmedi.",
  });
}
```

## Örnek 46

ile Request'i reddederiz.

Yani doğrudan update sorgusuna geçmemiz:

```text
Request kontrolünü bırak
```

## Örnek 47

anlamına gelmez.

Yalnızca MongoDB'ye güncellemeyi gönderme yöntemimiz değişir.

---

## Ana PATCH Route'unda Hangi Update Metodunu Seçelim?

Şimdi ana PATCH Route'umuzu düşünelim.

Başarılı Response'umuz şu yapıda:

```js
res.status(200).json({
  message: "Film güncellendi.",
  data: updatedMovie,
});
```

## Örnek 48

Yani yalnızca:

```text
güncelleme başarılı mı?
```

## Örnek 49

bilgisini istemiyoruz.

Güncellenmiş filmi de Response'ta göndermek istiyoruz.

`updateOne()` bize doğrudan:

```text
matchedCount
modifiedCount
```

## Örnek 50

gibi işlem bilgileri verir.

Güncellenmiş Document'ı vermez.

Bu nedenle `updateOne()` kullansaydık güncel filmi almak için ayrıca başka bir Query çalıştırmamız gerekebilirdi.

Bizim Route ihtiyacımız için daha uygun seçenek:

```js
findByIdAndUpdate()
```

## Örnek 51

olabilir.

Çünkü:

```text
_id ile filmi bul
↓
güncelle
↓
güncellenmiş Document'ı döndür
```

## Örnek 52

akışını tek Query ile kurabiliriz.

---

### `findByIdAndUpdate()` ile `$set` Birlikte Kullanalım

Query'mizi şöyle hazırlayabiliriz:

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

## Örnek 53

Sonra 49. bölümden beri kullandığımız Query düşüncesini koruyarak:

```js
const updatedMovie = await updateMovieQuery.exec();
```

## Örnek 54

ile çalıştırabiliriz.

Buradaki üç parçayı tekrar okuyalım:

```text
movieId
↓
hangi Document?

$set
↓
hangi alanlar hangi değerleri alacak?

options
↓
Query nasıl davranacak?
```

## Örnek 55

---

### `returnDocument: "after"` Neden Var?

`findByIdAndUpdate()` ile güncelleme yaptığımızda hangi hâlin geri döneceğini de belirlememiz gerekir.

Varsayılan davranışta güncellemeden önceki Document dönebilir.

Biz Response'ta güncel hâli istediğimiz için:

```js
returnDocument: "after"
```

## Örnek 56

kullanıyoruz.

Bunu şöyle okuyabiliriz:

```text
güncellemeyi uygula
↓
işlemden sonraki Document'ı döndür
```

## Örnek 57

Örneğin:

```text
voteAverage
8.0 → 8.3
```

## Örnek 58

ise `updatedMovie` içinde:

```text
voteAverage: 8.3
```

## Örnek 59

olmasını isteriz.

> **Bilgi Notu:** Eski Mongoose kaynaklarında aynı amaç için `new: true` görebilirsiniz. Güncel Mongoose sürümlerinde `returnDocument: "after"` tercih edilir.

---

### `runValidators: true` Neden Hâlâ Önemli?

Movie Schema'mızda şu kurallar var:

```text
releaseYear
↓
1888 veya daha büyük

voteAverage
↓
0 ile 10 arasında
```

## Örnek 60

`save()` kullandığımız eski akışta validation otomatik çalışıyordu.

Doğrudan update sorgularında ise update validator'larını açıkça etkinleştirmeliyiz:

```js
runValidators: true
```

## Örnek 61

Böylece örneğin:

```json
{
  "voteAverage": 25
}
```

## Örnek 62

gibi bir Request gönderildiğinde `$set` ile güncellenen değer Schema kurallarına göre kontrol edilir.

Validation başarısız olursa daha önce kurduğumuz merkezi hata yönetimi `ValidationError` durumunu 400 Response'a çevirebilir.

Akış:

```text
PATCH Request
↓
$set oluştur
↓
findByIdAndUpdate()
↓
runValidators: true
↓
Schema kurallarını kontrol et
↓
geçerliyse güncelle
```

## Örnek 63

olur.

---

## PATCH Controller'ını Doğrudan Update Sorgusuna Taşıyalım

Çalıştığımız dosya:

```text
src/controllers/movies.js
```

## Örnek 64

`updateMovieById` Controller'ımızı şu hâle getirebiliriz:

```js
export const updateMovieById = async (req, res) => {
  const { movieId } = req.params;

  if (!mongoose.isObjectIdOrHexString(movieId)) {
    return res.status(400).json({
      message: "Geçersiz film kimliği.",
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

  const fieldsToSet = {};

  if (title !== undefined) {
    fieldsToSet.title = title;
  }

  if (releaseYear !== undefined) {
    fieldsToSet.releaseYear = releaseYear;
  }

  if (voteAverage !== undefined) {
    fieldsToSet.voteAverage = voteAverage;
  }

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

  const updatedMovie = await updateMovieQuery.exec();

  if (updatedMovie === null) {
    return res.status(404).json({
      message: "Film bulunamadı.",
    });
  }

  res.status(200).json({
    message: "Film güncellendi.",
    data: updatedMovie,
  });
};
```

## Örnek 65

Dikkat edersek Controller'ın dış davranışı değişmedi.

Hâlâ:

```text
geçersiz movieId
↓
400

film yok
↓
404

validation hatası
↓
400

başarılı güncelleme
↓
200 + güncellenmiş film
```

## Örnek 66

akışını koruyoruz.

Değişen şey MongoDB'ye güncellemeyi nasıl gönderdiğimizdir.

---

### Eski ve Yeni PATCH Akışını Yan Yana Koyalım

Eski yöntem:

```text
findById(movieId)
↓
Mongoose Document
↓
alanları Document üzerinde değiştir
↓
save()
↓
MongoDB
```

## Örnek 67

Yeni yöntem:

```text
Request'ten $set nesnesini oluştur
↓
findByIdAndUpdate(movieId, { $set: ... })
↓
MongoDB güncellemeyi uygular
↓
güncellenmiş Document döner
```

## Örnek 68

Kod olarak eski yapı:

```js
const movie = await Movie.findById(movieId);

movie.voteAverage = voteAverage;

await movie.save();
```

## Örnek 69

Doğrudan update yaklaşımı:

```js
const updatedMovie = await Movie.findByIdAndUpdate(
  movieId,
  {
    $set: {
      voteAverage,
    },
  },
  {
    returnDocument: "after",
    runValidators: true,
  },
).exec();
```

## Örnek 70

Burada biri diğerini tamamen geçersiz kılmaz.

`findById()` + `save()` özellikle Document davranışlarına, Document Middleware'lerine veya güncellemeden önce Document'ın mevcut hâline ihtiyaç duyduğumuz durumlarda anlamlı olabilir.

Doğrudan update sorguları ise değişikliği Query üzerinden ifade etmek istediğimiz durumlarda kullanışlıdır.

Bu bölümde PATCH Controller'ımızı doğrudan update sorgusuna taşıyarak ikinci yaklaşımı uyguladık.

> **Önemli:** Buradaki amaç `save()` yöntemini eski veya yanlış ilan etmek değildir. Mongoose dokümantasyonu, Document üzerinde çalışabildiğimiz durumlarda `save()` yaklaşımının validation ve Middleware davranışları açısından güçlü bir seçenek olduğunu özellikle belirtir. Doğrudan update sorgularını ise Query üzerinden güncelleme yapmamız gereken durumlar için öğreniyoruz.

---

### `timestamps` Davranışı Bozuldu mu?

42. bölümde Schema'mıza:

```js
{
  timestamps: true,
}
```

## Örnek 71

eklemiştik.

Bunun sonucunda Mongoose:

```text
createdAt
updatedAt
```

## Örnek 72

alanlarını yönetiyordu.

PATCH Controller'ını `save()` yerine `findByIdAndUpdate()` kullanacak şekilde değiştirmemiz `updatedAt` davranışını kaybettirmez.

Mongoose, `timestamps: true` kullanılan Schema'larda `findOneAndUpdate()` / `findByIdAndUpdate()` ve `updateOne()` gibi update işlemlerinde de `updatedAt` alanını günceller.

Yani:

```text
Document.save()
↓
updatedAt güncellenir

doğrudan update Query'si
↓
updatedAt yine güncellenir
```

## Örnek 73

şeklinde düşünebiliriz.

Bu bağlantı önemlidir.

Çünkü update yöntemimizi değiştirirken 42. bölümde kazandığımız timestamp davranışını kaybetmek istemeyiz.

---

## `$inc` Nedir?

`$set` bir alanı belirli bir değere getiriyordu.

Bazen ise mevcut değeri tamamen değiştirmek yerine onun üzerine bir miktar eklemek isteriz.

MongoDB bunun için:

```js
$inc
```

## Örnek 74

operatörünü sağlar.

Örneğin Schema'mızda şöyle bir sayaç alanı olduğunu düşünelim:

```text
viewCount: 10
```

## Örnek 75

Bir görüntülenme daha geldiğinde:

```js
{
  $inc: {
    viewCount: 1,
  },
}
```

## Örnek 76

şunu söyler:

```text
mevcut viewCount değerini al
↓
1 artır
```

## Örnek 77

Sonuç:

```text
10
↓
11
```

## Örnek 78

olur.

Negatif bir değer de kullanılabilir:

```js
{
  $inc: {
    stock: -1,
  },
}
```

## Örnek 79

Bu kez değer 1 azalır.

Bizim mevcut Movie Schema'mızda doğal bir sayaç alanı olmadığı için sırf `$inc` kullanmak adına yeni alan eklemeyeceğiz.

Burada öğrenmemiz gereken fikir şudur:

```text
$set
↓
değeri belirle

$inc
↓
mevcut değeri artır veya azalt
```

## Örnek 80

---

### `$inc` ile Validation Konusunda Önemli Bir Ayrıntı

Burada 40. bölümle ilgili önemli bir sınır var.

Mongoose'un update validator'ları her update operatöründe aynı şekilde çalışmaz.

Özellikle `$inc`, Mongoose update validator'larının kontrol ettiği operatörlerden biri değildir.

Bu nedenle:

```js
{
  runValidators: true,
}
```

## Örnek 81

yazmış olmamız `$inc` ile değiştirilen bir değerin bütün Schema sınırlarından mutlaka geçeceği anlamına gelmez.

Örneğin `voteAverage` için:

```text
max: 10
```

## Örnek 82

kuralımız var.

Şu düşünceyi kurmak güvenli değildir:

```js
{
  $inc: {
    voteAverage: 1,
  },
}
```

## Örnek 83



```text
+
runValidators: true
↓
10 sınırı kesin korunur
```

## Örnek 84

Bu nedenle mevcut Movie API'mizde `$inc` operatörünü `voteAverage` güncellemesi için kullanmayacağız.

Bu ayrıntı bize daha genel bir ders verir:

> **Bir update operatörünün nasıl çalıştığını bilmek kadar, kullandığımız Mongoose validation davranışının o operatör için geçerli olup olmadığını bilmek de önemlidir.**

---

## `$unset` Nedir?

Bazen bir alanın değerini değiştirmek değil, alanı Document'tan kaldırmak isteriz.

Bunun için:

```js
$unset
```

## Örnek 85

kullanılabilir.

Movie Schema'mızda `releaseYear` zorunlu değildir.

Bir filmde yanlışlıkla yıl bilgisi bulunduğunu ve bu alanı tamamen kaldırmak istediğimizi düşünelim.

Şöyle bir update yazabiliriz:

```js
const result = await Movie.updateOne(
  { _id: movieId },
  {
    $unset: {
      releaseYear: "",
    },
  },
  {
    runValidators: true,
  },
).exec();
```

## Örnek 86

Buradaki:

```js
releaseYear: ""
```

## Örnek 87

ifadesindeki boş String yeni değer değildir.

`$unset` açısından önemli olan alan adıdır.

İşlemin anlamı:

```text
releaseYear alanını Document'tan kaldır
```

## Örnek 88

şeklindedir.

Alan zaten yoksa `$unset` hata üretmek zorunda değildir; yapılacak bir değişiklik bulunmaz.

---

### Zorunlu Bir Alanı `$unset` Etmeye Çalışırsak?

Movie Schema'mızda:

```text
title
↓
required
```

## Örnek 89

kuralı vardır.

Şu update'i düşünelim:

```js
{
  $unset: {
    title: "",
  },
}
```

## Örnek 90

ve seçeneklerde:

```js
{
  runValidators: true,
}
```

## Örnek 91

kullanalım.

Bu durumda `title` zorunlu olduğu için update validation başarısız olabilir.

Yani `$unset` bize:

```text
alanı kaldırabilirim
```

## Örnek 92

imkânı verir.

Ama bu:

```text
Schema kurallarını görmezden gelebilirim
```

## Örnek 93

anlamına gelmez.

Bu nedenle update operatörlerini her zaman veri modelimizin kurallarıyla birlikte düşünmeliyiz.

---

### Birden Fazla Update Operatörü Birlikte Kullanılabilir mi?

Evet.

Örneğin aynı update içinde bir alanı değiştirip başka bir alanı kaldırabiliriz:

```js
{
  $set: {
    voteAverage: 8.3,
  },
  $unset: {
    releaseYear: "",
  },
}
```

## Örnek 94

Bunu şöyle okuyabiliriz:

```text
voteAverage
↓
8.3 yap

releaseYear
↓
Document'tan kaldır
```

## Örnek 95

MongoDB update nesnesi yalnızca tek bir operatör taşımak zorunda değildir.

Her operatör kendi görevini anlatır.

Bu da update sorgularını okunabilir hâle getiren önemli özelliklerden biridir.

---

## `updateOne()` Ne Zaman Daha Uygun Olabilir?

Ana PATCH Route'umuzda `findByIdAndUpdate()` seçtik.

Çünkü Response'ta güncellenmiş filmi istiyoruz.

Fakat her güncelleme işleminde Document'ın kendisine ihtiyaç duymayabiliriz.

Örneğin yalnızca:

```text
işlem gerçekleşti mi?
kaç Document eşleşti?
kaç Document değişti?
```

## Örnek 96

bilgileri yeterliyse:

```js
updateOne()
```

## Örnek 97

daha doğal olabilir.

Örnek:

```js
const result = await Movie.updateOne(
  { _id: movieId },
  {
    $set: {
      voteAverage: 8.3,
    },
  },
  {
    runValidators: true,
  },
).exec();
```

## Örnek 98

Sonra:

```js
if (result.matchedCount === 0) {
  return res.status(404).json({
    message: "Film bulunamadı.",
  });
}
```

## Örnek 99

şeklinde kontrol yapabiliriz.

Fakat güncellenmiş filmi Response'ta göndermek istersek `updateOne()` sonucunda o Document elimizde değildir.

Kısa karar tablomuz:

| İhtiyaç | Uygun seçenek |
|---|---|
| Bir Document'ı güncelle, işlem sonucu yeterli | `updateOne()` |
| `_id` ile güncelle ve Document'ı da döndür | `findByIdAndUpdate()` |
| Birden fazla eşleşmeyi güncelle | `updateMany()` |
| Önce Mongoose Document üzerinde çalışmak istiyorum | `findById()` + `save()` |

Burada tek bir "her zaman en iyi" yöntem yoktur.

İhtiyaca göre seçim yaparız.

---

## Postman ile Güncel PATCH Route'unu Deneyelim

Dune'ın gerçek `_id` değerini kullanarak:

```http
PATCH http://localhost:3000/movies/SIZIN_DUNE_ID_DEGERINIZ
```

## Örnek 100

Request Body:

```json
{
  "voteAverage": 8.3
}
```

## Örnek 101

Gönderelim.

Başarılı Response yaklaşık şöyle olabilir:

```json
{
  "message": "Film güncellendi.",
  "data": {
    "_id": "...",
    "title": "Dune",
    "releaseYear": 2021,
    "voteAverage": 8.3,
    "createdAt": "...",
    "updatedAt": "...",
    "__v": 0
  }
}
```

## Örnek 102

Ardından birden fazla alanı deneyelim:

```json
{
  "title": "Dune: Part One",
  "voteAverage": 8.4
}
```

## Örnek 103

Yalnızca gönderilen alanların değiştiğini doğrulayın.

Son olarak geçersiz bir değer deneyin:

```json
{
  "voteAverage": 25
}
```

## Örnek 104

`runValidators: true` sayesinde Schema validation'ın devreye girdiğini ve merkezi hata yönetiminin 400 Response ürettiğini gözlemleyin.

---

## Bölüm Sonu Uygulaması

Bu bölümde yalnızca kodu okumayın.

Aşağıdaki üç farkı kendi projenizde gözlemleyin.

### 1. Eski PATCH Akışını Hatırlayın

Önce mevcut kodun eski hâlini zihninizde kurun:

```text
findById()
↓
Document üzerinde değişiklik
↓
save()
```

## Örnek 105

Sonra yeni Controller'daki:

```text
$set oluştur
↓
findByIdAndUpdate()
↓
exec()
```

## Örnek 106

akışıyla karşılaştırın.

### 2. `updateOne()` Sonucunu Görün

Geçici bir deneme için şu sorguyu çalıştırın:

```js
const result = await Movie.updateOne(
  { _id: movieId },
  {
    $set: {
      voteAverage: 8.3,
    },
  },
  {
    runValidators: true,
  },
).exec();

console.log(result);
```

## Örnek 107

Terminalde özellikle:

```text
matchedCount
modifiedCount
```

## Örnek 108

alanlarını inceleyin.

Ardından bulunmayan geçerli bir ObjectId ile aynı sorguyu deneyip `matchedCount` değerinin `0` oluşunu gözlemleyin.

`timestamps: true` nedeniyle `updatedAt` alanının da update sırasında değişebileceğini unutmayın; bu yüzden `modifiedCount` değerini yalnızca `voteAverage` değişti mi? sorusunun cevabı olarak kullanmayın.

Deney bittikten sonra bu geçici kodu kaldırın.

### 3. `$unset` Davranışını Gözlemleyin

Test verinizde `releaseYear` alanı bulunan bir film seçin.

Geçici olarak:

```js
await Movie.updateOne(
  { _id: movieId },
  {
    $unset: {
      releaseYear: "",
    },
  },
).exec();
```

## Örnek 109

çalıştırın.

Atlas veya GET Request'i üzerinden `releaseYear` alanının artık bulunmadığını doğrulayın.

Sonra test verinizi eski hâline getirin.

Bu uygulamanın sonunda şu üç cümleyi kendi kelimelerinizle açıklayabiliyor olmalısınız:

```text
$set bir alanın değerini belirler.

$inc mevcut sayısal değeri artırır veya azaltır.

$unset bir alanı Document'tan kaldırır.
```

## Örnek 110

---

## 🧠 Akılda Kalsın

Document tabanlı güncelleme:

```text
findById()
↓
Document'ı değiştir
↓
save()
```

## Örnek 111

Doğrudan update sorgusu:

```text
filter / id
+
update bilgisi
↓
update Query'si
```

## Örnek 112

`$set`:

```text
alanın değerini belirle
```

## Örnek 113

`$inc`:

```text
sayısal değeri artır veya azalt
```

## Örnek 114

`$unset`:

```text
alanı kaldır
```

## Örnek 115

`updateOne()`:

```text
güncellenmiş Document yerine işlem sonucu döndürür
```

## Örnek 116

`findByIdAndUpdate()`:

```text
_id ile Document'ı bulup güncelleyebilir
ve Document döndürebilir
```

## Örnek 117

Güncellenmiş hâli almak için:

```js
returnDocument: "after"
```

## Örnek 118

Update validation için:

```js
runValidators: true
```

## Örnek 119

kullanırız.

Fakat update validator'larının her operatörde aynı şekilde çalışmadığını unutmamalıyız. Özellikle `$inc` bu açıdan ayrı değerlendirilmelidir.

---

## Sonraki Adım

Artık Movie API'mizde hem Document tabanlı hem Query tabanlı güncelleme yaklaşımını biliyoruz.

Üstelik Query'lerimiz giderek daha fazla davranış taşıyor:

```text
filter
sort
projection
update
```

## Örnek 120

Bir süre sonra bazı sorgularda aynı koşulları veya aynı davranışları tekrar tekrar yazmaya başlayabiliriz.

Örneğin belirli Movie Query'lerine ortak bir filtre davranışı eklemek veya belirli Query'ler çalışmadan önce ortak bir işlem yapmak isteyebiliriz.

54. bölümde Mongoose'un:

```text
Query Helpers
Middleware
```
