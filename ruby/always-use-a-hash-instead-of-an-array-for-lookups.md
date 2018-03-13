---
id: 1
title: "Always use a Hash instead of an Array for lookups"
date: 2018-03-13T08:19:14Z
layout: default
tags:
  - ruby
  - lookup
  - performance
---

Whenever you need to store a sorted order of elements in your code, we intuitively think of using an array.

If you have some code like below, you are losing out on performance. The `.index` method on the arrays looks
for elements sequentially. So, if you have `n` products and `m` categories, your worst case performance would be `n*m`
  or O(n^2) which is very bad.

```ruby
category_priorities = [:electronics, :home, :supplies, :fruits, ...]

products.sort_by {|product| category_priorities.index(product.category)}
```

You can remedy this by using a `Hash` for lookups instead of an array with just a small change.
```ruby
category_priorities = [:electronics, :home, :supplies, :fruits, ...].each_with_index.map{|x| [x, i]}.to_h

products.sort_by {|product| category_priorities[product.category]}
```

Now your code is an order of magnitude faster!

I ran a small benchmark with the following code to see the difference in speed.

```ruby
require 'benchmark'

arr_lookup = (1..100).to_a
hash_lookup = arr_lookup.map{|x| [x, x]}.to_h

n = 10_000
Benchmark.bm do |x|
  x.report(:arr) { n.times { |i| arr_lookup.index(i) } }
  x.report(:hash) { n.times { |i| hash_lookup[i] } }
end
```

Even with an array of a 100 elements, the difference in speed is 7x, if you have a larger
array this difference is bound to be higher.

```
ruby-bm $ ruby arr_vs_hash_lookup.rb
       user     system      total        real
arr  0.010000   0.000000   0.010000 (  0.004564)
hash  0.000000   0.000000   0.000000 (  0.000640)
ruby-bm $
```
