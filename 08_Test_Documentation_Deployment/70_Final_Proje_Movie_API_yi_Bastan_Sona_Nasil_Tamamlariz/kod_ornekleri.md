# Bölüm 70 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Final Proje: Movie API'yi Baştan Sona Nasıl Tamamlarız?

## Kod Yazmayı Bırakıp Projeyi Teslim Etme Zamanı

Movie API artık özellik açısından dolu.

Bu noktada yeni bir özellik eklemek yerine şu soruyu cevaplamamız gerekir:

> Bu projeyi bugün başka birine versek gerçekten çalıştırabilir, test edebilir ve anlayabilir mi?

Bir projenin:

```text
"bende çalışıyor"
```

## Örnek 2

olması ile:

```text
"teslim edilmeye hazır"
```

## Örnek 3

olması aynı şey değildir.

Bu son bölümde odağımız yeni paket veya yeni mimari eklemek değil; daha önce kurduğumuz parçaların birlikte doğru çalıştığını doğrulamak.

Projeye bir **release candidate**, yani final olmaya aday sürüm gibi yaklaşacağız:

```text
yeni özellik ekleme
↓
mevcut sistemi kontrol et
↓
hata bulursan düzelt
↓
tekrar test et
```

## Örnek 4

---

## 1. Temiz Kurulum Gerçekten Çalışıyor mu?

İlk testimizi kendi açık terminal oturumumuza güvenmeden yapalım.

Projeyi yeni kuran birinin yapacağı adımları düşünelim:

```bash
npm install
```

## Örnek 5

sonra `example.env` dosyasına bakıp kendi `.env` dosyasını hazırlayacak.

Ardından:

```bash
npm start
```

## Örnek 6

çalıştıracak.

Kontrol soruları:

```text
package'lar eksiksiz kuruluyor mu?
↓
config eksikse anlaşılır hata veriyor mu?
↓
MongoDB bağlantısı kuruluyor mu?
↓
server başlıyor mu?
```

## Örnek 7

Burada bir hata çıkarsa final checklist'in geri kalanına geçmeden önce düzeltelim.

---

## 2. Önce En Basit Sağlık Kontrolünü Yapalım

Server başladıktan sonra:

```http
GET /health
```

## Örnek 8

gönderelim.

Beklenti:

```http
200 OK
```

## Örnek 9

Sonra:

```text
/docs
```

## Örnek 10

sayfasını açalım.

Bu iki küçük kontrol bize şunu söyler:

```text
uygulama ayakta
+
dokümantasyon Route'u erişilebilir
```

## Örnek 11

Ama CRUD davranışlarını henüz doğrulamadık.

---

## 3. Postman Collection'ı Local Ortamda Baştan Sona Çalıştıralım

67. bölümde hazırladığımız Collection'ı:

```text
Local
↓
baseUrl = http://localhost:3000
```

## Örnek 12

Environment ile çalıştıralım.

Runner'da şu temel akışın tamamı geçmeli:

```text
POST Movie
↓
GET Movie By Id
↓
PATCH Movie
↓
Invalid POST
↓
DELETE Movie
```

## Örnek 13

Bir test kırılırsa yalnızca:

```text
Failed
```

## Örnek 14

demeyelim.

Hangi sözleşmenin bozulduğunu bulalım:

```text
status mu farklı?
Body mi farklı?
validation mı değişti?
Response sözleşmesi mi değişti?
```

## Örnek 15

Final denetimin amacı tam olarak budur.

---

## 4. OpenAPI Dokümanı Gerçek Kodla Uyumlu mu?

`/docs` sayfasını açalım ve gerçek kodla karşılaştıralım.

Özellikle:

```text
GET /movies query parameter'ları
POST required alanları
PATCH gönderilebilen alanlar
status code'lar
Response data / meta / errors yapısı
```

## Örnek 16

kontrol edilmeli.

Somut örnek:

```text
pagination constant
↓
MAX_LIMIT = 50
```

## Örnek 17

ise OpenAPI içinde:

```text
maximum: 50
```

## Örnek 18

olmalı.

Joi:

```text
voteAverage 0–10
```

## Örnek 19

diyorsa dokümantasyon da aynı sınırı göstermeli.

Bir fark bulursak şu soruyu soralım:

> Gerçek davranış mı yanlış, yoksa doküman mı eski?

Sonra ikisini tekrar senkronize edelim.

---

## 5. Final Kod Temizliği Yapalım

Kitap boyunca deney yapmak için geçici kodlar kullandık.

Editörünüzde bütün projede şu ifadeleri arayın:

```text
console.log(
console.error(
TODO
TEMP
```

## Örnek 20

Her sonuç için karar verin:

```text
kalıcı uygulama log'u mu?
↓
logger kullan

eski deney kodu mu?
↓
sil
```

## Örnek 21

Aynı şekilde hardcoded değerleri arayalım:

```text
3000
10
50
8
60000
100
localhost
```

## Örnek 22

Ama gördüğümüz her sayıyı constant yapmayalım.

48 ve 58'de kurduğumuz karar ağacını kullanalım:

```text
ortak uygulama kararı mı?
↓
constant olabilir

ortama göre değişen ayar mı?
↓
config olabilir

yalnızca o kodun doğal literal değeri mi?
↓
yerinde kalabilir
```

## Örnek 23

Final refactor'ın amacı soyutlama sayısını artırmak değildir.

Amaç gereksiz dağınıklığı temizlemektir.

---

## 6. Secret ve Repository Kontrolü

Repository'ye girmemesi gereken şeyleri kontrol edelim.

Özellikle:

```text
.env
MongoDB gerçek connection string
şifre / token benzeri secret değerler
```

## Örnek 24

Git tarafından takip edilmemeli.

Ama:

```text
example.env
```

## Örnek 25

güncel olmalı.

Yeni biri hangi environment variable'ların gerektiğini buradan anlayabilmeli.

---

## 7. Production Environment'ta Aynı Testleri Çalıştıralım

69. bölümde oluşturduğumuz Postman Environment:

```text
Production
↓
baseUrl = https://...
```

## Örnek 26

ile aynı Collection'ı çalıştıralım.

Bu testlerde film ekleme, güncelleme ve silme işlemleri de var. Bu yüzden canlı ortamda gerçek kullanıcı kayıtları üzerinde deneme yapmayalım; yalnızca test için oluşturduğumuz kayıtları kullanalım.

Amaç:

```text
Local Passed
↓
Production Passed
```

## Örnek 27

görebilmektir.

Eğer local geçip production başarısız oluyorsa problem büyük ihtimalle artık şu alanlardan birindedir:

```text
production config
CORS
MongoDB network access
deployment / start ayarı
```

## Örnek 28

66. bölümdeki loglar teşhis için ilk bakacağımız yerlerden biridir.

---

## 8. Production Loglarını Gerçek Bir Request ile Kontrol Edelim

Canlı API'ye:

```http
GET /movies
```

## Örnek 29

gönderelim.

Platform loglarında ilgili HTTP logunu bulalım.

Şu bilgileri görebiliyor muyuz?

```text
Request path
status code
response time
```

## Örnek 30

Sonra veritabanında bulunmadığını bildiğimiz, biçim olarak geçerli bir ObjectId ile kontrollü `404` Request gönderelim:

```http
GET /movies/66f123456789abcdef123456
```

## Örnek 31

API Response'u ve server log'unun birbirinden farklı amaç taşıdığını yeniden gözlemleyelim.

Burada yeni logging bilgisi öğrenmiyoruz.

Sadece production'da gerçekten çalıştığını kanıtlıyoruz.

---

## 9. README Bir Yabancının Projeye Giriş Noktasıdır

Repository'yi açan kişi önce kodu okumak zorunda kalmamalı.

Basit bir `README.md` şu sorulara cevap vermelidir:

```text
Bu proje ne?
↓
Nasıl kurulur?
↓
Hangi environment variable'lar gerekli?
↓
Nasıl başlatılır?
↓
API dokümantasyonu nerede?
↓
Testler nasıl çalıştırılır?
↓
Canlı URL ne?
```

## Örnek 32

Örnek başlıklar:

```markdown
# Movie API

## Kullanılan Teknolojiler

## Kurulum

## Environment Variables

## Çalıştırma

## API Documentation

## Postman Tests

## Live URL
```

## Örnek 33

Kurulum örneği:

```bash
npm install
npm start
```

## Örnek 34

imariyi Tek Bir Haritada Görelim

Bu noktada bütün klasörleri yeniden öğretmeye gerek yok.

Sadece sorumluluk haritasını görelim:

| Klasör | Ana sorumluluk |
|---|---|
| `routers` | Method + path + Middleware / Controller bağlantısı |
| `controllers` | HTTP input ve Response |
| `services` | Uygulama / veri iş akışı |
| `db/models` | Mongoose Schema / Model / Query davranışları |
| `validation` | Joi Request sözleşmeleri |
| `middlewares` | Ortak Request akış adımları |
| `config` | Ortama göre değişen ayarları hazırlama |
| `constants` | Ortak uygulama kararları |
| `logging` | Uygulama logger'ı |
| `docs` | OpenAPI sözleşmesi |

Bu tabloyu ezberlememiz gerekmez.

Şu soruya cevap verebilmemiz yeterlidir:

> Yeni bir kod parçası eklersem sorumluluğu hangi katmana ait?

---

## 11. Endpoint Listemizi Son Kez Kontrol Edelim

Temel Movie API endpoint'leri:

| Method | Path | Beklenen ana sonuç |
|---|---|---|
| `GET` | `/movies` | Liste + `meta` |
| `GET` | `/movies/:movieId` | Tek Movie |
| `POST` | `/movies` | Yeni Movie, `201` |
| `PATCH` | `/movies/:movieId` | Güncellenmiş Movie |
| `DELETE` | `/movies/:movieId` | Silinen Movie, `200` |

Operasyonel / dokümantasyon endpoint'leri:

```text
GET /health
GET /docs
```

## Örnek 35

Her endpoint için artık dört soruya cevap verebilmeliyiz:

```text
hangi input'u kabul ediyor?
hangi status code'ları döndürüyor?
Response yapısı nasıl?
hangi Postman testi bunu doğruluyor?
```

## Örnek 36

---

## Final Projede Bilinçli Olarak Olmayan Şeyler

İyi bir proje “akla gelen her özelliği ekledik” demek değildir.

Bu kitapta bilinçli olarak dışarıda bıraktığımız konular var:

```text
register / login
password hashing
authentication
authorization
JWT / session
cookies
file upload
email
ileri cache / queue yapıları
```

## Örnek 37

Bu eksiklik değildir.

Kapsam kararıdır.

Bu kitap boyunca ulaşmak istediğimiz sonuç şuydu:

> Temelden başlayıp veritabanı kullanan, katmanlara ayrılmış, doğrulanan, gözlemlenebilen, test edilen, dokümante edilen ve canlıya alınabilen bir REST API geliştirmek.

Bu hedefi Movie API ile tamamladık.

---

## Final Teslim Kontrol Listesi

Aşağıdaki kutuları gerçekten tek tek kontrol edin:

```text
[ ] npm install temiz kurulumda çalışıyor
[ ] npm start server'ı başlatıyor
[ ] .env Git'e eklenmiyor
[ ] example.env güncel
[ ] /health 200 dönüyor
[ ] CRUD endpoint'leri çalışıyor
[ ] pagination / filter / search çalışıyor
[ ] Joi validation çalışıyor
[ ] Mongoose validation çalışıyor
[ ] Response yapıları tutarlı
[ ] CORS config'ten geliyor
[ ] Helmet aktif
[ ] Rate Limiting aktif
[ ] Request logları görünüyor
[ ] errorHandler hataları logluyor
[ ] Local Postman Collection testleri geçiyor
[ ] /docs gerçek kodla uyumlu
[ ] canlı URL çalışıyor
[ ] Production Postman testleri geçiyor
[ ] geçici debug kodları temizlendi
[ ] secret değerler repository'de yok
[ ] README güncel
```

## Örnek 38

Bir kutu başarısızsa:

```text
"sonra bakarım"
```

## Örnek 39

demek yerine o maddeyi düzeltip testi tekrar çalıştırın.

Final proje böyle tamamlanır.

---

## Kendimize Son Bir Kez Soralım

Kod bakmadan şu soruların çoğuna cevap verebiliyor musunuz?

1. Router, Controller ve Service neden ayrı sorumluluklar taşır?
2. Joi validation ile Mongoose validation neden aynı değildir?
3. `constants` ile `config` arasındaki fark nedir?
4. CORS neden authentication değildir?
5. Helmet ile Rate Limiting hangi farklı problemleri çözer?
6. Structured logging bize hangi bilgileri kazandırır?
7. Postman Environment ile Collection variable neden farklı yerlerde kullanıldı?
8. OpenAPI ile Swagger UI arasındaki fark nedir?
9. Deployment sırasında neden `PORT` ve `MONGODB_URL` kod içine gömülmez?
10. Local çalışan API'nin production'da bozulduğunu nasıl teşhis etmeye başlarsınız?

Bir soruda zorlanıyorsanız bu başarısızlık değildir.

Hangi bölüme geri dönmeniz gerektiğini gösterir.

---

## 🧠 Akılda Kalsın

Bu kitabın finalindeki en önemli beceri yeni bir Express metodu ezberlemek değildir.

Şu çalışma biçimini kazanmış olmak daha önemlidir:

```text
problem gör
↓
çalışan en basit çözümü kur
↓
problemin büyüdüğü noktayı gözlemle
↓
sorumlulukları ayır
↓
validation ekle
↓
logla
↓
test et
↓
dokümante et
↓
canlı ortamda doğrula
```

## Örnek 40

Ve final adımı:

```text
yeni özellik eklemeyi bırak
↓
mevcut sistemi baştan sona kanıtla
```
