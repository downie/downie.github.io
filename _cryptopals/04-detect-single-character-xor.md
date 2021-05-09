---
layout: post
title:  "Cryptopals 4: Detect single-character XOR"
set: 1
challenge: 4
challenge_title: Detect single-character XOR
challenge_link: https://cryptopals.com/sets/1/challenges/4
---
<!-- cspell:ignore xor'd -->
[This challenge]({{ page.challenge_link }}) implicitly asks you to decrypt a needle in a haystack. Only one of 327 byte sequences is encrypted with a single byte XOR. 

If you knew which one, then you'd have [challenge 3](./03-single-byte-xor) again, and you could solve it by changing this input. This scaling problem is what makes this slightly harder than last time.

## My Solution
This is just a good amount of work. I couldn't think of a clever way to make this faster. I just literally tried every `UInt8` value XOR'd with all of the inputs, and ran it through my English scoring heuristic.

> This was what really helped me fine tune my heuristic. Originally my scoring method punished differences from typical frequencies more, with a higher exponent:
> ```
> letterScore = 100 - difference ^ 2.0
> ``` 
> But I ended up knocking that exponent down to 1.2 to help get reasonable output from this challenge. 

This was also the first challenge that encouraged me to measure the runtime of my solutions. Turns out, doing about eighty three thousand runs of my scoring heuristic took about twelve seconds. 

[See my full solution here.](https://github.com/downie/cryptopals/blob/main/Cryptopals/Challenges/Set1/Challenge04.swift)