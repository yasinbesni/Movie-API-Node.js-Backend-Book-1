# Bölüm 45 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# MongoDB Index'ler Sorguları Nasıl Hızlandırır? COLLSCAN ve IXSCAN Nedir?

## Aynı Sorgu, Farklı Miktarda İş

Movie API'mizde şu sorguyu çalıştırdığımızı düşünün:

```js
Movie.find({
  title: "Dune",
  releaseYear: 2021,
});
```

## Örnek 2

Collection'da yalnızca birkaç film varken pek düşünmeyiz.

Ama film sayısı büyüsün:

```text
10
↓
1.000
↓
100.000
↓
1.000.000
```

## Örnek 3

Kodumuz hâlâ aynı.

Aradığımız film de aynı:

```text
Dune — 2021
```

## Örnek 4

Ama MongoDB'nin yapması gereken iş aynı mı?

İşte index'in performans tarafını bu soruyla anlayacağız.

---

## Uygun Index Yoksa MongoDB Ne Yapar?

Şimdilik:

```text
title + releaseYear
```

## Örnek 5

sorgumuzu destekleyen uygun bir index olmadığını düşünelim.

Collection'da şu Document'lar olsun:

```text
Document 1
↓
Arrival — 2016

Document 2
↓
Interstellar — 2014

Document 3
↓
Dune — 1984

Document 4
↓
Dune — 2021

Document 5
↓
Inception — 2010
```

## Örnek 6

MongoDB'nin aradığı:

```text
Dune — 2021
```

## Örnek 7

İlk Document:

```text
Arrival — 2016
↓
eşleşmedi
```

## Örnek 8

İkinci:

```text
Interstellar — 2014
↓
eşleşmedi
```

## Örnek 9

Üçüncü:

```text
Dune — 1984
↓
yıl eşleşmedi
```

## Örnek 10

Dördüncü:

```text
Dune — 2021
↓
eşleşti
```

## Örnek 11

Burada küçük ama önemli bir ayrıntı var.

Sorgumuz:

```js
find()
```

## Örnek 12

kullanıyor.

Yani MongoDB yalnızca:

```text
ilk eşleşeni bul
```

## Örnek 13

demiyor.

Collection'da başka eşleşmeler de bulunabilir.

Bu yüzden tarama devam eder:

```text
Document 5
↓
Inception — 2010
↓
eşleşmedi

collection sonu
↓
tarama tamamlandı
```

## Örnek 14

Beş Document için bu göz korkutucu değil.

Ama aynı taramayı:

```text
1.000.000 Document
```

## Örnek 15

üzerinde düşünün.

İşte uygun index olmadığında maliyet burada görünür hâle gelir.

---

## Bunun Adı COLLSCAN

MongoDB'nin collection'daki Document'ları taradığı çalışma biçimi sorgu planında:

```text
COLLSCAN
```

## Örnek 16

olarak karşımıza çıkabilir.

Kelimeyi parçalayalım:

```text
COLL
↓
Collection

SCAN
↓
Tarama
```

## Örnek 17

Yani:

```text
COLLSCAN
↓
Collection'ı tara
```

## Örnek 18

Bu bölümde aklımızda tutacağımız ilk kelime bu.

---

## COLLSCAN Görmek Her Zaman Felaket mi?

Hayır.

Collection'da:

```text
8 Document
```

## Örnek 19

varsa collection taraması önemli bir problem olmayabilir.

Bazı sorgular zaten collection'ın büyük bölümünü okumak zorunda da olabilir.

Dolayısıyla:

```text
COLLSCAN gördüm
↓
uygulamam kötü
```

## Örnek 20

diye otomatik bir sonuç çıkarmıyoruz.

Asıl soru:

> Bu sorgu, bu veri miktarında gereğinden fazla iş mi yapıyor?

Collection büyüdükçe index'ler bu soruda önem kazanır.

---

## Index İşin Neresine Giriyor?

44. bölümde:

```text
title + releaseYear
```

## Örnek 21

alanlarını kullanan bir compound index örneği görmüştük.

Bu bölümde `unique` tarafına değil, index'in **arama** tarafına bakacağız.

Performans mantığını görmek için aynı alan sırasına sahip şu index'i düşünelim:

```js
movieSchema.index({
  title: 1,
  releaseYear: 1,
});
```

## Örnek 22

Burada projeye yeni bir index eklediğimizi söylemiyoruz.

Amacımız yalnızca:

```text
uygun index varsa
arama nasıl değişebilir?
```

## Örnek 23

sorusunu görmek.

44. bölümde index'i zihnimizde şöyle canlandırmıştık:

```text
("Arrival", 2016)
↓
ilgili Document

("Dune", 1984)
↓
ilgili Document

("Dune", 2021)
↓
ilgili Document

("Inception", 2010)
↓
ilgili Document
```

## Örnek 24

Şimdi aradığımız değer:

```text
("Dune", 2021)
```

## Örnek 25

MongoDB uygun index'i kullanırsa önce aramayı index üzerinde daraltabilir:

```text
("Dune", 2021)
↓
index'te eşleşen kayıt
↓
ilgili Document
```

## Örnek 26

Artık temel farkı görebiliyoruz:

```text
uygun index yok
↓
collection'daki Document'ları tara
```

## Örnek 27



```text
uygun index var ve kullanılıyor
↓
index üzerinden aramayı daralt
```

## Örnek 28

---

## Bunun Adı IXSCAN

MongoDB sorgu planında bir index taraması görürsek:

```text
IXSCAN
```

## Örnek 29

ifadesi karşımıza çıkabilir.

Şöyle hatırlayabiliriz:

```text
IX
↓
Index

SCAN
↓
Tarama
```

## Örnek 30

Yani:

```text
IXSCAN
↓
Index'i tara
```

## Örnek 31

Artık iki temel terimimiz var:

```text
COLLSCAN
↓
Collection taraması
```

## Örnek 32



```text
IXSCAN
↓
Index taraması
```

## Örnek 33

![COLLSCAN ve IXSCAN karşılaştırması](images/45-collscan-ixscan-baski.svg)

---

## IXSCAN Varsa MongoDB Document'ı Hiç Okumuyor mu?

Her zaman değil.

Index'imiz:

```text
title
+
releaseYear
```

## Örnek 34

alanları üzerinden kurulmuş olsun.

Ama Movie Document'ımızda başka alanlar da var:

```text
voteAverage
createdAt
updatedAt
...
```

## Örnek 35

MongoDB index üzerinden doğru kaydı bulduktan sonra gerçek Document'ın diğer alanlarına ihtiyaç duyabilir.

Sorgu planında bu durumda:

```text
IXSCAN
↓
FETCH
```

## Örnek 36

gibi bir akış görebiliriz.

Başlangıç seviyesinde şöyle okuyabiliriz:

```text
IXSCAN
↓
Index'ten ilgili kaydı bul

FETCH
↓
Gerçek Document'ı getir
```

## Örnek 37

Yani:

```text
IXSCAN gördüm
↓
collection'a hiç gidilmedi
```

## Örnek 38

demiyoruz.

Index'in önemli faydalarından biri:

```text
hangi Document'ların inceleneceğini
daraltmak
```

## Örnek 39

tır.

---

## Peki MongoDB'nin Hangi Yolu Seçtiğini Nasıl Anlayacağız?

Şimdi bölümün en kullanışlı sorusuna geldik:

> MongoDB bu sorguda gerçekten ne yaptı?

Tahmin etmek zorunda değiliz.

MongoDB bize sorgunun çalışma planını gösterebilir.

Mongoose'ta bunun için:

```text
explain()
```

## Örnek 40

kullanabiliriz.

Ve:

```js
.explain("executionStats")
```

## Örnek 41

dediğimizde yalnızca planı değil, sorgu çalışırken oluşan bazı istatistikleri de görebiliriz.

---

## Bunu Mevcut Projemizde Nerede Deneyebiliriz?

Şu anda Route akışımız:

```text
GET /movies
↓
movies Router
↓
getMovies Controller
```

## Örnek 42

şeklinde.

Dosyamız:

```text
src/controllers/movies.js
```

## Örnek 43

Normalde `getMovies` içinde:

```js
const movies = await Movie.find({});
```

## Örnek 44

kullanıyoruz.

`explain()` davranışını görmek için **yalnızca test sırasında** `getMovies` fonksiyonunu geçici olarak şöyle değiştirebiliriz:

```js
export const getMovies = async (req, res) => {
  const stats = await Movie.find({
    title: "Dune",
    releaseYear: 2021,
  }).explain("executionStats");

  res.status(200).json(stats);
};
```

## Örnek 45

Şimdi:

```text
GET /movies
```

## Örnek 46

Request'i gönderdiğimizde Response olarak film listesini değil:

```text
MongoDB bu sorguyu nasıl çalıştırdı?
```

## Örnek 47

sorusunun ayrıntılı cevabını görürüz.

Bu değişiklik yalnızca sorgu planını geçici olarak incelemek içindir; kalıcı Controller davranışımız değildir.

Test tamamlandıktan sonra `getMovies` fonksiyonumuzu tekrar normal hâline getiririz:

```js
export const getMovies = async (req, res) => {
  const movies = await Movie.find({});

  res.status(200).json({
    message: "Filmler getirildi.",
    data: movies,
  });
};
```

## Örnek 48

Böylece proje endpoint'imizin davranışını kalıcı olarak değiştirmemiş oluruz.

---

## explain() Çıktısı Büyük Görünebilir

İlk kez baktığınızda Response içinde çok fazla alan görebilirsiniz.

Hepsini anlamaya çalışmayın.

MongoDB sürümüne ve seçilen sorgu planına göre çıktı yapısı da farklı görünebilir.

Bu bölümde yalnızca şu beş kavramın peşindeyiz:

```text
COLLSCAN
IXSCAN
nReturned
totalDocsExamined
totalKeysExamined
```

## Örnek 49

Şimdi bunların ne anlattığına bakalım.

---

## nReturned Ne Söylüyor?

```text
nReturned
```

## Örnek 50

sorgunun kaç sonuç döndürdüğünü gösterir.

Örneğin:

```text
nReturned: 1
```

## Örnek 51

görüyorsak:

```text
1 sonuç döndü
```

## Örnek 52

diye okuyabiliriz.

Ama yalnızca kaç sonuç döndüğünü bilmek yeterli değildir.

Çünkü MongoDB o tek sonucu bulmak için çok fazla iş yapmış olabilir.

---

## totalDocsExamined Ne Söylüyor?

```text
totalDocsExamined
```

## Örnek 53

sorgu yürütülürken kaç Document'ın incelendiğini anlamamıza yardımcı olur.

Şu iki hayali sonucu karşılaştıralım:

```text
nReturned: 1
totalDocsExamined: 1
```

## Örnek 54

ve:

```text
nReturned: 1
totalDocsExamined: 100000
```

## Örnek 55

İki durumda da kullanıcı:

```text
1 film
```

## Örnek 56

alıyor.

Ama MongoDB'nin yaptığı iş aynı değil.

İkinci örnekte:

```text
1 sonuç
↓
100.000 Document incelemesi
```

## Örnek 57

var.

İşte performans açısından asıl ilginç olan fark budur.

---

## totalKeysExamined Ne Söylüyor?

Index kullanıldığında:

```text
totalKeysExamined
```

## Örnek 58

değerini de görebiliriz.

Buradaki:

```text
key
```

## Örnek 59

index anahtarını ifade eder.

Bizim zihinsel örneğimizde bir index anahtarı:

```text
("Dune", 2021)
```

## Örnek 60

gibi düşünülebilir.

Dolayısıyla:

```text
totalKeysExamined
```

## Örnek 61

MongoDB'nin sorgu sırasında kaç index anahtarını incelediğini anlamamıza yardımcı olur.

---

## Aynı Sonuç, Farklı Emek

Şimdi iki sadeleştirilmiş senaryoyu yan yana koyalım.

Bunlar gerçek bir sorgudan alınmış sabit sayılar değil; farkı anlamamız için örnek değerlerdir.

### Senaryo A

```text
plan
↓
COLLSCAN

nReturned
↓
1

totalDocsExamined
↓
100000
```

## Örnek 62

Bunu şöyle okuyabiliriz:

```text
1 sonuç döndü
↓
ama çok sayıda Document incelendi
```

## Örnek 63

### Senaryo B

```text
plan
↓
IXSCAN

nReturned
↓
1

totalKeysExamined
↓
1

totalDocsExamined
↓
1
```

## Örnek 64

Bunu da şöyle okuyabiliriz:

```text
1 sonuç döndü
↓
index aramayı ciddi biçimde daralttı
```

## Örnek 65

Burada ezberleyeceğimiz bir:

```text
ideal sayı
```

## Örnek 66

yok.

Soracağımız soru şu:

> MongoDB istediğim sonucu bulmak için ne kadar iş yaptı?

---

## Index Varsa MongoDB Onu Kesin Kullanır mı?

Hayır.

Biz index oluşturduğumuzda:

```text
Bu sorguda mutlaka
bu index'i kullan.
```

## Örnek 67

demiş olmayız.

MongoDB sorgu geldiğinde kullanabileceği yolları değerlendirir.

Buna karar veren yapıya:

```text
query planner
```

## Örnek 68

yani:

```text
sorgu planlayıcısı
```

## Örnek 69

denir.

Başlangıç seviyesinde akış:

```text
Sorgu geldi
↓
hangi yollar kullanılabilir?
↓
uygun planı seç
↓
sorguyu çalıştır
```

## Örnek 70

şeklinde düşünülebilir.

Dolayısıyla:

```text
Index var
↓
kesin IXSCAN
```

## Örnek 71

kuralı yoktur.

Zaten `explain()` kullanmamızın nedeni de budur:

```text
tahmin etmek
```

## Örnek 72

yerine:

```text
gerçekte ne olduğunu görmek
```

## Örnek 73

---

## Compound Index'te Alan Sırası Neden Önemliydi?

Elimizde şu index olduğunu düşünelim:

```js
{
  title: 1,
  releaseYear: 1,
}
```

## Örnek 74

Alan sırası:

```text
1. title
2. releaseYear
```

## Örnek 75

Şimdi üç sorguya bakalım.

---

### Yalnızca title

```js
Movie.find({
  title: "Dune",
});
```

## Örnek 76

Sorgu index'in ilk alanıyla başlıyor:

```text
title
```

## Örnek 77

Bu compound index sorguyu destekleyebilir.

---

### title + releaseYear

```js
Movie.find({
  title: "Dune",
  releaseYear: 2021,
});
```

## Örnek 78

Bu kez:

```text
title
↓
releaseYear
```

## Örnek 79

iki alan da index sırasıyla uyumludur.

Bu sorgu da index tarafından desteklenebilir.

---

### Yalnızca releaseYear

```js
Movie.find({
  releaseYear: 2021,
});
```

## Örnek 80

Burada:

```text
releaseYear
```

## Örnek 81

index'in ilk alanı değildir.

Index:

```text
title
↓
releaseYear
```

## Örnek 82

şeklinde başlar.

Bu nedenle yalnızca `releaseYear` kullanan sorgu bu compound index'in **prefix**'ini kullanmaz.

Başlangıç seviyesinde:

```text
compound index
↓
soldan başlar
```

## Örnek 83

diye hatırlayabiliriz.

Yani bizim örneğimizde:

```text
title
```

## Örnek 84

ve:

```text
title + releaseYear
```

## Örnek 85

index'in başlangıç parçalarıdır.

Ama:

```text
releaseYear
```

## Örnek 86

tek başına değildir.

---

## "O Zaman Her Alana Index Ekleyelim"

Index sorguları hızlandırabiliyorsa ilk tepki şu olabilir:

```text
title
↓
index

releaseYear
↓
index

voteAverage
↓
index

createdAt
↓
index

her alan
↓
index
```

## Örnek 87

Ama index yalnızca avantaj sağlamaz; ek işler ve ek depolama kullanımı da oluşturur.

Yeni bir Document kaydedildiğinde MongoDB yalnızca Document'ı yazmakla kalmaz.

İlgili index kayıtlarını da güncellemesi gerekir:

```text
INSERT
↓
Document'ı kaydet
↓
ilgili index'leri güncelle
```

## Örnek 88

Update ve delete işlemleri de ilgili index'lerde ek çalışma oluşturabilir.

Ayrıca index'ler depolama alanı kullanır.

Dolayısıyla:

```text
index
↓
okumaları hızlandırabilir
```

## Örnek 89

ama:

```text
index
↓
yazma sırasında ek iş
+
ek depolama kullanımı
```

## Örnek 90

getirir.

Bu nedenle:

> En fazla index'e sahip uygulama en hızlı uygulamadır.

diyemeyiz.

---

## Hangi Index'i Eklemeliyiz?

Kendimize:

```text
Schema'da hangi alanlar var?
```

## Örnek 91

diye sormak tek başına yeterli değildir.

Daha önemli soru:

> Uygulamamız hangi sorguları gerçekten sık çalıştırıyor?

Örneğin sık sık:

```js
Movie.find({
  title: "Dune",
  releaseYear: 2021,
});
```

## Örnek 92

çalıştırıyorsak:

```text
title + releaseYear
```

## Örnek 93

üzerindeki bir index anlamlı olabilir.

Ama uygulamamızın yoğun sorgusu, puanı 8 veya daha yüksek olan filmleri bulmak olabilir.

Bu ihtiyacı göstermek için yeni bir MongoDB sorgu operatörüyle tanışalım: `$gte`.

`$gte`, İngilizce *greater than or equal* ifadesinin kısaltmasıdır. Türkçede **büyük veya eşit** anlamına gelir.

```js
Movie.find({
  voteAverage: {
    $gte: 8,
  },
});
```

## Örnek 94

Buradaki koşulu adım adım okuyalım:

```text
voteAverage
↓
Kontrol edilecek alan

$gte
↓
Büyük veya eşit

8
↓
Karşılaştırılacak değer
```

## Örnek 95

Yani sorgu, `voteAverage` değeri 8'e eşit veya 8'den büyük olan filmleri seçer.

| Film puanı | Koşula uyar mı? |
|---|---|
| 7.9 | Hayır |
| 8.0 | Evet |
| 8.3 | Evet |
| 9.0 | Evet |

`$gte`, JavaScript'teki `>=` operatörünün MongoDB sorgu dilindeki karşılığı gibi düşünülebilir. Ancak burada `>=` yazmıyoruz; MongoDB'nin `$gte` operatörünü kullanıyoruz.

Bu bir **filtreleme koşuludur**. Filmlerin puanını değiştirmez, yeni kayıt oluşturmaz ve veri silmez. Yalnızca hangi Document'ların sorgu sonucuna gireceğini belirler.

Şimdi index konumuza dönelim. Uygulamamız sık sık bu puan sorgusunu çalıştırıyorsa:

```text
title + releaseYear
```

## Örnek 96

index'i bu sorgunun temel ihtiyacını çözmez. Çünkü sorgunun koşulu `voteAverage` alanı üzerindedir. Böyle bir durumda puan alanıyla ilgili uygun bir index'in yararlı olup olmayacağını, sorgunun gerçek kullanımına ve `explain("executionStats")` sonuçlarına bakarak değerlendirebiliriz.

Yani index tasarımının başlangıç noktası:

```text
field listesi
```

## Örnek 97

değil:

```text
gerçek sorgular
```

## Örnek 98

olmalıdır.

---

## Baştaki Soruya Dönelim

Başlangıçta şunu sormuştuk:

> MongoDB, çok büyük bir collection içinde `Dune — 2021` kaydını bulmak için ne kadar iş yapıyor?

Artık iki temel yolu biliyoruz.

Uygun index kullanılmıyorsa:

```text
Sorgu
↓
COLLSCAN
↓
Collection'ı tara
```

## Örnek 99

Uygun index kullanılıyorsa:

```text
Sorgu
↓
IXSCAN
↓
Index üzerinden aramayı daralt
↓
gerekirse FETCH
↓
Document'ı getir
```

## Örnek 100

Ve en önemlisi:

```text
MongoDB hangisini yaptı?
```

## Örnek 101

sorusunu yalnızca tahmin etmiyoruz.

```js
.explain("executionStats")
```

## Örnek 102

ile inceleyebiliyoruz.

---

## 🧠 Akılda Kalsın

```text
COLLSCAN
↓
Collection Scan
↓
Collection'ı tara
```

## Örnek 103



```text
IXSCAN
↓
Index Scan
↓
Index'i tara
```

## Örnek 104



```text
FETCH
↓
Gerçek Document'ı getir
```

## Örnek 105

`explain("executionStats")` ile:

```text
MongoDB bu sorguyu
nasıl çalıştırdı?
```

## Örnek 106

sorusunu inceleyebiliriz.

Bakacağımız temel değerler:

```text
nReturned
↓
kaç sonuç döndü?
```

## Örnek 107



```text
totalDocsExamined
↓
kaç Document incelendi?
```

## Örnek 108



```text
totalKeysExamined
↓
kaç index anahtarı incelendi?
```

## Örnek 109

Ve index tasarımında en değerli soru:

> **Bu index hangi gerçek sorgumuzu destekliyor?**

---

## Bölüm Özeti

Bu bölümde index'i yeniden tanımlamak yerine 44. bölümde öğrendiğimiz yapının sorgu performansına etkisini inceledik.

Uygun index kullanılmadığında MongoDB'nin:

```text
COLLSCAN
```

## Örnek 110

ile collection'ı tarayabileceğini gördük.

Özellikle `find()` sorgusunda ilk eşleşmeyi bulmanın taramanın mutlaka bittiği anlamına gelmediğini fark ettik.

Uygun index kullanıldığında:

```text
IXSCAN
```

## Örnek 111

ile index üzerinden aramanın daraltılabileceğini gördük.

Gerçek Document'a ihtiyaç duyulduğunda:

```text
IXSCAN
↓
FETCH
```

## Örnek 112

akışıyla karşılaşabileceğimizi öğrendik.

Sonra mevcut:

```text
GET /movies
```

## Örnek 113

akışımızda `getMovies` Controller'ını geçici olarak kullanarak:

```js
.explain("executionStats")
```

## Örnek 114

ile sorgu planını nasıl inceleyebileceğimizi gördük.

`nReturned`, `totalDocsExamined` ve `totalKeysExamined` değerlerini okuyarak:

```text
kaç sonuç döndü?
```

## Örnek 115

ile:

```text
MongoDB ne kadar iş yaptı?
```

## Örnek 116

sorularının aynı şey olmadığını öğrendik.

Compound index'te alan sırasının önemli olduğunu da tekrar gerçek sorgular üzerinden gördük:

```text
{ title, releaseYear }

title
↓
prefix

title + releaseYear
↓
prefix

yalnız releaseYear
↓
prefix değil
```

## Örnek 117

Son olarak index'in ek yükleri olduğunu öğrendik.

Index'ler okumaları hızlandırabilir ama yazma işlemlerinde bakım ve ek depolama kullanımı oluşturur.

Bu yüzden:

```text
her field'a index
```

## Örnek 118

yerine:

```text
gerçek ve sık kullanılan sorgulara
uygun index
```

## Örnek 119

tasarlarız.

---

## Sonraki Adım

Index'te:

```text
alan sırası
```

## Örnek 120

önemli.

Peki uygulamamız filmleri:

```text
en yüksek puan
↓
en düşük puan
```

## Örnek 121

veya:

```text
en yeni film
↓
en eski film
```
