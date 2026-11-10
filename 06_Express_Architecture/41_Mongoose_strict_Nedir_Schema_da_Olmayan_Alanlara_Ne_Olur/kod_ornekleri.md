# Bölüm 41 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Mongoose strict Nedir? Schema'da Olmayan Alanlara Ne Olur?

## Değer Doğru Olsa Bile Alan Adı Yanlış Olabilir

40. bölümde Mongoose validation ile şu soruya cevap verdik:

```text
Alan Schema'da var.
Peki değeri kurala uygun mu?
```

## Örnek 2

Örneğin:

```json
{
  "voteAverage": 25
}
```

## Örnek 3

`voteAverage` alanı Schema'da vardır, fakat `25` değeri bizim belirlediğimiz `max: 10` kuralını aşar. Buradaki problem **alanın değeriyle** ilgilidir ve validation devreye girer.

Şimdi farklı bir hata düşünelim:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAvrage": 8.3
}
```

## Örnek 4

Burada `8.3` geçerli bir puan olabilir. Problem değer değil, alan adıdır:

```text
voteAverage   ← Schema'daki doğru alan
voteAvrage    ← yanlış yazılmış alan
```

## Örnek 5

Mongoose'un `strict` seçeneği tam olarak bu ikinci durumla ilgilenir.

---

## strict Ne Kontrol Eder?

`strict` için temel soru şudur:

> **Mongoose'a ulaşan bir alan Schema'da tanımlı değilse ne yapılacak?**

Yani iki kavramın görevleri farklıdır:

```text
validation
↓
Schema'da bulunan alanın DEĞERİ kurala uygun mu?
```

## Örnek 6



```text
strict
↓
Gelen ALAN Schema'da tanımlı mı?
```

## Örnek 7

Bu ayrımı bölüm boyunca temel referansımız olarak kullanalım.

---

## Varsayılan Davranış: strict: true

Mongoose'da `strict` varsayılan olarak `true` değerindedir. Bu nedenle Schema'ya ayrıca:

```js
strict: true
```

## Örnek 8

yazmamız gerekmez.

Movie Schema'mızda şu alanların bulunduğunu düşünelim:

```text
title
releaseYear
voteAverage
```

## Örnek 9

Şimdi Mongoose'a doğrudan fazladan bir alan gönderelim:

```js
const movie = new Movie({
  title: "Dune",
  releaseYear: 2021,
  voteAverage: 8.3,
  unknownField: "Test",
});
```

## Örnek 10

`unknownField` Schema'da tanımlı değildir. Varsayılan `strict: true` davranışında bu alan Document'a dahil edilmez ve MongoDB'ye kaydedilecek veriden çıkarılır.

Kayıt kabaca şöyle olur:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3
}
```

## Örnek 11

Burada önemli bir ayrıntı var: `strict: true`, bilinmeyen alanı **kaydetmemek** demektir; her durumda hata fırlatmak demek değildir.

Bu yüzden bir yazım hatası bazen sessizce gözden kaçabilir. Örneğin `voteAverage` yerine `voteAvrage` yazılırsa alan kaydedilmez, fakat geliştirici neden puanın veritabanına gitmediğini ilk bakışta anlayamayabilir.

---

## Bilinmeyen Alan Hata Üretsin İstersek: strict: "throw"

Schema dışında bir alan geldiğinde bunun sessizce çıkarılmasını değil, açıkça hata oluşturmasını istersek `strict: "throw"` kullanabiliriz:

```js
const movieSchema = new mongoose.Schema(
  {
    title: {
      type: String,
    },
  },
  {
    strict: "throw",
  },
);
```

## Örnek 12

Ardından:

```js
new Movie({
  title: "Dune",
  unknownField: "Test",
});
```

## Örnek 13

şeklinde Schema dışı bir alan verilirse Mongoose `StrictModeError` oluşturabilir.

Akış artık şöyledir:

```text
unknownField
↓
Schema'da yok
↓
strict: "throw"
↓
hata
```

## Örnek 14

Bu seçenek, özellikle alan adlarındaki hataların sessizce kaybolmasını istemediğimiz projelerde anlamlı olabilir.

---

## Schema Dışındaki Alanlara İzin Vermek İstersek: strict: false

Bazı özel veri modellerinde Schema'da önceden tanımlanmamış alanlara izin vermek isteyebiliriz. Böyle bir durumda:

```js
strict: false
```

## Örnek 15

kullanılabilir.

Örneğin:

```js
const movieSchema = new mongoose.Schema(
  {
    title: {
      type: String,
    },
  },
  {
    strict: false,
  },
);
```

## Örnek 16

Bu Schema ile:

```js
const movie = new Movie({
  title: "Dune",
  unknownField: "Test",
});

await movie.save();
```

## Örnek 17

şeklinde bir kullanımda `unknownField` da kaydedilebilir.

Üç davranışı tek yerde karşılaştıralım:

```text
strict: true
→ Schema dışı alanı kaydetme

strict: "throw"
→ Schema dışı alan için hata oluştur

strict: false
→ Schema dışı alana izin ver
```

## Örnek 18

Bu karşılaştırma bölümün geri kalanındaki tüm örnekler için yeterli olacak.

---

## Bizim POST Route'umuzda Neden Farklı Bir Ayrıntı Var?

Şu Request Body'nin geldiğini düşünelim:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3,
  "unknownField": "Test"
}
```

## Örnek 19

İlk bakışta `unknownField` alanını `strict` işleyecekmiş gibi görünebilir. Fakat mevcut `createMovie` Controller'ımız Request Body'den yalnızca bildiğimiz alanları seçiyor:

```js
const {
  title,
  releaseYear,
  voteAverage,
} = req.body;
```

## Örnek 20

Daha sonra Model'e de yalnızca bu alanları veriyoruz:

```js
const movie = new Movie({
  title,
  releaseYear,
  voteAverage,
});
```

## Örnek 21

Bu nedenle bizim mevcut akışımızda:

```text
Request Body
↓
unknownField var
↓
Controller yalnızca izin verdiğimiz alanları seçiyor
↓
unknownField new Movie(...) içine girmiyor
↓
Mongoose bu alanı hiç görmüyor
```

## Örnek 22

Buradaki önemli sonuç şu:

> `strict`, Request Body'nin tamamını denetleyen bir Express filtresi değildir. Mongoose'a gerçekten ulaşan veriye uygulanır.

---

## Controller Alan Seçiyorsa strict Gereksiz mi?

Hayır. İki mekanizma farklı katmanlarda çalışır.

Controller'ın görevi:

```text
Request'ten hangi alanları uygulamaya alacağım?
```

## Örnek 23

Mongoose `strict` seçeneğinin görevi ise:

```text
Model'e ulaşan bir alan Schema'da yoksa ne yapacağım?
```

## Örnek 24

Bunu iki ayrı kapı gibi düşünebiliriz:

```text
Request
↓
Controller alan seçimi
↓
Mongoose strict
↓
MongoDB
```

## Örnek 25

Bugünkü Controller'ımız seçici davranıyor olabilir. Fakat ileride uygulamanın başka bir yerinde:

```js
new Movie(req.body)
```

## Örnek 26

gibi daha doğrudan bir kullanım yazılabilir veya Model'e başka bir kaynaktan veri gelebilir. `strict`, Model seviyesinde bu tür durumlar için ayrı bir davranış kuralı sağlar.

---

## strict: false Neden Dikkatli Kullanılmalı?

Schema'nın amacı yalnızca veri kaydetmek değil, Document'lara ortak bir yapı kazandırmaktır.

`strict: false` kontrolsüz kullanılırsa aynı bilgi zamanla farklı alan adlarında tutulabilir:

```json
{
  "voteAverage": 8.3
}
```

## Örnek 27

başka bir kayıtta:

```json
{
  "voteAvrage": 7.9
}
```

## Örnek 28

başka bir kayıtta ise:

```json
{
  "rating": 9
}
```

## Örnek 29

Bu durumda:

```text
Film puanı hangi alanda tutuluyor?
```

## Örnek 30

sorusunun tek bir cevabı kalmaz.

Bu nedenle Schema dışı alanlara izin vermek gerçekten ihtiyaçtan doğan bilinçli bir karar olmalıdır.

---

## Projemizde Bir Değişiklik Yapacak mıyız?

Bu bölümde hayır.

Movie Schema'mızda ayrıca bir `strict` ayarı eklemeyeceğiz, çünkü Mongoose zaten varsayılan olarak `strict: true` kullanıyor.

Dosyamız:

```text
src/db/models/movie.js
```

## Örnek 31

şu hâliyle devam eder:

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

## Örnek 32

`errorHandler.js` dosyamız da değişmez. Çünkü projede `strict: "throw"` kullanma kararı vermedik; dolayısıyla teorik olarak var olan `StrictModeError` için henüz özel bir Response davranışı eklememize gerek yok.

Bu yaklaşım kitap boyunca izlediğimiz sırayı korur:

```text
önce ihtiyaç
↓
sonra kod
```

## Örnek 33

---

## Baştaki Örneğe Dönelim

Başlangıçta şu veriye bakmıştık:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAvrage": 8.3
}
```

## Örnek 34

Artık bu örneği iki katmanda okuyabiliyoruz.

Bizim mevcut Controller'ımız yalnızca `title`, `releaseYear` ve `voteAverage` alanlarını seçtiği için yanlış yazılmış `voteAvrage` alanı Mongoose'a hiç ulaşmayabilir.

Ancak Schema dışı bir alan gerçekten Model'e ulaşırsa, varsayılan `strict: true` davranışı bu alanın kaydedilmesini engeller.

Böylece validation ile strict arasındaki sınır da netleşir:

```text
voteAverage: 25
↓
alan doğru, değer yanlış
↓
validation
```

## Örnek 35



```text
voteAvrage: 8.3
↓
alan Schema'da yok
↓
strict
```

## Örnek 36

---

## 🧠 Akılda Kalsın

`strict`, **Schema dışı alanların Model seviyesinde nasıl ele alınacağını** belirler.

Varsayılan `strict: true` olduğu için mevcut Movie Schema'mıza ek bir ayar yazmamız gerekmez.

En önemli ayrım:

```text
validation → değeri kontrol eder
strict     → alanın Schema'da bulunup bulunmadığıyla ilgilenir
```

## Örnek 37

Ayrıca Controller alan seçimi ile `strict` aynı şey değildir; biri Request katmanında, diğeri Model katmanında görev yapar.

---

## Bölüm Özeti

Bu bölümde, doğru bir değerin yanlış bir alan adıyla gönderilmesi durumunu ele aldık ve Mongoose'un `strict` seçeneğinin Schema dışı alanlara nasıl davranacağını belirlediğini gördük.

Varsayılan davranışın mevcut projemiz için yeterli olduğuna karar verdiğimizden kodu değiştirmedik. Böylece yeni bir seçeneği yalnızca öğrendiğimiz için projeye eklemek yerine, hangi ihtiyacı çözdüğünü anlayarak ilerledik.

---

## Sonraki Adım

Movie Schema'mız artık iki farklı soruya cevap verebiliyor:

```text
Bu alan Schema'da tanımlı mı?
↓
strict
```

## Örnek 38

ve:

```text
Bu alanın değeri kurala uygun mu?
↓
validation
```
