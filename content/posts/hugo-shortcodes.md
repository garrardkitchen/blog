---
title: "Hugo Shortcodes"
date: 2020-04-06T15:31:12+01:00
draft: true
tags: [hugo, shortcodes, example]
---

## An example Shortcode

The theme I'm using doesn't offer out of the box anything to highlight important information.  This is the perfect use case for creating my first shortcode.

This shortcode is available [here](https://github.com/garrardkitchen/blog/blob/master/layouts/shortcodes/note.html)


### Information

#### Basic

{{< note >}}
Sample text
{{< /note>}}

#### With italics

{{< note italic="true" >}}
Sample text
{{< /note>}}

#### With header

{{< note title="With header">}}
Sample text
{{< /note>}}


### Warning

#### Basic

{{< note warning="true">}}
Sample text
{{< /note>}}

#### With italic

{{< note warning="true" italic="true" >}}
Sample text
{{< /note>}}

#### With header

{{< note warning="true" title="With header">}}
Sample text
{{< /note>}}

### Error

#### Basic

{{< note error="true">}}
Sample text
{{< /note>}}

#### With italic

{{< note error="true" italic="true">}}
Sample text
{{< /note>}}

#### With header

{{< note error="true" title="With header">}}
Sample text
{{< /note>}}

## How to create a shortcode
