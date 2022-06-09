To make your ÐApp work with on Ethereum, you'll need to know about the Ethereum Javascript bindings, or, if you like, magic Javascript objects. These are still pretty simple for PoC-6, rest assured, they'll become nicer as time goes on. In the meantime, bear with us.

There is currently only one such object; the `eth` object.

## Parameters

Parameters are always data represented as hex, prefixed with an `0x`. There's automatic conversion from decimal strings to the hex representation (interpreted as a big-endian as is standard for Ethereum). So, the following two forms are identically interpreted:

* `"0x414243"`
* `"4276803"`

In each case, they are interpreted as the number 4276803. To convert to or from other datatypes, there are a number of conversion functions, detailed later.

## eth

**Properties**: For each such item, there is also an asynchronous method, taking a parameter of the callback function, itself taking a single parameter of the property's return value and of the same name but prefixed with get and recapitalised, e.g. `getCoinbase(_fn)`.

###### coinbase
* `coinbase` Coinbase address of the client.
* `getCoinbase(_fn)` Asynchronous, returns coinbase address of the client.
* `setCoinbase(_address, _fn)` Asynchronous, set new coinbase address.

###### listening

* `listening` Actively listening for network connections.
* `getListening(_fn)` Asynchronous, returns true if and only if the client is atively listening for network connections.
* `setListening(_listening, _fn)` Asynchronous, set listening for network connections.

###### mining

* `mining` Client is actively mining new blocks.
* `getMining(_fn)` Asynchronous, returns true if and only if the client is actively mining new blocks.
* `setMining(_mining, _fn)` Asynchronous, set if client should mine.

###### gasPrice

* `gasPrice` Returns the special 256-bit number equal to the hard-coded testnet price of gas.
* `getGasPrice(_fn)` Asynchronous

###### key

* `key` Returns the special key-pair object corresponding to the preferred account owned by the client.
* `getKey(_fn)` Asynchronous

###### keys

* `keys` Returns a list of the special key-pair objects corresponding to all accounts owned by the client.
* `getKeys(_fn)` Asynchronous

###### peerCount

* `peerCount` Returns the number of peers currently connected to the client.
* `getPeerCount(_fn)` Asynchronous

###### defaultBlock

* `defaultBlock` The default block number/age to use when querying state. When positive this is a block number, when 0 or negative it is a block age. -1 therefore means the most recently mined block, 0 means the block being currently mined (i.e. to include pending transactions). Defaults to -1.
* `getDefaultBlock(_fn)` Asynchronous

###### number

* `number` Returns the number of the most recent block.
* `getNumber(_fn)` Asynchronous


**Synchronous Getters**: For each such item, there is also an asynchronous method, taking an additional parameter of the callback function, itself taking a single parameter of the synchronous method's return value and of the same name but prefixed with get and recapitalised, e.g. `getBalanceAt(_a, _fn)`.

###### balanceAt

* `balanceAt(_a)` Returns the balance of the account of address given by the address `_a`. For example, `eth.toDecimal(eth.balanceAt('0x1d916bed61249f6c12f3ca8b3f78b8f4cedbe24b'))` will return the balance (in Wei) for that account's address.
* `getBalanceAt(_a, _fn)` Asynchronous

###### stateAt

* `stateAt(_a, _s)` Returns the value in storage at position given by the string `_s` of the account of address given by the address `_a`.
* `getStateAt(_a, _s, _fn)` Asynchronous

###### countAt

* `countAt(_a)` Returns the number of transactions send from the account of address given by `_a`.
* `getCountAt(_a, _fn)` Asynchronous

###### codeAt

* `codeAt(_a)` Returns true if the account of address given by `_a` is a contract-account.
* `getCodeAt(_a, _fn)` Asynchronous

The block number you wish to query can be given either as an extra parameter (or age if less than 1: you may use 0 to include pending transactions, use -1 to include only mined transactions &c.), or alternatively, you may use these without the extra parameter, in which case the state at the end of the most recently mined block will be used. This can be altered with the `defaultBlock` property.

**Transactions**

###### transact

* `transact(_params)` Creates a new message-call transaction.
  * `_params`, an anonymous object specifying the parameters of the transaction.
    * `from`, the secret-key for the sender;
    * `value`, the value transferred for the transaction (in Wei), also the endowment if it's a contract-creation transaction;
    * `endowment`, synonym for `value`;
    * `to`, the destination address of the message, left undefined for a contract-creation transaction;
    * `data`, either a byte array or an array of values, to be 32-byte aligned, containing the associated data of the message, or in the case of a contract-creation transaction, the initialisation code;
    * `code`, a synonym for `data`;
    * `gas`, the amount of gas to purchase for the transaction (unused gas is refunded), defaults to the most gas your ether balance allows; and
    * `gasPrice`, the price of gas for this transaction, defaults to the mean network gasPrice.
  * `return` If the transaction was a contract-creation transaction, it is a single argument; the address of the new account.
* `makeTransact(_params, _fn)` Asynchronous
  * `_fn`, the callback function, called on completion of the transaction. If the transaction was a contract-creation transaction, it is passed with a single argument; the address of the new account.

###### call

* `call(_params)` Executes a new message-call immediately without creating a transaction on the block chain.
  * `_params`, an anonymous object specifying the parameters of the transaction, similar to that above.
  * `return` Output data of the message call.
* `makeCall(_params, _fn)` Asynchronous
  * `_fn`, the callback function, called on completion of the message call. Output data of the message call.

**Watches and Message Filtering** Past messages may be filtered and their attributes inspected, and future messages (and the changes they implicitly bring) may be notified of.

###### messages

* `messages(_filter)`: Returns the list of messages in Ethereum matching the given `_filter`. The filter is an object including fields:
  * `earliest`: The number of the earliest block (-1 may be given to mean the most recent, currently mining, block).
  * `latest`: The number of the latest block (-1 may be given to mean the most recent, currently mining, block).
  * `max`: The maximum number of messages to return.
  * `skip`: The number of messages to skip before the list is constructed. May be used with `max` to paginate messages into multiple calls.
  * `from`: Either an address or a list of addresses to restrict messages by requiring them to be made from a particular account.
  * `to`: Either an address or a list of addresses to restrict messages by requiring them to be made to a particular account.
  * `altered`: Either an address, or an address/location object, or a mixed list of such values, to restrict messages by requiring them to have made an alteration, respectively either to an account, a particular contract's storage location, or one of a number of these. An address/location object is an object that contains two fields:
    * `id`: The address of the contract.
    * `at`: The location of a point in contract's storage.
  * Returns a list of past message objects; each includes the following fields:
    * `from`: The sender address of the message.
    * `to`: The recipient address of the message (or '' if it was a contract-creation message).
    * `input`: The input data to the message (the initialisation code if it was a contract-creation message).
    * `output`: The output data of the message (the body code if it was a contract-creation message).
    * `value`: The value associated with the message (in Wei, the endowment if it was a contract-creation message). [ C++ : TODO ]
    * `path`: The call-path of the message. The first entry is the transaction index into the block. The second, if there is one, is the index of the message within the transaction, and so on.
    * `origin`: The original sender of the transaction.
    * `timestamp`: The timestamp of the block in which the message takes place.
    * `coinbase`: The coinbase of the block in which the message takes place.
    * `block`: The hash of the block in which the message takes place.
    * `number`: The number of the block in which the message takes place.
* `getMessages(_filter, _fn)`: Asynchronous

###### watch

* `watch(_filter)`: Creates a watch object to notify when the state changes in a particular way, given by `_filter`. Filter may be a message filter object, as defined above. It may also be either `'chain'` or `'pending'` to watch for changes in the chain or pending transactions respectively. Returns a watch object with the following methods:
  * `changed(_f)`: Installs a handler, `_f`, which is called when the state changes due to messages that fit `_filter`.
  * `messages()`: Returns the messages that fit `_filter`.
  * `uninstall()`: Uninstalls the watch. Should always be called once it is done with.

**Misc**

* `secretToAddress(_a)`: Determines the address from the secret key `_a`.
* `lll(_s)`: Compiles the LLL source code `_s` and returns the output data.
* `sha3(_s)`: Returns the SHA3 of the given data.
* `toAscii(_s)`: Returns an ASCII string made from the data `_s`.
* `fromAscii(_s, _padding = 32)`: Returns data of the ASCII string `_s`, auto-padded to `_padding` bytes (default to 32) and left-aligned.
* `toDecimal(_s)`: Returns the decimal string representing the data `_s` (when interpreted as a big-endian integer).
* `toFixed(_s)`: Returns the floating-point number representing the data `_s` (when interpreted as a fixed-point value divided by 2^128).
* `fromFixed(_s)`: Returns data representing the floating-point number `_s` (when interpreted as a fixed-point value divided by 2^128).
* `offset(_s, _o)`: Returns data representing the data `_s` when offset by the integer `_o`.

## Example

A simple HTML snippet that will display the user's primary account balance of Ether:
```html
<div>You have <span id="ether">?</span>.</div>
<script>
eth.watch({altered: eth.secretToAddress(eth.key)}).changed(function() {
    document.getElementById("ether").innerText = eth.toDecimal(eth.balanceAt(eth.secretToAddress(eth.key)))
});
</script>
```

To test it, just put `<html><body>` before it and `</body></html>` after, then save the file. Load it in AlethZero PoC-6 and point the URL to file:///WHEREVER_YOU_SAVED_IT

Job done. Now go create.