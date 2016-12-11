---
layout: page
title: Scala outline popup
comments: true
tags: [scala, outline, overview]
image: blur3.jpg

---

Opens a popup window containing classes, objects, types, defs and implicit vals from active scala file.
Keeps indentation of all the items. List is filterable. `Enter` on an item jumps to it's position in file.

<figure>
    <img src="/images/outline-popup.png" alt="Scala outline popup screenshot">
</figure>

## Dependencies

* [popup](https://github.com/auto-complete/popup-el)
* [scala-mode](https://github.com/hvesalai/scala-mode2)

## Installation

From melpa.

## Usage

Run from scala-mode: `M-x scala-outline-popup`

## Keymap

If you assign `scala-outline-popup` command a hotkey (ex., `C-e`), you may wan't same key to close the popup.
This can be done by adding the key to `popup-isearch-keymap`:

{% highlight lisp  %}

(require 'popup)

(define-key popup-isearch-keymap (kbd "C-e") 'popup-isearch-cancel)

{% endhighlight %}

## Initial item selection

It's possible to have closest, previous or next definition selected, when you open the popup.

{% highlight lisp  %}

(require 'scala-outline-popup)

(setq scala-outline-popup-select 'closest) // 'next or 'prev

{% endhighlight %}
