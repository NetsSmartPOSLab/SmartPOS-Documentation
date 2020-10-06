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

The properties of `PaymentData` are as follows:

|Property|Description|Data type|Required|Default value|
|--------|-----------|---------|--------|-------------|
|`uuid`|The UUID is the unique ID for this payment, and will be returned as in the result payload. This makes it easy to match payments and results in case of crashes and for persistence.|`java.util.UUID`|Yes\*|Random UUID|
|`amount`|The amount of this transaction, without value added tax, in hundredths of the main unit of the `currency`. That means that if the payment is 9.95 euros excl. VAT, the `amount` should be "995".|`Long`|Yes|N/A|
|`vat`|The amount of value added tax, or VAT, for this payment. Provided in the same form as the `amount`.|`Long`|Yes|N/A|
|`currency`|The three letter ISO 4217 code for this payment. Not all payment methods will allow for all currencies|`String`|Yes|N/A|
|`aux`|Any information required to be passed with the payment, either for logging or for specific information required by specific payment methods can be included in the `aux` map.|`Map<String, AuxValue>`\*\*|No|Empty map|
|`requestedMethod`|If the ECR application wishes to ask the Selection app to automatically pick a payment method, this optional property is available. The Selection application can't make any promises, if the requested method isn't installed, the user will be asked to select payment method regardless.| `TargetMethod`\*\*|No|`null`|

\* The UUID is required, but the builder will provide a random one if used.

\*\* See [types](/types) for information about the custom types of the SDK.

#### Builder

A "Builder" is available for all payload types, including `PaymentData`. To not
repeat the same over and over again, a special [builders](/builders) page is
available.

### PaymentResult

The `PaymentResult` is the corresponding payload to `PaymentData`. It is 
designed to return all the necessary data as properties, as well as a lot of
auxiliary data that depends on the selected payment method, so cannot be 
normalised.

The properties of `PaymentResult` are as follows:

|Property|Description|Data type|Required|
|--------|-----------|---------|--------|
|`uuid`|The UUID is the unique ID for this payment, and will match the `PaymentData` that initiated this payment, unless a crash caused the payment to fail.|`java.util.UUID`|Yes|
|`status`|The payment status of the payment.|`PaymentStatus`\*|Yes|
|`method`|The payment method that performed this payment attempt, regardless of status.|`TargetMethod`\*\*|Yes|
|`epochTimestamp`|The timestamp in the form of milliseconds since January 1st, 1970, 00:00 UTC|`Long`|Yes|
|`aux`|All data not included elsewhere - can vary a lot depending on payment methods, so separate documentation for this will be made|`Map<String, AuxValue>`\*\*|No|
|`data`|The `PaymentData` payload that initiated this result|`PaymentData`|Yes|

\* See below for information about `PaymentStatus`

\*\* See [types](/types) for information about the custom types of the SDK.

#### Builder

A "Builder" is available for all payload types, including `PaymentResult`.
To not repeat the same over and over again, a special [builders](/builders) page
is available.

## Using PaymentManager

To initialise payments, the SDK provides the `PaymentManager` class, which is
available from the `NetsClient` ([link](/client)) object, using either
`client.paymentManager` in Kotlin or `client.getPaymentManager()` in Java.

The functionality of the `PaymentManager` is fairly simple; it provides the
`register` function that is used to register a callback for when a payment 
attempt is completed, as well as an override for
Java interoperability. Furthermore, it contains the `process` function to
actually start processing the payment attempt.
It also stores the provided `PaymentData`, which is why
the `PaymentManager` is a single-use. The `PaymentData` is available after the
payment attempt has been finalised using `manager.data` in Kotlin or
`manager.getData()` in Java.

Using the `PaymentManager` is simple: 
In the `onCreate` function or in the `init` block/Java constructor, register the
callback that will handle the result using `register`. Then, once the 
app-switching should happen, call the `process` function.
The SDK is built with Kotlin-first in mind,
but an override for `register` is available for Java using the functional 
`SmartPosConsumer` interface, both as a consumer with a lambda or as common in 
many older Android applications by implementing it.

An example could be:

```kotlin
(...)
lateinit var paymentManager: PaymentManager
override fun onCreate(savedInstanceState: Bundle?) {
    val client = NetsClient.create(this)
    this.paymentManager = client.paymentManager
    client.dispose()
    this.paymentManager.register {
        // Some logic to handle paymentResult
    }
}
(...)
```

And then later, when the app-switch should happen


```kotlin
(...)
val data = paymentData {
    uuid = UUID.randomUUID()
    amount = 100L
    vat = 25L
    currency = "EUR"
}
manager.process(data) 
```

or in Java

```java
class TestActivity extends AppCompatActivity {
    PaymentManager paymentManager;
    @Override
    public void onCreate(Bundle savedInstanceState) {
        NetsClient client = NetsClient.create(this);
        this.paymentManager = client.getPaymentManager();
        client.dispose();
        this.paymentManager.register((PaymentResult result) -> System.out.println(result.toString()));
    }

    public void test() {
        PaymentData data = new PaymentData.Builder()
                .uuid(UUID.randomUUID())
                .amount(100L).vat(25L).currency("EUR")
                .build();
        try {
            manager.process(data)
        } catch (ClientNotDisposedException e) {
            e.printStackTrace();
        }
    }
}
```

or

```java
class TestActivity extends AppCompatActivity implements SmartPosConsumer<PaymentResult> {
    PaymentManager paymentManager;
    @Override
    public void onCreate(Bundle savedInstanceState) {
        NetsClient client = NetsClient.create(this);
        this.paymentManager = client.getPaymentManager();
        client.dispose();
        this.paymentManager.register(this);
    }

    public void test() {
        PaymentData data = new PaymentData.Builder()
                .uuid(UUID.randomUUID())
                .amount(100L).vat(25L).currency("EUR")
                .build();
        try {
            manager.process(data);
        } catch (ClientNotDisposedException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onSmartPosResponse(PaymentResult response) {
        System.out.println(response.toString());
    }
}
```

The handling of the payment result is very dependent on the specific ECR, so it
is omitted here.

## PaymentStatus

The following values exist for `PaymentStatus` and should be handled:

|Value|Description|
|-----|-----------|
|`PAYED`|The payment has been authorised and captured, the payment is fully complete|
|`AUTHORISED`|The payment has been authorised but not captured. Not currently returned to the ECR app|
|`DECLINED`|The payment was for some reason declined by the payment method. More info may be available in the `aux` map|
|`CANCELLED`|The payment was cancelled by the user|
|`NO_RESULT`|At some point during the process, the result was lost. Query the transaction ID to check for the status|
