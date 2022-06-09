To make your ÐApp work with on Ethereum, you'll need to know about the Ethereum Javascript bindings, or, if you like, magic Javascript objects. These are pretty simple for PoC-5, rest assured, they'll become nicer as time goes on. In the meantime, bear with us.

There is currently only one such object; the `eth` object.

### Parameters

Parameters are, in general, strings which are interpreted according to the required type. When converting between integers and hashes/byte-arrays, it is interpreted as a big-endian as is standard for Ethereum. The following forms are allowed; they are all interpreted in the same way:

* `"ABC"`
* `"0x414243"`
* `"4276803"`

In each case, they are interpreted as the number 4276803. The first two values may be alternated between with `bin()` and `unbin()`.

Such byte-arrays may be concatenated with the `+` operator as is normal for strings.

Strings also have a number of additional methods to help with conversion and alignment when switching between addresses, 256-bit integers and free-form byte-arrays for transaction data:

* `bin()`: Converts the string to binary format.
* `pad(a, b)`: Converts the string to binary format (ready for data parameters) and pads with zeroes on the left size until it is of width `a`. Then pads with zeroes on the right side until it has grown to size `b`. If `b` is less that the width of the string, it is resized accordingly.
* `pad(l)`: Converts the string to binary format (ready for data parameters) and pads with zeroes until it is of width `l`. Will pad to the left if the original string is numeric, or to the right if binary. If `l` is less than the width of the string, it is resized accordingly.
* `unbin()`: Converts the string from binary format to hex format.
* `unpad()`: Converts the string from binary format to hex format, first removing any zeroes from the right side.
* `dec()`: Converts the string to decimal format.

### eth

**Properties**: For each such item, there is also an asynchronous method, taking a parameter of the callback function, itself taking a single parameter of the property's return value and of the same name but prefixed with get and recapitalised, e.g. `getCoinbase(_fn)`.

* `coinbase` Returns the coinbase address of the client.
* `isListening` Returns true if and only if the client is actively listening for network connections.
* `isMining` Returns true if and only if the client is actively mining new blocks.
* `gasPrice` Returns the special 256-bit number equal to the hard-coded testnet price of gas.
* `key` Returns the special key-pair object corresponding to the preferred account owned by the client.
* `keys` Returns a list of the special key-pair objects corresponding to all accounts owned by the client.
* `peerCount` Returns the number of peers currently connected to the client.

**Synchronous Getters**: For each such item, there is also an asynchronous method, taking an additional parameter of the callback function, itself taking a single parameter of the synchronous method's return value and of the same name but prefixed with get and recapitalised, e.g. `getBalanceAt(_a, _fn)`.

* `balanceAt(_a)` Returns the balance of the account of address given by the address `_a`.
* `storageAt(_a, _x)` Returns the value in storage at position given by the number `_x` of the account of address given by the address `_a`.
* `txCountAt(_a)` Returns the number of transactions send from the account of address given by `_a`.
* `isContractAt(_a)` Returns true if the account of address given by `_a` is a contract-account.

**Transactions**

* `create(_sec, _xEndowment, _bCode, _xGas, _xGasPrice, _fn)` Creates a new contract-creation transaction, given parameters:
  * `_sec`, the secret-key for the sender;
  * `_xEndowment`, the number equal to the contract's endowment;
  * `_bCode`, the byte-array EVM-bytecode for the initialisation of the contract;
  * `_xGas`, the number equal to the amount of gas to purchase for the transaction (unused gas is refunded); and
  * `_xGasPrice`, the number equal to the price of gas for this transaction.
  * `_fn`, the callback function, called on completion of the transaction with a single argument; the address of the new account.
* `transact(_sec, _xValue, _aDest, _bData, _xGas, _xGasPrice, _fn)` Creates a new message-call transaction, given parameters:
  * `_sec`, the secret-key for the sender;
  * `_xValue`, the value transferred for the transaction (in Wei);
  * `_aDest`, the address representing the destination address of the message;
  * `_bData`, the byte array containing the associated data of the message;
  * `_xGas`, the amount of gas to purchase for the transaction (unused gas is refunded); and
  * `_xGasPrice`, the price of gas for this transaction.
  * `_fn`, the callback function, called on completion of the transaction.

**Events**

* `watch(_a, _fn)`: Registers _fn as a callback for whenever anything about the given address's state changes, and also on the initial load. 
* `watch(_a, _x, _fn)`: Registers _fn as a callback for whenever the given storage location of the given address changes, and also on the initial load.
* `newBlock(_fn)`: Registers _fn as a callback for whenever the state changes, and also on the initial load.

**Misc**

* `secretToAddress(_a)`: Determines the address from the secret key `_a`.
* `lll(_s)`: Compiles the LLL source code `_s` and returns the binary output.

## Example

A simple HTML snippet that will display the user's primary account balance of Ether:
```html
<div>You have <span id="ether">?</span>.</div>
<script>
eth.changed.connect(function() {
    document.getElementById("ether").innerText = eth.balanceAt(eth.secretToAddress(eth.key))
});
</script>
```

To test it, just put `<html><body>` before it and `</body></html>` after, then save the file. Load it in AlethZero PoC-5 and point the URL to file:///WHEREVER_YOU_SAVED_IT

Job done. Now go create.