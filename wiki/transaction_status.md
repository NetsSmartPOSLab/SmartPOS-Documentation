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
|`type`|What type of transaction is being request; payment, refund or end-of-day\*|`TransactionStatusRequestType`|Yes|
|`info`|What kind of payload is requested. Typically, this would be the result payload|`TransactionStatusRequestInfo|Yes|
|`pingCardTransaction`|If this is set to true, the SDK will ping the card payment application for a status, if a result is missing for the given transaction|`Boolean`|Yes|

\* End-of-day is not currently supported
