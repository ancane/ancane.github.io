---
layout: post
title: "Spray: sanitize query params with shapeless"
description: "Sanitizing http query parameters in spray using shapeless"
modified: 2016-12-24
tags: [scala spray shapeless]
categories: [scala]
---

### Overkill imports

{% highlight scala %}
  import spray.routing._
  import shapeless._
  import poly._
{% endhighlight %}


### Define sanity check

{% highlight scala %}

def sanitize(x: String): String = 
  (escapeJavascript compose escapeHtmlTags compose ...)(x)

{% endhighlight %}

### Put polymorphic sanity adder

{% highlight scala %}
trait DefaultSanity extends Poly1 {
  implicit def saneDefault[A] = at[A](identity)
}
{% endhighlight %}

### Override sanity for strings

{% highlight scala %}
object Sanity extends DefaultSanity {
  implicit def saneString = at[String](sanitize(_))
  implicit def saneOptString = at[Option[String]](_.map(sanitize))
}
{% endhighlight %}

### Define query params wrapper classes (2 classes just for fun, could be single class)

{% highlight scala %}

case class InsaneParams(
  a: Int,
  b: String,
  c: Option[String]
)

case class SaneParams(
  a: Int,
  b: String,
  c: Option[String]
)
{% endhighlight %}

### Apply sanity to wrapper class

{% highlight scala %}

  def sanitizeParams(x: InsaneParams): SaneParams = {
    val saneTuple = InsaneParams.unapply(x).get.map(Sanity)
    (SaneParams.apply _) tupled saneTuple
  }
{% endhighlight %}


### Upgrade spray parameters directive with some sanity

{% highlight scala %}

  val saneParameters = 
    parameters(('a, 'b, 'c.?)).as(InsaneParams).map(sanitizeParams)

{% endhighlight %}

### At last, define spray route with params sanity check

{% highlight scala %}
   import spray.http.StatusCodes.OK

   val route = 
     get("sanity") {
       saneParameters { params =>
      	   // only sane params allowed here!
      	   complete(OK)
      	 }
     }
{% endhighlight %}
