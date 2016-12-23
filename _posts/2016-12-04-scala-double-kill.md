---
layout: post
title: Multi-kill
description: "scala sys error"
modified: 2016-12-04
tags: [scala]
categories: [scala]
image:
    feature: feature.jpg
    credit: dargadgetz
    creditlink: http://www.dargadgetz.com/ios-8-abstract-wallpaper-pack-for-iphone-5s-5c-and-ipod-touch-retina/
---

{% highlight scala %}

scala> sys.error(sys.error(sys.error("...")))

java.lang.RuntimeException: ...
  at scala.sys.package$.error(package.scala:27)
  ... 31 elided

{% endhighlight %}
