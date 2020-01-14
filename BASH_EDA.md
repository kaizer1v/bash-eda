# EDA using BASH commands

This notebook explores ways to do basic data analysis on files using shell commands. These set of commands are really targeted towards linux or command line users - and who need to perform certain basic data related tasks, that you might be able to perform on excel or [pandas](pandas.pydata.org)

**WHY use this?** - you may ask

* You may not have access to Excel
* You may not have a UI for the OS (eg: working on a remote server)
* You may not have access to pandas (the python library)
* The file is too large to load on any UI & takes a lot of time
* You might quickly want to glance through what the data contains

This requires you to know some basics about the operating system and how to use the shell / command line and also regular expressions.

For the purpose of this notebook, we are going to be using the `flags.csv` data [available in this repo](flags.csv).

*NOTE - before you begin with the commands, ensure your command line is in the directory that contains the flags.csv file*

Also for every command that you see in this notebook, you can always check the `--help` or the `man` command to know more details about it. Eg: running the `head --help` will provide the necessary details for the `head` command.

Firstly, you want to open the file and see what's in it. Here's a way to view the first `n` lines to get an idea about what the data contains.

## Columns

```bash
head -1 flags.csv
```

will display the first two lines from the file. The first line would be the column titles (_if the data contains them_), else it would be the first row of the data file.

Each column would be separated by the delimiter that is contained in the file. This could be a `tab` or a `,` or anything else that is distinct enough and not contained in the data itself. Every row would be separated by the same delimiter.

If the delimiter is different than a `space`, for example, a `:`, you can print the column titles, each in a new line. To do that, pipe the `sed` command like

```bash
# you can replace the `:` char, with any other delimiter
head -1 flags.csv | sed -e s/:/\\n/g
```

If you want to know the number of columns that are present, append the `wc -l` command at the end of the above command

```bash
# assuming that the delimiter is a `:` char
head -1 flags.csv | sed -e s/:/\\n/g | wc -l
```

## Rows

We still need to understand what the data contains

**Display top `n` & bottom `n` rows**

```bash
head -n10 flags.csv
```

```bash
tail -n10 flags.csv
```

As the name `head` and `tail` suggests, you can view the top and bottom rows.

## Overview

Now that we have viewed the file the way we want, we would want to get some basic information about the file - like the total number of rows, the data types of each column etc. 

**Row Count**

If you want to know the total number of rows in the file. the `-l` parameter stands for lines.

```bash
wc -l flags.csv
```

_NOTE - `wc` stands for word count & you'll see this being used often_ 

**Word Count**

```bash
wc flags.csv
```

**Specific Columns**

Let's view a specific column in a little more in detail

```bash
# displays head of column 10
awk -F: '{print $10}' flags.csv | head -5
```

In the above command, we searched the column by the index, i.e. the number 10. But how did we get to know that.

**Get index of a column by it's name**

Let's say you want to know the index of the column `Continent`, you would do

```bash
# assuming that your delimeter is the `:` char
head -1 flags.csv | sed -e s/:/\\n/g | grep -n Continent | cut -c1-2 
```

If the column name does not exist, or there's a typo, then it will not return anything. Now, using the index, you can display only that particular column. You can print multiple columns separated by a tab, (_denoted by `"\t"`_)

```bash
awk -F: '{print $10 "\t" $11}' flags.csv | head -5
```

Once we view the data, we might feel some of the columns to be unnecessary and don't need to view them. To view only the required columns, we can run the following command.

```bash
cut -d: -f1,10,11 flags.csv
```

This will only show the 1st, 10th and 11th column.


## Sorting

The next basic activity one would perform on a data file is sorting. You can sort a file using the `sort` command.

```bash
# -r is for reverse/descending order
sort -r flags.csv
```

By default, this will sort based on the first column. But you can even sort based on a specific column.

Let's sort the data by the 10th column i.e. by `Name` - which is the country name in descending order

```bash
# -k is for column #, -r is for reverse
sort -k10 -r
```

We can apply this command to only on the necessary columns that we need by

```bash
cut -d: -f1,10,11 flags.csv | sort -k2 -r
```

Note, once we apply the first `cut` command, we only have 3 columns to view i.e. the `ID`, the `Name` and the `Continent`. Here, the index also gets updated i.e. 1, 2, and 3 respectively. The part following the `|` in the above command, refers to `Name` as `-k2`.


## Filtering

Let's filter by the Continent column and view only **Europe** continent

```bash
cut -d: -f1,10,11 flags.csv | grep Europe
```

and if you want to see the number of countries within Europe, you can

```bash
cut -d: -f1,10,11 flags.csv | grep Europe | wc -l
# 45 = the number of rows filtered by `Europe`
```

Finally, let's say you want the sum total of the 2nd column after filtering for `Europe`. We'll do this in two steps

**Step 1 - Sum of a column**

```bash
awk -F: '{sum += $2} END {print sum}' flags.csv
```

**Step 2 - Filtering & summing**

We already saw how to filter by a column. Let's combine that with the summing command using the `|` (pipe) operator

```bash
cut -d: -f1,2,10,11 flags.csv | grep Europe | awk -F: '{sum += $2} END {print sum}'
```
_NOTE - we need to include the column to be summed i.e. 2_


**NOT** 

Similarly, if you want to view all the Continent's other than Europe, then simply add the `-v` option in the grep

```bash
cut -d: -f1,10,11 flags.csv | grep -cv Europe
```

the `-c` in the `grep` command will only return the `count` by defult instead of adding `wc -l` at the end of the line.


## Unique Values

For example, you want to view all unique values of the `Continents` column

```bash
# 10th column points to the Continent column
awk -F: '{print $10}' flags.csv | sort -u
```

But if you want to sort the column numerically, say column 2, then you also add the `-n` flag like

```bash
# 2nd column is a numeric column
awk -F: '{print $2}' flags.csv | sort -un
```

Appending the `wc -l` command to the previous one will give you the total number of unique values (all together)

```bash
# counts the distinct count of `Name` column
awk -F: '{print $11}' flags.csv | sort -u | wc -l
```

**Value Counts**

This is different from the command above. This command will give you a total count of all the unique values combined. Eg: If you want to see, how many values with "Africa", along with how many values with "Europe", then run the following command

```bash
# Continent is the 11th column
awk -F: '{print $11}' flags.csv | sort | uniq -c
```

An even improved way of doing it is by using `cut` like

```bash
cut -f11 -d: flags.csv | sort | uniq -c | sort -k1,1nr
```

will sort the value counts in descending order for the `Continent` column i.e. column 11


## Merging

For this purposes, consider two files containing the following texts

*file1.txt*

```
Nawazuddin grapes 27
Sardar wasseypur 4
Piyush dhanbad 8
Brad fury 2
DiNero casino 5
Duvall godfather 12
Medson purple 30
Flintstone dude 7
```

and *file2.txt* containing

```
Nawazuddin <pink>
Sardar <blue>
Piyush <orange>
Brad <blue>
DiNero <red>
Duvall <purple>
Medson <blue>
Flintstone <orange>
```

You can merge two files like 

```bash
# delimeter as a space
join file1.txt file2.txt
```

will give you the output **merged** !! 

```
Nawazuddin <pink> grapes 27
Sardar <blue> wasseypur 4
Piyush <orange> dhanbad 8
Brad <blue> fury 2
DiNero <red> casino 5
Duvall <purple> godfather 12
Medson <blue> purple 30
Flintstone <orange> dude 7
```

## Large File Operations

**Preview a large file**

```bash
less large_file.csv
```

will help you load only the section of the file and then load the remaining as you navigate - saves memory usage for really large files - without reading the full file.

Now, if you want to view only the rows containing a particular pattern and view it partly

```bash
strings large_file.csv | grep some_string | less
```

Similary,

**view the count occurances of `some_string`**

```bash
strings large_file.csv | grep some_string | wc -l
```

## Numeric Value Operations

For this section prepare with a sample file which you download by running the following command

```bash
wget https://www.data36.com/demo1.csv --no-check-certificate
```

will save a file called `demo1.csv`

**Sort numeric values**

by running the `sort demo1.csv` will sort the values assuming that it contains text values, but you will need to add the `-n` parameter to make it sort like numbers

```bash
# the `u` will only return unique values
sort -nu demo1.csv
```

**Min & Max**

So if you had to get the max, min values you would

```bash
# min
sort -nu demo1.csv | head -1
# max
sort -nu demo1.csv | tail -1
```