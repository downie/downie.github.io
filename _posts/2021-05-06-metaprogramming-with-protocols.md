---
// cSpell:words metaprogramming metatype metatypes
// cspell:ignore Cryptopals
layout: post
title:  Metaprogramming with Protocols in Swift
date:   2021-05-06 05:24:00 -0700
categories: swift
---

I was recently writing a test runner for [the Cryptopals Crypto Challenges](https://cryptopals.com), and I ran into a problem. I had isolated all of my individual challenges into their own type, but I wanted to have a means of running all of my solutions at once. I naively wrote something like this:

```swift
let allChallenges: [Challenge] = [
    Challenge01(),
    Challenge02(),
    Challenge03(),
    Challenge04(),
    ...
]

func run(challenges: [Challenge]) {
    challenges.forEach { challenge in
        challenge.run()
    }
}

run(challenges: allChallenges)
```

This is good code. It does what I wanted. However, a number of these challenges use a good amount of memory when they're initialized, and this approach pays the memory cost of initializing all of the challenges at once. I really only need these types to live for a short amount of time -- how could I write this so it only initializes these objects right before using them?

## Metaprogramming to the rescue
Metaprogramming is a big word for writing code that refers to the _types_ in your code rather than the actual instances of those types. Fortunately, Swift supports this style of metaprogramming. Let's see how we can use it.

First, some context. This was my `Challenge` protocol that all ChallengeXX types conformed to:

```swift
protocol Challenge {
    func run()
}
```

That worked when I didn't have to dynamically create instances of those types, but now I need a way to do that. So let's tweak that a bit:

```swift
protocol Challenge {
    init()
    func run()
}
```

That's better. Now anything that conforms to the `Challenge` protocol can be instantiated with an `init` call, and then can be run by calling its `run()` method.

### Using .Type and .self
Now that I can dynamically initialize and run these types, I need a strongly-typed way to describe that I'm referring to types -- not instances -- that conform to this protocol. Fortunately, each type in Swift has a `.Type` static property that refers to... well, to the type of that type. It's confusing and hard to talk about. Let's look at an example

```swift
let instanceOfChallenge: Challenge = Challenge01()

let typeOfChallenge: Challenge.Type = Challenge01.self
```

In the above, `instanceOfChallenge` is the problem I had before: it's a concrete instance of a type that conforms to the `Challenge` protocol. Meanwhile, `typeOfChallenge` refers to the type of something that conforms to the `Challenge` protocol. It's not instantiating anything in that case, just referring to the type of `Challenge01`.

And, notably, all types know how to refer to themselves with `.self`. 

## Let's fix our code
First, the declaration of `allChallenges`. Rather than initialize all my challenges at once, let's make that a list of the _types_ of challenges that could be initialized.

```swift
let allChallenges: [Challenge.Type] = [
    Challenge01.self,
    Challenge02.self,
    Challenge03.self,
    Challenge04.self,
    ...
]
```

Great! We've solved our first problem; we're no longer using a ton of memory when `allChallenges` is created. Next, let's change our `run(challenges:)` method to support challenge _types_ instead of challenge instances.

```swift
func run(challenges: [Challenge.Type]) {
    challenges.forEach { challengeType in
        let challenge = challengeType.init()
        challenge.run()
    }
}
```

A few things have changed here:
- The type of our `challenges` parameter is now `[Challenge.Type]` instead of `[Challenge]`
- We have to instantiate that type before running it now, so we had to add `let challenge = challengeType.init()`

Why `challengeType.init()` and not `challengeType()`? The type-followed-by-parenthesis (like `String()`) is syntactic sugar for explicitly calling the init method (`String.init()`). However, this syntactic sugar isn't extended to metatypes, and the compiler will complain.

> Error: Initializing from a metatype value must reference `init` explicitly

Once that's all done, our call to run the challenge remains the same:

```swift
run(challenges: allChallenges)
```

## Recap
Hopefully y'all found this useful. Metaprogramming like this is not a tool I reach for often, but it was _absolutely_ the right tool to solve this problem. Now I can continue running all my crypto challenge solutions without using more memory than I need. 