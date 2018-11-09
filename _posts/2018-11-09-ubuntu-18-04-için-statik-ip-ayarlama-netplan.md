---
layout: post
title:  Ubuntu 18.04 için statik IP adresi ayarlama - Netplan
date:   2018-11-09 06:12 +0300
author: Berkhan Berkdemir

category: rehber
---

Ubuntu 18.04 ile birlikte artık yeni nesil bir ağ ayarlama aracı geldi &mdash;
Netplan. Artık Netplan ile daha anlaşılır ağ arayüzlerini ayarlamak mümkün çünkü
Netplan YAML dosyalarını yorumlayarak `systemd-network` veya
`Network Manager`'ın anlayabileceği bir hale getiriyor.

Ubuntu 18.04 öncesinden nasıl bir statik IP adresinin ayarlandığına bakalım.
Aynı zamanda bu kısmı takip ederseniz eski sisteminizi nasıl Netplan ile
değiştirebileceğinizi de görebilirsiniz.

## Ubuntu 18.04 öncesi statik IP adresi ayarlamak

İlk yapmamız gereken işlem `/etc/network/interfaces` dosyasını bulmak ve
bir metin editörü ile açmak olacak. Buradan **genelde** `eth0` adı ilk ağ
bağdaştırıcısını ifade etmektedir ve göreceğiniz içerik böyle birşey olacak

```bash
sudo nano /etc/network/interfaces
```

```
auto eth0
iface eth0 inet dhcp
```

Şimdi ise gerekli değişiklikleri yaparak statik IP adresini ayarlıyalım

```
auto eth0
iface eth0 inet static
    address 192.168.0.230
    netmask 255.255.255.0
    network 192.168.0.0
    gateway 192.168.0.1
    dns-nameservers 208.67.222.222 208.67.220.220
```

Yazdığımız içeriği anlatmak gerekirse `address` ile bir statik IP adresini
tanımladık ve DNS sunucusu olarak ise OpenDNS'in DNS adreslerini tanımladık.

Ardından `networking`'i yeniden başlatıyoruz

```bash
sudo service networking restart
```

Artık değişiklikler kaydedildi ve uygulandı artık sisteminize 192.168.0.230 ile
erişebilirsiniz.

## Ubuntu 18.04 sonrasi statik IP adresi ayarlamak

Eğer fark ederseniz ki artık `/etc/network/interfaces` içinde bir bilgilendirme
yazıyor. Bu bilgilendirme artık "ifupdown" kurulum ile birlikte gelmediğini onun
yerine Netplan'nın artık kullanılacağına.

Bizde bu yazımızda Netplan ile basit bir statik IP adresini ayarlamayı ne kadar
anlaşılır olacağını gösterceğiz.

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
```

Bu dosya da fark ederseniz ki `dhcp4: yes` değeri varki biz bunu aslında
değiştireceğiz ve bir statik adres için önemli değerleri atayacağız.

Aşağıda ki örnek ile en üstte verdiğim örnek arasında herhangi bir fark yok.

```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      addresses: [192.168.0.230/24]
      gateway4: 192.168.0.1
      nameservers:
        addresses: [208.67.222.222, 208.67.220.220]
```

Ve son olarak bu değerleri `systemd-network` veya
`Network Manager`'ın okuyabileceği bir dosya formatına sokalım ve değişiklikleri
uygulalıyalım

```bash
sudo netplan generate
sudo netplan apply
```

## Son söz

Artık sistem yönetim yazılımlarının çoğu okunaklı YAML gibi formatlara büründü
ki Netplan da onlardan birisi. Böylelikle hızlı bir şekilde aradığınız bir
değeri/değişkeni bulabilirsiniz. Aynı zamanda bu değerleri kod ile
yapılandırılan alt yapı (Infrastructure as a Code) çok hızlı bir şekilde
dağıtabilirsiniz. Eğer daha fazla bilgi almak istiyorsanız Netplan hakkında,
kendi web sayfasını ziyaret edebilirsiniz &mdash;
[netplan.io](https://netplan.io)
