# Bölüm 55 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# MongoDB'de Veriler Gömülmeli mi, Referansla mı Saklanmalı?

## Bir Filmin Dışındaki Bilgileri Nasıl Saklayacağız?

54. bölümde artık yalnızca Query yazmayı değil, tekrar eden Query davranışlarını nerede tutacağımızı da düşünmeye başladık.

Movie API'mizde şu anda bir film kabaca şu alanlara sahip:

```json
{
  "_id": "...",
  "title": "Interstellar",
  "releaseYear": 2014,
  "voteAverage": 8.7
}
```

## Örnek 2

Fakat gerçek bir film uygulamasında yalnızca filmin kendi bilgileri yetmez.

Örneğin filmin yönetmenini de göstermek isteyebiliriz:

```text
Interstellar
↓
Christopher Nolan
```

## Örnek 3

İlk bakışta bu çok basit görünebilir.

Movie Document'ına yeni bir alan ekleriz:

```json
{
  "title": "Interstellar",
  "director": "Christopher Nolan"
}
```

## Örnek 4

Ama yönetmen hakkında yalnızca isim tutmak istemediğimizi düşünelim.

Örneğin:

```text
name
birthYear
country
```

## Örnek 5

bilgilerine de ihtiyacımız olsun.

Şimdi asıl veri modelleme sorusu ortaya çıkar:

> Yönetmen bilgisini doğrudan Movie Document'ının içine mi koymalıyız, yoksa yönetmeni ayrı bir Document olarak saklayıp filme yalnızca bağlantısını mı vermeliyiz?

MongoDB'de bu iki yaklaşımın da yeri vardır.

Bu bölümde hangisinin "doğru" olduğunu ezberlemeyeceğiz.

Bunun yerine **hangi durumda hangisinin daha anlamlı olduğunu** öğreneceğiz.

---

## Önce Aynı Veriyi İki Farklı Şekilde Saklayalım

Christopher Nolan'ın iki filmi olduğunu düşünelim:

```text
Interstellar
Oppenheimer
```

## Örnek 6

Her iki film de aynı yönetmene bağlıdır.

Bu ilişkiyi MongoDB'de iki temel şekilde modelleyebiliriz.

Birinci yaklaşımda yönetmen bilgisini filmin içine koyabiliriz.

İkinci yaklaşımda yönetmeni ayrı bir Collection'da tutup Movie Document'ından ona referans verebiliriz.

Önce ilk yaklaşımı görelim.

---

### 1. Yaklaşım: Yönetmeni Movie Document'ının İçine Gömmek

Interstellar Document'ı şöyle olabilir:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "voteAverage": 8.7,
  "director": {
    "name": "Christopher Nolan",
    "birthYear": 1970,
    "country": "United Kingdom"
  }
}
```

## Örnek 7

Buradaki `director` artık yalnızca bir String değildir.

Kendi alanları olan iç içe bir Document'tır:

```text
Movie Document
↓
director
↓
name
birthYear
country
```

## Örnek 8

MongoDB'de ilişkili veriyi aynı Document'ın içinde bu şekilde tutmaya **embedding** denir.

Türkçe olarak bunu:

```text
ilişkili veriyi Document'ın içine gömmek
```

## Örnek 9

şeklinde düşünebiliriz.

---

### Embedding'in İlk Avantajını Hemen Görebiliriz

Kullanıcı:

```http
GET /movies/MOVIE_ID
```

## Örnek 10

Request'ini gönderdiğinde Movie Document'ı yönetmen bilgisini zaten kendi içinde taşıyorsa tek sonuç bize şunların hepsini verebilir:

```text
film adı
film yılı
puan
yönetmen adı
yönetmen doğum yılı
yönetmen ülkesi
```

## Örnek 11

Yani kavramsal olarak:

```text
Movie Document'ını getir
↓
film bilgisi + yönetmen bilgisi birlikte gelir
```

## Örnek 12

Bu oldukça kullanışlıdır.

İlişkili veriler çoğu zaman birlikte okunuyorsa embedding güçlü bir seçenek olabilir.

MongoDB'nin esnek Document yapısının önemli avantajlarından biri de budur.

---

### Fakat Aynı Yönetmen Başka Bir Filmde de Varsa Ne Olur?

Şimdi Oppenheimer Document'ını da düşünelim:

```json
{
  "title": "Oppenheimer",
  "releaseYear": 2023,
  "voteAverage": 8.6,
  "director": {
    "name": "Christopher Nolan",
    "birthYear": 1970,
    "country": "United Kingdom"
  }
}
```

## Örnek 13

Artık aynı yönetmen bilgisi iki farklı Movie Document'ında bulunuyor.

```text
Interstellar
↓
Christopher Nolan bilgileri

Oppenheimer
↓
Christopher Nolan bilgileri
```

## Örnek 14

Bu durum veri tekrarı oluşturur.

Ama burada önemli bir noktayı kaçırmayalım:

> MongoDB'de veri tekrarı görmek otomatik olarak "yanlış model" anlamına gelmez.

Bazen veriyi birlikte okumak için bilinçli olarak aynı bilgiyi farklı Document'larda tutmak mantıklı olabilir.

Asıl soru şudur:

> Bu tekrar, uygulamanın kullanım biçimi için kabul edilebilir mi?

---

### Tekrarlanan Verinin Oluşturduğu Ek İşi Somut Görelim

Yönetmenin `country` bilgisini değiştirmemiz gerektiğini düşünelim.

Yönetmen bilgisi her Movie Document'ına gömülmüşse:

```text
Interstellar içindeki director
↓
güncelle

Oppenheimer içindeki director
↓
güncelle

başka filmler varsa
↓
onları da güncelle
```

## Örnek 15

olabilir.

Aynı bilginin birçok yerde bulunması, ortak bilginin değiştiği durumlarda daha fazla güncelleme işi oluşturabilir.

Bu nedenle embedding yalnızca:

```text
tek Query'de gelir
```

## Örnek 16

diye seçilmemelidir.

Verinin nasıl değiştiğini de düşünmemiz gerekir.

---

### 2. Yaklaşım: Yönetmeni Ayrı Bir Document Olarak Saklamak

Şimdi farklı bir model kuralım.

Yönetmenleri ayrı bir Collection'da tuttuğumuzu düşünelim:

```text
directors
```

## Örnek 17

Collection'ındaki Christopher Nolan Document'ı:

```json
{
  "_id": "DIRECTOR_ID",
  "name": "Christopher Nolan",
  "birthYear": 1970,
  "country": "United Kingdom"
}
```

## Örnek 18

olsun.

Movie Document'ında ise bütün yönetmen bilgilerini tekrar etmek yerine yalnızca yönetmenin `_id` değerini tutalım:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "voteAverage": 8.7,
  "director": "DIRECTOR_ID"
}
```

## Örnek 19

Oppenheimer da aynı yönetmeni gösterebilir:

```json
{
  "title": "Oppenheimer",
  "releaseYear": 2023,
  "voteAverage": 8.6,
  "director": "DIRECTOR_ID"
}
```

## Örnek 20

Artık iki filmde yönetmenin bütün bilgilerini tekrar etmiyoruz.

İki Movie Document'ı da aynı Director Document'ına işaret ediyor.

Bu yaklaşıma **referencing** denir.

Bunu şöyle okuyabiliriz:

```text
Movie Document
↓
director ObjectId
↓
Director Document
```

## Örnek 21

---

### Referencing'in Somut Avantajı Nedir?

Farkı daha net görmek için Christopher Nolan örneği üzerinden ilerleyelim.

Christopher Nolan'ın iki filmimizin yönetmeni olduğunu düşünelim:

```text
Interstellar
Oppenheimer
```

## Örnek 22

Eğer **embedding** kullansaydık yönetmen bilgisi her Movie Document'ının içinde ayrı ayrı bulunabilirdi:

```js
// Interstellar
{
  title: "Interstellar",
  director: {
    name: "Christopher Nolan",
  },
}

// Oppenheimer
{
  title: "Oppenheimer",
  director: {
    name: "Christopher Nolan",
  },
}
```

## Örnek 23

Burada dikkat edilmesi gereken nokta şudur:

```text
Christopher Nolan bilgisi
↓
Interstellar içinde bir kez

Christopher Nolan bilgisi
↓
Oppenheimer içinde bir kez
```

## Örnek 24

Yani aynı yönetmen bilgisi birden fazla yerde tekrar eder.

Şimdi Christopher Nolan'a ait bir bilgiyi değiştirmemiz gerektiğini düşünelim.

Embedding kullanıyorsak değişikliğin tutarlı kalması için Christopher Nolan bilgisinin bulunduğu bütün Movie Document'larını güncellememiz gerekebilir:

```text
Interstellar
↓
director bilgisini güncelle

Oppenheimer
↓
director bilgisini güncelle
```

## Örnek 25

Film sayısı arttıkça aynı bilgi daha fazla Document içinde tekrar edebilir.

Referencing kullandığımızda ise yapı farklıdır.

Christopher Nolan için yalnızca bir Director Document'ı bulunur:

```js
{
  _id: "DIRECTOR_ID",
  name: "Christopher Nolan",
}
```

## Örnek 26

Movie Document'ları yönetmen bilgisinin tamamını tekrar saklamak yerine bu Director'ın `_id` değerini tutar:

```js
// Interstellar
{
  title: "Interstellar",
  director: "DIRECTOR_ID",
}

// Oppenheimer
{
  title: "Oppenheimer",
  director: "DIRECTOR_ID",
}
```

## Örnek 27

Artık iki Movie Document'ı da aynı Director Document'ına işaret eder:

```text
              Director
         Christopher Nolan
                ↑
                │
        aynı _id değerine
          referans verir
          ↗           ↖
Interstellar       Oppenheimer
```

## Örnek 28

Christopher Nolan'a ait bir bilgiyi değiştirmemiz gerektiğinde yalnızca Director Document'ını güncelleriz:

```text
Director Document
↓
Christopher Nolan bilgisini güncelle
```

## Örnek 29

Movie Document'larına dokunmamız gerekmez.

Çünkü onlar yönetmen bilgisinin bir kopyasını taşımıyor.

Yalnızca:

```text
Bu filmin yönetmeni hangi Director Document'ı?
```

## Örnek 30

sorusunun cevabını veren `_id` değerini saklıyorlar.

Bu nedenle referencing özellikle ilişkili veri:

```text
kendi başına ayrı bir varlıksa
birden fazla Document tarafından kullanılıyorsa
kendi başına güncellenmesi gerekiyorsa
```

## Örnek 31

daha anlamlı bir seçenek olabilir.

Bizim örneğimizde `Director` bu özelliklere uyuyor.

Christopher Nolan yalnızca Interstellar'ın içinde yaşayan küçük bir bilgi değildir.

Kendi başına bir yönetmendir ve:

```text
Interstellar
Oppenheimer
The Dark Knight
Inception
...
```

## Örnek 32

gibi birçok Movie ile ilişkilendirilebilir.

Bu yüzden Movie API'mizde yönetmeni ayrı bir `Director` Document'ı olarak saklayıp Movie Document'larından ona referans vermek mantıklı bir modelleme kararıdır.

---

### Referencing Ek Veri Getirme İhtiyacı Doğurur

Şu Movie Document'ını tekrar düşünelim:

```json
{
  "title": "Interstellar",
  "director": "DIRECTOR_ID"
}
```

## Örnek 33

Burada artık Movie Document'ının içinde:

```text
Christopher Nolan
1970
United Kingdom
```

## Örnek 34

bilgileri bulunmuyor.

Yalnızca bağlantı var:

```text
DIRECTOR_ID
```

## Örnek 35

Dolayısıyla bir filmi ve yönetmenin ayrıntılarını birlikte göstermek istersek ilişkili Director verisini de almamız gerekir.

Yani embedding'deki:

```text
Movie'yi getir
↓
yönetmen bilgisi zaten içinde
```

## Örnek 36

kolaylığını kaybettik.

Referencing bize veri tekrarını azaltma ve bağımsız veriyi tek yerde tutma avantajı verirken, ilişkili veriyi getirirken ek bir adım ihtiyacı doğurabilir.

Bu ek adımı 56. bölümde Mongoose'un:

```js
populate()
```

## Örnek 37

metoduyla ele alacağız.

Şimdilik yalnızca veri modelini doğru kurmamız gerekiyor.

---

### Embedding ve Referencing'i Aynı Örnek Üzerinden Karşılaştıralım

Embedding:

```json
{
  "title": "Interstellar",
  "director": {
    "name": "Christopher Nolan",
    "birthYear": 1970
  }
}
```

## Örnek 38

Burada:

```text
Movie
↓
yönetmen verisini kendi içinde taşır
```

## Örnek 39

Referencing:

```json
{
  "title": "Interstellar",
  "director": "DIRECTOR_ID"
}
```

## Örnek 40

ve ayrı Director Document'ı:

```json
{
  "_id": "DIRECTOR_ID",
  "name": "Christopher Nolan",
  "birthYear": 1970
}
```

## Örnek 41

Burada:

```text
Movie
↓
yönetmenin kimliğini taşır
↓
yönetmen ayrı Document'tır
```

## Örnek 42

Temel fark budur.

![Embedding ve referencing veri ilişkileri](images/55-embedding-referencing-baski.svg)

---

## Embedding mi Referencing mi? Kararı Nasıl Veririz?

Embedding ve referencing arasında evrensel bir kazanan yoktur.

Daha doğru soru şudur:

> Uygulamamız bu veriyi nasıl okuyacak ve nasıl güncelleyecek?

Örneğin ilişkili veri:

```text
çoğu zaman ana veriyle birlikte okunuyorsa
ana Document olmadan fazla anlam taşımıyorsa
birlikte oluşturulup birlikte değişiyorsa
boyutu kontrollü kalıyorsa
```

## Örnek 43

embedding daha doğal olabilir.

Ama ilişkili veri:

```text
kendi başına anlamlıysa
birçok Document tarafından paylaşılıyorsa
bağımsız güncelleniyorsa
çok büyüyebilecek bir liste oluşturuyorsa
```

## Örnek 44

referencing daha anlamlı olabilir.

Kısa karar tablosu:

| Soru | Embedding'e yaklaştırır | Referencing'e yaklaştırır |
|---|---|---|
| Veriler çoğunlukla birlikte mi okunuyor? | Evet | Hayır |
| İlişkili veri ana Document'tan bağımsız yaşayabiliyor mu? | Genellikle hayır | Evet |
| Aynı veri birçok Document tarafından paylaşılıyor mu? | Az / sınırlı | Evet |
| İlişkili veri sık bağımsız güncelleniyor mu? | Genellikle hayır | Evet |
| İç içe veri zamanla sınırsız büyüyebilir mi? | Uygun olmayabilir | Daha uygun olabilir |

Bu tablo kesin bir algoritma değildir.

Karar verirken uygulamanın gerçek kullanım biçimine bakarız.

---

### İlişkinin Türünü de Görelim

Bizim örneğimizde bir yönetmenin birden fazla filmi olabilir:

```text
Christopher Nolan
├── Interstellar
├── Oppenheimer
└── Inception
```

## Örnek 45

Bunu:

```text
bir yönetmen
↓
birçok film
```

## Örnek 46

şeklinde okuyabiliriz.

Bu tür ilişkiye **one-to-many**, yani bire-çok ilişki denir.

Film tarafında ise her Movie Document'ı şimdilik tek bir yönetmene bağlı olacak.

```text
Movie
↓
bir Director
```

## Örnek 47

Gerçek uygulamalarda:

```text
one-to-one
one-to-many
many-to-many
```

## Örnek 48

ilişkiler görülebilir.

Örneğin oyuncular ve filmler arasında many-to-many ilişki düşünülebilir:

```text
bir oyuncu → birçok film
bir film → birçok oyuncu
```

## Örnek 49

Fakat bu bölümde bütün ilişki türlerini ayrı ayrı modellemeyeceğiz.

Önce tek bir ilişkiyi doğru kuracağız:

```text
Director → Movies
```

## Örnek 50

---

### Movie API İçin Hangi Yaklaşımı Seçelim?

Bizim örneğimizde Director verisini ayrı bir varlık olarak düşünmek mantıklıdır.

Çünkü bir yönetmen:

```text
birden fazla filme sahip olabilir
kendi bilgilerine sahip olabilir
ileride kendi Route'larına sahip olabilir
Movie'den bağımsız güncellenebilir
```

## Örnek 51

Bu nedenle Movie API'mizde yönetmen ilişkisi için **referencing** yaklaşımını seçeceğiz.

Bu karar:

```text
embedding kötüdür
```

## Örnek 52

anlamına gelmez.

Yalnızca bizim seçtiğimiz Director kullanım biçiminde referencing daha uygun bir modeldir.

---

## Movie API'de Referansı Kuralım

Önce Director verisini ayrı bir Model olarak tanımlayacağız.

Yeni dosyamız:

```text
src/db/models/director.js
```

## Örnek 53

olsun.

İlk hâliyle Director Schema'mızı şöyle oluşturabiliriz:

```js
import mongoose from "mongoose";

const directorSchema = new mongoose.Schema(
  {
    name: {
      type: String,
      required: [
        true,
        "Yönetmen adı zorunludur.",
      ],
    },
    birthYear: {
      type: Number,
    },
    country: {
      type: String,
    },
  },
  {
    timestamps: true,
  },
);

export const Director =
  mongoose.models.Director ||
  mongoose.model("Director", directorSchema);
```

## Örnek 54

Burada yeni bir Mongoose fikri yok.

30–31. bölümlerde Movie için yaptığımız temel işlemi şimdi Director için de yapıyoruz:

```text
Schema oluştur
↓
Model oluştur
```

## Örnek 55

Director artık kendi Document'larına sahip olabilecek ayrı bir Model'dır.

---

### Movie Schema'ya Director Referansı Ekleyelim

Şimdi:

```text
src/db/models/movie.js
```

## Örnek 56

üzerindeki Movie Schema'ya yeni bir alan ekleyelim:

```js
director: {
  type: mongoose.Schema.Types.ObjectId,
  ref: "Director",
},
```

## Örnek 57

İlgili bölüm kabaca şöyle görünür:

```js
const movieSchema = new mongoose.Schema(
  {
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
    director: {
      type: mongoose.Schema.Types.ObjectId,
      ref: "Director",
    },
  },
  {
    timestamps: true,
  },
);
```

## Örnek 58

Şimdi bu iki satırı ayrı okuyalım.

---

### `type: mongoose.Schema.Types.ObjectId` Ne Anlatıyor?

25 ve 26. bölümlerde MongoDB'deki `_id` ve `ObjectId` kavramlarını öğrenmiştik.

Burada:

```js
type: mongoose.Schema.Types.ObjectId
```

## Örnek 59

şunu söylüyor:

```text
director alanında
↓
bir ObjectId değeri saklanacak
```

## Örnek 60

Yani Movie Document'ının `director` alanı bütün Director Document'ını içine gömmeyecek.

Yalnızca onun kimliğini taşıyacak.

Örneğin:

```json
{
  "title": "Interstellar",
  "director": "68c4f1a2b3c4d5e6f7890123"
}
```

## Örnek 61

gibi.

---

### `ref: "Director"` Ne Anlatıyor?

İkinci satır:

```js
ref: "Director"
```

## Örnek 62

Mongoose'a bu ObjectId'nin hangi Model ile ilişkili olduğunu söyler.

Kavramsal olarak:

```text
director
↓
ObjectId
↓
Director Model'ındaki Document
```

## Örnek 63

şeklinde düşünebiliriz.

Buradaki:

```text
Director
```

## Örnek 64

adı, 30. bölümde öğrendiğimiz Model oluşturma ifadesindeki Model adıyla eşleşir. Director Model dosyamızda korumalı biçimi kullandığımız için ilgili bölüm şöyledir:

```js
export const Director =
  mongoose.models.Director ||
  mongoose.model("Director", directorSchema);
```

## Örnek 65

Burada `ref: "Director"` ile eşleşen kısım, `mongoose.model()` çağrısının ilk argümanı olan `"Director"` değeridir. `mongoose.models.Director || ...` bölümü ise yalnızca daha önce derlenmiş bir Model varsa onu yeniden kullanmamızı sağlar.

Bu bağlantı özellikle bir sonraki bölümde kullanacağımız `populate()` için önemlidir.

Fakat önemli bir ayrım yapalım:

```js
ref: "Director"
```

## Örnek 66

yazmak Director Document'ını otomatik olarak Movie sonucunun içine getirmez.

Şimdilik Movie içinde hâlâ yalnızca ObjectId bulunur.

---

### Referansı Kurduk; İlişkili Veriyi Sonuca Nasıl Getireceğiz?

Şu ana kadar yalnızca Movie ile Director arasındaki ilişkiyi kurduk. Bir sonraki adım, bu referansı Query sonucunda gerçek Director bilgisine dönüştürmektir.

55. bölümde yaptığımız:

```text
veri modelini kur
↓
Movie hangi Director'a bağlı?
```

## Örnek 67

56. bölümde yapacağımız:

```text
referansı takip et
↓
Director'ın bilgilerini de Query sonucuna getir
```

## Örnek 68

Yani:

```text
referencing
≠
populate
```

## Örnek 69

Referencing veri modelleme kararıdır.

`populate()` ise Mongoose tarafında ilişkili veriyi Query sonucunda kullanabilmemizi sağlayan araçlardan biridir.

---

### Neden `director` Alanını Şimdilik `required` Yapmadık?

Movie Collection'ımızda daha önce oluşturduğumuz Document'lar var.

Onlarda:

```text
director
```

## Örnek 70

alanı bulunmuyor.

Bu bölümde bir anda:

```js
required: true
```

## Örnek 71

kararı verirsek mevcut veriler ve yeni Request akışımız için ayrıca migration ve validation kararları vermemiz gerekir.

Şu anki hedefimiz bu değildir.

Bu bölümün hedefi:

```text
embedding mi?
referencing mi?
```

## Örnek 72

kararını anlamaktır.

Bu nedenle `director` alanını şimdilik isteğe bağlı bırakıyoruz.

İleride API gereksinimi değişirse bu kural ayrıca değerlendirilebilir.

---

### Array Gördüğümüzde Otomatik Olarak Embedding Yapmamalıyız

Bir filmde oyuncular olduğunu düşünelim:

```text
actors
```

## Örnek 73

İlk fikir şöyle olabilir:

```json
{
  "title": "Interstellar",
  "actors": [
    {
      "name": "Matthew McConaughey"
    },
    {
      "name": "Anne Hathaway"
    }
  ]
}
```

## Örnek 74

Bu teknik olarak mümkün olabilir.

Ama yalnızca:

```text
MongoDB array destekliyor
```

## Örnek 75

diye bu tasarımı seçmemeliyiz.

Şunları sormalıyız:

```text
Actor kendi başına bir varlık mı?
Aynı Actor birçok filmde bulunacak mı?
Actor bilgileri bağımsız güncellenecek mi?
Liste zamanla ne kadar büyüyebilir?
```

## Örnek 76

Yani veri modelleme kararı veri tipinden değil, **ilişkinin kullanım biçiminden** doğar.

---

### Çok Büyüyen İç İçe Listelere Dikkat

Embedding güçlüdür ama bir Document'ın içine sınırsız veri koyabileceğimiz anlamına gelmez.

Örneğin bir filmin milyonlarca kullanıcı yorumunu tek Movie Document'ının içindeki:

```js
reviews: []
```

## Örnek 77

array'inde tutmak iyi bir model olmayabilir.

Çünkü bu liste sürekli büyür.

MongoDB Document'larının da boyut sınırı vardır.

Bu nedenle özellikle:

```text
sürekli büyüyen
çok yüksek sayıya ulaşabilen
bağımsız sorgulanması gereken
```

## Örnek 78

ilişkili verilerde referencing seçeneğini ayrıca düşünürüz.

Bu bölümde kesin sayı ezberlemekten çok şu fikri koruyalım:

> İç içe verinin boyutu kontrollü kalmalı ve ana Document'ın doğal bir parçası olmalıdır.

---

### Embedding İçin Daha Doğal Bir Movie Örneği Ne Olabilir?

Director için referencing seçtik.

Peki Movie içinde hiç embedding kullanmayacak mıyız?

Kullanabiliriz.

Örneğin filmin yayın bilgisini şöyle düşünelim:

```json
{
  "title": "Interstellar",
  "releaseInfo": {
    "year": 2014,
    "country": "USA"
  }
}
```

## Örnek 79

Eğer `releaseInfo`:

```text
Movie olmadan ayrı bir varlık değilse
her zaman Movie ile birlikte okunuyorsa
küçük ve kontrollü bir yapıysa
```

## Örnek 80

onu Movie içine gömmek doğal olabilir.

Burada amaç gerçekten `releaseYear` alanımızı hemen değiştirmek değildir.

Sadece embedding'in de doğru kullanılabileceği somut bir örnek görmektir.

---

### ObjectId ile Referans Kurduğumuzda Ne Olur?

Movie Schema'mızda şu alanı oluşturduk:

```js
director: {
  type: mongoose.Schema.Types.ObjectId,
  ref: "Director",
}
```

## Örnek 81

Bu kodu parçalara ayıralım.

Önce:

```js
type: mongoose.Schema.Types.ObjectId
```

## Örnek 82

şunu söyler:

```text
director alanında
↓
bir ObjectId değeri saklanacak
```

## Örnek 83

Bu ObjectId, ilişkili Director Document'ının `_id` değeri olacaktır.

Örneğin Director collection'ında şöyle bir Document bulunduğunu düşünelim:

```js
{
  _id: "DIRECTOR_ID",
  name: "Christopher Nolan",
}
```

## Örnek 84

Movie Document'ımız ise yönetmen bilgisinin tamamını kendi içinde saklamak yerine yalnızca bu `_id` değerini tutabilir:

```js
{
  title: "Interstellar",
  director: "DIRECTOR_ID",
}
```

## Örnek 85

Yani Movie Document'ı bize şunu söyler:

```text
Bu filmin yönetmeni kim?
↓
director alanındaki ObjectId'ye bak
↓
bu ObjectId ilgili Director Document'ını gösterir
```

## Örnek 86

Peki:

```js
ref: "Director"
```

## Örnek 87

ne işe yarar?

Bu ifade Mongoose'a:

```text
director alanındaki ObjectId
↓
Director Model'ı ile ilişkilidir
```

## Örnek 88

bilgisini verir.

Böylece Mongoose bu alanın hangi Model'a referans verdiğini bilir.

Ancak burada önemli bir ayrıntı vardır.

Şunu yazmamız:

```js
ref: "Director"
```

## Örnek 89

verilen ObjectId'nin gerçekten veritabanında bulunan bir Director Document'ına ait olduğunu otomatik olarak garanti etmez.

Örneğin Movie içinde:

```js
{
  director: "VAR_OLMAYAN_BIR_OBJECT_ID",
}
```

## Örnek 90

gibi bir değer bulunması teorik olarak mümkündür.

Yani şu ana kadar yaptığımız şey:

```text
Director Document oluştur
↓
_id değerini al
↓
Movie'nin director alanında sakla
↓
Mongoose'a bu alanın Director Model'ı ile ilişkili olduğunu söyle
```

## Örnek 91

oldu.

Henüz ilişkili Director bilgisini Movie ile birlikte Response'a getirmedik.

Şu anda Movie Document'ında hâlâ yalnızca:

```text
director: ObjectId
```

## Örnek 92

bulunuyor.

Bir sonraki bölümde bu ObjectId üzerinden gerçek Director bilgilerini nasıl getirebileceğimizi göreceğiz.

---

## Movie ve Director Yapımız Artık Nasıl Görünüyor?

Director Document:

```json
{
  "_id": "68c4f1a2b3c4d5e6f7890123",
  "name": "Christopher Nolan",
  "birthYear": 1970,
  "country": "United Kingdom"
}
```

## Örnek 93

Movie Document:

```json
{
  "_id": "68c4f2a2b3c4d5e6f7890456",
  "title": "Interstellar",
  "releaseYear": 2014,
  "voteAverage": 8.7,
  "director": "68c4f1a2b3c4d5e6f7890123"
}
```

## Örnek 94

Bağlantıyı çizelim:

```text
Movie
Interstellar
↓
director
68c4f1a2...
↓
Director
Christopher Nolan
```

## Örnek 95

Bu bölümde kurduğumuz şey budur.

Henüz Director bilgisini Movie Query'sinin içinde otomatik olarak açmadık.

Yalnızca iki Document arasındaki referansı modelledik.

---

## Bölüm Sonu Uygulaması

Bu bölümü yalnızca okuyarak geçmeyin.

### 1. Aynı Yönetmeni İki Şekilde Modelleyin

Önce bir kâğıtta veya geçici bir `.md` dosyasında Interstellar ve Oppenheimer filmlerini embedding ile yazın:

```json
{
  "title": "Interstellar",
  "director": {
    "name": "Christopher Nolan",
    "birthYear": 1970
  }
}
```

## Örnek 96

ve:

```json
{
  "title": "Oppenheimer",
  "director": {
    "name": "Christopher Nolan",
    "birthYear": 1970
  }
}
```

## Örnek 97

Aynı yönetmen bilgisinin tekrarlandığı alanları kendiniz görün.

### 2. Aynı Modeli Referencing ile Yazın

Tek bir Director Document oluşturun:

```json
{
  "_id": "DIRECTOR_ID",
  "name": "Christopher Nolan",
  "birthYear": 1970
}
```

## Örnek 98

Sonra iki Movie Document'ında yalnızca:

```json
{
  "director": "DIRECTOR_ID"
}
```

## Örnek 99

referansını kullanın.

Şu soruyu kendi cümlenizle cevaplayın:

> Christopher Nolan'ın `birthYear` değeri değişseydi iki modelde kaç yerde veri değiştirmem gerekirdi?

### 3. Director Model'ını Projeye Ekleyin

```text
src/db/models/director.js
```

## Örnek 100

dosyasını oluşturup bölümdeki Director Schema ve Model kodunu ekleyin.

### 4. Movie Schema'ya Referansı Ekleyin

Movie Schema'ya:

```js
director: {
  type: mongoose.Schema.Types.ObjectId,
  ref: "Director",
},
```

## Örnek 101

alanını ekleyin.

Bu aşamada `director` alanını `required` yapmayın.

### 5. Karar Verme Alıştırması

Aşağıdaki veriler için önce embedding mi referencing mi seçmeye daha yakın olduğunuzu düşünün:

```text
Movie'nin küçük releaseInfo bilgisi
Director
Actor
binlerce Review
```

## Örnek 102

Tek bir doğru cevap ezberlemek yerine her seçim için şu iki soruyu sorun:

```text
Bu veri ana Document ile birlikte mi yaşar?

Bu veri bağımsız olarak paylaşılır veya büyür mü?
```

## Örnek 103

Amaç koddan önce modelleme kararını verebilmektir.

---

## 🧠 Akılda Kalsın

Embedding:

```text
ilişkili veriyi
↓
aynı Document'ın içine koy
```

## Örnek 104

Örneğin:

```json
{
  "title": "Interstellar",
  "director": {
    "name": "Christopher Nolan"
  }
}
```

## Örnek 105

Referencing:

```text
ilişkili veriyi ayrı Document'ta tut
↓
ObjectId ile bağlantı kur
```

## Örnek 106

Örneğin:

```json
{
  "title": "Interstellar",
  "director": "DIRECTOR_ID"
}
```

## Örnek 107

Embedding özellikle veriler birlikte okunuyor ve birlikte yaşıyorsa güçlü olabilir.

Referencing özellikle ilişkili veri bağımsızsa, birçok Document tarafından paylaşılıyorsa veya ayrı güncelleniyorsa daha anlamlı olabilir.

Movie API'mizde Director için referencing seçtik:

```js
director: {
  type: mongoose.Schema.Types.ObjectId,
  ref: "Director",
},
```

## Örnek 108

Ama:

```text
ref var
↓
Director bilgileri otomatik gelir
```

## Örnek 109

diye düşünmemeliyiz.

Şimdilik Movie Document'ında yalnızca Director'ın ObjectId değeri vardır.

---

## Sonraki Adım

Artık Movie ile Director arasında bir ilişki kurduk:

```text
Movie
↓
director ObjectId
↓
Director
```

## Örnek 110

Fakat şu Query'yi çalıştırırsak:

```js
Movie.findById(movieId);
```

## Örnek 111

`director` alanında hâlâ yalnızca ObjectId görebiliriz.

Kullanıcı ise Response'ta şunu görmek isteyebilir:

```json
{
  "title": "Interstellar",
  "director": {
    "name": "Christopher Nolan",
    "birthYear": 1970
  }
}
```

## Örnek 112

Peki Mongoose'a:

```text
Movie içindeki director referansını takip et
↓
ilgili Director Document'ını bul
↓
sonuçta Director bilgilerini de getir
```

## Örnek 113

nasıl diyeceğiz?

56. bölümde Mongoose'un:

```js
populate()
```
