See [[LLL PoC 4]] or [[LLL PoC 5]] for the latest version of LLL.

LLL is the Ethereum PoC Low-level Lisp-like Language.

It is Lisp-like in syntax rather than anything deeper and is designed to make easier the task of writing a program in EVM-code 1, aka ES-1. It is automatically compiled in Ethereum PoC series including PoC-3 upwards.

A contract written in LLL takes the form of a single expression. An expression may be one of:

* A quoted string e.g. `"Hello world!"` or `"This is \"Great\""`. Such strings have maximum 32 characters are evaluate to the 32 byte value with the ASCII encoding of the string aligned to the left-side (i.e. in the high-order bytes should it be interpreted as a big endian integer).
* An integer, optionally prefixed with `0x` for hex base and suffixed with any of the standard Ethereum denominations. e.g. `69`, `0x42` or `3finney`.
* An executed expression which takes the form of a parenthesised list of expressions e.g. `(add 1 2)`, with the first expression of the list being the operation and the others being operands.

All instructions of the EVM-code 1 spec are valid, though you generally shouldn't need to use the stack manipulation and jump operations. The operands should be given in order from top of the stack downwards.

In addition, several control flow operations are provided:

* `(if PRED Y N)` executes `PRED`, pops the stack and executes `Y` if the popped value is non-zero otherwise `N`.
* `(when PRED BODY)` executes `PRED`, pops the stack and executes `BODY` if the popped value is non-zero.
* `(unless PRED BODY)` executes `PRED`, pops the stack and executes `BODY` if the popped value is zero.
* `(for PRED BODY)` executes `PRED`, pops the stack and executes `BODY` if the popped value is non-zero before repeating all again indefinitely.
* `(seq A B ...)` executes all following expressions in order.

And two shorthand forms for storing and loading to the permanent store:

* `(INT)`, `(STRING)` treats the value as an address and fetches to the top of the stack the value from storage (i.e. executes a `PUSH` & `SLOAD`)
* `(INT EXPR)`, `(STRING EXPR)` treats the first value as an address as above and stores the `EXPR` in storage at that address.

It's inefficient, but you can generally use the last two as a sort of replacement for global variables. Set `i` to 0 with `("i" 0)` and read it back again with `("i")`.

There's also `and` and `or` for building boolean conditions (they skip evaluation of later, unneeded arguments like in C) and may be used for any non-zero number of arguments. The multi-ary operators `+`, `-`, `*`, `/` and `%` may be used, along side the binary operators `<`, `<=`, `>`, `>=`, `=` and `!=` and the unary operator `!`. e.g. `(and 0 (= (+ 2 2 4) 8))` would evaluate to (i.e. leave atop the stack) `0` without evaluating the equality.

You'll generally want to enclose your programs with `(seq ...)` so you can execute more than a single expression.

Finally, any comments should begin with a `;`, after which all text will be ignored until the end of the line.

See [[LLL Examples for PoC 3]] for examples and the [[LLL Tutorial PoC 3]] for a tutorial.