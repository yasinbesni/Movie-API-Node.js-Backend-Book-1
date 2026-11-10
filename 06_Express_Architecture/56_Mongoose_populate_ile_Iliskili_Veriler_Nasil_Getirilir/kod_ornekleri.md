# Bölüm 56 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Mongoose `populate()` ile İlişkili Veriler Nasıl Getirilir?

## Filmdeki Yönetmen Kimliğinden Adına Ulaşmak

55. bölümde Movie ile Director verisini aynı Document'ın içine gömmek yerine iki ayrı Model olarak tutmaya karar verdik.

Director Model'ımızda örneğin şöyle bir Document bulunabilir:

```json
{
  "_id": "68c4f1a2b3c4d5e6f7890123",
  "name": "Christopher Nolan",
  "birthYear": 1970,
  "country": "United Kingdom"
}
```

## Örnek 2

Movie Document'ımız ise bütün Director bilgisini tekrar saklamak yerine yalnızca Director'ın `_id` değerini tutar:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "voteAverage": 8.7,
  "director": "68c4f1a2b3c4d5e6f7890123"
}
```

## Örnek 3

55. bölümde Movie ile Director arasındaki referansı da kurmuştuk:

```js
director: {
  type: mongoose.Schema.Types.ObjectId,
  ref: "Director",
},
```

## Örnek 4

Orada önemli bir noktada durmuştuk:

```text
Movie Director'a referans veriyor
↓
ama Director bilgileri henüz Movie sonucuna gelmiyor
```

## Örnek 5

Yani ilişkiyi modelledik.

Şimdi bu ilişkiyi Query sonucunda nasıl kullanacağımızı görelim.

---

## Önce `populate()` Kullanmadan Sonuca Bakalım

55. bölümün uygulamasında bir Director Document'ı oluşturup onun `_id` değerini Interstellar Movie Document'ının `director` alanına verdiğimizi düşünelim.

Şimdi tek bir filmi getiren Query'miz şöyle olsun:

```js
const movie = await Movie.findById(movieId).exec();
```

## Örnek 6

Postman'de:

```http
GET http://localhost:3000/movies/MOVIE_ID
```

## Örnek 7

Request'ini gönderdiğimizde Response kabaca şöyle görünebilir:

```json
{
  "message": "Film getirildi.",
  "data": {
    "_id": "MOVIE_ID",
    "title": "Interstellar",
    "releaseYear": 2014,
    "voteAverage": 8.7,
    "director": "68c4f1a2b3c4d5e6f7890123"
  }
}
```

## Örnek 8

Burada ilişki aslında kurulmuştur.

Movie bize şu bilgiyi veriyor:

```text
director
↓
68c4f1a2b3c4d5e6f7890123
```

## Örnek 9

Ama kullanıcı açısından bu değer çok anlamlı değildir.

Kullanıcı büyük ihtimalle şunu görmek ister:

```json
{
  "title": "Interstellar",
  "director": {
    "name": "Christopher Nolan",
    "birthYear": 1970,
    "country": "United Kingdom"
  }
}
```

## Örnek 10

Yani yeni ihtiyacımız şudur:

```text
Movie'yi bul
↓
director alanındaki ObjectId'yi oku
↓
ObjectId'nin işaret ettiği Director Document'ını bul
↓
Movie sonucunda Director bilgilerini de göster
```

## Örnek 11

Mongoose'ta bu işlem için `populate()` kullanabiliriz.

---

## İlk `populate()` Kullanımımızı Yapalım

Mevcut Query'miz:

```js
const movie = await Movie.findById(movieId).exec();
```

## Örnek 12

idi.

Şimdi Query zincirine:

```js
.populate("director")
```

## Örnek 13

ekleyelim:

```js
const movie = await Movie.findById(movieId)
  .populate("director")
  .exec();
```

## Örnek 14

Tek değişiklik budur.

Akışı şöyle okuyabiliriz:

```text
findById(movieId)
↓
Movie Document'ını bul

populate("director")
↓
director referansını takip et
↓
ilgili Director Document'ını getir

exec()
↓
Query'yi çalıştır
```

## Örnek 15

Artık Response'taki `director` alanı yalnızca ObjectId olmak yerine Director bilgilerini içerebilir:

```json
{
  "message": "Film getirildi.",
  "data": {
    "_id": "MOVIE_ID",
    "title": "Interstellar",
    "releaseYear": 2014,
    "voteAverage": 8.7,
    "director": {
      "_id": "68c4f1a2b3c4d5e6f7890123",
      "name": "Christopher Nolan",
      "birthYear": 1970,
      "country": "United Kingdom",
      "createdAt": "...",
      "updatedAt": "...",
      "__v": 0
    }
  }
}
```

## Örnek 16

İşte `populate()`ın temel görevi budur.

```text
önce
↓
director: ObjectId

populate() sonrası
↓
director: Director bilgileri
```

## Örnek 17

---

## `populate("director")` Director Model'ını Nereden Biliyor?

Şu kullanımda:

```js
.populate("director")
```

## Örnek 18

biz ayrıca:

```js
Director
```

## Örnek 19

Model'ını parametre olarak vermedik.

Mongoose bunu Movie Schema'daki şu tanımdan bilir:

```js
director: {
  type: mongoose.Schema.Types.ObjectId,
  ref: "Director",
},
```

## Örnek 20

Özellikle:

```js
ref: "Director"
```

## Örnek 21

şunu söyler:

```text
director alanı populate edilirse
↓
hangi Model'a bakılacak?
↓
Director
```

## Örnek 22

Bunu adım adım düşünelim.

Movie Document'ında:

```text
director = 68c4f1a2b3c4d5e6f7890123
```

## Örnek 23

var.

Schema'da:

```text
ref = Director
```

## Örnek 24

var.

Mongoose bu iki bilgiyi birlikte kullanır:

```text
Director Model'ına bak
↓
_id = 68c4f1a2b3c4d5e6f7890123 olan Document'ı bul
↓
director alanının sonucuna yerleştir
```

## Örnek 25

55. bölümde `ref` tanımlamamızın nedeni şimdi daha görünür hâle geldi.

---

## `populate()` Veritabanındaki Movie Document'ını Değiştirir mi?

Hayır.

Bu ayrım çok önemlidir.

MongoDB'deki Movie Document'ı hâlâ şuna benzer:

```json
{
  "title": "Interstellar",
  "director": "68c4f1a2b3c4d5e6f7890123"
}
```

## Örnek 26

Yani veritabanında:

```text
Movie
↓
Director ObjectId
```

## Örnek 27

yapısı korunur.

`populate()` çalıştığında Mongoose Query sonucunu hazırlarken ilişkili Director verisini getirir.

Bunu şöyle düşünmeyelim:

```text
populate()
↓
Director verisini Movie'nin içine kaydet
```

## Örnek 28

Doğru düşünce:

```text
MongoDB'de Movie
↓
director: ObjectId

Query sırasında populate()
↓
ObjectId'nin işaret ettiği Director'ı getir
↓
Response hazırlanırken Director bilgisini göster
```

## Örnek 29

Dolayısıyla 55. bölümde seçtiğimiz **referencing** modeli değişmedi.

`populate()` yalnızca bu referansı Query sonucunda kullanmamızı kolaylaştırır.

---

## Director'ın Bütün Alanlarını Döndürmek Zorunda mıyız?

İlk örnekte:

```js
.populate("director")
```

## Örnek 30

kullandığımız için Director Document'ındaki birçok alan sonucu gelebilir:

```text
_id
name
birthYear
country
createdAt
updatedAt
__v
```

## Örnek 31

Fakat Movie Response'unda bunların tamamına ihtiyacımız olmayabilir.

52. bölümde projection mantığını öğrenmiştik:

```text
hangi Document?
↓
filter

Document'ın hangi alanları?
↓
projection
```

## Örnek 32

Aynı düşünceyi populate edilen Director için de kullanabiliriz.

Örneğin yalnızca:

```text
name
birthYear
```

## Örnek 33

alanlarını istiyorsak:

```js
const movie = await Movie.findById(movieId)
  .populate({
    path: "director",
    select: {
      name: 1,
      birthYear: 1,
    },
  })
  .exec();
```

## Örnek 34

Buradaki:

```js
path: "director"
```

## Örnek 35

şunu söyler:

```text
hangi referans alanı populate edilecek?
↓
director
```

## Örnek 36

`select` ise:

```js
select: {
  name: 1,
  birthYear: 1,
}
```

## Örnek 37

şunu söyler:

```text
Director'dan hangi alanlar alınacak?
↓
name + birthYear
```

## Örnek 38

Response artık kabaca şöyle olabilir:

```json
{
  "title": "Interstellar",
  "director": {
    "_id": "68c4f1a2b3c4d5e6f7890123",
    "name": "Christopher Nolan",
    "birthYear": 1970
  }
}
```

## Örnek 39

52. bölümde projection içinde `_id` alanını nasıl hariç tutacağımızı zaten öğrenmiştik.

Bu bilgiyi burada yeniden kullanabiliriz:

```js
.populate({
  path: "director",
  select: {
    name: 1,
    birthYear: 1,
    _id: 0,
  },
})
```

## Örnek 40

Buradaki yeni nokta `_id: 0` değildir.

Yeni olan, projection'ın artık populate edilen **Director sonucu** üzerinde uygulanmasıdır.

Bu nedenle iki farklı projection olduğunu fark edin:

```text
Movie için select()
↓
Movie alanlarını belirler

populate içindeki select
↓
Director alanlarını belirler
```

## Örnek 41

Bu ayrım önemlidir.

---

## `getMovieById` Controller'ını Güncelleyelim

Çalıştığımız dosya:

```text
src/controllers/movies.js
```

## Örnek 42

Tek film getiren Controller'ımızdaki ana Query artık şöyle olabilir:

```js
const movieQuery = Movie.findById(movieId)
  .populate({
    path: "director",
    select: {
      name: 1,
      birthYear: 1,
      country: 1,
      _id: 0,
    },
  });

const movie = await movieQuery.exec();
```

## Örnek 43

Controller'ın ilgili bölümü bir bütün olarak:

```js
export const getMovieById = async (
  req,
  res,
) => {
  const { movieId } = req.params;

  if (!mongoose.isObjectIdOrHexString(movieId)) {
    return res.status(400).json({
      message: "Geçersiz film kimliği.",
    });
  }

  const movieQuery = Movie.findById(movieId)
    .populate({
      path: "director",
      select: {
        name: 1,
        birthYear: 1,
        country: 1,
        _id: 0,
      },
    });

  const movie = await movieQuery.exec();

  if (movie === null) {
    return res.status(404).json({
      message: "Film bulunamadı.",
    });
  }

  res.status(200).json({
    message: "Film getirildi.",
    data: movie,
  });
};
```

## Örnek 44

Burada 34. bölümden beri kullandığımız temel Route davranışı değişmedi:

```text
movieId geçerli mi?
↓
Movie'yi bul
↓
bulunamadıysa 404
↓
bulunduysa Response gönder
```

## Örnek 45

Yeni eklenen tek davranış:

```text
Movie bulunduğunda
↓
director referansını da getir
```

## Örnek 46

oldu.

---

## Referans Verilen Director Bulunamazsa Ne Olur?

55. bölümde önemli bir ayrım yapmıştık:

```js
ref: "Director"
```

## Örnek 47

yazmak, Movie içindeki ObjectId'nin gerçekten var olan bir Director Document'ına ait olduğunu otomatik olarak garanti etmez.

Şimdi bunun `populate()` tarafındaki sonucunu görelim.

Movie Document'ında:

```json
{
  "title": "Interstellar",
  "director": "68c4f1a2b3c4d5e6f7890123"
}
```

## Örnek 48

bulunduğunu düşünelim.

Ama Director collection'ında bu `_id` değerine sahip Document artık yoksa:

```js
Movie.findById(movieId)
  .populate("director")
```

## Örnek 49

sonucunda Movie yine bulunabilir.

Fakat tekil `director` referansı için değer:

```json
{
  "title": "Interstellar",
  "director": null
}
```

## Örnek 50

olabilir.

Burada iki ayrı durumu karıştırmayalım:

```text
Movie bulunamadı
↓
movie === null
↓
404 Film bulunamadı
```

## Örnek 51

ve:

```text
Movie bulundu
↓
Movie içinde director ObjectId var
↓
ama o Director Document'ı yok
↓
director: null
```

## Örnek 52

aynı şey değildir.

Bu bağlantı 55. bölümde öğrendiğimiz şu düşünceyi tamamlar:

```text
referans tanımlamak
≠
referansın her zaman geçerli olduğunu garanti etmek
```

## Örnek 53

---

## Liste Query'sinde de `populate()` Kullanabilir miyiz?

Evet.

54. bölümden sonra `GET /movies` Query'miz şu yapıya gelmişti:

```js
const moviesQuery = Movie.find(filter)
  .forMovieList()
  .skip(skip)
  .limit(limit)
  .lean();
```

## Örnek 54

Buraya da populate ekleyebiliriz:

```js
const moviesQuery = Movie.find(filter)
  .forMovieList()
  .populate({
    path: "director",
    select: {
      name: 1,
      _id: 0,
    },
  })
  .skip(skip)
  .limit(limit)
  .lean();
```

## Örnek 55

Bu durumda her Movie sonucunda:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "voteAverage": 8.7,
  "director": {
    "name": "Christopher Nolan"
  }
}
```

## Örnek 56

gibi bir yapı görebiliriz.

Burada 54. bölümde oluşturduğumuz Query Helper'ı hemen değiştirmedik.

`populate()` yeni öğrendiğimiz bir davranış olduğu için şimdilik Query zincirinde açıkça görünür bırakıyoruz:

```text
find(filter)
↓
forMovieList()
↓
populate("director")
↓
skip + limit
↓
lean
↓
exec
```

## Örnek 57

Böylece `director` bilgisinin sonuçlara `populate("director")` adımında eklendiği Query zinciri üzerinde açıkça görülür.

---

## Movie ve Director Alanlarını Ayrı Ayrı Seçelim

54. bölümde `forMovieList()` helper'ımız içinde şu projection vardı:

```js
movieSchema.query.forMovieList = function () {
  return this
    .select({
      title: 1,
      releaseYear: 1,
      voteAverage: 1,
    })
    .sort({
      voteAverage: -1,
      releaseYear: -1,
      _id: 1,
    });
};
```

## Örnek 58

Şimdi liste Response'unda Director ilişkisini de açıkça göstermek istediğimiz için helper'daki Movie projection'ına `director` alanını ekleyelim:

```js
movieSchema.query.forMovieList = function () {
  return this
    .select({
      title: 1,
      releaseYear: 1,
      voteAverage: 1,
      director: 1,
    })
    .sort({
      voteAverage: -1,
      releaseYear: -1,
      _id: 1,
    });
};
```

## Örnek 59

Şimdi iki ayrı katmanı net okuyabiliriz.

Birinci projection:

```js
.select({
  title: 1,
  releaseYear: 1,
  voteAverage: 1,
  director: 1,
})
```

## Örnek 60

Movie'den hangi alanların geleceğini belirler.

İkinci projection:

```js
.populate({
  path: "director",
  select: {
    name: 1,
    _id: 0,
  },
})
```

## Örnek 61

populate edilen Director'dan hangi alanların geleceğini belirler.

Kısaca:

```text
Movie select()
↓
Movie alanları

Director populate select
↓
Director alanları
```

## Örnek 62

Böylece projection mantığını iki farklı seviyede bilinçli kullanmış oluyoruz.

---

## `populate()` ile `lean()` Birlikte Çalışır mı?

Evet.

52. bölümde `lean()` ile liste sonucunda tam Mongoose Document'ları yerine sade JavaScript object'leri almak istediğimizi öğrenmiştik.

Liste Query'mizde `lean()` zaten vardı. Aynı zincire `populate()` eklediğimiz için iki metodu birlikte kullanabiliriz.

En küçük örnekle:

```js
Movie.find(filter)
  .populate("director")
  .lean();
```

## Örnek 63

Bu kullanım geçerlidir.

`lean()` kullanıldığında sade object davranışı populate edilen Director sonucuna da uygulanır.

Yani liste Route'umuzdaki düşünce korunur:

```text
Movie sonuçları
↓
sade object

populate edilen Director sonuçları
↓
sade object
```

## Örnek 64

Bu bizim `GET /movies` gibi yalnızca veri okuyup Response döndüren bir Route için uygundur.

Fakat tek bir Movie'yi daha sonra Document metotlarıyla değiştirmek istiyorsak `lean()` kullanıp kullanmama kararını yine o Query'nin ihtiyacına göre vermeliyiz.

---

## `populate()` Her Query'ye Otomatik Eklenmeli mi?

Hayır.

Şu anda elimizde iki farklı ihtiyaç olabilir.

Bir ekran yalnızca:

```text
film adı
yıl
puan
```

## Örnek 65

istiyorsa Director bilgisini ayrıca getirmek gereksiz olabilir.

Başka bir ekran:

```text
film adı
yönetmen adı
```

## Örnek 66

istiyorsa populate anlamlıdır.

Dolayısıyla:

```text
ref tanımladım
↓
her Query'de populate kullanmalıyım
```

## Örnek 67

şeklinde bir kural yoktur.

`populate()` ilişkili veriye gerçekten ihtiyaç duyduğumuz Query'lerde kullanılmalıdır.

Bunun bir nedeni de şudur:

```text
populate()
↓
ilgili veriyi almak için ek veritabanı çalışması gerekir
```

## Örnek 68

Yani populate kolaylık sağlar ama bedelsiz bir işlem değildir.

Bu aşamada ayrıntılı performans hesabına girmeyeceğiz.

Şimdilik şu soru yeterlidir:

> Bu Response'ta ilişkili Director bilgisine gerçekten ihtiyacım var mı?

Cevap evetse populate kullanmak anlamlı olabilir.

---

## `populate()` ile Ne Yapmadık?

Bu bölümde sınırı bilinçli tutalım.

Şunları yaptık:

```text
ObjectId referansını takip ettik
↓
Director Document'ını getirdik
↓
Director alanlarını projection ile sınırladık
↓
populate + lean birlikte kullandık
```

## Örnek 69

Ama şunları henüz yapmadık:

```text
virtual populate
çok katmanlı populate
populate match
birden fazla ilişkiyi karmaşık şekilde populate etme
```

## Örnek 70

Bir sonraki bölümde zaten **Virtuals** konusuna geçeceğiz.

Bu nedenle burada `populate()`ın temel kullanımını sağlamlaştırmak daha önemlidir.

---

## Bölüm Sonu Uygulaması

Bu bölümü yalnızca okuyarak geçmeyin.

55. bölümün sonunda oluşturduğunuz Director referansını kullanarak aşağıdaki adımları uygulayın.

### 1. Önce `populate()` Olmadan Deneyin

Tek film Query'sini:

```js
const movie = await Movie.findById(movieId).exec();
```

## Örnek 71

şeklinde çalıştırın.

Postman'de Director alanının yalnızca ObjectId olduğunu görün:

```json
{
  "director": "DIRECTOR_ID"
}
```

## Örnek 72

### 2. `populate("director")` Ekleyin

Query'yi:

```js
const movie = await Movie.findById(movieId)
  .populate("director")
  .exec();
```

## Örnek 73

hâline getirin.

Aynı Request'i tekrar gönderin.

Bu kez `director` alanında Director Document'ının geldiğini doğrulayın.

### 3. Director Alanlarını Sadeleştirin

Populate kodunu:

```js
.populate({
  path: "director",
  select: {
    name: 1,
    birthYear: 1,
    _id: 0,
  },
})
```

## Örnek 74

hâline getirin.

Response'ta Director için yalnızca:

```text
name
birthYear
```

## Örnek 75

alanlarının kaldığını görün.

### 4. Liste Query'sinde Kullanın

`forMovieList()` helper'ındaki Movie projection'ına:

```js
director: 1,
```

## Örnek 76

ekleyin.

Ardından `GET /movies` Query'sine:

```js
.populate({
  path: "director",
  select: {
    name: 1,
    _id: 0,
  },
})
```

## Örnek 77

ekleyin.

Pagination, filtering ve searching davranışlarının bozulmadığını kontrol edin.

### 5. Eksik Referans Durumunu Düşünün

Bir Movie'nin `director` alanındaki ObjectId artık hiçbir Director Document'ına karşılık gelmezse populate sonucunda ne beklediğinizi önce tahmin edin.

Sonra şu cümleyi kendi kelimelerinizle açıklayın:

```text
Movie'nin bulunması
ile
referans verilen Director'ın bulunması
aynı şey değildir.
```

## Örnek 78

---

## 🧠 Akılda Kalsın

55. bölümde Movie içinde yalnızca Director ObjectId'sini saklamıştık:

```js
director: {
  type: mongoose.Schema.Types.ObjectId,
  ref: "Director",
},
```

## Örnek 79

`populate()`:

```text
referans alanındaki ObjectId'yi kullanır
↓
ilgili Document'ı getirir
↓
Query sonucunda referans alanının yerine ilişkili veriyi koyar
```

## Örnek 80

Temel kullanım:

```js
Movie.findById(movieId)
  .populate("director")
  .exec();
```

## Örnek 81

Alan seçimiyle:

```js
.populate({
  path: "director",
  select: {
    name: 1,
    birthYear: 1,
    _id: 0,
  },
})
```

## Örnek 82

`populate()` veritabanındaki Movie Document'ını değiştirmez.

MongoDB'de hâlâ:

```text
director: ObjectId
```

## Örnek 83

saklanır.

`ref: "Director"` hangi Model'ın kullanılacağını söyler; hedef Director Document'ının gerçekten var olduğunu garanti etmez.

Tekil bir referansın karşılığı bulunamazsa populate edilen alan `null` olabilir.

`populate()` ve `lean()` birlikte kullanılabilir.

Ve en önemlisi:

```text
referans var
↓
her Query otomatik populate edilir
```

## Örnek 84

diye bir kural yoktur.

Populate, ilişkili veriye gerçekten ihtiyaç duyduğumuz Query'de bilinçli olarak kullanılır.

---

## Sonraki Adım

Artık Movie Response'unda yalnızca saklanan alanları değil, ilişkili Director bilgisini de gösterebiliyoruz.

Şimdi Director verisi üzerinden başka bir ihtiyaç düşünelim.

Director Document'ımızda şu bilgi saklanıyor olsun:

```json
{
  "name": "Christopher Nolan",
  "birthYear": 1970
}
```

## Örnek 85

`birthYear` değişmeyen bir bilgidir.

Fakat API'de yönetmenin yaşını da göstermek isteyebiliriz. Basitçe yıl üzerinden düşünürsek 2026'da:

```text
2026 - 1970
↓
56
```

## Örnek 86

gibi bir değer hesaplanabilir.

Bu `56` değerini doğrudan veritabanına kaydettiğimizi düşünelim.

Bir yıl sonra:

```text
2027 - 1970
↓
57
```

## Örnek 87

olacaktır.

Ama veritabanındaki:

```text
age: 56
```

## Örnek 88

değeri kendiliğinden değişmez. Onu ayrıca güncellememiz gerekir.

Burada iki farklı tür bilgi görüyoruz:

```text
birthYear
↓
veritabanında saklanması anlamlı olan kaynak bilgi

age
↓
birthYear ve mevcut yıldan hesaplanabilecek türetilmiş bilgi
```
