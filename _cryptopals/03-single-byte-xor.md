---
layout: post
title:  "Cryptopals 3: Single-byte XOR cipher"
set: 1
challenge: 3
---
<!-- cspell:ignore xor'd objc -->
[This challenge](https://cryptopals.com/sets/1/challenges/3) asks you to to both decrypt some XOR encrypted data and to find the key.

Fortunately, you know that a single byte has been XOR'd with all the bytes in the plaintext. There are at most 256 of those. This means it's feasible, but annoying, to try every single byte and attempt to read what that decrypts to.

If it helps your understanding, feel free to ignore the "don't do it by hand" warning. Just know that once you do that, you're not done. There are only 256 things to try because we know the key is a single byte (with `2 ^ 8` possibilities). But if we knew it was 8 bytes? That's `(2 ^ 8) ^ 8`, or `2 ^ 64`, possibilities which comes out to just _slightly_ more than 18 quintillion things to read through to see which one looks like English. 

You don't have time for that. So make computers do it for you with some heuristic that judges how English-looking some text is.

## Consider
First, we're still struggling with data-versus-string types here. You _do_ want to encrypt things with bytes. But a method that asks if something is english text? Well you probably want to evaluate that as a `String`.

`String`s are a fascinating type in Swift. They hide a lot of complexity. Specifically, when `String`s are transformed into `Data`, they have to do so with a specific encoding. Following [the advice from this objc.io post](https://www.objc.io/blog/2018/02/13/string-to-data-and-back/), I more often than not used a more obscure initializer for `String`: `init(decoding:as)`.

Second, the English heuristic. The challenge mentions character frequency as a good metric. I found these links helpful:
- The [Wikipedia article on letter frequency](https://en.wikipedia.org/wiki/Letter_frequency) had a helpful chart of English letter frequencies
- Google also led me to [this article on frequencies for punctuation marks](http://www.viviancook.uk/Punctuation/PunctFigs.htm)

## My solution
With only 256 possible solutions, I chose to decrypt them all. For each value of a `UInt8`, I made a new `FixedXorCipher` with that as the key, and then attempted to decrypt the data. I made sure my repeated-byte key matched with length of my data with `Data`'s `init(repeating:count)` method.

```swift
(0...UInt8.max)
    .map { charValue -> PartialResult in
        let cipher = FixedXorCipher(key: Data(repeating: charValue, count: ciphertext.count))
        let decryptedData = cipher.decrypt(data: ciphertext)
        let decryptedString = String(decoding: decryptedData, as: UTF8.self)

        // ...
    }
```

Now the hard part: the "what looks like English" heuristic

### English Score
I made a new `Analysis` class to house any methods that I'd use to, well, analyze data. In this case, it just had a single method: `englishScore(for:)`. I also encoded the frequencies for both [letters](https://github.com/downie/cryptopals/blob/main/CryptoTools/Analysis.swift#L11-L39) and [punctuation](https://github.com/downie/cryptopals/blob/main/CryptoTools/Analysis.swift#L41-L55) from those articles into that class as well.

My heuristic ended up looking like this:
1. Count how many times each character occurs in the target `String`
1. Divide that by the `String`'s length to get the frequency of that letter in that `String`
1. For each English letter in our frequency table, give that letter a score based on how close its frequency is to ideal. I ended up with:
```
letterScore = 100 - difference ^ 1.2
```
1. Use the same scoring algorithm for each English punctuation mark.
1. Sum the scores for all our letters and punctuation marks with known frequencies together to get a total score for the string.

Why use both punctuation and English letters? Turns out if I just used letters, then _two_ different keys decoded a phrase. One was in lowercase with nice punctuation and spacing. The other was mostly uppercase, but with weird punctuation and invisible characters. Adding a few points for common punctuation helped the heuristic reliably recommend the better looking `String`.

[See my full solution here.](https://github.com/downie/cryptopals/blob/main/Cryptopals/Challenges/Set1/Challenge03.swift)