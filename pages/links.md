---
layout: page
title: Links
description: 刚哥的朋友们
keywords: 友情链接
comments: true
menu: 链接
permalink: /links/
---

> XXXX的友联, 联系XXX互链啊

{% for link in site.data.links %}
* [{{ link.name }}]({{ link.url }})
{% endfor %}
