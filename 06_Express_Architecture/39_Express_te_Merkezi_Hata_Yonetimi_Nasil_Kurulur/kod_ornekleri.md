# Bölüm 39 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Express'te Merkezi Hata Yönetimi Nasıl Kurulur?

## Sinemada Bir Sorun Çıktı: Herkes Kendi Başına mı Çözecek?

Middleware'i ilk öğrendiğimiz 19. bölümde, onu bir sinemanın girişindeki ara kontrol noktalarına benzetmiştik.

38. bölümde ise middleware'in artık büyüyen proje mimarimizde nerede yaşayacağını ve `requestLogger` gibi ortak bir işin akışa nasıl yerleştirileceğini gördük.

Şimdi aynı sinema benzetmesini hata yönetimini anlamak için biraz ileri taşıyalım.

Normal yolculuk:

```text
seyirci gelir
↓
giriş görevlisi
↓
doğru salona yönlendirilir
↓
film başlar
```

## Örnek 2

Backend karşılığı:

```text
Request
↓
Middleware
↓
Router
↓
Controller
↓
Response
```

## Örnek 3

Şimdi film oynarken projeksiyon cihazının arızalandığını düşünün.

Her çalışan kendi çözümünü üretirse:

```text
giriş görevlisi
↓
kendi çözümü

salon görevlisi
↓
başka çözüm

gişe görevlisi
↓
başka çözüm
```

## Örnek 4

kısa sürede karmaşa çıkar.

Daha mantıklı olan:

```text
sorun oluştu
↓
ortak bir yere bildir
↓
orada yönet
```

## Örnek 5

yaklaşımıdır.

Backend'imizde de benzer bir sorun var.

Controller'larımızda tekrar tekrar:

```text
try/catch
```

## Örnek 6

görüyoruz.

Örneğin:

```js
export const getMovies = async (req, res) => {
  try {
    const movies = await Movie.find({});

    res.status(200).json({
      message: "Filmler getirildi.",
      data: movies,
    });
  } catch (error) {
    console.error(error);

    res.status(500).json({
      message: "Filmler getirilemedi.",
    });
  }
};
```

## Örnek 7

Burada iki iş aynı yerde:

```text
asıl iş
↓
filmleri getir

beklenmeyen hata
↓
500 Response gönder
```

## Örnek 8

Yeni sorumuz:

> Beklenmeyen hataları her Controller'da ayrı ayrı yönetmek yerine tek bir yerde yönetebilir miyiz?

Evet.

Fakat önce küçük bir kapıyı açmamız gerekiyor:

> Bir hata oluştuğunda Express'e "normal akıştan çık, hata yoluna geç" nasıl diyeceğiz?

Cevap, aslında 38. bölümde `requestLogger` içinde yeniden kullandığımız `next` fonksiyonunun içinde saklı.

---

## Bildiğimiz next() Bu Kez Bir Hata Taşırsa Ne Olur?

38. bölümde `requestLogger` middleware'inde:

```js
next();
```

## Örnek 9

için:

```text
işim bitti
↓
normal akışa devam et
```

## Örnek 10

demiştik.

Şimdi aynı fonksiyona bir hata verirsek:

```js
next(error);
```

## Örnek 11

anlam değişir.

Bu kez Express'e:

> Bir hata oluştu. Hata işleme akışına geç.

demiş oluruz.

Yan yana:

```text
next()
↓
NORMAL AKIŞ
```

## Örnek 12



```text
next(error)
↓
HATA AKIŞI
```

## Örnek 13

Bazı kodlarda:

```js
next(err);
```

## Örnek 14

görebilirsiniz.

`err` ve `error` burada yalnızca değişken adıdır.

Mantık aynıdır.

Bir şey dikkatinizi çekti mi?

Aynı `next` fonksiyonu:

```text
boş çağrıldığında
↓
normal akışı sürdürür

hata verilerek çağrıldığında
↓
akışın yönünü değiştirir
```

## Örnek 15

Bundan sonra `next()` ile `next(error)`ı iki farklı yol tabelası gibi düşünebiliriz.

---

## Peki Hata Akışı Nereye Gidiyor?

Şimdilik akışı şöyle düşünün:

```text
Request
↓
Router
↓
Controller
↓
hata
↓
next(error)
↓
???
```

## Örnek 16

Soru işaretinin olduğu yerde hatayı karşılayacak özel bir middleware gerekir.

İşte merkezi hata yönetiminin ilk parçası burada ortaya çıkıyor.

Normal middleware:

```js
(req, res, next)
```

## Örnek 17

Hata işleyen middleware:

```js
(error, req, res, next)
```

## Örnek 18

Aradaki fark:

```text
NORMAL MIDDLEWARE
↓
req
res
next
```

## Örnek 19



```text
HATA MIDDLEWARE'İ
↓
error
req
res
next
```

## Örnek 20

İlk parametre olan:

```js
error
```

## Örnek 21

oluşan hata bilgisini taşır.

![Merkezi hata yönetimi akışı](images/39-merkezi-hata-akisi-baski.svg)

---

## İlk Hata Middleware'imizi Yazalım

38. bölümden şu klasörümüz vardı:

```text
src/middlewares
```

## Örnek 22

İçine:

```text
src/middlewares/errorHandler.js
```

## Örnek 23

dosyasını ekleyelim.

Kod:

```js
export const errorHandler = (
  error,
  req,
  res,
  next,
) => {
  console.error(error);

  res.status(500).json({
    message: "Sunucuda beklenmeyen bir hata oluştu.",
  });
};
```

## Örnek 24

Bunu şöyle okuyun:

```text
hata geldi
↓
error parametresinde hata bilgisi var
↓
sunucuda logla
↓
500 JSON Response gönder
```

## Örnek 25

Şimdilik `next` parametresini kullanmıyoruz.

Ama hata middleware'inin dört parametreli yapısını koruyoruz:

```js
(error, req, res, next)
```

## Örnek 26

---

## errorHandler'ı Uygulamaya Bağlayalım

Çalıştığımız dosya:

```text
src/server.js
```

## Örnek 27

Önce import:

```js
import { errorHandler }
  from "./middlewares/errorHandler.js";
```

## Örnek 28

Sonra Router'ın altına ekleyelim:

```js
app.use("/movies", moviesRouter);

app.use(errorHandler);
```

## Örnek 29

Akış:

```text
normal middleware'ler
↓
Router
↓
Controller
↓
hata olursa
↓
errorHandler
```

## Örnek 30

Bu nedenle hata middleware'ini Route ve Router'lardan sonra bağlıyoruz.

---

## server.js Dosyasının Güncel Hâli

```js
import express from "express";

import { errorHandler }
  from "./middlewares/errorHandler.js";
import { requestLogger }
  from "./middlewares/requestLogger.js";
import moviesRouter from "./routers/movies.js";

export const app = express();

app.use(express.json());

app.use(requestLogger);

app.get("/", (req, res) => {
  res.send("Merhaba Express!");
});

app.use("/movies", moviesRouter);

app.use(errorHandler);
```

## Örnek 31

Artık hataların gidebileceği ortak bir yer var:

```text
src/middlewares/errorHandler.js
```

## Örnek 32

Resim biraz daha netleşti:

```text
hata için ortak adres
↓
errorHandler
```

## Örnek 33

Ama adresi oluşturmak yetmez.

Şimdi gerçek soru şu:

> Async Controller'da oluşan hata bu adrese nasıl ulaşacak?

---

## Önce Eski Yolu Görelim: Express 5'ten Önce Ne Yapılıyordu?

Bizim Controller'larımız:

```js
async (req, res) => {
  // ...
}
```

## Örnek 34

şeklinde `async` fonksiyonlar.

İçlerinde:

```js
await Movie.find({});
```

## Örnek 35

gibi asenkron işlemler var.

Uzun yıllar kullanılan Express 4'te böyle bir `async` işlem hata verdiğinde, hatayı merkezi hata middleware'ine kendimiz göndermemiz gerekiyordu.

Bu yaklaşım, Express 5'in hangi tekrarı ortadan kaldırdığını anlamak için iyi bir karşılaştırma noktasıdır.

Yaygın kullanım:

```js
export const getMovies = async (
  req,
  res,
  next,
) => {
  try {
    const movies = await Movie.find({});

    res.status(200).json({
      message: "Filmler getirildi.",
      data: movies,
    });
  } catch (error) {
    next(error);
  }
};
```

## Örnek 36

Akış:

```text
Movie.find({})
↓
başarılı
↓
200 Response
```

## Örnek 37

veya:

```text
Movie.find({})
↓
hata
↓
catch(error)
↓
next(error)
↓
errorHandler
↓
500 Response
```

## Örnek 38

Burada `next(error)` hatayı:

```text
Controller'dan
↓
ortak hata middleware'ine
```

## Örnek 39

taşıyan köprüydü.

---

## Aynı Köprü Promise Yazımında Nasıl Kuruluyordu?

`async/await` yerine Promise zinciri kullanılırsa:

```js
app.get("/movies", (req, res, next) => {
  return Movie.find({})
    .then((movies) => {
      res.status(200).json({
        message: "Filmler getirildi.",
        data: movies,
      });
    })
    .catch(next);
});
```

## Örnek 40

Buradaki:

```js
.catch(next);
```

## Örnek 41

kabaca:

```text
Promise hata verdi
↓
catch
↓
next(error)
↓
Express hata akışı
```

## Örnek 42

anlamına gelir.

Yani eski yaklaşımın özü:

```text
asenkron hata
↓
kendimiz yakala
↓
next(error)
↓
errorHandler
```

## Örnek 43

şeklindeydi.

---

## Bir Şey Can Sıkmaya Başladı: Tekrar Geri Döndü

Evet, 500 Response'u tek yerde topladık.

Ama bir şey can sıkmaya başladı:

```text
getMovies
↓
try/catch
↓
next(error)

getMovieById
↓
try/catch
↓
next(error)

createMovie
↓
try/catch
↓
next(error)
```

## Örnek 44

Controller'larda:

```text
try/catch + next(error)
```

## Örnek 45

tekrarı hâlâ sürüyor.

Bu tekrar özellikle Express 4 döneminde yeni bir soruyu doğurdu:

> Her Controller'a aynı `try/catch` bloğunu yazmak yerine bunu tek bir yardımcı fonksiyona taşıyabilir miyiz?

Evet.

Eski Express projelerinde bunun için sık karşılaşacağınız yapılardan biri:

```text
ctrlWrapper
```

## Örnek 46

gibi bir yardımcı fonksiyondur.

---

## Bu Tekrarı Bir Kez Yazsak Olmaz mı? ctrlWrapper

Önce ismine bakalım.

```text
ctrl
↓
Controller

wrapper
↓
saran yapı
```

## Örnek 47

Yani `ctrlWrapper`, Controller'ı saran yardımcı bir fonksiyon olarak düşünülebilir.

Önemli nokta:

> `ctrlWrapper`, Express'in hazır gelen bir fonksiyonu değildir.

Uygulamada bizim yazdığımız bir yardımcıdır. Amacı, her Controller'da aynı `try/catch` yapısını tekrar etmemektir.

Örneğin:

```js
export const ctrlWrapper = (controller) => {
  return async (req, res, next) => {
    try {
      await controller(req, res, next);
    } catch (error) {
      next(error);
    }
  };
};
```

## Örnek 48

İlk bakışta yoğun görünebilir.

Ama aslında tek yaptığı şey şu:

```text
Controller'ı çalıştır
↓
hata olursa yakala
↓
next(error)
```

## Örnek 49

Şimdi parçalarına ayıralım.

### 1. Bir Controller Alıyor

```js
(controller) => {
```

## Örnek 50

Örneğin:

```js
ctrlWrapper(getMovies)
```

## Örnek 51

yazarsak `controller` yerine `getMovies` gelir.

### 2. Yeni Bir Middleware Döndürüyor

```js
return async (req, res, next) => {
  // ...
};
```

## Örnek 52

Yani wrapper, Express'in çalıştırabileceği yeni bir middleware fonksiyonu üretir.

### 3. Asıl Controller'ı Çalıştırıyor

```js
await controller(req, res, next);
```

## Örnek 53

`controller` bizim örneğimizde `getMovies` ise bunu zihnimizde şöyle okuyabiliriz:

```js
await getMovies(req, res, next);
```

## Örnek 54

Wrapper Controller'ın yerine geçmez; onu dışarıdan sarar.

### 4. Hata Olursa next(error) Çağırıyor

```js
try {
  await controller(req, res, next);
} catch (error) {
  next(error);
}
```

## Örnek 55

Akış:

```text
Controller çalışır
↓
başarılıysa
↓
normal Response

hata olursa
↓
catch
↓
next(error)
↓
errorHandler
```

## Örnek 56

Artık `try/catch` her Controller'ın içinde değil, tek bir wrapper içinde durur.

---

## Router'da ctrlWrapper Nasıl Kullanılırdı?

Normalde:

```js
router.get("/", getMovies);
```

## Örnek 57

yazıyorduk.

Wrapper yaklaşımında:

```js
router.get(
  "/",
  ctrlWrapper(getMovies),
);
```

## Örnek 58

yazılabilirdi.

Akış:

```text
GET /
↓
ctrlWrapper(getMovies)
↓
getMovies çalışır
↓
başarılıysa Response

hata olursa
↓
wrapper catch
↓
next(error)
↓
errorHandler
```

## Örnek 59

Aynı yaklaşım diğer Controller'lara da uygulanabilirdi.

Böylece Express 4 döneminde tekrar eden:

```text
try/catch + next(error)
```

## Örnek 60

yapısı tek yerde toplanmış olurdu.

Ama `ctrlWrapper` Express'in zorunlu bir parçası değildir; özellikle eski Express sürümlerinde async Controller hatalarını merkezi hata akışına taşımayı kolaylaştıran yaygın bir yardımcı desendir.

`ctrlWrapper`ın hangi sorunu çözdüğünü artık biliyoruz. Şimdi Express 5'in aynı hata aktarımını nasıl sadeleştirdiğine bakalım.

---

## Tam Bu Noktada Express 5 ile Ne Değişti?

Express 5'te bir Route handler veya middleware bir Promise döndürüyorsa ve bu Promise reddedilirse Express hatayı otomatik olarak hata akışına aktarabilir.

`async` fonksiyonlar da Promise döndürür.

Bu nedenle şu Controller:

```js
export const getMovies = async (req, res) => {
  const movies = await Movie.find({});

  res.status(200).json({
    message: "Filmler getirildi.",
    data: movies,
  });
};
```

## Örnek 61

içinde:

```js
await Movie.find({});
```

## Örnek 62

hata verirse `async` fonksiyonun Promise'i reddedilir.

Express 5 bu hatayı hata akışına aktarır.

Artık sırf bu aktarım için:

```js
try {
  // ...
} catch (error) {
  next(error);
}
```

## Örnek 63

yazmamız gerekmez.

Yan yana:

```text
EXPRESS 4

async Controller
↓
hata
↓
catch
↓
next(error)
↓
errorHandler
```

## Örnek 64



```text
EXPRESS 5

async Controller
↓
hata
↓
Promise reddedildi
↓
Express otomatik aktarır
↓
errorHandler
```

## Örnek 65

Fark tam olarak budur.

---

## Kodu Değiştirmeden Önce: Biz Gerçekten Express 5 Kullanıyor muyuz?

Express 5 kullanabilmek için Node.js sürümümüzün en az:

```text
Node.js 18
```

## Örnek 66

olması gerekir.

Önce terminalde:

```bash
node -v
```

## Örnek 67

çalıştıralım.

Örneğin:

```text
v22.16.0
```

## Örnek 68

gibi bir sonuç görürsek baştaki ana sürüm numarasına bakarız:

```text
22
```

## Örnek 69

Bu değer:

```text
18 veya daha büyük
```

## Örnek 70

olduğu için Express 5 için uygundur.

Ama örneğin:

```text
v16.20.2
```

## Örnek 71

görürsek Node.js sürümümüz Express 5 için eski kalır.

Yani ilk kontrolümüz çok basit:

```text
node -v
↓
18 veya üzeri mi?
```

## Örnek 72

Bu kontrol, aşağıda kullanacağımız Express 5 davranışının proje ortamımızda gerçekten geçerli olduğunu doğrular.

---

### Node.js Sürümüm 18'den Küçükse Ne Yapacağım?

Başlangıç seviyesinde en kolay yol Node.js'in güncel **LTS** sürümünü kurmaktır.

LTS, uzun süre desteklenen sürümü ifade eder.

İzleyeceğimiz yol:

```text
Node.js resmi sitesi
↓
güncel LTS sürümünü indir
↓
kurulumu tamamla
↓
terminali kapatıp yeniden aç
↓
node -v
```

## Örnek 73

Kurulumdan sonra tekrar:

```bash
node -v
```

## Örnek 74

çalıştırın.

Artık:

```text
v18...
v20...
v22...
v24...
```

## Örnek 75

gibi 18 veya daha yüksek bir ana sürüm görüyorsanız Express 5'in Node.js şartını karşılıyorsunuz.

Ardından npm'in de kullanılabildiğini doğrulamak için:

```bash
npm -v
```

## Örnek 76

çalıştırabilirsiniz.

Önemli nokta:

```text
Node.js'i güncelledim
↓
eski terminal hâlâ açıksa
↓
eski sürümü göstermeye devam edebilir
```

## Örnek 77

Bu nedenle kurulumdan sonra terminali kapatıp yeniden açın.

Gerekirse bilgisayarı yeniden başlatın ve:

```bash
node -v
```

## Örnek 78

ile tekrar kontrol edin.

---

### Peki Express Sürümüm Kaç?

Proje klasöründe:

```bash
npm list express
```

## Örnek 79

çalıştırın.

Örneğin:

```text
express@5.1.0
```

## Örnek 80

gibi bir sonuç:

```text
Express 5 kullanıyoruz
```

## Örnek 81

demektir.

Ama:

```text
express@4.21.2
```

## Örnek 82

gibi bir sonuç görürseniz proje Express 4 kullanıyordur.

Kontrolümüz:

```text
npm list express
↓
5.x.x mi?
```

## Örnek 83

---

### Express 4 Kuruluysa Express 5'e Nasıl Geçeriz?

Önce Node.js sürümünün uygun olduğundan emin olun:

```bash
node -v
```

## Örnek 84

Sonuç en az:

```text
v18...
```

## Örnek 85

olmalı.

Ardından proje klasöründe:

```bash
npm install express@5
```

## Örnek 86

çalıştırabiliriz.

Kurulum tamamlandıktan sonra tekrar:

```bash
npm list express
```

## Örnek 87

çalıştırın.

Bu kez:

```text
express@5.x.x
```

## Örnek 88

gibi bir sonuç görmeyi bekleriz.

Ayrıca `package.json` içindeki Express sürümü de npm tarafından güncellenir.

Kısaca:

```text
node -v
↓
18 veya üzeri

npm list express
↓
Express 4 mü?

evet
↓
npm install express@5
↓
npm list express
↓
Express 5
```

## Örnek 89

Burada küçük bir uyarı önemli:

Express 4'ten Express 5'e geçiş bir **ana sürüm yükseltmesidir**.

Yani eski ve büyük bir projede:

```text
npm install express@5
```

## Örnek 90

yazıp hiçbir şeyi kontrol etmeden devam etmek doğru değildir.

Express 5'te bazı eski kullanımlar değişmiştir.

Geliştirdiğimiz Movie API'de Express 5 davranışını esas alacağız.

Eski bir gerçek projeyi yükseltirken ise Express'in Express 5 geçiş rehberindeki değişiklikler ayrıca kontrol edilmelidir.

---

## Burada İnce Bir Nokta Var: Express 5 Her Asenkron Hatayı Görür mü?

Kısa cevap:

```text
Hayır.
```

## Örnek 91

Bu cevabı ezberlemek yerine tek bir bağlantıyı takip etmek yeterlidir:

> **Express bu Promise'i görebiliyor mu?**

Bütün örnekleri bu soruyla çözeceğiz.

Express 5'in otomatik hata aktarımının kalbi şu bağlantıdır:

```text
Express'in çalıştırdığı handler
↓
handler bir Promise döndürüyor
↓
o Promise reddediliyor
↓
Express bunu görüyor
↓
hatayı errorHandler'a aktarıyor
```

## Örnek 92

`async` fonksiyonlar otomatik olarak Promise döndürür.

`getMovies` bir `async` handler olduğu için çağrıldığında bir Promise döndürür. Express de kendi çalıştırdığı handler'ın döndürdüğü bu Promise'i takip eder.

---

### 1. await Edilen İşlem Hata Verirse — Express Zincirin İçinde

Şu satıra bakalım:

```js
const movies = await Movie.find({});
```

## Örnek 93

`Movie.find({})` tarafından döndürülen Promise reddedilirse:

```text
Movie.find({})
↓
Promise reddedildi
↓
await bu hatayı async getMovies'a taşıdı
↓
getMovies'ın döndürdüğü Promise reddedildi
↓
Express 5 bunu gördü
↓
hata akışı
↓
errorHandler
```

## Örnek 94

Bu durumda bizim ayrıca:

```js
try {
  // ...
} catch (error) {
  next(error);
}
```

## Örnek 95

yazmamız gerekmez.

En kısa hali:

```text
await edilen Promise
↓
hata
↓
async handler'ın Promise'i reddedilir
↓
Express 5 görür
```

## Örnek 96

---

### 2. async Fonksiyon İçinde throw Kullanırsak — Express Yine Haberdar

Örneğin:

```js
export const getMovies = async (req, res) => {
  throw new Error("Test hatası");
};
```

## Örnek 97

`getMovies` bir `async` fonksiyon olduğu için içeride fırlatılan hata, fonksiyonun döndürdüğü Promise'in reddedilmesine neden olur. Express 5 bu reddedilmeyi hata olarak devralır.

Akış:

```text
async getMovies
↓
throw new Error(...)
↓
getMovies'in Promise'i reddedilir
↓
Express 5 hata akışına aktarır
↓
errorHandler çalışır
```

## Örnek 98

Buradaki `throw new Error("Test hatası")` satırı yalnızca bu mekanizmayı görmek için kullandığımız geçici bir testtir; gerçek `GET /movies` davranışının parçası değildir. Test tamamlandığında bu satırı kaldırır ve `getMovies` fonksiyonunu yeniden veritabanından filmleri getirip response döndüren gerçek görevine bırakırız.

Asıl öğrenmemiz gereken nokta şudur: `async` handler'ın döndürdüğü Promise reddedildiğinde Express 5 hatayı merkezi hata yönetimine aktarabildiği için sırf bu aktarım amacıyla ayrıca `try/catch` ve `next(error)` yazmamız gerekmez.

Proje kodumuzun kalıcı davranışı:

```text
GET /movies
↓
filmleri getir
```

## Örnek 99

olmaya devam eder.

---

### 3. Promise'i return Edersek — Express'e Teslim Etmiş Oluruz

Her handler'ın `async` olması şart değildir.

Örneğin:

```js
app.get("/test", (req, res) => {
  return Promise.resolve()
    .then(() => {
      throw new Error("Test hatası");
    });
});
```

## Örnek 100

Burada önemli kelime:

```js
return
```

## Örnek 101

Çünkü Route handler oluşan Promise'i Express'e geri döndürüyor.

Akış:

```text
handler
↓
Promise'i return etti
↓
Promise reddedildi
↓
Express 5 reddedilmeyi gördü
↓
hata akışı
```

## Örnek 102

---

### 4. Promise'i Ne await Ne de return Edersek?

Fark, Promise'in Express'e geri döndürülmemesinde ortaya çıkar.

Şöyle bir kod düşünün:

```js
app.get("/test", (req, res, next) => {
  Promise.resolve()
    .then(() => {
      throw new Error("Test hatası");
    })
    .catch(next);
});
```

## Örnek 103

Buradaki Promise:

```text
handler tarafından return edilmiyor
```

## Örnek 104

ve:

```text
await edilmiyor
```

## Örnek 105

Bu nedenle Express bu Promise'i kendi başına takip etmiyor.

Biz hatayı:

```js
.catch(next);
```

## Örnek 106

ile kendimiz Express'e gönderiyoruz.

Akış:

```text
ayrı Promise başladı
↓
Promise reddedildi
↓
Express bunu dönen Promise olarak takip etmiyor
↓
.catch(next)
↓
hatayı Express'e biz verdik
↓
errorHandler
```

## Örnek 107

Eğer burada:

```js
.catch(next);
```

## Örnek 108

da olmazsa Express hatayı merkezi hata akışına otomatik olarak taşıyamaz.

---

### someAsyncOperation() Neden Promise Döndürüyor?

Aşağıdaki örneklerde `someAsyncOperation()` adlı örnek bir asenkron işlem kullanacağız. Önce bu çağrının neden bir Promise döndürdüğünü netleştirelim.

Örnekte kullandığımız fonksiyon:

```js
const someAsyncOperation = () => {
  return Promise.reject(new Error("Test hatası"));
};
```

## Örnek 109

Burada:

```text
someAsyncOperation
↓
bir fonksiyon
```

## Örnek 110

Fonksiyonu çağırdığımızda:

```js
someAsyncOperation();
```

## Örnek 111

içerideki:

```js
Promise.reject(...)
```

## Örnek 112

çalışır.

`Promise.reject(...)` bize reddedilmiş bir Promise verir.

Yani:

```text
someAsyncOperation
↓
fonksiyon

someAsyncOperation()
↓
fonksiyon çalışır

Promise.reject(...)
↓
Promise oluşur

sonuç
↓
someAsyncOperation() bir Promise döndürür
```

## Örnek 113

Burada ince ama önemli ayrım şudur:

> `someAsyncOperation` kendi başına bir Promise değildir.  
> **Çağrıldığında Promise döndüren bir fonksiyondur.**

Bu yüzden:

```js
someAsyncOperation()
  .catch(...);
```

## Örnek 114

yazabiliriz.

Çünkü `.catch()` metodunu fonksiyonun kendisine değil, fonksiyon çağrısından dönen Promise'e bağlıyoruz.

Bu nedenle sonraki örneklerde `someAsyncOperation()` çağrısını şöyle okuyabiliriz:

```text
someAsyncOperation()
↓
Promise
```

## Örnek 115

---

### Tek Kuralımızı Yeniden Kullanalım

Asenkron bir kod gördüğünüzde kendinize şu soruyu sorun:

> **Express bu Promise'i görebiliyor mu?**

Bunu biraz daha teknik söylersek:

> Bu Promise, Express'in çalıştırdığı handler'ın döndürdüğü Promise'in parçası mı?

Cevap:

```text
EVET
```

## Örnek 116

ise Express 5 Promise reddedildiğinde hatayı otomatik olarak hata akışına aktarır.

Örneğin:

```js
app.get("/test", async (req, res) => {
  await someAsyncOperation();
});
```

## Örnek 117

veya:

```js
app.get("/test", (req, res) => {
  return someAsyncOperation();
});
```

## Örnek 118

Cevap:

```text
HAYIR
```

## Örnek 119

ise bu Promise'in reddedilmesi otomatik olarak Express hata akışına bağlanmaz.

Bu durumda hatayı:

```js
next(error);
```

## Örnek 120

veya Promise zincirinde:

```js
.catch(next);
```

## Örnek 121

ile kendimiz Express'e ulaştırmamız gerekebilir.

---

### Şimdi Üç Örneğe Tek Bakışta Karar Verelim

#### Express 5 otomatik aktarabilir

```js
app.get("/test", async (req, res) => {
  await someAsyncOperation();
});
```

## Örnek 122

Çünkü:

```text
async handler
↓
Promise döndürür
↓
await edilen hata
↓
handler Promise'i reddedilir
↓
Express görür
```

## Örnek 123

#### Express 5 yine otomatik aktarabilir

```js
app.get("/test", (req, res) => {
  return someAsyncOperation();
});
```

## Örnek 124

Çünkü:

```text
Promise return edildi
↓
Express takip edebilir
```

## Örnek 125

#### Hatayı kendimiz aktarıyoruz

```js
app.get("/test", (req, res, next) => {
  someAsyncOperation()
    .catch(next);
});
```

## Örnek 126

Bu örneğin akışı şöyledir:

```text
someAsyncOperation()
↓
Promise döner
```

## Örnek 127

Fakat handler bu Promise'i:

```js
return someAsyncOperation();
```

## Örnek 128

şeklinde Express'e döndürmüyor.

Yani:

```text
Promise oluştu
↓
ama handler'ın return değeri olmadı
```

## Örnek 129

Bu nedenle Express bu Promise'in reddedilmesini kendi başına takip eden taraf değildir.

Biz:

```js
.catch(next);
```

## Örnek 130

ekleyerek şunu söylüyoruz:

```text
Promise reddedilirse
↓
hatayı next'e ver
↓
Express hata akışına geçsin
```

## Örnek 131

Buradaki `.catch()`:

```text
someAsyncOperation fonksiyonuna
```

## Örnek 132

değil:

```text
someAsyncOperation() çağrısından
dönen Promise'e
```

## Örnek 133

bağlanır.

Böylece hata bağlantısını kendimiz kurmuş oluruz.

---

### O Hâlde "Express 5 Asenkron Hataları Yakalar" Cümlesini Nasıl Okuyacağız?

Bundan sonra bu cümleyi:

```text
Express 5
↓
Express handler'ının döndürdüğü Promise
↓
reject olursa
↓
next(error) çağrılmış gibi
hata akışına aktarır
```

## Örnek 134

şeklinde anlayın.

Şöyle anlamayın:

```text
uygulamanın herhangi bir yerinde
herhangi bir zamanda
bir asenkron hata oluştu
↓
Express 5 mutlaka yakalar
```

## Örnek 135

Bu doğru değildir.

En kısa kuralımız:

```text
Express Promise'i GÖRÜYOR mu?
↓
EVET
↓
reddedilmeyi hata akışına aktarabilir


Express Promise'i GÖRMÜYOR mu?
↓
EVET
↓
hatayı next(error)
veya .catch(next)
ile biz ulaştırırız
```

## Örnek 136

Bu ayrım oturduğunda Express 5'in async hata davranışı ezber olmaktan çıkar.

---

## Şimdi Baştaki Tekrarın Neden Kaybolduğunu Görebiliriz

`getMovies` artık:

```js
export const getMovies = async (req, res) => {
  const movies = await Movie.find({});

  res.status(200).json({
    message: "Filmler getirildi.",
    data: movies,
  });
};
```

## Örnek 137

olabilir.

Burada:

```text
try yok
catch yok
next(error) yok
```

## Örnek 138

Ama `Movie.find()` beklenmeyen bir hata verirse:

```text
getMovies
↓
Promise reddedildi
↓
Express 5
↓
errorHandler
↓
500 Response
```

## Örnek 139

akışı oluşabilir.

---

## Her Sorun errorHandler'a mı Gitmeli? 400 ve 404 Neden Kalıyor?

Çünkü beklenen API durumları ile beklenmeyen teknik hatalar aynı değildir.

Geçersiz `movieId`:

```js
if (!mongoose.isObjectIdOrHexString(movieId)) {
  return res.status(400).json({
    message: "Geçersiz film kimliği.",
  });
}
```

## Örnek 140

Bu bilinçli bir:

```text
400 Bad Request
```

## Örnek 141

kararıdır.

Film bulunamadığında:

```js
if (movie === null) {
  return res.status(404).json({
    message: "Film bulunamadı.",
  });
}
```

## Örnek 142

Bu da bilinçli bir:

```text
404 Not Found
```

## Örnek 143

kararıdır.

Ama MongoDB beklenmeyen biçimde hata verirse:

```text
beklenmeyen teknik hata
↓
Express hata akışı
↓
errorHandler
↓
500
```

## Örnek 144

olur.

Kısaca:

```text
GEÇERSİZ İSTEK
↓
Controller
↓
400
```

## Örnek 145



```text
FİLM YOK
↓
Controller
↓
404
```

## Örnek 146



```text
BEKLENMEYEN HATA
↓
errorHandler
↓
500
```

## Örnek 147

---

## Şimdi Controller Dosyasına Bir Bütün Olarak Bakalım

```js
import mongoose from "mongoose";

import { Movie } from "../db/models/movie.js";

export const getMovies = async (req, res) => {
  const movies = await Movie.find({});

  res.status(200).json({
    message: "Filmler getirildi.",
    data: movies,
  });
};

export const getMovieById = async (req, res) => {
  const { movieId } = req.params;

  if (!mongoose.isObjectIdOrHexString(movieId)) {
    return res.status(400).json({
      message: "Geçersiz film kimliği.",
    });
  }

  const movie = await Movie.findById(movieId);

  if (movie === null) {
    return res.status(404).json({
      message: "Film bulunamadı.",
    });
  }

  res.status(200).json({
    message: "Film getirildi.",
    data: movie,
  });
};

export const createMovie = async (req, res) => {
  const {
    title,
    releaseYear,
    voteAverage,
  } = req.body;

  const movie = new Movie({
    title,
    releaseYear,
    voteAverage,
  });

  await movie.save();

  res.status(201).json({
    message: "Film oluşturuldu.",
    data: movie,
  });
};

export const updateMovieById = async (req, res) => {
  const { movieId } = req.params;

  if (!mongoose.isObjectIdOrHexString(movieId)) {
    return res.status(400).json({
      message: "Geçersiz film kimliği.",
    });
  }

  const movie = await Movie.findById(movieId);

  if (movie === null) {
    return res.status(404).json({
      message: "Film bulunamadı.",
    });
  }

  const {
    title,
    releaseYear,
    voteAverage,
  } = req.body;

  const hasUpdate =
    title !== undefined ||
    releaseYear !== undefined ||
    voteAverage !== undefined;

  if (!hasUpdate) {
    return res.status(400).json({
      message: "Güncellenecek film bilgisi gönderilmedi.",
    });
  }

  if (title !== undefined) {
    movie.title = title;
  }

  if (releaseYear !== undefined) {
    movie.releaseYear = releaseYear;
  }

  if (voteAverage !== undefined) {
    movie.voteAverage = voteAverage;
  }

  await movie.save();

  res.status(200).json({
    message: "Film güncellendi.",
    data: movie,
  });
};

export const deleteMovieById = async (req, res) => {
  const { movieId } = req.params;

  if (!mongoose.isObjectIdOrHexString(movieId)) {
    return res.status(400).json({
      message: "Geçersiz film kimliği.",
    });
  }

  const deletedMovie =
    await Movie.findByIdAndDelete(movieId);

  if (deletedMovie === null) {
    return res.status(404).json({
      message: "Film bulunamadı.",
    });
  }

  res.status(200).json({
    message: "Film silindi.",
    data: deletedMovie,
  });
};
```

## Örnek 148

Merkezi hata yönetiminden önce Controller'ların yapısı şöyleydi:

```text
asıl işlem
↓
try/catch
↓
500 yönetimi
```

## Örnek 149

Şimdi dosyaya baktığımızda gözümüz doğrudan film işlemlerine gidiyor.

Tekrar eden `try/catch` blokları artık Controller'ların asıl işini perdelemiyor.

---

## next(error) Artık Gereksiz mi?

Hayır.

Express 5 `next(error)` kavramını kaldırmadı.

Sadece handler'ın döndürdüğü Promise reddedildiğinde hatayı bizim yerimize hata akışına aktarabiliyor.

Kısa ayrım:

```text
next()
↓
normal akış
```

## Örnek 150



```text
next(error)
↓
hata akışı
```

## Örnek 151



```text
Express 5 + reddedilen handler Promise'i
↓
Express otomatik aktarabilir
```

## Örnek 152

---

## try/catch Artık Kullanılmayacak mı?

Hayır.

`try/catch` JavaScript'in önemli hata yönetimi araçlarından biridir.

Biz yalnızca şu tekrarı kaldırdık:

```js
try {
  await someAsyncOperation();
} catch (error) {
  next(error);
}
```

## Örnek 153

Eğer tek amaç reddedilen Promise'i Express hata akışına taşımaksa Express 5 bunu bizim için yapabilir.

Ama hatayı Controller içinde özellikle yakalayıp farklı bir işlem yapmak istersek `try/catch` yine kullanılabilir.

---

## Proje Yapımız Şimdi Nasıl?

```text
src
├── controllers
│   └── movies.js
├── db
│   ├── initMongoConnection.js
│   └── models
│       └── movie.js
├── middlewares
│   ├── errorHandler.js
│   └── requestLogger.js
├── routers
│   └── movies.js
├── index.js
└── server.js
```

## Örnek 154

Akış:

```text
Request
↓
requestLogger
↓
Router
↓
Controller
↓
başarılıysa Response

beklenmeyen hata varsa
↓
errorHandler
↓
500 Response
```

## Örnek 155

---

## 🧠 Akılda Kalsın

```js
next();
```

## Örnek 156

normal middleware akışını sürdürür.

```js
next(error);
```

## Örnek 157

hatayı Express'in hata akışına aktarır.

Hata middleware'i dört parametreyle tanımlanır:

```js
(error, req, res, next)
```

## Örnek 158

Express 5'te temel kural şudur:

```text
Express'in çalıştırdığı handler
↓
Promise döndürür
↓
Promise reddedilir
↓
Express hatayı errorHandler'a aktarır
```

## Örnek 159

Handler'dan bağımsız başlatılan ve `return` ya da `await` ile bu zincire bağlanmayan bir Promise için aynı otomatik aktarım geçerli değildir. Böyle bir durumda hatayı `next(error)` veya `.catch(next)` ile hata akışına bağlamak gerekir.

Beklenen API durumları ile beklenmeyen teknik hataları da ayırıyoruz:

```text
geçersiz istek → 400
kaynak bulunamadı → 404
beklenmeyen teknik hata → errorHandler → 500
```

## Örnek 160

---

## Bölüm Özeti

Bu bölümde tekrar eden `try/catch` bloklarını merkezi hata yönetimine taşıdık. `next(error)` ile normal `next()` çağrısının farklı akışlar başlattığını ve dört parametreli error middleware'in bu hataları karşıladığını gördük.

Express 4 döneminde kullanılan `try/catch`, `.catch(next)` ve `ctrlWrapper` yaklaşımlarının neden gerekli olabildiğini öğrendik. Express 5'te ise Route veya Middleware'in döndürdüğü Promise reddedildiğinde hata otomatik olarak hata akışına aktarılır. Ancak Express'in görmediği, bağımsız başlatılmış asenkron işlemler için aynı garantiyi veremeyiz.

Son olarak geçersiz kimlik gibi beklenen API durumlarını Controller'da `400` veya `404` ile karşılamaya devam ettik. Beklenmeyen hatalar ise merkezi `errorHandler` üzerinden yönetiliyor. Geçici hata testini kaldırıp çalışan `getMovies` fonksiyonunu geri getirdik.

---

## Sonraki Adım

Hata yolculuğunu düzene soktuk.

Ama uygulamanın karşılaşacağı her problem bir sunucu hatası değildir.

Bazen kodumuz çalışır, MongoDB'ye ulaşır, hiçbir şey çökmez... fakat gelen verinin kendisi sorunludur.

Şu veriyi düşünün:

```json
{
  "title": "",
  "releaseYear": 1800,
  "voteAverage": 25
}
```

## Örnek 161

Backend yalnızca veri geldi diye bunu kabul etmeli mi?

Yoksa Movie Model'in:

```text
hangi değer geçerli?
hangi değer geçersiz?
```
