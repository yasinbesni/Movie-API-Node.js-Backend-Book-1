# Kod uyumluluğu kontrolü

Bu dosya çalışma projesindeki teknik düzeltmeleri belgeler; kitap metninin bir parçası değildir.

## Düzeltilenler

- 13: geçersiz Express bağımlılık sürümü düzeltildi
- 29: index.js kitapta anlatılan proje köküne taşındı; ./src/ importları artık doğru
- 32: index.js kitapta anlatılan proje köküne taşındı; ./src/ importları artık doğru
- 33: index.js kitapta anlatılan proje köküne taşındı; ./src/ importları artık doğru
- 34: index.js kitapta anlatılan proje köküne taşındı; ./src/ importları artık doğru
- 35: index.js kitapta anlatılan proje köküne taşındı; ./src/ importları artık doğru
- 36: index.js kitapta anlatılan proje köküne taşındı; ./src/ importları artık doğru

## Bölüm klasörlerinin kullanımı

Bölümlerin `code/` dizinlerinin büyük bir kısmı *yalnızca değişen dosyaları* içerir. Bu klasörlerde başka bir dosyanın import edilmesi, dosyanın depoda hiç bulunmadığı anlamına gelmez; önceki bölümde geliştirdiğiniz projede bulunması beklenir. Bu örnekler doğrudan tek başına çalıştırılacak şekilde tasarlanmamıştır.

29–36. bölümlerde `index.js` proje kökünde; `src/` klasörü içindeki MongoDB bağlantısı ve model dosyaları ise önceki bölümlerden oluşturulan çalışma projesindedir.

Final sürümün bağımsız örneği `08_Test_Documentation_Deployment/70_Final_Proje_Movie_API_yi_Bastan_Sona_Nasil_Tamamlariz/code/movie-api/` altındadır. Burada `npm install`, örnek `.env` üzerinden yerel `.env` oluşturma ve `npm start` adımları uygulanır. Gerçek MongoDB bağlantı bilgilerini hiçbir zaman Git'e eklemeyin.

## Test kapsamı

Tüm JavaScript dosyaları `node --check` ile sözdizimi açısından kontrol edilmiştir. Harici MongoDB sunucusu gerektiren entegrasyon testleri bu çalışmada yapılmamıştır.
