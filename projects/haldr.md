---
layout: page
title: HALdr
excerpt: "HAL resource representations builder for spray-json"
comments: true
tags: [HAL, spray, json]
image:
  feature: blur1.jpg
---

{% include _toc.html %}

HAL resource representations builder for spray-json

## Usage ##

Cross-built for Scala 2.10 and 2.11

{% highlight scala %}
libraryDependencies += "com.github.ancane" % "haldr" % "0.1"

import haldr._
{% endhighlight %}

## API ##

`Resource` class acts as a hal builder. Main methods are `link` and `embed`.
There is support for uri templates.
You still have to provide `JsonProtocol` for your domain classes.
Additional uri properties may be added with `prop` method on links.
It's still possible to have `String` or spray's `Path` link with `props`.

### URIs ###

There are 3 string interpolators at your service:

* `u` - absolute uri
* `r` - relative uri (relative to parent resource uri)
* `t` - absolute uri template; checks for [RFC 6570](https://tools.ietf.org/html/rfc6570)) compliance

It possible to append path parts to uris with `/`.

### Simple resource ###

Use `u` and `r` interpolation for DRY links

{% highlight scala %}

Resource(
  Customer("test@mail.com"), u"/customers/deadbeaf")
   .link("books", u"/customers/deadbeaf/books")
   .link("files", u"/customers/deadbeaf/files")

Resource(
  Customer("test@mail.com"), u"/customers/deadbeaf")
  .link("books", r"/books")
  .link("files", r"/files")

{% endhighlight %}

{% highlight json %}

{
   "email": "test@mail.com",
   "_links": {
      "self": {"href": "/customers/deadbeaf"},
      "books": {"href": "/customers/deadbeaf/books"},
      "files": {"href": "/customers/deadbeaf/files"}
   }
}

{% endhighlight %}

### Link properties ###

Use `prop` on `u`, `r`, `t` or `String`.

{% highlight scala %}

Resource(
  Customer("test@mail.com"),
    u"/customers/deadbeaf"
      .prop("name", "John Doe")
      .prop("title", "Dear customer"))

{% endhighlight %}

{% highlight json %}

{
   "email": "test@mail.com",
   "_links": {
      "self": {
          "href": "/customers/deadbeaf",
          "name": "John Doe",
          "title": "Dear customer"
      }
   }
}

{% endhighlight %}

### Uri template ###

{% highlight scala %}

Resource(Map("email" -> "test@mail.com"), u"/customers/deadbeaf")
  .link("search-by-name", t"/customers?name={name}")

{% endhighlight %}

{% highlight json %}

{
   "email": "test@mail.com",
   "_links": {
      "self": {"href": "/customers/deadbeaf"},
      "by-name": {
          "href": "/customers?name={name}",
          "templated": true
      }
   }
}

{% endhighlight %}

### Embedding resource property ###

{% highlight scala %}

Resource(
  Customer("test@mail.com"), u"/customers/deadbeaf")
  .embed("car", Vehicle("BMW", "i3"))

{% endhighlight %}

{% highlight json %}

{
  "email": "test@mail.com",
  "_embedded": {
     "car": {
        "make": "BMW",
        "model": "i3"
     }
  },
  "_links": {
     "self": {"href": "/customers/deadbeaf"}
  }
}

{% endhighlight %}

### Embedding related resource ###

{% highlight scala %}

Resource(Customer("test@mail.com"), u"/customers/deadbeaf")
  .embed("car", Resource(Car("BMW", "i3"), u"/customers/deadbeaf/cars/bmw_i3"))
  .link("books", u"/customers/deadbeaf/books")

{% endhighlight %}

{% highlight json %}

{
  "email": "test@mail.com",
  "_embedded": {
     "car": {
        "make": "BMW",
        "model": "i3",
        "_links": {
           "self": {"href": "/customers/deadbeaf/cars/bmw_i3"}
        }
     }
  },
  "_links": {
     "self": {"href": "/customers/deadbeaf"},
     "books": {"href": "/customers/deadbeaf/books"}
  }
}

{% endhighlight %}

### Multilevel embedding with relative links ###

`embed` inside another `embed` with relative links.

{% highlight scala %}

Resource(Customer("test@mail.com"), u"/customers/deadbeaf")
  .embed("addressHistory",
    Resource(Address("Argonautenstraat", 3), r"/address/1")
      .link("business", r"/business")
      .embed("locations", Resource(Location(10.0, 12.0), r"/location")
        .link("hide", r"/hide") :: Nil) :: Nil)

{% endhighlight %}

{% highlight json %}

{
  "email": "test@mail.com",
  "_embedded": {
     "addressHistory": [{
        "street": "Argonautenstraat",
        "house": 3,
        "_embedded": {
            "locations": [{
               "lng": 10.0,
               "lat": 12.0,
               "_links": {
                   "self": { "href" : "/customers/deadbeaf/address/1/location"},
                   "hide": { "href" : "/customers/deadbeaf/address/1/location/hide"}
               }
             }]
        },
        "_links": {
           "self": {"href": "/customers/deadbeaf/address/1"},
           "business": {"href": "/customers/deadbeaf/address/1/business"}
        }
     }]
  },
  "_links": {
      "self":   {"href": "/customers/deadbeaf"}
  }
}

{% endhighlight %}

### URL encoding ###

`u`, `r` and spray's Path parts are encoded, when String is lifted to spray's Path type.
One important thing to know, is when your path segment contains '/' - correct way to have it encoded is following:

{% highlight scala %}

u"/books" / "Slash / book"

{% endhighlight %}

{% highlight json %}

"/books/Slash%20%2F%20book"

{% endhighlight %}

otherwise `/` will be parsed as path separator.

### URL template encoding ###

Put the part into string interpolator arguments, like this:

{% highlight scala %}

val book = "Slash / book"
Resource(Map("email" -> "test@mail.com"), u"/customers/deadbeaf")
  .link("book", t"/books/$book{?format}")

{% endhighlight %}

{% highlight json %}

{
   "email": "test@mail.com",
   "_links": {
      "self": {"href": "/customers/deadbeaf"},
      "book": {
          "href": "/books/Slash%20%2F%20book?{format}",
          "templated": true
       }
    }
}

{% endhighlight %}
