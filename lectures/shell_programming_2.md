---
layout:  post
title: Shell programming II
description:  A Deeper look for more advanced commands
date: 2022/05/01
permalink: /shellProg2/ 
lecturers:
  - name:  Anass Belcaid
---

* (toc)
{:toc}





## Pipelines

The core unix bash programming uses `streams` to manipulate text data. Since in
**unix** everything is a file. your data are files. Your directories are files.
Your hard drive is a file. Linux choose this mechanism to **allows data to
move** from one file to another. The notion of **stream** is literally what is
sounds like: a little river of bits pouring from one file into another.

### The standard streams

Within the Unix world is a general convention that each file is connected to at
least **three** streams.

1. **Standard in (stdin)**: The standard stream for input into a file.

2. **Standard out(stdout)**: The standard stream for output of a file.

3. **Standard error (stderr)**: The standard stream for error output file.

We can use those streams by the following operators:

1. **process > data file**: redirect the output of a process to the data.

  ```bash
  #!/usr/local/bash
  echo Hello
  
  ./process > out
  ```

2.  **process >> data file**: redirect the output **at the end(append)** of a process to the data.

  ```bash
  #!/usr/local/bash
  echo Hello
  
  ./process >> out
  ./process >> out   # now out has (Hello\n Hello)
  ```
3. **process < data file** read the contents from the data file.
  
  ```bash
  more file.txt   # show the content of the file
    'Hello' 
  cat < file.txt  # Will show the text hello
  ```

> Try to search how to redirect the to standard error?


## Pipelines

**Pipelines**, often called pipes, is a way to chain commands and connect output
from one command to the input of the next. A pipeline is represented by the
pipe character `|`. It's particularly handy when a complex or long input is
required for a command.

```shell
command1 | command2
```

By default pipelines redirects only the standard output. if you want to include
the standard error you need to use the form `|&` which short hand for `2>&1 |`.

### Example

Imagine you quickly want to know the number of entries in a directory, you can
use a pipe to redirect the output of the `ls` command to the `wc` command with
option `-l`.

```bash
ls  | wc -l
```

Or least imagine, you want to see only the first **15 results**.


```bash
ls  | head -n 15
```

## Exercise

In this exercise, you need to print the number of processors based on the
information in the **cpuinof** file (`/proc/cpuinfo/`).


> Hint you can chain more than two commands.

## grep

A regular expression, often referred to as **regex**, is a description of a
pattern of text.

- There are countless applications of regular expressions.

    - Search of text in a given file.
    - Search/Replace text in a file.

- Virtually all programming languages implement regular expressin.
  - For example in `cpp`, the library **regex** contains all the tools for
      defining and searching a regular expression.



### Simple regex

Let's imagine that we have the file `candies.txt` with the following contents.

```bash
candies.txt

Twix
Sweet Tarts
Chocolate
Almond Joy
Jolly Ranchers
Kit Kat.
Dark chocolate
```

Lets search for the name **Chocolate** in this file.

```bash
grep -E "Chocoloate" candies.txt
```

- grep is **case_sensitive**.
- Remark that the command print the full line.


How about if we want to make the search **case insensitive**.

```bash
grep -Ei "Chocoloate" candies.txt
```

Let's imagine that we search for line that contain 

- **Any character** followed by an **a**.

```bash
grep -E ".a" candies.txt
```

> The character . matches **anything**.


Now we want a candy  that start by **K**.


```bash
grep -E "^K" candies.txt
```

> The character ^ matches the beginning of a line.


Imagine that we want to match a **T** but the in the start of  a word.


```bash
grep -E "\<K" candies.txt
```

> A word is considered as a string of characters consisting of letter, numbers, and underscores.

And for the end of a word.

```bash
grep -E "t\>" candies.txt
```

Here is a simple table for special character in regular expression.

- **.** : any character.
- **^** : start of line 
- **$**: end of line
- **\<**: start of a word.
- **\>**: end of a word.
- **\**:  Escape character.


----

We could search for multiples match using the **or** operator.


```bash
grep -E "Twix|Tarts" candies.txt
```

> Be careful of leading spaces 


What will be the result of this regular expression:


```bash
grep -E "(e|a)t" candies.txt
```

### Quantifier.

If we want to match an instance that we don't know the number of occurrences, we
could use **quantifiers** to simplify our search.



```bash
grep -E "e*t" candies.txt
```

> The operator * means **zero** or more of the previous pattern.



```bash
grep -E "e+t" candies.txt
```

> The operator + means **one** or more of the previous pattern.


```bash
grep -E "r?t" candies.txt
```

> The operator ? means **zero or one** or more of the previous pattern.


What will be the result of the command:


```bash
grep -E "(es)+" candies.txt
```


### Groups

If we want to specify a group or of characters. we use the **[]**.

```bash
grep -E "[abc]" candies.txt
```

> the [abc] = (a|b|c)

We can use the **-** to specify **ranges**.


```bash
grep -E "[a-z]" candies.txt
```

- `[a-z]` is all lowercase letters.
- `[A-Z]` is all uppercase letters.
- `[0-9]` is all digits.
- `[a-zA-Z]` is all letters.


How about if we want to match anything but a set of given pattern. for that we
use the **^** character.


```bash
grep -E "[^ao]" candies.txt
```

> [^ao] matches anything that **doesnt** contain the ao.


We also can use the **{}** to specify the number of repetition of a pattern.


```bash
grep -E "[0-9]{2}" passwords.txt
```

- **{x}**: matches exactly x times.
- **{x,}**: matches x or **more**.
- **{,x}**: matches x or **fewer**.
- **{x,y}** : matches a number between **x** and **y**.


### Backreference

Imagine that we wants to search for **any succession** of any two characters.


```bash
grep -E "(..)\1" passwords.txt
```

> The \1 special character is a **back reference** for the previous match.
