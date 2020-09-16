# NetsClient

The `NetsClient` is the main entry-point into the SDK. It gives access to all
the `Manager` types, and provides them with the right context.

The `NetsClient` is a strict "singleton", as it takes the context for the 
`Manager`s, which is an `ActivityResultCaller`, a fairly new addition to the
Android library in the 1.3.0 version of `androidx.fragment` and 1.2.0 version of
`androix.activity`. Both of these dependencies are included with the SDK.
Framents and activities are both `ActivityResultCaller`s.

Since `NetsClient` is a singleton, it is created with the object function 
`NetsClient.create(context: ActivityResultCaller)`, which is marked as 
`@JvmStatic` so it is available similarly in both Java and Kotlin. If another
`NetsClient` already exists, a `ClientNotDisposedException` is thrown to notify
you that there is an issue.

If you already have a `NetsClient`, and you need to access it again, you can use
`NetsClient.instance` in Kotlin, or `NetsClient.getInstance()` in Java - 
however, since it has a link to the original context, do no use it to create new
transactions in other contexts (both payment and refund manager is one-time use
anyway, so that wouldn't work). If you call `NetsClient.instance` and no
`NetsClient` has been created, a `ClientNotCreatedException` is thrown.

## Accessing the managers

Each of the available `Manager`s (at the time of writing, these are 
`PaymentManager`, `RefundManager`, `StatusManager` and `EndOfDayManager`) are
available from the `NetsClient`, and are instantiated as needed, automatically
by lazy loading. They all follow the same pattern, which is, as usual,
`client.endOfDayManager` in Kotlin and `client.getEndOfDayManager()` in Java.

Each of the `Manager`s are described in their respective sections;
[payment](/payment), [refund](/refund), [status](/transaction_status) and
[end of day](/end_of_day), along with the payloads for the given transaction.
