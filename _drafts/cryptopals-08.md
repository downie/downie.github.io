---
layout: post
title:  "Cryptopals 8: Detect AES in ECB mode"
categories: cryptopals
---
<!-- cspell:ignore codebook ciphertext -->
[This challenge](https://cryptopals.com/sets/1/challenges/8) is a stepping stone to breaking electronic codebook (ECB) mode of the AES-128 block cipher. Before we break it, we should be able to identify what's encoded using it. _That's_ the task at hand in this challenge.

[Wikipedia's entry on electronic codebook mode](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Electronic_codebook_(ECB)) once again had a great description of how the cipher works, and its large, repeating flaw.

## My Solution
This part from Wikipedia, coupled with the hint from the challenge, really stood out.

> The disadvantage of this method is a lack of diffusion. Because ECB encrypts identical plaintext blocks into identical ciphertext blocks, it does not hide data patterns well. ECB is not recommended for use in cryptographic protocols.

That became the technique I used to solve this challenge. Since I can't control the input, I just had to hope the message encrypted with ECB had a block that repeated. If it did, then I could count how many times I saw a block repeated in the ciphertext. Whichever entry had the most repeats was likely encoded in ECB

[See my full solution here.](https://github.com/downie/cryptopals/blob/main/Cryptopals/Challenges/Set1/Challenge08.swift)