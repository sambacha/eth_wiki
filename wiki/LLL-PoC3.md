最新のLLLについてはこちらを参照　 [[LLL PoC 4]] or [[LLL PoC 5]]

LLLは、EthereumのPoCの低位のLispに似ているプログラミング言語
Lispのような文法を持ち、EVM-Code1の中でプログラムを書くことを簡単にし、EthereumのPoC-3より上のPoCのシリーズにて自動的にコンパイルされる。

LLLで記述されるコントラクトは、1つの表記形式のみをとる。
その表現は下記かもしれない。

* 'Hello World'や、`"This is \"Great\""`のようなStringは最大で32文字を取り、
32バイトのアスキーエンコーディングの文字列として、左側に表記される（高位のバイトでは、ビッグエンディアンの整数として解釈されるべきだ。）
* 整数型、接頭辞に`0x`を持ち、hex baseとなり、他のEthereumの標準的なDenominations のどれにもつく。例えば`69`, `0x42` もしくは、`3finney`.
* 実行された表現は、（）のリストの表現形式をとる。例えば、(add 1 2)`, リストでは、最初に演算子が、後に被演算子が表れる。

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