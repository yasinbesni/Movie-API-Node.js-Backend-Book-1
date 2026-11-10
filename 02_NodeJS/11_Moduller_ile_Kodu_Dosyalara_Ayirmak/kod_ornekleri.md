# Bölüm 11 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Modüller ile Kodu Dosyalara Ayırmak

## Bütün Kod Tek Dosyada mı Kalacak?

Şimdiye kadar küçük örneklerimizi

tek bir dosyada çalıştırdık:

```text
index.js
```

## Örnek 2

Küçük bir program için

bu yeterli olabilir.

Fakat gerçek bir backend uygulaması büyüdükçe

aynı dosyada farklı görevler birikmeye başlar.

Örneğin ileride:

```text
Request'leri karşılayan kodlar,

veri işlemleri,

yardımcı fonksiyonlar,

veritabanı işlemleri,

uygulama ayarları
```

## Örnek 3

aynı projede bulunabilir.

Bütün bunları tek dosyada tutarsak

kodun okunması ve yönetilmesi zorlaşır.

Bu yüzden kodumuzu

sorumluluklarına göre farklı dosyalara ayırırız.

```text
Tek büyük dosya
↓
Görevleri ayır.
↓
Daha küçük ve anlamlı dosyalar
```

## Örnek 4

Bu noktada **modül** kavramıyla tanışırız.

## Modül Nedir?

Bu kitapta başlangıç düzeyinde

bir JavaScript dosyasını

kendi görevi olan bir modül gibi düşünebiliriz.

Örneğin:

```text
movieData.js
↓
Film verilerini tutar.

movieUtils.js
↓
Film verileri üzerinde çalışan fonksiyonları tutar.

index.js
↓
Programın başlangıç noktasıdır.
```

## Örnek 5

Fakat kodları ayrı dosyalara koymak

tek başına yeterli değildir.

Bir dosyada oluşturduğumuz değeri

başka bir dosyada kullanabilmemiz gerekir.

Bunun için iki önemli sözcük kullanacağız:

```text
export
↓
Bir değeri başka modüllerin kullanımına aç.

import
↓
Başka bir modülün dışa açtığı değeri kullan.
```

## Örnek 6

## ES Modules İçin Projemizi Hazırlayalım

Önceki bölümde

`package.json` dosyasını oluşturmuştuk.

Node.js projemizde

`import` ve `export` sözdizimini kullanabilmek için

`package.json` dosyamıza şu alanı ekleyelim:

```json
{
  "type": "module"
}
```

## Örnek 7

Örneğin dosyamız şöyle görünebilir:

```json
{
  "name": "movie-api",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "start": "node index.js"
  }
}
```

## Örnek 8

Buradaki:

```json
"type": "module"
```

## Örnek 9

Node.js'e şunu söyler:

> Bu projedeki `.js` dosyalarında ES Module sistemini kullanacağız.

Artık `.js` dosyalarımızda

`import` ve `export` kullanabiliriz.

## İlk Değeri Başka Bir Dosyaya Taşıyalım

Klasör yapımızı şöyle hazırlayalım:

```text
movie-api/
├── index.js
├── movieData.js
└── package.json
```

## Örnek 10

`movieData.js` dosyasına:

```js
export const movies = [
  "Interstellar",
  "Inception",
  "The Matrix",
];
```

## Örnek 11

kodunu yazalım.

Buradaki:

```js
export
```

## Örnek 12

`movies` değerinin

bu dosyanın dışından da kullanılabilmesini sağlar.

```text
movieData.js
↓
movies oluşturulur.
↓
export edilir.
```

## Örnek 13

Fakat `index.js` henüz

bu değeri kendiliğinden göremez.

Onu içeri almamız gerekir.

## Başka Dosyadaki Değeri import Edelim

`index.js` içine:

```js
import { movies } from "./movieData.js";

console.log(movies);
```

## Örnek 14

kodunu yazalım.

Programı çalıştıralım:

```bash
npm start
```

## Örnek 15

veya:

```bash
node index.js
```

## Örnek 16

Terminalde film listesini görürüz.

Artık bir modülde

dışa açılan değeri

başka bir modülde kullanabiliyoruz.

Akış şöyle gerçekleşir:

```text
movieData.js
↓
movies değerini export eder.
↓
index.js
↓
movies değerini import eder.
↓
Kod değeri kullanır.
```

## Örnek 17

## import Satırını Parçalara Ayıralım

Şu satıra bakalım:

```js
import { movies } from "./movieData.js";
```

## Örnek 18

İlk bölüm:

```js
import
```

## Örnek 19

başka bir modülden değer alacağımızı söyler.

Sonra:

```js
{ movies }
```

## Örnek 20

hangi değeri istediğimizi belirtir.

Ardından:

```js
from
```

## Örnek 21

bu değerin nereden geleceğini bağlar.

Son olarak:

```js
"./movieData.js"
```

## Örnek 22

hangi dosyadan import yaptığımızı belirtir.

Satırı şöyle okuyabiliriz:

> `movieData.js` dosyasının dışa açtığı `movies` değerini bu dosyada kullan.

## Neden ./ Kullanıyoruz?

Şu ifade:

```js
"./movieData.js"
```

## Örnek 23

`./` ile başlar.

Bu bize

kendi proje dosyalarımızdan birine

göreli bir yol kullandığımızı gösterir.

Örneğin:

```text
movie-api/
├── index.js
└── movieData.js
```

## Örnek 24

iki dosya aynı klasördeyse

`index.js` içinden:

```js
"./movieData.js"
```

## Örnek 25

yolunu kullanabiliriz.

Şimdilik şöyle hatırlayabiliriz:

```text
"./movieData.js"
↓
Projede bizim oluşturduğumuz yerel bir dosya
```

## Örnek 26

Bu ayrım, 14. bölümde Express'i kodumuza `import` ettiğimizde

çok önemli hâle gelecek.

## Named Export Nedir?

Şu kullanım:

```js
export const movies = [];
```

## Örnek 27

bir **named export** örneğidir.

Çünkü dışa açılan değerin

belirli bir adı vardır:

```text
movies
```

## Örnek 28

Import ederken de

bu adı süslü parantez içinde kullanırız:

```js
import { movies } from "./movieData.js";
```

## Örnek 29

Bir dosyadan birden fazla named export da yapabiliriz.

Örneğin `movieUtils.js` oluşturalım:

```js
export const getMovieCount = (movies) => {
  return movies.length;
};

export const getFirstMovie = (movies) => {
  return movies[0];
};
```

## Örnek 30

Bunları `index.js` içinde şöyle alabiliriz:

```js
import {
  getMovieCount,
  getFirstMovie,
} from "./movieUtils.js";
```

## Örnek 31

Burada:

```text
getMovieCount

ve

getFirstMovie
```

## Örnek 32

iki ayrı named export'tur.

## Default Export Nedir?

Bir başka export biçimi de

**default export**tur.

Örneğin `createMessage.js` dosyasına:

```js
const createMessage = () => {
  return "Node.js modülleri çalışıyor.";
};

export default createMessage;
```

## Örnek 33

kodunu yazalım.

Bu değeri başka bir dosyada:

```js
import createMessage from "./createMessage.js";
```

## Örnek 34

şeklinde alabiliriz.

Burada süslü parantez kullanmadık.

Çünkü değer:

```js
export default
```

## Örnek 35

ile dışa açılmıştır.

İki yapıyı yan yana görelim:

```text
Named export
↓
export const movies = ...
↓
import { movies } from ...

Default export
↓
export default createMessage
↓
import createMessage from ...
```

## Örnek 36

Şimdilik bu farkı tanımamız yeterlidir.

İlerleyen bölümlerde

çok sayıda gerçek örnek göreceğiz.

## Yerel Dosya ile npm Paketi Aynı Şekilde mi Gösterilir?

Kendi oluşturduğumuz bir dosyayı

şöyle import ettik:

```js
import { movies } from "./movieData.js";
```

## Örnek 37

Buradaki:

```text
./movieData.js
```

## Örnek 38

bir dosya yoludur.

npm ile kurulan paketleri import ederken ise

paketin adını doğrudan kullanabiliriz.

Örneğin genel bir kullanım

şöyle görünebilir:

```js
import packageName from "paket-adi";
```

## Örnek 39

Burada dikkat etmemiz gereken nokta,

import satırının başındaki isim veya

süslü parantez kullanımı değildir.

Bir npm paketinin nasıl import edileceği,

paketin değerlerini nasıl dışa açtığına bağlıdır.

Bizim şu anda görmek istediğimiz fark şudur:

```text
"./movieData.js"
↓
Yerel dosya yolu

"paket-adi"
↓
Kurulu npm paketinin adı
```

## Örnek 40

Dikkat edersek

npm paketinin adının başında:

```text
./
```

## Örnek 41

bulunmaz.

Çünkü burada

kendi oluşturduğumuz bir dosyaya değil,

projemize kurulmuş bir pakete

ulaşmaya çalışıyoruz.

İlerleyen bölümde

gerçek bir npm paketini kurduğumuzda

bu farkı uygulamalı olarak göreceğiz.

## Kodları Dosyalara Ayırmak Neden Önemli?

Şu yapıyı düşünelim:

```text
movie-api/
├── index.js
├── movieData.js
├── movieUtils.js
├── createMessage.js
└── package.json
```

## Örnek 42

Her dosyanın görevi daha açık hâle gelir.

```text
movieData.js
↓
Verileri tutar.

movieUtils.js
↓
Veri üzerinde çalışan fonksiyonları tutar.

createMessage.js
↓
Belirli bir görev için fonksiyon sağlar.

index.js
↓
Modülleri bir araya getirir ve programı başlatır.
```

## Örnek 43

İleride backend projemiz büyüdükçe

çok daha düzenli klasör yapıları oluşturacağız.

Ancak temel düşünce değişmeyecek:

> Her sorumluluğu mümkün olduğunca
> anlamlı bir yerde tut.

🧠 Akılda Kalsın

```text
export
↓
Bir değeri başka modüllerin kullanımına açar.

import
↓
Başka modülün dışa açtığı değeri kullanır.

"./dosya.js"
↓
Yerel bir dosyayı gösterir.

"paket-adi"
↓
Kurulu bir npm paketini gösterebilir.
```

## Örnek 44

## Bölüm Özeti

Bu bölümde

kodlarımızı farklı dosyalara ayırmanın

neden gerekli olduğunu gördük.

ES Modules kullanmak için

`package.json` içine:

```json
"type": "module"
```

## Örnek 45

alanını ekledik.

Bir değeri dışa açmak için:

```js
export
```

## Örnek 46

başka bir modüldeki değeri kullanmak için:

```js
import
```

## Örnek 47

kullandık.

Named export ile default export

arasındaki temel farkı gördük.

Ve ayrıca:

```text
"./movieData.js"
↓
Yerel dosya

"paket-adi"
↓
npm paketi
```

## Örnek 48

ayrımını kurduk.

## Sonraki Adım

Artık Node.js tarafında

ihtiyacımız olan temel araçları tanıyoruz.

```text
JavaScript
↓
Node.js

node index.js

npm

package.json

import / export
```

## Örnek 49

Şimdi yeni bir soruya geçebiliriz.

Node.js,

JavaScript kodlarımızı

sunucu tarafında çalıştırmamızı sağlıyor.

Peki web uygulaması geliştirirken

Request ve Response akışını

daha kolay nasıl yöneteceğiz?

Bir sonraki bölümde

bu işi kolaylaştıran bir araçla tanışacağız:

```text
Express
```
