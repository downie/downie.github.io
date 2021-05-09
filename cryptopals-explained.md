---
layout: post
title:  Cryptopals Explained
permalink: /cryptopals/
---

Let's talk about crypotpals.

## Set 1: "Basics"
<ol start="1">
  {% for post in site.cryptopals %}
    {% if post.set == 1 %}
      <li>
        <a href="{{post.url}}">
          {{ post.challenge_title }}
        </a>
      </li>
    {% endif %}
  {% endfor %}
</ol>

<!-- 
## Set 2: Block crypto
<ol start="9">
  {% for post in site.cryptopals %}
    {% if post.set == 2 %}
      <li>
        <a href="{{post.url}}">
          {{ post.challenge_title }}
        </a>
      </li>
    {% endif %}
  {% endfor %}
</ol> 
-->