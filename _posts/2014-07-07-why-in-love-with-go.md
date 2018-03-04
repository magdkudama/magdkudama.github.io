---
layout: post
title: Why I'm in love with Go?
description: this post explains why I like golang, and why you should give it a try
---

I really enjoy trying new languages and tools completely unknown to me, and learn from them. It can only make us better at what we do, so no harm, be brave and try!

When I first decided to give GoLang a try, I was pretty sure it was *just another Google tool*, which is probably trend because of the support it gets from the giant company.

![Golang logo]({{ site.url }}/assets/201407/gophers.png)

Basically, their promise sounded weird to me: ***Get the C performance, but write as you do in a dynamic language***. But you know, that's impossible! *What a hoax! And WTF is that logo?*, I thought. Despite all my skepticism, I decided to give it a try. *I'll write some stuff, and that's it*. And "some more stuff" after "more stuff", It's been six months since I wrote my very first line of code!

How is that possible? I promised myself just to write some simple stuff, and forget about it! What actually made me fall in love with it? As good stories should be, let's start with some history.

## Why Go?

The very first question is: ***Why reinventing the wheel with yet another language? We have enough!***. That's true, but let's refine this statement. Before Go, you had to decide what's your choice:

* C / C++ : amazing performance, difficult to write (and error-prone)
* *Intermediate* languages (JVM, .NET): good performance, a bit easier to write
* Dynamic languages (Python, PHP, Ruby): bad performance, fun to write

Where's the *amazing performance, fun to write* thing? Can't see it <i class="em em-smile"></i>. That's Go target! We'll try to explain that in more depth later (or just give it a try and realise that by yourself).

One more important point is that hardware is evolving really fast, with muticore machines. But most of the languages are not able to take profit of that! Go does that by default (no plugins, no libraries, nothing... it's a core feature of the language).

Finally, it really sucks how slow is the compilation process in most of the languages! Go is one of the fastest ones I've seen so far. That's also something done on purpose, for sure. Most of the programs should be built in less than a second or two! <i class="em em---1"></i> for that!

Okay, now that we have a better understanding of why Go is needed, let me try to explain some (in my opinion) of the Go's bullet points. After reading those points, you should want to try the language. If so, then I did my job:

## Expressiveness & simplicity

If you know Python, you'll probably feel quite comfortable in just a couple of hours for basic (and not so basic) operations. If you don't know it, don't worry, it'll be three hours instead, so not really a big deal!

{% highlight go startinline %}
package main

import "fmt"

func main() {
	fmt.Println("Hello, Go!")
}
{% endhighlight %}

Just try to do a simple program, and you'll see it's quite easy to write and get things done!

Also Go compiler is really smart, so it can infer the type of a variable automatically. That makes code really easy to write and reason about, as in dynamic programming languages! There's no type hierarchy, something that also helps. Less boilerplate code is always a good idea (remember that boilerplate != strict typing; boilerplate is just boilerplate code, and sucks!).

Go has the concept of interfaces, but no keyword ```implements``` exists. Just implement the methods, and you're actually implementing the interface. Concept of classes is absent, but you can see structs as classes, also (not exactly the same). Functions are first class citizens (closures available, yay)!

No ```public```, ```protected``` nor ```private``` keywords (as you've probably realised, Go has a few number of reserved words, which implies less things to learn <i class="em em---1"></i>!). Just make your variable or method with the first letter uppercase, and it's considered exported (usable from the outside).

Probably, the only "difficult" thing to learn from those coming from dynamic languages are pointers... But don't be afraid, they're easy! And Go does not have pointer arithmetic as C, so it doesn't allow you to do crazy things, don't worry :wink:

## Standards

I hate when I see code formatted following 2000 different standards, or with an invented one per each piece of code. It makes code harder to read. Go solves that with the command ```go fmt```. Run it and it'll make your code follow standards. Oh god, I really love that command!

Even if you don't run that command (why won't you?), Go compiler has some rules you have to respect if you want to build your code, such as disallowing opening braces to be in the next line of a for loop:

{% highlight go startinline %}
for i:=0; i<10; i++ {
	fmt.Printf(“%v\n”, i)
}


for i:=0; i<10; i++
{
	fmt.Printf(“%v\n”, i)
}
{% endhighlight %}

Those rules also make the compile step faster <i class="em em---1"></i>!

And what about documentation? No worries, Go heroes have though about that before us! ```go doc``` extracts and generates documentation from source code comments. You can also serve it and see the result in your preferred browser!

I'm starting to like Gophers!

## Deployment

Despite I have no real experience with the language professionally (so I won't be asserting things here), Go builds statically linked binaries. What does that imply? Well, your deployment process gets reduced to pushing a single binary with no dependencies (all dependencies are bundled).

Forget about tedious deployment processes with a bunch of software and machinery involved! Let's make things as easy as they should be! Don't overcomplicate <i class="em em-smile"></i>

Wow, that deserves another <i class="em em---1"></i>!

## Concurrency primitives

Goroutines are a simple a way to execute multiple functions concurrently. It's not a thread (it's more lightweight than it). As Go documentation says, you can have thousands of threads with no performance degradation. And that's true, as I tried it during my *language testing*.

Sounds like a complex thing right? Yea, concurrency is always complex! Well, it's not. Let's see a small example that does not use concurrency:

{% highlight go startinline %}
func main() {
	for i:=0; i<10; i++ {
		func(i int) {
			fmt.Println(i)
		}(i)
	}
}
{% endhighlight %}

And now, try to figure out the difference with it's concurrent version:

{% highlight go startinline %}
func main() {
	for i:=0; i<10; i++ {
		go func(i int) {
			fmt.Println(i)
		}(i)
	}
}
{% endhighlight %}

Oh yea, it's just a matter of adding a ```go``` keyword before the function name. I know you're thinking I'm kidding, but I'm not, sorry for being so simple <i class="em em-smile"></i>. This is an easy example, obviously, but in a goroutine you could be doing anything (perform API calls, database queries...).

Also, synchronization between goroutines is for free, by using ```channels``` (I can explain concurrency in a separate blog post, but it's not my intention now).

## Conclusions

There are some other points that I really like and are not mentioned here, such as the big (and growing) community it has, the way libraries are structured, and the absence of a central repository for package management (yes, I like that, don't you?)!

But as we all know, perfection does not exist. There is also something I really dislike about Go, and that's error handling. Probably it's because I haven't really experienced with the language deep enough, or that I think too much in the way other languages do error handling, but I don't like it in Go (at least, not yet) <i class="em em-cry"></i>

Ok, that's it. These are my bullet points. Maybe you should *give it a Go*! I hope after this reading you're (at least) interested in knowing more about the language. If that's true, then my recommendation is to start by reading the [GoLang book](https://www.golang-book.com/). Easy to follow and understand. If you want to write some code, you can use [golang playground](https://play.golang.org/), which works for most of the cases.

If you like videos, please don't miss those ones:

* [Writing, building, installing, and testing Go code](https://www.youtube.com/watch?v=XCsL89YtqCs)
* [Concurrency is not parallelism](https://www.youtube.com/watch?v=cN_DpYBzKso)
* [Go concurrency patterns](https://www.youtube.com/watch?v=f6kdp27TYZs)

![Gopher flying]({{ site.url }}/assets/201407/gopher-flying.jpg)

No need to say again, I'm in love with that programming language! I'm still a newbie, but that's what ***baby gophers*** are for. And now I even like the logo! It's quite cool!

Go Gophers! <i class="em em-smile"></i>
