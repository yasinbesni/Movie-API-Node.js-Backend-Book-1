# Bölüm 13 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Express'i Projemize Eklemek

## Yeni Bir Proje mi Oluşturacağız?

Hayır.

Node.js bölümlerinde

zaten bir proje oluşturmuştuk:

```text
movie-api
```

## Örnek 2

Önce:

```bash
npm init -y
```

## Örnek 3

komutuyla `package.json` dosyamızı hazırladık.

Ardından programımızı çalıştırmak için:

```json
"start": "node index.js"
```

## Örnek 4

script'ini ekledik.

Modüller bölümünde ise

ES Modules kullanabilmek için:

```json
"type": "module"
```

## Örnek 5

ayarını yaptık.

Yani elimizde

hazır bir Node.js projesi zaten var.

Şimdi baştan yeni bir proje oluşturmayacağız.

Mevcut projemizi

bir adım daha ileri taşıyacağız.

```text
Node.js projemiz
↓
Express eklenir.
↓
Web uygulaması geliştirmeye başlarız.
```

## Örnek 6

Bir önceki bölümde

Express'in neden gerekli olduğunu öğrendik.

Şimdi onu gerçekten

projemizin bir parçası hâline getireceğiz.

## Şu Anda Projemizde Neler Var?

Node.js bölümlerinde oluşturduğumuz

proje yapısını hatırlayalım:

```text
movie-api/
├── index.js
├── movieData.js
├── movieUtils.js
├── createMessage.js
└── package.json
```

## Örnek 7

Bu dosyaların bazılarını

modül sistemini öğrenmek için oluşturduk.

Şimdilik onları silmemize gerek yok.

Bu bölümde yapacağımız temel işlem:

```text
movie-api
↓
Express paketini ekle
```

## Örnek 8

olacak.

`package.json` dosyamızda da

daha önce eklediğimiz temel ayarlar bulunuyor.

Örneğin:

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

## Örnek 9

Bilgisayarınızdaki npm sürümüne göre

dosyada başka alanlar da bulunabilir.

Burada önemli olan,

projemizin zaten npm ile hazırlanmış olmasıdır.

Bu nedenle:

```bash
npm init -y
```

## Örnek 10

komutunu yeniden çalıştırmamıza gerek yoktur.

🧠 Akılda Kalsın

```text
npm init -y
↓
Daha önce yaptık.

package.json
↓
Zaten var.

"type": "module"
↓
Zaten ekledik.

"start": "node index.js"
↓
Zaten hazır.
```

## Örnek 11

Sıradaki eksik parça:

```text
Express
```

## Örnek 12

## Express'i Kuralım

Terminalimizin:

```text
movie-api
```

## Örnek 13

klasörünün içinde olduğundan emin olalım.

Ardından şu komutu çalıştıralım:

```bash
npm install express
```

## Örnek 14

Bu komutun yapısı

bize artık yabancı değil.

npm bölümünde paket kurmanın temel biçimini:

```bash
npm install paket-adi
```

## Örnek 15

olarak öğrenmiştik.

Şimdi genel bir örnek yerine

gerçekten kullanacağımız paketi kuruyoruz:

```text
paket-adi
↓
express
```

## Örnek 16

Yani:

```bash
npm install express
```

## Örnek 17

npm'e şunu söyler:

> Express paketini bu projeye ekle.

Kurulum tamamlandığında

Express artık projemizin

kullanabileceği paketlerden biri olur.

## Kurulumdan Sonra Ne Değişti?

Express'i kurduktan sonra

projemizde daha önce

kavramsal olarak öğrendiğimiz

bazı yapıları gerçekten görürüz.

İlk olarak:

```text
node_modules/
```

## Örnek 18

klasörü oluşur.

Bu klasörde

Express'in dosyaları

ve Express'in ihtiyaç duyduğu

diğer paketler bulunabilir.

Ayrıca:

```text
package-lock.json
```

## Örnek 19

dosyası oluşur.

Bu dosya,

kurulumda kullanılan paketlerin

ve bağımlılıklarının

sürüm yapısını ayrıntılı biçimde kaydeder.

`package.json` dosyamız da güncellenir.

Express artık:

```text
dependencies
```

## Örnek 20

alanında görünür.

Örneğin:

```json
{
  "name": "movie-api",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "express": "..."
  }
}
```

## Örnek 21

Buradaki:

```text
...
```

## Örnek 22

gerçek bir sürüm numarasını temsil eder.

Sürüm değeri

Express'in kurulan sürümüne göre değişebilir.

Bizim için şu anda önemli olan:

```text
dependencies
↓
express
```

## Örnek 23

bağlantısını görmektir.

## Daha Önce Öğrendiklerimiz Şimdi Gerçekleşti

npm bölümünde

bir paket kurulduğunda karşılaşabileceğimiz

üç önemli yapıyı öğrenmiştik:

```text
package.json

node_modules/

package-lock.json
```

## Örnek 24

O bölümde

belirli bir paketi gerçekten kurmamıştık.

Şimdi ise:

```bash
npm install express
```

## Örnek 25

komutunu çalıştırdık

ve bu yapıları

gerçek bir paket kurulumu sonucunda gördük.

Akışı birlikte okuyalım:

```text
npm install express
↓
Express indirilir.
↓
node_modules oluşur.
↓
package.json güncellenir.
↓
package-lock.json oluşur.
```

## Örnek 26

Burada yeni bir npm kavramı öğrenmiyoruz.

Daha önce öğrendiğimiz bilgiyi

gerçek projemizde uyguluyoruz.

## Projemizin Güncel Yapısı

Express kurulduktan sonra

proje yapımız yaklaşık olarak şöyle görünür:

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

## Örnek 27

Artık Express:

```text
movie-api
```

## Örnek 28

projemizin bir bağımlılığıdır.

Fakat henüz

Express'i kullanan bir uygulama yazmadık.

```text
Express kuruldu.
↓
Paket projede hazır.
↓
Henüz Express uygulaması oluşturulmadı.
```

## Örnek 29

Bu ayrım önemlidir.

Bir paketin projede kurulu olması,

onun kodumuzda otomatik olarak

kullanıldığı anlamına gelmez.

Bir sonraki adımda

Express'i `index.js` dosyamızın içine alacağız

ve ilk uygulamamızı oluşturacağız.

## Bölüm Özeti

Bu bölümde

yeni bir proje oluşturmadık.

Node.js bölümlerinde hazırladığımız:

```text
movie-api
```

## Örnek 30

projesini kullanmaya devam ettik.

`package.json` dosyamız zaten vardı.

`start` script'imiz zaten hazırdı.

ES Modules için:

```json
"type": "module"
```

## Örnek 31

ayarını da daha önce yapmıştık.

Bu nedenle doğrudan:

```bash
npm install express
```

## Örnek 32

komutunu çalıştırdık.

Kurulumdan sonra:

```text
dependencies
node_modules/
package-lock.json
```

## Örnek 33

yapılarının

gerçek bir paket kurulumu sırasında

nasıl ortaya çıktığını gördük.

Artık Express projemizde bulunuyor.

## Sonraki Adım

Express paketini kurduk.

Şimdi onu

JavaScript kodumuzun içinde kullanacağız.

`index.js` dosyamız zaten var.

Fakat şimdiye kadar

onu basit Node.js örnekleri için kullandık.

Bir sonraki bölümde

bu dosyanın içeriğini

ilk Express uygulamamızla değiştireceğiz.

İlk satırımız:

```js
import express from "express";
```

## Örnek 34

olacak.

Ardından:

```js
const app = express();
```
