# Linux Kafası

![Jekyll version](https://img.shields.io/badge/Jekyll-v3.7.4-green.svg)
![MIT license](https://img.shields.io/badge/License-MIT-blue.svg)
[![Twitter Follow](https://img.shields.io/twitter/follow/linuxkafasi.svg?style=social&label=Follow)](https://twitter.com/linuxkafasi)

Linux haberlerini takip edebileceğin bir haber platformu

## Başlarken

Linux Kafası'nın web sitesi, [Markdown][markdown] formatında metin dosyaları
oluşturalarak, [Jekyll][jekyllrb] statik site üreticisi (Static Site Generator -
SSG) sayesinde render ediliyor ve [GitHub Pages][deployments] üzerinde
barındırılıyor.

Markdown formatında ki metin dosyaları elbette bilgisayarlar tarafından otomatik
oluşmuyor &mdash; ileri aşamada belki :anguished:. Kısaca, katkıda bulunmak
istiyorsanız [katkıda bulunma (Contributing)](.github/CONTRIBUTING.md)
rehberinden ne tür katkılarda bulunabileceğinizi görebilirsiniz ve aşağıda ki
kurulum rehberine devam edebilirsiniz.

### Gereksinimler

* [Ruby][ruby-lang] 2.2.5 ve üstü
* [RubyGems][rubygems]
* [GCC][gcc] ve [Make][make]

### Kurulum

> GitHub üstünde ki [Wiki][wiki] de Ruby, RubyGems, GCC ve Make kurulum
detaylarını bulacaksınız.

git kod deposunun kopyasını makinamıza `git clone` ile alalım

    $ git clone https://github.com/linuxkafasi/linuxkafasi.org.git
    $ cd linuxkafasi.org

Ardından favori metin editörünüzü açın

    $ atom .

Linux Kafası'nın ön izlemesini yapmak istiyorsanız aşağıda ki komutları da
ayrıca çalıştırmanız gerek

    $ bundle install --path vendor/bundle
    $ bundle exec jekyll server

Arından internet tarayıcınızdan http://127.0.0.1:4000 e gidip yapacağınız
değişiklikleri görebilirsiniz.

## Lisans

Bu proje MIT Lisansı ile lisanslanmıştır - Daha fazla detay için
[LICENSE](LICENSE)'e bakın

Aynı zamanda içerikler (Resimler, Videolar ve metinler)
[Creative Commons Atıf-GayriTicari 4.0 Uluslararası (CC BY-NC 4.0)][cc-by-nc-40]
ile lisanslanmıştır.

[markdown]: https://guides.github.com/features/mastering-markdown/
[jekyllrb]: https://jekyllrb.com
[deployments]: https://github.com/linuxkafasi/linuxkafasi.org/deployments

[ruby-lang]: https://www.ruby-lang.org/en/downloads/
[rubygems]: https://rubygems.org/pages/download
[gcc]: https://gcc.gnu.org/install/
[make]: https://www.gnu.org/software/make/#content

[wiki]: https://github.com/linuxkafasi/linuxkafasi.org/wiki

[cc-by-nc-40]: https://creativecommons.org/licenses/by-nc/4.0/deed.tr
