# Bölüm 16 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# HTTP Yöntemleriyle Farklı İşlemler

## Kullanıcı Her Zaman Yalnızca Bilgi mi İster?

Şimdiye kadar oluşturduğumuz bütün Route'larda

GET yöntemini kullandık.

```js
app.get("/movies", (req, res) => {
  res.send("Filmler");
});
```

## Örnek 2

Kullanıcı `/movies` adresine gittiğinde

sunucudan film bilgilerini ister.

Express de bu GET Request'ini karşılar

ve uygun Response'u gönderir.

Ancak gerçek bir uygulamada kullanıcı

her zaman yalnızca bilgi istemez.

Bazen yeni bir film eklemek isteyebilir.

Bazen var olan bir filmin bilgilerini değiştirmek isteyebilir.

Bazen de bir filmi uygulamadan silmek isteyebilir.

```text
Filmleri görüntülemek

Yeni bir film eklemek

Bir filmi güncellemek

Bir filmi silmek
```

## Örnek 3

Bunların hepsi farklı işlemlerdir.

Peki sunucu,

kullanıcının hangi işlemi yapmak istediğini nasıl anlayacak?

Yalnızca adrese bakmak her zaman yeterli değildir.

Örneğin bütün işlemler aynı kaynakla ilgili olabilir:

```text
/movies
```

## Örnek 4

Fakat kullanıcının amacı değişebilir:

```text
Filmleri görmek

Yeni film eklemek

Bir filmi güncellemek

Bir filmi silmek
```

## Örnek 5

Demek ki Request'in yalnızca

hangi adrese gittiğini söylemesi yetmez.

Sunucuya,

o adreste hangi işlemin yapılmak istendiğini de bildirmesi gerekir.

İşte yapılmak istenen işlem,

Request'in HTTP yöntemiyle belirtilir.

## Request Ne Yapmak İstediğini Nasıl Söyler?

Bir Request'in amacı iki önemli bilgiyle anlaşılır:

```text
Hangi kaynak üzerinde işlem yapılacak?

Bu kaynak üzerinde hangi işlem yapılacak?
```

## Örnek 6

Request'in yolu,

hangi kaynakla ilgilendiğini belirtir:

```text
/movies
↓
Filmler
```

## Örnek 7

HTTP yöntemi ise

bu kaynak üzerinde yapılmak istenen işlemi belirtir:

```text
GET /movies
↓
Filmleri görüntülemek

POST /movies
↓
Yeni bir film oluşturmak
```

## Örnek 8



```text
Yol
↓
Hangi kaynak?

HTTP yöntemi
↓
Hangi işlem?
```

## Örnek 9

Express gelen Request'i değerlendirirken

bu iki bilgiyi birlikte kontrol eder.
## HTTP Yöntemlerini Express'te Nasıl Kullanıyoruz?

HTTP yöntemlerini

Web Foundations bölümünde

Request'in temel parçalarından biri olarak görmüştük.

Şimdi aynı bilgiyi

Express Route'larında kullanacağız.

HTTP yöntemi,

bir Request'in sunucudan hangi işlemi istediğini belirtir.

Bunu Request'in amacı gibi düşünebiliriz.

```text
GET
↓
Bilgi veya kaynak istemek

POST
↓
Genellikle yeni bir veri oluşturmak

PUT
↓
Genellikle mevcut verinin tamamını değiştirmek

PATCH
↓
Genellikle mevcut verinin belirli alanlarını değiştirmek

DELETE
↓
Bir verinin silinmesini istemek
```

## Örnek 10

Bu yöntemler HTTP protokolünün bir parçasıdır.

Express de bu yöntemleri karşılamak için

farklı metotlar sunar:

```js
app.get();
app.post();
app.put();
app.patch();
app.delete();
```

## Örnek 11

Burada önemli bir ayrım vardır.

```text
GET
↓
HTTP yöntemi

get
↓
GET Request'lerini karşılayan Express metodu
```

## Örnek 12

Örneğin:

```js
app.get("/movies", (req, res) => {
  res.send("Filmler");
});
```

## Örnek 13

Bu Route,

`/movies` adresine gelen GET Request'lerini karşılar.

Aynı şekilde:

```js
app.post("/movies", (req, res) => {
  res.send("Yeni film ekleme isteği alındı.");
});
```

## Örnek 14

bu Route da `/movies` adresine gelen POST Request'lerini karşılar.

Şimdi bu yöntemlerin görevlerini

tek tek inceleyelim.

## GET — Bilgi İstemek

GET yöntemi,

sunucudan bir bilgi veya kaynak istemek için kullanılır.

Örneğin kullanıcı film listesini görüntülemek istediğinde

şöyle bir Request gönderebilir:

```text
GET /movies
```

## Örnek 15

Express'te bu Request'i karşılayan Route şu şekilde yazılır:

```js
app.get("/movies", (req, res) => {
  res.send("Filmler");
});
```

## Örnek 16

Buradaki `get` metodu,

Route'un GET Request'lerini karşılayacağını belirtir.

```text
GET /movies
↓
Express eşleşen Route'u bulur.
↓
Route'un fonksiyonu çalışır.
↓
"Filmler" Response'u gönderilir.
```

## Örnek 17

GET yönteminin amacı bilgi istemektir.

Bu nedenle GET Request'leri

yeni veri oluşturmak,

mevcut veriyi değiştirmek

veya veri silmek için kullanılmamalıdır.

```text
GET
↓
Bilgi veya kaynak istemek
```

## Örnek 18

Örneğin:

```text
GET /movies
↓
Film listesini iste

GET /about
↓
Uygulama bilgilerini iste

GET /contact
↓
İletişim bilgilerini iste
```

## Örnek 19

Şimdiye kadar tarayıcının adres çubuğundan gönderdiğimiz Request'lerin

GET Request'i olmasının nedeni de budur.

Tarayıcıya bir adres yazıp Enter tuşuna bastığımızda

tarayıcı genellikle o adrese GET Request'i gönderir.

## POST — Yeni Bir Veri Oluşturmak

Kullanıcı her zaman yalnızca mevcut verileri görüntülemez.

Bazen sunucuya yeni bir veri eklemek ister.

Örneğin uygulamaya yeni bir film eklemek için

POST yöntemi kullanılabilir:

```text
POST /movies
```

## Örnek 20

Express'te POST Request'lerini karşılamak için

`app.post()` metodunu kullanırız:

```js
app.post("/movies", (req, res) => {
  res.send("Yeni film ekleme isteği alındı.");
});
```

## Örnek 21

Bu Route Express'e şu talimatı verir:

> `/movies` adresine bir POST Request'i gelirse  
> `"Yeni film ekleme isteği alındı."` Response'unu gönder.

```text
POST
↓
çoğunlukla sunucuya yeni bir veri oluşturma isteği göndermek için kullanılır.
```

## Örnek 22

GET ve POST aynı yol üzerinde kullanılabilir:

```text
GET /movies
↓
Filmleri görüntüle

POST /movies
↓
Yeni bir film oluştur
```

## Örnek 23

Burada yol değişmedi:

```text
/movies
```

## Örnek 24

Ancak HTTP yöntemi değiştiği için

yapılmak istenen işlem de değişti.

Şimdilik bu Route gerçek bir film oluşturmuyor.

Yalnızca POST Request'ini karşılıyor

ve bir Response gönderiyor.

Yeni filmin bilgilerinin nasıl alınacağını

ve verinin nasıl oluşturulacağını ilerleyen bölümlerde öğreneceğiz.

## PUT ve PATCH — Mevcut Veriyi Güncellemek

Bazen uygulamadaki mevcut bir verinin değiştirilmesi gerekir.

Örneğin bir filmin adı,

açıklaması

veya yayın yılı güncellenebilir.

HTTP,

güncelleme işlemleri için iki farklı yöntem sunar:

```text
PUT

PATCH
```

## Örnek 25

Bu iki yöntem benzer görünse de

genellikle farklı amaçlarla kullanılır.

### PUT

PUT yöntemi,

genellikle mevcut bir verinin tamamını

yeni bilgilerle değiştirmek için kullanılır.

Örneğin `42` numaralı filmi güncellemek istediğimizi düşünelim:

```text
PUT /movies/42
```

## Örnek 26

Express'te bu Request'i şöyle karşılayabiliriz:

```js
app.put("/movies/42", (req, res) => {
  res.send("Film tamamen güncellendi.");
});
```

## Örnek 27

Buradaki `42`,

örnek olarak seçtiğimiz filmin kimliğini temsil eder.

Şimdilik bu değeri Route'un içine doğrudan yazıyoruz.

Farklı film kimliklerini aynı Route ile nasıl karşılayacağımızı

dinamik Route'ları öğrenirken inceleyeceğiz.

Buradaki `/movies/42` adresi,

hangi film üzerinde işlem yapılacağını belirtir.

```text
PUT
↓
Verinin tamamını güncelle

/movies/42
↓
42 numaralı film
```

## Örnek 28

### PATCH

PATCH yöntemi,

genellikle bir verinin yalnızca belirli alanlarını

değiştirmek için kullanılır.

Örneğin filmin yalnızca puanını değiştirmek isteyebiliriz:

```text
PATCH /movies/42
```

## Örnek 29

Express'te bu Request'i şöyle karşılayabiliriz:

```js
app.patch("/movies/42", (req, res) => {
  res.send("Filmin belirli alanları güncellendi.");
});
```

## Örnek 30

PUT ve PATCH arasındaki temel farkı şöyle düşünebiliriz:

```text
PUT
↓
Verinin tamamını güncelle

PATCH
↓
Verinin belirli alanlarını güncelle
```

## Örnek 31

Örneğin elimizde şöyle bir film olduğunu düşünelim:

```text
Başlık: Interstellar
Yıl: 2014
Puan: 8.7
```

## Örnek 32

PUT ile güncelleme yapıldığında

filmin bütün bilgileri yeniden gönderilebilir.

PATCH ile güncelleme yapıldığında ise

yalnızca değiştirilmek istenen alan gönderilebilir:

```text
Puan: 8.8
```

## Örnek 33

Bu bölümde henüz gerçek bir güncelleme işlemi yapmıyoruz.

Yalnızca PUT ve PATCH Request'lerini karşılayan

Route'ların nasıl tanımlandığını görüyoruz.

## DELETE — Bir Veriyi Silmek

Bir veriyi uygulamadan kaldırmak istediğimizde

DELETE yöntemini kullanabiliriz.

Örneğin `42` numaralı filmi silmek için

şöyle bir Request gönderilebilir:

```text
DELETE /movies/42
```

## Örnek 34

Express'te DELETE Request'lerini karşılamak için

`app.delete()` metodunu kullanırız:

```js
app.delete("/movies/42", (req, res) => {
  res.send("Film silindi.");
});
```

## Örnek 35

Bu Route Express'e şu talimatı verir:

> `/movies/42` adresine bir DELETE Request'i gelirse  
> `"Film silindi."` Response'unu gönder.

```text
DELETE
↓
Bir veriyi silme isteği

/movies/42
↓
Silinmek istenen film
```

## Örnek 36

Şimdilik bu kod gerçek bir filmi silmiyor.

Yalnızca DELETE Request'ini karşılıyor

ve kullanıcıya bir Response gönderiyor.

Gerçek silme işlemini

veritabanı bölümlerinde gerçekleştireceğiz.

## Aynı Yol Farklı Yöntemlerle Kullanılabilir mi?

Evet.

Aynı yol,

farklı HTTP yöntemleriyle kullanılabilir.

Örneğin iki farklı Route tanımlayalım:

```js
app.get("/movies", (req, res) => {
  res.send("Filmler görüntüleniyor.");
});

app.post("/movies", (req, res) => {
  res.send("Yeni film oluşturuluyor.");
});
```

## Örnek 37

Her iki Route'un yolu da aynıdır:

```text
/movies
```

## Örnek 38

Ancak yöntemleri farklıdır:

```text
GET

POST
```

## Örnek 39

Bu nedenle Express bunları iki farklı Route olarak değerlendirir.

```text
GET /movies
↓
Filmler görüntüleniyor.

POST /movies
↓
Yeni film oluşturuluyor.
```

## Örnek 40

Bir Route'un yalnızca yoluyla belirlenmediğini artık görebiliyoruz.

Express eşleşme yaparken

HTTP yöntemini ve yolu birlikte kontrol eder.

```text
HTTP yöntemi + Yol
↓
Eşleşen Route
```

## Örnek 41

Burada `/movies` yolu,

filmlerin tamamıyla ilgili işlemleri temsil eder.

```text
/movies
↓
Filmlerin tamamı
```

## Örnek 42

Örneğin:

```text
GET /movies
↓
Film listesini görüntüle

POST /movies
↓
Yeni bir film oluştur
```

## Örnek 43

Bazen bütün filmlerle değil,

yalnızca belirli bir filmle işlem yapmak isteriz.

Bunun için filmin kimliğini yola ekleyebiliriz:

```text
/movies/42
```

## Örnek 44

Buradaki `42`,

örnek olarak seçtiğimiz filmin kimliğini temsil eder.

```text
/movies
↓
Filmlerin tamamı

/movies/42
↓
42 kimlikli belirli bir film
```

## Örnek 45

Aynı yol üzerinde farklı HTTP yöntemleri kullanarak

bu filmle ilgili farklı işlemler gerçekleştirebiliriz:

```js
app.get("/movies/42", (req, res) => {
  res.send("Film bilgileri");
});

app.put("/movies/42", (req, res) => {
  res.send("Film tamamen güncellendi.");
});

app.patch("/movies/42", (req, res) => {
  res.send("Filmin belirli alanları güncellendi.");
});

app.delete("/movies/42", (req, res) => {
  res.send("Film silindi.");
});
```

## Örnek 46

Bu Route'ların yolu aynıdır:

```text
/movies/42
```

## Örnek 47

Fakat her biri farklı bir HTTP yöntemini karşılar.

```text
GET /movies/42
↓
Filmi görüntüle

PUT /movies/42
↓
Filmi tamamen güncelle

PATCH /movies/42
↓
Filmin belirli alanlarını güncelle

DELETE /movies/42
↓
Filmi sil
```

## Örnek 48

🧠 Akılda Kalsın

```text
Yol
↓
Hangi kaynak üzerinde işlem yapılacak?

HTTP yöntemi
↓
Kaynak üzerinde hangi işlem yapılacak?
```

## Örnek 49

Burada dikkat etmemiz gereken önemli bir nokta vardır.

Tarayıcının adres çubuğuna bir adres yazmak

genellikle GET Request'i gönderir.

Bu nedenle POST, PUT, PATCH ve DELETE Route'larını

yalnızca adres çubuğunu kullanarak test edemeyiz.

Bu yöntemlerle Request gönderebilmek için

HTTP Request'lerini

istediğimiz yöntemle gönderebileceğimiz

bir test aracına ihtiyacımız vardır.

Bu tür bir araç,

oluşturduğumuz Request'in HTTP yöntemini

ve gideceği adresi seçmemizi sağlar.

Ardından bu Request'i sunucuya gönderir

ve sunucudan dönen Response'u incelememize yardımcı olur.

```text
HTTP yöntemini seç
↓
Request'in gideceği adresi yaz
↓
Request'i gönder
↓
Dönen Response'u incele
```

## Örnek 50

Bu amaçla kullanabileceğimiz araçlardan biri **Postman**'dır.

Şimdilik Postman'in ne işe yaradığını bilmemiz yeterlidir.

Bir sonraki bölümde Postman'i kullanarak

GET, POST, PUT, PATCH ve DELETE Request'lerini

Express uygulamamıza gerçekten göndereceğiz.

## Bölüm Özeti

Bu bölümde

daha önce tanıdığımız HTTP yöntemlerini

Express Route'ları üzerinde kullandık.

Yolun hangi kaynakla ilgilendiğimizi,

HTTP yönteminin ise o kaynak üzerinde

hangi işlemi yapmak istediğimizi belirttiğini

uygulama kodu üzerinde gördük.

Beş temel HTTP yöntemini inceledik:

```text
GET
↓
Bilgi veya kaynak istemek

POST
↓
Yeni veri oluşturmak

PUT
↓
Mevcut verinin tamamını güncellemek

PATCH
↓
Mevcut verinin belirli alanlarını güncellemek

DELETE
↓
Bir veriyi silmek
```

## Örnek 51

Express'te bu yöntemleri karşılayan metotları kullandık:

```js
app.get();
app.post();
app.put();
app.patch();
app.delete();
```

## Örnek 52

Aynı yolun farklı HTTP yöntemleriyle

farklı işlemler gerçekleştirebildiğini gördük.

```text
GET /movies
↓
Filmleri görüntüle

POST /movies
↓
Yeni film oluştur
```

## Örnek 53

Son olarak Express'in bir Route'u bulurken

HTTP yöntemini ve yolu birlikte kontrol ettiğini öğrendik.

```text
HTTP yöntemi + Yol
↓
Route
```
