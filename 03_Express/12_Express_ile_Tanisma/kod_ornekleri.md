# Bölüm 12 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Express ile Tanışma

## Node.js Varken Express'e Neden İhtiyacımız Var?

Artık JavaScript kodlarımızı

Node.js ile sunucu tarafında çalıştırabiliyoruz.

Ayrıca:

```text
npm

package.json

import / export
```

## Örnek 2

gibi temel araçları da tanıyoruz.

Fakat şimdi yeniden

web uygulamamızın asıl akışına dönelim.

Bir istemci Request gönderdiğinde

backend uygulamamızın bu Request'i karşılaması,

ne istendiğini anlaması

ve uygun bir Response hazırlaması gerekir.

```text
İstemci
↓
Request
↓
Backend uygulaması
↓
Response
↓
İstemci
```

## Örnek 3

Node.js,

JavaScript kodlarımızın çalışabileceği ortamı sağlar.

Fakat gerçek bir web uygulaması geliştirirken

Request ve Response akışını

daha düzenli ve daha kolay yönetmek isteriz.

İşte burada yeni bir araçla tanışırız:

```text
Express
```

## Örnek 4

## Express Nedir?

Express,

Node.js üzerinde çalışan

bir **web framework'üdür**.

Başlangıç düzeyinde bunu şöyle düşünebiliriz:

> Express,
> Node.js ile web uygulaması geliştirirken
> sık karşılaştığımız işleri
> daha kolay yönetmemizi sağlayan bir yapıdır.

Node.js'in yerine geçmez.

JavaScript'in yerine de geçmez.

Aralarındaki ilişki şöyledir:

```text
JavaScript
↓
Yazdığımız programlama dili

Node.js
↓
JavaScript kodlarının çalıştığı ortam

Express
↓
Node.js üzerinde web uygulaması
geliştirmeyi kolaylaştıran framework
```

## Örnek 5

🧠 Akılda Kalsın

```text
JavaScript
↓
Dili yazıyoruz.

Node.js
↓
Kodu çalıştırıyor.

Express
↓
Web uygulamasını geliştirmeyi kolaylaştırıyor.
```

## Örnek 6

## Express Hangi Sorunu Çözüyor?

Bir web uygulamasında

sunucuya farklı Request'ler gelebilir.

Örneğin:

```text
GET /movies

POST /movies

GET /users
```

## Örnek 7

Bu Request'lerin her biri

farklı bir işlem isteyebilir.

Backend uygulamamızın:

```text
hangi Request'in geldiğini,

hangi yolun istendiğini,

hangi işlemin yapılacağını,

ve hangi Response'un gönderileceğini
```

## Örnek 8

yönetmesi gerekir.

Bütün bunları

Node.js'in sunduğu temel araçlarla da yapabiliriz.

Ancak uygulama büyüdükçe

bu işlemleri düzenli biçimde yönetmek

daha fazla kod ve dikkat gerektirebilir.

Express,

bu işleri daha anlaşılır bir yapı içinde

organize etmemizi kolaylaştırır.

## Express'in Görevi Nedir?

Bir sinemaya girdiğinizi düşünün.

Kapıda bir görevli vardır.

Gelen kişiyi karşılar,

hangi salona gitmesi gerektiğini belirler

ve onu doğru yere yönlendirir.

Express'i de başlangıçta

buna benzer şekilde düşünebiliriz.

```text
Request gelir.
↓
Express uygulaması Request'i karşılar.
↓
İstenen yol ve işlem belirlenir.
↓
Uygun kod çalışır.
↓
Response gönderilir.
```

## Örnek 9

Bir Request:

```text
GET /movies
```

## Örnek 10

şeklinde geldiyse

uygulamamız film listesini gönderebilir.

Başka bir Request:

```text
POST /movies
```

## Örnek 11

şeklinde geldiyse

uygulamamız yeni bir film eklemek için

farklı bir işlem çalıştırabilir.

Bu işlemlerin nasıl tanımlandığını

ilerleyen bölümlerde adım adım göreceğiz.

Şimdilik önemli olan,

Express'in Request ve Response akışını

yönetmeyi kolaylaştırdığını anlamaktır.

## Express Bize Neleri Kolaylaştıracak?

İlerleyen bölümlerde Express ile:

```text
farklı URL yolları oluşturacağız,

GET ve POST gibi HTTP yöntemlerini yöneteceğiz,

Request içindeki verilere ulaşacağız,

Response göndereceğiz,

ve Request akışına
ara işlemler ekleyeceğiz.
```

## Örnek 12

Bu kavramların bazılarını

Web Foundations bölümünde

temel düzeyde tanıdık.

Express bölümünde ise

artık onları gerçek kodların içinde kullanacağız.

Yani daha önce öğrendiğimiz:

```text
Request
Response
HTTP Method
Path
Status Code
```

## Örnek 13

gibi kavramlar

şimdi uygulama koduna dönüşmeye başlayacak.

## Node.js ve Express'in Rolleri

Node.js ve Express aynı katmanda çalışan iki alternatif değildir.

```text
Node.js
↓
JavaScript'in sunucu tarafında çalıştığı ortam

Express
↓
Node.js üzerinde web uygulaması ve API geliştirmeyi kolaylaştıran framework
```

## Örnek 14

Movie API'nin Express uygulaması Node.js üzerinde çalışır. Node.js çalışma ortamını sağlar; Express ise route, middleware ve HTTP akışını daha düzenli kurmamıza yardımcı olur.

## Express'i Şu Anda Kullanabilir miyiz?

Henüz değil.

Express'in ne işe yaradığını biliyoruz,

fakat onu projemize daha eklemedik.

Bir önceki bölümde

npm paketlerinin nasıl kullanıldığını öğrenmiştik.

Express de

npm aracılığıyla projemize ekleyeceğimiz

gerçek bir pakettir.

Yani sıradaki adımımız:

```text
Express'i tanıdık.
↓
Projeyi hazırlayacağız.
↓
Express paketini kuracağız.
↓
Sonra kodumuzda kullanacağız.
```

## Örnek 15

olacak.

Bu sıralama önemlidir.

Önce paketi projemize ekleyeceğiz.

Ardından ilk Express kodumuzu yazacağız.

## Bölüm Özeti

Bu bölümde

Node.js ile Express arasındaki farkı gördük.

```text
Node.js
↓
JavaScript kodlarını çalıştıran ortam

Express
↓
Node.js üzerinde web uygulaması
geliştirmeyi kolaylaştıran framework
```

## Örnek 16

Express'in özellikle:

```text
Request'leri yönetmek,

farklı yolları ayırmak,

HTTP yöntemleriyle çalışmak,

ve Response göndermek
```

## Örnek 17

gibi web uygulaması görevlerini

daha düzenli biçimde gerçekleştirmemize

yardım edeceğini öğrendik.

Henüz Express kodu yazmadık.

Çünkü önce Express paketini

projemize eklememiz gerekiyor.

## Sonraki Adım

Artık Express'in

neden gerekli olduğunu biliyoruz.

Şimdi onu gerçekten

projemizin bir parçası hâline getireceğiz.

Bir sonraki bölümde

Node.js bölümlerinde hazırladığımız:

```text
movie-api
```

## Örnek 18

projesini kullanmaya devam edeceğiz.

Yeni bir proje oluşturmak yerine

mevcut projemize:

```bash
npm install express
```

## Örnek 19

komutuyla Express'i ekleyeceğiz.

Ardından kurulum sonucunda:

```text
dependencies
node_modules/
package-lock.json
```
