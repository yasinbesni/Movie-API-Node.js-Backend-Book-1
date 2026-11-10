# Bölüm 31 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Mongoose Document Nedir ve İlk Filmimizi Nasıl Kaydederiz?

## Önce Kodu Nereye Yazacağımızı Bilelim

Bir önceki bölümde `Movie` Model'imizi şu dosyada oluşturmuştuk:

```text
src/db/models/movie.js
```

## Örnek 2

Dosyanın sonunda da:

```js
export const Movie =
  mongoose.models.Movie ||
  mongoose.model("Movie", movieSchema);
```

## Örnek 3

yazmıştık.

30. bölümde bu yapının neden önce mevcut Model'i kontrol ettiğini ayrıntılı olarak gördük. Burada aynı açıklamayı tekrarlamak yerine hazır `Movie` Model'ini kullanmaya devam edeceğiz.

Yani `Movie` Model'i hazır.

Fakat şimdi önemli bir soru var:

> `new Movie(...)` kodunu nereye yazacağız?

Model dosyasının görevi:

```text
Schema'yı tanımlamak
↓
Movie Model'ini oluşturmak
↓
Movie Model'ini export etmek
```

## Örnek 4

Bu yüzden ilk filmimizi oluşturacağımız deneme kodunu model dosyasının içine yazmayacağız.

Onun yerine küçük bir dosya oluşturalım:

```text
src/scripts/createMovie.js
```

## Örnek 5

Şimdi `createMovie.js` dosyasında `Movie` Model'ini kullanmak istiyoruz.

Ama `Movie` başka bir dosyada.

O hâlde önce onu buraya getirmeliyiz:

```js
import { Movie } from "../db/models/movie.js";
```

## Örnek 6

Buradaki yolu dikkatlice okuyalım.

Çalıştığımız dosya:

```text
src/scripts/createMovie.js
```

## Örnek 7

Model dosyamız:

```text
src/db/models/movie.js
```

## Örnek 8

Bu nedenle:

```text
createMovie.js
↓
../
↓
src klasörüne bir seviye çık
↓
db/models/movie.js
```

## Örnek 9

Buradaki:

```text
../
```

## Örnek 10

bir klasör yukarı çıkmamızı sağlar.

Yani:

```text
src/scripts/createMovie.js
↓
../
↓
src
↓
db/models/movie.js
```

## Örnek 11

akışı oluşur.

Burada dosya adının büyük-küçük harf kullanımını da birebir koruyacağız.

Kitap boyunca Model dosyamız:

```text
movie.js
```

## Örnek 12

şeklinde küçük harfle adlandırılıyor.

Bu yüzden import yolumuz da:

```js
"../db/models/movie.js"
```

## Örnek 13

olmalıdır.

Özellikle Git kullanan projelerde yalnızca:

```text
Movie.js
↓
movie.js
```

## Örnek 14

şeklindeki büyük-küçük harf değişikliği bazı sistemlerde beklediğimiz gibi kaydedilmeyebilir.

Bu nedenle dosya adı ile import yolunun yazımını birebir aynı tutmak güvenli bir alışkanlıktır.

Artık bu dosyanın içinde `Movie` kullanılabilir.

Şimdi şu kodu gerçekten yazabiliriz:

```js
import { Movie } from "../db/models/movie.js";

const movie = new Movie({
  title: "Interstellar",
  releaseYear: 2014,
  voteAverage: 8.7,
});

console.log(movie.title);
```

## Örnek 15

Burada artık havada kalan hiçbir parça yok:

```text
Movie nerede tanımlandı?
↓
src/db/models/movie.js

Nasıl dışarı çıkarıldı?
↓
export

createMovie.js içine nasıl geldi?
↓
import

new Movie(...) nerede çalışıyor?
↓
src/scripts/createMovie.js
```

## Örnek 16

Şimdi terminali proje klasöründe açıp:

```bash
node src/scripts/createMovie.js
```

## Örnek 17

komutunu çalıştıralım.

Terminalde:

```text
Interstellar
```

## Örnek 18

görürüz.

Demek ki kod gerçekten çalıştı.

Interstellar artık uygulamamızın içinde var.

Ama şimdi asıl ilginç yere geldik.

---

## Interstellar Var... Ama Atlas'ta Yok

Filmimizi terminalde gördük.

Peki MongoDB Atlas'ta da var mı?

Burada küçük ama önemli bir noktayı netleştirelim.

MongoDB Atlas yalnızca cluster oluşturduğumuz bir yönetim ekranı değildir.

Atlas'ın kendi web arayüzündeki:

```text
Data Explorer
```

## Örnek 19

bölümünden tarayıcı üzerinden:

```text
database
↓
collection
↓
document
```

## Örnek 20

yapısını görebiliriz.

Yani şu aşamada verilerimizi görmek için MongoDB Compass veya MongoDB for VS Code kurmak zorunda değiliz.

O hâlde Atlas'ın `Data Explorer` bölümüne baktığımızı düşünelim.

Interstellar orada mı?

Hayır.

Biraz garip değil mi?

```text
Terminalde Interstellar var.
↓
Ama MongoDB'de Interstellar yok.
```

## Örnek 21

Demek ki:

```js
new Movie(...)
```

## Örnek 22

yazmak ile:

```text
MongoDB'ye film kaydetmek
```

## Örnek 23

aynı şey değil.

İşte Mongoose'taki `Document` kavramını tam burada anlamaya başlayacağız.

---

## `new Movie()` Yazdığımızda Ne Oluşuyor?

Önce bir önceki bölümde oluşturduğumuz yapıyı hatırlayalım.

30. bölümde:

```text
Movie
```

## Örnek 24

adında bir Model oluşturup dışarı aktarmıştık. Model'in temel ve korumalı oluşturma biçimlerini orada ayırdığımız için burada tanımı yeniden yazmayacağız.

Şimdi:

```js
new Movie({
  title: "Interstellar",
  releaseYear: 2014,
  voteAverage: 8.7,
});
```

## Örnek 25

yazdığımızda bu Model'den tek bir film oluşturuyoruz.

Akış şöyle:

```text
Movie Model
↓
new Movie(...)
↓
tek bir film
```

## Örnek 26

Mongoose bu tek film nesnesine:

```text
Document
```

## Örnek 27

der.

Yani şu değişken:

```js
const movie = new Movie({
  title: "Interstellar",
  releaseYear: 2014,
  voteAverage: 8.7,
});
```

## Örnek 28

artık bir:

```text
Mongoose Document
```

## Örnek 29

tır.

En sade hâliyle:

> **Mongoose Document, bir Model'den oluşturulan tek bir kayıt nesnesidir.**

Film platformumuzda düşünürsek:

```text
Movie
↓
Model

movie
↓
Interstellar'ı temsil eden
tek bir Mongoose Document
```

## Örnek 30

---

## Model ile Document Arasındaki Fark Ne?

Bu iki kavram birbirine çok yakın göründüğü için burada net ayıralım.

```text
Movie
↓
Model

movie
↓
Document
```

## Örnek 31

Model'i:

```text
film kayıtlarıyla çalışmamızı
sağlayan yapı
```

## Örnek 32

olarak düşünmüştük.

Document ise:

```text
tek bir film kaydı
```

## Örnek 33

dır.

Örneğin ileride elimizde:

```text
Interstellar
Dune
Arrival
```

## Örnek 34

olabilir.

Bunların hepsi aynı `Movie` Model'iyle çalışır.

Ama her biri ayrı bir Document'tır.

```text
Movie Model
│
├── Interstellar Document
├── Dune Document
└── Arrival Document
```

## Örnek 35

Böylece Model ile Document arasındaki fark yerine oturuyor:

```text
Model
↓
bütün film kayıtlarıyla çalışır

Document
↓
tek bir filmi temsil eder
```

## Örnek 36

---

## Peki Film Neden Atlas'ta Görünmüyor?

Şimdi asıl sorumuza dönelim.

Elimizde:

```js
const movie = new Movie({
  title: "Interstellar",
  releaseYear: 2014,
  voteAverage: 8.7,
});
```

## Örnek 37

var.

Ama Atlas'ta henüz film yok.

Çünkü `new Movie(...)` yalnızca uygulamamızın içinde bir Document oluşturdu.

Bu Document şu anda:

```text
Node.js uygulamasının belleğinde
```

## Örnek 38

duruyor.

Şöyle düşünün:

```text
new Movie(...)
↓
Mongoose Document oluştu
↓
bellekte duruyor
```

## Örnek 39

Henüz Mongoose'a:

```text
Bunu MongoDB'ye kaydet.
```

## Örnek 40

demedik.

Bu yüzden bölümün en önemli ayrımlarından biri:

```text
Document oluşturmak
≠
Document'ı kaydetmek
```

## Örnek 41

Bir filmi kâğıda yazmak ile onu arşive yerleştirmek arasındaki fark gibi düşünebilirsiniz.

Film bilgisi elimizde.

Ama henüz kalıcı arşive gitmedi.

---

## Normal Bir JavaScript Object'ten Farkı Ne?

Şu iki kod birbirine çok benziyor:

```js
const movie = {
  title: "Interstellar",
  releaseYear: 2014,
  voteAverage: 8.7,
};
```

## Örnek 42

ve:

```js
const movie = new Movie({
  title: "Interstellar",
  releaseYear: 2014,
  voteAverage: 8.7,
});
```

## Örnek 43

İlk örnek:

```text
normal JavaScript object
```

## Örnek 44

tır.

İkinci örnek ise:

```text
Mongoose Document
```

## Örnek 45

tır.

Mongoose Document'ın önemli farkı, Mongoose'un ona kendi davranışlarını kazandırmasıdır.

Örneğin birazdan:

```js
movie.save();
```

## Örnek 46

diyebileceğiz.

Normal bir JavaScript object'te böyle bir Mongoose metodu yoktur.

Şimdilik ayrımı şu kadar sade tutalım:

```text
Normal object
↓
JavaScript verisi

Mongoose Document
↓
Mongoose tarafından oluşturulmuş
ve MongoDB ile çalışabilen kayıt nesnesi
```

## Örnek 47

---

## Bir Şey Daha: `_id` Şimdiden Var

Şimdi küçük ama önemli bir sürpriz var.

Schema'mızda `_id` yazmamıştık.

Ama şu kodu çalıştırırsak:

```js
const movie = new Movie({
  title: "Interstellar",
  releaseYear: 2014,
  voteAverage: 8.7,
});

console.log(movie._id.toString());
```

## Örnek 48

şuna benzeyen bir değer görebiliriz:

```text
66c4f4d89f6a41e9b7a12345
```

## Örnek 49

Bu size tanıdık gelmeli.

26. bölümde öğrendiğimiz:

```text
ObjectId
```

## Örnek 50

artık karşımızda.

Mongoose varsayılan olarak Schema'ya `_id` alanı ekler ve yeni Document oluşturulduğunda bu alan için bir ObjectId hazırlayabilir.

Ama burada çok önemli bir tuzak var:

```text
_id oluştu
≠
MongoDB'ye kaydedildi
```

## Örnek 51

Filmimizin bir kimliği var.

Fakat hâlâ Atlas'ta değil.

Bu bilgi birazdan neden önemli olacak göreceğiz.

---

## O Hâlde Filmi Nasıl Kaydedeceğiz?

Artık ihtiyacımız çok net.

Elimizde Document var:

```js
const movie = new Movie({
  title: "Interstellar",
  releaseYear: 2014,
  voteAverage: 8.7,
});
```

## Örnek 52

Ama bellekte duruyor.

Biz onu:

```text
MongoDB'ye göndermek
```

## Örnek 53

istiyoruz.

Mongoose Document'ların bunun için bir metodu vardır:

```js
save()
```

## Örnek 54

Kodumuz:

```js
await movie.save();
```

## Örnek 55

olur.

Akış artık tamamlanır:

```text
new Movie(...)
↓
Mongoose Document oluşur
↓
bellekte durur

movie.save()
↓
MongoDB'ye kaydedilir
```

## Örnek 56

İşte `new Movie()` ile `save()` arasındaki fark budur.

```text
new Movie()
↓
oluştur

save()
↓
kalıcı olarak kaydet
```

## Örnek 57

---

## Neden `await` Kullandık?

29. bölümde MongoDB bağlantısını kurarken de `await` kullanmıştık.

Burada aynı mantık var.

```js
await movie.save();
```

## Örnek 58

dediğimizde:

```text
MongoDB'ye kaydet
↓
işlemin tamamlanmasını bekle
↓
sonraki satıra geç
```

## Örnek 59

akışını kuruyoruz.

Bu sayede:

```js
await movie.save();

console.log("Film kaydedildi.");
```

## Örnek 60

yazdığımızda:

```text
Film kaydedildi.
```

## Örnek 61

mesajı, kaydetme işlemi başarıyla tamamlandıktan sonra çalışır.

`async / await` konusunu burada yeniden açmıyoruz.

Şimdilik zaten bildiğimiz mantığı MongoDB işlemine uyguluyoruz.

---

## `save()` Schema'yı Unutuyor mu?

Hayır.

30. bölümde Schema'ya:

```js
title: {
  type: String,
  required: true,
}
```

## Örnek 62

yazmıştık.

Yani:

```text
title zorunlu
```

## Örnek 63

demiştik.

`save()` çalışırken Mongoose, Document'ı kaydetmeden önce doğrulama sürecini de çalıştırır.

Bu nedenle kabaca:

```text
movie.save()
↓
Schema kurallarını kontrol et
↓
uygunsa MongoDB'ye kaydet
```

## Örnek 64

akışı vardır.

Örneğin `title` hiç yoksa kaydetme işlemi başarıyla tamamlanmaz.

Validation konusunu ileride ayrıntılı inceleyeceğiz.

Burada bilmemiz gereken tek şey:

```text
Schema'da yazdığımız kurallar
kaydetme sırasında boşa gitmez.
```

## Örnek 65

---

## Şimdi Aynı Dosyayı Gerçek Kayda Hazırlayalım

`src/scripts/createMovie.js` dosyamızı zaten oluşturduk.

Şu ana kadar bu dosyada:

```text
Movie Model'ini import ettik
↓
Interstellar Document'ını oluşturduk
↓
terminalde gördük
```

## Örnek 66

Ama MongoDB'ye henüz bağlanmadık ve `save()` çalıştırmadık.

Şimdi aynı dosyayı bir adım ileri taşıyacağız:

```text
MongoDB'ye bağlan
↓
Interstellar Document'ını oluştur
↓
save() ile kaydet
↓
bağlantıyı kapat
```

## Örnek 67

Dosyanın tamamını şu hâle getirelim:

```js
import "dotenv/config";
import mongoose from "mongoose";

import { initMongoConnection } from "../db/initMongoConnection.js";
import { Movie } from "../db/models/movie.js";

const createMovie = async () => {
  try {
    await initMongoConnection();

    const movie = new Movie({
      title: "Interstellar",
      releaseYear: 2014,
      voteAverage: 8.7,
    });

    console.log("Kaydetmeden önce:");
    console.log(movie);

    await movie.save();

    console.log("Film başarıyla MongoDB'ye kaydedildi.");
  } catch (error) {
    console.error("Film kaydedilemedi:", error.message);
  } finally {
    await mongoose.disconnect();
  }
};

createMovie();
```

## Örnek 68

Kod ilk bakışta uzun görünebilir.

Ama aslında dört iş yapıyor.

---

## Kodu Bir Hikâye Gibi Okuyalım

İlk olarak:

```js
await initMongoConnection();
```

## Örnek 69

çalışıyor.

Yani:

```text
MongoDB Atlas'a bağlan
```

## Örnek 70

diyoruz.

Sonra:

```js
const movie = new Movie({
  title: "Interstellar",
  releaseYear: 2014,
  voteAverage: 8.7,
});
```

## Örnek 71

çalışıyor.

Yani:

```text
Interstellar için
bir Mongoose Document oluştur
```

## Örnek 72

diyoruz.

Bu noktada Document henüz bellekte.

Sonra:

```js
await movie.save();
```

## Örnek 73

çalışıyor.

İşte burada:

```text
Document'ın verisini
MongoDB'ye kaydet
```

## Örnek 74

diyoruz.

En sonunda:

```js
await mongoose.disconnect();
```

## Örnek 75

çalışıyor.

Bu dosya sürekli çalışan Express sunucumuz değil; yalnızca bir kez çalışacak küçük bir deneme dosyası olduğu için işi bitince bağlantıyı kapatıyoruz.

Bütün kodun hikâyesi:

```text
Bağlan
↓
Document oluştur
↓
Kaydet
↓
Bağlantıyı kapat
```

## Örnek 76

Bu kadar.

---

## Şimdi Terminale Geçebiliriz

Bu kez kod yalnızca yazılmış değil.

Gerçekten çalıştıracağız.

Terminali projemizin ana klasöründe açalım.

Ardından:

```bash
node src/scripts/createMovie.js
```

## Örnek 77

komutunu çalıştıralım.

Bu komut:

```text
createMovie.js
↓
createMovie()
↓
initMongoConnection()
↓
new Movie(...)
↓
movie.save()
```

## Örnek 78

akışını gerçekten başlatır.

Bağlantı ve kaydetme başarılıysa terminalde kabaca:

```text
MongoDB bağlantısı başarılı.

Kaydetmeden önce:
{
  title: 'Interstellar',
  releaseYear: 2014,
  voteAverage: 8.7,
  _id: ...
}

Film başarıyla MongoDB'ye kaydedildi.
```

## Örnek 79

gibi bir çıktı görebiliriz.

`_id` değeri sizde farklı olacaktır.

Buradaki güzel nokta şu:

```text
Kaydetmeden önce bile _id vardı.
```

## Örnek 80

Ama filmi MongoDB'ye gönderen satır yine:

```js
await movie.save();
```

## Örnek 81

oldu.

---

## Şimdi Atlas'a Bakın

Asıl güzel an şimdi geliyor.

Bölümün başında Atlas'ın kendi web arayüzündeki `Data Explorer` ile database, collection ve document'larımızı görebildiğimizi söylemiştik.

Şimdi bunu gerçekten kullanalım.

Atlas'ı açalım ve:

```text
Database
↓
Data Explorer
```

## Örnek 82

bölümüne gidelim.

Burada MongoDB Compass'a veya VS Code eklentisine ihtiyacımız yok.

Tarayıcıdaki Atlas ekranı yeterli.

Atlas arayüzünün görünümü zamanla değişebilir; önemli olan `Data Explorer` üzerinden verilerimizi açmaktır.

Şimdi:

```text
movieApp
```

## Örnek 83

database'ini bulun.

Ardından:

```text
movies
```

## Örnek 84

collection'ını açın.

Ve karşınızda şuna benzeyen bir kayıt olmalı:

```json
{
  "_id": "...",
  "title": "Interstellar",
  "releaseYear": 2014,
  "voteAverage": 8.7
}
```

## Örnek 85

İşte bu kez Interstellar gerçekten orada.

Biraz önce:

```text
kodun belleğinde
```

## Örnek 86

olan film artık:

```text
MongoDB'de kalıcı veri
```

## Örnek 87

hâline geldi.

İlk kez kendi Node.js kodumuzdan bir veriyi MongoDB Atlas'a kaydettik.

---

## Database ve Collection Neden Şimdi Görünebilir?

29. bölümde:

```text
MongoDB'ye bağlanmak
≠
Database oluşturmak
```

## Örnek 88

demiştik.

Çünkü o sırada yalnızca bağlantı kurmuştuk.

Henüz veri yazmamıştık.

Şimdi ise:

```js
await movie.save();
```

## Örnek 89

çalıştı.

Yani ilk gerçek verimizi MongoDB'ye gönderdik.

Eğer `movieApp` database'i ve `movies` collection'ı daha önce oluşturulmamışsa, ilk yazma işleminden sonra Atlas'ta görünür hâle gelebilirler.

Akış:

```text
MongoDB bağlantısı
↓
henüz veri yok

ilk save()
↓
ilk veri yazıldı

sonuç
↓
movieApp
↓
movies
↓
Interstellar
```

## Örnek 90

Artık 29. bölümde neden database'i hemen göremediğimiz de daha anlamlı.

---

## Mongoose Document ve MongoDB Document

`new Movie(...)` ile oluşturduğumuz nesne uygulama tarafında bir **Mongoose Document**'tır. `save()` gibi Mongoose davranışlarına sahiptir.

```text
Mongoose Document
↓
save()
↓
MongoDB Document
```

## Örnek 91

`save()` sonrasında aynı kaydın verisi MongoDB'de bir Document olarak saklanır. İkisi aynı filmi temsil eder, ancak biri uygulama katmanında çalışan nesne; diğeri veritabanında tutulan kayıttır.

## Bu Bölümde Asıl Öğrendiğimiz Şey

Bu bölümün tamamını tek bir ayrımda özetleyebiliriz:

```text
new Movie(...)
↓
Document oluşturur

movie.save()
↓
Document'ın verisini MongoDB'ye kaydeder
```

## Örnek 92

Bir de küçük ama önemli ikinci ayrımımız var:

```text
_id oluştu
≠
MongoDB'ye kaydedildi
```

## Örnek 93

Artık şu kodu gördüğünüzde:

```js
const movie = new Movie({
  title: "Interstellar",
  releaseYear: 2014,
  voteAverage: 8.7,
});

await movie.save();
```

## Örnek 94

iki ayrı aşamayı görebilmelisiniz:

```text
1. Interstellar için
   bir Mongoose Document oluştur.

2. Bu Document'ı
   MongoDB'ye kaydet.
```

## Örnek 95

İşte Mongoose Document mantığının temeli budur.

---

## Bölüm Özeti

Bu bölüme şu soruyla başladık:

```text
Kodda Interstellar var.
Peki neden Atlas'ta yok?
```

## Örnek 96

Cevabı bulduk.

`new Movie(...)`:

```text
MongoDB'ye veri yazmaz.
```

## Örnek 97

Bize yalnızca bir:

```text
Mongoose Document
```

## Örnek 98

oluşturur.

Bu Document ilk aşamada uygulamamızın belleğinde bulunur.

Mongoose'un yeni Document için varsayılan olarak `_id` oluşturabildiğini gördük.

Ama:

```text
_id var
≠
MongoDB'ye kaydedildi
```

## Örnek 99

ayrımını yaptık.

Gerçek kaydetme işlemini:

```js
await movie.save();
```

## Örnek 100

ile gerçekleştirdik.

Sonra:

```bash
node src/scripts/createMovie.js
```

## Örnek 101

komutuyla kodu gerçekten çalıştırdık.

Ve Atlas Data Explorer'da:

```text
movieApp
↓
movies
↓
Interstellar
```

## Örnek 102

kaydını gördük.

Artık elimizde:

```text
MongoDB bağlantısı
✅

Schema
✅

Model
✅

Mongoose Document
✅

İlk MongoDB kaydı
✅
```

## Örnek 103

var.

---

## Sonraki Adım

Şimdiye kadar filmi biz yazdık:

```js
const movie = new Movie({
  title: "Interstellar",
  releaseYear: 2014,
  voteAverage: 8.7,
});
```

## Örnek 104

Ama gerçek film platformunda böyle olmayacak.

Kullanıcı veya frontend bize:

```http
POST /movies
```

## Örnek 105

Request'i gönderecek.

Film bilgileri de:

```js
req.body
```

## Örnek 106

içinden gelecek.

O hâlde artık iki dünyayı birleştirebiliriz:

```text
POST /movies
↓
req.body
↓
Movie Model
↓
Mongoose Document
↓
save()
↓
MongoDB
```
