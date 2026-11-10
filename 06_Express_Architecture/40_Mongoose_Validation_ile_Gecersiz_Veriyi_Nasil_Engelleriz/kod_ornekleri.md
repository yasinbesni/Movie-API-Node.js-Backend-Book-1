# Bölüm 40 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Mongoose Validation ile Geçersiz Veriyi Nasıl Engelleriz?

## Hiçbir Şey Çökmedi Ama Veri Yanlışsa?

39. bölümde beklenmeyen hataları tek bir yerde yönetmeyi öğrendik.

Artık kabaca şöyle bir yolumuz var:

```text
Request
↓
Router
↓
Controller
↓
beklenmeyen hata
↓
errorHandler
↓
500 Response
```

## Örnek 2

Bu bize önemli bir rahatlık sağladı.

Ama her problem:

```text
sunucu çöktü
```

## Örnek 3

demek değildir.

Bazen Express çalışır.

MongoDB bağlantısı çalışır.

Controller çalışır.

Hatta `save()` bile çalışmaya hazırdır.

Sorun başka yerdedir:

```text
gelen verinin kendisi yanlıştır
```

## Örnek 4

Örneğin kullanıcı:

```http
POST /movies
```

## Örnek 5

Request'ini şu Body ile gönderdi:

```json
{
  "title": "",
  "releaseYear": 1800,
  "voteAverage": 25
}
```

## Örnek 6

Burada bir şey dikkatinizi çekti mi?

```text
title
↓
boş

releaseYear
↓
1800

voteAverage
↓
25
```

## Örnek 7

Kod açısından bunlar hâlâ:

```text
String
Number
Number
```

## Örnek 8

olabilir.

Ama film verisi açısından mantıklı değiller.

Yeni sorumuz ortaya çıktı:

> Bir değer doğru türde olsa bile yanlış bir değer olabilir mi?

Evet.

İşte artık yalnızca:

```text
Bu alan String mi?
Bu alan Number mı?
```

## Örnek 9

soruları yetmiyor.

Şunu da sormamız gerekiyor:

```text
Bu değer KABUL EDİLEBİLİR mi?
```

## Örnek 10

Mongoose'un **validation** özelliği tam burada devreye girer.

---

## Validation Ne Demek?

`validation` kelimesini şimdilik:

```text
veriyi kurallara göre kontrol etmek
```

## Örnek 11

olarak düşünebiliriz.

Örneğin film puanı için kuralımız:

```text
0'dan küçük olamaz
10'dan büyük olamaz
```

## Örnek 12

olsun.

O zaman:

```text
8.3
↓
geçerli
```

## Örnek 13

ama:

```text
25
↓
geçersiz
```

## Örnek 14

olur.

Yani validation:

```text
veri geldi
↓
kurallara uyuyor mu?
↓
evet → devam et
hayır → kabul etme
```

## Örnek 15

mantığıdır.

---

## Bu Kuralları Nerede Yazacağız?

Movie Model'imizi oluşturduğumuz dosyayı hatırlayın:

```text
src/db/models/movie.js
```

## Örnek 16

Movie Schema'mızda şu alanlar vardı:

```js
const movieSchema = new mongoose.Schema({
  title: {
    type: String,
    required: true,
  },
  releaseYear: {
    type: Number,
  },
  voteAverage: {
    type: Number,
  },
});
```

## Örnek 17

Aslında burada validation ile ilk kez karşılaşmıyoruz.

Şu satırı daha önce kullanmıştık:

```js
required: true
```

## Örnek 18

O zaman bunu:

```text
title alanı zorunlu
```

## Örnek 19

olarak okumuştuk.

Şimdi adını da koyabiliriz:

```text
required
↓
Mongoose validator
```

## Örnek 20

Yani ilk validation kuralımız zaten Schema'nın içindeydi.

---

## Validator Nedir?

Schema'da bir alan için yazdığımız kontrol kuralına:

```text
validator
```

## Örnek 21

diyebiliriz.

Örneğin:

```js
title: {
  type: String,
  required: true,
}
```

## Örnek 22

içinde iki farklı bilgi var:

```text
type: String
↓
bu alanın türü ne?
```

## Örnek 23

ve:

```text
required: true
↓
bu alan zorunlu mu?
```

## Örnek 24

Bunlar aynı soru değildir.

Bunu yan yana görelim:

```text
type
↓
VERİNİN TÜRÜ
```

## Örnek 25



```text
validator
↓
VERİNİN KURALI
```

## Örnek 26

Bu ayrım önemli.

Çünkü birazdan Number alanlarına yeni kurallar ekleyeceğiz.

---

## Önce title Alanını Deneyelim

Schema'mızda:

```js
title: {
  type: String,
  required: true,
}
```

## Örnek 27

var.

Şimdi şu Request'i düşünün:

```http
POST /movies
```

## Örnek 28

Body:

```json
{
  "releaseYear": 2021,
  "voteAverage": 8.3
}
```

## Örnek 29

Burada:

```text
title
↓
hiç gönderilmedi
```

## Örnek 30

Ama Schema:

```js
required: true
```

## Örnek 31

diyor.

Mongoose bu Document'ı kaydetmeye çalışırken kuralı kontrol eder.

Akış:

```text
new Movie(...)
↓
movie.save()
↓
Mongoose validation
↓
title var mı?
↓
hayır
↓
validation başarısız
```

## Örnek 32

Document MongoDB'ye kaydedilmez.

---

## Validation Ne Zaman Çalışıyor?

Bu noktada çok önemli bir bağlantı var.

Controller'ımızda:

```js
const movie = new Movie({
  title,
  releaseYear,
  voteAverage,
});

await movie.save();
```

## Örnek 33

yazıyoruz.

Mongoose, `save()` sırasında Document'ı kaydetmeden önce validation kurallarını çalıştırır.

Yani kabaca:

```text
new Movie(...)
↓
Document oluştu
↓
save()
↓
validation
↓
kurallar geçerli mi?
```

## Örnek 34

Eğer:

```text
evet
```

## Örnek 35

ise:

```text
MongoDB'ye kaydet
```

## Örnek 36

Ama:

```text
hayır
```

## Örnek 37

ise:

```text
save() başarısız olur
↓
Promise reddedilir
```

## Örnek 38

39. bölümde öğrendiğimiz Express 5 hata akışı burada tekrar karşımıza çıkıyor.

---

## 39. Bölüm Bir Anda Neden İşimize Yaramaya Başladı?

Şu Controller'a bakalım:

```js
export const createMovie = async (req, res) => {
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
};
```

## Örnek 39

Eğer validation başarısız olursa:

```js
await movie.save();
```

## Örnek 40

başarısız olur.

Yani:

```text
save()
↓
validation hatası
↓
Promise reddedildi
↓
Express 5 hata akışı
↓
errorHandler
```

## Örnek 41

Şimdi elimizde çok güzel bir bağlantı oluştu:

```text
40. bölüm
Mongoose validation
↓
hata oluştu

39. bölüm
Express merkezi hata yönetimi
↓
hata ortak yere ulaştı
```

## Örnek 42

Ama burada yeni bir problemimiz var.

Bizim `errorHandler` şu anda bütün beklenmeyen hatalara:

```text
500 Internal Server Error
```

## Örnek 43

dönüyor.

Peki kullanıcının geçersiz film bilgisi göndermesi gerçekten:

```text
sunucu hatası
```

## Örnek 44

mı?

Hayır.

Birazdan bunu düzelteceğiz.

Ama önce Schema kurallarımızı tamamlayalım.

---

## releaseYear İçin Bir Alt Sınır Koyalım

Şu an:

```js
releaseYear: {
  type: Number,
}
```

## Örnek 45

yazıyor.

Bu durumda:

```json
{
  "releaseYear": 1800
}
```

## Örnek 46

Number olduğu için tür açısından sorun görünmez.

Ama film yılı için böyle bir değeri kabul etmek istemiyoruz.

Bir alt sınır ekleyebiliriz:

```js
releaseYear: {
  type: Number,
  min: 1888,
}
```

## Örnek 47

Buradaki:

```js
min: 1888
```

## Örnek 48

şunu söyler:

```text
releaseYear
↓
1888'den küçük olamaz
```

## Örnek 49

Örneğin:

```text
2021
↓
geçerli
```

## Örnek 50

ama:

```text
1800
↓
geçersiz
```

## Örnek 51

olur.

Mongoose Number alanlarında `min` ve `max` validator'larını hazır olarak sunar.

---

## voteAverage İçin Hem min Hem max Kullanalım

Film puanımız:

```text
0 ile 10 arasında
```

## Örnek 52

olsun.

Şu an Schema:

```js
voteAverage: {
  type: Number,
}
```

## Örnek 53

şeklinde.

Bunu:

```js
voteAverage: {
  type: Number,
  min: 0,
  max: 10,
}
```

## Örnek 54

olarak değiştirebiliriz.

Artık:

```text
8.3
↓
geçerli
```

## Örnek 55



```text
0
↓
geçerli
```

## Örnek 56



```text
10
↓
geçerli
```

## Örnek 57

ama:

```text
-2
↓
geçersiz
```

## Örnek 58

ve:

```text
25
↓
geçersiz
```

## Örnek 59

olur.

Buradaki iki kural:

```text
min: 0
↓
en küçük değer
```

## Örnek 60



```text
max: 10
↓
en büyük değer
```

## Örnek 61

şeklinde okunabilir.

---

## Movie Schema'mızın Yeni Hâli

Çalıştığımız dosya:

```text
src/db/models/movie.js
```

## Örnek 62

Schema kısmı artık:

```js
const movieSchema = new mongoose.Schema({
  title: {
    type: String,
    required: true,
  },
  releaseYear: {
    type: Number,
    min: 1888,
  },
  voteAverage: {
    type: Number,
    min: 0,
    max: 10,
  },
});
```

## Örnek 63

Model export'umuz yine:

```js
export const Movie =
  mongoose.models.Movie ||
  mongoose.model("Movie", movieSchema);
```

## Örnek 64

olarak kalır.

Yani dosyanın görevi değişmedi.

Sadece Schema artık daha fazla şey biliyor:

```text
title
↓
String
+
zorunlu

releaseYear
↓
Number
+
en az 1888

voteAverage
↓
Number
+
0 ile 10 arasında
```

## Örnek 65

---

## Şimdi Baştaki Kötü Veriyi Tekrar Gönderelim

Request:

```http
POST /movies
```

## Örnek 66

Body:

```json
{
  "title": "",
  "releaseYear": 1800,
  "voteAverage": 25
}
```

## Örnek 67

Schema kurallarımız:

```text
title
↓
required

releaseYear
↓
min 1888

voteAverage
↓
min 0
max 10
```

## Örnek 68

Şimdi Mongoose veriyi kontrol eder.

Kabaca:

```text
title boş
↓
uygun değil

releaseYear 1800
↓
1888'den küçük
↓
uygun değil

voteAverage 25
↓
10'dan büyük
↓
uygun değil
```

## Örnek 69

Sonuç:

```text
validation başarısız
↓
save() başarısız
↓
MongoDB'ye kayıt yok
```

## Örnek 70

İşte validation'ın bize sağladığı koruma bu.

---

## Peki title: "" Gerçekten required Kuralına Takılır mı?

Evet.

String alanında:

```js
required: true
```

## Örnek 71

kullandığımızda boş String değeri de required kontrolünü geçmez.

Yani:

```json
{
  "title": ""
}
```

## Örnek 72

geçerli bir film adı olarak kabul edilmez.

Bu bizim örneğimiz açısından güzel.

Ama burada küçük bir sınır var.

Şu değer:

```json
{
  "title": "   "
}
```

## Örnek 73

teknik olarak boş String değildir.

Yalnızca boşluk karakterlerinden oluşur.

Bu bölümde konumuzu dağıtmamak için boşluk temizleme gibi ek işlemlere girmiyoruz.

Şimdilik öğrendiğimiz kural:

```text
title hiç yok
veya boş String
↓
required başarısız
```

## Örnek 74

olsun.

---

## Mongoose Hata Verdi Ama errorHandler Ne Yapacak?

Şimdi 39. bölümde oluşturduğumuz dosyaya dönelim:

```text
src/middlewares/errorHandler.js
```

## Örnek 75

Şu anda kabaca:

```js
export const errorHandler = (
  error,
  req,
  res,
  next,
) => {
  console.error(error);

  res.status(500).json({
    message: "Sunucuda beklenmeyen bir hata oluştu.",
  });
};
```

## Örnek 76

Validation başarısız olduğunda hata buraya ulaşabilir.

Ama bu durumda:

```text
500
```

## Örnek 77

göndermek doğru bir mesaj vermez.

Çünkü:

```text
MongoDB çökmüş değil
Express çökmüş değil
sunucuda beklenmeyen bir arıza yok
```

## Örnek 78

Sorun:

```text
Request ile gönderilen veri
Schema kurallarına uymuyor
```

## Örnek 79

Bu nedenle daha uygun Response:

```text
400 Bad Request
```

## Örnek 80

olur.

Yani errorHandler artık iki farklı hata türünü ayırt etmeli.

---

## Validation Hatasını Nasıl Tanıyacağız?

Mongoose validation başarısız olduğunda oluşturduğu hatanın:

```js
error.name
```

## Örnek 81

değeri:

```text
ValidationError
```

## Örnek 82

olabilir.

Bunu kullanarak hata adını kontrol edebiliriz:

```js
if (error.name === "ValidationError") {
  return res.status(400).json({
    message: "Gönderilen film bilgileri geçersiz.",
  });
}
```

## Örnek 83

Bunu Türkçe okuyalım:

```text
hatanın adı
ValidationError mı?
↓
evet
↓
gönderilen veri geçersiz
```

## Örnek 84

---

## errorHandler'ı Validation İçin Güncelleyelim

Dosya:

```text
src/middlewares/errorHandler.js
```

## Örnek 85

Yeni hâli:

```js
export const errorHandler = (
  error,
  req,
  res,
  next,
) => {
  console.error(error);

  if (error.name === "ValidationError") {
    return res.status(400).json({
      message: "Gönderilen film bilgileri geçersiz.",
    });
  }

  res.status(500).json({
    message: "Sunucuda beklenmeyen bir hata oluştu.",
  });
};
```

## Örnek 86

Şimdi akışımız:

```text
hata geldi
↓
ValidationError mı?
```

## Örnek 87

Eğer:

```text
evet
```

## Örnek 88

ise:

```text
400
↓
"Gönderilen film bilgileri geçersiz."
```

## Örnek 89

Eğer başka, beklenmeyen bir hata ise:

```text
500
↓
"Sunucuda beklenmeyen bir hata oluştu."
```

## Örnek 90

Bu ayrım çok önemli.

---

## 400 ile 500 Arasındaki Fark Artık Daha Net

Şöyle düşünün:

```text
KULLANICI GEÇERSİZ VERİ GÖNDERDİ
↓
400
```

## Örnek 91

ama:

```text
SUNUCUDA BEKLENMEYEN TEKNİK HATA
↓
500
```

## Örnek 92

Örneğin:

```json
{
  "voteAverage": 25
}
```

## Örnek 93

Schema kuralına uymuyorsa:

```text
400
```

## Örnek 94

Ama veritabanı bağlantısı beklenmeyen şekilde koparsa:

```text
500
```

## Örnek 95

Bu ikisini aynı Response ile karşılamak istemeyiz.

---

## Kullanıcı Hangi Alanın Hatalı Olduğunu Nasıl Anlayacak?

Şu an Response'umuz:

```json
{
  "message": "Gönderilen film bilgileri geçersiz."
}
```

## Örnek 96

Bu doğru.

Ama biraz genel.

Kullanıcı şunu sorabilir:

> Peki hangi bilgi geçersiz?

Mongoose validation hatasının içinde alanlara ait hata bilgileri de bulunur.

Örneğin:

```js
error.errors
```

## Örnek 97

içinde:

```text
title
releaseYear
voteAverage
```

## Örnek 98

gibi hata alanları bulunabilir.

Fakat önce Schema'daki hata mesajlarını bizim belirlememiz daha anlaşılır olacak.

---

## Validator'lara Kendi Hata Mesajımızı Verebilir miyiz?

Evet.

Örneğin:

```js
required: true
```

## Örnek 99

yerine:

```js
required: [
  true,
  "Film adı zorunludur.",
]
```

## Örnek 100

yazabiliriz.

Burada:

```text
true
↓
kural aktif
```

## Örnek 101



```text
"Film adı zorunludur."
↓
kural bozulursa hata mesajı
```

## Örnek 102

anlamına gelir.

Aynı şeyi `min` ve `max` için de yapabiliriz. Mongoose hazır validator'larda özel hata mesajlarını destekler.

---

## Schema'yı Daha Anlaşılır Hata Mesajlarıyla Güncelleyelim

Dosya:

```text
src/db/models/movie.js
```

## Örnek 103

Schema:

```js
const movieSchema = new mongoose.Schema({
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
});
```

## Örnek 104

Şimdi kurallar yalnızca makineye değil, bize de daha açık konuşuyor.

Örneğin:

```text
voteAverage = 25
↓
max kuralı bozuldu
↓
"Film puanı 10'dan büyük olamaz."
```

## Örnek 105

---

## Hata Mesajlarını Response'a Ekleyelim

Validation hatasında:

```js
error.errors
```

## Örnek 106

alanında her hatalı Schema alanına ait bilgiler bulunabilir.

Biz yalnızca mesajları almak istiyoruz.

Bunun için:

```js
Object.values(error.errors)
```

## Örnek 107

ile hata nesnelerini bir liste hâlinde alabiliriz.

Ardından:

```js
.map((item) => item.message)
```

## Örnek 108

ile her birinin yalnızca mesajını seçebiliriz.

İlk bakışta uzun görünüyorsa iki adımda okuyun:

```text
error.errors
↓
alanlara ait hatalar
```

## Örnek 109



```text
Object.values(...)
↓
hataları listeye çevir
```

## Örnek 110



```text
map(...)
↓
her hatadan message değerini al
```

## Örnek 111

---

## errorHandler'ın Daha Kullanışlı Hâli

Dosya:

```text
src/middlewares/errorHandler.js
```

## Örnek 112



```js
export const errorHandler = (
  error,
  req,
  res,
  next,
) => {
  console.error(error);

  if (error.name === "ValidationError") {
    const errors = Object.values(
      error.errors,
    ).map((item) => item.message);

    return res.status(400).json({
      message: "Gönderilen film bilgileri geçersiz.",
      errors,
    });
  }

  res.status(500).json({
    message: "Sunucuda beklenmeyen bir hata oluştu.",
  });
};
```

## Örnek 113

Şimdi kötü Request için Response kabaca şöyle olabilir:

```json
{
  "message": "Gönderilen film bilgileri geçersiz.",
  "errors": [
    "Film adı zorunludur.",
    "Film yılı 1888'den küçük olamaz.",
    "Film puanı 10'dan büyük olamaz."
  ]
}
```

## Örnek 114

Artık istemci yalnızca:

```text
veri yanlış
```

## Örnek 115

demekle kalmıyor.

Neyin yanlış olduğunu da görebiliyor.

---

## Baştan Sona POST Akışını İzleyelim

Request:

```http
POST /movies
```

## Örnek 116

Body:

```json
{
  "title": "",
  "releaseYear": 1800,
  "voteAverage": 25
}
```

## Örnek 117

Akış:

```text
POST /movies
↓
express.json()
↓
Router
↓
createMovie
↓
new Movie(...)
↓
movie.save()
↓
Mongoose validation
```

## Örnek 118

Schema kontrolü:

```text
title
↓
required başarısız

releaseYear
↓
min başarısız

voteAverage
↓
max başarısız
```

## Örnek 119

Sonra:

```text
save() Promise'i reddedildi
↓
Express 5 hata akışı
↓
errorHandler
↓
ValidationError
↓
400 Response
```

## Örnek 120

MongoDB tarafında:

```text
kayıt oluşmaz
```

## Örnek 121

İşte artık iki bölüm birbirine bağlandı:

```text
Mongoose
↓
veriyi koruyor

Express
↓
validation hatasını doğru Response'a çeviriyor
```

## Örnek 122

---

## Geçerli Bir Film Gönderirsek Ne Olur?

Request:

```http
POST /movies
```

## Örnek 123

Body:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3
}
```

## Örnek 124

Kontroller:

```text
title var
↓
geçerli

releaseYear >= 1888
↓
geçerli

0 <= voteAverage <= 10
↓
geçerli
```

## Örnek 125

Sonuç:

```text
validation başarılı
↓
save()
↓
MongoDB
↓
201 Created
```

## Örnek 126

Yani validation yalnızca hata üretmek için yoktur.

Asıl görevi:

```text
MongoDB'ye gitmeden önce
Document'ın kurallara uygun olduğundan
emin olmak
```

## Örnek 127

şeklinde düşünülebilir.

---

## PATCH /movies/:movieId İçin Validation Çalışacak mı?

Burada 35. bölümde verdiğimiz bir kararın faydasını görüyoruz.

Güncelleme akışımız:

```text
findById()
↓
Document'ı değiştir
↓
save()
```

## Örnek 128

şeklindeydi.

Örneğin:

```js
movie.voteAverage = voteAverage;

await movie.save();
```

## Örnek 129

kullanıyoruz.

`save()` çağrıldığı için Mongoose Document validation kurallarını yine çalıştırır.

Yani kullanıcı:

```json
{
  "voteAverage": 25
}
```

## Örnek 130

gönderirse:

```text
Document bulundu
↓
voteAverage = 25
↓
save()
↓
max: 10 kontrolü
↓
validation başarısız
↓
400 Response
```

## Örnek 131

olabilir.

Bu güzel bir sonuç.

Çünkü aynı Schema kuralları hem:

```text
yeni film oluştururken
```

## Örnek 132

hem de:

```text
Document'ı save() ile güncellerken
```

## Örnek 133

bizi korur.

---

## Doğrudan Update Sorgularında Küçük Bir Fark

Bizim ana PATCH Route'umuz Document'ı önce buluyor, alanlarını değiştiriyor ve ardından:

```js
await movie.save();
```

## Örnek 134

çağrısını kullanıyor. `save()` sırasında Schema validation kuralları otomatik olarak çalışır.

Mongoose'un doğrudan update sorgularında ise validation davranışı farklıdır. Update validator'ları varsayılan olarak kapalıdır ve gerektiğinde:

```js
{ runValidators: true }
```

## Örnek 135

seçeneğiyle etkinleştirilebilir.

Şimdilik bilmemiz gereken ayrım şudur:

```text
Document.save()
↓
validation otomatik çalışır

doğrudan update sorgusu
↓
update validator'ları ayrıca etkinleştirmek gerekebilir
```

## Örnek 136

Ana PATCH Route'umuz `save()` kullandığı için mevcut kodumuza `runValidators: true` eklemiyoruz. Doğrudan update sorgularını, update operatörlerini ve bu sorguların validation ayrıntılarını 53. bölümde ayrıca ele alacağız.

## required Her Alan İçin Zorunlu mu?

Hayır.

Şu an:

```js
title: {
  type: String,
  required: true,
}
```

## Örnek 137

diyoruz.

Ama:

```js
releaseYear
```

## Örnek 138

ve:

```js
voteAverage
```

## Örnek 139

alanlarına `required` koymadık.

Bu durumda bu alanlar hiç gönderilmezse yalnızca:

```text
min
max
```

## Örnek 140

validator'ları:

```text
undefined
```

## Örnek 141

değer üzerinde hata oluşturmaz.

Mongoose'da genel olarak validator'lar `undefined` değerlerde çalışmaz; bunun önemli istisnası `required` validator'ıdır.

Yani:

```text
alan mutlaka bulunmalı
↓
required
```

## Örnek 142

başka bir ihtiyaçtır.

```text
alan varsa belli sınırlar içinde olmalı
↓
min / max
```

## Örnek 143

başka bir ihtiyaçtır.

Bu ayrımı unutmayalım.

---

## Alan Gönderilmezse undefined Kalmasını İstemiyorsak Ne Olur?

Az önce önemli bir şey gördük:

```text
required yok
+
alan gönderilmedi
↓
değer undefined olabilir
```

## Örnek 144

Peki biz bazı alanlarda:

```text
undefined
```

## Örnek 145

yerine başlangıçtan itibaren belirli bir değer olmasını istersek?

Mongoose bunun için:

```js
default
```

## Örnek 146

seçeneğini sunar.

Burada önemli bir ayrım yapalım:

```text
default
↓
validator değildir
```

## Örnek 147

`required`, `min`, `max` gibi kurallar:

```text
Bu değer kabul edilebilir mi?
```

## Örnek 148

diye sorar.

`default` ise:

```text
Bu alan için değer gelmediyse
hangi başlangıç değerini kullanayım?
```

## Örnek 149

diye sorar.

---

### Örneğin Boş String İstiyorsak

Diyelim ki ileride Movie Schema'mıza isteğe bağlı bir:

```text
description
```

## Örnek 150

alanı ekledik.

Kullanıcı bu alanı göndermediğinde:

```js
description: {
  type: String,
}
```

## Örnek 151

yapısında Document üzerindeki değer:

```text
undefined
```

## Örnek 152

olabilir ve alan kayıtta hiç bulunmayabilir.

Ama uygulamamızın kuralı:

```text
description gönderilmediyse
boş String kullan
```

## Örnek 153

ise şöyle yazabiliriz:

```js
description: {
  type: String,
  default: "",
}
```

## Örnek 154

Artık:

```js
const movie = new Movie({
  title: "Dune",
});
```

## Örnek 155

oluşturduğumuzu düşünün.

`description` göndermedik.

Mongoose:

```text
description
↓
undefined
↓
default devreye girer
↓
""
```

## Örnek 156

şeklinde davranır.

Yani:

```js
movie.description
```

## Örnek 157

değeri:

```text
""
```

## Örnek 158

olur.

---

### default Tam Olarak Ne Zaman Çalışır?

Burada küçük ama çok önemli bir kural var.

Mongoose `default` değerini yalnızca alanın değeri gerçekten:

```js
undefined
```

## Örnek 159

olduğunda uygular.

Örneğin:

```js
new Movie({
  description: undefined,
});
```

## Örnek 160

için:

```js
default: ""
```

## Örnek 161

devreye girebilir.

Ama kullanıcı açıkça:

```js
description: null
```

## Örnek 162

gönderirse `default` bunu değiştirmez.

Aynı şekilde kullanıcı zaten:

```js
description: ""
```

## Örnek 163

gönderdiyse de default çalışmaz.

Kısaca:

```text
undefined
↓
default çalışır


null
↓
default çalışmaz


""
↓
default çalışmaz
```

## Örnek 164

Bu yüzden `default`:

```text
boş olan her şeyi otomatik düzelt
```

## Örnek 165

anlamına gelmez.

Sadece:

```text
değer hiç yoksa
bir başlangıç değeri koy
```

## Örnek 166

mantığıdır.

---

### Her undefined Alan İçin default Yazmalı mıyız?

Hayır.

Burada yine uygulamamızın anlamını düşünmeliyiz.

Örneğin:

```text
voteAverage yok
```

## Örnek 167

durumuna otomatik:

```js
default: 0
```

## Örnek 168

vermek teknik olarak mümkündür.

Ama bu kez:

```text
henüz puan bilgisi yok
```

## Örnek 169

ile:

```text
filmin puanı gerçekten 0
```

## Örnek 170

aynı anlama gelebilir.

Bu veri açısından yanıltıcı olabilir.

O yüzden:

```text
undefined görmek istemiyorum
```

## Örnek 171

diye her alana rastgele default vermek doğru değildir.

Daha doğru soru:

> Bu alan gönderilmediğinde uygulamam için gerçekten anlamlı başlangıç değeri nedir?

Örneğin isteğe bağlı bir açıklama alanında:

```js
default: ""
```

## Örnek 172

anlamlı olabilir.

Başka bir alanda:

```text
alanın hiç bulunmaması
```

## Örnek 173

daha doğru olabilir.

Yani yine aynı ilke:

```text
önce veri anlamı
↓
sonra Schema seçeneği
```

## Örnek 174

---

## Peki releaseYear ve voteAverage da required Olmalı mı?

Bu artık teknik bir zorunluluktan çok:

```text
uygulamamızın veri kuralı
```

## Örnek 175

sorusudur.

Şunu sormalıyız:

```text
Bir film releaseYear olmadan kaydedilebilir mi?

Bir film voteAverage olmadan kaydedilebilir mi?
```

## Örnek 176

Eğer uygulama kuralımız:

```text
hayır
```

## Örnek 177

derse bu alanlara da:

```js
required: true
```

## Örnek 178

ekleyebiliriz.

Ama Schema'ya kural eklerken:

```text
Mongoose böyle yapabiliyor
```

## Örnek 179

diye değil,

```text
uygulamamız gerçekten bunu gerektiriyor
```

## Örnek 180

diye karar vermeliyiz.

Bu yüzden bu bölümde mevcut yapımızı koruyoruz:

```text
title
↓
zorunlu

releaseYear
↓
varsa en az 1888

voteAverage
↓
varsa 0–10 arasında
```

## Örnek 181

---

## enum Ne Zaman İşimize Yarar?

Şu ana kadar gerçekten ihtiyaç duyduğumuz validator'ları kullandık:

```text
required
min
max
```

## Örnek 182

Bir başka sık karşılaşacağınız validator:

```js
enum
```

## Örnek 183

olacaktır.

Şu anda Movie Schema'mızdaki:

```text
title
releaseYear
voteAverage
```

## Örnek 184

alanlarında `enum` kullanmamızı gerektiren doğal bir ihtiyaç yok.

Bu yüzden sırf göstermek için Movie Schema'mıza eklemeyeceğiz.

Ama `enum`un ne yaptığını bilmek önemli.

---

### enum Ne Demek?

Bazen bir String alanına:

```text
her String değeri
```

## Örnek 185

gelmesini istemeyiz.

Yalnızca önceden belirlediğimiz birkaç seçenek kabul edilsin isteriz.

Örneğin bir kullanıcı Schema'sında:

```text
gender
```

## Örnek 186

alanımız olduğunu düşünelim.

Bu alan için yalnızca:

```text
erkek
kadın
belirtmek istemiyorum
```

## Örnek 187

değerlerini kabul etmek istiyorsak:

```js
gender: {
  type: String,
  enum: [
    "erkek",
    "kadın",
    "belirtmek istemiyorum",
  ],
}
```

## Örnek 188

yazabiliriz.

Buradaki:

```js
enum
```

## Örnek 189

Mongoose'a şunu söyler:

```text
gender değeri
↓
yalnızca bu listedeki
değerlerden biri olabilir
```

## Örnek 190

Örneğin:

```json
{
  "gender": "kadın"
}
```

## Örnek 191

geçerlidir.

Ama izin verilen listede bulunmayan bir değer gönderilirse:

```json
{
  "gender": "bilinmeyen-değer"
}
```

## Örnek 192

`enum` validation başarısız olur.

Akış:

```text
gender geldi
↓
enum listesinde var mı?
↓
evet
↓
geçerli
```

## Örnek 193

veya:

```text
gender geldi
↓
enum listesinde var mı?
↓
hayır
↓
ValidationError
```

## Örnek 194

---

### enum İçin Kendi Hata Mesajımızı da Verebiliriz

Daha önce `required`, `min` ve `max` için kendi hata mesajlarımızı yazmıştık.

`enum` için de aynı fikri kullanabiliriz:

```js
gender: {
  type: String,
  enum: {
    values: [
      "erkek",
      "kadın",
      "belirtmek istemiyorum",
    ],
    message:
      "Cinsiyet için izin verilen değerlerden birini gönderin.",
  },
}
```

## Örnek 195

Böylece listede olmayan bir değer geldiğinde daha anlaşılır bir validation mesajı üretebiliriz.

---

### enum'u Nerelerde Görebiliriz?

`enum` yalnızca cinsiyet gibi alanlarda kullanılmaz.

Mantığı şudur:

```text
seçenekler önceden belli
↓
başka değer istemiyorum
↓
enum
```

## Örnek 196

Örneğin:

```text
sipariş durumu
↓
hazırlanıyor
kargoda
teslim edildi
```

## Örnek 197

veya:

```text
kullanıcı rolü
↓
user
admin
```

## Örnek 198

gibi alanlarda da kullanılabilir.

Yani `enum`u gördüğünüzde aklınıza şu gelsin:

> Bu alan için izin verilen değerlerin bir listesi var.

Movie Schema'mızda bugün buna ihtiyacımız olmadığı için kullanmıyoruz.

Ama ileride böyle bir ihtiyaç doğarsa hangi validator'a başvuracağımızı artık biliyoruz.

---

## Mongoose'un Başka Hazır Validator'ları da Var mı?

Evet.

String alanlarında örneğin:

```text
match
minLength
maxLength
```

## Örnek 199

gibi başka hazır validator'lar da vardır.

Fakat hepsini sırf listeyi tamamlamak için kullanmayacağız.

Kuralımız değişmiyor:

```text
önce ihtiyaç
↓
sonra validator
```

## Örnek 200

---

## Validation ile Type Aynı Şey Değil

Bir kez daha yan yana görelim.

Şu veri:

```json
{
  "voteAverage": 25
}
```

## Örnek 201

için:

```text
25
↓
Number
```

## Örnek 202

Yani `type: Number` açısından uygun.

Ama:

```text
max: 10
```

## Örnek 203

kuralına göre uygun değil.

Dolayısıyla:

```text
TYPE
↓
Bu veri hangi tür?
```

## Örnek 204



```text
VALIDATION
↓
Bu değer kurala uygun mu?
```

## Örnek 205

Bu ikisini birbirine karıştırmayalım.

---

## Schema Validation ile Controller Kontrolünün Sınırı

Controller ve Schema farklı türde kontroller yapabilir.

Controller'daki şu kontrol:

```js
if (!mongoose.isObjectIdOrHexString(movieId)) {
  return res.status(400).json({
    message: "Geçersiz film kimliği.",
  });
}
```

## Örnek 206

Request'ten gelen route parametresinin biçimini denetler. Schema validation ise `title`, `releaseYear` veya `voteAverage` gibi Movie Document alanlarının kurallarını uygular.

```text
Request / route parametresi
↓
Controller veya middleware kontrolü

Document alanları
↓
Mongoose Schema validation
```

## Örnek 207

## Movie Model Dosyasının Güncel Hâli

Dosya:

```text
src/db/models/movie.js
```

## Örnek 208



```js
import mongoose from "mongoose";

const movieSchema = new mongoose.Schema({
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
});

export const Movie =
  mongoose.models.Movie ||
  mongoose.model("Movie", movieSchema);
```

## Örnek 209

Dosyada yeni klasör yok.

Yeni katman yok.

Yeni yardımcı yapı yok.

Yalnızca Movie verisinin kendi kuralları daha açık hâle geldi.

---

## errorHandler.js Dosyasının Güncel Hâli

Dosya:

```text
src/middlewares/errorHandler.js
```

## Örnek 210



```js
export const errorHandler = (
  error,
  req,
  res,
  next,
) => {
  console.error(error);

  if (error.name === "ValidationError") {
    const errors = Object.values(
      error.errors,
    ).map((item) => item.message);

    return res.status(400).json({
      message: "Gönderilen film bilgileri geçersiz.",
      errors,
    });
  }

  res.status(500).json({
    message: "Sunucuda beklenmeyen bir hata oluştu.",
  });
};
```

## Örnek 211

Burada da 39. bölümde kurduğumuz yapı bozulmadı.

Sadece errorHandler artık:

```text
her hata = 500
```

## Örnek 212

demiyor.

Önce soruyor:

```text
ValidationError mı?
```

## Örnek 213

---

## Projemizin Hata Haritası Biraz Daha Zenginleşti

Artık:

```text
movieId biçimi yanlış
↓
Controller
↓
400
```

## Örnek 214



```text
film bulunamadı
↓
Controller
↓
404
```

## Örnek 215



```text
Movie verisi Schema kurallarına uymuyor
↓
Mongoose ValidationError
↓
errorHandler
↓
400
```

## Örnek 216



```text
beklenmeyen teknik hata
↓
errorHandler
↓
500
```

## Örnek 217

şeklinde daha anlamlı bir yapımız var.

Bir önceki bölümde kurduğumuz merkezi hata yönetimi şimdi gerçekten kullanılmaya başladı.

---

## 🧠 Akılda Kalsın

Validation:

```text
veriyi
kurallara göre
kontrol etmek
```

## Örnek 218

Schema'daki:

```js
required
```

## Örnek 219

alanın zorunlu olup olmadığını kontrol edebilir.

Number alanlarında:

```js
min
max
```

## Örnek 220

değer sınırlarını kontrol edebilir.

Bir alan `undefined` kaldığında anlamlı bir başlangıç değeri vermek istiyorsak:

```js
default
```

## Örnek 221

kullanabiliriz.

Ama:

```text
default
↓
validator değildir
```

## Örnek 222

ve yalnızca değer gerçekten `undefined` olduğunda devreye girer.

Bir String alanında yalnızca belirli seçeneklere izin vermek istiyorsak:

```js
enum
```

## Örnek 223

kullanabiliriz.

`save()`:

```text
validation
↓
başarılı
↓
MongoDB
```

## Örnek 224

veya:

```text
validation
↓
başarısız
↓
save() reddedilir
↓
Express hata akışı
```

## Örnek 225

şeklinde ilerleyebilir.

Bizim PATCH akışımız:

```text
findById()
↓
Document'ı değiştir
↓
save()
```

## Örnek 226

olduğu için validation otomatik çalışır.

Doğrudan update sorgularında ise:

```js
runValidators: true
```

## Örnek 227

gerekebilir.

Ve artık:

```text
ValidationError
↓
400
```

## Örnek 228

ile:

```text
beklenmeyen sunucu hatası
↓
500
```

## Örnek 229

arasındaki farkı ayırabiliyoruz.

---

## Bölüm Özeti

Bu bölüme şu soruyla başladık:

> Kod çalışıyor ama gelen veri mantıksızsa ne olacak?

Önce:

```js
required: true
```

## Örnek 230

satırının aslında daha önce kullandığımız ilk validator'lardan biri olduğunu fark ettik.

Sonra Movie Schema'mıza:

```js
min
```

## Örnek 231

ve:

```js
max
```

## Örnek 232

kuralları ekledik.

`required` olmayan bir alan gönderilmediğinde değerin `undefined` kalabileceğini; eğer uygulamamız için anlamlıysa:

```js
default
```

## Örnek 233

ile başlangıç değeri tanımlayabileceğimizi gördük.

Örneğin isteğe bağlı bir String alanında:

```js
default: ""
```

## Örnek 234

kullanılabilir.

Ama `default`ın yalnızca `undefined` için çalıştığını ve bir validator olmadığını ayırdık.

Ayrıca `enum` validator'ını öğrendik:

```text
yalnızca önceden belirlenmiş
değerlerden biri kabul edilsin
↓
enum
```

## Örnek 235

Schema'mız artık yalnızca alan türlerini değil:

```text
hangi değerlerin kabul edilebilir olduğunu
```

## Örnek 236

da anlatmaya başladı.

`save()` sırasında Mongoose validation'ın otomatik çalıştığını gördük.

Validation başarısız olduğunda:

```text
save()
↓
Promise reddedilir
↓
Express 5 hata akışı
↓
errorHandler
```

## Örnek 237

bağlantısını kurduk.

Sonra merkezi hata yönetimimizi biraz geliştirdik:

```text
ValidationError
↓
400
```

## Örnek 238

ve:

```text
diğer beklenmeyen hatalar
↓
500
```

## Örnek 239

olarak ayırdık.

Validator'lara kendi hata mesajlarımızı ekledik ve Response içinde hangi alan kurallarının bozulduğunu gösterebildik.

Ayrıca:

```text
Document.save()
```

## Örnek 240

ile:

```text
doğrudan update sorguları
```

## Örnek 241

arasındaki validation farkını gördük.

Ana PATCH Route'umuz `save()` kullandığı için mevcut yapımızda ayrıca `runValidators: true` eklememize gerek olmadığını öğrendik.

---

## Sonraki Adım

Artık Movie Schema'mız:

```text
veri türünü
+
veri kurallarını
```

## Örnek 242

biliyor.

Ama yeni bir soru doğuyor.

Şu iki Request'i düşünün:

```json
{
  "title": "Dune"
}
```

## Örnek 243

ve:

```json
{
  "title": "Dune",
  "unknownField": "Bu alan nereden çıktı?"
}
```

## Örnek 244

Schema'da hiç tanımlamadığımız:

```text
unknownField
```

## Örnek 245

MongoDB'ye gider mi?

Mongoose Schema yalnızca:

```text
hangi alanların türünü ve kurallarını
```

## Örnek 246

mı belirler?

Yoksa:

```text
Schema'da olmayan alanlara
ne olacağını da
```
