# Reverse Engineering - C Source Code Challenges

C programming language questions covering standard library functions, data types, format specifiers, and source code analysis.

---

## Question 13 - printf()

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 2 |

**Prompt:**

> What does the printf() function do?
>
> Choose the correct statement from the options below:
> - It prints a file on the default printer
> - It sends formatted output to standard out (E.g. the terminal)
> - It reads a line from the specified stream and stores it into a character array
> - It compares two strings (character arrays)

**Reference Intelligence:**

- `printf()` is a standard library function in C defined in `<stdio.h>`
- It outputs formatted text to the standard output stream, typically the terminal
- Commonly used for debugging and displaying program results
- Supports format specifiers like `%d`, `%s`, `%f`, etc.

**Steps Taken:**

1. Reviewed C standard library documentation
2. Verified function behavior in `<stdio.h>`
3. Cross-checked usage examples in C programming guides
4. Confirmed output is directed to standard output

**Verified Answer:**

> It sends formatted output to standard out (E.g. the terminal)

**Notes:**

- `printf()` is one of the most frequently used functions in C
- Misunderstanding its role can lead to confusion in I/O operations
- Refer to Section 9.1 in Student Resources for formatted output syntax and examples

---

## Question 14 - fgets()

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 2 |

**Prompt:**

> What does the fgets() function do?
>
> Choose the correct statement from the options below:
> - It reads a line from the specified stream and stores it into a character array
> - It gets a file uploaded to a deployed web server
> - It ensures that the program stack is properly aligned
> - It gets files pointed to by the program

**Reference Intelligence:**

- `fgets()` is a standard C library function defined in `<stdio.h>`
- It reads characters from a specified input stream into a character array
- Stops reading after a newline character, EOF, or when the specified limit is reached
- Commonly used for safe string input to avoid buffer overflows

**Steps Taken:**

1. Reviewed C standard library documentation
2. Verified function prototype and behavior in `<stdio.h>`
3. Cross-checked usage examples in C programming tutorials
4. Confirmed correct usage for reading input safely into buffers

**Verified Answer:**

> It reads a line from the specified stream and stores it into a character array

**Notes:**

- `fgets()` is safer than `gets()` due to its buffer size limit
- Often used with `stdin` for reading user input
- Refer to Section 9.3 in Student Resources for input handling functions

---

## Question 15 - strcmp()

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 2 |

**Prompt:**

> What does the strcmp() function do?
>
> Choose the correct statement from the options below:
> - It breaks each argument down into hex and returns the output
> - It reads a line from the specified stream and stores it into a character array
> - It sends formatted output to standard out (E.g. the terminal)
> - It compares two strings (character arrays)

**Reference Intelligence:**

- `strcmp()` is a standard C library function defined in `<string.h>`
- It compares two null-terminated strings character by character
- Returns 0 if strings are equal, a positive or negative value if they differ
- Commonly used in control flow and string matching logic

**Steps Taken:**

1. Reviewed C standard library documentation
2. Verified function prototype and behavior in `<string.h>`
3. Cross-checked usage examples in C programming tutorials
4. Confirmed return values and comparison logic

**Verified Answer:**

> It compares two strings (character arrays)

**Notes:**

- `strcmp()` is case-sensitive and stops comparing at the first mismatch
- Useful for validating user input, parsing commands, and string filtering
- Refer to Section 9.5 in Student Resources for string comparison techniques

---

## Question 16 - strcmp() 2

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 2 |

**Prompt:**

> What is a successful return code for the strcmp() function if the two strings are the same?

**Reference Intelligence:**

- `strcmp()` is defined in `<string.h>` and compares two null-terminated strings
- It returns 0 when both strings are identical
- A non-zero value indicates a mismatch, with the sign showing lexicographic order
- Commonly used in conditionals to check string equality

**Steps Taken:**

1. Reviewed C standard library documentation
2. Verified return values for `strcmp()` in `<string.h>`
3. Cross-checked behavior in C programming examples
4. Confirmed that 0 indicates string equality

**Verified Answer:**

> 0

**Notes:**

- Always check for `== 0` when testing string equality with `strcmp()`
- Useful for validating user input, command parsing, and string matching
- Refer to Section 9.6 in Student Resources for return codes and usage examples

---

## Question 17 - main()

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 2 |

**Prompt:**

> What is main()?
>
> Choose the correct statement from the options below:
> - It is the designated entry point to a program
> - It's only role is to perform the majority of encryption operations for all Windows programs
> - Main() is not a standard function in C

**Reference Intelligence:**

- `main()` is the standard entry point for execution in C programs
- Defined by the C standard as the starting function where execution begins
- Typically returns an integer to indicate program exit status
- Accepts optional parameters: `int argc, char *argv[]` for command-line arguments

**Steps Taken:**

1. Reviewed ANSI C and ISO C standards
2. Verified function signature and role in program execution
3. Cross-checked with compiler behavior and runtime documentation
4. Confirmed `main()` is mandatory and standard in C programs

**Verified Answer:**

> It is the designated entry point to a program

**Notes:**

- `main()` is required for program execution unless overridden by platform-specific entry points
- Return value from `main()` is passed to the operating system
- Refer to Section 9.8 in Student Resources for function structure and execution flow

---

## Question 18 - C Source Code 1

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 2 |

**Prompt:**

> ```c
> int main(void){
>     int num1 = 77;
>     printf("%d",num1);
>     return 0;
> }
> ```
>
> What is num1's variable type?

**Reference Intelligence:**

- `num1` is declared using the `int` keyword
- In C, `int` represents a 32-bit signed integer on most systems
- The `%d` format specifier in `printf()` confirms that `num1` is an integer
- This type is used for whole numbers without decimal points

**Steps Taken:**

1. Analyzed the variable declaration in the source code
2. Verified the format specifier used in `printf()`
3. Cross-checked with C data type definitions
4. Confirmed `int` as the correct type for `num1`

**Verified Answer:**

> int

**Notes:**

- `int` is one of the most commonly used data types in C
- It typically occupies 4 bytes of memory depending on the system architecture
- Refer to Section 10.1 in Student Resources for C data types and their usage

---

## Question 19 - C Source Code 2

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 2 |

**Prompt:**

> ```c
> int main(void){
>     int num1 = 77;
>     printf("%d",num1);
>     return 0;
> }
> ```
>
> What is num1's value?

**Reference Intelligence:**

- `num1` is declared and initialized with the value 77
- The `printf()` function outputs this value using the `%d` format specifier
- No operations modify `num1` after its initialization

**Steps Taken:**

1. Analyzed the variable declaration and initialization
2. Verified that no changes are made to `num1`
3. Confirmed the output reflects the original value

**Verified Answer:**

> 77

**Notes:**

- Initialization assigns a value to a variable at the time of declaration
- `num1` remains unchanged throughout the program
- Refer to Section 10.2 in Student Resources for variable initialization and output

---

## Question 20 - C Source Code 3

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 5 |

**Prompt:**

> ```c
> int main(void){
>     int num1 = 77;
>     printf("%d",num1);
>     return 0;
> }
> ```
>
> What value is printed to the terminal upon execution?

**Reference Intelligence:**

- `num1` is declared and initialized with the value 77
- `printf("%d", num1);` prints the integer value of `num1` to standard output
- No other operations modify `num1` before printing
- The `%d` format specifier ensures the output is in decimal integer format

**Steps Taken:**

1. Analyzed the source code line by line
2. Verified variable initialization and usage in `printf()`
3. Confirmed output behavior based on format specifier
4. Validated that the printed value matches the initialized value

**Verified Answer:**

> 77

**Notes:**

- The terminal will display `77` as output when the program runs
- `return 0;` indicates successful program termination
- Refer to Section 10.3 in Student Resources for output formatting and execution flow

---

## Question 21 - C Source Code 4

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 5 |

**Prompt:**

> ```c
> int main(void){
>     int num1 = 77;
>     printf("%d",num1);
>     return 0;
> }
> ```
>
> What is "%d" in this program?
>
> Choose the correct statement from the options below:
> - %d stands for deviation and acts a place holder for printing standard deviation within a dataset
> - %d is a placeholder for day and parses the "day" out of a provided date
> - %d is a format specifier for a signed decimal/integer

**Reference Intelligence:**

- `%d` is a format specifier used in `printf()` to print signed decimal integers
- It matches variables of type `int` and displays their value in base 10
- It does not relate to dates, deviation, or statistical functions

**Steps Taken:**

1. Reviewed C standard I/O formatting rules in `<stdio.h>`
2. Verified `%d` behavior in `printf()` documentation
3. Cross-checked with examples in C programming guides
4. Confirmed `%d` is used for signed integers

**Verified Answer:**

> %d is a format specifier for a signed decimal/integer

**Notes:**

- Other format specifiers include `%f` for floating-point, `%s` for strings, `%c` for characters
- Refer to Section 10.4 in Student Resources for format specifier reference

---

## Question 22 - C Source Code 5

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 5 |

**Prompt:**

> ```c
> int main(void){
>     int num1 = 77;
>     printf("%d",num1);
>     return 0;
> }
> ```
>
> What is important about "return 0"?
>
> Choose the correct statement from the options below:
> - A return code of 0 is generally a clean exit of the program
> - A return code of 0 is the default exit code for a program exiting under catastrophic failure
> - A return code of 0 means that the program ran incorrectly and nothing happened during its execution

**Reference Intelligence:**

- In C, `main()` returns an integer to the operating system
- A return value of 0 conventionally signals successful program execution
- Non-zero return values typically indicate errors or abnormal termination
- This convention is widely used across operating systems and compilers

**Steps Taken:**

1. Reviewed C standard execution flow and return codes
2. Verified behavior in compiler and runtime documentation
3. Cross-checked with operating system conventions for exit status
4. Confirmed that `return 0;` indicates a clean, successful exit

**Verified Answer:**

> A return code of 0 is generally a clean exit of the program

**Notes:**

- `return 0;` is often used to signal that the program completed without errors
- Refer to Section 10.5 in Student Resources for exit codes and program termination behavior

---

## Question 23 - C Source Code 6

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 5 |

**Prompt:**

> ```c
> int main(void){
>     char *word1 = "word";
>     char *word2 = "words";
>     if(strcmp(word1,word2)==0){
>         printf("same");
>     }
>     else{
>         printf("different");
>     }
> }
> ```
>
> What is returned to stdout when executed?

**Reference Intelligence:**

- `word1` is initialized to `"word"`
- `word2` is initialized to `"words"`
- `strcmp(word1, word2)` compares the two strings
- Since `"word"` is not equal to `"words"`, `strcmp()` returns a non-zero value
- The `else` block executes, printing `"different"` to stdout

**Steps Taken:**

1. Analyzed string initialization and comparison logic
2. Verified `strcmp()` behavior for unequal strings
3. Confirmed control flow based on comparison result
4. Validated output from the `else` block

**Verified Answer:**

> different

**Notes:**

- `strcmp()` returns 0 only when strings are exactly equal
- Even a single character difference results in a non-zero return
- Refer to Section 10.6 in Student Resources for string comparison and control flow
