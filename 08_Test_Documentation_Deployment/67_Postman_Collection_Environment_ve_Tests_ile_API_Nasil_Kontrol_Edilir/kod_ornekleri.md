# Bölüm 67 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Postman Collection, Environment ve Tests ile API Nasıl Kontrol Edilir?

## Aynı Kontrolleri Her Değişiklikten Sonra Elle Yapmak Zorlaşıyor

Movie API'de küçük bir değişiklik yaptığımızı düşünelim.

Örneğin `getMoviesService()` içindeki Query'yi değiştirdik.

Uygulama açılıyor. `GET /movies` Request'i de Response döndürüyor.

Ama yalnızca buna bakarak bütün CRUD akışının hâlâ doğru çalıştığından emin olamayız.

Kontrol etmek istersek Postman'de sırayla şunları deneyebiliriz:

```text
GET /movies
↓
200 dönüyor mu?

POST /movies
↓
201 dönüyor mu?

GET /movies/:movieId
↓
doğru Movie geliyor mu?

PATCH /movies/:movieId
↓
alan gerçekten değişiyor mu?

DELETE /movies/:movieId
↓
doğru Movie siliniyor mu?
```

## Örnek 2

Bunu bir kez yapmak kolaydır.

Fakat kod her değiştiğinde aynı Request'leri açıp `Send` butonuna basmak, status code'u ve Response Body'yi gözle kontrol etmek zamanla yorucu hâle gelir.

Daha önemlisi bir adımı unutabiliriz.

Örneğin:

> "PATCH'i kontrol ettim ama geçersiz POST Request'ini denedim mi?"

Bu bölümde amacımız Postman'de yaptığımız bu kontrolleri **kaydetmek** ve tekrar çalıştırılabilir hâle getirmektir.

Bunun için üç Postman özelliğini birlikte kullanacağız:

```text
Collection
↓
Movie API Request'lerini bir arada tutacak

Environment
↓
localhost gibi değişebilen adresleri tek yerde tutacak

Post-response testleri
↓
Response'un beklediğimiz gibi olup olmadığını otomatik kontrol edecek
```

## Örnek 3

Bunları sırayla kuralım.

---

## Önce Movie API İçin Bir Collection Oluşturalım

Bu bölümde `POST`, `PATCH` ve `DELETE` isteklerini bir zincir hâlinde çalıştıracağız. Denemeden önce **gerçek kayıtların değil, test amaçlı filmlerin bulunduğu bir veritabanına** bağlandığımızdan emin olalım. Yanlış ortamı seçmek, otomatik testlerin gerçek kayıtları değiştirmesine veya silmesine neden olabilir.

Şimdiye kadar Postman'i çoğunlukla tek tek Request göndermek için kullandık.

Fakat şu Request'lerin tamamı aynı projeye ait:

```text
GET Movies
POST Movie
GET Movie By Id
PATCH Movie
DELETE Movie
```

## Örnek 4

Postman'de **Collection**, ilişkili Request'leri aynı yerde toplamamızı sağlar.

Movie API için bir Collection oluşturalım.

Postman'de:

1. Sol taraftaki **Collections** bölümünü açın.
2. Yeni bir Collection oluşturun.
3. Adını `Movie API` yapın.

Ardından daha önce kullandığımız Request'leri bu Collection içine kaydedebiliriz.

İlk hâlimiz şöyle görünebilir:

```text
Movie API
├── GET Movies
├── POST Movie
├── GET Movie By Id
├── PATCH Movie
└── DELETE Movie
```

## Örnek 5

Şimdilik yalnızca Request'leri aynı yerde topladık.

Henüz otomatik bir kontrol yapmıyoruz.

---

## URL'nin Aynı Kısmını Her Request'te Tekrar Yazıyoruz

`GET Movies` Request'imiz:

```http
GET http://localhost:3000/movies
```

## Örnek 6

`POST Movie` Request'imiz:

```http
POST http://localhost:3000/movies
```

## Örnek 7

`GET Movie By Id` Request'imiz de örneğin:

```http
GET http://localhost:3000/movies/68c9...
```

## Örnek 8

Üç Request'te de aynı bölüm tekrar ediyor:

```text
http://localhost:3000
```

## Örnek 9

Şu an API kendi bilgisayarımızda çalıştığı için bu adres doğru.

Ama ileride API canlı bir adrese taşındığında:

```text
http://localhost:3000
```

## Örnek 10

yerine örneğin:

```text
https://movie-api.example.com
```

## Örnek 11

kullanabiliriz.

Her Request'in URL'sini tek tek değiştirmek yerine bu ortak değere bir isim verebiliriz.

Postman'de bunu **Environment variable** ile yapacağız.

---

## Postman Environment Nedir?

Bu bölümde Environment'ı çok basit bir amaçla kullanacağız:

> Request'lerde tekrar kullandığımız ve çalıştığımız ortama göre değişebilen değerleri tek yerde tutmak.

Örneğin şu bilgiyi saklayabiliriz:

```text
baseUrl = http://localhost:3000
```

## Örnek 12

Sonra Request URL'sini:

```http
GET {{baseUrl}}/movies
```

## Örnek 13

şeklinde yazabiliriz.

Buradaki:

```text
{{baseUrl}}
```

## Örnek 14

Postman'e şunu söyler:

> `baseUrl` adlı variable'ın değerini bul ve buraya yerleştir.

Eğer aktif Environment içinde:

```text
baseUrl = http://localhost:3000
```

## Örnek 15

varsa Postman gerçekte şu Request'i gönderir:

```http
GET http://localhost:3000/movies
```

## Örnek 16

Yani `{{baseUrl}}` server'a aynen gönderilmez. Postman Request gönderilmeden önce variable'ın değerini yerine koyar.

---

## `Local` Environment'ını Postman'de Oluşturalım

Kavramı gördük. Şimdi Postman'de gerçekten oluşturalım.

Güncel Postman arayüzünde Environment oluşturmanın birden fazla yolu bulunabilir. Temel akış şöyledir:

1. **Environments** bölümünü açın veya üst taraftaki Environment seçicisinden yeni Environment oluşturun.
2. Adını `Local` yapın.
3. Yeni bir variable ekleyin.
4. Variable adı olarak `baseUrl` yazın.
5. Değer olarak `http://localhost:3000` yazın.

Elimizde şu yapı vardır:

```text
Local
└── baseUrl = http://localhost:3000
```

## Örnek 17

### `Local` Environment'ını Aktif Edelim

Environment'ı oluşturmak yeterli değildir.

Postman'in Request gönderirken hangi Environment'ı kullanacağını da bilmesi gerekir.

Üst taraftaki Environment seçicisinden:

```text
Local
```

## Örnek 18

Environment'ını aktif hâle getirin.

Şimdi `GET Movies` Request'inin URL'sini:

```http
GET {{baseUrl}}/movies
```

## Örnek 19

olarak değiştirip **Send** butonuna basın.

API yine çalışıyorsa ilk bağlantıyı kurduk:

```text
{{baseUrl}}
↓
aktif Local Environment
↓
http://localhost:3000
↓
GET http://localhost:3000/movies
```

## Örnek 20

Eğer Postman `baseUrl` değerini bulamıyorsa önce şunları kontrol edin:

```text
Local Environment var mı?
↓
baseUrl değeri yazıldı mı?
↓
Local aktif mi?
```

## Örnek 21

---

## Node.js Environment ile Postman Environment Nerede Kullanılır?

63. bölümde Node.js tarafında Environment Variables kullanmıştık:

```env
PORT=3000
MONGODB_URL=...
```

## Örnek 22

Uygulamamız bu değerleri:

```js
process.env.PORT
```

## Örnek 23

üzerinden okuyabiliyordu.

Postman Environment ise Node.js uygulamasının bir parçası değildir.

İkisini yan yana koyalım:

| Yapı | Kim kullanır? | Örnek |
|---|---|---|
| Node.js Environment Variable | Movie API | `PORT=3000` |
| Postman Environment Variable | Postman Request'leri | `baseUrl=http://localhost:3000` |

Node.js tarafındaki `PORT`, server'ın hangi portta dinleyeceğini belirler.

Postman tarafındaki `baseUrl` ise Request'in hangi adrese gönderileceğini belirler.

---

## Şimdi İlk Otomatik Kontrolümüzü Yazalım

`GET Movies` Request'ini gönderdiğimizde gözümüzle şuna bakabiliriz:

```text
Status: 200 OK
```

## Örnek 24

Fakat her seferinde gözümüzle kontrol etmek yerine Postman'e beklentimizi yazabiliriz:

> `GET Movies` Request'i çalıştığında Response status code `200` olmalı.

Bunun için `GET Movies` Request'ini açın ve:

```text
Scripts
↓
Post-response
```

## Örnek 25

alanına geçin.

**Post-response**, Response geldikten sonra çalışan JavaScript kodudur.

Akış şöyledir:

```text
Send
↓
Postman Request'i gönderir
↓
Movie API Response döndürür
↓
Postman Response'u alır
↓
Post-response script çalışır
```

## Örnek 26

Şu testi yazalım:

```js
pm.test(
  "Status 200 olmalı",
  function () {
    pm.response.to.have.status(200);
  },
);
```

## Örnek 27

Bu kodda ilk kez:

```js
pm
```

## Örnek 28

ile karşılaşıyoruz.

Burada durup önce `pm`nin ne olduğunu anlayalım.

---

## `pm` Nedir?

`pm`, bizim tanımladığımız bir variable değildir.

Şöyle bir kod yazmadık:

```js
const pm = ...;
```

## Örnek 29

Bir npm package da import etmedik.

Postman, kendi script alanında JavaScript çalıştırırken `pm` adlı nesneyi hazır olarak verir.

Bu nesne üzerinden Postman'in o Request'le ilgili özelliklerine erişebiliriz.

Bu bölümde kullanacağımız parçalar şunlardır:

```text
pm.response
↓
şu anki HTTP Response

pm.test()
↓
bir test tanımlama

pm.expect()
↓
bir değer için beklenti yazma

pm.collectionVariables
↓
Collection variable'larını okuma veya değiştirme
```

## Örnek 30

Kısaca:

```text
pm
↓
Postman'in script içinde kullanmamız için verdiği hazır nesne
```

## Örnek 31

`pm`yi biz oluşturmayız; Postman sağlar.

---

## İlk Testi Satır Satır Okuyalım

Kodumuz:

```js
pm.test(
  "Status 200 olmalı",
  function () {
    pm.response.to.have.status(200);
  },
);
```

## Örnek 32

Şu bölüm:

```js
pm.test(...)
```

## Örnek 33

bir test tanımlar.

İlk değer:

```text
Status 200 olmalı
```

## Örnek 34

testin adıdır.

İçerideki:

```js
pm.response
```

## Örnek 35

mevcut Request'in Response'unu temsil eder.

Son kısım:

```js
.to.have.status(200)
```

## Örnek 36

beklentimizi belirtir.

Yani bütün kodu Türkçe okursak:

> `Status 200 olmalı` adlı bir test çalıştır. Mevcut Response'un status code değeri 200 değilse test başarısız olsun.

Request'i tekrar gönderdiğimizde Postman test sonucunu **Passed** veya **Failed** olarak gösterir.

Artık sadece Response'a bakmıyoruz; beklentimizi de kaydetmiş oluyoruz.

---

## Response Body'yi de Kontrol Edelim

`200 OK` dönmesi tek başına listenin doğru yapıda olduğunu göstermez.

61. bölümde liste Response'umuzun `data` alanında Movie listesini taşımasına karar vermiştik.

Bu nedenle `data` değerinin Array olmasını da kontrol edebiliriz.

Önce Response Body'yi JavaScript nesnesine çevirelim:

```js
const body = pm.response.json();
```

## Örnek 37

`pm.response.json()` mevcut JSON Response Body'yi okumamızı sağlar.

Sonra testimizi yazalım:

```js
pm.test(
  "data bir Array olmalı",
  function () {
    const body = pm.response.json();

    pm.expect(body.data)
      .to.be.an("array");
  },
);
```

## Örnek 38

Buradaki:

```js
pm.expect(body.data)
```

## Örnek 39

şunu söyler:

> `body.data` değeriyle ilgili bir beklenti yazacağım.

Beklentimiz:

```js
.to.be.an("array")
```

## Örnek 40

olduğu için test `data` alanının Array olup olmadığını kontrol eder.

`GET Movies` Request'imiz artık iki şeyi otomatik kontrol ediyor:

```text
status === 200
ve
data bir Array
```

## Örnek 41

---

## Bir Sonraki Problem: POST'un Ürettiği `_id` Değeri

Şimdi gerçek CRUD akışına geçelim.

`POST Movie` ile yeni bir film oluşturduğumuzda MongoDB yeni bir `_id` üretir.

Örneğin Response:

```json
{
  "message": "Film oluşturuldu.",
  "data": {
    "_id": "68c9...",
    "title": "Postman Test",
    "releaseYear": 2024,
    "voteAverage": 8.4
  }
}
```

## Örnek 42

Sonraki Request'te aynı filmi getirmek için bu değere ihtiyacımız vardır:

```http
GET /movies/68c9...
```

## Örnek 43

Her çalıştırmada `_id` değişeceği için değeri elle kopyalamak istemiyoruz.

İstediğimiz akış şudur:

```text
POST Movie
↓
Response'taki _id'yi al
↓
movieId adıyla sakla
↓
GET /movies/{{movieId}}
↓
PATCH /movies/{{movieId}}
↓
DELETE /movies/{{movieId}}
```

## Örnek 44

Bu kez değer çalıştığımız ortama ait değildir. Movie API Collection'ındaki test akışı sırasında oluşur.

Bu nedenle `movieId` için **Collection variable** kullanacağız.

---

## `baseUrl` ile `movieId` Neden Aynı Yerde Tutulmuyor?

İki değerin görevi farklıdır.

```text
baseUrl
↓
Local veya Production ortamına göre değişir
↓
Environment variable
```

## Örnek 45



```text
movieId
↓
POST Request'i çalışınca oluşur
↓
aynı Collection'daki sonraki Request'lerde kullanılır
↓
Collection variable
```

## Örnek 46

Bu bölüm için bütün Postman variable scope'larını ezberlememiz gerekmiyor.

Şu ayrım yeterlidir:

> `baseUrl` çalıştığımız ortama ait; `movieId` ise bu Collection'ın CRUD akışı sırasında üretilen geçici bir değerdir.

---

## POST Movie: Filmi Oluşturalım ve `movieId`yi Saklayalım

`POST Movie` Request'i:

```http
POST {{baseUrl}}/movies
```

## Örnek 47

Body:

```json
{
  "title": "Postman Test {{$randomUUID}}",
  "releaseYear": 2024,
  "voteAverage": 8.4
}
```

## Örnek 48

`{{$randomUUID}}`, Postman'in her çalıştırmada yeni bir UUID üretebilen dinamik variable'larından biridir.

Bu sayede test filmi her çalıştırmada farklı bir başlıkla oluşturulabilir.

Önce status code'u kontrol edelim:

```js
pm.test(
  "Film 201 ile oluşturulmalı",
  function () {
    pm.response.to.have.status(201);
  },
);
```

## Örnek 49

Aynı Post-response script'in devamında oluşan `_id` değerini saklayalım:

```js
const body = pm.response.json();

pm.collectionVariables.set(
  "movieId",
  body.data._id,
);
```

## Örnek 50

Bu kodun yaptığı iş:

```text
pm.response.json()
↓
POST Response Body'sini oku
↓
body.data._id
↓
yeni Movie'nin id değerini al
↓
pm.collectionVariables.set(...)
↓
movieId adıyla Collection içinde sakla
```

## Örnek 51

`set()` burada değeri kaydeder.

Postman'de `Movie API` Collection'ının **Variables** bölümünü açarsanız `movieId` değerinin oluştuğunu görebilirsiniz.

Bu kontrol özellikle ilk kurulumda faydalıdır. `movieId` boşsa sonraki GET Request'i de doğru çalışmayacaktır.

---

## GET Movie By Id: Kaydettiğimiz `movieId`yi Kullanalım

Request URL'si:

```http
GET {{baseUrl}}/movies/{{movieId}}
```

## Örnek 52

Burada Postman iki değeri farklı yerlerden bulur:

```text
{{baseUrl}}
↓
aktif Local Environment

{{movieId}}
↓
Movie API Collection variable
```

## Örnek 53

Post-response testlerimiz:

```js
pm.test(
  "Film 200 ile gelmeli",
  function () {
    pm.response.to.have.status(200);
  },
);

pm.test(
  "Dönen id oluşturulan filmle aynı olmalı",
  function () {
    const body = pm.response.json();
    const movieId =
      pm.collectionVariables.get("movieId");

    pm.expect(body.data._id)
      .to.eql(movieId);
  },
);
```

## Örnek 54

Bu kez:

```js
pm.collectionVariables.get("movieId")
```

## Örnek 55

ile daha önce `set()` ile kaydettiğimiz değeri okuyoruz.

Kısa bağlantı:

```text
set()
↓
değeri sakla

get()
↓
saklanan değeri oku
```

## Örnek 56

İkinci test yalnızca `200` dönmesini değil, gelen Movie'nin gerçekten POST ile oluşturduğumuz Movie olmasını da kontrol eder.

---

## PATCH Movie: Status ile Birlikte Değişen Veriyi de Kontrol Edelim

Request:

```http
PATCH {{baseUrl}}/movies/{{movieId}}
```

## Örnek 57

Body:

```json
{
  "voteAverage": 9.1
}
```

## Örnek 58

Post-response testleri:

```js
pm.test(
  "Film 200 ile güncellenmeli",
  function () {
    pm.response.to.have.status(200);
  },
);

pm.test(
  "voteAverage 9.1 olmalı",
  function () {
    const body = pm.response.json();

    pm.expect(body.data.voteAverage)
      .to.eql(9.1);
  },
);
```

## Örnek 59

Burada iki farklı soruya cevap alıyoruz:

```text
200 döndü mü?
↓
Request başarıyla tamamlandı mı?

voteAverage 9.1 oldu mu?
↓
Movie gerçekten istediğimiz şekilde güncellendi mi?
```

## Örnek 60

Bu ayrım önemlidir. Yalnızca status code'u kontrol etmek Response içindeki verinin doğru olduğunu garanti etmez.

---

## Geçersiz Body'yi de Test Edelim

Sadece başarılı senaryoları çalıştırmak validation katmanımızın doğru davrandığını göstermez.

Yeni bir Request kaydedelim:

```text
POST Movie - Invalid Body
```

## Örnek 61

URL:

```http
POST {{baseUrl}}/movies
```

## Örnek 62

Body:

```json
{
  "title": "   ",
  "releaseYear": "2024"
}
```

## Örnek 63

59 ve 60. bölümlerde kurduğumuz validation nedeniyle bu Request geçersizdir.

Beklentimiz:

```http
400 Bad Request
```

## Örnek 64

ve hata ayrıntılarının `errors` Array'i içinde bulunmasıdır.

Testler:

```js
pm.test(
  "Geçersiz Body 400 dönmeli",
  function () {
    pm.response.to.have.status(400);
  },
);

pm.test(
  "Validation errors boş olmamalı",
  function () {
    const body = pm.response.json();

    pm.expect(body.errors)
      .to.be.an("array")
      .that.is.not.empty;
  },
);
```

## Örnek 65

Burada daha önce kurduğumuz birkaç parça birlikte doğrulanmış oluyor:

```text
Joi Schema
↓
Validation Middleware
↓
Response sözleşmesi
↓
400 + errors
↓
Postman testi
```

## Örnek 66

---

## DELETE Movie: Oluşturduğumuz Test Verisini Temizleyelim

Request:

```http
DELETE {{baseUrl}}/movies/{{movieId}}
```

## Örnek 67

61. bölümde başarılı DELETE Response'u için:

```text
200 OK
+
silinen Movie verisi
```

## Örnek 68

kararını vermiştik.

Bu nedenle:

```js
pm.test(
  "Film 200 ile silinmeli",
  function () {
    pm.response.to.have.status(200);
  },
);

pm.test(
  "Silinen film doğru id'ye sahip olmalı",
  function () {
    const body = pm.response.json();
    const movieId =
      pm.collectionVariables.get("movieId");

    pm.expect(body.data._id)
      .to.eql(movieId);
  },
);
```

## Örnek 69

kullanabiliriz.

İstersek DELETE Request'inden sonra bir `GET Movie By Id - After Delete` Request'i daha ekleyip:

```http
GET {{baseUrl}}/movies/{{movieId}}
```

## Örnek 70

için:

```http
404 Not Found
```

## Örnek 71

bekleyebiliriz.

Bu ek kontrol yalnızca "DELETE 200 döndü" demekle kalmaz; Movie'nin gerçekten artık bulunamadığını da doğrular.

---

## Artık Elimizde Bir CRUD Kontrol Zinciri Var

Kurulan akışı tek yerde görelim:

```text
POST Movie
↓
201
↓
Response'taki _id → movieId

GET Movie By Id
↓
200
↓
gelen _id === movieId

PATCH Movie
↓
200
↓
voteAverage === 9.1

POST Movie - Invalid Body
↓
400
↓
errors boş değil

DELETE Movie
↓
200
↓
silinen _id === movieId
```

## Örnek 72

Bu noktaya kadar Request'leri tek tek `Send` ile çalıştırabiliriz.

Ama asıl hedefimiz bu zinciri tek seferde çalıştırmaktır.

---

## Collection Runner ile Hepsini Sırayla Çalıştıralım

Postman'in **Collection Runner** özelliği Collection içindeki Request'leri belirlediğimiz sırada çalıştırabilir ve test sonuçlarını birlikte gösterebilir.

`Movie API` Collection'ını açın ve **Run** seçeneğini kullanın.

Run başlamadan önce iki şeyi kontrol edin:

```text
Environment = Local

Request sırası =
POST Movie
GET Movie By Id
PATCH Movie
POST Movie - Invalid Body
DELETE Movie
```

## Örnek 73

Sıra burada önemlidir.

Çünkü `GET Movie By Id`, `PATCH Movie` ve `DELETE Movie` Request'leri `POST Movie` tarafından oluşturulan `movieId` değerine ihtiyaç duyar.

Runner çalıştığında Postman Request'leri sırayla gönderir ve Post-response testlerini çalıştırır.

Örneğin sonuç:

```text
POST Movie             Passed
GET Movie By Id        Passed
PATCH Movie            Passed
POST Movie - Invalid   Passed
DELETE Movie           Passed
```

## Örnek 74

şeklinde olabilir.

PATCH kodumuz yanlışlıkla veriyi değiştirmese ama yine de `200` dönse şu sonucu görebiliriz:

```text
Film 200 ile güncellenmeli     Passed
voteAverage 9.1 olmalı         Failed
```

## Örnek 75

Bu sonuç bize problemi daraltır:

```text
Request çalıştı
↓
200 döndü

ama
↓
beklenen veri değişmedi
```

## Örnek 76

Artık kontrolü gözümüzle yapmak yerine beklentileri kaydetmiş durumdayız.

---

## Local ve Production İçin Aynı Request'leri Kullanabiliriz

Şu an:

```text
Local
└── baseUrl = http://localhost:3000
```

## Örnek 77

Environment'ını kullanıyoruz.

69. bölümde API canlı bir adrese taşındığında ikinci bir Environment oluşturabiliriz:

```text
Production
└── baseUrl = https://movie-api.example.com
```

## Örnek 78

Request URL'si değişmez:

```http
{{baseUrl}}/movies
```

## Örnek 79

Yalnızca aktif Environment değişir.

```text
Local aktif
↓
localhost API

Production aktif
↓
canlı API
```

## Örnek 80

Environment kullanmamızın gerçek faydası burada ortaya çıkar: aynı Collection ve aynı testler farklı API adreslerinde tekrar kullanılabilir.

---

## Bu Bölümdeki Dört Parçanın Görevi

Artık kavramları birbirinden ayırabiliriz:

| Parça | Görevi |
|---|---|
| Collection | Movie API Request'lerini ve test akışını bir arada tutar |
| Environment | `baseUrl` gibi ortama göre değişen değerleri tutar |
| `pm` | Postman'in script içinde sağladığı nesnedir |
| Collection Runner | Collection içindeki Request'leri ve testleri sırayla çalıştırır |

`movieId` ise ayrı bir Postman özelliği değildir. Bizim verdiğimiz variable adıdır ve bu bölümde Collection variable olarak saklanır.

---

## Postman Testleri Neyi Çözüyor, Neyi Çözmüyor?

Bu bölümde yaptığımız testler çalışan API'ye gerçek HTTP Request gönderir ve HTTP Response'u kontrol eder.

Yani örneğin:

```text
POST gerçekten 201 dönüyor mu?
PATCH gerçekten alanı değiştiriyor mu?
validation gerçekten 400 dönüyor mu?
DELETE gerçekten doğru Movie'yi siliyor mu?
```

## Örnek 81

sorularını tekrar çalıştırılabilir hâle getirdik.

Bu yaklaşım bütün yazılım testlerinin yerine geçmez.

İleride unit test, integration test, CI ve load test gibi başka test türleriyle de karşılaşabiliriz.

Buradaki hedefimiz daha sınırlıdır:

> Movie API için Postman'de elle yaptığımız temel HTTP kontrollerini kaydedilmiş ve tekrar çalıştırılabilir bir akışa dönüştürmek.

---

## 🧠 Akılda Kalsın

Movie API için kurduğumuz akış şu şekildedir:

```text
Collection
↓
Request'leri bir arada tutar

Local Environment
↓
baseUrl değerini sağlar

{{baseUrl}}
↓
Request gönderilmeden önce gerçek adresle değiştirilir

Scripts → Post-response
↓
Response geldikten sonra JavaScript çalışır

pm
↓
Postman'in hazır verdiği nesne

pm.response
↓
mevcut Response

pm.test()
↓
test tanımı

pm.expect()
↓
beklenti

pm.collectionVariables.set()
↓
POST'tan gelen movieId değerini saklar

Collection Runner
↓
CRUD akışını sırayla çalıştırır
```

## Örnek 82

En önemli değişim şudur:

```text
önce
↓
Send
↓
Response'a gözümüzle bak

şimdi
↓
Send veya Runner
↓
önceden yazdığımız beklentiler otomatik kontrol edilsin
```

## Örnek 83

---

## Sonraki Adım

Artık Movie API'nin temel CRUD davranışını Postman ile tekrar çalıştırılabilir şekilde kontrol edebiliyoruz.

Fakat projeyi başka bir geliştirici açtığında yalnızca test sonucunu görmek yetmez.

Şunları da bilmek ister:

```text
hangi endpoint'ler var?
hangi Request Body gönderilmeli?
hangi query parameter'lar destekleniyor?
hangi Response yapıları dönebilir?
```

## Örnek 84

Postman testleri bize:

```text
API beklediğimiz gibi çalışıyor mu?
```

## Örnek 85

sorusunda yardımcı oldu.

68. bölümde ise:

```text
API nasıl kullanılmalı?
```
