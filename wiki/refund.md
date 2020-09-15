# Refunds

This is a more detailed description of refunds, the payloads and properties,
and the different ways to start refund processing.

## Refund Payloads

There are two payloads for making a refund, the `RefundData` and the 
`RefundResult` payloads. Both of these are fairly simple, parcelable 
`data class`es that simply cary information about the refund.

### RefundData

The `RefundData` payload is the payload that initialises a refund in the Nets
SmartPOS ecosystem and is designed to carry the most important information as
properties, as well as the possibility to include additional data as auxiliary
data.

The properties of `RefundData` are as follows:

|Property|Description|Data type|Required|Default value|
|--------|-----------|---------|--------|-------------|
|`uuid`|The UUID is the unique ID for this refund, and will be returned as in the result payload. This makes it easy to match refunds and results in case of crashes and for persistence.|`java.util.UUID`|Yes\*|Random UUID|
|`totalAmount`|The total amount of this refundi, in hundredths of the main unit of the `currency`. That means that if the refund is 9.95 euros, the `totalAmount` should be "995".|`Long`|Yes|N/A|
|`currency`|The three letter ISO 4217 code for this refund. Not all payment methods will allow for all currencies|`String`|Yes|N/A|
|`method`|The payment method that should perform this refunda|`TargetMethod`\*\*|Yes|N/A|
|`aux`|Any information required to be passed with the refund, either for logging or for specific information required by specific payment methods can be included in the `aux` map.|`Map<String, AuxValue>`\*\*|No|Empty map|

\* The UUID is required, but the builder will provide a random one if used.

\*\* See [types](/types) for information about the custom types of the SDK.

#### Builder

A "Builder" is available for all payload types, including `RefundData`. To not
repeat the same over and over again, a special [builders](/builders) page is
available.

### RefundResult

The `RefundResult` is the corresponding payload to `RefundData`. It is 
designed to return all the necessary data as properties, as well as a lot of
auxiliary data that depends on the payment method, so cannot be normalised.

The properties of `RefundResult` are as follows:

|Property|Description|Data type|Required|
|--------|-----------|---------|--------|
|`uuid`|The UUID is the unique ID for this refund, and will match the `RefundData` that initiated this refund, unless a crash caused the refund to fail.|`java.util.UUID`|Yes|
|`status`|The refund status of the payment.|`RefundStatus`\*|Yes|
|`method`|The payment method that performed this refund attempt, regardless of status.|`TargetMethod`\*\*|Yes|
|`epochTimestamp`|The timestamp in the form of milliseconds since January 1st, 1970, 00:00 UTC|`Long`|Yes|
|`aux`|All data not included elsewhere - can vary a lot depending on payment methods, so separate documentation for this will be made|`Map<String, AuxValue>`\*\*|No|
|`data`|The `RefundData` payload that initiated this result|`RefundData`|Yes|

\* See below for information about `RefundStatus`

\*\* See [types](/types) for information about the custom types of the SDK.

#### Builder

A "Builder" is available for all payload types, including `RefundResult`. To not
repeat the same over and over again, a special [builders](/builders) page is
available.

## Using RefundManager

To initialise refunds, the SDK provides the `RefundManager` class, which is
available from the `NetsClient` ([link](/client)) object, using either
`client.refundManager` in Kotlin or `client.getRefundManager()` in Java.

The functionality of the `RefundManager` is fairly simple; it provides the
`process` function that is used to start the refund, as well as an override for
Java interoperability. It also stores the provided `RefundData`, which is why
the `RefundManager` is a single-use. The `RefundData` is available after the
refund attempt has been finalised using `manager.data` in Kotlin or 
`manager.getData()` in Java.

Using the `RefundManager` is simple: simply call the `process` function and
handle the response in the callback. The SDK is built with Kotlin-first in mind,
but an override is available for Java using the functional `SmartPosConsumer`
interface, both as a consumer with a lambda or as common in many older Android 
applications by implementing it.

An example could be:

```kotlin
(...)
val data = refundData {
    uuid = UUID.randomUUID()
    totalAmount = 100L
    currency = "EUR"
    method = TargetMethod.CARD
}
val client = NetsClient.create(this)
val manager = client.refundManager
manager.process(data) { refundResult -> 
    client.dispose() // Dispose of the NetsClient
    // Some logic to handle refundResult
}
```

or in Java

```java
class TestActivity extends AppCompatActivity {
    public void test() {
        RefundData data = new RefundData.Builder()
                .uuid(UUID.randomUUID())
                .totalAmount(100L).currency("EUR").method(TargetMethod.CARD)
                .build();
        try {
            NetsClient client = NetsClient.create(this);
            RefundManager manager = client.getRefundManager();
            manager.process(data, (RefundResult result) -> System.out.println(result.toString()));
        } catch (ClientNotDisposedException e) {
            e.printStackTrace();
        }
    }
}
```

or

```java
class TestActivity extends AppCompatActivity implements SmartPosConsumer<RefundResult> {
    public void test() {
        RefundData data = new RefundData.Builder()
                .uuid(UUID.randomUUID())
                .totalAmount(100L).currency("EUR").method(TargetMethod.CARD)
                .build();
        try {
            NetsClient client = NetsClient.create(this);
            RefundManager manager = client.getRefundManager();
            manager.process(data, this);
        } catch (ClientNotDisposedException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onSmartPosResponse(RefundResult response) {
        System.out.println(response.toString());
    }
}
```

The handling of the refund result is very dependent on the specific ECR, so it
is omitted here.

## RefundStatus

The following values exist for `RefundStatus` and should be handled:

|Value|Description|
|-----|-----------|
|`REFUNDED`|The refund succeeded successfully ;)|
|`DECLINED`|The refund was for some reason declined by the payment method. More info may be available in the `aux` map|
|`CANCELLED`|The refund was cancelled by the user|
|`NO_RESULT`|At some point during the process, the result was lost. Query the transaction ID to check for the status|
|`METHOD_UNAVAILABLE`|Requested payment method isn't available, or isn't available for refunds|
