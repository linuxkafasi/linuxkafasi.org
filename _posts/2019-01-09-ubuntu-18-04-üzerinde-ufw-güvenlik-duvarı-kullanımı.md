---
layout: post
title: Ubuntu 18.04 üzerinde UFW güvenlik duvarı kullanımı
date: 2019-01-09 19:09 +0300
author: Berkhan Berkdemir
seo:
  type: BlogPosting

category: rehber
tags:
- Ubuntu
- Güvenlik duvarı
---

Linux sistemlerin çoğu [iptables](https://netfilter.org/projects/iptables)
paket filter sistemini kullanmaktadır. Bu çok güçlü güvenlik duvarı yazılım,
öğrenme eğimi çok dik ve komutlarını akılda tutmak pek de kolay değil. Bu neden
ile basit bir ve bir o kadar da kullanımı kolay iptables üzerinde çalışabilen
bir güvenlik duvarı ihtiyacı ortaya doğmuştur ve bunun adı **Uncomplicated
FireWall**.

Adından da anlaşılacağı gibi *karışık olmayan güvenlik duvarı* yazılımı,
iptables üzerinden çalışmaktadır ve çok basit bir komut serisi vardır. Ayrıca
eklemek isteriz ki Ubuntu 18.04 sunucu veya masaüstü sürümleri ile hazır olarak gelmektedir.

> Birçok sistem seviyesi işlem süper kullanıcı ile yapıldığını ve bu neden ile
> `sudo` yu kullanabilmeniz gerekmektedir.

Eğer daha öncesinde UFW'yi kullanmadıysanız endişe etmenize gerek yok çünkü
UFW özellikle Ubuntu 14.04 den sonraki sürümlerde yüklü olarak gelmektedir.
Emin olmak istiyorsanız UFW'ye sahip olduğunuza dair çalıştırmanız gereken
komut `sudo ufw version` demeniz yeterli. Bu komut ile version bilgisi
karşınıza çıkması gerekmektedir. Aksi takdirde komut bulunamadı hatası
görmüş olmanız lazım.

## UFW servisini aktive etmek

UFW servisini, UFW komutu üzerinden hemen çalıştırabilirsiniz aşağıda ki
komutu uygalayarak

```bash
sudo ufw enable
```

Artık UFW üzerinde ki kurallar kullanılabilir durumdadır.

> Eğer UFW servisini tekrardan durdurmak istiyorsanız `sudo ufw disable` demeniz
> yeterlidir

### UFW durumunun kontrol edilmesi

Eğer öncesinden yapılandırılmış bir UFW yapılandırması varsa elinizde bunu
görmek için `sudo ufw status` yeterli. Ayrıca bu komutun uygulanması ile
UFW nin çalışıp çalışmadığını da hızlıca görebilirsiniz.

## Güvenlik duvarı kuralı işlemleri

Bu bölümde sunucumuzun bir Nginx sunucusu olduğunu düşünelim ve sadece HTTP
ve HTTPS uygulama protokolleri üzerinden çalışıyor. Yani dikkat etmemiz gereken
2 tane port var:

* HTTP için 80 TCP
* HTTPS için 443 TCP

Bu portlar *standart uygulama portları*. Yapacağımız işlem ise HTTP, HTTPS ve
SSH portlarını kabul edip geriye kalan bütün gelen portları engelleyeceğiz.

### Gerekli portların izin verilmesi

İlk izin vermek istediğim port HTTP portu olacak. Eğer 80 TCP portunu
kullanmıyorsanız, aşağıda ki açıklamayı okuyun

```bash
sudo ufw allow http
sudo ufw allow 8000 # eğer farklı bir HTTP portu kullanıyorsanız
```

Sonrasında ise HTTPS için aynı işlemi yapalım

```bash
sudo ufw allow https
```

#### Belirlenmiş IP havuzundan bağlantının kabul edilmesi

Senaryomuzda düşünelim ki iç ağımızdan Nginx sunucumuza SSH ile bağlanacağız.
İç ağımızın IP ağı ise 192.168.10.0 olduğunu düşünelim ve bizim çalışma
bilgisayarımız yine bu iç ağımızda olacak. Kısaca 192.168.10.0 gelen SSH
isteklerini kabul edelim.

```bash
sudo ufw allow from from 192.168.10.0/24 to any port 22
```

Böylelikle sadece 192.168.10.0 ağından SSH bağlantısı kabul edecek.

#### Bir portun bloklanması

Nginx sunucumuz üzerinde herhangi gib SMTP sunucusu bulunmadığından ötürü
UFW üzerinden bütün SMTP yani 25 TCP isteklerini reddetmesini isteyeceğiz

```bash
sudo ufw deny smtp
```

#### UFW üzerinden bir kuralın kaldırılması

Düşünelim ki bir ihtiyaçtan ötürü Nginx sunucumuzun üstüne ayrıca SMTP
isteklerini kabul edeceğiz. Bunu yapmak için önce eski kuralın silinmesi
gerekliliğini bilmeniz lazım. Sonrasında yeni kuralı ekleyeceğiz.

```bash
sudo ufw delete deny smtp
sudo ufw allow smtp
```

Artık 25 TCP portu üzerinden gelen isteklere bir engel olmayacak, UFW.

## Son söz

UFW ile çok hızlı güvenlik duvarı kurallarını yapılandırmanız mümkün. iptables
üzerinden çalışan UFW, anlaşılır bir komut ve kullanım şekline sahip olduğunu
gördük. Elbette UFW ile yapılabilecekler bunlar ile sınırlı değil ama en basit
kullanım şekline rağmen kullanışlı işlemleri ile birlikte size yardımcı
olabilir.
