## Sed
##### Remove the 1st line
```bash
sed 1d filename
```

##### Remove the first 100 lines (remove line 1-100)
```bash
sed 1,100d filename
```

##### Remove lines with string (e.g. 'bbo')
```bash
sed "/bbo/d" filename
# case insensitive:
sed "/bbo/Id" filename
```

##### Remove lines whose nth character not equal to a value (e.g. 5th character not equal to 2)
```bash
sed -E '/^.{5}[^2]/d'
#aaaa2aaa (you can stay)
#aaaa1aaa (delete!)
```

##### Edit infile (edit and save to file), (e.g. deleting the lines with 'bbo' and save to file)
```bash
sed -i "/bbo/d" filename
```

##### When using variable (e.g. $i), use double quotes " "
```bash
# e.g. add >$i to the first line (to make a bioinformatics FASTA file)
sed "1i >$i"
# notice the double quotes! in other examples, you can use a single quote, but here, no way!
# '1i' means insert to first line
```

##### Using environment variable and end-of-line pattern at the same time.
```bash
# Use backslash for end-of-line $ pattern, and double quotes for expressing the variable
sed -e "\$s/\$/\n+--$3-----+/"
```

##### Delete/remove empty lines
```bash
sed '/^\s*$/d'

# or

sed '/^$/d'
```
##### Delete/remove last line
```bash
sed '$d'
```

##### Delete/remove last character from end of file
```bash
sed -i '$ s/.$//' filename
```

##### Add string to beginning of file (e.g. "\[")
```bash
sed -i '1s/^/[/' file
```

##### Add string at certain line number (e.g. add 'something' to line 1 and line 3)
```bash
sed -e '1isomething' -e '3isomething'
```

##### Add string to end of file (e.g. "]")
```bash
sed '$s/$/]/' filename
```
##### Add newline to the end
```bash
sed '$a\'
```

##### Add string to beginning of every line (e.g. 'bbo')
```bash
sed -e 's/^/bbo/' file
```

##### Add string to end of each line (e.g. "}")
```bash
sed -e 's/$/\}\]/' filename
```

##### Add \n every nth character (e.g. every 4th character)
```bash
sed 's/.\{4\}/&\n/g'
```

##### Concatenate/combine/join files with a separator and next line (e.g separate by ",")
```bash
sed -s '$a,' *.json > all.json
```

##### Substitution (e.g. replace A by B)
```bash
sed 's/A/B/g' filename
```

##### Substitution with wildcard (e.g. replace a line start with aaa= by aaa=/my/new/path)
```bash
sed "s/aaa=.*/aaa=\/my\/new\/path/g"
```

##### Select lines start with string (e.g. 'bbo')
```bash
sed -n '/^@S/p'
```
##### Delete lines with string (e.g. 'bbo')
```bash
sed '/bbo/d' filename
```

##### Print/get/trim a range of line (e.g. line 500-5000)
```bash
sed -n 500,5000p filename
```

##### Print every nth lines
```bash
sed -n '0~3p' filename

# catch 0: start; 3: step
```

##### Print every odd # lines
```bash
sed -n '1~2p'
```
##### Print every third line including the first line
```bash
sed -n '1p;0~3p'
```
##### Remove leading whitespace and tabs
```bash
sed -e 's/^[ \t]*//'
# Notice a whitespace before '\t'!!
```

##### Remove only leading whitespace
```bash
sed 's/ *//'

# notice a whitespace before '*'!!
```

##### Remove ending commas
```bash
sed 's/,$//g'
```

##### Add a column to the end
```bash
sed "s/$/\t$i/"
# $i is the valuable you want to add

# To add the filename to every last column of the file
for i in $(ls);do sed -i "s/$/\t$i/" $i;done
```

##### Add extension of filename to last column
```bash
for i in T000086_1.02.n T000086_1.02.p;do sed "s/$/\t${i/*./}/" $i;done >T000086_1.02.np
```

##### Remove newline\ nextline
```bash
sed ':a;N;$!ba;s/\n//g'
```

##### Print a particular line (e.g. 123th line)
```bash
sed -n -e '123p'
```

##### Print a number of lines (e.g. line 10th to line 33 rd)
```bash
sed -n '10,33p' <filename
```

##### Change delimiter
```bash
sed 's=/=\\/=g'
```

##### Replace with wildcard (e.g A-1-e or A-2-e or A-3-e....)
```bash
sed 's/A-.*-e//g' filename
```

##### Remove last character of file
```bash
sed '$ s/.$//'
```

##### Insert character at specified position of file (e.g. AAAAAA --> AAA#AAA)
```bash
sed -r -e 's/^.{3}/&#/' file
```