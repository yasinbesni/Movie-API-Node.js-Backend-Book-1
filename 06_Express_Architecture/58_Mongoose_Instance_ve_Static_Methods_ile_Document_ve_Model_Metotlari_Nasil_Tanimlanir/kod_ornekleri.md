# Bölüm 58 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

# Mongoose Instance ve Static Methods ile Document ve Model Metotları Nasıl Tanımlanır?

## Virtual'dan Metotlara Geçiş

57. bölümde Director Document'ına şu şekilde erişebildiğimiz hesaplanan bir özellik ekledik:

```js
director.age;
```

## Örnek 2

`age` veritabanında saklanan bir alan değildi. `birthYear` üzerinden hesaplanan bir **Virtual** idi.

Şimdi benzer görünen ama farklı bir ihtiyacımız var.

Elimizde şöyle bir Movie Document'ı olduğunu düşünelim:

```text
title: Interstellar
voteAverage: 8.7
```

## Örnek 3

Uygulamamızda şu kural olsun:

```text
8 ve üzeri puan
↓
yüksek puanlı film
```

## Örnek 4

Bu kez bir değer üretip alan gibi okumak istemiyoruz. Movie'ye doğrudan şu soruyu sormak istiyoruz:

```js
movie.isHighRated();
```

## Örnek 5

Buradaki parantez önemlidir:

```text
director.age
↓
hesaplanan özellik
↓
Virtual
```

## Örnek 6



```text
movie.isHighRated()
↓
çağrılan davranış
↓
Instance Method
```

## Örnek 7

Önce tek bir Document'a ait bu davranışı oluşturalım.

---

## Tek Bir Movie Kendi Durumunu Cevaplasın

Şu anda aynı kararı Controller içinde doğrudan verebiliriz:

```js
const movie = await Movie.findById(
  movieId,
).exec();

const isHighRated =
  movie.voteAverage >= 8;
```

## Örnek 8

Bu kod yanlış değildir.

Eğer yalnızca bir yerde kullanılacaksa burada kalması da mümkündür.

Fakat aynı karar farklı yerlerde tekrar kullanılmaya başlarsa:

```text
film detay Controller'ı
öneri hazırlayan kod
raporlama işlemi
```

## Örnek 9

her yerin şu kuralı bilmesi gerekir:

```js
movie.voteAverage >= 8;
```

## Örnek 10

Aslında bu kodun cevapladığı soru Movie'nin kendi verisiyle ilgilidir:

> Bu Movie yüksek puanlı mı?

Bu davranışı Movie Document'ının kendisine taşıyabiliriz.

---

## İlk Instance Method'ımızı Yazalım

Çalıştığımız dosya:

```text
src/db/models/movie.js
```

## Örnek 11

Movie Schema oluşturulduktan sonra, Model oluşturulmadan önce şunu ekleyelim:

```js
movieSchema.methods.isHighRated = function () {
  if (typeof this.voteAverage !== "number") {
    return false;
  }

  return this.voteAverage >= 8;
};
```

## Örnek 12

Artık:

```js
const movie = await Movie.findById(
  movieId,
).exec();

const isHighRated = movie.isHighRated();
```

## Örnek 13

kullanabiliriz.

Interstellar'ın puanı `8.7` ise sonuç:

```text
true
```

## Örnek 14

olur.

Buradaki fark yalnızca kodun kısalması değildir.

Controller artık:

```text
yüksek puan sınırı kaç?
karşılaştırma nasıl yapılıyor?
```

## Örnek 15

sorularının ayrıntısını bilmek zorunda değildir.

Controller yalnızca:

```js
movie.isHighRated();
```

## Örnek 16

çağrısını yapar.

Yani davranış, ait olduğu Document seviyesine taşınmıştır.

---

## Instance Method İçindeki `this` Hangi Movie'yi Gösterir?

Şu çağrıyı düşünelim:

```js
const movie = await Movie.findById(
  interstellarId,
).exec();

movie.isHighRated();
```

## Örnek 17

Metot içindeki:

```js
this.voteAverage
```

## Örnek 18

şu anlama gelir:

```text
this
↓
metodun çağrıldığı Interstellar Document'ı

this.voteAverage
↓
8.7
```

## Örnek 19

Bunu görmek için metoda geçici olarak:

```js
console.log(this.title);
```

## Örnek 20

eklersek terminalde:

```text
Interstellar
```

## Örnek 21

görebiliriz.

Deneyden sonra `console.log()` satırını kaldırırız.

Şimdilik şu mental model yeterlidir:

```text
movie.isHighRated()
↓
this = movie
```

## Örnek 22

---

## Tek Bir Movie'den Model Seviyesine Geçelim

Instance Method tek bir Document için çalışıyor:

```js
movie.isHighRated();
```

## Örnek 23

Şimdi yönetim panelinde şu bilgiyi göstermek istediğimizi düşünelim:

> Veritabanında yüksek puanlı kaç film var?

Bu soruyu tek bir Movie Document'ı cevaplayamaz.

Bütün Movie collection'ı üzerinde sorgu yapmamız gerekir:

```js
const highRatedCount =
  await Movie.countDocuments({
    voteAverage: {
      $gte: 8,
    },
  }).exec();
```

## Örnek 24

Bu kod da geçerlidir.

Fakat bu sayım farklı yerlerde tekrar kullanılmaya başlarsa Model seviyesindeki davranışı tek bir isim altında toplamak isteyebiliriz:

```js
Movie.countHighRated();
```

## Örnek 25

Tek bir Document yerine Model üzerinde çağrılan bu tür metotlara **Static Method** denir.

---

## İlk Static Method'ımızı Yazalım

Yine:

```text
src/db/models/movie.js
```

## Örnek 26

dosyasındayız.

Schema üzerinde şu metodu tanımlayalım:

```js
movieSchema.statics.countHighRated = function () {
  return this.countDocuments({
    voteAverage: {
      $gte: 8,
    },
  });
};
```

## Örnek 27

Artık:

```js
const highRatedCount =
  await Movie.countHighRated().exec();
```

## Örnek 28

kullanabiliriz.

Örneğin koşulu karşılayan 12 film varsa sonuç:

```text
12
```

## Örnek 29

olur.

Burada da amaç yalnızca daha kısa kod yazmak değildir.

Şu ayrımı oluşturuyoruz:

```text
movie.isHighRated()
↓
tek bir Movie'nin kendi verisi hakkında karar
```

## Örnek 30



```text
Movie.countHighRated()
↓
Movie Model'ı üzerinden collection seviyesinde işlem
```

## Örnek 31

---

## Static Method İçindeki `this` Bu Kez Model'dır

Instance Method'ta:

```js
movie.isHighRated();
```

## Örnek 32

çağrısında:

```text
this = ilgili Movie Document'ı
```

## Örnek 33

olduğunu gördük.

Static Method'ta ise:

```js
Movie.countHighRated();
```

## Örnek 34

çağrısında:

```text
this = Movie Model'ı
```

## Örnek 35

olur.

Bu yüzden Static Method içinde:

```js
this.countDocuments(...)
```

## Örnek 36

kullanabiliyoruz.

İki çağrıyı yan yana okuyalım:

```text
movie.isHighRated()
↑
Document
↓
this = movie
```

## Örnek 37



```text
Movie.countHighRated()
↑
Model
↓
this = Movie
```

## Örnek 38

Bu, Instance Method ile Static Method arasındaki temel farktır.

---

## 54. Bölümdeki Query Helper Burada Nereye Oturuyor?

54. bölümde şuna benzer bir kullanım görmüştük:

```js
Movie.find(filter)
  .forMovieList();
```

## Örnek 39

Buradaki `forMovieList()` bir **Query Helper** idi.

Şimdi ise:

```js
Movie.countHighRated();
```

## Örnek 40

şeklinde bir **Static Method** kullanıyoruz.

İkisi de Schema üzerinden tanımlanabildiği için ilk bakışta benzer görünebilir. Fakat çağrıldıkları yer farklıdır.

Query Helper:

```text
Movie.find(filter)
↓
bir Query oluştu
↓
.forMovieList()
↓
mevcut Query'yi şekillendir
```

## Örnek 41

Static Method:

```text
Movie
↓
Model üzerinden metodu çağır
↓
metot kendi işlemini / sorgusunu başlatsın
```

## Örnek 42

Bu nedenle:

```js
Movie.find(filter).forMovieList();
```

## Örnek 43

şu soruya uygundur:

> Oluşturduğum Query'yi ortak bir kuralla nasıl şekillendireyim?

Ama:

```js
Movie.countHighRated();
```

## Örnek 44

şu soruya uygundur:

> Movie Model'ına ait, adı olan bir işlemi nasıl başlatayım?

Kısaca:

```text
Query Helper
↓
mevcut Query üzerinde çalışır
```

## Örnek 45



```text
Static Method
↓
Model üzerinde çağrılır
```

## Örnek 46

Böylece 54. bölümde öğrendiğimiz araçla yeni öğrendiğimiz Static Method'ın görevleri birbirine karışmaz.

---

## Aynı Sınır İki Metotta Tekrar Etmeye Başladı

Şimdi kodumuza bakalım.

Instance Method içinde:

```js
return this.voteAverage >= 8;
```

## Örnek 47

Static Method içinde de:

```js
$gte: 8
```

## Örnek 48

kullanıyoruz.

Artık gerçekten bir tekrar oluştu:

```text
8
↓
yüksek puan sınırı
↓
iki farklı davranış aynı değere bağlı
```

## Örnek 49

Bu kez `8` değerini tek bir yerde tutmak anlamlı hâle gelir.

48. bölümde pagination kararlarını `src/constants` altında toplamıştık. Aynı yaklaşımı burada da kullanabiliriz.

Şu dosyayı oluşturalım:

```text
src/constants/movie.js
```

## Örnek 50

ve içine:

```js
export const HIGH_RATING_THRESHOLD = 8;
```

## Örnek 51

yazalım.

Model dosyasında import edelim:

```js
import {
  HIGH_RATING_THRESHOLD,
} from "../../constants/movie.js";
```

## Örnek 52

Instance Method artık:

```js
movieSchema.methods.isHighRated = function () {
  if (typeof this.voteAverage !== "number") {
    return false;
  }

  return (
    this.voteAverage >=
    HIGH_RATING_THRESHOLD
  );
};
```

## Örnek 53

olur.

Static Method da aynı değeri kullanır:

```js
movieSchema.statics.countHighRated = function () {
  return this.countDocuments({
    voteAverage: {
      $gte: HIGH_RATING_THRESHOLD,
    },
  });
};
```

## Örnek 54

Burada artık üç ayrı sorumluluk nettir:

```text
HIGH_RATING_THRESHOLD
↓
ortak değeri tutar
```

## Örnek 55



```text
movie.isHighRated()
↓
tek bir Document hakkındaki kararı verir
```

## Örnek 56



```text
Movie.countHighRated()
↓
Model seviyesinde sayım yapar
```

## Örnek 57

Constant'ı bölümün başında tanıtmamamızın nedeni de budur: önce gerçekten tekrar eden değerin nerede ortaya çıktığını gördük, sonra onu merkezileştirdik.

---

## İki Metodu Controller'da Yan Yana Görelim

Tek bir Movie getirdiğimizi düşünelim:

```js
const movie = await Movie.findById(
  movieId,
).exec();
```

## Örnek 58

Bu Movie için:

```js
const isHighRated =
  movie.isHighRated();
```

## Örnek 59

kullanabiliriz.

Bütün Movie verisi için:

```js
const highRatedCount =
  await Movie.countHighRated().exec();
```

## Örnek 60

kullanabiliriz.

Yan yana:

```js
const movie = await Movie.findById(
  movieId,
).exec();

const isHighRated =
  movie.isHighRated();

const highRatedCount =
  await Movie.countHighRated().exec();
```

## Örnek 61

Şimdi çağrının sol tarafı bize hangi seviyede çalıştığımızı gösteriyor:

```text
movie.isHighRated()
↓
Document
```

## Örnek 62



```text
Movie.countHighRated()
↓
Model
```

## Örnek 63

---

## Kural 8'den 9'a Değişirse Ne Olur?

Mevcut sabit:

```js
export const HIGH_RATING_THRESHOLD = 8;
```

## Örnek 64

olsun.

Interstellar'ın puanı:

```text
8.7
```

## Örnek 65

olduğu için:

```js
movie.isHighRated();
```

## Örnek 66

şu sonucu verir:

```text
true
```

## Örnek 67

Diyelim ürün kararı değişti:

```text
artık 9 ve üzeri yüksek puanlı sayılsın
```

## Örnek 68

Yalnızca:

```js
export const HIGH_RATING_THRESHOLD = 9;
```

## Örnek 69

yaparız.

Controller'daki:

```js
movie.isHighRated();
```

## Örnek 70

ve:

```js
Movie.countHighRated();
```

## Örnek 71

çağrıları değişmez.

Interstellar için artık:

```text
8.7 >= 9
↓
false
```

## Örnek 72

sonucu oluşur.

Aynı zamanda `Movie.countHighRated()` da yeni sınırla sayım yapar.

Burada önemli olan yalnızca bir sayıyı tek yerde tutmak değildir. **Aynı iş kuralını kullanan Document ve Model davranışlarının aynı kaynağa bağlanmasıdır.**

---

## Her İşlemi Method Yapmalı mıyız?

Hayır.

Bu bölümün amacı:

```text
her fonksiyonu Schema'ya taşı
```

## Örnek 73

demek değildir.

Örneğin daha önce oluşturduğumuz:

```js
parsePaginationParams();
```

## Örnek 74

Request'in pagination değerlerini hazırlıyor.

Bu işlem:

```text
tek bir Movie Document'ına ait değil
Movie Model'ının veritabanı işlemi de değil
```

## Örnek 75

Bu nedenle Movie Schema içine taşımak anlamsız olur.

Aynı şekilde:

```js
calculatePaginationData();
```

## Örnek 76

pagination hesabıdır.

Ama:

```js
movie.isHighRated();
```

## Örnek 77

bir Movie'nin kendi verisiyle cevap verdiği bir sorudur.

Ve:

```js
Movie.countHighRated();
```

## Örnek 78

Movie Model'ı üzerinden collection seviyesinde yapılan bir işlemdir.

Kendimize şu soruyu sorabiliriz:

```text
Bu işlem tek bir Movie'nin kendi verisine mi ait?
↓
Instance Method düşünülebilir
```

## Örnek 79



```text
Bu işlem Movie Model'ı üzerinden collection seviyesinde mi çalışıyor?
↓
Static Method düşünülebilir
```

## Örnek 80



```text
Bu işlem Request, pagination veya başka bir uygulama sorumluluğu mu?
↓
Schema dışında kalması daha doğru olabilir
```

## Örnek 81

---

## Virtual, Instance Method, Static Method ve Query Helper Nerede Çalışır?

Bu dört Schema aracını, hangi nesne üzerinde kullanıldıklarına göre tek yerde toparlayalım.

57. bölümde:

```js
director.age;
```

## Örnek 82

kullanmıştık.

Bu bir metot çağrısı değildir.

`age`, hesaplanan bir Document özelliğidir:

```text
Virtual
↓
özellik gibi okunur
```

## Örnek 83

58. bölümde:

```js
movie.isHighRated();
```

## Örnek 84

kullanıyoruz:

```text
Instance Method
↓
Document üzerinde çağrılır
```

## Örnek 85

54. bölümde ise:

```js
Movie.find(filter)
  .forMovieList();
```

## Örnek 86

kullanmıştık:

```text
Query Helper
↓
Query üzerinde çağrılır
```

## Örnek 87

Şimdi dördünü yan yana görelim:

```text
director.age
↓
Virtual
```

## Örnek 88



```text
movie.isHighRated()
↓
Instance Method
```

## Örnek 89



```text
Movie.countHighRated()
↓
Static Method
```

## Örnek 90



```text
Movie.find(filter).forMovieList()
↓
Query Helper
```

## Örnek 91

Bu kadar ayrım şimdilik yeterlidir.

---

## Uygularken Bilmemiz Gereken İki Kısa Teknik Nokta

### `this` Kullanıyorsak Arrow Function Kullanmayalım

Instance Method'ta:

```js
this.voteAverage
```

## Örnek 92

Static Method'ta:

```js
this.countDocuments(...)
```

## Örnek 93

kullanıyoruz.

Bu nedenle normal `function` kullanıyoruz:

```js
movieSchema.methods.isHighRated = function () {
  ...
};
```

## Örnek 94



```js
movieSchema.statics.countHighRated = function () {
  ...
};
```

## Örnek 95

Arrow function burada ihtiyaç duyduğumuz Mongoose `this` bağlamını vermez.

### `lean()` Sonucunda Instance Method Beklemeyelim

57. bölümden biliyoruz:

```text
lean()
↓
sade JavaScript object
```

## Örnek 96

Bu nedenle normal Query ile gelen Mongoose Document üzerinde:

```js
movie.isHighRated();
```

## Örnek 97

çalışabilir.

Ama Query sonucunu `lean()` ile sade object'e dönüştürürsek Instance Method o object üzerinde bulunmaz.

Bu bölümde `lean()`ı yeniden öğrenmiyoruz; yalnızca daha önce öğrendiğimiz farkı yeni metoda uyguluyoruz.

---

## Bölüm Sonu Uygulaması

Bu bölümü gerçek Movie verisiyle tamamlayalım.

### 1. Önce Document Seviyesindeki İhtiyacı Görün

Varlığını bildiğiniz bir Movie Document'ını alın:

```js
const movie = await Movie.findById(
  movieId,
).exec();
```

## Örnek 98

Önce doğrudan:

```js
movie.voteAverage >= 8;
```

## Örnek 99

kontrolünü yapın.

### 2. Aynı Kararı Instance Method'a Taşıyın

`movieSchema.methods.isHighRated` metodunu ekleyin ve:

```js
movie.isHighRated();
```

## Örnek 100

çağrısını deneyin.

### 3. Model Seviyesinde Bir Static Method Ekleyin

`movieSchema.statics.countHighRated` metodunu oluşturun.

Ardından:

```js
await Movie.countHighRated().exec();
```

## Örnek 101

ile sonucu görün.

### 4. Ortak Sınırı Tek Yere Taşıyın

Şu dosyayı oluşturun:

```text
src/constants/movie.js
```

## Örnek 102

ve:

```js
export const HIGH_RATING_THRESHOLD = 8;
```

## Örnek 103

tanımlayın.

Hem Instance Method hem de Static Method bu sabiti kullansın.

### 5. `8 → 9` Deneyini Yapın

Yalnızca:

```js
HIGH_RATING_THRESHOLD
```

## Örnek 104

değerini `9` yapın.

Hem:

```js
movie.isHighRated();
```

## Örnek 105

hem de:

```js
Movie.countHighRated();
```

## Örnek 106

sonucunun yeni kurala göre değiştiğini doğrulayın.

### 6. Dört Yapının Farkını Kendi Cümlenizle Açıklayın

Şunların hangi seviyede çalıştığını açıklayın:

```text
Virtual
Instance Method
Static Method
Query Helper
```

## Örnek 107

Özellikle şu iki çağrının neden farklı araçlara ait olduğunu söyleyebilmelisiniz:

```js
Movie.find(filter).forMovieList();
```

## Örnek 108



```js
Movie.countHighRated();
```

## Örnek 109

---

## 🧠 Akılda Kalsın

Bu bölümde iki yeni davranış türü öğrendik:

```text
movie.isHighRated()
↓
Document üzerinde çağrılır
↓
Instance Method
```

## Örnek 110



```text
Movie.countHighRated()
↓
Model üzerinde çağrılır
↓
Static Method
```

## Örnek 111

Bunları daha önce öğrendiğimiz yapılarla birlikte düşünürsek:

```text
director.age
↓
Virtual
↓
hesaplanan özellik
```

## Örnek 112



```text
Movie.find(filter).forMovieList()
↓
Query Helper
↓
mevcut Query'yi şekillendirir
```

## Örnek 113

Ortak iş kuralındaki `8` değeri ise:

```js
HIGH_RATING_THRESHOLD
```

## Örnek 114

ile tek yerde tutulur. Constant'ın görevi davranış eklemek değil, ortak değerin tek kaynaktan yönetilmesini sağlamaktır.

Bir yapıyı yalnızca kodu kısaltmak için seçmiyoruz. Önce işlemin hangi seviyeye ait olduğunu soruyoruz:

```text
hesaplanan Document özelliği mi?
↓
Virtual

tek bir Document davranışı mı?
↓
Instance Method

Model seviyesinde bir işlem mi?
↓
Static Method

mevcut Query'yi tekrar kullanılabilir biçimde şekillendiren davranış mı?
↓
Query Helper
```

## Örnek 115

---

## Sonraki Adım

Bu bölümle Mongoose tarafında kullandığımız yapıların görevleri daha net bir tabloya oturdu:

```text
Virtual
↓
Document üzerinde hesaplanan özellik

Instance Method
↓
tek bir Document'a ait davranış

Static Method
↓
Model seviyesindeki davranış

Query Helper
↓
mevcut Query'yi şekillendiren davranış
```

## Örnek 116

Böylece 54–58 arasındaki Mongoose hattını tamamlamış olduk.

Şimdi odağı veritabanı/model katmanından API'ye gelen **Request** tarafına çevireceğiz.

Kullanıcı şu Body'yi gönderebilir:

```json
{
  "title": "",
  "releaseYear": "abc",
  "voteAverage": 15
}
```

## Örnek 117

Mongoose validation veritabanına yazılacak Document'ı korumaya devam eder. Fakat Request'in Controller'ın ana iş mantığına ulaşmadan önce API sözleşmesine uygun olup olmadığını da kontrol etmek isteyebiliriz.

Yeni soru şudur:

```text
HTTP Request
↓
Controller'a geçmeden önce
↓
Body beklediğimiz biçimde mi?
```
