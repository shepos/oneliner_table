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
