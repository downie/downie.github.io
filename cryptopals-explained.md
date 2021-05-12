---
layout: post
title:  Cryptopals Explained
permalink: /cryptopals/
---
<!-- cspell:ignore cryptosystems -->
In April 2021, I set out to solve the [Cryptopals Crypto Challenges](https://cryptopals.com) to further my understanding of modern cryptography. I'm a sucker for a coding challenge, and the idea of building something just to break it was surprisingly appealing.

As I solve more challenges, I'll post my solutions and some advice for solving them on your own here. 

The good news? There isn't really a wrong way to solve these challenges. If you're stuck and looking at a reference implementation helps you understand, then feel free to look at how I did it. If you just want a gentle nudge, or clarification on what the challenge is asking, you'll find that here to.

If you've got your own solution that's wildly different from what I've made, I'd <a href="mailto:cdownie@gmail.com">love to hear about it</a>.

## My Setup
- I'll be solving these challenges in Swift
- I'll be building a framework named `CryptoTools` for all reusable code
- This will be part of a macOS app that runs all challenges
- I'll make it as unit-tested and documented as I've got the energy for.

Without further ado, let's look at these challenges.

## Set 1: "Basics"
These were anything but basic. I'd call these challenges _foundational_. Some of these are a bit finnicky to get right. They did help cement my understanding of some basic ideas that I then used in later challenges.

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

## Set 2: Block crypto
Now we're _really_ getting into it. Some of these are pretty small and straightforward. Some took quite a bit of code to get through. But it's also your first foray into breaking real crypto, so it's super rewarding.

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
