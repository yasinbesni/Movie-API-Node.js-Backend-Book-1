# Bölüm 24 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# MongoDB'de Document Nedir?

## Şimdi Tek Bir Filmin İçine Girelim

Bir önceki bölümde

platformumuza yukarıdan bakmıştık.

Karşımızda:

```text
movieApp
│
└── movies
    ├── Interstellar
    ├── Inception
    └── Arrival
```

## Örnek 2

vardı.

Artık:

```text
movieApp
↓
Database

movies
↓
Collection
```

## Örnek 3

ilişkisini biliyoruz.

Şimdi biraz daha yaklaşalım.

`movies` collection'ını açın.

Karşınızda birçok film var.

Ama bu kez

hepsiyle ilgilenmeyeceğiz.

Yalnızca:

```text
Interstellar
```

## Örnek 4

kaydını seçin.

Ekranda şöyle bir yapı gördüğünüzü düşünün:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7
}
```

## Örnek 5

Şimdi artık

collection'a dışarıdan bakmıyoruz.

Tek bir kaydın

içindeyiz.

Peki MongoDB'de

bu tek kaydın adı ne?

```text
Document
```

## Örnek 6

---

## Document Bize Neyi Temsil Ediyor?

Film platformumuzda

her film hakkında

birden fazla bilgi tutmamız gerekir.

Örneğin:

```text
filmin adı

yayın yılı

puanı

türleri

süresi
```

## Örnek 7

Bunları birbirinden kopuk

beş ayrı veri olarak düşünmek

çok kullanışlı olmazdı.

Çünkü hepsi:

```text
aynı filme
```

## Örnek 8

aittir.

MongoDB,

aynı kayda ait bu bilgileri

bir document içinde

bir araya getirebilir.

Örneğin:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7
}
```

## Örnek 9

Bu yapının tamamı:

```text
bir document
```

## Örnek 10

olarak düşünülebilir.

Yani:

```text
Interstellar
↓
Tek bir film kaydı
↓
Document
```

## Örnek 11

Collection birçok document'ı tutuyordu.

Document ise

tek bir kaydı temsil ediyor.

```text
movies
↓
Collection

Interstellar
↓
Document
```

## Örnek 12

---

## Peki Document'ın İçindeki Bu Bilgiler Nedir?

Şimdi document'a

biraz daha yakından bakalım.

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7
}
```

## Örnek 13

Burada yalnızca

üç bilgi görmüyoruz.

Aslında üç ayrı:

```text
alan → değer
```

## Örnek 14

çifti görüyoruz.

MongoDB'de bir document,

temelde:

```text
field
↓
value
```

## Örnek 15

çiftlerinden oluşur.

Örneğin:

```text
title
↓
Interstellar
```

## Örnek 16

Burada:

```text
title
```

## Örnek 17

bir **field**,

```text
Interstellar
```

## Örnek 18

ise o field'ın **value** değeridir.

Aynı şekilde:

```text
releaseYear
↓
2014
```

## Örnek 19

ve:

```text
rating
↓
8.7
```

## Örnek 20

çiftleri vardır.

Document'ımızı artık

şöyle okuyabiliriz:

```text
title       → Interstellar

releaseYear → 2014

rating      → 8.7
```

## Örnek 21

İşte document yapısının

temel mantığı budur:

```text
Document
↓
Field + Value çiftleri
```

## Örnek 22

---

## Field Olmasaydı Ne Olurdu?

`Interstellar` document'ını

bir kez daha açalım.

Ama bu kez

field adlarını kaldırdığımızı düşünün.

Elimizde yalnızca:

```text
Interstellar

2014

8.7
```

## Örnek 23

kaldı.

Şimdi size sorsam:

> 2014 neyi ifade ediyor?

Filmin yayın yılı mı?

Süresi mi?

Bir kimlik numarası mı?

Peki:

```text
8.7
```

## Örnek 24

nedir?

Puan mı?

Bilet fiyatı mı?

Başka bir değer mi?

Değerler elimizde.

Ama ne anlama geldiklerini

söyleyen bir şey yok.

İşte document içindeki:

```text
title

releaseYear

rating
```

## Örnek 25

alanlarına

tam olarak bu yüzden ihtiyaç duyarız.

Şimdi aynı verilere

yeniden bakalım:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7
}
```

## Örnek 26

Artık hiçbir değer

tek başına durmuyor.

```text
title
↓
"Interstellar"

releaseYear
↓
2014

rating
↓
8.7
```

## Örnek 27

Soldaki isimler:

```text
title

releaseYear

rating
```

## Örnek 28

birer **field**'dır.

Field bize:

> Bu bilgi neyi temsil ediyor?

sorusunun cevabını verir.

Karşısındaki:

```text
"Interstellar"

2014

8.7
```

## Örnek 29

ise o field'a ait

**value** değerleridir.

Yani:

```text
Field
↓
Bilginin ne olduğunu söyler.

Value
↓
O bilginin gerçek değerini taşır.
```

## Örnek 30

Örneğin:

```text
releaseYear
```

## Örnek 31

olmasaydı:

```text
2014
```

## Örnek 32

yalnızca bir sayı olurdu.

Ama ikisini bir araya getirdiğimizde:

```text
releaseYear → 2014
```

## Örnek 33

artık bunun:

> Filmin yayın yılı 2014'tür.

anlamına geldiğini biliyoruz.

Document'ın temel yapı taşlarından biri

işte bu ilişkidir:

```text
Field
↓
Value
```

## Örnek 34

Bir document,

bu field–value çiftlerinin

bir araya gelmesiyle oluşur.

```text
Document
│
├── title       → "Interstellar"
├── releaseYear → 2014
└── rating      → 8.7
```

## Örnek 35

Şimdi document'a baktığınızda

yalnızca birkaç veri değil,

**anlamı tanımlanmış bilgiler**

görüyorsunuz.

---

## Value Her Zaman Metin mi Olur?

Hayır.

Şimdi filmimizi

biraz daha ayrıntılı hâle getirelim.

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7,
  "isPublished": true,
  "genres": ["Science Fiction", "Drama"]
}
```

## Örnek 36

Buradaki değerlerin

hepsi aynı türde değildir.

```text
"Interstellar"
↓
metin

2014
↓
sayı

8.7
↓
sayı

true
↓
boolean

["Science Fiction", "Drama"]
↓
array
```

## Örnek 37

BSON bölümünde

MongoDB'nin

farklı veri türlerini

temsil edebildiğini görmüştük.

Şimdi bunun

document içinde neden önemli olduğunu

görmeye başlıyoruz.

Çünkü bir document:

```text
yalnızca metinlerden
```

## Örnek 38

oluşmak zorunda değildir.

Farklı field'ların

farklı türlerde value'ları olabilir.

---

## Bir Field Birden Fazla Değeri Nasıl Taşır?

Filmimizin:

```text
genres
```

## Örnek 39

alanına bakalım.

Bir film

tek bir türe ait olmak zorunda değildir.

Örneğin:

```json
{
  "genres": ["Science Fiction", "Drama"]
}
```

## Örnek 40

Burada `genres` field'ının

karşısında:

```text
tek bir değer
```

## Örnek 41

yerine bir:

```text
array
```

## Örnek 42

bulunuyor.

Bu bize:

```text
genres
↓
birden fazla tür
```

## Örnek 43

saklama imkânı verir.

Şimdi document'ımız biraz daha

gerçek bir film kaydına benzemeye başladı:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7,
  "genres": ["Science Fiction", "Drama"]
}
```

## Örnek 44

Ama MongoDB document'ları

bundan da fazlasını yapabilir.

---

## Bir Document'ın İçinde Başka Bir Document Olabilir mi?

Şimdi filmin

bazı teknik bilgilerini

saklamak istediğinizi düşünün.

Örneğin:

```text
süre

orijinal dil
```

## Örnek 45

Bunları doğrudan:

```json
{
  "runtime": 169,
  "originalLanguage": "en"
}
```

## Örnek 46

şeklinde ayrı field'lar olarak

tutabilirsiniz.

Ama bu bilgilerin

aynı konuya ait olduğunu

daha belirgin göstermek istediğinizi düşünün.

O zaman şöyle bir yapı görebiliriz:

```json
{
  "title": "Interstellar",
  "details": {
    "runtime": 169,
    "originalLanguage": "en"
  }
}
```

## Örnek 47

Burada:

```text
details
```

## Örnek 48

field'ının value'su

basit bir metin veya sayı değildir.

Başka bir document'tır.

Yani:

```text
Document
↓
Field
↓
Başka bir document
```

## Örnek 49

yapısı mümkün olabilir.

Buna:

```text
embedded document
```

## Örnek 50

veya:

```text
nested document
```

## Örnek 51

denildiğini görebiliriz.

Başlangıçta bunu

şöyle düşünmeniz yeterlidir:

> Bir kaydın içinde,
> o kayda ait daha küçük
> bir yapı bulunabilir.

---

## Neden İç İçe Document Kullanırız?

Şimdi iki farklı görünümü

yan yana düşünün.

Birincisi:

```json
{
  "title": "Interstellar",
  "runtime": 169,
  "originalLanguage": "en"
}
```

## Örnek 52

İkincisi:

```json
{
  "title": "Interstellar",
  "details": {
    "runtime": 169,
    "originalLanguage": "en"
  }
}
```

## Örnek 53

İkisinde de

aynı türden bilgileri

saklayabiliriz.

Fakat ikinci yapıda:

```text
runtime

originalLanguage
```

## Örnek 54

bilgilerinin:

```text
details
```

## Örnek 55

başlığı altında

birbiriyle ilişkili olduğunu

daha açık biçimde görebiliriz.

Ama burada dikkat.

Şu anda:

> Her ilişkili bilgiyi
> mutlaka iç içe document yapın.

demiyoruz.

Bunun ne zaman iyi bir tercih olduğu

ileride:

```text
veri modelleme
```

## Örnek 56

konusunun parçası olacak.

Şimdilik yalnızca

MongoDB document'larının

iç içe yapılar

barındırabildiğini öğreniyoruz.

---

## Array İçinde Document Olabilir mi?

Bir adım daha ileri gidelim.

Filmin farklı platformlardaki

gösterim bilgilerini

sakladığınızı düşünün.

Örneğin:

```json
{
  "availability": [
    {
      "platform": "Platform A",
      "quality": "HD"
    },
    {
      "platform": "Platform B",
      "quality": "4K"
    }
  ]
}
```

## Örnek 57

Burada:

```text
availability
```

## Örnek 58

bir array'dir.

Fakat array'in içindeki

her eleman:

```text
bir document
```

## Örnek 59

yapısındadır.

Yani MongoDB'de:

```text
Document
↓
Array
↓
Document'lar
```

## Örnek 60

gibi yapılar da

oluşturulabilir.

Bu özellik

gerçek uygulamalarda

çok güçlü veri modelleri

kurmamıza yardımcı olabilir.

Ama henüz

hangi durumda hangi yapıyı

seçmemiz gerektiğine geçmiyoruz.

Önce parçaları tanıyoruz.

---

## Şimdi Document'ımızı Baştan Okuyalım

Artık daha zengin

bir film document'ımız olsun:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7,
  "genres": ["Science Fiction", "Drama"],
  "details": {
    "runtime": 169,
    "originalLanguage": "en"
  }
}
```

## Örnek 61

Şimdi siz okuyun.

```text
title
↓
field

"Interstellar"
↓
value
```

## Örnek 62

Sonra:

```text
genres
↓
field

["Science Fiction", "Drama"]
↓
array value
```

## Örnek 63

Son olarak:

```text
details
↓
field

{
  runtime: 169,
  originalLanguage: "en"
}
↓
embedded document
```

## Örnek 64

Bir süre önce

bu yapıya yalnızca:

```text
JSON'a benzeyen veri
```

## Örnek 65

gözüyle bakıyorduk.

Şimdi ise

içindeki parçaları

ayırt etmeye başladık.

---

## MongoDB Document ve JavaScript Object

22. bölümde kurduğumuz ayrımı burada yalnızca yerine oturtalım: JavaScript Object uygulama belleğindeki veri yapısıdır; MongoDB Document ise veritabanında saklanan kayıttır ve BSON biçiminde temsil edilir.

Görünümleri benzer olabilir, fakat farklı katmanlarda yaşarlar. Bu bölümde `Document` dediğimizde MongoDB'deki kaydı kastediyoruz.

## Collection İçindeki Yerini Tekrar Görelim

Şimdi yeniden

bir adım geri çekilelim.

```text
movieApp
│
└── movies
    │
    ├── Document
    │   ├── title
    │   ├── releaseYear
    │   └── rating
    │
    ├── Document
    │   ├── title
    │   ├── releaseYear
    │   └── rating
    │
    └── Document
        ├── title
        ├── releaseYear
        └── rating
```

## Örnek 66

Yani:

```text
Database
↓
Collection
↓
Documents
↓
Fields
↓
Values
```

## Örnek 67

Artık MongoDB'nin

veri yapısında

bir katman daha aşağı indik.

Önce:

```text
Database
```

## Örnek 68

vardı.

Sonra:

```text
Collection
```

## Örnek 69

geldi.

Şimdi:

```text
Document
```

## Örnek 70

içine girdik.

Ve document'ın içinde:

```text
Field
↓
Value
```

## Örnek 71

çiftlerini gördük.

---

## Ama Ekranda Bir Alan Daha Belirdi

Şimdi `movies` collection'ına

gerçekten yeni bir film

eklendiğini düşünün.

Siz şu veriyi

göndermiş olun:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7
}
```

## Örnek 72

Sonra kaydı

MongoDB tarafında açıyorsunuz.

Fakat document'ta

sizin yazmadığınız

bir alan görüyorsunuz:

```text
_id
```

## Örnek 73

Ve karşısında

şuna benzeyen bir değer var:

```text
ObjectId("...")
```

## Örnek 74

Bir saniye.

Biz:

```text
title

releaseYear

rating
```

## Örnek 75

göndermiştik.

Peki:

```text
_id
```

## Örnek 76

nereden geldi?

Ve daha da önemlisi:

> MongoDB aynı collection içindeki
> document'lardan tam olarak hangisiyle
> çalıştığını nasıl kesin olarak biliyor?

İşte yeni sorumuz artık açık.

Bir document'ın içeriğini öğrendik.

Şimdi onu diğer document'lardan

**nasıl ayırdığımızı** anlamamız gerekiyor.

---

## Bölüm Özeti

Bu bölümde

MongoDB'deki:

```text
Document
```

## Örnek 77

kavramını öğrendik.

Document'ın:

```text
tek bir kaydı
```

## Örnek 78

temsil ettiğini gördük.

Film platformumuzda:

```text
movies
↓
Collection

Interstellar
↓
Document
```

## Örnek 79

ilişkisini kurduk.

Bir document'ın temel yapısının:

```text
Field
↓
Value
```

## Örnek 80

çiftlerinden oluştuğunu öğrendik.

Örneğin:

```text
title
↓
Interstellar

releaseYear
↓
2014

rating
↓
8.7
```

## Örnek 81

Ayrıca value'ların

yalnızca metin olmak zorunda

olmadığını gördük.

Bir value:

```text
metin

sayı

boolean

array

başka bir document
```

## Örnek 82

gibi farklı yapılarda olabilir.

Document içinde:

```text
embedded document
```

## Örnek 83

ve:

```text
array
```

## Örnek 84

kullanılabileceğini de gördük.

Böylece temel haritamız

biraz daha büyüdü:

```text
Database
↓
Collection
↓
Document
↓
Field
↓
Value
```

## Örnek 85

Ama tam burada

MongoDB document'ının

üzerinde yeni bir şey fark ettik:

```text
_id
```

## Örnek 86

---

## Sonraki Adım

Şimdi `movies` collection'ında

aynı ada sahip iki film olduğunu düşünün:

```text
Dune — 1984

Dune — 2021
```

## Örnek 87

İkisinin de:

```text
title → "Dune"
```

## Örnek 88

değeri aynı.

O hâlde yalnızca film adına bakarak:

> Tam olarak hangi document'ı güncelleyeceğiz?

sorusuna kesin cevap veremeyiz.

İşte sıradaki bölümün problemi bu:

```text
Document'ın bilgileri
↓
değişebilir

ama

Document'ın kimliği
↓
onu diğerlerinden ayırmalı
```

## Örnek 89

Bir sonraki bölümde önce:

> **MongoDB bir document'ı diğerinden nasıl ayırır?**

sorusunu çözeceğiz.

Böylece ekranda gördüğümüz:

```text
_id
```
