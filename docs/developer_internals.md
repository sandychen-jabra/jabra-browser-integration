# Developer information

This document contains various implementation details of interest to developers and advanced integrators of the browser sdk. Normal users of the Jabra client API should not need this information.

## Internal message format between API, Extension and Chromehost

All communication between API, Extension and chromehost is based on message 
passing of basically the same json structure as documented in typescript format below:

General message format:
```typescript
{
           // Used for API and extension communication - not used by chromehost:
           direction?: string,
           // Used for all commands, responses, events. Null or "na" for errors.
           message?: string,
           // Used for errors only.
           error?: string,
           // Command arguments
           args?: any,
           // Response data, not used for requests.
           data?: any, 
           // Unique arbitary ID for each request used to pair requests with results. Empty for device orginating events!
           requestId?: number | string,
           // Unique arbitary ID for each api client instance used to pair requests with requesters. Empty for device orginating events!
           apiClientId?: string,
           // JS client api version.
           version_jsapi?: string,
           // Reserved for non-Jabra clients. Not presently used.
           customClientName?: string
           // Reserved for non-Jabra clients. Not presently used.
           customClientVersion?: string
}
```

The same generic format is used (with variation) for both requests, responses and device events in different layers of the stack.

The actual values used to fillout "requestId" and "apiClientId" in requests are not important but they should be unique across different requests and clients retrospectively.

### Chromehost command examples:

The examples shows common request/responses when sending commands. Note that not all commands have arguments, results or event responses.

#### getinstallinfo with result

Request:
```json 
{ 
    "message": "getinstallinfo", 
    "args": {}, 
    "requestId": 3, 
    "apiClientId": "3krfvjxzl"
}
```

Response:
```json 
{ 
    "message": "Event: getinstallinfo", 
    "requestId": 3, 
    "apiClientId": "3krfvjxzl", 
    "data": {
        "chromehost_logpath": "<somepath>\\JabraChromehost.log",
        "version_chromehost": "2.0.0",
        "version_nativesdk": "1.4.0.10"
    }
}
```

#### setmmifocus with response confirmation but no result

Request:
```json 
{ 
    "message": "setmmifocus", 
    "args": { 
        "capture":true, 
        "type":18
    }, 
    "requestId": 6,
    "apiClientId": "2qub8atrm"
}
```

Response:
```json 
{ 
    "message": "Event: setmmifocus", 
    "requestId": 6, 
    "apiClientId": "2qub8atrm", 
    "data": null
}
```

#### offhook without response

Request:
```json 
{ 
    "message": "offhook", 
    "args": {}, 
    "requestId": 7, 
    "apiClientId": "2qub8atrm"
}
```

#### error response
```json 
{
    "apiClientId": "2qub8atrm",
    "data": null,
    "error": "Error: No device",
    "message": "na",
    "requestId": 7
}
```

### Chromehost event examples:

The example(s) below show examples of device events that are orginating from a device or from
physically adding/removing a device (i.e. without a command being issued). Note that apiClientId and requestId are empty for such events.

#### device attach event 

```json 
{
    "data": {
        "batteryCharging":false,
        "batteryLevelInPercent":100,
        "batteryLow":false,
        "deviceConnection":"USB",
        "deviceFeatures":[1003],
        "deviceID":0,
        "deviceName":"Jabra SPEAK 510 USB",
        "errStatus":0,
        "firmwareVersion":"2.25.0",
        "isInFirmwareUpdateMode":false,
        "productID":1058,
        "serialNumber":"xxxxxxxxxxxx",
        "skypeCertified":true,
        "variant":"08-01"
    },
    "message": "Event: device attached",
    "apiClientId":"",
    "requestId":""
}
```