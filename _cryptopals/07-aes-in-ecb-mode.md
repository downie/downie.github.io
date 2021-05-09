---
layout: post
title:  "Cryptopals 7: AES in ECB mode"
set: 1
challenge: 7
---
<!-- cspell:ignore codebook -->
[This challenge](https://cryptopals.com/sets/1/challenges/7) brings us back to building encryption, by asking us to implement AES-128 in ECB, or electronic codebook, mode. 

This is the first of our work with block ciphers. Unlike the later challenges, we're encouraged to use existing frameworks to build our own ECB interface. My guess is that this is just tedious code to write on our own, and not as interesting as some of the later ciphers.

What is an electronic codebook mode? [Wikipedia has a pretty thorough answer](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Electronic_codebook_(ECB)).

## Consider
There are a handful of frameworks you could use to solve this problem on Apple platforms:
- [CryptoKit](https://developer.apple.com/documentation/cryptokit) is the one provided by Apple. Works great, but has a very C-style interface.
- [CryptoSwift](https://cryptoswift.io) provides all the same functionality, but is written entirely in Swift. 

## My Solution
I chose to build on CryptoSwift. Just like before, I made another `Cipher` implementation, this time named `ECBCipher`.

```swift
final public class ECBCipher: Cipher {
    private let aes: AES
    public let encoding = Encoding.electronicCodebook

    public init(key: Data, hasPadding: Bool = true) {
        aes = try! AES(key: key.bytes, blockMode: ECB(), padding: hasPadding ? .pkcs7 : .noPadding)
    }

    public func encrypt(data: Data) throws -> Data {
        let bytes = try aes.encrypt(data.bytes)
        return Data(bytes)
    }

    public func decrypt(data: Data) throws -> Data {
        let bytes = try aes.decrypt(data.bytes)
        return Data(bytes)
    }
}
```

> *What about padding?*
> Block ciphers only work if their inputs are block aligned -- if they fit into whole blocks. If they don't then the inputs need to be padded to the appropriate length. We'll see how that works in later challenges. For now, you can assume your inputs will always be full 16-byte blocks.
> However, `CryptoSwift`'s interface needs to know if _it_ should add padding as well. Hence the extra flag to the `AES` initializer.

[See my full solution here.](https://github.com/downie/cryptopals/blob/main/Cryptopals/Challenges/Set1/Challenge07.swift)