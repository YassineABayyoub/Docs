# Libft - Notes

Libft is the first project of the 42 common core. The subject is to build your own C library, `libft.a`, that you reuse in later projects. It has three parts:

1. Part 1, libc functions. You rewrite standard functions with an `ft_` prefix, such as `ft_isalpha`, `ft_strlen`, `ft_memset`, `ft_memcpy`, `ft_strlcpy`, `ft_strchr`, `ft_strncmp`, `ft_atoi`, `ft_calloc` and `ft_strdup`. They must behave like the real ones.
2. Part 2, additional functions. These are not in libc: `ft_substr`, `ft_strjoin`, `ft_strtrim`, `ft_split`, `ft_itoa`, `ft_strmapi`, `ft_striteri` and the `ft_put*_fd` functions.
3. Part 3 linked lists. You define a `t_list` struct and write `ft_lstnew`, `ft_lstadd_front`, `ft_lstsize`, `ft_lstlast`, `ft_lstadd_back`, `ft_lstdelone`, `ft_lstclear`, `ft_lstiter` and `ft_lstmap`.

There are also some general rules:

* You need a `Makefile` with the rules `all`, `clean`, `fclean` and `re` that builds `libft.a`.
* Your code must follow the 42 Norm.
* You may only use the external functions the subject allows, mostly `malloc`, `free` and `write`.
* You must compile with `cc` and the flags `-Wall -Wextra -Werror`.

## Definitions

### C library

A C library is a collection of ready-made functions (and sometimes types and constants) that other programs can reuse instead of rewriting them. It has two parts:

* the compiled code of the functions, stored in a library file;
* header files (`.h`) that declare the prototypes, so the compiler knows how to call the functions.

The standard C library (libc) is the best known one: it provides `printf`, `malloc`, `strlen`, `write` and many others. Libft is a small library of our own that imitates part of libc.

### Types of libraries

By origin:

* Standard library: comes with the compiler and the system (libc).
* Third-party or custom library: written by someone else or by yourself (Libft is one).

By the way it is linked to a program:

| Type | Extension | How it works |
|------|-----------|--------------|
| Static library | `.a` (Unix), `.lib` (Windows) | An archive of object files (`.o`). At link time, the needed code is copied into the executable. The executable is bigger but does not need the library file to run. |
| Shared (dynamic) library | `.so` (Linux), `.dylib` (macOS), `.dll` (Windows) | The code is not copied into the executable. It is loaded at run time and shared between programs. The executable is smaller but needs the library file to run. |

`libft.a` is a static library.

### libc

libc is the standard C library: the library that implements the functions defined by the C standard (and, on Unix systems, by POSIX). It gives programs access to input/output (`printf`), memory management (`malloc`, `free`), string and memory handling (`strlen`, `memcpy`), character tests (`isalpha`), and wrappers around system calls (`write`, `read`).

* The name follows the Unix convention `lib` + name: `lib` + `c` = `libc`.
* It is not unique: each system has its own implementation (glibc on most Linux systems, musl on Alpine Linux, the C library inside `libSystem` on macOS).
* It exists as a shared library (`libc.so`) and sometimes as a static one (`libc.a`).
* The compiler links it automatically, which is why a program can call `printf` or `malloc` without asking for the library explicitly.

### libc function

A libc function is a function that belongs to the standard C library (libc). It is available in every C program and its prototype is declared in a standard header:

| Header | Examples |
|--------|----------|
| `<string.h>` | `strlen`, `strchr`, `strncmp`, `memset`, `memcpy`, `strlcpy`, `strdup` |
| `<ctype.h>` | `isalpha`, `isdigit`, `toupper`, `tolower` |
| `<stdlib.h>` | `malloc`, `free`, `calloc`, `atoi` |
| `<unistd.h>` | `write` (a thin wrapper around a system call) |

In Part 1 of Libft, we rewrite some of these functions with the `ft_` prefix. Each one must have the same prototype (except the prefix) and the same behavior as the original, which is described in its man page (`man 3 strlen`, for example).

## Part 1 - libc functions

Part 1 has 23 functions, grouped here by category. For each function: the Libft prototype, and a summary of its man page (`man 3 name`).

### 1. Character checks

Functions that test a character and return non-zero (true) or 0 (false). They come from `<ctype.h>`.

#### ft_isalpha
* Prototype: `int ft_isalpha(int c);`
* Man (`man 3 isalpha`): tests if `c` is a letter (`A-Z` or `a-z`).
```c
int	ft_isalpha(int c)
{
	return ((c >= 'A' && c <= 'Z') || (c >= 'a' && c <= 'z'));
}
```

#### ft_isdigit
* Prototype: `int ft_isdigit(int c);`
* Man (`man 3 isdigit`): checks whether `c` is a decimal digit (`0-9`).
```c
int	ft_isdigit(int c)
{
	return (c >= '0' && c <= '9');
}
```

#### ft_isalnum
* Prototype: `int ft_isalnum(int c);`
* Man (`man 3 isalnum`): tests if `c` is a letter or a digit.
```c
int	ft_isalnum(int c)
{
	return (ft_isalpha(c) || ft_isdigit(c));
}
```

#### ft_isascii
* Prototype: `int ft_isascii(int c);`
* Man (`man 3 isascii`): tests if `c` is in the ASCII range (0 to 127).
```c
int	ft_isascii(int c)
{
	return (c >= 0 && c <= 127);
}
```

#### ft_isprint
* Prototype: `int ft_isprint(int c);`
* Man (`man 3 isprint`): tests if `c` is a printable character (32 to 126, the space included).
```c
int	ft_isprint(int c)
{
	return (c >= 32 && c <= 126);
}
```
**Note: Why `int` instead of `char`?**

These functions use `int` to follow the original C `<ctype.h>` prototypes.
In C, `char` values are automatically promoted to `int` when passed to functions.
The `int` type also allows the standard functions to handle `EOF`.

**what is `EOF`**

EOF (End Of File) is the condition that means “no more data can be read from this file or stream.” There is no special character stored at the end of the file. The system knows the file is finished, and the read functions report it.

### 2. Character conversion

Functions that change the case of a character. They come from `<ctype.h>`.

#### ft_toupper
* Prototype: `int ft_toupper(int c);`
* Man (`man 3 toupper`): converts a lowercase letter to uppercase. Any other character is returned unchanged.

#### ft_tolower
* Prototype: `int ft_tolower(int c);`
* Man (`man 3 tolower`): converts an uppercase letter to lowercase. Any other character is returned unchanged.

### 3. String length, copy and concatenation

Functions that measure a string or copy it into a buffer of a known size. They come from `<string.h>`.

#### ft_strlen
* Prototype: `size_t ft_strlen(const char *s);`
* Man (`man 3 strlen`): returns the number of bytes before the terminating `'\0'` (the `'\0'` is not counted).

#### ft_strlcpy
* Prototype: `size_t ft_strlcpy(char *dst, const char *src, size_t dstsize);`
* Man (`man 3 strlcpy`): copies at most `dstsize - 1` bytes from `src` to `dst` and always null-terminates the result (if `dstsize` is not 0). Returns the length of `src`.

#### ft_strlcat
* Prototype: `size_t ft_strlcat(char *dst, const char *src, size_t dstsize);`
* Man (`man 3 strlcat`): appends `src` to the end of `dst`, so that the total size, with the `'\0'`, never exceeds `dstsize`. Returns the length it tried to create: the initial length of `dst` (at most `dstsize`) plus the length of `src`.

### 4. String search and comparison

Functions that look for a character or a string inside another string, or compare two strings. They come from `<string.h>`.

#### ft_strchr
* Prototype: `char *ft_strchr(const char *s, int c);`
* Man (`man 3 strchr`): returns a pointer to the first occurrence of `c` in `s`, or `NULL` if it is not found. The terminating `'\0'` is part of the string, so searching for `'\0'` returns a pointer to it.

#### ft_strrchr
* Prototype: `char *ft_strrchr(const char *s, int c);`
* Man (`man 3 strrchr`): same as `strchr`, but returns the last occurrence of `c`.

#### ft_strncmp
* Prototype: `int ft_strncmp(const char *s1, const char *s2, size_t n);`
* Man (`man 3 strncmp`): compares at most `n` bytes of two strings (as `unsigned char`) and stops at a `'\0'`. Returns 0 if they are equal, otherwise the difference between the first two bytes that differ.

#### ft_strnstr
* Prototype: `char *ft_strnstr(const char *big, const char *little, size_t len);`
* Man (`man 3 strnstr`): looks for the first occurrence of `little` in `big`, searching at most `len` bytes. If `little` is empty, returns `big`. If it is not found, returns `NULL`. Otherwise returns a pointer to the start of the match.

### 5. Memory functions

Functions that work on raw bytes, not on strings: they do not stop at a `'\0'` and always need a size. `memset`, `memcpy`, `memmove`, `memchr` and `memcmp` come from `<string.h>`; `bzero` comes from `<strings.h>`.

#### ft_memset
* Prototype: `void *ft_memset(void *b, int c, size_t len);`
* Man (`man 3 memset`): writes `len` bytes of value `c` (converted to `unsigned char`) into `b`. Returns `b`.

#### ft_bzero
* Prototype: `void ft_bzero(void *s, size_t n);`
* Man (`man 3 bzero`): writes `n` zero bytes into `s`. Returns nothing.

#### ft_memcpy
* Prototype: `void *ft_memcpy(void *dst, const void *src, size_t n);`
* Man (`man 3 memcpy`): copies `n` bytes from `src` to `dst`. The two areas must not overlap. Returns `dst`.

#### ft_memmove
* Prototype: `void *ft_memmove(void *dst, const void *src, size_t len);`
* Man (`man 3 memmove`): copies `len` bytes from `src` to `dst`. The two areas may overlap: the copy behaves as if it went through a temporary buffer. Returns `dst`.

#### ft_memchr
* Prototype: `void *ft_memchr(const void *s, int c, size_t n);`
* Man (`man 3 memchr`): looks for the first occurrence of `c` (converted to `unsigned char`) in the first `n` bytes of `s`. Returns a pointer to it, or `NULL`.

#### ft_memcmp
* Prototype: `int ft_memcmp(const void *s1, const void *s2, size_t n);`
* Man (`man 3 memcmp`): compares the first `n` bytes of two memory areas (as `unsigned char`). Returns 0 if they are equal (or if `n` is 0), otherwise the difference between the first two bytes that differ.

### 6. Conversion

A function that converts a string into a number. It comes from `<stdlib.h>`.

#### ft_atoi
* Prototype: `int ft_atoi(const char *str);`
* Man (`man 3 atoi`): converts the beginning of `str` to an `int`: it skips the leading whitespace, reads an optional sign, then the digits. It does not detect errors.

### 7. Memory allocation

Functions that reserve memory on the heap with `malloc`. The memory they return must be released later with `free`. They come from `<stdlib.h>` (`calloc`) and `<string.h>` (`strdup`).

#### ft_calloc
* Prototype: `void *ft_calloc(size_t count, size_t size);`
* Man (`man 3 calloc`): allocates memory for `count` objects of `size` bytes each and sets all the bytes to zero. Returns a pointer to it, or `NULL` if the allocation fails.

#### ft_strdup
* Prototype: `char *ft_strdup(const char *s1);`
* Man (`man 3 strdup`): allocates memory, copies the string `s1` into it (with the `'\0'`) and returns the new string, or `NULL` if the allocation fails.

## Notes

Questions to come back to later (not the time to answer them yet):

* What is memory padding?
* In a struct, is the padding based on the first member's type or on the biggest member's type?
* Which attribute removes the padding from a struct?

## Additional

Extra information that is not needed to work on Libft.

### Struct attribute `packed`

`__attribute__((packed))` (GCC and Clang) tells the compiler not to insert padding between the members of a struct, so its size is the sum of its members' sizes.

```c
struct s_a
{
	char	c;
	int		i;
};				/* sizeof = 8 (3 bytes of padding) */

struct __attribute__((packed)) s_b
{
	char	c;
	int		i;
};				/* sizeof = 5 (no padding) */
```