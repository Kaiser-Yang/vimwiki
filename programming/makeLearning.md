**Note: this is the notes when learning `make`. Because I use `cmake` more, this note is not resonable enough, and I just put some commands down and don't explain the basic methodology of make.**

**Note: even you can use `cmake` to build `C/C++` projects, learning `make` is still necessary. Because you can use `make` to do other builds when necessary, especially when the projects are not well-supported by tools.**

**Note: the code blocks' commands are not started with TAB, if you want to run these codes, you may need to replace the spaces with TAB.**

# Basic Knowledge of Makefile

## Variables
You can define variables in `Makefile` and use the variables through `$(variable)`:

```Makefile
objects = obj1.o obj2.o obj3.o
all: $(object)
    $(CC) $(object) -o main
```

If you want to express the real `$`, you can use `$$`.

If you want to define a variable whose value is a single space, you can use:

```Makefile
nullstring :=
space := $(nullstring) # end of line
```

The `$nullstring` is a empty string, and `space` is a single space. Note that the `#` and a single space before `#` are necessary.

Note that the trailing spaces will be added to variables.

You can nest `$` to get value:

```Makefile
x = y
y = z
z = u
# $(x) is y
# $($(x)) is $(y), and $(y) is z
# $($($(x))) is $(z), and $(z) is u
a := $($($(x)))
```

### Target Variables
You can set variables only valid for the specified target:

```Makefile
prog : CFLAGS = -g
prog : prog.o foo.o bar.o
        $(CC) $(CFLAGS) prog.o foo.o bar.o
prog.o : prog.c
        $(CC) $(CFLAGS) prog.c
foo.o : foo.c
        $(CC) $(CFLAGS) foo.c
bar.o : bar.c
        $(CC) $(CFLAGS) bar.c
```

In the example above, only the `prog`'s `CFLAGS` is `-g`.

### = := += ?=
There are many equal signs in `make`, but they are very different with each other:
* `=`: this is like references in `C/C++`, for example, if you use `a = $(b)`, once `b`'s value changed after, the `a` will change too.
* `:=`: this is assignment equal sign, this is similar with `=` in `C/C++`.
* `+=`: this is to append a variable with new values.
* `?=`: this will check if the variable is assigned before, if it is, this will not work, otherwise it is similar with `=`.

For the `+=`:
* If the variable has not been defined, it will be `=`.
* If the variable has been define, it will follow the last equal sign. If the last equal is `=`, `+=` will use `=`; if the last equal sign is `:=`, `+=` will use `:=`.

### override
When the variable is defined by the `make` command, for example `make a=12` will define a variable called `a` whose value is `12`, the variable defined and assigned in your `Makefile` will be replaced by the command line's. If you don't want the variable be replaced, you can use `override`:

```Makefile
# you can use other equal signs
override a := 0
```

### Multi Line Variables
You can define multi line variables by `define`, the signature after `define` is the name of the variable. Note that commands in macro must start with tab, so if the lines are not started with tab, they will be treated as a multi line variable's value. There is an example below:

```Makefile
define two-lines
echo foo
echo $(bar)
endef
```

### $@ $< $* $% $? $^ $+
`$@` is a variable in `make`. Its value is the target. For example, if `$@` appears in commands following `main.o: main.cpp`, `$@` will be `main.o` exactly.

`$<` is a variable in `make`. Its value is the first dependency, if `$<` appears in commands following `main.o: main.cpp`, `$<` will be `main.cpp` exactly.

`$*` means the match's `%` and the string before `%`. For example, using `$*` following `pre_%.o: pre_%.c`, it will be `pre_%`.

`$%` when the target is in a lib, this variable is the names of members. For example, if a target is `foo.a(bar.o)` the `$%` will be `bar.o` and the `$@` will be `foo.a`.

`$?` means all the files which are newer than the target.

`$^` means all the dependencies of the target, this will have only one copy if the target depends on the same file more than once.

`$+` is similar with `$^` but will store the repetitive files.

## Auto Deduction
You can use `Makefile` with auto deduction. Auto deduction looks like this:

```Makefile
main.o: header1 header2 header3
```

The example above will add `main.c` or `main.cpp` for target `main.o`, and the command `$(CC) $(CPPFLAGS) $(CFLAGS) -c main.cpp header1 header2 header3 -o main.o` will be added automatically too.

## PHONY
`.PHONY` is to specify the target is a pseudo target. This is usually used for `clean` and `all`. `.PHONY` will let `make` not treat the target as a file:

```Makefile
.PHONY: clean
```

## include
`include` is very similar with the `include` in `C/C++`. The command will read the files' contents and put them where the `include` command is:

```Makefile
# this will read the contents of config.make and put it there.
include config.make
```

`make` can also use the `-I` to specify where to find the files used by `include` command, for example `make -I./include` will let `include` command find the `./include` directory.

## Wildcards
There are three types of wildcards in `make`: `*`, and `?`. They have the same meaning with `bash` of `Linux`. These wildcards can be used in commands, dependencies, and variables:

```Makefile
# wildcards in dependencies and commands
main: *.o
    g++ *.o -o main
.PHONY: clean
clean:
    rm -f *.o
# wildcards in variables
objects = *.o
```

Note that `objects = *.o` will let the variable's value be `*.o` rather than the exact object files found.

If you want `objects` to be the exact object files found, you can use `objects := $(wildcard *.o)`.

## VPATH
This variable is built-in within `make`, which is used to specify where to find files. This variable's value is separated with colon, and each item is a path:

```Makefile
# this will specify two directories to be used to find files
VPATH = src:../include
```

## vpath
`vpath` is a keyword of `make`, and this keyword also can be used to find files:

```Makefile
# % is similar with .* of regexpr
# this is to specify to find header files in ../include
vpath %.h ../include

# this is to specify to find C files in ./src
vpath %.c ./src
```

## Multi Targets
You can write more than one target in one line:

```Makefile
bigoutput littleoutput : text.g
	generate text.g -$(subst output,,$@) > $@
```

`-$(subst output,,$@)` means substitute `output` of `$@` with empty string which is specified by `,,`. The hyphen before means to ignore errors. `make` will check the return value after each command. When return value is non-zero, `make` will stop, the hyphen means don't check the return value.

More straightforward, the commands above will be convert to:

```Makefile
bigoutput : text.g
	generate text.g -big > bigoutput
littleoutput : text.g
	generate text.g -little > littleoutput
```

## Static Pattern Rules
In `make`, you can use static pattern rules to simplify the `Makefile`:

```Makefile
objects = foo.o bar.o
all: $(objects)
$(objects): %.o: %.c
	$(CC) -c $(CFLAGS) $< -o $@
```

`$(objects): %.o: %.c` will find `foo.o` and `bar.o` **from `$(objects)`** and place them at `%.o`, and then find `foo.c` and `bar.c` **from `foo.o bar.o`**.

There is another example to use `filter` and static pattern rules:

```Makefile
files = foo.elc bar.o lose.o
# $(filter %.o,%(files)) will get all the .o files from $(files)
$(filter %.o,$(files)): %.o: %.c
	$(CC) -c $(CFLAGS) $< -o $@
$(filter %.elc,$(files)): %.elc: %.el
	emacs -f batch-byte-compile $<
```

## Generate Dependencies Automatically
`gcc -MM *.c` will print the header files of `C` files, for example `gcc -MM test.c`'s output will look like this:

```
test.o: test.c header1 header2 header3
```

We can use the command to automatically add dependencies for a target:

```Makefile
# @ will not print the commands
%.d: %.c
    @set -e; \
    rm -f $@; \
    $(CC) -MM $< > $@.; \
    sed -e 's/\($*\)\.o[ : ]*/\1.o $@ :/g' < $@. > $@; \
    rm -f $@.
```

We use `$(CC) -MM $< > $@.` to generate a file `%.d.` to store the output. Then we use `sed` to substitute the `%.o` with `%.o %.d` and output to `%.d`. Finally we remove the `%.d.`.

After this, we can include the `%.d` files:

```Makefile
sources = a.c b.c
# substitude .c with .d in sources
include $(sources:.c=.d)
```

After `include`, we'll have something like `a.o a.d: a.c header` in our `Makefile`, then `make` will auto deduction the commands.

## Nested Makefiles
If you use 3rd parties, you want to build the 3rd parties by `make`:

```Makefile
subsystem:
    cd subdir $$ $(MAKE)
```

You can pass the variables of current `Makefile` to `sub-Makefile` through `export`:

```Makefile
export variable = value
# pass all variables
export
```

## define
In `make`, you can use `define` to define macros:

```Makefile
# note that there is no semicolon after each command
define name
    command1
    command2
    ...
endef
```

If you want to use the macro, you just need use `$(macro_name)` to call the macro.

## Conditional Structures
This part is simple, so I just post some examples.

The example using `ifeq`:

```Makefile
libs_for_gcc = -lgnu
normal_libs =
foo: $(objects)
ifeq ($(CC),gcc)
	$(CC) -o foo $(objects) $(libs_for_gcc)
else
	$(CC) -o foo $(objects) $(normal_libs)
endif
```

You can use `ifneq`, `ifdef` and `ifndef`, too.

## Functions
You can use functions in `make` through `$(func_name args)`.

### String Functions
There are some functions related to strings:
* `$(subst from,to,text)`: substitute `from` with `to` in `text`.
* `$(patsubst pattern,replacement,text)`: substitute `pattern` with `replacement` in `text`.
* `$(strip text)`: remove the white spaces in `text`.
* `$(findstring target,text)`: find `target` in text, if found, return `target` otherwise, return empty string.
* `$(filter pattern...,text)`: filter the contents matching the `pattern...` from `text`.
* `$(filter-out pattern...,text)`: filter out the contents matching the `pattern...` from `text`.
* `$(sort list)`: sort the contents in `list` lexicographically. Note that `sort` will unique the contents, too.
* `$(word i,text)`: get the `i`-th word from `text` (index started from `1`).
* `$(wordlist l,r,text)`: get the words whose index is in `[l,r]` in sequence.
* `$(firstword text)`: get the first word of `text`.

### File Functions
There are some functions related to files:
* `$(dir name...)`: get the directory part from `name`.
* `$(notdir name...)`: get the non-directory part from `name`.
* `$(suffix name...)`: get the suffixes of files from `name`.
* `$(basename name...)`: get the base name (files' name without extension) of files from `name`.
* `$(addsuffix suffix,name)`: add `suffix` for `name`.
* `$(addprefix prefix,name)`: add `prefix` for `name`.
* `$(join list1,list2)`: join two lists. This will append words in `list2` to `list1`. For example `$(join aaa bbb,111 222 333)` will get `aaa111 bbb222 333`.

### Other Functions
#### foreach
If I want add a suffix for every item in a variable, I can do it with this:

```Makefile
names := a b c d
files := $(foreach item,$(names),$(item).o)
```

#### if
Its pattern looks like this: `$(if condition,then-part,else-part)`. For this statement, if the `condition` is a non-empty string, it will run the `then-part`, otherwise it will run the `else-part`. The return value is the part which has been executed. Note that the `else-part` can be empty. If the `else-part` is empty and the `condition` is empty string, the return value will be empty string.

#### call
Let us use an example to explain this. Now you hope to have a function which can reverse two parameters, you can do it through this:

```Makefile
reverse = $(2) $(1)
reversedItemList = $(call reverse,item1,item2)
```

After that, `reversedItemList` will be `item2 item1`. Of course, you can add different suffixes for items:

```Makefile
addTargetAndDependency = $(1).o : $(1).c
result = $(call addTargetAndDependency,main)
```

`result` will be `main.o : main.c`.

#### origin
`$(origin variablename)` will tell you where the `variablename` comes from. The return values are explained below:
* `undefined`: never defined before.
* `environment`: the variable comes from environment variables.
* `default`: the default variable, such `CC`.
* `file`: the variable is defined in a `Makefile`.
* `command line`: the variable is defined by command lines (when you type `make a=1`, `a` is defined by command lines).
* `override`: the variable if defined by `override`.
* `automatic`: the variable is defined by `make` automatically.

#### shell
This is to execute a command in shell:

```Makefile
contents := $(shell cat foo)
files := $(shell echo *.c)
```

The commands above will get the output of a shell command.

### Control Functions
* `$(erro text)`: output `text` and stop `make`.
* `$(warning text)`: output `text` but don't stop `make`.

# Other Information about make

## Implicit Rules
Implicit rules are similar with auto deduction. Or we can say auto deduction depends on implicit rules.

There are different implicit rules for different files. I'll give the implicit rules of `C` and `C++` files:
* `C`: `*.o` will be deducted depending on `*.c` and the build command will be `$(CC) -c $(CPPFLAGS) $(CFLAGS)`.
* `C++`: `*.o` will be deducted depending on`*.C, *.cc or *.cpp` and the build command will be `$(CC) -c $(CPPFLAGS) $(CFLAGS)`.

## make's return value
* `0`: success.
* `1`: some errors.
* `2`: when you use `-q` (`-q` will not run `make`, but give you a return value `0` if the targets are up to date) and `make` cannot make sure whether or not the files is up to date.

## Specify Makefile
You can specify `Makefile` with `-f`. `make -f test.make` will use `test.make` as the `Makefile` rather than `Makefile`.

## Specify Target
If you run `make`, `make` will build the first target. But you can specify target by `make targetname`.

There are some rules you should obey for naming a target when you write `Makefile`:
* `all`: build all targets.
* `clean`: remove all the files created by `make`.
* `install`: install the targets, when in `C/C++`, this will move the binary files to `/usr/bin` and move the header files to `/usr/include`.
* `print`: print the files having been updated.
* `tar`: pack the source files into a tar file.
* `dist`: create a compressed file including all source files.

## Check Make Syntax
When you want to check your syntax in `Makefile` rather than run the `make`, you can use those options: `-n, --just-print, --dry-run, --recon`. The four options are synonyms.

## Other Options in make
**Note: the options seperated by comma are synonyms.**

`-t, --touch` will touch all the targets.

`-q, --question` will check if the target exists. If the target exists, `make` will print nothing, otherwise `make` will play some information.

`-W file, --what-if=file, --assume-new=file, --new-file=file` will let `make` build the targets that depend on the `file`.

`-B, --always-make` will re-build all the time, even if the targets are up to date. This is useful when the modified time of files is wrong.

`-C dir, --directory=dir` will change working directory to `dir`.

`--debug=options` will print the debug info of `make`, the available options are:
* `a`: print all info.
* `b`: print basic info.
* `v`: verbose.
* `i`: print implicit rules.
* `j`: print jobs' info including `PID`, return value and so on.
* `m`: this is for debugging when remaking makefiles.
* If you just use `-d`, this is same with `--debug=a`.

`-e, --environment-overrides` can specify the environment variables.

`-f file, --file=file, --makefile=file` will use the `file` rather than `Makefile`.

`-i, --ignore-errors` will ignore all the errors.

`-I dir, --include-dir=dir` will specify where to found files.

`-j num, --jobs=num` will use `num` cores to build.

`-k, --keep-going` will let `make` continue even if there are some errors.

`-o=file, --old-file=file, --assume-old=file` will ignore the `file` while building.

`-p, --print-data-base` will print all the variables in `Makefile`. This can be used with `-q` to just print the variables in `Makefile`.

`-r, --no-builtin-rules` means not use any builtin rules.

`-R, --no-builtin-variables` will let `make` not define any variables.

`-s, --silen, --quiet` print no commands executed by `make`.

`-S, --no-keep-going, --stop` will stop when any error occurs.

`-w, --print-directory` print information containing the working directory before and after other processing. This is useful when build sub-systems.

`--no-print-directory` will disable `-w`.

`--warn-undefined-variables` will give a warning when finding a undefined variable.

# References
1. [How to write makefiles](https://github.com/seisman/how-to-write-makefile).
