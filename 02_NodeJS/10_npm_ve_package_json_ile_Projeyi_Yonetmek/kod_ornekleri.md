# Bölüm 10 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# npm ve package.json ile Projeyi Yönetmek

## Her Şeyi Kendimiz mi Yazacağız?

Artık bir JavaScript dosyasını

Node.js ile çalıştırabiliyoruz.

```bash
node index.js
```

## Örnek 2

Fakat gerçek bir backend uygulaması büyüdükçe

ihtiyaçlarımız da artar.

Örneğin ileride:

```text
web sunucusu oluşturmak,

verileri doğrulamak,

veritabanıyla çalışmak,

farklı servislerle iletişim kurmak
```

## Örnek 3

gibi işler yapacağız.

Bu işlerin her biri için

bütün kodu sıfırdan yazmak zorunda değiliz.

JavaScript ekosisteminde

başka geliştiricilerin hazırladığı

kullanıma hazır paketler bulunur.

Peki bu paketleri

Node.js projemize nasıl ekleyeceğiz?

İşte burada **npm** devreye girer.

## npm Nedir?

npm,

Node.js projelerinde paketleri

yönetmemizi sağlayan bir **paket yöneticisidir**.

Basit bir akışla düşünelim:

```text
Bir pakete ihtiyaç duyarız.
↓
npm ile projeye ekleriz.
↓
Paket projenin bir parçası olur.
↓
Kodumuzda kullanabiliriz.
```

## Örnek 4

Şimdi önce npm'in

bir projeyi nasıl yönettiğini

ve paketlerle ilgili bilgileri

nerede tuttuğunu anlamamız gerekir.

## npm Kullanılabiliyor mu?

Terminalde şu komutu çalıştıralım:

```bash
npm -v
```

## Örnek 5

Bir sürüm numarası görüyorsak

npm komutu kullanılabiliyor demektir.

Node.js ve npm kontrolünü

şöyle ayırabiliriz:

```text
node -v
↓
Node.js sürümünü gösterir.

npm -v
↓
npm sürümünü gösterir.
```

## Örnek 6

Şimdilik sürüm numarasını ezberlememize gerek yok.

Önemli olan her iki komutun da

çalışıyor olmasıdır.

## Bir Projeyi npm ile Nasıl Hazırlarız?

Şimdi küçük bir çalışma klasörünü

npm ile yönetilebilen

bir proje hâline getirelim.

Yeni bir klasör oluşturalım.

Örneğin:

```text
movie-api
```

## Örnek 7

Şimdi terminali bu klasörün içinde açalım

ve şu komutu çalıştıralım:

```bash
npm init -y
```

## Örnek 8

Komut tamamlandığında

klasörün içinde yeni bir dosya oluşur:

```text
package.json
```

## Örnek 9

Yapımız artık şöyle görünür:

```text
movie-api/
└── package.json
```

## Örnek 10

Önceki bölümden tanıdığımız

`index.js` dosyasını da

bu proje klasörünün içinde oluşturalım.

```text
movie-api/
├── index.js
└── package.json
```

## Örnek 11

`index.js` dosyasına şimdilik

basit bir mesaj yazmamız yeterlidir:

```js
console.log("Movie API projesi çalışıyor.");
```

## Örnek 12

Böylece birazdan oluşturacağımız

`start` komutunu da

gerçekten çalıştırabileceğiz.

## package.json Nedir?

`package.json`,

proje hakkında önemli bilgileri tutar.

Örneğin:

```text
projenin adı,

sürümü,

çalıştırma komutları,

ve kullandığı paketler
```

## Örnek 13

bu dosyada yer alabilir.

Bunu şimdilik projemizin

**kimlik kartı ve paket listesi** gibi düşünebiliriz.

`npm init -y` sonrasında

şuna benzer bir dosya görebiliriz:

```json
{
  "name": "movie-api",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {},
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

## Örnek 14

Bilgisayarınızdaki npm sürümüne göre

oluşan alanlarda küçük farklılıklar olabilir.

Bu bölümde bütün alanları

ezberlemeye çalışmayacağız.

Önce en çok kullanacağımız parçaları anlayacağız.

## npm init -y Komutu Ne Yaptı?

Komutu parçalayalım:

```text
npm
↓
Paket yöneticisini kullan.

init
↓
Bu klasörde bir npm projesi başlat.

-y
↓
Başlangıç sorularını varsayılan cevaplarla geç.
```

## Örnek 15

Yani:

```bash
npm init -y
```

## Örnek 16

komutunu şu şekilde okuyabiliriz:

> Bu klasörü hızlıca bir npm projesi olarak hazırla.

Sonuçta:

```text
Proje klasörü
↓
npm init -y
↓
package.json
```

## Örnek 17

oluşur.

## scripts Alanı Ne İşe Yarar?

Önceki bölümde programımızı:

```bash
node index.js
```

## Örnek 18

komutuyla çalıştırdık.

Bu komutu sık sık kullanacağımızı düşünelim.

`package.json` içindeki `scripts` alanı sayesinde

bu komuta bir isim verebiliriz.

Örneğin:

```json
{
  "scripts": {
    "start": "node index.js"
  }
}
```

## Örnek 19

Artık terminalde:

```bash
npm start
```

## Örnek 20

yazdığımızda npm,

`start` adına karşılık gelen komutu bulur:

```text
npm start
↓
package.json içindeki start script'i
↓
node index.js
```

## Örnek 21

Bu bize uzun veya sık kullandığımız komutları

tek bir yerde düzenleme imkânı verir.

🧠 Akılda Kalsın

```text
scripts
↓
Projede sık kullandığımız komutlara isim verebilir.
```

## Örnek 22

## Bir Paket Nasıl Kurulur?

npm ile paket kurmanın temel biçimi şöyledir:

```bash
npm install paket-adi
```

## Örnek 23

Örneğin bir paketin adı:

```text
ornek-paket
```

## Örnek 24

olsaydı kurulum komutu şöyle görünürdü:

```bash
npm install ornek-paket
```

## Örnek 25

Şimdilik belirli bir paket kurmuyoruz.

Önce bir paket kurulduğunda

projemizde hangi yapıların değişebileceğini

anlamamız gerekiyor.

Bir paket kurulduğunda

genellikle üç yerde değişiklik görürüz:

```text
package.json

node_modules/

package-lock.json
```

## Örnek 26

Şimdi bunların görevlerini ayıralım.

## dependencies Nedir?

Bir paket kurulduğunda

`package.json` içinde

`dependencies` adlı bir alan oluşabilir.

Örneğin `ornek-paket` adlı bir paket kurulduğunda

şuna benzer bir kayıt görebiliriz:

```json
{
  "dependencies": {
    "ornek-paket": "..."
  }
}
```

## Örnek 27

Buradaki sürüm değeri

kurulan sürüme göre değişebilir.

Bizim için önemli olan şudur:

```text
dependencies
↓
Projenin çalışmak için kullandığı paketleri belirtir.
```

## Örnek 28

Yani başka biri projemizin

`package.json` dosyasına baktığında

hangi paketlere ihtiyaç duyduğunu anlayabilir.

## node_modules Nedir?

Bir paket kurulduğunda

paketin gerçek dosyaları

projemize indirilmelidir.

Bu dosyalar genellikle:

```text
node_modules/
```

## Örnek 29

klasörü altında tutulur.

Bir paket kurulduğunda

yalnızca o paketin kendi dosyaları değil,

çalışması için ihtiyaç duyduğu

başka paketler de burada bulunabilir.

Bu yüzden `node_modules`

beklediğimizden daha büyük olabilir.

```text
package.json
↓
Hangi paketlere ihtiyacımız olduğunu söyler.

node_modules
↓
Kurulan paketlerin gerçek dosyalarını içerir.
```

## Örnek 30

`node_modules` içindeki paket dosyalarını

normalde elle düzenlemeyiz.

## package-lock.json Nedir?

Bir paket kurduğumuzda

npm genellikle şu dosyayı da oluşturur:

```text
package-lock.json
```

## Örnek 31

Bu dosya,

kurulan paketlerin ve onların bağımlılıklarının

tam sürüm yapısını ayrıntılı biçimde kaydeder.

Şimdilik şu ayrım yeterlidir:

```text
package.json
↓
Projenin hangi paketlere ihtiyaç duyduğunu belirtir.

package-lock.json
↓
Kurulumda kullanılan paket sürümlerini ayrıntılı biçimde kaydeder.
```

## Örnek 32

`package-lock.json` dosyasını da

normalde elle hazırlamayız.

npm bizim için yönetir.

## node_modules Neden Her Zaman Paylaşılmaz?

`node_modules` klasörü

çok sayıda dosya içerebilir.

Bu yüzden bir proje Git ile paylaşılırken

bu klasör çoğunlukla doğrudan depoya eklenmez.

Peki başka biri paketleri nasıl elde eder?

Projedeki:

```text
package.json

ve

package-lock.json
```

## Örnek 33

dosyalarına bakılarak

gerekli paketler yeniden kurulabilir.

Bu aşamada Git ayrıntısına girmeyeceğiz.

Önemli olan şu ilişkiyi görmektir:

```text
Paketlerin kendisi
↓
node_modules

Paketlerin kaydı
↓
package.json + package-lock.json
```

## Örnek 34

## type Alanı Neden Bir Sonraki Bölümde Önemli Olacak?

Bir sonraki bölümde

kodumuzu birden fazla dosyaya ayıracağız.

Bir dosyadaki değeri

başka bir dosyada kullanabilmek için:

```js
import
```

## Örnek 35

ve:

```js
export
```

## Örnek 36

sözdizimini kullanacağız.

Node.js projemizde

`.js` dosyalarında ES Module sistemini kullanacağımızı belirtmek için

`package.json` dosyasına:

```json
{
  "type": "module"
}
```

## Örnek 37

alanını ekleyeceğiz.

Bu satırı burada yalnızca görüyoruz.

Neden gerekli olduğunu

ve `import` ile `export`un nasıl çalıştığını

bir sonraki bölümde adım adım öğreneceğiz.

🧠 Akılda Kalsın

```text
npm
↓
Paket yöneticisi

npm init -y
↓
package.json oluşturur.

package.json
↓
Projenin temel bilgilerini, script'lerini ve paket kayıtlarını tutar.

node_modules
↓
Kurulan paketlerin dosyalarını içerir.

package-lock.json
↓
Kurulan paket sürümlerini ayrıntılı biçimde kaydeder.
```

## Örnek 38

## Bölüm Özeti

Bu bölümde

Node.js projelerinde paketlerin

nasıl yönetildiğine giriş yaptık.

Önce npm'i tanıdık.

Ardından:

```bash
npm init -y
```

## Örnek 39

komutuyla:

```text
package.json
```

## Örnek 40

dosyasını oluşturduk.

`scripts` alanı sayesinde

bir komuta isim verebildiğimizi gördük:

```json
{
  "scripts": {
    "start": "node index.js"
  }
}
```

## Örnek 41

Paket kurma biçiminin:

```bash
npm install paket-adi
```

## Örnek 42

olduğunu öğrendik.

Ve paket kurulumu sırasında karşılaşacağımız

üç önemli yapıyı ayırdık:

```text
package.json
node_modules/
package-lock.json
```

## Örnek 43

## Sonraki Adım

Artık bir Node.js projesini

npm ile hazırlayabiliyoruz.

Fakat bütün kodumuzu

tek bir `index.js` dosyasına yazarsak

proje büyüdükçe dosyamız karmaşıklaşacaktır.

Kodlarımızı farklı dosyalara ayırmamız gerekir.

Peki bir dosyadaki değeri

başka bir dosyada nasıl kullanacağız?

Bir sonraki bölümde:

```text
modül
export
import
```
