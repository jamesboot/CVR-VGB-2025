# Text Processing and Linux Bash Scripting: Tutorial and Practical

## Overview

This tutorial introduces the fundamentals of  command-line text processing and Bash scripting. Linux is especially useful for life scientists and bioinformaticians who regularly handle large datasets, such as genome sequences and metadata.



## 1. Text processing

### 1.1 Searching using `grep` (Global Regular Expression Print)

`grep` is a very useful command that searches for and prints lines that contain a specified pattern in a file. These patterns can be very simple (searching for a character, word or phrase) but can also when needed detect complex patterns using the regular expression (regex) syntax. Exploring the regex syntax could be its own tutorial, so instead it is more useful to look for websites that help build regular expressions for the information you want to extract (regexr.com is an example). For this example, we will filter a fasta file to only show the sequence header information (headers can be identified by the > character)


```bash
user@alpha2:~$ grep ">" sc2.fasta 
>NC_045512.2 Severe acute respiratory syndrome coronavirus 2 isolate Wuhan-Hu-1, complete genome
```

Another example would be extracting the gene names from genbank files. We also use the `-o` flag to print only the parts of the input that match the expression rather than the full line.

```bash
user@alpha2:~$ grep "gene=" sarscov2.gb
```

<details>
  <summary>Click me</summary>
  
  ```bash
                     /gene="ORF1ab"
                     /gene="S"
                     /gene="ORF3a"
                     /gene="E"
                     /gene="M"
                     /gene="ORF6"
                     /gene="ORF7a"
                     /gene="ORF7b"
                     /gene="ORF8"
                     /gene="N"
                     /gene="ORF10"
  ```
</details>


Using a basic regular expression `'".*"' ` (This one captures all strings within "" parenthesis), we can take grep a little further and trim this output so that we only get the gene names rather than the genbank formated line.

```bash
user@alpha2:~$ grep "gene=" Datasets/sarscov2.gb | grep -o '".*"'               
"ORF1ab"
"S"
"ORF3a"
"E"
"M"
"ORF6"
"ORF7a"
"ORF7b"
"ORF8"
"N"
"ORF10"
```

`grep`  is one of the most powerful commands for processing text files in linux, and is especially useful when used in conjunction with other commands.

**Task 1:**
Extract the lines containing the paper titles in the sarscov2.gb file

 
### 1.3 `head`, `tail` for File Previews

```bash
head -n 5 metadata.csv
tail -n 3 metadata.csv
```

### 1.4 `awk` for Field Filtering

```bash
awk -F"," '$3=="Philippines" {print $4}' covid.csv
```

**Task 3:** Extract the total cases per day for the UK.

### 1.5 `wc` for Line and Word Counts

```bash
wc -l covid_spreadsheet.csv
```

---

### 1.6 Piping and Redirecting (`|`,`>`,`<`) 

```bash
head -n 100 data.csv | tail -n 10 > last10.csv
echo "New line" >> notes.txt
```


We might want to perform a series of different commands on the command line. We can do this using the pipe (`|`) and redirection (`>`,`<`) operators when using different commands. A pipe takes the standard output of one command and sends it as the standard input of another command. Using our `head` and `tail` commands, we can extract lines 495-500 by piping the output of the `head` command into the input of the `tail` command like this:

```bash
user@alpha2:~$ head -n 500 owid-covid-data.csv | tail -n 5  
AFG,Asia,Afghanistan,2021-07-02,122156.0,1940.0,1509.143,5048.0,2970.086,47.169,36.693
AFG,Asia,Afghanistan,2021-07-03,123485.0,1329.0,1480.143,5107.0,3002.399,32.313,35.988
AFG,Asia,Afghanistan,2021-07-04,124748.0,1263.0,1504.0,5199.0,3033.108,30.708,36.568
AFG,Asia,Afghanistan,2021-07-05,125937.0,1189.0,1455.143,5283.0,3062.017,28.909,35.38
AFG,Asia,Afghanistan,2021-07-06,127464.0,1527.0,1472.286,5360.0,3099.144,37.127,35.797
```

Lets say we want to save the output of this into a file. we can do this using a redirect (`>`) like so:

```bash
user@alpha2:~$ head -n 500 owid-covid-data.csv | tail -n 5  > 5_rows.txt
```
The `>` takes the output that would normally be printed to the terminal and redirects it into a file called "5_rows.txt". If this file does not exist, the file is created and filled with the output. If the file already exists, the file will be wiped blank and filled with the contents. If we want to keep what is already in the file, we can use the `>>` operator to append to the file.

```bash
user@alpha2:~$ head -n 505 owid-covid-data.csv | tail -n 5  >> 5_rows.txt
```

To check whether we have appended the rows to the file, we can use `cat` to display the file contents:

```bash
user@alpha2:~$ cat 5_rows.txt 
AFG,Asia,Afghanistan,2021-07-02,122156.0,1940.0,1509.143,5048.0,2970.086,47.169,36.693
AFG,Asia,Afghanistan,2021-07-03,123485.0,1329.0,1480.143,5107.0,3002.399,32.313,35.988
...
AFG,Asia,Afghanistan,2021-07-10,132777.0,1191.0,1327.429,5638.0,3228.324,28.958,32.275
AFG,Asia,Afghanistan,2021-07-11,133578.0,801.0,1261.429,5724.0,3247.799,19.475,30.67
```

----

**Task 4:**
Use `head` to retrieve the first 10 lines of the owid-covid-data.csv file use the redirect operator to make a new file called 10_owid.csv.

**Task 5:**
Retrieve the first 100 lines of the owid-covid-data.csv and pipe the output to retrieve the last 10 lines, before redirecting this to a new file.

----

### 1.7 Ordering text in files `sort`

The `sort` command allows for files to be sorted, and a number of flags can be used to specify how the sorting should be done. 2 useful flags when sorting formatted data like csv or tsv files are the `-t` flag which indicates the character that separates the columns and the `-k` flag which allows for column selection.

```bash
user@alpha2:~$ sort 5_rows.txt -t "," -k 4   
AFG,Asia,Afghanistan,2021-07-11,133578.0,801.0,1261.429,5724.0,3247.799,19.475,30.67
AFG,Asia,Afghanistan,2021-07-10,132777.0,1191.0,1327.429,5638.0,3228.324,28.958,32.275
AFG,Asia,Afghanistan,2021-07-09,131586.0,1473.0,1347.143,5561.0,3199.366,35.814,32.754
AFG,Asia,Afghanistan,2021-07-08,130113.0,1092.0,1413.857,5477.0,3163.552,26.551,34.376
AFG,Asia,Afghanistan,2021-07-05,125937.0,1189.0,1455.143,5283.0,3062.017,28.909,35.38
AFG,Asia,Afghanistan,2021-07-06,127464.0,1527.0,1472.286,5360.0,3099.144,37.127,35.797
AFG,Asia,Afghanistan,2021-07-03,123485.0,1329.0,1480.143,5107.0,3002.399,32.313,35.988
AFG,Asia,Afghanistan,2021-07-07,129021.0,1557.0,1480.286,5415.0,3137.001,37.857,35.991
AFG,Asia,Afghanistan,2021-07-04,124748.0,1263.0,1504.0,5199.0,3033.108,30.708,36.568
AFG,Asia,Afghanistan,2021-07-02,122156.0,1940.0,1509.143,5048.0,2970.086,47.169,36.693
```
By specifying the separator as a `,` and setting `-k 4`, we have sorted the file according to the 4 column of the csv file. By default the order is ascending, but the `-r` flag can be used to invert this behaviour to descending.

`sort` can also be used with the `-u` flag to make the sorted output unique (i.e it is similar to sorting a file and then piping the output to `uniq`)

----

**Task 6:**
Use `head` to retrieve the first 10 lines of the owid-covid-data.csv file and then sort the output on the 4th column.

----


### 1.8 Selecting column with `cut`
The `cut` command allows for the selection of sections of files (such as a column, byte, or field). Similar to `sort`, `cut` takes a delimiter/separator flag (`-d`) and a flag to specify which type of `cut` operation you want to use (`-c` for characters, `-f` for fields/named columns, and `-b` for bytes). 

```bash
user@alpha2:~$ cut -d "," -f 1,5 5_rows.txt
AFG,122156.0
AFG,123485.0
AFG,124748.0
AFG,125937.0
AFG,127464.0
AFG,129021.0
AFG,130113.0
AFG,131586.0
AFG,132777.0
AFG,133578.0
```
The `-f` flag takes lists of columns using `,`, or `-` so multiple columns can be selected. 

----

### Task 15
Use `head` to retrieve the first 10 lines of the owid-covid-data.csv file, then retrieve the 1st column and display it.

----

### 1.9 Filtering duplicates with `uniq` (Unique)
`uniq` allows for filtering of duplicate parts of a file (but only where the lines follow each other). At its most basic, `uniq` with used with no flags will output only unique lines of a sorted file. We can see this in action by selecting the first column

```bash
user@alpha2:~$ cut -d "," -f 2 owid-covid-data.csv| sort | uniq

Africa
Asia
Europe
North America
Oceania
South America
continent
```
By sorting the continent column, duplicate continent labels can be filtered by `uniq` since they will appear after each other. Try the command again without the `sort` to see how this behaviour changes.

----

### Task 16
Use `head` to retrieve the first 1000 lines of the owid-covid-data.csv file (excluding the header), retrieve the 1st column and filter for unique entries.

----


### 1.2 `cut`, `sort`, and `uniq` for Field and Value Extraction

```bash
cut -d"," -f2 metadata.csv | sort | uniq
```

**Task 2:** Count how many unique countries are in a COVID-19 dataset.



### Task 18
Extract the lines from the owid-covid-data.csv file that are from the Philippines, sort by the number of new cases and print the top row.

----

## 1.13: `sed` (Stream Editor)
`sed` is a command that allows for editing of a text stream. It allows for printing, deleting and replacing of regular expressions, words or characters in text streams like files. `sed` takes a string that specifies how the incoming text should be processed. For printing the `p` character can be used with a number (which indicates a line number) or a range (indicates a line range). By default the entire contents of the input stream will be printed, but this can be suppressed using the `-n` flag.

```bash
user@alpha2:~$ sed -n "1p" owid-covid-data.csv 
iso_code,continent,location,date,total_cases,new_cases,new_cases_smoothed,total_deaths,total_cases_per_million,new_cases_per_million,new_cases_smoothed_per_millioncomplete 

user@alpha2:~$ sed -n "1,5p" owid-covid-data.csv 
iso_code,continent,location,date,total_cases,new_cases,new_cases_smoothed,total_deaths,total_cases_per_million,new_cases_per_million,new_cases_smoothed_per_million
AFG,Asia,Afghanistan,2020-02-24,5.0,5.0,,,0.122,0.122,
AFG,Asia,Afghanistan,2020-02-25,5.0,0.0,,,0.122,0.0,
AFG,Asia,Afghanistan,2020-02-26,5.0,0.0,,,0.122,0.0,
AFG,Asia,Afghanistan,2020-02-27,5.0,0.0,,,0.122,0.0,
```

The `d` character can be used to delete lines in a similar manner. Here we delete the first line of the file before piping it to head to show that the firt line is no longer the header. `-n` is not needed here since we are wanting everyting that has not been deleted.

```bash
user@alpha2:~$ sed "1d" owid-covid-data.csv | head -n 1
```
Important to note as well, `sed` does not be default change the contents of the original file, unless the `-i` flag is used.

The `s` character is used to indicate a string substitution, with slashes used to indicate the search term followed by the replacement term. 

```bash
user@alpha2:~$ head -n 1 owid-covid-data.csv | sed -n 's/location/Loc/p'    
iso_code,continent,Loc,date,total_cases,new_cases,new_cases_smoothed,total_deaths,total_cases_per_million,new_cases_per_million,new_cases_smoothed_per_million
```

Numbers can be used after the last `/` to indicate what instance of the string should be changed (for example if you only want to substitute the second occurrence of a word). The `g` character can also be place here to indicate that the change should be made globally (be default, `sed` only changes the first instance). Regular expressions can be used with `sed` as well to capture more complex patterns to substitute.


----
 
### Task 19
Use `sed` to get lines 500-600 from the owid-covid-data.csv file and save them to a file.

 
### Task 20
Get the lines from the owid-covid-data.csv file that have GBR in them, substitute UK for United Kingdom, then save the output to another file.

----


## 1.14: `awk` 
`awk` is a command that takes a input file/stream, splits the input lines into fields, matches input lines to searchable patters (similar to `grep` and `sed`) and allows for operations to be carried out on them. `awk` takes a patter which specifies what operations should be carried out. The most basic `awk` pattern is just to print the contents of the input stream or file:


```bash
user@alpha2:~$ head owid-covid-data.csv | awk "{print}" 
iso_code,continent,location,date,total_cases,new_cases,new_cases_smoothed,total_deaths,total_cases_per_million,new_cases_per_million,new_cases_smoothed_per_million
AFG,Asia,Afghanistan,2020-02-24,5.0,5.0,,,0.122,0.122,
AFG,Asia,Afghanistan,2020-02-25,5.0,0.0,,,0.122,0.0,
AFG,Asia,Afghanistan,2020-02-26,5.0,0.0,,,0.122,0.0,
AFG,Asia,Afghanistan,2020-02-27,5.0,0.0,,,0.122,0.0,
AFG,Asia,Afghanistan,2020-02-28,5.0,0.0,,,0.122,0.0,
AFG,Asia,Afghanistan,2020-02-29,5.0,0.0,0.714,,0.122,0.0,0.017
AFG,Asia,Afghanistan,2020-03-01,5.0,0.0,0.714,,0.122,0.0,0.017
AFG,Asia,Afghanistan,2020-03-02,5.0,0.0,0.0,,0.122,0.0,0.0
AFG,Asia,Afghanistan,2020-03-03,5.0,0.0,0.0,,0.122,0.0,0.0
```
Here, we add a pattern to search for (GBR) which allows for lines with this pattern to be printed:

```bash
user@alpha2:~$ awk "/GBR/ {print}" owid-covid-data.csv  
GBR,Europe,United Kingdom,2020-01-30,,,,1.0,,,
GBR,Europe,United Kingdom,2020-01-31,2.0,2.0,,1.0,0.03,0.03,
...
GBR,Europe,United Kingdom,2020-02-04,8.0,0.0,,2.0,0.119,0.0,
```

By default `awk` splits fields by whitespace characters, but this can be changed by using the `-F` flag followed by the split character:


```bash
user@alpha2:~$ awk -F "," '/GBR/ {print $1}' owid-covid-data.csv 
GBR
GBR
...
GBR
GBR
```

`awk` is also flexible in that if/else conditions and loops can be used within the `awk` command:


```bash
user@alpha2:~$ awk -F "," '/GBR/ {if ($4=="2020-02-04") print}' owid-covid-data.csv 
GBR,Europe,United Kingdom,2020-02-04,8.0,0.0,,2.0,0.119,0.0,

```

----
 
### Task 21
Use `awk` to get lines that contain "Philippines", and display the Date column.

----

## 1.15: Links

Links are an alternative to copying a file. It allows for a user to easily access files in their current working directory without having to duplicate that file in the new location or give very long file paths to access the file. We can make a link using the `ln` command. 

```bash
user@alpha2:~$ ln -s [Source_File_Path] [Link_Path]
```
The -s flag is used to specify that the link is a softlink, and works similar to a hyperlink or alias where the new location is linked to a location elsewhere.
If we are in our home directory, we can make a link to the Datasets/owid-covid-data.csv file and call the link covid_spreadsheet.csv as follows:

```bash
user@alpha2:~$ ln -s Datasets/owid-covid-data.csv covid_spreadsheet.csv
```
Looking in our home directory now, you can see a file called covid_spreadsheet.csv that links to our initial Datasets/owid-covid-data.csv file but is in the more convenient location of our home director. It should be noted that it is not necessary to change the name of the file for the link.

## 1.16: `wc` (word count)
The `wc` command allows for counting words in a file. It can also be used to count characters and lines by specifying the right flag.

```bash
#Word count for covid_spreadsheet.csv
user@alpha2:~$ wc covid_spreadsheet.csv
#Line count for covid_spreadsheet.csv
user@alpha2:~$ wc -l covid_spreadsheet.csv
#Character count for covid_spreadsheet.csv
user@alpha2:~$ wc -c covid_spreadsheet.csv
```

----

## 2. Bash shell scripts

### 2.1.What is a Shell Script?

* A **shell script** is a text file containing a sequence of Unix/Linux commands.
* Scripts typically start with a **shebang** (`#!`) followed by the path to the shell (e.g., `#!/bin/bash`).
* If no shebang is included, the script is run using the current shell.
* Any command available in the user's `PATH` can be used (i.e., a list of directories the system checks).
* Commands are executed **sequentially** unless control structures (like `if`, `while`, or `for`) are used.



### 2.2 Why Write Scripts?

* Automate repetitive tasks (e.g., data filtering, renaming files).
* Ensure reproducibility.
* Simplify complex data workflows.
* Schedule jobs on high-performance clusters.



### 2.3 Text Editor `nano`

In linux, there are a number of text editors (`nano` and `vim` are two included examples) that allow the user to open files and type text on the command line. These operate similarly to traditional text editors you might use like Notepad, but often require the use of a number of key combinations(**macros**) to do common operations like save or even exit the editor. `nano` is an editor that is (relatively) easy to use. We can use `nano` to create new text files, or open existing ones as follows:


```bash
user@alpha2:~$ nano new_file.txt
```

Your terminal should now look as follows once you have typed "Here is some text I want to save":

```
GNU nano 4.8                                                                                                          

Here is some text I want to save.


                                                                                             [ New File ]
^G Get Help       ^O Write Out      ^W Where Is       ^K Cut Text       ^J Justify        ^C Cur Pos        M-U Undo          M-A Mark Text     M-] To Bracket    M-Q Previous      ^B Back           ^◀ Prev Word      ^A Home
^X Exit           ^R Read File      ^\ Replace        ^U Paste Text     ^T To Spell       ^_ Go To Line     M-E Redo          M-6 Copy Text     ^Q Where Was      M-W Next          ^F Forward        ^▶ Next Word      ^E End
```

`nano` displays the commonly used macros at the bottom of the interface. The user can otherwise type the contents of the file as they wish and save once they are done with Ctrl+O followed by Enter and  Ctrl+X to Exit.


**Task:** Open a text file with `nano`, add some text, save the file and exit `nano`.



### 2.4 Your First Script

Open `nano` on the command line and copy or write the following three lines of code.

```bash
#!/bin/bash
# This is our first script
echo "Hello World"
```

The first line of code is the shebang, so the script know where to find the command line interpreter.
The second line with the # is just a comment. It is good practice to comment your code so that
you and others that look at it, can understand what it is meant to do.
The third line is asking for the words "Hello World" to be printed.

**Task:** Save this in a file called `hello.sh`, then run it:

```bash
chmod +x hello.sh
./hello.sh
```

The `chmod` command is making your bash script executable. Then you can run the script with `./hello.sh`

### 2.5 Control Flow in Bash


#### 2.5.1 Hardcoding a string into a variable

```bash
#!/bin/bash
filename="sars2.fasta"
echo $filename
```

#### 2.5.2 Accepting Arguments

Open `nano` and save the folliwng lines as `script.sh`

```bash
#!/bin/bash
echo $1
echo $2
```

Make the script executable and  and run with:

```bash
./script.sh file1.txt file2.txt
```

**Task:** Modify your `hello.sh` so that you accept one argument (it could be your name) from the command line and prints it out. 


#### 2.5.3 Storing Command Outputs

```bash
#!/bin/bash
csv_file=$(cat owid-covid-data.csv)
echo "$csv_file"
```

**Task x:** Write a script that takes the genbank file `sarscov2.gb` as input and counts how many unique gene names the file has. Print out the name of the file and the number of unique gene names.