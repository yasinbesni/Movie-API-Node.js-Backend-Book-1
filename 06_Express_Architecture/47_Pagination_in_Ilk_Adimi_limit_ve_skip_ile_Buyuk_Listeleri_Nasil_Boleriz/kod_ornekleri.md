# Bölüm 47 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Pagination'ın İlk Adımı: limit() ve skip() ile Büyük Listeleri Nasıl Böleriz?

## 10.000 Filmi Tek Response İçinde mi Göndereceğiz?

46. bölümde filmleri istediğimiz sırada getirmeyi öğrendik. Şimdi katalogda on bin film olduğunu, kullanıcınınsa yalnızca ilk on filmi görmek istediğini düşünelim. Hepsini tek yanıtta gönderirsek kullanıcı gereksiz yere büyük bir veri bekleyecek.

Sorunumuz artık yalnızca filmleri **hangi sırada** göstereceğimiz değil, **kaçını** göndereceğimiz. Bunu Movie API'nin mevcut `src/controllers/movies.js` dosyasındaki Controller üzerinde çözeceğiz; yeni bir dosya oluşturmuyoruz.

Örneğin:

```js
Movie.find({}).sort({
  voteAverage: -1,
  releaseYear: -1,
  _id: 1,
});
```

## Örnek 2

ile:

```text
önce yüksek puan
↓
eşitse daha yeni yıl
↓
o da eşitse _id
```

## Örnek 3

şeklinde tutarlı bir sıra oluşturabiliyoruz.

Ama collection büyüsün:

```text
10 film
↓
100 film
↓
1.000 film
↓
10.000 film
```

## Örnek 4

Kullanıcı ana sayfayı açtığında gerçekten:

```text
10.000 filmi
tek Response içinde
```

## Örnek 5

göndermek ister miyiz?

Muhtemelen hayır.

Kullanıcı ekranda belki yalnızca:

```text
10 film
```

## Örnek 6

görecek.

O hâlde yeni ihtiyacımız şu:

> **MongoDB'den dönecek Document sayısını nasıl sınırlandıracağız?**

İşte burada:

```text
limit()
```

## Örnek 7

devreye girer.

---

## limit() Ne Yapar?

`limit()` bir sorgunun en fazla kaç Document döndüreceğini belirler.

Örneğin:

```js
const movies = await Movie.find({})
  .sort({
    voteAverage: -1,
    releaseYear: -1,
    _id: 1,
  })
  .limit(10);
```

## Örnek 8

Buradaki:

```js
.limit(10)
```

## Örnek 9

şunu söyler:

```text
en fazla
10 Document döndür
```

## Örnek 10

Collection'da:

```text
10.000 film
```

## Örnek 11

olsa bile bu sorgudan en fazla:

```text
10 film
```

## Örnek 12

döner.

---

## Ama Hangi 10 Film?

Burada önemli bir soru var:

> İlk karşılaştığın herhangi 10 filmi mi getir, yoksa en yüksek puanlı 10 filmi mi?

Bizim istediğimiz:

```text
en yüksek puanlı
ilk 10 film
```

## Örnek 13

Bu yüzden `sort()` ile:

```text
hangi sırayı istediğimizi
```

## Örnek 14

belirliyoruz.

`limit()` ise bu sıralı sonuçlardan:

```text
en fazla kaç tane
```

## Örnek 15

alacağımızı belirliyor.

Kod:

```js
Movie.find({})
  .sort({
    voteAverage: -1,
    releaseYear: -1,
    _id: 1,
  })
  .limit(10);
```

## Örnek 16

Bunu şöyle okuyabiliriz:

```text
filmleri istediğim sıraya göre düşün
↓
bu sıradan en fazla 10 tane döndür
```

## Örnek 17

Burada önemli olan kod satırlarını ezberlemek değil.

İki görevi ayırmak:

```text
sort()
↓
hangi sırayla?
```

## Örnek 18



```text
limit()
↓
kaç tane?
```

## Örnek 19

---

## İlk 10 Geldi. Peki Sonraki 10?

İlk ekranda:

```text
1 – 10
```

## Örnek 20

arasındaki filmleri gösterdik.

Kullanıcı şimdi devamını istiyor:

```text
11 – 20
```

## Örnek 21

Bu kez yalnızca:

```text
10 tane getir
```

## Örnek 22

demek yetmez.

Çünkü yine ilk 10 filmi alırız.

MongoDB'ye şunu da söylemeliyiz:

> **İlk 10 sonucu geç.**

İşte burada:

```text
skip()
```

## Örnek 23

devreye girer.

---

## skip() Ne Yapar?

`skip()` sonuçların başından kaç Document'ın atlanacağını belirler.

İkinci 10 filmi almak için:

```js
const movies = await Movie.find({})
  .sort({
    voteAverage: -1,
    releaseYear: -1,
    _id: 1,
  })
  .skip(10)
  .limit(10);
```

## Örnek 24

Buradaki:

```js
.skip(10)
```

## Örnek 25

şunu söyler:

```text
ilk 10 sonucu geç
```

## Örnek 26

Ardından:

```js
.limit(10)
```

## Örnek 27

şunu söyler:

```text
en fazla 10 sonuç döndür
```

## Örnek 28

Akış:

```text
sıralı sonuçlar
↓
ilk 10'u geç
↓
sonraki 10'u al
```

## Örnek 29

Artık büyük listeyi parçalara ayırmaya başladık.

Bu yaklaşımın adı:

```text
pagination
```

## Örnek 30

Türkçe:

```text
sayfalama
```

## Örnek 31

---

## İlk Üç Sayfada skip Nasıl Değişir?

Her sayfada:

```text
10 film
```

## Örnek 32

göstereceğimizi düşünelim.

```text
1. sayfa
↓
skip 0
↓
1 – 10
```

## Örnek 33



```text
2. sayfa
↓
skip 10
↓
11 – 20
```

## Örnek 34



```text
3. sayfa
↓
skip 20
↓
21 – 30
```

## Örnek 35

Bir düzen oluştu:

```text
1. sayfa → 0 atla
2. sayfa → 10 atla
3. sayfa → 20 atla
```

## Örnek 36

Peki bu sayıyı her seferinde elle mi hesaplayacağız?

Hayır.

---

## skip Değerini Nasıl Hesaplarız?

3. sayfaya gitmek istiyoruz.

Her sayfada:

```text
10 film
```

## Örnek 37

var.

3. sayfadan önce tamamlanmış:

```text
2 sayfa
```

## Örnek 38

bulunuyor.

Yani:

```text
2 × 10
↓
20 film
```

## Örnek 39

atlamamız gerekiyor.

Genel formül:

```text
skip = (page - 1) × limit
```

## Örnek 40

Örneğin:

```text
page = 3
limit = 10
```

## Örnek 41

ise:

```text
skip
↓
(3 - 1) × 10
↓
20
```

## Örnek 42

Kodda:

```js
const page = 3;
const limit = 10;

const skip = (page - 1) * limit;
```

## Örnek 43

---

## Hesabı Sorguya Bağlayalım

```js
const page = 3;
const limit = 10;

const skip = (page - 1) * limit;

const movies = await Movie.find({})
  .sort({
    voteAverage: -1,
    releaseYear: -1,
    _id: 1,
  })
  .skip(skip)
  .limit(limit);
```

## Örnek 44

Şimdi üç değişkenimizin görevi açık:

```text
page
↓
hangi sayfa?
```

## Örnek 45



```text
limit
↓
bir sayfada kaç film?
```

## Örnek 46



```text
skip
↓
o sayfaya ulaşmadan önce
kaç film geçilecek?
```

## Örnek 47

Sorgudaki üç metot da başka sorulara cevap veriyor:

```text
sort()
↓
hangi sırayla?
```

## Örnek 48



```text
skip()
↓
nereden başla?
```

## Örnek 49



```text
limit()
↓
kaç tane getir?
```

## Örnek 50

Pagination'ın temel mekanizması burada ortaya çıkıyor.

---

## Neden 46. Bölümde _id Ekledik?

46. bölümde sıralamamızı:

```js
.sort({
  voteAverage: -1,
  releaseYear: -1,
  _id: 1,
})
```

## Örnek 51

şeklinde kesinleştirmiştik.

Şimdi bunun neden önemli olduğunu daha net görebiliriz.

İki film aynı değerlere sahip olsun:

```text
Film A
↓
voteAverage: 8.3
releaseYear: 2021
```

## Örnek 52



```text
Film B
↓
voteAverage: 8.3
releaseYear: 2021
```

## Örnek 53

Yalnızca:

```text
voteAverage
+
releaseYear
```

## Örnek 54

ile sıralarsak bu iki Document'ın kendi arasındaki sıra kesin değildir.

Ama pagination yaparken:

```text
1. sayfa
↓
ilk 10

2. sayfa
↓
sonraki 10
```

## Örnek 55

diyoruz.

Bu yüzden aynı veri kümesi için sıralamanın tutarlı olması önemlidir.

Son karar verici olarak benzersiz:

```text
_id
```

## Örnek 56

alanını eklediğimizde eşitliği bozmuş oluruz.

Yani:

```text
puan eşit
↓
yıla bak

yıl da eşit
↓
_id'ye bak
```

## Örnek 57

Bu, aynı veriler üzerinde pagination yaparken sayfaların dayandığı sırayı kesinleştirir.

Burada küçük bir sınırı da bilelim:

```text
_id ile tutarlı sort
```

## Örnek 58

sonuç sırasını kesinleştirir.

Ama iki Request arasında collection'a yeni Document eklenirse, bir Document silinirse veya sıralamayı etkileyen değerler değişirse `skip()` tabanlı sayfaların konumu yine kayabilir.

Şimdilik aynı veri kümesi üzerinden temel pagination mantığını öğreniyoruz.

---

## Değişken ile Query Metodu Aynı Şey Değil

Şu kod ilk bakışta biraz garip görünebilir:

```js
const limit = 10;
const skip = (page - 1) * limit;

Movie.find({})
  .skip(skip)
  .limit(limit);
```

## Örnek 59

Çünkü:

```text
limit
```

## Örnek 60

ve:

```text
.limit()
```

## Örnek 61

çok benziyor.

Ama aynı şey değiller.

```text
limit
↓
JavaScript değişkeni
↓
örneğin 10 değerini tutar
```

## Örnek 62



```text
.limit()
↓
Mongoose Query metodu
↓
dönecek Document sayısını sınırlar
```

## Örnek 63

Aynı ayrım `skip` için de geçerli:

```text
skip
↓
hesapladığımız sayı
```

## Örnek 64



```text
.skip()
↓
Mongoose Query metodu
↓
o kadar sonucu atlar
```

## Örnek 65

Örneğin:

```text
skip = 20
```

## Örnek 66

ise:

```js
.skip(skip)
```

## Örnek 67

aslında:

```js
.skip(20)
```

## Örnek 68

anlamına gelir.

---

## skip() ile limit() Hangi Sırada Yazılmalı?

Biz kodumuzu:

```js
.sort(...)
.skip(skip)
.limit(limit)
```

## Örnek 69

şeklinde yazıyoruz.

Bu sıra insan gözüyle okumayı kolaylaştırıyor:

```text
sırayı belirle
↓
öncekileri geç
↓
gereken kadarını al
```

## Örnek 70

Ama burada metodların yazılma sırasını bir MongoDB kuralı sanmayalım.

`skip()` ve `limit()` aynı sorguda birlikte kullanıldığında:

```js
.skip(10)
.limit(10)
```

## Örnek 71

ile:

```js
.limit(10)
.skip(10)
```

## Örnek 72

aynı veri kümesi ve aynı sort için aynı sonucu verir.

MongoDB sonuç sırasına göre önce atlanacak kısmı belirler, sonra dönecek Document sayısını sınırlar.

Biz kitap boyunca yalnızca daha kolay okunması için:

```js
.sort(...)
.skip(...)
.limit(...)
```

## Örnek 73

biçimini kullanacağız.

---

## Mevcut Controller'a page Değerini Hemen Ekliyor muyuz?

Henüz hayır.

Şu anda pagination'ın MongoDB tarafındaki temelini öğrendik:

```text
sort()
skip()
limit()
```

## Örnek 74

Biraz önce kullandığımız:

```js
const page = 3;
const limit = 10;
```

## Örnek 75

değerlerini biz kodun içine yazdık.

Gerçek API'de ise kullanıcı:

```text
hangi sayfayı istediğini
```

## Örnek 76

Request ile bildirecek.

Ama bunun için önce URL üzerinden gönderilen değerleri nasıl okuyacağımızı öğrenmemiz gerekiyor.

Şimdilik yeni bir kavram eklemeden `skip()` ve `limit()` mantığını sağlamlaştırıyoruz.

---

## Peki skip Çok Büyürse?

Şimdilik:

```text
1. sayfa
↓
skip 0

2. sayfa
↓
skip 10

100. sayfa
↓
skip 990
```

## Örnek 77

gayet anlaşılır.

Ama çok ileri bir sayfaya gidersek:

```text
skip 100000
```

## Örnek 78

gibi büyük bir değer oluşabilir.

MongoDB `skip()` kullanırken başlangıçtan atlanacak sonuçlar üzerinden ilerlemek zorundadır.

Bu yüzden offset büyüdükçe `skip()` tabanlı pagination daha yavaş hâle gelebilir.

Bu, öğrendiğimiz yöntemin yanlış olduğu anlamına gelmez.

Başlangıç seviyesinde:

```text
page
+
skip
+
limit
```

## Örnek 79

pagination mantığını anlamak için çok uygun bir yöntemdir.

Daha büyük veri setlerinde neden başka pagination yaklaşımlarının kullanılabildiğini ileride ayrıca değerlendirebiliriz.

---

## Neden Bütün Veriyi Tek Seferde Göndermiyoruz?

Başlangıçtaki 10.000 film örneğine dönelim.

Hepsini tek Response içinde gönderirsek:

```text
MongoDB
↓
çok fazla Document getirir
```

## Örnek 80



```text
Backend
↓
çok fazla veriyi işler
```

## Örnek 81



```text
Network
↓
daha büyük Response taşır
```

## Örnek 82



```text
Frontend
↓
kullanıcının aynı anda
görmeyeceği veriyi de alır
```

## Örnek 83

Pagination'ın amacı yalnızca ekrana:

```text
1 2 3 4 5
```

## Örnek 84

gibi sayfa numaraları koymak değildir.

Asıl fikir:

```text
büyük sonuç kümesi
↓
ihtiyaç duyulan parçayı getir
```

## Örnek 85

dir.

---

## 🧠 Akılda Kalsın

`limit()`:

```text
en fazla kaç Document dönecek?
```

## Örnek 86

sorusuna cevap verir.

`skip()`:

```text
kaç Document atlanacak?
```

## Örnek 87

sorusuna cevap verir.

Sayfa tabanlı hesap:

```text
skip = (page - 1) × limit
```

## Örnek 88

Örneğin:

```text
page = 4
limit = 10
```

## Örnek 89

ise:

```text
skip
↓
30
```

## Örnek 90

olur.

Ve üç temel görev:

```text
sort()
↓
hangi sırayla?
```

## Örnek 91



```text
skip()
↓
nereden başla?
```

## Örnek 92



```text
limit()
↓
kaç tane getir?
```

## Örnek 93

---

## Sonraki Adım

Şu anda:

```js
const page = 3;
const limit = 10;
```

## Örnek 94

değerlerini kendimiz yazıyoruz.

Ama gerçek bir API'de kullanıcı:

```text
2. sayfayı getir
```

## Örnek 95

veya:

```text
sayfada 20 film göster
```

## Örnek 96

demek isteyecek.

Peki bu değerleri Request ile nasıl gönderecek?

Backend URL'den gelen:

```text
page
limit
```
