# Bölüm 69 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Node.js API Canlı Ortama Nasıl Yayımlanır?

## Arkadaşımıza `localhost` Linkini Göndersek Neden Çalışmaz?

Movie API'miz kendi bilgisayarımızda çalışıyor:

```text
http://localhost:3000
```

## Örnek 2

Bu adresi bir arkadaşımıza gönderdiğimizi düşünelim.

O da browser'a:

```text
http://localhost:3000/movies
```

## Örnek 3

yazıyor.

Ama bizim Movie API'mize ulaşamıyor.

Neden?

Çünkü onun bilgisayarında:

```text
localhost
↓
onun kendi bilgisayarı
```

## Örnek 4

anlamına gelir.

Bizim Node.js process'imiz ise bizim bilgisayarımızda çalışıyor.

Gerçek kullanıcıların API'ye ulaşabilmesi için uygulamanın:

```text
internetten erişilebilen bir server'da çalışması
+
public bir URL'ye sahip olması
```

## Örnek 5

gerekir.

Bu sürece genel olarak **deployment**, yani uygulamayı çalışacağı ortama yayımlama diyeceğiz.

Bu bölümde somut örnek olarak Render kullanacağız.

Ama asıl hedef Render ekranlarını ezberlemek değil; Node.js API'nin başka bir makinede çalışması için neye ihtiyaç duyduğunu anlamaktır.

---

## Projeyi Terminalden Başlatabildiğimizi Doğrulayalım

Canlı platform bizim VS Code'umuzu açıp:

```text
Run
```

## Örnek 6

butonuna basmayacak.

Projeyi komutla başlatabilmeliyiz.

`package.json`:

```json
{
  "scripts": {
    "dev": "node --watch src/index.js",
    "start": "node src/index.js"
  }
}
```

## Örnek 7

Lokal terminalde:

```bash
npm start
```

## Örnek 8

çalıştıralım.

Ardından:

```http
GET http://localhost:3000/movies
```

## Örnek 9

gönderelim.

Bu adım geçmiyorsa deployment'a başlamayalım.

Çünkü platformda çalıştıracağımız temel komut da aynı olacaktır:

```text
npm start
```

## Örnek 10

---

## Canlı Server Bizim `.env` Dosyamızı Nereden Bulacak?

Lokal bilgisayarımızda:

```text
.env
```

## Örnek 11

dosyası var.

Ama bu dosyayı Git'e göndermiyoruz.

Bu bilinçli bir güvenlik kararıdır.

Peki canlı server:

```text
MONGODB_URL
CORS_ORIGIN
RATE_LIMIT_WINDOW_MS
RATE_LIMIT_LIMIT
LOG_LEVEL
```

## Örnek 12

değerlerini nereden alacak?

Hosting platformunun environment variable ayarlarından.

Yani:

```text
lokal
↓
.env

canlı platform
↓
platformun environment variable ayarları
```

## Örnek 13

Aynı uygulama kodu iki ortamda da:

```js
env.mongodbUrl
env.port
env.corsOrigin
```

## Örnek 14

kullanır.

63. bölümde config katmanını kurmamızın gerçek faydası burada görünür.

---

## Portu Kodun İçine Yazmamak Neden Önemliydi?

Lokal ortamda:

```env
PORT=3000
```

## Örnek 15

kullanabiliriz.

Hosting platformu ise bize başka bir port verebilir.

Render web service'in platformun sağladığı `PORT` environment variable değerine bağlanmasını önerir.

`index.js` içinde:

```js
app.listen(
  env.port,
  "0.0.0.0",
  () => {
    logger.info(
      { port: env.port },
      "Server çalışmaya başladı.",
    );
  },
);
```

## Örnek 16

kullanabiliriz.

Buradaki:

```text
0.0.0.0
```

## Örnek 17

ifadesini basitçe şöyle düşünebiliriz:

```text
server yalnızca kendi makinesinden gelen bağlantıyı dinlemesin
↓
platformun yönlendirdiği dış bağlantıları da kabul edebilsin
```

## Örnek 18

Render public web service için uygulamanın `0.0.0.0` üzerinde bir porta bağlanmasını bekler.

---

## Canlıya Göndermeden Önce Basit Bir `/health` Endpoint'i Ekleyelim

Hosting platformu düzenli olarak şu soruyu sorabilir:

> Uygulama HTTP Request'e cevap verebiliyor mu?

Bunun için:

```js
app.get("/health", (req, res) => {
  return res.status(200).json({
    status: "ok",
  });
});
```

## Örnek 19

kullanabiliriz.

Response:

```json
{
  "status": "ok"
}
```

## Örnek 20

Bu endpoint Movie verisi döndürmez.

Sadece operasyonel bir soru cevaplar:

```text
HTTP server ayakta mı?
```

## Örnek 21

`/health` Route'unu global API rate limiter'dan önce tanımlayabiliriz.

Çünkü hosting platformunun otomatik health Request'lerinin normal kullanıcı rate limit sayacına dahil olmasını istemeyiz.

---

## Deployment Öncesi Lokal Kontrol

Canlı platforma geçmeden önce şu dört şey çalışmalı:

```text
npm install
↓
package'lar kuruluyor mu?

npm start
↓
uygulama başlıyor mu?

GET /health
↓
200 dönüyor mu?

Postman Local Collection
↓
temel testler geçiyor mu?
```

## Örnek 22

Bu kontrol bize şunu sağlar:

> Platforma taşıdığımız problemler ile zaten lokal ortamda var olan problemleri birbirine karıştırmayız.

---

## Render'da İlk Web Service'i Oluşturalım

Render Dashboard'da repository'mizi bağlayıp bir **Web Service** oluşturabiliriz.

Genel akış:

```text
Git repository bağla
↓
Node.js Web Service oluştur
↓
build / start komutlarını belirt
↓
environment variables ekle
↓
deploy
```

## Örnek 23

UI üzerindeki buton ve menü adları zamanla değişebilir.

Bu yüzden ekran ezberlemek yerine alanların anlamını öğrenelim.

**Kitabın kod deposundan yayımlıyorsanız:** GitHub deposunun kök dizininde değil, 70. bölümün altındaki `code/movie-api` klasöründe `package.json` bulunur. Render Web Service ayarlarında **Root Directory** alanını şu şekilde belirleyin:

```text
08_Test_Documentation_Deployment/70_Final_Proje_Movie_API_yi_Bastan_Sona_Nasil_Tamamlariz/code/movie-api
```

## Örnek 24

Böylece aşağıdaki `npm install` ve `npm start` komutları doğru klasörde çalışır. Kendi uygulamanızı ayrı bir depo kökünden yayımlıyorsanız bu yolu kullanmanız gerekmez.

### Build Command

Uygulamanın dependency'leri hazırlanmalıdır.

Basit bir Node.js projesinde örneğin:

```text
npm install
```

## Örnek 25

kullanılabilir.

### Start Command

Bizim uygulamamız için:

```text
npm start
```

## Örnek 26

kullanabiliriz.

Çünkü gerçek başlangıç dosyasını `package.json` içinde biz tanımladık.

---

## Production Environment Variables'ı Ekleyelim

Platformun environment variable bölümüne gerçek canlı değerleri ekleyelim:

```text
MONGODB_URL
CORS_ORIGIN
RATE_LIMIT_WINDOW_MS
RATE_LIMIT_LIMIT
LOG_LEVEL
```

## Örnek 27

`PORT` değerini Render sağlayabilir.

Burada `.env` dosyasını upload etmek veya repository'ye koymak zorunda değiliz.

Mantık:

```text
secret değerler
↓
kodun dışında
↓
runtime sırasında platformdan gelir
```

## Örnek 28

---

## İlk Deploy Başarısız Olursa Nereden Bakacağız?

Deployment'ta ilk hata çıktığında rastgele ayar değiştirmek yerine logları okuyalım.

Örneğin `MONGODB_URL` eklemeyi unutmuşsak bizim config kodumuz başlangıçta hata üretebilir.

Bu durumda 66. bölümde kurduğumuz logger sayesinde başlangıç hatasını görebiliriz.

Başka bir örnek:

```text
uygulama başladı
ama platform public URL'ye bağlanamıyor
```

## Örnek 29

ise port / host ayarlarını kontrol ederiz.

Teşhis sırası şöyle olabilir:

```text
build tamamlandı mı?
↓
start komutu çalıştı mı?
↓
config hatası var mı?
↓
MongoDB bağlantısı kuruluyor mu?
↓
server doğru portta dinliyor mu?
```

## Örnek 30

Bu yaklaşım deployment'ı “butona bas ve um” işleminden çıkarır.

---

## MongoDB Atlas Canlı Server'a İzin Veriyor mu?

Render'da çalışan Node.js process de MongoDB Atlas'a bağlanmak zorundadır.

Akış:

```text
Render Web Service
↓
MONGODB_URL
↓
MongoDB Atlas
```

## Örnek 31

Connection string doğru olsa bile Atlas Network Access kuralları canlı ortamın bağlantısına izin vermiyorsa bağlantı kurulamaz.

Burada şu kötü alışkanlığı edinmeyelim:

> Sadece çalışsın diye veritabanı erişimini gereksiz yere herkese aç.

Hosting sağlayıcınızın ağ yapısına ve Atlas'ın güncel Network Access seçeneklerine göre uygun kuralı seçin.

Bu bölümde sabit bir IP ezberi yapmıyoruz; ağ özellikleri sağlayıcıya göre değişebilir.

---

## Public URL Geldi: Gerçekten Çalışıyor mu?

Deploy başarılı olduğunda Render bize public HTTPS URL verir.

Örneğin:

```text
https://movie-api-example.onrender.com
```

## Örnek 32

Bu yalnızca örnek bir URL'dir.

Önce en basit kontrol:

```http
GET https://.../health
```

## Örnek 33

Beklenti:

```http
200 OK
```

## Örnek 34

Sonra:

```http
GET https://.../movies
```

## Örnek 35

ve:

```text
https://.../docs
```

## Örnek 36

adreslerini kontrol edelim.

Burada ilk kez Movie API'miz kendi bilgisayarımız dışında gerçek bir URL'de çalışıyor.

---

## Aynı Postman Testlerini Production'da Çalıştıralım

67. bölümde:

```text
Local
↓
baseUrl = http://localhost:3000
```

## Örnek 37

oluşturmuştuk.

Şimdi `Production` Environment:

```text
baseUrl = https://movie-api-example.onrender.com
```

## Örnek 38

olsun.

Request'leri değiştirmiyoruz:

```http
{{baseUrl}}/movies
```

## Örnek 39

Sadece Environment seçimini değiştiriyoruz.

Collection Runner'ı tekrar çalıştırıyoruz.

**Önemli:** Collection'ımız film oluşturma, güncelleme ve silme Request'leri de içeriyor. Bu nedenle Runner'ı gerçek kullanıcı kayıtlarının bulunduğu veritabanına karşı çalıştırmayalım. Önce ayrı bir test veritabanı veya yalnızca deneme kayıtları kullanan bir ortam hazırlayalım. Böylece kontrol ederken gerçek verileri değiştirmemiş oluruz.

Bu çok güçlü bir final kontroldür:

```text
aynı test akışı
↓
Local API'de geçti
↓
Production API'de de geçiyor mu?
```

## Örnek 40

---

## Rate Limiting Canlı Ortamda Aynı Kullanıcıyı mı Sayıyor?

65. bölümde Request sayısını sınırlamıştık. Yerel bilgisayarımızda bu kontrolü kolayca test edebildik. Ancak canlı ortamda araya hosting platformunun **proxy** adı verilen yönlendirme katmanı girebilir.

Böyle bir durumda uygulama, farklı kullanıcıları aynı IP adresinden geliyormuş gibi görebilir. Sonuç olarak bir kişinin Request'leri başka kişilerin limitini de doldurabilir.

Canlıya çıktığımızda yalnızca `429` Response'unun döndüğüne bakmayalım. Farklı bağlantılardan gelen Request'lerin doğru biçimde ayrı sayıldığını da kontrol edelim. Eğer loglarda `X-Forwarded-For` veya `trust proxy` ile ilgili bir uyarı görürsek Express'in proxy ayarlarını hosting platformunun ağ yapısına göre doğrulamamız gerekir.

**Burada herkese uyan sabit bir `trust proxy` değeri yoktur.** Değeri kontrol etmeden `true` yapmak güvenilir olmayan IP bilgisini kabul etme riskini doğurabilir. Amacımız bu aşamada karmaşık ağ ayarlarını ezberlemek değil, Rate Limiting'in canlı ortamda gerçekten beklediğimiz şekilde çalıştığını doğrulamaktır.

---

## CORS Neden Canlıda Tekrar Karşımıza Çıkabilir?

Lokal frontend için:

```env
CORS_ORIGIN=http://localhost:5173
```

## Örnek 41

kullanmış olabiliriz.

Canlı frontend daha sonra:

```text
https://movies.example.com
```

## Örnek 42

adresinde çalışırsa production config'teki `CORS_ORIGIN` değeri de değişmelidir.

Kod değişmez.

Environment variable değişir.

Bu da config katmanının neden önemli olduğunu yeniden gösterir.

---

## Render Health Check'i `/health` Üzerinden Yapabilir

Render'da HTTP health check path olarak:

```text
/health
```

## Örnek 43

belirleyebiliriz.

Render, HTTP health check için `2xx` veya `3xx` Response'u healthy kabul edebilir.

Biz:

```http
200 OK
```

## Örnek 44

döndürüyoruz.

Böylece platform yalnızca portun açık olup olmadığını değil, HTTP uygulamasının cevap verip vermediğini de kontrol edebilir.

---

## Yeni Commit Gönderdiğimizde Ne Olur?

Git tabanlı deployment kullanıyorsak bağlı branch'e yeni commit gönderildiğinde provider yeni deploy başlatabilir.

Kabaca:

```text
Git push
↓
yeni build
↓
yeni uygulama sürümü başlar
↓
health check geçer
↓
yeni sürüm trafik alır
```

## Örnek 45

Bu yüzden üretime gönderilecek her değişiklikten önce Postman testlerini çalıştırmak iyi bir alışkanlıktır.

---

## Deployment Mantığı Platformdan Bağımsızdır

Bu bölümde Render kullandık.

Başka platformlarda ekranlar ve ayar adları farklı olabilir.

Ama şu sorular yine karşımıza çıkar:

```text
uygulama nasıl kurulacak?
↓
hangi komutla başlayacak?
↓
config nereden gelecek?
↓
hangi porta bağlanacak?
↓
veritabanına erişebiliyor mu?
↓
logları nereden okuyacağız?
↓
health check nasıl yapılacak?
↓
public URL nedir?
```

## Örnek 46

Bunları anlayabiliyorsak başka bir provider'a geçmek daha kolay olur.

---

## Bölüm Sonu Uygulaması

1. `package.json` içinde `start` script'ini doğrulayın.
2. `npm start` ile uygulamayı lokal ortamda başlatın.
3. `.env` dosyasının Git'e gitmediğini ve `.env.example` dosyasının güncel olduğunu kontrol edin.
4. `/health` endpoint'ini ekleyip lokal ortamda `200` döndüğünü görün.
5. Render'da repository'yi bağlayıp Web Service oluşturun.
6. Start Command olarak `npm start` kullanın.
7. Production environment variable değerlerini platforma ekleyin.
8. Deploy loglarını okuyup uygulamanın başarılı başladığını doğrulayın.
9. Canlı `/health`, `/movies` ve `/docs` adreslerini kontrol edin.
10. Postman `Production` Environment oluşturun; veri değiştiren Collection testlerini gerçek kayıtlar yerine ayrılmış test ortamında çalıştırın.
11. Canlı ortamda farklı kullanıcıların Rate Limiting sayacını yanlışlıkla paylaşmadığını doğrulayın.

---

## 🧠 Akılda Kalsın

Deployment:

```text
aynı uygulama kodunu
↓
başka bir çalışma ortamında
↓
public URL ile çalıştırmak
```

## Örnek 47

olarak düşünülebilir.

Canlı ortamda özellikle şu parçalar birleşir:

```text
npm start
config / environment variables
PORT / host
MongoDB erişimi
logs
health check
Postman tests
OpenAPI docs
```

## Örnek 48

En önemli fikir:

```text
production için kodu elle yeniden yazma
↓
aynı kodu doğru config ile çalıştır
```

## Örnek 49

olmalıdır.

---

## Sonraki Adım

Movie API artık canlı URL'de çalışıyor.

Bu noktada yeni özellik eklemek kolayca cazip gelebilir.

Ama final teslim öncesi daha önemli bir iş var:

```text
temiz kurulum çalışıyor mu?
↓
Local testler geçiyor mu?
↓
Production testler geçiyor mu?
↓
doküman kodla aynı şeyi söylüyor mu?
↓
geçici debug kodları kaldı mı?
```
