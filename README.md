#Introduction

The purpose of this documentation is to give a brief introduction to the Nets
SmartPOS SDK prototype, which is an abstraction layer between an Android ECR
application (henceforth "ECR app") built for Nets SmartPOS devices (henceforth 
"SmartPOS"), and payment methods for the same device.

This means the use case where ECR app and payment method runs as separate
applications on the same device via application switching. The approach taken
here is made to be similar to the interface presented in SoftPay, another Nets
solution built for consumer devices.

The functionality of the Nets SmartPOS SDK (henceforth "SDK) is split into two
separate entities:

- The Nets SmartPOS SDK library
- The Nets Payment Selection application

The latter is provided through **TODO**, as a signed and packaged application
that runs independently on the device. There will follow a short description on
the purpose and functionality of the Nets Payment Selection application.

The former is the library that will be a dependency of the ECR app, and it will
help the communication between the ECP app and the Nets SmartPOS ecosystem, by
providing a "client" and payload models for all the supported functionality.

##Nets Payment Selection application

The Nets Payment Selection application (henceforth "Selection app") is a 
standalone application that is an abstraction between the ECR app and installed
payment methods.

The Selection app lies as the abstraction, as different merchants can have
different alternative payment methods installed, and as such, the Selection
app is responsible for keeping track of installed payment methods, as well as
propagating some commands to each of the payment methods where applicable.

If there isn't any alternative payment methods available, the Selection app will
not show its interface, but rather just send commands on to the card payment
application.

##Nets SmartPOS SDK

The following goes through the basic features of the SDK library.

###Gradle Installation

To use the SDK library, add it as a dependency to your Android project by adding
the following to your `gradle.build` files:

To your project `build.gradle` file, add the following

```
maven {
    url("https://creationlab.pkgs.visualstudio.com/2d5fd41c-2a79-4787-98dc-e0db6ec5c293/_packaging/NetsSmartPos/maven/v1")
}
```

to the `allprojects.repositories` block. To the module `build.gradle` file, add:

```
implementation("eu.nets.lab.smartpos:nets-smartpos-sdk:1.0.3")
```

to the `dependencies` block.

When this is done, make sure that the project syncs.

###Obtaining a client

The examples in the following will all use `Kotlin 1.3.72`, but it should be
fairly simple to use in Java as well. There is overloads available for the 
client functions that use higher-order functions, both that use `java.function`
for API level 24+ and one that use a custom callback functional interface, for
API level 23, the one that the Saturn S1000F1 uses.

The first step to make a payment is to obtain a `NetsClient` object. This can be
done from any activity or fragment, using the following:

```
val client = try {
    NetsClient.create(this)
} catch (e: ClientNotDisposedException) {
    NetsClient.instance.dispose()
    NetsClient.create(this)
}
```

This snippet does two things: it tries to create a new `NetsClient` using the
`create` function of the `NetsClient`'s companion object (like a `static` method
in Java). However, we enforce a limit of one NetsClient per task, so if one
exists, we access that with `NetsClient.isntance` and call `dispose` on this,
before creating a new instance. This is to ensure that the `context` of the
`NetsClient` is the current fragment or activity. In a real life scenario, it
would be wise to figure out why there already exist a client at this point, but
this is omitted here.

###Managers

Managers are what we call the objects that are actually responsible for managing
the app switching and the payloads that they utilise. Each different kind of
functionality has its own manager, and they each inherit from the `Manager`
interface. They are all available as "lazy" properties of the `NetsClient`,
meaning that they are available using `client.paymentManager` in Kotlin or
`client.getPaymentManager()` in Java. Each client has a simple `process` 
function (plus the two Java overloads) that is called when the actual app switch
should happen. It always take a `data` parameter of the request payload type of
that manager, and a callback consumer function of the result payload type. An
example of how to use `process` can be:

```
(...)
val paymentManager = client.paymentManager
try {
    paymentManager.process(data) {
        client.dispose()
        Log.i(LOG_TAG, "Payment status: ${it.status}")
    }
    Log.d(LOG_TAG, "payload: ${paymentManager.data}")
} catch (e: ManagerAlreadyUsedException) {
    Log.e(LOG_TAG, "Manager is used, dumpy")
}
```

What this snippet does it: it gets the payment manager from the client, using
the lazy-loaded property. It then calls `process` with a `data` parameter (more
on this coming up) and in the resulting callback, it disposes of the 
`NetsClient` and logs the payment result (probably not the desired functionality
in a real scenario). It does the `process` call inside a try-catch of a
`ManagerAlreadyUsedException`, since some managers are meant to be single use
and actually stores the provided payment data if needed for crash protection or
similar (examplified in the DEBUG log message).

###Payloads


