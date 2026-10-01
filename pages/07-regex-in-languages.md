# Regex in your language of choice

#### Same task everywhere: find every date and pull out the year with a capture group

::code-group

```python [Python]
import re

text = "Released 2023-11-15, patched 2024-04-02"
for m in re.finditer(r"(\d{4})-(\d{2})-(\d{2})", text):
    print(m.group(0), "year:", m.group(1))
```

```rust [Rust]
use regex::Regex;

    let text = "Released 2023-11-15, patched 2024-04-02";
    let re = Regex::new(r"(\d{4})-(\d{2})-(\d{2})").unwrap();
```

```java [Java]
import java.util.regex.*;

        String text = "Released 2023-11-15, patched 2024-04-02";
        Matcher m = Pattern.compile("(\\d{4})-(\\d{2})-(\\d{2})").matcher(text);

```

```go [Go]
import ("regexp")

	text := "Released 2023-11-15, patched 2024-04-02"
	re := regexp.MustCompile(`(\d{4})-(\d{2})-(\d{2})`)

```

```perl [Perl]
my $text = "Released 2023-11-15, patched 2024-04-02";
while ($text =~ /(\d{4})-(\d{2})-(\d{2})/g) {
    print "$& year: $1\n";
}
```

::

| | Python | Rust | Java | Go | Perl |
| --- | --- | --- | --- | --- | --- |
| Library | `re` | `regex` crate | `java.util.regex` | `regexp` | built in |
| Engine | backtracking | linear | backtracking | linear | backtracking |
