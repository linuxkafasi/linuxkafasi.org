---
layout: post
title: Docker Nedir?
date: 2018-12-02 07:10 +0300
author: Berkhan Berkdemir

category: nedir
tags:
- Docker
image: /image/2018/12/1-docker-logo.png
---

Docker son zamanlarda sıkça duyulan bir kelime oldu hem geliştiriciler için
hemde sistem yöneticileri için. Bu Nedir yazısında:

* Docker Nedir?
* **Image** ve **Container** arasında ki fark nedir?
* Basit bir Image nasıl yazılır ve nasıl çalıştırılır
* Hangi firmalar ne amaç ile kullanıyor?

konularına değineceğiz. Ayrıca öncesinde de belirtmekte fayda bu yazı giriş
seviyesinde olacak yani eğer öncesinde Docker hakkında hiçbirşey duymamış iseniz
rahatlık ile okuyabilirsiniz.

> Bazı kavramların tam Türkçe çevirisi olsa bile anlamını tam karşılamadığı
> için tercüme edilmedi.

## Docker Nedir?

Docker, açık kaynak kodlu yazılım geliştirme platformudur. Kendisi uygulamaları
paketleyerek (Containerization), bu paketleri taşınabilir ve platformdan
bağımsız bu uygulamaları çalıştırma olanağı sunuyor.

Eğer sanal makinalar ile çalıştıysanız Docker'ı bu makinalara benzetebilirsiniz
ama aralarında çok büyük bir çalışma farkı bulunmaktadır.

<div class="video-container">
  <iframe src="//www.slideshare.net/slideshow/embed_code/key/Dyjb9EDidLUzfG?startSlide=25" width="1920" height="1080" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe>
</div>

<!--
XXX: With Jekyll 4.0, they will make Commonmark default, but {:target} only can
be used with Kramdown:
https://stackoverflow.com/a/4705645/8929392
-->

[**dotCloud**](https://www.slideshare.net/dotCloud){:target="blank"}'dan
[**Why Docker**](https://www.slideshare.net/dotCloud/why-docker){:target="blank"}

> Örnek hosted hypervisor (tip-2) olarak
> [Oracle VirtualBox](/2018/11/oracle-virtualbox-kurulumu) verilebilir ki linkte
> ki yazıda Ubuntu 18.04 üzerine kurduk.

Üstteki slaytda Container motoru ile hosted hypervisor (hay-pır-vi-zor) (tip-2) üzerinde ki sanal
bilgisayarlar arasında ki farkı rahatlık ile görebilirsiniz ama senaryo ile
örnek vermek gerekirse; *Server* kısmı zaten bizim bildiğimiz bir metal yığını
veya bir bulut cihazı ve *Host OS* kısmı ise yine bildiğimiz sunucu işletim
sistemleri veya son kullanıcı için hazırlanan masaüstü işletim sistemleri
(Ubuntu, Red Hat Enterprise Linux vb.). İşin ilginç kısmı buradan itibaren
başlıyor.

Her bir uygulama için ayrı bir sanal makine oluşturulması gerekirken tip-2
sanalaştırmada, Docker tarafında bir makina üzerinde donanınımız yettiği kadar
uygulamayı çalıştırabiliyoruz çünkü Docker işletim-sistemi-seviyesinde
sanalaştırma[^1] sunuyor.

Ele alalım ki bir WordPress yazılımımız var ve Apache+PHP-CGI ile
çalıştıracağız.

Normalinde bir sunucu kurmamız, bir işletim sistemi ile gerekli paketleri
kurduktan sonra WordPress'i kurabiliriz - yani slaytta ki birinci durum ama
burada önemli üç sorun var:

1. Aynı sistemden birden fazla hazırlamamız gerekirse, tekrar aynı adımları
   takip etmemiz gerekiyor (ayrıca farklı yapılandırmalarda da bulunmamız
   gerekiyor. Örneğin bir statik IP adresi ayarlamak veya yük dengeleyiciye
   bağlamak).
2. Apache, PHP veya sistem paketlerine bir güncelleme gelirse her bir sisteme
   aynı güncellemeyi aynı zamanda yapmamız gerekiyor.

Aslında bu sorunlardan en önemlisi bu *Software Stack*'den[^2] birden fazla
ihtiyacınız olduğu gözünüze çarpmış olabilir ama, aynı zamanda bir geliştirici
perspektifinden bakarsak, geliştirici yazdığı yazılımın veya bir eklentinin bir
işletim sisteminde çalışıp çalışmamasından daha çok yazdığı kısım ile ilgilenmek
ister.

Bu yapacağımız yazılım yığınını şimdi Docker üzerinde nasıl yapılacağını
açıklayarak yapalım.

## **Image** ve **Container** arasında ki fark nedir?

En başta belirtmek isterim ki bu birbirine **nerdeyse** benzeyen ama farklı
şeyleri ifade eden iki kelimeye çok takılmaktan ise olayın nasıl işlediği ile
ilgilenmenizi isterim.

### Image

> üstteki slaytta Image kısmın göremezsiniz

Bir Docker Image, işletim sistemi ve uygulamamızın bir şablonudur. Eğer
nesne tabanlı bir programlama dili öğrendiyseniz, Image tanımını `Class`
tanımına benzetebilirsiniz yani sadece bir durumu veya nasıl çalışacağını ifade
etmekdir.

### Container

> üstteki slaytta Container, mavi renkli kutucuklar ve altlarında ki
> kütüphaneler ile tanımlı

Docker Container, artık sisteminiz üzerinden belirli kaynakları (ağ adaptörleri,
depolama birimi veya alanı) kullanan bir siyah kutu. Burada siyah kutu dememin
sebebi ise asıl sistemden izole bir şekilde çalışıyorlar. Nesne tabanlı bir
programlama dilinde ki karşılığı ise `Object`.

## Basit bir Image nasıl yazılır ve nasıl çalıştırılır

> Eğer bilgisayarınız da Docker yok ise [burada][docker-installation-on-ubuntu]
> ki rehberi 5:20'ye kadar takip ederek bu rehberde yapacağımız herşeyi
> yapabilirsiniz.

Bir Image tarifini `Dockerfile` adında ki kendisine özel bir sözdizimi (syntax)
olan metin dosyasına yazıyoruz. Hadi örnek bir `Dockerfile`'a bakalım

```dockerfile
FROM ubuntu:18.04

WORKDIR /home/berkhan

RUN apt update
RUN apt install -y nginx

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

Bu `Dockerfile` tarifimizde Nginx kuracağız Ubuntu üzerine. Fark ediceksiniz ki
sözdizimi çok bariz şeyleri ifade etmekte. `FROM` ile bir işletim sistemini baz
alıcağız, `WORKDIR` ile ana klasörümüzü ifade edeceğiz, `RUN` ile bazı komutları
çalıştıracağız, `EXPOSE` ile bir HTTP portunu dışarıya açacağız ve son olarak
`CMD` ile Nginx'i arka plan da çalıştıracağız.

Bu Image'i elde etmek için bilgisayarım da `docker build -t nginx:0.1.0 .`
komutunu çalıştırıyorum ve bu Image'i çalıştırmak için ise
`docker run -p 8000:80 nginx:0.1.0` komutunu çalıştırıp internet tarayıcımdan
http://127.0.0.1:8000 e gidiyorum.

Elbette bu yazdığımız Dockerfile production bir sistemde kullanmak mümkün değil
çünkü optimize edilmedi. Ama ilerki bir bölümde bu konuya da değinilecek.

Eğer kendiniz yazmak değilde hemen kullanmak istiyorsanız (ki bu örnekte Nginx)
hemen [Docker Hub](https://hub.docker.com)'a gidip arama kısmına Nginx
yazarsanız size kullanıcıların veya şirketlerin yaptığı Imageleri gösterecek.
Ama orjinal kütüphaneden bir Nginx kullanmak istiyorsanız ise bu
[nginx](https://hub.docker.com/_/nginx) sürümünü kullanabilirsiniz. Hazır
kullanmanın en avantajlı yanı bir çok Image production kullanım için optimize
edilmiş ve belli bir dökümantasyona sahip olan Imagelerdir.

## Hangi firmalar ne amaç ile kullanıyor?

[StackShare](https://stackshare.io/docker/in-stacks) verisine göre 3730 den
fazla şirket kullanmaktadır ve örnek olarak Twitter, Spotify, eBay, Cloudflare
veya Disqus gibi farklı sektörlerden bulunmakta.

En çok kullanılma amacı ise bir servisi (mikroservis veya monolit) Kubernetes
veya Docker Swarm gibi konteyner orkestrasyon yazılımları ile kullanılıyor.

## Son söz

Bu yazımızda Docker ile geliştiricilerin işletim sistemi tabanını düşünmeden
özellikler geliştirmesini ve sistem yöneticileri için ise donanımı efektif
kullanmayı, aynı zamanda uygulamayı sanal bir bilgisayara göre daha hızlı
çalıştırarak servisin hizmetini vermesini sağlıyor.

---

[^1]: [Operating-system-level virtualization](https://en.wikipedia.org/wiki/Operating-system-level_virtualization)
[^2]: [Software Stack](https://www.techopedia.com/definition/27268/software-stack)

[docker-installation-on-ubuntu]: /2018/11/digitalocean-da-ubuntu-18-04-%C3%BCzerine-docker-kurulumu
