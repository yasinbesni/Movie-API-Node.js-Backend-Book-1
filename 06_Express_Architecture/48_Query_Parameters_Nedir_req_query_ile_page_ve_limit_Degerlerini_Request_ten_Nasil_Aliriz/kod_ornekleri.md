# Bölüm 48 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Query Parameters Nedir? req.query ile page ve limit Değerlerini Request'ten Nasıl Alırız?

## Film Listesinde Sonraki Sayfaya Geçmek

Movie API'mizin bir film sitesindeki listeyi beslediğini düşünün.

İlk on film ekranda. Kullanıcı listenin devamını görmek istiyor ve **Sonraki** düğmesine basıyor.

47. bölümde bunu yapacak hesabı kurmuştuk:

```js
const page = 3;
const limit = 10;

const skip = (page - 1) * limit;
```

## Örnek 2

Ardından `skip()` ve `limit()` ile doğru kayıtları getirebiliyorduk. Ancak küçük bir sorun var: `page` ve `limit` değerlerini **biz kodun içine yazmıştık**.

Kullanıcı ikinci sayfayı isterse kodu açıp `page` değerini değiştiremeyiz. Sayfada yirmi film görmek isterse de aynı sorunla karşılaşırız.

O hâlde bu iki bilgiyi, kullanıcının gönderdiği istekle birlikte almamız gerekiyor.

Peki bir Request bize **hangi sayfanın** ve **kaç filmin** istendiğini nasıl söyleyebilir?

---

## Query Parameter Nedir?

Kullanıcı şu Request'i göndersin:

```http
GET /movies?page=3&limit=10
```

## Örnek 3

Burada Route yolu:

```text
/movies
```

## Örnek 4

bölümüdür.

`?` işaretinden sonraki:

```text
page=3&limit=10
```

## Örnek 5

bölümüne **query string** denir.

İçindeki:

```text
page=3
```

## Örnek 6

ve:

```text
limit=10
```

## Örnek 7

ifadeleri ise query parameter'lardır.

`&` işareti birden fazla query parameter'ı birbirinden ayırır.

Yani Request'i zihnimizde şöyle okuyabiliriz:

```text
GET /movies
↓
Film listesini istiyorum.

page=3
↓
3. sayfayı istiyorum.

limit=10
↓
Sayfada en fazla 10 film istiyorum.
```

## Örnek 8

---

## Express Query Değerlerini Nerede Tutar?

Express query parameter'lara:

```js
req.query
```

## Örnek 9

üzerinden ulaşmamızı sağlar.

Örneğin:

```http
GET /movies?page=3&limit=10
```

## Örnek 10

Request'i geldiğinde sadeleştirilmiş olarak:

```js
req.query
```

## Örnek 11

şöyle düşünülebilir:

```js
{
  page: "3",
  limit: "10",
}
```

## Örnek 12

Bu yüzden:

```js
req.query.page
```

## Örnek 13

değeri:

```js
"3"
```

## Örnek 14

ve:

```js
req.query.limit
```

## Örnek 15

değeri:

```js
"10"
```

## Örnek 16

olabilir.

Burada ilk önemli ayrım ortaya çıkıyor:

```text
URL'de 3 görüyoruz.
↓
Ama req.query.page çoğunlukla "3" String'idir.
```

## Örnek 17

Bizim `skip` hesabımızda ise sayıya ihtiyacımız var.

---

## O Zaman String'i Sayıya Çevirelim

İlk akla gelen çözüm:

```js
const page = Number(req.query.page);
const limit = Number(req.query.limit);
```

## Örnek 18

olabilir.

Örneğin:

```js
Number("3");
```

## Örnek 19

sonucu:

```js
3
```

## Örnek 20

olur.

Fakat yeni bir problem ortaya çıkar.

Kullanıcı hiç query göndermeyebilir:

```http
GET /movies
```

## Örnek 21

veya geçersiz bir değer gönderebilir:

```http
GET /movies?page=abc
```

## Örnek 22

Böyle durumlarda Controller'ın her defasında:

```text
değer geldi mi?
String mi?
sayıya dönüşüyor mu?
pozitif tam sayı mı?
gelmediyse hangi varsayılan kullanılacak?
```

## Örnek 23

sorularını tek tek çözmesini istemiyoruz.

Bu işin tek bir görevi var:

> Pagination için kullanılabilir bir sayı üretmek.

O hâlde bu işi küçük bir yardımcı fonksiyona verelim.

---

## İlk Yardımcı Fonksiyonumuz: `parseNumber`

Yeni bir dosya oluşturacağız:

```text
src/utils/parsePaginationParams.js
```

## Örnek 24

Önce dosyanın içinde yalnızca şu küçük fonksiyonu yazalım:

```js
const parseNumber = (value, defaultValue) => {
  if (typeof value !== "string") {
    return defaultValue;
  }

  const parsedNumber = Number(value);

  if (
    !Number.isSafeInteger(parsedNumber) ||
    parsedNumber < 1
  ) {
    return defaultValue;
  }

  return parsedNumber;
};
```

## Örnek 25

Fonksiyonun görevi çok nettir:

```text
value kullanılabilir mi?
↓
Evet → sayıya çevir ve döndür.

Hayır → defaultValue döndür.
```

## Örnek 26

Örneğin:

```js
parseNumber("3", 1);
```

## Örnek 27

sonucu:

```js
3
```

## Örnek 28

olur.

Ama:

```js
parseNumber(undefined, 1);
```

## Örnek 29

sonucu:

```js
1
```

## Örnek 30

olur.

Aynı şekilde:

```js
parseNumber("abc", 1);
```

## Örnek 31

sonucu da:

```js
1
```

## Örnek 32

olur.

---

## `defaultValue` Neden Parametre Olarak Geliyor?

Fonksiyonumuzu yalnızca `page` için yazmıyoruz.

Pagination'da iki farklı varsayılanımız var:

```text
page
↓
1

limit
↓
10
```

## Örnek 33

Eğer fonksiyonun içine doğrudan:

```js
return 1;
```

## Örnek 34

yazsaydık aynı fonksiyonu `limit` için kullanmak zorlaşırdı.

Bunun yerine:

```js
defaultValue
```

## Örnek 35

parametresi kullanıyoruz.

Böylece:

```js
parseNumber(page, 1);
```

## Örnek 36

sayfa için varsayılan `1` değerini,

```js
parseNumber(limit, 10);
```

## Örnek 37

ise limit için varsayılan `10` değerini kullanabilir.

Aynı yardımcı fonksiyon iki farklı ihtiyaca hizmet eder.

---

## Kodun İçini Adım Adım Okuyalım

İlk kontrol:

```js
if (typeof value !== "string") {
  return defaultValue;
}
```

## Örnek 38

şunu söyler:

> Gelen değer String değilse onu pagination sayısı olarak kullanma.

Sonra:

```js
const parsedNumber = Number(value);
```

## Örnek 39

ile String değeri sayıya dönüştürmeye çalışıyoruz.

Örneğin:

```text
"3"
↓
Number("3")
↓
3
```

## Örnek 40

Sonra şu kontrol geliyor:

```js
!Number.isSafeInteger(parsedNumber)
```

## Örnek 41

`Number.isSafeInteger()` değerin JavaScript tarafından güvenli biçimde temsil edilebilen bir tam sayı olup olmadığını kontrol eder.

Örneğin:

```js
Number.isSafeInteger(3);
// true
```

## Örnek 42

ama:

```js
Number.isSafeInteger(2.5);
// false
```

## Örnek 43

ve:

```js
Number.isSafeInteger(NaN);
// false
```

## Örnek 44

olur.

Pagination'da ayrıca:

```js
parsedNumber < 1
```

## Örnek 45

değerlerini de istemiyoruz.

Çünkü:

```text
page = 0
limit = 0
page = -2
```

## Örnek 46

bizim pagination kurallarımıza uygun değildir.

Bu yüzden geçersiz değerlerde:

```js
return defaultValue;
```

## Örnek 47

diyoruz.

---

## Neden `parseInt()` Yerine `Number()` Kullanıyoruz?

Şöyle bir kod da görebilirsiniz:

```js
parseInt("3");
```

## Örnek 48

Bu da `3` sonucunu verebilir.

Fakat `parseInt()` metnin başındaki sayısal kısmı okuyabildiği için:

```js
parseInt("3abc");
```

## Örnek 49

sonucunda:

```js
3
```

## Örnek 50

dönebilir.

Biz pagination parametresinde:

```text
"3abc"
```

## Örnek 51

değerini geçerli kabul etmek istemiyoruz.

`Number("3abc")` ise:

```js
NaN
```

## Örnek 52

üretir.

Ardından:

```js
Number.isSafeInteger(NaN);
```

## Örnek 53

`false` olduğu için yardımcı fonksiyon varsayılan değere döner.

Bu nedenle burada:

```js
Number()
```

## Örnek 54

kullanımı bizim seçtiğimiz kurala daha uygundur.

---

## Şimdi `page` ve `limit` Değerlerini Birlikte Hazırlayalım

`parseNumber()` küçük bir yardımcıdır.

Controller'ın ise şöyle bir şey yapmak zorunda kalmasını istemiyoruz:

```js
const page = parseNumber(req.query.page, 1);

const limit = parseNumber(req.query.limit, 10);
```

## Örnek 55

Bu kod yanlış değildir.

Ama burada üç değerin uygulamamız için özel bir anlamı vardır:

```text
1
↓
varsayılan page

10
↓
varsayılan limit

50
↓
izin verdiğimiz en büyük limit
```

## Örnek 56

Bunlar MongoDB'nin zorunlu değerleri değildir. Bizim API'mizin aldığı kararlardır.

Üstelik yarın:

```text
varsayılan limit
10 → 20

maximum limit
50 → 100
```

## Örnek 57

gibi bir değişiklik yapmak isteyebiliriz.

Bu tür uygulama kurallarını sayıların kullanıldığı her yere dağıtmak yerine isim vererek tek yerde tutmak daha anlaşılırdır.

37. bölümde proje yapısının ihtiyaç oldukça büyüyeceğini söylemiştik. Şimdi ilk kez ortak sabit değerler için yeni bir sorumluluk doğdu.

Yeni dosyamız:

```text
src/constants/pagination.js
```

## Örnek 58

olsun:

```js
export const DEFAULT_PAGE = 1;
export const DEFAULT_LIMIT = 10;
export const MAX_LIMIT = 50;
```

## Örnek 59

Artık sayıların ne anlattığını isimlerinden görebiliyoruz.

Projemizin ilgili kısmı da şöyle büyür:

```text
src
├── constants
│   └── pagination.js
├── controllers
├── db
├── routers
├── utils
│   └── parsePaginationParams.js
├── index.js
└── server.js
```

## Örnek 60

Şimdi `parsePaginationParams.js` içinde bu sabitleri kullanalım:

```js
import {
  DEFAULT_LIMIT,
  DEFAULT_PAGE,
  MAX_LIMIT,
} from "../constants/pagination.js";

export const parsePaginationParams = (query) => {
  const { page, limit } = query;

  const parsedPage = parseNumber(
    page,
    DEFAULT_PAGE,
  );

  const parsedLimit = parseNumber(
    limit,
    DEFAULT_LIMIT,
  );

  return {
    page: parsedPage,
    limit: Math.min(parsedLimit, MAX_LIMIT),
  };
};
```

## Örnek 61

Şimdi bütün pagination giriş kurallarımız tek yerde tanımlanıyor ve yardımcı fonksiyon bu isimlendirilmiş kuralları kullanıyor:

```text
page geçersiz veya yok
↓
1

limit geçersiz veya yok
↓
10

limit 50'den büyük
↓
50
```

## Örnek 62

Buradaki:

```js
Math.min(parsedLimit, MAX_LIMIT)
```

## Örnek 63

iki sayıdan küçük olanı döndürür. `MAX_LIMIT` değerimiz şu anda `50` olduğu için davranış değişmez.

Örneğin:

```js
Math.min(20, 50);
// 20
```

## Örnek 64

ama:

```js
Math.min(500, 50);
// 50
```

## Örnek 65

olur.

Yani kullanıcı:

```http
GET /movies?limit=500
```

## Örnek 66

gönderse bile API'miz en fazla 50 film döndürür.

`50` MongoDB'nin zorunlu sınırı değildir.

Bu, bizim API için belirlediğimiz sınırdır.

---

## Yardımcı Dosyanın Tam Hâli

Artık:

```text
src/utils/parsePaginationParams.js
```

## Örnek 67

dosyamızın tamamı şöyle:

```js
import {
  DEFAULT_LIMIT,
  DEFAULT_PAGE,
  MAX_LIMIT,
} from "../constants/pagination.js";

const parseNumber = (value, defaultValue) => {
  if (typeof value !== "string") {
    return defaultValue;
  }

  const parsedNumber = Number(value);

  if (
    !Number.isSafeInteger(parsedNumber) ||
    parsedNumber < 1
  ) {
    return defaultValue;
  }

  return parsedNumber;
};

export const parsePaginationParams = (query) => {
  const { page, limit } = query;

  const parsedPage = parseNumber(
    page,
    DEFAULT_PAGE,
  );

  const parsedLimit = parseNumber(
    limit,
    DEFAULT_LIMIT,
  );

  return {
    page: parsedPage,
    limit: Math.min(parsedLimit, MAX_LIMIT),
  };
};
```

## Örnek 68

Burada yalnızca:

```js
parsePaginationParams
```

## Örnek 69

fonksiyonunu export ediyoruz.

`parseNumber` ise bu dosyanın kendi içindeki yardımcı fonksiyondur.

Controller'ın `parseNumber` fonksiyonunu doğrudan bilmesine gerek yoktur.

---

## Controller Bu Yardımcı Fonksiyonu Nasıl Kullanacak?

Şimdi:

```text
src/controllers/movies.js
```

## Örnek 70

dosyasına dönelim.

Üste şu import'u ekleyelim:

```js
import {
  parsePaginationParams,
} from "../utils/parsePaginationParams.js";
```

## Örnek 71

`getMovies` içinde artık:

```js
const {
  page,
  limit,
} = parsePaginationParams(req.query);
```

## Örnek 72

yazabiliriz.

Bu satırın anlamı:

```text
req.query
↓
pagination yardımcısına gönder
↓
kullanılabilir page ve limit değerlerini al
```

## Örnek 73

olur.

Sonra 47. bölümde zaten bildiğimiz hesabı kullanırız:

```js
const skip = (page - 1) * limit;
```

## Örnek 74

Yani yeni konu eski konuyu değiştirmedi.

Sadece:

```text
page ve limit nereden geliyor?
```

## Örnek 75

sorusunu çözdü.

---

## `getMovies` Controller'ının Güncel Hâli

```js
import {
  parsePaginationParams,
} from "../utils/parsePaginationParams.js";

export const getMovies = async (req, res) => {
  const {
    page,
    limit,
  } = parsePaginationParams(req.query);

  const skip = (page - 1) * limit;

  const movies = await Movie.find({})
    .sort({
      voteAverage: -1,
      releaseYear: -1,
      _id: 1,
    })
    .skip(skip)
    .limit(limit);

  res.status(200).json({
    message: "Filmler getirildi.",
    page,
    limit,
    data: movies,
  });
};
```

## Örnek 76

Burada görevler artık daha açık ayrılmış durumda:

```text
parsePaginationParams()
↓
page ve limit değerlerini hazırla

Controller
↓
skip hesapla
↓
Mongoose sorgusunu çalıştır
↓
Response gönder
```

## Örnek 77

Controller'ın içinde tekrar tekrar:

```text
typeof
Number()
Number.isSafeInteger()
default değer
limit üst sınırı
```

## Örnek 78

kontrolleri yazmıyoruz.

Bu ayrım kodun okunmasını kolaylaştırır.

---

## Birkaç Request ile Davranışı Görelim

### Query yoksa

```http
GET /movies
```

## Örnek 79

sonuç:

```text
page = 1
limit = 10
skip = 0
```

## Örnek 80

olur.

### Geçerli değerler gelirse

```http
GET /movies?page=3&limit=20
```

## Örnek 81

sonuç:

```text
page = 3
limit = 20
skip = 40
```

## Örnek 82

olur.

### page geçersizse

```http
GET /movies?page=abc
```

## Örnek 83

`page` için varsayılan değer kullanılır:

```text
page = 1
```

## Örnek 84

### limit geçersizse

```http
GET /movies?limit=abc
```

## Örnek 85

sonuç:

```text
limit = 10
```

## Örnek 86

olur.

### limit çok büyükse

```http
GET /movies?limit=500
```

## Örnek 87

sonuç:

```text
limit = 50
```

## Örnek 88

olur.

Bu bölümde pagination parametreleri için şu API kararını aldık:

> Kullanılamayan değerlerde güvenli varsayılanlara dön.

İleride başka query parameter'larda farklı bir politika seçebiliriz.

Örneğin bir filtre değeri geçersizse:

```text
400 Bad Request
```

## Örnek 89

döndürmek daha uygun olabilir.

Yani:

```text
her query parameter
↓
aynı hata politikasını kullanmak zorunda değildir
```

## Örnek 90

---

## `req.params` ile `req.query` Aynı Şey Değil

Şu Request:

```http
GET /movies/abc123
```

## Örnek 91

ve Route:

```js
/movies/:movieId
```

## Örnek 92

için:

```js
req.params.movieId
```

## Örnek 93

kullanırız.

Ama:

```http
GET /movies?page=2&limit=10
```

## Örnek 94

için:

```js
req.query.page
req.query.limit
```

## Örnek 95

kullanırız.

Kısaca:

```text
req.params
↓
Route yolunun dinamik parçası

req.query
↓
? işaretinden sonraki ek bilgiler
```

## Örnek 96

---

## Peki Toplam Sayfa Bilgileri Nerede?

Şu anda Response'umuz:

```text
page
limit
data
```

## Örnek 97

bilgilerini içeriyor.

Ama henüz:

```text
toplam kaç film var?
toplam kaç sayfa var?
sonraki sayfa var mı?
önceki sayfa var mı?
```

## Örnek 98

sorularını cevaplamıyoruz.

Bunun için önce collection'daki toplam kayıt sayısını bilmemiz gerekir.

O yüzden:

```js
calculatePaginationData()
```

## Örnek 99

gibi ikinci bir yardımcı fonksiyonu bu bölüme sıkıştırmayacağız.

Önce ihtiyaç ortaya çıkacak.

49. bölümde:

```js
countDocuments()
```

## Örnek 100

ile toplam kayıt sayısını öğrendikten sonra pagination metadata hesaplarını ayrı bir yardımcı fonksiyona taşıyacağız.

Akışımız:

```text
47. bölüm
↓
skip ve limit mantığı

48. bölüm
↓
page ve limit Request'ten nasıl alınır?
↓
parsePaginationParams()

49. bölüm
↓
toplam kayıt sayısı
↓
calculatePaginationData()
```

## Örnek 101

şeklinde ilerleyecek.

---

## 🧠 Akılda Kalsın

Query parameter'lara:

```js
req.query
```

## Örnek 102

üzerinden ulaşırız.

Örneğin:

```http
GET /movies?page=3&limit=10
```

## Örnek 103

için:

```text
page
limit
```

## Örnek 104

değerleri Request'ten gelir.

Bu değerleri Controller içinde tekrar tekrar işlemek yerine:

```text
src/utils/parsePaginationParams.js
```

## Örnek 105

dosyasına taşıdık.

İçerideki:

```js
parseNumber()
```

## Örnek 106

fonksiyonu:

```text
değer kullanılabilir mi?
↓
evet → sayıya çevir
hayır → default kullan
```

## Örnek 107

görevini yapar.

Pagination için uygulama seviyesindeki sabit kararları:

```text
src/constants/pagination.js
```

## Örnek 108

içinde tuttuk:

```text
DEFAULT_PAGE = 1
DEFAULT_LIMIT = 10
MAX_LIMIT = 50
```

## Örnek 109

`parsePaginationParams()` ise bu kuralları kullanarak Request'ten gelen değerleri hazırlar.

Controller yalnızca hazır değerleri kullanır:

```js
const {
  page,
  limit,
} = parsePaginationParams(req.query);
```

## Örnek 110

Ardından 47. bölümde öğrendiğimiz:

```js
const skip = (page - 1) * limit;
```

## Örnek 111

hesabı aynen devam eder.

---

## Sonraki Adım

Artık kullanıcı hangi sayfayı istediğini Request ile gönderebiliyor.

Backend de:

```text
page
limit
skip
```

## Örnek 112

bilgilerini oluşturabiliyor.

Ama kullanıcı hâlâ şunu bilmiyor:

> Toplam kaç sayfa var?

Bu sorunun cevabı için önce:

```text
MongoDB'de toplam kaç Movie Document var?
```

## Örnek 113

bilgisini bulmamız gerekir.

Bir sonraki bölümde:

```js
countDocuments()
```

## Örnek 114

ile bu sayıyı öğreneceğiz.

Sonra pagination hesaplarını büyüyen Controller'ın içine yığmak yerine:

```js
calculatePaginationData()
```
