# bash, powershell, R table

## prerequisites

+ R .. tidyverse

# helper command

|bash|powershell|remarks|
|---|---|---|
|`<cmd> --help`|`man <cmd>`, `help <cmd>`, `<cmd> -?`||
||`alias \| ?{$_.Definition -match "Get-Content"}`|
||`alias \| group {$_.Definition} \| sort -p Name`|
||`Get-Command \| group Verb \| sort -p count -desc`|
|`which make`|`(gcm make).Path`||
|`which bash`|`$pshome`|
|`pwd`|`pwd`|

# data-processing

## calculation

|bash|powershell|R|remarks|
|---|---|---|--|
|`echo $((1+2))`, `echo '1+2' \| bc` | `1+2`, `"1+2" \| iex`| `1+2` |
|`seq 1 10 \| awk '{a+=$1} END{print a;}`, `seq 1 10 \| paste -s -d "+" - \| bc` | `(1..10 \| measure -sum).Sum`| `1:10 \|> sum()`, `eval(parse(text="1+2"))` |
||`1..100 \| %{$num=$_; ,@($_, (@(15, 5, 3, 1) \| %{$num % $_ -eq 0}).indexof($true))} \| %{@("fizzbuzz", "buzz", "fizz", $_[0])[$_[1]]}`| `1:100 \|> as_tibble() \|> mutate(v3 = if_else(value%%3==0,"fizz",NA), v5 = if_else(value%%5==0, "buzz",NA), v15 = str_c(v3, v5)) \|> mutate(res = coalesce(v15,v3,v5,as.character(value))) \|> pull(res)` |
|`echo $(seq 1 100 \| grep 3) $(seq 1 3 100) \| xargs -n1 \| sort -n \| uniq` | `1..100 \| ?{("{0}" -f $_).indexof("3") -ge 0 -or $_ % 3 -eq 0}`, `1..33 \| %{$_ * 3} \| ?{"3" -in [char[]][string]($_)}`| `1:100 \|> as_tibble() \|> mutate(chr = value \|> as.character()) \|> filter(value%%3==0 \| str_detect(chr,"3")) \|> pull(value)` |
||| `1:27 \|> reduce(\(tb,nxt) tb \|> filter(value == nth(value, nxt) \| (value %% nth(value, nxt) != 0)), .init=(2:10000 \|> as_tibble() \|> mutate(flag = TRUE))) \|> pull(value)` | Sieve of Eratosthenes |

## generate

|bash|powershell|R|remarks|
|---|---|---|---|
|`seq 1 10`, `{1..10}`| `1..10`| `cat(1:10)` |
|`seq -f %03g 10`, `echo {001..010}` |`1..10 \| % { '{0:d3}' -f $_ }`, `1..10 \| %{$_.ToString('000')}`| `1:10 \|> str_pad(width=3, pad="0")` |
|`yes 1 \| head -n10`| `@(1) * 10`, `,1 * 10`| `rep(1,10)`  | [link](https://stackoverflow.com/questions/226596/powershell-array-initialization)|
|`echo "hello" \| fold -s1 \| paste -s -d "," -`|`([char[]]"hello") -join "."`| `"hello" \|> str_split_1("") \|> str_flatten(collapse=".")` |
|`printf hello"%.s" {1..10}`|`"hello" * 10`| `rep("hello",10) \|> str_flatten()` |
|`yes 'echo $((RANDOM%100 + 1))' \| head -n10 \| bash`|`1..5 \| %{Get-Random -max 100}`| `sample.int(100,5,replace=TRUE)` |
||`'a'..'z' -join ""`(in PowerShell 7.2), `[char[]]([char]'a'..[char]'z') -join ""`| `letters \|> str_flatten()` |

## processing string

|bash|powershell|R|remarks|
|---|---|---|---|
|` echo "abcba" \| tr [:lower:] [:upper:]`, `TMP=abcba; echo ${TMP^^}`|`"abcba".ToUpper()`| `"abcba" \|> str_to_upper()` |
|`TMP="hello"; echo ${TMP:2:2}`|`-join "hello"[2..3]`| `"hello" \|> str_sub(3,4)` |
|`echo abcba \| sed s/b/z/g`, `echo abcba \| tr 'b' 'z'` | `"abcba" -replace "b", "z"` | `"abcba" \|> str_replace_all("b", "z")` |
|`cat utf8.txt`, `nkf --ic=UTF-8 utf8.txt`|`cat utf8.txt -enc utf8`| ? |`apt-get install nkf`|
|`sed -e -i '/^$/d' data.txt`|`cat data.txt \| ? {$_ -ne ""}`| `readLines("./data.txt") \|> discard(\(x) x=="") \|> cat(sep="\n")` |
|`echo "hello" \| rev`|`$s="hello"; -join $s[$s.length..0]`, `"hello" \| %{-join $_[$_.length..0]}`| `"hello" \|> str_split_1("") \|> rev() \|> str_flatten("")` | ps: `-join` is necessary|\
|`echo "2020/04/12" \| date "+%Y%m%d" -f -`|`"2020/04/12" \| date -f "yyyyMMdd"`| `"2020/04/12" \|> ymd() \|> format("%Y%m%d")` |
|`echo -n "hello" \| xxd -p`|`-join (("hello" \| Format-Hex).Bytes \| %{$_.ToString("x2"))`| ? |

## parse string

+ example `data.txt` is below:

```
abc,defg,hij
```

|bash|powershell|R|remarks|
|---|---|---|---|
|`cat data.txt \| wc -m`|`(cat data.txt).Length)`|
|`cat data.txt \| tr -d ','`|`-join (cat str1.txt).split(",")`, `cat data.txt \| %{ $_ -replace ",", ""}`, `(cat data.txt).Replace(",","")`, `${c:data.txt} -replace ",", ""`| `"abc,defg,hij" \|> str_split_1(",")` |ps: `c:...` is called probider|
|`cat data.txt \| cut -f 2 -d ','`|`cat data.txt \| %{($_ -split ",")[1]}`, `cat data.txt \| % { $_.Split(",")[1] }`| `"abc,defg,hij" \|> str_split_1(",") \|> _[2]` |
|`cat data.txt \| xargs -d, -n1 echo > out.txt`|`cat data.txt \| %{$_ -split ','}`|
||`[char[]][string](cat ./words2.txt) \| group \| sort -p Count -desc \| Select -first 5 Count, Name`| `"abc,defg,hij" \|> str_split_1("") \|> as_tibble() \|> summarize(n=n(), .by=value) \|> arrange(desc(n)) \|> slice(1:5)` |

## row * 1

+ example `data.txt` is below:

```txt
abc
defg
hij
```

|bash|powershell|R|remarks|
|---|---|---|---|
|`cat data.txt \| wc -l` | `(cat data.txt).Length`, `cat data.txt \| measure -Line`|
|`cat data.txt \| grep -E "^a"` | `cat data.txt \| ? { $_ -cmatch "^a" }`|  | use `-match` If insensitive|
|`cat data.txt \| head -n2`|`cat data.txt -head 2`, `cat data.txt \| select -First 2`||
|`cat data.txt \| paste -s -d "+" -` | `(cat data.txt) -join "+"`, `$OFS="+"; [string](0..10); rv OFS`|
|`cat data.txt \| shuf -n2`|`cat data.txt \| Get-Random -Count 2`| `read_lines(I("abc\ndefg\nhij")) \|> sample()` |
||`cat data.txt \| % {$i=0} {"Value:$_ Index:$i"; $i++}`|

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
|`cat data.txt \| cut -f 1,3 -d " "`|`cat data.txt \| cfs -d " " \| ft P1,P3`, `Import-csv ./data.txt -d " " -header name,age,country \| select name,country`||
|`cat data.txt \| sort -k2 -n`|`cat data.txt \| cfs -d " " \| sort -p "P2"`|`-p`=`-Property`|
|`cat data.txt \| sort -k2 -n \| cut -f1 -d " "`|`cat ./data.txt \| %{,$_.split(" ")} \| sort -p {[int]$_[1]} \| %{$_[0]}`|
|`cat data.txt \| cut -f2 -d ' ' \| awk '{x++;sum+=$1}END {printf("%.2f\n", sum/x)}'`|`cat data.txt \| cfs -d " " \| % {[int]$_.P2} \| measure -ave`|
||`(cat data.txt \| cfs -d "," \| %{$_.P1 * $_.P2} \| measure -sum).Sum`|

# file


|bash|powershell|remarks|
|---|---|---|
|`pwd`|`Convert-Path .`, `$pwd.Path`|
|`touch a.txt` | `New-Item -Type File a.txt`||
||`resolve-path ./a.txt`, `(ls ./a.txt).FullName`|
|`rm -rf dir`|`if { Test-Path dir } { rm -r -fo dir}`|`-fo`:`-force`|
|`mkdir -p sub/dir`|`mkdir -ea 0 sub/dir`|[link](https://stackoverflow.com/a/47357220)
|`grep "word" -r .`|`ls -r . \| Select-String -Pattern "word"`|
|`grep "word" -l -r .`|`ls -r . \| Select-String -Pattern "word" \| Select-Object -Unique Path`|[link](https://superuser.com/a/742120)|
|`find . -iname *.txt`|`ls -r . \| ? { $_ -match ".*.txt" }`|
|`find . -type d dirA`|||
|`find . -iname *.txt -exec du -bh {} \;`|||
|`cat data.txt \| md5sum`|`(Get-FileHash -a md5 data.txt).Hash`, ` get-filehash -a md5 -i ([IO.MemoryStream]::new([Text.Encoding]::utf8.GetBytes((cat -raw data.txt))))`|`-raw`: get as [string]|
|`echo -n "hello" \| md5sum`|`get-filehash -a md5 -i ([IO.MemoryStream]::new([Text.Encoding]::utf8.GetBytes(("hello"))))`|
|`import-csv ./test.csv \| gm -membertype NoteProperty \| Select Name|

# scripting

Note) prepare shellscript not REPL.

|bash|poweshell|remarks|
|---|---|---|
|`cd $(dirname ${0}) && pwd`|`$PSScriptRoot`|script path itself|
||||


# system

|bash|powershell|remarks|
|---|---|---|
|`ps aux`|`ps`||
|`pkill <process_name>`|`kill -n <process_name>`||
|`nproc`|`Get-CimInstance -Class Win32_Processor \| fl NumberOfCores`|
||`[System.Environment]::OSVersion`|
||`Get-CimInstance -ClassName Wind32_LogicalDisk \| Select *`|

# configs

|bash|powershell|remarks|
|---|---|---|
|`~/.bashrc`|`$profile`|
|`$BASH --version`|`$PSVersionTable`|
|`pwd`|`(pwd).path`|
|`echo 'export PATH=/path/to/dir:$PATH' >> .profile`|`$ENV:Path="C:/path/to/dir;"+$ENV:Path`|
|`printenv`, `env`|`ls env:`|

# misc

## links

+ https://yanor.net/wiki/?PowerShell
