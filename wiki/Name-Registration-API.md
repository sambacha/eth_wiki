### API

The interface has several calls and uses the 32-byte string method-name calling convention to differentiate between them.

All strings are assumed to be 32-bytes maximum length.

Registering a name is conducted with `register`, you can remove an existing name you registered with `unregister`. Calling the contract with a single argument returns the look up of that name/address.

* `"register" <name>` Associates the calling address with `<name>`. If `<name>` already exists this fails. If calling address already has a name attached, that name is unregistered first (and thus becomes free for others to register).
* `"unregister"` Removes any name association from the calling address, freeing up any previously associated name for others to use.
* `<address>` Returns the name associated with `<address>` (if any).
* `<name>` Returns the address associated with `<name>` (if any).

