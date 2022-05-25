---
layout:  post
title: Stream Editor Sed
description: a tutorial on Stream editor
date: 2022/06/01
permalink: /sed/ 
lecturers:
  - name:  Anass Belcaid
---

* (toc)
{:toc}

## Introduction

The acronym **Sed** (Stream EDitor) is a simple but powerful utility that parses
the text and transforms it **seamlessly**. SED was developed during 1973-74 by
Lee.E McMahon of Bell Labs. Today is runs on all the major operating systems.


McMahon wrote a general-purpose **line-oriented** editor, which eventually
became SED. SED borrowed syntax and may useful features from **ed editor**.
Since its beginning, it has support for `regular expression`. SED accepts inputs
from files as well as pipes. Additionally, it can also accept inputs from
standard input streams.


Here is a set of useful scenarios for **SEd**.

- Text Substitution
- Selective printing of text files.
- In a place editing of text files.
- Non Interactive editing of text files, and many more.


## Printing operations

Linux Sed command allows you to **print** only specific lines based on the
**line number** or pattern matches. `p` is a command for printing the data from
the pattern buffer.

To suppress automatic  printing of patern space use -n command with sed. `sed
-n` option wil not print anything, unless an explicit request to print is found.


```shell
sed -n 'ADDRESS'p filename

sed -n '/PATTERN/p' filename
```

In order to illustrate the functionalities of **sed**, let's create a simple file with given numbers.

```bash
cat thegeekstuff.txt
1. Linux - Sysadmin, Scripting etc.
2. Databases - Oracle, mySQL etc.
3. Hardware
4. Security (Firewall, Network, Online Security etc)
5. Storage
6. Cool gadgets and websites
7. Productivity (Too many technologies to explore, not much time available)
8. Website Design
9. Software Development
10.Windows- Sysadmin, reboot etc.
```

### Address format : numbers

In order to print **one given** line, we use its number:

```bash
sed -n '3'p thegeekstuff.txt
3. Hardware
```

If we want to skip a regular number of lines, we use

```bash
sed -n 'M~N'p filename
```


For example, `3~2p` will print every second line starting from the **third**
line.


```bash
sed -n '3~2'p thegeekstuff.txt

3. Hardware
5. Storage
7. Productivity (Too many technologies to explore, not much time available)
9. Software Development
```

k

If we want a **range** between two numbers, we use 

```bash
sed -n 'M,N'p filename
```

For example, if we want to print from the **fourth** line into the **eight**
line, we will use 

```bash
sed -n '4,8'p thegeekstuff.txt

4. Security (Firewall, Network, Online Security etc)
5. Storage
6. Cool gadgets and websites
7. Productivity (Too many technologies to explore, not much time available)
8. Website Design
```


Finally, if we want to start from a given line until the **end of the file**.

```bash
sed -n 'n,$'p filename
```


will print the content starting from the line `n` until the end of the file.

```bash
sed -n '4,$'p thegeekstuff.txt

4. Security (Firewall, Network, Online Security etc)
5. Storage
6. Cool gadgets and websites
7. Productivity (Too many technologies to explore, not much time available)
8. Website Design
9. Software Development
10.Windows- Sysadmin, reboot etc.
```


### Pattern

A **PATTERN** uses regular expression to identify the position of the processing. For example, Imagine we want to print all the lines that matches the pattern "Sysadmin"

```bash
sed -n /Sysadmin/p thegeekstuff.txt

1. Linux - Sysadmin, Scripting etc.
10.Windows- Sysadmin, reboot etc.
```

Now we can combine what we now about line numbering with those pattern. Imagine
we want to print the line containing the word **Hardware** into line **6**.


```bash
sed -n '/Hardware/,6p' thegeekstuff.txt

3. Hardware
4. Security (Firewall, Network, Online Security etc)
5. Storage
6. Cool gadgets and websites
```

Imagine now that we want print lines that start with **Storage** followed by
the **next 2 lines**.

```bash 
sed -n '/Storage/,+2p' thegeekstuff.txt

5. Storage
6. Cool gadgets and websites
7. Productivity (Too many technologies to explore, not much time available)
```


Finally, the start and the end of the range could be expressed as patterns.


```bash
sed -n '/Storage/,/Design/p' thegeekstuff.txt

5. Storage
6. Cool gadgets and websites
7. Productivity (Too many technologies to explore, not much time available)
8. Website Design
```

Will print the lines between the first line that contains **Storage** and the
first line that contain the word **Design**.


## Deleting

Instead of printing files, we could apply another command to delete the matching
lines. We will follow the same pattern but instead of using the  printing command **p**, we will use the deleting command **d**.

```bash
sed -n 'ADDRESS'd filename

sed -n /PATTERN/d filename
```

### Delete the Nth line

- `sed -n '3d' thegeekstuff.txt` : will delete the third line.
-  `sed -n '3~2d' thegeekstuff.txt`: will delete each second line staring from
    the third.
- `sed '4,8d' thegeekstuff.txt` will delete from the fourth to eight line.

- `sed '$d' thegeekstuff.txt` will delete the last line.

- `sed /Sysadmin/d thegeekstuff.txt` delete the line that matches **SysAdmin**.

**Exercise**

    >  Try to add some blank lines in your files and delete them using **sed**.


## Find and Replace

Now we will turn our attention to the most used command for sed which is
`substitution`.

The command **s** attempt to match the pattern space against the supplied
`REGEX`. If the match is successful, then that portion of the pattern space
which as matched is **replaced**.

```bash
sed 'ADDRESSs/REGEX/REPLACEMENT/FLAGS' filename
sed 'PATTERNs/REGEX/REPLACEMENT/FLAGS' filename
```

The set of acceptable flags is:

- `g` : Replace all the instances.
- `n` : Replace only the first **n** occurrences**.
- `p` : print the line if a substitution.
- `i` : regex matching is **case insensitive**.
- `w` : Write to a **file**.


### First example

Let's try to replace the word **Linux** with **Linux-Unix** using sed.


```bash
set 's/Linux/Linux-Unix/' thegeekstuff.txt
```

> We remark that in the first line, the **second** is not replaced.


```bash
set 's/Linux/Linux-Unix/g' thegeekstuff.txt
```

Also could be done by specifying the number of occurrences.

```bash
set 's/Linux/Linux-Unix/2' thegeekstuff.txt
```

### Writing substitution to a file

With **sed** the original file is not touched (modified). If we want to keep
the result of the substitution, we could write on a file:

```bash
$ sed -n 's/Linux/Linux-Unix/gpw output' thegeekstuff.txt
```

- **g** : is for global substitution.
- **p** : to print the substitution in the standard output.
- **w output** : to write the content in a file.


#### Pattern substitution

Also we could use **regular expression** to match a line and delete is
(substitution with empty string).

> Let's imagine the scenario where we want to delete the **last three
characters**.


```bash
$ sed 's/...$//' thegeekstuff.txt
```
> As an exercice, take any python file and try to remove all the comments from
it.

