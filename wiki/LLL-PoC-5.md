LLL is the Ethereum PoC Low-level Lisp-like Language.

It is Lisp-like in syntax rather than anything deeper and is designed to make easier the task of writing a program in EVM-code 1, aka ES-1. It is automatically compiled in Ethereum PoC series including PoC-3 upwards.

### Basic Syntax

A contract written in LLL takes the form of a single expression. An expression may be one of:

* A quoted string e.g. `"Hello world!"`. Such strings have maximum 32 characters and evaluate to the 32 byte value with the ASCII encoding of the string aligned to the left-side (i.e. in the high-order bytes should it be interpreted as a big endian integer).
* A space-less symbol-less string with a single quote at the beginning, e.g. `'HelloWorld`. Again such strings have a maximum character limit &c. as above.
* An integer, optionally prefixed with `0x` for hex base and suffixed with any of the standard Ethereum denominations. e.g. `69`, `0x42`, `10 ether` or `3finney`.
* An executed expression which takes the form of a parenthesised list of expressions e.g. `(add 1 2)`, with the first expression of the list being the operation and the others being operands.

Technically, all instructions of the EVM-code 1 spec are valid. However, they generally shouldn't be used directly since in almost all cases you'll find operations here that do a better job.

Any comments should begin with a `;`, after which all text will be ignored until the end of the line.

### Control Flow

In addition, several control flow operations are provided:

* `(if PRED Y N)` equivalent to the evaluation of `Y` if `PRED` evaluates to a non-zero value and otherwise to the evaluation of `N`.
* `(when PRED BODY)` evaluates `BODY` (ignoring any result) if and only if `PRED` evaluates to a non-zero value.
* `(unless PRED BODY)` evaluates `BODY` (ignoring any result) if and only if `PRED` evaluates to zero.
* `(for INIT PRED POST BODY)` evaluates `INIT` once (ignoring any result), then evaluates `BODY` and `POST` (discarding the result of both) as long as `PRED` is true.
* `(seq EXPR1 EXPR2 ...)` evaluates all following expressions in order. Evaluates to the result of the final expression given.
* `(raw EXPR1 EXPR2 ...)` evaluates all following expressions in order. Evaluates to the result of the first non-void expression (i.e. the first expression that leaves anything atop the stack), or void if there is none. This can be used with `POP` in order to customise exactly which expression's result you wish to have the block evaluate to.

There's a short form for `(seq EXPR1 EXPR2 ...)`: `{ EXPR1 EXPR2 ... }`. You'll generally want to enclose your programs with `{ ... }` so you can execute more than a single expression.

### Memory & Storage Access

There's a short form for accessing memory:

* `@ EXPR` expands to `(mload EXPR)`, used for dereferencing a location in memory. The C-language equivalent would be `*EXPR`.
* `[ EXPR1 ] EXPR2` expands to `(mstore EXPR1 EXPR2)`, used for placing a value into a location of memory. The C-language equivalent would be `*EXPR1 = EXPR2`.
* `@@ EXPR` expands to `(sload EXPR)`, used for dereferencing a location in storage.
* `[[ EXPR1 ]] EXPR2` expands to `(sstore EXPR1 EXPR2)`, used for placing a value, `EXPR2`, into a location in storage, `EXPR1`.

In the cases of both `[...]` and `[[...]]`, the last closing square-bracket may optionally have a colon inserted for readability.

Any otherwise undefined text strings are assumed to be variable definitions, thus a for loop to count between 0 to 9 looks something like:

```
(for [i]:0 (< @i 10) [i](+ @i 1) 
  ;; do something
)
```

### Arithmetic & Logical Operators

A number of operators are also valid. These include the bitwise operators:

* `&` (bitwise and)
* `|` (bitwise or)
* `^` (bitwise xor)
* `~` (bitwise not)

There are also logical operators, which have the same behaviour as in C in that they skip evaluation of later arguments if unneeded and, with the exception of logical not, may be used for any non-zero number of arguments:

* `&&` (logical and)
* `||` (logical or)
* `!` (logical not)

The multi-ary operators `+`, `-`, `*`, `/` and `%` may be used, along side the binary operators `<`, `<=`, `>`, `>=`, `=` and `!=`.

e.g. `(&& 0 (= (+ 2 2 4) 8))` would evaluate to (i.e. leave atop the stack) `0` without evaluating the equality.

### Literals & Code

When literals must be included that can be placed into memory, there is the `lit` operation:

* `(lit POS STRING)` Places the string `STRING` in memory at `POS` and evaluates to its length.
* `(lit POS INT1 INT2 ...)` Places each of the integers INT1, INT2 &c. in to memory beginning at POS and each 32-bytes apart. Evaluates to total memory used.
* `(lit POS BIGINT)` Places `BIGINT` into memory at POS and evaluates to the number of bytes it takes. Unlike for the previous case, `BIGINT` may be arbitrarily large, and thus if specified as hex, can facilitate storing arbitrary binary data.

For handling cases where code needs to be compiled and passed around, there is the `lll` operation:

* `(lll EXPR POS MAXSIZE)`
* `(lll EXPR POS)`

This places the EVM-code as compiled from `EXPR` into memory at position `POS` if and only if said EVM-code is at most `MAXSIZE` bytes, or if `MAXSIZE` is not provided. It evaluates to the number of bytes of memory written, i.e. either 0 or the number of bytes of EVM-code; if provided, this is always at most `MAXSIZE`.

Contract creation code will typically look something like:
```
{
  ;; Initialisation code goes here
  ;; This just records who the original creator is
  [[0]] (caller)

  ;; Return the contract code
  (return 0 (lll {
    ;; Contract code goes here
    ;; This just suicides if called by the original creator
    (when (= (caller) @@0) (suicide (caller)))
  } 0))
}
```

### Macro Expansion

Macros are supported with the `def` operation. This works for a single non-parameterised expression as well as one with parameters.

* `(def NAME EXPR)` From this point onwards in the code, any uses of the unquoted `NAME` as an expression will instead result in the code `EXPR`. `NAME` will typically be a quoted string, but if it is a symbol it must resolve through previous definitions into a string.
* `(def NAME (ARG1, ARG2, ...) EXPR)` Similar to the above, but `EXPR` here may involve the symbols `ARG1`, `ARG2`, &c. which will each be substituted at the time of macro expansion.

The behaviour is similar to `#define` in C, for example:

```
{
(def 'sixtynine 69)
(def 'sqr (z) (* z z))
(when (> (sqr sixtynine) sixtynine) (stop))
}
```

This results in the program stopping.

Unlike in C, macros here are scoped; environmental definitions at the time of the macro's definition are recorded alongside the macro. When a definition must be resolved, definitions made within the present macro are treated preferentially, then arguments to the macro, then definitions & arguments made in the scope of which the macro was called (treated in the same order), then finally definitions & arguments stemming from the scope in which the macro was created (again, treated in the same order).

This can be used to great effect for implementing counters and some functional-programming-esque idioms.

To include a whole file, you can use the `include` operation:

* `(include FILENAME)`: Expands to the contents of the string FILENAME.

### Including Inline Assembler

Low-level assembler may be included in line with one caveat; it must have transparent stack usage. This basically means that `JUMP` or `JUMPI` are best avoided; if used then ignoring their jump effects (and thus assuming the jump doesn't happen and the PC just gets incremented) must have a valid final result in terms of items deposited on the stack.

Usage is:

* `(asm ATOM1 ATOM2 ...)`

Where the `ATOM`s may be either valid, non-`PUSH` VM instructions (see the formal definition of the machine in the Yellow Paper) or literals (in which case they will result in an appropriate `PUSH` instruction).

For example:

```
(asm 69 42 ADD)
```

Evaluates into the value 111. Note any assembler fragment that results in fewer than zero items being deposited on the stack or greater than 1 will almost certainly not interoperate with the rest of the language and thus cause compile errors. Here be (even more than usual) dragons.

### Additional helpers

There are several addition helper operations to aid in program construction. These are implemented as macros (for what little it matters).

* `gav`: My address.
* `config`: The address of the configuration contract.
* `allgas`: All gas available, when used as a parameter to `send`, `call` or `msg`.
* `(sendgavcoin <to> <value>)`: Sends `<value>` GAV to account `<to>`.
* `(regname <name>)`: Registers the name `name` with the caller.
* `(regcoins <currency-code>)`: Registers the three-letter upper-case currency code `<currency-code>` with the caller.
* `(send <to> <value>)`: Sends `<value>` [Wei] to the account `<to>`.
* `(send <gaslimit> <to> <value>)`: Sends `<value>` Wei to the account `<to>` with a limit on the allowed gas `<gaslimit>`.
* `(msg <to> <data>)`: Sends a message to the account `<to>` with word-sized data item `<data>`, evaluates to the word-sized return value.
* `(msg <to> <value> <data>)`: Sends a message to the account `<to>` with word-sized data item `<data>` and `<value>` Wei, evaluates to the word-sized return value.
* `(msg <gaslimit> <to> <value> <data>)`: Sends a message to the account `<to>` with word-sized data item `<data>` and `<value>` Wei and a limit of gas `<gaslimit>`, evaluates to the word-sized return value.
* `(msg <gaslimit> <to> <value> <data> <datasize>)`: Sends a message to the account `<to>` with data of length `<datasize>`, stored in memory at position `<data>` and `<value>` Wei and a limit of gas `<gaslimit>`, evaluates to the word-sized return value.
* `(create <code>)`: Creates a new contract with initialisation code `<code>`.
* `(create <endowment> <code>)`: Creates a new contract with `endowment` Wei and initialisation code `<code>`.
* `(sha3 <value>)`: Evaluates to the sha3 of the given `<value>`.
* `(sha3pair <value1> <value2>)`: Evaluates to the sha3 of the 64-bytes given by the concatenation of `<value1>` and `<value2>`.
* `(sha3trip <value1> <value2> <value3>)`: Evaluates to the sha3 of the 64-bytes given by the concatenation of `<value1>`, `<value2>` and `<value3>`.
* `(return <value>)`: Returns from the call with the word-sized data `<value>`.
* `(returnlll <expression>)`: Returns from the call with the code representing the LLL expression `<expression>`.

### Examples

See [LLL Examples](LLL Examples for PoC 5) for examples.