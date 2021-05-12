---
layout: post
title:  "Cryptopals 11: An ECB/CBC detection oracle"
set: 2
challenge: 11
challenge_title: An ECB/CBC detection oracle
challenge_link: https://cryptopals.com/sets/2/challenges/11
---
<!-- cspell:ignore PKCS xor'ing funsies thankyouverymuch -->
<!-- cspell:word judgy -->
[This challenge]({{ page.challenge_link }}) improves on the electronic codebook (ECB) detection from [challenge 8](./08-detect-aes-in-ecb-mode) by having us build a detector that can detect ECB and cipher block chaining (CBC) modes of AES-128.

Well, it's actually asking for two things:
1. A method that can guess if data is AES-128-encrypted data was encrypted in ECB or CBC mode
1. Something that _generates_ encrypted data, either in ECB or CBC mode, chosen randomly.

## My Solution
Let's start with the second problem first.

### The Generator Problem
I made an `Oracle` class that encrypts data after adding some random bytes on either side.

```swift
class Oracle {
    // ...
    func encrypt(data: Data) -> (Data, AES128.Encoding) {
        let paddedData: Data =
            Data((0..<Int.random(in: 5...10)).map { _ in UInt8.random(in: 0...UInt8.max) })
            + data
            + Data((0..<Int.random(in: 5...10)).map { _ in UInt8.random(in: 0...UInt8.max) })

        return (try! cipher.encrypt(data: paddedData), encoding)
    }
}
```

This is cheating _a bit_. It's returning a tuple of the actually encrypted data and an enum value of the encryption method used. But by selfless decree, I'm going to say it's not cheating as long as I _only_ use the returned encoding to verify that my guessing is right.

Now, this code alone doesn't choose which cipher to use. That's chosen whenever you create the `Oracle`.

```swift
class Oracle {
    private let cipher: Cipher
    private let encoding: AES128.Encoding
    init() {
        let key = AES128.randomKey()
        encoding = AES128.Encoding.allCases.randomElement()!
        switch encoding {
        case .electronicCodebook:
            cipher = AES128.ECBCipher(key: key)
        case .cipherBlockChaining:
            cipher = AES128.CBCCipher(key: key, initializationVector: AES128.randomKey())
        }
    }
    // ...
}
```

### Now, the Detector
Well, again, we kind of already solved this in [challenge 8](./08-detect-aes-in-ecb-mode). And we can almost recycle that solution wholesale by simply saying that if it's not ECB, then it _must_ be CBC. 

So let's use the same approach:
- Go block-by-block through the encrypted data
- See if any later blocks are identical to the current one
- If so, it's ECB. Otherwise, it's CBC. 

### Rinse and Repeat
Notably, if our method for the detector was just "always guess CBC" then we'd be right 50% of the time. So running it once doesn't really guarantee that our solution is working. So let's run our test in a loop. I ended up running it about 10 times and only when it passed every time did I call it done.

```swift
var allCorrect = true
(0..<runCount)
    .forEach { attemptCount in
        let oracle = Oracle()
        let examplePair = oracle.encrypt(data: dataToEncrypt)
        let guess = detectCipherMode(for: examplePair.0)
        let didGuessCorrectly = guess == examplePair.1
        let icon = didGuessCorrectly ? "🎉" : "❌"
        update("\(icon) Run \(attemptCount): Oracle encrypted in \(examplePair.1) and we guessed \(guess)")

        allCorrect = allCorrect && didGuessCorrectly
    }
```

[See my full solution here.](https://github.com/downie/cryptopals/blob/main/Cryptopals/Challenges/Set2/Challenge11.swift)