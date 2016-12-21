---
layout: post
title: popup-imenu
tags: [emacs, popup, imenu]
image: /images/popup-imenu.jpg
---

I came across [popup-switcher](https://github.com/kostafey/popup-switcher) couple years back, loved using interfaces based on [popup.el](https://github.com/auto-complete/popup-el) since then.
Idea of presenting file outline for scala-mode came naturally and that's how `scala-outline-popup` was created, which then transformed into more general imenu list viewer - [popup-imenu](https://github.com/ancane/popup-imenu). Thing I like the most is that it opens at point, so I don't move my lazy eyes much.

<!--more-->

![popup-imenu gif](/images/popup-imenu.gif)

## Run
`M-x popup-imenu`

## Customize

### popup-imenu-position

Control popup horizontal positioning with:

* `'center` - open popup at window center
* `'fill-column` - center relative to fill-column (default setting)
* `'point` - open popup at point

```lisp
(setq popup-imenu-position 'point)
```
### popup-imenu-style

* `'flat` - flatten hierarchical imenu
* `'indent` - use whitespace indentation to show hierarchical imenu

```lisp
(setq popup-imenu-style 'indent)
```

### popup-imenu-fuzzy-match
Flx matching is enabled by default.
Disable it with:

```lisp
(setq popup-imenu-fuzzy-match nil)
```

## Keymap

```lisp
(global-set-key (kbd "C-e") 'popup-imenu)

;; Close the popup with same key
(define-key popup-isearch-keymap (kbd "C-e") 'popup-isearch-cancel)
```
