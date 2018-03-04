---
layout: post
title: Simple GoLang concurrency example
description: this post explains how to create a simple GoLang goroutine and it's advantages. We show how simple is to create and manage them
---

A while ago, I had to create a web scrapper using Python. I did it using ***http.client*** for Python3. Soon, we started having problems with it's performance (we ended up doing about a million requests, so obviously it became slow).

Being a naturally parallelisable problem, we started to think how to make multiple requests at the same time (as I said, that was needed for performance purposes). Finally we struggled to find something suitable and that could make the code still readable. The problem is not Python not having enough libraries to do proper parallelisation; it's just that you have to change almost all the code, the result is not reabale... Seems like not the natural solution.

## My weekend mini-project

Obviously, now that I'm doing stuff with GoLang (just for personal projects), I wanted to do the same with it, and see how simple it is. It has to be really easy, as concurrency is a first-class citizen in Go <i class="em em-smile"></i>!

To achieve my aim, I'll create a first example with no GoRoutines, and a second example with them, to be able to see the differences.

## Solution with no goroutines

Let's see a simple example using a for loop and requesting stuff one at a time:

{% highlight go startinline %}
package main

import (
  "time"
  "log"
  "net/http"
  "os"
  "bufio"
)

func getUrls() []string {
  urls := []string{}

  file, err := os.Open("urls.txt")
  if err != nil {
    log.Fatal(err)
  }
  defer file.Close()

  scanner := bufio.NewScanner(file)
  for scanner.Scan() {
    urls = append(urls, scanner.Text())
  }

  return urls
}

func main() {
  start := time.Now()

  log.Printf("Program started...")

  urls := getUrls()

  for _, url := range urls {
    log.Printf("Got url " + url)

    resp, err := http.Get(url)

    if err != nil {
      panic(err)
    }

    log.Printf("Response status: " + resp.Status)
  }

  elapsed := time.Since(start)

  log.Printf("Time taken %s", elapsed)
}
{% endhighlight %}

Easy code! We read a urls.txt file, and process the URLs inside in a for loop. Nothing really complicated. Our simple and non-scientific test says: ***35.813 seconds for 50 requests*** (I repeated the test 10 times, and that's the mean). Reasonable!

## Solution with goroutines

Now let's do the same but using Goroutines (doing all of the requests in parallel). Let's see the code:

{% highlight go startinline %}
package main

import (
  "time"
  "log"
  "net/http"
  "os"
  "bufio"
  "runtime"
)

func getUrls() []string {
  urls := []string{}

  file, err := os.Open("urls.txt")
  if err != nil {
    log.Fatal(err)
  }
  defer file.Close()

  scanner := bufio.NewScanner(file)
  for scanner.Scan() {
    urls = append(urls, scanner.Text())
  }

  return urls
}

func main() {
  start := time.Now()

  log.Println("Program started...")

  urls := getUrls()

  channel := make(chan string, len(urls))

  for _, url := range urls {
    go func(url string) {
      resp, err := http.Get(url)
      if err != nil {
        panic(err)
      }

      channel <- resp.Status
    }(url)
  }

  for _, url := range urls {
    log.Printf("Got url " + url)

    status := <- channel

    log.Printf("Response status: " + status)
  }

  elapsed := time.Since(start)

  log.Printf("Time taken %s", elapsed)
}
{% endhighlight %}

We're creating a buffered channel and:

* We loop over the urls and send each url over a goroutine, where we do the actual request and pass back to the channel the response status (we hope it's 200 OK)
* We traverse again the URLs, get the result from the channel and print the response status (this is probably not in the same order as we sent the requests)

The results are, of course, way better: ***2.691 seconds to get the same 50 URLs*** (again, repeated this test 10 times, trying to be fair).

As you can see, the code is still basically the same, we're just doing some more stuff because we need to get the response status back from the channel; but basically the code remains the same!

That was it, my non-scientific test for GoLang goroutines, hope you enjoyed it <i class="em em-smile"></i>!
