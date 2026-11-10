# Bölüm 17 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Postman ile Request Göndermek

## Tarayıcının Adres Çubuğu Neden Yeterli Değil?

Express uygulamamızda farklı HTTP yöntemlerini karşılayan

Route'lar oluşturabiliyoruz.

```js
app.get("/movies", (req, res) => {
  res.send("Filmler görüntüleniyor.");
});

app.post("/movies", (req, res) => {
  res.send("Yeni film oluşturuluyor.");
});

app.put("/movies/42", (req, res) => {
  res.send("Film tamamen güncelleniyor.");
});

app.patch("/movies/42", (req, res) => {
  res.send("Filmin belirli alanları güncelleniyor.");
});

app.delete("/movies/42", (req, res) => {
  res.send("Film siliniyor.");
});
```

## Örnek 2

Bu Route'ların her biri

farklı bir HTTP yöntemini karşılar:

```text
GET

POST

PUT

PATCH

DELETE
```

## Örnek 3

GET Route'larını tarayıcının adres çubuğundan deneyebiliriz.

Örneğin adres çubuğuna şunu yazabiliriz:

```text
http://localhost:3000/movies
```

## Örnek 4

Tarayıcı bu adrese genellikle GET Request'i gönderir.

Express de aşağıdaki Route'u çalıştırır:

```js
app.get("/movies", (req, res) => {
  res.send("Filmler görüntüleniyor.");
});
```

## Örnek 5

Peki POST Route'unu nasıl deneyeceğiz?

```js
app.post("/movies", (req, res) => {
  res.send("Yeni film oluşturuluyor.");
});
```

## Örnek 6

Adres çubuğuna yine aynı adresi yazarsak:

```text
http://localhost:3000/movies
```

## Örnek 7

tarayıcı genellikle POST değil,

GET Request'i gönderir.

```text
Adres çubuğuna adres yazılır.
↓
Tarayıcı GET Request'i gönderir.
↓
app.get("/movies") Route'u çalışır.
```

## Örnek 8

Bu nedenle adres çubuğunu kullanarak

gönderilecek HTTP yöntemini özgürce seçemeyiz.

Aynı durum PUT, PATCH ve DELETE için de geçerlidir:

```text
POST /movies

PUT /movies/42

PATCH /movies/42

DELETE /movies/42
```

## Örnek 9

Bu Request'leri gönderebilmek için

HTTP yöntemini kendimiz seçebileceğimiz bir araca ihtiyacımız vardır.

Bu aracın adı **Postman**'dır.

## Postman Nedir?

Postman,

HTTP Request'leri oluşturmamızı ve göndermemizi

ve sunucudan dönen Response'ları incelememizi sağlayan bir araçtır.

Postman'i,

sunucuyla iletişim kuran bir istemci gibi düşünebiliriz.

```text
Postman
↓
Request gönderir.

Express uygulaması
↓
Request'i karşılar ve işler.

Postman
↓
Dönen Response'u gösterir.
```

## Örnek 10

Tarayıcı da bir istemcidir.

Ancak Postman,

göndermek istediğimiz HTTP yöntemini

kendimizin seçmesine izin verir:

```text
GET

POST

PUT

PATCH

DELETE
```

## Örnek 11

Postman bizim sunucumuz değildir.

Express uygulamasının yerine geçmez

ve Route'ları kendisi çalıştırmaz.

```text
Postman
↓
Request gönderen istemci

Express
↓
Request'i karşılayan sunucu uygulaması
```

## Örnek 12

Bir Request gönderildiğinde süreç şöyle ilerler:

```text
Postman'de HTTP yöntemi seçilir.
↓
Request'in gideceği adres yazılır.
↓
Request gönderilir.
↓
Express eşleşen Route'u bulur.
↓
Route'un fonksiyonu çalışır.
↓
Response Postman'e döner.
↓
Postman Response'u ekranda gösterir.
```

## Örnek 13

🧠 Akılda Kalsın

```text
Postman
↓
Farklı HTTP yöntemleriyle Request göndermemizi
ve dönen Response'ları incelememizi sağlayan araçtır.
```

## Örnek 14

## Postman'i Hazırlayalım

Bu bölümde Postman'in masaüstü uygulamasını kullanacağız.

Masaüstü uygulamasını bilgisayarımıza kurduktan sonra

Postman'i açalım.

Postman'in arayüzü kullanılan sürüme göre

küçük farklılıklar gösterebilir.

Ancak bir HTTP Request'i oluşturmak için kullanacağımız

temel alanlar değişmez:

```text
HTTP yöntemi

Request adresi

Send düğmesi

Response alanı
```

## Örnek 15

## Postman'de Bir Request Nasıl Oluşturulur?

Postman'de yeni bir HTTP Request sekmesi açtığımızda

karşımıza Request oluşturabileceğimiz bir alan çıkar.

Bir Request göndermek için

üç temel işlem yaparız:

```text
HTTP yöntemini seç

Request adresini yaz

Send düğmesine bas
```

## Örnek 16

<img src="./images/17-postman/01-postman-request-ekrani.png" alt="Postman'de Request oluşturma ekranı" width="720">

*Görsel 17.1 — Postman'de HTTP yöntemi, Request adresi, Send düğmesi, Request ayarları ve Response alanı.*

HTTP yöntemini seçtiğimiz bölümden

göndermek istediğimiz yöntemi belirleriz:

```text
GET

POST

PUT

PATCH

DELETE
```

## Örnek 17

Yanındaki adres alanına

Request'in gideceği tam adresi yazarız:

```text
http://localhost:3000/movies
```

## Örnek 18

Burada önemli bir ayrım vardır:

```text
Tam Request adresi
↓
http://localhost:3000/movies

Route'un yolu
↓
/movies
```

## Örnek 19

`Send` düğmesine bastığımızda

Postman hazırladığımız Request'i sunucuya gönderir.

```text
HTTP yöntemini seç
↓
Adresi yaz
↓
Send düğmesine bas
↓
Response'u incele
```

## Örnek 20

Artık ilk Request'imizi göndermeye hazırız.

## İlk GET Request'imizi Gönderelim

Öncelikle `index.js` dosyamızda

test edeceğimiz Route'ların bulunduğundan emin olalım:

```js
import express from "express";

const app = express();

app.get("/movies", (req, res) => {
  res.send("Filmler görüntüleniyor.");
});

app.post("/movies", (req, res) => {
  res.send("Yeni film oluşturuluyor.");
});

app.put("/movies/42", (req, res) => {
  res.send("Film tamamen güncelleniyor.");
});

app.patch("/movies/42", (req, res) => {
  res.send("Filmin belirli alanları güncelleniyor.");
});

app.delete("/movies/42", (req, res) => {
  res.send("Film siliniyor.");
});

app.listen(3000, () => {
  console.log("Server 3000 portunda çalışıyor.");
});
```

## Örnek 21

Terminalimizi açalım

ve uygulamayı çalıştıralım:

```bash
npm start
```

## Örnek 22

Terminalde şu mesajı görmeliyiz:

```text
Server 3000 portunda çalışıyor.
```

## Örnek 23

Sunucumuz çalışmaya devam ederken

Postman'e geçelim.

HTTP yöntemi olarak GET'i seçelim:

```text
GET
```

## Örnek 24

Adres alanına şunu yazalım:

```text
http://localhost:3000/movies
```

## Örnek 25

Hazırladığımız Request şöyle görünmelidir:

```text
GET http://localhost:3000/movies
```

## Örnek 26

Şimdi `Send` düğmesine basalım.

Postman Request'i Express uygulamamıza gönderir.

Express gelen Request'in yöntemini ve yolunu kontrol eder:

```text
Yöntem
↓
GET

Yol
↓
/movies
```

## Örnek 27

Bu bilgiler aşağıdaki Route ile eşleşir:

```js
app.get("/movies", (req, res) => {
  res.send("Filmler görüntüleniyor.");
});
```

## Örnek 28

Route'un fonksiyonu çalışır

ve Postman'e şu Response gönderilir:

```text
Filmler görüntüleniyor.
```

## Örnek 29

Response alanında ayrıca şu Status Code görünür:

```text
200 OK
```

## Örnek 30

<img src="./images/17-postman/02-get-request-basarili.png" alt="Postman ile başarılı GET Request'i" width="760">

*Görsel 17.2 — `/movies` yoluna gönderilen GET Request'i, `200 OK` Status Code'u ve sunucudan dönen Response.*

Görselde gördüğümüz sonuç,

şu sürecin gerçekleştiğini gösterir:

```text
Postman Request'i gönderdi.
↓
Express Request'i aldı.
↓
Eşleşen GET Route'u bulundu.
↓
Route'un fonksiyonu çalıştı.
↓
Response Postman'e döndü.
```

## Örnek 31

İlk GET Request'imizi

Postman üzerinden başarıyla gönderdik.

## POST Request'i Gönderelim

Şimdi aynı yol üzerinde

farklı bir HTTP yöntemi kullanalım.

Test edeceğimiz Route şudur:

```js
app.post("/movies", (req, res) => {
  res.send("Yeni film oluşturuluyor.");
});
```

## Örnek 32

Bu Route'un yolu yine aynıdır:

```text
/movies
```

## Örnek 33

Ancak karşılayacağı HTTP yöntemi farklıdır:

```text
POST
```

## Örnek 34

Postman'de HTTP yöntemini POST olarak değiştirelim.

Adres alanına yine aynı adresi yazalım:

```text
http://localhost:3000/movies
```

## Örnek 35

Request'imiz şöyle görünmelidir:

```text
POST http://localhost:3000/movies
```

## Örnek 36

`Send` düğmesine basalım.

Express şu iki bilgiyi birlikte kontrol eder:

```text
POST

/movies
```

## Örnek 37

Bu bilgiler aşağıdaki Route ile eşleşir:

```js
app.post("/movies", (req, res) => {
  res.send("Yeni film oluşturuluyor.");
});
```

## Örnek 38

Postman'in Response alanında şu cevabı görmeliyiz:

```text
Yeni film oluşturuluyor.
```

## Örnek 39

Status Code ise:

```text
200 OK
```

## Örnek 40

olur.

GET ve POST Request'lerinin yolları aynıdır:

```text
/movies
```

## Örnek 41

Ancak yöntemleri farklı olduğu için

farklı Route'lar çalışır:

```text
GET /movies
↓
Filmler görüntüleniyor.

POST /movies
↓
Yeni film oluşturuluyor.
```

## Örnek 42

<img src="./images/17-postman/03-ayni-yol-farkli-yontemler.svg" alt="Aynı yolun farklı yöntemlerle kullanılması" width="720">

*Görsel 17.3 — `/movies` yolunun GET ve POST yöntemleriyle iki farklı Route oluşturması.*

HTTP yöntemini değiştirmeyi unutursak

beklediğimiz Route çalışmaz.

Örneğin yöntemi GET olarak bırakırsak:

```text
GET http://localhost:3000/movies
```

## Örnek 43

POST Route'u değil,

GET Route'u çalışır.

🧠 Akılda Kalsın

```text
Yol aynı olabilir.

Ancak yöntem değiştiğinde

eşleşen Route da değişir.
```

## Örnek 44

Şimdilik POST Request'imiz

herhangi bir film bilgisi taşımıyor.

Ayrıca gerçek bir film oluşturmuyor.

Yalnızca POST Route'unu çalıştırıyor

ve bir Response alıyor.

Request ile veri göndermeyi

bir sonraki bölümde öğreneceğiz.

## PUT ve PATCH Request'lerini Gönderelim

Şimdi güncelleme işlemleri için kullanılan

PUT ve PATCH yöntemlerini deneyelim.

Test edeceğimiz Route'lar şunlardır:

```js
app.put("/movies/42", (req, res) => {
  res.send("Film tamamen güncelleniyor.");
});

app.patch("/movies/42", (req, res) => {
  res.send("Filmin belirli alanları güncelleniyor.");
});
```

## Örnek 45

Her iki Route da aynı yolu kullanır:

```text
/movies/42
```

## Örnek 46

Buradaki `42`,

örnek olarak seçtiğimiz filmin kimliğini temsil eder.

### PUT Request'i Gönderelim

Postman'de HTTP yöntemi olarak PUT'u seçelim:

```text
PUT
```

## Örnek 47

Adres alanına şunu yazalım:

```text
http://localhost:3000/movies/42
```

## Örnek 48

Request şu şekilde görünmelidir:

```text
PUT http://localhost:3000/movies/42
```

## Örnek 49

`Send` düğmesine bastığımızda

şu Route çalışır:

```js
app.put("/movies/42", (req, res) => {
  res.send("Film tamamen güncelleniyor.");
});
```

## Örnek 50

Postman'de şu Response'u görmeliyiz:

```text
Film tamamen güncelleniyor.
```

## Örnek 51

### PATCH Request'i Gönderelim

Şimdi HTTP yöntemini PATCH olarak değiştirelim:

```text
PATCH
```

## Örnek 52

Adres aynı kalır:

```text
http://localhost:3000/movies/42
```

## Örnek 53

Request'imiz şöyle görünmelidir:

```text
PATCH http://localhost:3000/movies/42
```

## Örnek 54

`Send` düğmesine bastığımızda

bu kez şu Route çalışır:

```js
app.patch("/movies/42", (req, res) => {
  res.send("Filmin belirli alanları güncelleniyor.");
});
```

## Örnek 55

Postman'de şu Response'u görmeliyiz:

```text
Filmin belirli alanları güncelleniyor.
```

## Örnek 56

Yol değişmedi:

```text
/movies/42
```

## Örnek 57

Ancak HTTP yöntemi değişti:

```text
PUT /movies/42
↓
Film tamamen güncelleniyor.

PATCH /movies/42
↓
Filmin belirli alanları güncelleniyor.
```

## Örnek 58

Bu Route'lar henüz gerçek bir güncelleme işlemi yapmıyor.

Çünkü sunucuya filmin yeni bilgilerini göndermedik

ve herhangi bir veritabanı kullanmadık.

Şimdilik yalnızca farklı HTTP yöntemlerinin

doğru Route'ları çalıştırdığını test ediyoruz.

## DELETE Request'i Gönderelim

Son olarak DELETE yöntemini deneyelim.

Test edeceğimiz Route şudur:

```js
app.delete("/movies/42", (req, res) => {
  res.send("Film siliniyor.");
});
```

## Örnek 59

Postman'de HTTP yöntemi olarak DELETE'i seçelim:

```text
DELETE
```

## Örnek 60

Adres alanına şunu yazalım:

```text
http://localhost:3000/movies/42
```

## Örnek 61

Request şöyle görünmelidir:

```text
DELETE http://localhost:3000/movies/42
```

## Örnek 62

`Send` düğmesine basalım.

Express gelen Request'in yöntemini

ve yolunu kontrol eder:

```text
Yöntem
↓
DELETE

Yol
↓
/movies/42
```

## Örnek 63

Bu bilgiler aşağıdaki Route ile eşleşir:

```js
app.delete("/movies/42", (req, res) => {
  res.send("Film siliniyor.");
});
```

## Örnek 64

Postman'in Response alanında şunu görmeliyiz:

```text
Film siliniyor.
```

## Örnek 65

Bu Response'u görmemiz,

DELETE Route'unun çalıştığını gösterir.

Ancak uygulamamızda henüz gerçek bir film verisi yoktur.

Bu nedenle kodumuz gerçek bir filmi silmez.

Yalnızca DELETE Request'ini karşılar

ve metin biçiminde Response gönderir.

```text
DELETE Request'i gönderildi.
↓
Eşleşen Route bulundu.
↓
Route'un fonksiyonu çalıştı.
↓
"Film siliniyor." Response'u gönderildi.
```

## Örnek 66

Artık Postman ile temel HTTP yöntemlerini gönderebiliyoruz:

```text
GET

POST

PUT

PATCH

DELETE
```

## Örnek 67

## Gönderdiğimiz Request'e Sunucu Nasıl Cevap Veriyor?

Postman'de `Send` düğmesine bastığımızda

Request gerçekten Express uygulamamıza gönderilir.

```text
Postman
↓
Request'i gönderir.

Express
↓
Request'i karşılar.

Route
↓
Yapılacak işlemi belirler.

Response
↓
Postman'e geri döner.
```

## Örnek 68

Örneğin şu Request'i gönderdiğimizi düşünelim:

```text
PATCH http://localhost:3000/movies/42
```

## Örnek 69

Express iki temel bilgiyi kontrol eder:

```text
HTTP yöntemi
↓
PATCH

Yol
↓
/movies/42
```

## Örnek 70

Ardından bu bilgilerle eşleşen Route'u arar:

```js
app.patch("/movies/42", (req, res) => {
  res.send("Filmin belirli alanları güncelleniyor.");
});
```

## Örnek 71

Eşleşen Route bulunduğunda

Route'un fonksiyonu çalışır.

Fonksiyonun içindeki:

```js
res.send("Filmin belirli alanları güncelleniyor.");
```

## Örnek 72

kodu Response'u oluşturur

ve Postman'e gönderir.

```text
Request
↓
PATCH /movies/42

Eşleşen Route
↓
app.patch("/movies/42")

Çalışan kod
↓
res.send(...)

Response
↓
Filmin belirli alanları güncelleniyor.
```

## Örnek 73

Postman'in Response bölümünde

yalnızca gönderilen içeriği görmeyiz.

Response hakkında başka bilgiler de bulunur:

```text
Response Body
↓
Sunucunun gönderdiği içerik

Status Code
↓
Request'in sonucunu belirten kod

Response Time
↓
Cevabın ne kadar sürede geldiği

Response Size
↓
Dönen cevabın boyutu

Headers
↓
Response hakkında ek bilgiler
```

## Örnek 74

Bu bölümde bizi en çok ilgilendiren iki bilgi şunlardır:

```text
Response Body

Status Code
```

## Örnek 75

<img src="./images/17-postman/04-response-alani.png" alt="Postman Response alanının bölümleri" width="720">

*Görsel 17.4 — Postman'in Response alanında Body, Status Code, süre, boyut ve Header bilgileri.*

Örneğin başarılı bir Response şu bilgileri içerebilir:

```text
Status Code
↓
200 OK

Response Body
↓
Filmler görüntüleniyor.
```

## Örnek 76

🧠 Akılda Kalsın

```text
Postman Response oluşturmaz.

Postman Request'i gönderir.

Response'u Express uygulaması oluşturur.

Postman yalnızca dönen Response'u gösterir.
```

## Örnek 77

### Eşleşen Route Bulunamazsa Ne Olur?

Express'in bir Route'u çalıştırabilmesi için

HTTP yöntemi ile yolun birlikte eşleşmesi gerekir.

Uygulamamızda şu Route'un bulunduğunu düşünelim:

```js
app.delete("/movies/42", (req, res) => {
  res.send("Film siliniyor.");
});
```

## Örnek 78

Fakat Postman'den şu Request'i gönderelim:

```text
POST /movies/42
```

## Örnek 79

Uygulamamızda bu yöntem ve yol birleşimiyle eşleşen

bir Route bulunmamaktadır.

```text
POST + /movies/42
↓
Eşleşen Route yok
```

## Örnek 80

Bu durumda Express şuna benzer bir Response gönderir:

```text
Cannot POST /movies/42
```

## Örnek 81

Status Code ise:

```text
404 Not Found
```

## Örnek 82

olur.

<img src="./images/17-postman/05-route-bulunamadi.png" alt="Postman'de eşleşmeyen Route sonucu" width="720">

*Görsel 17.5 — Sunucuya ulaşan fakat HTTP yöntemi ve yoluyla eşleşen Route bulunamayan Request.*

Bu hata,

sunucunun kapalı olduğu anlamına gelmez.

Request sunucuya ulaşmıştır.

Ancak HTTP yöntemi ve yol ile eşleşen

bir Route bulunamamıştır.

```text
404 Not Found
↓
Sunucuya ulaşıldı.

Fakat eşleşen Route bulunamadı.
```

## Örnek 83

### Sunucu Çalışmıyorsa Ne Olur?

Postman'in Request gönderebilmesi için

Express sunucumuzun çalışıyor olması gerekir.

Terminalde uygulamayı başlatmadıysak:

```bash
npm start
```

## Örnek 84

Postman `localhost:3000` adresinde

ulaşabileceği bir sunucu bulamaz.

Yanlış port yazdığımızda da

benzer bir bağlantı problemi oluşur:

```text
Doğru adres
↓
http://localhost:3000

Yanlış port
↓
http://localhost:4000
```

## Örnek 85

<img src="./images/17-postman/06-sunucuya-ulasilamadi.png" alt="Postman'de sunucuya ulaşılamaması" width="720">

*Görsel 17.6 — Express sunucusu çalışmadığında Postman'in bağlantı kuramaması.*

404 ile bağlantı hatası aynı şey değildir:

```text
404 Not Found
↓
Sunucuya ulaşıldı.
Eşleşen Route bulunamadı.

Bağlantı hatası
↓
Sunucuya ulaşılamadı.
```

## Örnek 86

Sorun yaşadığımızda şu bilgileri kontrol etmeliyiz:

```text
Sunucu çalışıyor mu?

Port doğru mu?

HTTP yöntemi doğru mu?

Route yolu doğru mu?
```

## Örnek 87

## Bölüm Özeti

Bu bölümde Postman kullanarak

Express uygulamamıza farklı HTTP yöntemleriyle

Request göndermeyi öğrendik.

Bir Request oluştururken

üç temel adımı kullandık:

```text
HTTP yöntemini seç

Request adresini yaz

Send düğmesine bas
```

## Örnek 88

GET Request'i gönderdik:

```text
GET /movies
```

## Örnek 89

Aynı yola POST Request'i gönderdik:

```text
POST /movies
```

## Örnek 90

Belirli bir film üzerinde işlem yapmak için

PUT, PATCH ve DELETE yöntemlerini kullandık:

```text
PUT /movies/42

PATCH /movies/42

DELETE /movies/42
```

## Örnek 91

Aynı yolun farklı HTTP yöntemleriyle

farklı Route'ları çalıştırabildiğini gördük.

```text
HTTP yöntemi + Yol
↓
Eşleşen Route
```

## Örnek 92

Postman'in Response alanında

sunucudan dönen bilgileri inceledik:

```text
Response Body

Status Code

Response Time

Response Size

Headers
```

## Örnek 93

Ayrıca Postman'in sunucu olmadığını öğrendik:

```text
Postman
↓
Request gönderen istemci

Express
↓
Request'i karşılayan sunucu uygulaması
```

## Örnek 94

Son olarak iki farklı hata durumunu ayırdık:

```text
Eşleşen Route yok
↓
404 Not Found

Sunucu çalışmıyor veya port yanlış
↓
Bağlantı hatası
```

## Örnek 95

Artık Express Route'larımızı

farklı HTTP yöntemleriyle

Postman üzerinden test edebiliriz.

## Sonraki Adım

POST Request'imizi başarıyla gönderdik:

```text
POST /movies
```

## Örnek 96

Ancak bu Request yalnızca

yeni bir film oluşturmak istediğimizi belirtti.

Sunucuya filmin bilgilerini göndermedik.

Örneğin yeni filmin:

```text
Başlığı

Yayın yılı

Türü

Puanı
```

## Örnek 97

gibi bilgilerini Request'e nasıl ekleyeceğiz?

Bir Request yalnızca yöntem ve yoldan mı oluşur?

Yoksa sunucuya veri de taşıyabilir mi?

Yeni bir film oluşturmak için

şuna benzer bilgiler göndermemiz gerekir:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7
}
```
