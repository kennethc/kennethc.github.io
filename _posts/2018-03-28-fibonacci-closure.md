---
layout: post
title: Fibonacci Closure
date: 2018-03-28 21:36:55 +1100
categories: go fibonacci closure
---

I was going through the Tour of Go, and came across the [Fibonacci Closure Exercise][go-fib]. It wasn't my first time solving a Fibonacci problem, and it wasn't the first time I have heard of a closure, but it _was_ my first time solving Fibonacci with a closure with some interesting constraints thrown in.

The sequence has to be solved with a closure (a function returning a function in simple terms) that is invoked inside a loop. The function signature looks like this:
```
func fibonacci() func() int {
}
```

This presents some interesting challenges as I wasn't allowed to pass in the initial sequence of "0, 1" to kick start the process - which would have made it too easy. So I started thinking about what I needed to achieve: I needed to start from 0 with no seed, and produce a Fibonacci sequence iteratively. The tricky part will be self-seeding my algorithm to produce the correct results.

It took me a few attempts to experiment and fail, to finally come to a thought process that actually seemed like it will work. I started experimenting with 2 variables initialised to 0 and 1, and attempted to solve the sequence - it didn't quite work. So I thought, maybe I can add a third variable, solve the next sequence and output the last solved sequence, but that fell just a bit short.

After a bit more thinking, I finally came to a [solution][fib-closure]. I needed the "0, 1" seed, I needed a place to store a deferred sequence, and I needed a place to store the next sequence. I needed 4 variables to store a rolling sequence: I needed 2 of them to be my buffer and seed, I needed 1 before my buffer to serve as a deferred return value, and I needed to calculate the next sequence after my seed.

If I initialise my starting state to be "0, 0, 1, 0", during every iteration, I will shift all my values to the left (the "rolling" mechanism), calculate the next sequence based of the values in my buffer, and return the left-most value. This is visualised in the table below:

| return | buffer | buffer | compute | output |
|:------:|:------:|:------:|:-------:|:------:|
| 0      | 0      | 1      | 0       |        |
| 0      | 1      | 1      | 2       | 0      |
| 1      | 1      | 2      | 3       | 1      |
| 1      | 2      | 3      | 5       | 1      |
| 2      | 3      | 5      | 8       | 2      |
| 3      | 5      | 8      | 13      | 3      |

[go-fib]: https://tour.golang.org/moretypes/26
[fib-closure]: https://github.com/kennethc/kata/tree/master/fibclosure
