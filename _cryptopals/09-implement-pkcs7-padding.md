---
layout: post
title:  "Cryptopals 9: Implement PKCS#7 padding"
set: 2
challenge: 9
challenge_title: Implement PKCS#7 padding
challenge_link: https://cryptopals.com/sets/2/challenges/9
---
<!-- cspell:ignore PKCS  -->
[This challenge]({{ page.challenge_link }}) asks us to implement [a specific padding algorithm, PKCS#7](https://en.wikipedia.org/wiki/PKCS_7). We'll later be able to use this algorithm to encode data that doesn't perfectly match the block size of our block cipher.  

There's a specific detail about padding to a block size that this challenge omits. When used with a block cipher, PKCS#7 should always add at least one byte of padding. That means that, if the input already fits perfectly in a number of blocks, you need to add an entire block of padding. You'll see why when you try to implement [challenge 15].

## My Solution
This challenge had a succinct solution for me. Just [a bit of math, then a handful of repeating bytes](https://github.com/downie/cryptopals/blob/main/CryptoTools/Padding.swift#L15-L21).

[See my full solution here.](https://github.com/downie/cryptopals/blob/main/Cryptopals/Challenges/Set2/Challenge09.swift)