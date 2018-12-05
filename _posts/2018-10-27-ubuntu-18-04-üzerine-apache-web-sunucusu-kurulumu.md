---
layout: post
title: Ubuntu 18.04 üzerine Apache web sunucusu kurulumu
date: 2018-10-27 05:20 +0300
author: Berkhan Berkdemir
seo:
  type: BlogPosting

category: rehber
tags:
- Ubuntu
- Apache HTTP sunucusu
image: /image/2018/10/3-apache-http-server.png
---

![Apache Yazılım Vakfı](/image/2018/10/3-apache-http-server.png)

## Giriş

Apache web sunucusu (httpd), Linux'un bu kadar popüler bir işletim sistemi
olmasına neden olan sunucu yazılımlarından biridir. 1995 yılında Apache Yazılım Vakfı
tarafından çıkan web sunucusu modüler yapısı sayesinde dinamik içerikleri PHP,
Perl veya Ruby gibi diller ile oluşturmak mümkün ama biz bu kurulum rehberinde
statik içeriğimizi sunacağız. Aynı zamanda bu rehberimiz de `.htaccess`, httpd
günlük dosyalarının nasıl okunması gibi konulara yüzeysel değinilecek. Ama bu
rehberin asıl değineceğimiz noktaları ise `/etc/apache2` klasörü içindeki
yapılandırma dosyaları nasıl düzenlenir, bir Apache web sunucusu nasıl
başlatılır ve birden fazla statik içerikli website *Virtual Hosts* ile sunulur.

### Gereksinimler

Bu içeriği test etmek istiyorsanız Oracle Virtual Box kullanmanız yeterli
olacak

* Yeni kurulmuş bir Ubuntu Server 18.04
* Superuser kullanıcı yetkisi (`sudo`)
* İnternet bağlantısı

## Kurulum

Bu rehberde kaynaktan derleme ile kurulum yapmayacağız. Bu sayede bu rehberi
olabildiğince basit tutmuş olacağız ama bu demek oluyor ki son sürüm olan Apache
web sunucusunu kullanamış olacağız.

İlk önce Ubuntu sunucumuzun yazılım deposunu güncellememiz ve içindeki
yazılımları güncelleyerek başlamamız doğru bir hareket olucak.

Birçok Ubuntu kurulum rehberinde `apt-get` kullanılır ama bu bir Debian
alışkanlığı. Uzun zamandır Ubuntu `apt` kullanmaktadır yani herhangi bir fark
yok `apt-get` ile `apt`.

> Eğer `root` kullanıcısı değilseniz aşağıda ki birçok komutu çalıştıramazsınız.
> Standart olarak Ubuntu'da normal kullanıcı ile çalışıyor olursunuz. Root
> kullanıcısına değiştirmek veya root kullanıcısı gibi davramak istiyorsanız
> `sudo` komutunu izin isteyen komut için kullanabilirsiniz. Ama burada dikkatli
> olmanız gerekli.


```bash
apt update && apt upgrade -y
```

`apt update` Ubuntu sunucumuzun yazılım deposunu güncelleyerek hangi
güncellemeleri şuan mümkün olduğunu görmüş olduk. `apt upgrade` ile ise bu
yazılımları sisteminize indirmiş olduk.

> En sonda ki `-y`'nin görevi size onaylamanız gereken sorulara **evet** yanıtı
> verir.

Yazılımlarımızı güncellediğimize göre şimdi artık Apache web sunucusnu
indirebiliriz.

```bash
apt install apache2 -y
service apache2 start
```

İşlem bittikten sonra artık sisteminizde Apache web sunucusu bulunmakta ve
hizmete hazır. Eğer `ip addr` komutunu girerseniz sunucunuzun IP addres
listesini karşınıza çıkacak. Bu listede dışarıya bakan IP addresini bulmanız
gerekli. Eğer ev ortamında çalışıyorsanız bu IP addresi 192 veya 10 ile
başlıyabilir ama farklı bir ortamda bu sunucuyu kurduysanız (örneğin Digital
Ocean gibi bir VPS hizmeti sunan firmada) lütfen hizmet aldığınız yere sorun
veya panellerinde bu bilgi bulunmaktadır. Ben kendi bilgisayarıma kuruduğum
işletim sistemine bağlanmam gerekecek bu durumda. İnternet tarayıcıma
`172.17.0.2` yazdım ve karşıma bir merhaba sayfası çıktı.

> :bulb: eğer sunucunuzu Türkçe kurduysanız bu çıktı Türkçe olabilir.

![Ubuntu üzerinde ki Apache web sunucusunun merhaba sayfası](/image/2018/10/4-apache2-ubuntu-default-page-it-works.png)

Eğer bu HTML dökümanına hızlı bir göz atarsanız zaten birçok önemli dosyanın
nerede saklandığını görüceksiniz. Örneğin bu dökümandan yapılandırma klasörünün
`/etc/apache2` olduğunu gördük.

## Geçerli sayfaya kendi içeriğimizi koymak

Şuan belkide merak ettiğiniz kısım olan ben bu *Default Page*'i nasıl
değiştireceğim kısmına gelelim. Aslında yine aynı döküman bunu nerede
bulacağınızı söylüyor. Bu standart dosya `/var/www/html/index.html` altında
bulunmakta.

> :warning: burada Ubuntu yine yetki hatası verebilir ve `sudo` kullanmanız
> gerekiyor bu işleme devam etmek için. Aynı zamanda Nano ile yaptığınız
> değişiklikleri kaydetmek için `sudo` kullanmak zorundasınız.

```bash
cd /var/www/html # o klasöre gireceğiz
rm index.html # bu komut ile o standart dosyayı sileceğiz
nano index.html # bu komutu sudo ile çalıştırmazsanız kaydedemezsiniz
```

Şimdi karşınıza bir metin editörü çıktı. Bu editörün adı Nano. Benim düşüncem
Nano en basit editör yeni başlıyanlar için ama dilerseniz ki vi, sed, emacs gibi
limitlerinizi zorlayabileceğiniz editörler kullanabilirsiniz.

> I've been using Vim for about 2 years now, mostly because I can't figure out
> how to exit it.
>
> &mdash; I Am Devloper (@iamdevloper)
> [February 17, 2014](https://twitter.com/iamdevloper/status/435555976687923200)

Editöre girdikten sonra artık yeni dökümanımızı yazabiliriz.

```html
<!DOCTYPE html>
<html lang="tr" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title>Merhaba</title>
  </head>
  <body>
    <p>Merhaba. Bu bir Ubuntu 18.04 Apache sunucusu!</p>
  </body>
</html>
```

Yazmak konusunda ciddiyim çünkü copy-paste yaparsanız hatalı işlemler
olabiliyor. Bu yüzden yazarak işinizi garantiye alın. Yazma işleminiz bitti ise
şimdi o metin editöründen `CTRL` + `x` ve ardıdan da onaylarsanız
çıkabileceksiniz.

Tekrar internet tarayıcımıza geri dönersek sayfayı yenilememiz gerekicek. Fark
edeceksiniz ki artık o standard HTML döküman yerine bizim az önce yazdığımız
döküman karşımıza çıktı.

## Yapılandırma klasörüne hızlı bakış

Aslında iki bölüm öncesinde nerede bulacağınızı söylemiştik bu *yapılandırma*
klasörünü. Hemen o klasöre girelim

```bash
cd /etc/apache2
```

Burada `ls` ile içeride ki klasör ve dosyaları görebilirsiniz. Bu yapılandırma
klasöründe bizim için **şuan** önemli olan tek bir dosya var ve 2 klasör var:
`apache2.conf` dosyasi ve `sites-available` ile `sites-enabled` klasörleri.

![/etc/apache2 klasörünün ls çıktısı](/image/2018/10/5-ls-output-of-etc-apache2-folder.png)

Öncelikle anlatmak istediğim yapılandırma klasörü olacak.

### `apache2.conf` neden bulunuyor?

Bu dosya Apache web sunucumuzun ana yapılandırma dosyası ve içinde HTTPD'nin
çalışmasını sağlayan hayati değişkenler bulunmakta. Hadi neler var bakalım.

```bash
cat apache2.conf
```

`cat` komut ile metin dosyasını çıktısını almış olursunuz. Biraz yukarıya
çıkmanız gerekecek en üstteki çıktıyı görmek için. Eğer hızlı bir göz atarsanız
günlük dosyalarının nasıl bir formatta nereye çıktığını, HTTPD ne kadar süre
sonra bağlantıyı kopardığı, sanal sunucu klasörleri gibi birçok **genel**
yapılandırma burada.

![cat ile apache2.conf çıktısı](/image/2018/10/6-output-of-apache2-conf.png)

Bu dosya aslında kurcalayacağınız yapılandırma dosyalarından ama sadece yüzeysel
geçtim çünkü statik dökümanlar yayınlamak için bu yapılandırma yeterli.

### `sites-available` ile `sites-enabled` arasında ki fark

Bu klasörlerin bizim bir siteyi aktif etmemizi veya diğerki siteyi aktif dışı
bırakmamızı sağlayan iki klasör.

Örnek vermeden önce bu klasörlerin nasıl çalıştığını anlatmak isterim.

```bash
cd /etc/apache2/sites-available
```

`sites-available` altında iki tane dosya göreceksiniz: `000-default.conf` ve
`default-ssl.conf`. Birincisi bizim o ön tanımlı sayfamıza bağlanmamızı sağlayan
bir yapılandırma dosyası. İkincisi ise eğer o ön tanımlı dökümanımıza SSL ile
bağlanmak istersek kullanacağımız bir yapılandırma. `sites-enabled`'da ise
sadece bir tane dosya var ve o da `000-default.conf` ve fark ettiyseniz rengi
açık mavi. Eğer `ll` komutunu çalıştırırsanız, `sites-enabled` altında ki
`000-default.conf`, `sites-available` altında ki `000-default.conf` işaret
ediyor.

![sites-available ile sites-enabled arasında ki fark](/image/2018/10/7-available-vs-enabled.png)

Bu kısmı şöyle düşünebilirsiniz. `sites-enabled` klasörünün altında
`sites-available`'a bir kısa yol var. Bu işleme symlink veya symbolic link
oluşturmak deniyor.

O zaman hemen bu kısımda öğrendiklerinizi örnek vererek tekrar edelim.

Elinizde iki websitesi var ve birisi ana sayfanız diğerkisi ise kampanya
sayfanız. Her iki sayfanın da yapılandırma dosyaları `sites-available` altında
bulunmakta. `sites-enabled` altında ise şuan için her iki dosyaya sembolik link
var ama bir ay sonra kampanyanız bitecek ve o websitesini yayından
kaldıracaksınız. Tek yapmanız gerekcek işlem `sites-enabled` altında kampanya
websitesini sembolik linkini silmeniz olacak. Sonrasında HTTPD'yi
başlattığınızda artık o kampanya sayfasına erişimleri olmayacak.

## `.htaccess` &mdash; klasör yapılandırma dosyası

`.htaccess` sadece bir Apache web server için bir dosya değil. Birçok web
suncusu bu dosyaları okuyabilir. Bu kısımda tahmin edeceksiniz ki ben sadece
HTTPD için olan `.htaccess`'e değineceğim.

Sıklıkla normal kullanıcının klasörde ki içeriğe girmemesi için kullanılan
`.htaccess` aslında bir çok rolü var.

Örneğin yeni bir sunucuya veya eski olan dosyayı yeni bir dosyaya
yönlendirebilirsiniz, CSS, JS veya resimlerin ne kadar uzun süre ön bellekte
kalması gerektiğini ifade edebilirsiniz. Aynı zamanda `.htpasswd` ile
klasör veya dosyayı şifre koruması altına alabilirsiniz.

Eğer elinizde kişisel hata sayfalarınız var ise yine bunları `.htaccess`'de
ifade edebilirsiniz.

Bir fikriniz yok ise `.htaccess` hakkında bilmeniz gereken ekstra tek bir şey
var o da *Regular expression*. Çok sık bir şekilde kullanılan bu örüntü bulma
dili size çok yardımcı olacak.

> İnternete bir sürü regex kaynağı bulabilirsiniz.

Aynı zamanda yine internete bulabileceğiniz `.htaccess` oluşturuclar bulunmakta.
Bunlar ile çok hızlı ve görsel bir şekilde `.htaccess` dosyanızı
yapılandırabilirsiniz.

## Günlük okumak

Bir sistem yöneticisinin en sık yaptığı işlemler arasında bir servisin
günlüklerini okumak ilk sırada yer alabilir. Günlük okumak belirli analiz
sistemleri ile mümkün olabileceği gibi ham günlük verilerini direkt olarak
okuyabilirsiniz. Apache web sunucusu günlük verileri `/var/log/apache2` klasörü
altında bulunmakta. `access.log` web sunucunuzdan alınan başarılı istekleri
ifade eder. `error.log` ise adından da anlaşılacağı gibi başarısız (400, 500)
hataları ifade eder. Birinci günlük ile ziyaretçi trafiğinizi görebilirsiniz,
ikinci günlük ile ise eksik dökümanlarınızı veya yanlış yazılmış
programlarınızı fark edebilirsiniz.

## Son söz

Elbette Apache web sunucusu ile yapabilecekleriniz sadece statik içerik sunmak
değil. HTTPD ile aynı zamanda PHP ile yazılmış WordPress gibi CMS sistemleride
kullanabilirsiniz veya bu site gibi Jekyll gibi statik site oluşturuclarıda
kullanarak web sunucusu hizmeti verebilirsiniz.

Bunun yanında birçok modül ile birden fazla görev yükleyebilirsiniz. örneğin
HTTPD sunucusunu HTTP yük dengeleyicisi olarak kullanabilirsiniz veya birden
fazla websitenizi tek bir bir HTTPD üzerinde barındırabilirsiniz.
