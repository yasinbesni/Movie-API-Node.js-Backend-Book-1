# Bölüm 21 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# SQL ve NoSQL Arasındaki Fark Nedir?

## Veriyi Hangi Modelle Düzenleyeceğiz?

Bir önceki bölümde

veritabanına neden ihtiyaç duyduğumuzu gördük.

Artık verinin:

```text
uygulama kapansa da
var olmaya devam etmesi
```

## Örnek 2

gerektiğini biliyoruz.

Şimdi bir adım ileri gidebiliriz.

Veriyi kalıcı olarak saklamak

tek başına yeterli değildir.

Onu nasıl düzenleyeceğimize de

karar vermemiz gerekir.

Veritabanı sistemleri

bu konuda aynı modeli kullanmaz.

İşte bu noktada

iki kavramla karşılaşacağız:

```text
İlişkisel veritabanları

ve

NoSQL veritabanları
```

## Örnek 3

Bu bölümün amacı

bu iki yaklaşımı ezberletmek değil,

MongoDB'nin neden

```text
belge tabanlı NoSQL veritabanı
```

## Örnek 4

olarak tanımlandığını anlayacağımız

zihinsel modeli kurmaktır.

---

## SQL Aslında Nedir?

Burada önce

önemli bir kavram ayrımı yapalım.

**SQL**,

bir veritabanının adı değildir.

SQL'in açılımı:

```text
Structured Query Language
```

## Örnek 5

şeklindedir.

Türkçede:

```text
Yapılandırılmış Sorgu Dili
```

## Örnek 6

olarak ifade edilebilir.

SQL,

ilişkisel veritabanlarındaki

verilerle çalışmak için

yaygın olarak kullanılan bir sorgu dilidir.

Örneğin SQL kullanarak:

```text
veri ekleyebilir,

veri okuyabilir,

veri güncelleyebilir,

veri silebiliriz.
```

## Örnek 7

Bu kitapta

SQL komutlarını öğrenmeyeceğiz.

Şimdilik şu ayrım yeterlidir:

```text
SQL
↓
Bir sorgu dili

İlişkisel veritabanı
↓
Verileri ilişkisel modelle
düzenleyen veritabanı sistemi
```

## Örnek 8

Günlük kullanımda:

```text
SQL veritabanı
```

## Örnek 9

ifadesini sıkça duyabiliriz.

Bu ifade çoğunlukla:

```text
ilişkisel veritabanı
```

## Örnek 10

anlamında kullanılır.

Fakat teknik olarak:

```text
SQL
```

## Örnek 11

ile:

```text
veritabanının kendisi
```

## Örnek 12

aynı şey değildir.

🧠 Akılda Kalsın

```text
SQL
≠
Veritabanının kendisi

SQL
↓
Verilerle çalışmak için
kullanılan sorgu dili
```

## Örnek 13

---

## İlişkisel Veritabanı Nedir?

İlişkisel veritabanlarında

veriler çoğunlukla:

```text
tablolar
```

## Örnek 14

içinde düzenlenir.

Bir film uygulamasını düşünelim.

Filmleri saklamak için:

```text
movies
```

## Örnek 15

adında bir tablomuz olabilir.

Bu tablonun sütunları:

```text
id

title

releaseYear

rating
```

## Örnek 16

olabilir.

Veriler ise

satırlar hâlinde tutulabilir:

```text
movies

id | title        | releaseYear | rating
---|--------------|-------------|-------
1  | Interstellar | 2014        | 8.7
2  | Inception    | 2010        | 8.8
```

## Örnek 17

Burada:

```text
Sütun
↓
Hangi bilginin tutulduğunu gösterir.

Satır
↓
Tek bir kaydı temsil eder.
```

## Örnek 18

Örneğin:

```text
Interstellar
```

## Örnek 19

filmi,

tablodaki bir satırdır.

Bu filmin:

```text
title

releaseYear

rating
```

## Örnek 20

bilgileri ise

farklı sütunlarda bulunur.

---

## Neden “İlişkisel” Deniyor?

Çünkü farklı veriler

birbirleriyle ilişkilendirilebilir.

Örneğin uygulamamızda:

```text
users
```

## Örnek 21

ve:

```text
reviews
```

## Örnek 22

adında iki tablo olduğunu düşünelim.

Kullanıcılarımız:

```text
users

id | name
---|------
1  | Ada
```

## Örnek 23

şeklinde tutulabilir.

Yorumlarımız ise:

```text
reviews

id | userId | movieId | comment
---|--------|---------|----------------
1  | 1      | 42      | Harika bir film.
```

## Örnek 24

şeklinde olabilir.

Buradaki:

```text
userId = 1
```

## Örnek 25

bilgisi,

yorumun:

```text
users
```

## Örnek 26

tablosundaki:

```text
id = 1
```

## Örnek 27

olan kullanıcıyla

ilişkili olduğunu gösterebilir.

```text
users
↓
id = 1
↓
reviews
↓
userId = 1
```

## Örnek 28

İlişkisel modelin

önemli fikirlerinden biri budur.

Veriler:

```text
farklı tablolar
```

## Örnek 29

içinde tutulabilir

ve aralarında ilişkiler kurulabilir.

---

## NoSQL Nedir?

Şimdi diğer büyük yaklaşıma geçelim.

**NoSQL**,

ilişkisel tablo modelinden farklı

veri modelleri kullanan

veritabanı sistemleri için kullanılan

genel bir ifadedir.

Burada önemli bir ayrım vardır.

NoSQL:

```text
tek bir veritabanı türü
```

## Örnek 30

değildir.

NoSQL dünyasında

farklı veri modelleri bulunur.

Örneğin:

```text
belge tabanlı

anahtar-değer

graf

geniş sütun
```

## Örnek 31

gibi yaklaşımlar vardır.

Bu kitapta

bunların tamamını incelemeyeceğiz.

Bizim kullanacağımız MongoDB:

```text
belge tabanlı
```

## Örnek 32

bir NoSQL veritabanıdır.

Şimdilik bağlantımız şöyle olsun:

```text
NoSQL
↓
Farklı ilişkisel olmayan
veri modellerini kapsayan
genel kategori

MongoDB
↓
Belge tabanlı
NoSQL veritabanı
```

## Örnek 33

---

## Belge Tabanlı Veri Ne Demek?

İlişkisel modelde

bir filmi:

```text
Tablo
↓
Satır
↓
Sütunlar
```

## Örnek 34

şeklinde düşünmüştük.

Belge tabanlı yaklaşımda ise

bir filme ait bilgiler

aynı belge içinde bulunabilir.

Örneğin:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7
}
```

## Örnek 35

İlk bakışta bu yapı

bize oldukça tanıdık gelebilir.

Çünkü JavaScript'te de

şöyle nesneler oluşturabiliyoruz:

```js
const movie = {
  title: "Interstellar",
  releaseYear: 2014,
  rating: 8.7,
};
```

## Örnek 36

Fakat önemli bir noktayı

şimdiden ayıralım.

MongoDB'de saklanan veri

doğrudan bir JavaScript nesnesi değildir.

MongoDB,

belgeleri kendi veri biçimiyle saklar.

Bir sonraki bölümde

bu konuya geri döneceğiz.

Şimdilik temel fikir:

```text
Bir film
↓
Bir belge
↓
Filmin alanları
```

## Örnek 37

şeklindedir.

---

## MongoDB Bu Yaklaşımda Nerede Duruyor?

MongoDB,

belge tabanlı bir NoSQL veritabanıdır.

Başlangıç için şu ilişkiyi bilmemiz yeterlidir:

```text
MongoDB
↓
Belgelerle çalışır.
```

## Örnek 38

MongoDB'de bu belgelerin

nasıl gruplandığını

ve hangi yapılar içinde tutulduğunu

ilerleyen bölümlerde

ayrıntılı olarak öğreneceğiz.

Şimdilik yalnızca

ilişkisel tablo modeliyle

belge tabanlı modelin

aynı şey olmadığını ayıralım.

## SQL ve NoSQL Arasındaki Temel Fark Nedir?

Başlangıç düzeyinde

en görünür farklardan biri

verinin nasıl modellendiğidir.

İlişkisel yaklaşım:

```text
Tablolar
↓
Satırlar
↓
Sütunlar
↓
Tablolar arasındaki ilişkiler
```

## Örnek 39

Belge tabanlı yaklaşımda ise:

```text
Belgeler
↓
Belge içindeki alanlar
```

## Örnek 40

Örneğin ilişkisel bir sistemde:

```text
movies tablosu
↓
Interstellar satırı
```

## Örnek 41

bulunabilir.

Belge tabanlı bir sistemde ise

aynı film:

```text
Film belgesi
↓
Interstellar
```

## Örnek 42

şeklinde temsil edilebilir.

Buradan şu sonucu çıkarmamalıyız:

> NoSQL veritabanlarında ilişkiler yoktur.

Bu doğru değildir.

NoSQL sistemlerinde de

veriler arasında bağlantılar kurulabilir.

Fark,

verinin ve bu bağlantıların

nasıl modellendiğidir.

![SQL ve NoSQL veri modelleri karşılaştırması](images/21-sql-nosql-veri-modeli-baski.svg)

---

## NoSQL “Kuralsız Veri” mi Demek?

Hayır.

Bu oldukça önemli bir yanlış anlamadır.

MongoDB'de

aynı collection içindeki belgelerin

alanları farklılık gösterebilir.

Örneğin:

```json
{
  "title": "Interstellar",
  "rating": 8.7
}
```

## Örnek 43

ve:

```json
{
  "title": "Inception",
  "rating": 8.8,
  "director": "Christopher Nolan"
}
```

## Örnek 44

aynı collection içinde bulunabilir.

İkinci belgede:

```text
director
```

## Örnek 45

alanı varken

ilk belgede bulunmayabilir.

Bu bize bir esneklik sağlar.

Fakat:

```text
Esnek yapı
```

## Örnek 46

şu anlama gelmez:

```text
Hiçbir kural yok.
```

## Örnek 47

Gerçek bir backend uygulamasında

verilerin belirli kurallara uymasını

çoğu zaman isteriz.

Örneğin:

```text
title boş olmasın.

rating sayı olsun.

releaseYear belirli bir aralıkta olsun.
```

## Örnek 48

gibi kurallarımız olabilir.

MongoDB tarafında

veri doğrulama mekanizmaları bulunabilir.

Daha sonra Mongoose kullandığımızda da

veri yapımızı tanımladığımız

çok daha belirgin kurallarla karşılaşacağız.

Şimdilik:

```text
Esnek yapı
≠
Kuralsız yapı
```

## Örnek 49

ayrımını bilmemiz yeterlidir.

---

## NoSQL Her Zaman Daha Hızlı mı?

Hayır.

Şöyle bir kural yoktur:

```text
NoSQL her zaman hızlıdır.

SQL her zaman yavaştır.
```

## Örnek 50

Aynı şekilde

bunun tersi de doğru değildir.

Bir veritabanının performansı:

```text
veri modeline,

sorgulara,

indekslere,

veri miktarına,

uygulama tasarımına,

kullanılan altyapıya
```

## Örnek 51

ve başka birçok etkene bağlı olabilir.

Bu nedenle:

> Hangisi daha hızlı?

sorusu tek başına

veritabanı seçmek için yeterli değildir.

Aynı şekilde:

```text
Yeni proje
↓
Mutlaka NoSQL kullan.
```

## Örnek 52

gibi bir kural da yoktur.

Veritabanı seçimi

uygulamanın ihtiyaçlarına göre yapılır.

---

## Peki Biz Neden MongoDB Kullanacağız?

Backend tarafında:

```text
JavaScript

Node.js

Express
```

## Örnek 53

kullanıyoruz.

MongoDB'nin belge yapısı da

alan ve değerlerden oluşan

tanıdık bir yapı sunar.

Örneğin uygulamamızda:

```js
const movie = {
  title: "Interstellar",
  releaseYear: 2014,
  rating: 8.7,
};
```

## Örnek 54

şeklinde düşündüğümüz veri,

MongoDB'de de

belge biçiminde

benzer bir yapıyla temsil edilebilir.

Bu sayede ilerleyen bölümlerde:

```text
Postman
↓
Request Body
↓
req.body
↓
JavaScript
↓
MongoDB
```

## Örnek 55

akışını daha rahat takip edebileceğiz.

Fakat MongoDB kullanmamız:

```text
MongoDB
↓
Her proje için en iyi seçim
```

## Örnek 56

anlamına gelmez.

Amacımız:

```text
tek bir veritabanını ezberlemek
```

## Örnek 57

değil,

bir backend uygulamasının

veritabanıyla nasıl çalıştığını

sağlam bir örnek üzerinden öğrenmektir.

---

## İki Yaklaşımı Yan Yana Görelim

Elimizde şu film olsun:

```text
Interstellar
2014
8.7
```

## Örnek 58

İlişkisel yaklaşımda:

```text
movies tablosu

id | title        | releaseYear | rating
---|--------------|-------------|-------
1  | Interstellar | 2014        | 8.7
```

## Örnek 59

Belge tabanlı yaklaşımda:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7
}
```

## Örnek 60

İki yapı da

aynı gerçek dünyadaki filmi

temsil edebilir.

Fakat bunu

farklı veri modelleriyle yaparlar.

Bu nedenle temel soru:

> Hangisi doğru?

değildir.

Daha doğru soru:

> Uygulamamızın verisini
> hangi modelle düzenlemek
> ihtiyaçlarımıza daha uygun?

olmalıdır.

---

## Bölüm Özeti

Bu bölümde

bütün veritabanlarının

veriyi aynı şekilde

düzenlemediğini gördük.

Önce:

```text
SQL
```

## Örnek 61

ile:

```text
ilişkisel veritabanı
```

## Örnek 62

arasındaki farkı ayırdık.

SQL'in:

```text
bir veritabanı değil,

bir sorgu dili
```

## Örnek 63

olduğunu öğrendik.

İlişkisel veritabanlarında

verilerin çoğunlukla:

```text
tablolar

satırlar

sütunlar
```

## Örnek 64

üzerinden düzenlendiğini gördük.

Ardından NoSQL kavramına geçtik.

NoSQL'in:

```text
tek bir veri modeli
```

## Örnek 65

olmadığını,

farklı veri modellerini kapsayan

genel bir kategori olduğunu öğrendik.

MongoDB'nin ise:

```text
belge tabanlı
bir NoSQL veritabanı
```

## Örnek 66

olduğunu gördük.

Temel karşılaştırmamız:

```text
İlişkisel yaklaşım
↓
Tablolar ve satırlar

Belge tabanlı yaklaşım
↓
Belgeler ve alanlar
```

## Örnek 67

şeklinde oldu.

MongoDB'deki:

```text
collection

document
```

## Örnek 68

kavramlarını ise

ilerleyen bölümlerde

ayrı ayrı inceleyeceğiz.

Ayrıca iki önemli yanlış anlamayı ayırdık:

```text
Esnek yapı
≠
Kuralsız yapı
```

## Örnek 69

ve:

```text
NoSQL
≠
Her zaman daha hızlı
```

## Örnek 70

---

## Sonraki Adım

Artık MongoDB'nin

veritabanı dünyasındaki yerini biliyoruz.

```text
Veritabanları
↓
İlişkisel / NoSQL
↓
NoSQL
↓
Belge tabanlı
↓
MongoDB
```

## Örnek 71

Fakat henüz

MongoDB'deki bir belgenin

gerçekte nasıl saklandığını bilmiyoruz.

Biraz önce şöyle bir yapı gördük:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7
}
```

## Örnek 72

Bu görünüm

JSON'a çok benziyor.

Peki MongoDB

veriyi gerçekten JSON olarak mı saklıyor?

Yoksa arka planda

başka bir veri biçimi mi kullanıyor?

Bir sonraki bölümde

MongoDB'deki verinin

JSON'a neden benzediğini

ama neden tam olarak JSON olmadığını göreceğiz.

Bu bizi önemli bir kavrama götürecek:

```text
BSON
```
