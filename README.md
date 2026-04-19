# Project has been moved to >>> https://github.com/AiLang-Author/Ailang-Self-Hosting-
MIT/Public Commons Licensed

# C64_BASIC_INTERPRETER(1)

## NAME

c64_basic_interpreter - Commodore 64 BASIC V2 interpreter written in AILang

## SYNOPSIS

```
./c64_basic_interpreter_exec
```

## DESCRIPTION

A fully functional Commodore 64 BASIC V2 interpreter compiled to native x86-64 Linux executable. No libc dependency - uses direct syscalls only. Features a TUI interface with classic C64 blue screen aesthetics.

Written entirely in AILang, demonstrating the language's capability for systems programming and complex interpreter implementation.

## FEATURES

### Variables
- **Numeric**: A-Z (26 integer variables)
- **String**: A$-Z$ (26 string variables)
- Long variable names supported (WEAPON$, SCORE%, etc.) - first letter determines storage

### Operators

#### Arithmetic
```
+   Addition
-   Subtraction
*   Multiplication
/   Integer division
```

#### Comparison (Numeric and String)
```
=   Equal
<>  Not equal
<   Less than
>   Greater than
<=  Less than or equal
>=  Greater than or equal
```

#### String
```
+   Concatenation ("HELLO" + " WORLD")
```

### Commands

#### Program Control
```
RUN          Execute program from first line
LIST         Display program listing
NEW          Clear program from memory
END          Terminate program execution
REM          Comment (remainder of line ignored)
```

#### Flow Control
```
GOTO n              Jump to line n
GOSUB n             Call subroutine at line n
RETURN              Return from subroutine
FOR v = a TO b      Begin loop (optional STEP s)
NEXT v              End of FOR loop
IF expr THEN stmt   Conditional execution
ON n GOTO a,b,c     Computed GOTO
ON n GOSUB a,b,c    Computed GOSUB
```

#### Input/Output
```
PRINT expr          Output to screen
PRINT expr;         Output without newline
PRINT expr,         Output with tab
INPUT "prompt"; v   Read user input into variable
INPUT v             Read user input (default ? prompt)
```

#### Memory
```
CLR                 Clear all variables
CLS                 Clear screen
PEEK(addr)          Read byte from memory address
POKE addr, val      Write byte to memory address
```

#### File I/O
```
SAVE "filename"     Save program to file
LOAD "filename"     Load program from file
```

#### Data Statements
```
DATA val,val,...    Define inline data
READ v,v,...        Read next DATA value(s)
RESTORE             Reset DATA pointer to beginning
```

### Functions

#### Math Functions
```
ABS(n)      Absolute value
SGN(n)      Sign (-1, 0, or 1)
INT(n)      Integer part (floor)
SQR(n)      Integer square root
RND(n)      Random number 0 to n-1
```

#### String Functions
```
LEN(s$)             Length of string
ASC(s$)             ASCII code of first character
CHR$(n)             Character from ASCII code
VAL(s$)             Convert string to number
STR$(n)             Convert number to string
LEFT$(s$, n)        First n characters
RIGHT$(s$, n)       Last n characters
MID$(s$, p, n)      Substring from position p, length n
```

### Multi-Statement Lines

Multiple statements per line using colon separator:
```
10 A = 1 : B = 2 : PRINT A + B
20 IF X > 0 THEN PRINT "POS" : GOTO 100
```

## FILES

The interpreter consists of:

### Main Program
- `c64_basic_interpreter.ailang` - Main interpreter (~2000 LOC)

### Libraries
- `Library.C64Math.ailang` - Math functions (ABS, SGN, SQR, RND, PEEK, POKE)
- `Library.C64String.ailang` - String functions (LEN, LEFT$, RIGHT$, MID$, etc.)
- `Library.C64FileIO.ailang` - File operations (SAVE, LOAD)
- `Library.TUI.ailang` - Terminal UI (screen control, input, colors)
- `Library.XArrays.ailang` - Dynamic arrays for program storage

## ARCHITECTURE

```
┌─────────────────────────────────────────────────┐
│              TUI Layer (Blue Screen)            │
├─────────────────────────────────────────────────┤
│                  REPL Loop                      │
│         (Read - Eval - Print - Loop)            │
├─────────────────────────────────────────────────┤
│    Parser          │      Executor              │
│  ┌───────────┐     │   ┌─────────────┐          │
│  │ PExpr     │     │   │ CmdPrint    │          │
│  │ PStrExpr  │     │   │ CmdInput    │          │
│  │ PCompare  │     │   │ CmdLet      │          │
│  │ PFactor   │     │   │ CmdGoto     │          │
│  │ PTerm     │     │   │ CmdGosub    │          │
│  └───────────┘     │   │ CmdFor/Next │          │
│                    │   │ CmdIf       │          │
│                    │   └─────────────┘          │
├─────────────────────────────────────────────────┤
│  C64Math    │  C64String   │  C64FileIO         │
├─────────────────────────────────────────────────┤
│           AILang Runtime (syscalls)             │
└─────────────────────────────────────────────────┘
```

## EXAMPLES

### Hello World
```basic
10 PRINT "HELLO WORLD"
RUN
```

### Factorial
```basic
10 FOR N = 1 TO 10
20 F = 1
30 FOR I = 1 TO N
40 F = F * I
50 NEXT I
60 PRINT STR$(N) + "! = " + STR$(F)
70 NEXT N
RUN
```

### String Manipulation
```basic
10 A$ = "COMMODORE 64"
20 PRINT "LENGTH: " + STR$(LEN(A$))
30 PRINT "FIRST 4: " + LEFT$(A$, 4)
40 PRINT "LAST 2: " + RIGHT$(A$, 2)
50 PRINT "MIDDLE: " + MID$(A$, 5, 4)
RUN
```

### Guessing Game
```basic
10 N = RND(100) + 1
20 INPUT "GUESS (1-100)"; G
30 IF G = N THEN PRINT "CORRECT!" : END
40 IF G < N THEN PRINT "TOO LOW"
50 IF G > N THEN PRINT "TOO HIGH"
60 GOTO 20
RUN
```

### Subroutines
```basic
10 GOSUB 100
20 PRINT "BACK IN MAIN"
30 END
100 REM SUBROUTINE
110 PRINT "IN SUBROUTINE"
120 RETURN
RUN
```

### Save and Load
```basic
10 PRINT "THIS PROGRAM WAS SAVED"
SAVE "MYPROGRAM.BAS"
NEW
LOAD "MYPROGRAM.BAS"
LIST
RUN
```

## KEYBOARD

```
ESC         Quit interpreter
ENTER       Execute line / Submit input
BACKSPACE   Delete character
```

## DIFFERENCES FROM ORIGINAL C64 BASIC

1. **Integer only** - No floating point (SQR returns integer square root)
2. **Case sensitive strings** - "A" and "a" are different
3. **No PETSCII** - Standard ASCII character set
4. **No sprites/sound** - Text mode only
5. **No tape/disk commands** - SAVE/LOAD use filesystem directly
6. **64-bit integers** - Larger number range than original

## TECHNICAL DETAILS

- **Binary size**: ~140KB standalone executable
- **Dependencies**: None (no libc)
- **Syscalls**: read, write, open, close, exit, brk
- **Memory model**: Dynamic allocation via brk()
- **Target**: x86-64 Linux (ELF64)

## AUTHOR

Built with AILang - a language designed for LLM-assisted systems programming.

## SEE ALSO

- AILang compiler documentation
- Original Commodore 64 Programmer's Reference Guide
- Dave Plummer's BASIC interpreter challenge

## VERSION

1.0 - December 2025

## VERSION

1.0 - December 2025
