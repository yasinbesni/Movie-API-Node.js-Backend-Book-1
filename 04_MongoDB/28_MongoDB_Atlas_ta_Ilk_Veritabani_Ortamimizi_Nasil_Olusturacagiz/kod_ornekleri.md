# Bölüm 28 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# MongoDB Atlas'ta İlk Veritabanı Ortamımızı Nasıl Oluşturacağız?

## Artık Gerçek Bir MongoDB Ortamı Oluşturacağız

Bir önceki bölümde

öğrenme rotamızı netleştirdik:

```text
MongoDB Atlas
↓
Free / M0 öğrenme ortamı
```

## Örnek 2

Artık:

```text
Local mi?
Atlas mı?
```

## Örnek 3

sorusunu yeniden tartışmayacağız.

Bu bölümün görevi,

verdiğimiz kararı

çalışan bir MongoDB ortamına

dönüştürmek.

Şimdi tarayıcınızı açtığınızı

ve Atlas'a girdiğinizi düşünün.

Karşınızda henüz:

```text
bizim cluster'ımız yok,

bizim database'imiz yok,

bizim collection'ımız yok.
```

## Örnek 4

Yani uygulamamızın kullanacağı

MongoDB ortamı henüz hazır değil.

Bu bölümde adım adım:

```text
Atlas hesabı
↓
Project
↓
Free / M0 Cluster
↓
Database User
↓
IP Access List
```

## Örnek 5

yapısını kuracağız.

Ama yalnızca:

```text
buraya tıkla,

şunu seç,

buna bas
```

## Örnek 6

şeklinde ilerlemeyeceğiz.

Her yeni adımda:

> Bu parça neden gerekli?

sorusunun cevabını da

kısa biçimde yerine oturtacağız.

Bir önceki bölümde açıkladığımız

kavramları ise

gereksiz yere baştan anlatmayacağız.

---

## Önce Atlas'a Girmemiz Gerekiyor

MongoDB Atlas

internette çalışan

bir hizmettir.

Bu yüzden önce

Atlas'a giriş yapabileceğimiz

bir hesaba ihtiyacımız var.

Kayıt ekranında:

```text
Google

GitHub

veya

e-posta
```

## Örnek 7

gibi seçeneklerle

karşılaşabilirsiniz.

Burada oluşturduğumuz şey:

```text
Atlas hesabı
```

## Örnek 8

dır.

Bu hesapla:

```text
Atlas web sitesine gireriz.
```

## Örnek 9

Şimdilik bundan fazlasını

düşünmemize gerek yok.

Ama birazdan

başka bir kullanıcı daha

oluşturacağız:

```text
Database User
```

## Örnek 10

Bu ikisini

şimdiden ayıralım:

```text
Atlas hesabı
↓
Atlas web arayüzüne girmek için

Database User
↓
MongoDB'ye bağlanmak için
```

## Örnek 11

Yani:

```text
Atlas hesabı
≠
Database User
```

## Örnek 12

Bu ayrım birazdan

çok daha anlamlı hâle gelecek.

---

## Hesaba Girdik, Peki MongoDB Nerede?

Atlas'a giriş yaptınız.

Ama henüz:

```text
movies
```

## Örnek 13

collection'ınız yok.

Hatta:

```text
movieApp
```

## Örnek 14

adında bir database'iniz de yok.

Çünkü önce

MongoDB'nin çalışacağı

ortamı hazırlamamız gerekiyor.

Atlas'ta bunu yaparken

karşımıza:

```text
Project
```

## Örnek 15

ve:

```text
Cluster
```

## Örnek 16

kelimeleri çıkacak.

Şimdi yalnızca

bu iki kavrama odaklanalım.

---

## Project Nedir?

Bir süre sonra

Atlas hesabınızda

birden fazla uygulamanız

olduğunu düşünün:

```text
Film Platformu

Görev Yönetimi Uygulaması

Finans Uygulaması
```

## Örnek 17

Her uygulamanın

MongoDB ayarlarını

birbirine karıştırmak istemezsiniz.

İşte:

```text
Project
```

## Örnek 18

bize bunun için

bir çalışma alanı sağlar.

Şimdilik şöyle düşünün:

```text
Project
↓
Bir uygulamaya ait
MongoDB kaynaklarını
bir arada tuttuğumuz alan
```

## Örnek 19

Örneğin film platformumuz için:

```text
MovieApp
```

## Örnek 20

adında bir project

oluşturabiliriz.

Atlas ilk girişte

sizin için zaten

bir project oluşturmuş olabilir.

Böyle bir durumda

yenisini oluşturmak zorunda değilsiniz.

Bizim için önemli olan:

```text
Şu anda hangi project'in içindeyiz?
```

## Örnek 21

sorusunun cevabını bilmek.

Çünkü birazdan

oluşturacağımız MongoDB ortamı

bu project'in içinde olacak.

---

## Ekranda “Organization” Görürsem Ne Olacak?

Atlas'ta bazen:

```text
Organization
```

## Örnek 22

kelimesini de görebilirsiniz.

Bu kelime

şimdilik sizi durdurmasın.

Basitçe:

```text
Organization
↓
Project'lerin bulunduğu üst alan
```

## Örnek 23

olarak düşünebilirsiniz.

Tek başına öğrenen biri için

şu anda asıl önemli yapı:

```text
Project
```

## Örnek 24

tir.

Organization konusuna

ekip ve yetkilendirme

ihtiyacı doğduğunda

daha ayrıntılı döneceğiz.

---

## Şimdi MongoDB'nin Çalışacağı Ortamı Oluşturalım

Project hazır.

Şimdi sıra,

MongoDB'nin gerçekten çalışacağı

ortamı oluşturmaya geldi.

Bir önceki bölümde

bu ortam için:

```text
cluster
```

## Örnek 25

kelimesini kullanmıştık.

Tanımı yeniden uzatmadan

doğrudan uygulayalım:

```text
Project
↓
Cluster
```

## Örnek 26

Birazdan oluşturacağımız

database'ler de

bu cluster'ın içinde yer alacak.

Yani artık kavramı konuşmuyoruz;

Atlas ekranında

gerçek cluster'ımızı oluşturuyoruz.

---

## Hangi Cluster'ı Seçeceğiz?

Bu seçimi de

bir önceki bölümde yaptık.

Öğrenme ortamımız için:

```text
Free
```

## Örnek 27

seçeneğiyle devam edeceğiz.

Atlas arayüzünde

aynı ücretsiz katmanı:

```text
M0
```

## Örnek 28

adıyla da görebilirsiniz.

Yani ekranda:

```text
Free
```

## Örnek 29

veya:

```text
M0
```

## Örnek 30

ifadesini gördüğünüzde

ücretli seçenekleri yeniden

karşılaştırmamız gerekmiyor.

Bu bölümde amacımız

seçenekleri tartışmak değil,

seçtiğimiz öğrenme ortamını

oluşturmaktır.

---

## Bulut Sağlayıcısını Seçelim

Free / M0 seçeneğini

seçtiğinizde

Atlas sizden bir

bulut sağlayıcısı seçmenizi isteyebilir:

```text
AWS

Google Cloud

Azure
```

## Örnek 31

Bu isimlerin ne anlama geldiğini

bir önceki bölümde gördük.

Burada yeniden teoriye dönmeyeceğiz.

Yapacağımız şey basit:

```text
Free / M0'ın desteklediği
uygun sağlayıcılardan birini seçmek
```

## Örnek 32

Sağlayıcı seçimi:

```text
Database
Collection
Document
```

## Örnek 33

gibi temel MongoDB kavramlarını

değiştirmez.

Yani bu adımda

amacımız yeni bir MongoDB türü seçmek değil,

cluster'ımızın hangi bulut altyapısında

çalışacağını belirlemektir.

---

## Region Ne Demek?

Bulut sağlayıcısını

seçtikten sonra

şu kelimeyle

karşılaşabilirsiniz:

```text
Region
```

## Örnek 34

Türkçede bunu:

```text
bölge
```

## Örnek 35

olarak düşünebiliriz.

Bu bölüm,

MongoDB ortamımızın

hangi coğrafi bölgede

çalışacağını belirtir.

Örneğin:

```text
Avrupa

Amerika

Asya
```

## Örnek 36

gibi farklı bölgeler

görebilirsiniz.

Neden önemli?

Çünkü backend ile MongoDB

birbirine ne kadar yakınsa

ağ üzerinden iletişim de

genellikle o kadar avantajlı olur.

Ama şu anda

performans planlaması

yapmıyoruz.

İlk öğrenme ortamımızı

oluşturuyoruz.

Bu yüzden:

```text
Free seçeneğinin desteklediği
uygun bir region
```

## Örnek 37

seçmemiz yeterli.

Region konusu

ileride gerçekten ihtiyaç olduğunda

daha ayrıntılı ele alınacak.

---

## Cluster'ımıza Bir İsim Verelim

Atlas sizden

cluster için

bir isim isteyebilir.

Biz kitap boyunca:

```text
BookCluster
```

## Örnek 38

adını kullanalım.

Böylece:

```text
Project
↓
BookCluster
```

## Örnek 39

şeklinde

kolay takip edebileceğimiz

bir yapı oluşur.

Buradaki amaç

mükemmel bir isim bulmak değil.

Daha sonra gördüğümüzde:

```text
Bu hangi cluster'dı?
```

## Örnek 40

diye düşünmemek.

Anlamlı bir isim

bu yüzden işimizi kolaylaştırır.

---

## Create Düğmesine Bastığımızda Ne Oluyor?

Artık temel seçimlerimiz hazır:

```text
Free / M0

Bulut sağlayıcısı

Region

Cluster adı
```

## Örnek 41

Ve:

```text
Create
```

## Örnek 42

düğmesine basıyoruz.

Atlas şimdi

arka tarafta bizim için

MongoDB'nin çalışacağı

ortamı hazırlıyor.

Biraz önce:

```text
Project
```

## Örnek 43

vardı.

Şimdi:

```text
Project
↓
BookCluster
```

## Örnek 44

var.

Yani artık

Atlas üzerinde çalışan

bir MongoDB ortamımız bulunuyor.

Ama henüz

ona bağlanamayız.

Neden?

Çünkü Atlas'ın

iki önemli sorusu var:

```text
Kim bağlanacak?

Nereden bağlanacak?
```

## Örnek 45

İşte şimdi

güvenlik adımlarına geçiyoruz.

---

## Database User Oluşturalım

Cluster'ımız hazır.

Şimdi backend'imizin

MongoDB'ye bağlanmak istediğini düşünün.

Backend:

```text
BookCluster'a bağlanmak istiyorum.
```

## Örnek 46

diyor.

Peki Atlas

karşısına çıkan her uygulamaya

hemen izin verir mi?

Hayır.

Önce şunu bilmek ister:

> Bağlanmaya çalışan kim?

İşte:

```text
Database User
```

## Örnek 47

bu sorunun cevabıdır.

Database User'ı şimdilik:

> Backend'in MongoDB'ye
> bağlanırken kullandığı kimlik.

olarak düşünebilirsiniz.

Yani backend,

Atlas'a bağlanırken

yalnızca cluster'ın adresini bilmez.

Kendisini tanıtmak için

iki bilgi de gönderir:

```text
Kullanıcı adı

Parola
```

## Örnek 48

Akışı önce bir bütün olarak görelim:

<img src="./images/28_database_user_kimlik_akisi.png" alt="Database User ile kimlik doğrulama akışı" width="600">

Backend'in gönderdiği

kullanıcı adı ve parola

Atlas tarafından kontrol edilir.

Bilgiler doğruysa:

```text
Kimlik doğrulandı.
↓
Bağlantı devam edebilir.
```

## Örnek 49

Bilgiler yanlışsa:

```text
Kimlik doğrulanamadı.
↓
Bağlantı reddedilir.
```

## Örnek 50

Şimdi kendi Database User'ımızı

oluşturalım.

Atlas'ın güvenlik ekranında

şuna benzer bir soru görebilirsiniz:

```text
How would you like to authenticate your connection?
```

## Örnek 51

Yani:

> Bağlantı sırasında
> kimliğini nasıl doğrulamak istiyorsun?

Biz bu kitapta:

```text
Username and Password
```

## Örnek 52

yani:

```text
Kullanıcı adı ve parola
```

## Örnek 53

yöntemiyle devam edeceğiz.

Bu seçeneği seçtiğimizde

Atlas bizden

bir kullanıcı adı

ve parola ister.

Örneğin kullanıcı adımız:

```text
book_user
```

## Örnek 54

olsun.

Parolamız ise:

```text
güçlü

tahmin edilmesi zor

başka yerde tekrar kullanılmayan
```

## Örnek 55

bir parola olmalıdır.

Gerçek parolamızı

kitapta göstermeyeceğiz.

Örneklerde onu:

```text
********
```

## Örnek 56

şeklinde yazacağız.

Artık bağlantı sırasında

backend'in Atlas'a

ne göndereceğini biliyoruz:

```text
Backend
↓
book_user
+
********
↓
MongoDB Atlas
```

## Örnek 57

Yani:

```text
Kullanıcı adı (Database Username)
+
Parola (Database Password)
```

## Örnek 58

backend'in MongoDB'ye:

> Ben bağlanmasına izin verdiğin
> kullanıcıyım.

diyebilmesini sağlar.

Burada daha önce gördüğümüz

ayrımı da yeniden yerine koyalım:

```text
Atlas hesabı
↓
Atlas web sitesine girmek için

Database User
↓
Backend'in MongoDB'ye bağlanması için
```

## Örnek 59

Bunlar:

```text
aynı kullanıcı değildir.
```

## Örnek 60

Kullanıcı adını

ve parolayı saklayın.

Bilgileri girdikten sonra

arayüzde:

```text
Create User
```

## Örnek 61

veya aynı görevi yapan

benzer bir düğme görebilirsiniz.

Bu düğmeye bastığımızda

Database User gerçekten oluşturulur.

Quickstart ekranı

yetkiler için ayrıca bir seçim

göstermeyebilir.

Farklı bir Atlas ekranında

```text
Privileges
```

## Örnek 62

veya yetkilerle ilgili

bir alan görürseniz

şimdilik varsayılan

okuma-yazma yetkisiyle

devam etmemiz yeterlidir.

Yetkilendirme konusunu

ihtiyaç doğduğunda

ayrıntılı ele alacağız.

Bir sonraki bölümde

backend'imizi Atlas'a bağlarken

bu bilgileri yeniden kullanacağız.

---

## Parolayı JavaScript Dosyasına mı Yazacağız?

Bu noktada

aklınıza şu soru gelebilir:

> Backend bağlanacaksa
> parolayı kodun içine mi yazacağız?

Hayır.

Doğrudan:

```text
app.js

server.js

index.js
```

## Örnek 63

gibi dosyaların içine

parolamızı yazmayacağız.

İleride:

```text
.env
```

## Örnek 64

dosyası

ve:

```text
ortam değişkenleri
```

## Örnek 65

üzerinden

bu tür gizli bilgileri

nasıl saklayacağımızı

adım adım göreceğiz.

Şimdilik aklınızda

tek bir kural kalsın:

```text
MongoDB parolası
↓
Kaynak kodun içine açıkça yazılmaz.
```

## Örnek 66

Ve:

```text
GitHub'a gönderilmez.
```

## Örnek 67

Bu konuya

bağlantıyı kurarken

yeniden döneceğiz.

---

## Database User Hazır. Artık Bağlanabilir miyiz?

Henüz değil.

Çünkü Atlas

yalnızca:

```text
Kim bağlanıyor?
```

## Örnek 68

sorusunu sormaz.

Bir soru daha sorar:

```text
Nereden bağlanıyor?
```

## Örnek 69

Backend veya bilgisayarımız

hangi ağdan geliyor?

Atlas bu ağdan gelen

bağlantıya izin vermeli mi?

Bu sorunun cevabı:

```text
IP Access List
```

## Örnek 70

içinde tutulur.

---

## IP Access List Nedir?

Adı ilk bakışta

biraz teknik görünebilir.

Ama fikir oldukça basit.

```text
IP Access List
↓
Bağlantı kurmasına izin verilen
ağ adreslerinin listesi
```

## Örnek 71

Yani Atlas iki şeyi

kontrol eder:

```text
Database User
↓
Kimliğin doğru mu?

IP Access List
↓
Geldiğin ağ adresine izin var mı?
```

## Örnek 72

Bir benzetmeyle:

```text
Database User
↓
Kimlik kartı

IP Access List
↓
Kapıdaki izin listesi
```

## Örnek 73

Kimliğiniz doğru olabilir.

Ama geldiğiniz yer

izinli değilse

kapı yine açılmaz.

---

## Kendi IP Adresimizi Ekleyelim

Biz şu anda

kendi bilgisayarımızdan

Atlas'a bağlanacağız.

Quickstart ekranında

bağlantının nereden geleceğini

soran bir bölüm görürseniz:

```text
My Local Environment
```

## Örnek 74

yani:

```text
Kendi yerel ortamım
```

## Örnek 75

seçeneği bizim senaryomuza uygundur.

Çünkü bağlantıyı

şu anda kendi bilgisayarımızdan

kuracağız.

Bu nedenle

mevcut internet bağlantımızın

IP adresini

izin verilenler listesine

ekleyebiliriz.

Atlas arayüzünde:

```text
Add My Current IP Address
```

## Örnek 76

gibi bir seçenek

görebilirsiniz.

Bunun sade anlamı:

```text
Şu anda kullandığım
internet bağlantısına izin ver.
```

## Örnek 77

dir.

Bu işlemi yaptığımızda:

```text
IP Access List
↓
Mevcut IP adresimiz
```

## Örnek 78

şeklinde bir kayıt

oluşur.

Atlas arayüzü daha sonra:

```text
Finish and Close

veya

Go to Database
```

## Örnek 79

gibi ilerleme seçenekleri

gösterebilir.

Düğmenin adı değişse bile

burada kontrol edeceğimiz şey şudur:

```text
Database User hazır mı?
↓
Evet

IP adresimiz listede mi?
↓
Evet
```

## Örnek 80

Bu iki cevap da:

```text
Evet
```

## Örnek 81

ise bağlantı güvenliği için

temel hazırlığımız tamamdır.

---

## `0.0.0.0/0` Ne Demek?

Atlas'ta IP Access List

ayarlarını yaparken

şuna benzeyen bir seçenekle

karşılaşabilirsiniz:

```text
0.0.0.0/0
```

## Örnek 82

İlk kez gören biri için

bu ifade oldukça anlamsız

görünebilir.

Şimdilik:

```text
CIDR
```

## Örnek 83

hesaplarına girmeyeceğiz.

Bilmeniz gereken şey şu:

```text
0.0.0.0/0
↓
Tüm IPv4 adreslerinden
bağlantı denemesine izin ver
```

## Örnek 84

Yani yalnızca:

```text
benim şu anki IP adresim
```

## Örnek 85

değil,

internetteki herhangi bir IPv4 adresi

Atlas'taki cluster'ınıza

bağlantı kurmayı deneyebilir.

Burada önemli bir ayrım var.

```text
0.0.0.0/0
```

## Örnek 86

eklemek:

```text
Herkes veritabanına
doğrudan girebilir.
```

## Örnek 87

demek değildir.

Atlas yine:

```text
Database User

kullanıcı adı

parola
```

## Örnek 88

gibi kimlik bilgilerini

kontrol eder.

Ama ağ kapısını

çok daha geniş bir alana

açmış olursunuz.

Şöyle düşünün:

```text
Sadece kendi IP adresimiz
↓
Kapıya yalnızca belirli bir sokaktan
gelinmesine izin vermek

0.0.0.0/0
↓
Kapıya dünyanın her yerinden
gelinmesine izin vermek
```

## Örnek 89

Kapının üzerinde hâlâ

kilit vardır.

Ama kapıya ulaşabilecek

kişi sayısı çok daha fazladır.

Bu yüzden öğrenme aşamasında

mümkün olduğunca:

```text
Add My Current IP Address
```

## Örnek 90

seçeneğiyle

yalnızca kendi mevcut IP adresimize

izin vermeyi tercih edeceğiz.

MongoDB de güvenlik açısından

mümkün olan en dar IP aralığının

kullanılmasını önerir.

İleride backend'imizi

uzak bir sunucuya taşıdığımızda

IP Access List'i

o ortama göre

yeniden düzenleyeceğiz.

Şimdilik aklınızda

şu ayrım kalsın:

```text
Kendi IP adresim
↓
Daha sınırlı erişim

0.0.0.0/0
↓
Her IPv4 adresinden
bağlantı denemesine açık
```

## Örnek 91

---

## IP Adresimiz Değişirse Ne Olur?

Burada ileride

çok işimize yarayacak

bir bilgi var.

Ev internetinizin

IP adresi

her zaman aynı kalmayabilir.

Örneğin:

```text
Ev Wi-Fi
↓
IP A

Telefon Hotspot
↓
IP B
```

## Örnek 92

Atlas'ta yalnızca:

```text
IP A
```

## Örnek 93

izinliyse

telefon hotspot'una geçtiğinizde

bağlantı kurulmayabilir.

Bu durumda

ilk düşünceniz:

> Kodum bozuldu.

olabilir.

Ama sorun kodda değil,

şurada olabilir:

```text
IP Access List
```

## Örnek 94

Bu yüzden şu cümleyi

aklınızda tutun:

```text
MongoDB bağlantı hatası
↓
Her zaman kod hatası değildir.
```

## Örnek 95

Bazen sorun:

```text
ağ erişim izni
```

## Örnek 96

olabilir.

Bu konuyu ileride

gerçek bir bağlantı hatası

üzerinden tekrar göreceğiz.

---

## Şu Anda Neleri Hazırladık?

Biraz önce

boş bir Atlas ekranındaydık.

Şimdi elimizde:

```text
Atlas hesabı

Project

BookCluster

Database User

IP Access List
```

## Örnek 97

var.

Bunların görevlerini

tek tek biliyoruz:

```text
Atlas hesabı
↓
Atlas'a girmemizi sağlar.

Project
↓
MongoDB kaynaklarımızı
bir arada tuttuğumuz çalışma alanı.

BookCluster
↓
MongoDB'nin çalıştığı ortam.

Database User
↓
Kim bağlanıyor?

IP Access List
↓
Nereden bağlanıyor?
```

## Örnek 98

Şimdi yapı

çok daha anlamlı görünüyor.

---

## Peki Database'imiz Nerede?

Burada önemli

bir noktayı fark edelim.

Cluster'ı oluşturduk.

Ama henüz:

```text
movieApp
```

## Örnek 99

adında bir database

oluşturmadık.

Bu bir hata değildir.

Çünkü:

```text
Cluster
```

## Örnek 100

ile:

```text
Database
```

## Örnek 101

aynı şey değildir.

Şöyle düşünün:

```text
Cluster
↓
MongoDB'nin çalıştığı ortam

Database
↓
Uygulamamızın verilerinin
bulunduğu yapı
```

## Örnek 102

Şu anda:

```text
BookCluster
```

## Örnek 103

hazır.

Ama onun içinde

bizim uygulamamıza ait:

```text
Database

Collection

Document
```

## Örnek 104

henüz yok.

Bunları sonraki adımlarda

oluşturmaya başlayacağız.

Yalnızca cluster'a bağlanmak,

tek başına bir database

veya collection oluşturmak

zorunda değildir.

---

## Şimdi Eksik Olan Tek Şey Ne?

Atlas tarafı hazır.

BookCluster çalışıyor.

Database User hazır.

IP adresimize izin verildi.

Ama backend'imiz hâlâ

şunu bilmiyor:

> BookCluster'a nasıl ulaşacağım?

İşte artık:

```text
bağlantı adresi
```

## Örnek 105

kavramına ihtiyacımız var.

Bir önceki bölümde

İngilizce dokümantasyonda bunun:

```text
connection string
```

## Örnek 106

olarak geçtiğini görmüştük.

Şimdi o adresi

gerçekten kullanacağız.

---

## Connect Düğmesine Bastığımızda Ne Göreceğiz?

Atlas tarafındaki temel hazırlığımız tamamlandı.

Artık:

```text
BookCluster hazır.

Database User hazır.

IP Access List hazır.
```

## Örnek 107

Şimdi cluster'ımızın yanında:

```text
Connect
```

## Örnek 108

düğmesine bastığımızı düşünün.

Atlas karşımıza

üç adımlı bir bağlantı süreci çıkarabilir:

```text
1. Set up connection security
↓
2. Choose a connection method
↓
3. Connect
```

## Örnek 109

Bu üç satırın anlamını

Türkçeye çevirelim:

```text
1. Bağlantı güvenliğini hazırla
↓
2. Nasıl bağlanacağını seç
↓
3. Bağlantıyı kur
```

## Örnek 110

İlk adımı zaten yaptık.

Çünkü biraz önce:

```text
Database User
```

## Örnek 111

oluşturduk

ve:

```text
IP Access List
```

## Örnek 112

içine kendi IP adresimizi ekledik.

Yani ekranın üst kısmında:

```text
Set up connection security
```

## Örnek 113

adımının tamamlandığını

görürseniz şaşırmayın.

Şimdi bulunduğumuz yer:

```text
Choose a connection method
```

## Örnek 114

yani:

> MongoDB'ye hangi yöntemle bağlanmak istiyorsun?

sorusudur.

Aşağıdaki ekran

tam olarak bu noktayı gösterir:

<img src="./images/28_connect_baglanti_yontemi.png" alt="MongoDB Atlas bağlantı yöntemi seçim ekranı" width="620">

*Görsel 28.2 — BookCluster için bağlantı yöntemi seçim ekranı.*

---

## Bu Ekranda Hangi Seçeneği Seçeceğiz?

Ekranda birden fazla seçenek görüyorsunuz:

```text
Drivers

Compass

Shell

MongoDB for VS Code

Atlas SQL
```

## Örnek 115

İlk kez gören biri için

çok doğal bir soru ortaya çıkar:

> Hangisine basacağım?

Bizim cevabımız:

```text
Drivers
```

## Örnek 116

olacak.

Neden?

Çünkü biz MongoDB'ye:

```text
Node.js + Express
```

## Örnek 117

ile yazdığımız backend üzerinden

bağlanacağız.

Ekrandaki:

```text
Drivers
```

## Örnek 118

seçeneğinin altında da

uygulamaların MongoDB'ye

MongoDB sürücüleriyle bağlanabildiği

anlatılır.

Bizim yolumuz tam olarak budur:

```text
Backend kodumuz
↓
Node.js sürücüsü
↓
MongoDB Atlas
```

## Örnek 119

Bu yüzden:

```text
Drivers
```

## Örnek 120

seçeneğine tıklıyoruz.

---

## Diğer Seçenekler Ne İçin?

Burada diğer seçenekleri

ayrıntılı öğrenmemiz gerekmiyor.

Ama ekranda karşınıza çıktıklarında

ne işe yaradıklarını

kısaca bilmek faydalı olacaktır.

```text
Compass
↓
MongoDB verilerini
görsel bir masaüstü uygulamasıyla
incelemek ve düzenlemek için
```

## Örnek 121



```text
Shell
↓
Terminal üzerinden
MongoDB ile çalışmak için
```

## Örnek 122



```text
MongoDB for VS Code
↓
VS Code içinden
MongoDB verilerine ulaşmak için
```

## Örnek 123



```text
Atlas SQL
↓
Atlas verileri üzerinde
SQL tabanlı analiz araçlarıyla
çalışmak için
```

## Örnek 124

Bunların hiçbiri:

```text
yanlış seçenek
```

## Örnek 125

değildir.

Sadece farklı amaçlara

hizmet ederler.

Bizim amacımız:

```text
Backend
↓
MongoDB Atlas
```

## Örnek 126

bağlantısını kurmak olduğu için

bu kitapta:

```text
Drivers
```

## Örnek 127

ile devam edeceğiz.

---

## Drivers'a Bastıktan Sonra Ne Olacak?

`Drivers` seçeneğine

tıkladığınızda

Atlas artık daha özel bir soru sorar:

> Hangi uygulama teknolojisiyle
> MongoDB'ye bağlanacaksın?

Bizim cevabımız:

```text
Node.js
```

## Örnek 128

olacak.

Çünkü backend'imiz:

```text
Node.js + Express
```

## Örnek 129

ile yazıldı.

Node.js'i seçtiğimizde

Atlas bize:

```text
mongodb+srv://...
```

## Örnek 130

ile başlayan

bir bağlantı adresi gösterir.

İşte tam burada

28. bölümün görevini tamamlıyoruz.

Çünkü artık:

```text
Atlas ortamı hazır.

Güvenlik ayarları hazır.

Bağlantı yöntemi seçildi.

Node.js seçildi.

Bağlantı adresi karşımızda.
```

## Örnek 131

Ama henüz bu adresi

kodumuza vermedik.

Şu ana kadar izlediğimiz yol:

```text
Connect
↓
Choose a connection method
↓
Drivers
↓
Node.js
↓
mongodb+srv://...
```

## Örnek 132

29. bölümde ise

bu adresi parçalara ayıracak,

içindeki bilgilerin

ne anlama geldiğini görecek

ve backend'imizde

güvenli biçimde kullanacağız.

Yani sınırımız net:

```text
28. bölüm
↓
Atlas'ı bağlantıya hazırla

29. bölüm
↓
Backend'i Atlas'a bağla
```

## Örnek 133

---

## Ekranda Farklı Bir Görünüm Varsa Ne Yapmalıyım?

Atlas arayüzü

zaman içinde değişebilir.

Düğmelerin yeri

veya bazı başlıkların görünümü

farklı olabilir.

Bu nedenle bu kitapta

yalnızca ekran görüntüsünü

ezberlemiyoruz.

Mantığı öğreniyoruz:

```text
Cluster'ı bul
↓
Connect'e bas
↓
Bağlantı yöntemlerini aç
↓
Drivers'ı seç
↓
Node.js'i seç
↓
Bağlantı adresinin göründüğü ekrana ulaş
```

## Örnek 134

Ekran tasarımı değişse bile

arayacağımız mantık

aynı kalacaktır.

---

## Bölüm Özeti

Bu bölüme

boş bir Atlas ortamıyla başladık.

Önce:

```text
Atlas hesabı
```

## Örnek 135

ile Atlas'a girdik.

Sonra:

```text
Project
```

## Örnek 136

kavramını öğrendik.

Project'in:

```text
MongoDB kaynaklarımızı
bir arada tuttuğumuz
çalışma alanı
```

## Örnek 137

olduğunu gördük.

Ardından:

```text
Free Cluster / M0
```

## Örnek 138

oluşturduk.

Cluster'ın:

```text
MongoDB'nin Atlas üzerinde
çalıştığı ortam
```

## Örnek 139

olduğunu öğrendik.

Bulut sağlayıcısı

ve region seçtik.

Cluster'ımıza:

```text
BookCluster
```

## Örnek 140

adını verdik.

Sonra Atlas'ın

iki güvenlik sorusunu

cevapladık:

```text
Kim bağlanıyor?
↓
Database User

Nereden bağlanıyor?
↓
IP Access List
```

## Örnek 141

Kendi IP adresimizi

izin verilenler listesine

ekledik.

Ayrıca:

```text
0.0.0.0/0
```

## Örnek 142

kuralının tüm IPv4 adreslerinden

bağlantı denemesine izin verdiğini

ve bu nedenle öğrenme ortamımızda

mümkün olduğunca daha dar

bir IP izni tercih edeceğimizi gördük.

Ardından:

```text
Connect
↓
Drivers
↓
Node.js
```

## Örnek 143

yoluyla

bağlantı adresinin

göründüğü ekrana kadar geldik.

Ayrıca:

```text
Cluster
≠
Database
```

## Örnek 144

ayrımını netleştirdik.

Yani şu anda:

```text
MongoDB ortamımız hazır.
```

## Örnek 145

Ama henüz:

```text
uygulamamızın database'i
```

## Örnek 146

oluşturulmadı.

Ve backend'imiz

Atlas'a hâlâ bağlı değil.

---

## Sonraki Adım

Şimdi iki tarafı

karşınıza koyun:

```text
Node.js + Express
```

## Örnek 147

bir tarafta.

```text
BookCluster
```

## Örnek 148

diğer tarafta.

Atlas tarafı hazır.

Backend tarafı hazır.

Ama aralarında

henüz bağlantı yok.

İhtiyacımız olan:

```text
Node.js + Express
↓
Bağlantı adresi
↓
MongoDB Atlas
```

## Örnek 149

Bağlantı adresi artık

karşımızda.

Peki:

```text
mongodb+srv://
```
