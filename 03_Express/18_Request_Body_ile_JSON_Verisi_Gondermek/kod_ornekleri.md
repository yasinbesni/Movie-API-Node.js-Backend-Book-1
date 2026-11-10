# Bölüm 18 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Request Body ile JSON Verisi Göndermek

## Bir Request Yalnızca Yöntem ve Yoldan mı Oluşur?

Önceki bölümde Postman kullanarak

Express uygulamamıza farklı HTTP yöntemleriyle

Request gönderdik.

Yeni bir film oluşturmak için şu Request'i kullandık:

```text
POST /movies
```

## Örnek 2

Express de bu Request'i şu Route ile karşıladı:

```js
app.post("/movies", (req, res) => {
  res.send("Yeni film oluşturuluyor.");
});
```

## Örnek 3

Bu Request bize iki önemli bilgi verdi:

```text
POST
↓
Yeni bir veri oluşturulmak isteniyor.

/movies
↓
İşlem filmler üzerinde yapılacak.
```

## Örnek 4

Ancak önemli bir bilgi hâlâ eksik.

Sunucu hangi filmi oluşturacak?

Yeni filmin başlığı nedir?

Hangi yıl yayımlanmıştır?

Türü ve puanı nedir?

```text
Başlık

Yayın yılı

Tür

Puan
```

## Örnek 5

Yalnızca şu Request'e bakarak

bu soruların cevabını bilemeyiz:

```text
POST /movies
```

## Örnek 6

Çünkü HTTP yöntemi,

yapılmak istenen işlemi belirtir.

Route yolu ise

işlemin hangi kaynak üzerinde yapılacağını gösterir.

```text
HTTP yöntemi
↓
Ne yapılacak?

Route yolu
↓
Hangi kaynak üzerinde yapılacak?
```

## Örnek 7

Fakat oluşturulacak filmin bilgileri

bu iki bölümde bulunmaz.

Örneğin sunucuya şu bilgileri göndermek isteyebiliriz:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "genre": "Science Fiction",
  "rating": 8.7
}
```

## Örnek 8

Bu bilgiler Request ile birlikte

sunucuya gönderilmelidir.

```text
POST /movies
↓
Yeni film oluşturmak istiyorum.

Film bilgileri
↓
Hangi filmin oluşturulacağını belirtir.
```

## Örnek 9

Peki bu veriler Request'in neresinde taşınır?

HTTP Request'leri,

yöntem ve yol dışında

başka bilgiler de içerebilir.

Sunucuya gönderilmek istenen veriler

Request'in **Body** adı verilen bölümünde taşınabilir.

```text
Request
├── HTTP yöntemi
├── Yol
└── Body
```

## Örnek 10

Artık yeni bir kavramla karşı karşıyayız:

```text
Request Body
```

## Örnek 11

Şimdi Request Body'nin ne olduğunu

ve neden ihtiyaç duyduğumuzu inceleyelim.

## Request Body Nedir?

Request Body,

istemcinin sunucuya göndermek istediği verilerin

taşındığı bölümdür.

Örneğin yeni bir film oluşturmak istediğimizi düşünelim.

Request'in yöntemi ve yolu şöyle olabilir:

```text
POST /movies
```

## Örnek 12

Bu bilgiler bize yapılmak istenen işlemi gösterir:

```text
POST
↓
Yeni bir veri oluşturulmak isteniyor.

/movies
↓
İşlem filmler üzerinde yapılacak.
```

## Örnek 13

Oluşturulacak filmin bilgileri ise

Request Body içinde gönderilebilir:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "genre": "Science Fiction",
  "rating": 8.7
}
```

## Örnek 14

Request'in tamamını zihnimizde şöyle canlandırabiliriz:

```text
Request
│
├── HTTP yöntemi
│   └── POST
│
├── Yol
│   └── /movies
│
└── Body
    ├── title
    ├── releaseYear
    ├── genre
    └── rating
```

## Örnek 15

Body,

Request'in her zaman bulunması gereken bir bölümü değildir.

Örneğin yalnızca film listesini isteyen bir GET Request'i

çoğunlukla Body taşımaz:

```text
GET /movies
```

## Örnek 16

Ancak yeni veri oluşturmak

veya mevcut bir veriyi güncellemek istediğimizde

sunucuya ek bilgiler göndermemiz gerekebilir.

Bu nedenle Request Body genellikle:

```text
POST

PUT

PATCH
```

## Örnek 17

gibi yöntemlerle birlikte kullanılır.

🧠 Akılda Kalsın

```text
HTTP yöntemi
↓
Ne yapılmak istendiğini belirtir.

Yol
↓
Hangi kaynak üzerinde işlem yapılacağını belirtir.

Body
↓
İşlem için gerekli verileri taşır.
```

## Örnek 18

## Veriyi Neden JSON Biçiminde Gönderiyoruz?

Request Body içinde farklı biçimlerde veri taşınabilir.

Biz bu bölümde verilerimizi

**JSON** biçiminde göndereceğiz.

JSON'un açılımı şöyledir:

```text
JavaScript Object Notation
```

## Örnek 19

JSON,

verileri anahtar ve değer çiftleriyle

temsil etmemizi sağlayan metin tabanlı bir veri biçimidir.

Örneğin bir filmin bilgilerini

JSON biçiminde şöyle yazabiliriz:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "genre": "Science Fiction",
  "rating": 8.7
}
```

## Örnek 20

Buradaki her bilgi

bir anahtar ve değerden oluşur:

```text
"title"
↓
Anahtar

"Interstellar"
↓
Değer
```

## Örnek 21

Bir başka örnek:

```text
"releaseYear"
↓
Anahtar

2014
↓
Değer
```

## Örnek 22

JSON'un web uygulamalarında sık kullanılmasının önemli nedenlerinden biri,

insanlar tarafından okunabilmesi

ve farklı programlama dilleri tarafından işlenebilmesidir.

```text
İstemci
↓
JSON verisi gönderir.

Sunucu
↓
JSON verisini işler.
```

## Örnek 23

Ancak JSON ile JavaScript nesnesi

tam olarak aynı şey değildir.

JavaScript'te şöyle bir nesne yazabiliriz:

```js
const movie = {
  title: "Interstellar",
  releaseYear: 2014,
};
```

## Örnek 24

JSON biçiminde ise anahtarlar

çift tırnak içinde yazılır:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014
}
```

## Örnek 25

JSON yazarken bazı temel kurallara dikkat etmeliyiz:

```text
Anahtarlar çift tırnak içinde yazılır.

Metin değerleri çift tırnak içinde yazılır.

Sayılar tırnak içine alınmaz.

Özelliklerin arasına virgül konur.

Son özellikten sonra virgül konmaz.
```

## Örnek 26

Doğru bir JSON örneği:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "isPublished": true
}
```

## Örnek 27

Hatalı bir JSON örneği:

```json
{
  title: "Interstellar",
  "releaseYear": 2014,
}
```

## Örnek 28

Bu örnekte iki sorun vardır:

```text
title anahtarı çift tırnak içinde değildir.

Son özellikten sonra gereksiz virgül vardır.
```

## Örnek 29

Şimdi film bilgilerimizi

Postman üzerinden JSON biçiminde gönderelim.

## Postman'de Body Bölümü Nasıl Kullanılır?

Öncelikle Express uygulamamızın çalıştığından emin olalım:

```bash
npm start
```

## Örnek 30

Ardından Postman'de yeni bir Request oluşturalım.

HTTP yöntemi olarak:

```text
POST
```

## Örnek 31

seçelim.

Request adresini yazalım:

```text
http://localhost:3000/movies
```

## Örnek 32

Hazırladığımız Request şu şekilde görünmelidir:

```text
POST http://localhost:3000/movies
```

## Örnek 33

Şimdi Request ayarlarının bulunduğu bölümden

`Body` sekmesine geçelim.

Postman bize farklı Body türleri sunar.

Bu bölümde şu seçeneği kullanacağız:

```text
raw
```

## Örnek 34

`raw` seçeneği,

Body içine metin biçiminde veri yazmamızı sağlar.

Ardından veri türü olarak:

```text
JSON
```

## Örnek 35

seçelim.

Adımlarımız şöyle olmalıdır:

```text
Body
↓
raw
↓
JSON
```

## Örnek 36

Body alanına şu veriyi yazalım:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "genre": "Science Fiction",
  "rating": 8.7
}
```

## Örnek 37

<img src="./images/18-postman/01-body-raw-json.png" alt="Postman'de JSON Body hazırlama adımları" width="720">

*Görsel 18.1 — Postman'de `POST` yöntemiyle JSON veri göndermek için `Body`, `raw` ve `JSON` seçimlerinin yapılması.*

Postman ekranındaki hazırlığımızı şöyle özetleyebiliriz:

```text
HTTP yöntemi
↓
POST

Adres
↓
http://localhost:3000/movies

Body türü
↓
raw — JSON

Gönderilen veri
↓
Film bilgileri
```

## Örnek 38

Henüz `Send` düğmesine basmayalım.

Önce Express uygulamamızın

bu veriyi karşılayacak Route'unu hazırlayalım.

## İlk JSON Verimizi Gönderelim

`index.js` dosyamızda

şu POST Route'unun bulunduğundan emin olalım:

```js
app.post("/movies", (req, res) => {
  res.send("Film bilgileri alındı.");
});
```

## Örnek 39

Dosyamızın ilgili bölümü şöyle görünebilir:

```js
import express from "express";

const app = express();

app.post("/movies", (req, res) => {
  res.send("Film bilgileri alındı.");
});

app.listen(3000, () => {
  console.log("Server 3000 portunda çalışıyor.");
});
```

## Örnek 40

Uygulamayı yeniden başlatalım:

```bash
npm start
```

## Örnek 41

Şimdi Postman'e dönelim.

Body alanında şu JSON verisi bulunmalıdır:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "genre": "Science Fiction",
  "rating": 8.7
}
```

## Örnek 42

`Send` düğmesine basalım.

Postman şu Request'i gönderir:

```text
POST /movies
```

## Örnek 43

Express eşleşen Route'u bulur

ve şu Response'u gönderir:

```text
Film bilgileri alındı.
```

## Örnek 44

Ancak burada önemli bir sorun vardır.

Biz sunucuya film bilgilerini gönderdik.

Fakat Route içinde bu bilgilere henüz ulaşmadık.

```js
app.post("/movies", (req, res) => {
  res.send("Film bilgileri alındı.");
});
```

## Örnek 45

Bu kod yalnızca Request'i karşılıyor

ve sabit bir Response gönderiyor.

Peki gönderdiğimiz JSON verisi nerede?

Express bu veriye nasıl ulaşacak?

Bu soruyu cevaplamadan önce

sunucunun gönderilen verinin JSON olduğunu

nasıl anlayabildiğini inceleyelim.

## Sunucu Verinin JSON Olduğunu Nasıl Anlar?

Bir Request sunucuya veri gönderirken

verinin hangi biçimde olduğunu da belirtmelidir.

Biz Body içinde JSON verisi gönderiyoruz:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014
}
```

## Örnek 46

Request'in taşıdığı verinin türü,

`Content-Type` adlı Header ile belirtilebilir.

JSON verisi gönderdiğimizde kullanılan değer şöyledir:

```text
Content-Type: application/json
```

## Örnek 47

Bu Header sunucuya şu bilgiyi verir:

```text
Request Body içindeki veri
↓
JSON biçimindedir.
```

## Örnek 48

Postman'de:

```text
Body
↓
raw
↓
JSON
```

## Örnek 49

seçtiğimizde,

Postman gerekli `Content-Type` Header'ını

genellikle otomatik olarak ekler.

Postman'deki `Headers` sekmesine baktığımızda

şuna benzer bir bilgi görebiliriz:

```text
Content-Type
↓
application/json
```

## Örnek 50

<img src="./images/18-postman/02-content-type-application-json.png" alt="Postman'de Content-Type Request Header'ı" width="720">

*Görsel 18.2 — Postman'in Request Header'ları arasında `Content-Type: application/json` bilgisinin görüntülenmesi.*

Ancak burada önemli bir ayrım vardır.

`Content-Type` yalnızca verinin biçimini belirtir.

JSON verisini kendiliğinden okuyup

JavaScript nesnesine dönüştürmez.

```text
Content-Type
↓
Verinin JSON olduğunu söyler.

Express
↓
JSON verisini ayrıca işlemelidir.
```

## Örnek 51

Peki Express gönderdiğimiz JSON verisini

kendiliğinden işleyebilir mi?

Bunu deneyerek görelim.

## Express Gönderilen Veriyi Neden Göremiyor?

Express'te gelen Request'i temsil eden nesne:

```text
req
```

## Örnek 52

nesnesidir.

Request Body içindeki verilere ulaşmak için

şu özelliği kullanırız:

```js
req.body
```

## Örnek 53

POST Route'umuzu şöyle değiştirelim:

```js
app.post("/movies", (req, res) => {
  console.log(req.body);

  res.send("Film bilgileri alındı.");
});
```

## Örnek 54

Sunucuyu yeniden başlatalım:

```bash
npm start
```

## Örnek 55

Postman'den aynı JSON verisini tekrar gönderelim:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "genre": "Science Fiction",
  "rating": 8.7
}
```

## Örnek 56

Terminalde görmeyi beklediğimiz şey

film bilgileridir.

Ancak şu sonuçla karşılaşabiliriz:

```text
undefined
```

## Örnek 57

Request Body sunucuya gönderilmiştir.

Fakat Express bu JSON verisini

henüz JavaScript'in anlayabileceği bir yapıya dönüştürmemiştir.

```text
Postman
↓
JSON verisi gönderir.

Express
↓
Request'i karşılar.

req.body
↓
Henüz hazırlanmadığı için undefined olabilir.
```

## Örnek 58

Demek ki yalnızca JSON verisi göndermek yeterli değildir.

Express'e:

> Gelen JSON verisini oku ve kullanılabilir hâle getir.

dememiz gerekir.

Bunun için `express.json()` kullanacağız.

## express.json() Ne Yapar?

Bir önceki denememizde:

```js
console.log(req.body);
```

## Örnek 59

yazdığımızda şu sonuçla karşılaştık:

```text
undefined
```

## Örnek 60

JSON verisi Request ile birlikte sunucuya ulaşmıştı.

Ancak Express,

Body içindeki JSON verisini henüz işlememişti.

İşte bu sorunu:

```js
express.json()
```

## Örnek 61

çözer.

`express.json()`,

Request Body içindeki JSON verisini okur

ve JavaScript içinde kullanabileceğimiz bir nesneye dönüştürür.

Ardından işlenen veriyi:

```js
req.body
```

## Örnek 62

üzerinden erişilebilir hâle getirir.

```text
Request Body içindeki JSON
↓
express.json() tarafından okunur.
↓
JavaScript nesnesine dönüştürülür.
↓
req.body üzerinden erişilebilir hâle gelir.
```

## Örnek 63

### express.json() Uygulamaya Nasıl Eklenir?

Önce Express uygulamamızı oluşturuyorduk:

```js
import express from "express";

const app = express();
```

## Örnek 64

Şimdi bu kodun hemen altına şu satırı ekleyelim:

```js
app.use(express.json());
```

## Örnek 65

Kodumuz şöyle görünür:

```js
import express from "express";

const app = express();

app.use(express.json());
```

## Örnek 66

Buradaki:

```js
express.json()
```

## Örnek 67

gelen JSON Body'lerini işleyen

middleware'i oluşturur.

```js
app.use()
```

## Örnek 68

ise bu middleware'i

Express uygulamamıza ekler.

Bu nedenle:

```js
app.use(express.json());
```

## Örnek 69

satırını şöyle okuyabiliriz:

> Uygulamaya gelen JSON Body'lerini oku  
> ve içindeki verileri `req.body` üzerinden kullanılabilir hâle getir.

### Request Geldiğinde Ne Olur?

Artık uygulamamızda şu kod bulunuyor:

```js
import express from "express";

const app = express();

app.use(express.json());

app.post("/movies", (req, res) => {
  console.log(req.body);

  res.send("Film bilgileri alındı.");
});

app.listen(3000, () => {
  console.log("Server 3000 portunda çalışıyor.");
});
```

## Örnek 70

Postman'den şu JSON verisini gönderdiğimizi düşünelim:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "genre": "Science Fiction",
  "rating": 8.7
}
```

## Örnek 71

Request uygulamaya ulaştığında süreç şöyle ilerler:

```text
Postman JSON Body gönderir.
↓
Request Express uygulamasına ulaşır.
↓
express.json() Body içindeki JSON'u okur.
↓
Veriyi JavaScript nesnesine dönüştürür.
↓
Sonucu req.body içine yerleştirir.
↓
POST /movies Route'u çalışır.
```

## Örnek 72

Route çalıştığında artık:

```js
console.log(req.body);
```

## Örnek 73

şuna benzer bir sonuç verir:

```js
{
  title: "Interstellar",
  releaseYear: 2014,
  genre: "Science Fiction",
  rating: 8.7
}
```

## Örnek 74

Artık veriye ulaşabiliyoruz.

### Kodların Sırası Neden Önemlidir?

Express,

uygulamaya eklenen görevleri

yazıldıkları sıraya göre çalıştırır.

Bu nedenle `express.json()` satırını

Route'lardan önce yazmalıyız:

```js
const app = express();

app.use(express.json());

app.post("/movies", (req, res) => {
  console.log(req.body);
});
```

## Örnek 75

Doğru akış şöyledir:

```text
Önce JSON Body okunur.
↓
req.body hazırlanır.
↓
Ardından Route çalışır.
```

## Örnek 76

Eğer `express.json()` satırını

Route'un altına yazarsak:

```js
const app = express();

app.post("/movies", (req, res) => {
  console.log(req.body);
});

app.use(express.json());
```

## Örnek 77

Request önce Route'a ulaşır.

Route çalıştığı sırada

JSON Body henüz okunmamış olabilir.

Bu nedenle `req.body` kullanıma hazır olmaz.

```text
Yanlış sıra

Route çalışır.
↓
req.body kullanılmak istenir.
↓
express.json() henüz çalışmamıştır.
```

## Örnek 78

🧠 Akılda Kalsın

```text
express.json()
↓
Request Body içindeki JSON verisini okur
ve JavaScript nesnesine dönüştürür.

app.use(express.json())
↓
Bu görevi Express uygulamasına ekler.

req.body
↓
İşlenen verilere ulaşmamızı sağlar.
```

## Örnek 79

`express.json()`,

Express'in yerleşik **middleware** yapılarından biridir.

Middleware kavramını bir sonraki bölümde

daha ayrıntılı inceleyeceğiz.

Şimdilik şunu bilmemiz yeterlidir:

```text
Request
↓
express.json()
↓
Route
```

## Örnek 80

Yani `express.json()`,

Request Route'a ulaşmadan önce çalışır

ve JSON verisini Route'un kullanabileceği hâle getirir.

## req.body ile Veriye Nasıl Ulaşırız?

Artık uygulamamızda şu satır bulunuyor:

```js
app.use(express.json());
```

## Örnek 81

Postman'den JSON verisini yeniden gönderelim:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "genre": "Science Fiction",
  "rating": 8.7
}
```

## Örnek 82

Route içinde:

```js
console.log(req.body);
```

## Örnek 83

kullandığımızda terminalde şuna benzer bir sonuç görürüz:

```js
{
  title: "Interstellar",
  releaseYear: 2014,
  genre: "Science Fiction",
  rating: 8.7
}
```

## Örnek 84

Postman'den JSON biçiminde gönderilen veri,

Express tarafından işlenmiş

ve JavaScript nesnesine dönüştürülmüştür.

Artık bu nesnenin içindeki alanlara ulaşabiliriz.

Filmin başlığına ulaşmak için:

```js
req.body.title
```

## Örnek 85

yazabiliriz.

Yayın yılına ulaşmak için:

```js
req.body.releaseYear
```

## Örnek 86

yazabiliriz.

Diğer alanlara da aynı şekilde ulaşırız:

```js
req.body.genre;
req.body.rating;
```

## Örnek 87

Route'umuzu şöyle güncelleyelim:

```js
app.post("/movies", (req, res) => {
  console.log(req.body.title);
  console.log(req.body.releaseYear);
  console.log(req.body.genre);
  console.log(req.body.rating);

  res.send("Film bilgileri alındı.");
});
```

## Örnek 88

Request'i gönderdiğimizde terminalde şunları görürüz:

```text
Interstellar

2014

Science Fiction

8.7
```

## Örnek 89

İstersek alanları değişkenlere de ayırabiliriz:

```js
app.post("/movies", (req, res) => {
  const title = req.body.title;
  const releaseYear = req.body.releaseYear;
  const genre = req.body.genre;
  const rating = req.body.rating;

  console.log(title);
  console.log(releaseYear);
  console.log(genre);
  console.log(rating);

  res.send("Film bilgileri alındı.");
});
```

## Örnek 90

Aynı işlemi object destructuring kullanarak

daha kısa biçimde de yazabiliriz:

```js
app.post("/movies", (req, res) => {
  const { title, releaseYear, genre, rating } = req.body;

  console.log(title);
  console.log(releaseYear);
  console.log(genre);
  console.log(rating);

  res.send("Film bilgileri alındı.");
});
```

## Örnek 91

Buradaki:

```js
const { title, releaseYear, genre, rating } = req.body;
```

## Örnek 92

satırı,

`req.body` içindeki alanları

aynı isimlere sahip değişkenlere ayırır.

Ancak önemli bir noktayı unutmamalıyız.

`req.body` içindeki bilgiler

istemci tarafından gönderilir.

Yani kullanıcı bu bilgileri:

```text
Eksik

Yanlış

Beklenmeyen

veya zararlı
```

## Örnek 93

biçimde gönderebilir.

Bu nedenle `req.body` içindeki verilere

kontrol etmeden güvenmemeliyiz.

Gönderilen verileri nasıl doğrulayacağımızı

ilerleyen bölümlerde öğreneceğiz.

## Gönderilen Veriyi Response Olarak Geri Döndürelim

Şimdi Postman'den gönderdiğimiz veriyi

Response içinde geri gönderelim.

Route'umuzu şöyle yazabiliriz:

```js
app.post("/movies", (req, res) => {
  const movie = req.body;

  res.json({
    message: "Film bilgileri alındı.",
    data: movie,
  });
});
```

## Örnek 94

Buradaki:

```js
const movie = req.body;
```

## Örnek 95

satırı,

gönderilen film bilgilerini

`movie` değişkenine kaydeder.

Ardından:

```js
res.json()
```

## Örnek 96

ile JSON biçiminde Response göndeririz.

Postman'den şu veriyi gönderelim:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "genre": "Science Fiction",
  "rating": 8.7
}
```

## Örnek 97

Response alanında şuna benzer bir sonuç görürüz:

```json
{
  "message": "Film bilgileri alındı.",
  "data": {
    "title": "Interstellar",
    "releaseYear": 2014,
    "genre": "Science Fiction",
    "rating": 8.7
  }
}
```

## Örnek 98

Gerçekleşen işlemi şöyle gösterebiliriz:

```text
Postman
↓
JSON Body gönderir.

express.json()
↓
JSON verisini işler.

req.body
↓
İşlenen veriyi taşır.

Route
↓
Veriyi kullanır.

res.json()
↓
JSON Response gönderir.
```

## Örnek 99

<img src="./images/18-postman/03-request-body-veri-akisi.svg" alt="Request Body'den Response'a veri akışı" width="720">

*Görsel 18.3 — JSON verisinin Postman'den gönderilmesi, `express.json()` ile işlenmesi, `req.body` üzerinden Route'ta kullanılması ve `res.json()` ile geri gönderilmesi.*

Burada henüz gerçek bir film oluşturmadık.

Verileri bir veritabanına kaydetmedik.

Yalnızca:

```text
Request Body'den veriyi aldık

ve

Response olarak geri gönderdik.
```

## Örnek 100

Bu küçük işlem,

istemciden sunucuya veri taşıma mantığını

anlamamız için önemli bir adımdır.

## Body Gönderilmezse Ne Olur?

POST Route'umuzun Request Body beklediğini düşünelim:

```js
app.post("/movies", (req, res) => {
  console.log(req.body);

  res.json({
    data: req.body,
  });
});
```

## Örnek 101

Postman'de `Body` bölümünü boş bırakarak

Request'i tekrar gönderirsek,

sunucuya film bilgileri ulaşmaz.

Express 5 kullandığımız uygulamamızda,

işlenecek bir Body bulunmadığında:

```js
req.body
```

## Örnek 102

değeri `undefined` olabilir.

```text
Body gönderilmedi
↓
req.body
↓
undefined
```

## Örnek 103

Ancak Body içine özellikle boş bir JSON nesnesi gönderirsek:

```json
{}
```

## Örnek 104

bu kez `req.body` boş bir nesne olabilir:

```js
{}
```

## Örnek 105

Aradaki fark şudur:

```text
Hiç Body gönderilmedi
↓
undefined

Boş JSON nesnesi gönderildi
↓
{}
```

## Örnek 106

Her iki durumda da

sunucunun kullanabileceği film bilgileri bulunmaz.

Önemli olan şudur:

```text
Body gönderilmediyse
↓
Sunucu kullanabileceği film bilgilerini alamaz.
```

## Örnek 107

Benzer bir sorun,

JSON seçilmediğinde

veya yanlış `Content-Type` gönderildiğinde de yaşanabilir.

`express.json()` yalnızca uygun biçimde gönderilen

JSON Body'lerini işler.

Body içinde bazı alanlar eksik de olabilir.

Örneğin yalnızca şu veri gönderilebilir:

```json
{
  "title": "Interstellar"
}
```

## Örnek 108

Bu durumda:

```js
req.body.title
```

## Örnek 109

değeri bulunur.

Ancak:

```js
req.body.releaseYear
```

## Örnek 110

değeri bulunmaz ve `undefined` olabilir.

Express bu eksikliği

kendiliğinden hata olarak kabul etmek zorunda değildir.

Route yine çalışabilir.

Bu nedenle uygulamamızın şu soruları

kendisi kontrol etmesi gerekir:

```text
Body gönderilmiş mi?

Gerekli alanlar bulunuyor mu?

Alanların veri türleri doğru mu?

Gönderilen değerler kabul edilebilir mi?
```

## Örnek 111

Bu kontrolleri ilerleyen bölümlerde

validation adı verilen doğrulama işlemleriyle yapacağız.

🧠 Akılda Kalsın

```text
Request Body
↓
İstemciden gelen veriyi taşır.

express.json()
↓
JSON Body'yi işler.

req.body
↓
İşlenen veriye ulaşmamızı sağlar.
```

## Örnek 112

## Bölüm Özeti

Bu bölümde bir Request'in yalnızca

HTTP yöntemi ve yoldan oluşmadığını öğrendik.

Sunucuya ek veriler göndermek için

Request Body kullanabileceğimizi gördük.

```text
Request
├── HTTP yöntemi
├── Yol
└── Body
```

## Örnek 113

Verilerimizi JSON biçiminde hazırladık:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "genre": "Science Fiction",
  "rating": 8.7
}
```

## Örnek 114

Postman'de JSON Body göndermek için:

```text
Body
↓
raw
↓
JSON
```

## Örnek 115

seçeneklerini kullandık.

JSON verisinin türünün şu Header ile belirtildiğini öğrendik:

```text
Content-Type: application/json
```

## Örnek 116

Express'in Request Body'yi

kendiliğinden kullanılabilir hâle getirmediğini gördük.

Bunun için:

```js
app.use(express.json());
```

## Örnek 117

satırını ekledik.

`express.json()` gelen JSON Body'yi işledi

ve veriyi:

```js
req.body
```

## Örnek 118

üzerinden erişilebilir hâle getirdi.

Gönderilen alanlara şu şekilde ulaştık:

```js
req.body.title;
req.body.releaseYear;
req.body.genre;
req.body.rating;
```

## Örnek 119

Son olarak aldığımız veriyi

JSON Response olarak geri gönderdik:

```js
res.json({
  message: "Film bilgileri alındı.",
  data: req.body,
});
```

## Örnek 120

Temel veri akışımız artık şöyledir:

```text
Postman
↓
JSON Body

express.json()
↓
Body'yi işler.

req.body
↓
Veriye ulaşılır.

Route
↓
Veri kullanılır.

res.json()
↓
Response gönderilir.
```

## Örnek 121

## Sonraki Adım

Bu bölümde şu satırı kullandık:

```js
app.use(express.json());
```

## Örnek 122

Bu satır bir Route değildir.

Belirli bir adrese doğrudan Response göndermez.

Bunun yerine Request ile Route arasına girer

ve Request üzerinde bir işlem gerçekleştirir.

```text
Request
↓
express.json()
↓
Route
↓
Response
```
