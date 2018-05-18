---
title: Ubuntu'nun 18.10 masaüstü için planları yayınlandı
auhtor: BerkhanBerkdemir
date: 2018-05-19 02:19 +0300
category: Ubuntu
---

Ubuntu 18.04 26 Ağustos 2018 çıkması ile bir çok yeniliğe imza attı ve yapmak
istediği yeni şeylerinde olduğunu yeni bir [blog](https://blog.ubuntu.com/2018/05/18/desktop-plans-for-18-10)
yazısı ile ortaya çıkardı.

Kısaca bu blog yazısında Ubuntu 18.04'ün "point release" olan 18.04.01'e ve
Kasım ayında çıkması beklenen 18.10'a değindiği bir yazı oldu.

İlk point release Haziran 2018 de olucak, ayrıca hatırlatmakta fayda var
17.10'cular hazırlıklı olsunlar çünkü 17.10'nun güncellemeleri aynı zamanda
bitecek.

Elbette "point release"'de sadece güvenlik yamaları olmayacak. Şu an planda olan
birkaç havalı özellik 18.04 için planlandı

* Parmak izi ile açmak
* GNOME Control Center'de Thunderbolt ayarları
* Ubuntu'nun snap çözümüne (Snapcraft) XDG Portal desteği

## GNOME yazılımını stabileştirme

17 ve 18 ile birlikte Ubuntu kendi desktop environment çözümü olan Unity'i
bırakıp GNOME'a geçiş yaptı ama bu geçişte temel bir UX ve GNOME yazılımlarının
stabil çalışmama sorunu ortada. Bu sorunun çözümü içinde GNOME geliştiricleri
Ubuntu'nun tasarım ve geliştirici ekiplerinde fikirlerini paylaşmasını
bekleniyor. Sonucu ise Haziran'da bir rapor ile görmeyi bekliyoruz.

## Snap başlangıç süresi

Snapcraft, Ubuntu'ya yazılım kurmanın en hızlı ve sorunsuz yolu olduğu açık.
Ama bu hızlı çözüm şu an açılış süresi sorunu ile karşı karşıya. Bu sorunun
çözümü ne zaman yapılacağı hakkında kesin bir açıklama bulunmamakta.

## Güç tüketimi

Ubuntu 18.04 ilk çıktığında vaatlerden birisi de bu idi. Şuan tam olarak
niyetleri açık.

> There are a number of kernel options for switching devices like HDD
> controllers, USB controllers etc to a low power state when not in use.

Kısaca cihaz kullanım dışında iken o kontrolcüyü devre dışı bırakmak.

## Medya ortamı cihazı

Akıll televizyonlarda halen DLNA önemli bir yere sahip. Yeni bir özelik olarak
bilgisayarınızda ki bir medyayı herhangi bir yazılım olmadan direkt
paylaşabileceksiniz. Sorun ise DLNA bir standart değil aynı HDMI gibi. Bu da
geliştirmeyi zorlaştırdığı bir durum. Ayrıca bir SMB paylaşmayı daha
kolaylaştırma da gündemde.

## Yeni tema

Bu 18.04 planı iken 18.10'a geçti. Şu an planlanan tema şöyle gözükmekte

![](https://admin.insights.ubuntu.com/wp-content/uploads/8762/communitheme.png)
