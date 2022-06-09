To make your ÐApp work with on Ethereum, you'll need to know about the Ethereum Javascript bindings, or, if you like, magic Javascript objects. These are pretty horrible for PoC-5, rest assured, they'll become nicer as time goes on. In the meantime, bear with us.

There are four such objects; here they are:

* `eth` does most of the magic.
* `u256` manages operations on 256-bit integers, used for holding amounts of ETH and looking into account storage.
* `key` manages operations on keys and, in particular, addresses.
* `bytes` lets you concatenate two bunches of bytes. Used for composing the data for a transaction.

### eth
* `coinbase()` Returns the coinbase address of the client.
* `isListening()` Returns true if and only if the client is actively listening for network connections.
* `isMining()` Returns true if and only if the client is actively mining new blocks.
* `balanceAt(_a)` Returns the balance of the account of address given by the special address object `_a`.
* `storageAt(_a, _x)` Returns the value in storage at position given by the special 256-bit number `_x` of the account of address given by the special address object `_a`.
* `txCountAt(_a)` Returns the number of transactions send from the account of address given by the special address object `_a`.
* `isContractAt(_a)` Returns true if the account of address given by the special address object `_a` is a contract-account.
* `gasPrice()` Returns the special 256-bit number equal to the hard-coded testnet price of gas.
* `key()` Returns the special key-pair object corresponding to the prefered account owned by the client.
* `keys()` Returns a list of the special key-pair objects corresponding to all accounts owned by the client.
* `peerCount()` Returns the number of peers current connected to the client.
* `create(_sec, _xEndowment, _bBody, _bInit, _xGas, _xGasPrice)` Creates a new contract-creation transaction, given parameters:
  * `_sec`, the special secret-key object for the sender;
  * `_xEndowment`, the special 256-bit number equal to the contract's endowment;
  * `_bBody`, the special byte-array object containing EVM-bytecode for the body of the contract;
  * `_bInit`, the special byte-array object EVM-bytecode for the initialisation of the contract;
  * `_xGas`, the special 256-bit number equal to the amount of gas to purchase for the transaction (unused gas is refunded); and
  * `_xGasPrice`, the special 256-bit number equal to the price of gas for this transaction.
  Returns the special address object representing the new contract's account.
* `transact(_sec, _xValue, _aDest, _bData, _xGas, _xGasPrice)` Creates a new message-call transaction, given parameters:
  * `_sec`, the special secret-key object for the sender;
  * `_xValue`, the special 256-bit number equal to the value transfered for the transaction;
  * `_aDest`, the special address object representing the destination address of the message;
  * `_bData`, the special byte-array object containing the associated data of the message;
  * `_xGas`, the special 256-bit number equal to the amount of gas to purchase for the transaction (unused gas is refunded); and
  * `_xGasPrice`, the special 256-bit number equal to the price of gas for this transaction.
* `changed.connect(_fn)`: Registers _fn as a callback for whenever the state changes, and also on the initial load. 

### u256

* `add(_x1, _x2)` Returns the sum of `_x1` and `_x2`, all special 256-bit numbers.
* `sub(_x1, _x2)` Returns the subtraction of `_x2` from `_x1`, all special 256-bit numbers.
* `mul(_x1, _x2)` Returns the product of `_x1` and `_x2`, all special 256-bit numbers.
* `div(_x1, _x2)` Returns the quotient of `_x1` and `_x2`, all special 256-bit numbers.
* `value(_n)` Returns a special 256-bit number of value equal to `_n`, a normal number.
* `ether(_n)` Returns a special 256-bit number that has the value of `_n`, a normal number times Ether (10^18).
* `toValue(_x)` Returns a normal number, equal to the number of Ether of `_x`, a special 256-bit number.
* `toEther(_x)` Returns a normal number, equal to the number of Ether of `_x`, a special 256-bit number.
* `ethOf(_x)` Returns a string that nicely renders the Ether balance given by the special 256-bit number `_x`.
* `stringOf(_x)` Returns a string that dumbly renders the Ether balance given by the special 256-bit number `_x`.
* `bytesOf(_x)` Returns the 32-bytes (as a special byte-array object) that form the Big Endian representation of the special 256-bit number `_x`.
* `fromHex(_s)` Returns the the special 256-bit number equal to the number contained in the normal string `_s` when interpreted with a big-endian hexadecimal representation.
* `fromAddress(_a)` Returns the special 256-bit number equal to the special address object `_a` when interpreted as a Big Endian number.
* `toAddress(_x)` Returns an address corresponding to the u256 number `_x`
* `isNull(_x)` Returns a boolean which is true if the 256_bit number `_x` is zero.

### key

* `create()` Returns a special key-pair object, whose value is a new randomly created secret-key and the corresponding address.
* `secret(_pair)` Returns a special secret-key object, equal to the secret-key component of the special key-pair object `_pair`.
* `address(_pair)` Returns a special address object, equal to the address component of the special key-pair object `_pair`.
* `keypair(_sec)` Returns a special key-pair object, whose secret-key component is equal to the special secret-key object `_sec`. The address component is derived accordingly.
* `isNull(_a)` Returns false if the special address object, `_a` is empty and true otherwise.
* `addressOf(_s)` Returns the special address object given by the normal string `_s`, which is assumed to contain a 40-character hexadecimal address.
* `stringOf(_a)` Returns the normal string that is equal to the 40-byte hexadecimal representation of the special address object `_a`.
* `toAbridged(_a)` Returns the normal string that is equal to a shortened form of the 40-byte hexadecimal representation of the special address object `_a`.

### bytes

* `concat(_b1, _b2)` Returns a special byte-array object equal to the concatenation of the two byte-array objects `_b1` and `_b2`.
* `toString(_b)` Returns a string constructed from the byte-array `_b`
* `fromString(_s,_n)` Returns a byte-array equivalent of string `_s` which is `_n` bytes long 
* `u256of(_b)` Returns the u256 number equivalent of the byte-array `_b`

## Example

A simple HTML snippet that will display the user's primary account balance of Ether:
```
<div>You have <span id="ether">?</span>.</div>
<script>
eth.changed.connect(function() {
    document.getElementById("ether").innerText = u256.ethOf(eth.balanceAt(key.address(eth.key())))
});
</script>
```

To test it, just put `<html><body>` before it and `</body></html>` after, then save the file. Load it in AlethZero PoC-5 and point the URL to file:///WHEREVER_YOU_SAVED_IT

Job done. Now go create.
