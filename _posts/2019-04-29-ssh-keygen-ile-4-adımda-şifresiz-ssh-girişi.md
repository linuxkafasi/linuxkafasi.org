---
layout: post
title: SSH keygen ile 4 adımda şifresiz SSH girişi
date: 2019-04-29 00:06 +0300

author: Berkhan Berkdemir
seo:
  type: BlogPosting

category: linux
tags:
- SSH
---

OpenSSH birçok kişinin hiç farkında olmadan hayatında mutlaka karşlaştığı
devasa bir araç seti. Bu araç seti içinde sistem yöneticilerini sıkça
kullandığı bir program bulunuyor: SSH &mdash; Secure Shell (Güvenli Kabuk).

SSH ile *ortada ki adam saldırısı* gibi saldırılara maruz kalmadan veya sunucu
ile bağlantı sırasında bağlantının her iki taraftanda (sunucu ve istemci)
şifreli olarak sağlanması SSH'i güvenilir kılıyor. Elbette OpenSSH araç seti
sadece SSH'ten ibaret değil, SCP, SFTP veya SSHD en sık duyulan araçlarından.

{%- include asciinema.html source="/public/2019/04/asciicats/passwordless-ssh.cast" -%}
