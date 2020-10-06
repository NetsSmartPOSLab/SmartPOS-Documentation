# Change log

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
