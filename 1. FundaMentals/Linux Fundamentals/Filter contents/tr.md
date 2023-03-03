The **`tr`** command is a Linux command-line utility that translates or deletes characters from standard input (**`stdin`**) and writes the result to standard output (**`stdout`**). 

Use **`tr`** to perform different text transformations, including case conversion, squeezing or deleting characters, and basic text replacement.

Since **`tr`** can't read a file directly and outputs the results in standard output, it is often used with pipes (**`|`**) and redirects (**`>>`**) to allow more complex file content processing.


The basic **`tr`** command syntax is:

```
tr [options] SET1 [SET2]
```

**Options**

Running **`tr`** without any options replaces each of the characters specified in **`SET1`** with the characters from **`SET2`** that have the same position.

For example:

![Running the tr command without options.](https://phoenixnap.com/kb/wp-content/uploads/2022/05/running-tr-command-without-options.png)

In the example above, the [echo command's](https://phoenixnap.com/kb/echo-command-linux) output is piped into the **`tr`** command, replacing each instance of _e_ with _o_.

<mark style="background: #BBFABBA6;">### Linux tr Command Options</mark>

The options provide additional character transformation actions. The available options are:

Option

Description

**`-C`**

Complements the characters in **`SET1`**, including every character in the output except the ones specified.

**`-c`**

Complements the values in **`SET1`**. Operations apply to characters that are not in the given set.

**`-d`**

Deletes characters from the **`SET1`** input.

**`-s`**

Squeezes repeated characters specified in the last operand (either **`SET1`** or **`SET2`**) and replaces them with a single occurrence of that character.

**`-t`**

Truncates **`SET1`** to the length of **`SET2`**.

**`-u`**

Ensures that any output is unbuffered.

**`--help`**

Displays the help file with all available options.

**`--version`**

Displays the program version information.



<mark style="background: #BBFABBA6;">**SET**</mark>

**`SET`**s are strings of characters. The command accepts the following interpreted sequences for character matching:

Sequence

Interpretation

**`\NNN`**

Characters with the **`NNN`** octal value (1 to 3 octal digits).

**`\\`**

Backslash.

**`\a`**

An audible bell character.

**`\b`**

Backspace.

**`\f`**

Form feed.

**`\n`**

Newline character.

**`\r`**

Return character.

**`\t`**

Horizontal tab.

**`\v`**

Vertical tab.

**`CHAR1-CHAR2`**

All characters from **`CHAR1`** to **`CHAR2`** in an ascending order.

**`[CHAR*]`**

Copies **`CHAR*`** in **`SET2`** up to the length of **`SET1`**.

**`[CHAR*REPEAT]`**

Repeats copies of **`CHAR`**. Repeats octal if starting with 0.

**`[:alnum:]`**

All letters and digits.

**`[:alpha:]`**

All letters.

**`[:blank:]`**

Horizontal whitespaces.

**`[:cntrl:]`**

All control characters.

**`[:digit:]`**

All digits.

**`[:graph:]`**

Printable characters, excluding space.

**`[:lower:]`**

All lowercase characters.

**`[:print:]`**

Printable characters, including space.

**`[:punct:]`**

All punctuation characters.

**`[:space:]`**

Horizontal or vertical whitespace characters.

**`[:upper:]`**

All uppercase letters.

**`[:xdigit:]`**

Hexadecimal digits.

**`[=CHAR=]`**

All characters equivalent to **`CHAR`**.