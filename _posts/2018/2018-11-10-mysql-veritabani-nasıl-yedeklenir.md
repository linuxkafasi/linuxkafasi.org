---
layout: post
title: MySQL veritabani nasıl yedeklenir
date: 2018-11-10 08:05 +0300
author: Berkhan Berkdemir
seo:
  type: BlogPosting

category: rehber
tags:
- Ubuntu
- MySQL
image: /image/2018/11/1-mysql.png
---

MySQL, açık kaynak kodlu popüler bir ilişkisel veritabanı yönetim sistemidir.
Popüler olmasını sağlayan ana etken ise çok geniş bir topluluk tarafından
kullanılması &mdash; GitHub, Shopify ve [dahası](https://stackshare.io/mysql).

Bu yazımızda bir MySQL veritabanının yedeği mysqldump ile nasıl alınır olacak.
Yazının en son kısımda ise bu işi nasıl otomatize edeğinizi crontab ile
göstereceğiz.

> :warning: Burada anlatacağımız herşey tamamı ile bir rehber niteliğinde. Yani
> herhangi bir veri kaybından biz sorumlu değiliz.

## MySQL'in yedeği alınması

`mysqldump` ile yapacağımız yedeğin alınmasında sisteminizi durdurmanız gerek
kalmayacak çünkü *Hot Backup* alacağız. Eğer *Cold Backup* almanız gerekiyor
ise MySQL servisini durdurmak zorundasınız aksi takdirde eksik veya yanlış
bir yedek almış olursunuz.

MySQL'in sunduğu `mysqldump` ile çok kısa bir sürede bir veritabanının
tamamının yedeğini alabiliyorsunuz

```bash
mysqldump -u "[kullanıcı adı]" -p "[şifre]" "[veritabani adı]" > "backup.sql"
```

Bu tabiki de en basit hali. Eğer yedek dosyanızın adına tarih de eklemek
istiyorsanız `backup.sql` yerine `backup-$(date "+%Y-%m-%d").sql`
kullanabilirsiniz bu da çıktı olarak `backup-2018-11-10.sql` olacak.

> :bulb: Eğer shell scripting hakkında daha fazla şey öğrenmek istyorsanız bu
> [link](/2018/10/packt-den-bedava-linux-shell-scripting-kitab%C4%B1)e bir göz
> atın.

### mysqldump'ı crontab ile otomatize etmek

Burada kullanacağımız araç [crontab](http://man7.org/linux/man-pages/man5/crontab.5.html)
olacak. Bu cron programı bizim bazı işleri zaman ayarlı olarak otomatize
etmemizi sağlıyor. Örneğin bu yazımızda `mysqldump`'ı her gün çalıştıracak ve
bir yere bu yedek dosyalarını kaydedicek. Sisteminizde `cron` paketinin kurulu
olduğundan emin olun. Aynı zamanda crontab'ı kullanmak için bir metin editörüne
ihtiyacınız var.

```bash
sudo crontab -e
```

```
# Edit this file to introduce tasks to be run by cron.
#
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
#
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').#
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
#
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
#
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
#
# For more information see the manual pages of crontab(5) and cron(8)
#
# m h  dom mon dow   command
```

Karşınıza böyle bir yorum grubu çıkabilir. İstiyorsanız silebilirsiniz ama
kalmasından yana faydası olacağından eminim.

İşte buraya biz cron işimizi gireceğiz ama nasıl? Altta ki grafik size nasıl
olduğu hakkında fikir vericek ama eğer hızlıca bir cron tarihi ayarlamak
istiyorsanız bu [link](https://crontab.guru)i kullanabilirsiniz.


```
# ┌───────────── dakika (0 - 59)
# │ ┌───────────── saat (0 - 23)
# │ │ ┌───────────── gün (1 - 31)
# │ │ │ ┌───────────── ay (1 - 12)
# │ │ │ │ ┌───────────── haftanın günü (0 - 6) (Pazardan Cumartesiye;
# │ │ │ │ │                                   7 bazı sistemlerde Pazar)
# │ │ │ │ │
# │ │ │ │ │
# * * * * * çalıştırılacak komut
```

Biz burada ön tanımlı değikenleri kullanarak yapacağız.

```
@daily mysqldump -u "john_doe" -p "secretPassw0rd" "Customers" > "/home/john/backup-$(date "+%Y-%m-%d").sql"
```

> :warning: MySQL şifrenizi crontab üzerine kaydettiğiniz zaman crontab'a
> erişebilecek birisi bu metin dosyasını rahatlıkla okuyabilir. Eğer daha
> detaylı bir cron işi oluşturmak istiyorsanız daha ayrıntılı shell script
> yazmanız gerekiyor.

Üstteki komuttaki önemli kısımları değiştirmeniz gerekiyor. Ardından ise
kaydettiğiniz de bu metin dosyasını artık Cron işiniz hazır ve her gün saat
00.00 da bu iş çalışacak.
