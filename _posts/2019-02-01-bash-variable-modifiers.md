---
layout: post
title: "Bash variable modifiers"
description: "The ${variable} modifiers"
modified: 2019-02-01
tags: [bash linux]
categories: [linux]
---

The `${variable}` syntax also supports a few of the standard bash modifiers as specified below:

- `${variable:-word}` indicates that if variable is set then the result will be that value. If variable is not set then `word` will be the result.
- `${variable:+word}` indicates that if variable is set then `word` will be the result, otherwise the result is the empty string.
In all cases, word can be any string, including additional environment variables.
