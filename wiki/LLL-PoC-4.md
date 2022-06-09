See also [[LLL PoC 5]].

LLL is the Ethereum PoC Low-level Lisp-like Language.

It is Lisp-like in syntax rather than anything deeper and is designed to make easier the task of writing a program in EVM-code 1, aka ES-1. It is automatically compiled in Ethereum PoC series including PoC-3 upwards.

A contract written in LLL takes the form of a single expression. An expression may be one of:

* A quoted string e.g. `"Hello world!"` or `"This is \"Great\""`. Such strings have maximum 32 characters and evaluate to the 32 byte value with the ASCII encoding of the string aligned to the left-side (i.e. in the high-order bytes should it be interpreted as a big endian integer).
* An integer, optionally prefixed with `0x` for hex base and suffixed with any of the standard Ethereum denominations. e.g. `69`, `0x42` or `3finney`.
* An executed expression which takes the form of a parenthesised list of expressions e.g. `(add 1 2)`, with the first expression of the list being the operation and the others being operands.

All instructions of the EVM-code 1 spec are valid, though you generally shouldn't need to use the stack manipulation and jump operations. The operands should be given in order from top of the stack downwards.

In addition, several control flow operations are provided:

* `(if PRED Y N)` executes `PRED`, pops the stack and executes `Y` if the popped value is non-zero otherwise `N`.
* `(when PRED BODY)` executes `PRED`, pops the stack and executes `BODY` if the popped value is non-zero.
* `(unless PRED BODY)` executes `PRED`, pops the stack and executes `BODY` if the popped value is zero.
* `(for INIT PRED POST BODY)` executes `INIT` once, then executes `PRED`, pops the stack and executes `BODY` if the popped value is non-zero before executing `POST` and going back to `PRED` to repeat.
* `(seq EXPR1 EXPR2 ...)` executes all following expressions in order.

There's a short form for `(seq EXPR1 EXPR2 ...)`: `{ EXPR1 EXPR2 ... }`.

There's also short form for accessing memory:

* `@ EXPR` expands to `(mload EXPR)`.
* `[ EXPR1 ] EXPR2` expands to `(mstore EXPR1 EXPR2)`.
* `@@ EXPR` expands to `(sload EXPR)`.
* `[[ EXPR1 ]] EXPR2` expands to `(sstore EXPR1 EXPR2)`.

Any otherwise undefined text strings are assumed to be variable definitions, thus a for loop to count between 0 to 9 looks something like:

```
(for [i]:0 (< @i 10) [i](+ @i 1) 
  ;; do something
)
```

There's also `and` and `or` for building boolean conditions (they skip evaluation of later, unneeded arguments like in C) and may be used for any non-zero number of arguments. The multi-ary operators `+`, `-`, `*`, `/` and `%` may be used, along side the binary operators `<`, `<=`, `>`, `>=`, `=` and `!=` and the unary operator `!`. e.g. `(and 0 (= (+ 2 2 4) 8))` would evaluate to (i.e. leave atop the stack) `0` without evaluating the equality.

You'll generally want to enclose your programs with `{ ... }` so you can execute more than a single expression.

Finally, any comments should begin with a `;`, after which all text will be ignored until the end of the line.

See [[LLL Examples for PoC 4]] for examples.