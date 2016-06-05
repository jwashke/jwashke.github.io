---
title: Huffman Coding in Ruby
layout: post
date: 2016-04-12
---

Theres a lot of technologies that seem like just magic to me. I know they’re not magic,
but I just assume its thing handled by people far smarter than me.
Compression was always one of those things to me. Compression tools like 7zip take a file, and magically makes it smaller.
I’m a student at the Turing School of Software and Design, and recently someone asked me to build a very simple compression
tool using ruby. While building the tool I had to learn how compression actually works, or at least the basics of it.

The compression tool I built uses Huffman coding, a technique created by David Huffman in 1952. In normal text, each character is stored
as a single byte of 8 bits, which gives 256 possible combinations. Including letters, numbers, and special characters, you can only type 95 characters on a standard keyboard.
The basics of Huffman coding is that you reassign the number of bits required to represent each character, based on the frequency of the character in the message.
The huffman coding implementation I built was a simple ruby script, that took in a string message and output a shortened bitstring of the message.

The first step was to get the message, and get a count of all the characters in the message.
{% highlight ruby %}
  # build a hash where each character is a key, while the value is the number of times that character appears in the message
  def character_counts
    count = Hash.new(0)
    @message.chars.each do |char|
      count[char] += 1
    end
    count
  end
{% endhighlight %}

The next step is to create a priority queue of the characters as nodes to be stored in a trie
{% highlight ruby %}
  def priority_queue
    queue = character_counts.map { |key, value| Node.new(key, value) }
  end
{% endhighlight %}
Then sort the queue based on the frequency of the characters so that the lowest appearing characters have the highest priority
{% highlight ruby %}
  def sort_queue(queue)
    queue.sort_by { |node| node.number }
  end
{% endhighlight %}
While there is more than one node in the queue:
Remove the two nodes of highest priority (lowest probability) from the queue
Create a new internal node with these two nodes as children and with probability equal to the sum of the two nodes' probabilities.
Add the new node to the queue.
Build the tree with by removing the two highest priority nodes from the queue, creating a new internal node with those two nodes as its children, and its frequency equal to the sum of its children. Then add the new node to the queue. Repeat
this process until there is only 1 node left in the queue.
{% highlight ruby %}
  def build_tree
    queue = priority_queue
    until queue.size == 1
      queue = sort_queue(queue)
      # remove the two nodes with the highest priority
      node1 = queue.shift
      node2 = queue.shift
      # create a new internal node with these two nodes as children and with
      # probability equal to the sum of the two nodes probablities
      node3 = Node.new(node1.string + node2.string,
                       node1.number + node2.number)
      node3.left  = node1
      node3.right = node2
      # add this new node to the queue,
      queue.push(node3)
    end
    @root_node = queue.first
    # the last node is the root node and the tree is complete
    # now count the number of nodes and leaf nodes
    "Tree made with #{count_nodes(@root_node)} total nodes and #{count_leaves(@root_node)} leaf nodes"
  end
{% endhighlight %}
Wikipedia has a good visual representation of the process
![Huffman](https://upload.wikimedia.org/wikipedia/commons/a/ac/Huffman_huff_demo.gif)

Finally, encoding the message is just mapping through the message, changing each character into its new bitstring form.
You encode each character by traversing down the tree, starting at the root node, until you find that characters leaf. Everytime you traverse to the left, you add a 0, every time you traverse right you add a 1. the string of 0s and 1s you get at the end is your new encoded bitstring for the character.
{% highlight ruby %}
  def coded_array
    @message.chars.map { |char| char_to_code(char) }
  end

  def char_to_code(char, node = @root_node)
    bytes = ""
    if node.left != nil and node.left.string.include?(char)
        bytes = "0" + char_to_code(char, node.left)
    elsif node.right != nil and node.right.string.include?(char)
        bytes = "1" + char_to_code(char, node.right)
    end
    bytes
  end
{% endhighlight %}

With this we can take a message
{% highlight ruby %}
2.3.0 :008 > encoder.message
 => "Thats not moon, thats a space station"
{% endhighlight %}
And encode to a smaller bitstream form
{% highlight ruby %}
2.3.0 :014 > encoder.build_tree
=> "Tree made with 27 total nodes and 14 leaf nodes"
2.3.0 :015 > encoder.coded_array.join
=> "001100010100111011110000010111110001110100100001011111101110010100111011110100110011101110100101101010011001111110011110101010000"
2.3.0 :016 > encoder.original_bitlength
=> 296
2.3.0 :017 > encoder.coded_bitlength
=> 129
{% endhighlight %}

To decompress the message. You need to reconstruct the tree, and run through the process in reverse.

The amazing thing about learning programming is the more I learn the more it dispels the notion of technology as magic, at least as magic
insofar as that its for people far smarter than me. I used think I could learn to build some cool stuff. Now I believe I can learn to build anything
given enough time and motivation.
