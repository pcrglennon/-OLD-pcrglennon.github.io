---
layout: post
title: "Ruby's Hash.new and {}"
date: 2014-06-10 21:18:18 -0400
comments: true
categories: [Ruby, Hashes, Flatiron&nbsp;School]
---


In a recent lab exercise at Flatiron, we were tasked with tallying the occurences of a word in a given sentence.  My first shot, as a Ruby newbie (Rubie?  Nuby?):

{% codeblock lang:ruby %}
def count_words(sentence)
  occurences = {}
  sentence.split(" ").each do |word|
    if occurences[word]
      occurences[word] += 1
    else
      occurences[word] = 1
    end
  end
  occurences
end
{% endcodeblock %}

And it worked.  But it felt bulky, like there must be a more concise way.  Looking over the spec file, I saw a comment which advised using ```Hash.new(0)``` rather than ```{}```.  There were two things I didn't understand.  1: that ```Hash.new``` and ```{}``` were different, and 2: that it looked like you could pass ```Hash.new``` an argument!

### Hash.new(0)?

Googling "Ruby Hash.new vs. braces" led to [this blog post](http://www.developwithpurpose.com/ruby-hash-awesomeness-part-1/
) elaborating on the differences between the two methods of hash construction.  Constructing as hash like this:
```ruby
hash = {"a" => 1, "b" => 2}
```
allows you to initialize the hash with data all on one line.  Whereas using ```Hash.new``` can only return an empty hash.  What ```Hash.new``` can do, though, is accept an argument.  As the author of that post points out, that argument is the default value for the hash, in place of ```nil```.  For example:

```ruby
hash_1 = {}          # Empty hash
hash_1[:a]           # nil

hash_2 = Hash.new(0) # Empty hash
hash_2[:a]           # 0
```

This was perfect!  If I could set the default value for my ```occurences``` hash to 0, I could eliminate most of the code in my each block, reducing it to:
```ruby
  sentence.split(" ").each do |word|
    occurences[word] += 1
  end
```

This works because ```occurences[word] += 1``` really means ```occurences[word] = occurences[word] + 1```. As the default value of ```occurences``` is 0, whenever a new word is found, ```occurences[word]``` is set to 0 + 1.  The tests pass, and I'm satisfied.  On to the next one.

### Hash.new([]): humbled again

Next problem: organize a list of songs, in the format of an "Artist - Song" String, into a hash of this format: artist => array of songs.  With my newfound knowledge of default Hash values, I jumped in like this:

```ruby
def organize_songs_by_artist(songs)
  songs_by_artist = Hash.new([])
  songs.each do |song|
    artist, song_name = song.split(" - ")
    songs_by_artist[artist] << song_name
  end
  songs_by_artist
end
```

With an empty array as the default Hash value, this should work just like the previous question!  Right?

Well no, of course it didn't.  I was returned an empty hash.  I look back over my code, and try some quick tests in irb, to no avail.  Puzzled, I throw myself once again upon the mercy of Google.

As often happens, I found a [StackOverflow question](http://stackoverflow.com/questions/2552579/ruby-method-array-not-updating-the-array-in-hash) from somehow having a very similar problem.  They're using yet another method of initializing a Hash, using a block:

```ruby
  h = Hash.new{Array.new}
```

I didn't know you could do that, but looking at the [Hash documentation](http://www.ruby-doc.org/core-2.1.2/Hash.html#method-c-new) explains that this method will cause the block to execute and return a default value.  That's pretty neat, but the same problem is present.  It looks like nothing shoveled onto the default empty array is saved.

[The top answer](http://stackoverflow.com/a/2552946/3536464) does an excellent job of explaining this confusing behavior.

I don't think I fully understand it, but what I got from this answer is that when I call ```songs_by_artist[artist] << song_name``` for an artist that is not yet in the hash, it evaluates to this: ```[] << song_name```.  So the value DOES get shoveled to an array, but since there is nothing that points to that array, it gets garbage collected.  The solution is to initialize my hash with a block that will, as a default, explicitly assign an empty array to the non-existant key.  That is, instead of initializing my hash with this:

```ruby
 songs_by_artist = Hash.new([])
```

 I can do this:

```ruby
   songs_by_artist = Hash.new{|hash, key| hash[key] = []}
```

So when ```songs_by_artist[artist] << song_name``` executes for a non-existant artist, the hash will create an empty array, assign it as the value of ```songs_by_artist[artist]```, and then shovel ```song_name``` onto that array.

My final code, which passes:

```ruby
def organize_songs_by_artist(songs)
  songs_by_artist = Hash.new{ |hash, key| hash[key] = [] }
  songs.each do |song|
    artist, song_name = song.split(" - ")
    songs_by_artist[artist] << song_name
  end
  songs_by_artist
end
```