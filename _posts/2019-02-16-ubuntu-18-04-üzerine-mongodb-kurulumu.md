---
layout: post
title: Ubuntu 18.04 üzerine MongoDB kurulumu
date: 2019-02-16 00:21 +0300
author: Berkhan Berkdemir
seo:
  type: BlogPosting

category: rehber
tags:
- Ubuntu
- MongoDB
---

MongoDB, açık kaynak kodlu, şemasız döküman-tabanlı bir NoSQL (Not only SQL)
veritabanı. Şuana kadar kayıtlı 2021 şirket arasında Uber, Trello, CircleCI ve
Buffer gibi şirketler MongoDB'yi kullanıyor[^1] ve NoSQL mekanızması ile daha
basit ölçeklenebilir hizmetler sunabiliyorlar.

Bu yazımızda MongoDB'nin ne olduğu hakkında kısa bir özet geçtikten sonra asıl
ilgilendiğimiz kısım olan nasıl yüklendiği ve MongoDB ile gelen önemli araçlara
göz atacağız.

> Bu yazımızda temiz bir Ubuntu üzerine kurulum yapılacağını, son
> güncellemelerin kurulu olduğu var sayılıyor.

## MongoDB'nin kurulumu

MongoDB ilk kurarken herhangi bir sorun yaşamayacağınızı biliyoruz ama ileride
olabiliecek güvenlik yamalarının hepsini alabilmeniz için paket yönetim
sistemimiz olan APT'a MongoDB şirketinin paket deposunu tanıtacağız. Böylelikle
`apt update` çalıştırdığımız zaman MongoDB sunucumuzun güncel olup olmadığını
görebileceğiz.

En başta MongoDB'nin deposunu kullanabilmemiz için GPG anahtarını girmemiz
gerekiyor

```bash
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 9DA31620334BD75D9DCB49F368818C72E52529D4
```

Ardindan ise APT yöneticimize bu MongoDB deposunun nerede olduğunu göstermemiz
gerekiyor

```bash
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.0.list
```

Bu kısımdan sonra ise tek yapmamız gereken işlem depomuzu güncellemek

```bash
sudo apt update
```

Artık `mongodb-org` paketini MongoDB deposundan alabiliriz

```bash
sudo apt install -y mongodb-org
```

### MongoDB servisinin başlatılması

Diğer hazır paketlerde de olduğu gibi bu pakette de `service` komutları hali
hazırda geliyor ve başlıca kullanımı şöyle

```bash
sudo service mongod [start|stop|restart]
```

MongoDB'nin daemon[^2]nu başladığında kısa bir süre içersinde TCP 27017
portundan istekleri kabul edebilecek.

#### MongoDB için UFW kuralı

Eğer yazılım tabanlı bir güvenlik duvarı kullanmak istiyorsanız UFW bütün
işinizi görecek ve daha detaylı bilgi için
[Ubuntu 18.04 üzerinde UFW güvenlik duvarı kullanımı](/2019/01/ubuntu-18-04-üzerinde-ufw-güvenlik-duvarı-kullanımı)
adli yazımıza da bakabilirsiniz.

> UFW üzerinden SSH servisi için TCP 22 portunu açtığınızı ve UFW'nin aktive
> edildiği baz alınacak. Daha detaylar için üstte ki linke bakınız

Burada tek satır ile UFW'nin TCP 27017 portunu kabul etmesini sağlayacağız

```bash
sudo ufw allow 27017
```

Böylelikle dışarıdan gelebilecek TCP 27017 kabul edilecek.

### MongoDB ile çalışmaya başlamak

`mongo` aracı ile local MongoDB sunucumuzuna erişebiliriz ve işlem yapabiliriz.
Burada dikkat etmeniz gereken kısım `mongo` dediğiniz anda şifresiz bir
süper kullanıcı (admin) hesabına başlanmış oluyorsunuz. Eğer şifre eklemek
isterseniz, ki eklemeniz gerekiyor, lütfen bunu şimdi aşağıda ki gibi yapın.

#### MongoDB kullanıcı güvenliği

MongoDB kabuğuna `mongo` ile girdikten sonra *admin* veritabanına gireceğiz

```javascript
use admin
```

Sonrasında ise hemen bir süper kullanıcı hesabı açacağız

```javascript
db.createUser({user: "admin", pwd: "SecretPassw0rd", roles: [{role: "root", db: "admin"}]})
```

Şimdi artk **admin** adında bir kullanıcımız var ve şifresi **SecretPassw0rd**.

##### MongoDB kullanıcı onaylama

`/lib/systemd/system/mongod.service` dosyasini bir metin editörü ile açın
ve karşınıza çıkacak olan dosyanın içinde ki `ExecStart` değerinin sonuna
`--auth` anahtarını ekleyin

```
ExecStart=/usr/bin/mongod --auth --config /etc/mongod.conf
```

Önce `systemd` sonrasında ise MongoDB sunucumuzu tekrardan başlatmamız gerekiyor

```bash
sudo systemctl daemon-reload
sudo service mongod restart
```

Ve artık MongoDB sunucumuza gerekli giriş bilgileri ile girebiliyor olacağız

```bash
mongo -u admin -p SecretPassw0rd --authenticationDatabase admin
```

## Son söz MongoDB hakkında

Bu yazımızda sadece MongoDB'nin nasıl Ubuntu 18.04 üzerine kurulduğunu
gösterdik ve bir kullanıcı ekleyip bunu MongoDB süper kullanıcısı yaptık. Aynı
zamanda UFW üzerinden TCP 27017 portunu açıp bunu MongoDB'nin kullanması için
izin verdik.

---

[^1]: [MongoDB kullanan şirketler - StackShare - Şubat 2019](https://stackshare.io/mongodb)
[^2]: [Daemon - tureng](https://tureng.com/en/turkish-english/daemon)
