---
title: Learning to Ruby
layout: post
date: 2016-03-18
---

When I first I started learning Ruby I thought it was a very strange language. Not necessarily the syntax of the language itself but rather the cultural conventions surrounding it. Rubyists have a very distinct style of doing things and seems to adhere very strong to that style.

I didn't have any particular problems with the style, but rather had not experienced such a fervor in the other languages I had worked. Rubyists seems to stress very readable code, such that its almost always preferable for it to read as much as english as possible.

A good example of this is a simple guard clause to check for nil. Coming from other language my instinct was to write this:

{% highlight ruby %}
def foo(bar)
  return if bar == nil
end
{% endhighlight %}

In Ruby I quickly had to adapt to change this to

{% highlight ruby %}
def foo(bar)
  return if bar.nil?
end
{% endhighlight %}

Ultimately its a small difference but something about my personal style I had to change.

##### Enumerables

Nothing demonstrates the ruby style like proper use of enumerables. When I first started learning I was resistant to them at first. The for and while loop were old friends, I was comfortable using them and didn't see the need to memorize a giant handful of new loops.

Learning map was a good example of the usefulness of ruby enumerables. It was more convient and readable to write a quick map method on an array rather then the old style with a for loop I would have written.

{% highlight ruby %}
y = []
for x in array
  y << x * 2
end
{% endhighlight %}

I could appreciate the elegance and simplicity of cutting the above down to

{% highlight ruby %}
y = x.map { |num| num * 2 }
{% endhighlight %}

But it wasn't until I learned Reduce that I began to see the light. Reduce showed me the power and simplicity of enumerables and now I somewhat dreading moving to other languages like javascript where I will no longer be able to feel thier warm embrace.

Summing elements in an array is something I have found myself doing fairly frequently in programming. Previously I would have done so like this:

{% highlight ruby %}
total = 0
for x in array
  total += x
end
{% endhighlight %}

With reduce:

{% highlight ruby %}
  array.reduce(0) { |num, sum| sum + num }
{% endhighlight %}

which seems like a small change, but is in my mind a large improvement over the way I used to do it. The best part about enumerables is how readable they make your code. Whenever I looked at a loop before learning Ruby, I would have to strain a bit to find out what that particular loop is doing. Comments were often necessary to explain the generic loop structure and bad variables name such as x, y, and i.

With Ruby I can look at a simple enumerable like reduce and know they they are probably summing a total of elements. Or I can look at sort_by and know they are sorting the array. Or I can look at select and instantly know they are creating a new array with elements that meet the block conditions.

Working on our latest project Black Thursday has made me curse ruby somewhat. I know our 90 second test suite would run in a fraction of the time if it was coded in something like C++.

But that curse is gone pretty quickly when I look at how readable and concise much of my code is because of the power of enumerables. I can step away from a long class and come back days later and easily reacquaint myself with is being done mostly just by reading what enumerables I used.
