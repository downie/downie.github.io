---
layout: post
title:  "Cryptopals 10: Implement CBC mode"
set: 2
challenge: 10
challenge_title: Implement CBC mode
challenge_link: https://cryptopals.com/sets/2/challenges/10
---
<!-- cspell:ignore PKCS xor'ing funsies thankyouverymuch -->
<!-- cspell:word judgy -->
[This challenge]({{ page.challenge_link }}) asks us to a different block cipher, [cipher block chaining (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CBC). This block cipher attempts to fix the repeated-inputs-lead-to-repeated-outputs that we exploited to identify electronic codebook (ECB) encrypted data in [challenge 8](./08-detect-aes-in-ecb-mode). It does so by carefully XOR'ing blocks together, and requires an extra piece of data, an Initialization Vector, to start this cascade of XOR operations. 

What's the _scope_ of this challenge? It wasn't clear to me when I started. But I decided I'd call this complete when:
- I could encrypt something with CBC mode
- I could decrypt something (including the given file) in CBC mode
- I could use PKCS#7 to pad arbitrary-length messages to an even number of blocks for CBC mode
- ... and I could strip PKCS#7 to decrypt those padded messages.

## Consider
I just did that second-to-last one in [challenge 9](09-implement-pkcs7-padding). And that last one is actually [challenge 15]. So I paused this, and did the challenges out-of-order to accomplish the scope of work I'd set for myself.

I also used [this online encryption tool](http://www.cryptogrium.com/aes-cbc.html) to provide some reference data to make sure I was doing the encryption right. 

> The rules are what you make them here! Do challenges out of order, ignore a judgy "don't cheat" box and verify your results with a reference implementation. This is a self-paced challenge set, so break the rules of the challenge briefly if it helps you gain an understanding of these encryption implementations.

That said, since this whole set is about building your own encryption methods, and CBC can be built out of your already-built-and-broken ECB cipher, probably best to use that rather than _someone else's_ CBC cipher. You know, for funsies. 

## My Solution
Just like with my ECB cipher implementation, the initializer to my CBC cipher takes an argument to use padding or not. Notably, the underlying ECB cipher here _never_ uses its own padding, since that's now code that I've written and can do myself, thankyouverymuch.

```swift
public init(key: Data, initializationVector iv: Data, hasPadding: Bool = true) {
    precondition(key.count == AES128.blockSize)
    precondition(iv.count == AES128.blockSize)

    self.iv = iv
    shouldPadData = hasPadding

    // We do our own padding, so we don't need to use padding with the ECB cipher
    ecbCipher = ECBCipher(key: key, hasPadding: false)
}
```

Having done that, I just do my best to recreate the image [in the Wikipedia article](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CBC). I go block by block, perform a XOR, do my encryption/decryption, and save some bytes for later. 

[See my full solution here.](https://github.com/downie/cryptopals/blob/main/Cryptopals/Challenges/Set2/Challenge10.swift)