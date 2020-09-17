# Card Transactions, Auxiliary Data

There is a lot of data returned when performing card transactions. Currently, no
documentation apparently exist on the full amount of data returned, but to ease
the printing of valid receipts, we include two sub-maps in the auxiliary data 
(`aux`) property of the result payloads: `receipt` and `raw`. (That is, the
`eodAux` property in the end-of-day response.)

## Payment Receipt Data

The following includes information about what is added to the "receipt" map in
payment response payloads. These are the values that should be printed on a card
payment receipts, and we therefore aim to provide them in an easily read manner.

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
|`formattedTimestamp`|`timestamp` formatted as "yyyy-MM-dd HH:mm:ss"|`ended_at`\*|
|`applicationLabel`|The application name, if any|`app_label`|
|`type`|The transaction type, e.g. "sale" or "refund"|`type`|
|`terminalID`|The terminal ID|`online_response`.`terminal_ID`|
|`currency`|The currency as a numerical code\*\*|Value "5F2A" from `tags`|

\* `ended_at` is chosen as the source at the moment, instead of 
`online_response`.`datetime` as this should be almost the same, and removes the
dependency on `online_response`. It is chosen over the "9A" and "9F21" values 
from `tags` as these aren't consistent with regards to local time zones.

\*\* It is my intention to translate the currency into the proper ISO 4217 three
letter currency code

The following values are missing from the "receipt" map at the current time:
authorization code, merchant ID, TCC, reference number and currency translateon.

## Refund Receipt Data

Currently not implemented, returns payment receipt data

## End of Day Receipt Data

The following data is what could be printed on an end of day receipt. This 
section includes information about what properties are included.

|Key|Description|Data source|
|---|-----------|-----------|
|`visa`|The information about Visa transactions. The output is a map, described below|`online_response`.`visa`|
|`mastercard`|The information about MasterCard transactions. The output is a map, described below|`online_response`.`mastercard`|
|`maestro`|The information about Maestro transactions. The output is a map, described below|`online_response`.`maestro`|
|`terminalID`|The terminal ID|`online_response`.`terminal_ID`|
|`timestamp`|The timestamp of the transaction|`ended_at`\*|
|`formattedTimestamp`|`timestamp` formatted as "yyyy-MM-dd HH:mm:ss"|`ended_at`\*|
|`totalAmount`|The total amount|`online_response`.`total_amount`|

\* `ended_at` is chosen as the source at the moment, instead of 
`online_response`.`datetime` as this should be almost the same, and removes the
dependency on `online_response`. It is chosen over the "9A" and "9F21" values 
from `tags` as these aren't consistent with regards to local time zones.

In each of `visa`, `mastercard` and `maestro`, the following data is included

|Key|Description|Data source|
|---|-----------|-----------|
|`debitsTotal`|The total amount for debit transactions|`debits_total`|
|`debitsCredit`|The total count of debit transactions||`debits_count`|
|`creditsTotal`|The total amount for credit transactions|`credits_total`|
|`creditsCredit`|The total count of credit transactions||`credits_count`|
|`cashAmount`|The total amount for cash transactions|`cash_amount`|
|`cashAmountQuantity`|The total count of cash transactions||`cash_amount_qty`|
|`result`|The result for this scheme type|`result`|

The descriptions here may not be completely correct.

## Raw data

This is where I'd put the documentation for the raw data if I had any.

I will add the raw KDoc I've written for the types, however, please translate
camel case to snake case yourself:

Main data:

```
@property accountNumber The masked card PAN
@property amount The amount in hundredths of currency denotation
@property amountOther ???
@property appConfirmationRequired App confirmation required?
@property appLabel I assume the label of the app of [appConfirmationRequired]
@property attemptedSources The list of attempted sources of transaction media (contactless, contact, etc)
@property attemptsRemaining The remaining attempts of this transaction
@property authCode The authentication code ???
@property cardExpiry The expiry year and month of the card that made the transaction
@property cvmMode ???
@property duration The duration of the transaction, I assume
@property endedAt The timestamp of the payment, in seconds since epoch
@property host Always Nets ???
@property id The ID of the transaction
@property inputSource The source that completed the transaction
@property isActive ???
@property isContactless `true` if contactless was tried
@property isContactlessTransactionLimitExceeded I assume `true` if the contactless limit is exceeded
@property isEmv `true` if EMV was tried
@property isFallback `true` if fallback method
@property isForceOnline `true` if this is forced online
@property isKeyed `true` if ???
@property isOfflineRepeat `true` if ???
@property isOnline `true` if online transaction
@property isOnlinePin `true` if online PIN was used
@property isOfflinePin `true` if offline PIN was used
@property isReversed ???
@property isSignatureRequired `true` if signature is required
@property issuerId The issuer ID of the card (I assume)
@property ksn ???
@property messageNumber ???
@property numAttempts ???
@property onlineResponse The online response object
@property onlineResult The online result
@property pinBlock ???
@property referenceTxnId ???
@property result The result. Not 100% sure how this is different from [status]
@property scheme ???
@property sessionNumber ???
@property shouldFallback ???
@property shouldRetry ???
@property startedAt The timestamp of the payment, in seconds since epoch
@property status The status of the transaction
@property statusDetails The status, but longer
@property tags ???
@property terminalOnlineDecision ???
@property timeoutMs The timeout of the transaction
@property tipAmount ???
@property totalAmount [amount] + [amountOther] I assume
@property type What type of transaction this is
```

For `card_details`:

```
@property scheme The card scheme ???
@property track1 ???
@property track2 ???
```

For `online_response`:

```
@property dialIndicator ???
@property terminalId The terminal ID I suppose
@property messageNumber Seems to match [TransactionInfo.messageNumber]
@property messageTypeId ???
@property responseCode ???
@property confirmationRequest ???
@property debitsTotal The total amount processed as debit transactions
@property debitsCount The total count of debit transactions
@property creditsTotal The total amount processed as credit transactions
@property creditsCount The total count of credit transactions
@property cashAmount Cash amount???
@property cashAmountQty Cash amount quantity???
@property depositAmount Deposit amount??
@property depositAmountQty Deposit amount quantity???
@property authorization Authorisation information I assume
@property totalAmount Not the same as [TransactionInfo.totalAmount], no clue what it is. Possibly the total amount since last EOD. Is this for EOD transactions
@property sessionNumber The session number, same as [TransactionInfo.sessionNumber]
@property responseText Some response text information
@property referralPhoneNumber ???
@property floorLimit ???
@property datetime The date-time in the format "yyMMddHHmmss"
@property issuerId The issuer ID, seems to match [TransactionInfo.issuerId]
@property informationField If there is any justice, it contains information
@property optionalData If there is any justice, it contains optional data
@property iccData ??
@property result The result, seem to match [TransactionInfo.result]
@property responseCodeHuman The result, but for humans (more info)
```

For `online_response`.`authorization`:

```
@property txnSeqNum ???
@property atc ???
```

For `online_response`.`response_text`:

```
@property merchant ???
@property receipt ???
@property pinpad ???
```


