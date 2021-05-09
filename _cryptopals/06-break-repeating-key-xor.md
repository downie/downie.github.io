---
layout: post
title:  "Cryptopals 6: Break repeating-key XOR"
set: 1
challenge: 6
---
<!-- cspell:ignore xor'd -->
[This challenge](https://cryptopals.com/sets/1/challenges/6) takes the repeating-key XOR cipher that we built in [challenge 5] and asks us to break it. That is -- to decrypt something that's been encrypted with this particular method, without knowing the private key. 

This is a good amount of work. There are a lot of smaller problems that need to be solved before you can break this kind of encryption. While this challenge gives you a pretty clear roadmap of the steps needed, it can still be a pain to implement because you might not know which step isn't working if you're not getting a good result. Keep at it.

## Consider
The smaller problems involved in solving this are:
- Determining the key size 
- Computing the [binary hamming distance](https://en.wikipedia.org/wiki/Hamming_distance) between two strings
- Scoring decrypted English text

Fortunately, we solved that last one back in [challenge 3]. We can reuse our code from that solution to accomplish that here.

As for the other two, we can write some unit tests to make sure those smaller problems are solved correctly. 

## My Solution
Each of those smaller problems became methods in my `Analysis` class. 

The [`hammingDistance(_:_:)` method I had written](https://github.com/downie/cryptopals/blob/main/CryptoTools/Analysis.swift#L94-L112) was fairly succinct. XOR each byte together, then count the bits that are 1.

```swift
public static func hammingDistance(_ from: Data, _ to: Data) -> Int {
    assert(from.count == to.count)
    let difference = zip(from, to)
        .map { pair -> Int in
            let diffBits: UInt8 = pair.0 ^ pair.1
            var sum: UInt8 = 0
            (0..<UInt8(8)).forEach { shiftBy in
                sum += (diffBits >> shiftBy) & 0x01
            }
            return Int(sum)
        }
        .reduce(0, +)
    return difference
}
```

My method for determining key size wasn't quite as tidy. I ended up finding the hamming distance between all combinations of the first _five_ blocks in the encrypted data. When I did it with fewer, I would either get block sizes that were too small (often 2 or 5) or I would get ones that were large multiples of the correct block size.

[See my full solution here.](https://github.com/downie/cryptopals/blob/main/Cryptopals/Challenges/Set1/Challenge06.swift)