For Ethereum to allow currency services (including wallets, exchanges, payment mechanisms), it is important that each coin contract satisfy a general interface. This is called the Ethereum MetaCoin API and is described here.

The interface has several calls and uses the 32-byte string method-name calling convention to differentiate between them.

* `"balance" <address>` Returns the current balance of `<address>`.
* `"send" <to-address> <value>` Transfers `<value>` currency units from caller to `<to-address>`. Returns 1 on success, 0 on failure.
* `"send" <to-address> <value> <from-address>` Transfers `<value>` currency units from `<from-address>` to `<to-address>`. In general, there should be additional security requirements (e.g. `<from-address>` should equal the originating address), or allowing only particular caller/`<from-address>` combinations through the approval mechanism, denoted below. Returns 1 on success, 0 on failure.

### Approve API

An additional API for approving third-parties (contracts or external entities) to issue `send`s in proxy for your account exists. This is similar to the direct-debit system in the UK. This is more a recommendation than a standard.

* `"approve" <address> <enable>` If `<enable>` is non-zero, allows `<address>` to issue `send` API calls on this interface on behalf of the caller (though `send` may possibly have additional safeguards in place). If `<enable>` is zero, resets the state so that `<address>` cannot issue proxy-`send`s.
* `"approved" <address>` Returns 1 if `<address>` is approved for issuing proxy `send`s funded by the caller, 0 otherwise.
