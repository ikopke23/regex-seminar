---
clicks: 3
---

# PCRE2 Syntax

<SyntaxTabs :step="$clicks" :tabs="{
  chars: 'Character Types',
  quant: 'Quantifiers',
  groups: 'Groups & Backrefs',
  look: 'Lookarounds',
}">

<template #chars>

In examples, `↵` = newline and `→` = tab.

| Syntax | Meaning | Pattern | Matches |
| --- | --- | --- | --- |
| `.` | any char except newline (any char in dotall mode) | `a.c` | <code><mark>abc</mark> <mark>a-c</mark> ac</code> |
| `\d` | decimal digit | `\d0\d` | <code><mark>101</mark> <mark>609</mark> 333 </code> |
| `\D` | not a decimal digit | `\d\d\d\D`| <code><mark>100s</mark> <mark>222-</mark> 5000</code> |
| `\N` | not a newline | `.\.\N` | <code><mark>100</mark> <mark>222</mark> 1p↵</code>|
| `\R` | newline sequence | `.\.\R` | <code><mark>10↵</mark> <mark>22↵</mark> 1ps</code> |
| `\s` | whitespace | `I\sA` | <code><mark>I A</mark> <mark>I→A</mark>  Ian</code> |
| `\S` | not whitespace | `I\Sn` | <code><mark>Ian</mark> <mark>I1n</mark>  I n</code> |
| `\w` | "word" character | \wa\w | <code><mark>Ian</mark> <mark>5a9</mark>    -a-</code> |
| `\W` | "non-word" character | \Wa\W | <code><mark>=a)</mark> <mark>-a-</mark> Ian </code>

### Anchors

Anchors match a **position**, not a character.

| Syntax | Meaning | Pattern | Matches |
| --- | --- | --- | --- |
| `^` | start of subject (start of any line in multiline mode) | `^\d+` | <code><mark>42</mark> is 42</code> |
| `$` | end of subject or before a final newline (end of any line in multiline mode) | `\d+$` | <code>42 is <mark>42</mark></code> |
| `\A` | start of subject only, even in multiline mode | `(?m)\A\d` vs `(?m)^\d` | <code><mark>1</mark>a↵2b</code> vs <code><mark>1</mark>a↵<mark>2</mark>b</code> |
| `\Z` | end of subject or before a final newline | `b\Z` | <code>a<mark>b</mark>↵</code> |
| `\z` | end of subject only | `b\z` | <code>ab↵</code> <span class="opacity-50">no match</span> |
| `\G` | where the previous match ended | `\G\d` | <code><mark>1</mark><mark>2</mark><mark>3</mark>a4</code> <span class="opacity-50">stops at the gap</span> |
| `\b` | word boundary | `\bcat\b` | <code><mark>cat</mark> concat</code> |
| `\B` | not a word boundary | `\Bcat` | <code>cat con<mark>cat</mark></code> |

</template>

<template #quant>

| Quantifier | Count | Greedy | Lazy | Possessive |
| --- | --- | --- | --- | --- |
| `?` | 0 or 1 | `ab?b`<br><code><mark>ab</mark> <mark>abb</mark></code> | `ab??b`<br><code><mark>ab</mark> <mark>ab</mark>b</code> | `ab?+b`<br><code>ab <mark>abb</mark></code> |
| `*` | 0 or more | `".*"`<br><code><mark>"a" "b"</mark></code> | `".*?"`<br><code><mark>"a"</mark> <mark>"b"</mark></code> | `".*+"`<br><code>"a" "b"</code> <span class="opacity-50">no match</span> |
| `+` | 1 or more | `<.+>`<br><code><mark>&lt;a&gt; &lt;b&gt;</mark></code> | `<.+?>`<br><code><mark>&lt;a&gt;</mark> <mark>&lt;b&gt;</mark></code> | `<.++>`<br><code>&lt;a&gt; &lt;b&gt;</code> <span class="opacity-50">no match</span> |
| `{n}` | exactly n | `\d{3}`<br><code><mark>123</mark>45</code> | <span class="opacity-50">same as greedy</span> | <span class="opacity-50">same as greedy</span> |
| `{n,m}` | n to m | `\d{2,4}\d`<br><code><mark>1234</mark></code> | `\d{2,4}?\d`<br><code><mark>123</mark>4</code> | `\d{2,4}+\d`<br><code>1234</code> <span class="opacity-50">no match</span> |
| `{n,}` | n or more | `o{2,}o`<br><code>b<mark>oooo</mark>k</code> | `o{2,}?o`<br><code>b<mark>ooo</mark>ok</code> | `o{2,}+o`<br><code>booook</code> <span class="opacity-50">no match</span> |
| `{,m}` | 0 to m | `\d{,2}\d`<br><code><mark>12</mark> <mark>5</mark></code> | `\d{,2}?\d`<br><code><mark>1</mark><mark>2</mark> <mark>5</mark></code> | `\d{,2}+\d`<br><code>12 5</code> <span class="opacity-50">no match</span> |

</template>


<template #groups>

| Syntax | Meaning | Pattern | Matches |
| --- | --- | --- | --- |
| <code>a&#124;b&#124;c</code> | alternation | <code>cat&#124;dog</code> | <code><mark>cat</mark> and <mark>dog</mark></code> |
| `(...)` | capture group | `(\d+)-(\d+)` | <code>call <mark>10-20</mark></code><br><span class="opacity-50">1 = 10, 2 = 20</span> |
| `\n` | backref by number (can be ambiguous) | `(\w)\1` | <code>he<mark>ll</mark>o</code> |
| `\gn` `\g{n}` | backref by number | `(\d)\g{1}0` | <code>5<mark>550</mark></code><br><span class="opacity-50"><code>\\10</code> would mean group 10</span> |
| `\g-n` `\g{-n}` | relative backref | `(a)(b)\g{-1}` | <code><mark>abb</mark> aba</code><br><span class="opacity-50">-1 = nearest group to the left</span> |
| `\g+n` `\g{+n}` | relative backref, forward (PCRE2) | <code>(?:(?:\g{+1}&#124;a)(\w))+</code> | <code><mark>axxyyz</mark></code><br><span class="opacity-50">each char repeats the last capture</span> |
<!-- | `(?<name>...)` | named capture group | `(?<year>\d{4})-(?<mon>\d\d)` | <code>on <mark>2026-09</mark></code><br><span class="opacity-50">year = 2026, mon = 09</span> |-->
<!-- | `\k<name>` `\k'name'` `\g{name}` | backref by name | `(?<q>['"]).*?\k<q>` | <code><mark>'hi'</mark> <mark>"yo"</mark></code> |-->

</template>

<template #look>

Lookarounds check what's before or after the current position **without consuming it** (they match zero characters).

| Syntax | Aliases | Meaning |
| --- | --- | --- |
| `(?=...)` | `(*pla:...)` `(*positive_lookahead:...)` | what follows **must** match `...` |
| `(?!...)` | `(*nla:...)` `(*negative_lookahead:...)` | what follows **must not** match `...` |
| `(?<=...)` | `(*plb:...)` `(*positive_lookbehind:...)` | what precedes **must** match `...` |
| `(?<!...)` | `(*nlb:...)` `(*negative_lookbehind:...)` | what precedes **must not** match `...` |

### Examples

| Pattern | Input | Why |
| --- | --- | --- |
| `^(?=.*\d)(?=.*[A-Z]).{8,}$` | <code><mark>hunter2Go</mark></code><br><code>hunter22</code> <span class="opacity-50">no uppercase</span><br><code>Hunter2</code> <span class="opacity-50">too short</span> | two lookaheads at `^` each scan the whole line for a requirement, then `.{8,}` checks the length |
| `^(?!.*\.min\.js$).*\.js$` | <code><mark>app.js</mark></code><br><code>app.min.js</code> <span class="opacity-50">excluded</span><br><code>app.json</code> <span class="opacity-50">not .js</span> | rule out one case up front, then match the general case |
| `(?<=\$)\d+(\.\d\d)?` | <code>costs $<mark>42.50</mark> or 30 EUR</code> | `$` has to come before the number but isn't part of the match |
| `(?<!-)\b\d+` | <code><mark>5</mark> -3 <mark>12</mark></code> | skips numbers with a `-` in front |
| `\B(?=(\d{3})+(?!\d))` | <code>1<mark>,</mark>234<mark>,</mark>567</code> <span class="opacity-50">replace with ,</span> | matches only **positions**: places followed by a multiple of 3 digits |

</template>

</SyntaxTabs>
