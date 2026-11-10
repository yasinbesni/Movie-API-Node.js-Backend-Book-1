# Bölüm 62 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Service Katmanı Neden Kullanılır ve İş Mantığı Controller'dan Nasıl Ayrılır?

## Controller Artık Yalnızca HTTP ile İlgilenmiyor

61. bölümde Response yapısını düzenledik. Böylece Controller'ın HTTP tarafındaki görevi daha görünür hâle geldi:

```text
Request'ten gerekli bilgiyi al
↓
uygun HTTP status'u seç
↓
Response gönder
```

## Örnek 2

Fakat `getMovies` Controller'ına baktığımızda bununla yetinmediğini görüyoruz.

Controller aynı zamanda:

```text
page ve limit değerlerini hazırlıyor
↓
filter oluşturuyor
↓
toplam kayıt sayısını buluyor
↓
Movie listesini getiriyor
↓
populate() uyguluyor
↓
skip() ve limit() uyguluyor
↓
pagination bilgisini hesaplıyor
↓
Response gönderiyor
```

## Örnek 3

Yani tek fonksiyon içinde iki farklı türde iş yan yana duruyor:

```text
HTTP ile ilgili kararlar
+
Movie verisini hazırlayan uygulama akışı
```

## Örnek 4

Bu kod yanlış değildir. Sorun, Controller büyüdükçe hangi satırın HTTP ile, hangi satırın veriyi hazırlamakla ilgili olduğunu takip etmenin zorlaşmasıdır.

Önce bu farkı gerçek kod üzerinde görelim.

---

## `getMovies` İçinde İki Farklı Sorumluluk Var

Controller'ın başında şuna benzer kodlar bulunuyor:

```js
const {
  page,
  limit,
} = parsePaginationParams(req.query);

const {
  filter,
  error,
} = buildMovieFilter(req.query);
```

## Örnek 5

Burada Controller, HTTP Request içindeki `req.query` bilgisini uygulamanın kullanabileceği değerlere dönüştürüyor.

Sonra şu tür işlemler geliyor:

```js
const skip = (page - 1) * limit;

const countQuery = Movie.countDocuments(filter);

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

## Örnek 6

Bu kısım artık HTTP ile değil, **Movie listesinin nasıl hazırlanacağıyla** ilgileniyor.

Sonunda tekrar HTTP tarafına dönüyoruz:

```js
res.status(200).json({
  message: "Filmler getirildi.",
  data: movies,
  meta: {
    page,
    limit,
    totalItems,
    totalPages,
    hasNext,
    hasPrevious,
  },
});
```

## Örnek 7

Akış aslında şöyle:

```text
HTTP Request'i yorumla
↓
Movie listesini hazırla
↓
HTTP Response gönder
```

## Örnek 8

Ortadaki bölüm büyüdükçe Controller'ın ana görevi görünmez hâle geliyor.

Tam bu noktada Service katmanı anlamlı hâle gelir.

---

## Service Katmanı Neyi Üstlenecek?

Service'i yeni ve soyut bir kavram gibi düşünmeyelim.

Bu bölümde Service'in görevi çok somut:

> Controller'dan çıkan ve HTTP'ye ait olmayan uygulama akışını ayrı bir fonksiyonda toplamak.

Bizim örneğimizde bu akış:

```text
aynı filtreyle toplam kayıt sayısını bul
↓
Movie listesini getir
↓
populate uygula
↓
pagination uygula
↓
pagination bilgisini hesapla
↓
sonucu Controller'a döndür
```

## Örnek 9

olacak.

Controller ise şunlarla ilgilenmeye devam edecek:

```text
req.query
req.params
req.body
HTTP status kodları
Response body
```

## Örnek 10

Bu ayrımı akılda tutmak için:

```text
Controller
↓
HTTP ile konuşur

Service
↓
uygulamanın işlemini yürütür
```

## Örnek 11

şeklinde düşünebiliriz.

---

## İlk Service'i Gerçek İhtiyacın Olduğu Yerden Çıkaralım

Yeni klasörümüz:

```text
src/services
```

## Örnek 12

olsun.

Movie işlemleri için:

```text
src/services/movies.js
```

## Örnek 13

oluşturalım.

İlk olarak `getMovies` içindeki veri hazırlama kısmını buraya taşıyacağız.

```js
import { Movie } from "../db/models/movie.js";

import {
  calculatePaginationData,
} from "../utils/calculatePaginationData.js";

export const getMoviesService = async ({
  filter,
  page,
  limit,
}) => {
  const skip = (page - 1) * limit;

  const countQuery =
    Movie.countDocuments(filter);

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

  const totalItems =
    await countQuery.exec();

  const movies =
    await moviesQuery.exec();

  const {
    totalPages,
    hasNext,
    hasPrevious,
  } = calculatePaginationData(
    totalItems,
    limit,
    page,
  );

  return {
    movies,
    totalItems,
    totalPages,
    hasNext,
    hasPrevious,
  };
};
```

## Örnek 14

Bu fonksiyonun yaptığı şey tek cümlede şöyle özetlenebilir:

> Verilen filtre ve sayfalama bilgilerine göre Movie listesini ve pagination sonucunu hazırla.

Burada dikkat edin: fonksiyon `req` veya `res` bilmiyor.

Sadece ihtiyacı olan verileri alıyor:

```js
{
  filter,
  page,
  limit,
}
```

## Örnek 15

ve bir sonuç döndürüyor.

---

## Controller Şimdi Daha Kolay Okunuyor

Controller tarafında Service'i import edelim:

```js
import {
  getMoviesService,
} from "../services/movies.js";
```

## Örnek 16

Sonra `getMovies` şu hâle gelir:

```js
export const getMovies = async (
  req,
  res,
) => {
  const {
    page,
    limit,
  } = parsePaginationParams(req.query);

  const {
    filter,
    error,
  } = buildMovieFilter(req.query);

  if (error !== null) {
    return res.status(400).json({
      message: error,
    });
  }

  const result = await getMoviesService({
    filter,
    page,
    limit,
  });

  res.status(200).json({
    message: "Filmler getirildi.",
    data: result.movies,
    meta: {
      page,
      limit,
      totalItems: result.totalItems,
      totalPages: result.totalPages,
      hasNext: result.hasNext,
      hasPrevious: result.hasPrevious,
    },
  });
};
```

## Örnek 17

Artık Controller'ı yukarıdan aşağı okuduğumuzda akış daha nettir:

```text
Request'ten page, limit ve filter hazırla
↓
hatalı Request varsa 400 dön
↓
Service'i çağır
↓
Service sonucunu HTTP Response'a dönüştür
```

## Örnek 18

Mongoose Query ayrıntıları artık Controller'ın içinde görünmüyor.

Bu, Service katmanının ilk somut kazancıdır.

---

## Neden Service'e Doğrudan `req.query` Vermiyoruz?

Şöyle yazmak teknik olarak mümkündür:

```js
getMoviesService(req.query);
```

## Örnek 19

Ama o zaman Service, Express Request yapısını tanımaya başlar.

Örneğin Service şu ayrıntıları bilmek zorunda kalabilir:

```text
req.query.page nerede?
req.query.limit nasıl geliyor?
query string değerleri string mi?
hangi alan HTTP'den geliyor?
```

## Örnek 20

Biz bunun yerine Controller'da HTTP verisini önce sadeleştiriyoruz:

```text
req.query
↓
Controller
↓
page + limit + filter
↓
Service
```

## Örnek 21

Service böylece yalnızca uygulamanın kullanacağı normal JavaScript değerleriyle çalışıyor.

Örneğin:

```js
getMoviesService({
  filter,
  page,
  limit,
});
```

## Örnek 22

Bu ayrımın avantajı şudur:

```text
Controller
↓
Express'e bağlı

Service
↓
Express'ten mümkün olduğunca bağımsız
```

## Örnek 23

---

## Service Sonucu HTTP Status Değildir

Tek bir Movie getiren örnekte ayrım daha da net görünür.

Service:

```js
export const getMovieByIdService = async (
  movieId,
) => {
  return Movie.findById(movieId)
    .populate({
      path: "director",
      select: {
        name: 1,
        birthYear: 1,
        country: 1,
        _id: 0,
      },
    })
    .exec();
};
```

## Örnek 24

Bu fonksiyon Movie varsa Document'ı, yoksa:

```js
null
```

## Örnek 25

döndürür.

Service'in görevi burada biter.

Controller ise bu sonucu HTTP açısından yorumlar:

```js
const movie = await getMovieByIdService(
  movieId,
);

if (movie === null) {
  return res.status(404).json({
    message: "Film bulunamadı.",
  });
}

res.status(200).json({
  message: "Film getirildi.",
  data: movie,
});
```

## Örnek 26

Yani:

```text
Service
↓
Movie bulundu mu?

Controller
↓
bu sonucun HTTP karşılığı nedir?
```

## Örnek 27

Service içine:

```js
res.status(404).json(...);
```

## Örnek 28

koysaydık Service'i tekrar Express'e bağlamış olurduk.

---

## Her Mongoose Çağrısını Service'e Taşımak Zorunda mıyız?

Hayır.

Örneğin şu Controller:

```js
export const createMovie = async (
  req,
  res,
) => {
  const movie = await Movie.create(req.body);

  res.status(201).json({
    message: "Film oluşturuldu.",
    data: movie,
  });
};
```

## Örnek 29

şu anda oldukça kısa ve anlaşılırdır.

Sadece:

```js
Movie.create(req.body);
```

## Örnek 30

satırını başka bir dosyaya taşıyıp:

```js
createMovieService(req.body);
```

## Örnek 31

yazmak tek başına büyük bir kazanç sağlamaz.

Service ihtiyacı genellikle işlem büyüdüğünde daha belirgin hâle gelir.

Örneğin Movie oluşturma işlemi ileride:

```text
ilgili veriyi kontrol et
↓
Movie oluştur
↓
başka bir kaydı güncelle
↓
sonucu hazırla
```

## Örnek 32

şeklinde bir akışa dönüşürse bu adımları Service'te toplamak anlamlı olur.

İstersek proje genelinde tutarlı bir katman yapısı için `createMovieService()` ekleyebiliriz. Ancak bu durumda Service'in görevi açık olmalıdır:

> Service katmanını yalnızca projede ayrı bir katman bulunsun diye kullanmıyoruz. İşlem birden fazla adıma dönüştüğünde, bu akışı Controller'ın HTTP sorumluluklarından ayırmak için kullanıyoruz.

---

## Model ile Service Arasındaki Fark Nedir?

Bu ayrım ilk bakışta karışabilir çünkü Service de Model'i kullanır.

Örneğin:

```js
Movie.findById(movieId);
```

## Örnek 33

bir Mongoose Model Query'sidir.

Model'in görevi verinin yapısı ve MongoDB ile etkileşimdir.

Örneğin:

```text
Movie Schema
validation kuralları
instance / static method'lar
MongoDB Query'leri
```

## Örnek 34

Model tarafına aittir.

Service ise bir kullanım senaryosunu yürütür.

Örneğin `getMoviesService()`:

```text
filtreyle toplam kayıt sayısını bul
↓
aynı filtreyle Movie listesini getir
↓
populate uygula
↓
pagination bilgisini hesapla
↓
hepsini tek sonuçta birleştir
```

## Örnek 35

Bu nedenle:

```text
Model
↓
veriyle nasıl çalışılır?

Service
↓
bu işlem için hangi adımlar birlikte çalıştırılır?
```

## Örnek 36

şeklinde ayırabiliriz.

---

## Utility ile Service de Aynı Rolü Taşımaz

Daha önce kullandığımız:

```js
calculatePaginationData()
```

## Örnek 37

küçük ve belirli bir işi yapar:

```text
totalItems + limit + page
↓
pagination metadata
```

## Örnek 38

Bu bir Utility örneğidir.

`getMoviesService()` ise birden fazla adımı koordine eder:

```text
Query oluştur
↓
Query'leri çalıştır
↓
Utility kullan
↓
sonucu bir araya getir
```

## Örnek 39

Bu nedenle:

```text
Utility
↓
küçük, tekrar kullanılabilir yardımcı işlem

Service
↓
bir kullanım senaryosunun akışını koordine eden fonksiyon
```

## Örnek 40

olarak düşünebiliriz.

---

## Service Eklenince Hata Akışı Değişiyor mu?

Hayır.

39. bölümde kurduğumuz merkezi hata yönetimi çalışmaya devam eder.

Örneğin Service içindeki şu Query hata verirse:

```js
await Movie.find(filter).exec();
```

## Örnek 41

akış şöyle olur:

```text
Mongoose Query reject olur
↓
Service Promise'i reject olur
↓
Controller'daki await aynı hatayı alır
↓
async Controller Promise'i reject olur
↓
Express 5 hatayı errorHandler'a aktarır
```

## Örnek 42

Yani Service katmanı hata yönetim zincirini kesmez.

Beklenen durumlar ise yine normal sonuç olarak dönebilir.

Örneğin:

```text
Service → null
↓
Controller → 404
```

## Örnek 43

Beklenmeyen hata ise:

```text
Service → throw / rejected Promise
↓
errorHandler → 500
```

## Örnek 44

akışını izler.

---

## Proje Yapısı Nasıl Değişti?

Artık ilgili klasör yapısı şöyle olabilir:

```text
src
├── constants
├── controllers
│   └── movies.js
├── db
│   └── models
├── middlewares
├── routers
├── services
│   └── movies.js
├── utils
├── validation
├── index.js
└── server.js
```

## Örnek 45

Yeni `services` klasörü:

```text
HTTP'den bağımsız uygulama iş akışlarını
```

## Örnek 46

toplamak için kullanılıyor.

---

## Bir Request Artık Hangi Katmanlardan Geçiyor?

`GET /movies` için akışı tek parça hâlinde görelim:

```text
Request
↓
Router
↓
Middleware / Validation
↓
Controller
↓
Service
↓
Movie Model
↓
MongoDB
↓
Service sonucu
↓
Controller
↓
HTTP Response
```

## Örnek 47

Her katmanın sorusu farklıdır:

```text
Router
↓
hangi URL hangi handler'a gidecek?

Middleware
↓
Request devam etmeden önce hangi ortak kontrol yapılacak?

Controller
↓
HTTP girdisi nasıl okunacak ve HTTP sonucu nasıl gönderilecek?

Service
↓
bu kullanım senaryosunun adımları nasıl yürütülecek?

Model
↓
veri MongoDB'de nasıl temsil edilecek ve sorgulanacak?
```

## Örnek 48

Bu ayrım sayesinde kodu okurken her dosyada ne aramamız gerektiği daha tahmin edilebilir hâle gelir.

![Service katmanı üzerinden istek akışı](images/62-service-katmani-baski.svg)

---

## Bölüm Sonu Uygulaması

1. `src/services/movies.js` dosyasını oluşturun.
2. `getMoviesService({ filter, page, limit })` fonksiyonunu yazın.
3. `countDocuments()`, `find()`, `populate()`, `skip()`, `limit()` ve pagination hesaplamasını Controller'dan Service'e taşıyın.
4. `getMovies` Controller'ında yalnızca Request'i hazırlama, hata durumunda HTTP kararı verme, Service'i çağırma ve Response gönderme adımlarının kaldığını kontrol edin.
5. `getMovieByIdService(movieId)` ekleyin.
6. Service'in `null` döndürmesi ile Controller'ın `404` göndermesi arasındaki farkı kendi cümlenizle açıklayın.
7. Service dosyasında `req`, `res` veya `res.status()` kullanılmadığını doğrulayın.
8. `createMovie` gibi tek satırlık bir veri işleminde Service eklemenin zorunlu olmadığını, hangi durumda anlamlı hâle geleceğini açıklayın.

---

## 🧠 Akılda Kalsın

Service katmanı Controller'ı ortadan kaldırmaz.

Controller'ın sorumluluğunu daraltır.

```text
Controller
↓
HTTP

Service
↓
uygulama iş akışı

Model
↓
MongoDB ve Mongoose
```

## Örnek 49

Service'e:

```text
req
res
HTTP status code
```

## Örnek 50

taşımamaya çalışırız.

Controller'da ise büyüyen Mongoose Query ve uygulama akışı ayrıntılarını tutmamaya çalışırız.

En önemli fikir şudur:

> Service katmanı, her Model çağrısını başka dosyaya taşımak için değil; bir kullanım senaryosunda büyüyen uygulama akışını HTTP kodundan ayırmak için kullanılır.

---

## Sonraki Adım

Controller ile uygulama iş akışını ayırdık.

Şimdi uygulamanın çalışma ayarlarına baktığımızda başka bir dağınıklık görüyoruz.

MongoDB bağlantı adresini:

```js
process.env.MONGODB_URL
```

## Örnek 51

üzerinden alıyoruz.

Server portu ise hâlâ:

```js
app.listen(3000);
```
