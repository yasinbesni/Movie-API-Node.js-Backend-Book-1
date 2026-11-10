# Bölüm 30 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Mongoose'ta Schema ve Model Neden Var?

## Bağlantı Var, Peki Düzen Nerede?

Bir önceki bölümde

backend'imizi MongoDB Atlas'a bağladık.

Artık şu yol çalışıyor:

```text
Node.js + Express
↓
Mongoose
↓
MongoDB Atlas
```

## Örnek 2

Yani backend'imiz

MongoDB'ye ulaşabiliyor.

Fakat bağlantının kurulmuş olması

tek başına şunu söylemez:

> Uygulamamızdaki bir film kaydı
> nasıl görünmeli?

Film platformumuza

şu veri geldiğini düşünün:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "voteAverage": 8.7
}
```

## Örnek 3

Gayet düzenli.

Bir süre sonra

başka bir film geliyor:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8
}
```

## Örnek 4

Burada da sorun yok.

Ama üçüncü Request:

```json
{
  "name": "Arrival",
  "year": "iki bin on altı",
  "score": 7.9
}
```

## Örnek 5

şeklinde gelsin.

Bir şey dikkatinizi çekti mi?

İlk iki filmde:

```text
title
releaseYear
voteAverage
```

## Örnek 6

kullanılmıştı.

Şimdi ise:

```text
name
year
score
```

## Örnek 7

geldi.

Üstelik:

```text
releaseYear
↓
sayı bekliyoruz
```

## Örnek 8

derken:

```text
"iki bin on altı"
```

## Örnek 9

gibi sayıya çevrilemeyecek

bir değerle karşılaştık.

Bir başka Request'te ise:

```json
{
  "releaseYear": 1999,
  "voteAverage": 8.7
}
```

## Örnek 10

gelsin.

Bu kez de:

```text
title
```

## Örnek 11

hiç yok.

Bir süre sonra

`movies` collection'ımızın

şöyle bir hâle geldiğini düşünün:

```text
movies
│
├── title + releaseYear + voteAverage
├── title + releaseYear + voteAverage
├── name + year + score
├── releaseYear + voteAverage
└── ...
```

## Örnek 12

MongoDB'nin esnek belge yapısı

buna teknik olarak izin verebilir.

Ama bizim uygulamamız için

yeni bir problem oluşur:

> Film dediğimiz veri
> hangi kurallara göre oluşturulacak?

İşte Mongoose'un

asıl rolü burada

daha görünür hâle gelir.

---

## Mongoose Burada Ne Yapıyor?

29. bölümde

Mongoose'u yalnızca bağlantı kurarken

kullanmıştık:

```js
await mongoose.connect(process.env.MONGODB_URL);
```

## Örnek 13

Bu yüzden Mongoose'u

şimdilik yalnızca:

```text
MongoDB'ye bağlanan araç
```

## Örnek 14

gibi düşünmüş olabilirsiniz.

Ama görevi bununla sınırlı değildir.

Mongoose,

Node.js uygulamamız ile MongoDB arasında

çalışırken verilerimizi

belirlediğimiz bir yapıya göre

yönetmemize de yardımcı olur.

Büyük resmi şöyle düşünün:

```text
Node.js uygulamamız
↓
Mongoose
↓
MongoDB
```

## Örnek 15

MongoDB:

```text
Verinin saklandığı veritabanı
```

## Örnek 16

Mongoose ise:

```text
Uygulama tarafında
MongoDB verileriyle çalışmamızı
kolaylaştıran katman
```

## Örnek 17

Şimdi bu katmanın

iki temel parçasıyla

tanışacağız:

```text
Schema

Model
```

## Örnek 18

Fakat ikisini

ezberleyerek değil,

neden ihtiyaç duyduğumuzu

görerek öğreneceğiz.

---

## İlk İhtiyacımız: Film Nasıl Görünmeli?

Film kayıtlarımızın

şu yapıda olmasını istediğimizi düşünün:

```text
Film
│
├── title       → String
├── releaseYear → Number
└── voteAverage → Number
```

## Örnek 19

Ayrıca:

```text
title
```

## Örnek 20

olmadan bir film kaydı

oluşturmak istemiyoruz.

O hâlde beklentimiz:

```text
title
↓
String
↓
zorunlu

releaseYear
↓
Number

voteAverage
↓
Number
```

## Örnek 21

şeklinde.

Bu kuralları

yalnızca bizim bilmemiz yetmez.

Uygulamamızın da

bilmesi gerekir.

İşte Mongoose'ta:

```text
Schema
```

## Örnek 22

bu ihtiyaca cevap verir.

---

## Schema Nedir?

Mongoose'ta Schema'yı

şimdilik şöyle düşünün:

> Bir document'ın
> nasıl görünmesini beklediğimizi
> tanımlayan yapı.

Film örneğimizde:

```text
Schema
↓
Bir film document'ında
hangi field'lar olacak?

Bu field'larda
hangi tür değerler bekliyoruz?

Bazı field'lar
zorunlu mu?
```

## Örnek 23

sorularına cevap verir.

Kodumuzu yazalım:

```js
import mongoose from "mongoose";

const movieSchema = new mongoose.Schema({
  title: {
    type: String,
    required: true,
  },
  releaseYear: {
    type: Number,
  },
  voteAverage: {
    type: Number,
  },
});
```

## Örnek 24

İlk bakışta kod

biraz kalabalık görünebilir.

Fakat aslında az önce

söylediğimiz kuralların

JavaScript karşılığıdır.

```text
title
↓
String
↓
zorunlu

releaseYear
↓
Number

voteAverage
↓
Number
```

## Örnek 25

Şimdi kodu

küçük parçalara ayıralım.

---

## `new mongoose.Schema()` ile Ne Oluşturduk?

Biraz önce oluşturduğumuz Schema yapısına tekrar bakalım.

Bu kez bütün alanları yeniden yazmak yerine yalnızca Schema'yı oluşturan yapıya odaklanalım:

```js
const movieSchema = new mongoose.Schema({
  // Film alanları ve kuralları burada tanımlanır.
});
```

## Örnek 26

Burada:

```text
movieSchema
```

## Örnek 27

adında bir Schema oluşturuyoruz.

Yani uygulamamız artık:

```text
Bir film document'ı
nasıl görünmeli?
```

## Örnek 28

sorusunun cevabını

kod içinde taşıyor.

Şimdilik:

```text
movieSchema
↓
Film yapısının tanımı
```

## Örnek 29

dememiz yeterli.

---

## `title`, `type` ve `required` Ne Anlatıyor?

Schema'nın şu bölümüne bakalım:

```js
title: {
  type: String,
  required: true,
}
```

## Örnek 30

Buradaki:

```text
title
```

## Örnek 31

document'taki field adıdır.

Daha önce MongoDB bölümünde:

```json
{
  "title": "Interstellar"
}
```

## Örnek 32

için:

```text
title
↓
field

Interstellar
↓
value
```

## Örnek 33

demiştik.

Şimdi aynı field'a

Mongoose tarafında

bazı bilgiler ekliyoruz.

İlk bilgi:

```js
type: String
```

## Örnek 34

Sade anlamı:

```text
title
↓
String türünde bir değer bekliyoruz
```

## Örnek 35

Benzer şekilde:

```js
releaseYear: {
  type: Number,
}
```

## Örnek 36

şunu söyler:

```text
releaseYear
↓
Number bekliyoruz
```

## Örnek 37

`title` için ayrıca:

```js
required: true
```

## Örnek 38

yazdık.

Bunun anlamı:

```text
title
↓
zorunlu
```

## Örnek 39

Yani uygulamamız

film kaydında `title`

bulunmasını bekliyor.

---

## `type: Number` Gelen Her String'i Reddeder mi?

Burada önemli

bir ayrıntı var.

Şu Schema'yı düşünün:

```js
releaseYear: {
  type: Number,
}
```

## Örnek 40

Buna:

```text
"2016"
```

## Örnek 41

gibi sayıya çevrilebilen

bir String gelirse

Mongoose bunu:

```text
2016
```

## Örnek 42

sayısına dönüştürebilir.

Yani:

```text
"2016"
↓
Mongoose casting
↓
2016
```

## Örnek 43

olabilir.

Bu işleme:

```text
casting
```

## Örnek 44

denir.

Türkçe düşünürsek:

```text
değeri beklenen türe
dönüştürmeye çalışma
```

## Örnek 45

diyebiliriz.

Ama:

```text
"iki bin on altı"
```

## Örnek 46

gibi `Number`a çevrilemeyecek

bir değer geldiğinde

Mongoose bunu geçerli bir sayı

hâline getiremez.

Bu ayrıntıyı

ileride Validation konusuna geldiğimizde

daha derin inceleyeceğiz.

Şimdilik şu kadarını bilin:

```text
type: Number
↓
Mongoose o field'da
sayısal bir değerle çalışmak ister
```

## Örnek 47

---

## `required: true` Hata Ne Zaman Verir?

Bir başka önemli nokta da

`required` kontrolüdür.

Schema'mız:

```js
title: {
  type: String,
  required: true,
}
```

## Örnek 48

diyor.

Peki şu veriyi yazdığımız anda:

```js
const movie = new Movie({
  releaseYear: 2014,
});
```

## Örnek 49

Mongoose hemen:

```text
HATA!
```

## Örnek 50

diye durur mu?

Hayır.

Document oluşturma ile

validation aynı an değildir.

Mongoose,

document doğrulanırken

ve özellikle kaydetme sürecinde

`required` gibi kuralları kontrol eder.

Yani şimdilik akış:

```text
Document oluşturulur
↓
Mongoose kuralları kontrol eder
↓
title yoksa validation başarısız olur
↓
kayıt MongoDB'ye gönderilemez
```

## Örnek 51

şeklinde düşünülebilir.

`validate()` ve `save()`

konularını henüz işlemedik.

Bu yüzden burada duruyoruz.

Ama önemli ayrımı gördük:

```text
Schema kuralı tanımlar
↓
Mongoose bu kuralı
uygun aşamada kontrol eder
```

## Örnek 52

---

## Schema MongoDB'deki Collection mı?

Şimdi elimizde az önce tanımladığımız `movieSchema` var.

Peki bunu yazınca

Atlas'ta:

```text
movies
```

## Örnek 53

collection'ı oluştu mu?

Hayır.

Çünkü:

```text
Schema
≠
Collection
```

## Örnek 54

Schema:

```text
uygulama tarafındaki yapı tanımı
```

## Örnek 55

Collection ise:

```text
MongoDB tarafında
document'ların tutulduğu yapı
```

## Örnek 56

dır.

Yan yana görelim:

```text
movieSchema
↓
Node.js / Mongoose tarafında
film yapısının tanımı

movies
↓
MongoDB tarafında
film document'larının bulunduğu collection
```

## Örnek 57

Yani Schema yazmak:

```text
MongoDB'de collection oluşturmak
```

## Örnek 58

demek değildir.

---

## Schema ile MongoDB'nin Esnekliği Çelişiyor mu?

Burada şu soru

aklınıza gelebilir:

> MongoDB esnek bir veritabanıysa
> neden Schema kullanıyoruz?

Çünkü iki farklı katmandan

bahsediyoruz.

MongoDB tarafında:

```text
Document yapısı esnek olabilir.
```

## Örnek 59

Mongoose tarafında ise:

```text
Uygulamamızın beklediği yapıyı
tanımlıyoruz.
```

## Örnek 60

Yani:

```text
MongoDB
↓
Veriyi saklayan veritabanı

Mongoose Schema
↓
Uygulamamızdaki veri yapısının tanımı
```

## Örnek 61

Schema kullanmamız:

```text
MongoDB artık esnek değil
```

## Örnek 62

anlamına gelmez.

Biz yalnızca

film platformumuz için:

```text
Bir film kaydı
hangi yapıya göre çalışmalı?
```

## Örnek 63

sorusuna

uygulama tarafında

bir cevap veriyoruz.

---

## Schema Hazır. Peki Bu Tanımı Nasıl Kullanacağız?

Şimdi elimizde:

```text
movieSchema
```

## Örnek 64

var.

Bu Schema bize:

```text
Bir filmde hangi field'lar olacak?

Bu field'larda hangi tür değerler bekleniyor?

Hangi kurallar uygulanacak?
```

## Örnek 65

sorularının cevabını veriyor.

Fakat yalnızca yapıyı tanımlamak

uygulamamız için yeterli değil.

Çünkü 31–36. bölümlerde bu Model ile şu işlemleri adım adım yapmak isteyeceğiz:

```text
yeni film oluşturmak

filmleri bulmak

bir filmi güncellemek

bir filmi silmek
```

## Örnek 66

Burada iki farklı ihtiyacımız ortaya çıkıyor:

```text
1. Film nasıl görünmeli?
↓
Schema

2. Bu film kayıtlarıyla nasıl çalışacağız?
↓
Model
```

## Örnek 67

İşte Schema'dan Model'e geçişin nedeni budur.

---

## Model Nedir ve Neden İhtiyacımız Var?

Mongoose'ta Model,

bir Schema'yı kullanarak

MongoDB'deki kayıtlarla çalışmamızı sağlayan

uygulama tarafındaki yapıdır.

Film örneğimizde:

```text
movieSchema
↓
Film verisinin yapısını ve kurallarını tanımlar.

Movie Model
↓
Bu kurallara sahip film kayıtlarıyla
çalışmamızı sağlar.
```

## Örnek 68

Bu yüzden Schema ile Model'i

iki ayrı rakip kavram gibi düşünmeyin.

Model,

Schema'nın tanımladığı yapıyı kullanır.

İlişki şöyledir:

```text
Schema
↓
kurallar
↓
Model
↓
bu kurallara göre kayıtlarla çalışma
```

## Örnek 69

Bu bağlantı kurulmadan yalnızca:

```text
Film nasıl görünmeli?
```

## Örnek 70

sorusunun cevabına sahibiz.

Model'i oluşturduğumuzda ise

bu tanımı uygulamada gerçekten

kullanabileceğimiz aşamaya geçeriz.

---

## Schema'dan `Movie` Model'ini Oluşturalım

Film Schema'mız şu anda hazır:

```js
const movieSchema = new mongoose.Schema({
  title: {
    type: String,
    required: true,
  },
  releaseYear: {
    type: Number,
  },
  voteAverage: {
    type: Number,
  },
});
```

## Örnek 71

Şimdi hemen altına:

```js
const Movie = mongoose.model("Movie", movieSchema);
```

## Örnek 72

yazalım.

Bu satırı soldan sağa okuyalım.

```text
mongoose.model(...)
↓
Mongoose'tan bir Model oluşturmasını istiyoruz.

"Movie"
↓
Model'e verdiğimiz ad.

movieSchema
↓
Model'in kullanacağı Schema.
```

## Örnek 73

Yani cümle hâline getirirsek:

> `movieSchema` tanımını kullanarak
> `Movie` adında bir Model oluştur.

Sonuç artık:

```text
movieSchema
↓
Film yapısının tanımı

Movie
↓
Bu yapıyla çalışacağımız Model
```

## Örnek 74

şeklindedir.

Burada `Movie` adını

büyük harfle başlatmamız

JavaScript'in zorunlu bir kuralı değildir.

Mongoose Model'lerinde yaygın olarak:

```text
Movie
User
Review
```

## Örnek 75

gibi büyük harfle başlayan isimler kullanılır.

Böylece kodu okurken

bir Model ile çalıştığımızı

daha kolay fark ederiz.

---

## `Movie` Model'i Bize Ne Sağlıyor?

Model oluşturulmadan önce elimizde:

```text
film yapısının tanımı
```

## Örnek 76

vardı.

Model oluşturulduktan sonra ise

bu yapı üzerinden MongoDB kayıtlarıyla

çalışabileceğimiz bir Mongoose arayüzümüz var.

İleride örneğin:

```js
Movie.find()
```

## Örnek 77

ile film arayacağız.

Başka bir yerde:

```js
Movie.create(...)
```

## Örnek 78

ile film oluşturabileceğiz.

Bu metodların nasıl çalıştığını

henüz öğrenmiyoruz.

Şimdilik yalnızca şunu görüyoruz:

```text
movieSchema
↓
veriyi tarif eder

Movie
↓
o tarife göre kayıtlarla çalışır
```

## Örnek 79

Model'in Schema'dan farklı olmasının

asıl nedeni budur.

---

## `Movie` Model'i Hangi Collection ile Çalışacak?

Şimdi Model'i MongoDB tarafına bağlayalım.

Biz:

```js
mongoose.model("Movie", movieSchema);
```

## Örnek 80

dedik.

Model adımız:

```text
Movie
```

## Örnek 81

Mongoose varsayılan isimlendirme davranışıyla

bu Model'i MongoDB tarafındaki:

```text
movies
```

## Örnek 82

collection'ıyla ilişkilendirir.

Yani bizim örneğimizde:

```text
movieSchema
↓
Movie Model
↓
movies collection
```

## Örnek 83

zinciri oluşur.

Collection adını özel olarak belirlemek mümkündür.

Fakat kitap boyunca

varsayılan davranış bizim için yeterli.

Böylece artık şu iki tarafı

birbirine bağlayabiliyoruz:

```text
Uygulama tarafı
↓
Movie Model

MongoDB tarafı
↓
movies collection
```

## Örnek 84

Model,

uygulama kodumuzun

bu collection'daki kayıtlarla

çalıştığı ana Mongoose yapısıdır.

---

## Model Oluşturmanın İki Biçimini Ayıralım

Buraya kadar Model'i öğrenmek için en sade biçimi kullandık:

```js
const Movie = mongoose.model("Movie", movieSchema);
```

## Örnek 85

Bu kullanım doğrudur.

Satırı bir kez daha okuyalım:

```text
"Movie"
↓
Model'in adı

movieSchema
↓
Model'in kullanacağı Schema
```

## Örnek 86

Yani Mongoose'a şunu söylüyoruz:

> `movieSchema` tanımını kullanarak `Movie` adında bir Model oluştur.

Normal bir Node.js uygulamasında bu Model yalnızca bir kez derleniyorsa bu kısa kullanım çoğu zaman yeterlidir.

Fakat burada Mongoose'un bir davranışını daha bilmemiz gerekir.

Mongoose oluşturduğu Model'leri kendi içinde saklar. Bu kayıtlı Model'lere:

```js
mongoose.models
```

## Örnek 87

üzerinden ulaşabiliriz.

Örneğin `Movie` Model'i daha önce oluşturulduysa:

```js
mongoose.models.Movie
```

## Örnek 88

bize mevcut Model'i verir.

Bu bilgi bizi ikinci kullanıma götürür.

### Aynı Model Tekrar Oluşturulmaya Çalışırsa Ne Olur?

Geliştirme sırasında sıcak yeniden yükleme, bazı test düzenleri veya aynı Model tanımının yeniden çalıştırıldığı başka senaryolar oluşabilir.

Bu durumda kod yeniden şu satıra gelirse:

```js
mongoose.model("Movie", movieSchema);
```

## Örnek 89

Mongoose aynı isimdeki Model'i yeniden derlemeye çalışabilir. Özellikle aynı Model adı farklı bir Schema nesnesiyle yeniden tanımlanırsa `OverwriteModelError` oluşabilir.

Bu nedenle Model dosyalarında sık kullanılan korumalı bir biçim vardır:

```js
const Movie =
  mongoose.models.Movie ||
  mongoose.model("Movie", movieSchema);
```

## Örnek 90

Buradaki `||` JavaScript'in kısa devre davranışını kullanır.

Akış şöyledir:

```text
mongoose.models.Movie var mı?
↓
Evet
↓
Mevcut Movie Model'ini kullan.

Hayır
↓
mongoose.model("Movie", movieSchema) çalışsın
↓
Movie Model'ini oluştur.
```

## Örnek 91

Yani iki kullanımın görevi farklı değildir. Her ikisinin sonunda da elimizde `Movie` Model'i vardır.

Fark şuradadır:

```text
Temel kullanım
↓
const Movie = mongoose.model("Movie", movieSchema);
↓
Model'i doğrudan oluşturur veya mevcut tanımla çalışır.

Korumalı kullanım
↓
mongoose.models.Movie || mongoose.model(...)
↓
Önce kayıtlı Model'i arar; yoksa oluşturur.
```

## Örnek 92

Buradaki “korumalı” sözü güvenlik anlamında değildir. Parola, yetkilendirme veya veritabanı güvenliği sağlamaz. Yalnızca Model'in gereksiz yere yeniden derlenmesine karşı daha savunmacı bir yazım biçimidir.

Movie API'nin Model dosyalarında bu ikinci biçimi kullanacağız. Böylece ileride aynı dosya farklı geliştirme veya test akışlarında yeniden değerlendirildiğinde Model'i tekrar oluşturmayı denememiş oluruz.

---

## Model Dosyamızı Oluşturalım

Artık iki biçim arasındaki farkı bildiğimize göre kodu proje yapısındaki yerine koyalım.

MongoDB bağlantısını daha önce:

```text
src/db/initMongoConnection.js
```

## Örnek 93

dosyasına ayırmıştık.

Film Schema ve Model'ini de:

```text
src/db/models/movie.js
```

## Örnek 94

dosyasına yerleştirelim.

Klasör yapımız:

```text
src
└── db
    ├── initMongoConnection.js
    └── models
        └── movie.js
```

## Örnek 95

`movie.js` dosyasının içeriği:

```js
import mongoose from "mongoose";

const movieSchema = new mongoose.Schema({
  title: {
    type: String,
    required: true,
  },
  releaseYear: {
    type: Number,
  },
  voteAverage: {
    type: Number,
  },
});

export const Movie =
  mongoose.models.Movie ||
  mongoose.model("Movie", movieSchema);
```

## Örnek 96

Artık bu son iki satırı gördüğümüzde ne yaptığını biliyoruz:

```text
Movie zaten kayıtlıysa
↓
mevcut Model'i export et

Movie kayıtlı değilse
↓
movieSchema'dan Model'i oluştur
↓
onu export et
```

## Örnek 97

Dosyanın iki temel görevi de böylece açık:

```text
movieSchema
↓
Film yapısını tanımlar.

Movie
↓
Bu yapıyla çalışacağımız Model'i dışarı aktarır.
```

## Örnek 98

`Movie` Model'ini export ediyoruz. Çünkü sonraki bölümlerde film oluştururken, ararken, güncellerken ve silerken aynı Model'i farklı dosyalarda kullanacağız.

---

## Kavramları Aynı Zincirde Görelim

Bu bölümde artık üç katmanı

birbirine bağlayabiliriz:

```text
movieSchema
↓
Film verisinin yapısını ve kurallarını tanımlar.

Movie
↓
Bu Schema'yı kullanarak
film kayıtlarıyla çalışacağımız Model'dir.

movies
↓
MongoDB'de film kayıtlarının
tutulacağı collection'dır.
```

## Örnek 99

Yani akış:

```text
Schema
↓
Model
↓
Collection
```

## Örnek 100

şeklindedir.

Burada önemli olan,

bu üç kavramın

aynı şey olmadığını bilmektir.

Bir sonraki bölümde

Model'den tek bir film oluşturduğumuzda

ortaya çıkan nesnenin neden:

```text
Mongoose Document
```

## Örnek 101

olarak adlandırıldığını göreceğiz.

Böylece zincirin son parçasını da

yerine koyacağız.

---

## Şimdi Nerede Duruyoruz?

29. bölüm sonunda:

```text
MongoDB bağlantısı
✅
```

## Örnek 102

hazırdı.

Bu bölümde:

```text
movieSchema
✅

Movie Model
✅
```

## Örnek 103

oluşturduk.

Yani artık uygulamamız:

```text
MongoDB'ye nasıl ulaşacağını
```

## Örnek 104

bildiği gibi:

```text
bir film kaydının
nasıl görünmesini beklediğini
```

## Örnek 105

de biliyor.

Ve bu yapı üzerinden

MongoDB ile çalışabilecek

Model'e de sahip.

Fakat henüz:

```text
tek bir gerçek film
kaydetmedik.
```

## Örnek 106

İşte burada durmamız gerekiyor.

Çünkü bir sonraki adım

yeni bir kavram getiriyor.

---

## Bölüm Özeti

Bu bölümde

Mongoose'un yalnızca

MongoDB bağlantısı kurmak için

kullanılmadığını gördük.

Uygulama tarafındaki veriyi

daha düzenli yönetmek için:

```text
Schema

Model
```

## Örnek 107

yapılarını kullanmaya başladık.

Önce:

```text
Bir film nasıl görünmeli?
```

## Örnek 108

sorusunu sorduk.

Cevabımızı `new mongoose.Schema(...)` ile tanımladık.

Schema'nın:

```text
field yapısını

beklenen türleri

bazı kuralları
```

## Örnek 109

tanımlayabildiğini gördük.

`type: Number` kullanımında

Mongoose'un bazı değerleri

beklenen türe dönüştürmeye

çalışabileceğini de gördük.

Örneğin:

```text
"2016"
↓
2016
```

## Örnek 110

dönüşümü mümkün olabilir.

Ama:

```text
"iki bin on altı"
```

## Örnek 111

gibi sayı hâline getirilemeyen

bir değer sorun oluşturur.

Ardından Schema'nın

tek başına MongoDB işlemleri

yapmadığını fark ettik.

Bu nedenle önce Model oluşturmanın temel biçimini gördük:

```js
const Movie = mongoose.model("Movie", movieSchema);
```

## Örnek 112

Ardından Mongoose'un kayıtlı Model'lerini `mongoose.models` altında tuttuğunu öğrenip proje dosyamızda korumalı biçimi kullandık:

```js
export const Movie =
  mongoose.models.Movie ||
  mongoose.model("Movie", movieSchema);
```

## Örnek 113

Böylece `Movie` daha önce oluşturulmuşsa mevcut Model'i yeniden kullanıyor, oluşturulmamışsa `movieSchema` üzerinden Model'i oluşturuyoruz.

Ve şu temel ayrımı

yerine oturttuk:

```text
Schema
↓
Yapıyı tanımlar.

Model
↓
Bu yapıyla MongoDB'de
işlem yapmamızı sağlar.
```

## Örnek 114

Ayrıca bizim örneğimizde:

```text
Movie Model
↓
movies collection
```

## Örnek 115

ilişkisini gördük.

Artık:

```text
Bağlantı
✅

Schema
✅

Model
✅
```

## Örnek 116

hazır.

---

## Sonraki Adım

Artık elimizde:

```text
MongoDB bağlantısı
↓
movieSchema
↓
Movie Model
```

## Örnek 117

var.

Yani uygulamamız hem

MongoDB'ye nasıl ulaşacağını

hem de film kayıtlarıyla

hangi yapı üzerinden çalışacağını biliyor.

Fakat henüz önemli bir parçayı

gerçekten kullanmadık:

```text
Movie Model'den
tek bir film oluşturmak
```

## Örnek 118

Bir sonraki bölümde:

```js
new Movie({ ... })
```

## Örnek 119

yazdığımızda ne oluştuğunu göreceğiz.

Sonra şu ayrımı kuracağız:

```text
Movie
↓
Model

new Movie(...)
↓
tek bir Mongoose Document
```
