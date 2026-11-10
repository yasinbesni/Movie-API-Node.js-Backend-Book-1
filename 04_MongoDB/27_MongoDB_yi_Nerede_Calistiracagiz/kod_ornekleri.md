# Bölüm 27 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# MongoDB'yi Nerede Çalıştıracağız?

## Veritabanımız Şu Anda Nerede?

Bir süredir

MongoDB'nin içini inceliyoruz.

Artık şu yapıyı okuyabiliyoruz:

```text
Database
↓
Collection
↓
Document
↓
Field + Value
↓
_id
↓
ObjectId
```

## Örnek 2

Ama şimdi

çok basit görünen

bir soru soralım:

> Bütün bunlar
> gerçekte nerede çalışacak?

Film platformunuzun

backend'ini açtığınızı düşünün.

Node.js çalışıyor.

Express çalışıyor.

Request geliyor.

Route bulunuyor.

Ve backend:

> Filmleri getir.

diyor.

Peki bu isteği

**hangi MongoDB'ye**

gönderecek?

Şu ana kadar:

```text
MongoDB
```

## Örnek 3

dediğimizde

sanki tek bir yerden

bahsediyormuşuz gibi konuştuk.

Oysa MongoDB'nin çalışması için

gerçek bir ortama ihtiyacı vardır.

Bu ortam:

```text
kendi bilgisayarınız
```

## Örnek 4

olabilir.

Ya da:

```text
internetteki uzak sunucular
```

## Örnek 5

üzerinde olabilir.

İşte şimdi

MongoDB'nin içinden çıkıp

ilk kez:

```text
MongoDB nerede yaşayacak?
```

## Örnek 6

sorusuna bakacağız.

---

## Önce En Basit Seçenek: Kendi Bilgisayarımız

Film platformunu

yalnızca kendi bilgisayarınızda

geliştirdiğinizi düşünün.

Backend'iniz de orada.

Kodunuz da orada.

MongoDB'yi de

aynı bilgisayara kurabilirsiniz.

Bu durumda yapı

kabaca şöyle olur:

```text
Sizin Bilgisayarınız
│
├── Node.js
├── Express
└── MongoDB
```

## Örnek 7

Backend:

```text
aynı bilgisayarda çalışan
MongoDB'ye
```

## Örnek 8

bağlanır.

Buna başlangıçta:

```text
local MongoDB
```

## Örnek 9

diyebiliriz.

`local` burada:

```text
kendi bilgisayarımızda çalışan
```

## Örnek 10

anlamına gelir.

Örneğin bilgisayarınızda

MongoDB Community Edition

çalışıyor olabilir.

Bu durumda veritabanını

kendiniz çalıştırır,

kendiniz durdurur,

kendi bilgisayarınızın

kaynaklarını kullanırsınız.

İlk bakışta

oldukça mantıklı.

İnternet bağlantısı olmadan bile

çalışabileceğiniz durumlar vardır.

Veritabanı sizin makinenizdedir.

Kontrol sizdedir.

Ama hikâye

burada bitmiyor.

---

## Bilgisayarınız Açık Olsa Bile Ne Olur?

Şimdi geliştirme ortamından

bir adım dışarı çıkalım.

Film platformunuzu

internete açtığınızı düşünün.

MongoDB hâlâ

sizin bilgisayarınızda.

Bilgisayarınız açık.

MongoDB de çalışıyor.

Ama backend artık

sizin bilgisayarınızda değil.

İnternetteki başka bir sunucuda

çalışıyor.

Yani elimizde

iki ayrı bilgisayar var:

```text
Sizin Bilgisayarınız
↓
Local MongoDB


Uzak Sunucu
↓
Node.js + Express
```

## Örnek 11

Şimdi kritik soru şu:

> Uzak sunucudaki backend,
> sizin bilgisayarınızdaki MongoDB'ye
> nasıl ulaşacak?

İlk akla gelen adres:

```text
localhost:27017
```

## Örnek 12

olabilir.

Ama burada:

```text
localhost
```

## Örnek 13

kelimesini doğru anlamamız gerekir.

`localhost`:

> Şu anda programın çalıştığı
> bilgisayarın kendisi.

anlamına gelir.

Backend sizin bilgisayarınızda çalışıyorsa:

```text
Backend
↓
localhost
↓
Sizin bilgisayarınız
```

## Örnek 14

olur.

MongoDB de aynı bilgisayardaysa

backend:

```text
localhost:27017
```

## Örnek 15

üzerinden

ona ulaşabilir.

Ama backend

uzaktaki bir sunucuda çalışıyorsa

durum değişir.

Bu kez:

```text
Backend
↓
localhost
↓
Uzak sunucunun kendisi
```

## Örnek 16

demektir.

Yani uzaktaki backend:

```text
localhost:27017
```

## Örnek 17

adresine bağlandığında

sizin bilgisayarınızı aramaz.

Kendi bulunduğu sunucuda

MongoDB arar.

İşte burada

çok önemli bir ayrım ortaya çıkar:

```text
Bilgisayarınız açık
≠
MongoDB'niz internetten erişilebilir
```

## Örnek 18

Local MongoDB'nin

çalışıyor olması,

internetteki başka bir bilgisayarın

ona otomatik olarak

ulaşabileceği anlamına gelmez.

Peki kendi bilgisayarımızdaki

MongoDB'yi dışarıdan erişilebilir

hâle getiremez miyiz?

Teknik olarak mümkündür.

Ama artık yalnızca:

```text
MongoDB'yi çalıştırmak
```

## Örnek 19

yeterli değildir.

Ağ tarafını da

yönetmemiz gerekir.

Örneğin:

```text
genel (public) IP adresi

port yönlendirme

güvenlik duvarı (firewall) kuralları

MongoDB erişim ayarları

güvenli bağlantı
```

## Örnek 20

gibi konular

devreye girebilir.

Üstelik bilgisayarınızı kapatırsanız

bu ortam da

erişilemez hâle gelir.

Demek ki sorun yalnızca:

```text
Bilgisayar açık mı?
```

## Örnek 21

sorusu değildir.

Asıl soru:

> Veritabanımız,
> backend'in güvenli ve sürekli biçimde
> ulaşabileceği bir ortamda mı?

İşte local geliştirme ile

internette çalışan bir uygulamanın

veritabanı ihtiyacı

tam burada ayrılmaya başlar.

---

## MongoDB'yi Uzak Bir Sunucuya Kendimiz Kursak?

O zaman başka bir çözüm

akla gelebilir.

> MongoDB ev bilgisayarımda olmasın.
> İnternetteki bir sunucuda çalışsın.

Bir sunucu kiraladığınızı düşünün.

MongoDB'yi de

o sunucuya siz kuruyorsunuz.

Bu durumda backend,

internetten erişebildiği

uzak bir MongoDB ortamına

bağlanabilir.

```text
Backend
↓
İnternet
↓
Kendi yönettiğimiz MongoDB sunucusu
```

## Örnek 22

Bu tamamen mümkün bir yaklaşımdır.

Ama yeni sorular doğar:

```text
MongoDB'yi kim kuracak?

Güncellemeleri kim yapacak?

Sunucuyu kim yönetecek?

Yedeklemeyi kim planlayacak?

Erişimi kim güvenli hâle getirecek?

Bir sorun çıkarsa kim müdahale edecek?
```

## Örnek 23

Yani bu kez:

```text
MongoDB uzakta
```

## Örnek 24

ama:

```text
altyapı sorumluluğu
↓
hâlâ bizde
```

## Örnek 25

olur.

İşte şimdi

bir sonraki seçenek

çok daha anlamlı hâle geliyor.

---

## Peki Bu Altyapıyı Biz Yönetmeseydik?

Şimdi bir seçenek daha düşünün.

MongoDB'yi:

```text
kendi bilgisayarınıza
```

## Örnek 26

kurmuyorsunuz.

Bir sunucu kiralayıp

MongoDB kurmakla da

uğraşmıyorsunuz.

Bunun yerine:

> Bana çalışır durumda
> bir MongoDB ortamı ver.

diyorsunuz.

Sunucular başka bir yerde.

MongoDB orada çalışıyor.

Siz ise uygulamanızdan

internet üzerinden

ona bağlanıyorsunuz.

```text
Backend
↓
İnternet
↓
Uzak MongoDB ortamı
```

## Örnek 27

İşte burada:

```text
MongoDB Atlas
```

## Örnek 28

karşımıza çıkıyor.

---

## MongoDB Atlas Nedir?

**MongoDB Atlas**,

MongoDB'nin bulutta sunulan

yönetilen veritabanı hizmetidir.

Bu cümlede

iki kelime özellikle önemli:

```text
bulut

yönetilen
```

## Örnek 29

Önce:

```text
bulut
```

## Örnek 30

kelimesini açalım.

Çünkü ilk kez öğrenirken

bu kelime olduğundan

daha gizemli görünebilir.

---

## “Bulut” Gerçekte Neresi?

MongoDB Atlas için:

```text
bulutta çalışan
```

## Örnek 31

bir hizmet dedik.

Peki:

```text
bulut
```

## Örnek 32

tam olarak ne demek?

Bir film document'ımızın

Atlas'ta bulunduğunu düşünün:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014
}
```

## Örnek 33

Bu veri gerçekten:

```text
gökyüzünde
```

## Örnek 34

durmuyor. 🙂

Arka tarafta

gerçek bilgisayarlar,

diskler,

işlemciler

ve veri merkezleri vardır.

Fark şu:

Bu bilgisayarlar

sizin masanızın üzerinde değildir.

Uzak veri merkezlerinde

çalışırlar.

Basitleştirirsek:

```text
Local MongoDB

Sizin bilgisayarınız
↓
MongoDB
↓
Veriler
```

## Örnek 35

Atlas tarafında ise:

```text
Uzak altyapı
↓
MongoDB
↓
Veriler
```

## Örnek 36

Siz bu uzak ortama:

```text
internet üzerinden
```

## Örnek 37

bağlanırsınız.

İşte teknoloji dünyasında

bu tür uzak altyapılar için

sık sık:

```text
bulut
```

## Örnek 38

kelimesini kullanırız.

İngilizce dokümantasyonlarda

aynı kavramı:

```text
cloud
```

## Örnek 39

olarak görebilirsiniz.

Yani bu kitapta:

```text
bulut = cloud
```

## Örnek 40

aynı kavramı ifade edecek.

Biz anlatım boyunca

Türkçe karşılığı olan:

```text
bulut
```

## Örnek 41

kelimesini kullanacağız.

Şimdilik bulutu:

> İnternet üzerinden eriştiğimiz
> uzak bilgisayar altyapısı.

olarak düşünmeniz yeterli.

---

## Peki “Yönetilen” Ne Demek?

Şimdi kendi MongoDB sunucunuzu

kurduğunuzu düşünün.

Sunucu sizin.

MongoDB kurulumu sizin.

Altyapının çalışmasıyla ilgili

birçok sorumluluk da sizin.

Atlas'ta ise

bu altyapının önemli bir bölümü

hizmet tarafından yönetilir.

Başlangıçta farkı

şöyle görebiliriz:

```text
Kendi MongoDB ortamımız
↓
Altyapıyı daha çok biz yönetiriz.

MongoDB Atlas
↓
Altyapının önemli bir bölümünü
Atlas yönetir.
```

## Örnek 42

Ama burada

çok önemli bir çizgi çekelim.

İngilizce dokümantasyonlarda:

```text
managed
```

## Örnek 43

kelimesini görebilirsiniz.

Bu kitapta bunun için:

```text
yönetilen
```

## Örnek 44

ifadesini kullanacağız.

Yani:

```text
yönetilen = managed
```

## Örnek 45

aynı kavramı ifade eder.

Fakat:

```text
yönetilen hizmet
```

## Örnek 46

demek:

```text
Artık hiçbir şeyden
biz sorumlu değiliz.
```

## Örnek 47

demek değildir.

Uygulamamızın:

```text
veri modelini,

kullanıcı yetkilerini,

bağlantı bilgilerini,

uygulama güvenliğini,

hangi verinin saklanacağını
```

## Örnek 48

hâlâ doğru tasarlamamız gerekir.

Atlas:

```text
veritabanı altyapısını
yönetmeyi kolaylaştırır.
```

## Örnek 49

Bizim yerimize

uygulamamızı tasarlamaz.

---

## Atlas'ın Altında Hangi Bulut Var?

Atlas'ta bir MongoDB ortamı

oluştururken

karşınıza şu isimler çıkabilir:

```text
AWS

Google Cloud

Microsoft Azure
```

## Örnek 50

Bunlar:

```text
bulut sağlayıcısı
```

## Örnek 51

denilen hizmetlerdir.

İngilizce arayüzlerde bunu:

```text
cloud provider
```

## Örnek 52

olarak görebilirsiniz.

Şöyle düşünebiliriz:

```text
MongoDB Atlas
↓
MongoDB hizmetini yönetir.

AWS / Google Cloud / Azure
↓
Alt taraftaki bulut altyapısını sağlar.
```

## Örnek 53

Başlangıçta:

> Hangisini seçersem
> MongoDB tamamen değişecek mi?

diye düşünmeyin.

Bizim öğreneceğimiz

MongoDB kavramları:

```text
collection

document

_id

ObjectId
```

## Örnek 54

seçtiğimiz bulut sağlayıcısına göre

başka kavramlara dönüşmez.

Sağlayıcı seçimi

altyapının nerede çalışacağıyla ilgilidir.

---

## Bir Kelime Daha Karşımıza Çıkacak: Cluster

Atlas ekranını açtığınızda

muhtemelen:

```text
cluster
```

## Örnek 55

kelimesini göreceksiniz.

Yeni bir kelime.

Ama burada durup

dağıtık sistemler dersine

girmeyeceğiz.

Şimdilik cluster'ı:

> MongoDB veritabanımızın
> Atlas üzerinde çalıştığı ortam.

gibi düşünebilirsiniz.

Yani:

```text
MongoDB Atlas
↓
Cluster
↓
Database'ler
↓
Collection'lar
↓
Document'lar
```

## Örnek 56

Bu tanım

ileride genişleyecek.

Çünkü gerçek anlamda:

```text
cluster
```

## Örnek 57

tek bir bilgisayardan

daha fazlasını ifade edebilir.

Ama bu kelime

şimdilik sizi durdurmasın.

Cluster konusuna

ihtiyaç duyduğumuz yerde

daha ayrıntılı döneceğiz.

Bugün bilmemiz gereken

yalnızca şu:

> Atlas'ta MongoDB'mizi
> hangi ortamda çalıştıracağız?

sorusunun cevabı.

---

## Free, Flex, Dedicated... Bunlar Ne?

Atlas'ta ilerledikçe

farklı kullanım seviyeleriyle

karşılaşabilirsiniz.

Arayüzde örneğin:

```text
Free

Flex

Dedicated
```

## Örnek 58

gibi seçenekler görebilirsiniz.

İsimler ilk bakışta

birbirinden çok farklı

MongoDB türleriymiş gibi görünebilir.

Aslında burada değişen şey

temel MongoDB kavramları değil,

ortamın sunduğu kaynaklar

ve kullanım kapasitesidir.

Kabaca:

```text
Free
↓
Öğrenme ve küçük denemeler

Flex
↓
Düşük maliyetli geliştirme
ve küçük ölçekli kullanım

Dedicated
↓
Daha yüksek gereksinimli
iş yükleri için ayrılmış kaynaklar
```

## Örnek 59

şeklinde düşünebilirsiniz.

Biz daha:

```text
MongoDB'ye ilk bağlantımızı
```

## Örnek 60

bile yapmadık.

Bu yüzden şu anda

bu seçeneklerin bütün ayrıntılarını

öğrenmemiz gerekmiyor.

Şimdilik bilmemiz gereken:

> Atlas'ta aynı MongoDB'yi
> farklı kapasite ve kullanım düzeylerinde
> çalıştırabileceğimizdir.

Birazdan local MongoDB ile Atlas'ı

yan yana koyduğumuzda,

bu kitapta hangi yolu

izleyeceğimizi de netleştireceğiz.

> Not:
> Atlas'ın paketleri, limitleri ve arayüzü
> zaman içinde değişebilir.
> Bu nedenle amaç ekrandaki bir seçeneği
> ezberlemek değil,
> deployment türlerinin mantığını anlamaktır.

---

## Local MongoDB mi, Atlas mı?

Şimdi iki seçeneği

yan yana koyalım.

### Local MongoDB

```text
MongoDB
↓
Kendi bilgisayarınızda çalışır.
```

## Örnek 61

Geliştirme sırasında:

```text
hızlı erişim,

internet olmadan çalışabilme,

ortam üzerinde doğrudan kontrol
```

## Örnek 62

gibi avantajları olabilir.

Ama MongoDB'nin

kurulması,

çalıştırılması

ve bilgisayarınızdaki ortamın

yönetilmesi gerekir.

### MongoDB Atlas

```text
MongoDB
↓
Uzak bulut altyapısında çalışır.
```

## Örnek 63

Yerel MongoDB sunucusu

kurmadan başlayabilirsiniz.

Backend'iniz

internet üzerinden

Atlas'a bağlanabilir.

Ayrıca aynı uzak veritabanına

izin verdiğiniz farklı ortamlardan

ulaşabilirsiniz.

Ama burada da:

```text
internet bağlantısı,

erişim izinleri,

database user,

bağlantı güvenliği
```

## Örnek 64

gibi yeni kavramlarla

karşılaşacağız.

Yani:

```text
Local
↓
Her zaman daha kolay.

Atlas
↓
Her zaman daha iyi.
```

## Örnek 65

şeklinde bir kural yoktur.

İkisi:

```text
farklı ihtiyaçlara
cevap veren seçeneklerdir.
```

## Örnek 66

---

## Aynı MongoDB, Farklı Yer

Burada çok önemli

bir yanlış anlamayı

baştan engelleyelim.

```text
Local MongoDB
```

## Örnek 67

ile:

```text
Atlas'taki MongoDB
```

## Örnek 68

iki tamamen farklı

veritabanı teknolojisi değildir.

Her iki durumda da

MongoDB ile çalışıyoruz.

Öğrendiğimiz:

```text
Database

Collection

Document

Field

Value

_id

ObjectId
```

## Örnek 69

kavramları kaybolmaz.

Değişen temel şey:

```text
MongoDB'nin çalıştığı yer

ve

o ortamı kimin yönettiği
```

## Örnek 70

dir.

Şöyle düşünebilirsiniz:

```text
MongoDB
│
├── Kendi ortamınızda
│   └── Kendi yönettiğimiz (self-managed)
│
└── Atlas üzerinde
    └── Yönetilen bulut (managed cloud)
```

## Örnek 71

Bu ayrım

ileride başka teknolojileri

öğrenirken de

karşınıza çıkacak.

---

## Backend Atlas'taki MongoDB'yi Nasıl Bulacak?

Şimdi film platformumuza

geri dönelim.

Backend:

```text
Node.js + Express
```

## Örnek 72

ile çalışıyor.

MongoDB ise

Atlas'ta.

Aralarında kilometreler olabilir.

Backend şu soruyu soracak:

> MongoDB nerede?

Bir adres gerekiyor.

Biz bir web sitesine giderken:

```text
https://...
```

## Örnek 73

şeklinde bir adres

kullanabiliyoruz.

MongoDB bağlantısında da

backend'in hangi MongoDB ortamına

bağlanacağını anlatan

bir adres vardır.

Bu kitapta buna:

```text
bağlantı adresi
```

## Örnek 74

diyeceğiz.

İngilizce dokümantasyonlarda ise

aynı kavramı:

```text
connection string
```

## Örnek 75

olarak görebilirsiniz.

Yani:

```text
bağlantı adresi = connection string
```

## Örnek 76

Atlas'ta daha sonra

şuna benzeyen bir yapı göreceğiz:

```text
mongodb+srv://...
```

## Örnek 77

Şimdilik bunu

ezberlemeyin.

İçindeki parçaları da

henüz açmayacağız.

Bugün yalnızca şu ilişkiyi

görmemiz yeterli:

```text
Backend
↓
Bağlantı adresi
↓
MongoDB Atlas
```

## Örnek 78

Yani bağlantı adresi,

backend'imize:

> Bağlanacağın MongoDB
> burada.

demeye yardımcı olur.

Ve tam burada

yeni bir sorun doğuyor.

---

## Adresi Bilen Herkes Veritabanına Girebilir mi?

Bir düşünün.

Atlas:

```text
internette erişilebilir
bir MongoDB ortamı
```

## Örnek 79

sunuyor.

Backend de

internet üzerinden

ona bağlanacak.

Peki bağlantı adresini

öğrenen herkes:

```text
filmleri silebilir mi?

kullanıcıları okuyabilir mi?

verileri değiştirebilir mi?
```

## Örnek 80

Elbette böyle olmasını istemeyiz.

Demek ki yalnızca:

```text
MongoDB nerede?
```

## Örnek 81

sorusunu cevaplamak

yeterli değil.

Bir de:

```text
Kim bağlanabilir?
```

## Örnek 82

sorusunun cevabı gerekiyor.

Atlas bağlantısında

ileride iki önemli güvenlik katmanıyla

karşılaşacağız:

```text
Database User

IP Access List
```

## Örnek 83

Bu iki isim

ilk bakışta biraz teknik görünebilir.

Ama endişelenmeyin.

Atlas kurulumuna geçtiğimizde

ikisini de ekranda

adım adım oluşturacağız.

O zaman:

```text
neden gerekli olduklarını,

nasıl ayarlandıklarını,

hangi hataların çıkabileceğini
```

## Örnek 84

çok daha net göreceğiz.

Şimdilik yalnızca

neden onlara ihtiyaç duyduğumuzu

hissedelim.

```text
Database User
↓
Bağlanmaya çalışan kişi veya uygulama
hangi yetkiyle geliyor?

IP Access List
↓
Hangi ağ adreslerinden
bağlantıya izin veriyoruz?
```

## Örnek 85

Böylece:

```text
Adres
+
Kimlik doğrulama
+
Ağ erişim izni
```

## Örnek 86

birlikte

Atlas bağlantısının

temel parçalarını oluşturmaya başlar.

Ama bunların her birini

ekranda kendimiz oluşturmadan

tam olarak oturtmak zor.

Artık teori

gerçekten yeterli.

---

## Peki Bu Kitapta Hangisini Kullanacağız?

İki çalışma yolunu artık biliyoruz:

```text
Local MongoDB
```

## Örnek 87

ve:

```text
MongoDB Atlas
```

## Örnek 88

Bu kitapta

ana öğrenme yolu olarak:

```text
MongoDB Atlas
```

## Örnek 89

kullanacağız.

İlk öğrenme ortamımız da

Atlas'ın ücretsiz seçeneği olacak.

Arayüzde bunu:

```text
Free
```

## Örnek 90

veya bazı ekranlarda:

```text
M0
```

## Örnek 91

olarak görebilirsiniz.

Neden Atlas?

Çünkü amacımız

yalnızca kendi bilgisayarımızda

çalışan bir demo yapmak değil.

Backend'in:

```text
uzaktaki bir veritabanına
nasıl bağlandığını
```

## Örnek 92

da görmek istiyoruz.

Ayrıca farklı işletim sistemlerinde

MongoDB kurulum ayrıntılarının

kitabın ana hikâyesini

dağıtmasını istemiyoruz.

Böylece odağımız:

```text
işletim sistemi kurulumu
```

## Örnek 93

değil,

```text
MongoDB + Backend ilişkisi
```

## Örnek 94

olacak.

Bu seçim:

```text
Local MongoDB yanlış.
```

## Örnek 95

anlamına gelmez.

Local MongoDB de

farklı geliştirme ihtiyaçlarında

gayet geçerli bir seçenektir.

Biz yalnızca

bu kitabın öğrenme rotasını

netleştiriyoruz:

```text
Bu kitap
↓
MongoDB Atlas
↓
Free / M0 öğrenme ortamı
```

## Örnek 96

Bir sonraki bölümde

bu kararı tekrar tartışmayacağız.

Doğrudan Atlas'ta

ilk ortamımızı oluşturacağız.

---

## Haritayı Bir Kez Daha Açalım

Birkaç bölüm önce

haritamız şöyleydi:

```text
Database
↓
Collection
↓
Document
↓
Field + Value
↓
_id
↓
ObjectId
```

## Örnek 97

Şimdi bu haritayı

bir adım geriye çekebiliriz.

Atlas kullanırsak:

```text
MongoDB Atlas
↓
Cluster
↓
Database
↓
Collection
↓
Document
↓
Field + Value
↓
_id
↓
ObjectId
```

## Örnek 98

Ama uygulamamız açısından

bir parça daha var:

```text
Node.js + Express
↓
?
↓
MongoDB Atlas
```

## Örnek 99

Aradaki soru işaretinin

ne olduğunu artık biliyoruz:

```text
Bağlantı adresi
```

## Örnek 100

Fakat henüz

o bağlantıyı kuracak

Atlas ortamımız yok.

İşte sıradaki işimiz bu.

---

## Bölüm Özeti

Bu bölümde

ilk kez MongoDB'nin:

```text
içinde ne var?
```

## Örnek 101

sorusundan çıkıp:

```text
MongoDB nerede çalışacak?
```

## Örnek 102

sorusuna geçtik.

MongoDB'nin:

```text
kendi bilgisayarımızda
```

## Örnek 103

çalışabileceğini gördük.

Buna:

```text
local MongoDB
```

## Örnek 104

diyebileceğimizi öğrendik.

Ardından uzak bir ortamı

kendimiz yönetmek yerine

yönetilen bir hizmet

kullanabileceğimizi gördük.

Burada:

```text
MongoDB Atlas
```

## Örnek 105

ile tanıştık.

Atlas'ın:

```text
bulutta çalışan

ve

altyapının önemli bölümünü yöneten
```

## Örnek 106

bir MongoDB hizmeti olduğunu öğrendik.

Ayrıca:

```text
Local MongoDB
≠
Atlas
```

## Örnek 107

olsa da

ikisinde de temel teknolojinin:

```text
MongoDB
```

## Örnek 108

olduğunu gördük.

Atlas'ta:

```text
Free

Flex

Dedicated
```

## Örnek 109

gibi farklı deployment seçenekleriyle

karşılaşabileceğimizi öğrendik.

Bu kitapta başlangıç için:

```text
Atlas Free
```

## Örnek 110

kullanacağımıza karar verdik.

Ve backend'in

uzaktaki MongoDB ortamını

bulabilmesi için:

```text
bağlantı adresi
```

## Örnek 111

gerektiğini gördük.

İngilizce dokümantasyonlarda

bunun:

```text
connection string
```

## Örnek 112

olarak geçtiğini de öğrendik.

Son olarak

yalnızca adresin yetmediğini;

bağlantının:

```text
kimlik doğrulama

ve

ağ erişim kuralları
```

## Örnek 113

ile de korunması gerektiğini

fark ettik.

Artık MongoDB'nin

nerede çalışacağını biliyoruz.

Şimdi onu

gerçekten oluşturmamız gerekiyor.

---

## Sonraki Adım

Tarayıcınızı açtığınızı düşünün.

MongoDB Atlas'a giriyorsunuz.

Henüz:

```text
database yok.

collection yok.

document yok.
```

## Örnek 114

Önümüzde

boş bir ekran var.

Birkaç dakika sonra ise

orada bizim:

```text
ilk MongoDB ortamımız
```

## Örnek 115

çalışıyor olacak.

Ama önce:

```text
Atlas hesabı

Project

Free cluster

Database User

IP Access List
```
