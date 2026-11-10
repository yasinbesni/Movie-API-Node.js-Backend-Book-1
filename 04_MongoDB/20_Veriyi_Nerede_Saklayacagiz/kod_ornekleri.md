# Bölüm 20 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Veriyi Nerede Saklayacağız?

## Request Bittiğinde Veriye Ne Oluyor?

Bir önceki bölümde

Request akışını şu şekilde kurduk:

```text
Request
↓
Middleware
↓
Route
↓
Response
```

## Örnek 2

Artık istemciden gelen JSON verisine:

```js
req.body
```

## Örnek 3

üzerinden ulaşabiliyoruz.

Örneğin kullanıcı bize:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014
}
```

## Örnek 4

verisini gönderebilir.

Route içinde bu veriyi okuyabiliriz.

İşleyebiliriz.

Hatta kullanıcıya bir Response da gönderebiliriz.

Fakat Request tamamlandığında

önemli bir soru ortaya çıkar.

Bu film bilgisini

daha sonra tekrar kullanmak istersek

nereden bulacağız?

```text
POST /movies
↓
Film bilgisi geldi.
↓
Backend veriyi aldı.
↓
Response gönderildi.
↓
?
```

## Örnek 5

Uygulamamızın yalnızca

veriyi alması yetmez.

Bazı verilerin

Request bittikten sonra da

saklanması gerekir.

Örneğin:

```text
kullanıcılar,

filmler,

siparişler,

ürünler,

yorumlar
```

## Örnek 6

uygulama yeniden çalıştırıldığında da

var olmaya devam etmelidir.

İşte backend uygulamalarında

yeni bir ihtiyacımız burada doğar:

```text
Veriyi saklamak
```

## Örnek 7

## Veriyi Bir Değişkende Tutsak Olmaz mı?

İlk akla gelen çözüm

veriyi JavaScript içinde

bir değişkende tutmak olabilir.

Örneğin:

```js
const movies = [];
```

## Örnek 8

Yeni bir film geldiğinde

onu bu diziye eklediğimizi düşünelim.

```js
movies.push(req.body);
```

## Örnek 9

Uygulama çalışmaya devam ettiği sürece

bu verilere ulaşabiliriz.

```text
Request gelir.
↓
Veri diziye eklenir.
↓
Uygulama çalışmaya devam eder.
↓
Veri kullanılabilir.
```

## Örnek 10

İlk bakışta

sorun çözülmüş gibi görünür.

Fakat sunucuyu durdurup

yeniden başlatalım.

JavaScript programımız yeniden çalışır.

Şu satır da tekrar çalışır:

```js
const movies = [];
```

## Örnek 11

Ve dizimiz yeniden boş başlar.

```text
Uygulama çalışıyor.
↓
Veriler bellekte tutuluyor.
↓
Uygulama kapanıyor.
↓
Bellekteki geçici durum kayboluyor.
↓
Uygulama yeniden başlıyor.
↓
movies = []
```

## Örnek 12

Bu nedenle yalnızca

uygulamanın çalışma belleğinde

veri tutmak,

kalıcı saklama ihtiyacımızı çözmez.

🧠 Akılda Kalsın

```text
Değişken
↓
Program çalışırken veri tutabilir.

Program sona ererse
↓
Bu geçici veri kaybolabilir.
```

## Örnek 13

Bizim ihtiyacımız farklıdır.

```text
Uygulama kapanabilir.

Sunucu yeniden başlayabilir.

Kod güncellenebilir.

Ama önemli veriler
var olmaya devam etmelidir.
```

## Örnek 14

## Veriyi Bir Dosyaya Yazsak?

Başka bir seçenek de

veriyi bir dosyada saklamaktır.

Örneğin:

```text
movies.json
```

## Örnek 15

adında bir dosya oluşturduğumuzu düşünelim.

İçinde:

```json
[
  {
    "title": "Interstellar",
    "releaseYear": 2014
  },
  {
    "title": "Inception",
    "releaseYear": 2010
  }
]
```

## Örnek 16

gibi bilgiler bulunabilir.

Bu yaklaşımın önemli bir avantajı vardır.

Uygulama kapansa bile

dosya bilgisayarın depolama alanında

kalmaya devam edebilir.

```text
Uygulama
↓
Dosyaya veri yazar.
↓
Uygulama kapanır.
↓
Dosya kalır.
```

## Örnek 17

Küçük örneklerde

dosyalarla veri saklamak mümkündür.

Fakat uygulamamız büyüdüğünde

yeni sorular ortaya çıkar.

Binlerce film varsa

belirli bir filmi nasıl hızlı bulacağız?

Bir filmin yalnızca puanını

nasıl güncelleyeceğiz?

Aynı anda birçok kullanıcı

veri eklemeye çalışırsa

bu işlemleri nasıl yöneteceğiz?

Bir kullanıcı silindiğinde

ona bağlı diğer bilgileri

nasıl kontrol edeceğiz?

Veriyi yedeklemek,

aramak,

sıralamak

ve güvenli biçimde güncellemek

nasıl yönetilecek?

```text
Veriyi saklamak
↓
Tek sorun bu değil.

Veriyi bulmak
↓
Güncellemek
↓
Silmek
↓
Düzenlemek
↓
Birden fazla işlemi yönetmek
```

## Örnek 18

gerekecektir.

İşte burada

yalnızca bir dosyadan

daha güçlü bir yapıya ihtiyaç duyarız.

## Veritabanı Nedir?

**Veritabanı**,

uygulamanın ihtiyaç duyduğu verilerin

düzenli biçimde saklanabildiği

ve yönetilebildiği bir veri yapısıdır.

Başlangıç düzeyinde

şöyle düşünebiliriz:

```text
Uygulamanın verileri
↓
Düzenli bir yerde saklanır.
↓
Gerektiğinde bulunur.
↓
Okunur.
↓
Güncellenir.
↓
Silinebilir.
```

## Örnek 19

Örneğin film uygulamamızda

veritabanında şunları tutabiliriz:

```text
Filmler

Kullanıcılar

Favoriler

Yorumlar

İzleme listeleri
```

## Örnek 20

Backend uygulamamız

gerektiğinde veritabanıyla iletişim kurar.

```text
İstemci
↓
Request
↓
Express uygulaması
↓
Veritabanı
↓
Express uygulaması
↓
Response
↓
İstemci
```

## Örnek 21

Burada önemli bir değişiklik oldu.

Şimdiye kadar akışımız:

```text
Request
↓
Backend
↓
Response
```

## Örnek 22

şeklindeydi.

Artık backend'in arkasında

veriyi yöneten yeni bir katman vardır:

```text
Request
↓
Backend
↓
Veritabanı
↓
Backend
↓
Response
```

## Örnek 23

Bu bağlantıyı

ilerleyen bölümlerde

gerçek kodla kuracağız.

## Veritabanı Neden Gerekli?

Veritabanını yalnızca:

> Veriyi kaybetmemek için kullanılan yer

olarak düşünmek eksik olur.

Kalıcı saklama

önemli ihtiyaçlardan yalnızca biridir.

Bir uygulamada

veriler üzerinde sürekli işlemler yaparız.

Örneğin:

```text
Yeni film ekle.

Bütün filmleri getir.

Belirli bir filmi bul.

Filmin puanını değiştir.

Filmi sil.
```

## Örnek 24

Veritabanı sistemleri

bu işlemleri yönetebilmemiz için

bize araçlar sunar.

Film uygulamamız üzerinden düşünelim.

Bir kullanıcı:

```text
GET /movies
```

## Örnek 25

Request'i gönderdiğinde

backend'in sabit bir metin göndermesini istemeyiz.

Gerçek film verilerini

veritabanından almak isteriz.

```text
GET /movies
↓
Express Route
↓
Veritabanına sor
↓
Filmleri getir
↓
Response
```

## Örnek 26

Bir kullanıcı:

```text
POST /movies
```

## Örnek 27

ile yeni film gönderdiğinde ise

verinin yalnızca `req.body` içinde

bir anlığına bulunması yetmez.

```text
POST /movies
↓
req.body
↓
Film bilgileri
↓
Veritabanına kaydet
↓
Daha sonra tekrar kullanılabilir.
```

## Örnek 28

Backend uygulamamızın

gerçek bir veri kaynağıyla çalışması

işte burada başlar.

## Kalıcı Veri Ne Demek?

Bu bölümde sık sık:

```text
kalıcı veri
```

## Örnek 29

ifadesini kullanıyoruz.

Buradaki amaç,

verinin yalnızca

uygulamanın çalışma süresine

bağlı olmamasıdır.

Örneğin:

```text
Sunucu çalışıyor.
↓
Film kaydedildi.
↓
Sunucu kapandı.
↓
Sunucu yeniden açıldı.
↓
Film hâlâ bulunabiliyor.
```

## Örnek 30

Bu davranışa

**persistence**,

Türkçede ise genellikle

**kalıcılık** denir.

Bir backend geliştiricisi olarak

bu kelimeyle sık karşılaşacağız.

```text
Geçici durum
↓
Uygulamanın çalışma süresine bağlı olabilir.

Kalıcı veri
↓
Uygulama yeniden başlasa da
saklanması beklenen veridir.
```

## Örnek 31

Şimdilik bu ayrımı bilmemiz yeterlidir.

## Backend ve Veritabanının Görevleri

Backend ile veritabanı farklı sorumluluklar taşır.

```text
Backend
↓
Request'i karşılar
iş kurallarını çalıştırır
veritabanıyla konuşur
Response üretir

Veritabanı
↓
veriyi saklar
sorgular
günceller
siler
```

## Örnek 32

Örneğin `GET /movies` isteğinde Express isteği karşılar, film verilerini veritabanından ister ve sonucu HTTP Response olarak döndürür. Veritabanı ise filmlerin kalıcı olarak tutulduğu ve sorgulandığı katmandır.

## Veritabanında Her Şeyi Saklar mıyız?

Hayır.

Bir uygulamada bulunan

her bilgi mutlaka veritabanında tutulmaz.

Örneğin bazı bilgiler:

```text
geçici olabilir,

hesaplanarak üretilebilir,

önbellekte tutulabilir,

dosya sisteminde saklanabilir,

veya başka bir servisten alınabilir.
```

## Örnek 33

Veritabanı,

özellikle uygulamanın

daha sonra tekrar ihtiyaç duyacağı

yapısal verileri yönetmek için

çok önemli bir araçtır.

Film uygulamamızda:

```text
film bilgileri,

kullanıcı hesapları,

favoriler,

yorumlar
```

## Örnek 34

veritabanında tutulmaya uygun olabilir.

Fakat örneğin:

```text
"Server 3000 portunda çalışıyor."
```

## Örnek 35

gibi terminal mesajını

veritabanına kaydetmemiz gerekmez.

Bu nedenle:

> Bir veri var.
> O hâlde veritabanına atalım.

şeklinde düşünmeyiz.

Önce verinin

uygulamada nasıl kullanılacağını düşünürüz.

## Veritabanları Veriyi Aynı Şekilde mi Saklar?

Hayır.

Buraya kadar

veritabanının neden gerekli olduğunu öğrendik.

Fakat:

```text
Veritabanı
```

## Örnek 36

tek bir veri düzenleme biçiminin adı değildir.

Farklı veritabanı sistemleri

uygulamadaki veriyi

farklı modellerle düzenleyebilir.

Bu fark,

yalnızca teknik bir ayrıntı değildir.

Verileri nasıl gruplayacağımızı,

aralarındaki bağlantıları nasıl kuracağımızı

ve uygulamanın bu verilerle

nasıl çalışacağını etkileyebilir.

Fakat bu bölümün amacı

veri modellerini karşılaştırmak değil,

önce şu ihtiyacı kurmaktı:

```text
Request bittiğinde de
veriye ulaşabilmeliyiz.
↓
Bunun için kalıcı bir veri kaynağına
ihtiyacımız var.
↓
Veritabanı
```

## Örnek 37

Biz bu kitapta

uygulamamızın verilerini

**MongoDB** ile saklayacağız.

MongoDB koduna geçmeden önce ise

onun veritabanı dünyasında

nasıl bir yaklaşım kullandığını

anlamamız gerekiyor.

Bu karşılaştırmayı

bir sonraki bölümde yapacağız.

## Bölüm Özeti

Bu bölümde

Request ile aldığımız verinin

neden yalnızca:

```js
req.body
```

## Örnek 38

içinde kalmasının yeterli olmadığını gördük.

Veriyi JavaScript değişkeninde tutmanın

uygulama yeniden başladığında

kalıcılık sağlamadığını öğrendik.

Dosyaların veri saklayabildiğini,

fakat büyüyen uygulamalarda:

```text
arama,

güncelleme,

silme,

düzenleme,

çok sayıda veriyle çalışma
```

## Örnek 39

gibi ihtiyaçların

daha sistemli bir çözüm gerektirdiğini gördük.

Bu noktada:

```text
Veritabanı
```

## Örnek 40

kavramıyla tanıştık.

Temel akışımız artık şöyledir:

```text
İstemci
↓
Request
↓
Express
↓
Veritabanı
↓
Express
↓
Response
↓
İstemci
```

## Örnek 41

Ayrıca:

```text
Express
↓
Request ve Response akışını yönetir.

Veritabanı
↓
Uygulamanın verilerini yönetir.
```

## Örnek 42

ayrımını kurduk.

Ve son olarak

bütün veritabanlarının

veriyi aynı şekilde

organize etmediğini gördük.

## Sonraki Adım

Artık veritabanına

neden ihtiyaç duyduğumuzu biliyoruz.

Fakat önümüzde yeni bir soru var.

Bir film bilgisini:

```text
tablo ve satırlar
```

## Örnek 43

içinde mi saklayacağız?

Yoksa:

```text
belgeler
```

## Örnek 44

şeklinde mi tutacağız?

Bu iki yaklaşım bizi

veritabanı dünyasındaki

önemli bir ayrıma götürür:

```text
SQL
ve
NoSQL
```
