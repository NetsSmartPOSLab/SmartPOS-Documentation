# Nets SmartPOS SDK types

This is just a short description of the types included in the SDK. More thorough
documentation is available in the KDoc documentation included with the SDK, and
which is also available from the 
[repository](https://creationlab.visualstudio.com/SmartPOS%20Feed/_packaging?_a=feed&feed=NetsSmartPos).

## AuxValue

The `eu.nets.lab.smartpos.sdk.payload.AuxValue` type is a "sealed class" 
supertype of different kinds of values that are possible to include as
auxiliary data in payloads. Below is a short description of the different
subtypes of `AuxValue`.

- `AuxNumber` is a numeral value, stored as a `Long`.
- `AuxString` is a string value, stored as a `String`.
- `AuxBoolean` is a boolean value, stored as a `Boolean`.
- `AuxMap` is a map of strings to `AuxValue`s, allowing for hierarchical 
auxiliary maps
- `AuxList` is a list of `AuxValue`s, allowing for lists of auxiliary data

In addition to the types, the SDK includes convenience extension values for
easy conversions from normal types to `AuxValue`s. These are all called `toAux`
and handles `null` values as well.

## TargetMethod

The `TargetMethod` enumeration class is an enumeration of *all* available
payment methods. Each payment method has a `TargetMethod` value, which can be
used to request specific payment methods when making payments, as well as for
persistence and more.

At the time of writing, the following values are included:

- `CARD` Card payments, the default payment method
- `NETS_INVOICE` Invoice at store payments provided by Nets NAA
- `FACEPAY` Nets facial recognition payments prototype, not in production
- `SWISH` Swish in store payments
- `CRASH` Method used when a crash prevents a proper payload result from being
returned
- `SIMULATION` Nets Payment simulation application, for testing SDK integration
- `CANCELLED` If a transaction is cancelled before a method is selected
