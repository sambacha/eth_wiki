### API

The interface has several calls and uses the 32-byte string method-name calling convention to differentiate between them.

Currencies are specified as their currency contract address. Currency contracts must conform at least to the `send` call of the [[MetaCoin API]]. If a currency contract is specified as `0`, then it is understood as being ETH (value denominated in Wei).

Placing an order is conducted with `new`, you can remove an order with `delete` and find the current best price for a particular exchange with `price`.

* `"new" <offer-currency> <offer-value> <want-currency> <want-value>` Creates a new order in the order book. Attempts to fulfil it instantly, if it cannot, registers it in the order book ready for others to match against.
In the case of the offer currency being ETH, the `<offer-value>` is ignored and the `callvalue` is used instead. If it is not ETH, then a call is immediately made into the contract to secure the funds (if this fails for whatever reason, the operation will be aborted).
* `"delete" <offer-currency> <want-currency>` Removes the best order on the order book of `<offer-currency>` to `<want-currency>` owned by caller.
* `"price" <offer-currency> <want-currency>` Returns the current best price (irrespective of volume) for exchange `<offer-currency>` for `<want-currency>`.


