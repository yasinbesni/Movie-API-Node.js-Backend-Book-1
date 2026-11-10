# Bölüm 42 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Mongoose timestamps Nedir? createdAt ve updatedAt Alanları Nasıl Otomatik Eklenir?

## Bir Film Ne Zaman Oluşturuldu?

Movie API'miz artık yalnızca film verisini kaydetmiyor.

Schema'mız:

```text
hangi alanlar var?
↓
strict
```

## Örnek 2

ve:

```text
hangi değerler geçerli?
↓
validation
```

## Örnek 3

sorularına da cevap verebiliyor.

Şimdi gerçek bir uygulamada çok sık ihtiyaç duyacağımız başka bir bilgi düşünelim.

Bir film kaydı var:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3
}
```

## Örnek 4

Peki bu kayıt:

```text
ne zaman oluşturuldu?
```

## Örnek 5

Birkaç gün sonra puanı değiştirildiğinde:

```text
en son ne zaman güncellendi?
```

## Örnek 6

Bu iki bilgi elimizde yok.

İstersek Schema'ya kendimiz iki alan ekleyebiliriz:

```text
createdAt
updatedAt
```

## Örnek 7

Ama sonra başka bir problem doğar.

Her film oluşturduğumuzda:

```text
createdAt değerini biz yazacağız
```

## Örnek 8

Her film güncellediğimizde de:

```text
updatedAt değerini biz değiştireceğiz
```

## Örnek 9

Bu iş tekrar tekrar yapılacak.

O hâlde yeni sorumuz şu:

> Mongoose bu tarihleri bizim için otomatik olarak yönetebilir mi?

Evet.

Bunun için Schema'nın:

```text
timestamps
```

## Örnek 10

seçeneğini kullanabiliriz.

---

## Önce İki Tarihin Görevini Ayıralım

`timestamps` kullanıldığında Mongoose varsayılan olarak iki alan ekler:

```text
createdAt
updatedAt
```

## Örnek 11

İsimleri birbirine benzediği için önce görevlerini ayıralım.

### createdAt

Şu soruya cevap verir:

> Bu Document ne zaman oluşturuldu?

Örneğin:

```text
createdAt
↓
2026-09-02T05:30:00.000Z
```

## Örnek 12

---

### updatedAt

Şu soruya cevap verir:

> Bu Document en son ne zaman güncellendi?

Örneğin:

```text
updatedAt
↓
2026-09-02T08:15:00.000Z
```

## Örnek 13

En kısa ayrım:

```text
createdAt
↓
oluşturulma zamanı
```

## Örnek 14



```text
updatedAt
↓
son güncellenme zamanı
```

## Örnek 15

---

## Bunları Schema'ya Tek Tek Yazmak Zorunda mıyız?

Hayır.

Şöyle yazabilirdik:

```js
createdAt: {
  type: Date,
},
updatedAt: {
  type: Date,
},
```

## Örnek 16

Ama bu yalnızca alanları tanımlar.

Tarihleri:

```text
ne zaman oluşturacağımızı
```

## Örnek 17

ve:

```text
ne zaman değiştireceğimizi
```

## Örnek 18

yine bizim yönetmemiz gerekirdi.

Mongoose bunun yerine tek bir Schema seçeneği sunar:

```js
timestamps: true
```

## Örnek 19

---

## timestamps: true Nereye Yazılır?

Movie Schema'mızda Document alanlarını Schema constructor'ının ilk nesnesinde tanımlıyoruz.

Örneğin:

```js
const movieSchema = new mongoose.Schema({
  title: {
    type: String,
  },
});
```

## Örnek 20

`timestamps` bir film alanı değildir.

Yani:

```text
title
releaseYear
voteAverage
```

## Örnek 21

ile aynı yere yazılan yeni bir Movie bilgisi değildir.

Bu, Schema'nın **davranış ayarıdır**.

Bu yüzden Schema constructor'ının ikinci nesnesine yazarız:

```js
const movieSchema = new mongoose.Schema(
  {
    title: {
      type: String,
    },
  },
  {
    timestamps: true,
  },
);
```

## Örnek 22

Bunu şöyle okuyabiliriz:

```text
ilk nesne
↓
Document alanları

ikinci nesne
↓
Schema seçenekleri
```

## Örnek 23

41. bölümde `strict` seçeneklerini incelerken de aynı ikinci nesne yapısını görmüştük.

---

## Movie Schema'mıza timestamps Ekleyelim

Çalıştığımız dosya:

```text
src/db/models/movie.js
```

## Örnek 24

Güncel Schema'mız:

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

export const Movie =
  mongoose.models.Movie ||
  mongoose.model("Movie", movieSchema);
```

## Örnek 25

Eklediğimiz tek bölüm:

```js
{
  timestamps: true,
}
```

## Örnek 26

oldu.

Ama bunun sonucunda Mongoose artık iki alanı bizim için yönetebilir:

```text
createdAt
updatedAt
```

## Örnek 27

---

## Yeni Bir Film Oluşturduğumuzda Ne Olur?

Controller'ımızı değiştirmiyoruz.

Hâlâ:

```js
const movie = new Movie({
  title,
  releaseYear,
  voteAverage,
});

await movie.save();
```

## Örnek 28

yazıyoruz.

Dikkat edin.

Controller'da:

```text
createdAt yok
updatedAt yok
```

## Örnek 29

Buna rağmen `timestamps: true` sayesinde kayıt oluşturulduğunda Mongoose bu alanları ekler.

MongoDB'deki Document kabaca şöyle görünebilir:

```json
{
  "_id": "...",
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3,
  "createdAt": "2026-09-02T05:30:00.000Z",
  "updatedAt": "2026-09-02T05:30:00.000Z"
}
```

## Örnek 30

Bir şey dikkatinizi çekti mi?

İlk oluşturma anında:

```text
createdAt
↓
aynı zaman

updatedAt
↓
aynı zaman
```

## Örnek 31

gibi görünebilir.

Bu normaldir.

Çünkü Document:

```text
az önce oluşturuldu
```

## Örnek 32

ve:

```text
en son da az önce değişti
```

## Örnek 33

---

## İlk Kayıtta Neden updatedAt da Var?

İlk bakışta:

> Daha yeni oluşturduk, ne zaman güncellendi ki?

diye düşünülebilir.

Ama yeni Document oluşturulurken o Document'ın mevcut hâli de ilk kez yazılmış olur.

Bu yüzden başlangıçta:

```text
createdAt
↓
oluşturulduğu an

updatedAt
↓
son değişiklik anı
```

## Örnek 34

aynı zamana işaret edebilir.

Daha sonra Document değiştiğinde fark ortaya çıkacaktır.

---

## Bir Filmi Güncellersek Ne Olur?

PATCH Route'umuzda şu yaklaşımı kullanıyoruz:

```text
findById()
↓
Document'ı değiştir
↓
save()
```

## Örnek 35

Örneğin:

```js
movie.voteAverage = 8.5;

await movie.save();
```

## Örnek 36

Şimdi Mongoose:

```text
createdAt
↓
ilk oluşturulduğu zamanı korur
```

## Örnek 37

ama:

```text
updatedAt
↓
yeni güncelleme zamanına geçer
```

## Örnek 38

Örneğin önce:

```text
createdAt → 10:00
updatedAt → 10:00
```

## Örnek 39

olsun.

Filmi 12:30'da güncelledik.

Sonra:

```text
createdAt → 10:00
updatedAt → 12:30
```

## Örnek 40

olabilir.

Yani:

```text
createdAt
↓
geçmişi anlatır
↓
Document ilk ne zaman oluştu?
```

## Örnek 41



```text
updatedAt
↓
değişebilir
↓
Document en son ne zaman güncellendi?
```

## Örnek 42

---

## createdAt Neden Değişmiyor?

Çünkü amacı:

```text
Document'ın doğum anını
```

## Örnek 43

saklamaktır.

Bir filmin puanını değiştirdiğimizde:

```text
film kaydı yeniden doğmuş olmaz
```

## Örnek 44

Aynı Document üzerinde güncelleme yapılmıştır.

Bu yüzden:

```text
createdAt
↓
korunur
```

## Örnek 45

`updatedAt` ise:

```text
yeni güncelleme oldu
↓
yenilenir
```

## Örnek 46

---

## Akışı Baştan Sona Görelim

Yeni film:

```text
POST /movies
↓
new Movie(...)
↓
save()
↓
Mongoose timestamps
↓
createdAt oluştur
↓
updatedAt oluştur
↓
MongoDB
```

## Örnek 47

Daha sonra güncelleme:

```text
PATCH /movies/:movieId
↓
findById()
↓
Document bulundu
↓
alan değişti
↓
save()
↓
createdAt aynı kalır
↓
updatedAt güncellenir
```

## Örnek 48

Bu sayede Controller'larda ayrıca:

```js
movie.updatedAt = new Date();
```

## Örnek 49

gibi kodlar yazmamıza gerek kalmaz.

---

## Tarihlerin Türü Nedir?

Mongoose:

```text
createdAt
updatedAt
```

## Örnek 50

alanlarını varsayılan olarak:

```text
Date
```

## Örnek 51

türünde oluşturur.

Yani Schema açısından kabaca:

```text
createdAt → Date
updatedAt → Date
```

## Örnek 52

gibi düşünebiliriz.

API Response içinde ise bunları çoğu zaman:

```json
{
  "createdAt": "2026-09-02T05:30:00.000Z"
}
```

## Örnek 53

gibi String'e benzeyen bir biçimde görebiliriz.

Burada 42 öncesindeki ObjectId hatasına benzer bir tuzağa düşmeyelim:

```text
JSON'da String gibi görünüyor
↓
veritabanındaki tür mutlaka String
```

## Örnek 54

demek değildir.

JSON'a dönüştürüldüğünde Date değeri metinsel bir tarih gösterimiyle karşımıza çıkabilir.

---

## Sondaki Z Harfi Ne?

Şuna benzer bir tarih görebiliriz:

```text
2026-09-02T05:30:00.000Z
```

## Örnek 55

Buradaki:

```text
Z
```

## Örnek 56

zamanın UTC olarak gösterildiğini ifade eder.

Bu yüzden bilgisayarınızdaki yerel saat ile MongoDB veya API Response'ta gördüğünüz saat arasında fark görebilirsiniz.

Örneğin Türkiye saati UTC'den ileride olduğu için:

```text
API'deki UTC zamanı
```

## Örnek 57

ile:

```text
yerel saat
```

## Örnek 58

aynı saat rakamlarını göstermeyebilir.

Bu:

```text
timestamps yanlış çalıştı
```

## Örnek 59

anlamına gelmez.

Tarihler uygulama ekranında gösterilirken gerektiğinde kullanıcının yerel saatine dönüştürülebilir.

Bu bölümde tarih biçimlendirmesine girmiyoruz.

Şimdilik:

```text
Date saklanır
↓
JSON'da ISO tarih metni gibi görünebilir
```

## Örnek 60

fikri yeterli.

---

## createdAt Değerini Kendimiz Değiştirmeli miyiz?

Normal uygulama akışında hayır.

`createdAt`:

```text
Document ilk ne zaman oluşturuldu?
```

## Örnek 61

bilgisini taşır.

Mongoose timestamps kullanırken bu alanı kendi yönetir.

Örneğin bir film güncellenirken:

```text
createdAt
```

## Örnek 62

değerinin değişmesini istemeyiz.

Mongoose da normal `save()` ve yaygın update işlemlerinde `createdAt` değerini korumaya çalışır.

Bizim için en sağlıklı yaklaşım:

```text
createdAt
updatedAt
↓
Mongoose yönetsin
```

## Örnek 63

olacaktır.

---

## updatedAt Değerini Elle Yazmalı mıyız?

Normal durumda buna da gerek yok.

Şöyle bir kod yazmak yerine:

```js
movie.updatedAt = new Date();
```

## Örnek 64

sadece:

```js
await movie.save();
```

## Örnek 65

yazabiliriz.

`timestamps: true` açıksa Mongoose güncelleme zamanını yönetir.

Bu bize iki fayda sağlar:

```text
tekrar eden kod azalır
```

## Örnek 66

ve:

```text
bir Route'ta tarihi güncellemeyi
unutma riski azalır
```

## Örnek 67

---

## save() Dışında da updatedAt Güncellenir mi?

Evet.

Mongoose timestamps yalnızca:

```js
save()
```

## Örnek 68

ile sınırlı değildir.

Örneğin Mongoose'un çeşitli update işlemlerinde de `updatedAt` otomatik olarak yönetilebilir.

Bunlara örnek:

```text
updateOne()
updateMany()
findOneAndUpdate()
```

## Örnek 69

verilebilir.

Bu bizim için güzel bir bağlantı.

35. bölümde farklı güncelleme yöntemlerini görmüştük.

Yani ileride:

```js
findOneAndUpdate(...)
```

## Örnek 70

gibi bir yöntem kullansak da `timestamps: true` aktif olduğunda Mongoose `updatedAt` alanını yönetebilir.

Bizim mevcut PATCH Route'umuz ise zaten:

```text
Document
↓
save()
```

## Örnek 71

kullandığı için davranışı çok kolay gözlemleyebiliriz.

---

## Peki Her save() Çağrısı updatedAt'i Değiştirir mi?

Başlangıç seviyesinde şu genel davranışı aklımızda tutalım:

```text
Document üzerinde güncelleme
↓
save()
↓
updatedAt güncellenir
```

## Örnek 72

Mongoose timestamps'ın amacı son güncelleme zamanını bizim yerimize takip etmektir.

İleri seviye bazı özel işlemlerde timestamps davranışı ayrıca kontrol edilebilir.

Ama şu anda buna ihtiyacımız yok.

Bizim film API'miz için:

```text
timestamps: true
↓
oluşturulma ve güncellenme zamanını
Mongoose yönetsin
```

## Örnek 73

fikri yeterli.

---

## ObjectId'de Zaman Bilgisi Varsa createdAt Neden Var?

26. bölümde ObjectId'nin içinde zaman bilgisi bulunduğunu görmüştük.

Bu yüzden akla çok doğal bir soru gelebilir:

> Madem `_id` bize bir zaman bilgisi verebiliyor, neden ayrıca `createdAt` alanı tutuyoruz?

Bunu bir örnekle ayıralım.

Şu Document'ı oluşturduğumuzu düşünün:

```js
const movie = new Movie({
  title: "Dune",
});
```

## Örnek 74

Mongoose bu Document için daha `save()` çağrılmadan önce bir `_id` oluşturabilir.

Yani ObjectId'nin içindeki zaman bize kabaca şunu söyler:

```text
_id ne zaman oluşturuldu?
```

## Örnek 75

Ama uygulamada sormak istediğimiz soru farklıdır:

```text
Bu Movie kaydı MongoDB'ye ilk kez ne zaman yazıldı?
```

## Örnek 76

İşte `createdAt` bu bilgiyi açıkça tutar.

Yan yana görelim:

```text
ObjectId içindeki zaman
↓
_id'nin oluşturulma zamanı
```

## Örnek 77



```text
createdAt
↓
kaydın MongoDB'ye ilk kez yazıldığı zaman
```

## Örnek 78

Çoğu zaman bu iki zaman birbirine çok yakın olabilir.

Ama aynı şeyi ifade etmek zorunda değildir.

Çünkü `_id`, Document MongoDB'ye kaydedilmeden önce de oluşturulabilir.

---

### Peki updatedAt Neden Ayrı?

Burada fark daha da belirginleşir.

Diyelim ki film bugün oluşturuldu:

```text
createdAt
↓
2 Eylül
```

## Örnek 79

Bir hafta sonra puanını değiştirdik:

```text
voteAverage
↓
8.3 → 8.5
```

## Örnek 80

Document'ın `_id` değeri değişmez.

Dolayısıyla ObjectId içindeki zaman bilgisi bize:

```text
Bu kayıt en son ne zaman güncellendi?
```

## Örnek 81

sorusunun cevabını vermez.

Ama `updatedAt` değişir:

```text
createdAt
↓
2 Eylül

updatedAt
↓
9 Eylül
```

## Örnek 82

Bu yüzden üç bilgiyi şöyle ayırmak daha kolaydır:

```text
_id
↓
Bu Document hangisi?
```

## Örnek 83



```text
createdAt
↓
Bu Document ne zaman oluşturuldu?
```

## Örnek 84



```text
updatedAt
↓
Bu Document en son ne zaman değiştirildi?
```

## Örnek 85

Yani ObjectId'nin içinde zaman bilgisi bulunması, `createdAt` ve `updatedAt` alanlarının yaptığı işi gereksiz hâle getirmez.

---

## Alan İsimlerini Değiştirmek Mümkün mü?

Evet.

Varsayılan isimler:

```text
createdAt
updatedAt
```

## Örnek 86

olsa da Mongoose bu isimlerin özelleştirilmesine izin verir.

Örneğin teorik olarak:

```js
{
  timestamps: {
    createdAt: "created_at",
    updatedAt: "updated_at",
  },
}
```

## Örnek 87

şeklinde başka isimler kullanılabilir.

Ama bizim Movie API'mizde buna ihtiyaç yok.

Bu nedenle yaygın ve anlaşılır varsayılan isimleri kullanacağız:

```text
createdAt
updatedAt
```

## Örnek 88

Yine aynı ilke:

```text
özellik var
↓
hemen kullan
```

## Örnek 89

değil.

```text
ihtiyaç var mı?
↓
varsa kullan
```

## Örnek 90

---

## Movie Model Dosyasının Güncel Hâli

Dosya:

```text
src/db/models/movie.js
```

## Örnek 91



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

export const Movie =
  mongoose.models.Movie ||
  mongoose.model("Movie", movieSchema);
```

## Örnek 92

Bu kez proje kodumuz gerçekten değişti.

Çünkü artık gerçek bir ihtiyacımız var:

```text
Document ne zaman oluşturuldu?
Document en son ne zaman güncellendi?
```

## Örnek 93

ve `timestamps` bu ihtiyacı doğrudan çözüyor.

---

## Controller Dosyalarını Değiştirmemiz Gerekiyor mu?

Hayır.

Örneğin `createMovie` hâlâ:

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

## Örnek 94

şeklinde kalabilir.

Biz:

```js
createdAt: new Date()
```

## Örnek 95

veya:

```js
updatedAt: new Date()
```

## Örnek 96

eklemiyoruz.

Mongoose yönetiyor.

Bu da `timestamps` özelliğinin asıl rahatlığıdır.

---

## POST Response'unda Yeni Alanları Görebiliriz

Yeni film oluşturduğumuzda Response içindeki `data` artık kabaca:

```json
{
  "_id": "...",
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3,
  "createdAt": "2026-09-02T05:30:00.000Z",
  "updatedAt": "2026-09-02T05:30:00.000Z"
}
```

## Örnek 97

gibi görünebilir.

Biz Controller'a bu alanları eklemedik.

Schema seçeneği sayesinde Mongoose ekledi.

---

## PATCH Sonrasında Neyi Kontrol Edelim?

Önce bir film oluşturun.

Örneğin:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3
}
```

## Örnek 98

Response'taki:

```text
createdAt
updatedAt
```

## Örnek 99

değerlerini not edin.

Sonra aynı filmi PATCH ile güncelleyin:

```json
{
  "voteAverage": 8.5
}
```

## Örnek 100

Yeni Response'ta şunu bekleriz:

```text
createdAt
↓
aynı
```

## Örnek 101



```text
updatedAt
↓
daha yeni
```

## Örnek 102

İşte `timestamps` davranışını gözümüzle görebileceğimiz en temiz deney budur.

---

## 🧠 Akılda Kalsın

Schema'ya:

```js
{
  timestamps: true,
}
```

## Örnek 103

eklediğimizde Mongoose varsayılan olarak:

```text
createdAt
updatedAt
```

## Örnek 104

alanlarını ekler.

```text
createdAt
↓
Document ne zaman oluşturuldu?
```

## Örnek 105



```text
updatedAt
↓
Document en son ne zaman güncellendi?
```

## Örnek 106

Yeni Document oluşturulduğunda ikisi de başlangıçta aynı zamana yakın olabilir.

Daha sonra Document güncellendiğinde:

```text
createdAt
↓
korunur

updatedAt
↓
yenilenir
```

## Örnek 107

Bu alanların türü:

```text
Date
```

## Örnek 108

olur.

Ve Controller'larda tarihleri elle yönetmemize gerek kalmaz.

---

## Bölüm Özeti

Bu bölüme iki soruyla başladık:

```text
Bu film ne zaman oluşturuldu?
```

## Örnek 109

ve:

```text
En son ne zaman güncellendi?
```

## Örnek 110

Bu bilgileri Controller'larda elle yönetmenin tekrar oluşturacağını fark ettik.

Mongoose'un:

```js
timestamps: true
```

## Örnek 111

Schema seçeneğini öğrendik.

Bu seçenek sayesinde Mongoose'un:

```text
createdAt
updatedAt
```

## Örnek 112

alanlarını otomatik olarak ekleyebildiğini gördük.

Yeni bir Document oluşturulduğunda:

```text
createdAt
updatedAt
```

## Örnek 113

değerlerinin oluşturulduğunu;

Document güncellendiğinde ise:

```text
createdAt
↓
korunur

updatedAt
↓
güncellenir
```

## Örnek 114

davranışını öğrendik.

Ayrıca timestamps'ın:

```text
validation
```

## Örnek 115

veya:

```text
default
```

## Örnek 116

olmadığını ayırdık.

Son üç kavramımız artık şöyle:

```text
validation
↓
değer uygun mu?

strict
↓
alan Schema'da var mı?

timestamps
↓
Document ne zaman oluşturuldu / güncellendi?
```

## Örnek 117

---

## Sonraki Adım

Şimdi yeni oluşturduğumuz Movie Document'a bir kez daha bakalım.

Biz Schema'da:

```text
title
releaseYear
voteAverage
```

## Örnek 118

alanlarını tanımladık.

Sonra:

```js
timestamps: true
```

## Örnek 119

ile:

```text
createdAt
updatedAt
```

## Örnek 120

alanlarını ekledik.

Ama MongoDB'deki Mongoose Document'larında bazen başka bir alan daha görebiliriz:

```text
__v
```
