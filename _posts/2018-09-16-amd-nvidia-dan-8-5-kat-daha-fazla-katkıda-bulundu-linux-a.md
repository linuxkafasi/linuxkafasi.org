---
layout: post
title:  AMD, Nvidia'dan 8.5 kat daha fazla katkıda bulundu Linux'a
date:   2018-09-16 07:20 +0300
author: Berkhan Berkdemir

category: haber
---

Bugün itibari ile yapılan git commitlerin de AMD üstünlük sağlıyor net 1,753,343
satır kod ile. Bu sırada Nvidia ise net 205,983 satır kod katkıda bulundu.

Elbette biliyoruz ki satır kodun pek de bir ifadesi yok. Bu yüzden bu kadar
katkıda bulunmanın sonucunda neler olmuş ona bakalım.

AMD Linux Kernel'inin içine çeşitli AMD CPU/chipset, kendi açık kaynak kodlu
AMDKFD/AMDGPU/Radeon grafik sürücü komponentleri ve TTM bellek yönetimi.

Nvidia tarafında ise daha çok Tegra'ya yönelik katkıda bulunuldu.
Çok az bir kısmı ise Nvidia'nın açık kaynak kodlu DRM (Nouveau) sürücüsüne
oldu.

Sonuç olarak AMD yaklaşık 8.5 kat Nvidia'ya göre daha fazla kod katkıda bulundu kernel'e.

Toplam commit sayısı ise AMD'nin 12,386, Nvidia'nın ise 4,551.

Tabi bu sırada Intel (ki kendisi en eski Linux katkıcılarından) halen
liderliğini devam ettirmekte. Intel geliştiricileri 59,926 commit yapmış
oldu ve net 1,778,301 satır kod değişikliği yaptılar.
