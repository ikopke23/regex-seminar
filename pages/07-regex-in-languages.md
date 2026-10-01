# Regex in your language of choice

#### Same task everywhere: find every date and pull out the year with a capture group

<div class="flex gap-4 mb-2 text-sm">
  <span
    v-for="(lang, i) in ['Python', 'Rust', 'Java', 'Go', 'Perl']"
    :key="lang"
    :class="$clicks === i ? 'font-bold border-b-2 border-current' : 'opacity-40'"
  >{{ lang }}</span>
</div>

<v-switch>
<template #0>

```python
# library: re (standard library) · engine: backtracking
import re

text = "Released 2023-11-15, patched 2024-04-02"
for m in re.finditer(r"(\d{4})-(\d{2})-(\d{2})", text):
    print(m.group(0), "year:", m.group(1))

# prints:
#   2023-11-15 year: 2023
#   2024-04-02 year: 2024
```

</template>
<template #1>

```rust
// library: regex crate (Cargo.toml: regex = "1") · engine: linear
use regex::Regex;

fn main() {
    let text = "Released 2023-11-15, patched 2024-04-02";
    let re = Regex::new(r"(\d{4})-(\d{2})-(\d{2})").unwrap();
    for caps in re.captures_iter(text) {
        println!("{} year: {}", &caps[0], &caps[1]);
    }
}

// prints:
//   2023-11-15 year: 2023
//   2024-04-02 year: 2024
```

</template>
<template #2>

```java
// library: java.util.regex · engine: backtracking
// Java has no raw strings, so every \ in the pattern is written \\
import java.util.regex.*;

public class Main {
    public static void main(String[] args) {
        String text = "Released 2023-11-15, patched 2024-04-02";
        Matcher m = Pattern.compile("(\\d{4})-(\\d{2})-(\\d{2})").matcher(text);
        while (m.find()) {
            System.out.println(m.group(0) + " year: " + m.group(1));
        }
    }
}

// prints:
//   2023-11-15 year: 2023
//   2024-04-02 year: 2024
```

</template>
<template #3>

```go
// library: regexp (standard library) · engine: linear
package main

import (
	"fmt"
	"regexp"
)

func main() {
	text := "Released 2023-11-15, patched 2024-04-02"
	re := regexp.MustCompile(`(\d{4})-(\d{2})-(\d{2})`)
	for _, m := range re.FindAllStringSubmatch(text, -1) {
		fmt.Println(m[0], "year:", m[1])
	}
}

// prints:
//   2023-11-15 year: 2023
//   2024-04-02 year: 2024
```

</template>
<template #4>

```perl
# library: built into the language · engine: backtracking
my $text = "Released 2023-11-15, patched 2024-04-02";
while ($text =~ /(\d{4})-(\d{2})-(\d{2})/g) {
    print "$& year: $1\n";
}

# prints:
#   2023-11-15 year: 2023
#   2024-04-02 year: 2024
```

</template>
</v-switch>
