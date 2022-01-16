# bash powershell table

|bash|powershell|remarks|
|---|---|---|
|`<cmd> --help`|`man <cmd>`, `help <cmd>`|Get-Help|
|`echo abcba \| sed s/b/z/g`, `echo abcba \| tr 'b' 'z'` | `"abcba" -replace "b", "z"` |

# data-processing

## generate

|bash|powershell|remarks|
|---|---|---|
|`seq 1 10`, `{1..10}`| `1..10`|
|`seq -f %03g 10`|`1..10 \| % { '{0:d3}' -f $_ }`|

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
|`cat data.txt \| wc -l` | `(cat data.txt).Length`|
|`cat data.txt \| grep -E "^a"` | `cat data.txt \| ? { $_ -match "^a" }`|
|`cat data.txt \| head -n2`|||

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

# file


|bash|powershell|remarks|
|---|---|---|
|`touch a.txt` | `New-Item -Type File a.txt`||
|`rm -rf dir`|`if { Test-Path dir } { rm -r -fo dir}`|
|`mkdir -p sub/dir`|``mkdir -ea 0 sub/dir`|
|`grep "word" -r .`||
|`find . -iname \*.txt`||
|`find . -iname \*.xlsm -exec du -bh {} \;`||
