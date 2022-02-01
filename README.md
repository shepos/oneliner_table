# bash powershell table

# helper command

|bash|powershell|remarks|
|---|---|---|
|`<cmd> --help`|`man <cmd>`, `help <cmd>`, `<cmd> -?`||
|`which make`|`(gcm make).Path`||
|`which bash`|`$pshome`|
|`pwd`|`pwd`|

# data-processing

## calculation

|bash|powershell|remarks|
|---|---|---|
|`echo $((1+2))`, `echo '1+2' \| bc`|`1+2`, `"1+2" \| iex`|
|`seq 1 10 \| awk '{a+=$1} END{print a;}`, `seq 1 10 \| paste -s -d "+" - \| bc` | `(1..10 \| measure -sum).Sum`|

## generate

|bash|powershell|remarks|
|---|---|---|
|`seq 1 10`, `{1..10}`| `1..10`|
|`seq -f %03g 10`, `echo {001..010}` |`1..10 \| % { '{0:d3}' -f $_ }`|or `% $_.ToString('000')`|
|`yes 1 \| head -n10`| `@(1) * 10`, `,1 * 10`|[link](https://stackoverflow.com/questions/226596/powershell-array-initialization)|

## processing string

|bash|powershell|remarks|
|---|---|---|
|`echo abcba \| sed s/b/z/g`, `echo abcba \| tr 'b' 'z'` | `"abcba" -replace "b", "z"` |
|`cat utf8.txt`, `nkf --ic=UTF-8 utf8.txt`|`cat utf8.txt -enc utf8`|`apt-get install nkf`|
|`sed -e -i '/^$/d' data.txt`|`cat data.txt \| ? {$_ -ne ""}`|

## parse string

+ example `data.txt` is below:

```
abc,defg,hij
```

|bash|powershell|remarks|
|---|---|---|
|`cat data.txt \| tr -d ','`|`cat data.txt \| %{ $_ -replace ",", ""}`, `(cat data.txt).Replace(",","")`|
|`cat data.txt \| cut -f 2 -d ','`|`cat data.txt \| %{($_ -split ",")[1]}`| or ` % { $_.Split(",")[1] }`|
|`cat data.txt \| xargs -d, -n1 echo > out.txt`|`cat data.txt \| %{$_ -split ','}`|


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
|`cat data.txt \| head -n2`|`cat data.txt -head 2`, `cat data.txt \| select -First 2`||
|`cat data.txt \| paste -s -d "+" -` | `(cat data.txt) -join "+"`, `$OFS="+"; [string](0..10); rv OFS`|

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
|`cat data.txt \| sort -k2 -n \| cut -f1 -d " "`|`cat ./data.txt \| %{,$_.split(" ")} \| sort -p {[int]$_[1]} \| %{$_[0]}`|
||`cat data.txt \| cfs -d " " \| % {[int]$_.P2} \| measure -ave`|

# file


|bash|powershell|remarks|
|---|---|---|
|`pwd`|`Convert-Path .`, `$pwd.Path`|
|`touch a.txt` | `New-Item -Type File a.txt`||
|`rm -rf dir`|`if { Test-Path dir } { rm -r -fo dir}`|`-fo`:`-force`|
|`mkdir -p sub/dir`|`mkdir -ea 0 sub/dir`|[link](https://stackoverflow.com/a/47357220)
|`grep "word" -r .`|`ls -r . \| Select-String -Pattern "word"`|
|`grep "word" -l -r .`|`ls -r . \| Select-String -Pattern "word" \| Select-Object -Unique Path`|[link](https://superuser.com/a/742120)|
|`find . -iname *.txt`|`ls -r . \| ? { $_ -match ".*.txt" }`|
|`find . -type d dirA`|||
|`find . -iname *.txt -exec du -bh {} \;`|||

# scripting

Note) prepare shellscript not REPL.

|bash|poweshell|remarks|
|---|---|---|
|`cd $(dirname ${0}) && pwd`|`$MyInvocation.MyCommand.Path`|script path itself|
||||


# system

|bash|powershell|remarks|
|---|---|---|
|`ps aux`|`ps`||
|`pkill <process_name>`|`kill -n <process_name>`||
|`nproc`|`Get-CimInstance -Class Win32_Processor \| fl NumberOfCores`|

# configs

|bash|powershell|remarks|
|---|---|---|
|`~/.bashrc`|`$profile`|
|`$BASH --version`|`$PSVersionTable`|
|`echo 'export PATH=/path/to/dir:$PATH' >> .profile`|`$ENV:Path="C:/path/to/dir;"+$ENV:Path`|
|`printenv`, `env`|`ls env:`|

# misc

## links

+ https://yanor.net/wiki/?PowerShell
