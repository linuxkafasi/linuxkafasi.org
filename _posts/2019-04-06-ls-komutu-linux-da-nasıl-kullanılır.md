---
layout: post
title: ls komutu Linux'da nasıl kullanılır
date: 2019-04-06 08:55 +0300

author: Berkhan Berkdemir
seo:
  type: BlogPosting

category: linux
tags:
- Komutlar
image: /image/2019/04/screenshot-of-ln-and-ls.png
---

Bir dosya için kısayol oluşturmak ile karşı karşıya geldi iseniz Linux ortamında
kafa karıştırıcı birçok kısım ile karşı karşıya gelebilirsiniz ama endişe edicek
bir durum yok çünkü `ls` komutu bu işi bizim için yapacak.

`ls` komutunun oluşturuğu bu **kısayol**'a Unix/Linux sistemler de **sembolik
link** (Symbolic link, symlink)[^1]; Windows sistemler'de ise **kısayol** denir.
Ayrıca birçok yerde **soft link** olarak görebilirsiniz symlink kavramını. Bu
tip dosyanın amacı bir asıl dosya veya klasör'e referens oluşturur.

![ln ve ls'nin kullanımı](/image/2019/04/1-screenshot-of-ln-and-ls.png)

`ln` sözdim kuralı çok basittir ve en basiti ile iki parametre alır: **kaynak
dosya veya klasör** ve **link adı**.

```bash
ln -s ${KAYNAK} ${LINK_ADI}
```

Burada dikkat edilmesi gereken kısım eğer kaynak dosya veya klasör silinse bile
sembolik link silinmeyecek. Bu da linke bağımlı olan işlemlerin çökmesine
veya beklenmedik hatalara neden olabilir. Bu yüzden sembolik kullanılacağı zaman
gerekli dökümantasyonun yapılması gereklidir.

![Sembolik link kaynağı silindi ve son durumu](/image/2019/04/2-screenshot-symlink-source-deleted-and-output-of-ls.png)

---

[^1]: https://en.wikipedia.org/wiki/Symbolic_link
