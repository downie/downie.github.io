---
layout: post
title:  "Cryptopals 5: Implement repeating-key XOR"
set: 1
challenge: 5
challenge_title: Implement repeating-key XOR
challenge_link: https://cryptopals.com/sets/1/challenges/5
---
<!-- cspell:ignore xor'd -->
[This challenge]({{ page.challenge_link }}) evolves our XOR cipher to use more than one byte. The key is now three bytes instead of one, although it's still repeated to match the length of the input. 

This is beginning a common pattern throughout these challenges. We're iterating on our encryption methods to make them stronger. Once they're stronger, then we figure out how to break it. We just pushed the limits of our repeating-byte XOR and proved in [challenge 4](./04-detect-single-character-xor) that if even a small region of a large file used that encryption method, we could decrypt it without knowing the key. 

So now we make the encryption stronger.

## My Solution
My first attempt was pretty naive. I simply repeated the key until it was the size of the data. Then I've got a `FixedXorCipher` again, just like we did in [challenge 2](./02-fixed-xor).

That worked, but I wanted to see if I could achieve the same result without needing to use that much memory. That resulted in `RepeatingXorCipher`, a `Cipher` with the following encrypt method:

```swift
public func encrypt(data: Data) -> Data {
    let bytes = data
        .enumerated()
        .map { pair in
            pair.element ^ key[pair.offset % key.count]
        }
    return Data(bytes)
}
```

[See my full solution here.](https://github.com/downie/cryptopals/blob/main/Cryptopals/Challenges/Set1/Challenge05.swift)