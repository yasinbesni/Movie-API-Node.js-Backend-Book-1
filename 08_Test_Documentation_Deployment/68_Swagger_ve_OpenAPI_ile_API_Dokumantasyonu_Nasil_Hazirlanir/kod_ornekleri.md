# Bölüm 68 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Swagger ve OpenAPI ile API Dokümantasyonu Nasıl Hazırlanır?

## Projeyi Başka Bir Geliştiriciye Versek İlk Ne Sorardı?

67. bölümde Movie API için tekrar çalıştırılabilir Postman testleri oluşturduk.

Şimdi projeyi başka bir geliştiriciye verdiğimizi düşünelim.

İlk mesajı şöyle olabilir:

> `POST /movies` için hangi alanları göndermeliyim? `limit` en fazla kaç olabilir? Tek film bulunamazsa hangi status dönüyor?

Biz bu soruların cevaplarını biliyoruz.

Çünkü kodu biz yazdık.

Ama yeni biri cevapları bulmak için:

```text
Router'a bak
↓
Joi Schema'ya bak
↓
Controller'a bak
↓
Response kodunu ara
```

## Örnek 2

yapmak zorunda kalmamalı.

İhtiyacımız şu:

> Movie API'nin nasıl kullanılacağını tek bir standart dokümanda tarif etmek.

Bu bölümde bu tarif için **OpenAPI**, tarayıcıda okunabilir ve denenebilir hâle getirmek için **Swagger UI** kullanacağız.

Örneğin `GET /movies` isteğinde `limit` için kabul ettiğimiz üst sınır değişirse, yalnızca Controller kodunu düzeltmek yeterli değildir. Dokümanda eski sınır kalırsa başka bir geliştirici yanlış beklentiyle istek gönderir. Bu yüzden OpenAPI tanımını gerçek API davranışıyla birlikte güncel tutacağız.

---

## Önce Tek Bir Endpoint'i Browser'da Görelim

Kavramları uzun uzun tanımlamadan önce çalışan sonucu görelim.

Paketi kuralım:

```bash
npm install swagger-ui-express
```

## Örnek 3

Yeni dosya:

```text
src/docs/openapi.js
```

## Örnek 4

İlk hâli çok küçük olsun:

```js
export const openApiDocument = {
  openapi: "3.1.0",
  info: {
    title: "Movie API",
    version: "1.0.0",
  },
  servers: [
    {
      url: "/",
    },
  ],
  paths: {
    "/movies": {
      get: {
        summary: "Filmleri listeler",
        responses: {
          200: {
            description:
              "Film listesi başarıyla getirildi.",
          },
        },
      },
    },
  },
};
```

## Örnek 5

Şimdi `server.js` içine:

```js
import swaggerUi from "swagger-ui-express";

import {
  openApiDocument,
} from "./docs/openapi.js";
```

## Örnek 6

ekleyelim.

Ve genel 404 / error handling akışından önce:

```js
app.use(
  "/docs",
  swaggerUi.serve,
  swaggerUi.setup(openApiDocument),
);
```

## Örnek 7

kullanalım.

Browser'da:

```text
http://localhost:3000/docs
```

## Örnek 8

adresini açalım.

Artık `GET /movies` endpoint'ini dokümantasyon ekranında görebiliriz.

Bu ilk küçük sonuç önemli:

```text
JavaScript object
↓
OpenAPI tanımı
↓
Swagger UI
↓
browser'da API dokümantasyonu
```

## Örnek 9

---

## OpenAPI Sözleşmeyi Tanımlar, Swagger UI Onu Gösterir

Şimdi bu iki parçanın görevini adlandıralım.

```text
OpenAPI
↓
HTTP API'nin nasıl tarif edileceğini belirleyen standart

Swagger UI
↓
OpenAPI tanımını okuyup browser'da gösteren arayüz
```

## Örnek 10

Yani:

```text
OpenAPI = sözleşme / tanım
Swagger UI = tanımı gösteren araç
```

## Örnek 11

Bu ayrımı bilmek önemlidir.

Çünkü OpenAPI dokümanı Swagger UI dışında başka araçlar tarafından da kullanılabilir.

---

## İlk Dosyamızdaki Alanları Okuyalım

Şimdi ilk çalışan örneğe geri dönelim.

### `openapi`

```js
openapi: "3.1.0"
```

## Örnek 12

bu dokümanın hangi OpenAPI Specification sürümüne göre yazıldığını belirtir.

### `info`

```js
info: {
  title: "Movie API",
  version: "1.0.0",
}
```

## Örnek 13

bizim API'miz hakkında genel bilgi verir.

Buradaki:

```text
openapi: 3.1.0
```

## Örnek 14

ile:

```text
info.version: 1.0.0
```

## Örnek 15

aynı şey değildir.

Birincisi kullandığımız **OpenAPI standardının sürümü**, ikincisi bizim **Movie API sürümümüzdür**.

### `servers`

```js
servers: [
  {
    url: "/",
  },
]
```

## Örnek 16

Swagger UI'ın Request gönderirken hangi server adresini temel alacağını tarif eder.

Burada relative `/` kullandığımız için:

```text
/docs local server'da açıldı
↓
local API'yi kullan

/docs canlı domain'de açıldı
↓
aynı canlı domain'deki API'yi kullan
```

## Örnek 17

şeklinde çalışabilir.

### `paths`

```js
paths: {
  "/movies": {
    get: {
      ...
    },
  },
}
```

## Örnek 18

API'deki endpoint ve HTTP operasyonlarını tarif eder.

Bu yüzden Swagger UI ilk endpoint'imizi gösterebildi.

---

## `GET /movies` Gerçekte Ne Kabul Ediyorsa Doküman da Onu Söylesin

Movie listemiz şu query parameter'ları destekleyebiliyor:

```text
page
limit
year
minVoteAverage
search
```

## Örnek 19

Önce `page` ve `limit` ekleyelim:

```js
parameters: [
  {
    in: "query",
    name: "page",
    schema: {
      type: "integer",
      minimum: 1,
      default: 1,
    },
    description:
      "İstenen sayfa numarası",
  },
  {
    in: "query",
    name: "limit",
    schema: {
      type: "integer",
      minimum: 1,
      maximum: 50,
      default: 10,
    },
    description:
      "Bir sayfada dönecek maksimum film sayısı",
  },
],
```

## Örnek 20

Buradaki:

```js
in: "query"
```

## Örnek 21

şunu söyler:

```text
bu değer URL query string içinde gelir
```

## Örnek 22

Örneğin:

```http
GET /movies?page=2&limit=10
```

## Örnek 23

En önemli nokta ise şudur:

```text
kodda MAX_LIMIT = 50
↓
dokümanda maximum: 50
```

## Örnek 24

Dokümantasyon kendi başına yeni kural üretmez.

Gerçek API kararını anlatır.

---

## Swagger UI'da İlk Request'i Gönderelim

`/docs` sayfasını yenileyelim.

`GET /movies` altında query parameter alanlarını görmeliyiz.

Swagger UI'daki **Try it out** özelliğiyle:

```text
page = 1
limit = 5
```

## Örnek 25

girip Request gönderebiliriz.

Bu küçük deney iki şeyi aynı anda kontrol eder:

```text
doküman parametreyi gösteriyor mu?
↓
gerçek endpoint bu Request'i kabul ediyor mu?
```

## Örnek 26

Ama dikkat:

> Request'in çalışması dokümantasyonun her ayrıntısının doğru olduğunu garanti etmez.

Dokümanın kodla uyumunu yine bizim korumamız gerekir.

---

## `POST /movies` Body'sini Tarif Edelim

59. bölümde Joi ile gerçek Request validation kurallarını yazmıştık.

Şimdi API kullanıcısına aynı beklentiyi dokümantasyonda göstermeliyiz.

```js
post: {
  summary: "Yeni film oluşturur",
  requestBody: {
    required: true,
    content: {
      "application/json": {
        schema: {
          type: "object",
          required: [
            "title",
          ],
          additionalProperties: false,
          properties: {
            title: {
              type: "string",
              pattern: "\\S",
              example: "Dune",
            },
            releaseYear: {
              type: "integer",
              minimum: 1888,
              example: 2021,
            },
            voteAverage: {
              type: "number",
              minimum: 0,
              maximum: 10,
              example: 8.3,
            },
          },
        },
      },
    },
  },
  responses: {
    201: {
      description:
        "Film başarıyla oluşturuldu.",
    },
    400: {
      description:
        "Request Body geçersiz.",
    },
  },
},
```

## Örnek 27

Burada:

```js
requestBody
```

## Örnek 28

Request Body sözleşmesini tarif eder.

```js
required: ["title"]
```

## Örnek 29

59. bölümdeki gerçek Joi kararımızı yansıtır: `title` zorunludur; `releaseYear` ve `voteAverage` gönderilebilir ama zorunlu değildir.

```js
pattern: "\\S"
```

## Örnek 30

`title` değerinin yalnızca whitespace karakterlerinden oluşmaması kuralını dokümana taşır.

```js
additionalProperties: false
```

## Örnek 31

ise tanımlamadığımız ek alanları kabul etmediğimizi sözleşmede görünür hâle getirir.

Son olarak:

```js
"application/json"
```

## Örnek 32

bu endpoint'in JSON Body beklediğini söyler.

---

## Aynı Movie Yapısını Her Response'ta Tekrar Yazmayalım

Movie alanları birçok endpoint'te tekrar eder.

Örneğin:

```text
GET by id Response
POST Response
PATCH Response
```

## Örnek 33

hepsi Movie verisi taşıyabilir.

OpenAPI'de reusable schema oluşturabiliriz:

```js
components: {
  schemas: {
    Movie: {
      type: "object",
      properties: {
        _id: {
          type: "string",
        },
        title: {
          type: "string",
        },
        releaseYear: {
          type: "integer",
        },
        voteAverage: {
          type: "number",
        },
      },
    },
  },
},
```

## Örnek 34

Sonra başka bir yerde:

```js
{
  $ref: "#/components/schemas/Movie",
}
```

## Örnek 35

kullanabiliriz.

`$ref` ifadesini şöyle okuyabiliriz:

```text
bu yapıyı tekrar yazma
↓
components.schemas.Movie tanımına git
```

## Örnek 36

---

## 61'deki Response Sözleşmesini Dokümana Taşıyalım

Liste Response'umuzda:

```text
data
↓
Movie listesi

meta
↓
pagination bilgileri
```

## Örnek 37

vardır.

Bunu da reusable schema yapabiliriz:

```js
MovieListResponse: {
  type: "object",
  properties: {
    message: {
      type: "string",
    },
    data: {
      type: "array",
      items: {
        $ref:
          "#/components/schemas/Movie",
      },
    },
    meta: {
      type: "object",
      properties: {
        page: {
          type: "integer",
        },
        limit: {
          type: "integer",
        },
        totalItems: {
          type: "integer",
        },
        totalPages: {
          type: "integer",
        },
      },
    },
  },
},
```

## Örnek 38

Sonra `GET /movies` için:

```js
200: {
  description:
    "Film listesi başarıyla getirildi.",
  content: {
    "application/json": {
      schema: {
        $ref:
          "#/components/schemas/MovieListResponse",
      },
    },
  },
},
```

## Örnek 39

kullanabiliriz.

Artık doküman yalnızca:

```text
200 döner
```

## Örnek 40

demiyor.

Response Body'nin şeklini de tarif ediyor.

---

## Dokümantasyon ile Runtime Validation Farklı Aşamalarda Çalışır

OpenAPI ve Joi aynı sözleşmenin farklı aşamalarında görev alır.

```text
Joi
↓
gerçek Request geldiğinde validation çalıştırır

OpenAPI
↓
API kullanıcısına hangi Request / Response sözleşmesini beklediğimizi tarif eder
```

## Örnek 41

Örneğin OpenAPI dosyasına:

```text
voteAverage 0 ile 10 arasında
```

## Örnek 42

yazmamız Express uygulamasının Request'i otomatik reddedeceği anlamına gelmez.

Runtime validation için Joi kullanmaya devam ediyoruz.

Bu örnekte OpenAPI'den otomatik doğrulama kodu üretmiyoruz.

---

## Doküman ile Kod Birbirinden Koparsa Ne Olur?

Şöyle bir hata düşünelim:

```text
gerçek kod
↓
limit en fazla 50

OpenAPI
↓
limit en fazla 100
```

## Örnek 43

Swagger UI kullanıcıya `100` değerinin kabul edildiğini düşündürebilir.

Ama gerçek API başka davranır.

Bu yüzden şu alışkanlığı kazanmalıyız:

```text
API sözleşmesi değişti
↓
Joi / Controller / Response değişti mi?
↓
OpenAPI tanımını da kontrol et
```

## Örnek 44

Yanlış dokümantasyon bazen hiç dokümantasyon olmamasından daha yanıltıcı olabilir.

---

## Postman ve Swagger UI’ı Farklı Amaçlarla Kullanıyoruz

67 ve 68 birbirini tamamlıyor.

```text
Postman Collection
↓
Request gönder
↓
beklenti yaz
↓
testleri tekrar çalıştır
```

## Örnek 45



```text
OpenAPI + Swagger UI
↓
API sözleşmesini tarif et
↓
endpoint'leri keşfet
↓
kullanım biçimini gör
```

## Örnek 46

Her ikisiyle de Request gönderilebilir; ancak bu bölümde üstlendikleri ana görev farklıdır.

---

## Kalan Endpoint'leri Aynı Mantıkla Ekleyelim

Sonraki adımda:

```text
GET /movies/{movieId}
PATCH /movies/{movieId}
DELETE /movies/{movieId}
```

## Örnek 47

endpoint'lerini ekleyebiliriz.

Path parameter örneği:

```js
parameters: [
  {
    in: "path",
    name: "movieId",
    required: true,
    schema: {
      type: "string",
    },
    description:
      "Movie Document ObjectId değeri",
  },
],
```

## Örnek 48

Buradaki:

```js
in: "path"
```

## Örnek 49

parameter'ın URL path içinden geldiğini anlatır.

Hepsini bir anda yazmak zorunda değiliz.

Bir endpoint'i doğru tarif edip aynı modeli diğerlerine uygulamak daha öğreticidir.

---

## Güncel Proje Yapısı

Yeni klasörümüz:

```text
src
├── config
├── constants
├── controllers
├── db
├── docs
│   └── openapi.js
├── logging
├── middlewares
├── routers
├── services
├── utils
├── validation
├── index.js
└── server.js
```

## Örnek 50

`docs/openapi.js` dosyasının tek cümlelik sorumluluğu:

> Movie API'nin dışarıdan nasıl kullanılacağını tarif etmek.

---

## Bölüm Sonu Uygulaması

1. `swagger-ui-express` paketini kurun.
2. `src/docs/openapi.js` içinde yalnızca `GET /movies` endpoint'iyle başlayan minimal OpenAPI document oluşturun.
3. `/docs` Route'unu Express'e bağlayın ve browser'da endpoint'i görün.
4. `page` ve `limit` query parameter'larını ekleyin.
5. Swagger UI `Try it out` ile gerçek Request gönderin.
6. `POST /movies` için `requestBody` tanımlayın.
7. `Movie` schema'sını `components.schemas` içine taşıyıp `$ref` ile kullanın.
8. Liste Response'unu `data + meta` yapısıyla dokümante edin.
9. Gerçek `MAX_LIMIT` ile OpenAPI `maximum` değerini karşılaştırın.
10. `GET /movies/{movieId}` endpoint'ini kendiniz ekleyin.

---

## 🧠 Akılda Kalsın

OpenAPI:

```text
API'nin kullanım sözleşmesini
standart biçimde tarif eder
```

## Örnek 51

Swagger UI:

```text
bu tanımı
browser'da okunabilir ve denenebilir hâle getirir
```

## Örnek 52

Joi:

```text
runtime validation
```

## Örnek 53

OpenAPI:

```text
dokümantasyon / sözleşme tarifi
```

## Örnek 54

Ve en önemli kural:

```text
kod değişti
↓
API sözleşmesi değişti mi?
↓
evetsa dokümantasyonu da güncelle
```

## Örnek 55

---

## Sonraki Adım

Artık Movie API'miz:

```text
çalışıyor
↓
test ediliyor
↓
dokümante ediliyor
```

## Örnek 56

Ama hâlâ adresimiz:

```text
http://localhost:3000
```
