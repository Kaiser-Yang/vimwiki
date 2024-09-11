# Wildcards in Linux
这里的 `wildcards` 指的是能够被 `shell` 扩展的符号。如果在执行的命令中出现这些符号，`shell` 会对这些
进行解析，解析完成后再传入需要执行的命令。也就是说只要 `shell` 支持，这些 `wildcards` 是可以在任意
命令中使用的。

常见的 `wildcards` 有以下几种：
* `*`：匹配任意数量( 包含 `0` 个 )的字符
* `?`：匹配单个任意字符
* `[]`：匹配出现在中括号中的某个符号，在中括号中可以使用 `-` 来表示范围，也可以使用 `!` 来表示取反，
例如 `[!0-9]` 表示匹配不是数字的字符，`[!ab]` 表示不是 `a` 也不是 `b` 的字符

上面的通配符并不是所有的 `shell` 都支持的，但是在 `bash` 中是支持的。例如在 `fish shell` 中，只有
`*`、`**` 以及 `?` 是支持的，其中 `*` 和 `?` 不会匹配 `/`，而 `**` 会匹配 `/` ，也就是说 `**` 是
递归匹配。

**注意**：在正则表达式的语法中，`*` 表示匹配前面的字符 0 次或者多次，`?` 表示匹配前面的字符 0 次或者 1 次，
`[]` 表示匹配中括号中的任意一个字符，`-` 表示范围，`^` 表示取反。

# 常用命令 `Cheat Sheet`
## `grep`

| 选项                        | 说明 |
| ---                         | --- |
| `-r`                        | 递归搜索目录 |
| `--include "*.py"`          | 搜索指定文件 |
| `--exclude "test*"`         | 排除指定文件 |
| `--exclude-dir "test*"`     | 排除指定目录 |
| `--color=auto,always,never` | 何时进行高亮 |
| `-n`                        | 显示行号。这里的行号指的是文件中的行号 |
| `-l`                        | 只显示文件名。当对多个文件中的内容进行匹配时，使用 `-l` 会在匹配成功时只显示文件名，而不是文件中具体的位置 |
| `-L`                        | 只显示不含有任何匹配的文件名 |
| `-i`                        | 忽略大小写 |
| `-v`                        | 反向匹配 |
| `-c`                        | 显示匹配次数。使用 `-v` 时，显示反向匹配的数量 |
| `-w`                        | 只匹配完整单词。完整单词指的是前后均不是字母、数字或者下划线的部分。 |
| `-x`                        | 只匹配整行 |
| `-A 2`                      | 显示匹配行的后两行 |
| `-B 2`                      | 显示匹配行的前两行 |
| `-C 2`                      | 显示匹配行的前两行和后两行 |
| `-E`                        | 使用扩展正则表达式 |
| `-F`                        | 使用固定字符串。这个选项会将模式中的特殊字符当作普通字符对待 |

**注意**：`grep` 的正则表达式是基于 `POSIX` 的基本正则表达式，而 `egrep` 和 `grep -E` 是基于 `POSIX`
的扩展正则表达式。`grep` 和 `egrep` 的区别在于 `egrep` 默认使用扩展正则表达式，而 `grep` 默认使用基本
正则表达式。

题外话：`grep` 的名字来源于 `g/re/p`，其中 `g` 表示 `global`，`re` 表示 `regular expression`，`p`
表示 `print`，直译即全局正则表达式打印。

## `sort`

| 选项                     | 说明 |
| ---                      | --- |
| `-r`                     | 逆序排序 |
| `-n`                     | 按照数字排序 |
| `-k`                     | 按照某一列排序 |
| `-u`                     | 去重 |
| `-t`                     | 指定分隔符 |
| `-f`                     | 忽略大小写。默认情况下，大写会在所有小写的前面，而使用 `-f` 可以让大小写混合在一起排序 |
| `-h`                     | 人类可读的排序。例如 `1K` 会被排序到 `512` 的前面 |
| `-M`                     | 按照月份排序 |
| `--files0-form=-`        | 从标准输入读取以 `NUL` 作为文件名分隔符的多个文件内容 |
| `--files0-form=filename` | 从某个文件中读取 `NUL` 作为文件名分隔符的多个文件内容 |
| `-c`                     | 检查文件是否已经排序 |

**注意**：`-k` 可以指定多个列，例如 `-k2,2 -k1,1` 表示先按照第二列排序，然后再按照第一列排序。`-k` 也
按照某列的部分进行排序，例如 `-k2.2,2.3` 表示按照第二列的第二个字符到第三个字符进行排序。`-k` 还可以
指定列的类型，例如 `-k2n,2` 表示按照第二列的数字进行排序。`-r` 选项也可以指定到 `-k` 选项中，例如
`-k2r,2` 表示按照第二列逆序排序。

## `awk`

| 选项 | 说明 |
| ---  | --- |
| `-F` | 指定输入的分隔符。 |
| `-f` | 指定 `awk` 脚本文件。 |

### 打印列
* `$0`: 整行
* `$1`: 第一列
* `...`
* `$NF`: 最后一列

例如可以使用 `awk '{print $1,$2,$NF}'` 打印出每行的第一列、第二列和最后一列。

### 分隔符
可以通过 `OFS=` (Output Field Separator) 来指定输出的分隔符，例如 `awk '{print $1,$2,$NF}' OFS=","`
表示使用 `,` 作为分隔符。也可以在 `BEGIN` 模式串中指定 `OFS`，例如 `awk 'BEGIN {OFS=","} {print $1,$2,$NF}'`。

除了 `OFS` 以外，还有 `FS` 用于制定输入文件的分隔符。

### 预定义变量
除了之前提到的 `OFS`, `FS` 之外，还有一些预定义变量：
* `NR`: 当前行的行号
* `NF`: 当前行的列数
* `RS`：记录分隔符，默认是换行符，也就是没一行作为一条记录
* `ORS`：输出的记录分隔符，默认是换行符
* `FILENAME`：当前文件的文件名
* `FNR`：当前文件的行号，当时用多个文件的时候，`NR` 记录的是当前的总行号，而 `FNR` 记录的是当前文件的行号

我们可以使用 `awk 'NR > 1'` 从第二行开始打印，也可以使用 `awk 'NF > 0'` 打印列数大于 `0` 的行
(也就是移除空行)。

如果要打印第一行到第四行，我们可以通过 `awk 'NR == 1, NR == 4'` 来实现。其中的 `,` 表示范围，`NR == 1`
表示第一行，`NR == 4` 表示第四行。我们还可以使用 `awk 'NR > 1 && NR < 5'` 来实现。

### 模式
`BEGIN` 和 `END` 模式串：
* `BEGIN`: 在处理输入之前执行
* `END`: 在处理输入之后执行

例如可以使用 `awk 'BEGIN {print "Start"} {print $1,$2,$NF} END {print "End"}'` 来在处理输入之前和之后
打印出 `Start` 和 `End`。

在 `awk` 中可以使用模式来过滤行，例如 `awk '$1 > 10'` 表示只打印第一列大于 `10` 的行 (当不书写动作时，
默认动作是打印整行)。也可以使用搜索模式，例如 `awk '/pattern/'` 表示只打印包含 `pattern` 的行，搜索
模式支持正则表达式。

`,` 也可以和搜索模式一起使用，例如 `awk '/pattern1/,/pattern2/'` 表示打印找到的 `pattern1` 到
`pattern2` 的行。

`~` 可以用来表示是否与搜索模式匹配，例如 `awk '$1 ~ /pattern/'` 表示第一列是否包含 `pattern`。`!~` 可以
用来表示是否不匹配，例如 `awk '$1 !~ /pattern/'` 表示第一列是否不包含 `pattern`。

### `awk` 脚本
我们也可以书写 `awk` 脚本实现更加复杂的功能，例如下面的脚本可以实现统计文件中每个单词出现的次数：

```awk
#! /usr/bin/awk -f

BEGIN {
    # 设置输入和输出的分隔符
    FS=":"
    OFS=" "
    tot_count = 0
}
{
    for (i = 1; i <= NF; i++) {
        words[$i]++
        tot_count++
    }
}
END {
    print "Total words:", tot_count
    for (word in words) {
        print word, words[word]
    }
}
```

**注意**：由于在 `awk file` 的意思是对某个文件的内容进行处理，所以要用 `awk -f file` 来表示将文件作为
脚本执行。

### 内置函数
`awk` 中还有一些内置函数，例如 `length` 函数可以返回字符串的长度，`substr` 函数可以返回字符串的子串，
我们可以使用下面的命令计算最后一列 (从第二行起) 的数字和：
`awk 'NR > 1 { printf "%s",$NF"+" }' OFS="" | awk '{ print substr($0, 1, length($0) - 1) }' | bc`。

在 `awk` 中，`print` 会在输出的字符串后面自动添加换行符，而 `printf` 可以进行格式化输出，上面的例子中
我们通过 `printf` 来输出不带换行符的字符串。在 `awk` 中字符串是可以直接拼接的，例如 `printf "%s", $NF"+"`
表示将最后一列的值和 `+` 拼接在一起。

`printf` 的格式化方法与 `C/C++` 中的类似，这里不再进行详细介绍。

当然要实现同样的功能有更简单的命令：`awk 'NR > 1 { sum+=$NF } END { print sum }'` 或者
`awk 'NR > 1 { print sep $NF; sep="+" }' OFS="" ORS=""`。

**注意**：在 `awk` 中，下标是从 `1` 开始的，而不是从 `0` 开始的，且区间是闭区间。

这里再列出一些常用的内置函数：
* `tolower`：将字符串转换为小写
* `toupper`：将字符串转换为大写
* `split`：将字符串按照某个分隔符分割为数组，接收三个参数，第一个参数是要分割的字符串，第二个参数是
数组名，第三个参数是分隔符。在分隔符部分，我们可以传入搜索表达式，例如 `split($0, words, /:+/)` 表示
将当前行按照 `:` (或者多个连续的 `:`) 分割为数组。
* `gsub`：全局替换，接收三个参数，第一个参数是查找字符串，第二个参数是替换后的字符串，第三个参数要替换
的文本，例如 `gsub(/pattern/, "replace", $0)` 表示将当前行中的 `pattern` 替换为 `replace`。
* `system`：执行系统命令，例如 `system("ls")` 会执行 `ls` 命令并将结果输出到标准输出。

### 改变分隔符
前面介绍到 `FS` 和 `OFS` 可以指定输入和输出的分隔符。如果我们想要直接改变分隔符后输出，我们可能会写出
`awk '{print}' FS=':' OFS=' '` 这样的命令，试图将 `:` 改成空格。但是这样并不能正确工作，在 `awk` 中
只有当列被修改后 (或者在 `print` 中打印多个 `fields` 的时候) 才会使用新的输出分隔符，所以我们可以通过
`awk '($1=$1) || 1' FS=':' OFS=' '` 来实现 (这里省略了动作，因此会打印一整行)。

这里的 `($1=$1) || 1` 是为了保证能够成功输出原始的空行，因为空行在赋值后返回的是空字符串，而空字符串在
`awk` 中被当作 `false`，所以我们需要通过 `|| 1` 来保证输出。这里的括号是必须的，如果没有括号，
`$1=$1 || 1` 会被解释为 `$1=($1 || 1)`，这样会将 `$1` 赋值为 `1`，而不是保留原来的值。

题外话：`awk` 的名字来源于三个创始人的名字 `Alfred Aho`、`Peter Weinberger` 和 `Brian Kernighan` 的
首字母。

### `POSIX` 字符类
`awk` 中支持 `POSIX` 字符类：
* `[:alnum:]`：字母和数字
* `[:alpha:]`：字母
* `[:blank:]`：空格和制表符
* `[:cntrl:]`：控制字符
* `[:digit:]`：数字
* `[:graph:]`：可打印字符，不包括空格
* `[:lower:]`：小写字母
* `[:print:]`：可打印字符，包括空格
* `[:punct:]`：标点符号
* `[:space:]`：空白字符
* `[:upper:]`：大写字母
* `[:xdigit:]`：十六进制数字

例如，我们可以使用 `awk '/[[:digit:]]/'` 来匹配包含数字的行。当然也可以写成 `awk '/[0-9]/'`。

# 巨人的肩膀
* [10 Practical Examples Using Wildcards to Match Filenames in Linux](https://www.tecmint.com/use-wildcards-to-match-filenames-in-linux/)
* [fish shell wildcards](https://fishshell.com/docs/current/fish_for_bash_users.html#wildcards-globs)
* [Linux Tutorial - Cheat Sheet - grep](https://ryanstutorials.net/linuxtutorial/cheatsheetgrep.php)
* [Linux Handbook: sort Command Examples](https://linuxhandbook.com/sort-command/)
* [15+ Tips to PROPERLY sort files in Linux \[Cheat Sheet\]](https://www.golinuxcloud.com/linux-sort-files/#1_Sort_by_name)
* [Linux and Unix sort command tutorial with examples](https://shapeshed.com/unix-sort/)
* [Linux sort Command](https://www.baeldung.com/linux/sort-command)
* [How to Use the awk Command on Linux](https://www.howtogeek.com/562941/how-to-use-the-awk-command-on-linux/)
* [30+ awk examples for beginners / awk command tutorial in Linux/Unix](https://www.golinuxcloud.com/awk-examples-with-command-tutorial-unix-linux/)
* [8 Powerful Awk Built-in Variables – FS, OFS, RS, ORS, NR, NF, FILENAME, FNR](https://www.thegeekstuff.com/2010/01/8-powerful-awk-built-in-variables-fs-ofs-rs-ors-nr-nf-filename-fnr/)
* [Getting Started With AWK Command \[Beginner's Guide\]](https://linuxhandbook.com/awk-command-tutorial/)
