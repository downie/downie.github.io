---
layout: post
title:  "Cryptopals 1: Convert hex to base64"
set: 1
challenge: 1
challenge_title: Convert hex to base64
challenge_link: https://cryptopals.com/sets/1/challenges/1
---
In [this challenge]({{ page.challenge_link }}) we're asked to implement our own base64 encoding method. And while that's _all_ it asks, it'd probably be a good idea to write our own decoding method as well.

It's harder than it looks! Especially if you haven't had much experience working with individual bits. However, you're in luck, that experience is exactly what this challenge provides.

## Consider:
- Use a fixed-width unsigned integer (`UInt8` in Swift) to represent each byte.
- Use bytes as an intermediary -- decode the hex string into bytes, then encode those bytes into base64. Those steps will be useful later, rather than operating on strings alone.
- The [Wikipedia article on Base64](https://en.wikipedia.org/wiki/Base64) is an excellent documentation of what's expected from your code
  - There are great, small examples in here. Use these as unit tests before tackling the whole challenge.

## My Solution
I made a `DataDisplay` namespace with four methods:
1. `data(forHexString:)`
1. `hexString(for:)`
1. `data(forBase64String:)`
1. `base64String(for:)`

The first two translate between `Data` and `String` for hex strings, whereas the last two translate between `Data` and `String` for base64 strings.

Notably, I didn't write these as extensions on `Data` or `String`. Those are natural places for these methods. So natural, in fact, that `Foundation` provides them. In my first attempt at this, I added my own extensions, and then got confused as to if I was using my code or `Foundation` code when working with these methods. 

### Hex Strings
You can see the full implementations of [`data(forHexString:)`](https://github.com/downie/cryptopals/blob/main/CryptoTools/DataDisplay.swift#L18-L33) and [`hexString(for:)`](https://github.com/downie/cryptopals/blob/main/CryptoTools/DataDisplay.swift#L38-L42) in the repo. The heavy lifting is done in two `Foundation` methods:

- `UInt8(_: String, radix: Int)` takes a 2-digit hex number and converts it to a byte when radix is 16.
- `String(_: UInt8, radix: Int)` takes a byte and converts it to a hex number when radix is 16.

### Base64 Strings
This was trickier.

First, I made lookup methods for converting between the index and the character values in [the Base64 index table](https://en.wikipedia.org/wiki/Base64#Base64_table). Smaller, more easily tested problem solved.

Then, the [details on padding](https://en.wikipedia.org/wiki/Base64#Decoding_Base64_with_padding) and [thorough documented examples](https://en.wikipedia.org/wiki/Base64#Examples) gave me an insight. Three characters of ASCII always translate to four characters in base64. Even when three ASCII characters aren't present due to the padding, it will be represented by four Base64 characters.Those blocks line up perfectly at the binary level. In other words, twenty-four bits directly translate between either encoding.

While there isn't a `UInt24` type _perfectly_ suited to this purpose, you can store all those bits in a `UInt32`, which is precisely what I did.

- [To convert bytes from Base64](https://github.com/downie/cryptopals/blob/main/CryptoTools/DataDisplay.swift#L93-L135), load four characters into a `UInt32`, then re-interpret that as three bytes. 
- [To covert Base64 from bytes](https://github.com/downie/cryptopals/blob/main/CryptoTools/DataDisplay.swift#L140-L182), load three bytes into a `UInt32`, then re-interpret that as four Base64 characters.  
<!-- 
### Takeaway
This was a useful learning experience and an introduction to these challenges. I took away a few things:

1. Always work on bytes. Even if the challenge refers to "strings", it's reasonable to take an unnamed step of converting these inputs to bytes. This challenge even calls that out as a "Cryptopals Rule", but it took me stumbling over this for a few challenges to really internalize it.
2. Look for smaller problems to solve when the b -->

[See my full solution here.](https://github.com/downie/cryptopals/blob/main/Cryptopals/Challenges/Set1/Challenge01.swift)