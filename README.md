# MINT Transaction API

### Authentication
Static bearer token
**Future update**: refreshable tokens

### Endpoint
`/transactions/{transactionId}`

With the `transactionId` replaced by the ID of the transaction, received from MINT.

### Message structure
```json
{
    "profileStartTime": "2026-04-09T10:00:00Z",
    "profileStopTime": "2026-04-09T16:00:00Z",
    "requestedEnergy": 25,
    "requestedRange": 100,
    "averageConsumption": 20
}
```

| jsonpath             | Type                      | Optional | Unit      | Description                                                                                            |
| -------------------- | ------------------------- | :------: | --------- | ------------------------------------------------------------------------------------------------------ |
| `profileStartTime`   | string                    |    Y     |           | When the profile is started<br>In UTC, RFC 3339 format<br>_current time by default_                    |
| `profileStopTime`    | string                    |          |           | When the profile will end<br>In UTC, RFC 3339 format                                                   |
| `requestedEnergy`    | unsigned float, 1 decimal |    Y     | kWh       | The requested energy to be charged<br>_not to be used together with `requestedRange` and  `averageConsumption`_ |
| `requestedRange`     | unsigned int              |    Y     | km        | The additional requested range<br>_not to be used together with `requestedEnergy`_                              |
| `averageConsumption` | unsigned float, 1 decimal |    Y     | kWh/100km | The average consumption of the vehicle<br>_not to be used together with `requestedEnergy`_                      |

### Errors
```json
{
    "code": "VALIDATION_ERROR",
    "message": "The given profile is invalid",
    "errors": [
        {
            "target": "PV-INV-01",
            "code": "INVALID_PROFILE",
            "message": "'requestedEnergy' can not be used together with 'requestedRange'"
        }
    ]
}
```

| HTTP    | Category (`code`)       | Sub-Code (`errors[].code`) | Description                                                                                                                                                                    |
| :------ | :---------------------- | :------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **401** | `AUTHORIZATION_FAILURE` | `INVALID_CREDENTIALS`      | Credentials are missing or invalid                                                                                                                                             |
| **404** | `RESOURCE_NOT_FOUND`    | `TRANSACTION_NOT_FOUND`    | Transaction with given ID does not exist                                                                                                                                       |
| **400** | `VALIDATION_ERROR`      | `INVALID_DATA_TYPE`        | Field contains invalid data type                                                                                                                                               |
| **400** | `VALIDATION_ERROR`      | `INVALID_TIME_DELTA`       | Difference between stop and start time is negative<br>Stop time is in the past                                                                                                 |
| **400** | `VALIDATION_ERROR`      | `INVALID_PROFILE`          | `requestedEnergy` is used together with `requestedRange` and/or `averageConsumption`<br>`requestedRange` and/or `averageConsumption` are used together with `requestedEnergy`<br>`requestedRange` is used without `averageConsumption`<br>`averageConsumption` is used without `requestedRange` |
