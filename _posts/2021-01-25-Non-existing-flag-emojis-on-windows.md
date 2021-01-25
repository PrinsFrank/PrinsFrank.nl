---
title: Missing flag emojis on Windows
subtitle: Why 🇳 and 🇱 make 🇳🇱
Description:
categories: [development, standards]
tags: []
readtime:
---

While they seem quite new, the first known emoji set was created by [Softbank in 1997](https://emojipedia.org/softbank/1997/) for Japanese customers, but only in [2008 Apple introduced support on the Japanese market](https://web.archive.org/web/20090703152413/http://support.apple.com/kb/TS2404), with eventually the entire world being able to use them. And while all emoji differ between platforms, most do work when you send them in an email from your Apple device to an Android or Windows device, or displayed on a web page. With one noticeable exception: country flags;

{% include img.html src="/images/2021/01/emoji_platforms.jpg" alt="Flag emojis on macOS" aspect_ratio=31.5 %}

You can see the flags that are represented on macOS quite well, are only two letter codes on Windows. Here is how they are shown on your device:

> 🇦🇫 🇦🇱 🇩🇿 🇦🇩 🇦🇴 🇦🇬 🇦🇷 🇦🇲 🇦🇺 🇦🇹 🇦🇿 🇧🇸 🇧🇭 🇧🇩 🇧🇧 🇧🇾 🇧🇪 🇧🇿 🇧🇯 🇧🇹 🇧🇴 🇧🇦 🇧🇼 🇧🇷 🇧🇳 🇧🇬 🇧🇫 🇧🇮 🇨🇻 🇰🇭 🇨🇲 🇨🇦 🇨🇫 🇹🇩 🇨🇱 🇨🇴 🇰🇲 🇨🇩 🇨🇷 🇨🇮 🇭🇷 🇨🇺 🇨🇾 🇨🇿 🇰🇵 🇨🇬 🇩🇰 🇩🇯 🇩🇲 🇩🇴 🇪🇨 🇪🇬 🇸🇻 🇬🇶 🇪🇷 🇪🇪 🇸🇿 🇪🇹 🇫🇯 🇫🇮 🇫🇷 🇬🇦 🇬🇲 🇬🇪 🇩🇪 🇬🇭 🇬🇷 🇬🇩 🇬🇹 🇬🇳 🇬🇼 🇬🇾 🇭🇹 🇭🇳 🇭🇺 🇮🇸 🇮🇳 🇮🇩 🇮🇷 🇮🇶 🇮🇪 🇮🇱 🇮🇹 🇯🇲 🇯🇵 🇯🇴 🇰🇿 🇰🇪 🇰🇮 🇰🇼 🇰🇬 🇱🇦 🇱🇻 🇱🇧 🇱🇸 🇱🇷 🇱🇾 🇱🇮 🇱🇹 🇱🇺 🇲🇬 🇲🇼 🇲🇾 🇲🇻 🇲🇱 🇲🇹 🇲🇭 🇲🇷 🇲🇺 🇲🇽 🇫🇲 🇲🇨 🇲🇳 🇲🇪 🇲🇦 🇲🇿 🇲🇲 🇳🇦 🇳🇷 🇳🇵 🇳🇱 🇳🇿 🇳🇮 🇳🇪 🇳🇬 🇲🇰 🇳🇴 🇴🇲 🇵🇰 🇵🇼 🇵🇦 🇵🇬 🇵🇾 🇵🇪 🇵🇭 🇵🇱 🇵🇹 🇶🇦 🇰🇷 🇲🇩 🇷🇴 🇷🇺 🇷🇼 🇰🇳 🇱🇨 🇻🇨 🇼🇸 🇸🇲 🇸🇹 🇸🇦 🇸🇳 🇷🇸 🇸🇨 🇸🇱 🇸🇬 🇸🇰 🇸🇮 🇸🇧 🇸🇴 🇿🇦 🇸🇸 🇪🇸 🇱🇰 🇸🇩 🇸🇷 🇸🇪 🇨🇭 🇸🇾 🇹🇯 🇹🇭 🇹🇱 🇹🇬 🇹🇴 🇹🇹 🇹🇳 🇹🇷 🇹🇲 🇹🇻 🇺🇬 🇺🇦 🇦🇪 🇬🇧 🇹🇿 🇺🇸 🇺🇾 🇺🇿 🇻🇺 🇻🇪 🇻🇳 🇾🇪 🇿🇲 🇿🇼

How can these visual representations that are so common not be supported across platforms? And is there a way to make them work anyway?

The organization that standardizes the emoji and their code points is the Unicode Consortium. They decide what characters get assigned code points in the Unicode standard, including emoji but also any characters for modern or ancient languages (like hieroglyphs for example). The first emoji were introduced in unicode 1.1 (only later given emoji representation), and several more were added over version 3.x, 4.x and 5.x. With Unicode 6.0, country flag emoji were introduced in October 2010.

Instead of adding a code point for every country, something smarter was done. Countries regularly combine, break up etc. The newest country - South Sudan, independent since 2011 - didn't appear on my country map because I used an outdated svg. Exactly this problem is what the consortium wanted to prevent, by coming up with a clever workaround. The International Organization for Standardization - or ISO - already manages a standard of what countries exist: ISO 3166. Part 1, ISO 3166-1 specifies country codes. The alpha-2 representation was chosen to make up the building blocks of the emoji. For a 2 character code there are 26<sup>2</sup> or 676 possible combinations, so 676 possible countries before the standard needs to be revised.

Every alpha-2 representation for a country is unique and consists of 2 letters from the modern english alphabet. These 26 letters all got another [code point specifically for "regional indicators" in range 1F1E6-1F1FF](https://www.unicode.org/charts/PDF/U1F100.pdf); 🇦 🇧 🇨 🇩 🇪 🇫 🇬 🇭 🇮 🇯 🇿 🇱 🇲 🇳 🇴 🇵 🇶 🇷 🇸 🇹 🇺 🇻 🇼 🇽 🇾 🇿. To include a countries flag. simply look up the alpha-2 code for it and look up the regional indicators for that country code. The alpha-2 for the Netherlands is NL. Take the 🇳 and 🇱 code points and squash them together and you have a flag: 🇳🇱. Or not when you are visiting on Windows.

There are quite some forum posts asking why these otherwise universal emojis don't work on Windows yet dating back several years. Up until 2015, Apple only supported 10 flags, but during that year they introduced almost all flags for iOS and OS X, and more than 5 years later they still don't exist for Windows. How is that possible?

The actual conversion from code point to visual representation happens with the use of fonts. All large vendors like Apple, Google and Microsoft have their own emoji fonts that render the emojis when they are displayed in an app or on a web page. And while Microsofts emoji font "Segoe UI Emoji" does have representations for the single regional indicators, it doesn't have any for the combinations. And that's why the characters show individually. Why they don't include representations for the flags in their emoji font is not clear. Some forum discussions reference international discussions about what countries should be recognized, and which not. But that doesn't give the full picture, as Apple operates in the same countries and does have them in their font. We will probably not know the real reason soon, but we can try to make some cross platform working emojis.

Weirdly, all forums discussing a solution to this mention it can't be done, while fixing this is actually quite simple. Maybe it is because most of these posts are 1 year old or older, but it can also be that I didn't test this solution thoroughly enough across platforms. 

While normally I only show the end result of my experimentation, I will also show some intermediate results here. While searching for a font that includes country flags I came across one by Babelstone called [Babelstone Flags](https://www.babelstone.co.uk/Fonts/Flags.html). I tried some flags, and they worked! The Dutch, American, Swiss. I was happy and decided to write this post, including all country flags. I wrote this snippet:

```html
<style>
    @font-face {
      font-family: BabelStoneFlags;
      src: url("/assets/fonts/BabelStoneFlags.woff2") format('woff2'),
           url("/assets/fonts/BabelStoneFlags.woff") format('woff');
    }
</style>
<div style="font-family: 'BabelStoneFlags'">
    🇦🇫 🇦🇱 ...
</div>
```

But when this was rendered, I realised not all flags where in there;

<style>
    @font-face {
      font-family: BabelStoneFlags;
      src: url("/assets/fonts/BabelStoneFlags.woff2") format('woff2'),
           url("/assets/fonts/BabelStoneFlags.woff") format('woff');
    }
</style>
<div style="font-family: 'BabelStoneFlags'">
    <blockquote>
        🇦🇫 🇦🇱 🇩🇿 🇦🇩 🇦🇴 🇦🇬 🇦🇷 🇦🇲 🇦🇺 🇦🇹 🇦🇿 🇧🇸 🇧🇭 🇧🇩 🇧🇧 🇧🇾 🇧🇪 🇧🇿 🇧🇯 🇧🇹 🇧🇴 🇧🇦 🇧🇼 🇧🇷 🇧🇳 🇧🇬 🇧🇫 🇧🇮 🇨🇻 🇰🇭 🇨🇲 🇨🇦 🇨🇫 🇹🇩 🇨🇱 🇨🇴 🇰🇲 🇨🇩 🇨🇷 🇨🇮 🇭🇷 🇨🇺 🇨🇾 🇨🇿 🇰🇵 🇨🇬 🇩🇰 🇩🇯 🇩🇲 🇩🇴 🇪🇨 🇪🇬 🇸🇻 🇬🇶 🇪🇷 🇪🇪 🇸🇿 🇪🇹 🇫🇯 🇫🇮 🇫🇷 🇬🇦 🇬🇲 🇬🇪 🇩🇪 🇬🇭 🇬🇷 🇬🇩 🇬🇹 🇬🇳 🇬🇼 🇬🇾 🇭🇹 🇭🇳 🇭🇺 🇮🇸 🇮🇳 🇮🇩 🇮🇷 🇮🇶 🇮🇪 🇮🇱 🇮🇹 🇯🇲 🇯🇵 🇯🇴 🇰🇿 🇰🇪 🇰🇮 🇰🇼 🇰🇬 🇱🇦 🇱🇻 🇱🇧 🇱🇸 🇱🇷 🇱🇾 🇱🇮 🇱🇹 🇱🇺 🇲🇬 🇲🇼 🇲🇾 🇲🇻 🇲🇱 🇲🇹 🇲🇭 🇲🇷 🇲🇺 🇲🇽 🇫🇲 🇲🇨 🇲🇳 🇲🇪 🇲🇦 🇲🇿 🇲🇲 🇳🇦 🇳🇷 🇳🇵 🇳🇱 🇳🇿 🇳🇮 🇳🇪 🇳🇬 🇲🇰 🇳🇴 🇴🇲 🇵🇰 🇵🇼 🇵🇦 🇵🇬 🇵🇾 🇵🇪 🇵🇭 🇵🇱 🇵🇹 🇶🇦 🇰🇷 🇲🇩 🇷🇴 🇷🇺 🇷🇼 🇰🇳 🇱🇨 🇻🇨 🇼🇸 🇸🇲 🇸🇹 🇸🇦 🇸🇳 🇷🇸 🇸🇨 🇸🇱 🇸🇬 🇸🇰 🇸🇮 🇸🇧 🇸🇴 🇿🇦 🇸🇸 🇪🇸 🇱🇰 🇸🇩 🇸🇷 🇸🇪 🇨🇭 🇸🇾 🇹🇯 🇹🇭 🇹🇱 🇹🇬 🇹🇴 🇹🇹 🇹🇳 🇹🇷 🇹🇲 🇹🇻 🇺🇬 🇺🇦 🇦🇪 🇬🇧 🇹🇿 🇺🇸 🇺🇾 🇺🇿 🇻🇺 🇻🇪 🇻🇳 🇾🇪 🇿🇲 🇿🇼
    </blockquote>
    <blockquote>
        Babelstone flags abcdefghijklmnopqrstuvwxyz0123456789
    </blockquote>
</div>

My search continued, until I found the [Noto Color Emoji font](https://github.com/googlefonts/noto-emoji). Written down in that readme is also the reason older discussions mentioned that there was no solution to the missing flag emoji on Windows: 

> NotoColorEmoji uses the CBDT/CBLC color font format, (..) Windows supports it starting with Windows 10 Anniversary Update (..)

Great! This means we kind of solved the Windows emoji issue, but only for people on a recently updated version of Windows 10, wich will be around 75% percent of the Windows users. Quite a substantial fix! I included the font as follows;

```html
<style>
    @font-face {
      font-family: NotoColorEmoji;
      src: url("/assets/fonts/NotoColorEmoji.woff2") format('woff2'),
           url("/assets/fonts/NotoColorEmoji.woff") format('woff'),
           url("/assets/fonts/NotoColorEmoji.ttf") format('truetype');
    }
</style>
<div style="font-family: 'NotoColorEmoji'">
    🇦🇫 🇦🇱 ...
</div>
```

With the following as output:

<style>
    @font-face {
      font-family: NotoColorEmoji;
      src: url("/assets/fonts/NotoColorEmoji.woff2") format('woff2'),
           url("/assets/fonts/NotoColorEmoji.woff") format('woff'),
           url("/assets/fonts/NotoColorEmoji.ttf") format('truetype');
    }
</style>
<div style="font-family: 'NotoColorEmoji'">
    <blockquote>
        🇦🇫 🇦🇱 🇩🇿 🇦🇩 🇦🇴 🇦🇬 🇦🇷 🇦🇲 🇦🇺 🇦🇹 🇦🇿 🇧🇸 🇧🇭 🇧🇩 🇧🇧 🇧🇾 🇧🇪 🇧🇿 🇧🇯 🇧🇹 🇧🇴 🇧🇦 🇧🇼 🇧🇷 🇧🇳 🇧🇬 🇧🇫 🇧🇮 🇨🇻 🇰🇭 🇨🇲 🇨🇦 🇨🇫 🇹🇩 🇨🇱 🇨🇴 🇰🇲 🇨🇩 🇨🇷 🇨🇮 🇭🇷 🇨🇺 🇨🇾 🇨🇿 🇰🇵 🇨🇬 🇩🇰 🇩🇯 🇩🇲 🇩🇴 🇪🇨 🇪🇬 🇸🇻 🇬🇶 🇪🇷 🇪🇪 🇸🇿 🇪🇹 🇫🇯 🇫🇮 🇫🇷 🇬🇦 🇬🇲 🇬🇪 🇩🇪 🇬🇭 🇬🇷 🇬🇩 🇬🇹 🇬🇳 🇬🇼 🇬🇾 🇭🇹 🇭🇳 🇭🇺 🇮🇸 🇮🇳 🇮🇩 🇮🇷 🇮🇶 🇮🇪 🇮🇱 🇮🇹 🇯🇲 🇯🇵 🇯🇴 🇰🇿 🇰🇪 🇰🇮 🇰🇼 🇰🇬 🇱🇦 🇱🇻 🇱🇧 🇱🇸 🇱🇷 🇱🇾 🇱🇮 🇱🇹 🇱🇺 🇲🇬 🇲🇼 🇲🇾 🇲🇻 🇲🇱 🇲🇹 🇲🇭 🇲🇷 🇲🇺 🇲🇽 🇫🇲 🇲🇨 🇲🇳 🇲🇪 🇲🇦 🇲🇿 🇲🇲 🇳🇦 🇳🇷 🇳🇵 🇳🇱 🇳🇿 🇳🇮 🇳🇪 🇳🇬 🇲🇰 🇳🇴 🇴🇲 🇵🇰 🇵🇼 🇵🇦 🇵🇬 🇵🇾 🇵🇪 🇵🇭 🇵🇱 🇵🇹 🇶🇦 🇰🇷 🇲🇩 🇷🇴 🇷🇺 🇷🇼 🇰🇳 🇱🇨 🇻🇨 🇼🇸 🇸🇲 🇸🇹 🇸🇦 🇸🇳 🇷🇸 🇸🇨 🇸🇱 🇸🇬 🇸🇰 🇸🇮 🇸🇧 🇸🇴 🇿🇦 🇸🇸 🇪🇸 🇱🇰 🇸🇩 🇸🇷 🇸🇪 🇨🇭 🇸🇾 🇹🇯 🇹🇭 🇹🇱 🇹🇬 🇹🇴 🇹🇹 🇹🇳 🇹🇷 🇹🇲 🇹🇻 🇺🇬 🇺🇦 🇦🇪 🇬🇧 🇹🇿 🇺🇸 🇺🇾 🇺🇿 🇻🇺 🇻🇪 🇻🇳 🇾🇪 🇿🇲 🇿🇼
    </blockquote>
    <blockquote>
        Noto Color Emoji abcdefghijklmnopqrstuvwxyz0123456789
    </blockquote>
</div>

That looks nice! It is also a monospace font, meaning that contrary to the BabelStone font all flags are the same width. And the borders around the flags make them more distinguishable when part of the flag is white on a white background.

There is still one issue left. All characters are displayed in the font, even non emoji characters like spaces. You can see this very clearly especially in the NotoColorEmoji font as the spaces are huge and the numbers are different. We could wrap every individual flag in a span and make sure the font is only used for that span by using a css selector or applying the style on that element directly. But we can use more css magic, the unicode-range property on the font face. Let's take the previous snippet and introduce it:

```html
<style>
    @font-face {
      font-family: NotoColorEmojiLimited;
      unicode-range: U+1F1E6-1F1FF;
      src: url("/assets/fonts/NotoColorEmoji.woff2") format('woff2'),
           url("/assets/fonts/NotoColorEmoji.woff") format('woff'),
           url("/assets/fonts/NotoColorEmoji.ttf") format('truetype');
    }
</style>
<div style="font-family: 'NotoColorEmojiLimited', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol'">
    🇦🇫 🇦🇱 ...
</div>
```

The NotoColorEmojiLimited - differently named as the previous one to avoid a collision - is set as a font family before the other fonts to make sure that it is used when one of the characters in the range 1F1E6-1F1FF is used. The other fonts are also set as font families as they are the defaults for this site, so things like spaces and numbers are displayed in the style of the rest of the site. 


<style>
    @font-face {
      font-family: NotoColorEmojiLimited;
      unicode-range: U+1F1E6-1F1FF;
      src: url("/assets/fonts/NotoColorEmoji.woff2") format('woff2'),
           url("/assets/fonts/NotoColorEmoji.woff") format('woff'),
           url("/assets/fonts/NotoColorEmoji.ttf") format('truetype');
    }
</style>
<div style="font-family: 'NotoColorEmojiLimited', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol'">
    <blockquote>
        🇦🇫 🇦🇱 🇩🇿 🇦🇩 🇦🇴 🇦🇬 🇦🇷 🇦🇲 🇦🇺 🇦🇹 🇦🇿 🇧🇸 🇧🇭 🇧🇩 🇧🇧 🇧🇾 🇧🇪 🇧🇿 🇧🇯 🇧🇹 🇧🇴 🇧🇦 🇧🇼 🇧🇷 🇧🇳 🇧🇬 🇧🇫 🇧🇮 🇨🇻 🇰🇭 🇨🇲 🇨🇦 🇨🇫 🇹🇩 🇨🇱 🇨🇴 🇰🇲 🇨🇩 🇨🇷 🇨🇮 🇭🇷 🇨🇺 🇨🇾 🇨🇿 🇰🇵 🇨🇬 🇩🇰 🇩🇯 🇩🇲 🇩🇴 🇪🇨 🇪🇬 🇸🇻 🇬🇶 🇪🇷 🇪🇪 🇸🇿 🇪🇹 🇫🇯 🇫🇮 🇫🇷 🇬🇦 🇬🇲 🇬🇪 🇩🇪 🇬🇭 🇬🇷 🇬🇩 🇬🇹 🇬🇳 🇬🇼 🇬🇾 🇭🇹 🇭🇳 🇭🇺 🇮🇸 🇮🇳 🇮🇩 🇮🇷 🇮🇶 🇮🇪 🇮🇱 🇮🇹 🇯🇲 🇯🇵 🇯🇴 🇰🇿 🇰🇪 🇰🇮 🇰🇼 🇰🇬 🇱🇦 🇱🇻 🇱🇧 🇱🇸 🇱🇷 🇱🇾 🇱🇮 🇱🇹 🇱🇺 🇲🇬 🇲🇼 🇲🇾 🇲🇻 🇲🇱 🇲🇹 🇲🇭 🇲🇷 🇲🇺 🇲🇽 🇫🇲 🇲🇨 🇲🇳 🇲🇪 🇲🇦 🇲🇿 🇲🇲 🇳🇦 🇳🇷 🇳🇵 🇳🇱 🇳🇿 🇳🇮 🇳🇪 🇳🇬 🇲🇰 🇳🇴 🇴🇲 🇵🇰 🇵🇼 🇵🇦 🇵🇬 🇵🇾 🇵🇪 🇵🇭 🇵🇱 🇵🇹 🇶🇦 🇰🇷 🇲🇩 🇷🇴 🇷🇺 🇷🇼 🇰🇳 🇱🇨 🇻🇨 🇼🇸 🇸🇲 🇸🇹 🇸🇦 🇸🇳 🇷🇸 🇸🇨 🇸🇱 🇸🇬 🇸🇰 🇸🇮 🇸🇧 🇸🇴 🇿🇦 🇸🇸 🇪🇸 🇱🇰 🇸🇩 🇸🇷 🇸🇪 🇨🇭 🇸🇾 🇹🇯 🇹🇭 🇹🇱 🇹🇬 🇹🇴 🇹🇹 🇹🇳 🇹🇷 🇹🇲 🇹🇻 🇺🇬 🇺🇦 🇦🇪 🇬🇧 🇹🇿 🇺🇸 🇺🇾 🇺🇿 🇻🇺 🇻🇪 🇻🇳 🇾🇪 🇿🇲 🇿🇼
    </blockquote>
    <blockquote>
        Noto Color Emoji abcdefghijklmnopqrstuvwxyz0123456789
    </blockquote>
</div>

One last thing you'll notice is that the font gets reloaded on every page load and it takes a little while for the flags to appear. As this site is hosted on Github, I don't have control over caching headers, but you can make sure the font is only downloaded once and then cached using these headers. You could also use a CDN to serve this font and make sure the user doesn't download the font again when it was already loaded for another site. My personal distaste for CDNs will be discussed in a next post.
