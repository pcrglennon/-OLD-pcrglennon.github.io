---
layout: post
title: "The (&amp;:) syntactic sugar"
date: 2014-06-25 21:43:44 -0400
comments: true
categories: [Ruby, Syntactic&nbsp;Sugar]
---

While trying to figure out how to capitalize every word in a sentence (for a Ruby project), I came across some syntax I'd never seen before:

```ruby
words.map(&:capitalize)
```

I tried it out in irb, and sure enough, it seemed to do what I wanted.

```ruby
words = ['hello', 'world']
words.map(&:capitalize)    #=> ['Hello', 'World']
```

It looks like the (&), together with a symbolized method name, applys that method to each element in the collection.  And that's pretty much the short answer.  So if you just needed to figure out how to use it, no need to go further!

### It's actually a bit more complicated

I was reminded of a similar shortcut in Scala:

```scala
words.map(_.capitalize)
// Equivalent to
words.map(word => word.capitalize)
```

The `_` acts as a placeholder for the current element being iterated over.  With this in mind, my first thought of the ruby syntax was that `&` was encapsulating the current element, along with a `send`.  So it would really boil down to something like this:

```ruby
words.map { |word| word.send(:capitalize) }
```

Though it effectively works this way, the actual process going on is more complicated.  Probably more complicated than I can adequately explain, but I'm going to try in my next post.