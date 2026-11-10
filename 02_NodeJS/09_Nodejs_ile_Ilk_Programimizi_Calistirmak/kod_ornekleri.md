# Bölüm 9 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Node.js ile İlk Programımızı Çalıştırmak

## Node.js Çalışmaya Hazır mı?

Terminali açtığımızda karşımızda yanıp sönen bir imleç var. Birazdan kendi yazdığımız JavaScript kodunu tarayıcıya ihtiyaç duymadan çalıştıracağız. Ekranda `Node.js çalışıyor.` mesajını görmek küçük bir adım olacak; ama bu adımı kendi bilgisayarımızda atmak önemli.

Önce Node.js'in bilgisayarımızda hazır olup olmadığını kontrol edelim. Terminale şu komutu yazın:

```bash
node -v
```

## Örnek 2

Node.js kullanılabiliyorsa

terminalde bir sürüm numarası görürüz.

Örneğin:

```text
vXX.X.X
```

## Örnek 3

Buradaki sayının tam olarak kaç olduğu

bu bölüm için önemli değildir.

Önemli olan terminalin

`node` komutunu tanımasıdır.

```text
node -v
↓
Node.js kullanılabiliyor mu?
```

## Örnek 4

Bir sürüm numarası görüyorsak

ilk programımıza geçebiliriz.

Eğer komut bulunamıyorsa

Node.js bilgisayarınızda kurulu olmayabilir.

Bu durumda Node.js'in güncel **LTS** sürümünü

resmî kaynağından kurduktan sonra

terminali yeniden açıp

aynı komutu tekrar çalıştırmanız gerekir.

## İlk Çalışma Klasörümüzü Oluşturalım

Kodlarımızı rastgele bir yerde tutmak yerine

küçük bir proje klasörü oluşturalım.

Örneğin:

```text
node-basics
```

## Örnek 5

Bu klasörün içinde

şu dosyayı oluşturalım:

```text
index.js
```

## Örnek 6

Başlangıç yapımız şöyle olsun:

```text
node-basics/
└── index.js
```

## Örnek 7

`index.js` dosyasına

şu kodu yazalım:

```js
console.log("Node.js çalışıyor.");
```

## Örnek 8

Kod çok basit.

Terminale bir mesaj yazdırıyor.

Fakat burada bizim için önemli olan

mesajın kendisi değil.

Bu JavaScript kodunu

bir HTML dosyasına bağlamadan

çalıştıracak olmamızdır.

## JavaScript Dosyasını Node.js ile Çalıştıralım

Terminalimizin

`node-basics` klasörünün içinde olduğundan emin olalım.

Ardından:

```bash
node index.js
```

## Örnek 9

komutunu çalıştıralım.

Terminalde şu çıktıyı görürüz:

```text
Node.js çalışıyor.
```

## Örnek 10

İlk Node.js programımız çalıştı.

Şu anda yaptığımız işlemi

adım adım düşünelim:

```text
index.js
↓
JavaScript kodunu içerir.

node index.js
↓
Node.js'e bu dosyayı çalıştırmasını söyler.

Terminal
↓
Kodun çıktısını gösterir.
```

## Örnek 11

Bir tarayıcı açmadık.

HTML oluşturmadık.

Bir web sayfasına ihtiyaç duymadık.

JavaScript dosyamız

Node.js ortamında çalıştı.

## node index.js Komutunu Nasıl Okumalıyız?

Şu komutu iki parçaya ayıralım:

```bash
node index.js
```

## Örnek 12

İlk bölüm:

```text
node
```

## Örnek 13

Node.js'i kullanacağımızı belirtir.

İkinci bölüm:

```text
index.js
```

## Örnek 14

hangi JavaScript dosyasının çalıştırılacağını belirtir.

Yani komutu şöyle okuyabiliriz:

> `index.js` dosyasındaki JavaScript kodunu Node.js ile çalıştır.

Bu komut ileride çok tanıdık hâle gelecek.

Çalıştırmak istediğimiz JavaScript dosyasının adı

`index.js` olduğunda

aynı komutu tekrar kullanabileceğiz:

```bash
node index.js
```

## Örnek 15

## Terminal Neden Doğru Klasörde Olmalı?

Şu yapıya sahip olduğumuzu düşünelim:

```text
node-basics/
└── index.js
```

## Örnek 16

`node index.js` komutunu çalıştırdığımızda

Node.js,

terminalin bulunduğu konumdan

`index.js` dosyasını arar.

Terminal başka bir klasördeyse

Node.js istediğimiz dosyayı bulamayabilir.

Bu durumda dosyanın bulunamadığını belirten

bir hata mesajıyla karşılaşabiliriz.

Bu yüzden bir komut çalıştırmadan önce

şu iki soruyu sormak faydalıdır:

```text
Hangi klasördeyim?
↓
Çalıştırmak istediğim dosya burada mı?
```

## Örnek 17

VS Code'da proje klasörünü açıp

yerleşik terminali kullanmak

bu süreci takip etmeyi kolaylaştırır.

## Kodumuzu Biraz Değiştirelim

`index.js` dosyasını şu şekilde güncelleyelim:

```js
const movieTitle = "Interstellar";
const releaseYear = 2014;

console.log(movieTitle);
console.log(releaseYear);
```

## Örnek 18

Dosyayı kaydedelim

ve tekrar çalıştıralım:

```bash
node index.js
```

## Örnek 19

Terminalde:

```text
Interstellar
2014
```

## Örnek 20

çıktısını görürüz.

Burada JavaScript'in temel söz dizimi değişmedi.

Değişen şey,

kodun nerede çalıştığıdır.

```text
JavaScript dosyası
↓
Node.js
↓
Terminalde çalışan program
```

## Örnek 21

## console.log() Çıktısı Nereye Gidiyor?

Node.js tarafında:

```js
console.log("Merhaba");
```

## Örnek 22

kullandığımızda

çıktıyı terminalde görürüz.

Bu nedenle backend geliştirirken

kodumuzun belirli noktalarda ne yaptığını görmek için

`console.log()` sık sık karşımıza çıkacaktır.

Örneğin:

```js
const message = "Request alındı.";

console.log(message);
```

## Örnek 23

çalıştırıldığında terminalde:

```text
Request alındı.
```

## Örnek 24

görürüz.

Şimdilik bunu yalnızca

programımızdan terminale bilgi göndermenin

basit bir yolu olarak düşünelim.

## Program Neden Hemen Bitti?

Şu kodu çalıştırdığımızda:

```js
console.log("Node.js çalışıyor.");
```

## Örnek 25

Node.js mesajı yazdırır

ve program sona erer.

Çünkü yapması gereken başka bir iş kalmamıştır.

```text
Program başlar.
↓
Kod çalışır.
↓
İş biter.
↓
Program sona erer.
```

## Örnek 26

Bu davranış ileride önemli bir farkı anlamamızı sağlayacak.

Şu anda çalıştırdığımız programın

yapması gereken işlem tamamlandığında

program sona eriyor.

Fakat ilerleyen bölümlerde

bir web sunucusu oluşturduğumuzda

farklı bir davranış göreceğiz.

Sunucu,

yeni Request'leri karşılayabilmek için

çalışmaya devam edecektir.

Şimdilik yalnızca şu farkı aklımızda tutalım:

```text
Basit Node.js programı
↓
İşini yapar ve sona erebilir.

Web sunucusu
↓
Yeni Request'leri beklemek için
çalışmaya devam eder.
```

## Örnek 27

## Sık Karşılaşılan İki Sorun

### node Komutu Bulunamıyor

Şu komut:

```bash
node -v
```

## Örnek 28

çalışmıyorsa

önce Node.js kurulumunu kontrol etmeliyiz.

### index.js Bulunamıyor

Şu komut:

```bash
node index.js
```

## Örnek 29

dosya bulunamadığına dair bir hata veriyorsa

terminalin doğru klasörde olup olmadığını kontrol etmeliyiz.

Basit kontrol sıramız şöyle olabilir:

```text
Node.js çalışıyor mu?
↓
node -v

Doğru klasörde miyim?
↓
index.js burada mı?

Sonra
↓
node index.js
```

## Örnek 30

🧠 Akılda Kalsın

```text
node -v
↓
Node.js komutunun kullanılabildiğini kontrol eder.

node index.js
↓
index.js dosyasını Node.js ile çalıştırır.

console.log()
↓
Çıktıyı terminalde görmemizi sağlar.
```

## Örnek 31

## Bölüm Özeti

Bu bölümde ilk JavaScript dosyamızı

Node.js ile çalıştırdık.

Önce:

```bash
node -v
```

## Örnek 32

ile Node.js'in kullanılabilir olduğunu kontrol ettik.

Ardından:

```text
node-basics/
└── index.js
```

## Örnek 33

yapısını oluşturduk.

Dosyamızı:

```bash
node index.js
```

## Örnek 34

komutuyla çalıştırdık.

Böylece artık şu akışı uygulayabiliyoruz:

```text
JavaScript dosyası
↓
Node.js
↓
Kod çalışır.
↓
Sonuç terminalde görünür.
```

## Örnek 35

## Sonraki Adım

Artık kendi yazdığımız

bir JavaScript dosyasını

Node.js ile çalıştırabiliyoruz.

```text
index.js
↓
Node.js
↓
Program çalışır.
```

## Örnek 36

Fakat gerçek bir backend projesinde

ihtiyacımız olan her şeyi

kendimiz yazmak zorunda değiliz.

Başka geliştiricilerin hazırladığı

hazır JavaScript paketlerinden de

yararlanabiliriz.

Bu noktada yeni sorular ortaya çıkar.

Bir paketi projemize

nasıl ekleyeceğiz?

Projemizin hangi paketleri kullandığını

nasıl takip edeceğiz?

Ve proje hakkındaki bilgiler

nerede tutulacak?

Bu sorular bizi

Node.js ekosisteminin önemli bir parçasına götürür:

```text
npm
```
