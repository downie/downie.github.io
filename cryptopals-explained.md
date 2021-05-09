---
layout: post
title:  Cryptopals Explained
permalink: /cryptopals/
---

Let's talk about crypotpals.

## Set 1: "Basics"
<ol start="1">
  {% for post in site.cryptopals %}
    <li>
      <a href="{{post.url}}">
        {{ post.challenge_title }}
      </a>
    </li>
  {% endfor %}
</ol>