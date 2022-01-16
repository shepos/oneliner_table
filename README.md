# bash powershell table

|bash|powershell|remarks|
|---|---|---|
|`<cmd> --help`|`man <cmd>`, `help <cmd>`|Get-Help|
|`echo abcba \| sed s/b/z/g`, `echo abcba \| tr 'b' 'z'` | `"abcba" -replace "b", "z"` |
|`touch a.txt` | `New-Item -Type File a.txt`||

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

## row * col

+ example `data.txt` is below:

```txt
Alice 22 Japan
Bob 34 France
Carol 9 Australia
```
