---
layout: post
title: "Reflecting on Go"
date: 2018-03-25 19:26:20 +1100
categories: go
---

Go tries to feel like a dynamically typed language while being statically typed. Sometimes this introduces weird things like arrays can be compared with each other, but not slices.

{% highlight go %}
package main

import (
	"fmt"
)

func main() {
	a := [3]int{1, 2, 3}
	b := [3]int{1, 2, 3}
	c := [3]int{1, 1, 2}
	fmt.Println(a == b)
	fmt.Println(a == c)
}
{% endhighlight %}

The above outputs:

```
true
false
```

Now switch to slices:

{% highlight go %}
package main

import (
	"fmt"
)

func main() {
	a := []int{1, 2, 3}
	b := []int{1, 2, 3}
	c := []int{1, 1, 2}
	fmt.Println(a == b)
	fmt.Println(a == c)
}
{% endhighlight %}

And you get:

```
prog.go:11:16: invalid operation: a == b (slice can only be compared to nil)
prog.go:12:16: invalid operation: a == c (slice can only be compared to nil)
```

To compare slices, you need to use the `reflect` package:

{% highlight go %}
package main

import (
	"fmt"
	"reflect"
)

func main() {
	a := []int{1, 2, 3}
	b := []int{1, 2, 3}
	c := []int{1, 1, 2}
	fmt.Println(reflect.DeepEqual(a, b))
	fmt.Println(reflect.DeepEqual(a, c))
}
{% endhighlight %}

```
true
false
```
