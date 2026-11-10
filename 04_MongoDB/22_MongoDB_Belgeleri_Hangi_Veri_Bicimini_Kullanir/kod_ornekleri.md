# Bölüm 22 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# MongoDB Belgeleri Hangi Veri Biçimini Kullanır?

## JSON'a Benzeyen Belge Gerçekten JSON mu?

Bir önceki bölümde MongoDB'nin **belge tabanlı bir NoSQL veritabanı** olduğunu gördük. Bir filmi de şu şekilde temsil etmiştik:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7
}
```

## Örnek 2

Bu yapı bize tanıdık geliyor. Çünkü alan ve değerlerden oluşuyor ve görünüş olarak JSON'a çok benziyor.

Burada önemli bir soru ortaya çıkıyor:

> MongoDB bu belgeyi gerçekten bir `.json` dosyasındaki metin gibi mi saklıyor?

Hayır. MongoDB belgeleri, uygulama tarafında JSON'a benzeyen bir yapıyla görsek de, veriyi **BSON** adı verilen ikili bir belge biçimiyle temsil eder.

Yani bu bölümde iki şeyi birbirinden ayıracağız:

```text
Bizim gördüğümüz okunabilir belge
↓
JSON'a benzeyen gösterim

MongoDB'nin kullandığı belge biçimi
↓
BSON
```

## Örnek 3

BSON'u anlamanın en kolay yolu, önce JSON'un bu akıştaki görevini netleştirmektir.

---

## Önce JSON'un Rolünü Netleştirelim

18. bölümde Request Body ile veri gönderirken JSON kullanmıştık. Yukarıdaki film örneği de bize bu yapıyı hatırlatıyor.

JSON, veriyi **metin tabanlı ve taşınabilir bir biçimde** temsil etmemizi sağlar. Bu nedenle özellikle istemci ile sunucu arasında veya birbirinden farklı uygulamalar arasında veri taşırken çok yaygın kullanılır.

Fakat burada görev değişiyor.

Bir API'nin veriyi taşımasıyla, bir veritabanının o veriyi kendi içinde saklaması aynı ihtiyaç değildir. Veritabanı tarafında yalnızca değerleri değil, bazı durumlarda o değerlerin **daha ayrıntılı tür bilgisini** de korumak isteriz.

MongoDB'nin BSON kullanmasının temel nedenlerinden biri tam olarak burada ortaya çıkar.

---

## BSON Nedir?

**BSON**, MongoDB belgelerini ikili biçimde temsil etmek için kullanılan bir veri formatıdır.

Adı:

```text
Binary JSON
```

## Örnek 4

ifadesinden gelir.

Başlangıç düzeyinde şu karşılaştırma yeterlidir:

```text
JSON
↓
Metin tabanlı veri gösterimi

BSON
↓
JSON'a benzeyen belgelerin
ikili biçimde temsil edilmesi
```

## Örnek 5

Buradaki en önemli ifade **JSON'a benzeyen** kısmıdır. Çünkü BSON, JSON'un birebir kopyası değildir.

JSON'daki `string`, `number`, `boolean`, `array`, `object` ve `null` gibi temel yapıları karşılayabilir; buna ek olarak MongoDB'nin ihtiyaç duyduğu başka veri türlerini de taşıyabilir.

🧠 Akılda Kalsın

```text
BSON
≠
JSON'un birebir kopyası

BSON
=
MongoDB belgelerini temsil etmek için
kullanılan ikili belge formatı
```

## Örnek 6

Peki burada geçen **binary** kelimesi tam olarak ne anlatıyor?

---

## “Binary” Ne Demek?

JSON'u açtığımızda doğrudan okuyabileceğimiz bir metin görürüz:

```json
{
  "title": "Interstellar"
}
```

## Örnek 7

BSON ise insanların elle okuyup yazması için tasarlanmış bir metin biçimi değildir. Belgedeki değerler ve onların tür bilgileri, bilgisayarın işleyebileceği ikili bir kodlama içinde tutulur.

Bu nedenle MongoDB ile çalışırken genellikle ham BSON verisini ekranda görmeyiz. Araçlar bize yine yukarıdaki gibi okunabilir, JSON'a benzeyen bir gösterim sunar; arka planda ise belge BSON biçiminde temsil edilir.

```text
Bizim çalıştığımız görünüm
↓
JSON'a benzeyen belge

MongoDB'nin kullandığı temsil
↓
BSON
```

## Örnek 8

Burada önemli olan nokta şudur: BSON'un amacı bize farklı bir yazım dili öğretmek değildir. Amaç, MongoDB'nin belgeleri ihtiyaç duyduğu tür bilgileriyle birlikte temsil edebilmesidir.

![JSON ve BSON karşılaştırması](images/22-json-bson-baski.svg)

Bu da bizi asıl soruya götürür: MongoDB neden yalnızca JSON ile yetinmiyor?

---

## MongoDB Neden BSON Kullanır?

JSON birçok uygulama için yeterli veri türleri sunar. Temel olarak şu türlerle çalışabiliriz:

```text
string
number
boolean
null
array
object
```

## Örnek 9

Örneğin:

```json
{
  "title": "Interstellar",
  "releaseYear": 2014,
  "rating": 8.7,
  "isPublished": true,
  "genres": ["Science Fiction", "Drama"]
}
```

## Örnek 10

Burada:

```text
"Interstellar"
↓
string

2014 ve 8.7
↓
number

true
↓
boolean

[...]
↓
array
```

## Örnek 11

olarak düşünülebilir.

Fakat veritabanında bazı değerleri daha ayrıntılı türlerle saklamak isteyebiliriz. BSON bu noktada JSON'dan daha fazla veri türünü temsil edebilir.

Örneğin MongoDB dünyasında şu türlerle karşılaşabiliriz:

```text
Date
ObjectId
Binary
Decimal128
farklı tam sayı türleri
```

## Örnek 12

Bunların tamamını şu anda öğrenmemiz gerekmiyor. Şimdilik kurulması gereken bağlantı şudur:

```text
JSON
↓
Temel ve taşınabilir veri türleri

BSON
↓
MongoDB'nin ihtiyaç duyduğu
daha zengin tür bilgisi
```

## Örnek 13

Bu farkı en kolay bir tarih değeri üzerinden görebiliriz.

---

## Tarih Örneğiyle Farkı Görelim

JSON içinde şöyle bir değer yazalım:

```json
{
  "releaseDate": "2014-11-07"
}
```

## Örnek 14

İnsan olarak bu değerin bir tarihi temsil ettiğini anlayabiliriz. Fakat JSON açısından:

```text
"2014-11-07"
```

## Örnek 15

tırnak içinde olduğu için bir `string`, yani metindir.

Bir veritabanı açısından ise bazen yalnızca:

```text
Bu değer "2014-11-07" yazısını içeriyor.
```

## Örnek 16

bilgisi yeterli değildir. Sistemin ayrıca:

```text
Bu değer bir tarihtir.
```

## Örnek 17

bilgisini de taşımasını isteyebiliriz.

MongoDB, BSON sayesinde bir değeri `Date` türü olarak temsil edebilir.

Düşünce farkı şöyledir:

```text
"2014-11-07"
↓
Metin değeri

Date
↓
Tarih türü taşıyan değer
```

## Örnek 18

İşte BSON'un sağladığı önemli avantajlardan biri budur: yalnızca değeri değil, MongoDB'nin ihtiyaç duyduğu daha ayrıntılı **tür bilgisini** de koruyabilir.

Aynı mantık ileride karşımıza çıkacak `ObjectId` gibi özel MongoDB türleri için de geçerlidir.

Bu noktada üç benzer görünen kavramı birbirinden ayırmak gerekir.

---

## JavaScript Object, JSON ve BSON Arasındaki Fark

Aynı film verisi uygulamanın farklı noktalarında farklı biçimlerde karşımıza çıkabilir.

```text
JavaScript Object
↓
Node.js uygulamasının belleğinde kullandığımız veri yapısı

JSON
↓
veriyi metin biçiminde taşımak ve göstermek için kullanılan format

BSON
↓
MongoDB belgelerinin veritabanında temsil edildiği ikili format
```

## Örnek 19

Örneğin Postman'dan gelen JSON Request Body, Express tarafından JavaScript nesnesi olarak kullanılır; MongoDB tarafındaki belge ise BSON biçiminde temsil edilir. Bu dönüşümlerin büyük bölümünü kullandığımız araçlar bizim için yönetir.

## Biz BSON Yazacak mıyız?

Genellikle hayır.

MongoDB kullanırken ham BSON verisini elle oluşturmayacağız. Node.js tarafında çoğunlukla bize tanıdık gelen JavaScript nesneleriyle çalışacağız.

Örneğin:

```js
const movie = {
  title: "Interstellar",
  releaseYear: 2014,
  rating: 8.7,
};
```

## Örnek 20

Bu veriyi MongoDB'ye gönderdiğimizde, MongoDB ile uygulamamız arasındaki bağlantıyı sağlayan sürücü veya daha sonra kullanacağımız araçlar gerekli dönüşümü yönetir.

Başlangıç düzeyinde akışı şöyle düşünebiliriz:

```text
JavaScript nesnesi
↓
MongoDB'ye gönderilir
↓
BSON biçimine dönüştürülür
↓
MongoDB belgesi olarak saklanır
```

## Örnek 21

Veri geri okunurken de ters yönde bir dönüşüm gerçekleşir ve uygulamamızın kullanabileceği değerlere ulaşırız.

Yani BSON'u bilmemiz gerekir; fakat günlük geliştirmede ham ikili BSON verisini elle yazmamız gerekmez.

Bu ayrım, bir başka yaygın yanlış anlamayı da düzeltmemizi sağlar.

---

## BSON Her Zaman JSON'dan Daha Küçük mü?

Hayır.

`Binary` kelimesini görünce BSON'un JSON'un sıkıştırılmış ve her zaman daha küçük hâli olduğunu düşünmek kolaydır. Fakat BSON'un temel amacı yalnızca dosya boyutunu küçültmek değildir.

BSON; değerlerin tür bilgisini taşıyabilmek ve belgenin bilgisayar tarafından işlenebilecek bir ikili biçimde temsil edilmesini sağlamak için tasarlanmıştır.

Bu nedenle bazı BSON belgeleri, aynı verinin JSON metniyle gösteriminden daha büyük olabilir.

🧠 Akılda Kalsın

```text
Binary
≠
Her zaman daha küçük

BSON
≠
Sıkıştırılmış JSON
```

## Örnek 22

Peki BSON ikili bir format ise MongoDB araçlarında neden hâlâ JSON'a benzeyen belgeler görüyoruz?

---

## MongoDB'de Gördüğümüz Veri Neden JSON'a Benziyor?

Çünkü geliştiricinin doğrudan ham BSON ile uğraşması kullanışlı değildir. MongoDB araçları ve sürücüler, belgeleri bizim okuyabileceğimiz daha anlaşılır gösterimlerle sunar.

Bu nedenle ekranda şuna benzer bir belge görebiliriz:

```json
{
  "title": "Interstellar",
  "rating": 8.7
}
```

## Örnek 23

Bu görünüm bize JSON'u hatırlatır. Fakat alttaki kavramsal ayrım değişmez:

```text
Ekranda gördüğümüz
↓
Okunabilir, JSON'a benzeyen belge gösterimi

MongoDB belgesinin veri biçimi
↓
BSON
```

## Örnek 24

Bu yüzden:

> MongoDB belgeleri JSON'a benziyor, öyleyse MongoDB veriyi yalnızca JSON metni olarak saklıyor.

sonucunu çıkarmamalıyız.

Artık bunun neden doğru olmadığını biliyoruz.

---

## BSON'u Bilmek Neden Önemli?

Bir backend geliştiricisi olarak her gün ham BSON verisini görmeyeceğiz. Buna rağmen BSON'u bilmek, ileride karşılaşacağımız MongoDB kavramlarını doğru yere oturtmamızı sağlar.

Örneğin:

```text
ObjectId
Date
Decimal128
Binary
```

## Örnek 25

gibi türleri gördüğümüzde:

> JSON'da böyle bir tür yoktu. Bu nereden geldi?

sorusu bizi şaşırtmaz.

Çünkü artık şu bağlantıyı kurabiliyoruz:

```text
MongoDB document'ı
↓
BSON ile temsil edilir
↓
BSON, JSON'dan daha fazla veri türünü
temsil edebilir
```

## Örnek 26

BSON bu nedenle ayrı bir ezber konusu değil; MongoDB'de birazdan öğreneceğimiz `ObjectId`, tarih alanları ve document yapısı için temel bir köprüdür.

---

## Bölüm Özeti

Bu bölümde önceki bölümde gördüğümüz JSON'a benzeyen MongoDB belgesinin gerçekte hangi veri biçimiyle temsil edildiğini netleştirdik.

JSON'un:

```text
metin tabanlı,
taşınabilir bir veri formatı
```

## Örnek 27

olduğunu hatırladık.

BSON'un ise:

```text
MongoDB belgelerini temsil etmek için
kullanılan ikili belge formatı
```

## Örnek 28

olduğunu öğrendik.

En önemli farklardan biri BSON'un şu türleri de temsil edebilmesidir:

```text
Date
ObjectId
Binary
Decimal128
farklı tam sayı türleri
```

## Örnek 29

Ayrıca üç kavramı birbirinden ayırdık:

```text
JavaScript Object
≠
JSON
≠
BSON
```

## Örnek 30

ve iki yaygın yanlış anlamayı düzelttik:

```text
BSON
≠
Sıkıştırılmış JSON

Binary
≠
Her zaman daha küçük
```

## Örnek 31

Artık MongoDB'de gördüğümüz belge yapısının neden JSON'a benzediğini, fakat neden yalnızca JSON metninden ibaret olmadığını biliyoruz.

---

## Sonraki Adım

Şimdi tek bir film belgesinin MongoDB tarafında BSON ile temsil edildiğini biliyoruz.

Örneğin Interstellar kaydı, bizim JSON'a benzeyen okunabilir bir yapıyla gördüğümüz tek bir document'tır.

Fakat gerçek bir uygulamada yalnızca bir document olmayacak:

```text
Interstellar
Inception
The Matrix
Arrival
...
```

## Örnek 32

Aynı amaca ait yüzlerce, hatta binlerce document oluşabilir.

Bu kez sorumuz veri biçimiyle ilgili değil, **düzenle** ilgilidir:

> MongoDB aynı türden document'ları nerede bir arada tutar?

Bu sorunun cevabı:

```text
Collection
```
