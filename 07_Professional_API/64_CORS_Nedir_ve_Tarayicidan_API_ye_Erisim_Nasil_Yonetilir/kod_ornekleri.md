# Bölüm 64 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# CORS Nedir ve Tarayıcıdan API'ye Erişim Nasıl Yönetilir?

## Postman'de Çalışıyor, Tarayıcıda Neden Çalışmıyor?

63. bölümde config yapımızı kurduk.

Şimdi gerçek bir frontend senaryosu düşünelim.

Frontend uygulamamız:

```text
http://localhost:5173
```

## Örnek 2

adresinde çalışıyor.

Movie API'miz ise:

```text
http://localhost:3000
```

## Örnek 3

adresinde.

Frontend tarafında:

```js
const response = await fetch(
  "http://localhost:3000/movies",
);
```

## Örnek 4

yazdığımızı düşünelim.

Buradaki `fetch()` browser JavaScript'inden HTTP Request göndermeye yarayan bir Web API'dir. Bu bölümde frontend geliştirmeyi öğrenmeyeceğiz. `fetch()` kullanımını yalnızca CORS problemini gerçek browser ortamında görebilmek için kullanıyoruz.

Aynı endpoint'i Postman'de açtığımızda Response geliyor.

Ama tarayıcıdaki JavaScript CORS ile ilgili bir hata gösterebilir.

İlk tepki şu olabilir:

> API çalışıyor. Postman de çalışıyor. Tarayıcı neden aynı Response'u alamıyor?

Bu bölümün problemi tam olarak budur.

---

## CORS Problemini Önce Anlayalım

### Önce `Origin` Ne Demek?

Bir web adresinin origin'i üç temel parçadan oluşur:

```text
protocol
+
host
+
port
```

## Örnek 5

Örneğin:

```text
http://localhost:5173
```

## Örnek 6

için:

```text
protocol → http
host     → localhost
port     → 5173
```

## Örnek 7

API'miz:

```text
http://localhost:3000
```

## Örnek 8

olduğunda host aynı görünse bile port farklıdır.

Bu nedenle iki adresin origin'i aynı değildir.

```text
http://localhost:5173
≠
http://localhost:3000
```

## Örnek 9

Bu durum:

```text
cross-origin
```

## Örnek 10

yani farklı origin'ler arası iletişimdir.

---

### Tarayıcı Neden Böyle Bir Kontrol Yapıyor?

Tarayıcılar web sayfalarının başka origin'lerdeki kaynaklarla nasıl iletişim kuracağını güvenlik kurallarıyla sınırlar.

Bu sınırın merkezinde:

```text
same-origin policy
```

## Örnek 11

vardır.

Biz burada browser security modelini bütün ayrıntılarıyla incelemeyeceğiz.

Bilmemiz gereken temel fikir şu:

```text
frontend başka origin'de
↓
API başka origin'de
↓
tarayıcı Response'un JavaScript tarafından okunmasına izin verilip verilmediğini kontrol eder
```

## Örnek 12

İşte CORS bu noktada devreye girer.

---

### CORS Nedir?

CORS:

```text
Cross-Origin Resource Sharing
```

## Örnek 13

ifadesinin kısaltmasıdır.

Bunu Türkçe zihinsel model olarak şöyle düşünebiliriz:

> Sunucu Response header'larıyla tarayıcıya, hangi origin'lerin Response'u JavaScript üzerinden okuyabileceğini söyler.

Örneğin API şu header'ı gönderirse:

```http
Access-Control-Allow-Origin: http://localhost:5173
```

## Örnek 14

browser şunu anlayabilir:

```text
bu Response'u
http://localhost:5173 origin'indeki JavaScript okuyabilir
```

## Örnek 15

---

### CORS Browser’ın Response Erişimini Yönetir

CORS’un rolünü doğru konumlandırmak için şu akışı düşünelim.

CORS:

```text
"yalnızca bu domain API'me Request gönderebilir"
```

## Örnek 16

demek değildir.

Sunucu Request'i alabilir ve Response üretebilir.

CORS header'larını kontrol eden taraf çoğunlukla browser'dır.

Bu nedenle:

```text
Postman
curl
başka bir backend server
```

## Örnek 17

CORS kurallarını browser gibi uygulamak zorunda değildir.

Bu yüzden:

```text
Postman çalışıyor
ama browser fetch hata veriyor
```

## Örnek 18

senaryosu normaldir.

En önemli cümle:

> **CORS authentication veya authorization değildir.**

CORS API'mizi yetkisiz kişilerden koruyan kullanıcı kimliği sistemi değildir.

Kimlik doğrulama ve yetkilendirmeyi, serinin devamı olan *Node.js ile Backend Geliştirme: Kullanıcı Yönetimi ve Güvenli API'ler* (**2. Kitap**) içinde aynı Movie API üzerinde ele alacağız.

---

## Express'te CORS'u Kuralım

### CORS Header'larını Elle Yazmak Zorunda mıyız?

Hayır.

Express ekosisteminde yaygın kullanılan:

```text
cors
```

## Örnek 19

Middleware paketini kullanabiliriz.

Terminalde:

```bash
npm install cors
```

## Örnek 20

çalıştıralım.

Ardından `server.js` içinde:

```js
import cors from "cors";
```

## Örnek 21

ile paketi alabiliriz.

---

### İlk Denemede Her Origin'e İzin Versek?

Şöyle yazabiliriz:

```js
app.use(cors());
```

## Örnek 22

Bu kullanım birçok basit senaryoda:

```http
Access-Control-Allow-Origin: *
```

## Örnek 23

gibi geniş bir izin üretebilir.

Yani browser açısından:

```text
birçok origin Response'u okuyabilir
```

## Örnek 24

demektir.

Bu davranışı doğrudan browser'da görmek, CORS kuralının neden browser tarafında ortaya çıktığını netleştirir.

Ama Movie API'mizin gerçek frontend origin'i belliyse daha kontrollü bir yapı kullanmak isteyebiliriz.

---

### Hangi Origin'e İzin Vereceğimiz Kodun İçine Gömülmeli mi?

Şöyle yazabiliriz:

```js
app.use(
  cors({
    origin: "http://localhost:5173",
  }),
);
```

## Örnek 25

Bu development ortamında çalışır.

Ama production frontend adresimiz şöyle olabilir:

```text
https://movies.example.com
```

## Örnek 26

Bu durumda JavaScript dosyasını değiştirmek zorunda kalırız.

63. bölümde tam da bu tür değerler için merkezi config yapısı kurmuştuk.

CORS origin'i de ortama göre değişebilir.

Bu nedenle config'e taşıyalım.

---

### `.env` Dosyasına `CORS_ORIGIN` Ekleyelim

Development ortamında:

```env
MONGODB_URL=...
PORT=3000
CORS_ORIGIN=http://localhost:5173
```

## Örnek 27

kullanabiliriz.

`.env.example` dosyasına da gerçek secret içermeden:

```env
MONGODB_URL=your_mongodb_connection_string
PORT=3000
CORS_ORIGIN=http://localhost:5173
```

## Örnek 28

ekleyelim.

---

### Merkezi Config'i Genişletelim

63. bölümde oluşturduğumuz:

```text
src/config/env.js
```

## Örnek 29

içindeki `env` nesnesine:

```js
corsOrigin: getRequiredEnv(
  "CORS_ORIGIN",
),
```

## Örnek 30

ekleyebiliriz.

İlgili bölüm:

```js
export const env = {
  mongodbUrl: getRequiredEnv(
    "MONGODB_URL",
  ),
  port: parsePort(process.env.PORT),
  corsOrigin: getRequiredEnv(
    "CORS_ORIGIN",
  ),
};
```

## Örnek 31

Şimdi `server.js` gerçek domain adını bilmek zorunda değildir.

Sadece:

```js
env.corsOrigin
```

## Örnek 32

kullanır.

---

### `server.js` İçinde CORS'u Aktifleştirelim

```js
import cors from "cors";
import express from "express";

import { env } from "./config/env.js";
import { requestLogger } from "./middlewares/requestLogger.js";
import moviesRouter from "./routers/movies.js";
```

## Örnek 33

Ardından Express app oluşturulduktan sonra:

```js
app.use(
  cors({
    origin: env.corsOrigin,
  }),
);
```

## Örnek 34

ekleyelim.

Temel yapı:

```js
export const app = express();

app.use(
  cors({
    origin: env.corsOrigin,
  }),
);

app.use(express.json());
app.use(requestLogger);

app.use("/movies", moviesRouter);
```

## Örnek 35

olabilir.

---

### `origin` Seçeneği Ne Yapıyor?

Şu bölüm:

```js
cors({
  origin: env.corsOrigin,
})
```

## Örnek 36

Middleware'e şunu söyler:

```text
CORS Response header'larını
bu origin'e göre oluştur
```

## Örnek 37

Örneğin:

```env
CORS_ORIGIN=http://localhost:5173
```

## Örnek 38

ise uygun Response'ta:

```http
Access-Control-Allow-Origin: http://localhost:5173
```

## Örnek 39

header'ını görebiliriz.

---

## CORS Davranışını Gözlemleyelim

### Postman'de CORS'u Nasıl Gözlemleyebiliriz?

Postman CORS'u browser gibi zorlamaz.

Ama Response header'larını görmemizi sağlar.

Şu Request'i gönderelim:

```http
GET http://localhost:3000/movies
```

## Örnek 40

Headers bölümünde:

```text
Access-Control-Allow-Origin
```

## Örnek 41

header'ını arayabiliriz.

Değer:

```text
http://localhost:5173
```

## Örnek 42

olmalıdır.

Bu test bize CORS Middleware'in Response'a header eklediğini gösterir.

Ama tarayıcının gerçek davranışını görmek için Request'in browser'daki farklı bir origin'den gelmesi gerekir.

---

### Browser Tarafında Akışı Nasıl Düşünmeliyiz?

Frontend origin'i:

```text
http://localhost:5173
```

## Örnek 43

olsun.

JavaScript:

```js
fetch("http://localhost:3000/movies");
```

## Örnek 44

çalıştırır.

Basitleştirilmiş akış:

```text
Browser
↓
API'ye Request
↓
API Response
↓
Access-Control-Allow-Origin header'ı
↓
Browser kontrolü
↓
uygunsa JavaScript Response'u okuyabilir
```

## Örnek 45

Burada CORS kararını uygulayan önemli aktör browser'dır.

---

### İzin Verilmeyen Origin Ne Olur?

Config:

```env
CORS_ORIGIN=http://localhost:5173
```

## Örnek 46

iken başka bir browser origin'inden JavaScript Request geldiğini düşünelim:

```text
http://localhost:5500
```

## Örnek 47

Sunucu Request'i alabilir.

Fakat Response'taki CORS header'ı bu origin'e izin vermiyorsa browser JavaScript'e Response'u açmayabilir.

Yine altını çizelim:

```text
CORS
≠
sunucuya hiç Request gitmez
```

## Örnek 48

Bu ayrım güvenlik açısından çok önemlidir.

---

### `*` Ne Zaman Kullanılır?

CORS'ta:

```text
*
```

## Örnek 49

genellikle:

```text
her origin'e izin ver
```

## Örnek 50

anlamında kullanılır.

Tamamen public ve browser tarafından okunması amaçlanan bir API'de bu karar uygun olabilir.

Ama:

```text
"kolay olsun, her projede * yazalım"
```

## Örnek 51

gibi otomatik bir kural yoktur.

Movie API'mizde belirli bir frontend origin'i olduğunu varsayıp:

```js
origin: env.corsOrigin
```

## Örnek 52

kullanacağız.

---

### CORS'ta Port Neden Önemliydi?

Şu iki adres:

```text
http://localhost:5173
http://localhost:3000
```

## Örnek 53

aynı host'a sahip.

Ama port farklı olduğu için origin farklıdır.

Aynı şekilde:

```text
http://example.com
https://example.com
```

## Örnek 54

protocol farklı olduğu için de origin farklıdır.

Origin karşılaştırırken yalnızca domain adına bakmıyoruz.

---

### Preflight Request Nedir?

Bazı cross-origin Request'lerde browser asıl Request'ten önce:

```http
OPTIONS
```

## Örnek 55

Request'i gönderebilir.

Bu ilk kontrol Request'ine:

```text
preflight
```

## Örnek 56

denir.

Örneğin bazı:

```text
PATCH
DELETE
custom header
```

## Örnek 57

senaryolarında bunu görebiliriz.

Browser kabaca şunu sorar:

```text
"Bu asıl Request'i göndermeme izin var mı?"
```

## Örnek 58

`cors` Middleware'ini:

```js
app.use(cors(...));
```

## Örnek 59

şeklinde application-level kullandığımızda paket preflight akışını da bizim için yönetebilir.

Bu bölümde OPTIONS header ayrıntılarını tek tek ezberlemeyeceğiz.

Bilmemiz gereken:

```text
browser bazen asıl cross-origin Request'ten önce izin kontrolü yapabilir
```

## Örnek 60

olmasıdır.

---

## CORS’un Sınırı: Kimlik Doğrulama Yapmaz

Örneğin şu ayar API erişimini yalnızca tek bir siteyle sınırlamaz:

```text
CORS_ORIGIN=https://benim-site.com
↓
artık API'ye sadece benim site erişebilir
```

## Örnek 61

Hayır.

Bir kişi:

```text
Postman
curl
kendi backend'i
```

## Örnek 62

ile yine API'ye Request gönderebilir.

CORS'un görevi:

```text
browser cross-origin Response erişimi
```

## Örnek 63

ile ilgilidir.

Kullanıcı kimliği ve izin kontrolü:

```text
authentication
authorization
```

## Örnek 64

konularıdır.

CORS bu iki kontrolün yerini tutmaz: API bir isteğe yanıt verip vermemeyi ayrıca değerlendirebilir. Burada yalnızca tarayıcının farklı origin'den gelen yanıtı okuma iznini düzenliyoruz.

---

## Config ile CORS'un Bağlantısı Neden Güzel Oldu?

63. bölümde config yapısını kurmasaydık:

```js
origin: "http://localhost:5173"
```

## Örnek 65

kodun içinde kalacaktı.

Şimdi:

```text
development
↓
CORS_ORIGIN=http://localhost:5173

production
↓
CORS_ORIGIN=https://movies.example.com
```

## Örnek 66

gibi farklı ortamlar aynı JavaScript kodunu kullanabilir.

Bu, 63 ve 64. bölümlerin bilinçli bağlantısıdır.

---

## Proje Akışımız Şimdi Nasıl?

```text
Browser frontend
↓
Cross-origin Request
↓
CORS Middleware
↓
uygun CORS header'ları
↓
diğer Middleware'ler
↓
Router
↓
Controller
↓
Service
↓
Response
↓
Browser CORS header'larını kontrol eder
```

## Örnek 67

CORS:

```text
Controller'ın işi değil
Service'in işi değil
Model'ın işi değil
```

## Örnek 68

Application-level Middleware için uygun bir sorumluluktur.

---

## Bölüm Sonu Uygulaması

1. `npm install cors` komutuyla paketi ekleyin.
2. `.env` ve `.env.example` dosyalarına `CORS_ORIGIN` ekleyin.
3. `env.js` içinde `corsOrigin` config değerini tanımlayın.
4. `server.js` içinde `cors({ origin: env.corsOrigin })` Middleware'ini ekleyin.
5. Postman'de Response Headers altında `Access-Control-Allow-Origin` değerini gözlemleyin.
6. `http://localhost:5173` ve `http://localhost:3000` origin'lerinin neden farklı olduğunu açıklayın.
7. CORS'un neden authentication olmadığını kendi cümlenizle açıklayın.
8. `origin: "*"` kullanmanın neden her API için otomatik doğru karar olmadığını düşünün.

---

## 🧠 Akılda Kalsın

Origin:

```text
protocol + host + port
```

## Örnek 69

ile belirlenir.

CORS:

```text
sunucu Response header'ları
↓
browser kontrolü
↓
JavaScript Response'u okuyabilir mi?
```

## Örnek 70

akışında çalışır.

En önemli sınır:

```text
CORS
≠
authentication

CORS
≠
authorization

CORS
≠
API'ye hiç Request ulaşmaması
```

## Örnek 71

Biz:

```js
cors({
  origin: env.corsOrigin,
})
```

## Örnek 72

ile izin verilen browser origin'ini config üzerinden yönetiyoruz.

---

## Sonraki Adım

CORS'u ekledik.

Fakat artık şunu biliyoruz:

```text
CORS tek başına API güvenliği değildir
```

## Örnek 73

API Response'larımızda güvenlikle ilgili HTTP header'larını daha bilinçli yönetmek isteyebiliriz.

Ayrıca aynı client'ın çok kısa sürede:

```text
1000
10000
çok daha fazla
```

## Örnek 74

Request göndermesi durumunda uygulamaya temel bir sınır koymak isteyebiliriz.

Bunlar iki farklı problemdir:

```text
security headers
↓
Helmet

çok sık Request
↓
Rate Limiting
```
