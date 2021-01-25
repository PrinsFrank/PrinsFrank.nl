---
layout: page
title: About
order: 99
---

This site is built using github pages running on Jekyll. The source code can be found [here](https://github.com/PrinsFrank/PrinsFrank.nl)

<style>
    @font-face {
      font-family: NotoColorEmoji;
      src: url("/assets/fonts/NotoColorEmoji.woff2") format('woff2'),
           url("/assets/fonts/NotoColorEmoji.woff") format('woff'),
           url("/assets/fonts/NotoColorEmoji.ttf") format('truetype');
    }
</style>
<div style="font-family: 'NotoColorEmoji'">
{{ '>' | append: flag_emoji | markdownify }}
{{ '>' | append: alpha_numeric | markdownify }}
</div>

