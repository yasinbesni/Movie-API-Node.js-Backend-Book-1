# Bölüm 36 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# DELETE /movies/:movieId ile Bir Filmi Nasıl Sileriz?

## Bir Hata Yaptık: Dune İki Kez Eklendi

Film API'miz artık oldukça şey yapabiliyor.

Bir filmi oluşturabiliyoruz:

```http
POST /movies
```

## Örnek 2

Bütün filmleri getirebiliyoruz:

```http
GET /movies
```

## Örnek 3

Tek bir filmi getirebiliyoruz:

```http
GET /movies/:movieId
```

## Örnek 4

Bir filmi güncelleyebiliyoruz:

```http
PATCH /movies/:movieId
```

## Örnek 5

Peki ya veritabanında artık bulunmaması gereken bir kayıt varsa?

Bunu gerçek bir durum üzerinden görelim.

Dune'ı daha önce MongoDB'ye eklemiştik.

Şimdi yanlışlıkla aynı POST Request'ini bir kez daha gönderdiğimizi düşünün:

```http
POST /movies
```

## Örnek 6

Body:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3
}
```

## Örnek 7

Şu anki yapımızda `title` alanı için bir benzersizlik kuralı tanımlamadık.

Bu nedenle ikinci bir Dune document'ı oluşabilir.

`GET /movies` gönderdiğimizde kabaca şöyle bir sonuç görebiliriz:

```text
Interstellar

Dune
_id: 111...

Dune
_id: 222...
```

## Örnek 8

Başlıklar aynı.

Ama `_id` değerleri farklı.

Şimdi hatalı eklenen ikinci Dune'ı kaldırmak istiyoruz.

İşte DELETE işleminin gerçek ihtiyacı burada doğuyor.

---

## "Dune'ı Sil" Demek Yeterli mi?

Hayır.

Çünkü elimizde artık iki tane:

```text
Dune
```

## Örnek 9

var.

Backend'e yalnızca:

> Dune'ı sil.

dersek hangi Dune'dan söz ettiğimiz belirsiz olabilir.

Ama MongoDB her document'ı `_id` ile ayırt ediyordu.

Bu yüzden silmek istediğimiz kaydın kimliğini kullanacağız.

Örneğin hatalı Dune'ın `_id` değeri:

```text
68ac1234abcd5678ef901234
```

## Örnek 10

olsun.

Request:

```http
DELETE /movies/68ac1234abcd5678ef901234
```

## Örnek 11

olabilir.

Böylece Request iki şeyi birlikte söyler:

```text
DELETE
↓
ne yapmak istiyoruz?
↓
silmek

/movies/68ac1234abcd5678ef901234
↓
hangi kaydı?
↓
bu _id değerine sahip filmi
```

## Örnek 12

Daha önce öğrendiğimiz:

```text
HTTP yöntemi + URL
```

## Örnek 13

fikri artık gerçek bir MongoDB silme işlemine dönüşüyor.

---

## Route Şablonumuz Zaten Tanıdık

34 ve 35. bölümlerde:

```http
/movies/:movieId
```

## Örnek 14

yapısını kullandık.

DELETE için de aynı Route Parameter yapısını kullanabiliriz:

```js
app.delete("/movies/:movieId", async (req, res) => {
});
```

## Örnek 15

Buradaki:

```text
:movieId
```

## Örnek 16

yine gerçek bir `_id` değildir.

Express'e:

> URL'nin bu kısmı değişebilir.
> Gelen değeri `movieId` adıyla yakala.

der.

Gerçek Request ise örneğin:

```http
DELETE /movies/68ac1234abcd5678ef901234
```

## Örnek 17

şeklinde gelir.

Express bu değeri:

```js
req.params.movieId
```

## Örnek 18

içinde tutar.

---

## Hangi Dosyada Çalışıyoruz?

35. bölümde kullandığımız `index.js` dosyasındayız. `mongoose` ve `Movie` import'larımız da hazır.

Yeni bir dosya veya Model oluşturmuyoruz; mevcut CRUD akışına yalnızca şu davranışı ekliyoruz:

```http
DELETE /movies/:movieId
```

## Örnek 19

---

## Önce movieId'yi Alalım

Artık bu satır bize tanıdık geliyor:

```js
const { movieId } = req.params;
```

## Örnek 20

Request:

```http
DELETE /movies/68ac1234abcd5678ef901234
```

## Örnek 21

ise:

```js
movieId
```

## Örnek 22

değişkenimizin değeri:

```text
68ac1234abcd5678ef901234
```

## Örnek 23

olur.

Akış:

```text
URL
↓
:movieId
↓
req.params
↓
movieId
```

## Örnek 24

Şimdi hangi document'ı silmek istediğimizi biliyoruz.

Ama MongoDB sorgusuna geçmeden önce bir kontrolümüz daha vardı.

---

## Kimlik Biçimini Yine Kontrol Edelim

34. bölümden beri:

```js
mongoose.isObjectIdOrHexString(movieId)
```

## Örnek 25

kontrolünü kullanıyoruz.

DELETE Route'unda da bunu koruyacağız:

```js
if (!mongoose.isObjectIdOrHexString(movieId)) {
  return res.status(400).json({
    message: "Geçersiz film kimliği.",
  });
}
```

## Örnek 26

Örneğin:

```http
DELETE /movies/abc
```

## Örnek 27

gelirse:

```text
abc
↓
beklenen ObjectId biçiminde değil
↓
400 Bad Request
```

## Örnek 28

Response'u gönderebiliriz.

Bu kontrol artık bölümün yeni konusu değil.

API'mizin tutarlı davranışlarından biri hâline geldi.

---

## Mongoose'a "Bu Filmi Sil" Nasıl Deriz?

35. bölümde güncelleme yaparken önce:

```js
Movie.findById(movieId)
```

## Örnek 29

ile filmi bulmuş,

sonra Document'ı değiştirmiş,

ardından:

```js
movie.save()
```

## Örnek 30

ile kaydetmiştik.

Silme işleminde ise değiştireceğimiz bir alan yok.

Document'ın kendisini kaldırmak istiyoruz.

Mongoose Model'inde bunun için çok anlaşılır bir metot vardır:

```js
findByIdAndDelete()
```

## Örnek 31

Adını parçalayalım:

```text
find
↓
bul

ById
↓
_id değerine göre

AndDelete
↓
ve sil
```

## Örnek 32

Yani:

```js
Movie.findByIdAndDelete(movieId)
```

## Örnek 33

satırını şöyle okuyabiliriz:

> Movie kayıtları içinde
> bu `_id` değerine sahip document'ı bul
> ve sil.

35. bölümde gördüğümüz:

```js
findByIdAndUpdate()
```

## Örnek 34

fikrine oldukça benziyor.

Bu kez:

```text
bul
+
güncelle
```

## Örnek 35

değil,

```text
bul
+
sil
```

## Örnek 36

işlemini tek sorguda yapıyoruz.

---

## findByIdAndDelete() Aslında Neyi Arıyor?

Mongoose burada bize `_id` için hazırlanmış okunaklı bir kısa yol sunar.

Şu ifade:

```js
Movie.findByIdAndDelete(movieId)
```

## Örnek 37

temelde şu fikri taşır:

```js
Movie.findOneAndDelete({
  _id: movieId,
});
```

## Örnek 38

Yani Mongoose:

```text
_id = movieId
```

## Örnek 39

olan tek document'ı arar.

Bulursa siler.

Bu ayrıntıyı bilmek metodun adını ezberlemek yerine ne yaptığını anlamamızı sağlar.

---

## Silinen Film Nereye Gidecek?

Şimdi şu kodu yazalım:

```js
const deletedMovie = await Movie.findByIdAndDelete(movieId);
```

## Örnek 40

Buradaki değişken adına dikkat edin:

```text
deletedMovie
```

## Örnek 41

Çünkü bu işlemden sonra MongoDB'deki kayıt silinmiş olacaktır.

Ama Mongoose, sildiği document'ı sonuç olarak bize verebilir.

Örneğin silinen kayıt Dune ise `deletedMovie` kabaca:

```json
{
  "_id": "68ac1234abcd5678ef901234",
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3
}
```

## Örnek 42

bilgilerini taşıyabilir.

Burada çok kolay karışabilecek bir nokta var:

```text
deletedMovie değişkeninde Dune'ı görebiliyoruz
```

## Örnek 43

diye:

```text
Dune MongoDB'de hâlâ duruyor
```

## Örnek 44

sonucuna varmayın.

İki tarafı ayıralım:

```text
Node.js tarafı
↓
deletedMovie değişkeninde
silinen film bilgisi hâlâ elimizde

MongoDB tarafı
↓
o _id değerine sahip document
artık yok
```

## Örnek 45

Yani silinen kaydın bilgisini Response'ta gösterebilmemiz, kaydın veritabanında hâlâ var olduğu anlamına gelmez.

---

## Ya Böyle Bir Film Zaten Yoksa?

Şimdi önemli ikinci ihtimal geliyor.

Şu kod:

```js
const deletedMovie = await Movie.findByIdAndDelete(movieId);
```

## Örnek 46

çalıştı.

Ama verilen `_id` değerine sahip hiçbir film yoksa ne olacak?

Sonuç:

```js
null
```

## Örnek 47

olabilir.

Yani:

```text
deletedMovie
↓
null
```

## Örnek 48

Bu durumda:

```text
Film silindi.
```

## Örnek 49

demek yanlış olur.

Çünkü silinecek bir kayıt bulamadık.

Bu yüzden:

```js
if (deletedMovie === null) {
  return res.status(404).json({
    message: "Film bulunamadı.",
  });
}
```

## Örnek 50

kontrolünü ekleyebiliriz.

Akış:

```text
movieId biçimi doğru
↓
MongoDB'de ara
↓
eşleşen film yok
↓
404 Not Found
```

## Örnek 51

34 ve 35. bölümlerdeki `404` davranışımız burada da korunmuş oldu.

---

## Başarılı Silmede Hangi Status Code'u Kullanacağız?

Film bulundu ve silindi.

Peki kullanıcıya hangi HTTP durum kodunu gönderelim?

Burada birden fazla geçerli API tasarımı görebilirsiniz.

Biz silinen filmin bilgisini ve bir mesajı Response Body içinde göndermek istiyoruz:

```json
{
  "message": "Film silindi.",
  "data": {
    "title": "Dune"
  }
}
```

## Örnek 52

Bu durumda:

```text
200 OK
```

## Örnek 53

kullanabiliriz.

Response:

```js
res.status(200).json({
  message: "Film silindi.",
  data: deletedMovie,
});
```

## Örnek 54

olabilir.

Bunu şöyle okuyabiliriz:

```text
silme işlemi başarılı
↓
200 OK
↓
silinen kaydın bilgisini JSON olarak gönder
```

## Örnek 55

---

## Peki 204 No Content Nedir?

DELETE işlemlerinde bazen:

```text
204 No Content
```

## Örnek 56

durum kodunu da görebilirsiniz.

`204` şu fikri taşır:

> İşlem başarılı oldu,
> fakat Response Body göndermiyorum.

Örneğin:

```js
res.status(204).send();
```

## Örnek 57

kullanılabilir.

Ama önemli nokta şudur:

```text
204 No Content
↓
Response Body yok
```

## Örnek 58

Bu örnekte silinen filmi Response Body'de de döndürmek istediğimiz için:

```text
200 OK
```

## Örnek 59

kullanacağız.

Yani:

```text
200 OK
↓
başarılı + Response Body var

204 No Content
↓
başarılı + Response Body yok
```

## Örnek 60

İkisini birbirine karıştırmayalım.

---

## DELETE Route'unu Tamamlayalım

Artık bütün parçaları birleştirebiliriz:

```js
app.delete("/movies/:movieId", async (req, res) => {
  try {
    const { movieId } = req.params;

    if (!mongoose.isObjectIdOrHexString(movieId)) {
      return res.status(400).json({
        message: "Geçersiz film kimliği.",
      });
    }

    const deletedMovie = await Movie.findByIdAndDelete(movieId);

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
});
```

## Örnek 61

İlk bakışta Route birkaç kontrol içeriyor.

Ama yeni olan fikir aslında tek satır:

```js
const deletedMovie = await Movie.findByIdAndDelete(movieId);
```

## Örnek 62

Geri kalanı daha önce oluşturduğumuz API davranışlarını koruyor.

---

## Route'u Bir Hikâye Gibi Okuyalım

Request:

```http
DELETE /movies/DUNE_ID
```

## Örnek 63

geldi.

Express URL'deki değeri yakaladı:

```js
const { movieId } = req.params;
```

## Örnek 64

Kimlik biçimini kontrol ettik:

```text
uygun değil
↓
400 Bad Request
```

## Örnek 65

Kimlik uygunsa:

```js
const deletedMovie = await Movie.findByIdAndDelete(movieId);
```

## Örnek 66

çalıştı.

Film bulunamadıysa:

```text
404 Not Found
```

## Örnek 67

gönderdik.

Film bulundu ve silindiyse:

```text
200 OK
```

## Örnek 68

ile silinen filmin bilgisini gönderdik.

Bütün yol:

```text
DELETE /movies/DUNE_ID
↓
req.params.movieId
↓
ObjectId kontrolü
↓
Movie.findByIdAndDelete(movieId)
↓
film yoksa 404
↓
film silindiyse 200
↓
silinen film Response'ta
```

## Örnek 69

---

## Şimdi Gerçekten Deneyelim

Burada ana Dune kaydımızı değil, yanlışlıkla oluşturduğumuz ikinci Dune kaydını sileceğiz.

Önce:

```http
GET http://localhost:3000/movies
```

## Örnek 70

Request'ini gönderin.

İki Dune görüyorsanız `_id` değerlerine bakın:

```text
Dune
_id: 111...

Dune
_id: 222...
```

## Örnek 71

Silmek istediğiniz ikinci kaydın `_id` değerini kopyalayın.

Postman'de HTTP yöntemini:

```text
DELETE
```

## Örnek 72

olarak seçin.

Adres:

```text
http://localhost:3000/movies/SILINECEK_DUNE_ID_DEGERI
```

## Örnek 73

olsun.

Bu Request için Body göndermemize gerek yok.

Çünkü:

```text
hangi kayıt silinecek?
```

## Örnek 74

bilgisi zaten URL'nin içinde.

`Send` düğmesine basalım.

---

## Response'ta Ne Görmeliyiz?

Silme işlemi başarılıysa:

```text
200 OK
```

## Örnek 75

görmeliyiz.

Response Body şuna benzer olabilir:

```json
{
  "message": "Film silindi.",
  "data": {
    "_id": "SILINECEK_DUNE_ID_DEGERI",
    "title": "Dune",
    "releaseYear": 2021,
    "voteAverage": 8.3
  }
}
```

## Örnek 76

Burada silinen filmin bilgisini görebiliyoruz.

Ama tekrar hatırlayalım:

```text
Response'ta silinen film var
≠
MongoDB'de film hâlâ var
```

## Örnek 77

Response bize hangi kaydın silindiğini gösteriyor.

---

## Gerçekten Silindi mi?

Şimdi en önemli kontrolü yapalım.

Az önce sildiğimiz `_id` ile:

```http
GET http://localhost:3000/movies/SILINECEK_DUNE_ID_DEGERI
```

## Örnek 78

Request'ini gönderelim.

Bu kez:

```text
404 Not Found
```

## Örnek 79

görmeliyiz.

Çünkü o `_id` değerine sahip document artık MongoDB'de yok.

Response:

```json
{
  "message": "Film bulunamadı."
}
```

## Örnek 80

olabilir.

Ardından:

```http
GET http://localhost:3000/movies
```

## Örnek 81

Request'ini tekrar gönderirsek yalnızca kalmasını istediğimiz Dune kaydı listede bulunmalıdır.

Böylece silme işlemini yalnızca Response'a bakarak değil, gerçekten MongoDB'deki sonuç üzerinden doğrulamış oluruz.

---

## Aynı Filmi İkinci Kez Silmeye Çalışırsak?

Az önce sildiğimiz `_id` ile tekrar:

```http
DELETE /movies/SILINMIS_DUNE_ID_DEGERI
```

## Örnek 82

gönderirsek ne olur?

Mongoose:

```js
Movie.findByIdAndDelete(movieId)
```

## Örnek 83

ile arama yapar.

Ama document artık yoktur.

Sonuç:

```js
null
```

## Örnek 84

olur.

Bu yüzden Route:

```text
404 Not Found
```

## Örnek 85

gönderir.

Bu da mantıklıdır.

Çünkü ikinci Request geldiğinde artık silinecek bir kaynak yoktur.

---

## Neden title ile Silmedik?

Bu bölümün başındaki iki Dune'ı hatırlayın:

```text
Dune
_id: 111...

Dune
_id: 222...
```

## Örnek 86

Eğer yalnızca:

```text
title = Dune
```

## Örnek 87

bilgisine güvenseydik hangi document'ın hedeflendiği belirsizleşebilirdi.

Ama:

```text
_id
```

## Örnek 88

tek bir MongoDB document'ını hedeflememizi sağladı.

Şu ana kadar `_id` ile:

```text
tek filmi getirdik
↓
GET /movies/:movieId

filmi güncelledik
↓
PATCH /movies/:movieId

filmi sildik
↓
DELETE /movies/:movieId
```

## Örnek 89

Aynı kimlik farklı işlemlerde aynı görevi yapıyor:

```text
hangi document?
```

## Örnek 90

sorusunu cevaplıyor.

HTTP yöntemi ise:

```text
o document üzerinde ne yapacağız?
```

## Örnek 91

sorusunu cevaplıyor.

---

## Mongoose'ta Silme İçin Tek Bir Metot Yok

Şu ana kadar gerçek DELETE Route'umuzda:

```js
Movie.findByIdAndDelete(movieId)
```

## Örnek 92

kullandık.

Ama Mongoose'ta silme işlemleri için başka Model metotları da vardır.

Özellikle şu dört güncel yöntemi birbirinden ayırabilmemiz önemli:

```js
deleteOne(filter)
deleteMany(filter)
findByIdAndDelete(id)
findOneAndDelete(filter)
```

## Örnek 93

Ayrıca eski projelerde karşımıza çıkabilecek üç eski yöntem daha vardır:

```js
remove()
findByIdAndRemove()
findOneAndRemove()
```

## Örnek 94

Bunları **yeni kodda kullanmayacağız**.

Ama eski eğitimlerde veya eski projelerde görürsek ne olduklarını anlayabilmeliyiz.

Önce güncel yöntemlerden başlayalım.

---

### `deleteOne(filter)`

`deleteOne()` bir filtre alır ve bu filtreyle eşleşen **bir document'ı** silmeye çalışır.

Örneğin:

```js
const result = await Movie.deleteOne({
  _id: movieId,
});
```

## Örnek 95

Buradaki nesne filtredir. Yani Mongoose'a:

> `_id` değeri `movieId` olan bir document ara ve bir tanesini sil.

 demiş oluruz.

`deleteOne()` bize silinen Document'ın kendisini vermez. Bunun yerine silme işleminin sonucunu verir.

Örneğin:

```js
{
  acknowledged: true,
  deletedCount: 1
}
```

## Örnek 96

Buradaki:

```js
acknowledged: true
```

## Örnek 97

şunu anlatır:

> MongoDB yazma işlemini kabul etti ve sonucunu bildirdi.

Ama bu değer:

```text
mutlaka bir document silindi
```

## Örnek 98

anlamına gelmez.

Örneğin hiçbir document eşleşmezse:

```js
{
  acknowledged: true,
  deletedCount: 0
}
```

## Örnek 99

olabilir.

Asıl bakacağımız değer:

```js
deletedCount
```

## Örnek 100

olur.

```text
deletedCount: 1
↓
bir document silindi

deletedCount: 0
↓
hiçbir document silinmedi
```

## Örnek 101

Bu nedenle `deleteOne()` kullansaydık:

```js
const result = await Movie.deleteOne({
  _id: movieId,
});

if (result.deletedCount === 0) {
  return res.status(404).json({
    message: "Film bulunamadı.",
  });
}
```

## Örnek 102

gibi bir kontrol yapabilirdik.

---

### `deleteMany(filter)`

Şimdi tek bir kayıt değil, filtreye uyan **birden fazla document'ı** silmek istediğimizi düşünelim.

Örneğin:

```js
const result = await Movie.deleteMany({
  releaseYear: 2021,
});
```

## Örnek 103

Bu kod:

> `releaseYear` değeri `2021` olan bütün eşleşen document'ları sil.

 demektir.

Sonuç yine silinen Document'ların listesi değildir. İşlem sonucudur.

Örneğin:

```js
{
  acknowledged: true,
  deletedCount: 4
}
```

## Örnek 104

Buradaki:

```text
deletedCount: 4
```

## Örnek 105

dört document'ın silindiğini anlatır.

İki yöntemi karşılaştıralım:

```text
deleteOne(filter)
↓
eşleşen bir document'ı sil

deleteMany(filter)
↓
eşleşen bütün document'ları sil
```

## Örnek 106

`deleteMany()` daha geniş bir işlem yaptığı için filtre konusunda özellikle dikkatli olunmalıdır.

Örneğin:

```js
Movie.deleteMany({});
```

## Örnek 107

bütün document'larla eşleşebilecek bir filtredir. Bu kitapta bunu çalıştırmayacağız.

---

### `findByIdAndDelete(id)`

Bizim gerçek Route'umuzda kullandığımız yöntem buydu:

```js
const deletedMovie =
  await Movie.findByIdAndDelete(movieId);
```

## Örnek 108

Bu metot özellikle `_id` ile tek bir document'ı hedefler.

```text
_id ile document'ı bul
↓
sil
↓
silinen Document'ı döndür
```

## Örnek 109

Dune bulundu ve silindiyse `deletedMovie` içinde silinen filmin bilgileri olabilir.

Film bulunamazsa:

```js
deletedMovie === null
```

## Örnek 110

olur.

`findByIdAndDelete(id)` şu kullanımın `_id` için hazırlanmış kısa yoludur:

```js
Movie.findOneAndDelete({
  _id: movieId,
});
```

## Örnek 111

---

### `findOneAndDelete(filter)`

Peki `_id` dışında başka bir filtreyle **tek bir document** bulup silmek istersek?

Bu kez:

```js
Movie.findOneAndDelete(filter)
```

## Örnek 112

kullanabiliriz.

Örneğin:

```js
const deletedMovie = await Movie.findOneAndDelete({
  title: "Dune",
  releaseYear: 2021,
});
```

## Örnek 113

Bu kod verilen filtreye uyan tek bir document'ı bulur ve siler.

Hiçbir document bulunamazsa sonuç `null` olabilir.

Fark:

```text
findByIdAndDelete(id)
↓
özellikle _id ile tek document

findOneAndDelete(filter)
↓
istediğimiz filtreyle tek document
```

## Örnek 114

şeklindedir.

---

## Dört Güncel Silme Metodunu Yan Yana Görelim

```text
deleteOne(filter)
↓
filtreye uyan bir document'ı siler
↓
işlem sonucu döndürür
↓
deletedCount

deleteMany(filter)
↓
filtreye uyan bütün document'ları siler
↓
işlem sonucu döndürür
↓
deletedCount

findByIdAndDelete(id)
↓
_id ile tek document'ı bulur ve siler
↓
silinen Document'ı döndürebilir
↓
bulamazsa null

findOneAndDelete(filter)
↓
istenen filtreyle tek document'ı bulur ve siler
↓
silinen Document'ı döndürebilir
↓
bulamazsa null
```

## Örnek 115

Kısaca:

```text
Bir document silmek
ve işlem sonucu yeterli
→ deleteOne()

Birden fazla document silmek
→ deleteMany()

_id ile tek document silmek
ve silinen Document'ı da almak
→ findByIdAndDelete()

Başka bir filtreyle tek document silmek
ve silinen Document'ı da almak
→ findOneAndDelete()
```

## Örnek 116

Bizim Route'umuz:

```http
DELETE /movies/:movieId
```

## Örnek 117

olduğu için elimizde zaten `_id` var. Üstelik silinen filmi Response'ta göstermek istiyoruz.

Bu yüzden:

```js
findByIdAndDelete(movieId)
```

## Örnek 118

bizim senaryomuz için doğal bir seçimdir.

---

## Eski Kodlarda `remove()` Görürsek Ne Anlama Geliyor?

Eski Mongoose projelerinde şöyle kodlar görebiliriz:

```js
await Movie.remove({
  title: "Dune",
});
```

## Örnek 119

`remove()` artık güncel bir Mongoose yöntemi değildir.

Eski sürümlerde kullanılıyordu; daha sonra daha açık isimlere sahip:

```js
deleteOne()
deleteMany()
```

## Örnek 120

metotları tercih edildi.

Eski kullanımın niyetine göre:

```text
eski:
remove(filter)

çoğu çoklu silme senaryosunda güncel:
deleteMany(filter)
```

## Örnek 121

Eski kod `single: true` kullanıyorsa amaç en fazla bir document silmekti ve güncel karşılığı:

```js
deleteOne(filter)
```

## Örnek 122

olur.

Mongoose 7 ile `remove()` Model ve Document API'sinden kaldırıldı.

Bu yüzden yeni kodda:

```js
remove()
```

## Örnek 123

kullanmayacağız.

---

## Peki `findByIdAndRemove()` Nedir?

Eski projelerde şu kodla da karşılaşabiliriz:

```js
Movie.findByIdAndRemove(movieId)
```

## Örnek 124

Bu yöntem eski Mongoose sürümlerinde:

```js
Movie.findByIdAndDelete(movieId)
```

## Örnek 125

ile aynı amaca hizmet eden eski bir isimdi.

```text
findByIdAndRemove()
↓
eski isim

findByIdAndDelete()
↓
güncel isim
```

## Örnek 126

Mongoose 8 ile `findByIdAndRemove()` desteği kaldırıldı.

Bu nedenle yeni kodda:

```js
Movie.findByIdAndDelete(movieId)
```

## Örnek 127

kullanacağız.

Aynı eski isim ailesinde:

```js
findOneAndRemove()
```

## Örnek 128

da vardı.

Onun güncel karşılığı:

```js
findOneAndDelete()
```

## Örnek 129

olur.

---

## Eski ve Güncel İsimleri Birlikte Hatırlayalım

```text
ESKİ
remove()
↓
GÜNCEL
deleteOne() / deleteMany()

ESKİ
findByIdAndRemove()
↓
GÜNCEL
findByIdAndDelete()

ESKİ
findOneAndRemove()
↓
GÜNCEL
findOneAndDelete()
```

## Örnek 130

Bu eski metotları öğrenmemizin amacı onları kullanmak değildir.

Ama eski bir GitHub projesinde, eski eğitim videosunda veya yıllar önce yazılmış bir backend'de gördüğümüzde neyin yerine geçtiğini anlayabiliriz.

---

## index.js Dosyamızın İlgili Tam Hâli

Dosya sürekliliğini yine koruyalım.

`index.js`:

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

app.patch("/movies/:movieId", async (req, res) => {
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
});

app.delete("/movies/:movieId", async (req, res) => {
  try {
    const { movieId } = req.params;

    if (!mongoose.isObjectIdOrHexString(movieId)) {
      return res.status(400).json({
        message: "Geçersiz film kimliği.",
      });
    }

    const deletedMovie = await Movie.findByIdAndDelete(movieId);

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

## Örnek 131

Artık API'mizde:

```text
POST
↓
Create

GET
↓
Read

PATCH
↓
Update

DELETE
↓
Delete
```

## Örnek 132

işlemlerinin hepsi var.

Bu dört kelimenin baş harflerini yan yana getirdiğimizde:

```text
CRUD
```

## Örnek 133

ortaya çıkar.

Ve aslında son birkaç bölümde adım adım bir CRUD API kurduk.

---

## CRUD Artık Bir Kısaltma Değil

CRUD kelimesini daha önce görmüş olabilirsiniz.

Şimdi ise ne anlama geldiğini kodumuzun içinden biliyoruz.

```text
C
↓
Create
↓
POST /movies

R
↓
Read
↓
GET /movies
GET /movies/:movieId

U
↓
Update
↓
PATCH /movies/:movieId

D
↓
Delete
↓
DELETE /movies/:movieId
```

## Örnek 134

Bu noktaya kadar ayrı ayrı öğrendiğimiz Route'lar artık tek bir bütün oluşturuyor:

```text
Movie API
│
├── film oluştur
├── filmleri oku
├── tek filmi oku
├── filmi güncelle
└── filmi sil
```

## Örnek 135

Backend'imizin ilk gerçek veri yönetimi döngüsünü tamamladık.

---

## 🧠 Akılda Kalsın

DELETE Route'umuz:

```http
DELETE /movies/:movieId
```

## Örnek 136

şu soruları cevaplıyor:

```text
DELETE
↓
NE yapacağız?
↓
sileceğiz

movieId
↓
HANGİ document?
```

## Örnek 137

Mongoose tarafında:

```js
const deletedMovie =
  await Movie.findByIdAndDelete(movieId);
```

## Örnek 138

kullanıyoruz.

Sonuç:

```text
kimlik biçimi yanlış
↓
400 Bad Request

kimlik geçerli
ama film yok
↓
404 Not Found

film bulundu ve silindi
↓
200 OK
```

## Örnek 139

Silinen Document'ın bilgisini Response'ta görebilmemiz:

```text
MongoDB'de hâlâ bulunduğu
```

## Örnek 140

anlamına gelmez.

---

## Bölüm Özeti

Bu bölüme yanlışlıkla oluşturulmuş ikinci bir Dune kaydıyla başladık.

İki kaydın başlığı aynı olsa da:

```text
_id
```

## Örnek 141

değerleri farklıydı.

Bu yüzden silinecek document'ı `_id` ile hedefledik.

Route'umuzu:

```js
app.delete("/movies/:movieId", ...)
```

## Örnek 142

şeklinde oluşturduk.

URL'deki kimliği:

```js
const { movieId } = req.params;
```

## Örnek 143

ile aldık.

ObjectId biçimini kontrol ettik.

Ardından:

```js
const deletedMovie =
  await Movie.findByIdAndDelete(movieId);
```

## Örnek 144

ile document'ı bulup sildik.

Film bulunamadığında:

```text
404 Not Found
```

## Örnek 145

gönderdik.

Silme başarılı olduğunda:

```text
200 OK
```

## Örnek 146

ile silinen kaydın bilgisini Response olarak döndürdük.

`204 No Content` seçeneğinin de bulunduğunu, fakat Body göndermek istediğimiz için bu bölümde `200 OK` kullandığımızı gördük.

Ve sonunda şu yapıyı tamamladık:

```text
Create
Read
Update
Delete
↓
CRUD
```

## Örnek 147

---

## Sonraki Adım

CRUD API'miz artık çalışıyor.

Bir filmi:

```text
oluşturabiliyoruz
↓
POST

okuyabiliyoruz
↓
GET

güncelleyebiliyoruz
↓
PATCH

silebiliyoruz
↓
DELETE
```

## Örnek 148

Ama şimdi başka bir sorun ortaya çıktı.

Bütün bu Route'ları nereye yazdık?

```text
index.js
```

## Örnek 149

dosyasına.

Şu anda aynı dosyada:

```text
GET /
GET /movies
GET /movies/:movieId
POST /movies
PATCH /movies/:movieId
DELETE /movies/:movieId
MongoDB bağlantısı
sunucunun başlatılması
```

## Örnek 150

gibi farklı görevler bir arada duruyor.

Film API'miz henüz küçük olduğu için kodu takip edebiliyoruz.

Peki uygulamaya:

```text
users
auth
reviews
favorites
```

## Örnek 151

gibi yeni alanlar eklendiğinde ne olacak?

Her yeni Route'u yine:

```text
index.js
```

## Örnek 152

içine mi yazacağız?

Bir süre sonra sorun yalnızca dosyanın uzunluğu olmayacak.

Asıl sorun:

```text
hangi kod
hangi göreve ait?
```

## Örnek 153

sorusunun cevabını bulmanın zorlaşması olacak.

İşte bir sonraki bölümde yalnızca bu problemi çözeceğiz.

Önce iki sorumluluğu birbirinden ayıracağız:

```text
Router
↓
Hangi Request hangi işlemi çalıştıracak?

Controller
↓
Request geldiğinde ne yapılacak?
```

## Örnek 154

Ardından Express uygulamasını kuran kod ile uygulamayı başlatan kodu da ayıracağız:

```text
server.js
↓
Express uygulamasını kur

index.js
↓
MongoDB'ye bağlan
ve uygulamayı başlat
```
