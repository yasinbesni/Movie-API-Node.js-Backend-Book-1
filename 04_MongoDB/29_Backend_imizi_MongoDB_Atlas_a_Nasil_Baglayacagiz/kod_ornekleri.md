# Bölüm 29 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Backend'imizi MongoDB Atlas'a Nasıl Bağlayacağız?

## İki Taraf da Hazır, Ama Henüz Birbirini Tanımıyor

Bir önceki bölümün sonunda

iki tarafı karşı karşıya bırakmıştık:

```text
Node.js + Express
```

## Örnek 2

bir tarafta.

```text
MongoDB Atlas
```

## Örnek 3

diğer tarafta.

Atlas tarafında artık:

```text
BookCluster

Database User

IP Access List
```

## Örnek 4

hazır.

Backend tarafında ise

Express uygulamamız çalışıyor.

Ama aralarında

henüz bir bağlantı yok.

Backend şu anda:

> MongoDB nerede?

sorusunun cevabını bilmiyor.

İşte bu bölümde

aradaki boşluğu dolduracağız:

```text
Node.js + Express
↓
?
↓
MongoDB Atlas
```

## Örnek 5

Soru işaretinin yerine:

```text
bağlantı adresi
```

## Örnek 6

gelecek.

İngilizce dokümantasyonda

bu kavramı:

```text
connection string
```

## Örnek 7

olarak göreceğiz.

Ama önce

bu adresin neden gerekli olduğunu

iyice anlayalım.

---

## Backend MongoDB'yi Nasıl Bulacak?

Bir arkadaşınıza:

> Bana gel.

dediğinizi düşünün.

Ama adresinizi söylemediniz.

Arkadaşınız sizi tanıyor olabilir.

Gelmek de istiyor olabilir.

Fakat:

```text
nereye gideceğini
```

## Örnek 8

bilmediği için

size ulaşamaz.

Backend ile MongoDB arasında da

benzer bir durum vardır.

Backend:

```text
MongoDB'ye bağlanmak istiyorum.
```

## Örnek 9

diyebilir.

Ama MongoDB'nin nerede olduğunu

da bilmesi gerekir.

Bu bilgiyi taşıyan yapıya:

```text
bağlantı adresi
```

## Örnek 10

diyoruz.

Yani en sade hâliyle:

```text
Bağlantı adresi
↓
Backend'e
“MongoDB burada”
diyen adres
```

## Örnek 11

Şimdi bu adresi

Atlas'tan alacağız.

---

## Bağlantı Adresi Artık Karşımızda

28. bölümün sonunda

şu yolu izlemiştik:

```text
Connect
↓
Drivers
↓
Node.js
```

## Örnek 12

Ve Atlas bizi

şuna benzeyen bir adresin

bulunduğu ekrana getirmişti:

```text
mongodb+srv://<db_username>:<db_password>@bookcluster.xxxxx.mongodb.net/?retryWrites=true&w=majority&appName=BookCluster
```

## Örnek 13

28. bölümde

bu adrese dokunmadan

orada durmuştuk.

Çünkü o noktaya kadar yaptığımız iş:

```text
Atlas'ı bağlantıya hazırlamak
```

## Örnek 14

idi.

Şimdi görev değişiyor.

Artık:

```text
Bu adres ne anlatıyor?

İçine hangi bilgileri yazacağız?

Backend bunu nasıl kullanacak?

Bu bilgiyi nasıl güvenli saklayacağız?
```

## Örnek 15

sorularını cevaplayacağız.

İlk bakışta uzun görünen

bu adresi şimdi

parçalara ayıralım.

---

## Bağlantı Adresini Parçalara Ayıralım

Önümüzde şu adres var:

```text
mongodb+srv://<db_username>:<db_password>@bookcluster.xxxxx.mongodb.net/...
```

## Örnek 16

Şimdilik yalnızca

dört parçaya bakalım:

```text
mongodb+srv://
↓
MongoDB bağlantı adresinin başlangıcı

<db_username>
↓
Database User kullanıcı adımız

<db_password>
↓
Database User parolamız

bookcluster.xxxxx.mongodb.net
↓
Atlas'taki cluster'ımızın adresi
```

## Örnek 17

Yani uzun görünen adresin

temel fikri aslında şu:

```text
MongoDB'ye bağlan
↓
Bu kullanıcıyla
↓
Bu parolayla
↓
Bu cluster'a
```

## Örnek 18

Şimdi biraz daha

anlamlı görünmeye başladı.

---

## `mongodb+srv://` Ne Demek?

Bağlantı adresinin başında:

```text
mongodb+srv://
```

## Örnek 19

yazıyor.

Burada durup

DNS ve ağ protokolleri

dersine girmeyeceğiz.

Şimdilik bilmemiz gereken:

```text
mongodb+srv://
↓
MongoDB'nin SRV biçimindeki
bağlantı adresinin başlangıcı
```

## Örnek 20

Atlas bu biçimi kullanarak

uygulamamızın cluster'daki

MongoDB sunucularını

bulabilmesine yardımcı olur.

Yani:

```text
mongodb+srv://
```

## Örnek 21

gördüğünüzde:

> Bu bir MongoDB bağlantı adresi.

diye düşünmeniz yeterli.

Şu anda hedefimiz:

```text
bağlantıyı kurmak
```

## Örnek 22

---

## Kullanıcı Adı ve Parola Nereye Gidecek?

Bir önceki bölümde

Database User oluşturmuştuk.

Örneğin:

```text
Kullanıcı adı
↓
book_user

Parola
↓
********
```

## Örnek 23

Atlas'ın verdiği bağlantı adresinde ise:

```text
<db_username>

<db_password>
```

## Örnek 24

yer tutucularını görüyoruz.

Bunlar bize:

> Buraya kendi bilgilerini koy.

demektedir.

Yani:

```text
<db_username>
↓
book_user
```

## Örnek 25

olur.

Parola bölümüne de

Database User oluştururken

belirlediğimiz parola gelir.

Mantıksal olarak:

```text
mongodb+srv://book_user:PAROLAMIZ@bookcluster.xxxxx.mongodb.net/...
```

## Örnek 26

gibi görünür.

Ama bu adresi

gerçek parolayla birlikte

GitHub'a veya herkese açık

bir yere yazmayacağız.

Birazdan güvenli biçimde

saklayacağız.

---

## Parolamda `@` Gibi Bir Karakter Varsa?

Burada sık karşılaşılabilecek

küçük bir tuzak var.

Parolanızda örneğin:

```text
@
```

## Örnek 27

gibi bağlantı adresinde

özel anlam taşıyan

bir karakter bulunabilir.

Bu durumda karakterin

bağlantı adresinde:

```text
percent encoding
```

## Örnek 28

denilen biçimde

yazılması gerekebilir.

Örneğin:

```text
@
↓
%40
```

## Örnek 29

şeklinde görünür.

Bu yüzden:

> Parolam doğru ama
> authentication failed hatası alıyorum.

dediğiniz bir durumda

parolanın bağlantı adresinde

doğru biçimde yer alıp almadığını da

kontrol etmeniz gerekebilir.

Şimdilik bunu ezberlemeyin.

Sadece şu ilişkiyi hatırlayın:

```text
Özel karakterli parola
↓
Bağlantı adresinde
özel yazım gerektirebilir
```

## Örnek 30

---

## Peki Database Adı Nerede?

Şimdiye kadar:

```text
BookCluster
```

## Örnek 31

oluşturduk.

Ama:

```text
movieApp
```

## Örnek 32

adında gerçek bir database

henüz oluşturmamıştık.

Bağlantı adresinde

cluster adresinden sonra:

```text
/
```

## Örnek 33

işareti bulunur.

Bu bölümün arkasına

çalışmak istediğimiz

database adını yazabiliriz.

Örneğin:

```text
mongodb+srv://book_user:PAROLA@bookcluster.xxxxx.mongodb.net/movieApp
```

## Örnek 34

Buradaki:

```text
movieApp
```

## Örnek 35

backend'imizin kullanacağı

database adıdır.

Ama önemli bir nokta var:

```text
Database adını bağlantı adresine yazmak
↓
Database'in hemen oluşturulduğu
anlamına gelmez.
```

## Örnek 36

Yalnızca database adını

bağlantı adresine yazmak,

tek başına:

```text
database oluştur
```

## Örnek 37

komutu değildir.

MongoDB'de database'in görünür hâle gelmesi için

içinde bir collection'ın

veya verinin gerçekten

oluşturulması gerekir.

Bu işlem ilk veri yazıldığında

gerçekleşebileceği gibi,

bir collection açıkça

oluşturulduğunda da gerçekleşebilir.

Yani bağlantımız başarılı olsa bile

Atlas ekranında:

```text
movieApp
```

## Örnek 38

database'ini hemen görmeyebilirsiniz.

Bu bir hata değildir.

---

## Atlas'ın Verdiği Adresin Sonundaki Uzun Kısım Ne?

Atlas'ın verdiği adres

şuna benzer şekilde

devam edebilir:

```text
?retryWrites=true&w=majority&appName=BookCluster
```

## Örnek 39

Buradaki ifadeler:

```text
bağlantı seçenekleridir.
```

## Örnek 40

İlk bakışta:

```text
retryWrites

w

appName
```

## Örnek 41

gibi yeni kelimeler

göreceksiniz.

Ama şu anda

bunları öğrenmemiz gerekmiyor.

Atlas'ın verdiği bağlantı adresini

kopyalarken:

```text
sonundaki seçenekleri
silmeden bırakacağız.
```

## Örnek 42

İhtiyaç doğduğunda

bu ayarları tek tek

ele alacağız.

---

## Bağlantı Adresini Doğrudan JavaScript'e Yazalım mı?

Şimdi elimizde

şuna benzeyen

çok değerli bir bilgi var:

```text
mongodb+srv://book_user:PAROLA@...
```

## Örnek 43

Bu adresin içinde:

```text
kullanıcı adı

parola

cluster adresi
```

## Örnek 44

bulunabilir.

Peki şöyle mi yazmalıyız?

```js
const mongoUrl =
  "mongodb+srv://book_user:GERCEK_PAROLA@bookcluster.xxxxx.mongodb.net/movieApp";
```

## Örnek 45

Hayır.

Çünkü bu dosyayı

GitHub'a gönderdiğinizi düşünün.

Kodunuzu gören biri

MongoDB parolanızı da

görebilir.

Bu yüzden bağlantı adresini

kaynak kodumuzdan ayıracağız.

---

## `.env` Dosyası Burada Neden Gerekli?

Daha önce:

```text
.env
```

## Örnek 46

dosyasının

gizli veya ortama göre değişen

bilgileri koddan ayırmamıza

yardımcı olduğunu görmüştük.

MongoDB bağlantı adresi

bunun için çok iyi bir örnektir.

Projemizin ana klasöründe:

```text
.env
```

## Örnek 47

dosyasını açalım.

İçine:

```env
MONGODB_URL=mongodb+srv://book_user:PAROLAMIZ@bookcluster.xxxxx.mongodb.net/movieApp?retryWrites=true&w=majority&appName=BookCluster
```

## Örnek 48

yazarız.

Buradaki:

```text
MONGODB_URL
```

## Örnek 49

bizim verdiğimiz

ortam değişkeni adıdır.

Sade anlamı:

```text
MongoDB bağlantı adresim
```

## Örnek 50

Şimdi gerçek bağlantı bilgimiz

JavaScript dosyamızın içinde değil,

`.env` dosyasındadır.

---

## `.env` Dosyasını GitHub'a Göndermemeliyiz

`.gitignore` dosyamızda:

```gitignore
.env
```

## Örnek 51

satırı bulunmalıdır.

Bunun anlamı:

```text
Git
↓
.env dosyasını takip etme
```

## Örnek 52

Yani yanlışlıkla

MongoDB parolamızı

GitHub'a göndermemizi

önlemeye yardımcı olur.

Şu anda yapı şöyle:

```text
.env
↓
MONGODB_URL
↓
Gerçek bağlantı bilgisi

JavaScript
↓
process.env.MONGODB_URL
↓
Bağlantı bilgisini kullanır
```

## Örnek 53

---

## Backend `.env` İçindeki Değeri Nasıl Okuyacak?

`.env` dosyamıza:

```env
MONGODB_URL=...
```

## Örnek 54

yazdık.

Ama burada önemli bir nokta var.

Sadece `.env` dosyasını oluşturmak,

uygulamamızın bu dosyadaki

değerleri kendiliğinden

`process.env` içine aldığı

anlamına gelmez.

Bu değerleri yükleyecek

bir yöntem kullanmamız gerekir.

Bizim mevcut uygulamamız:

```text
npm start
↓
node index.js
```

## Örnek 55

şeklinde başlıyor.

Bu nedenle `.env` dosyasındaki

değerleri uygulamaya yükleyecek

bir adıma ihtiyacımız var.

Bu kitapta bunun için:

```text
dotenv
```

## Örnek 56

paketini kullanacağız.

Terminalde,

projemizin ana klasöründeyken:

```bash
npm install dotenv
```

## Örnek 57

komutunu çalıştıralım.

Ardından ana başlangıç dosyamız olan:

```text
index.js
```

## Örnek 58

dosyasının en üstüne:

```js
import "dotenv/config";
```

## Örnek 59

yazacağız.

Bu satırın yaptığı işi

şimdilik şöyle düşünün:

```text
.env dosyasını oku
↓
İçindeki değerleri process.env içine yükle
```

## Örnek 60

Artık JavaScript tarafında:

```js
process.env.MONGODB_URL
```

## Örnek 61

yazdığımızda,

`.env` içindeki:

```text
MONGODB_URL
```

## Örnek 62

değerine ulaşabiliriz.

Yani akışımız:

```text
.env
↓
dotenv
↓
process.env
↓
MONGODB_URL
```

## Örnek 63

şeklindedir.

Bu ayrım önemli.

Çünkü:

```text
.env dosyasını oluşturmak
≠
.env dosyasını uygulamaya yüklemek
```

## Örnek 64

Şimdi bağlantı adresimiz

JavaScript dosyasının içine

açıkça yazılmadan

uygulamamız tarafından

okunabilir hâle geldi.

---

## Node.js MongoDB ile Nasıl Konuşacak?

Elimizde bağlantı adresi var.

Ama şu soru hâlâ duruyor:

> Node.js bu adresi kullanarak
> MongoDB'ye nasıl bağlanacak?

Node.js'in kendi çekirdeğinde:

```text
MongoDB'ye özel
hazır bir bağlantı katmanı
```

## Örnek 65

bulunmaz.

MongoDB'nin resmi:

```text
Node.js Driver
```

## Örnek 66

paketini doğrudan

kullanmak mümkündür.

Ama bu kitapta

MongoDB ile çalışmayı

daha düzenli hâle getiren:

```text
Mongoose
```

## Örnek 67

kütüphanesini kullanacağız.

Yani Mongoose:

```text
tek mümkün yol
```

## Örnek 68

değildir.

Bizim bu kitap için

seçtiğimiz yoldur.

---

## Mongoose Nedir?

Yeni bir kelime daha.

Ama uzun tanımını

ezberlemeye çalışmayın.

Şimdilik şöyle düşünün:

```text
Node.js uygulamamız
↓
Mongoose
↓
MongoDB
```

## Örnek 69

Mongoose:

> Node.js uygulamamızın
> MongoDB ile çalışmasını
> kolaylaştıran bir kütüphanedir.

İleride:

```text
Schema

Model

validation

query
```

## Örnek 70

gibi konulara geldiğimizde

Mongoose'un neden önemli olduğunu

çok daha net göreceğiz.

Bugün yalnızca

bir iş için kullanacağız:

```text
MongoDB Atlas'a bağlanmak
```

## Örnek 71

---

## Mongoose'u Projemize Kuralım

Terminali açalım.

Projemizin klasöründeyken:

```bash
npm install mongoose
```

## Örnek 72

komutunu çalıştıralım.

Bu işlem:

```text
Mongoose
↓
projemizin dependency'leri arasına ekler
```

## Örnek 73

Kurulum tamamlandığında:

```js
import mongoose from "mongoose";
```

## Örnek 74

yazabiliriz.

Ama yalnızca import etmek

MongoDB'ye bağlanmak için

yeterli değildir.

Bağlantı adresimizi

Mongoose'a vermemiz gerekir.

---

## Bağlantıyı Kuracak Fonksiyonumuzu Yazalım

Bağlantı kodunu

uygulamanın her yerine

dağıtmak istemiyoruz.

Örneğin:

```text
src/db/initMongoConnection.js
```

## Örnek 75

dosyasını oluşturalım.

İçine:

```js
import mongoose from "mongoose";

export const initMongoConnection = async () => {
  await mongoose.connect(process.env.MONGODB_URL);

  console.log("MongoDB bağlantısı başarılı.");
};
```

## Örnek 76

yazalım.

Şimdi satır satır bakalım.

---

## `mongoose.connect()` Ne Yapıyor?

Kodumuzdaki:

```js
mongoose.connect(process.env.MONGODB_URL)
```

## Örnek 77

satırı aslında şunu söylüyor:

```text
Mongoose
↓
MONGODB_URL içindeki adresi al
↓
O MongoDB ortamına bağlan
```

## Örnek 78

Mongoose bu adresi kullanarak:

```text
BookCluster
```

## Örnek 79

ile bağlantı kurmaya çalışır.

---

## Neden `await` Kullanıyoruz?

Bağlantı:

```text
anında gerçekleşen
```

## Örnek 80

bir işlem değildir.

Backend Atlas'a ulaşır,

kimlik bilgilerini gönderir

ve bağlantının kurulmasını bekler.

Bu yüzden:

```js
await mongoose.connect(...)
```

## Örnek 81

yazıyoruz.

Buradaki fikir:

> MongoDB bağlantısının sonucunu
> beklemeden devam etme.

Uygulamanın:

```text
MongoDB hazır değilken
```

## Örnek 82

veritabanı işlemleri yapmasını

istemeyiz.

---

## Peki Bağlantı Başarısız Olursa?

Şu anki fonksiyonumuzu

biraz daha güvenli hâle getirelim.

```js
import mongoose from "mongoose";

export const initMongoConnection = async () => {
  try {
    await mongoose.connect(process.env.MONGODB_URL);

    console.log("MongoDB bağlantısı başarılı.");
  } catch (error) {
    console.error(
      "MongoDB bağlantısı kurulamadı:",
      error.message
    );

    throw error;
  }
};
```

## Örnek 83

Şimdi iki olasılığımız var:

```text
Bağlantı başarılı
↓
MongoDB bağlantısı başarılı.

Bağlantı başarısız
↓
Hata mesajını gör
```

## Örnek 84

Hata mesajı

sorunun nerede olduğunu

bulmamıza yardımcı olacak.

---

## Bu Fonksiyon Ne Zaman Çalışacak?

Bağlantı fonksiyonunu

şu dosyada oluşturduk:

```text
src/db/initMongoConnection.js
```

## Örnek 85

Ama bu dosyada `initMongoConnection` fonksiyonunu tanımladık ve dışarı aktardık.

Henüz onu uygulamayı başlatan dosyadan çağırmadık.

Henüz fonksiyonu:

```text
çalıştırmadık.
```

## Örnek 86

Burada çok önemli

bir ayrım yapalım.

Terminalde:

```bash
node src/db/initMongoConnection.js
```

## Örnek 87

yazmak da tek başına

bağlantıyı kurmaz.

Neden?

Çünkü dosya çalışsa bile

içindeki fonksiyon:

```js
initMongoConnection();
```

## Örnek 88

şeklinde çağrılmadığı sürece

bağlantı başlamaz.

Bizim uygulamamızın

başlangıç noktası zaten:

```text
index.js
```

## Örnek 89

dosyasıydı.

Daha önce:

```text
npm start
↓
node index.js
```

## Örnek 90

akışıyla Express uygulamamızı

başlatıyorduk.

O hâlde MongoDB bağlantısını da

uygulamanın başlangıcına

eklemeliyiz.

`index.js` dosyamızı

şu mantıkla düzenleyelim.

Burada çok önemli bir nokta var:

> Önceki bölümlerde yazdığımız
> middleware ve Route'ları silmiyoruz.

Onlar dosyamızda

olduğu gibi kalacak.

Biz yalnızca:

```text
MongoDB bağlantısını import ediyoruz

ve

app.listen() çalışmadan önce
bağlantıyı başlatıyoruz
```

## Örnek 91

Örneğin dosyamızın

ilgili bölümleri şöyle görünür:

```js
import "dotenv/config";
import express from "express";
import { initMongoConnection } from "./src/db/initMongoConnection.js";

const app = express();

app.use(express.json());

app.get("/movies", (req, res) => {
  res.send("Filmler");
});

const startServer = async () => {
  try {
    await initMongoConnection();

    app.listen(3000, () => {
      console.log("Server 3000 portunda çalışıyor.");
    });
  } catch (error) {
    console.error("Uygulama başlatılamadı.");
  }
};

startServer();
```

## Örnek 92

Yani bu örneğin amacı

`index.js` dosyasını

baştan yazmak değildir.

Amaç yalnızca:

```text
mevcut Express uygulamamız
+
MongoDB başlangıç bağlantısı
```

## Örnek 93

ikisini bir araya getirmektir.

Burada yeni olan

asıl parça şudur:

```js
await initMongoConnection();
```

## Örnek 94

Artık fonksiyon yalnızca

tanımlanmış değil,

gerçekten çağrılmıştır.

Üstelik bağlantıyı:

```text
Express sunucusu başlamadan önce
```

## Örnek 95

kuruyoruz.

Yani sıra:

```text
MongoDB'ye bağlan
↓
Bağlantı başarılı olursa
↓
Express sunucusunu başlat
```

## Örnek 96

şeklindedir.

MongoDB bağlantısı başarısız olursa

`app.listen()` satırına

geçilmez.

Bu da uygulamanın:

```text
Veritabanına bağlanamadım
ama yine de hazırım.
```

## Örnek 97

demesini engeller.

---

## Başlangıç Akışını Bir Bütün Olarak Görelim

Uygulamanın başlangıç mantığı:

```text
npm start
↓
package.json
↓
node index.js
↓
dotenv → .env dosyasını yükler
↓
process.env.MONGODB_URL hazır olur
↓
index.js → initMongoConnection() çağırır
↓
Mongoose → MongoDB Atlas'a bağlanır
↓
Bağlantı başarılı
↓
app.listen() çalışır
↓
Express sunucusu başlar
```

## Örnek 98

Artık parçalar

birbirine bağlanıyor.

28. bölümde hazırladığımız:

```text
Database User

IP Access List
```

## Örnek 99

bu noktada gerçekten

işe yarıyor.

Atlas bağlantı sırasında:

```text
Kullanıcı doğru mu?

Geldiği IP izinli mi?
```

## Örnek 100

diye kontrol ediyor.

---

## Bağlantıyı Test Edelim

Artık gerçekten

çalıştırabileceğimiz bir akışımız var.

Şu üç parça hazır:

```text
.env
↓
MONGODB_URL

src/db/initMongoConnection.js
↓
MongoDB bağlantı fonksiyonu

index.js
↓
initMongoConnection() çağrısı
```

## Örnek 101

Şimdi terminali

projemizin ana klasöründe açalım.

Yani:

```text
movie-api/
```

## Örnek 102

klasöründe olmalıyız.

Ardından:

```bash
npm start
```

## Örnek 103

komutunu çalıştıralım.

Daha önce öğrendiğimiz gibi:

```text
npm start
↓
package.json
↓
"start": "node index.js"
↓
index.js çalışır
```

## Örnek 104

Peki `index.js` şimdi ne yapacak?

```text
index.js
↓
.env dosyasını yükler
↓
initMongoConnection() fonksiyonunu çağırır
↓
Mongoose Atlas'a bağlanmaya çalışır
```

## Örnek 105

Bağlantı başarılıysa

önce terminalde:

```text
MongoDB bağlantısı başarılı.
```

## Örnek 106

mesajını görmeliyiz.

Ardından:

```text
Server 3000 portunda çalışıyor.
```

## Örnek 107

mesajı gelir.

Yani terminalde kabaca:

```text
MongoDB bağlantısı başarılı.
Server 3000 portunda çalışıyor.
```

## Örnek 108

görmemiz beklenir.

Bu iki mesajın sırası da

bize önemli bir şey anlatır:

```text
Önce MongoDB hazır
↓
Sonra Express hazır
```

## Örnek 109

Artık şu zincirin

gerçekten çalıştığını biliyoruz:

```text
npm start
↓
index.js
↓
initMongoConnection()
↓
Mongoose
↓
Bağlantı adresi
↓
MongoDB Atlas
```

## Örnek 110

Ama dikkat:

```text
Bağlandık
≠
Veri ekledik
```

## Örnek 111

Henüz hiçbir film

kaydetmedik.

Sadece:

```text
backend ile MongoDB arasındaki
iletişim yolunu açtık.
```

## Örnek 112

---

## Atlas'ta Database'i Neden Hâlâ Göremiyorum?

Bağlantı başarılı.

Terminalde hata yok.

Sonra Atlas ekranını açıyorsunuz.

Ama:

```text
movieApp
```

## Örnek 113

database'i görünmüyor.

İlk kez öğrenen biri:

> Bağlantı kurulmadı mı?

diye düşünebilir.

Hayır.

Çünkü:

```text
Bağlantı kurmak
```

## Örnek 114

ile:

```text
veri yazmak
```

## Örnek 115

aynı işlem değildir.

Çünkü yalnızca bağlantı kurmak

MongoDB'ye:

```text
Şimdi bir collection oluştur
```

## Örnek 116

veya:

```text
Şimdi veri kaydet
```

## Örnek 117

demek değildir.

`movieApp`,

içinde gerçekten bir collection

veya veri oluşturulduğunda

Atlas'ta görünür hâle gelecektir.

Bu yüzden database'i

henüz görmemeniz

tek başına hata değildir.

---

## Bağlantı Kurulmazsa Nereden Başlayacağız?

Terminalde:

```text
MongoDB bağlantısı kurulamadı
```

## Örnek 118

mesajını gördüğünüzü düşünün.

İlk düşünceniz:

> Kodum yanlış.

olabilir.

Ama bağlantı hatası

her zaman kod hatası değildir.

Önce şunları kontrol edin:

```text
Database User
↓
Kullanıcı adı doğru mu?
Parola doğru mu?

IP Access List
↓
Şu anki IP adresime izin var mı?

Bağlantı adresi
↓
Atlas'tan doğru adresi kopyaladım mı?

Parola
↓
Özel karakterler doğru biçimde yazılmış mı?
```

## Örnek 119

Bunlardan biri bile yanlışsa

bağlantı başarısız olabilir.

---

## “Authentication Failed” Ne Anlatıyor?

Örneğin:

```text
Authentication failed
```

## Örnek 120

hatası gördünüz.

Sade anlamı:

```text
Kimlik doğrulama başarısız oldu.
```

## Örnek 121

Atlas:

> Bana gelen kullanıcı bilgilerini
> kabul edemedim.

diyor.

Bu durumda özellikle:

```text
Database User kullanıcı adı

Database User parolası

parolanın bağlantı adresindeki yazımı
```

## Örnek 122

kontrol edilmelidir.

Bu hata:

```text
Express route'un bozuk olduğu
```

## Örnek 123

anlamına gelmez.

Sorunun hangi katmanda

olduğunu ayırmak

backend geliştirmede

çok önemlidir.

---

## IP Hatasında Ne Olur?

Başka bir gün

ev Wi-Fi'niz yerine

telefon internetine

bağlandığınızı düşünün.

Kod aynı.

Parola aynı.

Bağlantı adresi aynı.

Ama MongoDB'ye

bağlanamıyorsunuz.

Ne değişti?

```text
Ağ
↓
IP adresi
```

## Örnek 124

değişmiş olabilir.

Atlas'ın IP Access List'inde

yeni IP adresiniz yoksa

bağlantı engellenebilir.

Bu yüzden bağlantı problemi

yaşadığınızda:

```text
Kod

Kimlik bilgileri

Ağ erişimi
```

## Örnek 125

üçünü ayrı ayrı

düşünmek gerekir.

---

## Şimdi Büyük Resme Bakalım

27. bölümde:

```text
MongoDB nerede çalışacak?
```

## Örnek 126

sorusunu sorduk.

28. bölümde:

```text
Atlas ortamımızı
```

## Örnek 127

hazırladık.

Şimdi ise:

```text
Backend
↓
MongoDB Atlas
```

## Örnek 128

bağlantısını kurduk.

Bütün yol:

```text
Node.js + Express
↓
Mongoose
↓
MONGODB_URL
↓
MongoDB Atlas
↓
BookCluster
```

## Örnek 129

şeklinde.

Atlas tarafında:

```text
Database User
↓
Kim bağlanıyor?

IP Access List
↓
Nereden bağlanıyor?
```

## Örnek 130

kontrolleri yapılıyor.

Artık backend'imiz

MongoDB'ye ulaşabiliyor.

---

## Bölüm Özeti

Bu bölümde

Atlas ortamını

backend'imize bağladık.

Önce:

```text
bağlantı adresi
```

## Örnek 131

kavramını yerine oturttuk.

İngilizce dokümantasyonda:

```text
connection string
```

## Örnek 132

olarak görebileceğimizi

hatırladık.

28. bölümün sonunda ulaştığımız:

```text
Connect
↓
Drivers
↓
Node.js
```

## Örnek 133

ekranından gelen

bağlantı adresini

bu bölümde kullanmaya başladık.

Bağlantı adresinin temel parçalarını:

```text
mongodb+srv://

Database User

Database Password

Cluster adresi

Database adı
```

## Örnek 134

şeklinde okuduk.

Paroladaki bazı özel karakterlerin

bağlantı adresinde

özel yazım gerektirebileceğini

gördük.

Daha sonra bağlantı adresini:

```text
.env
```

## Örnek 135

dosyasına taşıdık.

Kodumuzda:

```js
process.env.MONGODB_URL
```

## Örnek 136

üzerinden kullandık.

Ardından:

```text
Mongoose
```

## Örnek 137

ile tanıştık.

Şimdilik Mongoose'u:

```text
Node.js
↓
MongoDB
```

## Örnek 138

arasında çalışmamızı

kolaylaştıran kütüphane

olarak düşündük.

Bağlantıyı:

```js
await mongoose.connect(process.env.MONGODB_URL);
```

## Örnek 139

ile kurduk.

Son olarak:

```text
Bağlantı başarılı
≠
Database hemen oluşturuldu
```

## Örnek 140

ayrımını yaptık.

Artık:

```text
Backend
↓
MongoDB Atlas
```

## Örnek 141

bağlantımız hazır.

---

## Sonraki Adım

Şimdi film platformumuza

geri dönelim.

Backend MongoDB'ye

bağlanabiliyor.

Ama elimizde hâlâ:

```text
film document'ı yok.
```

## Örnek 142

Bir filmi kaydetmek istediğimizi

düşünün:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "voteAverage": 8.7
}
```

## Örnek 143

Backend artık

bu veriyi MongoDB'ye

gönderebilir.

Ama yeni bir soru doğuyor:

> MongoDB'ye gönderilecek
> bir film document'ının
> nasıl görünmesi gerektiğini
> uygulamamız nereden bilecek?

Başka bir deyişle:

```text
title string mi?

releaseYear number mı?

hangi field zorunlu?

hangi field isteğe bağlı?
```
