#Payments

This is a more detailed description of payments, the payloads and properties,
and the different ways to start payment processing.

## Payment Payloads

There are two payloads for making a payment, the `PaymentData` and the 
`PaymentResult` payloads. Both of these are fairly simple, parcelable 
`data class`es that simply cary information about the payment.

### PaymentData

The `PaymentData` payload is the payload that initialises a payment in the Nets
SmartPOS ecosystem and is designed to carry the most important information as
properties, as well as the possibility to include additional data as auxiliary
data.

The properties of `PaymentData` is as follows:

|Property|Description|Data type|Required|Default value|
|--------|-----------|---------|--------|-------------|
|`uuid`|The UUID is the unique ID for this payment, and will be returned as in
the result payload. This makes it easy to match payments and results in case of
crashes and for persistence.|`java.util.UUID`|Yes\*|Random UUID|
|`amount`|The amount of this transaction, without value added tax, in
hundredths of the main unit of the `currency`. That means that if the payment is
9.95 euros excl. VAT, the `amount` should be "995".|`Long`|Yes|N/A|
|`vat`|The amount of value added tax, or VAT, for this payment. Provided in the
same form as the `amount`.|`Long`|Yes|N/A|
|`currency`|The three letter ISO 4217 code for this payment. Not all payment
methods will allow for all currencies|`String`|Yes|N/A|
|`aux`|Any information required to be passed with the payment, either for 
logging or for specific information required by specific payment methods can be
included in the `aux` map.|`Map<String, 
eu.nets.lab.smartpos.sdk.payload.AuxValue>`|No|Empty map|
|`requestedMethod`|If the ECR application wishes to ask the Selection app to 
automatically pick a payment method, this optional property is available. The
Selection application can't make any promises, if the requested method isn't
installed, the user will be asked to select payment method regardless.|
`eu.nets.lab.smartpos.sdk.payload.TargetMethod`|No|`null`|
