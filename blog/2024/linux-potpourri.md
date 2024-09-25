# Wildcards in Linux
这里的 `wildcards` 指的是能够被 `shell` 扩展的符号。如果在执行的命令中出现这些符号，`shell` 会对这些
进行解析，解析完成后再传入需要执行的命令。也就是说只要 `shell` 支持，这些 `wildcards` 是可以在任意
命令中使用的。

常见的 `wildcards` 有以下几种：
* `*`：匹配任意数量( 包含 `0` 个 )的字符
* `**`：递归匹配任意数量( 包含 `0` 个 )的字符
* `?`：匹配单个任意字符
* `[]`：匹配出现在中括号中的某个符号，在中括号中可以使用 `-` 来表示范围，也可以使用 `!` 来表示取反，
例如 `[!0-9]` 表示匹配不是数字的字符，`[!ab]` 表示不是 `a` 也不是 `b` 的字符

上面的通配符并不是所有的 `shell` 都支持的，但是在 `bash` 中是支持的。例如在 `fish shell` 中，只有
`*`、`**` 以及 `?` 是支持的，其中 `*` 和 `?` 不会匹配 `/`，而 `**` 会匹配 `/` ，也就是说 `**` 是
递归匹配。

**注意**：在正则表达式的语法中，`*` 表示匹配前面的字符 0 次或者多次，`?` 表示匹配前面的字符 0 次或者 1 次，
`[]` 表示匹配中括号中的任意一个字符，`-` 表示范围，`^` 表示取反。

# 常用命令 `Cheat Sheet`
## `cat`

| 选项 | 说明 |
| ---  | --- |
| `-n` | 显示行号 |
| `-b` | 显示非空行的行号 |
| `-s` | 合并多个空行为一个空行 |
| `-v` | 使用 `^` 和 `M-` 显示不可打印字符 |
| `-E` | 在每行的结尾显示 `$` |
| `-T` | 将制表符显示为 `^I` |
| `-A` | 等价于 `-v -E -T` |
| `-e` | 等价于 `-v -E` |
| `-t` | 等价于 `-v -T` |
| `-`  | 从标准输入读取内容 |

使用 `cat` 可以直接创建带有内容的文件或者追加内容到文件中，例如 `cat > file` 会等待输入，当输入完成
后，使用 `^D` (EOF, end of file) 来结束输入。如果要追加内容到文件中，可以使用 `cat >> file`。

`-` 可以出现在多个文件的任意位置，例如 `cat file1 - file2` 表示将标准输入中读取到的内容放置在
`file1` 和 `file2` 的内容之间，而 `cat file1 - file2 - file3` 表示将标准输入中读取到的内容放置在
`file1` 和 `file2` 的内容之间，`file2` 和 `file3` 的内容之间，此时会要求输入两次，第一次输入完成后，
使用 `^D` 结束，然后输入第二次，再次使用 `^D` 结束，第一次输入的内容会放置在 `file1` 和 `file2` 的内容
之间，第二次输入的内容会放置在 `file2` 和 `file3` 的内容之间。

补充：`tac` 可以将文件内容逆序输出 (优先输出最后一行)，`tac` 的名字来源于 `cat` 的逆序。

题外话：`cat` 的名字来源于 `concatenate`，即连接的意思，其可以将多个文件的内容拼接在一起。

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
| `-o`                        | 只显示匹配的部分 |
| `-m`                        | 指定匹配的次数。例如 `-m 1` 表示当有多个匹配的时候只显示第一个匹配。 |
| `-f`                        | 从文件中读取模式。文件中每行一个模式 |
| `-e`                        | 指定模式。可以指定多个模式，例如 `grep -e pattern1 -e pattern2` |
| `-w`                        | 只匹配完整单词。完整单词指的是前后均不是字母、数字或者下划线的部分。 |
| `-x`                        | 只匹配整行 |
| `-A 2`                      | 显示匹配行的后两行 |
| `-B 2`                      | 显示匹配行的前两行 |
| `-C 2`                      | 显示匹配行的前两行和后两行 |
| `-E`                        | 使用扩展正则表达式 |
| `-F`                        | 使用固定字符串。这个选项会将模式中的特殊字符当作普通字符对待 |
| `-H`                        | 显示文件名。默认情况下，当只有一个文件时，`grep` 不会显示文件名，而使用 `-H` 可以强制显示文件名 |
| `-h`                        | 不显示文件名。默认情况下，当有多个文件时，`grep` 会显示文件名，而使用 `-h` 可以强制不显示文件名 |

**注意**：`-m 1` 在有多个文件的时候，每个文件都会显示一个匹配的行，而不是总共只显示一个匹配的行。

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

除了 `OFS` 以外，还有 `FS` 用于指定输入文件的分隔符。

### 预定义变量
除了之前提到的 `OFS`, `FS` 之外，还有一些预定义变量：
* `NR`: 当前行的行号
* `NF`: 当前行的列数
* `RS`：记录分隔符，默认是换行符，也就是每一行作为一条记录
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

## `find`

| 选项          | 说明 |
| ---           | --- |
| `-type`       | 指定文件类型。`f` 表示普通文件，`d` 表示目录，`l` 表示符号链接 |
| `-readable`   | 查找可读文件或目录 |
| `-writable`   | 查找可写文件或目录 |
| `-executable` | 查找可执行文件或目录 |
| `-name`       | 指定文件名。可以使用 `wildcads` |
| `-path`       | 指定路径。可以使用 `wildcards` |
| `-iname`      | 忽略大小写的文件名 |
| `-ipath`      | 忽略大小写的路径 |
| `-empty`      | 查找空文件或者空目录 |
| `-perm`       | 指定权限。例如 `-perm 644` 表示查找权限为 `644` 的文件或目录 |
| `-mtime`      | 指定修改时间。例如 `-mtime +1` 表示查找修改时间在 `1` 天前的文件或目录 |
| `-atime`      | 指定访问时间。例如 `-atime +1` 表示查找访问时间在 `1` 天前的文件或目录 |
| `-ctime`      | 指定创建时间。例如 `-ctime +1` 表示查找创建时间在 `1` 天前的文件或目录 |
| `-mmin`       | 指定修改时间。例如 `-mmin +1` 表示查找修改时间在 `1` 分钟前的文件或目录 |
| `-amin`       | 指定访问时间。例如 `-amin +1` 表示查找访问时间在 `1` 分钟前的文件或目录 |
| `-cmin`       | 指定创建时间。例如 `-cmin +1` 表示查找创建时间在 `1` 分钟前的文件或目录 |
| `-user`       | 指定拥有者 |
| `-group`      | 指定所属组 |
| `-delete`     | 删除查找到的文件或目录 |
| `-maxdepth`   | 指定查找的最大深度 |
| `-mindepth`   | 指定查找的最小深度 |
| `-and`        | 逻辑与。 |
| `-or`         | 逻辑或。 |
| `-not`        | 逻辑非。 |
| `-P`          | 不跟踪符号链接。默认行为。 |
| `-L`          | 跟踪符号链接。 |
| `-H`          | 只对命令行参数进行跟踪。例如 `find -H /path/to/file -name filename` 只对 `/path/to/file` 进行跟踪 |
| `-print0`     | 使用 `NUL` 作为文件名分隔符 |
| `-regex`      | 使用正则表达式匹配文件名 |
| `-iregex`     | 使用忽略大小写的正则表达式匹配文件名 |
| `-samefile`   | 指定文件的硬链接。例如 `find . -samefile file` 表示查找和 `file` 硬链接的文件 |
| `-links`      | 指定文件的硬链接数。例如 `find . -links 2` 表示查找硬链接数为 `2` 的文件，`+` 和 `-` 可以用来表示大于和小于 |

`-type` 还可以以下类型有：
* `b`：块设备文件
* `c`：字符设备文件
* `p`：管道文件
* `s`：套接字文件

**注意**：使用正则表达式匹配含有某个字符的文件名时，需要使用 `.*` 来表示任意数量的字符，例如
`find . -regex ".*pattern.*"`，而不能直接使用 `find . -regex "pattern"`。

**注意**：如果要使用复杂的逻辑表达式，需要使用 `()` 来分组，例如 `find . \( -name "*.txt" -or -name "*.md" \)`
表示查找所有 `txt` 或者 `md` 文件，其中的括号需要进行转义。

### 指定文件大小
`-size` 参数有以下几种单位：
* `b`：块，取决于文件系统，默认是 `512` 字节
* `c`：字节
* `k`：千字节 (1024 字节)
* `M`：兆字节 (1024 千字节)
* `G`：吉字节 (1024 兆字节)

知道了上述单位后，查找某个固定大小的文件只需要指定大小和单位即可，例如 `find . -size 1M` 表示查找大小为
`1` 兆字节的文件。

但是通常我们会查找大于或者小于某个大小的文件，这时候我们可以使用 `+` 和 `-` 来表示大于和小于，例如
`find . -size +1M` 表示查找大于 `1MB` 的文件，`find . -size -1M` 表示查找小于 `1MB` 的文件。
这两者也可以结合使用，例如 `find . -size +1M -size -2M` 表示查找大于 `1MB` 且小于 `2MB` 的文件。

### 对查找到的文件执行操作
`-exec` 可以对查找到的文件执行操作，例如 `find . -name "*.txt" -exec cat {} \;` 表示查找当前目录下的
所有 `txt` 文件并将其内容输出到标准输出。`{}` 会被替换为查找到的文件名，`\;` 表示结束。

也可以使用 `grep` 命令过滤查找到的文件，例如 `find . -name "*.txt" -exec grep "pattern" {} \;` 表示查找
当前目录下的所有 `txt` 文件并在其中查找 `pattern`。

## `tar`

| 选项          | 说明 |
| ---           | --- |
| `-c`          | 创建文档 |
| `-f`          | 指定文件名 |
| `-v`          | 显示详细信息 |
| `-z`          | 使用 `gzip` 压缩或解压 |
| `-j`          | 使用 `bzip2` 压缩或解压 |
| `-J`          | 使用 `xz` 压缩或解压 |
| `-x`          | 提取文档 |
| `-C`          | 指定提取的目录 |
| `-t`          | 查看文档内容 |
| `--wildcards` | 使用 `wildcards` 匹配文件。例如 `tar -xf a.tar --wildcards '*.txt'` 可以提取 `a.tar` 中的所有 `txt` 文件 |
| `--delete`    | 删除文档中的文件。例如 `tar -f a.tar --delete '*.txt'` 可以删除 `a.tar` 中的所有 `txt` 文件 |
| `--exclude=`  | 排除文件。例如 `tar -cf a.tar --exclude=*.txt .` 可以创建 `a.tar` 时排除所有 `txt` 文件 |
| `-r`          | 向文档中追加文件。例如 `tar -rf a.tar b.txt` 可以将 `b.txt` 追加到 `a.tar` 中 |
| `-A`          | 向文档中追加另一个文档。例如 `tar -Af a.tar b.tar` 可以将 `b.tar` 追加到 `a.tar` 中 |
| `-W`          | 检验文档。例如 `tar -Wf a.tar` 可以检验 `a.tar` 的完整性 |
| `-u`          | 更新文档。例如 `tar -uf a.tar b.txt` 可以更新 `a.tar` 中的 `b.txt` 文件 |

**注意**：在使用 `--exclude` 时必须使用 `=` 进行连接，且 `--exclude` 要出现在待打包文件之前。
`tar -cf a.tar . --exclude=*.txt` 是错误用法。

**注意**：在使用 `-u` 的时候，并不会覆盖旧的文件，而是直接追加新的文件，这样 `tar` 文件中可能会有多个
同名的文件，目前我并没有找到如何提取旧文件的方法。

## `ln`

| 选项 | 说明 |
| ---  | --- |
| `-s` | 创建软连接 |
| `-f` | 强制创建。如果软连接已经存在，会覆盖原有的软连接 |
| `-t` | 指定连接的目标目录。`ln -t dir target` 与 `ln target dir` 效果相同 |

使用 `ln target link_name` 创建的时候如果 `link_name` 是一个目录，那么会在目录下创建一个名为
`target` 的文件。

`ln` 默认创建硬连接。

使用 `rm` 和 `unlink` 命令均可以删除软连接或者硬连接。但是 `unlink` 一次只能删除一个文件，而 `rm`
可以删除多个文件。

### 硬连接与软连接
`ln` 命令可以创建硬连接和软连接，硬连接是指多个文件指向同一个 `inode`，而软连接是指一个文件指向
另一个文件。除此之外，硬连接和软连接还有以下区别：
* 软连接可以指向不同文件系统的文件，而硬连接只能指向同一个文件系统的文件
* 软连接可以指向目录，而硬连接不能指向目录
* 源文件被删除的时候，硬连接不会受到影响，而软连接会失效

在 `Linux` 中，可以通过 `ls -l` 命令查看文件的硬连接数 (第二列为硬连接数)，硬连接数为 `1` 表示只有一个文件指向该
`inode`，而硬连接数大于 `1` 表示有多个文件指向该 `inode`。

## `scp`

| 选项 | 说明 |
| ---  | --- |
| `-r` | 递归复制。如果复制的是目录，需要使用 `-r` 选项 |
| `-P` | 指定端口。默认端口是 `22` |
| `-p` | 保留文件属性 (例如修改时间等)。默认情况下，`scp` 不会保留文件的属性，使用 `-p` 可以保留文件的属性 |
| `-q` | 静默模式。不显示进度信息 |
| `-v` | 显示详细信息。可以使用多个 `-v` 来显示更多的信息 |
| `-C` | 压缩传输。使用 `-C` 可以压缩传输的数据 |
| `-i` | 指定密钥文件。默认情况下，`scp` 使用 `~/.ssh/id_rsa` 作为密钥文件 |
| `-l` | 限制带宽。单位是 `Kb/s` |
| `-3` | 通过本机在两个远端之间传输文件 |
| `-4` | 强制使用 `IPv4` |
| `-6` | 强制使用 `IPv6` |

使用 `scp` 的时候，如果在 `~/.ssh/config` 中配置了主机信息，可以直接使用主机名进行传输，例如
`scp file host_name:/path/to/file`。

`scp` 可以一次拷贝多个文件，例如 `scp file1 file2 host_name:/path/to/`。

## `apropos`

| 选项 | 说明 |
| ---  | --- |
| `-a` | 逻辑与。可用于匹配多个关键字，例如 `apropos -a keyword1 keyword2` |
| `-e` | 精确匹配 |
| `-w` | 匹配带有 `Shell` 支持的通配符 |
| `-r` | 使用正则表达式匹配 |
| `-l` | 不依照终端宽度进行裁剪 |
| `-s` | 指定 `man` 手册的节。例如 `apropos -s 3 keyword` 表示查找第 `3` 节的手册 |

`man` 手册的节有以下几个：
* `1`：命令或程序
* `2`：系统调用
* `3`：库函数
* `4`：特殊文件
* `5`：文件格式和约定
* `6`：游戏
* `7`：杂项
* `8`：系统管理命令
* `9`：内核相关

## `tee`

| 选项 | 说明 |
| ---  | --- |
| `-a` | 追加到文件。默认情况下，`tee` 会覆盖文件内容，使用 `-a` 可以追加到文件末尾 |
| `-i` | 忽略中断信号 |

管道在进行传递的时候可能会遇到需要 `root` 权限的时候，这时候就需要使用 `sudo tee` 从管道中读取信息
并写入到文件中。例如 `echo "content" | sudo tee file`。

## `usermod`

| 选项 | 说明 |
| ---  | --- |
| `-l` | 修改用户名。例如 `usermod -l new_name old_name` 表示将 `old_name` 修改为 `new_name` |
| `-u` | 修改用户 `UID`。例如 `usermod -u 1000 user` 表示将 `user` 的 `UID` 修改为 `1000` |
| `-o` | 允许重复的 `UID`。默认情况下，`usermod` 不允许重复的 `UID`，使用 `-o` 可以允许重复的 `UID` |
| `-g` | 修改基本用户组。例如 `usermod -g group user` 表示将 `user` 的基本用户组修改为 `group` |
| `-G` | 修改附加用户组。例如 `usermod -G group1,group2 user` 表示将 `user` 的附加用户组修改为 `group1` 和 `group2` |
| `-a` | 添加用户到附加用户组。默认情况下 `-G` 会覆盖用户的附加用户组，使用 `-a` 可以进行追加 |
| `-c` | 修改用户描述 |
| `-d` | 修改用户主目录 |
| `-m` | 移动用户主目录。默认情况下，`usermod -d dir` 不会移动用户主目录，使用 `-m` 可以移动用户主目录 |
| `-s` | 修改用户登录 `shell` |
| `-e` | 修改用户过期时间。例如 `usermod -e 2025-12-31 user` 表示将 `user` 的过期时间修改为 `2025-12-31` |
| `-p` | 设置新的密码。注意新的密码应该是加密后的密码，可以使用 `openssl passwd` 来生成加密后的密码，更加推荐使用 `passwd` 命令进行密码修改 |
| `-L` | 锁定用户。 |
| `-U` | 解锁用户。 |

**注意**：创建出来的用户默认是不会过期的。如果设置了过期时间后，可以通过 `chmod -e ""` 来取消。

## `sudo`

| 选项 | 说明 |
| ---  | --- |
| `-l` | 列出用户可以执行的命令。可以使用 `sudo -l command` 来检查是否可以执行某个命令 |
| `-U` | 与 `-l` 一同使用，指定列出的用户而不是执行 `sudo` 的用户 |
| `-u` | 指定用户执行命令。例如 `sudo -u user command` 表示以 `user` 用户的身份执行 `command` |
| `-g` | 指定用户组。例如 `sudo -g group command` 表示以 `group` 用户组的身份执行 `command` |
| `-s` | 指定 `shell`。默认情况下，`sudo` 会使用 `root` 用户的 `shell`，使用 `-s` 可以指定其他的 `shell` |
| `-k` | 使 `sudo` 忘记密码。默认情况下，`sudo` 会记住密码一段时间，使用 `-k` 可以使 `sudo` 忘记密码 |
| `-v` | 更新记住密码时间的时间戳为当前时刻 |

### `/etc/sudoers`
`/etc/sudoers` 文件用于配置 `sudo` 的权限，只有拥有 `root` 权限的用户可以修改这个文件。
`/etc/sudoers` 文件的格式如下：

```
# 配置某个用户
user host=(runas[:runasgroup]) [NOPASSWD:] command
# 配置某个组下的所有用户
%group host=(runas[:runasgroup]) [NOPASSWD:] command
# 引入目录中的所有文件
@includedir dirname
```

对于上述的规则，方括号中代表可选项，这里给出几个实例：
* `user ALL=(ALL) ALL`：允许 `user` 用户在任何主机上以任何用户的身份执行任何命令
* `%group ALL=(ALL) NOPASSWD: ALL`：允许 `group` 组下的所有用户在任何主机上以任何用户的身份执行任何
命令，且不需要输入密码
* `@includedir /etc/sudoers.d` 表示引入 `/etc/sudoers.d` 目录下的所有文件，这是默认添加的配置，这
意味着我们对于其他用户的配置可以放在 `/etc/sudoers.d` 目录下，并以用户名命名文件方便管理。

**注意**：在 `@includedir` 目录下的文件不能以 `~` 结尾并且不能含有 `.`。这是在
`/etc/sudoers.d/README` 中明确指出的：
> This will cause `sudo` to read and parse any files in the `/etc/sudoers.d` directory that do not
end in `~` or contain a `.` character.

**注意**：如果要配置多条命令应该使用 `,` 作为分隔符。

## `visudo`

推荐使用 `visudo` 对 `/etc/sudoers` 文件进行修改 (即通过命令 `sudo visudo`)，`visudo` 会检查语法
错误并在保存之前进行检查。

`visudo` 还有一些选项：

| 选项 | 说明 |
| ---  | --- |
| `-c` | 检查语法错误。例如 `sudo visudo -c` 表示检查 `/etc/sudoers` 文件的语法错误 |
| `-f` | 指定文件。例如 `sudo visudo -f /path/to/file` 表示编辑 `/path/to/file` 文件 |

## `mount`

| 选项       | 说明 |
| ---        | --- |
| `-a`       | 挂载所有在 `/etc/fstab` 中配置的文件系统 |
| `-t`       | 指定文件系统类型。例如 `mount -t ext4 /dev/sda1 /mnt` 表示将 `/dev/sda1` 挂载到 `/mnt` 目录上，并且文件系统类型是 `ext4` |
| `-o`       | 指定挂载选项。例如 `mount -o ro /dev/sda1 /mnt` 表示将 `/dev/sda1` 以只读模式挂载到 `/mnt` 目录上 |
| `-r`       | 以只读模式挂载。等价于 `-o ro` |
| `-w`       | 以读写模式挂载。等价于 `-o rw` |
| `--move`   | 移动挂载点。例如 `mount --move /mnt /mnt2` 表示将 `/mnt` 移动到 `/mnt2` 上 |
| `--fake`   | 模拟挂载。例如 `mount --fake /mnt` 表示模拟挂载 `/mnt` 目录上的文件系统，但是不会真正挂载 |

常见的文件系统类型：
* `ext4`：`Linux` 文件系统
* `ntfs`：`Windows` 文件系统, 使用 `mount -t ntfs-3g` 进行挂载
* `FAT32`：`FAT32` 文件系统，使用 `mount -t vfat` 进行挂载
* `exFAT`：需要安装 `exfat-fuse` 和 `exfat-utils` 包，使用 `mount -t exfat` 进行挂载
* `ISO`：`ISO` 文件系统，使用 `mount -t iso9660` 进行挂载
* `nfs`：网络文件系统，使用 `mount -t nfs -o vers=num` 可以指定版本

**注意**：直接使用 `mount` 可以列出所有已经挂载的文件系统。也可以使用 `mount -t type` 来列出指定类型
的文件系统。

### 获取设备的文件系统类型及 `UUID`
可以使用 `blkid` 命令来获取设备的文件系统类型及 `UUID`，例如 `blkid /dev/sda1`。

### `fstab`
直接通过 `mount` 命令挂载的文件系统在系统重启后会失效，为了让文件系统在系统重启后自动挂载，我们可以
将文件系统的信息写入 `/etc/fstab` 文件中。`/etc/fstab` 文件的格式如下：

```
# device <mount point>   <type>  <options>     <dump>  <pass>
/dev/sda1       /mnt        ext4    defaults       0       2
```

其中各个字段的含义如下：
* `<device>`：设备文件
* `<mount point>`：挂载点
* `<type>`：文件系统类型
* `<options>`：挂载选项，多个选项通过 `,` 进行分隔
* `<dump>`：备份标志。`0` 表示不备份，`1` 表示备份 (需要 `dump` 工具，通常设置为 `0`)
* `<pass>`：文件系统检查顺序。`0` 表示不检查，`1` 表示第一个检查，`2` 表示第二个检查 (根文件系统通常
设置为 `1`，其他文件系统设置为 `2`)

## `umount`
`umount` 用于卸载文件系统，使用方式为 `umount <mount point>`，例如 `umount /mnt`。

**注意**：卸载文件系统的时候，如果文件系统正在被使用，会提示 `device is busy`，这时候可以使用
`lsof <mount point>` 来查看哪些进程在使用这个文件系统，然后选择是否通过 `kill` 命令杀死这些进程。也
可以使用 `umount -l <mount point>` 在空闲时自动卸载。

# `git`
## `.gitignore`
`.gitignore` 文件用于指定不需要被 `git` 追踪的文件或目录，这些文件或目录不会被提交到版本库中。在
`.gitignore` 文件中可以使用 `wildcards` 来指定不需要被追踪的文件或目录。

### `wildcards`
`.gitignore` 中的 `wildcards` 与 `bash` 中基本一致，可以查看 [Wildcards in Linux](#wildcards-in-linux) 来
了解更多关于 `wildcards` 的内容。

### 基本用法
默认情况下，`.gitignore` 中的条目会进行递归的忽略，如果不想进行递归忽略，可以在条目前加上 `/` 表示只对
当前目录生效。例如 `/foo` 表示只忽略当前目录下的 `foo` 文件或目录，而 `foo` 表示忽略所有的 `foo` 文件或
目录。

默认情况下，`.gitignore` 中的条目会匹配目录和文件，如果只想匹配目录则可以在条目末尾加上 `/` 表示只匹配
目录。例如 `foo/` 表示只匹配目录 `foo`，而 `foo` 表示匹配所有的 `foo` 文件或目录。你可能会有疑惑：
在 `Linux` 中，同一目录下的文件和目录不能够重名，这样做的意义是什么？其实如果不进行递归匹配，确实是
没有意义的，但是 `build/` 和 `build` 表示的意义是不同的，前者表示忽略所有 `build` 目录，而后者表示
忽略所有 `build` 文件或目录。前者可以保留某个子目录下面的名为 `build` 的文件，而后者却做不到这一点。
当然如前面所言，我们可以使用 `/build/` 只忽略当前目录下的 `build` 目录。

### 全局忽略
在 `git` 中可以配置全局忽略文件，通常使用 `.git` 管理的仓库不需要追踪一些特定的文件，例如 `*.pyc`、
`*.o` 等，这时候我们可以配置全局忽略文件。全局忽略文件的配置文件是 `~/.config/git/ignore`。

### 忽略文件优先级
在 `git` 中任何一个目录下都可以有一个 `.gitignore` 文件，这个文件会对当前目录下的文件和目录生效。如果
在父目录下有一个 `.gitignore` 文件，那么这个文件会对当前目录下的文件和目录生效，但是如果当前目录下有
一个 `.gitignore` 文件，那么这个文件会覆盖父目录下的文件。也就是说，`.gitignore` 文件的优先级是从
子目录到父目录逐渐降低的。全局忽略文件的优先级最低。

# 巨人的肩膀
* [10 Practical Examples Using Wildcards to Match Filenames in Linux](https://www.tecmint.com/use-wildcards-to-match-filenames-in-linux/)
* [fish shell wildcards](https://fishshell.com/docs/current/fish_for_bash_users.html#wildcards-globs)
* [Linux Tutorial - Cheat Sheet - grep](https://ryanstutorials.net/linuxtutorial/cheatsheetgrep.php)
* [20 grep command examples in Linux \[Cheat Sheet\]](https://www.golinuxcloud.com/grep-command-in-linux/)
* [Linux Handbook: sort Command Examples](https://linuxhandbook.com/sort-command/)
* [15+ Tips to PROPERLY sort files in Linux \[Cheat Sheet\]](https://www.golinuxcloud.com/linux-sort-files/#1_Sort_by_name)
* [Linux and Unix sort command tutorial with examples](https://shapeshed.com/unix-sort/)
* [Linux sort Command](https://www.baeldung.com/linux/sort-command)
* [How to Use the awk Command on Linux](https://www.howtogeek.com/562941/how-to-use-the-awk-command-on-linux/)
* [30+ awk examples for beginners / awk command tutorial in Linux/Unix](https://www.golinuxcloud.com/awk-examples-with-command-tutorial-unix-linux/)
* [8 Powerful Awk Built-in Variables – FS, OFS, RS, ORS, NR, NF, FILENAME, FNR](https://www.thegeekstuff.com/2010/01/8-powerful-awk-built-in-variables-fs-ofs-rs-ors-nr-nf-filename-fnr/)
* [Getting Started With AWK Command \[Beginner's Guide\]](https://linuxhandbook.com/awk-command-tutorial/)
* [25+ most used find commands in Linux \[Cheat Sheet\]](https://www.golinuxcloud.com/find-command-in-linux/)
* [find Linux Command Cheatsheet](https://onecompiler.com/cheatsheets/find)
* [Linux Find Cheatsheet](https://linuxtutorials.org/linux-find-cheatsheet/)
* [Find files and directories on Linux with the find command](https://opensource.com/article/21/9/linux-find-command)
* [10 ways to use the Linux find command](https://www.redhat.com/sysadmin/linux-find-command)
* [Find cheatsheet](https://quickref.me/find)
* [Ignoring files](https://docs.github.com/en/get-started/getting-started-with-git/ignoring-files)
* [cat command examples for beginners \[cheatsheet\]](https://www.golinuxcloud.com/cat-command-examples/)
* [Linux Audit: tar cheat sheet](https://linux-audit.com/cheat-sheets/tar/)
* [15+ tar command examples in Linux \[Cheat Sheet\]](https://www.golinuxcloud.com/tar-command-in-linux/)
* [10+ practical examples to create symbolic link in Linux](https://www.golinuxcloud.com/create-symbolic-link-linux/)
* [15+ scp command examples in Linux \[Cheat Sheet\]](https://www.golinuxcloud.com/scp-command-in-linux/)
* [apropos Linux Command Explained](https://phoenixnap.com/kb/apropos-linux)
* [10 tee command examples in Linux \[Cheat Sheet\]](https://www.golinuxcloud.com/tee-command-in-linux/)
* [How To Use ‘sudo’: The Complete Linux Command Guide](https://raspberrytips.com/sudo-linux-command/)
* [Linux visudo command](https://www.computerhope.com/unix/visudo.htm)
* [15 usermod command examples in Linux \[Cheat Sheet\]](https://www.golinuxcloud.com/usermod-command-in-linux/)
