# Bölüm 7 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

rıyla tamamlandı.

404

İstenen kaynak bulunamadı.

500

Sunucuda beklenmeyen bir hata oluştu.

🧠 Akılda Kalsın

Status Code,

sunucunun isteğin sonucunu tek bir sayı ile özetler.

### Header

Response Header,

sunucunun gönderdiği cevapla ilgili ek bilgileri taşır.

Header'ın içinde hangi bilgilerin bulunduğunu ilerleyen bölümlerde gerçek örneklerle öğreneceğiz.

Şimdilik yalnızca cevap hakkında ek bilgiler taşıdığını bilmeniz yeterlidir.

🧠 Akılda Kalsın

Header,

cevabın kendisini değil,

cevap hakkında ek bilgileri taşır.

### Body

Response Body,

sunucunun göndermek istediği asıl veridir.

Film listesi,

bir kullanıcının bilgileri,

oluşturulan yeni kayıt,

veya herhangi bir veri burada bulunur.

Kullanıcının ekranda gördüğü içerik çoğu zaman Response Body'den gelir.

🧠 Akılda Kalsın

Response Body,

sunucunun kullanıcıya göndermek istediği gerçek içeriği taşır.

## Sonraki Adım

Artık istemci ile sunucu arasındaki iletişimin

temel parçalarını biliyoruz.

Request'in nasıl oluşturulduğunu,

sunucuya nasıl ulaştığını

ve Response'un nasıl geri döndüğünü gördük.

Fakat şimdi başka bir soru ortaya çıkıyor.

Biz bu sunucu tarafındaki uygulamayı

hangi teknolojiyle geliştireceğiz?

```text
Request gelir.
↓
Backend çalışır.
↓
Bir işlem yapılır.
↓
Response hazırlanır.
```

## Örnek 2

Şimdiye kadar bu akışın nasıl gerçekleştiğini öğrendik.

Sırada,

bu akışı gerçekleştirecek

backend kodlarını yazmaya başlamak var.

Movie API'nin backend tarafında

**JavaScript** kullanacağız.

Fakat JavaScript kodlarımızın

sunucu tarafında çalışabilmesi için

önce yeni bir ortamla tanışmamız gerekiyor:

```text
Node.js
```
