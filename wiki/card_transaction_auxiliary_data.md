# Card Transactions, Auxiliary Data

There is a lot of data returned when performing card transactions. Currently, no
documentation apparently exist on the full amount of data returned, but to ease
the printing of valid receipts, we include two sub-maps in the auxiliary data 
(`aux`) property of the result payloads: `receipt` and `raw`. (That is, the
`eodAux` property in the end-of-day response.)

## Receipt Data

The following includes information about what is added to the "receipt" map in
response payloads. These are the values that should be printed on a card payment
receipts, and we therefore aim to provide them in an easily read manner.

The following table include the key, description and data source (in the raw 
data) for each:

|Key|Description|Data source|
|---|-----------|-----------|
|`result`|The result of the transaction, e.g. "approved" or "declined"|`result`|
|`method`|The method used for this transaction, e.g. "contactless" or "contact\_emv"|`input_source`|
|`PAN`|The masked PAN from the used card|`account_number`|
|`authorizedAmount`|The total amount authorised for this transaction|Value "9F02" from `tags`|
|`expiry`|The expiry of the used card, formatted as mm/yy|`card_expiry` formatted with a `/` after the second character|
|`AID`|The "Application ID"|Value "84" from `tags`|
|`TVR`|The "Terminal Verification Result|Value "95" from `tags`|
|`messageNumber`|The message number|`message_number`|
|`cardSequenceNumber`|The card sequence number|Value "5F34" from `tags`|
|`authNumber`|The authorization number, different from the authorization code|`auth_code`|
|`timestamp`|The timestamp of the transaction|`ended_at`\*|
|`formattedTimestamp`|`timestamp` formatted as "yyyy-MM-dd HH:mm:ss"|`ended_at`|
|`applicationLabel`|The application name, if any|`app_label`|
|`type`|The transaction type, e.g. "sale" or "refund"|`type`|
|`terminalID`|The terminal ID|`online_response`.`terminal_ID`|
|`currency`|The currency as a numerical code\*\*|`Value "5F2A" from `tags`|

\* `ended_at` is chosen as the source at the moment, instead of 
`online_response`.`datetime` as this should be almost the same, and removes the
dependency on `online_response`. It is chosen over the "9A" and "9F21" values 
from `tags` as these aren't consistent with regards to local time zones.

\*\* It is my intention to translate the currency into the proper ISO 4217 three
letter currency code

The following values are missing from the "receipt" map at the current time:
authorization code, merchant ID, TCC, reference number and currency translateon.
