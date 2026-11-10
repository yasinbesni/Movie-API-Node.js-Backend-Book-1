# Bölüm 14 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Express Uygulamasının Temeli

## index.js Artık Ne Yapacak?

Bir önceki bölümde

Express'i mevcut:

```text
movie-api
```

## Örnek 2

projemize ekledik.

Artık proje klasörümüzde:

```text
node_modules/
package.json
package-lock.json
```

## Örnek 3

yapıları bulunuyor

ve `package.json` içinde

Express bir bağımlılık olarak kayıtlı.

Fakat henüz

Express'i kullanan bir uygulama yazmadık.

`index.js` dosyamız ise

Node.js bölümlerinden beri bizimle.

Şimdiye kadar bu dosyada

basit JavaScript kodları çalıştırdık.

Artık aynı dosyayı

Express uygulamamızın başlangıç noktası

hâline getireceğiz.

Mevcut proje yapımız yaklaşık olarak şöyledir:

```text
movie-api/
├── node_modules/
├── index.js
├── movieData.js
├── movieUtils.js
├── createMessage.js
├── package.json
└── package-lock.json
```

## Örnek 4

Modül konusunu öğrenmek için oluşturduğumuz

diğer dosyalar şimdilik projede kalabilir.

Bu bölümde odaklanacağımız dosya:

```text
index.js
```

## Örnek 5

olacak.

`index.js` içindeki

önceki alıştırma kodlarını kaldırıp

ilk Express uygulamamızı yazacağız.

## Kodun Tamamını Önce Görelim

`index.js` dosyasına:

```js
import express from "express";

const app = express();

app.get("/", (req, res) => {
  res.send("Merhaba Express!");
});

app.listen(3000, () => {
  console.log("Server 3000 portunda çalışıyor.");
});
```

## Örnek 6

kodunu yazalım.

İlk bakışta

birkaç yeni parça görüyoruz.

```text
import express from "express";
↓
Express paketini bu dosyada kullan.

const app = express();
↓
Express uygulamasını oluştur.

app.get(...)
↓
Bir Request geldiğinde ne yapılacağını belirt.

res.send(...)
↓
Response gönder.

app.listen(...)
↓
Uygulamanın Request beklemeye başlamasını sağla.
```

## Örnek 7

Bu bölümün amacı

her ayrıntıyı bir anda ezberlemek değildir.

Önce uygulamanın temel iskeletini anlayacağız.

Route yapısını ise

bir sonraki bölümde

çok daha yakından inceleyeceğiz.

## Kurduğumuz Express Paketine Nasıl Ulaşıyoruz?

İlk satır:

```js
import express from "express";
```

## Örnek 8

11. bölümde öğrendiğimiz

`import` bilgisinin

gerçek bir npm paketi üzerindeki kullanımıdır.

Yerel bir dosyada:

```js
import { movies } from "./movieData.js";
```

## Örnek 9

gibi bir yol kullanmıştık.

Burada ise:

```js
"express"
```

## Örnek 10

yazıyoruz.

Başında:

```text
./
```

## Örnek 11

bulunmuyor.

Çünkü Express,

bizim oluşturduğumuz yerel bir dosya değildir.

npm ile kurduğumuz bir pakettir.

```text
"./movieData.js"
↓
Yerel dosya

"express"
↓
Kurulu npm paketi
```

## Örnek 12

Bu nedenle:

```js
import express from "express";
```

## Örnek 13

satırıyla

kurduğumuz Express paketini

`index.js` dosyamızda kullanmaya başlarız.

Burada `import` kavramını

yeniden öğrenmiyoruz.

Daha önce öğrendiğimiz modül sistemini

gerçek bir npm paketinde uyguluyoruz.

🧠 Akılda Kalsın

```text
npm install express
↓
Express'i projeye ekler.

import express from "express";
↓
Kurulu Express paketini
bu dosyada kullanmamızı sağlar.
```

## Örnek 14

## Express Uygulaması Nasıl Oluşuyor?

Express paketine ulaştık.

Şimdi kendi Express uygulamamızı

oluşturmamız gerekir.

Bunu:

```js
const app = express();
```

## Örnek 15

satırıyla yapıyoruz.

Sağ taraftan başlayalım:

```js
express()
```

## Örnek 16

Buradaki parantezler,

`express` fonksiyonunu çağırdığımızı gösterir.

Bu çağrı sonucunda

bir Express uygulaması elde ederiz.

Oluşan uygulamaya

daha sonra tekrar ulaşabilmek için

onu bir değişkende saklarız:

```js
const app = express();
```

## Örnek 17

Buradaki:

```text
app
```

## Örnek 18

adı,

İngilizcedeki:

```text
application
```

## Örnek 19

yani:

```text
uygulama
```

## Örnek 20

kelimesinin yaygın bir kısaltmasıdır.

Değişkenin adının teknik olarak

mutlaka `app` olması gerekmez.

Fakat Express projelerinde

bu kullanım çok yaygındır.

Bu kitapta da:

```js
const app = express();
```

## Örnek 21

kullanacağız.

Şu üç parçayı birbirinden ayıralım:

```text
express
↓
Import ettiğimiz paket üzerinden
kullandığımız yapı

express()
↓
Express uygulaması oluşturur.

app
↓
Oluşturulan uygulamaya
ulaştığımız değişken
```

## Örnek 22

Bundan sonraki birçok Express işlemini

`app` üzerinden gerçekleştireceğiz.

## İlk Request Kuralımızı Ekleyelim

Uygulamayı oluşturduk.

Fakat şu anda

bir Request geldiğinde

ne cevap vereceğini bilmiyor.

Kodumuzdaki:

```js
app.get("/", (req, res) => {
  res.send("Merhaba Express!");
});
```

## Örnek 23

bölümü,

ilk Request kuralımızdır.

Şimdilik bunu

tek tek bütün ayrıntılarıyla

parçalamayacağız.

Bunun için bir sonraki bölümde

**Route** kavramını öğreneceğiz.

Bu bölümde şu kadarını bilmemiz yeterlidir:

```text
GET /
↓
Ana adrese GET Request'i gelir.

app.get(...)
↓
Uygun kod çalışır.

res.send(...)
↓
Response gönderilir.
```

## Örnek 24

Kodun içindeki:

```js
req
```

## Örnek 25

gelen Request'i temsil eder.

```js
res
```

## Örnek 26

ise Response oluştururken

kullandığımız nesnedir.

Yani daha önce öğrendiğimiz:

```text
Request
Response
```

## Örnek 27

kavramları artık

gerçek kodun içinde karşımıza çıkıyor.

```text
Request
↓
req

Response
↓
res
```

## Örnek 28

Bu bölümde `req` içindeki

bütün bilgileri incelemeyeceğiz.

Aynı şekilde

`app.get()` yapısını da

bir sonraki bölümde ayrıntılı olarak öğreneceğiz.

Şimdilik amacımız

ilk çalışan Express uygulamasını kurmaktır.

## İlk Response'umuzu Gönderelim

İlk Request kuralımızın içinde:

```js
res.send("Merhaba Express!");
```

## Örnek 29

satırı bulunuyor.

Buradaki:

```js
res.send()
```

## Örnek 30

Express aracılığıyla

bir Response göndermemizi sağlar.

Parantezlerin içindeki:

```text
Merhaba Express!
```

## Örnek 31

metni,

Response'un Body bölümünde

istemciye gönderilir.

Daha önce öğrendiğimiz kavram

şimdi kod karşılığına kavuştu:

```text
Response
↓
res

Response Body
↓
res.send("Merhaba Express!");
```

## Örnek 32

Tarayıcı bu Response'u aldığında

Body içindeki metni

ekranda gösterebilir.

## Uygulama Neden Hemen Bitmeyecek?

Node.js ile ilk programımızı çalıştırdığımızda

şöyle bir durum görmüştük:

```text
Program başlar.
↓
Kod çalışır.
↓
İş biter.
↓
Program sona erer.
```

## Örnek 33

O bölümde

bir web sunucusunun farklı davranacağını söylemiştik.

İşte şimdi o noktaya geldik.

Bir web uygulamasının

tek bir işlemi yapıp kapanmasını istemeyiz.

Yeni Request'ler gelebilir.

Uygulamamızın:

```text
Request beklemesi,

Request geldiğinde işlemesi,

Response göndermesi,

sonra yeni Request'leri
beklemeye devam etmesi
```

## Örnek 34

gerekir.

Bunun için:

```js
app.listen(3000, () => {
  console.log("Server 3000 portunda çalışıyor.");
});
```

## Örnek 35

kodunu kullanıyoruz.

## app.listen() Ne Yapıyor?

Önce şu kısmı ele alalım:

```js
app.listen(3000)
```

## Örnek 36

`app.listen()`,

Express uygulamasının

gelen bağlantıları dinlemeye başlamasını

ve Request beklemesini sağlar.

Parantez içindeki:

```text
3000
```

## Örnek 37

uygulamanın hangi portta

dinleyeceğini belirtir.

Akışı şöyle düşünebiliriz:

```text
app.listen(3000)
↓
Uygulama çalışmaya devam eder.
↓
3000 numaralı portu dinler.
↓
Yeni Request'leri bekler.
```

## Örnek 38

Bu,

Node.js bölümünde gördüğümüz

basit programdan önemli bir farktır.

```text
Basit program
↓
İşini tamamlayıp sona erebilir.

Web uygulaması
↓
Yeni Request'leri beklemeye devam eder.
```

## Örnek 39

Kodun ikinci bölümü:

```js
() => {
  console.log("Server 3000 portunda çalışıyor.");
}
```

## Örnek 40

uygulama dinlemeye başladığında

çalışan callback fonksiyonudur.

Terminalde:

```text
Server 3000 portunda çalışıyor.
```

## Örnek 41

mesajını görürüz.

Bu mesaj,

kullanıcıya gönderilen bir Response değildir.

Yalnızca geliştirici olarak bize

uygulamanın çalışmaya başladığını bildirir.

## Uygulamamızı Nasıl Çalıştıracağız?

10. bölümde

`package.json` içine şu script'i eklemiştik:

```json
{
  "scripts": {
    "start": "node index.js"
  }
}
```

## Örnek 42

Bu nedenle artık

`node index.js` komutunu

her seferinde doğrudan yazmak zorunda değiliz.

Proje klasöründeki terminalde:

```bash
npm start
```

## Örnek 43

komutunu çalıştıralım.

npm,

`package.json` içindeki:

```text
start
```

## Örnek 44

script'ini bulur

ve arka planda:

```bash
node index.js
```

## Örnek 45

komutunu çalıştırır.

```text
npm start
↓
package.json
↓
"start": "node index.js"
↓
Node.js index.js dosyasını çalıştırır.
```

## Örnek 46

Terminalde:

```text
Server 3000 portunda çalışıyor.
```

## Örnek 47

mesajını görmeliyiz.

Bu kez program hemen sona ermez.

Çünkü:

```js
app.listen(3000)
```

## Örnek 48

uygulamanın

Request beklemeye devam etmesini sağlar.

## Uygulamaya Tarayıcıdan Ulaşalım

Sunucumuz çalışıyor.

Şimdi tarayıcıyı açalım

ve adres çubuğuna:

```text
http://localhost:3000
```

## Örnek 49

yazalım.

Ekranda:

```text
Merhaba Express!
```

## Örnek 50

yazısını görmeliyiz.

İlk Express uygulamamız çalıştı.

Peki:

```text
localhost:3000
```

## Örnek 51

ne anlama geliyor?

## localhost Nedir?

`localhost`,

bu örnekte

kendi bilgisayarımızı ifade eder.

```text
localhost
↓
Bu bilgisayar
```

## Örnek 52

Express uygulamamız

şu anda başka bir uzak sunucuda değil,

kendi bilgisayarımızda çalışıyor.

Tarayıcıya:

```text
http://localhost:3000
```

## Örnek 53

yazdığımızda

kendi bilgisayarımızda çalışan

uygulamaya ulaşmaya çalışıyoruz.

## 3000 Nedir?

Bilgisayarımızda

aynı anda birçok uygulama

ağ üzerinden bağlantı bekleyebilir.

Port numarası,

hangi uygulamaya ulaşmak istediğimizi

ayırt etmeye yardımcı olur.

Biz uygulamamızı:

```js
app.listen(3000)
```

## Örnek 54

ile başlattığımız için

tarayıcıdan da:

```text
http://localhost:3000
```

## Örnek 55

adresine gideriz.

```text
localhost
↓
Hangi bilgisayar?

3000
↓
O bilgisayardaki hangi port?
```

## Örnek 56

Portu,

başlangıç seviyesinde

bir binadaki kapı numarası gibi düşünebiliriz.

```text
localhost
↓
Bina

3000
↓
Kapı numarası
```

## Örnek 57

`3000`,

Express'in zorunlu tuttuğu

özel bir port değildir.

Örneğin:

```js
app.listen(5000, () => {
  console.log("Server 5000 portunda çalışıyor.");
});
```

## Örnek 58

kullansaydık

uygulamaya:

```text
http://localhost:5000
```

## Örnek 59

adresinden ulaşırdık.

Bu kitapta şimdilik:

```text
3000
```

## Örnek 60

portunu kullanmaya devam edeceğiz.

## Tarayıcıda Adresi Açınca Ne Oldu?

Şimdi gerçekleşen yolculuğu

baştan sona takip edelim.

Tarayıcı:

```text
http://localhost:3000
```

## Örnek 61

adresine gitti.

Ana yol için:

```text
/
```

## Örnek 62

bir GET Request'i gönderdi.

Express uygulamamızda:

```js
app.get("/", (req, res) => {
  res.send("Merhaba Express!");
});
```

## Örnek 63

kodu bulunuyordu.

Bu kod Request'i karşıladı

ve:

```text
Merhaba Express!
```

## Örnek 64

Response'unu gönderdi.

Tarayıcı da

gelen Response'u ekranda gösterdi.

```text
Tarayıcı
↓
GET /
↓
Express uygulaması
↓
app.get(...)
↓
res.send(...)
↓
Response
↓
Tarayıcı
```

## Örnek 65

İlk bölümlerde

teorik olarak takip ettiğimiz:

```text
Request
↓
Backend
↓
Response
```

## Örnek 66

yolculuğu artık

kendi bilgisayarımızda gerçekten çalışıyor.

## Bölüm Özeti

Bu bölümde

daha önce oluşturduğumuz:

```text
index.js
```

## Örnek 67

dosyasını

ilk Express uygulamamızın

başlangıç noktası hâline getirdik.

Express'i:

```js
import express from "express";
```

## Örnek 68

ile dosyamıza aldık.

Ardından:

```js
const app = express();
```

## Örnek 69

ile Express uygulamamızı oluşturduk.

İlk Request kuralımızı:

```js
app.get("/", (req, res) => {
  res.send("Merhaba Express!");
});
```

## Örnek 70

şeklinde yazdık.

`req` değerinin Request'i,

`res` değerinin ise Response tarafını

temsil ettiğini gördük.

Uygulamamızın

Request beklemeye başlaması için:

```js
app.listen(3000)
```

## Örnek 71

kullandık.

Ve 10. bölümde oluşturduğumuz:

```bash
npm start
```

## Örnek 72

komutuyla

uygulamamızı çalıştırdık.

Son olarak tarayıcıdan:

```text
http://localhost:3000
```

## Örnek 73

adresine giderek

ilk Response'umuzu gördük.

🧠 Akılda Kalsın

```text
import express from "express"
↓
Express paketini dosyada kullan.

express()
↓
Express uygulamasını oluştur.

app
↓
Oluşturulan uygulamaya ulaş.

app.get(...)
↓
Bir Request için işlem tanımla.

res.send(...)
↓
Response gönder.

app.listen(3000)
↓
Uygulamayı 3000 portunda
Request bekler hâle getir.

npm start
↓
index.js dosyasını çalıştır.
```

## Örnek 74

## Sonraki Adım

Uygulamamız artık çalışıyor.

Fakat şu anda yalnızca:

```text
/
```

## Örnek 75

yoluna gelen Request için

bir işlem tanımladık.

Gerçek bir web uygulamasında ise:

```text
/movies

/about

/contact
```
