---
layout: post
title: "For the Love of Loops"
date: 2018-03-27 19:54:30 +1100
categories: go
---

Continuing on from the theme of Go being a statically typed language that feels like a dynamically typed language, I want to talk about looping.

Forget about your `while` and `do...while`, there is just `for`. The `for` loop does everything in Go.

As a plain `for` loop:

{% highlight go %}
for i := 0; i < 10; i++ {
    fmt.Println(i)
}
{% endhightlight %}

As a `while` loop:

{% highlight go %}
i := 0
for i < 10 {
    fmt.Println(i)
    i++
}
{% endhighlight %}

As a `while(true)` infinite loop:

{% highlight go %}
for {
    fmt.println(i) // intentional syntax error in case anyone is crazy enough to run this
}
{% endhighlight %}

As a `foreach` loop that iterates through an iterable:

{% highlight go %}
s = []string{"one", "two", "three"}
for i, v := range s {
    fmt.Printf("Index: %v; Value: %v\n", i, v)
}
{% endhighlight %}

I think the `foreach` looping style above really makes it feel like a dynamically typed language where such functionality is missing from some statically typed languages.

The only type of loop that perhaps Go doesn't do well is the `do...while` loop where the code in the loop must run once, and may dynamically break out of the loop (if you know in advance how many times you need to iterate, you would just use a standard `for` loop).

I would surmise that this problem may need to be defined in a different way so it could run in an infinite loop with a check for the triggering condition to break out of the loop.
