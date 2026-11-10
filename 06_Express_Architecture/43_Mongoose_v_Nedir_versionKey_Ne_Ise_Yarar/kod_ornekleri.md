# Bölüm 43 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Mongoose __v Nedir? versionKey Ne İşe Yarar?

> **İleri Okuma:** Bu bölüm, Mongoose'un sürüm bilgisinin neden var olduğunu anlamak için `__v` ve `versionKey` davranışını ayrıntılandırır. Ana Movie API akışını sürdürmek için yeni bir Route veya kalıcı Schema değişikliği gerekmez. Ayrıntılar ilk okumada ağır gelirse ana fikri alıp 44. bölüme geçebilir, daha sonra bu bölüme dönülebilir.

## Bu `__v: 0` Nereden Geldi?

42. bölümde `timestamps` ile tanıştık. Yeni bir film kaydettiğimizde Document'ımızda şu alanları da görebiliyoruz:

```json
{
  "_id": "...",
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3,
  "createdAt": "2026-09-02T05:30:00.000Z",
  "updatedAt": "2026-09-02T05:30:00.000Z",
  "__v": 0
}
```

## Örnek 2

`_id` Document'ın kimliği, `createdAt` oluşturulma zamanı, `updatedAt` ise son güncellenme zamanıdır. Peki bizim eklemediğimiz `__v` alanı ne işe yarıyor?

`__v`, Mongoose'un Document için tuttuğu sürüm bilgisidir. Yeni bir Document genellikle `0` ile başlar. Fakat **Document'ın kaç kez güncellendiğini gösteren bir sayaç değildir.** `__v: 3` gördüğümüzde “üç kez güncellenmiş” diyemeyiz. Bu sayının ne zaman ilerlediğini ve neden kullanıldığını birazdan göreceğiz.

## versionKey ile __v Arasındaki Fark

`versionKey`, Schema'daki sürüm alanının adını belirleyen ayardır. Varsayılan alan adı `__v` olduğu için hiçbir şey yazmasak bile bu alanı görürüz.

Örneğin:

```js
const exampleSchema = new mongoose.Schema(
  {
    title: String,
  },
  {
    versionKey: "version",
  },
);
```

## Örnek 3

yazsaydık sürüm alanının adı `version` olurdu. Bu yalnızca bir örnektir; gerçek Movie Schema'mızın alan adını değiştirmiyoruz.

`versionKey: false` ile bu mekanizma kapatılabilir. Ancak sırf Response'ta `__v` görünmesin diye sürüm kontrolünü kapatmak iyi bir gerekçe değildir. Normal uygulama kodunda `movie.__v = 25` gibi elle müdahaleler de yapmayacağız; sürüm bilgisini Mongoose yönetsin.

## Eski Sürüm Ayrı Bir Kayıt mı?

“Eski sürüm” dediğimizde MongoDB'nin aynı filmin bütün geçmiş hâllerini sakladığını düşünmeyin. `__v` bir geçmiş kayıt sistemi değildir.

Bu bölümde eski sürüm, daha önce MongoDB'den okunmuş ve uygulamanın belleğinde kalmış Mongoose Document'ıdır. Güncel sürüm ise MongoDB'de şu anda bulunan kayıttır. Aynı `_id` değerine sahip iki ayrı film oluşturmayacağız; aynı kaydın farklı zamanlarda okunmuş kopyalarını inceleyeceğiz.

---

## Array Değiştiğinde Neden Sürüm Kontrolüne İhtiyaç Duyarız?

Bu mekanizmayı anlamak için geçici olarak `genres` adında bir array kullanalım. Gerçek Movie Schema'mıza bu alanı eklemiyoruz.

```text
MongoDB
genres: ["Science Fiction", "Drama", "Adventure"]
__v: 0
```

## Örnek 4

Aynı Document'ı iki ayrı işlem okusun:

```js
const movieA = await Movie.findById(movieId);
const movieB = await Movie.findById(movieId);
```

## Örnek 5

İkisi de henüz değişiklik yapılmadan okuduğu için aynı array'e ve `__v: 0` değerine sahiptir.

Şimdi A ilk elemanı kaldırsın:

```js
movieA.genres.splice(0, 1);
await movieA.save();
```

## Örnek 6

Bu tür array değişikliklerinde Mongoose'un varsayılan sürüm mekanizması sürümü ilerletebilir. Senaryomuzda A'nın kaydından sonra MongoDB'deki durum:

```text
0 → Drama
1 → Adventure

__v: 1
```

## Örnek 7

olur.

B'nin belleğindeki Document ise kendiliğinden yenilenmez:

```text
0 → Science Fiction
1 → Drama
2 → Adventure

__v: 0
```

## Örnek 8

B, eski array'deki `index 1` değerinin Drama olduğunu sanıyor. Oysa güncel array'de aynı index artık Adventure'ı gösteriyor. Eski index'e dayanarak yapılan değişiklik yanlış elemanı etkileyebilir.

Şimdi B'nin bu eski bilgiyle kaydetmeye çalıştığında neler olduğuna bakalım.

---

## Mongoose Burada `__v`yi Nasıl Kullanıyor?

Şimdi `movieB`nin eski array üzerinde bir değişiklik yaptığını düşünelim. Kendi belleğinde `Drama` hâlâ 1 numaralı eleman:

```js
movieB.set("genres.1", "Thriller");
```

## Örnek 9

Bu satır henüz MongoDB'ye yazmaz. Yalnızca `movieB`nin belleğindeki veriyi değiştirir. MongoDB'de ise A'nın kaydettiği yeni array bulunmaktadır.

```text
movieB'nin belleği             MongoDB

0 → Science Fiction           0 → Drama
1 → Thriller                  1 → Adventure
2 → Adventure

__v: 0                        __v: 1
```

## Örnek 10

Buradaki önemli soru artık şu: `movieB.save()` çağrıldığında Mongoose bu farkı nasıl fark edecek?

### save() Eski Sürümü Nasıl Kontrol Ediyor?

`movieB` daha önce MongoDB'den okunurken `__v: 0` bilgisini de almıştı. Mongoose bu bilgiyi kullanarak, sürüm kontrolünün gerekli olduğu bu array değişikliği için MongoDB'ye bir güncelleme isteği hazırlar.

İsteğin mantığı şudur:

> Bu `_id` değerine sahip Document'ı bul. Ancak sürümü hâlâ `0` ise hazırladığım değişikliği uygula.

Mongoose'un hazırladığı güncelleme koşulunu, gerçek bir JavaScript nesnesiyle daha açık görebiliriz:

```js
const expectedVersion = movieB.__v;

const filter = {
  _id: movieB._id,
  __v: expectedVersion,
};
```

## Örnek 11

Bu kod, sürüm kontrollü `save()` işleminin kullandığı arama koşulunu anlamak için yazılmış bir örnektir; Mongoose'un iç kodunun birebir kopyası değildir. Projemize ayrıca eklemiyoruz.

`movieB.__v` değeri `0` olduğu için `filter` şu anlama gelir:

```js
{
  _id: movieB._id,
  __v: 0,
}
```

## Örnek 12

Burada iki şart birlikte aranır:

```text
_id aynı olacak
VE
__v değeri 0 olacak
```

## Örnek 13

Yani `__v: 0` burada “veritabanındaki sürümü 0 yap” demek değildir. **“Sürümü 0 olan Document'ı bul”** demektir.

Mongoose, sürüm kontrolünün gerekli olduğu bu `save()` işleminde, hazırladığı güncellemeyi böyle bir sürüm koşuluyla MongoDB'ye gönderir. Güncelleme başarılı olursa ilgili sürüm artırma işlemini de yönetebilir. Bütün `save()` çağrılarında aynı kontrolün kullanıldığını ise varsaymıyoruz.

### MongoDB Bu Koşulu Kontrol Ettiğinde Ne Olur?

Veritabanındaki güncel kayıt:

```js
{
  _id: demoId,
  genres: ["Drama", "Adventure"],
  __v: 1,
}
```

## Örnek 14

olsun. MongoDB arama koşulunu değerlendirir:

```text
_id eşleşiyor mu?
↓
evet

__v: 0 koşulu sağlanıyor mu?
↓
hayır, güncel değer 1
```

## Örnek 15

Böylece:

```text
0 ≠ 1
↓
iki şartı birlikte sağlayan Document yok
```

## Örnek 16

sonucuna ulaşılır. MongoDB bu güncelleme için eşleşen Document bulamadığından değişikliği uygulamaz.

Burada özellikle ayıralım: MongoDB'nin yaptığı işlem, sorgunun koşullarına uyan kayıt olup olmadığını belirlemektir. `VersionError` adındaki Mongoose hatasını doğrudan MongoDB üretmez.

### Eşleşme Bulunmadığını Mongoose Nasıl Anlıyor?

MongoDB bir güncelleme işleminin sonucunda kaç Document'ın arama koşuluyla eşleştiği bilgisini de döndürür. Bu bilgi modern MongoDB sürücüsünde `matchedCount` adıyla görülebilir.

Örneğin sorgu sonucunun ilgili kısmı şöyle olabilir:

```js
{
  matchedCount: 0,
  modifiedCount: 0,
}
```

## Örnek 17

Bu iki alanın görevi farklıdır:

```text
matchedCount
↓
arama koşuluna kaç Document uydu?
```

## Örnek 18



```text
modifiedCount
↓
kaç Document'ın verisi değişti?
```

## Örnek 19

Bizim örneğimizde `matchedCount: 0` olması önemlidir. Çünkü `_id` ve beklenen sürüm koşullarını birlikte sağlayan kayıt bulunamamıştır.

Mongoose, sürüm kontrolüyle hazırladığı kaydetme işleminin bu şekilde eşleşme bulamadığını görünce işlemi başarılı kabul etmez. Beklediği sürümle kaydı gerçekleştiremediği için bir `VersionError` oluşturur ve `save()` tarafından dönen Promise reddedilir.

Böylece:

```js
await movieB.save();
```

## Örnek 20

satırında hata ortaya çıkar. Daha önce öğrendiğimiz `try/catch` veya Express 5'in merkezi hata akışı bu reddedilen Promise'i yakalayabilir.

Akışın tamamı:

```text
movieB bellekte __v: 0 taşıyor
↓
Mongoose sürüm kontrollü save() hazırlıyor
↓
MongoDB'ye _id + beklenen sürüm koşulu gönderiliyor
↓
MongoDB'deki gerçek __v: 1
↓
__v: 0 koşulu sağlanmıyor
↓
eşleşen kayıt yok
↓
Mongoose kaydetme işlemini başarısız kabul ediyor
↓
VersionError
```

## Örnek 21

### Bu Kontrol Neden Ayrı Bir find() ile Yapılmıyor?

Şöyle bir yöntem düşünebiliriz:

```text
önce MongoDB'deki __v değerini oku
↓
0 mı diye kontrol et
↓
sonra kaydet
```

## Örnek 22

Fakat bu iki ayrı işlem olsaydı, kontrol ile kaydetme arasında başka bir işlem Document'ı değiştirebilirdi.

Sürüm koşulunun güncelleme işleminin kendisinde bulunması bu yüzden önemlidir. MongoDB, koşulu ve güncellemeyi aynı Document üzerinde tek bir atomik güncelleme işlemi olarak değerlendirir. Koşul artık sağlanmıyorsa değişiklik uygulanmaz.

Bu, Mongoose'un her `save()` çağrısında bütün alanları karşılaştırdığı anlamına gelmez. Varsayılan versioning mekanizması özellikle bazı array değişikliklerinde bu kontrolü kullanır. Bizim örneğimiz de o durumlardan biridir.

---
## VersionError Tam Olarak Ne Demek?

İngilizce adıyla:

```text
Version
↓
sürüm

Error
↓
hata
```

## Örnek 23

Yani Mongoose'un sürüm kontrolüyle ilgili bir hata.

Ama bunu yalnızca “sürüm hatası” diye çevirmek yeterli değil. Bizim örneğimizde anlattığı şey şu:

> Bu Document'ı daha önce okudun. O zamandan beri veritabanındaki hâli değişti. Şimdi eski bilgilere dayanarak yaptığın bu değişikliği güvenle kaydedemiyorum.

Bu, `title` alanının zorunlu olması veya `voteAverage` değerinin 10'u aşması gibi bir validation hatası değildir. Veritabanı bağlantısının kopması da değildir. Buradaki problem, değişikliği hazırlarken kullandığımız Document'ın artık güncel olmamasıdır.

Önemli olan, hatanın adını ezberlemekten çok hangi tehlikeyi önlediğini anlamaktır.

---
## Hata Oluştuğunda Hangi İşlem Kaydedilmiş Olur?

İki işlemi yan yana düşünelim:

```text
movieA
↓
ilk türü kaldırdı
↓
save() başarılı
↓
MongoDB güncellendi
```

## Örnek 24



```text
movieB
↓
eski array'e göre değişiklik hazırladı
↓
save() denendi
↓
VersionError
↓
bu kaydetme işlemi reddedildi
```

## Örnek 25

`movieA`nın başarılı kaydı geri alınmaz. `movieB`nin başarısız kaydetme işlemi de yeni değişikliğini MongoDB'ye yazmış olmaz.

Bu senaryonun sonunda:

```text
MongoDB'deki güncel Document

genres: ["Drama", "Adventure"]
__v: 1
```

## Örnek 26

olarak kalır.

`movieB`nin belleğindeki eski ve değiştirilmiş Document ise kendiliğinden güncellenmez. Hata, “eski kopyayı otomatik olarak yeniledim” anlamına gelmez.

---
## Hatayı Kodda Nasıl Tanırız?

Daha önce `try/catch` ile hataları yakalamayı öğrenmiştik. Bu kez hatanın türüne bakalım:

```js
try {
  await movieB.save();
} catch (error) {
  console.log(error.name);
}
```

## Örnek 27

Bu senaryoda sürüm kontrolü hatayı oluşturursa:

```text
VersionError
```

## Örnek 28

görürüz.

Mongoose'un hata nesnesinde ayrıca hata mesajı ve ilgili sürüm bilgileri bulunabilir. Mesaj, kaydedilmeye çalışılan Document'ı ve beklenen sürümü anlamamıza yardımcı olur.

Örneğin mesajın anlattığı şey kabaca:

```text
Bu Document için beklediğim sürüm 0 idi.
Sürüm koşuluna uygun bir kayıt bulunamadığı için kaydetme başarısız oldu.
```

## Örnek 29

şeklindedir.

Hata mesajının tamamını ezberlememize gerek yok. İlk bakacağımız yer:

```js
error.name
```

## Örnek 30

olabilir.

---
## Bu Davranışı Kendimiz de Görebilir miyiz?

Evet. Ancak bizim gerçek Movie Schema'mızda `genres` alanı bulunmuyor. Sırf bu örneği denemek için film platformumuzun Schema'sını değiştirmeyeceğiz.

Bunun yerine, yalnızca bu deneme için ayrı bir Model ve ayrı bir collection kullanabiliriz. Böylece mevcut filmlerimize dokunmadan aynı mekanizmayı gözlemleyebiliriz.

Yeni dosya:

```text
src/versionErrorDemo.js
```

## Örnek 31

Bu dosya normal API'mizin bir parçası olmayacak. Terminalden bir kez çalıştıracağımız küçük bir deneme dosyası olacak.

### Deneme kodu

Dosyanın tamamı:

```js
import "dotenv/config";
import mongoose from "mongoose";

const demoSchema = new mongoose.Schema(
  {
    genres: [String],
  },
  {
    collection: "version_error_demos",
  },
);

const VersionDemo =
  mongoose.models.VersionDemo ||
  mongoose.model("VersionDemo", demoSchema);

const run = async () => {
  let demoId;

  try {
    await mongoose.connect(process.env.MONGODB_URL);

    const created = await VersionDemo.create({
      genres: [
        "Science Fiction",
        "Drama",
        "Adventure",
      ],
    });

    demoId = created._id;

    const movieA = await VersionDemo.findById(demoId);
    const movieB = await VersionDemo.findById(demoId);

    console.log("Başlangıç:", {
      a: movieA.__v,
      b: movieB.__v,
    });

    movieA.genres.splice(0, 1);
    await movieA.save();

    console.log("A kaydedildi:", {
      genres: movieA.genres,
      version: movieA.__v,
    });

    movieB.set("genres.1", "Thriller");

    try {
      await movieB.save();
    } catch (error) {
      console.log("Hata türü:", error.name);
      console.log("Hata mesajı:", error.message);
    }

    const current = await VersionDemo.findById(demoId);

    console.log("MongoDB'deki güncel kayıt:", {
      genres: current.genres,
      version: current.__v,
    });
  } finally {
    if (demoId) {
      await VersionDemo.deleteOne({ _id: demoId });
    }

    await mongoose.disconnect();
  }
};

run().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```

## Örnek 32

Bu dosyada `VersionDemo` adını kullanıyoruz; gerçek `Movie` Model'imizi yeniden tanımlamıyoruz. `collection` seçeneği de deneme verilerinin ayrı bir collection'da tutulmasını sağlıyor.

Kodun sonunda yalnızca bu deneme sırasında oluşturulan Document'ı siliyoruz. Bütün collection'ı silmiyoruz.

### Kodun kritik kısmını okuyalım

Önce tek bir kayıt oluşturuyoruz:

```js
const created = await VersionDemo.create({
  genres: [
    "Science Fiction",
    "Drama",
    "Adventure",
  ],
});
```

## Örnek 33

Sonra aynı kaydı iki kez okuyoruz:

```js
const movieA = await VersionDemo.findById(demoId);
const movieB = await VersionDemo.findById(demoId);
```

## Örnek 34

Bunlar iki farklı MongoDB kaydı değildir. Aynı kaydın uygulama belleğine alınmış iki ayrı Mongoose Document örneğidir.

Ardından A'yı kaydediyoruz:

```js
movieA.genres.splice(0, 1);
await movieA.save();
```

## Örnek 35

B ise hâlâ eski array'i taşıyor. Bu nedenle:

```js
movieB.set("genres.1", "Thriller");
await movieB.save();
```

## Örnek 36

satırları, varsayılan sürüm kontrolünün yakalayabildiği çakışmayı oluşturur.

Terminalde:

```bash
node src/versionErrorDemo.js
```

## Örnek 37

çalıştırabiliriz.

MongoDB bağlantınızın ve `.env` içindeki `MONGODB_URL` değerinin doğru olması gerekir. Deneme başarılıysa `VersionError` adını ve MongoDB'deki güncel array'in korunduğunu görürsünüz. Hata mesajının tamamı kullandığınız Mongoose sürümüne göre farklı görünebilir.

Bu deneme, gerçek API'mizin Controller'larına yeni bir Route eklemez ve mevcut Movie Schema'mızı değiştirmez.

---
## VersionError Oluşursa Ne Yapmalıyız?

İlk düşüncemiz:

```text
Hata çıktı.
↓
Aynı save() işlemini tekrar dene.
```

## Örnek 38

olmamalı.

Çünkü sorun, elimizdeki Document'ın eski olmasıdır. Aynı eski kopyayı hiç değerlendirmeden tekrar kaydetmek sorunu çözmeyebilir.

Daha doğru yaklaşım:

```text
VersionError
↓
güncel Document'ı yeniden oku
↓
arada ne değiştiğini değerlendir
↓
istenen değişiklik hâlâ uygunsa
yeni bilgiye göre tekrar uygula
```

## Örnek 39

si gerekebilir. Bunlar uygulamanın ihtiyacına göre tasarlanır.

Önemli olan, hatayı görmezden gelip `__v` değerini elle değiştirmemek ve veriyi zorla kaydetmeye çalışmamaktır.

---

## VersionError API Yanıtına Nasıl Dönüşür?

`VersionError`, Mongoose'un ürettiği bir hata türüdür; `400`, `409` ve `500` ise HTTP durum kodlarıdır. Bu nedenle aralarında otomatik bir eşleşme yoktur.

Örneğin gerçekten bir sürüm çakışmasını temsil eden durumda API tasarımı `409 Conflict` döndürmeyi seçebilir. Ancak Mongoose `VersionError` oluşturduğunda Express kendiliğinden `409` göndermez. Bunun için merkezi hata yönetiminde açık bir eşleme tanımlamak gerekir.

Bu aşamada projeye yeni bir hata politikası eklemiyoruz; burada önemli olan `VersionError`'ın Mongoose katmanında neyi anlattığını bilmektir.

## Peki İki Kullanıcı Normal Bir Alanı Değiştirirse?

Array örneği, aynı Document üzerinde çakışan değişikliklerin oluşturabileceği riski gösterdi. Peki aynı durum Schema'daki normal bir alan değiştirilirken de ortaya çıkar mı?

> A ve B aynı filmi okursa, A yayın yılını 2022, B ise 2025 yapıp kaydederse ne olur?

Bu kez gerçek Schema'mızda bulunan `releaseYear` alanını düşünelim. Başlangıçta MongoDB'de:

```js
{
  title: "Dune",
  releaseYear: 2021,
  __v: 0,
}
```

## Örnek 40

bulunuyor. A ve B aynı kaydı henüz değişiklik yapılmadan okuyor:

```js
const movieA = await Movie.findById(movieId);
const movieB = await Movie.findById(movieId);
```

## Örnek 41

A önce kaydediyor:

```js
movieA.releaseYear = 2022;
await movieA.save();
```

## Örnek 42

Bizim mevcut Schema'mızda `optimisticConcurrency` açık değildir. Varsayılan sürüm kontrolü normal bir sayı alanının her değişikliğinde sürümü artırmak zorunda değildir. Bu senaryoda güncel kayıt:

```text
releaseYear: 2022
__v: 0
```

## Örnek 43

olur.

B'nin belleğindeki eski Document hâlâ `releaseYear: 2021` ve `__v: 0` taşır. B şimdi:

```js
movieB.releaseYear = 2025;
await movieB.save();
```

## Örnek 44

yaparsa, varsayılan sürüm kontrolü bu normal alan değişikliğini engellemez. B'nin kaydı başarılı olur ve son değer `2025` olur.

```text
2021 → A kaydeder → 2022
2022 → B kaydeder → 2025
```

## Örnek 45

Böylece A'nın 2022 değişikliği, aynı alana daha sonra yazılan 2025 değeriyle değiştirilir. Buna *last write wins*, yani “son yazan kazanır” davranışı denir.

Burada bütün Document'ın mutlaka eski hâliyle üzerine yazıldığını düşünmeyin. Mongoose `save()` genellikle değiştirilmiş alanları günceller. Bizim örneğimizde iki işlem de aynı `releaseYear` alanını değiştirdiği için son kaydedilen değer öncekinin üzerine yazılıyor.

Demek ki `__v` alanının bulunması, bütün eşzamanlı değişikliklerin otomatik olarak korunduğu anlamına gelmez. Daha kapsamlı bir koruma istiyorsak başka bir seçeneğe ihtiyacımız var.

---

## Normal Alanlarda da Sürüm Koruması İstersek?

Mongoose bu ihtiyaç için `optimisticConcurrency` seçeneğini sunar. *Optimistic concurrency*, iyimser eşzamanlılık kontrolü anlamına gelir. İşlemler baştan kilitlenmez; kaydetme sırasında Document'ın okunduğundan beri değişip değişmediği kontrol edilir.

Küçük bir örnek Schema:

```js
const exampleSchema = new mongoose.Schema(
  {
    releaseYear: Number,
  },
  {
    optimisticConcurrency: true,
  },
);
```

## Örnek 46

Bu ayarı gerçek Movie Schema'mıza şu anda eklemiyoruz. Önce neyi değiştirdiğini anlayalım.

### Aynı Senaryoyu Koruma Açıkken İzleyelim

A ve B yine `releaseYear: 2021`, `__v: 0` olan aynı Document'ı okudu.

A:

```js
movieA.releaseYear = 2022;
await movieA.save();
```

## Örnek 47

çağırdığında Mongoose, beklenen sürümü güncelleme koşuluna ekler ve başarılı kayıtta sürümü ilerletir. Bu işlemin mantığını şöyle gösterebiliriz:

```js
{
  filter: {
    _id: movieA._id,
    __v: 0,
  },
  update: {
    $set: {
      releaseYear: 2022,
    },
    $inc: {
      __v: 1,
    },
  },
}
```

## Örnek 48

Bu nesne gerçek MongoDB komutunun birebir çıktısı değil, mekanizmayı anlamak için sadeleştirilmiş bir gösterimdir. `filter` ve `update` alanları da burada açıklayıcı gruplardır; bu nesneyi doğrudan bir Model metoduna göndermiyoruz.

#### `$set` ve `$inc` Ne Yapıyor?

Bu iki MongoDB update operatörünü henüz ayrıntılı öğrenmedik. Burada yalnızca örneği okuyabilmek için görevlerini bilelim.

```js
$set: {
  releaseYear: 2022,
}
```

## Örnek 49

`$set`, alanı belirtilen değere ayarlar. Yani yayın yılını 2022 yapar.

```js
$inc: {
  __v: 1,
}
```

## Örnek 50

`$inc`, sayısal alanın mevcut değerine belirtilen miktarı ekler. `__v: 0` ise 1, `__v: 4` ise 5 olur. Sürümü doğrudan 1'e eşitlemez.

Bu iki işlem ancak arama koşulu sağlanırsa uygulanır. Koşul eşleşmezse ne yayın yılı değiştirilir ne de sürüm artırılır. `$set`, `$inc`, `$unset` ve diğer operatörleri doğrudan güncelleme sorgularına geçtiğimizde ayrıntılı öğreneceğiz.

### B Eski Sürümle Kaydetmeye Çalışırsa

A'nın başarılı kaydından sonra MongoDB:

```text
releaseYear: 2022
__v: 1
```

## Örnek 51

durumundadır. B'nin belleğinde ise hâlâ `__v: 0` bulunur.

B:

```js
movieB.releaseYear = 2025;
await movieB.save();
```

## Örnek 52

çağırdığında Mongoose'un sürüm koşulu `__v: 0` değerini bekler. Fakat MongoDB'deki değer 1 olduğu için `_id` ve sürüm koşulunu birlikte sağlayan kayıt bulunamaz.

```text
B'nin beklediği sürüm → 0
MongoDB'deki sürüm   → 1
↓
Koşul eşleşmiyor
↓
VersionError
```

## Örnek 53

oruma yalnızca aynı alanın üzerine yazılmasını engellemekle sınırlı değildir. `optimisticConcurrency: true`, farklı alanları değiştiren iki eski Document kopyasını da çakışma olarak değerlendirebilir. Amaç Document'ın okunduğundan beri değiştiğini fark etmektir.

---

### Varsayılan Sürüm Kontrolü ile Farkı

| Senaryo | Varsayılan versioning | optimisticConcurrency: true |
|---|---|---|
| Normal bir sayı alanında iki eski kopya kaydedilir | İkinci kayıt aynı alanın üzerine yazabilir. | İkinci kayıt eski sürüme dayanıyorsa VersionError oluşur. |
| Bazı güvenli olmayan array değişiklikleri | Sürüm kontrolü devreye girebilir. | Daha kapsamlı sürüm kontrolü uygulanır. |
| Sürüm her başarılı değişiklikte artar mı? | Hayır. | Mevcut Document'ı değiştiren başarılı `save()` işlemlerinde sürüm ilerletilir. |

Bu seçenek bütün MongoDB işlemlerine otomatik olarak yayılmaz. Özellikle `updateOne()` ve `findOneAndUpdate()` gibi doğrudan update sorgularında sürüm davranışını ayrıca değerlendirmek gerekir.

---

## `updatedAt` ve `__v` Farklı Bilgiler Tutar

`updatedAt`, Document'ın en son ne zaman güncellendiğini gösterir. `__v` ise Mongoose'un sürümleme mekanizmasında kullandığı sayısal alandır.

```text
updatedAt → son değişiklik zamanı
__v       → Mongoose sürüm değeri
```
