---
title: "Hugo Shortcodes"
date: 2020-04-05T17:31:12+01:00
draft: true
---

## Adding images to blog

For adding images to my blog through VSCode, I use an extension called Paste Image.  [Click here](https://github.com/mushanshitiancai/vscode-paste-image) for it's GitHub repos.

It comes with many configuration settings.  I've used 2 so far.  These settings enable me to (1) place the resulting image into the correct folder location and (2) to prepend the markdown link syntax so it points correctly to the image's location in my folder structure.

VSCode `settings.json`:

```json
"pasteImage.prefix": "../",
"pasteImage.path": "${currentFileDir}/img/",
```

When I press `ctrl+Alt+v` this would be injected into my markdown:

```
![](../img/2020-04-06-09-56-57.png)
```

And the .png file will appear in my folder structure in the correct location:

![](../img/2020-04-06-09-56-57.png)


## An example Shortcode

The theme I'm using doesn't offer out of the box anything to highlight important information.  This is the perfect use case for creating my first shortcode.

### Information

#### Basic

{{< note >}}
Sample text
{{< /note>}}

#### With italics

{{< note italic="true">}}
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

{{< note warning="true" italic="true">}}
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


