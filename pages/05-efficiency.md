---
clicks: 11
---

# Fast or slow?

#### Which bucket does each feature go in?

<SortBuckets :step="$clicks" fast-label="Fast: linear-time engines (Go, Rust) can do it" slow-label="Slow: needs backtracking" :items="[
  { label: 'literals', example: 'cat', bucket: 'fast' },
  { label: 'alternation', example: 'cat|dog', bucket: 'fast' },
  { label: 'quantifiers', example: 'a* a+ a{2,4}', bucket: 'fast', note: '{n,m} is copied out, so big counts grow the automaton' },
  { label: 'lazy quantifiers', example: 'a*? a+?', bucket: 'fast', note: 'only changes which match is reported' },
  { label: 'character classes', example: '\\d [a-z] .', bucket: 'fast' },
  { label: 'anchors', example: '^ $ \\b', bucket: 'fast' },
  { label: 'capture groups', example: '(\\d+)', bucket: 'fast', note: 'a DFA cannot report positions, but NFA simulation can, still O(m·n)' },
  { label: 'lookarounds', example: '(?=\\d)', bucket: 'slow', note: 'regular in theory; linear-time algorithms are 2024 research' },
  { label: 'backreferences', example: '(\\w+) \\1', bucket: 'slow', note: 'not regular, not even context-free; NP-complete' },
  { label: 'recursion', example: '(?R)', bucket: 'slow', note: 'context-free: balanced parentheses need a stack' },
]" />

<p v-click="11" style="color: #888888">
The real split isn't DFA vs NFA, it's automata vs backtracking: simulating an NFA is still linear.
</p>

---

# Fast or slow?

#### Which bucket does each feature go in?

<SortBuckets :step="$clicks" fast-label="Fast: a DFA can do it" slow-label="Slow: needs backtracking or a stack" :items="[
  { label: 'literals', example: 'cat', bucket: 'fast' },
  { label: 'alternation', example: 'cat|dog', bucket: 'fast' },
  { label: 'quantifiers', example: 'a* a+ a{2,4}', bucket: 'fast' },
  { label: 'character classes', example: '\\d [a-z] .', bucket: 'fast' },
  { label: 'anchors', example: '^ $ \\b', bucket: 'fast' },
  { label: 'capture groups', example: '(\\d+)', bucket: 'fast', note: 'finding the groups needs NFA simulation, still O(m·n)' },
  { label: 'lookarounds', example: '(?=\\d)', bucket: 'slow', note: 'regular in theory, but engines backtrack' },
  { label: 'backreferences', example: '(\\w+) \\1', bucket: 'slow', note: 'not regular, not even context-free' },
  { label: 'recursion', example: '(?R)', bucket: 'slow', note: 'context-free: needs a stack (CFG)' },
]" />

<p v-click="10" style="color: #888888">
If a DFA can do it, it's fast. The further you get from a DFA, the more the engine has to guess and undo.
</p>

---

# What does it cost?

#### m = length of the regex, n = length of the input

| Engine | Used by | Matching time |
| --- | --- | --- |
| <span v-click="1">DFA</span> | <span v-after>grep, RE2, Rust (built lazily)</span> | <span v-after>O(n), but building it can take O(2<sup>m</sup>) states</span> |
| <span v-click="2">NFA simulation</span> | <span v-after>RE2, Go, Rust</span> | <span v-after>O(m·n), guaranteed</span> |
| <span v-click="3">Backtracking</span> | <span v-after>PCRE2, Perl, Python, JS, Java</span> | <span v-after>O(2<sup>n</sup>) worst case</span> |

---

# Catastrophic backtracking

#### No fancy features needed: `^(a+)+$` on `aaa…a!` in PCRE2

```bash
# N = number of a's; swap in ^a+$ to compare
N=20; printf '/^(a+)+$/\n    %s!\n' "$(printf 'a%.0s' $(seq $N))" | pcre2test -t 1
```

| n | `^(a+)+$` | `^a+$` |
| --- | --- | --- |
| <span v-click="1">10</span> | <span v-after>40 µs</span> | <span v-after>1 µs</span> |
| <span v-click="2">15</span> | <span v-after>911 µs</span> | <span v-after>1 µs</span> |
| <span v-click="3">20</span> | <span v-after>26.7 ms</span> | <span v-after>2 µs</span> |
| <span v-click="4">25</span> | <span v-after>**gives up: match limit exceeded**</span> | <span v-after>1 µs</span> |

<p v-click="5" style="color: #888888">
Backreferences can't escape this: matching with them is NP-complete.
</p>

---

# Sources

- Russ Cox, [Regular Expression Matching Can Be Simple And Fast](https://swtch.com/~rsc/regexp/regexp1.html): Thompson NFA is O(m·n); backreferences are NP-complete
- Russ Cox, [Regular Expression Matching: the Virtual Machine Approach](https://swtch.com/~rsc/regexp/regexp2.html): capture groups in linear time
- Russ Cox, [Regular Expression Matching in the Wild](https://swtch.com/~rsc/regexp/regexp3.html): how RE2 combines a DFA and an NFA
- [RE2 syntax](https://github.com/google/re2/wiki/Syntax): which features a linear-time engine leaves out
- [Rust `regex` docs](https://docs.rs/regex): worst case O(m·n); no look-around or backreferences
- [PCRE2 pattern docs](https://www.pcre.org/current/doc/html/pcre2pattern.html): recursion and balanced parentheses
- Berglund et al., [Regular Expressions with Lookahead](https://lib.jucs.org/article/66330/) (2021): lookaheads stay regular
- Barrière & Pit-Claudel, [Linear Matching of JavaScript Regular Expressions](https://arxiv.org/abs/2311.17620) (PLDI 2024)
- Nogami & Terauchi, [arXiv 2406.18918](https://arxiv.org/abs/2406.18918): backreferences can describe non-context-free languages
