# Bölüm 25 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# MongoDB Bir Document'ı Diğerinden Nasıl Ayırır?

## Yanlış Filmi Güncellemek Üzeresiniz

Film platformunuz artık

küçük bir deneme projesi değil.

`movies` collection'ında

binlerce film var.

Bir gün yönetim panelini açıyorsunuz.

Size küçük bir düzeltme görevi verilmiş:

> Dune filminin puanını güncelle.

Kolay.

Arama kutusuna:

```text
Dune
```

## Örnek 2

yazıyorsunuz.

Enter.

Ve ekranda iki sonuç beliriyor:

```text
Dune — 1984

Dune — 2021
```

## Örnek 3

Bir saniye.

İkisinin de adı aynı.

Siz:

```text
Dune — 2021
```

## Örnek 4

kaydıyla ilgileniyorsunuz.

Şimdi backend tarafını düşünün.

Eğer MongoDB'de yalnızca:

```text
title = "Dune"
```

## Örnek 5

koşuluyla arama yaparsak,

bu koşul:

```text
Dune — 1984

Dune — 2021
```

## Örnek 6

document'larının ikisiyle de

eşleşebilir.

İşte sorun burada.

Biz:

```text
Dune — 2021
```

## Örnek 7

document'ını hedeflemek istiyoruz.

Ama kullandığımız bilgi:

```text
title = "Dune"
```

## Örnek 8

bunu tek başına

kesinleştirmiyor.

Yani artık ihtiyacımız yalnızca:

> Dune adlı filmi bul.

değil.

Asıl ihtiyacımız:

> Collection içindeki
> tam olarak bu document'ı bul.

olmaya başladı.

---

## Yılı da Göndersek?

Aklınıza hemen bir çözüm gelebilir:

> Filmin yılını da kullanalım.

O zaman:

```text
title       → Dune
releaseYear → 2021
```

## Örnek 9

diyebiliriz.

Ve büyük ihtimalle

aradığımız filmi buluruz.

Sorun çözülmüş gibi görünüyor.

Ama biraz daha düşünelim.

`title` nedir?

```text
Filmin adı.
```

## Örnek 10

`releaseYear` nedir?

```text
Filmin yayın yılı.
```

## Örnek 11

Yani ikisi de

film hakkında tuttuğumuz:

```text
veriler
```

## Örnek 12

dir.

Ve bu veriler

değişebilir.

Başlık yanlış girilmiş olabilir.

Yayın yılı düzeltilebilir.

Başka bilgiler güncellenebilir.

Demek ki:

```text
title

releaseYear

rating
```

## Örnek 13

bir document'ın

**içeriğini anlatır.**

Fakat bizim başka bir ihtiyacımız var.

Biz:

> Bu document'ın içeriği nedir?

sorusunu değil,

> Collection'daki document'lardan
> tam olarak hangisi?

sorusunu cevaplamak istiyoruz.

Bu ikisi aynı soru değildir.

---

## İçerik Değişse Bile Document Aynı Document

Şimdi `Dune — 2021`

document'ını seçtiğinizi düşünün.

Başlığı yanlış yazılmış olsun:

```text
Dunne
```

## Örnek 14

Düzeltiyorsunuz:

```text
Dune
```

## Örnek 15

Sonra puanı değişiyor:

```text
8.0
↓
8.1
```

## Örnek 16

Belki daha sonra

başka alanlar da değişecek.

```text
title değişti.

rating değişti.

başka bilgiler değişti.
```

## Örnek 17

Ama siz hâlâ:

```text
aynı document
```

## Örnek 18

üzerinde çalışıyorsunuz.

İşte ihtiyacımız burada

çok daha net ortaya çıkıyor.

Document'ın içeriğinden bağımsız olarak

onu diğer document'lardan

ayırt edebileceğimiz

bir kimlik gerekiyor.

```text
Document'ın bilgileri
↓
Değişebilir.

Document'ın kimliği
↓
Onu diğer kayıtlardan ayırır.
```

## Örnek 19

---

## Her Document'ın Kendine Ait Bir Kimliği Olsaydı?

`movies` collection'ımıza

yeniden bakalım.

```text
movies
│
├── Dune — 1984
├── Dune — 2021
├── Interstellar
└── Arrival
```

## Örnek 20

Şimdi her document'ın

kendine ait bir kimliği

olduğunu düşünün.

```text
Dune — 1984
↓
Kimlik: A

Dune — 2021
↓
Kimlik: B
```

## Örnek 21

Artık:

> Dune'u güncelle.

demek zorunda değiliz.

Şöyle düşünebiliriz:

> Kimliği B olan document'ı güncelle.

Film adı değişse bile:

```text
Kimlik: B
```

## Örnek 22

aynı document'ı göstermeye devam eder.

Puan değişse bile:

```text
Kimlik: B
```

## Örnek 23

bizi yine aynı document'a götürür.

İşte MongoDB'de

document'ları birbirinden ayırmak için

özel bir field bulunur:

```text
_id
```

## Örnek 24

---

## Demek `_id` Bir Field

Bir önceki bölümde

şu yapıyı öğrenmiştik:

```text
Field
↓
Value
```

## Örnek 25

Şimdi document'ımıza

yeni gözlerle bakalım.

```text
_id
↓
Document'ın kimliğini taşıyan field
```

## Örnek 26

Örneğin MongoDB'de

bir document şöyle görünebilir:

```text
_id          → ?
title        → "Dune"
releaseYear  → 2021
rating       → 8.1
```

## Örnek 27

`title` filmin adını,

`releaseYear` yayın yılını,

`rating` puanını taşır.

`_id` ise başka bir soruya cevap verir:

> Bu document hangisi?

Bu nedenle:

```text
title
↓
Document hakkında bilgi

_id
↓
Document'ı tanımlayan kimlik
```

## Örnek 28

ayrımını yapmak önemlidir.

---

## Ama Bir Şey Eksik

`_id` alanını öğrendik.

Fakat karşısına

ne yazacağız?

```text
_id → ?
```

## Örnek 29

Bir sayı olabilir mi?

```text
_id → 1
```

## Örnek 30

Bir metin olabilir mi?

```text
_id → "dune-2021"
```

## Örnek 31

Yoksa MongoDB'nin

bunun için kullandığı

özel bir yapı mı var?

Şimdi MongoDB'de

bir document açtığınızı düşünün.

Karşınıza şu çıkıyor:

```text
_id: ObjectId("507f191e810c19729de860ea")
```

## Örnek 32

Durun.

```text
_id
```

## Örnek 33

kısmını artık biliyoruz.

Bu bir field.

Ama karşısındaki:

```text
ObjectId("507f191e810c19729de860ea")
```

## Örnek 34

ne?

Üstelik bunu

biz yazmadık.

Nereden geldi?

Neden:

```text
1
```

## Örnek 35

gibi basit bir sayı değil?

Neden bu kadar uzun?

Ve MongoDB,

bu değeri nasıl oluşturuyor?

Artık `_id` alanına

neden ihtiyaç duyduğumuzu biliyoruz.

Şimdi sıra,

o alanın karşısında gördüğümüz

garip değeri çözmekte.

```text
_id
↓
Kimlik field'ı

ObjectId(...)
↓
?
```

## Örnek 36

İşte şimdi

**ObjectId'nin içine girmeye hazırız.**

---

## Bölüm Özeti

Bu bölümde

küçük görünen bir sorunla başladık:

```text
Dune — 1984

Dune — 2021
```

## Örnek 37

İki document'ın da:

```text
title = "Dune"
```

## Örnek 38

değerine sahip olabileceğini gördük.

Bu yüzden yalnızca:

```text
title

releaseYear

rating
```

## Örnek 39

gibi bilgilere bakmanın

bir document'ı her zaman

kesin biçimde tanımlamak için

yeterli olmadığını fark ettik.

Çünkü bunlar:

```text
document'ın içeriğidir.
```

## Örnek 40

Ve içerik:

```text
değişebilir.
```

## Örnek 41

Bizim ihtiyaç duyduğumuz şey ise

document'ın içeriğinden bağımsız olarak

onu diğer document'lardan

ayırt edebilecek bir kimlikti.

MongoDB'de bu görev için

özel bir field bulunur:

```text
_id
```

## Örnek 42

Artık şu ayrımı biliyoruz:

```text
title
↓
Document hakkında bilgi

_id
↓
Document'ı diğerlerinden
ayırt eden kimlik field'ı
```

## Örnek 43

Ama bölümün sonunda

yeni bir şey fark ettik.

`_id` field'ının karşısında

şuna benzeyen bir değer vardı:

```text
ObjectId("507f191e810c19729de860ea")
```

## Örnek 44

`_id` kısmını artık biliyoruz.

Fakat:

```text
ObjectId(...)
```

## Örnek 45

hâlâ çözülmeyi bekliyor.

---

## Sonraki Adım

Bir sonraki bölümde

şu sorunun peşine düşeceğiz:

**ObjectId Nedir ve Neden Böyle Görünür?**

Neden:

```text
1
```

## Örnek 46

veya:

```text
"film-42"
```

## Örnek 47

gibi basit bir değer yerine

şuna benzeyen bir yapı görüyoruz?

```text
ObjectId("507f191e810c19729de860ea")
```
