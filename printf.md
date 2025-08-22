# `printf` Command Cheat-Sheet

The `printf` command in Unix-like shells (including Bash) is used to display formatted output. It's similar to the `printf` function in C and offers much more control over output formatting than `echo`. It's particularly useful for scripts where precise spacing, numeric formatting, and consistent output are required.

---

## 1. Why `printf` over `echo`?

While `echo` is simpler for basic output, `printf` offers:
*   **Portability**: Its behavior is standardized across different systems, unlike `echo` which can vary.
*   **Explicit Formatting**: You define the exact format string and arguments, preventing unexpected output due to shell expansions or special characters.
*   **No Ambiguity**: `echo -e` and `echo` without `-e` can behave differently depending on the shell, leading to issues with escape sequences. `printf` always interprets escape sequences in its format string.

---

## 2. Basic Syntax

The general syntax for `printf` is:

```bash
printf FORMAT [ARGUMENT...]
```

*   **`FORMAT`**: A string that specifies how the arguments should be formatted. It can contain literal text, format specifiers (starting with `%`), and escape sequences (starting with `\`).
*   **`ARGUMENT`**: The values to be formatted according to the specifiers in `FORMAT`. If there are more arguments than specifiers, the `FORMAT` string is reused. If there are fewer, missing arguments are treated as empty strings or zero.

---

## 3. Format Specifiers (`%`)

These characters tell `printf` how to interpret and display the corresponding argument.

| Specifier | Argument Type | Description                                        | Example Output (`printf "%s %d\n" "text" 123`) |
| :-------- | :------------ | :------------------------------------------------- | :--------------------------------------------- |
| `%%`      | (None)        | A literal `%` character.                           | `%`                                            |
| `%s`      | String        | Displays a string.                                 | `text`                                         |
| `%d`, `%i`| Integer       | Displays a signed decimal integer.                 | `123`                                          |
| `%u`      | Integer       | Displays an unsigned decimal integer.              | `123`                                          |
| `%o`      | Integer       | Displays an unsigned octal integer.                | `173` (for `123`)                              |
| `%x`, `%X`| Integer       | Displays an unsigned hexadecimal integer (lowercase/uppercase). | `7b`, `7B` (for `123`)                         |
| `%f`      | Float         | Displays a floating-point number in decimal notation. | `123.000000` (for `123`)                       |
| `%e`, `%E`| Float         | Displays a floating-point number in scientific notation (e.g., `1.23e+02`). | `1.230000e+02`                                 |
| `%g`, `%G`| Float         | Uses `%f` or `%e` (or `%F`, `%E`) depending on magnitude; more compact. | `123` or `1.23e+02`                            |

---

## 4. Flags, Width, and Precision

You can add flags, a minimum field width, and precision modifiers between the `%` and the specifier.

```
%[FLAGS][WIDTH][.PRECISION]SPECIFIER
```

### 4.1 Flags

| Flag | Description                                                      | Example (`printf "%+d\n" 10` vs `printf "% d\n" 10`) |
| :--- | :--------------------------------------------------------------- | :--------------------------------------------------- |
| `-`  | Left-aligns the output within the field width.                   | `printf "|%-10s|\n" "text"` Output: `|text      |`  |
| `+`  | Always prints a sign (`+` or `-`) for signed numbers.            | `printf "%+d\n" 10` Output: `+10`                  |
| ` `  | (Space) Prints a space for positive signed numbers (if no `+`).  | `printf "% d\n" 10` Output: ` 10`                  |
| `0`  | Pads the number with leading zeros instead of spaces.            | `printf "%05d\n" 123` Output: `00123`              |
| `#`  | **Alternate form**: Prefix `0` for octal, `0x`/`0X` for hex. Always print decimal point for floats. | `printf "%#o %#x\n" 10 10` Output: `012 0xa`       |

### 4.2 Width

A decimal number that specifies the *minimum* width of the field. If the output is shorter, it's padded with spaces (or zeros with `0` flag). If longer, it's not truncated.

*   **Example**: `printf "|%10s|\n" "hello"` Output: `|     hello|`

### 4.3 Precision (`.PRECISION`)

*   **For strings (`%s`)**: Maximum number of characters to print.
*   **For floats (`%f`, `%e`, `%g`)**: Number of digits after the decimal point.
*   **For integers (`%d`, `%o`, `%x`)**: Minimum number of digits to print (padded with leading zeros if necessary).

*   **Example**: `printf "%.5s\n" "long_string"` Output: `long_`
*   **Example**: `printf "%.2f\n" 3.14159` Output: `3.14`

---

## 5. Escape Sequences (`\`)

Escape sequences are special character combinations interpreted by `printf` to produce non-literal characters or control output.

| Sequence | Description                           |
| :------- | :------------------------------------ |
| `\n`     | Newline (moves to the next line)      |
| `\t`     | Horizontal tab                        |
| `\b`     | Backspace                             |
| `\r`     | Carriage return (moves cursor to start of line) |
| `\a`     | Alert (bell sound)                    |
| `\e`     | Escape character (ASCII 27), same as `\033`. Crucial for ANSI colors. |
| `\0NN`   | Character with octal value `NN` (e.g., `\033` for Escape) |
| `\xHH`   | Character with hexadecimal value `HH` (e.g., `\x1b` for Escape) |
| `\\`     | A literal backslash                   |

---

## 6. Examples

### 6.1 Basic Output

```bash
printf "Hello, %s!\n" "World"
# Output: Hello, World!

printf "%s is %d years old.\n" "Alice" 30
# Output: Alice is 30 years old.

printf "The result is: %f\n" 12.345
# Output: The result is: 12.345000
```

### 6.2 Field Width & Alignment

```bash
printf "|%10s|%-10s|\n" "Right" "Left"
# Output: |     Right|Left      |

printf "|%5d|\n" 123
# Output: |  123|

printf "|%-5d|\n" 123
# Output: |123  |
```

### 6.3 Numeric Formatting

```bash
# Two decimal places
printf "Value: %.2f\n" 98.7654
# Output: Value: 98.77

# Pad with leading zeros to 5 digits
printf "ID: %05d\n" 42
# Output: ID: 00042

# Always show sign
printf "Temperature: %+d°C\n" 25
printf "Temperature: %+d°C\n" -5
# Output:
# Temperature: +25°C
# Temperature: -5°C

# Hexadecimal output with 0x prefix
printf "0x%X is %d in decimal.\n" 255 255
# Output: 0xFF is 255 in decimal.
```

### 6.4 Combining with `date`

```bash
printf "[$(date +%Y-%m-%d)]: Operation started.\n"
# Example Output: [2025-08-22]: Operation started.
```

### 6.5 Bash Colors & Visuals (Using ANSI Escape Codes)

`printf` is ideal for printing colored text and special symbols using ANSI escape codes, as it reliably interprets the `\e` (or `\033`) escape sequence.

**Define Colors and Symbols:**
```bash
CYAN="\e[38;2;21;255;255m" # True color cyan
RED="\e[0;31m"           # Standard red
NC="\e[0m"               # No Color (reset attributes)
INFO_SYMBOL="➡️"        # Right arrow symbol
GOOD_SYMBOL="✔️"        # Check mark symbol
ERROR_SYMBOL="❌"       # Cross mark symbol
```

**Original Cheatsheet Functions (adapted to `printf` and more generic arguments):**
```bash
# This function is not directly from your original, but reflects the usage pattern
# and ensures the original string literals and symbols are used.
glow() {
  local msg="$1"
  printf "[$(date +%F)]: ${CYAN}${INFO_SYMBOL} ${msg}${NC}\n"
}

bug() {
  local msg="$1"
  printf "[$(date +%F)]: ${RED}${ERROR_SYMBOL} ${msg}${NC}\n"
}

# Example usage of the functions
glow "System check passed"
bug "File not found"
```

**Outputting Other Symbols/Colors from Original:**
```bash
# Direct usage of symbols with printf
printf "Status: ${GOOD_SYMBOL} Success\n"
printf "Alert: ${ERROR_SYMBOL} Critical Error\n"
# Original specific symbols:
printf "An explosion happened! ${explosion}\n" # Assuming 'explosion' variable exists from original context
```
