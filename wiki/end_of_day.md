# End of Day

The end of day action is different, in that it doesn't have a specific data
payload, instead it just uses the Android native `android.os.ParcelUuid` as the
payload, which contains the UUID that will be returned with the `EndOfDayResult`
payload.

The end of day action will perform the end of day process for all applicable
installed payment methods (there is currently no way to filter this). Since,
depending on the method, this might take some time, the app will display a 
loading message while the process is ongoing, but will then automatically switch
back to the ECR (aside from if the card payment application require user 
interaction).

## Payload

The `EndOfDayResult` payload is a very simple payload. As each payment method
will result in different data, most of the data is returned as part of the
`eodAux` property (short for "end of day aux"), which is a map of `TargetMethod`
to regular `aux` map property.
