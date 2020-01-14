# README

## Data Analysis using the shell

* Read a csv file
* head\(10\)
* tail\(10\)
* show number of rows \(.shape\)
* show all columns \(.columns\)
* display specific rows by index   \(df.iloc\[:, 1, 2, 3\]\)
* display specific column by index \(df.iloc\[1, 2, 3, :\]\)
* display specific column by name

If you want to do some really advanced stuff on the bash, you can also check out the [csvkit python library](https://pypi.org/project/csvkit/)

There's also a great book on [Data Science at the command line](https://www.datascienceatthecommandline.com/index.html)

## cat

`cat <filename>` will display contents of any given file. You can concatinate multiple files to read at onces like

```bash
cat file1.txt file2.txt
```

and shell will display the contents of the files in order specified. You can also use `cat` to copy contents of one file into another, like

```bash
cat file1.txt > newfile.txt
```

Here the `>` will bascially overwrite the contents in `newfile.txt`. If you want to append contents of `file1.txt` into `newfile.txt` after the content already present within `newfile.txt`, then use the `>>` like

```bash
cat file1.txt >> newfile.txt
```

Similarly, you can even read the file in reverse, by using `tac` \(_reverse of `cat`_\)

```bash
tac file1.txt
```

### using `cat` to create a file

You can use `cat` command to create a file, just like `touch` or a `vim` editor

```bash
cat > file2.txt
```

will bring the cursor to a new line and will allow you to enter the contents of the file on the shell. Once you finish entering the text, press the `ctrl + d` key to exit and the file will automatically be saved.

_The only drawback in entering the contents in such a way is that you won't be allowed to remove lines after the new line entered._

You can even directly append content into an existing file by using the `>>`

```bash
cat >> file2.txt
This is a new line appended at the end of the file.
```

and then press the `ctrl + d` keys to save and exit.

## echo

`echo` simply displays the text and can be used like

```bash
echo hello
hello
```

Unlike, `cat`, `echo` will not read and output the contents of the file. This is more like just a **print** statement.

### write and append to files

You can even use `echo` to append lines into a file using the `>` and the `>>`

```bash
# append new line to existing file
echo last line >> file1.txt

# overwrite content into a file
echo overwritten content > file1.txt
```

You can use the `-e` option to enable input of special characters like the

* newline `\n`
* tab `\t`

### global variables

You can echo the values of the global variables onto shell

```bash
echo $USER
```

> _To view list of all global variables on bash, use the `printenv` command on shell_

## sed

Also known as **Stream Editor**, primarily used for modifying contents of a file, like fileting content within a file and substituting values. The two options mentioned below allow you run commands directly from an external file \(`-f`\) or from the shell command line \(`-e`\).

```bash
# run srcipt inline on a textfile
sed -e command <filename>

# run script from a scriptfile on a textfile
sed -f scriptfile <filename>

# run script inline and overwrite within the textfile
sed -i command <filename>
```

The commands that you want to run on a file are usually regular expressions \(covered later in this file\) on files to match pattern files.

The syntax of `sed` command is like

```bash
# replace the first occurance of the pattern
sed -e /pattern/replace_string/ <filename>

# replace all the occurances of the pattern
sed s/pattern/replace_string/g <filename>

# replace all occurances in line range 1 to 3
sed 1,3s/pattern/replace_string/g <filename>

# replace all occurances and overwrite the file
sed -i s/pattern/replace_string/g <filename>
```

You can even replace the text and write into a new file

```bash
sed -e s/pattern/replace_string/g file1 > file2
```

### multiple patterns

Replace multiple patterns at the same time like

```bash
sed -e 's/01/JAN/' -e 's/02/FEB/' file1 > file2
```

will replace **01** to **JAN** as well as **02** to **FEB** from file1 and write into a file2.

## awk

## grep

You mainly use `grep` to search for contents within a file.

Say you have a `sample.txt` that looks something like this:

```text
Fred apples 20
Susy oranges 5
Mark watermellons 12
Robert pears 4
Terry oranges 9
Lisa peaches 7
Susy oranges 12
Mark grapes 39
Anne mangoes 7
Greg pineapples 3
Oliver rockmellons 2
Betty limes 14
```

Now, let's use grep to search for some specific lines in this file.

#### Search for all the lines that contain `apple`

```bash
grep apple sample.txt
```

#### Search for all the lines that contain `apple` and also display the line numbers

```bash
grep -n apple sample.txt
```

#### Search for all the lines that contain `apple` and have an even number

```bash
grep apple sample.txt | grep [24680]$
```

#### Search for all the lines that contain `oranges` but not `susy`

```bash
grep oranges sample.txt | grep -v Susy
```

If you wanted `susy` to be case-sensitive, add the -i option

```bash
grep oranges sample.txt | grep -vi susy
```

#### Search for all the lines that start from letters between C and M and have `apples`

```bash
grep -n ^[C-Mc-m] sample.txt | grep apples
```

#### How many lines contain the word `apple`

```bash
grep -c apples sample.txt
```

* EXTRA \*

#### How many lines in a file

```bash
wc -l sample.txt
```

 _`wc` as in word count_ 

By now you would have understood that at the heart of `grep` lies  _regular expressions_ . In fact the name `grep` comes from the early days of unix, where if one had to search for a the word `junk` in a file, they wrote it like\*

`g/junk/p`

This feature of searching within a file was so widely used that they separated this search feature and called it `regular expression` and thus, if you had to find any `expression` within a file, you would use the command

`g/re/p` \_ re for regular expression and thus,  _grep_ 

So, what are regular expressions ?

## re

[Ref Link](http://www.grymoire.com/Unix/Regular.html#uh-1)

A regular expression syntax consists of 3 main parts, namely

1. An Anchor - specifies the position of a pattern
2. Character Sets - the characters that match once or more at that specific position
3. Modifiers - the number of times a previous character set is repeated

For Example `^#*` is the simplist example show-casing all the 3 parts.

1. `^` - indicates that the character set should `begin with`.
2. `#` - the character '\#' is the character that the line should start with.
3. `*` - repeat that search for that character multiple times

