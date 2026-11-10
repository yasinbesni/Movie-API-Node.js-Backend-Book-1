# Bölüm 15 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Route ile Yeni Yollar Açmak

## Tek Bir Adres Yeterli mi?

İlk Express uygulamamız artık çalışıyor.

Tarayıcıdan şu adrese gittiğimizde:

```text
http://localhost:3000
```

## Örnek 2

karşımıza şu cevap çıkıyor:

```text
Merhaba Express!
```

## Örnek 3

Bu, önemli bir başlangıç.

Ancak gerçek bir web uygulaması yalnızca tek bir adresten oluşmaz.

Bir film uygulamasını düşünün.

Kullanıcı ana sayfayı görüntülemek isteyebilir.

Filmlere bakmak,

uygulama hakkında bilgi almak

veya iletişim sayfasına gitmek isteyebilir.

Bunun için farklı adreslere ihtiyacımız vardır:

```text
/
↓
Ana sayfa

/movies
↓
Filmler

/about
↓
Uygulama hakkında bilgiler

/contact
↓
İletişim bilgileri
```

## Örnek 4

Bu adreslerin hepsi aynı sunucuya ulaşır.

Fakat her biri farklı bir şey ister.

Peki Express,

hangi adrese hangi Response'u göndereceğini nasıl anlayacak?

Şu anda uygulamamızda yalnızca şu kod bulunuyor:

```js
app.get("/", (req, res) => {
  res.send("Merhaba Express!");
});
```

## Örnek 5

Bu kod yalnızca `/` adresine gelen GET Request'lerini karşılar.

Kullanıcı `/movies` adresine giderse,

uygulamamız ona hangi Response'u göndereceğini henüz bilmiyor.

Demek ki Express'e yalnızca nasıl cevap vereceğini değil,

hangi adrese nasıl cevap vereceğini de söylememiz gerekiyor.

İşte bunun için yeni yollar oluşturacağız.

## Express Hangi Adrese Hangi Cevabı Göndereceğini Nasıl Biliyor?

Bir sinemaya girdiğinizi düşünün.

Bir izleyici birinci salona,

başka bir izleyici ise ikinci salona gitmek istiyor.

Görevli herkesi aynı salona göndermez.

Biletin üzerindeki salon numarasına bakar

ve izleyiciyi doğru yere yönlendirir.

Express de gelen Request'leri buna benzer şekilde ayırır.

Her Request'in hangi yöntemle gönderildiğine

ve hangi adrese ulaşmak istediğine bakar.

Örneğin kullanıcı şu adrese bir GET Request'i gönderebilir:

```text
/movies
```

## Örnek 6

Express uygulamamızın içinde bu adres için bir talimat bulunuyorsa,

o talimatı çalıştırır.

```js
app.get("/movies", (req, res) => {
  res.send("Filmler");
});
```

## Örnek 7

Bu kod Express'e şunu söyler:

> `/movies` adresine bir GET Request'i gelirse  
> `"Filmler"` Response'unu gönder.

Ana adres içinse farklı bir talimatımız olabilir:

```js
app.get("/", (req, res) => {
  res.send("Ana Sayfa");
});
```

## Örnek 8

Bu iki Route uygulamada tanımlandığında,

Express iki farklı adresi tanıyabilir:

```text
/
↓
Ana Sayfa

/movies
↓
Filmler
```

## Örnek 9

Kullanıcı `/` adresine giderse ilk kod,

`/movies` adresine giderse ikinci kod çalışır.

Express gelen Request'in yöntemini

ve ulaşmak istediği adresi kontrol eder.

Ardından bu bilgilerle eşleşen kodu bulur

ve çalıştırır.

Böylece aynı uygulama,

farklı adreslere farklı Response'lar gönderebilir.

Peki Express'e verdiğimiz bu yol talimatının bir adı var mı?

Evet.

Şimdi bu yapıyla tanışalım.

## Route Nedir?

Express'e belirli bir Request geldiğinde

ne yapılması gerektiğini söyleyen yapıya **Route** denir.

Bir Route iki temel soruya cevap verir:

```text
Request hangi yöntemle geliyor?

Request hangi adrese geliyor?
```

## Örnek 10

Örneğin:

```js
app.get("/movies", (req, res) => {
  res.send("Filmler");
});
```

## Örnek 11

Bu Route, Express'e şunu söyler:

> `/movies` adresine bir GET Request'i gelirse  
> `"Filmler"` Response'unu gönder.

Route'u,

Express'e verdiğimiz bir yol tarifi gibi düşünebiliriz.

Express, Request'in yöntemini ve yolunu kontrol eder.

Eşleşen bir Route bulduğunda

o Route için belirlediğimiz işlemi çalıştırır.

Peki bir Route bunu nasıl başarıyor?

Bunu anlamak için şimdi Route'u parçalarına ayıralım.

## Bir Route Hangi Parçalardan Oluşur?

Aşağıdaki Route'a yeniden bakalım:

```js
app.get("/movies", (req, res) => {
  res.send("Filmler");
});
```

## Örnek 12

Bu kodu okurken dört farklı yapı görürüz:

```text
app
↓
Express uygulamasına ulaşmamızı sağlayan değişken

get
↓
GET Request'lerini karşılayan Express metodu

"/movies"
↓
Route'un yolu

(req, res) => {}
↓
Eşleşme olduğunda çalışacak fonksiyon
```

## Örnek 13

Burada önemli bir ayrım vardır.

`app`, Route'un temel bilgilerinden biri değildir.

Route'u oluşturduğumuz Express uygulaması üzerinde tanımlamamızı sağlar.

Bir Route'u belirleyen üç temel bilgi şunlardır:

```text
HTTP yöntemi

Yol

Eşleşme olduğunda çalışacak fonksiyon
```

## Örnek 14

Şimdi bu parçaları sırayla inceleyelim.

### `app`

```js
app
```

## Örnek 15

`app`,

oluşturduğumuz Express uygulamasına ulaşmamızı sağlayan değişkendir.

Route'larımızı bu uygulama üzerinde tanımlarız.

### `get`

```js
app.get()
```

## Örnek 16

`get`,

bu Route'un GET Request'lerini karşılayacağını belirtir.

Yani Express yalnızca adrese bakmaz.

Request'in hangi yöntemle gönderildiğini de kontrol eder.

### `"/movies"`

```js
app.get("/movies")
```

## Örnek 17

`"/movies"`,

Route'un adresidir.

Bu adres bazen **path**,

yani **yol** olarak da adlandırılır.

Express yalnızca `/movies` adresine gelen uygun Request'lerde

bu Route'u çalıştırır.

### `(req, res) => {}`

```js
(req, res) => {
  res.send("Filmler");
}
```

## Örnek 18

Bu fonksiyon,

GET yöntemi ile `/movies` adresine bir Request geldiğinde çalışır.

Fonksiyonun içindeki:

```text
req
↓
Gelen Request'i temsil eder.

res
↓
Response oluşturmak ve göndermek için kullanılır.
```

## Örnek 19

Biz bu örnekte `res.send()` metodunu kullanarak

kullanıcıya şu cevabı gönderiyoruz:

```text
Filmler
```

## Örnek 20

Route'un çalışma sırasını şöyle gösterebiliriz:

```text
GET Request
↓
/movies adresine gelir.
↓
Express eşleşen Route'u bulur.
↓
Route'un fonksiyonu çalışır.
↓
"Filmler" Response'u gönderilir.
```

## Örnek 21

🧠 Akılda Kalsın

```text
app.get("/movies", (req, res) => {
  res.send("Filmler");
});
```

## Örnek 22



```text
get
↓
GET Request'lerini karşılayan Express metodu

"/movies"
↓
Adres

(req, res) => {}
↓
Çalışacak fonksiyon
```

## Örnek 23

Artık bir Route'un hangi parçalardan oluştuğunu biliyoruz.

Şimdi uygulamamıza yeni bir Route ekleyerek

bu yapıyı birlikte kullanalım.

## İlk Yeni Route'umuzu Uygulamaya Ekleyelim

Şimdiye kadar aşağıdaki Route'u örnek olarak inceledik:

```js
app.get("/movies", (req, res) => {
  res.send("Filmler");
});
```

## Örnek 24

Bu örnek üzerinden Route'un ne olduğunu

ve hangi parçalardan oluştuğunu öğrendik.

Ancak bu kodu henüz `index.js` dosyamıza eklemedik.

Uygulamamızın içinde şu anda yalnızca ana sayfa Route'u bulunuyor:

```js
app.get("/", (req, res) => {
  res.send("Merhaba Express!");
});
```

## Örnek 25

Şimdi incelediğimiz `/movies` Route'unu

gerçekten uygulamamıza ekleyelim.

Ana sayfa Route'unun altına şu kodu yazalım:

```js
app.get("/movies", (req, res) => {
  res.send("Filmler");
});
```

## Örnek 26

Dosyamızın tamamı artık şöyle görünmelidir:

```js
import express from "express";

const app = express();

app.get("/", (req, res) => {
  res.send("Merhaba Express!");
});

app.get("/movies", (req, res) => {
  res.send("Filmler");
});

app.listen(3000, () => {
  console.log("Server 3000 portunda çalışıyor.");
});
```

## Örnek 27

## Her Yol Farklı Bir Response Gönderebilir mi?

Uygulamamız artık iki farklı yolu tanıyor:

```text
/
↓
Merhaba Express!

/movies
↓
Filmler
```

## Örnek 28

Her Route kendi adresine gelen Request'i karşılıyor

ve kendi Response'unu gönderiyor.

Bu durumda uygulamamıza daha fazla Route ekleyebilir miyiz?

Elbette.

Şimdi kullanıcıya uygulamamız hakkında bilgi veren

yeni bir yol oluşturalım:

```text
/about
```

## Örnek 29

`/movies` Route'unun altına şu kodu ekleyelim:

```js
app.get("/about", (req, res) => {
  res.send("Uygulama hakkında bilgiler");
});
```

## Örnek 30

Ardından iletişim sayfası için bir Route daha oluşturalım:

```js
app.get("/contact", (req, res) => {
  res.send("İletişim bilgileri");
});
```

## Örnek 31

`index.js` dosyamız artık şöyle görünmelidir:

```js
import express from "express";

const app = express();

app.get("/", (req, res) => {
  res.send("Merhaba Express!");
});

app.get("/movies", (req, res) => {
  res.send("Filmler");
});

app.get("/about", (req, res) => {
  res.send("Uygulama hakkında bilgiler");
});

app.get("/contact", (req, res) => {
  res.send("İletişim bilgileri");
});

app.listen(3000, () => {
  console.log("Server 3000 portunda çalışıyor.");
});
```

## Örnek 32

Yaptığımız değişikliklerin çalışabilmesi için

sunucuyu yeniden başlatalım.

Sunucu hâlâ çalışıyorsa terminalde:

```text
Ctrl + C
```

## Örnek 33

tuşlarına basalım.

Ardından uygulamayı yeniden çalıştıralım:

```bash
npm start
```

## Örnek 34

Bu komut,

10. bölümde `package.json` içine eklediğimiz:

```json
"start": "node index.js"
```

## Örnek 35

script'ini çalıştırır.

Şimdi Route'larımızı tarayıcıdan deneyebiliriz.

Ana sayfa:

```text
http://localhost:3000
```

## Örnek 36

Ekranda:

```text
Merhaba Express!
```

## Örnek 37

Filmler:

```text
http://localhost:3000/movies
```

## Örnek 38

Ekranda:

```text
Filmler
```

## Örnek 39

Hakkında:

```text
http://localhost:3000/about
```

## Örnek 40

Ekranda:

```text
Uygulama hakkında bilgiler
```

## Örnek 41

İletişim:

```text
http://localhost:3000/contact
```

## Örnek 42

Ekranda:

```text
İletişim bilgileri
```

## Örnek 43

Bütün Request'ler aynı Express uygulamasına ulaşıyor.

Ancak Request'in adresi değiştiğinde

çalışan Route ve gönderilen Response da değişiyor.

```text
GET /
↓
Ana sayfa Route'u çalışır.

GET /movies
↓
Filmler Route'u çalışır.

GET /about
↓
Hakkında Route'u çalışır.

GET /contact
↓
İletişim Route'u çalışır.
```

## Örnek 44

Express gelen Request'in yöntemini ve adresini kontrol eder.

Ardından bu bilgilerle eşleşen Route'u çalıştırır.

Böylece tek bir Express uygulaması,

birçok farklı adrese farklı Response'lar gönderebilir.

Peki kullanıcı uygulamamızda hiç tanımlamadığımız bir adrese giderse ne olur?

## Tanımlanmayan Bir Adrese Gidilirse Ne Olur?

Uygulamamızda şu yolları tanımladık:

```text
/

/movies

/about

/contact
```

## Örnek 45

Express bu adreslere gelen GET Request'lerini karşılayabilir.

Peki kullanıcı uygulamamızda bulunmayan bir adrese giderse ne olur?

Örneğin tarayıcımıza şu adresi yazalım:

```text
http://localhost:3000/profile
```

## Örnek 46

Uygulamamızda `/profile` adresi için herhangi bir Route bulunmuyor.

Bu nedenle tarayıcıda şu mesaja benzer bir cevap görürüz:

```text
Cannot GET /profile
```

## Örnek 47

Bu mesaj,

sunucunun çalışmadığı anlamına gelmez.

Request sunucuya ulaşmıştır.

Ancak Express,

GET yöntemi ve `/profile` adresiyle eşleşen bir Route bulamamıştır.

```text
GET Request
↓
/profile adresine gelir.
↓
Express eşleşen Route'u arar.
↓
Eşleşen Route bulunamaz.
↓
404 Response gönderilir.
```

## Örnek 48

Daha önce öğrendiğimiz `404` Status Code'u,

istenen kaynağın bulunamadığını belirtir.

Buradaki sorun,

`/profile` adresi için eşleşen bir Route tanımlanmamış olmasıdır.

Yeni adresin çalışmasını istiyorsak

o adres için bir Route tanımlamamız gerekir:

```js
app.get("/profile", (req, res) => {
  res.send("Profil");
});
```

## Örnek 49

Bu Route eklendikten sonra Express,

`/profile` adresine gelen GET Request'ini karşılayabilir.

🧠 Akılda Kalsın

```text
Eşleşen Route varsa
↓
Route'un içindeki işlem çalışır.

Eşleşen Route yoksa
↓
404 Response gönderilir.
```

## Örnek 50

Şimdilik Express'in tanımlanmayan adreslere verdiği varsayılan cevabı görmemiz yeterlidir.

İlerleyen bölümlerde kendi 404 mesajımızı nasıl oluşturacağımızı

ve hataları nasıl yöneteceğimizi ayrıca öğreneceğiz.

## Bölüm Özeti

Bu bölümde bir Express uygulamasının

yalnızca tek bir adresten oluşmak zorunda olmadığını gördük.

Farklı adreslere gelen Request'leri karşılamak için

Route adı verilen yapıları kullandık.

Bir Route'u belirleyen üç temel bilgiyi inceledik:

```text
Yöntem
↓
GET

Adres
↓
/movies

Çalışacak fonksiyon
↓
(req, res) => {}
```

## Örnek 51

`/movies`, `/about` ve `/contact` gibi

farklı yollar oluşturduk.

Her Route'un kendi Response'unu gönderebildiğini gördük.

Express'in gelen Request'in hem yöntemini

hem de adresini kontrol ettiğini öğrendik.

Son olarak tanımlanmamış bir adrese gidildiğinde

eşleşen Route bulunamadığını

ve `404` Response gönderildiğini gördük.

🧠 Akılda Kalsın

```text
Route
↓
Belirli bir yöntem ve adrese gelen Request karşısında
hangi işlemin yapılacağını belirler.
```

## Örnek 52



```text
Yöntem + Adres
↓
Eşleşen Route
↓
Çalışacak işlem
↓
Response
```

## Örnek 53

## Sonraki Adım

Şimdiye kadar oluşturduğumuz bütün Route'larda

GET yöntemini kullandık:

```js
app.get("/movies", (req, res) => {
  res.send("Filmler");
});
```

## Örnek 54

GET yöntemi,

sunucudan bir bilgi veya kaynak istememizi sağladı.

Ancak gerçek bir uygulamada kullanıcı yalnızca bilgi istemez.

Yeni bir film eklemek,

mevcut bir filmi güncellemek

veya bir filmi silmek de isteyebilir.

Peki bütün bu işlemler GET yöntemiyle mi yapılır?

Hayır.

HTTP,

farklı işlemleri belirtmek için farklı yöntemler sunar:

```text
GET

POST

PUT

PATCH

DELETE
```
