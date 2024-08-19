开始阅读文本之前，我强烈建议先看`vimtutor`以确保你能看懂我使用的一些符号：
* `<CR>`：回车键；
* `<C->`：表示按住`Control`然后点按另一个键；
* `ab`：表示在规定的时间内依次按下`a`和`b`；
* `<leader>`：前置键（前缀键）默认是`,`，可以自定义；

**注意：一般以尖括号包围的是指某类特殊按键，而不是需要你输入尖括号。**

**注意：尖括号中的键是不区分大小写的，所以`<C-o>`和`<C-O>`实际上是一个意思。**

# 花费一周的时间从零学习并配置vim值得吗？
要回答这个问题，首先需要解释我为什么会转战`vim`。

## 我和vim
和`vim`的结缘要说到高中时期，我在高中时期第一次接触到`vim`，当时我只是觉得这个工具非常的麻烦，竟然不能够通过鼠标进行操作（当然可以设置开启鼠标功能，但我当时并不知道这一点），于是在我高中的时候我对`vim`的认识只是停留在通过`h,j,k,l`进行导航，修改文件需要先按一次`i`键，输入完成后如果要保存需要按下`<Esc>`，然后输入`:w<CR>`而且还必须是英文的冒号，这就导致了如果你输入中文之后忘记按`<Shift>`（或者没有关闭输入法），那么你的命令将不被`vim`所识别，还有使用最多的操作应该是`:wq<CR>`保存退出和`:q!<CR>`不保存退出了吧。

我相信很多知道`vim`。但是没有真正开始学习`vim`的人和我高中时期是很相像的——知道几条`vim`的基本指令。

## 烦人的鼠标操作
在我高中时觉得`vim`最难用的就是不能使用鼠标，而现在我转战`vim`也是因为我要完成的很多操作都需要使用鼠标，严重影响了我的操作的连贯性。举一个例子，如果我要在`markdown`文件中插入一段代码，那么我需要连续按下三次强调符号（和波浪线在一起符号），然后输入我的代码语言，接着按下回车键，这个时候有的编辑器例如`typora`会自动生成一个代码块，可以在里面写代码，写完后需要通过鼠标或者方向键将光标移到后面的位置，如果你没有在`markdown`文件后面插入一些空行的习惯，可能你还需要进入源代码模式进行操作，而如果使用一些没有自动生成代码块的编辑器，你还需要在输入完代码后继续键入三次强调符号。上面的过程实在是非常的麻烦，主要是因为鼠标和方向键以及强调符键都与主键盘区（字母集中区）较远，难以操作，而且强调符号键不独立容易按错，比如按到`1`或者是`<Tab>`。

正是因为上述的原因我认识到了，如果只是单纯的顺序浏览文件，那么使用鼠标是最方便的，我不需要到处跳转，就算我需要进行一些链接的跳转，我也可以通过鼠标侧键进行返回，而对于需要浏览加编辑的文件，只使用鼠标是近乎不可能完成的，而使用鼠标`+`键盘的形式同样不是最优解，而`vim`可以只使用键盘完成浏览`+`编写的操作，这如果使用`vim`编写纯英文的文件是更为方便的，而作为一个写代码的，平常的大部分需要编写的文件应该是满足纯英文条件的（当然你可能需要写中文注释，或者写一些笔记，但是使用`vim`进行操作并不会太慢）。

## The Missing Semester
`MIT`的`The Missing Semester`让我真正认识到了好的工具的重要性，在三位顶级学府的教授的推荐下，以及看了他们的实操演示后，我被`vim`所吸引——使用`vim`不是单纯地操作鼠标键盘，而是在大脑里进行一个类似编程的过程。

这个过程需要你进一步分解每一个操作，然后完全通过键盘完成相关的操作。

比如我要跳到某个函数的开始，那么我就有很多种方法：
* 通过鼠标滚轮找到这个函数，通过点击鼠标放置鼠标指针；
* 通过搜索函数名字并跳转到函数（当然得能大概记住函数的名字才行）；
* 如果在当前的屏幕内看见函数，那么可以直接通过输入行号进行跳转；
* 如果你刚好在函数的尾部括号处，那么你可以通过`%`进行跳转；
* ......

使用鼠标的话，会面临我之前提到的问题，而使用`vim`可以轻松的通过后面几种方式实现。

## 值不值
在我学习了`vim`并用`vim`写下这一篇`blog`的时候，我已经感受到一周`vim`带给我的速度与我写了几年`VSCode`的速度相当了（当然也是因为我比较懒，不会去记忆太多`VSCode`的快捷键）。

而学会`vim`可能需要很短的时间，但是你可能需要一生的时间去掌握这一个工具（这是在`The Missing Semester`里面看到的）。根据我目前的体验看下来，我是相信`vim`给我带来的效率提升远远超过我花费的时间，也就是投入产出比非常小。

# 我如何学习vim
## vimtutor
首先我通过观看`The Missing Semester`了解到了`vim`的工作模式，并且我按照课程后面的`exercises`中的建议阅读了`vimtutor`并自己安装了自己`vim`的前两个插件`vim-plug`和`ctrlp`（当然，我现在已经拥抱`LeaderF`了）。

通过阅读`vimtutor`我学会了`vim`的大部分基本操作，这个时候我已经可以对单个文件进行纯键盘操作。

这个阶段我发现了一些非常用的知识（常用知识非常多，我这里只列举一些容易被忽视的）：
* 通过`f,F`进行一行中单个字符的查找，通过`,;`进行下一个和上一个查找位置的跳转；
* 通过`inside, around`模式进行快速修改，例如要修改`'`（单引号）中的所有内容只需要`ci'`，如果要连同`'`一同修改只需要输入`ca'`；
* 通过`xp`可以快速交换当前字符和后一个字符的位置，这个在修改错误单词的时候比较常用，比如如果要修改`raelly`只需要在`a`的位置输入`xp`既可以完成相关的操作；
* 在插入模式下通过`<C-o>zz`快速把插入模式的指针移动到中间，`zt`和`zm`分别对应顶部和底部；
* 通过`q`与任意字母进行宏录制，宏可以进行多次的重复操作，在`The Missing Semester`中介绍了如何通过录制宏将一个`html`文件转换成`json`模式，当时我便被这种工作模式所震惊，因为它实在是太方便了；
* 通过`<C-o>, <C-i>`可以实现`IDE`中鼠标侧键的功能；
* 普通模式下，如果光标在一个数字上，那么可以通过`<C-a>`让数字增加`1`，通过`<C-x>`可以让数字减少`1`；
* 如果当前指针在一个路径上，且这个路径是一个文件，那么可以通过`gf`跳转到这个文件（`go file`）；
* 通过`gv`可以快速选择上次`visual`模式下选择的内容；
* 通过`:reg<CR>`可以查看当前的寄存器的内容（每一次删除或者复制的内容会存在不同的寄存器，你可以在网上找到`vim`寄存器内容的介绍），而通过`<name>p`可以粘贴寄存器中的内容（其中`<name>`是寄存器的名字）。特别有用的一点是`"0`寄存器永远存放的你在`vim`中通过复制获得的内容。
* ...

**注意：`vim`的帮助文档很强大，如果你不知道某个按键或者命令以及某些变量的作用，你只需要打开一个`vim`窗口，比如我不知道`<C-o>`有什么作用，我只需要在`normal`模式下输入`:h ^O<CR>`那么就会弹出该快捷键的帮助信息（查找快捷键时使用`^`和大写字母表示按住`Ctrl`后点按某个按键）**

## TheCW
`TheCW`是一个`Bilibili`的`up`，他的个人主页：[TheCW Bilibili](https://space.bilibili.com/13081489)。

我通过观看`TheCW`的视频完成我的第二阶段的学习，我的大部分`vim`配置的思路也来自其视频中所提及的东西（虽然`TheCW`现在已经拥抱`Neovim`了，但是其`vim`的配置思路依然值得学习）。

在这个阶段我学会了对按键进行映射，编写自动命令等基本操作，以及自己去寻找插件并自定义安装的相关操作。

## 自定义vim
为什么需要自定义`vim`，进行自定义`vim`之前一定要搞清楚这个问题。

虽然网上有很多现成的`vim`配置（通过关键字`vim`或者`dotfiles`便可以检索到），这些配置拿过来往往只需要一两句命令便可以将你的`vim`变成一个完全现代化的`IDE`，但是我是非常不推荐直接将别人的配置拿来进行使用的，如果直接将别人的配置拿来使用，那你直接下载一个`IDE`并在里面装一个可以支持`vim`三种模式的插件不就完成了所有的操作吗？为什么还需要单独去使用`vim`呢？

在我看来使用`vim`的插件是因为`vim`本身不能很好的实现你需要的功能或者你需要通过非常复杂的操作才能完成，那么这个时候你就需要插件来帮你简化操作，因此我对于`vim`自定义插件方面一定是你发现了某个功能很繁琐后你才开始去安装一个插件，这样你才能够真正的安装并且运用一个插件，不然就和我之前一样装了一个`IDE`有着很多很多的功能，但每次还是只是用最基本的几个功能。

在这个阶段我基本完成了我的`vim`配置，这个阶段也是耗时最多的阶段，因为我在这个阶段安装每个插件之前，我会阅读插件的文档（至少我会将`README.md`读完，在大多数情况下我还需要阅读仓库目录下`doc/*.txt`以进行更个性化的定义）。

# 换电脑了怎么办？又要重配吗？
已经`2024`年，该使用现代化脚本来帮你完成任务了！

我在知道如何配置`vim`以及配置文件如何工作时，我便意识到了这个问题。而在我进行自定义开始，我便创建了一个名叫`dotfiles`的`git`仓库，并且便写了一个`python`脚本，我只需要通过`python3 dot_files.py init`便可为一台全新的`Unix/Linux`服务器安装完成我的所有配置而在这个过程中我只需要按下几次回车键，退出几个`vim`窗口，输入一次用户密码（甚至没有`vim`也行，但是服务器上得需要有`python3`）。`Life is short; I use python!`是时候使用`python`来帮你做重复的工作了。

# 我的vim配置
## 基础配置
基础配置主要指的是不安装插件进行的配置。

### 指针、预览与导航
首先使用`vim`进行预览的时候，你可能会难以找到你的指针位置或者你希望每次能够看见后面几行代码，那么你可以通过设置`scrolloff`来决定你的指针每次停留距离顶部或者底部几行的位置：
```vim
set scrolloff=5
```

我一开始设置了让鼠标指针永远处于最中间，但是这样如果使用`tmux`的话状态栏会增加一行，导致滚动不流畅，而如果以`tmux`的高度保证`vim`始终行数始终是奇数的话，那么退出`tmux`后使用`vim`又变成了偶数，又会出现滚动不流畅。除此之外，让鼠标永远居中还有一个非常严重的问题如果遇到一个比较长的函数，如果你要进行多次在该函数内部与首部跳转的话，你就会发现你只有半页的屏幕能够有效的显示该函数，因此我非常不建议设置鼠标一直居中（但我依然在我的配置文件中保留了这一部分代码，因为我当时找这个代码还是找了一段时间的，结果用了一段时间不想用了，有点舍不得删除）。

设置鼠标指针的形状，在`vim`中我非常建议将`normal`模式下的指针设置为一个长方形，也就是按下`insert`键后的形状，这个能够非常清楚的提醒你的修改操作究竟会改变什么：
```vim
let &t_SI.="\e[5 q"
let &t_SR.="\e[3 q"
let &t_EI.="\e[1 q"
autocmd VimEnter * silent !echo -ne "\e[1 q"
autocmd VimLeave * silent !echo -ne "\e[5 q"
```

在设置过程中我发现上述的`autocmd`会使状态栏在每次打开`vim`的时候底部命令行中会出现奇怪的字符，我目前并没有找到解决方法（不过这并不影响使用，因为当窗口重绘的时候，命令行中也就清空了）。

使用`vim`时，每次打开`vim`你的指针会在文件的最开始，可以通过以下的设置让每次打开文件的时候，你的指针在你上次退出的位置：
```vim
autocmd BufReadPost *
  \ if line("'\"""'") >= 1 && line("'\"""'") <= line("$") && &ft !~# 'commit'
  \ |   exe "normal! g`\""
  \ | endif
```

如果你希望通过一条线来只是你的指针所在行，那么你可以通过以下命令：
```vim
set cursorline
```

在`vim`中默认是没有行数提示的，可以通过以下命令设置显示行数提示：
```vim
set number
```

设置行号并不能很好的帮助我们导航，我们往往需要进行行之间的跳转，直接使用多次`j,k`按键是非常不好的习惯，应该使用增加数字前兆的`j,k`操作。因此如果我们能够显示相对行号那么是非常方便的，相对行号会显示其他行与当前行的距离，可通过以下命令设置相对行号：
```vim
set relativenumber
```

在`VSCode`等软件中，一行会无限的延长，而在`vim`中可以设置自动折行，能够让一行显示在多行中，但是并不会实际更改文件的内容：
```vim
set wrap
```

除此之外，我们往往需要重新映射我们的`j,k`命令使其能够在分行是进行行内跳转：
```vim
noremap <silent> <expr> j (v:count == 0 ? 'gj' : 'j')
noremap <silent> <expr> k (v:count == 0 ? 'gk' : 'k')
```

`vim`的自动截断会在你的行过长时，将其分成多行（根据你的输入字符截断，例如你最后输入空格，那么太长就会进行截断），与折行不同，自动截断会改变文件的实际内容，这个功能可以通过设置截断长度来决定是否开启（而默认是关闭的，设置`0`就是关闭）：
```vim
set tw=0
```

`vim`在命令行中输入可以通过`<Tab>`进行补全，多次`<Tab>`可以切换补全结果，但是我们并不知道下一次的补全结果是什么，而可以通过以下设置显示所有可用的补全结果，帮助我们快速定位需要的命令：
```vim
set wildmenu
```

`vim`的默认缩进是八个字符，这是非常不合理的，我将其改成了使用四个空格进行替代：
```vim
set expandtab
set tabstop=4
set shiftwidth=4
```

我在使用`vim`的时候发现按下`<Esc>`后需要等待一秒左右才能生效，这与`ttimeoutlen`变量有关，可以将其设置为一个较小的值，我直接将其设置为`0`：
```vim
set ttimeoutlen=0
```

想要使用`vim`编译非纯英文的文档，往往需要设置编码为`utf-8`（这取决于源文件是什么编码，大部分情况是`utf-8`）：
```vim
scriptencoding utf-8
set encoding=utf-8
```

有时候你会不小心在行位输入多余的空格，而在`normal`模式下，如果你不进行相关的设置，你是很难发现你的行末会有多余的空格的，而我们可以设置`listchars`显示行末的空格：
```vim
set list
" when setting tab, it must have two characters.
set listchars=tab:»·,trail:·
```

在直接启动`vim`的时候，会出现`vim`的默认界面（包含一些版本信息），由于我设置了透明主题，其会进行重绘导致这个画面一闪而过，于是我关闭了这个默认信息：
```vim
set shortmess+=I
```

如果在`vim`中只有一个窗口的时候底部的状态栏可能并不会显示，可以通过以下的命令总是显示状态栏：
```vim
set laststatus=2
```

在默认情况下，如果在打开一个新的窗口的时候，你的上一个窗口没有保存，那么`vim`不会允许你打开新的窗口，这在使用`vim`进行代码的跳转的时候是非常麻烦的，于是我将此功能关闭：
```vim
set hidden
```

在搜索的时候可以开启动态搜索（会在你一开始输入的时候就开始跳转指针）和智能大小写（默认忽略大小写，但是大写的字母一定会被认为是大写）：
```vim
set incsearch
set ignorecase
set smartcase
```

可以开启搜索高亮, 这样设置后每次确认搜索所有的匹配单词都会高亮，但是有时候我们需要关闭搜索高亮，我们可以将其绑定到一个快捷键上（我的`<LEADER>`设置成了空格）：
```vim
let mapleader=" "
" close the last time's highlight when entering vim
exec "nohlsearch"
set hlsearch
nnoremap <LEADER><CR> :nohlsearch<CR>
```

使用`vim`删除一些不存在的东西的时候，就会有报警器的声音，这非常的烦人，可以通过以下命令关闭：
```vim
set noerrorbells visualbell t_vb=
```

`vim`可以开启显示你输入的命令（会在右下角显示你的按键）：
```vim
set showcmd
```

`vim`可以设置自动切换工作目录，也就是在你打开一个新的文件的时候，将你的工作目录切换为打开的文件，当你回到上一个文件时又切换回来，这对于使用相对目录的命令非常好用（使用插件进行文件查找的时候，也可以查找到达的文件所在目录）：
```vim
set autochdir
```

`vim`可以开启高亮（这也是很多插件需要要求开启的）：
```vim
syntax on
```

### 分屏与新建窗口
`vim`可以任意上下左右分屏，默认是打开当前`buffer`的相同文件，分屏后可以通过文件搜索插件找到新的文件在分屏中显示，这是非常实用的功能，我使用如下快捷键进行分屏的操作，在下面的配置中所谓上下左右分屏是指你的指针会留在那个位置，而在插入模式中，不能使用`<C-j>, <C-k>`进行分屏，因为这是我代码补全的上下选择按键：
```vim
" set <C> + h, j, k and l to be split left, down, up, right.
" set <leader> h, j, k and l to move cursor between Windows.
" set <leader>H, J, K, L to move current window to left, down, up, right
" set <leader>T to let current window be a new tab
nnoremap <C-h> :set nosplitright<CR>:vsplit<CR>:set splitright<CR>
nnoremap <C-j> :set splitbelow<CR>:split<CR>:set nosplitbelow<CR>
nnoremap <C-k> :set nosplitbelow<CR>:split<CR>
nnoremap <C-l> :set splitright<CR>:vsplit<CR>
inoremap <C-h> <ESC>:set nosplitright<CR><ESC>:vsplit<CR><ESC>:set splitright<CR>
" we can not use <C-j> in insert mode,
" because we use <C-j> to select code completion.
" inoremap <C-j> <ESC>:set splitbelow<CR><ESC>:split<CR><ESC>:set nosplitbelow<CR>
" inoremap <C-k> <ESC>:set nosplitbelow<CR><ESC>:split<CR>
inoremap <C-l> <ESC>:set splitright<CR><ESC>:vsplit<CR>
nnoremap <LEADER>h <C-w>h
nnoremap <LEADER>j <C-w>j
nnoremap <LEADER>k <C-w>k
nnoremap <LEADER>l <C-w>l
nnoremap <LEADER>H <C-w>H
nnoremap <LEADER>J <C-w>J
nnoremap <LEADER>K <C-w>K
nnoremap <LEADER>L <C-w>L
nnoremap <LEADER>T <C-w>T
```

我将上下左右按键映射为了调整窗口的大小，上和右对应着增大当前窗口，而下和左对应减少当前窗口（我同样绑定了输入模式下的上下左右，这意味着你不能使用上下左右进行光标的移动）：
```vim
nnoremap <Up> :res +5<CR>
nnoremap <Down> :res -5<CR>
nnoremap <Left> :vertical resize -5<CR>
nnoremap <Right> :vertical resize +5<CR>
inoremap <Up> <C-o>:res +5<CR>
inoremap <Down> <C-o>:res -5<CR>
inoremap <Left> <C-o>:vertical resize -5<CR>
inoremap <Right> <C-o>:vertical resize +5<CR>
```

`vim`中也可以创建新的`tab`，也就是一个独立的窗口，我将其映射为了如下的快捷键：
```vim
inoremap <C-t> <ESC>:tabnew<CR>
nnoremap <C-t> :tabnew<CR>
" Now we use <LEADER>n and <LEADER>b to got next and back
" and <LEADER>nubmer to go specified tab.
nnoremap <LEADER>b :tabprev<CR>
nnoremap <LEADER>n :tabnext<CR>
nnoremap <LEADER>1 1gt
nnoremap <LEADER>2 2gt
nnoremap <LEADER>3 3gt
nnoremap <LEADER>4 4gt
nnoremap <LEADER>5 5gt
nnoremap <LEADER>6 6gt
nnoremap <LEADER>7 7gt
nnoremap <LEADER>8 8gt
nnoremap <LEADER>9 9gt
```

### 插件需要的兼容性配置
这里介绍一些插件需要你开启的配置，通常这一部分直接写入`.vimrc`文件即可，这里不做过多的解释：
```vim
set nocompatible
filetype on
filetype indent on
filetype plugin on
filetype plugin indent on
" this is for some plugin that can fold your code,
" I didn't install one now
set foldmethod=indent
set foldlevel=99
" turn on popup option, this can enable floating window of plugins
set completeopt=popup
```

### 自定义的基础按键
每次退出都需要输入`:wq`非常的麻烦，我进行了如下的绑定：
```vim
" set Q to be :q!
nnoremap Q :q!<CR>
" set ^S to be :w
nnoremap <C-s> :w<CR>
inoremap <C-s> <C-o>:w<CR>
" set S to be :wq
nnoremap S :wq<CR>
```

我使用`<leader>r`的方式来编译并运行当前的文件，这在写算法题目或者预览`markdown`文件效果的时候非常方便：
```vim
func! CompileRunGcc()
    exec "w"
    if &filetype == 'c'
        exec "!gcc -g -Wall % -o %<"
        exec "!time ./%<"
    elseif &filetype == 'cpp'
        exec "!g++ -g -Wall -std=c++17 % -o %<"
        exec "!time ./%<"
    elseif &filetype == 'java'
        exec "!javac %"
        exec "!time java %<"
    elseif &filetype == 'sh'
        :!time bash %
    elseif &filetype == 'python'
        silent! exec "!clear"
        exec "!time python3 %"
    elseif &filetype == 'html'
        exec "!wslview % &"
    elseif &filetype == 'markdown'
        exec "MarkdownPreviewToggle"
    elseif &filetype == 'vimwiki'
        exec "MarkdownPreviewToggle"
    endif
endfunc
nnoremap <LEADER>r :call CompileRunGcc()<CR>
```

### 其他的配置
可以将空文件类型的文件类型设置为`none`，这样可以在后面为这一类文件编写`autocmd`：
```vim
" set empty filetype be none.
function! SetFiletypeNewBuffer()
  if @% == ""
    :set filetype=none
  endif
endfunction
autocmd BufEnter * :call SetFiletypeNewBuffer()
```

我并不需要自动注释功能（如果上一行是注释，那么通过回车后会自动添加注释符号），因此我将其关闭（这个设置要放在加载所有插件之后，因为我发现如果放在之前会导致某些插件不能使用，目前没有发现原因）：
```vim
" Note that this must be at the bottom,
" and I don't know why, it seems that some plugins will disable something.
" close the annoying auto comments
autocmd FileType * setlocal formatoptions-=c formatoptions-=r formatoptions-=o
```

我使用的是`WSL`（目前已经可以在上面直接运行`GUI`程序了，可以不用使用`XForwarding`了），因此我希望我能够读取或者写入`Windows`的剪切板，同时我发现如果在写算法题目的时候，我非常需要能够一下复制整个文件内容的功能，于是我进行了如下的映射（如果你不知道这是什么你不用关注，`WSL`用户会知道我在说什么）：
```vim
" NOTE: I've found new solution for this (copy and paste with windows using
" wsl vim). After 2021, wsl has internal gui app,  which is called wslg,
" using windows to show gui apps, so now the system clipboard can be used.
" make sure you have sudo apt install vim-gtk
vnoremap <LEADER>y "+y<CR>
nnoremap <LEADER>p "+p<CR>
nnoremap <LEADER>P "+P<CR>
vnoremap <LEADER>p "+p<CR>
vnoremap <LEADER>P "+P<CR>
" copy the whole file is used very often, so we add a new bind for this.
nnoremap <LEADER>ya :w !clip.exe<CR><CR>
```

一个不那么常用的功能，单词拼写检查，可能写英文文档的时候会用到，目前我并没有经常用到这个功能，不过我还是设置了相应的快捷键进行开启或者关闭拼写检查：
```vim
nnoremap <leader>sc :set spell!<CR>
```

下面是一些拼写检查的使用说明：
* 开启拼写检查后，普通模式下可以通过`]s,[s`来定位下一个和上一个拼写错误的单词位置；
* 在拼写错误的单词上，普通膜下通过`z=`可以打开选项列表可以通过输入对应的数字选择正确的单词；
* 如果在插入模式下你忘记了某个单词如何写，你可以写下你记得的一部分，然后通过`<C-x>s`，启动查词模式, 可以通过`<C-j>, <C-k>`在词语列表中进行导航；
* 通过`zg`可以将当前的单词加入到词典中；通过`zw`可以将当前的单词从词典中删除。

### 一些需要注意的点
我在配置过程中遇到了很多问题，我将一部分问题整理出来：
* 某些终端并不能使用`<Alt>`键作为前缀键，也就是说你映射的所有以`<Alt>`开头的命令如果不进行特殊配置均会失效，`Windows Terminal`就是这样的终端，所以我的所有按键映射中都不存在含有`<Alt>`按键的映射；
* 某些终端不能分别映射某些按键，例如目前我发现在`Windows Terminal`中，如果映射`<C-m>`那么`<CR>`的功能也会跟着更改；如果映射`<Tab>`那么`<C-i>`的功能也会被更改；反之亦然。目前只发现了这两组按键，因此为了兼容性，我同样没有映射这类按键。

## 我使用的插件以及我的配置
如果你完成基础配置，那么你的`vim`应该已经不会太难用了，至少对于单独编写一个文件而言，如果你能将你在`vimtutor`中学的东西运用起来，那么你的速度是不会低于直接使用`VSCode`的（先不考虑代码补全的问题）。

这一部分我配置了很多非常常用的插件，这些插件让我的`vim`完全变成了一个轻量级的`IDE`（我从一个什么都没有的电脑上配置好我的`vim`利用我的脚本只需要两三分钟）。

在插件安装开始之前，我强烈建议你升级到`vim 9.0`这样能够最好的获得插件的支持：
```bash
sudo add-apt-repository ppa:jonathonf/vim
sudo apt update
# vim-gtk enable you to use system clipboard,
# if you don't need it, you can remove it.
sudo apt install vim vim-gtk
```

### 如何管理插件的配置文件
你可以将所有插件的配置都写在`.vimrc`中，但是这样会非常不好管理。

我为每一个插件都创建了单独的配置文件，然后在我的`.vimrc`中通过`source`命令引入这些配置文件。这样只有在第一次安装插件的时候，我需要创建一个配置文件并在`.vimrc`中引入，而后续的配置相关的操作，我则可以通过插件的名称很容易的定位到配置文件进行修改，以下便是我用到的所有插件：
```vim
source ~/.vim/vim_plugin_config/vimplug_config.vim
source ~/.vim/vim_plugin_config/leaderf_config.vim
source ~/.vim/vim_plugin_config/youcompleteme_config.vim
source ~/.vim/vim_plugin_config/nerdtree_config.vim
source ~/.vim/vim_plugin_config/onedark_config.vim
source ~/.vim/vim_plugin_config/vimpolyglot_config.vim
source ~/.vim/vim_plugin_config/undotree_config.vim
source ~/.vim/vim_plugin_config/tagbar_config.vim
source ~/.vim/vim_plugin_config/vimgitgutter_config.vim
source ~/.vim/vim_plugin_config/nerdcommenter_config.vim
source ~/.vim/vim_plugin_config/vimwhichkey_config.vim
source ~/.vim/vim_plugin_config/nerdtreegitplugin_config.vim
source ~/.vim/vim_plugin_config/markdownpreview_config.vim
source ~/.vim/vim_plugin_config/vimwiki_config.vim
source ~/.vim/vim_plugin_config/indentline_config.vim
source ~/.vim/vim_plugin_config/autopairs_config.vim
source ~/.vim/vim_plugin_config/vimsurround_config.vim
source ~/.vim/vim_plugin_config/tabular_config.vim
source ~/.vim/vim_plugin_config/markdownhelper_config.vim
```

接下来我将一个个介绍这些插件。

插件的获取只需要在[github](https://www.github.com)中搜索相应的名称即可，或者在`www.github.com`后面追加上`vimplug_config.vim`文件中的插件路径即可（推荐这种方法，因为`github`模糊查找功能很难用），例如`www.github.com/Yggdroot/LeaderF`就是`leaderf`的插件仓库。

### vim-plug
这是一个`vim`的管理插件的插件，他的作用就是安装所有其他的插件。对于`vim`来说一般是必装一个管理插件的插件的，你也可以使用`vundle`等其他插件代替，这里我使用该插件，这个插件的安装非常简单，只需要执行以下命令即可：
```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

如果上述的命令执行失败，你可以直接打开上面的网址，把里面的内容复制到`~/.vim/autoload/plug.vim`即可。

我写了这个插件的配置文件，之后安装插件只需要在配置文件中增加即可：
```vim
call plug#begin('~/.vim/plugged')
Plug 'vim-airline/vim-airline'
Plug 'Yggdroot/LeaderF', { 'do': ':LeaderfInstallCExtension' }
Plug 'ycm-core/YouCompleteMe'
Plug 'preservim/nerdtree'
Plug 'joshdick/onedark.vim'
Plug 'sheerun/vim-polyglot'
Plug 'mbbill/undotree'
Plug 'preservim/tagbar'
Plug 'airblade/vim-gitgutter'
Plug 'preservim/nerdcommenter'
Plug 'liuchengxu/vim-which-key'
Plug 'Xuyuanp/nerdtree-git-plugin'
Plug 'iamcco/markdown-preview.nvim', { 'do': { -> mkdp#util#install() }, 'for': ['markdown', 'vim-plug']}
Plug 'vimwiki/vimwiki'
Plug 'Yggdroot/indentLine'
Plug 'jiangmiao/auto-pairs'
Plug 'tpope/vim-surround'
Plug 'godlygeek/tabular'
call plug#end()
```

在增加新的`Plug`之后，你还需要在`shell`中执行`vim +PlugInstall`或者打开任意一个`vim`的窗口输入`:PlugInstall<CR>`等待安装完成。

一般情况下只需要写`Plug 'git repository name'`即可，某些需要你写其他内容的插件会在其`README.md`中标明，例如上面的`LeaderF`配置行写的命令是安装`C`库，这样可以让其查找速度更快。

### LeaderF
这是一个文件查找的插件，我开始使用的是`CtrlP`（也是一个文件查找插件），但是我发现`LeaderF`是一个中国人写的，我便转到了`LeaderF`。在我使用了`LeaderF`之后我发现其并不输给`CtrlP`。`LeaderF`能够将窗口进行悬浮显示，并且支持预览文件功能，效果类似这样：

![](https://img-blog.csdnimg.cn/img_convert/5d79122f798c740d00943e860b5453ed.png)

上面的图片中，最下的窗口是我本身的窗口，我启动`LeaderF`后，会在中间生成一个悬浮窗口，悬浮窗口的上方便是我指针所在位置文件的前几行内容。

我的`LeaderF`的相关配置如下（写了详细的注释，这里不进行过多的解释）：
```vim
" use popup rather than bottom.
let g:Lf_WindowPosition = 'popup'
let g:Lf_PopupHeight = 0.3
let g:Lf_PopupWidth = 0.75
let g:Lf_PopupPreviewPosition = 'top'

" the icons are all ?, may need some extensions,
" you should install nerd-font for your terminal.
let g:Lf_ShowDevIcons = 1

" use version control to find root directory
let g:Lf_WorkingDirectoryMode = 'AF'
let g:Lf_RootMarkers = ['.git', '.svn', '.hg', '.project', '.root']

" those will be ignored
let g:Lf_WildIgnore = {
    \ 'file': ['*.vcproj', '*.vcxproj', '*.exe', '*.so', '*.dll', '*.o', '*.obj'],
    \ 'dir': ['build', 'tmp', '.git']}

" show hidden files, this is helpful when you are editing configure files.
let g:Lf_ShowHidden = 1

" use ^P to open
let g:Lf_ShortcutF = '<C-P>'
inoremap <C-p> <Esc>:LeaderfFile<CR>

" when find symbols we don't ignore dotfiles,
" we'll ignore .git and LICENSE
let g:Lf_RgConfig = [
    \ "--hidden",
    \ "--glob=!.git/*",
    \ "--glob=!LICENSE",
\ ]

" we use <C-f> to find symbols in files.
nnoremap <C-f> :Leaderf rg<CR>
inoremap <C-f> <ESC>:Leaderf rg<CR>
```

### YouCompleteMe
**注意：我目前已经不再使用`ycm`，而是使用`coc`，可以在文末的`git`仓库中查看最新的插件配置。由于使用了`coc`很多之前的插件可以使用`coc-extensions`进行替代因此文中的部分插件可能也不再使用，但并没有一一标明。**

这是一个自动补全的插件，基于`LSP`实现（不过目前没有`python`的代码诊断功能，如果经常写`python`，那么我推荐你选择其他的自动补全工具，例如`ale`）。

这个插件的安装稍微复杂一点，因为他在通过`vim-plug`安装后还需要自己编译一部分的代码，而且按照官方给的步骤执行可能会出现某些代码的补全装不上的情况（我直接安装所有的话，我对`go lang`补全始终不能安装成功），建议选择自己需要的语言进行安装，例如我需要安装对于`C\C++`代码补全的支持，我只需要执行以下命令即可：
```bash
sudo apt install build-essential cmake vim-nox python3-dev
# if you want install ycm as root, you need add --force-sudo
# you should replace ~/.vim/plugged/YouCompleteMe with where your ycm is.
cd ~/.vim/plugged/YouCompleteMe && python3 install.py --clangd-completer
```

**注意：安装的时候需要确保你不处于任何`conda`的虚拟环境中，也就是说你需要执行几次`conda deactivate`以确保你看不到`base`等`conda`环境提示。通过`conda`环境安装可能会失败。**

接下来是我的`ycm`配置：
```vim
" use <F5> to refresh
nnoremap <F5> :YcmForceCompileAndDiagnostics<CR>

" use gd to go to definition
nnoremap gd :YcmCompleter GoTo<CR>
" nnoremap gh <Nop>
nnoremap gh :YcmCompleter GoToAlternateFile<CR>
" use gr to go to references
nnoremap gr :YcmCompleter GoToReferences<CR>
" use gc to go to callers, find those who call this function
nnoremap gc :YcmCompleter GoToCallers<CR>

" use <Leader>R to refactor rename
nnoremap <Leader>R :YcmCompleter RefactorRename 

" use H as fix it command
nnoremap H :YcmCompleter FixIt<CR>

" use <Leader>d show doc for current symbol
nnoremap <Leader>d <plug>(YCMHover)

let g:ycm_extra_conf_globlist = ['~/.vim/vim_plugin_config/ycm_extra_config_global.py']
let g:ycm_seed_identifiers_with_syntax = 1
let g:ycm_collect_identifiers_from_comments_and_strings = 1
let g:ycm_confirm_extra_conf = 0
let g:ycm_key_list_select_completion = ['<C-j>', '<TAB>']
let g:ycm_key_list_previous_completion = ['<C-k>', '<S-TAB>']
let g:ycm_min_num_of_chars_for_completion = 1
let g:ycm_auto_hover = ''
let g:ycm_enable_semantic_highlighting = 1

" add # to trigger for C-family language
" input two characters will trigger ycm for c and cpp files
let g:ycm_semantic_triggers =  {
    \   'c': ['->', '.', '#', 're!w{2}'],
    \   'objc': ['->', '.', 're!\[[_a-zA-Z]+\w*\s', 're!^\s*[^\W\d]\w*\s',
    \            're!\[.*\]\s'],
    \   'ocaml': ['.', '#'],
    \   'cpp,cuda,objcpp': ['->', '.', '::', '#', 're!w{2}'],
    \   'perl': ['->'],
    \   'php': ['->', '::'],
    \   'cs,d,elixir,go,groovy,java,javascript,julia,perl6,python,scala,typescript,vb': ['.'],
    \   'ruby,rust': ['.', '::'],
    \   'lua': ['.', ':'],
    \   'erlang': [':'],
    \ }
" let ycm complete markdown files
let g:ycm_filetype_blacklist = {
    \ 'tagbar': 1,
    \ 'notes': 1,
    \ 'netrw': 1,
    \ 'unite': 1,
    \ 'text': 1,
    \ 'pandoc': 1,
    \ 'infolog': 1,
    \ 'leaderf': 1,
    \ 'mail': 1
    \}
```

上面的配置中最重要的是`ycm_semantic_triggers`中的`re!w{2}`这表示输入任意两个字符都会进行语义补全，而不是只有输入指定的字符才会进行语义补全（现在的`ycm`是异步的，所以补全的速度非常快）。

使用`ycm`后的效果如下：

![](https://img-blog.csdnimg.cn/img_convert/e999050f2dc0016d2a25358db649901d.png)

如果你的`C/C++`项目构建过程中能够生成`compile_commands.json`，那么`ycm`可以自动找到当前目录及其子目录下的该文件，根据里面的编译命令对于进行提示以及纠错，如果你没有这个文件，那么你可以通过`ycm_extra_conf_globlist`来设置你的自定义命令，我的设置文件如下（优先使用`compile_commands.json`）：
```python
def Setting(**kwargs):
    return {
        'flags' : ['-x', 'c++', '-Wall', '-Wextra', '-std=c++17'],
    }
```

### NerdTree
这是一个文件树插件，我的配置如下：
```vim
nnoremap <C-e> :NERDTreeToggle<CR>
inoremap <C-e> <Esc>:NERDTreeToggle<CR>

" Exit Vim if NERDTree is the only window remaining in the only tab.
autocmd BufEnter * if tabpagenr('$') == 1 && winnr('$') == 1 && exists('b:NERDTree') && b:NERDTree.isTabTree() | quit | endif
" Close the tab if NERDTree is the only window remaining in it.
autocmd BufEnter * if winnr('$') == 1 && exists('b:NERDTree') && b:NERDTree.isTabTree() | quit | endif
" If another buffer tries to replace NERDTree, put it in the other window, and bring back NERDTree.
autocmd BufEnter * if winnr() == winnr('h') && bufname('#') =~ 'NERD_tree_\d\+' && bufname('%') !~ 'NERD_tree_\d\+' && winnr('$') > 1 |
    \ let buf=bufnr() | buffer# | execute "normal! \<C-W>w" | execute 'buffer'.buf | endif
" Open the existing NERDTree on each new tab.
autocmd BufWinEnter * if &buftype != 'quickfix' && getcmdwintype() == '' | silent NERDTreeMirror | endif

" show dot files
let g:NERDTreeShowHidden = 1
" set nerdtree sort by numerical
let g:NERDTreeNaturalSort = 1
```

配置好后的效果如下图所示：

![](https://img-blog.csdnimg.cn/img_convert/d17623a93f1a86c4c17a9c9ae575eb99.png)

### onedark
这是一个主题插件，你可以自己选择自己喜欢的主题。

### vimpolyglot
这是一个增强语法高亮效果（也支持自动缩进等功能）的插件，这个插件我没有进行额外的配置。

我选择这个插件主要是因为我发现我的`C++`代码中`std::vector`没有高亮，我装了这个插件他便成功高亮了。

### undotree
这款插件能够保存你对文件的所有操作记录，你可以跳转到任意一个版本。这个软件主要用来解决需要撤销操作的时候，在`vim`中只能保存一条撤销路径（很多编辑器都是这样），新的路径会覆盖掉旧的路径，导致你不能回到文件的任意位置，而`undotree`就可以实现回到文件的任意位置，你也可以将记录持久化下来，这样你重新打开文件后，你甚至还能回到很久之前的状态，我的配置如下（我的配置会为所有打开的文件保存撤销记录）：
```vim
" use ^Q to open undotree
nnoremap <C-q> :UndotreeToggle<CR>:UndotreeFocus<CR>
inoremap <C-q> <ESC>:UndotreeToggle<CR>:UndotreeFocus<CR>
if has("persistent_undo")
   let target_path = expand('~/.undodir')
    " create the directory and any parent directories
    " if the location does not exist.
    if !isdirectory(target_path)
        call mkdir(target_path, "p", 0700)
    endif
    let &undodir=target_path
    set undofile
endif
```

设置好后打开`undotree`的效果：

![](https://img-blog.csdnimg.cn/img_convert/2b25a442597064184ad852cee56e15e6.png)

可以看到上图中的多条路径。

### tagbar
这个插件是查看大纲所需要的（显示`markdown`标题，显示函数声明等）。我的配置如下：
```vim
" save files before open tagbar
fun! BeforeTagbarSaveCheck()
    if &buftype == ''
        exec "w"
    endif
endf
nnoremap <C-w> :call BeforeTagbarSaveCheck()<CR>:TagbarToggle<CR>
inoremap <C-w> <Esc>:call BeforeTagbarSaveCheck()<CR>:TagbarToggle<CR>

" once open, cursor is in tagbar window
let g:tagbar_autofocus = 1
" open at left
let g:tagbar_position = 'topleft vertical'
" when tagbar is the last window, close it automatically
let g:tagbar_autoclose_netrw = 1
" show line numbers of functions
let g:tagbar_show_linenumbers = 1
" Add support for markdown files in tagbar.
let g:tagbar_type_markdown = {
    \ 'ctagstype': 'markdown',
    \ 'ctagsbin' : '~/.vim/autoload/markdown2ctags.py',
    \ 'ctagsargs' : '-f - --sort=yes --sro=»',
    \ 'kinds' : [
        \ 's:sections',
        \ 'i:images'
    \ ],
    \ 'sro' : '»',
    \ 'kind2scope' : {
        \ 's' : 'section',
    \ },
    \ 'sort': 0,
\ }
" Add support for vimwiki files in tagbar.
let g:tagbar_type_vimwiki = {
    \ 'ctagstype': 'vimwiki',
    \ 'ctagsbin' : '~/.vim/autoload/markdown2ctags.py',
    \ 'ctagsargs' : '-f - --sort=yes --sro=»',
    \ 'kinds' : [
        \ 's:sections',
        \ 'i:images'
    \ ],
    \ 'sro' : '»',
    \ 'kind2scope' : {
        \ 's' : 'section',
    \ },
    \ 'sort': 0,
\ }
```

需要注意的`tagbar`并不是原生的支持`markdown`文件查看大纲，因此你需要添加倒数第二项并通过指定脚本的位置来将`markdown`的符号转成`ctag`。你唯一需要修改就是`ctagsbin`的路径，你可以通过[markdown2ctags.py](https://github.com/Kaiser-Yang/dotfiles/blob/main/.vim/autoload/markdown2ctags.py)获取脚本。

配置好后的效果如下图所示：

![](https://img-blog.csdnimg.cn/img_convert/7e68102078629510f3e8aa479cde7256.png)

### vimgitgutter
该插件会在你用`vim`打开一个`git`仓库的文件时，显示当前文件每一行的状态（是否被更改，是否新增等）。我为没有为该插件进行单独的配置，使用的是模式配置：
* 通过`]c,[c`跳转到下一次更改和上一次更改；
* 通过`<leader>h`开头的相关快捷键可以快速还原（`u`）或者暂存（`s`）。

打开一个修改过得`git`仓库中的文件效果如下：

![](https://img-blog.csdnimg.cn/img_convert/b8b87e64fdc0ca93cf77e9c9fb185573.png)

### nerdcomment
这是一个可以注释多行的插件，我使用的快捷键是该插件默认的快捷键，我的配置文件如下：
```vim
" Create default mappings
let g:NERDCreateDefaultMappings = 1

" Add spaces after comment delimiters by default
let g:NERDSpaceDelims = 1
" Use compact syntax for prettified multi-line comments
let g:NERDCompactSexyComs = 1
" Align line-wise comment delimiters flush start instead of following code indentation
let g:NERDDefaultAlign = 'start'
" Add your own custom formats or override the defaults
let g:NERDCustomDelimiters = { 'c': { 'left': '/**','right': '*/' } }
" Allow commenting and inverting empty lines (useful when commenting a region)
let g:NERDCommentEmptyLines = 1
" Enable trimming of trailing whitespace when uncommenting
let g:NERDTrimTrailingWhitespace = 1
" Enable NERDCommenterToggle to check all selected lines is commented or not 
let g:NERDToggleCheckAllLines = 1
" This enables you can comment for closed () and {}
nnoremap <silent> <leader>c% V%:call nerdcommenter#Comment('x', 'toggle')<CR
```

虽然这个插件的快捷键很多但我一般只用`<leader>c<leader>`也就是批量注释或者批量取消注释，进入`Visual Line`模式选择多行后执行，或者在`normal`模式下键入数字后执行则是将当前行开始时的多行注释或者取消注释。

指的一提的是如果你当前光标在一个括号上面我将`<leader>c%`绑定成了注释一个闭括号区域，这对于注释有括号包围的局部代码很好用，例如我要注释某个函数，我只需要跳到函数的开头，执行`f{`查找大括号的位置，然后执行`<leader>c%`即可将整个函数注释。

### vimwhichkey
这个插件可以用来提示你都进行了哪些按键绑定，但是这个插件并不能是我完全满意。

目前我只设置了`<leader>`按键的提示，因为其他按键的提示可能会出问题，设置后按下空格，在一段时间后便会弹出窗口，在窗口中你可以继续键入按键以获得进一步提示或者执行命令。

这个插件不能获取数字前缀，也就是说，如果我想通过`20<leader>c<leader>`注释`20`行代码，那么我第一次按下空格后，必须要在窗口弹出之前键入完成后续的`c<leader>`，否则等窗口弹出后我键入后续命令则只会对当前行注释。

我的配置如下：
```vim
set timeoutlen=500
let g:mapleader = "\<Space>"
let g:maplocalleader = ','
nnoremap <silent> <leader> :<c-u>WhichKey '<Space>'<CR>
```

效果如下图所示：

![](https://img-blog.csdnimg.cn/img_convert/228fdc9a9b67b28cc2572d214703c5cf.png)

### nerdtreegitplugin
该插件用于显示文件树（`nerdtree`）中的文件状态（前提是文件是属于一个`git`仓库），我的配置如下：
```vim
let g:NERDTreeGitStatusIndicatorMapCustom = {
                \ 'Modified'  :'✹',
                \ 'Staged'    :'✚',
                \ 'Untracked' :'✭',
                \ 'Renamed'   :'➜',
                \ 'Unmerged'  :'═',
                \ 'Deleted'   :'✖',
                \ 'Dirty'     :'✗',
                \ 'Ignored'   :'☒',
                \ 'Clean'     :'✔︎',
                \ 'Unknown'   :'?',
                \ }
let g:NERDTreeGitStatusShowIgnored = 1
let g:NERDTreeGitStatusConcealBrackets = 1
```

显示的效果如下图：

![](https://img-blog.csdnimg.cn/img_convert/d8cd11642953a817efbbd0648430e281.png)

### markdownpreview
顾名思义，可以预览`markdown`的效果（如果你是`WSL`用户你需要安装`wslu`，`sudo apt install wslu`，其他用户需要你装有浏览器）。

我没有对该插件进行额外的配置，通过基础部分的配置，我可以通过`<leader>r`来为`markdown`文件快速启动预览，效果如下图：

![](https://img-blog.csdnimg.cn/img_convert/a2b06fcd45d4bee6c2660d2b209b0a63.png)

### vimwiki
这个插件可以为你管理所有的`markdown`文件，如果你想要使用`vim`编写`markdown`文件的话，那么我非常推荐你通过该插件管理你的`markdown`文件（`by the way`这个插件也是中国人写的），它能够为你创建一个索引文件，你通过回车可以快速创建或者进入另一个文件，通过退格（`normal`模式）可以返回上一级。也就是你的`markdown`文件变成了一本书。而你可以根据你`markdown`文件记录的内容差异创建多本书。

我的配置文件如下：
```vim
let g:vimwiki_list = [
\ {
    \ 'path_html': '/mnt/e/vimwiki/programming/html',
    \ 'path': '/mnt/e/vimwiki/programming',
    \ 'syntax': 'markdown',
    \ 'ext':'.md',
    \ 'custom_wiki2html': '~/.vim/autoload/wiki2html.sh'
\ },
\ {
    \ 'path_html': '/mnt/e/vimwiki/blog/html',
    \ 'path': '/mnt/e/vimwiki/blog',
    \ 'syntax': 'markdown',
    \ 'ext':'.md',
    \ 'custom_wiki2html': '~/.vim/autoload/wiki2html.sh'
\ }]

" disable html convertion default key bindings.
let g:vimwiki_key_mappings =
    \ {
    \   'all_maps': 1,
    \   'global': 1,
    \   'headers': 1,
    \   'text_objs': 1,
    \   'table_format': 1,
    \   'table_mappings': 1,
    \   'lists': 1,
    \   'links': 1,
    \   'html': 0,
    \   'mouse': 0,
    \ }

" treat all .md files as vimwiki
" this will make input in markdown files more easily
let g:vimwiki_global_ext = 1

" you should not let a single markdown file be seen as vimwiki,
" and you should not add links for that,
" if you want another index, you should add another config.
let g:vimwiki_ext2syntax = {}

" don't conceal links
let g:vimwiki_conceallevel = 0

" use <LEADER>wh convert all files to html files
autocmd Filetype vimwiki nnoremap <LEADER>wh :VimwikiAll2HTML<CR>
```

在我的配置中我设置了支持`markdown`语法，创建了两个索引目录，一个用来记录编程相关的笔记，一个用来记录博客相关的内容。

在打开的索引中，我只需要将鼠标移动到连接为止，通过回车便可以打开当前这篇博客的文件：

![](https://img-blog.csdnimg.cn/img_convert/a684f47c3487765ee0fcf3ed223ed2fa.png)

![](https://img-blog.csdnimg.cn/img_convert/abbbb4a9e9d9c6fe31344fd56157e89c.png)

**注意：最好不要让文件名有中文和特殊符号（最好不要有`.`，不然导出`html`可能会出错）建议先写出英文文件名字，然后进入`Visual`模式选择后回车进入文件后保存后再回到上级更改方括号中的描述即可。**

**注意：你可以通过[markdown2ctags.py](https://github.com/Kaiser-Yang/dotfiles/blob/main/.vim/autoload/markdown2ctags.py)（需要你有`pandoc`，我推荐使用`conda intall pandoc`进行安装，这样安装的版本比较新）将你的所有`markdown`文件转换成网页。你需要在配置文件中指定该脚本的位置。**

### indentline
这是一个线索缩进位置的插件，我只是关闭了未知文件和`markdown`文件的显示：
```vim
autocmd FileType markdown let g:indentLine_setConceal = 0
autocmd FileType markdown :IndentLinesDisable
autocmd FileType vimwiki let g:indentLine_setConceal = 0
autocmd FileType vimwiki :IndentLinesDisable
" If you want this work properly, you should set filetype for those
" whose filetype is ''
autocmd FileType none let g:indentLine_setConceal = 0
autocmd FileType none :IndentLinesDisable
```

效果如下图：

![](https://img-blog.csdnimg.cn/img_convert/68666460fa23513d6cf1e5fba9313843.png)

### autopairs
这是一个括号的自动补全插件，我的配置如下：
```vim
" use ^B to add brackets when mismatch
let g:AutoPairsShortcutBackInsert = '<C-b>'
let g:AutoPairsFlyMode = 1

" use ^A to auto wrap
let g:AutoPairsShortcutFastWrap = '<C-a>'

" double quotation is vim's comment sign, so remove it from auto pairs of vim files
autocmd Filetype vim let b:AutoPairs = {'(':')', '[':']', '{':'}', "'":"'", "`":"`", '```':'```', '"""':'"""', "'''":"'''"}
```

这里面的`FlyMode`我认为非常好用，例如我的鼠标指针在`{(())}`的正中间，我只需要按一次`}`便可以跳到最末尾。`FlyMode`如果用的不熟练可能会让你非常恼火，如果你不熟悉，建议你关闭`FlyMode`模式，并删除`<C-a>`的映射。

### vimsurround
这个一个快速更改括号的插件，我没有进行额外的配置，我常用的操作如下（以括号举例，也可以是引号，尖括号等）：
* `cs({`：将包围当前的小括号换成大括号；
* `ds(`：删除包围当前的小括号；
* `yss(`：为当前整行添加小括号（从非空字符开始）；
* 在`visual`模式选择后键入`yS(`：为选择的部分增加小括号；
* `ySS{`：为当前行增加大括号并换行缩进；

**注意：上述的所有操作如果是输入左括号，那么会在括号的内部两侧增加或者删除一个空格，而如果使用右括号则不会增加或者删除空格。**

### tabular
这是一个对齐的插件，他可以将你的代码依据等号、冒号或者竖线对齐，我的配置如下：
```vim
nnoremap <Leader>a= :Tabularize /=<CR>
vnoremap <Leader>a= :Tabularize /=<CR>
nnoremap <Leader>a: :Tabularize /:<CR>
vnoremap <Leader>a: :Tabularize /:<CR>
nnoremap <Leader>a<Bar> :Tabularize /<Bar><CR>
vnoremap <Leader>a<Bar> :Tabularize /<Bar><CR>
```

对齐效果如下图：

![](https://img-blog.csdnimg.cn/img_convert/2e6d5ecaf6e4f3aed9fe52f4eaba4d13.png)

### markdownhelper
这并不是一个插件，而是一些关于写`markdown`的一些快捷键。

在`markdown`文件中我们往往需要加粗字体，那么我们需要输入四个`*`这非常的麻烦，而有了这个文件里面的配置，我们只需要输入`,b`（速度略快一点），那么就会插入`****<++>`此时你的指针在四个`*`的正中间，这个时候你输入内容输入完成后按下`,f`（速度略快一点），那么就会变成`**contents**`这个时候你的指针来到了星号的最后，你可以继续输入了，这只是一个简单的内容，完整的配置文件如下（这一部分的思路来自`TheCW`，建议可以看他介绍`vim`的视频）：
```vim
" this expiration comes from https://github.com/theniceboy/.vim/blob/master/snippits.vim
" if you press quickly, it will trigger, otherwise, it just add two characters
" this is resonable, in english, we usually add space after comma rather than letters.
" find next placeholder and remove it.
autocmd Filetype markdown inoremap ,f <Esc>/<++><CR>:nohlsearch<CR>c4l
" bold
autocmd Filetype markdown inoremap ,b ****<++><Esc>F*hi
" italic
autocmd Filetype markdown inoremap ,i **<++><Esc>F*i
" for code blocks
autocmd Filetype markdown inoremap ,c <CR><CR>```<CR><CR>```<CR><CR><++><Esc>4kA
" for pictures, mostly, we don't add pictures' descriptions
autocmd Filetype markdown inoremap ,p <CR><CR>![]()<++><Esc>F(a
" for for links <a> are html links tag, so we use ,a
autocmd Filetype markdown inoremap ,a [](<++>)<++><Esc>F[a
" for headers
autocmd Filetype markdown inoremap ,1 #<Space>
autocmd Filetype markdown inoremap ,2 ##<Space>
autocmd Filetype markdown inoremap ,3 ###<Space>
autocmd Filetype markdown inoremap ,4 ####<Space>
" delete lines
autocmd Filetype markdown inoremap ,d ~~~~<++><Esc>F~hi
" tilde
autocmd Filetype markdown inoremap ,t ``<++><Esc>F`i
" math formulas
autocmd Filetype markdown inoremap ,M <CR><CR>$$<CR><CR>$$<CR><CR><++><Esc>3kA
" math formulas in line
autocmd Filetype markdown inoremap ,m $$<++><Esc>F$i
" newline but not new paragraph
autocmd FileType markdown inoremap ,n <br><CR>
" use mouse in markdown file,
" this is useful when you are writing Chinese
" which is inconvenient to navigate with f/F or t/T.
autocmd FileType markdown set mouse+=a
```

## Markdown的番外篇，插入图片
众所周知，如果使用`typora`那么你截取图片后，你可以直接将这个图片复制到`markdown`文件中，而`typora`会根据你的设置来将图片保存到你指定的位置。并自动插入到图片的链接。

如果用`vim`写`markdown`遇到需要插入图片应该怎么办？能不能也有这么方便的方法？

> 答案是肯定的，如果你使用的不是`WSL`那么你可以直接去下载一个插件，他可以将剪切板的图片直接拷贝到你指定的目录，并在你的`markdown`文件中增加图片链接（你只需要动动小手去搜搜一下即可完成）。

如果是`WSL`怎么办呢？

> 不幸的是`WSL`目前并不能从`Windows`的剪切板中读取图片（至少我目前并没有找到可行的方法）。但是依然有可以替代的方法：图床和`PicGo`。<br>
> 我在`github`上面创建了一个仓库作为我的图床并将其与我的`PicGo`相关联（如何创建`github`的图床，网上资料一找一大堆，整个过程只要三四分钟）。之后我每次截图后，我只需要按下`<C-g>`（我的`PicGo`读取剪切板图片的快捷键，可以在`PicGo`的设置中进行设置）等待上传完后，图片的`markdown`链接形式将会自动复制到我的剪切板，这个时候我只需要在我的`markdown`文件中点击鼠标右键（如果你开了`vim`中的鼠标操作，那么你需要在点击右键之前按住`<Shift>`），图片的链接变成功拷入到`marrkdown`文件中了。

这是我目前在`WSL`环境中能够找到的最接近完美的解决方案了，希望`WSL`早日支持从`Windows`剪切板读取图片。

如我我的图片太多了，超过了`github`仓库的容纳上限怎么办？
> 首先不用过于焦虑，因为通常截图的图片大小很小，要几千上万张才能达到仓库的容量上限。其次如果对于已经有链接的图片你可以直接选择复制图片链接，这样你可以在`markdown`文件中键入`,p`然后将链接拷入最后键入`,f`（如果你是按照我的快捷键配置的话）。<br>
> 当然图片还是可能会存满的，为此我们只需要知道我们该方案解决的是不好在`vim`中放入图片的操作，而不是真的需要一个图床，如果后续有一个脚本能够将这些链接中的图片下载到本地，并且自动更改我的`markdown`文件中的链接为本地位置，那么我们只需要隔几个月或一年（取决你图片的多少）通过这个脚本将图片拉取下来并更新，之后删除仓库中的图片即可，我已经写好了该脚本：[replace_md_image.py](https://github.com/Kaiser-Yang/dotfiles/blob/main/replace_md_image.py)。

## 自动配置脚本
最后简单介绍一下自动配置脚本的思路。

### 目标
* 能够自动配置好`vim`的一切，并且在该过程中认为干预越少越好。
* 如果要更改配置文件，可以直接更改后生效，而不需要重新进行整个配置过程。

### 实现方法、实现过程
1. 首先我们需要将原始的文件进行备份，这样即使脚本执行失败，我们也可以恢复到默认的配置（因此需要备份和恢复两个基本功能）；
2. 能够自动的拉去所有的`vim`需要的仓库库等，因此需要一个`init`功能进行安装；
3. 后续如果只是更改配置文件或者单独增加插件，我们只需要将新的配置文件拷贝过去，而不是完全重新构建，因此需要一个功能用于实现这一点（这也是该脚本的默认功能，因为我发现该功能最常用，所以将其设置为了默认功能）；

如果想要了解具体的实现，可以查看[dot_files.py](https://github.com/Kaiser-Yang/dotfiles/blob/main/dot_files.py)。

### 脚本的行为
执行脚本之前有以下几点需要保证：
* 确保你的工作目录为脚本所在目录；
* 确保你自定义的文件与脚本在同一目录；
* 你一般需要更新脚本中的`copy_file`变量，将你家目录下不存在的配置文件放入其中，在`copy_file`中的文件会被覆盖而不是被追加，没在`copy_file`中的文件将会通过追加的方式更新家目录下的文件；
* `ignore_file`控制哪些文件会被忽略（既不备份也不更改）。

了解上述的要求后，在完成配置后可以通过`python3 dot_files.py init`进行第一次的安装，在这个工程中你需要依据提示键入几次回车，并在弹出的`vim`界面安装完成插件后通过`:qa`关闭`vim`窗口（你需要确保你能成功从`github`拉取仓库）。过程中你可能需要输入几次用户密码（这是升级`vim`需要的操作）。

**注意：脚本默认会安装并配置`fish`为默认终端，如果你不需要这样做，你需要注释掉对`install_fish()`函数的调用。**

如果你要修改配置，那么我建议你直接将仓库`fork`下来，这样你可以直接在这个仓库中更改配置，更改完成后你只需要通过`python3 dot_files.py`用当前仓库的配置文件去更改你的家目录下的配置文件（如果你安装的新的插件且使用的是`vim-plug`作为你的插件管理器，那么你可能还需要执行`vim +PlugInstall`）。

**注意：强烈建议阅读代码中的注释你确保你清楚脚本会做什么事情，如果你不信任该脚本，你应该提前将你的配置文件备份。**

# 最后的话
我不建议你直接拉取任何人的配置直接使用，也不建议你直接执行我的代码，你在执行我的代码之前应该认真阅读注释以确保你明白我的代码在做什么事情，以及出错了应该如何操作。如果你会写`python`那么你完全可以按照你自己的喜好自己设计自动配置脚本，或者根据已有的脚本进行改进，而不是在直接拿来就用。

最后如果你在学习`vim`的过程中有一个人能够实时提供给你帮助，那么这是非常方便的，我在这个过程中没有这样的人，大部分的资料来自于搜索引擎，少部分的资料来自`AI`（我的`AI`太笨了，给我的答案经常都在胡说，这让我很恼火，我决定后面换个`AI`）。

如果你真的想学会`vim`，那么你是一定需要下功夫的——`vim`的简单使用很快就能学会，但却要花一生的时间去掌握。

最后一件不怎么重要的事情，我的配置文件仓库（如果你认真看了本文，你应该可以自己管理自己的配置文件仓库）：[Kaiser-Yang/dotfiles](https://github.com/Kaiser-Yang/dotfiles)。

# 未来的更新
每次增加一个插件就来更新一次博客是很麻烦的事情，因此可以通过查看`git`仓库的日志来确定有哪些更新，目前除了更正错误外不会再新增插件的配置以及新的插件。从`50e68d4168f98a17e65a12bf96ff3b2e928dd7b9`开始（包含）的提交将不会被更新在本文中。

下图是部分新增的仓库提交：

![](https://img-blog.csdnimg.cn/img_convert/ae55ba176ae06c8c79303b62cbbe4752.png)
