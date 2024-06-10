# Quick guide to gdb
`Text User Interface (TUI)` will show (user `-tui` to enable this):
* commands and history towards the bottom
* source code position towards the top

Once the source get "messed up", you can use `C-L` to force a redraw of the terminal screen.

Once entering `gdb`, you can use `tui enable` and `tui disable` to enable or disable `tui` mode.

## Breakpoints
| Command          | Effect                                                                   |
| :-               | :-                                                                       |
| break main       | Stop running at beginning of main(), the main may be other function name |
| break file:line  | Stop running at specific line of a specific file                         |
| break            | Stop running at current line                                             |
| info break       | Print all the breakpoints' information                                   |
| disable 2        | Don't stop at breakpoint #2 but keep it there                            |
| enable 2         | Stop at breakpoint #2 again                                              |
| clear main       | Remove the breakpoint at main()                                          |
| delete 2         | Remove breakpoint #2                                                     |
| break *0x1248f2  | Break at specific instruction address                                    |
| break *func+24   | Break at instruction with decimal offset (unit: byte) from a label       |
| break *func+0x18 | Break at instruction with hex offset (unit: byte) from a label           |

Note that `clear 2` can not remote the `breakpoint #2`, this will remove the breakpoint at line `2` instead. On the contrary, `delete 2` will remove the `breakpoint #2`.

## Arguments and Running
| Command        | Effect                                          |
| :-             | :-                                              |
| set args a b c | Set command arguments to a b c                  |
| show args      | Show the current arguments                      |
| run            | Start running the program from the beginning    |
| kill           | Kill the running program                        |
| file program   | Load program and start debugging                |
| quit           | Exit the debugger                               |
| starti         | Start running and stop at the first instruction |

Note that when using `set args` you can not specify the filename, which means the first argument will be `argv[1]` rather than `argv[0]`.

Besides `set args`, `run arg1 arg2` will do the same.

## Stepping
| Command         | Effect                                                             | Notes                              |
| :-              | :-                                                                 | :-                                 |
| step            | Step forward one line                                              |                                    |
| step 4          | Step forward four line                                             | step goes into functions           |
| next            | Step forward one line but over function calls                      |                                    |
| next 4          | Step forward four lines but over function calls                    | next doe not go into functions     |
| stepi           | Step a single assembly instruction forward                         | stepi goes into functions          |
| nexti           | Step a single assembly instruction forward but over function calls | nexti doe not go into functions    |
| finish          | Finish executing the current function                              | Shows return value on finishing it |
| continue / cont | Continue running until the next breakpoint                         |                                    |

## Printing Values in Memory and Stack
| Command         | Effect                                                                              |
| :-              | :-                                                                                  |
| print a         | Print value of variable a which must be in current function                         |
| print/x a       | Print value of a as a hexadecimal number                                            |
| print/o a       | Print value of a as a octal number                                                  |
| print/t a       | Print value of a as a binary number                                                 |
| print/s a       | Print value of a as a string even if it is not one                                  |
| print arr[2]    | Print value of arr[2] according to its C type                                       |
| print 0x4A25    | Print decimal value of hex constant 0x4A25 which is 18981                           |
| display a       | Display value of variable a, this will print every command unless a is not in scope |
| info display    | Show all the variables being displayed                                              |
| undisplay 1     | Stop displaying the first variable                                                  |
| printf "%d\n" a | Print value of a as a decimal number (use printf format)                            |
| x a             | Examine memory pointed to by a                                                      |
| x/d a           | Print memory pointed to by a as a decimal integer                                   |
| x/s a           | Print memory pointed to by a as a string                                            |
| x/s (a+4)       | Print memory pointed to by a+4 as a string                                          |
| x/x a           | Print memory pointed to by a as a hexadecimal number                                |
| x $rax          | Print memory pointed to by register rax                                             |
| x $rax+8        | Print memory 8 bytes above where register rax points                                |
| x /wx $rax      | Print as "words" of 32-bit numbers in hexadecimal formats                           |
| x /gx $rax      | Print as "giant" 64-bit numbers in hexadecimal format                               |
| x /5gd $rax     | Print 5 64-bit numbers starting where rax points; use decimal format                |
| x /3wd $rsp     | Print 3 32-bit numbers at the top of the stack (starting at register rsp)           |

## Other Useful Commands
| Command             | Effect                                                                      |
| backtrace           | Show your current function call stack, with the current function at the top |
| advance hello.cpp:5 | Continue to this temporary breakpoint                                       |
| jump *0x1248f2      | Jump to a specific instruction address                                      |
| set variable a = 5  | Set the value of variable a to 5                                            |
| watch a             | Stop running when the value of a changes                                    |
| info watchpoints    | Show all the watchpoints set                                                |
| rwatch a            | Stop running when the value of a is read                                    |
| awatch a            | Stop running when the value of a is read or written                         |
| attach pid          | Attach to a running process with the given process ID                       |
| detach              | Detach from the running process                                             |
| info win            | Show all the windows currently open                                         |
| focus next          | Move the cursor to the next window                                          |
| focus prev          | Move the cursor to the previous window                                      |
| focus name          | Move the cursor to the window with the given name                           |
| winheight regs +2   | Make the registers windows 2 lines bigger                                   |
| winheight regs -2   | Make the registers windows 2 lines smaller                                  |

## Core Dumps
When a program crashes, a core dump is generated. You can use `gdb` to debug the core dump file. If there is no core dump file, you can use `ulimit -c unlimited` to enable core dump generation, this command will set the core file size be unlimited.

`gdb -tui -c core binary` can be used to debug the core dump file.

## Command History and Screen Management in TUI Mode
| Command      | TUI Mode Effect                        | Normal Mode Effect                                          |
| :-           | :-                                     | :-                                                          |
| C-l          | Re-draw the screen to remove cruft     | Clear the screen                                            |
| C-p / C-n    | Previous/Next commands                 | Same                                                        |
| C-r          | Interactive search backwards           | Same                                                        |
| C-b / C-f    | Move cursor left/right                 | Same                                                        |
| Up / Down    | Move the source code window up/down    | Previous/Next commands                                      |
| Left / Right | Move the source code window left/right | Move cursor left/right                                      |
| list         | No effect                              | Show 10 lines of source code around current execution point |

Note that even `list` does not work in `tui` mode, but commands like `list hello.cpp:5` really work in `tui` mode.

## Breakpoints in Binary Files
For binary files, `nm filename` or `objdump -t filename` can output the `T` symbols, which mean they are "program text" and are similar with functions of `C`. For those symbols, you can add breakpoints at them.

Besides, you can add breakpoints at specific addresses (similar with lines of `C` files).

## Debugging Assembly in GDB
| Command             | TUI Mode Effect                                  | Normal Mode Effect                                               |
| :-                  | :-                                               | :-                                                               |
| layout asm          | Show assembly code window if not currently shown | No effect                                                        |
| layout reg          | Show a window holding register contents          | No effect                                                        |
| layout src          | Show a window holding source code                | No effect                                                        |
| layout split        | Show both source and assembly code windows       | No effect                                                        |
| info register       | Print all the registers' information             | Same                                                             |
| disassemble / disas | No effect                                        | Show lines of assembly in binary, includes instruction addresses |

## Other Useful Tools for Working With Binary Files
| Command           | Effect                                                                     |
| :-                | :-                                                                         |
| objdump -d file.o | Show decompiled assembly from compiled file                                |
| objdump -t file.o | Show the symbols (functions/global vars) defined in the file               |
| nm file.o         | Short for "names", Similar to objdump -t but omits some details of symbols |
| strings file.o    | Find and show ASCII strings that are present in the file                   |

