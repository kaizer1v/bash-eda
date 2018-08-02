# EDA using BASH commands

This notebook explores ways to do basic data analysis on files using shell commands. This requires you to know some basic bash scripting and shell commands. It also assumes that you are aware of basic data analysis methods. This project is inspired by [pandas](https://pandas.pydata.org/) - python based data analysis library, a very popular one.

For the purpose of this notebook, we are going to be using the `flags.csv` data available in this repo.

# Reading Files

**Display columns**

```bash
head -1 flags.csv
```

If the delimiter is different than a space and want to print every column in a new line, pipe the `sed` command like

```bash
# assuming that the delimiter is a `:` char
head -1 flags.csv | sed -e s/:/\\n/g
```

Similarly, if you want to know the number of columns

```bash
# assuming that the delimiter is a `:` char
head -1 flags.csv | sed -e s/:/\\n/g | wc -l
```


**Display top `n` columns**

```bash
head -n10 flags.csv
```

**Display bottom `n` columns**

```bash
tail -n10 flags.csv
```

**Display number of lines**

```bash
wc -l flags.csv
```

**Display total word counts**

```bash
wc flags.csv
```

**Display head of specific columns**

```bash
# displays head of column 10 and 11
awk -F: '{print $10 $11 }' flags.csv | head -5
```

**Get index of a column by it's name**
Let's say you want to know the index of the column `Continent`, you would do

```bash
# assuming that your delimeter is the `:` char
head -1 flags.csv | sed -e s/:/\\n/g | grep -n Continent | cut -c1-2 
```

if the column name does not exist, then it will not return anything.

**You can sort a file like**

```bash
# -r is for reverse/descending order
sort -r flags.csv
```

**You can view unique values of a column like**

For example, you want to view all distinct values of the `Continents` column

```bash
# 10th column points to the Continent column
awk -F: '{print $10}' flags.csv | sort -u
```

**To count the unique rows**

```bash
# counts the distinct count of `Name` column
awk -F: '{print $11}' flags.csv | sort -u | wc -l
```

**value counts**

get the value counts for a the `Continent` column, do the following

```bash
# Continent is the 11th column
awk -F: '{print $11}' flags.csv | sort | uniq -c
```

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
