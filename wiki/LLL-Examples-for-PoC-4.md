### Key-Value Publisher

```
[[69]] (caller)
(when (= (caller) @@69)
  (for () (< @i (calldatasize)) [i](+ @i 64)
    [[ (calldataload @i) ]] (calldataload (+ @i 32))
  )
)
```

### Name Registrar
A simple name registeration service. Names (represented as 32-byte values whose left-most bytes store an ASCII string) are stored in the address space of the contract leading to the (160-bit ethereum) address of the name's owner.
The owner addresses are similarly stored in the contract's address space with a value leading back to the  name.

```
;; Initialiser...
{
  [[(address)]] "NameReg"
  [["NameReg"]] (address)
  [[69]] (caller)
}
;; If there's at least one argument
(if (calldatasize)
  {
    ;; Stop if the first arg (name) has already been registered.
    (when @@(calldataload 0) (stop))

    ;; Store sender at name, and name at sender.
    (when @@(caller) [[@@(caller)]] 0)
    [[(calldataload 0)]] (caller)
    [[(caller)]] (calldataload 0)
    (stop)
  }

  ;; No arguments - either deregister or suicide (if it's from owner's address).
  {
    ;; Suicide if it's from owner's address.
    (when (= (caller) @@69) (suicide (caller)))

    ;; Otherwise, just deregister any name sender has, if they are registered.
    (when @@(caller) {
      [[@@(caller)]] 0
      [[(caller)]] 0
    })
    (stop)
  }
)
```

### Bank

A very simple bank. You can send ether in a transaction to load your account with funds. Withdraw your ether by specifying a 32-byte data argument of the amount to withdraw. Withdraw to a different account by specifying a second data argument with the account.

```
{
  [0] "Bank"
  (call 0x929b11b8eeea00966e873a241d4b67f7540d1f38 0 0 0 4 0 0)
}
(if (>= @@(caller) (calldataload 0))
  ;; Withdrawal:
  {
    ;; Subtract the value from the balance of the account
    [[ (caller) ]] (- @@(caller) (calldataload 0))
    ;; Transfer the funds either to...
    (if (<= (calldatasize) 32)
      (call (caller) (calldataload 0) 0 0 0 0 0)  ; ...the sender...
      (call (calldataload 32) (calldataload 0) 0 0 0 0 0)  ; ...or the supplied account.
    )
  }
  ;; Deposit; just increase the account balance by that amount.
  [[(caller)]] (+ @@(caller) (callvalue))
)
```

### Splitter
Simple cash splitter; splits the value sent amongst each of the addresses given as data items.
```
{
  [0] "Splitter"
  (call 0x929b11b8eeea00966e873a241d4b67f7540d1f38 0 0 0 8 0 0)
}
{
  [count] (/ (calldatasize) 20)
  [pay] (/ (callvalue) @count)

  ;; Cycle through each address
  (for () (< @i @count) [i](+ @i 1)
    ;; Send to 'i'th argument (assuming it's an address).
    (call (calldataload (* @i 20)) @pay 0 0 0 0 0)
  )
}
```

### Currency

```
{
  ;; Give caller a whole bunch of cash.
  [[ (caller) ]]: 0x1000000000000000000000000
  ;; Register with the NameReg contract.
  [0] "GavCoin"
  (call 0x929b11b8eeea00966e873a241d4b67f7540d1f38 0 0 0 7 0 0)
}
{
  (when (!= (calldatasize) 64) (stop))      ; stop if there's not enough data passed.
  [fromBal] @@(caller)
  [toBal]: @@(calldataload 0)
  [value]: (calldataload 32)
  (when (< @fromBal @value) (stop))         ; stop if there's not enough for the transfer.
  [[ (caller) ]]: (- @fromBal @value)       ; subtract amount from caller's account.
  [[ (calldataload 0) ]]: (+ @toBal @value) ; add amount on to recipient's account.
}
```