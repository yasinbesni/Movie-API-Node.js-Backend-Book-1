# Bölüm 63 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Environment Variables ve Merkezi Config Yapısı Nasıl Kurulur?

## Aynı Kod Farklı Ortamlarda Çalıştığında Hangi Değerler Değişir?

62. bölümde Controller ile uygulama iş akışını birbirinden ayırdık.

Şimdi Movie API'nin **çalışma ayarlarına** bakalım.

Şu anda server'ı kendi bilgisayarımızda çalıştırırken portu doğrudan kodun içinde tutabiliriz:

```js
app.listen(3000);
```

## Örnek 2

MongoDB bağlantı adresini ise daha önce `.env` dosyasına taşıdık:

```env
MONGODB_URL=mongodb+srv://...
```

## Örnek 3

ve uygulama içinde:

```js
process.env.MONGODB_URL
```

## Örnek 4

üzerinden okuyoruz.

Kendi bilgisayarımızda bu yapı çalışabilir.

Ama aynı API'yi başka bir bilgisayarda veya bir deployment platformunda çalıştırdığımızı düşünelim.

O ortamda:

```text
PORT=8080
```

## Örnek 5

gerekebilir.

MongoDB bağlantısı da geliştirme sırasında kullandığımız veritabanından farklı olabilir:

```text
kendi bilgisayarımız
↓
PORT=3000
MONGODB_URL=geliştirme veritabanı

production ortamı
↓
PORT=8080
MONGODB_URL=production veritabanı
```

## Örnek 6

Burada değişen şey Movie API'nin yaptığı iş değildir.

API hâlâ:

```text
film oluşturur
filmleri listeler
filmleri günceller
filmleri siler
```

## Örnek 7

Fakat uygulamanın **hangi portta çalışacağı** veya **hangi veritabanına bağlanacağı** bulunduğu ortama göre değişebilir.

Bu yüzden şu tür değerleri JavaScript dosyalarının içine sabitlemek istemeyiz:

```text
port
veritabanı bağlantı adresi
izin verilen frontend adresi
rate limit ayarları
```

## Örnek 8

Bu tür, uygulamanın çalıştığı ortama göre değişebilen değerlere **environment variable** üzerinden ulaşabiliriz.

`environment` kelimesini burada şöyle düşünebiliriz:

```text
uygulamanın çalıştığı ortam
```

## Örnek 9

Örneğin kendi bilgisayarımızdaki geliştirme ortamına genellikle:

```text
development
```

## Örnek 10

yayınlanan gerçek çalışma ortamına ise:

```text
production
```

## Örnek 11

denir.

Bu bölümde kuracağımız yapı sayesinde **kod aynı kalacak, çalışma ayarları dışarıdan değiştirilebilecek**.

---

## `.env` Dosyasını Zaten Kullanıyoruz; Sorun Nerede?

29. bölümde MongoDB bağlantı adresini `.env` dosyasına taşımıştık:

```env
MONGODB_URL=mongodb+srv://...
```

## Örnek 12

ve:

```js
import "dotenv/config";
```

## Örnek 13

ile değeri `process.env` içine yüklemiştik.

Bu önemli bir adımdı.

Çünkü bağlantı adresini artık doğrudan JavaScript kodunun içine yazmıyoruz.

Fakat uygulama büyüdükçe yalnızca **değerleri dışarı almak** yeterli olmayabilir.

Örneğin MongoDB bağlantı dosyamız şunu okuyabilir:

```js
process.env.MONGODB_URL
```

## Örnek 14

Server başlangıç dosyamız da şunu okumaya başlayabilir:

```js
process.env.PORT
```

## Örnek 15

Bir süre sonra başka ayarlar eklenebilir:

```text
CLIENT_ORIGIN
RATE_LIMIT_MAX
RATE_LIMIT_WINDOW_MS
```

## Örnek 16

Bu durumda farklı dosyalar yalnızca değeri kullanmakla kalmaz; aynı zamanda şu sorularla da ilgilenmeye başlar:

```text
bu değer gerçekten var mı?
boş bırakılmış mı?
Number'a çevrilmeli mi?
geçersiz bir değer verilmiş mi?
değer verilmezse default kullanılacak mı?
```

## Örnek 17

Örneğin `index.js` içinde şuna benzer bir kod yazmak zorunda kalabiliriz:

```js
const port = Number(process.env.PORT ?? 3000);

if (
  !Number.isSafeInteger(port) ||
  port < 1 ||
  port > 65535
) {
  throw new Error(
    "PORT 1 ile 65535 arasında tam sayı olmalıdır.",
  );
}
```

## Örnek 18

MongoDB bağlantı dosyasında da ayrıca:

```js
if (!process.env.MONGODB_URL) {
  throw new Error(
    "MONGODB_URL zorunludur.",
  );
}
```

## Örnek 19

yazmamız gerekebilir.

Kod çalışır.

Ama artık environment ayarlarını **okuma, doğrulama ve dönüştürme işi farklı dosyalara dağılmış olur**.

Asıl sorun burada başlıyor.

---

## `.env` ile `config` Aynı Görevi Yapmaz

Bu iki parçanın görevini ayıralım.

`.env` dosyası bize ham değerleri sağlar:

```env
MONGODB_URL=mongodb+srv://...
PORT=3000
```

## Örnek 20

Merkezi config dosyası ise bu değerleri uygulamaya vermeden önce hazırlar:

```text
process.env değerini oku
↓
eksik mi kontrol et
↓
gerekirse dönüştür
↓
geçerli mi kontrol et
↓
default gerekiyorsa uygula
↓
uygulamanın kullanacağı değeri export et
```

## Örnek 21

Yani:

```text
.env
↓
ham değer

config/env.js
↓
uygulamaya hazır değer
```

## Örnek 22

Bu ayrım önemli.

Ayrıca production ortamında her zaman fiziksel bir `.env` dosyası bulunmak zorunda değildir.

Bir deployment platformu environment variable'ları doğrudan sisteme verebilir.

Uygulamamız açısından önemli olan şudur:

```text
değer hangi kaynaktan geldi?
↓
.env veya deployment platformu

uygulama ne okuyor?
↓
process.env
```

## Örnek 23

Bizim `config/env.js` dosyamız bu kaynağın ayrıntısıyla ilgilenmeyecek; yalnızca `process.env` içindeki değerleri hazırlayacak.

---

## Önce `PORT` Değerini de Dışarı Alalım

Proje kökündeki `.env` dosyamıza:

```env
MONGODB_URL=mongodb+srv://...
PORT=3000
```

## Örnek 24

ekleyelim.

Böylece port artık JavaScript dosyasının içine gömülü değildir.

Fakat hemen şunu yazmak istemiyoruz:

```js
app.listen(process.env.PORT);
```

## Örnek 25

Çünkü henüz şu soruları çözmedik:

```text
PORT hiç verilmezse ne olacak?
PORT boşsa ne olacak?
PORT="abc" ise ne olacak?
PORT JavaScript tarafında Number mı gelecek?
```

## Örnek 26

Bu sorular config yapısının neden gerekli olduğunu daha da netleştiriyor.

---

## `process.env` Değerleri String Olarak Gelir

`.env` içinde:

```env
PORT=3000
```

## Örnek 27

yazsak bile:

```js
console.log(process.env.PORT);
console.log(typeof process.env.PORT);
```

## Örnek 28

çıktısı kavramsal olarak şöyledir:

```text
3000
string
```

## Örnek 29

Yani:

```text
.env
↓
PORT=3000
↓
process.env.PORT
↓
"3000"
```

## Örnek 30

`PORT` değerini uygulama içinde Number olarak kullanmak istiyorsak dönüşümü bizim yapmamız gerekir.

Bu yalnızca `PORT` için geçerli bir ayrıntı değildir.

Environment variable'lar dışarıdan text olarak gelir; uygulama içindeki gerçek tipe config aşamasında dönüştürmek daha güvenlidir.

---

## Merkezi Config Dosyasını Oluşturalım

Yeni dosyamız:

```text
src/config/env.js
```

## Örnek 31

olsun.

İlk olarak `.env` içindeki değerlerin `process.env` içine yüklenmesini sağlayalım:

```js
import "dotenv/config";
```

## Örnek 32

Şimdi config dosyasının iki işi olacak:

```text
MONGODB_URL
↓
zorunlu String olarak kontrol et

PORT
↓
Number'a dönüştür ve geçerli aralıkta mı kontrol et
```

## Örnek 33

Bunları sırayla yapalım.

---

### Zorunlu Değerleri Kontrol Edelim

`MONGODB_URL` olmadan veritabanı bağlantısı kuramayız.

Bu nedenle küçük bir yardımcı fonksiyon yazalım:

```js
const getRequiredEnv = (name) => {
  const value = process.env[name];

  if (
    typeof value !== "string" ||
    value.trim() === ""
  ) {
    throw new Error(
      `${name} environment variable zorunludur.`,
    );
  }

  return value;
};
```

## Örnek 34

Fonksiyonun akışı şu:

```text
getRequiredEnv("MONGODB_URL")
↓
process.env.MONGODB_URL değerini oku
↓
yok mu veya boş mu?
├─ evet → hata üret
└─ hayır → değeri döndür
```

## Örnek 35

Örneğin:

```env
MONGODB_URL=
```

## Örnek 36

şeklinde boş bırakılırsa uygulama daha başlarken:

```text
MONGODB_URL environment variable zorunludur.
```

## Örnek 37

hatasını üretir.

Bu davranış özellikle değerlidir; çünkü eksik config ile server'ın kısmen çalışmaya başlamasını istemeyiz.

---

### `PORT` Değerini Number'a Çevirelim

`PORT` için yalnızca “değer var mı?” kontrolü yeterli değildir.

Şu değerler birbirinden farklıdır:

```text
PORT=3000
PORT=abc
PORT=70000
```

## Örnek 38

İlki geçerli olabilir.

Diğer ikisini kabul etmek istemeyiz.

Bu nedenle:

```js
const parsePort = (value) => {
  const port = Number(value ?? 3000);

  if (
    !Number.isSafeInteger(port) ||
    port < 1 ||
    port > 65535
  ) {
    throw new Error(
      "PORT 1 ile 65535 arasında tam sayı olmalıdır.",
    );
  }

  return port;
};
```

## Örnek 39

kullanabiliriz.

50. bölümde `Number.isSafeInteger()` ile bir değerin güvenli bir tam sayı olup olmadığını kontrol etmiştik.

Burada aynı bilgiyi `PORT` için kullanıyoruz.

Akış:

```text
process.env.PORT
↓
"3000"
↓
Number(...)
↓
3000
↓
tam sayı mı?
↓
1–65535 aralığında mı?
↓
geçerliyse kullan
```

## Örnek 40

---

### `??` Burada Neden Kullanılıyor?

Şu ifade:

```js
value ?? 3000
```

## Örnek 41

`value` yalnızca:

```text
null
veya
undefined
```

## Örnek 42

ise `3000` değerini kullanır.

Yani `PORT` hiç verilmemişse:

```text
undefined
↓
3000
```

## Örnek 43

olur.

Bu sayede development ortamında port için mantıklı bir default değer kullanabiliyoruz.

Ama:

```env
PORT=abc
```

## Örnek 44

gibi verilmiş fakat geçersiz bir değeri sessizce `3000`e çevirmiyoruz.

`Number("abc")` sonucu `NaN` olur ve doğrulamadan geçemez.

Bu ayrım önemlidir:

```text
değer verilmemiş
↓
default kullanılabilir

değer verilmiş ama hatalı
↓
hata üret
```

## Örnek 45

---

## Uygulamanın Kullanacağı `env` Nesnesini Oluşturalım

Şimdi iki parçayı tek bir nesnede birleştirebiliriz:

```js
export const env = {
  mongodbUrl: getRequiredEnv(
    "MONGODB_URL",
  ),
  port: parsePort(process.env.PORT),
};
```

## Örnek 46

Artık config dosyamızın tamamı şöyledir:

```js
import "dotenv/config";

const getRequiredEnv = (name) => {
  const value = process.env[name];

  if (
    typeof value !== "string" ||
    value.trim() === ""
  ) {
    throw new Error(
      `${name} environment variable zorunludur.`,
    );
  }

  return value;
};

const parsePort = (value) => {
  const port = Number(value ?? 3000);

  if (
    !Number.isSafeInteger(port) ||
    port < 1 ||
    port > 65535
  ) {
    throw new Error(
      "PORT 1 ile 65535 arasında tam sayı olmalıdır.",
    );
  }

  return port;
};

export const env = {
  mongodbUrl: getRequiredEnv(
    "MONGODB_URL",
  ),
  port: parsePort(process.env.PORT),
};
```

## Örnek 47

Buradaki önemli değişiklik şudur:

Uygulamanın geri kalanı artık ham environment değerleriyle uğraşmayacak.

```text
process.env.MONGODB_URL
↓
zorunlu olduğu kontrol edilmemiş ham String

env.mongodbUrl
↓
varlığı kontrol edilmiş değer
```

## Örnek 48

ve:

```text
process.env.PORT
↓
"3000"

env.port
↓
3000
```

## Örnek 49

şeklinde bir ayrım oluştu.

Tam akış artık şöyledir:

```text
.env veya deployment ortamı
↓
process.env
↓
src/config/env.js
↓
doğrulanmış / dönüştürülmüş env nesnesi
↓
uygulamanın geri kalanı
```

## Örnek 50

---

## MongoDB Bağlantısını Merkezi Config'e Bağlayalım

Daha önce `src/db/initMongoConnection.js` içinde:

```js
await mongoose.connect(
  process.env.MONGODB_URL,
);
```

## Örnek 51

kullanıyorduk.

Şimdi:

```js
import mongoose from "mongoose";

import { env } from "../config/env.js";

export const initMongoConnection = async () => {
  await mongoose.connect(env.mongodbUrl);
};
```

## Örnek 52

kullanabiliriz.

Bu dosyanın artık şunları bilmesine gerek yok:

```text
environment variable'ın adı ne?
değer boş olabilir mi?
nereden yüklendi?
```

## Örnek 53

Onun ihtiyacı yalnızca şudur:

```text
MongoDB'ye bağlanacağım URL nedir?
↓
env.mongodbUrl
```

## Örnek 54

Bu, merkezi config yapısının asıl faydalarından biridir.

---

## Server Portunu da `env` Üzerinden Kullanalım

Daha önce:

```js
app.listen(3000, () => {
  console.log(
    "Server 3000 portunda çalışıyor.",
  );
});
```

## Örnek 55

kullanıyorduk.

Şimdi `index.js` içinde:

```js
import { env } from "./config/env.js";
import { initMongoConnection } from "./db/initMongoConnection.js";
import { app } from "./server.js";

const startServer = async () => {
  try {
    await initMongoConnection();

    app.listen(env.port, () => {
      console.log(
        `Server ${env.port} portunda çalışıyor.`,
      );
    });
  } catch (error) {
    console.error(
      "Uygulama başlatılamadı:",
      error.message,
    );

    process.exitCode = 1;
  }
};

startServer();
```

## Örnek 56

Artık port değiştirmek için JavaScript dosyasını değiştirmiyoruz.

Sadece çalışma ortamındaki değeri değiştiriyoruz:

```env
PORT=4000
```

## Örnek 57

Uygulama yeniden başladığında:

```text
env.port
↓
4000
```

## Örnek 58

olur.

---

## `dotenv/config` Neden Artık `env.js` İçinde?

Daha önce `index.js` içinde:

```js
import "dotenv/config";
```

## Örnek 59

kullanmış olabiliriz.

Artık environment değerlerini hazırlayan merkez:

```text
src/config/env.js
```

## Örnek 60

olduğu için `dotenv/config` import'unu da burada tutuyoruz.

Böylece config akışı tek yerde toplanır:

```text
.env değerlerini yükle
↓
process.env üzerinden oku
↓
doğrula / dönüştür
↓
env nesnesi olarak export et
```

## Örnek 61

`index.js`, `initMongoConnection.js` veya başka bir dosya bu ayrıntılarla ilgilenmez.

---

## Hatalı Config'i Bilerek Deneyelim

Config yapısının gerçekten çalıştığını görelim.

### Geçersiz Port

`.env` dosyasında geçici olarak:

```env
PORT=abc
```

## Örnek 62

yazalım.

Uygulamayı başlattığımızda:

```text
PORT 1 ile 65535 arasında tam sayı olmalıdır.
```

## Örnek 63

hatasını görmeliyiz.

Sonra değeri tekrar:

```env
PORT=3000
```

## Örnek 64

yapalım.

### Eksik MongoDB Adresi

`MONGODB_URL` değerini geçici olarak boş bırakalım:

```env
MONGODB_URL=
```

## Örnek 65

Bu kez:

```text
MONGODB_URL environment variable zorunludur.
```

## Örnek 66

hatasını görmeliyiz.

Bu davranışın avantajı şudur:

```text
hatalı config
↓
server Request kabul etmeye başlamadan fark edilir
```

## Örnek 67

Config hatasını ilk gerçek Request geldiğinde veya MongoDB işlemi yapılırken keşfetmek yerine, uygulama başlangıcında görmek daha nettir.

---

## `.env.example` Neden Gerekli?

Gerçek `.env` dosyamız Git'e gönderilmemelidir.

Çünkü içinde bağlantı bilgileri, API key'ler veya başka secret değerler bulunabilir.

Ama projeyi başka biri çalıştırdığında şu bilgiyi bilmesi gerekir:

```text
hangi environment variable'ları tanımlamalıyım?
```

## Örnek 68

Bunun için proje köküne:

```text
.env.example
```

## Örnek 69

ekleyebiliriz.

Örneğin:

```env
MONGODB_URL=your_mongodb_connection_string
PORT=3000
```

## Örnek 70

Burada gerçek secret bulunmaz.

Sadece gereken key'lerin şablonu vardır.

Kullanım akışı:

```text
.env.example
↓
hangi key'ler gerekli gör
↓
kendi .env dosyanı oluştur
↓
gerçek değerleri yaz
```

## Örnek 71

Bu yüzden:

```text
.env
↓
gerçek değerler
↓
Git'e gönderilmez

.env.example
↓
şablon
↓
Git'e gönderilebilir
```

## Örnek 72

ayrımını koruruz.

---

## `config` ile `constants` Arasındaki Farkı Şimdi Netleştirelim

58. bölümde:

```js
export const HIGH_RATING_THRESHOLD = 8;
```

## Örnek 73

gibi bir constant kullanmıştık.

48. bölümde de pagination için ortak sabitler oluşturmuştuk.

Şimdi elimizde ayrıca:

```text
src/config/env.js
```

## Örnek 74

var.

İkisi de ortak değer tutuyor gibi görünebilir; fakat karar noktası farklıdır.

Şu soruyu sorabiliriz:

> Bu değer uygulamanın kendi kararı mı, yoksa çalıştığı ortama göre değişebilir mi?

Örneğin:

```text
HIGH_RATING_THRESHOLD = 8
↓
uygulamanın kendi kararı
↓
constants

DEFAULT_LIMIT = 10
↓
uygulamanın kendi kararı
↓
constants

PORT=3000 veya PORT=8080
↓
çalışma ortamına göre değişebilir
↓
config

MONGODB_URL
↓
ortama göre değişir
↓
config
```

## Örnek 75

Kısa ayrım:

```text
constants
↓
kodun ortak sabit kararları

config
↓
çalışma ortamına göre değişebilen ayarlar
```

## Örnek 76

Bu karşılaştırmayı config yapısını kurduktan sonra yapmak daha anlamlıdır; çünkü artık iki tarafı da gerçek kod içinde gördük.

---

## Proje Yapımız Şimdi Nasıl?

```text
project
├── .env
├── .env.example
└── src
    ├── config
    │   └── env.js
    ├── constants
    ├── controllers
    ├── db
    ├── middlewares
    ├── routers
    ├── services
    ├── utils
    ├── validation
    ├── index.js
    └── server.js
```

## Örnek 77

Bu yapıda:

```text
src/config/env.js
↓
environment ayarlarını hazırlar

src/constants
↓
uygulamanın ortak sabit kararlarını tutar
```

## Örnek 78

---

## Config'e Her Olası Ayarı Şimdiden Eklemeyelim

Şu anda gerçekten kullandığımız iki environment ayarı var:

```text
MONGODB_URL
PORT
```

## Örnek 79

Bu yüzden `env` nesnemizde de yalnızca bunlar bulunuyor.

İleride örneğin CORS için izin verilen frontend origin'i gerçekten gerektiğinde:

```text
CLIENT_ORIGIN
```

## Örnek 80

gibi yeni bir değer ekleyebiliriz.

Ama henüz kullanılmayan onlarca config key'i önceden oluşturmak, yapıyı daha anlaşılır hâle getirmez.

Pratik ölçüt şu olabilir:

```text
uygulama gerçekten bu ayara ihtiyaç duyuyor mu?
├─ evet → config'e ekle
└─ hayır → şimdilik ekleme
```

## Örnek 81

---

## Bölüm Sonu Uygulaması

1. `.env` dosyanıza `PORT=3000` ekleyin.
2. `src/config/env.js` dosyasını oluşturun.
3. `getRequiredEnv()` ile `MONGODB_URL` değerini kontrol edin.
4. `parsePort()` ile `PORT` değerini Number'a dönüştürüp 1–65535 aralığında doğrulayın.
5. `env.mongodbUrl` ve `env.port` değerlerini export edin.
6. `initMongoConnection.js` içinde `process.env.MONGODB_URL` yerine `env.mongodbUrl` kullanın.
7. `index.js` içinde sabit `3000` yerine `env.port` kullanın.
8. `PORT=abc` ile config doğrulamasının uygulama başlarken hata verdiğini gözlemleyin.
9. `MONGODB_URL` değerini boş bırakarak zorunlu config kontrolünü deneyin.
10. `.env.example` oluşturup gerçek secret yazmadan gerekli variable adlarını ekleyin.
11. `constants` ile `config` arasındaki farkı kendi projenizden birer örnekle açıklayın.

---

## 🧠 Akılda Kalsın

Environment variable:

```text
uygulamanın çalıştığı ortama göre değişebilen dış değer
```

## Örnek 82

olarak düşünülebilir.

`.env` dosyası local development sırasında bu değerleri sağlamanın kullanışlı bir yoludur.

Merkezi config yapısı ise:

```text
process.env
↓
oku
↓
doğrula
↓
dönüştür / default uygula
↓
env nesnesi
↓
uygulama
```

## Örnek 83

akışını kurar.

Böylece uygulamanın farklı dosyalarında:

```js
process.env.X
```

## Örnek 84

okuyup aynı kontrolleri tekrar etmek zorunda kalmayız.

En önemli ayrım:

```text
.env / deployment environment
↓
ham değerleri sağlar

config/env.js
↓
bu değerleri uygulamaya hazırlar

constants
↓
uygulamanın ortak sabit kararlarını tutar
```

## Örnek 85

---

## Sonraki Adım

Merkezi config yapımız artık hazır.

Bir sonraki adımda Movie API'yi gerçek bir frontend uygulamasının kullanacağını düşünelim.

Frontend:

```text
http://localhost:5173
```

## Örnek 86

adresinde,

API:

```text
http://localhost:3000
```
