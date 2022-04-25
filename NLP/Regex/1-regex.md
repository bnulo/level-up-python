
## Regex

Basically, a regular expression is a pattern describing a certain amount of text. You can use this tool in almost all programming languages. Some use cases are:

- data validation
- data scraping
- data wrangling (transform data from “raw” to another format)
- string parsing (for example catch all URL GET parameters, capture text inside a set of parenthesis)
- string replacement
- syntax highlightning, file renaming, packet sniffing and many other applications involving strings (where data need not be textual)

### Anchors — ^ and $

```
^Hellow        matches any string that starts with Hello
Hello$        matches a string that ends with Hello
^Hello Thanks$   exact string match (starts with Hello and ends with Thanks)
like        matches any string that has the text like in it
```

### Quantifiers — * + ? and {}

```
abc*      matches a string that has ab followed by zero or more c
abc+     matches a string that has ab followed by one or more c
abc?       matches a string that has ab followed by zero or one c
abc{2}     matches a string that has ab followed by 2 c
abc{2,}     matches a string that has ab followed by 2 or more c
abc{2,5}    matches a string that has ab followed by 2 up to 5 c
a(bc)*      matches a string that has a followed by zero or more copies of the sequence bc
a(bc){2,5}  matches a string that has a followed by 2 up to 5 copies of the sequence bc
```

### OR operator — | or []

```
a(b|c)     matches a string that has a followed by b or c (and captures b or c)
a[bc]      same as previous, but without capturing b or c
```

### Character classes — \d \w \s and .

```
\d         matches a single character that is a digit
\w         matches a word character (alphanumeric character plus underscore)
\s         matches a whitespace character (includes tabs and line breaks)
.          matches any character
```

Use the . operator carefully since often class or negated character class (which we’ll cover next) are faster and more precise.
\d, \w and \s also present their negations with \D, \W and \S respectively.
For example, \D will perform the inverse match with respect to that obtained with \d.

```
\D         matches a single non-digit character
```

In order to be taken literally, you must escape the characters ^.[$()|*+?{\with a backslash \ as they have special meaning.

```
\\$\d       matches a string that has a $ before one digit
```

Notice that you can match also non-printable characters like tabs \t, new-lines \n, carriage returns \r.

### Flags

We are learning how to construct a regex but forgetting a fundamental concept: flags.
A regex usually comes within this form /abc/, where the search pattern is delimited by two slash characters /. At the end we can specify a flag with these values (we can also combine them each other):

- g (global) does not return after the first match, restarting the subsequent searches from the end of the previous match
- m (multi-line) when enabled ^ and $ will match the start and end of a line, instead of the whole string
- i (insensitive) makes the whole expression case-insensitive (for instance /aBc/i would match AbC)

>

### Grouping and capturing — ()

```
a(bc)           parentheses create a capturing group with value bc
a(?:bc)        using ?: we disable the capturing group
a(?<foo>bc)     using ?<foo> we put a name to the group
```

This operator is very useful when we need to extract information from strings or data using your preferred programming language. Any multiple occurrences captured by several groups will be exposed in the form of a classical array: we will access their values specifying using an index on the result of the match.
If we choose to put a name to the groups (using (?<foo>...)) we will be able to retrieve the group values using the match result like a dictionary where the keys will be the name of each group.

### Bracket expressions — []

```
[abc]           matches a string that has either an a or a b or a c -> is the same as a|b|c
[a-c]            same as previous
[a-fA-F0-9]      a string that represents a single hexadecimal digit, case insensitively
[0-9]%           a string that has a character from 0 to 9 before a % sign
[^a-zA-Z]        a string that has not a letter from a to z or from A to Z. In this case the ^ is used as negation of the expression
```

### Greedy and Lazy match

The quantifiers ( * + {}) are greedy operators, so they expand the match as far as they can through the provided text.
For example, <.+> matches <div>simple div</div> in This is a <div> simple div</div> test. In order to catch only the div tag we can use a ? to make it lazy:

```
<.+?> matches any character one or more times included inside < and >, expanding as needed
```

Notice that a better solution should avoid the usage of . in favor of a more strict regex:

```
<[^<>]+>         matches any character except < or > one or more times included inside < and >
```

### Boundaries — \b and \B

```
\babc\b          performs a "whole words only" search
```

\b represents an anchor like caret (it is similar to $ and ^) matching positions where one side is a word character (like \w) and the other side is not a word character (for instance it may be the beginning of the string or a space character).
It comes with its negation, \B. This matches all positions where \b doesn’t match and could be if we want to find a search pattern fully surrounded by word characters.

```
\Babc\B          matches only if the pattern is fully surrounded by word characters
```

### Back-references — \1

```
([abc])\1              using \1 it matches the same text that was matched by the first capturing group
([abc])([de])\2\1      we can use \2 (\3, \4, etc.) to identify the same text that was matched by the second (third, fourth, etc.) capturing group
(?<foo>[abc])\k<foo>   we put the name foo to the group and we reference it later (\k<foo>). The result is the same of the first regex
```

### Look-ahead and Look-behind — (?=) and (?<=)

```
d(?=r)       matches a d only if is followed by r, but r will not be part of the overall regex match
(?<=r)d      matches a d only if is preceded by an r, but r will not be part of the overall regex
```

You can use also the negation operator!

```
d(?!r)       matches a d only if is not followed by r, but r will not be part of the overall regex match
(?<!r)d      matches a d only if is not preceded by an r, but r will not be part of the overall regex match
```

[Here you can read the original article](https://medium.com/factory-mind/regex-tutorial-a-simple-cheatsheet-by-examples-649dc1c3f285)

## Most wanted regex

#### Digits

```
^(\d+)$
Simple numbers
37

^(\d*)\[.,](\d+)$
Decimal numberes
12.37 or 37,12

^(\d+)[\/](\d+)$
Fractions
12/37
```

#### Alphanumeric chars

```
^(\w*)$
Alphanumeric without spaces
hi

^[a-zA-Z0-9 ]*$
Alphanumeric with spaces
hi there
```

#### Email

```
^([a-zA-Z0-9._%-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,6})*$/
Valic classic email
somename.someothername@something.com


^([a-z0-9_\.\+-]+)@([\da-z\.-]+)\.([a-z\.]{2,6})$/
Email tokens
somename.someothername@something.com
```

#### Trim spaces

```
^[\s]*(.*?)[\s]*$
Matches text avoiding additional spaces
test  
   avoid starting spaces
avoid ending spaces  
 avoid starting and ending spaces
```

#### HTML Tag

```
<([a-z]+)([^<]+)*(?:>(.*)<\/\1>|\s+\/>)
Matches any valid HTML tag and the corresponding closing tag
```

#### Hexadecimal value

```
\B#(?:[a-fA-F0–9]{6}|[a-fA-F0–9]{3})\b
Matches any valid hex color inside text
```

#### Valid email (RFC5322

```
\b[\w.!#$%&’*+\/=?^`{|}~-]+@[\w-]+(?:\.[\w-]+)*\b
Matches any valid email inside text
```

#### Username

```
/^[a-z0-9_-]{3,16}$/
Minimum length of 3, maximum length of 16, composed by letters, numbers or dashes.
```

#### Strong password

```
(?=^.{6,}$)((?=.*\w)(?=.*[A-Z])(?=.*[a-z])(?=.*[0-9])(?=.*[|!"$%&\/\(\)\?\^\'\\\+\-\*]))^.* | Minimum length of 6, at least 1 uppercase letter, at least 1 lowercase letter, at least 1 number, at least 1 special character
```

#### 2 of a kind

```
^(?=([0-9]*[a-z]){2,})([a-zA-Z0-9]{8,32})$
At least 2 letters (uppercase or lowercase) at any index, minimum length of 8, maximum length of 32

^(((https?|ftp):\/\/)?([\w\-\.])+(\.)([\w]){2,4}([\w\/+=%&_\.~?\-]*))*$
If you want to use capturing groups to get scheme, path, etc. (or add user-info, host, port…) feel free to ask it in comments!
```

#### IPv4 address

```
\b(?:(?:25[0-5]|2[0-4]\d|[01]?\d\d?)\.){3}(?:25[0-5]|2[0-4]\d|[01]?\d\d?)\b  

Matches any valid IPv4 address inside text
```

#### “Defanged” URL or IPv4 address

```
^(((h..ps?|f.p):\/\/)?(?:([\w\-\.])+(\[?\.\]?)([\w]){2,4}|(?:(?:25[0–5]|2[0–4]\d|[01]?\d\d?)\[?\.\]?){3}(?:25[0–5]|2[0–4]\d|[01]?\d\d?)))*([\w\/+=%&_\.~?\-]*)$
```

#### SSN — Social Security Number (simple)

```
^((?<area>[\d]{3})[-][\d]{2}[-][\d]{4})$
```

#### Alpha-numeric, literals, digits, lowercase, uppercase chars only

```
\w
alpha-numeric only

[a-zA-Z]  
literals only

\d  
digits only

[a-z]  
lowercase literal only

[A-Z]  
uppercase literal only
```

[Here you can read the original article](https://medium.com/factory-mind/regex-cookbook-most-wanted-regex-aa721558c3c1)
