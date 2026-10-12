# 67. Bölüm — Postman örnek dosyaları

Bu klasör, kitabın 67. bölümünde adım adım oluşturulan Postman isteklerinin **isteğe bağlı kontrol kopyasını** içerir. Önce kitapla kendiniz kurabilir, sonra bu dosyaları karşılaştırma amacıyla içe aktarabilirsiniz.

- `Movie-API.postman_collection.json` — Kitaptaki GET, POST, GET by ID, PATCH, geçersiz POST ve DELETE testleri; ayrıca bölümde isteğe bağlı belirtilen silme sonrası 404 kontrolü.
- `Local.postman_environment.json` — Yalnızca `baseUrl = http://localhost:3000` değerini tanımlar.

## Kullanım

1. 70. bölümdeki `code/movie-api` uygulamasını kendi test veritabanınıza bağlayıp başlatın.
2. Postman'de **Import** ile iki JSON dosyasını içe aktarın.
3. `Local — Movie API (Kitap 67)` Environment'ını seçin.
4. İstekleri **yukarıdan aşağıya** Collection Runner ile çalıştırın. `POST Movie` yanıtından alınan `movieId` Collection içinde saklanır.
5. Runner bitince özellikle DELETE ve silme sonrası 404 kontrolünün geçtiğini doğrulayın.

**Dikkat:** Bu Collection kayıt oluşturur, günceller ve siler. Gerçek kullanıcıların verilerini içeren veritabanlarında veya canlı ortamda çalıştırmayın. Testleri yerel uygulama + ayrı test veritabanında yapın.

Collection, kitaptaki örnekleri göstermek için hazırlanmıştır; canlı MongoDB bağlantısıyla uçtan uca çalıştırıldığı iddia edilmez.
