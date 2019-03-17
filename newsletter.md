---
title: Haftalık haber bülteni
description: Haftalık Linux ve özgür yazılımlar hakkında haber bülteni
---

Linux Kafası üzerinden yayınlanan LKN, haftalık Linux ve özgür yazılımlar
hakkında haber bülteni, burada listelenecek.

<ul>
{%- for newsletter in site.newsletters reversed -%}
  <li><a href="{{ newsletter | relative_url }}">{{ newsletter.title }}</a></li>
{%- endfor -%}
</ul>
