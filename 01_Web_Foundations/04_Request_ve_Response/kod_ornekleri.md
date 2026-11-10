# Bölüm 4 — Kitaptaki örnekler

Örnekler kitaptaki sırayla verilmiştir. Açıklama ve tasarım karşılaştırmalarını kalıcı uygulama dosyalarıyla karıştırmayın.

## Örnek 1

 hava durumu nedir?"

Bu mesaj sunucuya doğru yola çıkar.

İstemcinin sunucuya gönderdiği bu ilk mesaja **Request (İstek)** denir.

---

## Sunucu Ne Yapar?

Telefon isteği gönderdikten sonra beklemeye başlar.

Telefonunuzda gördüğünüz o kısa bekleme süresi, aslında istemci ile sunucu arasındaki iletişim gerçekleşirken geçer.

Bu sırada sunucu gelen isteği alır.

Önce istemcinin ne istediğini anlamaya çalışır.

Ardından gerekli bilgileri bulur veya gerekli işlemleri gerçekleştirir.

Son olarak hazırladığı cevabı istemciye geri gönderir.

---

## İkinci Adım: Yanıt Göndermek

Sunucunun telefona geri gönderdiği bu cevaba **Response (Yanıt)** denir.

Telefonun ekranında hava durumunun görünmesi, sunucudan gelen bu yanıt sayesinde mümkün olur.

Bu iletişim sürecini adım adım inceleyelim.

📱 Telefon

"Hava durumunu gönder."

──────────►

🖥️ Sunucu

İsteği alır.

↓

Ne istendiğini anlar.

↓

Veriyi bulur.

↓

Cevabı hazırlar.

──────────►

📱 Telefon

28°C

Response bazen yalnızca istenen veriyi içermez.

Aynı zamanda işlemin başarılı olup olmadığını, bir hata oluşup oluşmadığını ve gerekiyorsa ek bilgileri de içerebilir.

---

## Sonraki Adım

Artık iletişimin iki temel yönünü biliyoruz:

```text
İstemci
↓
Request

Sunucu
↓
Response
```
