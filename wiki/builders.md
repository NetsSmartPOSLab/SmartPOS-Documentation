# Builders

For all the payloads, there are builders following the classic Java builder
pattern.

They all follow the same pattern of having methods named after the payload
properties, as well as public getters and setters for the individual properties
in the builder.

For the purposes of this, we will use the `PaymentData.Builder` as an example,
but they are all similar.

As with the entire SDK, it is built Kotlin-first, however, a lot of thought has
been put into it also feeling very native to Java, as the builder pattern is 
more needed in Java.

Therefore, it is easily possible to make a `PaymentData` object in both Java and
Kotlin respectively like so:

```java
PaymentData data = new PaymentData.Builder()
        .uuid(UUID.randomUUID)
        .amount(100L).vat(25L).currency("EUR")
        .aux(Collections.singletonMap("akey", new AuxString("avalue")))
        .requestedMethod(TargetMethod.CARD)
        .build();
```
or

```kotlin
val data = PaymentData.Builder()
        .uuid(UUID.randomUUID)
        .amount(100L).vat(25L).currency("EUR")
        .aux(mapOf("akey" to "avalue".toAux))
        .requestedMethod(TargetMethod.CARD)
        .build()
```

However, to smarten the Kotlin development, there is also a DSL annotated 
top-level function that provides the same functionality but in a format much
more similar to those used by the Kotlin standard library. Therefore, the same
implementation can also look as follows in Kotlin

```kotlin
val data = paymentData {
    uuid = UUID.randomUUID()
    amount = 100L
    vat = 25L
    currency = "EUR"
    aux(mapOf("akey" to "avalue".toAux))
    requestedMethod = TargetMethod.CARD
}
```

There is no need to call a `build` function, since it is called automatically.

## Kotlin "aux" extensions

As can be seen from the previous example, the `aux` property of the builder is
not a variable in the context of the builder, and as such, we have to use the
`aux` function instead of setting it using the usual setter.

This, however enables some smart infix extension functions on the `aux` property
to add entries to it in a very verbose manner.

The first extension function is the `copyFrom` extension function, which simply
just copies another `Map<String, AuxValue>` into this builder's `aux` property.

The second extension function is `put` that takes a key `string` and creates a 
function type of `(AuxValue) -> Unit` which can then be fed an `AuxValue` that
is then added for the given key value.

The third function `value`, and its overloads, are extensions of the above
mentioned function type, which allows for an infix way of feeding the `AuxValue`
to the function. Examples of all three can be seen below

```kotlin
val data = paymentData {
    (...)
    this.aux copyFrom anotherAux
    this.aux put "akey" value "avalue"
}
```
