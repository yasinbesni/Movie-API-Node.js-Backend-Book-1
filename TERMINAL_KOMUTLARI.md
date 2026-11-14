# Terminal Komutları — Geliştirici İçin Başvuru Rehberi

Bu rehber, kitap örneklerini uygularken veya kendi Node.js projenizde çalışırken ihtiyaç duyabileceğiniz yaygın terminal komutlarını açıklar. Baştan sona ezberlemeniz gerekmez; yapmak istediğiniz işlemin başlığına bakın.

Komut bloklarını satır satır çalıştırın. Örneklerdeki klasör ve dosya adlarını kendi projenize göre değiştirin. `node-basics`, kitabın ilk Node.js denemesinin; `movie-api` ise geliştireceğimiz uygulamanın örnek klasör adıdır. Bu klasörler siz oluşturduktan veya ilgili kodları indirdikten sonra kullanılabilir.

## 1. Önce Hangi Terminali Kullandığınızı Bilin

VS Code'un terminali, bilgisayarınızdaki bir komut ortamını açar. Linux/macOS'ta Bash veya Zsh, Windows'ta PowerShell ya da Komut İstemi (`cmd`) kullanıyor olabilirsiniz. Bazı komutların yazımı bu ortamlara göre değişir.

Aşağıdaki tablolar PowerShell için tam komut adlarını kullanır. Böylece PowerShell'de takma ad olarak çalışan bazı kısa komutların başka ortamlardaki seçenekleriyle karışması önlenir. Git Bash kullanıyorsanız Bash/Zsh sütununu izleyin.

## 2. Bulunduğumuz Klasörü Görme ve Klasör Değiştirme

| İşlem | Bash / Zsh | PowerShell | Windows Komut İstemi (`cmd`) |
|---|---|---|---|
| Bulunduğumuz klasörü göster | `pwd` | `Get-Location` veya `pwd` | `cd` |
| Alt klasöre geç | `cd node-basics` | `Set-Location node-basics` veya `cd node-basics` | `cd node-basics` |
| Bir üst klasöre dön | `cd ..` | `cd ..` | `cd ..` |
| Kullanıcının ana klasörüne geç | `cd ~` | `cd ~` | `cd /d "%USERPROFILE%"` |
| Klasörün içeriğini listele | `ls` | `Get-ChildItem` | `dir` |
| Gizli dosyalarla birlikte listele | `ls -a` | `Get-ChildItem -Force` | `dir /a` |

`cd`, çalışma klasörünü değiştirir; klasör oluşturmaz ve dosyalarınızı başka bir yere taşımaz. `node index.js` yazdığınızda Node.js, `index.js` dosyasını bu çalışma klasörüne göre bulmaya çalışır. `npm` komutlarını da üzerinde çalıştığınız uygulamanın `package.json` dosyasının bulunduğu klasörde çalıştırın.

### Adım adım klasör kontrolü

Terminalde `pwd` yazdığınızda şu örnek yolu gördüğünüzü düşünün:

```text
/home/yasin/projeler
```

`node-basics` klasörünü bu klasörün içinde oluşturduysanız:

```bash
cd node-basics
pwd
```

komutlarından sonra yol şöyle olur:

```text
/home/yasin/projeler/node-basics
```

Burada `index.js` dosyasını listeleyerek kontrol edebilir, ardından `node index.js` komutunu çalıştırabilirsiniz. Zaten `node-basics` klasöründeyseniz tekrar `cd node-basics` yazmayın; bu kez onun içinde başka bir `node-basics` aramış olursunuz. Komut İstemi'nde konumu kontrol etmek için bu örnekteki `pwd` satırları yerine tek başına `cd` yazın.

### Tam yol ve boşluk içeren yollar

Bulunduğunuz konumdan bağımsız olarak tam yol kullanabilirsiniz. Örnek yolları kendi bilgisayarınızdaki konumla değiştirin.

Bash/Zsh:

```bash
cd "/home/yasin/Node.js Projeleri/node-basics"
```

PowerShell:

```powershell
cd "C:\Users\Yasin\Node.js Projeleri\node-basics"
```

Komut İstemi:

```bat
cd /d "D:\Node.js Projeleri\node-basics"
```

Tırnaklar boşluk içeren yolu tek parça olarak belirtir. Komut İstemi'ndeki `/d`, gerekirse sürücüyü de değiştirir; bu seçenek PowerShell için değildir.

## 3. Klasör ve Dosyalarla Çalışma

Bu örnekler birbirinden bağımsızdır. Kopyalama, taşıma veya silme komutunu kullanmadan önce kaynak dosyanın adını ve hedef yolu kontrol edin.

| İşlem | Bash / Zsh | PowerShell | Komut İstemi |
|---|---|---|---|
| Klasör oluştur | `mkdir deneme` | `New-Item -ItemType Directory deneme` | `mkdir deneme` |
| Dosyanın içeriğini göster | `cat package.json` | `Get-Content package.json` | `type package.json` |
| Dosya kopyala | `cp example.env .env` | `Copy-Item example.env .env` | `copy example.env .env` |
| Dosyayı yeniden adlandır | `mv notlar.txt eski-notlar.txt` | `Rename-Item notlar.txt eski-notlar.txt` | `ren notlar.txt eski-notlar.txt` |
| Dosyayı mevcut bir klasöre taşı | `mv notlar.txt arsiv/` | `Move-Item notlar.txt arsiv/` | `move notlar.txt arsiv\` |
| Tek dosya sil | `rm deneme.txt` | `Remove-Item deneme.txt` | `del deneme.txt` |
| Boş klasör sil | `rmdir deneme` | `Remove-Item deneme` | `rmdir deneme` |

Son satırdaki PowerShell örneğinde yalnız boş bir deneme klasörünü hedefleyin. Dolu bir klasör silinmek istendiğinde PowerShell onay isteyebilir. Silme komutları dosyaları geri dönüşüm kutusuna göndermeyebilir; örnekleri önemli dosyalarınız üzerinde denemeyin. Kopyalama veya yeniden adlandırmada hedef ad zaten varsa üzerine yazma ya da hata davranışı komuta göre değişir.

Bu API deposundaki örnek ortam dosyasının adı **`example.env`**'dir. Kopyalama örneğini bu dosyanın bulunduğu uygulama klasöründe çalıştırın. Mevcut `.env` dosyanız kendi ayarlarınızı içeriyorsa onu yeniden kopyalayarak değiştirmeyin.

## 4. Node.js ve npm Komutları

Aşağıdaki komutlar, Node.js ve npm kuruluysa üç terminal ortamında da aynı biçimde kullanılabilir.

| Komut | Görevi ve kullanım yeri |
|---|---|
| `node -v` | Kullanılabilen Node.js sürümünü gösterir. |
| `npm -v` | npm sürümünü gösterir. |
| `node index.js` | Bulunduğunuz klasördeki JavaScript dosyasını çalıştırır. Başka bir dosya için onun yolunu yazın. |
| `node --watch src/index.js` | Belirtilen dosyayı izleme modunda çalıştırır; ilgili dosyalar değiştiğinde programı yeniden başlatır. Bu yolun bulunduğu proje aşamasında kullanın. |
| `npm init -y` | Yeni bir npm projesi için varsayılan değerlerle `package.json` oluşturur. Mevcut bölüm projesinde tekrar çalıştırmanız gerekmez. |
| `npm install` | `package.json` ve varsa kilit dosyasını kullanarak bağımlılıkları kurar. Kilit dosyasını güncelleyebilir. |
| `npm ci` | Uyumlu `package-lock.json` bulunan projede kilit dosyasına göre temiz kurulum yapar. Mevcut `node_modules` klasörünü yeniden oluşturur; kilit dosyasını güncellemez. |
| `npm install express` | Express'i projeye ekler; bağımlılık kayıtlarını günceller. |
| `npm install --save-dev supertest` | Paketi geliştirme bağımlılığı olarak ekler. Bu, sözdizimini gösteren bir örnektir; kitabın her bölümünde kurmanız gerekmez. |
| `npm uninstall paket-adi` | Adını belirttiğiniz paketi projeden kaldırır. `paket-adi` yerine gerçek adı yazın. |
| `npm run` | Projenin tanımlı script'lerini listeler. |
| `npm start` | `package.json` içindeki `start` script'ini çalıştırır. |
| `npm run dev` | Varsa `dev` script'ini çalıştırır. |
| `npm test` | Projenin tanımladığı test komutunu çalıştırır. Test script'i bulunmayan bölüm klasörlerinde aynı sonuç beklenmez. |
| `npm list --depth=0` | Projede kurulu doğrudan bağımlılıkları listeler. |
| `npm list express` | Kurulu Express sürümünü ve bağımlılık ağacındaki yerini gösterir. |

**Bu deponun final projesi için** kök `README.md` dosyasındaki kurulumu izleyin. Oradaki `npm ci --ignore-scripts`, kurulum sırasında paket yaşam döngüsü script'lerini çalıştırmadan kurulum yapar. Genel `npm ci` örneği ile projenin özel kurulum talimatını birbirinin yerine kullanmayın.

`npm start` çalışmıyorsa önce doğru uygulama klasöründe olduğunuzu ve `package.json` içindeki script adlarını kontrol edin. Deponun kökü ile 70. bölümün `code/movie-api/` uygulama klasörü aynı yer değildir. Final uygulamanın konumunu kök `README.md` ve bölüm klasörleri üzerinden bulabilirsiniz.

## 5. Çalışan Programı Durdurma ve Terminali Kullanma

| Komut / tuş | Görevi |
|---|---|
| `Ctrl+C` | Ön planda çalışan programa durdurma sinyali gönderir. Node.js sunucusunu durdurmak için kullanılır; program sinyali özel olarak işleyebilir. |
| `clear` | Bash/Zsh'de ekranı temizler. |
| `Clear-Host` | PowerShell'de ekranı temizler. |
| `cls` | Komut İstemi'nde ekranı temizler. |
| Yukarı ok | Önceki komutları geri çağırır. |
| `Tab` | Terminal ortamının desteklediği ad/yol tamamlamasını kullanır. |
| `exit` | İçinde çalıştığınız komut ortamından çıkar. |

Sunucu çalışırken terminal yeni komut beklemiyorsa önce `Ctrl+C` ile sunucuyu durdurun veya ikinci bir terminal açın. Ekranı temizlemek, çalışan programı durdurmakla aynı işlem değildir.

## 6. Dosyalarda Metin Arama ve HTTP İsteği Gönderme

| İşlem | Bash / Zsh | PowerShell | Komut İstemi |
|---|---|---|---|
| Dosyada `scripts` metnini ara | `grep -n 'scripts' package.json` | `Select-String -Path package.json -Pattern 'scripts'` | `findstr /n scripts package.json` |
| Yerel API'ye GET isteği gönder | `curl -i http://localhost:3000/movies` | `Invoke-WebRequest -Uri http://localhost:3000/movies` | `curl.exe -i http://localhost:3000/movies` |

Arama komutlarında çıktı satır numarasıyla birlikte gösterilir. `curl` veya `curl.exe` ayrıca kurulu olmalıdır; PowerShell'de açık komut adı kullanmamız sürümler arasındaki `curl` takma adı farkını önler. API örneğinde sunucunun ilgili portta çalışması ve `/movies` yolunun tanımlanmış olması gerekir. İstek gövdesi ve farklı HTTP yöntemleri için kitabın Postman örneklerinden yararlanabilirsiniz.

## 7. Sık Kullanılan Git Komutları

Git ayrıca kurulu olmalıdır. `git clone` dışında aşağıdaki komutları bir Git deposunun içinde çalıştırın. İndirilen ZIP, Git geçmişini ve `.git` klasörünü içermeyebilir; yalnız ZIP'i açmak `git status` için yeterli değildir.

| Komut | Görevi |
|---|---|
| `git --version` | Git sürümünü gösterir. |
| `git clone https://github.com/yasinbesni/Movie-API-Node.js-Backend-Book-1.git` | Depoyu bulunduğunuz klasörün altında yeni bir klasöre indirir; komut bitince oluşan klasöre `cd` ile geçin. |
| `git status` | Değişen, hazırlanan ve takip edilmeyen dosyaları gösterir. |
| `git diff` | Takip edilen dosyalardaki henüz hazırlama alanına alınmamış değişiklikleri gösterir. Yeni, takip edilmeyen dosyaların içeriğini göstermez. |
| `git diff --staged` | Bir sonraki commit için hazırlanan değişiklikleri gösterir. |
| `git add TERMINAL_KOMUTLARI.md` | Belirtilen dosyanın o anki değişikliklerini hazırlama alanına alır. |
| `git commit -m "Terminal rehberini ekle"` | Hazırlanmış değişiklikleri yerel geçmişe kaydeder; ilk kullanımda kullanıcı adı/e-posta ayarı istenebilir. |
| `git log --oneline -5` | Son beş commit'i kısa biçimde gösterir. |
| `git branch` | Yerel dalları ve bulunduğunuz dalı gösterir. |
| `git switch -c terminal-rehberi` | Yeni dal oluşturup o dala geçer. |
| `git switch main` | Var olan `main` dalına geçer; değişiklikleriniz geçişi engelleyebilir. Dal adı projede farklı olabilir. |
| `git fetch origin` | Uzak depodaki güncellemeleri alır; çalışma dalınızla kendiliğinden birleştirmez. |
| `git pull --ff-only` | Yapılandırılmış uzak daldan güncellemeleri alır; yalnız ileri sarma mümkünse yerel dala uygular, aksi durumda durur. |
| `git push` | Yerel commit'leri yapılandırılmış uzak dala gönderir; erişim yetkisi ve uzak dal ayarı gerekir. |

`git commit` ile `git push` farklı adımlardır: ilki yerel kayıt oluşturur, ikincisi bu kayıtları uzak depoya gönderir. Göndermeden önce `git status` ve `git diff --staged` ile seçtiğiniz dosyaları kontrol edin; gerçek parola ve bağlantı bilgisi içeren `.env` dosyasını eklemeyin.

## 8. Bir Komut Çalışmadığında Nereden Başlamalıyız?

| Gördüğünüz durum | İlk kontrol |
|---|---|
| `node` veya `npm` tanınmıyor | Kurulumu kontrol edin; kurulumdan sonra yeni terminal açıp sürüm komutunu deneyin. |
| `cd` hedefi bulamıyor | Hedef klasör gerçekten var mı, doğru yerden mi geçiyorsunuz? Tam yolu ve yazımı kontrol edin. |
| `index.js` bulunamıyor | Bulunduğunuz klasörü ve dosya listesini kontrol edin. Dosyanın kaydedildiğinden emin olun. |
| `package.json` bulunamıyor | Bölümün gerçek uygulama klasörüne geçin. Depo kökü uygulama kökü olmayabilir. |
| Script bulunamıyor | `npm run` ile tanımlı adları görün; `package.json` dosyasını kontrol edin. |
| Port zaten kullanımda | Önceden başlattığınız sunucunun terminaline dönün; gerekirse `Ctrl+C` ile durdurun. |
| Git deposu bulunamıyor | Bir klonun içinde misiniz, yoksa ZIP'ten açılmış klasörde misiniz? |

Bir komutun ayrıntılı seçenekleri için Bash'te `man komut-adi`, PowerShell'de `Get-Help Komut-Adi`, Komut İstemi'nde ilgili yerleşik komut için `komut /?` kullanılabilir. Örneğin `man ls`, `Get-Help Get-ChildItem` ve `dir /?`. `npm help` ve `git help` de ilgili aracın yardımına giriş sağlar.

## Resmî Başvuru Kaynakları

- npm kurulum davranışı: https://docs.npmjs.com/cli/v11/commands/npm-ci/
- npm script çalıştırma: https://docs.npmjs.com/cli/commands/npm-start/
- PowerShell klasör değiştirme: https://learn.microsoft.com/powershell/module/microsoft.powershell.management/set-location
- Git güncelleme davranışı: https://git-scm.com/docs/git-pull
