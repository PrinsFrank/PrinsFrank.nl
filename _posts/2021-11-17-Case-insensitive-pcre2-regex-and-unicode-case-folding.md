---
title: Case insensitive PCRE2 regexes and Unicode case folding
subtitle: A complicated hunt for a bug in my code with a satisfying answer
Description:
categories: [standards, development]
tags: []
readtime:
---

_All of the code samples below are written in PHP as it is the main language I work with, but these examples all apply in any language that has PCRE2 regexes_ 

When searching in a string for a bunch of values using a regex and subsequently retrieving those values and matching them back to the original strings, I ran into a weird issue: The matched string and the string I was matching weren't the same. This issue eventually boils down to the description in the title, but let's start at the beginning.

My code tries to solve the following problem: I have a bunch of strings I want to classify. When 'l' or 'liter' occurs in a string, I want to classify the occurrence as a 'Liter', when 'ml' or 'milliliter' occur I want to classify the occurrence as a 'Milliliter'. As this has to work on strings in multiple languages and has to be optimized for performance, I chose to specify an array (hashmap subtype) with a key of the possible matched values and a value of the classification it belongs to;

```php
$classificationList = [
    'l'          => Liter::class,
    'liter'      => Liter::class,
    'ml'         => Milliliter::class,
    'milliliter' => Milliliter::class,
];
```

The keys are used in a regex, so we don't have to call `str_contains` in a bunch of ifs or a loop:

```php
preg_match_all('/(?<value>' . implode('|', array_keys($classificationList)) . ')/imu', $string, $matches);
```

The flag 'i' is used to match case-insensitive and 'u' for unicode support. The mapping can then be retrieved by simply using the matched value as an index in the classificationList:

```php
$classificationValue = $classificationList[$match['value']] ?? null;
```

So far so good, right?

Enter the microliter, also indicated by 'µl' or 'µL'. After adding it to the classificationList, the classificationValue was sometimes null, even when this shouldn't be happening, right? After all, we took the keys and matched on them directly, why shouldn't it exist anymore in the original array? After quite some debugging I found the issue; In the classificationList I had put the character ['μ' - Unicode 03BC](https://util.unicode.org/UnicodeJsps/character.jsp?a=03BC), and the character that matched and resulted in the null value when searching in the original array was ['µ' - Unicode 00B5](https://util.unicode.org/UnicodeJsps/character.jsp?a=00B5).

Another character looking almost identical to the character we're looking for, matching in the regex. What's going on here?

Normally when debugging regexes I use tools like regex101, but it gives the same result and not any info on why. I have been looking for official documentation about PCRE2 regexes [as PHP uses PCRE (PCRE2 starting from 7.3)](https://www.php.net/manual/en/book.pcre.php), but have not stumbled on any good in-depth documentation before. When looking at [pcre.org](http://pcre.org), I did find a link to [Philip Hazels' PCRE2 repository](https://github.com/PhilipHazel/pcre2), which is apparently the official source now. At an astonishing star count of 72, I reckon this has to be one of the least starred repositories for the amount of developers counting on it.

Looking through the few issues I found a very interesting one: ["Caseless ASCII matching"](https://github.com/PhilipHazel/pcre2/issues/11), talking about a particular interesting feature in unicode: "Case folding", with a [link to a list af characters that can be 'folded'](http://www.unicode.org/Public/12.1.0/ucd/CaseFolding.txt). Including a line saying `00B5; C; 03BC; # MICRO SIGN`. Our mystery character!

With the underlying issue now found, we can now fix the problem. When we get the code point using the mb_ord function and convert it to hex, we can see the difference in the characters:

```php
$micro = 'µ';
$mu    = 'μ';

dechex(mb_ord($micro)); // 00B5
dechex(mb_ord($mu));    // 03BC
```

To manually use the same 'Code Folding' as the regex does, we can use the built-in PHP 'mb_convert_case' function with the option 'MB_CASE_FOLD';

```php
dechex(mb_ord(mb_convert_case($micro, MB_CASE_FOLD))); // 03BC
dechex(mb_ord(mb_convert_case($mu,    MB_CASE_FOLD))); // 03BC
```

If we simply apply this 'Case Folding' to the keys of our initial array, we will have fixed our bug!

---

But why?

In our regex, we use the flag 'i' to indicate we want to ignore cases. This way, both 'l' and 'L' will match when looking for 'l' for example. 

What character corresponds to what uppercase letter and vice versa is maintained by the Unicode Consurtium at [unicode.org](https://unicode.org). The previously referenced [FAQ about Case Mappings](https://unicode.org/faq/casemap_charprop.html#1) links to three resources:

- [UnicodeData.txt](https://unicode.org/faq/casemap_charprop.html#1) - one-to-one case mappings, where we can find 'l'(006C) mapped to 'L'(004C) and back.
- [SpecialCasing.txt](https://www.unicode.org/Public/UCD/latest/ucd/SpecialCasing.txt) - additional info like needed for uppercasing the 'ß' character.
- [CaseFolding.txt](https://www.unicode.org/Public/UCD/latest/ucd/CaseFolding.txt) - additional resources for case folding and caseless matching.

The third file is the file our character is in. The doc says specifically "In addition, CaseFolding.txt contains additional mappings used in case folding and caseless matching", exactly the flag we were using in our regex!

All Case Folding characters (At time of writing in November 2021) are provided below. For your interest, but also to provide some anchor points for people running into a similar issue to find this page in search engines;

```
{% include unicode/case_folded_characters_2021-11-17.html %}
```
