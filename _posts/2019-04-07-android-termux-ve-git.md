---
layout: post
title: Android, Termux ve git
date: 2019-04-07 08:21 +0300

author: Berkhan Berkdemir
seo:
  type: BlogPosting

category: android
tags:
- Emülatör
---

Bundan tam 2 sene 4 gün öncesinde yazdığım yazı eski website ile gitti ve birçok
kişi o yazıyı görüntülemeye çalışıp 404 aldığını gördüm ve o yazıyı
olabildiğince revize etmeye çalışacağım. Eğer istediğiniz
[eski yazı][wm-old-site] ise WaybackMachine de bir nushası duruyor.

Android ekosisteminin ne kadar hızlı gittiğini ve hiç şaşırtmayacak şekilde
birçok geliştiricinin işlerini (yazdıkları kodlarını) artık telefonlarına
taşıdığını bunun ile birlikte eskisi gibi yazılımların arşivlerinin
tutulmadığını artık aşina olduk. Bu durumda da merak uyandırarak nasıl olduğunu
merak ediyor olabilirsiniz ki bu yazıda nasıl Android telefonunuzu basit bir
geliştirme ortamına çevirebileceğinizi ilk adım olan versiyon kontrol sistemi
kullanarak başlayacağız &mdash; `git`.

`git` proje dosya sisteminizin fotoğraflarını çekermiş gibi tarihçesini tutan
bir program. Yani öncesinde yaptığınız ve **tarihçesinin** kaydını tutunuz dosya
anına geri dönmenizi sağlayan bir geliştirici aracı.

> [Linux Kafası](https://github.com/linuxkafasi/linuxkafasi.org) git aracı ile
> dosya ve klasör arşivini tutuluyor.

Elbette Android ve iOS sistemler üzerinde halen git uygulamaları pek meşhur
olmadığı ile birlikte Termux gibi taşınabilir sistemler kullanabilirsiniz.

[Termux](https://termux.com) bir Android terminal emülatörü. Yani tam anlamı ile
bir Linux ortamı değil ama sizin için o ortamı sunan bir uygulama. Bu uygulama
ile birlikte telefonunuzu aynı Linux çalışma ortamına Bash, Zsh veya Nano, Vim
(tamam bir de Emacs var :expressionless:) gibi yazılımları telefonunuzdan
çalıştırabilirsiniz. Elbette Termux'u bu yazımızda tekrardan anlatmamızın
sebebi eğer isterseniz katkıda bulunabileceğiniz gibi
[GitHub](https://github.com/termux) sayfası var.

## git kurulumu

Öncelikle işimize başlamadan öncesinden Android Store'dan indirmeniz gereken
Termux uygulamasını aratıp sonrasında indirerek başlıyabiliriz. Uygulamayı
açtığınızda karşınıza simsiyah bir terminal (emülatörü) ile karşılaşıcaksınız.
Bu durumda yapacağımız şey aynı bir Linux dağıtımında yaptığımız gibi paket
indirirek veya yazılımın kaynak kodunu derleyerek elde edebiliriz ama biliyoruz
ki çoğu kimse derlemek kadar (biraz) sıkıcı bir iş ile ilgelenmeyecek ve bu
yazıda Termux'un kullandığı paket yönetim sistemini kullanacağız.

```bash
packages install -y git # packages yerine apt kullanabilirsiniz
```

Birkaç bağımlılık ile birlikte git yazılımız kuruldu ama biraz yapılandırmaya
ihtiyacı olucak örneğin GitHub'da email adresiniz gizli ise GitHub hesabınızın
*noreply* adresini kullanmanız gerekli. Örneğin:

```bash
git config --global user.name "Berkhan Berkdemir"
git config --global user.email "...+BerkhanBerkdemir@users.noreply.github.com"
```

## Sıkça kullanılan kısayollar

Termux terminal emülatörünü kullanırken aklınızda tutmak isteyeceğiniz birkaç
tuş var: Ses azaltma tuşu: `CTRL`, ses arttırma ise

| Kombinasyon      | Özellik          |
| ---------------- | ---------------- |
| Ses arttırma + Q | Ekstra tuşlar    |
| Ses arttırma + E | ESC              |
| Ses arttırma + T | Tab              |
| Ses arttırma + W | Yukarı karakteri |
| Ses arttırma + S | Aşağı karakteri  |
| Ses arttırma + A | Sol karakteri    |
| Ses arttırma + D | Sağ karakteri    |

[wm-old-site]: https://web.archive.org/web/20180313144602/http://linuxkafasi.org/2017/04/03/android-termux-ve-git/
