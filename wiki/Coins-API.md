### API

The interface has a single call, which registers a contract as fulfilling the [[MetaCoin API]].

* `<currency-code>` Registers the calling contract as a currency with upper-case three-letter code `<currency-code>`. If this code is greater than three letters the operation fails.
