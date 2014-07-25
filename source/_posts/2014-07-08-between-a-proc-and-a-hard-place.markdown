---
layout: post
title: "Between a Proc and a Hard Place"
date: 2014-07-08 12:50:36 -0400
comments: true
categories: [Ruby, Procs, Blocks]
---

Before I get into talking about Procs, I should address something.  If you came here because StackOverflow is down and this blog showed up on Google (that's ambitious), skip to the next section.

I never felt the desire to write a blog before The Flatiron School, where student blogs are required.  There are so many blogs, and writing another just seems to me like a drop in the bucket.  And the bucket is more like a reservoir.  There's a lot of great blogs on the internet, and a lot of crappy ones.  And between them, it seems to me like everything of interest has already been written and re-written.  For instance, while researching how Procs work, I found two excellent posts from [skorks.com](http://www.skorks.com/2013/04/ruby-ampersand-parameter-demystified/) and [iain.nl](http://www.iain.nl/going-crazy-with-to_proc).  Really, those posts are much better written than this one will be, and I heartily encourage anyone reading this (if there is anyone reading this) to check those out first.  Effectively I'm just rehashing what I learned from those blogs.

So why blog?  Well, for selfish reasons.  Writing my last two blog posts really helped me understand the concepts I was writing about, much like explaining your code to someone else helps you understand as well.  So maybe this won't help anyone but myself, but as long as Github Pages are free, I'll probably keep writing for a while.  This is the reasoning for the subtitle of this blog: at the end of the day, it does feel like I'm just talking to myself, explaining things to nobody.

Alright, that's enough of that.  On to the actual post.

##Ok, Procs

Put simply, a Proc is a block object.  That is, you can assign a Proc to a variable and re-use it.  blocks are one of the few non-object elements in the Ruby language, and Procs are used to remedy this.

```ruby
a_block = { |x| x + 1 }          #=> Syntax Error!
a_proc = Proc.new { |x| x + 1 }  #=> Success!
[1, 2, 3].map(&a_proc)           #=> [2, 3, 4]
```

That ampersand before a method argument (in this case, `a_proc`) is used to explicitly convert the method argument to a Proc.  In this example, the conversion is not necessary, but the ampersand is *still required.*

Procs are also used in method definitions, to assign a variable to whatever block is passed to the method.  This is incredibly useful when you want to re-use the block within the method body.

Here's an example lovingly stolen from my Flatiron classmate [Brandon Hopkins](http://bajh.github.io/).  This is the `each` method from a Binary Search Tree implementation, which performs an in-order traversal of the tree:

```ruby
def each(&block)
  if self.left
    self.left.each(&block)
  end
  block.call(data)
  if self.right
    self.right.each(&block)
  end
end
```

The method takes a block, which is converted to a Proc and assigned to the variable `block`.  `block` is called with `data` (the value at this node of the BST) and reused for the `each` method of both the `left` and `right` sub-trees of the BST.