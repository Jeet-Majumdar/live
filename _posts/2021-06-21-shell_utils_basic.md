---
layout: post
title: Basic shell utilities
categories: [Tech] 
---

## awk

`awk` can deal with rows and columns to do complex numerical and text extraction or manipulations. It is a best choice if you have to do some compute operation on some part of a text. Many C functions can be used with it.

 `awk 'BEGIN{commands} pattern {commands} END {commands} file'`. `BEGIN` and `END` are optional. They are actions before process and after process, respectively.

**Major  variables**

- `NR`: number of current row
- `NF`: number of fields, default delimeter is space 
- `$0`: content of current row
- `$1`: the first column of the content 
- `OFS` is Output Field Separator

It works in 3 steps:

1. Begin with `commands` in `BEGIN{commands}`
2. search for texts in `file`  that matches the `pattern`, and execute commands in `pattern {commands}` on the matched text
3. End with `commands` in `END{commands}`

**Examples**

1. `awk '/Jeet/ {print $3}' file.csv`

   If the line contains Jeet, print the third field

2. `awk 'BEGIN {getline; print $0} {s+=$3} END {print s}' file.csv`

   Jump the first line; calculate the sum of column 3

3. `awk -F"," 'BEGIN{getline} min > $3 {min = $3; minline=$0} END{print minline}' file.csv`: 

   Calculate the max of column 3 of a ',' separated file; print this line



## sed

`sed` can print, delete and substitute text

`sed [options] commands [file-to-edit]`

The pattern of `commands` decides where and how the operations are to be performed.

`options`:

- p = print
- d = delete
- s = substitute
- ! = to negate the range

By default, sed will print or delete or substitute according to the commands given bellow and print the output with the changes. You can direct the output to a new file. But, if you want to do these changes in the original file itself, supply `-i` option with the sed commands.

**print**

sed will do `echo` for matched lines by default. `-n` will suppress this action.

- `sed -n '1p' filename`: Print the first line
- `sed -n '15,20p' filename`: Print 15-20 line
- `sed -n '15,+9p' filename`: Print 10 lines starting from line 15
- `sed -n '15~7p' filename`: Print from line 15 to the end, except line 7

**delete**

- `sed '3d' filename`: Delete the third line

- `sed '15,20d' filename`: Delete 15-20 line

- `sed /error/!d filename`: Delete line without error

- `sed /^$/d filename`: Delete blank line
- `sed /^abc/d filename`: Delete line starting with `abc`

**substitute**

- `sed 's/hello/This/g' filename`: `g`, Global
- `sed 's/hello/This/' filename`: Substitute only the first occurrance
- `sed 's/hello/This/2 filename`: Substitute the second occurrance
- `sed -n 's/hello/This/2p' filename`: Print the substituting lines
- `sed 's/hello/This/i filename`: `i`, case insensitive

- `sed -e 's/hello/This/' -e 's/dear/That/' filename`: Multiple sed


## cut

`cut` can help to fetch some text based  on column that can be used somewhere else

- `cut -d ',' -f1,6 filename`: Get the first and the sixth columns
- `cut -d ':' -f5-7 filename`: Get the fifth to the seventh columns with delimeter `:`
- `cut -d ',' -f2 --complement filename`: Get all columns other than the 2nd

## paste

`paste` command helps to merge files in parallel (default) or sequential  

- `paste animal.dat sound.dat > animal_sound.dat`: Joins the two files animal.dat and sound.dat side by side with a space  (default) separating them as delimiter
- `paste -d "|" animal.dat sound.dat > animal_sound.dat`  Joins the two files animal.dat and sound.dat side by side with a "|" separating them as delimiter

- `paste -s animal.dat sound.dat > animal_sound.dat`  Joins the two files animal.dat and sound.dat one after the other in two different rows
- `paste -s -d ":" animal.dat sound.dat > animal_sound.dat`  Joins the two files animal.dat and sound.dat one after the other in two different rows, as well as ":" as a delimiter between each elements of animal.dat and sound.dat