# Node.js ile Backend Geliştirme: Temelden Gerçek Bir API'ye

## 1. Kitap — Movie API kodları

Resmî depo: https://github.com/yasinbesni/Movie-API-Node.js-Backend-Book-1

Bu paket kitapla birlikte kullanılır. 01–70 numaralı bölüm klasörleri öğrenme sırasını korur. Çoğu `code/` klasörü yalnız ilgili bölümde değişen dosyaları içerir; bunlar ayrı uygulamalar değildir. Kodları tek bir çalışma kopyasında birleştirin. `kod_ornekleri.md` içindeki tasarım karşılaştırmaları, HTTP örnekleri ve fonksiyon parçaları da bağımsız çalıştırılacak dosyalar değildir.

## Terminal komutları için yardım

Bulunduğunuz klasörü kontrol etme, klasörler arasında geçiş yapma veya dosyalarla çalışma komutlarına ihtiyaç duyarsanız depo kökündeki **[TERMINAL_KOMUTLARI.md](TERMINAL_KOMUTLARI.md)** rehberine bakın. Rehber; Bash/Zsh, Windows PowerShell ve Komut İstemi karşılıklarının yanı sıra Node.js, npm, Git ve temel HTTP isteği örneklerini içerir.

## Tam projeyi çalıştırma

70. bölümün `code/movie-api/` klasörü birleştirilmiş final uygulamasıdır. Terminali burada açın. Node.js 22.12 veya üzeri gerekir; doğrulama Node.js 24 ile yapıldı.

```bash
npm ci --ignore-scripts
cp example.env .env
```

PowerShell kullanıyorsanız kopyalama komutu `Copy-Item example.env .env` olur. `.env` içindeki MongoDB adresini kendi deneme veritabanınızla değiştirin. Gizli değer içeren ayarlarda her değişken için ayrı bir değer üretin:

```bash
node -e "console.log(require('node:crypto').randomBytes(48).toString('hex'))"
npm start
```

Tek örnek ortam dosyası `example.env` dosyasıdır. Kendi ayarlarınız için bu dosyayı `.env` adıyla kopyalayın. Örnek dosyalar gerçek şifre içermez. Bir bölümde yalnız değişen dosyalar varsa o bölümün `example.env` dosyasını çalışma projenizin `package.json` dosyasının yanında kullanın.

## Kontrol

Depo kökünde `node tools/dogrula.mjs`, bölüm sayısını, JavaScript sözdizimini, tam projelerin importlarını ve örnek ortam ayarlarını denetler. Final proje klasöründe `npm test`, otomatik olarak ayrı ve geçici MongoDB replica set oluşturur; sizin MongoDB adresinizi kullanmaz. İlk testte MongoDB test ikilisi indirilir; internet erişimi gerekir. Bu test bağımlılıklarını canlı ortama yüklemek için kullanmayın; canlı kurulumda `npm ci --ignore-scripts --omit=dev` kullanılır.

Test kapsamı ve dış hizmet sınırları için `UYUMLULUK_RAPORU.md` dosyasına bakın.
