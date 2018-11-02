---
layout: post
title: Ubuntu 18.04 üzerine Vtiger CRM kurulumu
date: 2018-10-31 04:21 +0300
updated: 2018-11-02 06:04 +0300
description: Vtiger CRM ile müşterilerinizi kaydedip takip edebilirsiniz veya onlara epostalar yollayabilirsiniz. Aynı zamanda önemli toplantılarınızı buradan oluşturup bütün ekip arkadaşlarınıza gönderebilirsiniz.
author: Berkhan Berkdemir

category: rehber
image: /image/2018/10/8-vtiger-crm-7-welcome-and-install-page.png
---

## Giriş

Vtiger CRM, PHP tabanlı MySQL veritabanı kullanan bir web tabanlı CRM çözümüdür.
Bu kurulum rehberinde Vtiger 7.1.0 yani yazının tarihi için en son sürümü
indirmiş ve kurmuş olacağız. Ayrıca şunuda belirtmekte fayda var bu kurulumda
biz Community sürümü olan Vtiger yanında, Apache web sunucusu, MySQL 5.7 ve
PHP 7.2 kullanacağız.

### Gereksinimler

Bu kurulum rehberi biraz zor gelebilir çünkü fazla yapılacak işlemi var. Bu
işlemleri gerçek bir sistemde yapmadan önce ne yaptığınızdan emin olun.

* Yeni kurulmuş bir Ubuntu 18.04
* Superuser kullanıcı yetkisi (`sudo`)
* İnternet bağlantısı

Aynı zamanda eğer Ubuntu 18.04 kurulum sırasında DHCP olarak ayarladıysanız
bu yazıda netplan ile nasıl statik IP ayarlanacağını da göstermiş olacağız.

## Kurulum

Ubuntu ISO dosyamız ne kadar yeni olsada ister istemez eskimiş yazılım ve
paketlerimiz olabilir ve durumu çözmek çok basit

```bash
sudo apt update
sudo apt upgrade -y
```

İlk komut yazılım depomuzun güncelliğini kontrol ediyor. Eğer bir paket
güncelliğini yitirmiş ise bize hangi paketlerin güncellenmesini gerektiğini
bildirmekte. Ama biliyoruz ki yeni kurulan sunucu kurulumlarında ne kadar ISO
yeni indirilmiş olsada eskimiş paketler mevcut. Bu neden ile hemen ikinci
komutu çalıştırıyoruz. En sonrada ki `-y` ile ekstra bir işlemde bulunmayarak
bütün sorulacak sorulara evet diyoruz ama bunu kaldırarak 1 veya 2 kere
onaylamanız gerekebilir. Aynı zamanda _flag_'ı kaldırarak hangi paketlerin
güncelleneceğini görebilirsiniz.

### Web suncusu kurulumu

Bu kurulum rehberinde girişte de bahsettiğimiz gibi Apache web sunucusu
kullanacağız. Bunun başlıca nedeni ise birçok firma zaten hali hazırda bu web
sunucusunu kullanmakta ve birçok kullanıcı da zaten bu yazılım ile bir
tanıklığı vardır.

```bash
sudo apt install apache2 -y
```

Üstteki komut ile Ubuntu yazılım deposunda bulunan en son sürüm Apache web
sunucusunu indirmiş olacağız. Ayrıca yapmamız gereken işlemler arasında bu web
sunucusunun boot aşamasında açılması gerektiğni de belirtmemiz gerekiyor.

#### Apache web sunucusunu başlatmak

```bash
sudo systemctl start apache2.service
sudo systemctl enable apache2.service
```

Burada ki ilk komut ile HTTPD (Apache web sunucusunun kısaltmasıdır) sunucunuzun
hemen şimdi başlatmanızı sağlar. İkinci komut ile HTTPD sunucunuzun boot
aşamasında başlatmanızı sağlar. Böylelikle eğer sunucunuzu yeniden başlatmanız
gerekirse ekstra bir işlem yapmadan Apache sunucunuz başlıyacaktır.

#### HTTPD'yi yapılandırmak

Apache web sunucumuzun yapılandırma dosyasına gidip hızlıca değiştirmemiz
gereken bir değer var. Bu ise `Indexes` değerini silmek.

Nedeni ise `Indexes` değeri bizim klasörümüz içindeki bütün herşeyi gösterebilir
ki bu istediğimiz birşey çünkü Vtiger klasörümüz içinde veritabanı verilimizden
diğer ki hassas verilerimiz (örneğin önbellek klasörümüz) bulunmakta.

```bash
sudo nano /etc/apache2/apache2.conf
```

> En sevdiğiniz editörü kullanın derim ama öneri istiyorsanız kesinlikle nano
> olacak eğer bilginiz yok ise metin editörleri hakkında.

Burada birazcık aşağıya gelcek olursanız şöyle bir parça göreceksiniz

```apache
<Directory /var/www/>
  Options Indexes FollowSymLinks
  AllowOverride None
  Require all granted
</Directory>
```

Bu parçada biz şöyle değiştireceğiz

```apache
<Directory /var/www/>
  Options FollowSymLinks
  AllowOverride None
  Require all granted
</Directory>
```

Ayrıca hemen `/var/www/html` içindeki `index.html` dökümanını da silelim

```bash
sudo rm /var/www/html/index.html
```

#### UFW &mdash; Karmaşık Olmayan Güvenlik Duvarı

Elbette kuracağımız yazılımın güvenliğini sağlamamız lazım bunun için ise en
hızlı güvenlik önemlini yazılım tabanlı güvenlik duvarı ile sağlıyabiliriz.

[UFW](https://help.ubuntu.com/community/UFW), Ubuntu ile gelen çok basit bir
güvenlik duvarı yazılımı. Aslında açılımı olan Uncomplicated Firewall (karmaşık
olmayan güvenlik duvarı), siz ile iptables arasında çok basit bir katman
oluşturarak çok hızlı bir şekilde güvenlik duvarı kurallarınızı
oluşturabiliyorsunuz.

Biz bu yazımız için SSH ve HTTP portlarını serbest bırakacağız.

```bash
sudo ufw allow http
sudo ufw allow ssh
```

Ardından ise UFW'yi aktif edeceğiz. Bu kısmıda eğer SSH ile bağlanıyorsanız
bağlantınızın kopma ihtimalini unutmayın

```bash
sudo ufw enable
```

### PHP 7.2 kurulumu

Ubuntu yazılım deposu sayesinde işimizi kolaylaştıracak diğerki kurulum ise
PHP. PHP, C ile yazılmış bir dil olduğundan dolayı uzun bir kurulum aşaması
mevcut. Aynı zamanda birçok bağımlı olduğu yan paket ve kütüphaneler mevcut
olduğundan giriş seviyesinde bilgisi olan birisi için zor olabilir bu durum.

İndirmemiz önerilen paketleri dökümantasyondan görmek isterdik ama malesef eski
bilgiler mevcut bu yüzden deneme yanılma yöntemi ile bulmuş olduk bu yazımızda.

> Kurduğumuz sistem efektif bir şekilde çalışmakta şuan.

```bash
sudo apt install -y php7.2 php7.2-common php7.2-curl php7.2-gd php7.2-mysql php7.2-xml libapache2-mod-php
```

Bu komut ile ihtiyacımız olan PHP ve PHP eklentilerini indirmiş oluyoruz. Bu
işlem sisteminize bağlı olarak uzun veya kısa sürebilir.

#### PHP 7.2 IMAP ve mbstring eklentileri

Bu eklentilerden özellikle mbstring eklentisi en önemli olanı Aksi takdirde
yorum sistemini kullanamıyorsunuz ve hata döndürüyor size. Bu eklenileri
kullanabilmek için öncelikle `sources.list`'e ekleme de bulunmamız gerekiyor

```bash
sudo nano /etc/apt/sources.list
```

Her satırın en sonuna `universe` ekleyeceğiz

```
deb http://archive.ubuntu.com/ubuntu bionic main universe
deb http://archive.ubuntu.com/ubuntu bionic-security main universe
deb http://archive.ubuntu.com/ubuntu bionic-updates main universe
```

Şimdi ise `CTRL` + `x` ile kaydedip çıkalım.

Sonraki aşamamız ise paket depomuzu güncellemek olacak.

```bash
sudo apt update
```

Ve artık `php7.2-imap` ve `php7.2-mbstring`'i indirebiriz.

```bash
sudo apt install -y php7.2-imap php7.2-mbsting
```

### Vtiger CRM'in indirilmesi

Bu kısımda `wget` ile sıkıştırılmış dosyayı indirip onu `/var/www/html` altına
çıkartacağız. Ama öncelikle ana klasörümüz yani `Home` klasörüne gelelim.

> Yedek olarak kendi GitLab sistemlerinden bu arşiv dosyasını alabilirsiniz ama
> çok yavaş olduğunu belirtmek isterim.
>
> http://code.vtiger.com/vtiger/vtigercrm/repository/archive.tar.gz?ref=7.1.0

```bash
cd ~/
wget "https://cfhcable.dl.sourceforge.net/project/vtigercrm/vtiger%20CRM%207.1.0/Core%20Product/vtigercrm7.1.0.tar.gz"
tar -xf vtigercrm7.1.0.tar.gz
```

Ardından `vtigercrm`  adında ki çıkartığımız klasörü `/var/www/html` altına
taşıyacağız. Ayrıca bu klasörün yetkilisi HTTPD olacak.

```bash
sudo mv ./vtigercrm /var/www/html
sudo chown -R www-data:www-data /var/www/html
```

Şimdi ise en önemli kısımlardan olan okuma, yazma ve çalıştırma yetkileri
vereceğiz.

```bash
sudo chmod 775 backup/ # not found
sudo chmod -R 775 cache/
sudo chmod -R 775 cron/
sudo chmod 775 install/ # not found
sudo chmod -R 775 languages/
sudo chmod -R 775 layouts/v7/modules/
sudo chmod 775 logs/
sudo chmod -R 775 modules/
sudo chmod 775 storage/
sudo chmod -R 775 test/
sudo chmod -R 775 user_privileges/
```

Şimdi ise internet tarayıcınızdan sunucunuzun IP adresini girdiğiniz zaman
bu ekran çıkması gerekiyor.

![Vtiger CRM 7 karşılama ekranı](/image/2018/10/8-vtiger-crm-7-welcome-and-install-page.png)

Sonraki aşamadan sözleşmeyi kabul ettikten sonra karşınıza bulunması gereken
eklentilerin listesi çıkacak.

![Vtiger CRM 7 için gerekli olan işlemler](/image/2018/10/9-vtiger-crm-7-installation-prerequisites.png)

Bu kısımda fark ederseniz ki ben eksik iki tane
PHP eklentisi indirmişim. Bunlardan benim için önemli olanı cURL eklentisi.
Ben ekstra olarak

> Nedenini bilmediğimiz şekilde `php7.2-imap` Ubuntu deposunda eksik. Bu neden ile
> gösteremeyeceğim.
>
> Güncelleme: `php7.2-imap` ve `php7.2-mbsting` nasıl eklenir bu [linke](#php-72-imap-ve-mbstring-eklentileri) eklendi

```bash
sudo apt install -y php7.2-curl
```

çalıştırmış oluyorum.

Şimdi ise alt kısımda olan tavsiye edilen PHP ayalarına bakalım.

Bu ayarları `/etc/php/7.2/apache/php.ini` dosyası içinde bulacaksınız. Biz
sadece buraya hangi değişkenleri değiştirmeniz gerektiğini yazağız. Nano
metin editöründe bir kelime aratmak için `CTRL` + `w`

```ini
display_errors = on
error_reporting = E_WARNING & ~E_NOTICE & ~E_DEPRECATED & ~E_STRICT
log_errors = Off
```

Ardından next dediğinizde sistem için yapılandırma sayfası çıkacak.

### MySQL kurulumu

Karşımıza gelen bu sayfada ise bir eksiğimiz var, MySQL.

Bu kısmı sona bıraktım böylelikle rahatça nerden hangi parçanın yanlış olduğunu
görebiliriz ama buraya kadar sorun yaşamadıysanız zaten bu kısımdan sonra belki
hiç yaşamayacaksınız.

```bash
sudo apt install -y mysql-server
```

İndirme işlemi bittikten sonra hızlı bir şekilde ilk kurulum scriptini
çalıştıralım.

```bash
sudo mysql_secure_installation
```

En bu kurulum bir localhost kurulumu olacağı için validation password
eklentisini kurmayacağız

![MySQL şifre doğrulama eklentisini N diyip indirmiyoruz](/image/2018/10/10-mysql-disallow-validate-password-plugin.png)

Ardından karşınıza bir MySQL şifre kısmı gelecek. Bu kısmı sizin Ubuntu
sunucunuzun şifresi ile alakası yok. O yüzden farklı bir şifre seçebilirsiniz.

![MySQL'i production için hazırlıyoruz](/image/2018/10/11-mysql-will-be-ready-for-production.png)

Ardından MySQL'i başlatıyoruz

```bash
sudo systemctl start mysql.service
sudo systemctl enable mysql.service
```

Sonrasında MySQL komut istemcisi ile kullanıcı ve veritabanı oluşturacağız

```bash
sudo mysql -u root -p
```

Bu kısımda size şifrenizi sorucak girin ve karşınıza `mysql>` komut istemcisi
belirecek. İlk önce `vtigercrm` adında bir veritabanı oluşturuyoruz. Sonrasında
ise `vtiger` adında bir kullanıcı ve şifresi ise `Passw0rd` olacak. Bu kullanıcı
`vtigercrm` veritabanında ise tam yetkili olacak. En son kısımda ise yetkileri
yeniliyoruz ve MySQL istemcisinden çıkıyoruz.

```sql
CREATE DATABASE vtigercrm;

CREATE USER "vtiger"@"localhost" IDENTIFIED BY "Passw0rd";
GRANT ALL ON vtigercrm.* TO "vtiger"@"localhost";

FLUSH PRIVILEGES;
QUIT
```

### Vtiger CRM kurulumu

Sonunda artık Vtiger CRM kurulumunu yapabiliriz. Tekrar internet tarayıcımıza
geri dönelim burada istenen verileri girelim.

![](/image/2018/10/12-vtiger-crm-system-configuration.png)

#### "MySQL Server should be configured with: sql_mode" hatası

Eğer bu kısımda `sql_mode` hatası aldıysanız çok normal çünkü Vtiger SQL modunun
değişmesini istiyor.

![](/image/2018/10/13-vtiger-crm-mysql-sql-mode-error.png)

Bunu ise hemen `mysqld.cnf` dosyasına ekleyecğiz

```bash
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
```

Dosyanın değişikliğini aynen böyle yapın

```conf
...
[mysqld]
#
# * Basic Settings
#
sql_mode = ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
user     = mysql
...
```

![](/image/2018/10/14-changing-sql-mode-on-mysql.png)

Bu işlemi yaptıktan sonra ise MySQL servisini yeniden başlatmanız gerek.

```bash
sudo systemctl restart mysql.service
```

Şimdi tekrar tarayıcınıza gelin ve değerleri tekrar girin.

#### "vtigercrm -> Database charset or collation not compatible with UTF8" hatası

Vtiger diğerki istediği kısım ise UTF8 olmasını istiyor veritabanın bunu ise
alttaki işlemler ile yapacağız.

```bash
sudo mysql -u root -p
```

MySQL şifrenizi girin ve ardından karşınıza tekrar `mysql>` komut istemcisi
bekleyecek ve alttaki komutu girin

```sql
ALTER DATABASE vtigercrm CHARACTER SET utf8 COLLATE utf8_general_ci;
QUIT
```

Artık kurulumu başlatabiliriz.

![](/image/2018/10/16-vtiger-crm-returns-no-error.png)

Bu sırada kendinize bir kahve yapın çünkü 5 ile 10 dakika arasında değişiyor bu
kurulum süresi.

![](/image/2018/10/17-vtiger-crm-instalation-in-progress.png)

Kurulum bittikten sonra karşınıza böyle modüller gelicek. Burada belirtmek
isterim ki php7.2-imap modülümüz olmadığından eposta ile alakali işlemlerde
bulunamayacaksınız.

![](/image/2018/10/18-vtiger-crm-selects-modules.png)

### Statik IP ayarlanması

Aslında yazının en başında da bahsetmiştik ki Ubuntu artık kendi yazılımı olan
[netplan](https://netplan.io)'ı kullanmaya başladı. Sahsen ben çok basit buldum
bir önceki ifupdown'a göre. İçeriği YAML formatında giriyorsunuz ve ne
yazdığınızı çok hızlı bir şekilde anlıyabiliyorsunuz. Ama şunu da belirtmek
isterim eğer halen ifupdown kullanmak istiyorsanız indirebilirsiniz.

Netplan yapılandırma klasörü `/etc/netplan`. Bunun altında sadece bir tane
bulabileceğiniz YAML formatında bir dosya var ve adı - şuan için -
`50-cloud-init.yaml`. İsterseniz değiştirebilirsiniz ama ben böyle bırakacağım.

> Buradaki değerler yazarın ağ ihtiyaçlarını karşılayan değer. Sizin gateway
> addresiniz farklı olabilri veya farklı bir subnette olabilirsiniz.

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: no
      addresses: [192.168.0.13/24]
      gateway4: 192.168.0.1
      nameservers:
        addresses: [208.67.222.222, 208.67.220.220]
```

![](/image/2018/10/19-set-static-ip-with-netplan-on-ubuntu.png)

Ardından ise

```bash
sudo netplan generate
sudo netplan apply
```

Artık IP adresiniz statik.

## Son söz

Vtiger ile müşterilerinizi takip edebilir, onlara epostalar atabilir veya
önemli günleri takvimden işaretliyebilirsiniz. Community sürümü ile 1-10 kişilik
küçük şirketlerin ihtiyaçlarını karşıyabilir. Ama belirttmekta fayda var ki
kendileri kurumsal sürümüde yayınlıyorlar.
