# DNS Configuration Guide: GitHub Pages & DigitalOcean Droplet

Bu proje; internet altyapısının temel taşı olan DNS (Alan Adı Sistemi) mimarisini anlamak, A ve CNAME kayıt tiplerini kullanarak özel bir alan adını (`aziz-mevlana.me`) hem GitHub Pages bulut altyapısına hem de bağımsız bir DigitalOcean Ubuntu Droplet sunucusuna yönlendirmek amacıyla hazırlanmış bir eğitim dokümantasyonudur.

## 📋 DNS Kayıt Tipleri Özeti

* **A (Address) Record:** Bir alan adını doğrudan hedef sunucunun IPv4 adresine bağlar. Uzak sunucu yönlendirmelerinde temel yöntemdir.
* **CNAME (Canonical Name) Record:** Bir alan adını başka bir alan adına (alias) bağlar. Özellikle bulut sağlayıcıların dinamik URL'lerini maskelemek için kullanılır.

---

## Görev 1: GitHub Pages Üzerinde Custom Domain Yapılandırması

GitHub Pages üzerinde barındırılan statik bir web sitesini özel bir alt alan adına (`github.aziz-mevlana.me`) bağlamak için aşağıdaki adımlar izlenmiştir:

### 1. DNS Sağlayıcı Ayarları (Namecheap)
DNS yönetim paneline girilerek bir takma ad (Alias) kaydı oluşturulmuştur:
* **Type:** `CNAME Record`
* **Host:** `github`
* **Target:** `aziz-mevlana.github.io.`

### 2. GitHub Repo Ayarları
* İlgili GitHub deposunun **Settings -> Pages** sekmesine gidilmiştir.
* **Custom Domain** alanına `github.aziz-mevlana.me` adresi girilmiş ve DNS yayılımı doğrulandıktan sonra **Enforce HTTPS** aktif edilerek web sitesi güvenli protokol üzerinden yayına alınmıştır.

---

##️ Görev 2: DigitalOcean Droplet Üzerinde Custom Domain Yapılandırması

`rsync` ile dağıtımı yapılan ve Nginx tarafından servis edilen statik web sitesini IP adresi yerine `sunucu.aziz-mevlana.me` üzerinden yayına açmak için aşağıdaki adımlar izlenmiştir:

### 1. DNS Sağlayıcı Ayarları (Namecheap)
DNS yönetim paneline girilerek alan adını doğrudan sunucu donanımına bağlayan bir adres kaydı eklenmiştir:
* **Type:** `A Record`
* **Host:** `sunucu`
* **Value/IP:** `188.166.164.249`

### 2. Nginx Sanal Konak (Virtual Host) Güncellemesi
Uzak sunucuya SSH ile bağlanılmış ve Nginx'in gelen HTTP istek başlıklarındaki domain adını ayırt edebilmesi için blok güncellenmiştir:

    sudo nano /etc/nginx/sites-available/default

Dosya içerisindeki `server_name` parametresi revize edilmiştir:

    server_name sunucu.aziz-mevlana.me;

Değişikliklerin ardından konfigürasyon test edilmiş ve servis reload edilmiştir:

    sudo nginx -t
    sudo systemctl restart nginx

---

## Kazanımlar
* DNS yönlendirme mantığı, TTL süreleri ve propagasyon (yayılım) süreçlerinin takibi.
* CNAME ve A kayıtlarının kullanım senaryolarına göre mimari farklılıkları.
* Nginx HTTP istek başlığı (Host Header) eşleştirme mekanizmasının yapılandırılması.
