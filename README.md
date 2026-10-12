# Backend Book — Movie API Kodları

Bu repository, backend kitabını okurken bölüm sonundaki kod durumunu hızlıca kontrol edebilmeniz için hazırlanmıştır.

## Nasıl Kullanılır?

1. Kitaptaki bölümü okuyun ve kodu önce kendiniz yazın.
2. Aynı numaralı bölüm klasörünü açın.
3. Bölümde kalıcı bir kod değişikliği varsa `code/` klasöründeki dosyalarla kendi kodunuzu karşılaştırın.
4. Sonra bir sonraki bölüme geçin.

`code/` klasörü kitaptaki her ara kod bloğunu içermez. **Yalnızca ilgili bölüm tamamlandığında eklenen veya değiştirilen kodun son hâlini** gösterir.

Kod değişikliği olmayan bölümlerde yalnızca `README.md` bulunur. Bu bilinçli bir tercihtir; kitapta zaten açıklanan örnekleri burada tekrar etmiyoruz.

## Klasör Yapısı

Repository, kitabın sekiz ana kısmıyla aynı yapıyı kullanır ve bölüm klasörleri `01`–`70` numaralarıyla eşleşir.

Örnek:

```text
07_Professional_API/
└── 59_Joi_ile_Request_Verisi_Neden_Dogrulanir/
    ├── README.md
    └── code/
        ├── package.json
        └── src/
            ├── controllers/
            └── validation/
```

Bu örnekte `code/`, 59. bölüm sonunda değişen dosyaların son hâlini gösterir.

## Çalıştırma Notu

Bir bölüm klasöründeki `code/` her zaman tek başına çalıştırılabilir tam proje değildir; çoğu bölüm yalnızca o bölümde değişen dosyaları içerir. Kitabın sonunda, 70. bölümde `code/movie-api/` altında final proje hâli bulunur.

Gerçek `.env` dosyaları ve `node_modules` repository'ye eklenmez. Gerekli yerlerde `.env.example` kullanılır.

## 67. Bölüm — Postman kontrol kopyası

Kitapta birlikte oluşturulan Collection ve yerel Environment'ın örnek JSON dosyaları, `08_Test_Documentation_Deployment/67_Postman_Collection_Environment_ve_Tests_ile_API_Nasil_Kontrol_Edilir/postman/` klasöründedir. Bunlar **opsiyoneldir**; okuyucu önce testleri kitapla kurabilir. **Yalnızca ayrı test veritabanında kullanın** (POST/PATCH/DELETE veriyi değiştirir).
