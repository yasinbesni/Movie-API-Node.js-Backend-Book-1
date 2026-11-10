# Bölüm 8 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# JavaScript Sunucu Tarafında Nasıl Çalışır?

## JavaScript'i Sunucu Tarafında Ne Çalıştıracak?

Backend kodlarımızı

JavaScript ile yazacağımızı artık biliyoruz.

Fakat burada önemli bir soru ortaya çıkıyor.

Yazdığımız JavaScript kodlarını

sunucu tarafında ne çalıştıracak?

Bir JavaScript dosyasını

bir web sayfasına bağlamadan

nasıl çalıştırabiliriz?

```text
JavaScript kodu
↓
?
↓
Sunucu tarafında çalışır.
```

## Örnek 2

Eksik olan parça,

JavaScript kodlarımızın çalışabileceği bir ortamdır.

İşte burada:

```text
Node.js
```

## Örnek 3

ile tanışırız.

## Node.js Nedir?

Node.js,

JavaScript kodlarını

tarayıcının dışında çalıştırmamızı sağlayan

bir **çalışma ortamıdır**.

Burada iki farklı şeyi birbirinden ayırmalıyız:

```text
JavaScript
↓
Yazdığımız programlama dili

Node.js
↓
JavaScript kodlarının çalışabileceği ortam
```

## Örnek 4

Yani Node.js,

yeni bir programlama dili değildir.

Biz yine JavaScript yazacağız.

Node.js ise

yazdığımız JavaScript kodlarını

tarayıcıya ihtiyaç duymadan

çalıştırabilmemizi sağlayacaktır.

```text
JavaScript dosyası
↓
Node.js
↓
Kod çalışır.
```

## Örnek 5

🧠 Akılda Kalsın

```text
JavaScript
↓
Kodumuzu yazdığımız dil

Node.js
↓
Bu kodun tarayıcı dışında çalışabileceği ortam
```

## Örnek 6

## Neden Böyle Bir Ortama İhtiyacımız Var?

Bir backend uygulamasını düşünelim.

Uygulamamız ilerleyen bölümlerde:

```text
Request karşılayacak,

verileri işleyecek,

dosyalarla çalışacak,

veritabanına ulaşacak,

Response hazırlayacak.
```

## Örnek 7

Bütün bu işlemleri gerçekleştirecek

JavaScript kodlarının

bir yerde çalışması gerekir.

Tarayıcı,

web sayfalarını görüntüler

ve tarayıcı ortamındaki

JavaScript kodlarını çalıştırabilir.

Bizim backend tarafındaki ihtiyacımız ise farklıdır.

```text
Kullanıcı
↓
Request
↓
Sunucu tarafındaki uygulama
↓
İşlem
↓
Response
```

## Örnek 8

Bu uygulamadaki JavaScript kodlarını

çalıştıracak ortama ihtiyacımız vardır.

Node.js,

bu ortamı sağlar.

```text
Request
↓
Node.js üzerinde çalışan JavaScript uygulaması
↓
İşlem
↓
Response
```

## Örnek 9

Şimdilik burada

Request'i nasıl karşılayacağımızı

ve Response'u nasıl göndereceğimizi bilmiyoruz.

Bunları ilerleyen bölümlerde öğreneceğiz.

Bu bölümde bilmemiz gereken temel nokta şudur:

> Backend kodlarımız JavaScript ile yazılacaksa,
> bu JavaScript kodlarının sunucu tarafında çalışabileceği bir ortama ihtiyacımız vardır.

Bu ortam:

```text
Node.js
```

## Örnek 10

olacaktır.

## Tarayıcı ile Node.js Aynı Ortam mı?

Hayır.

Her iki ortamda da

JavaScript yazabiliriz.

Fakat JavaScript'in çalıştığı yer değiştiğinde

ulaşabildiği imkânlar da değişebilir.

Bir web sayfasında çalışan JavaScript,

tarayıcının sunduğu yapılardan yararlanabilir.

Örneğin:

```js
document
```

## Örnek 11

ve:

```js
window
```

## Örnek 12

tarayıcı ortamıyla ilişkilidir.

Node.js tarafında ise

bir web sayfası çalıştırmıyoruz.

Bu nedenle tarayıcıya ait yapıları

aynı şekilde kullanamayız.

Node.js bize

sunucu tarafında kullanabileceğimiz

başka imkânlar sunar.

İlerleyen bölümlerde örneğin:

```text
dosyalarla çalışabilecek,

ortam değişkenlerini okuyabilecek,

ağ işlemleri yapabilecek

ve backend uygulamaları çalıştırabileceğiz.
```

## Örnek 13

Burada bunları ezberlememize gerek yok.

Asıl fikir daha basittir:

```text
Programlama dili
↓
JavaScript

Çalıştığı ortam
↓
Tarayıcı veya Node.js
```

## Örnek 14

Dil aynı olabilir.

Fakat çalışma ortamı değiştiğinde

kullanabileceğimiz özellikler de değişebilir.

![Tarayıcı ve Node.js çalışma ortamlarının karşılaştırılması](images/08-node-ortam-karsilastirma-baski.svg)

## Node.js Olmadan Ne Eksik Kalırdı?

Şu anda elimizde

JavaScript ile yazmak istediğimiz

bir backend uygulaması fikri var.

Fakat yalnızca JavaScript kodunu yazmak

onu kendiliğinden çalıştırmaz.

```text
JavaScript kodu
↓
Çalıştıracak bir ortam gerekir.
```

## Örnek 15

Sunucu tarafında bu görevi

Node.js üstlenecektir.

Bu nedenle temel ilişkimizi

şöyle kurabiliriz:

```text
JavaScript
↓
Node.js üzerinde çalışır.
↓
Sunucu tarafında uygulama geliştirilebilir.
```

## Örnek 16

Node.js'in bizim için

neden gerekli olduğunu artık biliyoruz.

Fakat henüz onu kullanarak

tek bir JavaScript dosyası bile çalıştırmadık.

## Bölüm Özeti

Bu bölümde

backend kodlarımızı JavaScript ile yazarken

neden Node.js'e ihtiyaç duyduğumuzu öğrendik.

```text
JavaScript
↓
Programlama dili

Node.js
↓
JavaScript kodlarını tarayıcı dışında
çalıştırabileceğimiz ortam
```

## Örnek 17

Node.js'in

JavaScript'in yerine geçmediğini gördük.

Yazdığımız dil değişmez:

```text
JavaScript
```

## Örnek 18

Değişen,

kodun çalıştığı ortamdır.

Temel bağlantımız artık şöyledir:

```text
JavaScript kodu
↓
Node.js
↓
Sunucu tarafında çalışır.
```

## Örnek 19

## Sonraki Adım

Node.js'in ne işe yaradığını biliyoruz.

Şimdi bunu gerçekten görelim.

Bir sonraki bölümde

önce Node.js'in bilgisayarımızda

kullanılabilir olup olmadığını kontrol edeceğiz.

Ardından ilk JavaScript dosyamızı oluşturacak

ve onu terminal üzerinden

Node.js ile çalıştıracağız.

Böylece:

```text
JavaScript dosyası
↓
Node.js
↓
Çalışan program
```
