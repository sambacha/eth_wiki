(By Gav Wood, 2014)

### Writing a Contract in LLL

We're going to use PoC-3 build of AlethZero to write a simple contract in LLL, the Low-level Lisp-like Language. We'll assume you have a decent supply of ether to pay for this contract to be created. If you don't have so much, then you should mine for a little while to build up your supply.

First thing to creating a contract is to make sure the _To_ field of the _Transact_ pane is empty, in order that you get a grayed-out _(Create Contract)_ in the box. We'll endow no cash into the contract, so we'll put a zero into the _Value_ box.

The _Data_ box (i.e. of the two, the top box) contains our contract's source code; the lower box contains the assembly-language corresponding to that source code - when you alter the source code, you can immediately see the assembly changing.

With the transaction otherwise set up we're ready to actually author it. For this example, we'll make a simple bank. We want people to be able to deposit ether with the contract and be able to withdraw that ether at some time later.

We're going to need to execute several 'steps' in a sequence (much like in a normal programming language). To do this in LLL, we use the `seq` operation. In LLL, all operations take the form of an open-parenthesis (i.e. `(`), the operator (`seq` in our case), any expressions (here we've yet to decide what these are), and ended by a close-parenthesis (`)`).

So we begin with:

```
(seq
)
```

This does nothing, of course, since we haven't described yet which steps we must execute. When an Ethereum contract executes in the Ethereum virtual machine (EVM), you get a grace period of 16 execution cycles free from fees. This is so you can check whether the transaction sender has sent you enough ether for you to bother running the contract into the fee-paying portion or not.

So the first step will be to check that the transaction value, `(txvalue)`, is greater than or equal to the minimum cost of running this contract, twenty times the base fee, `(basefee)`. As such our program becomes:

```
(seq
  (unless (>= (txvalue) (* 20 (basefee))) (stop))
)
```

Once you've typed this in you'll notice assembly code filling up the lower box. We're on our way!

To make the decision over whether to stop early, we use the `unless` operator. This takes two operands; firstly an expression for the 'condition', and secondly an expression for the 'body', the latter to be evaluated only if the condition evaluates to zero.

In this case, our body is the `stop` operation, which unsurprisingly, immediately halts all execution of the contract. Our condition is a `>=` operation, and as such only evaluates to one if the first argument to that operation (`(txvalue)`, the value of all the ether bundled with the transaction) is greater than or equal to the second argument (`(* 20 (basefee))`, twenty times the basefee for transaction execution).

We might read the line as "unless _txvalue_ is greater than or equal to 20 times the _basefee_ then stop".

Right, so we've checked that they've given us at least twenty times the basefee, so the next thing to do is to handle the deposit. Because our memory is (theoretically) 2^256 entries large (each entry able to store a 256 bits worth of data), we can just use it as a massive associative array for our addresses, storing each address's account balance at that place in memory.

So, to handle deposits, we'll increment the value in our memory corresponding to the transaction sender's address by the amount that they've given us less the cost of transacting with this contract, 20 times the _basefee_:

```
(seq
  (unless (>= (txvalue) (* 20 (basefee))) (stop))
  (sstore
    (txsender)
    (+
      (sload (txsender))
      (- (txvalue) (* 20 (basefee)))
    )
  )
)
```

So here we subtract our price from the transaction's value, add it to whatever was in the transaction sender's slot before (`(sload (txsender))`; "load the value from storage at the location given by the transaction sender's hash") and stores the resultant amount back at the same position (`(sstore ...`).

So that works fine for depositing ether, but it's a pretty rubbish bank that doesn't let you withdraw from your account! So how can we do that? The trick is to use the transaction data (`txdatan` and `txdata` operators) to determine if there's any additional data and, if so, withdraw the amount given from the account. Of course, we'll have to check that they have at least that amount in their account first!

The first thing to do is to check whether it's purely a depositing transaction or whether they want to issue a withdrawal. For this we use the `txdatan` to see how many data items were bundled with the transaction; if it's zero then it's just a deposit, however if there's (at least) one, then we've been issued a withdrawal, too:

```
(seq
  (unless (>= (txvalue) (* 20 (basefee))) (stop))
  (if (txdatan)
    (seq
      ;; TODO: Withdraw
    )
    ;; Else... Deposit
    (sstore (txsender) (+ (sload (txsender)) (- (txvalue) (* 20 (basefee))) ) )
  )
)
```

You'll notice we added a few comments. There is also the `if` operation that evaluates the second operand if the first is non-zero otherwise the third. You might put an imaginary "else" between the second and third operands.

Now we have to implement the withdraw portion. To conduct a withdrawal, two things need to happen: subtract the withdrawal amount from the account balance and make a transaction to bestow the funds back to the sender.

The first is easy enough: we use the `txdata` operator to get the first data item of the transaction (`(txdata 0)`) and then just `sstore`, `sload` and `txsender` as for depositing.

The second is quite easy, too. We use the `mktx` operator to make a new transaction that delivers the required amount of ether (`(txdata 0)`; the first data item to the transaction) to the sender's account (`(txsender)`). The final `0` of the operation is the number of data items for the transaction; none.

So we end up with:

```
(seq
  (unless (>= (txvalue) (* 20 (basefee))) (stop))
  (if (txdatan)
    ;; At least one data item... Withdraw
    (seq
      (sstore (txsender) (- (sload (txsender)) (txdata 0)))
      (mktx (txsender) (txdata 0) 0)
    )
    ;; Else... Deposit
    (sstore (txsender) (+ (sload (txsender)) (- (txvalue) (* 20 (basefee))) ) )
  )
)
```

### Checks and Balances

So this is a minimal working implementation, however there are several things missing. First and foremost is that the `mktx` operation we execute for withdrawal costs us 100 times the _basefee_, yes when a withdrawal takes place, they can pay only the minimal 20 times; we should really bump up our price accordingly. To do this, we'll add an additional condition for withdrawal - that you must pay at least 135 times the _basefee_ (using the boolean `and` operator). So `(if (txdatan) ...` becomes

```
(if (and (txdatan) (>= (txvalue) (* 135 (basefee))) )
  ...
```

So now they must pay the extra amount for withdrawing funds, however, nothing (aside from the contract running out of funds) is stopping them from withdrawing more than they have. We'll make a third check that ensures they have at least the amount they wish to withdraw in their account with us. We just use the `txdata` and `sload` operators to determine this, so our final condition for withdrawal becomes:

```
(if (and
    (txdatan)
    (>= (txvalue) (* 135 (basefee)))
    (>= (sload (txsender)) (txdata 0))
  )
  ...
```

### Low Sender

The final issue is that we're also storing the program in the lower portion of the memory (approximately the first 256 entries) and we don't want to stomp on our program in the unlikely event of someone with an address evaluating to a number less than 256 depositing cash with us.

We'll take the quickest way out and deny "low-addresses", by putting in an extra condition at the very start, so the first `unless` now becomes:

```
(unless (and (> (txsender) 0xff) (>= (txvalue) (* 20 (basefee)))) (stop))
```

Leaving us with the final contract of:

```
(seq
  ;; Stop unless there is at least the fee and the sender has a valid account.
  (unless (and (> (txsender) 0xff) (>= (txvalue) (* 20 (basefee)))) (stop))

  ;; Check to see if there's at least one argument (i.e. is a withdrawal) and 
  ;; if the appropriate fees have been paid for withdrawal.
  (if (and (txdatan)
        (>= (txvalue) (* 135 (basefee)))
        (>= (sload (txsender)) (txdata 0)) )
    ;; At least one data item... Withdraw
    (seq
      ;; Subtract the value from the balance of the account
      (sstore (txsender) (- (sload (txsender)) (txdata 0)))
      (mktx (txsender) (txdata 0) 0)
    )
    ;; Else... Deposit
    (sstore (txsender) (+ (sload (txsender)) (- (txvalue) (* 20 (basefee)))) )
  )
)
```

### Creating and Using

Now, once this LLL code is in the Data of the transaction, it's time to actually create the contract on the block chain. To do this we simply "send" the transaction.

You'll see your contract-creation transaction become pending (see the _Pending Transactions_ pane), and after a little time, assuming there are miners on the network, you'll see it become written into the block chain (see the _Block Chain_ pane). Once in the block chain, you can interact with it! We'll make two deposit transactions and a withdrawal. You'll need to know the account address of your new contract. You'll see in initially the pending and then the block chain pane an item corresponding to your transaction; it will have one of your addresses (see _Owned Addresses_ pane; and remember it for later) followed by a "+>", denoting a contract creation transaction and then the new contract's address. Once your contract has been created, look down the list in _All Contracts_ until you find that same address of your new contract and double-click the item, which copies the full address into the clipboard.

For the first deposit, we'll deposit one ether (minus banking charges) with our newly created bank. Paste the freshly copied contract address into the _To_ box and make sure the _Amount_ reads "1 ether". Clear the _Data_ box as we're not making a withdrawal, then click _Send_ and wait for it to move onto the block chain from the _Pending Transactions_ (or just press the _Preview_ button on the toolbar if you can't wait).

Let's check that it went through alright; find your contract address in the _All Contracts_ pane and this time select it to inspect its storage. In here you'll see a big dump of EVM code which corresponds to the program we just authored and then one additional entry; this should be your address and it should denote that some considerable sum (a hex number next to your address, denominated in wei, and so it'll be quite big) is deposited for you.

Let's check that it goes up when we make a second deposit. We'll make the deposit by clicking _Send_ again and wait for the transaction to be mined onto the block chain (or ensure _Preview_ is depressed). Selecting the contract again from the _All Contracts_ pane to inspect it should show you a higher amount next to your address in its state than before. If it doesn't something has gone wrong with your contract and you should check its code.

Finally, we'll make a withdrawal of fund back to our account. Let's assume we want to take one ether back. All we must do is send a transaction to the contract making sure to pay the charge of 135 times the _basefee_ (100 szabo), and specify the amount to withdraw as a single item in the _Data_. So change the _Amount_ so it reads "13500 szabo" and write "1ether" (note that there's no space between the "1" and the "ether" - that's important) and click _Send_. Once that transaction goes through on to the block chain (or if you have _Preview_ down, then immediately) then you should see your account go up by an ether. If you then check your account with the bank (looking at the contract's state in _All Contracts_, you'll see that the amount associated with your address is reduced to just shy of an ether's worth of wei.

And so you've just learnt to write, create and interact with a contract in Ethereum PoC. While pointless, the code we've made can be used a base for all sorts of contracts - currencies, lending banks, interest-giving banks, even reputation systems. Your imagination is the limit.

