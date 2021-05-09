---
layout: post
title:  Cryptopals Explained
permalink: /cryptopals/
---

Let's talk about crypotpals.

{% for post in site.cryptopals %}
<a href="{{post.url}}">
  {{ post.title }}
</a>
{% endfor %}