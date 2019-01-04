---
layout: post
title: Ubuntu 18.04 üzerine htop u kaynak kodundan kurulum
date: 2019-01-04 23:13 +0300
author: Berkhan Berkdemir
seo:
  type: BlogPosting

category: nedir
tags:
- Ubuntu
---

Birçok zaman paket yönetim sistemlerinde bulamayacağımız yazılımlar olabilir ve
bunları kaynak kodundan (source code) kurmamız gerekebilir. Bu yazımızda kaynak
kodu ile birlikte dağıtılan htop programını kuracağız.

> htop interaktif sistem monitör yazılımıdır ve burada amaç kaynak kod ile
> bir program Ubuntu üzerinde (veya herhangi bir dağıtım üzerinde) nasıl kurulur
> kısmını göstermek.

Öncelikle bilinmesi gereken kısım Linux ortamı için yazılan birçok yazılım C
veya C++ dilleri ile yazıldığından ötürü bu dilleri derledikten sonra
çalıştırılabilir hale getirebiliriz. Kısaca bir C dosyasını çift tıklayarak
çalıştıramayız.

Bu nedenledir ki biz [`build-essential`](https://packages.ubuntu.com/xenial/build-essential)
paketini APT aracı ile indireceğiz çünkü bu yazılım paketinde ihtiyacımız olan
GNU C Compiler, GNU C++ Compiler, libc6-dev ve Make bulunmakta ve böylelikle
kullanmak istediğimiz programı kullanabileceğiz.

İşlemimize dediğimiz gibi `build-essential` paketini indirerek başlıyalım

```bash
sudo apt install -y build-essential
```

Şimdi ise [htop'un proje sayfası](https://hisham.hm/htop/)na gelirsek buradan da
gerekli kaynak kodun sıkıştırılmış halini sistemimize indirip açacağız ve
elimizde kaynak kodu olan bir yazılım klasörü önümüzde olucak

> htop'un en son sürümü bu tarih için 2.2.0

```bash
wget "http://hisham.hm/htop/releases/2.2.0/htop-2.2.0.tar.gz"
tar -zxvf "htop-2.2.0.tar.gz"
cd "htop-2.2.0"
```

Eğer şimdi klasörün içindekileri `ls` ile görüntülerseniz bir sürü C veya H
uzantılı dosya göreceksiniz bu da demek oluyor ki program C dili ile yazılmış.
Buna rağmen bizim bu bilgiye pek de ihtiyacımız olmayacak çünkü yazar, programın
GNU Autotools ile kullanılabilir/kurulabilir olduğunu ifade etmiş.

Şimdi yapacağımız işlem ise en başta `./configure` programını çalıştırmak olacak
ki `./configure` ile o programı kendimize göre yapılandırabiliyoruz. Ardından
artık `make` programını çalıştırabiliriz. Bu program ile artık programımızı
(bu yazıda htop) derleyecek, sistem kütüphanelerimiz ile. Son dokunuş olarak ise
`make install`. Bu son komut ise artık derlenen bütün bilgiyi kayıt edilmesi
gereken son noktaya kopyalanıyor.

```bash
./configure
make
sudo make install
```

Ve artık kullanmak istediğimiz program olan `htop` terminalimiz üzerinden
erişebilir ve kullanabiliriz.

```bash
htop
```
