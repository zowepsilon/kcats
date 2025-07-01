## kcats

An esolang inspired by Forth using an infinite tape of stacks as memory. Each cell is a string that can be interpreted in different ways (string or integer) depending of the instruction. The program is executed instruction by instruction (postfix notation).

I made this project as an exercice to language design: I wanted to see how I could develop of ideas from concatenative programming languages to make them more powerful and somewhat easier to use.

A notable feature is the lack of functions and structured control flow, which can be implemented as programming patterns instead. [You can even implement the `?` operator from Rust!](examples/monads.kc)

### Example

Some examples can be found in [examples/](examples/), in particular [a complete brainfuck interpreter](examples/brainfuck.kc).

The following program checks if a given integer is prime.
```rust
main // at the start of the program, jump to the "main" label

#include std.kc // import the standard library (see examples/std.kc)

// is_prime function (takes one integer)
// :is_prime defines the "is_prime" label
:is_prime =>
    2

    // loop over integers to check divisibility
    :is_prime_loop
        !dup2 % 0 == "is_prime_notprime" goto_if

        1 +

        !dup2
        != "is_prime_loop" goto_if

    pop pop
    1 ret // note that ret is a procedure defined in std.kc, not a builtin instruction!

    :is_prime_notprime
    pop pop
    0 ret

// define the "main" label
:main
    "> " !ask dup !is_prime // asks the user a number and check if it's prime
    "prime" goto_if

    " is not prime!" concat !println
    exit

    :prime
    " is prime!" concat !println
    exit

:exit
```

### Builtin instructions

```rust
$x $y +                     -- pops 2 values from the current stack, adds them and pushes the result
$base $by -                 -- difference
$x $y *                     -- product
$dividend $divisor /        -- (integer) division
$code chr                   -- converts an integer to its corresponding ascii character
$start $end concat          -- concatenates the last 2 values from the current stack
<-                          -- switches to the stack on the left of the current stack
->                          -- same thing the stack to the right
$value <=                   -- pops a value from the current stack and places it the stack to the left in the tape
$value =>                   -- same thing but with the stack to the right
$value dup                  -- creates a copy of top value on the stack
empty                       -- returns a non zero value if the current stack is empty
input                       -- read a line from stdin
$x $y ==                    -- equality
$string $index .            -- string indexing
$condition $label goto_if   -- jumps to the label if the condition is non zero
$pc jump                    -- jumps to a location in the program from the program counter (index in the program)
$condition $pc jump_if      -- same as goto_if but the program counter instead of a label
$string len                 -- length of string
$value $modulo %            -- remainder
$x $y !=                    -- not equal
pop                         -- discards a value from the current stack
$value print                -- prints a value
```
