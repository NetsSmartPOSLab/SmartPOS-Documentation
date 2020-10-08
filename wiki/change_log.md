# Change log

## 1.0.18

- `PaymentData`, `PaymentResult`, `RefundData` and `RefundResult` now all 
inherit from the `AuxPayload` interface that requires that they have an `aux`
property of type `Map<String, AuxValue>`, as they've always had, but the
interface type can be used as parameter type for instance for printing.

## 1.0.17

- `NetsConverters now support nullable `UUID`s.
- Improvements to utility linter

## 1.0.16

The ActivityResultContract API from Google has throws exceptions now if used as
done previously, to avoid results getting lost if an activity is closed in the
background. That means we need to register callbacks before a lifecycle-owner
is put into the `STARTED` state. This means the following changes:

- All `Manager` implementations now have the former `process` call split into
`register` and `process` to align with the changes to the ActivityResultContract
API.
- All `Manager` `process` Java overload is now an overload of the `register`
function instead.

Aside from this, changes include utility convenience functions and some custom
linting.
