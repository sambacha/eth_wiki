### Bank

A very simple bank. You can send ether in a transaction to load your account with funds along with 20 * basefee. Withdraw your ether by specifying a data argument with the amount to withdraw (and attach an extra 125 * basefee). Withdraw to a different account by specifying a second data argument with the account.

```
(seq
  ;; Stop unless there is at least the fee and the sender has a valid account.
  (unless (and (> (txsender) 0xff) (>= (txvalue) (* 20 (basefee)))) (stop))

    ;; Check to see if there's at least one argument (i.e. is a withdrawal) and 
    ;; if the appropriate fees have been paid for withdrawal.
    (if (and (txdatan)
          (>= (txvalue) (* 125 (basefee)))
          (>= (sload (txsender)) (txdata 0)) )
      ;; Withdrawal:
      (seq
        ;; Subtract the value from the balance of the account
        (sstore (txsender) (- (sload (txsender)) (txdata 0)))
        ;; Transfer the funds either to...
        (if (= (txdatan) 1)
          (mktx (txsender) (txdata 0) 0)  ; ...the sender...
          (mktx (txdata 1) (txdata 0) 0)  ; ...or the supplied account.
        )
      )
      ;; Deposit; just increase the account balance by that amount.
      (sstore (txsender) (+ (sload (txsender)) (- (txvalue) (* 20 (basefee)))) )
    )
  )	
)
```

### Name Registrar
A simple name registeration service. Names (represented as 32-byte values whose left-most bytes store an ASCII string) are stored in the address space of the contract leading to the (160-bit ethereum) address of the name's owner.
The owner addresses are similarly stored in the contract's address space with a value leading back to the  name.

```
(seq

;; Stop if the value is less than 150 * basefee.
(unless (and
    (> (txvalue) (* 150 (basefee)))
    (> (txsender) 0xff)
  ) (stop)
)

;; If there's at least one argument
(if (txdatan)
  (seq

    ;; Stop if the first arg (name) is in program space or if it's already been registered.
    (unless (and
      (> (txdata 0) 0xff)
      (! (sload (txdata 0)))
    ) (stop))

    ;; If there's only one arg
    (if (= (txdatan) 1)

      (seq
        ;; Store sender at name, and name at sender.
        (sstore (txdata 0) (txsender))
        (sstore (txsender) (txdata 0))
      )

      ;; No - the second arg (address) exists.
      (seq

        ;; Stop if the address is in program space or if it's already registered.
        (unless (and
          (> (txdata 1) 0xff)
          (! (sload (txdata 1)))
        ) (stop))

        ;; Store the address at the name and the name at the address.
        (sstore (txdata 0) (txdata 1))
        (sstore (txdata 1) (txdata 0))
      )
    )
  )

  ;; No arguments - either deregister or suicide (if it's from owner's address).
  (seq
    ;; Suicide if it's from owner's address.
    (when (= (txsender) 0x8a40bfaa73256b60764c1bf40675a99083efb075)  (suicide 0x8a40bfaa73256b60764c1bf40675a99083efb075) )

    ;; Otherwise, just deregister any name sender has, if they are registered.
    (when (sload (txsender)) (seq
      (sstore (sload (txsender)) 0)
      (sstore (txsender) 0)
    ))
  )
)

)
```

### Splitter
Simple cash splitter - removes 150 * baseFee for each item plus one, then splits the rest amongst each of the addresses given as data items.
```
(seq
  ;; Stop if the transaction is worth less than the usage fee (150 * (txdatan + 1) * basefee).
  (unless (> (txvalue) (* 150 (basefee) (+ 1 (txdatan)))) (stop))

  ;; Set 'value' as the value to be paid out to each address.
  ("value" (/ (- (txvalue) (* 150 (basefee) (+ 1 (txdatan)))) (txdatan)))

  ;; Cycle through each address with 'i' being the index.
  ("i" 0)                    ; Initialise 'i'.
  (for (< ("i") (txdatan))   ; Exit once 'i' hits the argument count.
    (seq
      ;; Send to 'i'th argument (assuming it's an address).
      (mktx (txdata ("i")) ("value") 0)

      ("i" (+ ("i") 1))      ; Increment 'i'.
    )
  )
)
```