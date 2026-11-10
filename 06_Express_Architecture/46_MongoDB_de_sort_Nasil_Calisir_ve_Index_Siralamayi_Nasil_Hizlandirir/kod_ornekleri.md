# Bölüm 46 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# MongoDB'de sort() Nasıl Çalışır ve Index Sıralamayı Nasıl Hızlandırır?

## En Yüksek Puanlı Film Neden En Üstte Değil?

Movie API'miz filmleri getiriyor.

Ama şu sonuç geldiğini düşünün:

```text
Arrival       → 7.9
Dune          → 8.3
Interstellar  → 8.7
Inception     → 8.4
```

## Örnek 2

Kullanıcıya:

```text
en yüksek puanlı filmler
```

## Örnek 3

listesi göstermek istiyoruz.

O hâlde beklediğimiz sıra:

```text
Interstellar  → 8.7
Inception     → 8.4
Dune          → 8.3
Arrival       → 7.9
```

## Örnek 4

olmalı.

Yani artık MongoDB'ye yalnızca:

```text
Filmleri getir.
```

## Örnek 5

demek yetmiyor.

Şunu da söylememiz gerekiyor:

> **Hangi sırayla getir?**

Bu ihtiyacın cevabı:

```text
sort()
```

## Örnek 6

Burada önemli bir nokta var:

```text
find({})
↓
filmleri getir
```

## Örnek 7

demek:

```text
filmleri benim istediğim
belirli sırada getir
```

## Örnek 8

demek değildir.

Sonuçların hangi sırada gelmesini önemsiyorsak bu sırayı açıkça `sort()` ile belirtiriz.

---

## sort() ile Sıralamayı Belirleyelim

Şu anda `getMovies` Controller'ımızda:

```js
const movies = await Movie.find({});
```

## Örnek 9

kullanıyoruz.

Dosya:

```text
src/controllers/movies.js
```

## Örnek 10

Filmleri puanı en yüksekten en düşüğe getirmek için:

```js
const movies = await Movie.find({}).sort({
  voteAverage: -1,
});
```

## Örnek 11

yazarız.

Buradaki:

```text
-1
↓
azalan sıra
↓
büyükten küçüğe
```

## Örnek 12

demektir.

Yani:

```text
8.7
↓
8.4
↓
8.3
↓
7.9
```

## Örnek 13

Tersini istersek:

```js
const movies = await Movie.find({}).sort({
  voteAverage: 1,
});
```

## Örnek 14

kullanırız.

Bu kez:

```text
1
↓
artan sıra
↓
küçükten büyüğe
```

## Örnek 15

olur.

44. bölümde index tanımlarında gördüğümüz `1` değerinin:

```text
artan yön
```

## Örnek 16

anlamına geldiğini öğrenmiştik.

`sort()` içinde de aynı yön gösterimini kullanıyoruz:

```text
1  → artan
-1 → azalan
```

## Örnek 17

---

## "En Yeni Film" Derken Hangisini Kastediyoruz?

Burada küçük ama önemli bir ayrım yapalım.

Filmleri vizyon yılına göre:

```text
yeni yapım
↓
eski yapım
```

## Örnek 18

sıralamak istiyorsak:

```js
Movie.find({}).sort({
  releaseYear: -1,
});
```

## Örnek 19

kullanırız.

Ama:

```text
API'ye en son eklenen film
```

## Örnek 20

hangisi diye soruyorsak başka bir alan gerekir.

42. bölümde öğrendiğimiz:

```text
createdAt
```

## Örnek 21

Document'ın sisteme ne zaman eklendiğini gösteriyordu.

Bu durumda:

```js
Movie.find({}).sort({
  createdAt: -1,
});
```

## Örnek 22

kullanabiliriz.

Yani:

```text
releaseYear
↓
film hangi yıl yayımlandı?
```

## Örnek 23



```text
createdAt
↓
bu kayıt sistemimize ne zaman eklendi?
```

## Örnek 24

Aynı şey değildir.

---

## Puanlar Eşitse Ne Olacak?

Şimdi iki filmin puanı aynı olsun:

```text
Dune
↓
8.3
↓
2021

Arrival
↓
8.3
↓
2016
```

## Örnek 25

Yalnızca:

```js
.sort({
  voteAverage: -1,
})
```

## Örnek 26

dersek:

```text
puanı yüksek olan önce
```

## Örnek 27

kuralını vermiş oluruz.

Ama puan eşitse hangisinin önce geleceğini ayrıca söylemedik.

Puanlar eşit olduğunda daha yeni yapım önce gelsin istiyorsak:

```js
Movie.find({}).sort({
  voteAverage: -1,
  releaseYear: -1,
});
```

## Örnek 28

yazabiliriz.

Bunu cümle gibi okuyalım:

```text
önce
↓
voteAverage büyükten küçüğe

eşitse
↓
releaseYear büyükten küçüğe
```

## Örnek 29

Örneğin:

```text
Interstellar  → 8.7 — 2014
Dune          → 8.3 — 2021
Arrival       → 8.3 — 2016
```

## Örnek 30

sonucu:

```text
Interstellar
↓
Dune
↓
Arrival
```

## Örnek 31

şeklinde olur.

Artık `sort()`ın yaptığı iş net:

> **Sonuçların hangi sırada döneceğini belirler.**

---

## Puan ve Yıl da Eşitse?

Şimdi iki filmin hem puanı hem de yılı aynı olsun:

```text
Film A
↓
8.3 — 2021

Film B
↓
8.3 — 2021
```

## Örnek 32

Şu sıralamada:

```js
.sort({
  voteAverage: -1,
  releaseYear: -1,
})
```

## Örnek 33

iki film için de sıralama değerleri aynıdır.

Bu durumda:

```text
hangisi önce gelecek?
```

## Örnek 34

sorusuna henüz kesin bir kural vermiş olmayız.

MongoDB'de aynı sort değerlerine sahip Document'ların kendi aralarındaki sırasına güvenmemeliyiz.

Eğer her çalıştırmada tutarlı bir son sıra istiyorsak benzersiz bir alanı son karar verici olarak ekleyebiliriz.

Bunun için zaten her Document'ta benzersiz olan:

```text
_id
```

## Örnek 35

alanını kullanabiliriz:

```js
Movie.find({}).sort({
  voteAverage: -1,
  releaseYear: -1,
  _id: 1,
});
```

## Örnek 36

Bunu şöyle okuyabiliriz:

```text
önce puan
↓
eşitse yıl
↓
o da eşitse _id
```

## Örnek 37

Burada `_id`yi:

```text
film daha iyi
veya
film daha yeni
```

## Örnek 38

anlamında kullanmıyoruz.

Yalnızca eşitlik tamamen bozulamadığında sonuç sırasını kesinleştiren son alan olarak kullanıyoruz.

Bu ayrım bir sonraki bölümde sonuçları parçalara ayırmaya başladığımızda daha da önemli olacak.

---

## Sıralama Doğru. Peki Verimli mi?

Buraya kadar yalnızca:

```text
istediğimiz sonucu
```

## Örnek 39

tanımladık.

Ama 45. bölüm bize başka bir soru sormayı öğretmişti:

> MongoDB bu sonucu elde etmek için ne kadar iş yaptı?

Şimdi collection'da:

```text
1.000.000 film
```

## Örnek 40

olduğunu düşünün.

Sorgumuz:

```js
Movie.find({}).sort({
  voteAverage: -1,
});
```

## Örnek 41

MongoDB istediğimiz sırayı üretecek.

Ama bunu nasıl elde edecek?

İşte index burada tekrar karşımıza çıkar.

---

## Uygun Index Yoksa: SORT

MongoDB istediğimiz sıralamayı sağlayabilecek uygun bir index kullanamıyorsa sonuçları ayrıca sıralaması gerekebilir.

Kabaca:

```text
Document'ları bul
↓
sırala
↓
Response için hazırla
```

## Örnek 42

Sorgu planında böyle bir durumda:

```text
SORT
```

## Örnek 43

aşaması görebiliriz.

Yani:

```text
SORT
↓
MongoDB ayrıca sıralama yaptı
```

## Örnek 44

Bu tek başına bir hata değildir.

Yalnızca MongoDB'nin sıralama için ayrıca iş yaptığını gösterir.

---

## Index Varsa Ne Değişebilir?

Şimdi `voteAverage` alanında şu index'in bulunduğunu düşünelim:

```js
movieSchema.index({
  voteAverage: 1,
});
```

## Örnek 45

Zihnimizde index sırası:

```text
7.9
↓
8.3
↓
8.4
↓
8.7
```

## Örnek 46

şeklinde olsun.

Biz ise:

```js
.sort({
  voteAverage: -1,
})
```

## Örnek 47

istiyoruz.

İlk bakışta:

```text
index → 1
sort  → -1
```

## Örnek 48

uyuşmuyor gibi görünebilir.

Ama tek alanlı bir index iki yönde de taranabilir.

MongoDB index üzerinde ters yönde ilerleyebilir:

```text
8.7
↓
8.4
↓
8.3
↓
7.9
```

## Örnek 49

Böylece uygun index, MongoDB'nin ayrıca büyük bir sıralama işlemi yapmadan istediğimiz sırayı elde etmesine yardımcı olabilir.

Buradaki ayrım:

```text
sort()
↓
hangi sırayı istiyorum?
```

## Örnek 50



```text
index
↓
MongoDB bu sıraya
daha verimli ulaşabilir mi?
```

## Örnek 51

Ama index'in yapmadığı bir şey de var.

Şu sorgu:

```js
Movie.find({}).sort({
  voteAverage: -1,
});
```

## Örnek 52

collection'da:

```text
1.000.000 film
```

## Örnek 53

varsa hâlâ çok büyük bir sonuç kümesi döndürebilir.

Index:

```text
sıralama işini
daha verimli hâle getirebilir
```

## Örnek 54

ama:

```text
kaç Document döneceğini
kendiliğinden azaltmaz
```

## Örnek 55

Yani iki ayrı problemimiz var:

```text
hangi sırayla?
↓
sort()
```

## Örnek 56



```text
kaç tane?
↓
henüz çözmedik
```

## Örnek 57

Bir sonraki bölümde `limit()` ve `skip()` ihtiyacının doğacağı yer tam olarak burasıdır.

---

## Birden Fazla Alanla Sıralamada Index Ne Olur?

Şu sıralamayı kullanıyoruz:

```js
.sort({
  voteAverage: -1,
  releaseYear: -1,
})
```

## Örnek 58

Bu kez yalnızca `voteAverage` değil:

```text
voteAverage
+
releaseYear
```

## Örnek 59

birlikte önemlidir.

Böyle bir sıralamayı desteklemek için compound index de aynı alanları uygun sırada içerebilir.

Örneğin:

```js
{
  voteAverage: 1,
  releaseYear: 1,
}
```

## Örnek 60

index'i:

```js
{
  voteAverage: -1,
  releaseYear: -1,
}
```

## Örnek 61

sıralamasını index'i ters yönde tarayarak destekleyebilir.

Burada 45. bölümde öğrendiğimiz kural devam ediyor:

```text
compound index
↓
alan sırası önemlidir
```

## Örnek 62

Sıralama birden fazla alan içerdiğinde yönlerin birbiriyle ilişkisi de önem kazanır.

Şu aşamada bütün kombinasyonları ezberlemeyeceğiz.

Daha değerli alışkanlık:

```text
sort hangi alanları istiyor?
↓
hangi index'ler var?
↓
MongoDB gerçekten hangisini kullandı?
```

## Örnek 63

sorularını sormaktır.

---

## Bunu Nasıl Kontrol Ederiz?

45. bölümde bunun aracını zaten öğrendik:

```text
explain()
```

## Örnek 64

Bu yüzden aynı konuyu baştan anlatmayacağız.

Sıralama sorgusunu incelemek istersek:

```js
const stats = await Movie.find({})
  .sort({
    voteAverage: -1,
  })
  .explain("executionStats");
```

## Örnek 65

kullanabiliriz.

Bu kez `explain()`e sorduğumuz soru:

> **MongoDB bu sıralamayı nasıl elde etti?**

Çıktıda ayrıca:

```text
SORT
```

## Örnek 66

aşaması görürsek MongoDB'nin sıralama işlemi yaptığını anlayabiliriz.

Uygun bir index sıralamayı sağlıyorsa ayrı bir `SORT` aşamasına ihtiyaç kalmayabilir.

---

## Her sort() İçin Index Eklemeli miyiz?

Hayır.

Örneğin:

```js
movieSchema.index({
  voteAverage: 1,
});
```

## Örnek 67

satırını sırf bu bölümü okuduk diye projemize eklemiyoruz.

Önce şu sorular önemlidir:

```text
Bu sıralama sık kullanılıyor mu?
```

## Örnek 68



```text
Collection büyük mü?
```

## Örnek 69



```text
Sorgu gerçekten yavaşlıyor veya gereğinden fazla kaynak kullanıyor mu?
```

## Örnek 70



```text
Mevcut index'lerden biri zaten yardımcı oluyor mu?
```

## Örnek 71

Küçük bir collection'da `SORT` görmek önemli bir problem olmayabilir.

Dolayısıyla:

```text
sort() kullandım
↓
index ekle
```

## Örnek 72

diye otomatik bir kural yoktur.

45. bölümde olduğu gibi burada da kararımız:

```text
tahmin
```

## Örnek 73

değil:

```text
ölçüm
```

## Örnek 74

üzerinden verilmelidir.

---

## 🧠 Akılda Kalsın

`sort()`:

```text
sonuçların hangi sırada
döneceğini belirler
```

## Örnek 75

Temel yönler:

```text
1
↓
artan
```

## Örnek 76



```text
-1
↓
azalan
```

## Örnek 77

Birden fazla alan:

```js
.sort({
  voteAverage: -1,
  releaseYear: -1,
})
```

## Örnek 78

şeklinde kullanılabilir.

Bunu:

```text
önce puan
↓
eşitse yıl
```

## Örnek 79

diye okuyabiliriz.

Eşitlik tamamen devam ediyorsa tutarlı bir son sıra için:

```text
_id
↓
son karar verici
```

## Örnek 80

olarak eklenebilir.

Performans tarafında:

```text
uygun index yok
↓
SORT aşaması gerekebilir
```

## Örnek 81



```text
uygun index var
↓
MongoDB index sırasından
yararlanabilir
```

## Örnek 82

Ve önemli ayrım:

```text
releaseYear
↓
filmin yayın yılı
```

## Örnek 83



```text
createdAt
↓
Document'ın sisteme eklenme zamanı
```

## Örnek 84

---

## Sonraki Adım

Filmleri artık istediğimiz sırada getirebiliyoruz.

Ama collection'da:

```text
10.000 film
```

## Örnek 85

varsa hepsini tek Response içinde döndürmek istemeyebiliriz.

Belki önce:

```text
10 film
```
