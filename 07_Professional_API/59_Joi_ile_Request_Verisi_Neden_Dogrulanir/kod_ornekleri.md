# Bölüm 59 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Joi ile Request Verisi Neden Doğrulanır?

## Önce Nerede Olduğumuzu Netleştirelim

Bu bölümde geçersiz veri problemiyle **ilk kez karşılaşmıyoruz**.

40. bölümde Movie Schema'ya validation kuralları eklemiştik:

```text
title
↓
zorunlu

releaseYear
↓
en az 1888

voteAverage
↓
0 ile 10 arasında
```

## Örnek 2

Böylece örneğin:

```json
{
  "title": "",
  "releaseYear": 1800,
  "voteAverage": 25
}
```

## Örnek 3

gibi bir veri Mongoose validation'ı geçemiyor ve MongoDB'ye yazılmıyordu.

Yani burada amacımız aynı kontrolleri yeniden keşfetmek değildir. Mongoose, **veritabanına yazılacak Document'ın Schema kurallarına uygunluğunu** zaten kontrol ediyor.

59. bölümde farklı bir soruya geçiyoruz:

> **HTTP Request, Mongoose'a ulaşmadan önce API'nin kabul ettiği veri biçimine uygun mu?**

Bu farkı görmek için mevcut `POST /movies` akışımıza bakalım.

Bir film oluştururken `releaseYear` alanına yanlışlıkla `"2014"` yazdığımızı düşünelim. Bu değeri bir metin olarak kabul etmek istemiyorsak isteği daha veritabanına ulaşmadan reddedebiliriz. Bu bölümde kullanacağımız **Joi**, HTTP isteklerini bizim belirlediğimiz kurallara göre denetleyen bir kütüphanedir. Örneğimizde dönüşümü kapattığımızda (`convert: false`) metin olarak gelen yılı reddeder. **Mongoose ise belgenin veritabanına yazılırken model kurallarını sağlayıp sağlamadığını** denetler. Aynı alanı kontrol etseler de bu iki katmanın sorumluluğu farklıdır.

```text
HTTP Request
↓
createMovie Controller
↓
new Movie(...)
↓
Mongoose validation
↓
MongoDB
```

## Örnek 4

Şu ana kadar asıl korumamız Mongoose katmanındaydı. Şimdi Request ile Mongoose arasındaki sınırı da açıkça tanımlayacağız.

---

## Mongoose'un Kabul Edebildiği Ama API'de İstemeyebileceğimiz Değerler

`createMovie` Controller'ımız Body'den alanları alıyor:

```js
export const createMovie = async (req, res) => {
  const {
    title,
    releaseYear,
    voteAverage,
  } = req.body;

  const movie = new Movie({
    title,
    releaseYear,
    voteAverage,
  });

  await movie.save();

  res.status(201).json({
    message: "Film oluşturuldu.",
    data: movie,
  });
};
```

## Örnek 5

Şimdi şu Body'yi düşünelim:

```json
{
  "title": "   ",
  "releaseYear": "2014",
  "voteAverage": 8.7
}
```

## Örnek 6

Bu örneği seçmemizin nedeni, 40. bölümdeki validation'ın yetersiz olduğunu söylemek değildir. Burada **iki katmanın farklı sorular sorduğunu** görmek istiyoruz.

### `title: "   "`

40. bölümde şunu görmüştük:

```json
{
  "title": ""
}
```

## Örnek 7

boş String olduğu için `required: true` kontrolünü geçmez.

Fakat:

```json
{
  "title": "   "
}
```

## Örnek 8

aynı şey değildir. String'in içinde karakter vardır; sadece bu karakterlerin tamamı boşluktur.

API sözleşmemizde daha kesin bir kural koymak isteyebiliriz:

```text
title
↓
yalnızca gönderilmiş olmak yetmez
↓
en az bir gerçek karakter içermeli
```

## Örnek 9

### `releaseYear: "2014"`

Schema'da `releaseYear` alanımız `Number` olsa da Mongoose bazı değerleri uygun türe **cast** etmeye çalışabilir:

```text
"2014"
↓
Mongoose casting
↓
2014
```

## Örnek 10

Bu, Mongoose açısından yararlı bir davranıştır. Fakat API sözleşmemiz daha katı olabilir:

```text
releaseYear
↓
JSON Body içinde gerçekten Number olarak gönderilmeli
```

## Örnek 11

Yani şu iki değer bizim API tasarımımız açısından aynı olmayabilir:

```json
{ "releaseYear": 2014 }
```

## Örnek 12



```json
{ "releaseYear": "2014" }
```

## Örnek 13

İlkinde istemci Number gönderir; ikincisinde String gönderir.

Buradaki yeni ihtiyaç şudur:

```text
Mongoose
↓
Document veritabanı kurallarına uygun mu?

Request validation
↓
İstemci endpoint'in beklediği veri biçimini gönderdi mi?
```

## Örnek 14

---

## Önce Aynı Kontrolü `if` ile Yapalım

Request sınırında kontrol yapmak için yeni bir kütüphane kullanmak zorunda değiliz.

Mevcut JavaScript bilgimizle bunu doğrudan `if` bloklarıyla yazabiliriz.

`POST /movies` için istediğimiz kurallar şunlar olsun:

```text
title
↓
String olmalı
zorunlu olmalı
yalnızca boşluklardan oluşmamalı

releaseYear
↓
Number olmalı
tam sayı olmalı
1888 veya daha büyük olmalı

voteAverage
↓
Number olmalı
0–10 arasında olmalı
```

## Örnek 15

Controller'ın başında bu kontrolleri elle yazabiliriz:

```js
const {
  title,
  releaseYear,
  voteAverage,
} = req.body;

if (
  typeof title !== "string" ||
  !/\S/.test(title)
) {
  return res.status(400).json({
    message: "title geçerli bir metin olmalı.",
  });
}

if (
  typeof releaseYear !== "number" ||
  !Number.isSafeInteger(releaseYear) ||
  releaseYear < 1888
) {
  return res.status(400).json({
    message: "releaseYear geçerli bir yıl olmalı.",
  });
}

if (
  typeof voteAverage !== "number" ||
  !Number.isFinite(voteAverage) ||
  voteAverage < 0 ||
  voteAverage > 10
) {
  return res.status(400).json({
    message: "voteAverage 0 ile 10 arasında olmalı.",
  });
}
```

## Örnek 16

Bu kod yanlış değildir.

Hatta üç alan için baktığımızda oldukça anlaşılırdır:

```text
Request geldi
↓
alanları tek tek kontrol et
↓
hata varsa 400 döndür
↓
geçerliyse kayıt işlemine devam et
```

## Örnek 17

Dolayısıyla Joi'ye ihtiyacımızı yalnızca:

```text
if kullanmak kötü
```

## Örnek 18

gibi bir düşünceyle açıklayamayız.

Asıl ihtiyaç proje büyüdüğünde ortaya çıkar.

---

## `if` Kontrolleri Büyümeye Başladığında Ne Olur?

Şu anda yalnızca `POST /movies` için üç alanı kontrol ettik.

Fakat aynı Movie verisini `PATCH /movies/:movieId` içinde de kabul ediyoruz. PATCH'te bu kez alanların tamamı zorunlu değildir; yalnızca gönderilen alanları kontrol etmemiz gerekir.

Örneğin `voteAverage` için yeniden şöyle bir kontrol gerekir:

```js
if (voteAverage !== undefined) {
  if (
    typeof voteAverage !== "number" ||
    !Number.isFinite(voteAverage) ||
    voteAverage < 0 ||
    voteAverage > 10
  ) {
    return res.status(400).json({
      message: "voteAverage 0 ile 10 arasında olmalı.",
    });
  }
}
```

## Örnek 19

Aynı durum `title` ve `releaseYear` için de oluşur.

Böylece aynı kurallar farklı Controller'larda tekrar etmeye başlar:

```text
POST /movies
↓
title kontrolü
releaseYear kontrolü
voteAverage kontrolü

PATCH /movies/:movieId
↓
title kontrolü
releaseYear kontrolü
voteAverage kontrolü
```

## Örnek 20

Bir süre sonra Controller yalnızca:

```text
film oluştur
film güncelle
```

## Örnek 21

işini anlatmaz; validation ayrıntılarıyla da dolmaya başlar.

Yeni bir kural eklediğimizi düşünelim:

```text
title en fazla 120 karakter olsun
```

## Örnek 22

Bu kuralı kullanan her yerde ilgili `if` bloğunu bulup değiştirmemiz gerekir. Bir yerde unutursak aynı alan farklı endpoint'lerde farklı davranabilir.

Sorun artık tek bir `if` değildir. Sorun, **aynı veri sözleşmesinin birçok koşul bloğuna dağılmasıdır**.

İhtiyacımız şu hâle gelir:

```text
Request Body kuralları
↓
tek yerde tanımlansın
↓
ayrı ayrı if bloklarına dağılmasın
↓
aynı kurallar tekrar kullanılabilsin
↓
validation sonucu düzenli biçimde alınabilsin
```

## Örnek 23

İşte Joi bu ihtiyaca cevap verir.

Joi ile kuralları tek tek `if` blokları olarak yazmak yerine, verinin nasıl görünmesi gerektiğini bir **schema** ile tarif ederiz.

Örneğin birazdan yazacağımız yapı kavramsal olarak şunu söyleyecek:

```text
createMovieSchema
↓
title nasıl olmalı?
releaseYear nasıl olmalı?
voteAverage nasıl olmalı?
```

## Örnek 24

Sonra gelen `req.body` bu schema'ya göre tek adımda doğrulanabilir.

Böylece Joi, daha önce yapabildiğimiz bir kontrolü sihirli biçimde mümkün hâle getirmez. **Dağılmaya başlayan validation kurallarını daha okunabilir, tekrar kullanılabilir ve merkezi bir yapıya taşır.**

---

## Request Doğrulaması Akışta Nereye Oturacak?

Joi eklendiğinde akışımız şu hâle gelir:

```text
HTTP Request
↓
Request Body doğrulaması
↓
geçersizse 400 Bad Request
↓
geçerliyse Controller'ın kayıt işlemi
↓
Mongoose validation
↓
MongoDB
```

## Örnek 25

Burada önemli nokta şudur: Joi, Mongoose'un yerine geçmez. Request sınırındaki kuralları düzenli biçimde yönetir.

---

## Joi Geldiğinde Mongoose Validation Neden Hâlâ Kalıyor?

Joi'yi eklememizin nedeni Mongoose validation'ı kaldırmak değildir.

Bir Request Joi kontrolünü geçtikten sonra uygulama içinde başka bir kod yolu da Movie Document oluşturabilir veya güncelleyebilir. Bu nedenle Model kendi kurallarını korumaya devam etmelidir.

Akış iki ayrı sınırda korunur:

```text
HTTP Request
↓
Joi
API'nin kabul ettiği veri biçimi
↓
Controller / uygulama kodu
↓
Mongoose
Movie Document'ın Schema kuralları
↓
MongoDB
```

## Örnek 26

Örneğin `voteAverage` için `0–10` kuralı iki yerde benzer görünse de amacı farklıdır: Joi hatalı Request'i API girişinde reddeder; Mongoose ise hangi kod yolundan gelirse gelsin geçersiz bir Movie Document'ın kaydedilmesini engeller.

---

## Joi'yi Projeye Ekleyelim

Terminalde proje klasöründe:

```bash
npm install joi
```

## Örnek 27

çalıştıralım.

Ardından Joi'yi ES Module yapımızda:

```js
import Joi from "joi";
```

## Örnek 28

şeklinde kullanabiliriz.

Yeni paket:

```text
package.json
package-lock.json
```

## Örnek 29

dosyalarımıza da kaydedilir.

---

## İlk Joi Schema'mızı Doğrudan Movie Üzerinden Yazalım

Yeni dosyamız:

```text
src/validation/movieSchemas.js
```

## Örnek 30

olsun.

POST Request'i için:

```js
import Joi from "joi";

export const createMovieSchema = Joi.object({
  title: Joi.string()
    .pattern(/\S/)
    .required(),

  releaseYear: Joi.number()
    .integer()
    .min(1888),

  voteAverage: Joi.number()
    .min(0)
    .max(10),
});
```

## Örnek 31

Önce dış yapıyı okuyalım:

```js
Joi.object({
  ...
})
```

## Örnek 32

şunu söyler:

```text
doğrulayacağımız değer
↓
bir object olmalı
↓
object içindeki alanların kuralları burada tanımlanacak
```

## Örnek 33

Bizim örneğimizde bu object, `req.body` içindeki Movie verisidir.

İçerideki:

```text
title
releaseYear
voteAverage
```

## Örnek 34

key'lerinin her biri için ayrı Joi kuralı yazıyoruz.

Bu kodu ezberlemeye çalışmayalım.

Biz az önce bir Request sözleşmesi yazmıştık.

Şimdi onun Joi karşılığını görüyoruz.

### `title`

```js
title: Joi.string()
  .pattern(/\S/)
  .required(),
```

## Örnek 35

şunu söyler:

```text
title
↓
String olmalı
↓
içinde en az bir boşluk olmayan karakter bulunmalı
↓
alan mutlaka gönderilmeli
```

## Örnek 36

Buradaki yeni kısım:

```js
.pattern(/\S/)
```

## Örnek 37

ifadesidir.

`pattern()` bir String'in verdiğimiz **regular expression**, yani düzenli ifade desenine uyup uymadığını kontrol eder.

51. bölümde MongoDB'nin `$regex` operatörüyle düzenli ifadelerden yararlanmıştık. Burada ise MongoDB Query yazmıyoruz; JavaScript tarafında bir regular expression veriyoruz.

Şu yazım:

```js
/\S/
```

## Örnek 38

bir JavaScript regular expression literal'ıdır.

İçindeki:

```text
\S
```

## Örnek 39

şu anlama gelir:

> boşluk, tab veya satır sonu gibi whitespace karakterleri **dışındaki** herhangi bir karakter

Dolayısıyla Joi şu soruyu sorar:

```text
title içinde
↓
en az bir whitespace olmayan karakter var mı?
```

## Örnek 40

Örneğin:

```text
"   "
↓
sadece whitespace
↓
pattern eşleşmez
```

## Örnek 41

Ama:

```text
"Dune"
↓
D, u, n, e whitespace değildir
↓
pattern eşleşir
```

## Örnek 42

Önemli bir ayrıntı: `.pattern(/\S/)` String'i temizlemez veya başındaki/sonundaki boşlukları silmez. Yalnızca en az bir uygun karakter bulunup bulunmadığını kontrol eder.

Bu nedenle:

```json
{
  "title": "   "
}
```

## Örnek 43

geçmez.

Ama:

```json
{
  "title": "Dune"
}
```

## Örnek 44

geçebilir.

### `releaseYear`

```js
releaseYear: Joi.number()
  .integer()
  .min(1888),
```

## Örnek 45

şunu söyler:

```text
Number
↓
tam sayı
↓
1888 veya daha büyük
```

## Örnek 46

### `voteAverage`

```js
voteAverage: Joi.number()
  .min(0)
  .max(10),
```

## Örnek 47

ise:

```text
0 <= voteAverage <= 10
```

## Örnek 48

kuralını oluşturur.

`8.3` gibi ondalıklı değerler geçerli olduğu için `.integer()` kullanmıyoruz.

`releaseYear` ve `voteAverage` alanlarını POST'ta zorunlu yapmadık.

Çünkü mevcut Movie Schema'mızda da yalnızca `title` zorunlu.

Request sözleşmemiz ile Mongoose Schema'mızın birbiriyle çelişmemesi önemlidir.

---

## Az Önceki Hatalı Body'yi Joi ile Deneyelim

Şu veriyi yeniden kullanalım:

```js
const body = {
  title: "   ",
  releaseYear: "2014",
  voteAverage: 8.7,
};
```

## Örnek 49

Doğrulama:

```js
const result = createMovieSchema.validate(
  body,
  {
    abortEarly: false,
    convert: false,
  },
);
```

## Örnek 50

Joi bize yalnızca:

```text
true / false
```

## Örnek 51

döndürmez.

Sonuçta özellikle iki bilgiyle ilgileniyoruz:

```js
const {
  error,
  value,
} = createMovieSchema.validate(
  body,
  {
    abortEarly: false,
    convert: false,
  },
);
```

## Örnek 52

Burada:

```text
error
↓
validation başarısızsa hata bilgisi
```

## Örnek 53



```text
value
↓
validation sonucundaki kullanılabilir değer
```

## Örnek 54

anlamına gelir.

Bizim örneğimizde `error` oluşur.

Çünkü:

```text
title = "   "
↓
pattern kuralından geçmez

releaseYear = "2014"
↓
String
↓
Number bekleniyor
```

## Örnek 55

Tam olarak istediğimiz şeyi yaptık:

```text
Request
↓
Controller'ın asıl işine geçmeden önce
↓
API sözleşmesine göre kontrol edildi
```

## Örnek 56

---

## `convert: false` Neden Özellikle Önemli?

Joi varsayılan davranışında bazı değerleri beklenen tipe dönüştürmeye çalışabilir.

Örneğin uygun bir Numeric String:

```text
"2014"
```

## Örnek 57

Number'a dönüştürülebilir.

Ama bizim bu API için verdiğimiz karar şu olsun:

> `releaseYear` JSON Body içinde gerçekten Number olarak gönderilmeli.

Bu nedenle:

```js
convert: false
```

## Örnek 58

kullanıyoruz.

Böylece:

```json
{
  "releaseYear": "2014"
}
```

## Örnek 59

ile:

```json
{
  "releaseYear": 2014
}
```

## Örnek 60

aynı şey sayılmaz.

Burada önemli olan `convert: false` seçeneğinin her projede zorunlu olması değildir.

Bu, bizim API sözleşmesi kararımızdır.

Başka bir API gelen değerleri dönüştürmeyi tercih edebilir.

---

## `abortEarly: false` Neden Kullanıyoruz?

Varsayılan davranışta Joi ilk validation hatasında durabilir.

Fakat kullanıcı şöyle bir Body gönderdiyse:

```json
{
  "title": "   ",
  "releaseYear": "abc",
  "voteAverage": 15
}
```

## Örnek 61

ona yalnızca ilk problemi söylemek yerine, Body'de bulduğumuz hataları birlikte göstermek daha kullanışlı olabilir.

Bu nedenle:

```js
abortEarly: false
```

## Örnek 62

kullanıyoruz.

Akış:

```text
ilk hata
↓
durma
↓
diğer alanları da kontrol et
↓
bulunan validation hatalarını birlikte döndür
```

## Örnek 63

olur.

---

## API'nin Tanımadığı Bir Alan Gönderilirse Ne Olacak?

Şu Request'i düşünelim:

```json
{
  "title": "Dune",
  "releaseYear": 2021,
  "voteAverage": 8.3,
  "isAdmin": true
}
```

## Örnek 64

Movie oluşturma endpoint'imizin:

```text
isAdmin
```

## Örnek 65

diye bir alanı yok.

`Joi.object({ ... })` içinde tanımlamadığımız alanları varsayılan olarak kabul etmiyoruz.

Bu nedenle bu Body validation'dan geçmez.

Bu bize önemli bir kontrol sağlar:

```text
Request Body
↓
yalnızca endpoint'in bildiği alanlar
```

## Örnek 66

Fakat yine de ileride göreceğimiz gibi, veritabanına gönderilecek alanları Controller tarafında açıkça seçme alışkanlığımızı bırakmayacağız.

Request validation ile field whitelist birbirini tamamlayan iki farklı kontroldür.

---

## Joi Hata Mesajlarını Nereden Getiriyor?

Şimdi önemli bir noktayı tamamlayalım.

Joi bir kural başarısız olduğunda yalnızca:

```text
hata var
```

## Örnek 67

demez.

Kendi **varsayılan hata mesajını** da üretir.

Örneğin şu alan hiç gönderilmezse:

```json
{}
```

## Örnek 68

`title` için Joi'nin varsayılan mesajı buna benzer olabilir:

```text
"title" is required
```

## Örnek 69

`releaseYear` String gönderilirse:

```json
{
  "title": "Interstellar",
  "releaseYear": "2014"
}
```

## Örnek 70

`convert: false` kullandığımız için buna benzer bir mesaj görebiliriz:

```text
"releaseYear" must be a number
```

## Örnek 71

`voteAverage` değeri 10'dan büyükse:

```text
"voteAverage" must be less than or equal to 10
```

## Örnek 72

gibi bir mesaj oluşabilir.

> Joi'nin tam varsayılan mesaj metni kullanılan kurala ve Joi sürümüne göre küçük farklılıklar gösterebilir. Burada önemli olan, mesajın Joi tarafından otomatik üretildiğini görmektir.

Yani şu ana kadar ayrıca mesaj yazmamış olsak bile Joi bize kullanılabilir bir hata açıklaması verir.

### Joi Hangi Kuralın Hata Verdiğini de Söyler

Joi'nin `error.details` içindeki her hata kaydında yalnızca `message` yoktur.

Hatanın türünü belirten bir:

```text
type
```

## Örnek 73

değeri de bulunur.

Örneğin `title` şu kurala takılırsa:

```js
.pattern(/\S/)
```

## Örnek 74

hata kaydında kabaca:

```js
{
  message: "...",
  path: ["title"],
  type: "string.pattern.base",
}
```

## Örnek 75

gibi bir bilgi görebiliriz.

Buradaki:

```text
string.pattern.base
```

## Örnek 76

Joi'nin bu validation hatası için kullandığı **hata kodudur**.

Başka örnekler:

```text
any.required
↓
zorunlu alan gönderilmedi

number.base
↓
Number beklenirken farklı tip geldi

number.integer
↓
Number geldi ama tam sayı değildi

number.min
↓
minimum sınırının altında kaldı

number.max
↓
maksimum sınırının üstüne çıktı

object.unknown
↓
Schema'da tanımlanmayan bir alan gönderildi
```

## Örnek 77

Bu kodlar birazdan kendi mesajlarımızı yazarken işimize yarayacak.

### Kendi Hata Mesajımızı Yazabilir miyiz?

Evet.

Örneğin `title` için Joi'nin İngilizce varsayılan mesajlarını kullanmak yerine API'mize daha uygun mesajlar yazmak isteyelim.

Bunun için:

```js
.messages({
  ...
})
```

## Örnek 78

kullanabiliriz.

Örneğin:

```js
title: Joi.string()
  .pattern(/\S/)
  .required()
  .messages({
    "string.base":
      "title String olmalıdır.",
    "string.pattern.base":
      "title yalnızca boşluklardan oluşamaz.",
    "any.required":
      "title zorunludur.",
  }),
```

## Örnek 79

Bunu şöyle okuyalım:

```text
string.base hatası oluşursa
↓
"title String olmalıdır."

string.pattern.base hatası oluşursa
↓
"title yalnızca boşluklardan oluşamaz."

any.required hatası oluşursa
↓
"title zorunludur."
```

## Örnek 80

Yani `.messages()` validation kuralını değiştirmez.

Şunu değiştirmez:

```text
hangi veri geçerli?
```

## Örnek 81

Yalnızca şunu özelleştirir:

```text
kural başarısız olduğunda hangi mesaj gösterilecek?
```

## Örnek 82

Aynı yaklaşımı diğer alanlarda da kullanabiliriz.

Örneğin:

```js
releaseYear: Joi.number()
  .integer()
  .min(1888)
  .messages({
    "number.base":
      "releaseYear Number olmalıdır.",
    "number.integer":
      "releaseYear tam sayı olmalıdır.",
    "number.min":
      "releaseYear 1888 veya daha büyük olmalıdır.",
  }),

voteAverage: Joi.number()
  .min(0)
  .max(10)
  .messages({
    "number.base":
      "voteAverage Number olmalıdır.",
    "number.min":
      "voteAverage 0 veya daha büyük olmalıdır.",
    "number.max":
      "voteAverage 10 veya daha küçük olmalıdır.",
  }),
```

## Örnek 83

Burada önemli olan bütün Joi mesajlarını mutlaka değiştirmek değildir.

İki yaklaşım da geçerlidir:

```text
Joi default mesajları
↓
hızlıca kullanılabilir

kendi .messages() tanımlarımız
↓
API dilimize ve sözleşmemize göre özelleştirilebilir
```

## Örnek 84

Movie API'mizde hata mesajlarını daha açık ve tutarlı tutmak için bu alanlarda kendi mesajlarımızı tanımlayacağız.

Böylece `src/validation/movieSchemas.js` içindeki POST Schema'mız artık şöyle olabilir:

```js
export const createMovieSchema = Joi.object({
  title: Joi.string()
    .pattern(/\S/)
    .required()
    .messages({
      "string.base":
        "title String olmalıdır.",
      "string.pattern.base":
        "title yalnızca boşluklardan oluşamaz.",
      "any.required":
        "title zorunludur.",
    }),

  releaseYear: Joi.number()
    .integer()
    .min(1888)
    .messages({
      "number.base":
        "releaseYear Number olmalıdır.",
      "number.integer":
        "releaseYear tam sayı olmalıdır.",
      "number.min":
        "releaseYear 1888 veya daha büyük olmalıdır.",
    }),

  voteAverage: Joi.number()
    .min(0)
    .max(10)
    .messages({
      "number.base":
        "voteAverage Number olmalıdır.",
      "number.min":
        "voteAverage 0 veya daha büyük olmalıdır.",
      "number.max":
        "voteAverage 10 veya daha küçük olmalıdır.",
    }),
});
```

## Örnek 85

Artık `error.details` içindeki `message` değeri ilgili kural için kendi yazdığımız mesaj olabilir.

---

## Şimdi Joi'yi Gerçek `createMovie` Controller'ında Kullanalım

Çalıştığımız dosya:

```text
src/controllers/movies.js
```

## Örnek 86

Önce Schema'yı import edelim:

```js
import {
  createMovieSchema,
} from "../validation/movieSchemas.js";
```

## Örnek 87

Ardından Controller'ın başında Body'yi doğrulayalım:

```js
export const createMovie = async (req, res) => {
  const {
    error,
    value,
  } = createMovieSchema.validate(
    req.body,
    {
      abortEarly: false,
      convert: false,
    },
  );

  if (error !== undefined) {
    return res.status(400).json({
      message: "Geçersiz Request Body.",
      errors: error.details.map(
        (detail) => detail.message,
      ),
    });
  }

  const {
    title,
    releaseYear,
    voteAverage,
  } = value;

  const movie = new Movie({
    title,
    releaseYear,
    voteAverage,
  });

  await movie.save();

  res.status(201).json({
    message: "Film oluşturuldu.",
    data: movie,
  });
};
```

## Örnek 88

### `error.details.map(...)` Ne Yapıyor?

Controller'daki şu bölüm ilk bakışta yoğun görünebilir:

```js
errors: error.details.map(
  (detail) => detail.message,
),
```

## Örnek 89

Joi validation başarısız olduğunda `error.details` içinde bulunan validation problemlerini bir liste hâlinde verir.

Kabaca şöyle düşünebiliriz:

```text
error.details
↓
[
  title hatası,
  releaseYear hatası,
  voteAverage hatası
]
```

## Örnek 90

Her hata bilgisinin içinde kullanıcıya gösterebileceğimiz bir:

```text
message
```

## Örnek 91

değeri bulunur.

40. bölümde `.map()` ile bir listedeki her elemandan istediğimiz bilgiyi seçmiştik. Burada aynı fikri kullanıyoruz:

```js
error.details.map(
  (detail) => detail.message,
)
```

## Örnek 92

şöyle okunabilir:

```text
her validation hatasını al
↓
onun message değerini seç
↓
yalnızca mesajlardan oluşan yeni listeyi oluştur
```

## Örnek 93

Yani Response'a Joi'nin bütün hata nesnesini değil, API istemcisinin doğrudan kullanabileceği anlaşılır validation mesajlarını gönderiyoruz.

Burada akış artık çok daha görünür:

```text
req.body
↓
Joi
↓
error var mı?
├── evet → 400
└── hayır → value
             ↓
             Movie oluştur
```

## Örnek 94

### Neden `req.body` Yerine `value` Kullanıyoruz?

Validation başarılı olduğunda Joi bize doğrulamadan geçmiş değeri `value` içinde verir.

Bu nedenle Controller'ın devamında:

```js
value
```

## Örnek 95

üzerinden çalışıyoruz.

Biz `convert: false` kullandığımız için bu bölümde Joi'nin tipi dönüştürmesini istemiyoruz.

Yine de kodun söylediği şey nettir:

```text
Controller'ın asıl işi
↓
validation'dan geçmiş veriyle devam et
```

## Örnek 96

---

## Postman'de Farkı Gerçekten Görelim

### 1. Geçerli Request

```http
POST http://localhost:3000/movies
```

## Örnek 97

Body:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "voteAverage": 8.7
}
```

## Örnek 98

Beklentimiz:

```text
Joi geçer
↓
Controller çalışır
↓
Mongoose validation çalışır
↓
film kaydedilir
↓
201 Created
```

## Örnek 99

### 2. `releaseYear` String Gönderelim

```json
{
  "title": "Interstellar",
  "releaseYear": "2014",
  "voteAverage": 8.7
}
```

## Örnek 100

Bu kez:

```text
convert: false
↓
"2014" String olarak kalır
↓
Joi Number bekler
↓
400 Bad Request
```

## Örnek 101

İşte Joi eklemeden önce Mongoose casting nedeniyle kolayca gözden kaçabilecek Request sözleşmesi farkını burada açıkça görüyoruz.

### 3. Birden Fazla Hata Gönderelim

```json
{
  "title": "   ",
  "releaseYear": "abc",
  "voteAverage": 15
}
```

## Örnek 102

`abortEarly: false` sayesinde Response'ta birden fazla validation problemi görebiliriz.

Bu noktada `title`, `releaseYear` ve `voteAverage` için `.messages()` ile yazdığımız kendi hata mesajlarımızın Response'a geldiğini de kontrol edin.

### 4. Tanımadığımız Alanı Gönderelim

```json
{
  "title": "Dune",
  "isAdmin": true
}
```

## Örnek 103

`isAdmin` Schema'da olmadığı için Request reddedilir.

Bu dört deneyi gerçekten yapmak, Joi'nin neden eklendiğini ezberlemekten daha değerlidir.

---

## PATCH Aynı Sözleşmeyi Kullanabilir mi?

Hayır.

`POST /movies` ile yeni bir Movie oluştururken `title` zorunlu.

Ama:

```http
PATCH /movies/:movieId
```

## Örnek 104

Request'inde yalnızca değiştirmek istediğimiz alanı gönderebilmeliyiz.

Örneğin:

```json
{
  "voteAverage": 9.1
}
```

## Örnek 105

geçerli olmalıdır.

Bu nedenle PATCH için ayrı bir Schema oluşturacağız.

Aynı dosyaya:

```js
export const updateMovieSchema = Joi.object({
  title: Joi.string()
    .pattern(/\S/)
    .messages({
      "string.base":
        "title String olmalıdır.",
      "string.pattern.base":
        "title yalnızca boşluklardan oluşamaz.",
    }),

  releaseYear: Joi.number()
    .integer()
    .min(1888)
    .messages({
      "number.base":
        "releaseYear Number olmalıdır.",
      "number.integer":
        "releaseYear tam sayı olmalıdır.",
      "number.min":
        "releaseYear 1888 veya daha büyük olmalıdır.",
    }),

  voteAverage: Joi.number()
    .min(0)
    .max(10)
    .messages({
      "number.base":
        "voteAverage Number olmalıdır.",
      "number.min":
        "voteAverage 0 veya daha büyük olmalıdır.",
      "number.max":
        "voteAverage 10 veya daha küçük olmalıdır.",
    }),
})
  .min(1)
  .messages({
    "object.min":
      "Güncellemek için en az bir alan gönderilmelidir.",
  });
```

## Örnek 106

ekleyelim.

Burada dikkat edin:

```js
.required()
```

## Örnek 107

yok.

Çünkü PATCH'te hangi alanın güncelleneceğini kullanıcı seçebilir.

Ama şu Body'yi de kabul etmek istemiyoruz:

```json
{}
```

## Örnek 108

Bu nedenle:

```js
.min(1)
```

## Örnek 109

kullanıyoruz.

Bunu tek başına:

```text
"en az bir desteklenen alan gönder"
```

## Örnek 110

diye okumak eksik olur.

Doğru düşünce şudur:

```text
.min(1)
↓
object içinde en az bir key olsun

Joi Schema
↓
tanımadığımız key'leri reddet
```

## Örnek 111

Bu iki davranış birlikte çalıştığında boş PATCH Body'si kabul edilmez ve yalnızca desteklediğimiz alanlarla çalışırız.

---

## PATCH Controller'da Ne Değişiyor?

`updateMovieById` Controller'ının başında:

```js
const {
  error,
  value,
} = updateMovieSchema.validate(
  req.body,
  {
    abortEarly: false,
    convert: false,
  },
);

if (error !== undefined) {
  return res.status(400).json({
    message: "Geçersiz Request Body.",
    errors: error.details.map(
      (detail) => detail.message,
    ),
  });
}
```

## Örnek 112

kullanabiliriz.

Ardından alanları:

```js
const {
  title,
  releaseYear,
  voteAverage,
} = value;
```

## Örnek 113

üzerinden alırız.

53. bölümdeki şu kontrol artık gereksiz hâle gelir:

```js
const hasUpdate =
  title !== undefined ||
  releaseYear !== undefined ||
  voteAverage !== undefined;
```

## Örnek 114

Çünkü boş Body'yi artık Joi Schema:

```js
.min(1)
```

## Örnek 115

ile reddediyor.

Fakat şu yapı kalır:

```js
const fieldsToSet = {};

if (title !== undefined) {
  fieldsToSet.title = title;
}

if (releaseYear !== undefined) {
  fieldsToSet.releaseYear = releaseYear;
}

if (voteAverage !== undefined) {
  fieldsToSet.voteAverage = voteAverage;
}
```

## Örnek 116

Neden?

Çünkü bu kodun görevi farklıdır:

```text
Joi
↓
Request Body geçerli mi?

fieldsToSet
↓
veritabanı update işlemine hangi alanlar gidecek?
```

## Örnek 117

Bir kontrol diğerinin yerine geçmiyor.

---

## Joi Ekledik, Mongoose Validation'ı Neden Hâlâ Tutuyoruz?

Şöyle düşünelim:

```text
HTTP Request
↓
Joi
↓
Controller
↓
Mongoose
↓
MongoDB
```

## Örnek 118

Joi ilk kapıdır.

Mongoose ise veritabanına yazılan Document için hâlâ kendi kurallarını uygular.

Örneğin PATCH tarafında 53. bölümden kalan:

```js
runValidators: true
```

## Örnek 119

seçeneğini kaldırmıyoruz.

Çünkü:

```text
Request validation var
↓
Mongoose validation gereksiz
```

## Örnek 120

sonucu doğru değildir.

Daha doğru mental model:

```text
Joi
↓
API'ye gelen veri

Mongoose
↓
veritabanına yazılan Document
```

## Örnek 121

şeklindedir.

---

## Bu Bölümde Bilerek Ne Yapmadık?

Şimdilik yalnızca:

```text
req.body
```

## Örnek 122

validation'ına odaklandık.

Henüz:

```text
req.params
req.query
```

## Örnek 123

için Joi Schema oluşturmadık.

Ayrıca validation kodunu Controller'dan çıkarmadık.

Bu bilinçli bir karar.

Çünkü önce şu kodu görmemiz gerekiyordu:

```js
createMovieSchema.validate(...)
```

## Örnek 124

ve gerçekten ne yaptığını anlamamız gerekiyordu.

Şimdi ise yeni bir problem ortaya çıktı.

`createMovie` Controller'ının başında:

```text
Joi validation
↓
hata kontrolü
↓
400 Response
```

## Örnek 125

 neredeyse aynı kod var.

Yani Joi problemini çözdük ama yeni bir tekrar oluşturduk.

Bu, 60. bölümün başlangıç noktası olacak.

---

## Bölüm Sonu Uygulaması

Bu bölümü bitirmeden aşağıdaki deneyleri gerçekten yapın:

1. `joi` paketini kurun ve `src/validation/movieSchemas.js` dosyasını oluşturun.
2. `createMovieSchema` ile geçerli bir POST Request gönderin.
3. `releaseYear` değerini `2014` yerine `"2014"` gönderin ve `convert: false` farkını görün.
4. `title` değerini yalnızca boşluklardan oluşturun ve Joi'nin Request'i reddettiğini görün.
5. Aynı anda üç hatalı alan gönderip `abortEarly: false` davranışını gözlemleyin.
6. Schema'da olmayan `isAdmin` alanını gönderin ve Request'in reddedildiğini görün.
7. `updateMovieSchema` ile yalnızca `voteAverage` gönderen PATCH Request'ini deneyin.
8. Boş `{}` PATCH Body'sinin `.min(1)` nedeniyle reddedildiğini görün.
9. `title` için `.messages()` içindeki bir hata mesajını geçici olarak değiştirin ve Postman Response'unda yeni metnin geldiğini görün.
10. Joi ekledikten sonra Mongoose validation ve `runValidators: true` kodlarını kaldırmadığınızdan emin olun.

---

## 🧠 Akılda Kalsın

Bu bölümün ana fikri Joi sözdizimi değildir.

Asıl fikir:

```text
HTTP Request
↓
veritabanı işlemine geçmeden önce
↓
API'nin kabul ettiği veri biçimine uyuyor mu?
```

## Örnek 126

sorusudur.

Joi ile:

```text
Request sözleşmesi
↓
kodla görünür hâle gelir
```

## Örnek 127

Joi validation başarısız olduğunda kendi default mesajını üretir.

İstersek:

```js
.messages({
  "hata.kodu": "Kendi mesajımız",
})
```

## Örnek 128

ile belirli hata kodlarının mesajlarını API dilimize göre değiştirebiliriz.

Mongoose ile ise:

```text
Document kuralları
↓
korunmaya devam eder
```

## Örnek 129

Bu yüzden:

```text
Joi ≠ Mongoose validation'ın yerine geçen şey
```

## Örnek 130

Daha doğru ifade:

```text
Joi
+
Mongoose validation
↓
farklı katmanlarda iki koruma
```

## Örnek 131

olmalıdır.

Ve artık şu Request'i neden erken reddetmek istediğimizi biliyoruz:

```json
{
  "title": "   ",
  "releaseYear": "2014",
  "voteAverage": 15
}
```

## Örnek 132

---

## Sonraki Adım

Joi çalışıyor.

Ama şimdi `createMovie` ve `updateMovieById` Controller'larının başına bakalım.

İkisinde de şu yapı tekrar ediyor:

```text
schema.validate(req.body)
↓
error kontrolü
↓
400 Response
```

## Örnek 133

Yani validation kuralını merkezi hâle getirdik ama validation **mekanizmasını** hâlâ Controller'lara dağıtıyoruz.

60. bölümde önce bu tekrarı gerçekten göreceğiz, sonra validation kodunu Controller ile Route arasına taşıyacağız:

```text
Request
↓
Validation Middleware
↓
Controller
```
