# Transaction Status

This is a detailed description of transaction status queries. These differ from
other transactions in the Nets SmartPOS ecosystem, as they aren't instantiating
anything, and you typically won't see a change in UI, though it may happen. 
There will, however, be no user interaction with the Selection app, even if it
does show a loading UI for a bit.

## Transaction Status Payloads

There is only one payload for transaction status, because the resulting payload
will be one of the payment or refund payloads.

### TransactionStatusRequest

The `TransactionStatusRequest` is the payload for making transaction status
requests. Its 4 properties allows for many different kinds of transaction stati
to be returned.

|Property|Description|Data type|Required|
|--------|-----------|---------|--------|
|`uuid`|If a UUID is provided, only transactions matching that will be considered|`java.util.UUID`|No|
|`type`|What type of transaction is being request; payment, refund or either\*|`TransactionStatusRequestType`|Yes|
|`info`|What kind of payload is requested. Typically, this would be the result payload|`TransactionStatusRequestInfo`|Yes|
|`pingCardTransaction`|If this is set to true, the SDK will ping the card payment application for a status, if a result is missing for the given transaction|`Boolean`|Yes|

\* End-of-day is not currently supported

### Behaviour

Depending on what is being requested, 1 of 5 types of `TransactionPayload` will
be returned; `PaymentData`, `PaymentResult`, `RefundData`, `RefundResult` and
the special `TransactionPayload.NoPayload` type.

What is returned is largely dependent on what is being asked. If `type` is set
to `PAYMENT`, only `PaymentData`, `PaymentResult` and `NoPayload` can be 
returned, and similarly for `REFUND`. If `BOTH` is selected, there will be no
filtering for type done.

If `uuid` is not `null`, only payloads matching that specific `uuid` will be 
returned, otherwise the latest payload that matches the filters will be 
returned. There can be several result payloads for a `uuid`, but only the newest
are ever returned (at a later time, we can implement functionality to return
a list of all result payloads matching a `uuid` or matching the `uuid` of the
latest data payload).

The `info` filter only allows for searching for either data or result payloads,
since the result payload includes its corresponding data payload regardless.

`pingCardTransaction` is a boolean flag that can be enabled, and if so, whenever
a result payload is requested, if that result payload is missing, the SDK will
try to ping the card payment application for information about that transaction.
Currently, if not provided with a `uuid` this only happens if there are no 
stored transactions at all.

### Future

There are a lot of improvements that can be made to the transaction status 
requests, and some will be listed here.

The `pingCardTransaction` without a `uuid` could possibly search for the newest
data and see if that has a matching result first.

Currently the transactions are stored forever, which probably shouldn't be the
case.

Any other ideas can be submitted to @Simon Malone
