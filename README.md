# bash powershell table

# helper command

|bash|powershell|remarks|
|---|---|---|
|`<cmd> --help`|`man <cmd>`, `help <cmd>`, `<cmd> -?`||
|`which make`|`gcm make \| ft definition`||

# data-processing

## generate

|bash|powershell|remarks|
|---|---|---|
|`seq 1 10`, `{1..10}`| `1..10`|
|`seq -f %03g 10`|`1..10 \| % { '{0:d3}' -f $_ }`||

## processing string

|bash|powershell|remarks|
|---|---|---|
|`echo abcba \| sed s/b/z/g`, `echo abcba \| tr 'b' 'z'` | `"abcba" -replace "b", "z"` |

## parse string

+ example `data.txt` is below:

```
abc,defg,hij
```

|bash|powershell|remarks|
|---|---|---|
|`cat data.txt \| cut -f 2 -d ','`|`cat data.txt \| %{($_ -split ",")[1]}`| or ` % { $_.Split(",")[1] }`|
|`cat ddd.txt \| xargs -d, -n1 echo > out.txt`|


## row * 1

+ example `data.txt` is below:

```txt
abc
defg
hij
```

|bash|powershell|remarks|
|---|---|---|
|`cat data.txt \| wc -l` | `(cat data.txt).Length`, `cat data.txt \| measure -Line`|
|`cat data.txt \| grep -E "^a"` | `cat data.txt \| ? { $_ -cmatch "^a" }`|use `-match` If insensitive|
|`cat data.txt \| head -n2`|`cat data.txt \| select -First 2`||

## row * col

+ example `data.txt` is below:

```txt
Alice 22 Japan
Bob 34 France
Chen 19 China
Dick 9 Australia
```

|bash|powershell|remarks|
|---|---|---|
|`cat data.txt \| cut -f 1,3 -d " "`|`cat data.txt \| cfs -d " " \| ft P1,P3`||
|`cat data.txt \| sort -k2 -n`|`cat data.txt \| cfs -d " " \| sort -p "P2"`|`-p`=`-Property`|

# file


|bash|powershell|remarks|
|---|---|---|
|`touch a.txt` | `New-Item -Type File a.txt`||
|`rm -rf dir`|`if { Test-Path dir } { rm -r -fo dir}`|`-fo`:`-force`|
|`mkdir -p sub/dir`|`mkdir -ea 0 sub/dir`|[link](https://stackoverflow.com/a/47357220)
|`grep "word" -r .`|`ls -r . \| Select-String -Pattern "word"`|
|`grep "word" -l -r .`|`ls -r . \| Select-String -Pattern "word" \| Select-Object -Unique Path`|[link](https://superuser.com/a/742120)|
|`find . -iname *.txt`|`ls -r . \| ? { $_ -match ".*.txt" }`|
|`find . -type d dirA`|||
|`find . -iname *.txt -exec du -bh {} \;`|||

# system

|bash|powershell|remarks|
|---|---|---|
|`ps aux`|`ps`||
|`kill -9 <process>`|`kill <process>`||

# configs

|bash|powershell|remarks|
|---|---|---|
|`~/.bashrc`|`$profile`|
|`$BASH --version`|`$PSVersionTable`|
