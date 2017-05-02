---
layout: page
title: About
description: 关于本人
keywords: Donaldjohn,Zhao Changgang, 赵昌刚
comments: true
menu: 关于
permalink: /about/
---

我是赵昌刚，

热衷于学习、使用开源软件，

认同开源软件的思想，

乐于传播理念，

让我一起拥抱这个开源的世界!

## 联系

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
