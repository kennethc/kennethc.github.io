---
layout: post
title: "Unit Testing in Go"
date: 2018-03-27 19:08:25 +1100
categories: go unit testing
---

Go is a really simple language to get up and go. So simple that sometimes it is incredible.

Take the [Testing][go-test] section in the How to Write Go Code document for example. The documentation is concise and to the point. By the end of that section you will be writing unit tests quite effectively. The example also shows you how to write effective test code, which short cuts common TDD approaches of writing many small tests, then refactoring into a single test to remove duplication.

Some of the unit testing framework documentation that I've read tends to go into too much detail too soon, and assumes you have domain knowledge about unit testing. This is also another distinguishing point between Go and other languages. Where other languages use domain specific language like _assert_ or _assertEquals_ which looks nothing like the language itself, Go does away with all that and requires you to make comparisons in Go. This is a very low barrier to entry for people who already know programming, but not necessarily unit testing.

[go-test]: https://golang.org/doc/code.html#Testing
