# Bölüm 26 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# ObjectId Nedir ve Neden Böyle Görünür?

## Neden `1` Değil?

Bir önceki bölümde

şu görüntünün önünde durmuştuk:

```text
_id: ObjectId("507f191e810c19729de860ea")
```

## Örnek 2

`_id` kısmını artık biliyoruz.

Bu:

```text
document'ın kimlik field'ı
```

## Örnek 3

Ama karşısındaki değer...

biraz garip.

```text
ObjectId("507f191e810c19729de860ea")
```

## Örnek 4

İlk kez gören biri için

akla çok doğal bir soru geliyor:

> Bütün bunlara gerçekten gerek var mı?

Neden:

```text
_id: 1
```

## Örnek 5

değil?

Sonraki film:

```text
_id: 2
```

## Örnek 6

Sonraki:

```text
_id: 3
```

## Örnek 7

olsa daha kolay görünmüyor mu?

Aslında olabilir.

MongoDB'de `_id` değerinin

mutlaka ObjectId olması gerekmez.

Benzersiz olduğu sürece

başka uygun BSON değerleri de

kimlik olarak kullanılabilir.

O hâlde yeni sorumuz şu:

> MongoDB dünyasında
> ObjectId neden bu kadar sık karşımıza çıkıyor?

Cevabı görmek için

önce `1, 2, 3` fikrimizi

biraz büyütelim.

---

## Platform Büyüdüğünde Sıradaki Numara Kimin?

Film platformumuzda

kimlikleri kendimizin verdiğini düşünün.

```text
1 → Interstellar

2 → Inception

3 → Arrival
```

## Örnek 8

Şimdi yeni bir film ekleyeceğiz.

Sıradaki kimlik:

```text
4
```

## Örnek 9

Kolay.

Ama platform büyüdü.

Aynı anda

birden fazla işlem

yeni document oluşturmaya başladı.

İki farklı işlem de

şu soruyu soruyor:

> Sıradaki numara kaç?

İkisi de:

```text
4
```

## Örnek 10

cevabına ulaşırsa ne olacak?

Kimlik üretimini

bir yerde koordine etmemiz gerekir.

```text
Son kullanılan numara neydi?

Sıradaki numarayı kim alacak?

Aynı numara iki kez üretildi mi?
```

## Örnek 11

gibi yeni sorunlar ortaya çıkar.

Bu:

```text
1, 2, 3
```

## Örnek 12

kullanamayız demek değildir.

Ama merkezi bir sıra üretmek

ayrı bir mekanizma gerektirebilir.

ObjectId'nin güçlü taraflarından biri

tam burada ortaya çıkar.

Kimlik,

merkezi bir:

```text
1 → 2 → 3 → 4
```

## Örnek 13

sayacını beklemeden

üretilebilir.

Yani başlangıçta şöyle düşünebiliriz:

```text
Merkezi sıra numarası
↓
Sıradaki değeri bilmek gerekir.

ObjectId
↓
Yeni kimlik doğrudan üretilebilir.
```

## Örnek 14

Ama ObjectId'nin

neden buna uygun olduğunu

henüz görmedik.

Şimdi o uzun değeri

parçalara ayıralım.

---

## O Uzun Değeri Masaya Yatıralım

Karşımızda:

```text
507f191e810c19729de860ea
```

## Örnek 15

var.

İlk bakışta:

```text
anlamsız bir karakter yığını
```

## Örnek 16

gibi görünüyor.

Ama bu değer

rastgele yazılmış bir metin değildir.

Bir ObjectId:

```text
12 byte
```

## Örnek 17

uzunluğundadır.

Ekranda ise çoğu zaman:

```text
24 hexadecimal karakter
```

## Örnek 18

olarak görürüz.

Yani:

```text
ObjectId
↓
12 byte

Ekrandaki gösterimi
↓
24 hexadecimal karakter
```

## Örnek 19

Burada küçük bir ayrım yapalım.

```text
24 karakter
```

## Örnek 20

görmemiz,

ObjectId'nin:

```text
24 byte
```

## Örnek 21

olduğu anlamına gelmez.

Her byte

iki hexadecimal karakterle

gösterilebildiği için:

```text
12 byte
↓
24 hexadecimal karakter
```

## Örnek 22

görürüz.

Şimdilik bundan daha fazla

hexadecimal matematiğine

ihtiyacımız yok.

Asıl ilginç kısım

şimdi başlıyor.

---

## ObjectId'yi Üç Parçaya Ayırsak?

Şu ObjectId'yi yeniden alın:

```text
507f191e810c19729de860ea
```

## Örnek 23

İlk bakışta

tek parça ve anlamsız

bir değer gibi görünüyor.

Şimdi onu şöyle ayıralım:

```text
507f191e | 810c19729d | e860ea
```

## Örnek 24

Bir anda görüntü değişti.

Çünkü ObjectId'nin

12 byte'lık yapısı

üç bölümden oluşur:

```text
4 byte
↓
zaman bilgisi

5 byte
↓
rastgele değer

3 byte
↓
sayaç
```

## Örnek 25

Başka bir ifadeyle:

```text
ObjectId
│
├── 4 byte → ne zaman oluşturuldu?
├── 5 byte → kimlikleri ayırmaya yardımcı olan rastgele değer
└── 3 byte → aynı anda üretilen kimlikleri ayırmaya yardımcı olan sayaç
```

## Örnek 26

Bu üç parçanın

byte sayılarını ezberlemeniz gerekmiyor.

Asıl önemli soru şu:

> Neden tek bir değer yerine
> üç farklı parçaya ihtiyaç var?

```text
Zaman bilgisi ne işe yarıyor?

Rastgele değer neden var?

Sayaç neden gerekiyor?
```

## Örnek 27

Şimdi onları

tek tek açalım.

---

## İlk Parça: Bu Kimlik Ne Zaman Oluşturuldu?

ObjectId'nin ilk:

```text
4 byte
```

## Örnek 28

bölümü

bir:

```text
timestamp
```

## Örnek 29

taşır.

Bu timestamp,

ObjectId'nin oluşturulduğu zamanı

Unix epoch'tan itibaren

saniye cinsinden temsil eder.

Şimdilik Unix epoch ayrıntısına

takılmayalım.

Bizim için önemli olan:

```text
ObjectId
↓
Oluşturulma zamanıyla ilgili
bir bilgi taşıyabilir.
```

## Örnek 30

olmasıdır.

Yani ObjectId'nin

ilk kısmı tamamen rastgele değildir.

Zamanın da

kimlik üretimine katkısı vardır.

Ama burada önemli bir tuzak var.

### ObjectId'nin Zamanı = `createdAt` mı?

Hayır.

ObjectId içindeki timestamp:

```text
ObjectId'nin oluşturulduğu zamanı
```

## Örnek 31

temsil eder.

Bu değer,

document'ın veritabanına

tam olarak ne zaman kaydedildiğini

anlatmak zorunda değildir.

Çünkü bir ObjectId

document veritabanına eklenmeden önce de

oluşturulabilir.

Bu yüzden:

```text
ObjectId timestamp'i
≠
uygulamadaki createdAt alanı
```

## Örnek 32

diye düşünmek daha güvenlidir.

Gerçek bir uygulamada

oluşturulma zamanını açıkça tutmak istiyorsak:

```text
createdAt
```

## Örnek 33

gibi ayrı alanlar

yine kullanılabilir.

ObjectId'nin içindeki zamanı

şimdilik yalnızca:

```text
kimlik yapısının bir parçası
```

## Örnek 34

olarak görelim.

---

## İkinci Parça: Zaman Bilgisi Tek Başına Yeter mi?

Şimdi yalnızca zamanı

kimlik üretmek için

kullandığımızı düşünün.

Aynı saniye içinde

iki film document'ı oluşturuldu:

```text
Interstellar

Inception
```

## Örnek 35

İkisi de aynı saniyede

oluşturulduysa,

zaman bilgileri aynı olabilir.

O hâlde yalnızca:

```text
timestamp
```

## Örnek 36

bu iki kimliği

ayırt etmeye yetmez.

ObjectId'nin ikinci parçası

tam burada devreye girer:

```text
5 byte
↓
rastgele değer
```

## Örnek 37

Bu değer,

ObjectId'yi oluşturan

çalışan uygulama örneği için

üretilir.

Şimdilik bunu şöyle düşünün:

> Kimlik üreten çalışan uygulamanın
> kendisine ait ek bir değer vardır.

Bu ek değer,

aynı zaman bilgisini taşıyan

kimliklerin birbirine karışma

olasılığını azaltmaya yardımcı olur.

Teknik dokümantasyonda

bu çalışan uygulama örneği için:

```text
process
```

## Örnek 38

kelimesini görebilirsiniz.

Yani ileride:

```text
client-side process
```

## Örnek 39

ifadesiyle karşılaştığınızda

bunu şimdilik:

```text
ObjectId'yi üreten
çalışan uygulama örneği
```

## Örnek 40

olarak okuyabilirsiniz.

Burada önemli bir ayrıntı daha var.

Bu 5 byte'lık değer:

> Her yeni ObjectId için
> baştan tamamen farklı
> bir rastgele değer oluşturulur.

anlamına gelmez.

Aynı çalışan uygulama örneği

ObjectId üretmeye devam ederken

bu bölüm aynı kalabilir.

Uygulama yeniden çalıştırıldığında ise

yeni bir rastgele değer

oluşturulabilir.

Şimdilik şu fikir yeterli:

```text
Timestamp
↓
Zamanı ayırmaya yardım eder.

Rastgele değer
↓
Kimlikleri ayırmaya
ek katkı sağlar.
```

## Örnek 41

Ama yeni bir soru doğdu.

Aynı çalışan uygulama,

aynı saniye içinde

birden fazla ObjectId üretirse

ne olacak?

---

## Üçüncü Parça: Sayaç Neden Gerekiyor?

Şimdi aynı çalışan uygulamanın

aynı saniye içinde

birden fazla document oluşturduğunu düşünün.

```text
Interstellar

Inception

Arrival
```

## Örnek 42

Üçü de

aynı saniyede oluşturuluyor.

Bu durumda:

```text
zaman bilgisi aynı olabilir.

rastgele değer aynı olabilir.
```

## Örnek 43

Peki ObjectId'ler

birbirinden nasıl ayrılacak?

Üçüncü parça

burada devreye girer:

```text
3 byte
↓
sayaç
```

## Örnek 44

Sayaç,

her yeni ObjectId üretildiğinde

artan bir değer gibi düşünülebilir.

Basitleştirerek:

```text
İlk ObjectId
↓
Sayaç: A

İkinci ObjectId
↓
Sayaç: A + 1

Üçüncü ObjectId
↓
Sayaç: A + 2
```

## Örnek 45

şeklinde düşünebiliriz.

Gerçekte sayaç

rastgele bir başlangıç değerinden

başlatılır.

Ama başlangıç için

bütün ayrıntıyı ezberlememize

gerek yok.

Asıl fikir:

```text
Aynı saniye
+
aynı çalışan uygulama
+
farklı sayaç değeri
↓
farklı ObjectId'ler
```

## Örnek 46

şeklindedir.

Artık üç parçanın

neden birlikte kullanıldığını

daha net görebiliyoruz:

```text
Zaman bilgisi
↓
Ne zaman?

Rastgele değer
↓
Hangi çalışan uygulama örneğiyle ilişkili?

Sayaç
↓
Aynı anda üretilenlerden hangisi?
```

## Örnek 47

Bu üç parça birlikte

ObjectId'nin

çakışma ihtimali çok düşük

kimlikler üretmesine

yardım eder.

---

## ObjectId Gerçekten Benzersiz mi?

Şimdi kritik soruya geldik.

Bu kadar parçaya rağmen:

> İki ObjectId
> hiçbir zaman aynı olamaz mı?

Burada dikkatli konuşmalıyız.

ObjectId'ler:

```text
likely unique
```

## Örnek 48

yani aynı değerin oluşma olasılığı

çok düşük olacak şekilde

tasarlanmıştır.

Fakat:

```text
Evrenin hiçbir yerinde
asla aynı ObjectId oluşamaz.
```

## Örnek 49

demek doğru olmaz.

Bizim uygulamamız açısından

daha önemli bir garanti vardır.

Standart bir MongoDB collection'ında:

```text
_id
```

## Örnek 50

alanı benzersiz olmalıdır.

Örneğin:

```text
_id: A → Interstellar

_id: B → Inception
```

## Örnek 51

olabilir.

Ama aynı collection içinde:

```text
_id: A → Interstellar

_id: A → Inception
```

## Örnek 52

şeklinde iki document

bulunamaz.

MongoDB,

standart collection'larda `_id` için

benzersiz bir index oluşturur.

Bu yüzden aynı `_id` ile

ikinci bir document eklemeye çalışırsak

işlem kabul edilmez.

Şimdilik:

```text
ObjectId
↓
Çakışma ihtimali çok düşük
kimlikler üretmeye yardım eder.

_id
↓
Aynı collection içinde
benzersiz olmak zorundadır.
```

## Örnek 53

ayrımını bilmemiz yeterlidir.

---

## Kritik Ayrım: `_id` ile ObjectId Aynı Şey Değil

Burada yalnızca

bir cümleyi net tutalım:

```text
_id
↓
Kimlik field'ı

ObjectId(...)
↓
Bu field için yaygın kullanılan
BSON value türü
```

## Örnek 54

Yani:

```text
_id
≠
ObjectId
```

## Örnek 55

`_id` alanının değeri

mutlaka ObjectId olmak zorunda değildir.

Örneğin uygun bir tasarımda:

```json
{
  "_id": 42,
  "title": "Interstellar"
}
```

## Örnek 56

gibi başka benzersiz değerler de

kullanılabilir.

ObjectId,

MongoDB ile çalışırken

çok sık karşılaşacağımız

varsayılan ve yaygın seçimdir.

---

## Peki Biz `_id` Yazmazsak?

Şimdi film platformumuza

geri dönelim.

Backend'den şu document'ı

gönderdiğinizi düşünün:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7
}
```

## Örnek 57

Dikkat edin.

Burada:

```text
_id
```

## Örnek 58

yok.

Document MongoDB'ye ekleniyor.

Sonra collection'ı açıyorsunuz.

Ve karşınızda:

```text
_id: ObjectId("...")
```

## Örnek 59

alanını görüyorsunuz.

Peki bunu kim ekledi?

MongoDB driver'ları,

document'ta `_id` yoksa

çoğu standart kullanımda

bir ObjectId üretip

`_id` alanını ekler.

Bizim kullanacağımız:

```text
Node.js MongoDB driver
```

## Örnek 60

da bu davranışı gösterir.

Eğer uygulama veya driver

`_id` sağlamazsa,

MongoDB sunucusu da

gerekli `_id` alanını

oluşturabilir.

Başlangıçta akışımızı

şöyle düşünebiliriz:

```text
Document hazırlanıyor.
↓
_id yok.
↓
Kimlik gerekiyor.
↓
ObjectId üretiliyor.
↓
_id alanına yerleştiriliyor.
↓
Document kaydediliyor.
```

## Örnek 61

Bu yüzden MongoDB'yi

ilk kez kullanan biri:

```text
Ben bunu yazmadım.
```

## Örnek 62

dediği hâlde

document'ta `_id` görebilir.

Artık nedenini biliyoruz.

---

## ObjectId Aynı Filmin İki Kez Eklenmesini Engeller mi?

Şimdi küçük bir tuzak.

Aynı filmi yanlışlıkla

iki kez eklediğimizi düşünün:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014
}
```

## Örnek 63

Birinci document:

```text
_id → A
title → Interstellar
```

## Örnek 64

İkinci document:

```text
_id → B
title → Interstellar
```

## Örnek 65

olabilir.

Çünkü:

```text
A ≠ B
```

## Örnek 66

MongoDB açısından

bunlar iki farklı document'tır.

Yani ObjectId'nin görevi:

```text
Bu film daha önce eklendi mi?
```

## Örnek 67

sorusuna cevap vermek değildir.

Görevi:

```text
Bu document hangisi?
```

## Örnek 68

sorusuna cevap verebilecek

bir kimlik sağlamaktır.

Bu ayrımı koruyalım:

```text
ObjectId
↓
Document'ları ayırmaya yardım eder.

Ama
↓
Aynı gerçek dünya verisinin
iki kez eklenmesini
tek başına engellemez.
```

## Örnek 69

Bir filmin iki kez eklenmesini

engellemek istiyorsak

başka kurallar tasarlamamız gerekir.

Onlarla ileride karşılaşacağız.

---

## ObjectId'yi Oluşturulma Tarihi Olarak Kullanabilir miyiz?

Artık ObjectId'nin

ilk dört byte'ında

timestamp olduğunu biliyoruz.

O zaman akla şu gelebilir:

> Madem zaman bilgisi var,
> document'ları `_id`ye göre sıralayıp
> oluşturulma tarihini bulamaz mıyız?

ObjectId'ler

oluşturulma zamanına göre

yaklaşık bir sıralama sağlayabilir.

Fakat bu sıralama

kusursuz bir zaman çizelgesi değildir.

Çünkü:

```text
timestamp
↓
saniye hassasiyetindedir.
```

## Örnek 70

Aynı saniyede üretilen ObjectId'lerin

kesin oluşturulma sırası

yalnızca timestamp'e bakılarak

garanti edilemez.

Üstelik ObjectId'ler

client tarafında üretilebildiği için

farklı sistemlerin saatleri de

etkili olabilir.

Bu yüzden:

```text
ObjectId
↓
yaklaşık oluşturulma zamanı bilgisi

createdAt
↓
uygulamanın açık tarih alanı
```

## Örnek 71

ayrımını korumak daha doğrudur.

Şimdilik ObjectId'yi

bir zaman kayıt sistemi olarak değil,

öncelikle:

```text
document kimliği için
kullanılan BSON veri türü
```

## Örnek 72

olarak düşünelim.

---

## Şimdi Baştan Cevaplayalım: Neden `1` Değil?

Bölümün başında

şunu sormuştuk:

> Neden `_id: 1` değil?

Artık daha iyi bir cevap verebiliriz.

Aslında:

```text
_id: 1
```

## Örnek 73

kullanmak teknik olarak

mümkün olabilir.

Ama o kimlikleri

biz üretmek ve

benzersiz tutmak zorundayız.

ObjectId ise:

```text
küçük,

hızlı üretilebilen,

çakışma olasılığı çok düşük,

merkezi bir sıra numarasına
bağımlı olmayan
```

## Örnek 74

bir kimlik üretme yaklaşımı sunar.

Ve MongoDB driver'ları

bunu bizim için

otomatik olarak üretebilir.

Yani ObjectId'nin amacı:

```text
Uzun ve karmaşık görünmek
```

## Örnek 75

değildir.

Amaç:

```text
Document'lar için
çakışma olasılığı çok düşük
kimlik değerleri üretmek
```

## Örnek 76

tir.

---
## Bölüm Özeti

Bu bölüme:

```text
Neden _id değeri
1, 2, 3 değil?
```

## Örnek 77

sorusuyla başladık.

ObjectId'nin

MongoDB'de yaygın kullanılan

bir BSON kimlik türü olduğunu gördük.

Bir ObjectId:

```text
12 byte
```

## Örnek 78

uzunluğundadır.

Ekranda ise genellikle:

```text
24 hexadecimal karakter
```

## Örnek 79

olarak görünür.

Yapısı:

```text
ObjectId
│
├── 4 byte → zaman bilgisi
├── 5 byte → rastgele değer
└── 3 byte → sayaç
```

## Örnek 80

şeklindedir.

Bu parçaların birlikte

kimlik üretmeye yardımcı olduğunu

öğrendik.

Ayrıca:

```text
_id
≠
ObjectId
```

## Örnek 81

ayrımını tekrar netleştirdik.

```text
_id
↓
Kimlik field'ı

ObjectId
↓
Bu field için
yaygın kullanılan BSON value türü
```

## Örnek 82

ObjectId'nin

`_id` için zorunlu olmadığını,

fakat `_id` verilmediğinde

MongoDB driver'ları tarafından

çok sık otomatik üretildiğini gördük.

Son olarak:

```text
ObjectId
↓
Document kimliği

≠

Aynı verinin
iki kez eklenmesini önleyen kural
```

## Örnek 83

ayrımını yaptık.

Artık şu görüntüyü:

```text
_id: ObjectId("507f191e810c19729de860ea")
```

## Örnek 84

gördüğümüzde

neye baktığımızı biliyoruz.

---

## Sonraki Adım

MongoDB'nin temel yapı taşlarını

artık tanıyoruz.

```text
Database

Collection

Document

Field

Value

_id

ObjectId
```

## Örnek 85

Ama fark ettiyseniz

şimdiye kadar

bunların hepsini:

```text
konuştuk,

çizdik,

örnekledik...
```

## Örnek 86

fakat henüz

gerçek bir MongoDB ortamına

bağlanmadık.

Şimdi teoriden

uygulamaya geçme zamanı.

Fakat önce bir karar vermeliyiz.

MongoDB:

```text
kendi bilgisayarımızda mı
çalışacak?
```

## Örnek 87

Yoksa:

```text
bulutta mı?
```

## Örnek 88

Bu noktada karşımıza MongoDB Atlas çıkar.

Peki:

```text
MongoDB Atlas
```
