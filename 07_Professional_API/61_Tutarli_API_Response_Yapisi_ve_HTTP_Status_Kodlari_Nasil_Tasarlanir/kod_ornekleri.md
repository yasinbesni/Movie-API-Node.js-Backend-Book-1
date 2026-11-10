# Bölüm 61 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Tutarlı API Response Yapısı ve HTTP Status Kodları Nasıl Tasarlanır?

## Response Çalışıyor, Peki Kullanması Kolay mı?

Bu aşamadaki HTTP yanıtı ve sorgu örneklerini mevcut `src/controllers/movies.js` dosyasındaki Movie Controller üzerinden takip edeceğiz. Yeni dosya oluşturmadıkça aynı dosyanın ilgili fonksiyonunu güncelliyoruz.

60. bölümün sonunda üç farklı Response yapısını yan yana görmüştük.

Hepsi çalışıyordu.

Fakat şimdi yalnızca film listesine bakalım.

Mevcut Response'umuz kabaca şöyle:

```json
{
  "message": "Filmler getirildi.",
  "page": 1,
  "limit": 10,
  "totalItems": 42,
  "totalPages": 5,
  "hasNext": true,
  "hasPrevious": false,
  "data": [
    {
      "title": "Interstellar"
    }
  ]
}
```

## Örnek 2

Bu Response yanlış değildir.

Ama API'yi kullanan bir frontend açısından iki farklı tür bilgi aynı seviyede duruyor:

```text
data
↓
asıl veri?
↓
Movie listesi

page / limit / totalItems / ...
↓
ne hakkında bilgi?
↓
listenin pagination bilgileri
```

## Örnek 3

Örneğin frontend kodu filmi:

```js
body.data;
```

## Örnek 4

ile, sayfa bilgisini ise:

```js
body.page;
```

## Örnek 5

ile okumak zorunda.

Buradaki problem JavaScript'in bunu yapamaması değildir.

Problem, **asıl veri ile o veriyi açıklayan ek bilgilerin aynı seviyede karışmasıdır.**

Bunu daha düzenli düşünürsek:

```text
Movie listesi
↓
data

Movie listesinin pagination bilgileri
↓
meta
```

## Örnek 6

şeklinde ayırabiliriz.

O zaman Response'u kullanan kişi şunu tahmin etmeye başlar:

```text
Asıl veri nerede?
↓
data

Listeyle ilgili ek bilgi nerede?
↓
meta
```

## Örnek 7

Bu bölümde kuracağımız **Response sözleşmesinin** amacı tam olarak budur:

> API'yi kullanan kişi, bir Response'u ilk kez gördüğünde hangi tür bilginin nerede olacağını tahmin edebilsin.

---

## Önce Basit Bir Response Sözleşmesi Seçelim

Movie API'de başarılı HTTP yanıtları için şu temel yapıyı kullanacağız:

```json
{
  "message": "...",
  "data": {}
}
```

## Örnek 8

Liste Response'unda ek bilgiler gerekiyorsa:

```json
{
  "message": "...",
  "data": [],
  "meta": {}
}
```

## Örnek 9

Hata Response'unda ise:

```json
{
  "message": "..."
}
```

## Örnek 10

ve hata ayrıntıları varsa:

```json
{
  "message": "...",
  "errors": []
}
```

## Örnek 11

kullanacağız.

Buradaki fikir:

```text
message
↓
Response'un kısa açıklaması

data
↓
başarılı işlemin asıl verisi

meta
↓
asıl verinin ek bilgileri

errors
↓
hata ayrıntıları gerekiyorsa
```

## Örnek 12

şeklindedir.

> Bu yapı HTTP'nin zorunlu kıldığı tek doğru API Response formatı değildir. Biz Movie API için anlaşılır ve tutarlı bir sözleşme seçiyoruz.

---

## Response Sözleşmesini İşleme Göre Esnek Tutalım

Örneğin 404 Response'unda:

```json
{
  "message": "Film bulunamadı."
}
```

## Örnek 13

yeterlidir.

Şöyle yapmak zorunda değiliz:

```json
{
  "message": "Film bulunamadı.",
  "data": null,
  "meta": null,
  "errors": null
}
```

## Örnek 14

Bu ikinci yapı daha fazla alan içeriyor olabilir.

Ama sırf bütün key'ler görünsün diye gereksiz `null` değerleri üretmek zorunda değiliz.

Bizim sözleşmemiz şu:

```text
başarılı işlem
↓
message + data

liste ve ek bilgi
↓
message + data + meta

hata
↓
message

hata ayrıntıları varsa
↓
message + errors
```

## Örnek 15

Bu kadar.

---

## Film Listesini Daha Okunabilir Hâle Getirelim

Şu an Response'umuz:

```js
res.status(200).json({
  message: "Filmler getirildi.",
  page,
  limit,
  totalItems,
  totalPages,
  hasNext,
  hasPrevious,
  data: movies,
});
```

## Örnek 16

şeklinde olabilir.

Pagination bilgilerini `meta` altında toplayalım:

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

## Örnek 17

Postman'de artık yapı daha kolay okunur:

```json
{
  "message": "Filmler getirildi.",
  "data": [
    {
      "title": "Interstellar"
    }
  ],
  "meta": {
    "page": 1,
    "limit": 10,
    "totalItems": 42,
    "totalPages": 5,
    "hasNext": true,
    "hasPrevious": false
  }
}
```

## Örnek 18

Şimdi iki farklı sorumluluk görünür biçimde ayrıldı:

```text
data
↓
filmler

meta
↓
filmlerin nasıl sayfalandırıldığı
```

## Örnek 19

---

## Bir Response İki Bilgi Taşır: HTTP Status Kodu ve JSON Body

Aynı Response içinde bu iki bilgi birlikte gönderilir.

Şu kod:

```js
res.status(200).json({
  message: "Film getirildi.",
  data: movie,
});
```

## Örnek 20

iki ayrı bilgi gönderir.

Birincisi:

```text
HTTP status
↓
200
```

## Örnek 21

İkincisi:

```text
Response body
↓
JSON
```

## Örnek 22

Bu yüzden JSON içine ayrıca:

```json
{
  "status": 200
}
```

## Örnek 23

eklemek zorunda değiliz.

Frontend zaten HTTP Response üzerinden status kodunu okuyabilir:

```js
response.status;
```

## Örnek 24

Bizim JSON Body'miz ise uygulamaya ait veriyi taşır.

---

## Movie API'de En Sık Kullandığımız Status Kodlarını Yerine Oturtalım

Bu bölümde bütün HTTP status kodlarını ezberlemeye çalışmayacağız.

Movie API'de gerçekten kullandığımız kodlara bakalım.

### `200 OK`

Request başarıyla işlendi ve Response Body döndürüyoruz.

Örneğin:

```http
GET /movies
```

## Örnek 25

ve:

```http
GET /movies/:movieId
```

## Örnek 26

başarılı olduğunda:

```js
res.status(200).json(...);
```

## Örnek 27

kullanabiliriz.

PATCH sonrasında güncellenmiş Movie'yi döndürüyorsak yine:

```text
200 OK
```

## Örnek 28

uygundur.

### `201 Created`

Yeni bir kaynak oluşturulduğunda:

```http
POST /movies
```

## Örnek 29

başarılı Response'u:

```js
res.status(201).json({
  message: "Film oluşturuldu.",
  data: movie,
});
```

## Örnek 30

olabilir.

Buradaki `201`, yalnızca “işlem başarılı” demez.

Daha özel olarak:

```text
yeni bir kaynak oluşturuldu
```

## Örnek 31

anlamını taşır.

### `400 Bad Request`

Client'ın gönderdiği Request'i bizim API sözleşmemize göre işleyemediğimiz durumlarda kullanıyoruz.

Örneğin Joi validation:

```json
{
  "releaseYear": "2014"
}
```

## Örnek 32

veya geçersiz ObjectId biçimi.

### `404 Not Found`

Request biçimi geçerli olabilir ama aradığımız kaynak yoktur.

Örneğin ObjectId biçimi geçerli:

```text
66f123456789abcdef123456
```

## Örnek 33

ama bu `_id` ile Movie bulunamıyor.

Bu durumda:

```text
404 Not Found
```

## Örnek 34

anlamlıdır.

### `500 Internal Server Error`

Beklemediğimiz sunucu hatalarında merkezi `errorHandler` kullanıyorduk.

Bu hata:

```text
client yanlış bir şey gönderdi
```

## Örnek 35

demek değildir.

Daha çok:

```text
sunucu Request'i işlerken beklenmeyen bir problem yaşadı
```

## Örnek 36

anlamına gelir.

---

## `400` ile `404` Arasındaki Farkı Somutlaştıralım

Şu Request'i düşünelim:

```http
GET /movies/abc
```

## Örnek 37

`abc` geçerli MongoDB ObjectId biçiminde değilse:

```text
Request parametresi geçersiz
↓
400
```

## Örnek 38

kararı verebiliriz.

Ama:

```http
GET /movies/66f123456789abcdef123456
```

## Örnek 39

biçim olarak geçerli bir ObjectId içeriyor olabilir.

Query çalışır ve:

```js
movie === null
```

## Örnek 40

sonucu dönerse:

```text
Request anlaşılır
↓
ama kaynak yok
↓
404
```

## Örnek 41

olur.

Bu ayrım frontend için de değerlidir.

Çünkü:

```text
400
↓
Request'i düzelt

404
↓
aranan kaynak yok
```

## Örnek 42

şeklinde farklı kararlar alınabilir.

---

## DELETE İşleminde `200` mü, `204` mü?

Burada iki geçerli tasarım görebiliriz.

Silinen Movie bilgisini veya bir mesajı döndürmek istiyorsak:

```js
res.status(200).json({
  message: "Film silindi.",
  data: deletedMovie,
});
```

## Örnek 43

uygundur.

Ama başarılı silme işleminden sonra hiçbir Response Body göndermeyeceksek:

```text
204 No Content
```

## Örnek 44

kullanılabilir.

Movie API'mizde şu anda:

```text
silinen Movie'yi ve mesajı göstermek
```

## Örnek 45

istediğimiz için `200` kullanmaya devam edeceğiz.

Yani status kodu seçimi:

```text
hangi HTTP methodunu kullandım?
```

## Örnek 46

sorusundan tek başına çıkmaz.

Response'un ne anlattığı da önemlidir.

---

## Joi Hataları Bu Sözleşmede Nereye Oturuyor?

59. bölümde Joi'nin validation hatalarını ürettiğini, 60. bölümde ise Validation Middleware'in bu mesajları Response'a taşıdığını görmüştük.

Örneğin kullanıcı şöyle bir Body göndersin:

```json
{
  "title": "   ",
  "releaseYear": "2014"
}
```

## Örnek 47

Joi iki ayrı problem bulabilir.

Validation Middleware'imiz bunları şu Response ile döndürebilir:

```json
{
  "message": "Geçersiz Request Body.",
  "errors": [
    "title yalnızca boşluklardan oluşamaz.",
    "releaseYear Number olmalıdır."
  ]
}
```

## Örnek 48

Bu yapı seçtiğimiz hata sözleşmesine zaten uyuyor:

```text
message
↓
Request'in genel olarak neden başarısız olduğunu söyler

errors
↓
hangi validation kurallarının başarısız olduğunu ayrıntılı gösterir
```

## Örnek 49

Bu nedenle 59 ve 60. bölümlerde yazdığımız Joi validation kodunu burada yeniden kurmuyoruz.

Yalnızca üç katmanın görevini ayırıyoruz:

```text
Joi Schema
↓
hata mesajını üretir

Validation Middleware
↓
validation'ı çalıştırır ve mesajları toplar

Response sözleşmesi
↓
ayrıntılı hata listesinin errors alanında duracağını belirler
```

## Örnek 50

Yani yeni olan Joi'nin nasıl hata ürettiği değil, **üretilen hata bilgisinin API Response'unda nerede duracağıdır.**

---

## `getMovieById` Response'unu Kontrol Edelim

Başarılı durumda:

```js
res.status(200).json({
  message: "Film getirildi.",
  data: movie,
});
```

## Örnek 51

404 durumunda:

```js
return res.status(404).json({
  message: "Film bulunamadı.",
});
```

## Örnek 52

Bu iki Response da yeni sözleşmemize uygundur.

Burada önemli nokta:

```text
success: true
success: false
```

## Örnek 53

gibi ek bir alan koymamamızdır.

Bunu yapamaz mıyız?

Elbette yapabiliriz.

Ama şu anda HTTP status zaten işlemin başarılı mı başarısız mı olduğunu taşıyor.

Aynı bilgiyi iki farklı yerde tutmak zorunda değiliz.

---

## Response Sözleşmemizi Bir Tabloyla Görelim

| Durum | HTTP Status | Body |
|---|---:|---|
| Film listesi | 200 | `message + data + meta` |
| Tek film | 200 | `message + data` |
| Film oluşturma | 201 | `message + data` |
| Film güncelleme | 200 | `message + data` |
| Film silme ve body döndürme | 200 | `message + data` |
| Geçersiz Body | 400 | `message + errors` |
| Geçersiz Movie ID | 400 | `message` |
| Movie bulunamadı | 404 | `message` |
| Beklenmeyen sunucu hatası | 500 | `message` |

Bu tablo, API'nin farklı durumlarda hangi HTTP status ve Body yapısını kullandığını tek yerde gösterir.

---

## Postman ile Response'ları Yan Yana Açalım

Şu Request'leri sırayla deneyin:

```http
GET /movies
```

## Örnek 54



```http
GET /movies/:movieId
```

## Örnek 55



```http
POST /movies
```

## Örnek 56



```http
PATCH /movies/:movieId
```

## Örnek 57

ve bilerek geçersiz bir POST Body gönderin.

Her Response'ta şu üç noktayı kontrol edin:

```text
1. HTTP status ne söylüyor?
2. Asıl veri hangi alanda?
3. Ek bilgi veya hata ayrıntısı nerede?
```

## Örnek 58

Cevaplar artık daha tahmin edilebilir olmalıdır.

---

## Response Helper Ne Zaman Anlamlı Olur?

Response sözleşmesini şu aşamada Controller içinde açık biçimde kullanıyoruz:

```js
res.status(200).json({
  message: "Film getirildi.",
  data: movie,
});
```

## Örnek 59

Bunu hemen:

```js
sendSuccess(...);
sendError(...);
```

## Örnek 60

gibi yardımcı fonksiyonların arkasına taşımak zorunlu değildir. Açık `res.status().json()` çağrıları, Response'un hangi status ve Body ile gönderildiğini bu aşamada doğrudan görmemizi sağlar.

İleride aynı Response oluşturma kalıbı birçok Controller'da tekrar etmeye başlarsa helper kullanmak anlamlı olabilir. O durumda helper'ın görevi yeni bir davranış eklemek değil, tekrar eden Response oluşturma kodunu tek yerde toplamak olur.

---

## Bölüm Sonu Uygulaması

1. `GET /movies` Response'undaki pagination alanlarını `meta` içine taşıyın.
2. `GET /movies/:movieId`, `POST`, `PATCH` ve `DELETE` Response'larını seçtiğimiz sözleşmeyle karşılaştırın.
3. Geçersiz Joi Request'inde `400`, geçerli ama bulunamayan Movie'de `404` gördüğünüzü doğrulayın.
4. Yeni Movie oluşturduğunuzda `201` döndüğünü kontrol edin.
5. DELETE işleminde body döndürdüğümüz için neden `200` kullandığımızı kendi cümlenizle açıklayın.
6. JSON Body'ye ayrıca `status: 200` eklemememizin nedenini açıklayın.

---

## 🧠 Akılda Kalsın

Tutarlı Response demek:

```text
her Response tamamen aynı olsun
```

## Örnek 61

demek değildir.

Bizim sözleşmemiz:

```text
başarılı işlem
↓
message + data

liste
↓
message + data + meta

hata
↓
message

ayrıntılı hata varsa
↓
message + errors
```

## Örnek 62

şeklindedir.

HTTP status ise JSON Body'den ayrı bir katmandır.

```text
200
↓
başarılı ve body var

201
↓
yeni kaynak oluşturuldu

400
↓
Request tarafında problem

404
↓
kaynak bulunamadı

500
↓
beklenmeyen sunucu problemi
```

## Örnek 63

En önemli hedef:

> API'yi kullanan kişi, Response'u tahmin edebilsin.

---

## Sonraki Adım

Response'larımız artık daha düzenli.

Ama Controller dosyamıza baktığımızda başka bir şey fark ediyoruz.

Controller yalnızca:

```text
req'den bilgi al
↓
status seç
↓
Response gönder
```

## Örnek 64

yapmıyor.

Aynı zamanda:

```text
Mongoose Query oluşturuyor
countDocuments() çalıştırıyor
populate() yapıyor
pagination hesaplıyor
Movie oluşturuyor
Movie güncelliyor
```
