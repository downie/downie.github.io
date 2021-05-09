---
layout: post
title:  "Cryptopals 2: Fixed XOR"
set: 1
challenge: 2
challenge_title: Fixed XOR
challenge_link: https://cryptopals.com/sets/1/challenges/2
---
<!-- cspell:ignore xor'ing xor'd -->
[This challenge]({{ page.challenge_link }}) asks you to create a function that exclusive-or's (XOR) two byte buffers. 

## Introduction to XOR
This is the first of a handful of challenges that use exclusive-or. There's [an excellent article on Wikipedia](https://en.wikipedia.org/wiki/Exclusive_or) that goes into the full depth of its power. But here's a quick primer.

There are two unique facets to XOR that make it interesting for encrypting data:
1. Anything XOR'd with itself results in all zeros (`0x23` ⨁ `0x23` = `0x00`)
1. Anything XOR'd with all zeroes results in itself. (`0x23` ⨁ `0x00` = `0x23`)
1. XOR is commutative. You can XOR things in any order and get the same result. (`0x23` ⨁ `0x14` = `0x14` ⨁ `0x23`)

As a result of this, XOR can act as a kind of symmetric encryption. You can encrypt things by XOR'ing bytes together, and as long as you XOR those same bytes again, you can get out your plaintext.

```
Secret: 0xac
Encrypted with 0x23: (0x23 ⨁ 0xac)
Decrypted with 0x23: (0x23 ⨁ 0xac) ⨁ 0x23

Rule #3: 0xac ⨁ 0x23 ⨁ 0x23
Rule #1: 0xac ⨁ 0x00
Rule #2: 0xac

Decrypted!
```

## My Solution
I over-engineered this a bit anticipating some of the upcoming challenges. I created [a `Cipher` protocol](https://github.com/downie/cryptopals/blob/main/CryptoTools/Ciphers/Cipher.swift) for things that can encrypt or decrypt data.

```swift
public protocol Cipher {
    func encrypt(data: Data) throws -> Data
    func decrypt(data: Data) throws -> Data
}
```

Then I [made a `FixedXorCipher` class](https://github.com/downie/cryptopals/blob/main/CryptoTools/Ciphers/FixedXorCipher.swift) that actually performed the exclusive-or on the given data. 

```swift
public class FixedXorCipher: Cipher {
    // ...
    public func encrypt(data: Data) -> Data {
        precondition(data.count == key.count)

        let bytes = zip(data, key)
            .map { (left, right) -> UInt8 in
                left ^ right
            }

        return Data(bytes)
    }
}
```

Once that was written, all I had to do was re-use my `DataDisplay` from `Challenge01` to convert the hex string to bytes, and then use my new `FixedXorCipher` class to encrypt it.

[See my full solution here.](https://github.com/downie/cryptopals/blob/main/Cryptopals/Challenges/Set1/Challenge02.swift) 