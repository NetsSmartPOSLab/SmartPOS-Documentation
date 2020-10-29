# Printing Card Slips

From version 1.0.19, the SDK includes a utility that allows for easily printing
proper card receipt slips for card payments\*. This feature allows to simply
provide the result payload for a payment or a refund to the utility, and it will
print an accepted\*\* payment slip.

> This feature is still in beta, and more work will be done to make sure that it
> will always print a valid, certified card receipt slip.

## Printing a payment receipt slip

When you want to print a card payment receipt slip, the first thing is to 
create a `CardSlipPrinter` from the `eu.nets.lab.smartpos.sdk.utility` package.

An example for how to create this, in Kotlin could look like this:

```kotlin
// 'payload' is a PaymentResult returned by the Nets SmartPOS SDK
// 'printer' is a CtPrint object, used by the ECR app to print the product 
// receipt (CtPrint is included in the SDK library since 1.0.19)
val slipPrinter = CardSlipPrinter(printer, payload, Locale.US)

// We provide the payload status, as it is specific for PaymentResult and thus
// not defined in the AuxPayload interface
slipPrinter.printSaleSlip(payload.status)
```

Or in Java

```java
CardSlipPrinter slipPrinter = CardSlipPrinter(printer, payload, Locale.US)

slipPrinter.printSaleSlip(payload.getStatus())
```

Similarly, the functions `printCustomerRefundSlip` and `printMerchantRefundSlip`
takes a `RefundStatus` and prints the customer and merchant refund slips. This
has been split into two, to allow the ECR app control, if the merchant wishes
to rip off the customer receipt (as the Castles SmartPOS device doesn't cut
receipts) and print extra information for the merchant slip if they want to.
