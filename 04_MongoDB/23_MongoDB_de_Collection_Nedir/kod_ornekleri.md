# Bölüm 23 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# MongoDB'de Collection Nedir?

## Platformumuz Bir Gecede Büyüseydi?

Dün film kataloğumuzda yalnızca birkaç film vardı. *Interstellar*, *Inception*, *Arrival* ve *Dune*... Aradığımız her kayıt bir filmdi; hangisinin nereye ait olduğu belliydi.

Şimdi bir sabah Movie API'nin beklenmedik bir ilgi gördüğünü düşünün.

Yeni filmler ekleniyor.

İnsanlar hesap açmak istiyor.

Yorumlar gelmeye, kişisel film listeleri oluşmaya başlıyor.

Ve veri çoğalmaya devam ediyor.

Henüz Movie API'mize kullanıcı hesabı, yorum veya kişisel liste özelliği eklemedik. Bunlar bugün kurduğumuz uygulamanın değil, büyümeye başlayan bir film platformunun karşılaşabileceği yeni ihtiyaçlar.

Ama böyle bir gün geldiğinde önümüze çıkacak soruyu şimdiden görebiliyoruz: **Birbirinden farklı kayıtları nasıl düzenli tutacağız?**

Veritabanında şu kayıtların yan yana bulunduğunu düşünün:

```text
Interstellar

Inception

Arrival

Dune

kullanıcı 1

kullanıcı 2

yorum 1

yorum 2

...
```

## Örnek 2

Şimdi bütün bunların

MongoDB içinde

hiçbir düzen olmadan

yan yana durduğunu düşünün.

Bir filmi arıyorsunuz.

Araya kullanıcılar giriyor.

Bir yorumu arıyorsunuz.

Karşınıza filmler çıkıyor.

Veri var.

Ama düzen yok.

İşte tam burada

yeni bir ihtiyaç doğuyor:

> Aynı amaca ait belgeleri
> nasıl bir arada tutacağız?

MongoDB'nin bu soruya

bir cevabı var.

Adı:

```text
Collection
```

## Örnek 3

---

## Collection Bize Ne Sağlıyor?

Önce film verilerimize bakalım.

Elimizde:

```text
Interstellar

Inception

Arrival

Dune
```

## Örnek 4

gibi birçok film belgesi var.

Bunları:

```text
movies
```

## Örnek 5

adında bir yapı altında

bir araya getirdiğimizi düşünün.

```text
movies
│
├── Interstellar
├── Inception
├── Arrival
└── Dune
```

## Örnek 6

İşte MongoDB'de

belgelerin bir arada tutulduğu

bu yapıya:

```text
Collection
```

## Örnek 7

denir.

Yani:

```text
Tek film kaydı
↓
Document

Birçok film document'ı
↓
Collection
```

## Örnek 8

Collection'ı yalnızca:

> Belgelerin bulunduğu yer.

diye ezberlemek yerine

neden var olduğunu düşünün.

```text
Veri çoğaldı.
↓
Benzer verileri gruplama ihtiyacı doğdu.
↓
Collection
```

## Örnek 9

Kavramın özü budur.

---
## Database ile Collection Arasındaki Fark Nedir?

Şimdi MongoDB'de

film platformumuz için:

```text
movieApp
```

## Örnek 10

adında bir veritabanı

oluşturduğumuzu düşünün.

Bu veritabanını açtığınızda

içinde yalnızca filmler bulunmak zorunda değildir.

Daha kapsamlı bir film platformunda:

```text
filmler,

kullanıcılar,

yorumlar
```

## Örnek 11

gibi farklı türde veriler bulunabilir. Bizim örneğimizde önce yalnızca `movies` koleksiyonunun neden gerekli olduğunu öğreniyoruz; MongoDB bağlantısını ve gerçek kayıtları sonraki adımlarda oluşturacağız.

Bunları birbirinden ayırmak için

veritabanının içinde

farklı collection'lar bulunabilir.

Örneğin:

```text
movieApp
│
├── movies
├── users
└── reviews
```

## Örnek 12

Şimdi yapıyı

adım adım okuyalım.

```text
movieApp
```

## Örnek 13

uygulamamızın veritabanıdır.

Yani:

```text
movieApp
↓
Database
```

## Örnek 14

Bu veritabanının içinde:

```text
movies
```

## Örnek 15

adında bir collection bulunabilir.

```text
movieApp
↓
movies
```

## Örnek 16

Buradaki:

```text
movies
```

## Örnek 17

film belgelerini

bir arada tutar.

Biraz daha içeri girelim.

```text
movieApp
│
└── movies
    ├── Interstellar
    ├── Inception
    └── Arrival
```

## Örnek 18

Şimdi üç farklı seviyeyi

aynı anda görebiliyoruz.

```text
movieApp
↓
Database

movies
↓
Collection

Interstellar
↓
Document
```

## Örnek 19

Yani:

```text
Database
↓
Collection'ları barındırır.

Collection
↓
Document'ları barındırır.

Document
↓
Tek bir kaydı temsil eder.
```

## Örnek 20

Film platformumuzda bunu şöyle okuyabiliriz:

```text
movieApp
↓
Platformumuzun veritabanı

movies
↓
Film kayıtlarının bulunduğu collection

Interstellar
↓
Tek bir film document'ı
```

## Örnek 21

Şimdi küçük bir kontrol yapalım.

Size:

> Kullanıcı kayıtları nerede tutulabilir?

diye sorsam,

cevabımız:

```text
users collection
```

## Örnek 22

olabilir.

Peki:

> `users` collection nerede bulunur?

Cevap:

```text
movieApp database
```

## Örnek 23

Peki:

> Tek bir kullanıcı kaydı nedir?

O da:

```text
bir document
```

## Örnek 24

olacaktır.

Artık temel yapı daha net:

```text
movieApp
│
├── movies
│   ├── film document'ları
│   └── ...
│
├── users
│   ├── kullanıcı document'ları
│   └── ...
│
└── reviews
    ├── yorum document'ları
    └── ...
```

## Örnek 25

Bu nedenle:

```text
Database
```

## Örnek 26

ile:

```text
Collection
```

## Örnek 27

aynı şey değildir.

Database,

collection'ları bir arada tutan

daha geniş yapıdır.

Collection ise

belirli bir amaçla kullandığımız

document'ları bir arada tutar.

🧠 Akılda Kalsın

```text
Database
↓
Collection'lar

Collection
↓
Document'lar

Document
↓
Tek bir kayıt
```

## Örnek 28

---

## Peki Neden Her Şeyi Tek Collection'a Atmıyoruz?

Şimdi karar sizin.

Elinizde:

```text
filmler

kullanıcılar

yorumlar
```

## Örnek 29

var.

Hepsini:

```text
everything
```

## Örnek 30

adında tek bir collection'a

koyduğunuzu düşünün.

Başlangıçta kolay görünüyor.

Tek yer var.

Her şeyi oraya atıyorsunuz.

Ama platform büyüyor.

Bir süre sonra:

```text
everything
│
├── film
├── kullanıcı
├── yorum
├── film
├── kullanıcı
├── yorum
└── ...
```

## Örnek 31

şeklinde bir yapı oluşuyor.

Artık veri sayısı arttıkça

hangi kaydın hangi amaçla

kullanıldığını anlamak zorlaşıyor.

Bu yüzden başlangıçta

benzer amaçla kullanılan belgeleri:

```text
movies

users

reviews
```

## Örnek 32

gibi ayrı collection'larda

düşünmek çok daha anlaşılırdır.

Ama burada küçük bir tuzak var.

Şu sonucu çıkarmayın:

> Her ilişkili veri
> mutlaka ayrı collection'da tutulur.

MongoDB'de bazı ilişkili veriler

aynı document içinde de bulunabilir.

Bazıları ise

ayrı collection'larda tutulabilir.

Bu karar:

```text
veri modelleme
```

## Örnek 33

konusunun parçasıdır.

O kapıyı

şimdilik kapalı bırakalım.

Çünkü henüz

collection'ın kendisini öğreniyoruz.

---

## Collection Bir SQL Tablosu mu?

Bir önceki bölümden

ilişkisel veritabanlarını hatırlayın.

Orada:

```text
Table
```

## Örnek 34

kavramını görmüştük.

Şimdi MongoDB'de:

```text
Collection
```

## Örnek 35

ile karşılaşıyoruz.

İkisini zihnimizde

yan yana koyabiliriz:

```text
İlişkisel veritabanı
↓
Table

MongoDB
↓
Collection
```

## Örnek 36

İlk bakışta

aynı işi yapıyorlarmış gibi görünebilir.

İkisi de

verileri gruplamaya yardım eder.

Ama tam burada

önemli bir çizgi çekelim:

```text
Table
≠
Collection
```

## Örnek 37

Çünkü ilişkisel tablo

ve MongoDB collection'ı

aynı veri modeline dayanmaz.

Bu yüzden collection'ı:

> MongoDB'deki tablonun başka adı.

diye öğrenmek

ileride bizi yanıltır.

Daha doğru düşünce:

```text
Benzer bir ihtiyaca
cevap verebilirler.

Ama aynı yapı değildirler.
```

## Örnek 38

---

## Aynı Collection'daki Her Film Aynı Görünmek Zorunda mı?

Şimdi `movies` collection'ını açın.

İlk film:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7
}
```

## Örnek 39

Bir sonraki film:

```json
{
  "title": "Inception",
  "releaseYear": 2010,
  "rating": 8.8,
  "director": "Christopher Nolan"
}
```

## Örnek 40

Bir şey dikkatinizi çekti mi?

İkinci filmde:

```text
director
```

## Örnek 41

alanı var.

İlkinde yok.

Yine de ikisi de:

```text
movies
```

## Örnek 42

collection'ında bulunabilir.

Bu bize daha önce gördüğümüz

bir fikri hatırlatıyor:

```text
Esnek
≠
Kuralsız
```

## Örnek 43

MongoDB collection'ları

esnek olabilir.

Fakat gerçek uygulamalarda

verinin nasıl olması gerektiğine dair

kurallar koymak isteyeceğiz.

O konuya

Mongoose ile geldiğimizde

daha ciddi biçimde döneceğiz.

Şimdilik burada duralım.

Çünkü collection'ın görevi:

```text
belgelerin bütün kurallarını belirlemek
```

## Örnek 44

değil,

önce onları:

```text
bir arada tutmak
```

## Örnek 45

fikrini anlamaktır.

---

## Collection'ı Önceden Oluşturmak Zorunda mıyız?

Şimdi platformunuza

ilk filmi eklediğinizi düşünün.

MongoDB'de henüz:

```text
movies
```

## Örnek 46

collection'ı yok.

İlk film geliyor.

Ne olacak?

Temel kullanımda MongoDB,

ilk belge eklenirken

gerekli collection'ı oluşturabilir.

```text
movies yok
↓
İlk film ekleniyor
↓
movies oluşuyor
↓
Film document'ı ekleniyor
```

## Örnek 47

Ama collection'lar

önceden açıkça da oluşturulabilir.

Örneğin ileride

özel ayarlar

veya doğrulama kuralları

tanımlamak isteyebiliriz.

Şimdilik komut yok.

Ezber yok.

Çünkü önce

haritayı çıkarıyoruz.

Komutlara geldiğimizde

hangi yapıya neden komut verdiğimizi

zaten biliyor olacağız.

---

## Şimdi Bir Katman Daha Aşağı İnelim

Şu ana kadar

platformumuza yukarıdan baktık.

```text
movieApp
↓
movies
```

## Örnek 48

Şimdi `movies` collection'ını açın.

Karşınızda:

```text
Interstellar

Inception

Arrival
```

## Örnek 49

var.

Bir tanesini seçin.

Mesela:

```text
Interstellar
```

## Örnek 50

Artık binlerce filmle

ilgilenmiyoruz.

Collection'ı da

bir kenara bırakıyoruz.

Tek bir kayıt var.

Peki bu kayıt

MongoDB açısından

tam olarak nasıl oluşturuluyor?

Şu bilgileri görüyoruz:

```text
title

releaseYear

rating
```

## Örnek 51

Ama bunların:

```text
adı ne?

değeri ne?

MongoDB bunları
document içinde nasıl tutuyor?
```

## Örnek 52

henüz konuşmadık.

İşte collection'ın sonuna geldiğimiz yerde

yeni bir kapı açılıyor.

Kapının üzerinde:

```text
Document
```

## Örnek 53

yazıyor.

---

## Bölüm Özeti

Bu bölümde

MongoDB'deki:

```text
Collection
```

## Örnek 54

kavramını,

bir tanımı ezberleyerek değil,

veri çoğaldığında ortaya çıkan

bir ihtiyaç üzerinden öğrendik.

```text
Birçok film belgesi
↓
Bunları gruplama ihtiyacı
↓
movies collection
```

## Örnek 55

Temel hiyerarşimiz:

```text
Database
↓
Collection
↓
Document
```

## Örnek 56

şeklinde oluştu.

Film platformumuzda:

```text
movieApp
│
├── movies
├── users
└── reviews
```

## Örnek 57

gibi collection'lar

bulunabileceğini gördük.

Ayrıca:

```text
Table
≠
Collection
```

## Örnek 58

ayrımını yaptık.

Ve aynı collection içindeki

document'ların

birebir aynı alanlara sahip olmak

zorunda olmadığını hatırladık.

Artık:

```text
Collection nedir?
```

## Örnek 59

sorusunun cevabını biliyoruz.

Ama tam da bu cevap

bizi yeni bir soruya götürüyor.

---

## Sonraki Adım

`movies` collection'ının

içinde olduğunuzu düşünün.

Karşınızda yalnızca:

```text
Interstellar
```

## Örnek 60

var.

Şimdi collection'a değil,

o tek kaydın içine bakacağız.

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7
}
```

## Örnek 61

Bu yapının içindeki:

```text
title

releaseYear

rating
```
