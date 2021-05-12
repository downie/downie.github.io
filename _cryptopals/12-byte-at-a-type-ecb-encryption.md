---
layout: post
title:  "Cryptopals 12: Byte-at-a-time ECB decryption"
set: 2
challenge: 12
challenge_title: Byte-at-a-time ECB decryption
challenge_link: https://cryptopals.com/sets/2/challenges/12
---
<!-- cspell:word crackin -->
[This challenge]({{ page.challenge_link }}) asks us to reveal data that is appended to our input to an electronic codebook (ECB) encryption method.

That shouldn't be possible! The whole idea of encrypting something is that it can't be decrypted without the key. But that's the point of these exercises in general, and this exercise specifically. This isn't magic, it's just code: cold, predictable code. Since we **know** how this encryption method works, that means we can rely on its predictability to do things that we're told shouldn't be possible.

Let's get crackin'.

## My Solution
Fortunately, this challenge gives a pretty clear walkthrough of the steps you need in order to decrypt your secret, appended `String`. They amount to:
1. Determine the block size 
1. Determine the encryption method
1. Trick the encryption method to encrypting just _one_ byte of unknown, appended data. 
1. Try ALL POSSIBLE VALUES for that last byte in the block, until you get an identical block encrypted. 
1. Go to step 3 again, and repeat until you know the entirety of the suffix.

### Wait... How Did That Work?
This all hinges on that third step. It's easier for us to guess-and-check one byte at a time than it is _the entirety of the appended data_. So let's see what that looks like with a 4-byte block where we append the word `CAT`.

Let's encrypt `AAA`, one byte less than the block size. We're asking it to encrypt

```
Input:     AAA
Plaintext: AAAC AT__
Encrypted: XXXZ XFQQ
```

Now, we're only looking at that first block here. We only know that block is encrypted with "AAA_" and we don't actually know what that fourth letter is. So, we try _everything_ until we get a match in that first block.

```
Input:     AAA?

Input:     AAAA
Plaintext: AAAA CAT_
Encrypted: XXXX ZXFQ
XXXX != XXXZ, no match.

Input:     AAAB
Plaintext: AAAB CAT_
Encrypted: XXXY ZXFQ
XXXY != XXXZ, no match.

Input:     AAAC
Plaintext: AAAC CAT_
Encrypted: XXXZ ZXFQ
XXXZ == XXXZ, matched! First letter is C.
```

Now that we've cracked the first byte, we send one fewer byte to get the _next_ byte of the secret data.

```
Input:     AA
Plaintext: AACA T___
Encrypted: XXZX FQQQ
```

Since we already know the first byte is `C`, we still only have to guess the last byte in that block in order go get a perfect match.

```
Input:     AAC?

Input:     AACA
Plaintext: AACA CAT_
Encrypted: XXZX ZXFQ
```

### One More Thing
That's great when the plaintext suffix added fits in a single block. But what happens when the encryption method is adding a suffix that's more than one block? You can't exactly send an input that's _less than zero_ to keep shifting bytes into that first block.

What you *can* do, is start looking beyond the first block.

Imagine the method was instead appending `CATSRULE` to your input before encrypting it. You can use the method above to decrypt `CATS`, but then we can't send any _less_ input. But what if we looked beyond the first block _now_?

```
Input:     AAA
Plaintext: AAAC ATSR ULE_
Encrypted: XXXZ XFBG 943Q
```

Because we've decrypted the first block, we still only have one byte of unknown data appended to our input. It's just appended in the second block rather than the first. Again, we already know it starts with `CATS`, so let's append that to our input along with our guess:

```
Input:     AAAC ATS?

Input:     AAAC ATSA
Plaintext: AAAC ATSA CATS RULE ____
Encrypted: XXXZ XFBX ZXFB G943 QQQQ

Input:     AAAC ATSB
Plaintext: AAAC ATSB CATS RULE ____
Encrypted: XXXZ XFBY ZXFB G943 QQQQ
```

It's an absolute pain to do this by hand, but thankfully computers don't get bored of it. Best leave it to them, then.

### Congrats 
This really isn't easy. Congrats on getting this far. 

[See my full solution here.](https://github.com/downie/cryptopals/blob/main/Cryptopals/Challenges/Set2/Challenge12.swift)