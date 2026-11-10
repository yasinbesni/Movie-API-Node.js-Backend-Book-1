# Bölüm 44 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Mongoose unique Nedir? Aynı Verinin Tekrar Kaydedilmesini Nasıl Engelleriz?

## Aynı Film İki Kez Kaydedilirse Ne Olur?

Movie API'mize şu filmi eklediğimizi düşünün:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3
}
```

## Örnek 2

MongoDB bunu kaydetti.

Bir süre sonra aynı Request yanlışlıkla tekrar gönderildi:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3
}
```

## Örnek 3

MongoDB açısından bu iki kayıt farklı `_id` değerlerine sahip olabilir:

```text
Dune — 2021
↓
_id: A
```

## Örnek 4



```text
Dune — 2021
↓
_id: B
```

## Örnek 5

Yani:

```text
_id farklı
↓
iki farklı Document
```

## Örnek 6

olarak saklanabilirler.

Ama bizim uygulamamız açısından problem var:

> Aynı filmi yanlışlıkla iki kez kaydetmek istemiyoruz.

Peki bunu Schema tarafında nasıl engelleyebiliriz?

Burada karşımıza:

```text
unique
```

## Örnek 7

kavramı çıkar.

Ama `unique`ın nasıl çalıştığını anlayabilmek için önce:

```text
index nedir?
```

## Örnek 8

sorusunu çözmemiz gerekiyor.

Çünkü tekrar eden değeri gerçekten engelleyen yapı MongoDB'deki:

```text
unique index
```

## Örnek 9

olacaktır.

Önce index'i somutlaştıralım, sonra `unique`a geri dönelim.

---

## Önce Index'i Gerçekten Anlayalım

`unique` konusuna geçmeden önce:

```text
index
```

## Örnek 10

kelimesinin ne yaptığını netleştirelim.

Çünkü `unique`, MongoDB'deki bir index üzerinden çalışır.

Önce index olmadan arama yaptığımızı düşünelim.

Collection'ımızda şu filmler olsun:

```text
1. Document
↓
Arrival — 2016

2. Document
↓
Interstellar — 2014

3. Document
↓
Dune — 1984

4. Document
↓
Dune — 2021

5. Document
↓
Inception — 2010
```

## Örnek 11

Şimdi MongoDB'ye şunu soruyoruz:

```text
title = "Dune"
releaseYear = 2021
```

## Örnek 12

Eğer bu alanlar için kullanılabilecek bir index yoksa MongoDB aradığı kaydı bulmak için birçok Document'ı kontrol etmek zorunda kalabilir:

```text
Arrival — 2016
↓
değil

Interstellar — 2014
↓
değil

Dune — 1984
↓
yıl farklı

Dune — 2021
↓
bulundu
```

## Örnek 13

Yani arama:

```text
Document
↓
Document
↓
Document
↓
Document
```

## Örnek 14

şeklinde ilerleyebilir.

Collection büyüdükçe bu daha önemli hâle gelir:

```text
5 Document
↓
az kontrol

5.000 Document
↓
daha fazla kontrol

500.000 Document
↓
çok daha fazla kontrol
```

## Örnek 15

İşte index ihtiyacı burada doğar.

---

## Index Bir Kısa Yol Oluşturur

Şimdi MongoDB'nin:

```text
title
+
releaseYear
```

## Örnek 16

alanları için ayrıca bir index oluşturduğunu düşünün.

MongoDB bu iki alanın değerlerinden ayrı bir arama yapısı oluşturur.

Zihnimizde şöyle canlandırabiliriz:

```text
INDEX

("Arrival", 2016)
↓
Document 1

("Dune", 1984)
↓
Document 3

("Dune", 2021)
↓
Document 4

("Inception", 2010)
↓
Document 5

("Interstellar", 2014)
↓
Document 2
```

## Örnek 17

Burada index'in içinde bütün Movie Document'ını göstermiyoruz.

Index'in bize verdiği temel fikir şu:

```text
aradığım değer
↓
hangi Document'a gitmeliyim?
```

## Örnek 18

Yani:

```text
Collection
↓
gerçek Document'lar
```

## Örnek 19



```text
Index
↓
seçtiğimiz alan değerlerinden
ilgili Document'a ulaşmak için
ayrı bir arama yapısı
```

## Örnek 20

---

## Aynı Aramayı Index ile Yapalım

Aradığımız değer yine:

```text
"Dune" + 2021
```

## Örnek 21

olsun.

Bu kez akışı şöyle düşünebiliriz:

```text
("Dune", 2021)
↓
index'te bul
↓
Document 4
↓
gerçek Document'a git
```

## Örnek 22

Yani index:

```text
Bütün collection'ı tek tek dolaşmak yerine
aradığım Document'a giden
daha kısa bir yol var mı?
```

## Örnek 23

sorusuna yardım eder.

Bu yüzden index'lerin önemli görevlerinden biri:

```text
sorguları hızlandırmak
```

## Örnek 24

tır.

Ama bizim bu bölümde index'e ihtiyaç duyma nedenimiz farklı.

Biz ayrıca şunu istiyoruz:

> Aynı `title + releaseYear` değeri ikinci kez kaydedilmesin.

İşte index'e böyle bir kural verdiğimizde:

```text
unique index
```

## Örnek 25

elde ederiz.

---

## Normal Index ile Unique Index Arasındaki Fark

Normal bir index'te:

```text
("Dune", 2021)
↓
Document 4
```

## Örnek 26

bulunduğunu düşünün.

Başka bir Document da aynı değere sahip olabilir:

```text
("Dune", 2021)
↓
Document 6
```

## Örnek 27

Normal bir index, aynı index değerinin birden fazla Document için oluşmasına izin verir.

Index'i `unique` yaptığımızda ise aynı index değeri ikinci bir Document için tekrar oluşturulamaz:

```text
aynı index değeri
↓
yalnızca bir Document
```

## Örnek 28

Yani index'te zaten:

```text
("Dune", 2021)
```

## Örnek 29

varsa yeni kayıt da aynı değeri üretince:

```text
("Dune", 2021)
↓
zaten var
↓
ikinci kez kabul etme
```

## Örnek 30

olur.

İşte `unique`ın arkasındaki mekanizma budur.

---

## Buradaki "Index Değeri" Ne?

Bu ifade de havada kalmasın.

Bizim index'imiz iki alana bakıyor:

```text
title
+
releaseYear
```

## Örnek 31

Bir Document:

```json
{
  "title": "Dune",
  "releaseYear": 2021
}
```

## Örnek 32

ise bu Document için index'te kullandığımız değer zihnimizde:

```text
("Dune", 2021)
```

## Örnek 33

olarak düşünülebilir.

Başka bir Document:

```json
{
  "title": "Dune",
  "releaseYear": 1984
}
```

## Örnek 34

ise onun index değeri:

```text
("Dune", 1984)
```

## Örnek 35

olur.

Yani:

```text
("Dune", 1984)
≠
("Dune", 2021)
```

## Örnek 36

Bu yüzden ikisi birlikte bulunabilir.

Ama ikinci kez:

```text
("Dune", 2021)
```

## Örnek 37

gelirse aynı index değeri tekrar etmiş olur.

Unique index'in engellediği şey tam olarak budur.

---

## title Alanına unique: true Koysak?

İlk akla gelen çözüm şu olabilir:

```js
title: {
  type: String,
  required: true,
  unique: true,
}
```

## Örnek 38

Bunu şöyle okuyabiliriz:

```text
title
↓
aynı değer tekrar kullanılmasın
```

## Örnek 39

İlk bakışta mantıklı.

Ama 25. bölümde gördüğümüz problemi hatırlayın:

```text
Dune — 1984
Dune — 2021
```

## Örnek 40

İki farklı filmin adı aynı olabilir.

Dolayısıyla yalnızca:

```text
title benzersiz olsun
```

## Örnek 41

dersek:

```text
Dune — 1984
↓
kaydedildi

Dune — 2021
↓
aynı title
↓
engellenebilir
```

## Örnek 42

Bu da yanlış olur.

Demek ki gerçek kuralımız:

```text
film adı tek başına benzersiz
```

## Örnek 43

değil.

Bizim bu örnekte söylemek istediğimiz:

> Aynı `title` ve aynı `releaseYear` birlikte tekrar etmesin.

Yani:

```text
Dune + 1984
↓
olabilir

Dune + 2021
↓
olabilir

Dune + 2021
↓
bir kez daha
↓
olmasın
```

## Örnek 44

---

## Birden Fazla Alan Birlikte Benzersiz Olabilir mi?

Evet.

Bizim örneğimizde benzersiz olmasını istediğimiz şey tek bir alan değil:

```text
title + releaseYear
```

## Örnek 45

birlikteliğidir.

Bunu Mongoose Schema'da şöyle tanımlayabiliriz.

Ama burada yalnızca mekanizmayı öğreniyoruz.

Şu anda bu index'i mevcut `movie.js` dosyamıza kalıcı olarak eklemiyoruz; çünkü birazdan `releaseYear` alanının zorunlu olmaması nedeniyle bu kuralın proje açısından gerçekten doğru olup olmadığını ayrıca değerlendireceğiz.

Örnek tanım:

```js
movieSchema.index(
  {
    title: 1,
    releaseYear: 1,
  },
  {
    unique: true,
  },
);
```

## Örnek 46

Şimdilik:

```text
1
```

## Örnek 47

değerlerinin ayrıntısına takılmayın.

Burada bizim için önemli olan:

```text
title
+
releaseYear
↓
birlikte değerlendir
```

## Örnek 48

fikridir.

Ve:

```js
{
  unique: true,
}
```

## Örnek 49

şunu söyler:

```text
bu alanların oluşturduğu
aynı kombinasyonu
ikinci kez kabul etme
```

## Örnek 50

---

## Örneklerle Görelim

İlk kayıt:

```json
{
  "title": "Dune",
  "releaseYear": 1984
}
```

## Örnek 51

kabul edilebilir.

İkinci kayıt:

```json
{
  "title": "Dune",
  "releaseYear": 2021
}
```

## Örnek 52

da kabul edilebilir.

Çünkü kombinasyonlar farklı:

```text
Dune + 1984
≠
Dune + 2021
```

## Örnek 53

Ama tekrar:

```json
{
  "title": "Dune",
  "releaseYear": 2021
}
```

## Örnek 54

gönderilirse:

```text
Dune + 2021
↓
zaten var
↓
aynı kombinasyon tekrar ediyor
```

## Örnek 55

MongoDB bu kaydı reddedebilir.

İşte bizim film örneğimizde ihtiyaç duyduğumuz benzersizlik kuralı bu.

---

## Yazdığımız Index Kodunu Parçalara Ayıralım

Kodumuz:

```js
movieSchema.index(
  {
    title: 1,
    releaseYear: 1,
  },
  {
    unique: true,
  },
);
```

## Örnek 56

Şimdi bunu satır satır okuyalım.

İlk bölüm:

```js
movieSchema.index(
```

## Örnek 57

şunu söyler:

```text
Bu Schema için
bir MongoDB index'i tanımla.
```

## Örnek 58

Sonra:

```js
{
  title: 1,
  releaseYear: 1,
}
```

## Örnek 59

geliyor.

Burada index'e hangi alanların gireceğini söylüyoruz:

```text
title
+
releaseYear
```

## Örnek 60

Yani index yalnızca:

```text
title
```

## Örnek 61

değerine bakmayacak.

Şu ikisini birlikte kullanacak:

```text
title + releaseYear
```

## Örnek 62

Örneğin zihnimizde index değerleri şöyle olabilir:

```text
("Dune", 1984)
("Dune", 2021)
("Arrival", 2016)
```

## Örnek 63

---

## Peki `1` Ne Demek?

Şu kısım:

```js
title: 1
```

## Örnek 64

şu anlama gelmez:

```text
title'ın değeri 1 olsun
```

## Örnek 65

veya:

```text
title daha önemli
```

## Örnek 66

MongoDB index tanımında:

```text
1
↓
ascending
↓
artan yön
```

## Örnek 67

anlamına gelir.

Aynı şekilde:

```js
releaseYear: 1
```

## Örnek 68

da `releaseYear` alanının index içinde artan yönde tutulacağını belirtir.

Bu yönün sorgu ve sıralama performansını nasıl etkilediğini bir sonraki bölümde, indexlerin sorguları nasıl hızlandırdığını ele alırken daha ayrıntılı inceleyeceğiz.

Bu bölümde önemli olan:

```text
title ve releaseYear
aynı index'in parçaları
```

## Örnek 69

olmasıdır.

---

## Compound Index Mantığı

Bir index yalnızca tek bir alan üzerinde tanımlanabilir. Örneğin sadece `email` alanını kapsayan bir index, **tek alanlı index** olur.

Bizim oluşturduğumuz index ise iki alanı birlikte kapsıyor:

```text
title
+
releaseYear
```

## Örnek 70

Bir index birden fazla alan üzerinde tanımlandığında buna **compound index** denir. Türkçede bunu **birleşik index** olarak düşünebiliriz.

Biz ayrıca index tanımına:

```js
{
  unique: true,
}
```

## Örnek 71

eklediğimiz için yapı bir **unique compound index** hâline gelir.

Buradaki iki kavramın görevi farklıdır:

```text
compound
↓
birden fazla alan aynı index içinde birlikte kullanılır

unique
↓
aynı alan kombinasyonunun tekrar kaydedilmesine izin verilmez
```

## Örnek 72

---

## unique: true Tam Olarak Neyi Benzersiz Yapıyor?

Bizim index'imiz:

```text
title + releaseYear
```

## Örnek 73

üzerine kurulu.

O hâlde:

```js
{
  unique: true,
}
```

## Örnek 74

tek başına `title` alanını benzersiz yapmaz.

Benzersiz olan:

```text
title + releaseYear
kombinasyonudur
```

## Örnek 75

Örneğin:

```text
Dune + 1984
```

## Örnek 76

ve:

```text
Dune + 2021
```

## Örnek 77

farklı index değerleridir.

Bu yüzden ikisi birlikte bulunabilir.

Ama:

```text
Dune + 2021
```

## Örnek 78

ikinci kez gelirse aynı index değeri tekrar edecektir.

İşte MongoDB'nin reddettiği şey budur.

---

## En Önemli Nokta: unique Bir Validator Değildir

40. bölümde validator'ları öğrenmiştik:

```text
required
min
max
enum
```

## Örnek 79

Bunlar Mongoose validation sisteminin parçasıdır.

Örneğin:

```text
voteAverage: 25
↓
max: 10
↓
ValidationError
```

## Örnek 80

oluşabilir.

Ama:

```text
unique
```

## Örnek 81

aynı şekilde çalışan bir validator değildir.

Bu ayrım çok önemli.

`unique`:

```text
Mongoose validation
```

## Örnek 82

kuralı eklemek yerine MongoDB'de:

```text
unique index
```

## Örnek 83

oluşturulmasını ister.

Yani:

```text
required
min
max
enum
↓
validator
```

## Örnek 84

ama:

```text
unique
↓
validator DEĞİL
↓
unique index tanımı
```

## Örnek 85

---

## Bunun Sonucunda Hata da Farklı Olur

Şu geçersiz değeri düşünün:

```json
{
  "title": "",
  "releaseYear": 2021
}
```

## Örnek 86

`title` required kuralına takılırsa Mongoose:

```text
ValidationError
```

## Örnek 87

oluşturabilir.

Ama zaten var olan:

```text
Dune + 2021
```

## Örnek 88

kombinasyonunu tekrar kaydetmeye çalışırsak sorun validation aşamasından gelmez.

MongoDB unique index'i:

```text
aynı değer zaten var
```

## Örnek 89

diyerek işlemi reddeder.

Bu durumda genellikle:

```text
duplicate key error
```

## Örnek 90

ile karşılaşırız.

Ve hata kodu:

```text
11000
```

## Örnek 91

olabilir.

Çoğu logda bunu:

```text
E11000 duplicate key error
```

## Örnek 92

şeklinde görürsünüz.

---

## ValidationError Beklememeliyiz

Bu yüzden yalnızca:

```js
error.name === "ValidationError"
```

## Örnek 93

kontrolüne bakmak duplicate kayıt hatasını tanımak için yeterli değildir.

Çünkü:

```text
duplicate key
↓
ValidationError değildir
```

## Örnek 94

Bu bölümün en önemli teknik ayrımlarından biri budur:

```text
yanlış değer
↓
ValidationError
```

## Örnek 95



```text
unique index çakışması
↓
MongoDB duplicate key error
↓
code: 11000
```

## Örnek 96

---

## Duplicate Hatasını API'de Nasıl Karşılarız?

39 ve 40. bölümlerde merkezi:

```text
errorHandler
```

## Örnek 97

kurmuştuk.

Unique index kullanan bir uygulamada aynı değer tekrar gönderildiğinde bu hata:

```text
beklenmeyen sunucu arızası
```

## Örnek 98

değildir.

İstemci mevcut veriyle çakışan yeni bir kayıt oluşturmaya çalışmıştır.

Bu durum için:

```text
409 Conflict
```

## Örnek 99

uygun bir HTTP durum kodu olabilir.

Örneğin `errorHandler` içinde şu kontrol kullanılabilir:

```js
if (error.code === 11000) {
  return res.status(409).json({
    message: "Bu kayıt zaten mevcut.",
  });
}
```

## Örnek 100

Buradaki fikir:

```text
ValidationError
↓
400
```

## Örnek 101



```text
duplicate key
↓
11000
↓
409
```

## Örnek 102



```text
beklenmeyen teknik hata
↓
500
```

## Örnek 103

ayrımını kurmaktır.

Ama bu kodu mevcut Movie projemize kalıcı olarak eklemeden önce gerçekten hangi alanı benzersiz yapacağımıza karar vermeliyiz.

---

## title + releaseYear Kuralını Seçseydik Nasıl Görünürdü?

Diyelim ki uygulama kuralımız gerçekten şu oldu:

```text
aynı title
+
aynı releaseYear
↓
ikinci kez kaydedilemez
```

## Örnek 104

Bu durumda Schema'da şu index tanımı kullanılabilir:

```js
movieSchema.index(
  {
    title: 1,
    releaseYear: 1,
  },
  {
    unique: true,
  },
);
```

## Örnek 105

Bu kod:

```text
title + releaseYear
↓
birlikte unique
```

## Örnek 106

kuralını tanımlar.

Fakat burada önemli bir nokta var.

Bizim mevcut Schema'mızda:

```text
title
↓
required
```

## Örnek 107

ama:

```text
releaseYear
↓
required değil
```

## Örnek 108

Yani şöyle bir Movie de gelebilir:

```json
{
  "title": "Dune"
}
```

## Örnek 109

Eğer film benzersizliğini gerçekten:

```text
title + releaseYear
```

## Örnek 110

ile tanımlayacaksak `releaseYear` eksik olduğunda ne olacağını da düşünmemiz gerekir.

Daha da önemlisi, gerçek uygulamamızda bir filmi benzersiz yapan değer başka bir alan olabilir.

Örneğin ileride harici bir film kimliği kullanabiliriz.

Bu yüzden sırf `unique` konusunu öğrendik diye Movie Schema'mıza hemen bu index'i eklemiyoruz.

Önce iş kuralı:

> **Hangi değer gerçekten tekrar etmemeli?**

sorusuyla kesinleşmelidir.

Bu bölümde `title + releaseYear` yalnızca compound unique index mantığını anlamak için kullandığımız örnektir.

---

## Tek Bir Alan İçin unique Nasıl Görünür?

Bir uygulamada gerçekten tek başına benzersiz olması gereken bir alanınız varsa kullanım daha kısa olabilir.

Örneğin kullanıcı e-postası:

```js
email: {
  type: String,
  unique: true,
}
```

## Örnek 111

Bu kullanım Mongoose'a kabaca:

```text
email alanı için
unique index tanımla
```

## Örnek 112

der.

Ama yine:

```text
unique: true
↓
validator
```

## Örnek 113

demek değildir.

Bizim Movie örneğimizde `title` tek başına benzersiz olmadığı için bu kısa kullanımı `title` alanına uygulamıyoruz.

---

## Index Oluşmadan unique Çalışır mı?

İşte `unique`ın validator olmamasının önemli sonuçlarından biri.

Schema'da:

```js
unique: true
```

## Örnek 114

yazmak tek başına:

```text
Mongoose her save() öncesi
duplicate kontrolü yapacak
```

## Örnek 115

demek değildir.

Asıl kural MongoDB'deki:

```text
unique index
```

## Örnek 116

tarafından uygulanır.

Dolayısıyla index henüz oluşturulmadıysa yalnızca Schema koduna bakarak:

```text
duplicate kesin engellendi
```

## Örnek 117

diyemeyiz.

Mongoose geliştirme sırasında Schema'da tanımladığımız index'leri oluşturabilir.

Ama önemli fikir şu:

```text
Schema'daki unique tanımı
↓
MongoDB unique index'i oluşturulsun

MongoDB unique index'i
↓
tekrar eden değeri gerçekten engelle
```

## Örnek 118

---

## Mevcut Verilerde Duplicate Varsa Ne Olur?

Şimdi collection'ımızda zaten şunlar bulunduğunu düşünün:

```text
Dune + 2021
Dune + 2021
```

## Örnek 119

Yani duplicate kayıtlar zaten var.

Sonradan:

```text
title + releaseYear
↓
unique index
```

## Örnek 120

oluşturmak istediğimizde MongoDB:

```text
Mevcut veride zaten tekrar var.
```

## Örnek 121

diyerek index oluşturmayı reddedebilir.

Bu yüzden unique kuralı eklerken yalnızca yeni kayıtları değil:

```text
collection'da şu anda ne var?
```

## Örnek 122

sorusunu da düşünmek gerekir.

---

## Test Ederken Dikkat

Yeni bir veritabanında veya index'lerin yeni oluşturulduğu bir ortamda duplicate testi yapıyorsanız index'in gerçekten hazır olduğundan emin olmak gerekebilir.

Mongoose Model'inde:

```js
await Movie.init();
```

## Örnek 123

çağrısı Model için tanımlı index'lerin oluşturulmasının tamamlanmasını beklemek için kullanılabilir.

Bunu her Request'te yazmıyoruz.

Özellikle test gibi:

```text
veritabanı yeni
↓
index yeni kuruluyor
↓
hemen duplicate kayıt deneniyor
```

## Örnek 124

durumlarında neden bazen zamanlama farkı yaşanabileceğini bilmek için bu ayrıntıyı görüyoruz.

Normal Request Controller'ımıza:

```js
await Movie.init();
```

## Örnek 125

eklemiyoruz.

---

## Baştaki Probleme Dönelim

İlk film:

```json
{
  "title": "Dune",
  "releaseYear": 2021
}
```

## Örnek 126

kaydedildi.

Sonra aynı kombinasyon yeniden geldi:

```json
{
  "title": "Dune",
  "releaseYear": 2021
}
```

## Örnek 127

Eğer MongoDB'de:

```text
title + releaseYear
↓
unique index
```

## Örnek 128

varsa:

```text
aynı kombinasyon
↓
ikinci kez ekleme
↓
duplicate key
↓
code 11000
↓
409 Conflict
```

## Örnek 129

akışı oluşabilir.

Ama:

```json
{
  "title": "Dune",
  "releaseYear": 1984
}
```

## Örnek 130

farklı kombinasyondur:

```text
Dune + 1984
≠
Dune + 2021
```

## Örnek 131

Bu nedenle kabul edilebilir.

---

## 🧠 Akılda Kalsın

`unique` için en önemli cümle:

> **`unique` bir Mongoose validator'ı değildir.**

`unique`:

```text
MongoDB'de unique index
oluşturulmasını tanımlar
```

## Örnek 132

Duplicate kayıt oluşursa:

```text
ValidationError
```

## Örnek 133

beklemeyiz.

Genellikle:

```text
E11000 duplicate key error
```

## Örnek 134

ve:

```text
code: 11000
```

## Örnek 135

görürüz.

Tek alan benzersiz olabilir:

```js
email: {
  unique: true,
}
```

## Örnek 136

Birden fazla alanın kombinasyonu da benzersiz olabilir. Bunun için bu bölümde daha önce gördüğümüz `movieSchema.index(...)` tanımını kullanabiliriz.

Ama önce mutlaka şu soru sorulmalı:

```text
Hangi değer gerçekten
tekrar etmemeli?
```

## Örnek 137

---

## Bölüm Özeti

Bu bölüme aynı filmin iki kez kaydedilmesi problemiyle başladık:

```text
Dune — 2021
Dune — 2021
```

## Örnek 138

Farklı `_id` değerleri olduğu için MongoDB'nin bunları iki ayrı Document olarak saklayabileceğini gördük.

İlk olarak:

```text
title
```

## Örnek 139

alanını tek başına unique yapmanın doğru olmadığını fark ettik.

Çünkü:

```text
Dune — 1984
Dune — 2021
```

## Örnek 140

gibi aynı isimde farklı filmler olabilir.

Bunun üzerine:

```text
title + releaseYear
```

## Örnek 141

kombinasyonunun benzersiz olması fikrini gördük.

Mongoose'da bunu bir **compound unique index** ile tanımlayabileceğimizi öğrendik.

En önemli teknik ayrımı yaptık:

```text
required / min / max / enum
↓
validator
```

## Örnek 142



```text
unique
↓
validator değil
↓
MongoDB unique index
```

## Örnek 143

Duplicate kayıt hatasının:

```text
ValidationError
```

## Örnek 144

olmadığını;

genellikle:

```text
E11000
code: 11000
```

## Örnek 145

ile geldiğini gördük.

Duplicate key hatasını API tarafında:

```text
409 Conflict
```

## Örnek 146

olarak karşılayabileceğimizi gördük.

Son olarak unique kuralının yalnızca kodda yazılmasının değil, MongoDB'deki index'in gerçekten var olmasının önemli olduğunu gördük.

---

## Sonraki Adım

Bu bölümde `index` kavramını gerçek bir ihtiyaç üzerinden tanıdık.

Unique index'in tekrar eden değerleri engelleyebildiğini gördük; aynı zamanda index'in aramaya yardımcı olan ayrı bir yapı olduğunu da öğrendik.

Şimdi bu ikinci tarafı daha yakından inceleyeceğiz.

Collection büyüdüğünde:

```text
binlerce
yüz binlerce
milyonlarca Document
```
