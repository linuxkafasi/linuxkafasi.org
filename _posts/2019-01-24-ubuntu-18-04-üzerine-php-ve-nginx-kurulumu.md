---
layout: post
title: Ubuntu 18.04 üzerine PHP ve Nginx kurulumu
date: 2019-01-24 11:39 +0300
author: Berkhan Berkdemir
seo:
  type: BlogPosting

category: rehber
tags:
- Ubuntu
- PHP
- Nginx
---

PHP internetin önemli bir kısımına güç veren bir dil olarak yerini halen
koruduğunu söylebilir ki bununun başlıca sebebi WordPress gibi PHP ve MySQL
kullanan basit ama etkili yazılımlar ile sağladı.

> İlk 1 milyon web sitesinde [WordPress](https://wordpress.org) %32.54 kullanıma
> sahip ve Türkiye de bunların 18,658 tanesi sıralamada bulunmakta.
>
> [Kaynak](https://trends.builtwith.com/cms/WordPress)  
> Ocak 20, 2019

Bu yazıda PHP ve Nginx HTTP sunucusunun Ubuntu 18.04 üzerine APT aracı ile nasıl
yükleneceğini ve nasıl yapılandırılacağını göstereceğiz. Eğer istiyorsanız bu
yazımızı [Oracle VirtualBox](/2018/11/oracle-virtualbox-kurulumu) üzerinden veya
[Digital Ocean](https://m.do.co/c/99d57a22944c) hesabı açarak yapabilirsiniz ama
şimdiden belirtmek isterim ki burada işimi kolaylaştırmak amaçlı Digital Ocean
üzerinden çalışacağım.

> Eğer yukarıda verdiğim link ile birlikte yeni bir Digital Ocean hesabı
> oluşturursanız, 60 gün için $100 ile başlayacaksınız. Kısaca bu yazı denerken
> için cebinizden para ödemenize gerek kalmayacayıp üstüne başka servislerini
> de 60 gün için sorunsuz deneme şansınız olacak.
>
> [Referans linki](https://m.do.co/c/99d57a22944c)

## Ubuntu 18.04 sunucunun hazır hale getirilmesi

> Eğer Oracle VirtualBox kullanmak istiyorsanız bu kısmı atlayıp,
> [şu kısma](#nginxin-yüklenmesi-ve-yapılandırılması) geçebilirsiniz

Digital Ocean'a kayıt olduktan sonrasında size bir email gelecek ve onun
onaylanması gerektiğin aynı zamanda email hesap sekmeninizi şuan için
kapatmamanınızı öneririm çünkü ileriki aşamalarda tekrardan gerekecek.

Ardından ana ekranda -aşağıda ki resim- köşe de bulunan **Create**'e tıklayarak
bir menü karşınıza çıkacak ve ilk kısım olan **Droplets**'e giriş yapın.

![Digital Ocean'a giriş yaptıktan sonra ki ana ekran](/image/2019/01/2-digitalocean-landing-page.png)

Şimdi ise karşınızda biraz daha uzunca bir sayfa çıktı ve burada artık hangi
sürümleri seçeceğimizi ve neler olacağını belirleyeceğiz. Sırası ile

1. Ubuntu 18.04 x64
2. $5/mo
3. No backups
4. Burada sunucunun yeri kolaylaştırmak ben Frankfurt olarak seçiyorum ama bu
size kalmış bir durum
5. *Additional Options* kısmında hiçbirini seçmiyorum

Bu yaptığımız seçenklerde kaç tane olması gerektiği sorusuna 1 olarak
bırakıyoruz, **1 Droplet** yazan kısımda.
Aynı zamanda hostname kısmı yine size kalmış ama ben burada olduğu gibi
bırakıcam ve yeşil **Create** butonuna basıcağız.

![Digital Ocean'da Droplet oluşturmanın son adımı](/image/2019/01/3-digitalocean-final-step-of-creating-a-droplet.png)

Ardından da bir ilerleme göreceğiz

![Droplet'in ilerleme göstergesi](/image/2019/01/4-digitalocean-progress-of-deployed-droplet.png)

1 dakika kadar sürecek bu işlem sonrasında sunucunuzun IP adresini göreceksiniz

![Konuşlandırılan sunucunun IP adresi](/image/2019/01/5-digitalocean-result-of-deployed-droplet.png)

Eğer dilerseniz hemen sunucunun adının olduğu yere tıklayın ve sizi şu arayüze
götürecek

<!-- TODO: ALT değerini ekle -->
![](/image/2019/01/6-digitalocean-dropblet-dashboard.png)

Sunucu kurulum bu kısımda bitti. Şimdi eposta adresinize bir email gelecek ve bu
email aynı aşağıda ki gibi gözükecek. Gerekli kısımları kopyalayıp yeni bir
şifre atayacağız sunucumuza.

![](/image/2019/01/7-digitalocean-sends-an-email-about-the-droplet.png)

Epostamıza gelen IP adresini, kullanıcı adını ve şifreyi kopyalayıp SSH aracımız
ile giriş yapıyoruz.

![](/image/2019/01/8-digitalocean-login-to-the-new-server.png)

Bu kısımda ise eski şifremizi tekrardan yapıştıracağız -eğer yazmak istiyorsanız
orası ayrı bir kısım- ve yeni şifremizi yapılandıracağız. Sonrasında ise
artık sunucumuza yeni atadığımız şifre ile rahatlıkla girebiliyor olacağız.

## Yazılım deposu ve yazılımların güncellenmesi

Her sunucu kurulumunda mutlaka en sürümlere sahip olduğunuzdan emin olmanız
gerekmektedir. Bu neden ile APT aracı ile ilk önce yazılım deposunu sonrasında
ise yazılımlarımızı güncelleyeceğiz.

> Süper kullanıcı olduğumuzdan dolayı `sudo` olarak çalıştırmamıza gerek yok

```bash
apt update     # yazılım depomuzu günceller
apt upgrade -y # güncellenmesi gereken yazılımları günceller
```

## Nginx'in yüklenmesi ve yapılandırılması

Burada APT aracı ile yüklemeyi yapacağımızdan dolayı işimiz çok kolay olacak
ama sonrasında yapılandırma klasörünü ve klasör yapısını da anlatmam gerekiyor.

### Nginx'in yüklenmesi ve kurulması

Tek bir komut ile indirdiğimiz Nginx artık hazır

```bash
apt install -y nginx
```

Nginx ile çalışmaya başlamadan öncesinde anlatmam gereken birkaç kısım var.
Örneğin nasıl Nginx işlemini durduracağınız, Nginx'i durdurmadan nasıl yeniden
başlatacağınız kısımlar ile birlikte yapılandırma klasörü nerede ve içinde var
gibi.

#### Systemd ile Nginx işlemi yönetimi

Systemd çok güçlü bir işlem yönetimi aracı. APT aracı ile indirdiğimiz Nginx,
Systemd ile kullanbilmemiz için bazı betikler ile birlikte geldi. Bazı
komutlara göz atalım

```bash
systemctl start nginx.service   # işlemi başlatır
systemctl stop nginx.service    # işlemi durdurur

systemctl enable nginx.service  # sistem başladığında otomatik başlatır
systemctl disable nginx.service # sistem başladığında otomatik durdurur

systemctl restart nginx.service # işlemi tekrardan başlatır
systemctl reload nginx.service  # yapılandırma klasörünü tekrardan okur
```

Eğer işinizi kolaylaştırmak isterseniz `service`'i de kullanabilirsiniz ama
burada malesef ki `enable` veya `disable` bulunmamakta.

```bash
service nginx start
service nginx stop
service nginx restart
service nginx reload
```

#### Nginx yapılandırma klasörü

Elbette APT aracımızı kurarken birkaç yapılandırma dosyası kurulduğunu görmüş
olabilirsiniz ve bunların hepsi önceden tanımlanmış değerler ve herkese
bu değerler uymaz, örneğin biz Nginx ile PHP içerikleri yayınlamak istiyoruz.
Yani bizim bu yapılandırma klasörünü ne olduğunu ve içeriğini de bilmemiz
gerekiyor.

Bu durumda bilmemiz gereken 2 yol var

* `/etc/nginx`: Ana yapılandırma klasörü. Herşeyi burada bulacaksınız
* `/usr/share/nginx`: Ön tanımlı gösteri (demo) klasörü. IP adresinize
eriştiğinizde gördüğünüz merhaba dökümanı burada

Biz daha çok `/etc/nginx` ile ilgileneceğiz.

![/etc/nginx klasörü içerisinde ls komutu çıktısı](/image/2019/01/9-list-files-at-etc-nginx.png)

Şimdi ise ilgilenmemiz gereken iki klasörü ifade etmek isterim.

* `sites-available`
* `sites-enabled`

Bu iki klasör birbirine tanıdık gelebilir ama içerik ve semantik olarak
kullanımı çok farklı. İlk klasör elimizde bulunan web sitelerini ifade ederken;
ikinci klasör şu an aktif olan web sitelerini ifade etmektedir. Bu sebep ile,
`sites-available` içerisinde ki içerikler, sembolik link ile `sites-enabled`
klasörü içersine alınır ve en son olarak Nginx işlemi, Systemd üzerinden
`reload` yapılır. Böylelikle herhangi bir web sitesi etkilenmeden değişiklikler
yapılmış olunur.

## PHP 7.2 yüklenmesi ve yapılandırılması

Nginx web sunucusunu yüklerken kullandığımız APT aracını tekrardan bu işlem
içinde kullanacağız yoksa birçok bağımlılık ile birlikte yapılandırmalar ile
uğraşmamız gerekecek.

### PHP 7.2 yüklenmesi

> Ubuntu 18.04 üzerinde `php` ile `php7.2` aynı şeyleri ifade etmektedir ve
> birçok rehber bu konuya değinmez ve kafa karışıklığına neden olur.
>
> Ayrıca belirtmekte fayda var ki bu işlemi farklı Ubuntu sürümlerinde
> yapıyorsanız PHP 7.2 yerine farklı bir sürüm indirebilirsiniz. Bu neden ile
> sürümü kontrol etmek isteyebilirsiniz `apt-cache` ile.

```bash
apt install -y php php-fpm
```

Şuan bizim için bu paketler yeterli olacak ama sizin ihtiyacınıza göre paketleri
eklemeleyebilirsiniz. Örneğin, PHP için MySQL eklentisi eklemek için `php-mysql`
veya `php7.2-mysql` yeterli.

İndirdiğimiz PHP'nin versiyonunu kontrol etmek için çalıştırmanız gereken
komut ise şudur

```bash
php --version
```

Ardından

```bash
systemctl start php7.2-fpm.service  # servisi başlattık
systemctl enable php7.2-fpm.service # boot işleminde sonra otomatik başlar
```

### Nginx için PHP 7.2 yapılandırması

PHP-FPM (FastCGI Process Manager) servisimiz başladığına göre Nginx'e nasıl bu
işlemi yapacağını gösterebiliriz.

Öncelikle `/etc/nginx/sites-available` klasörüne gideceğiz ve `default`
dosyasını açacağız bir metin editörü (Vim, Nano vb.) ile. Eğer süper kullanıcı
değilseniz mutlaka bu kısımda `sudo` kullanın aksi takdirde değişiklikler kayıt
edemezsiniz. Karşınıza çok uzunca satırlar çıkacak ve biz buradan birkaç
değişiklikte bulunucağız.

```nginx
# index'in arkasına index.php'yi ekleyin
index index.php index.html index.htm index.nginx-debian.html;

# Aşağıya doğru inin ve server_name'i bulun
server_name IP_ADRESINIZ_VEYA_ALAN_ADINIZ

# Biraz daha aşağıya inin ve `location ~ \.php$` satırını bulun
# ve parantezlerin içerisinde ki değerleri yazın
location ~ \.php$ {
  include snippets/fastcgi-php.conf;
  fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
  # veya
  fastcgi_pass 127.0.0.1:9000;
}
```

Kaydedin ve çıkın. Şimdi ise yazdığımız yapılandırma dosyasını kontrol edelim

```bash
nginx -t
```

Eğer başarılı ise artık değişiklikleri uygulayabiliriz.

```bash
systemctl reload nginx.service
```

## Test için PHP dosyası yazalım

Burada öyle şatafatlı bir PHP dosyası yazacağımızı söyleyemem. Sadece
`php_info();` çıktısını göstereceğiz.

Hemen `/usr/share/nginx/html` klasörüne `cd` ile girelim, `index.html`'i
silelim ve `index.php` dosyası oluşturalım.

```bash
cd /usr/share/nginx/html
rm -rf index.html
touch index.php
```

Şimdi artık editörümüzü açıp gerekli kodumuzu yazabiliriz

```php
<?php
  phpinfo();
?>
```

Artık internet tarayıcınızdan erişebilirsiniz ve basit böylelikle basit bir
PHP ve Nginx kurulumunu yapmış oldunuz.

## Son söz

Nginx çok kısa sürede adını duyurmuş ve sadece HTTP sunucusu olarak değil
aynı zamanda proxy sunucusu, yük dengeleyicisi, email proxy olarak da
kullanbilirsiniz ama biz bu yazımızda PHP FastCGI Process Manager ile haberleşen
bir HTTP sunucusu hazırladık.
