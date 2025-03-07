---
title: HTTP V2 API
description: Use the HTTP API V2 to send data directly from your server to the HTTP V2 endpoint.
---

Use the HTTP V2 API to send data directly from your server to the HTTP V2 endpoint.

The HTTP V2 API replaces the deprecated HTTP API.

--8<-- "includes/postman.md"

--8<-- "includes/auth-api-key.md"

## Endpoints

| Region | Endpoint |
| --- | --- |
| Standard Server | [https://api2.amplitude.com/2/httpapi](https://api2.amplitude.com/2/httpapi) |
| EU Residency Server | [https://api.eu.amplitude.com/2/httpapi](https://api.eu.amplitude.com/2/httpapi) |

## Considerations

### Upload limit

**For Starter plan customers:**

Limit your upload to 100 batches per second and 1000 events per second. You can batch events into an upload, but don't send more than 10 events per batch. Amplitude expects fewer than 100 batches per second, and the 1000 events per second limit still applies.

**For customers on Growth and Enterprise plans:**

Contact Support if you need to send more than 1000 events per second. There is no hard limit on the Enterprise plan, but devices that exceed 30 events per second are throttled.

Keep request sizes under 1 MB with fewer than 2000 events per request. When you exceed these size limits, you get a 413 error.

If you have high volume and concerned with scale, partition your work based on `device_id` or `user_id`.
 This ensures that throttling on a particular `device_id` (or `user_id`) doesn't impact all senders in your system.
 If you are using a proxy service to send events to Amplitude, make sure that throttling is forwarded to your clients, instead of letting spammy clients slow down a partition of work in your system.

### All-zero device IDs: Limit Ad Tracking enabled

As of iOS 10, Apple replaces the Identifier for Advertiser (IDFA) with all zeros if the user enables Limit Ad Tracking. 
Because all events require a device ID, Amplitude drops device IDs of all zeros and returns an error on the request.

If you are passing the IDFA as the device ID, first run a check on the IDFA value. If it's all zeros, pass a different value for the device ID (such as the Identifier for Vendor (IDFV).

### Windows OS

If you are using a Windows operating system, then you may have to replace all single quotes with escaped double quotes.

### String character limit

All string values, like `user_id`, event, or user property values, have a character limit of 1024 characters.

### Set date values

Amplitude compares dates as strings, so it's best to use the ISO 8601 format (`YYYY-MM-DDTHH:mm:ss`). This format lets you perform date comparisons, (for example: `'2016-01-31' > '2016-01-01'`). Comparison also works for datetime values in this format (for example: `'2017-08-07T10:09:08' > '2017-08-07T01:07:00'`).

### Set time values

The `time` parameter in each event must be sent as millisecond since epoch. Any other format (such as ISO format) results in a 400 Bad Request response.

### Event deduplication

It's highly recommended that you send an `insert_id` for each event to prevent sending duplicate events to Amplitude. Amplitude ignores subsequent events sent with the same `insert_id` within the past 7 days.

### Device IDs and User IDs minimum length

Device IDs and User IDs must be strings with a length of 5 characters or more. This is to prevent potential instrumentation issues.
 If an event contains a device ID or user ID that's too short, the ID value is removed from the event.
 If the event doesn't have a `user_id` or `device_id` value, the upload may be rejected with a 400 status. Override the default minimum length of 5 character by passing the `min_id_length` option with the request.

??? info "Invalid IDs"
    These IDs are invalid, and result in a 400 error:

    * "anonymous"
    * "nil"
    * "none"
    * "null"
    * "n/a"
    * "na"
    * "undefined"
    * "unknown"
    * """"
    * "00000000-0000-0000-0000-000000000000"
    * "{}"
    * "lmy47d"
    * "0"
    * "-1"

## Upload request

Send a POST request to `https://api2.amplitude.com/2/httpapi`

### Example request

=== "cURL"
    ```bash
    # You can also use wget
    curl -X POST https://api2.amplitude.com/2/httpapi \
      -H 'Content-Type: application/json' \
      -H 'Accept: */*'
    ```
=== "HTTP"
    ```bash
    POST https//api2.amplitude.com/2/httpapi HTTP/1.1
    Host: api2.amplitude.com
    Content-Type: application/json
    Accept: */*
    ```
=== "JavaScript"
    ```js
    var headers = {
      'Content-Type':'application/json',
      'Accept':'*/*'

    };

    $.ajax({
      url: 'https://api2.amplitude.com/2/httpapi',
      method: 'post',

      headers: headers,
      success: function(data) {
        console.log(JSON.stringify(data));
      }
    })
    ```
=== "NodeJs"

    ```js
    const request = require('node-fetch');
    const inputBody = '{
      "api_key": "my_amplitude_api_key",
      "events": [
        {
          "user_id": "203201202",
          "device_id": "C8F9E604-F01A-4BD9-95C6-8E5357DF265D",
          "event_type": "watch_tutorial",
          "time": 1396381378123,
          "event_properties": {
            "load_time": 0.8371,
            "source": "notification",
            "dates": [
              "monday",
              "tuesday"
            ]
          },
          "user_properties": {
            "age": 25,
            "gender": "female",
            "interests": [
              "chess",
              "football",
              "music"
            ]
          },
          "groups": {
            "company_id": "123",
            "department_id": [
              "abc",
              "xyz"
            ]
          },
          "app_version": "2.1.3",
          "platform": "iOS",
          "os_name": "Android",
          "os_version": "4.2.2",
          "device_brand": "Verizon",
          "device_manufacturer": "Apple",
          "device_model": "iPhone 9,1",
          "carrier": "Verizon",
          "country": "United States",
          "region": "California",
          "city": "San Francisco",
          "dma": "San Francisco-Oakland-San Jose, CA",
          "language": "English",
          "price": 4.99,
          "quantity": 3,
          "revenue": -1.99,
          "productId": "Google Pay Store Product Id",
          "revenueType": "Refund",
          "location_lat": 37.77,
          "location_lng": -122.39,
          "ip": "127.0.0.1",
          "idfa": "AEBE52E7-03EE-455A-B3C4-E57283966239",
          "idfv": "BCCE52E7-03EE-321A-B3D4-E57123966239",
          "adid": "AEBE52E7-03EE-455A-B3C4-E57283966239",
          "android_id": "BCCE52E7-03EE-321A-B3D4-E57123966239",
          "event_id": 23,
          "session_id": 1396381378123,
          "insert_id": "5f0adeff-6668-4427-8d02-57d803a2b841"
        }
      ]
    }';
    const headers = {
      'Content-Type':'application/json',
      'Accept':'*/*'

    };

    fetch('https://api2.amplitude.com/2/httpapi',
    {
      method: 'POST',
      body: inputBody,
      headers: headers
    })
    .then(function(res) {
        return res.json();
    }).then(function(body) {
        console.log(body);
    });
    ```

=== "Ruby"
    ```ruby
    require 'rest-client'
    require 'json'

    headers = {
      'Content-Type' => 'application/json',
      'Accept' => '*/*'
    }

    result = RestClient.post 'https://api2.amplitude.com/2/httpapi',
      params: {
      }, headers: headers

    p JSON.parse(result)
    ```
=== "Python"
    ```python
    import requests
    headers = {
      'Content-Type': 'application/json',
      'Accept': '*/*'
    }

    r = requests.post('https://api2.amplitude.com/2/httpapi', params={

    }, headers = headers)

    print r.json()
    ```
=== "Java" 
    ```java
    URL obj = new URL("https://api2.amplitude.com/2/httpapi");
    HttpURLConnection con = (HttpURLConnection) obj.openConnection();
    con.setRequestMethod("POST");
    int responseCode = con.getResponseCode();
    BufferedReader in = new BufferedReader(
        new InputStreamReader(con.getInputStream()));
    String inputLine;
    StringBuffer response = new StringBuffer();
    while ((inputLine = in.readLine()) != null) {
        response.append(inputLine);
    }
    in.close();
    System.out.println(response.toString());
    ```
=== "Go"
    ```go
    package main

    import (
          "bytes"
          "net/http"
    )

    func main() {

        headers := map[string][]string{
            "Content-Type": []string{"application/json"},
            "Accept": []string{"*/*"},

        }

        data := bytes.NewBuffer([]byte{jsonReq})
        req, err := http.NewRequest("POST", "https://api2.amplitude.com/2/httpapi", data)
        req.Header = headers

        client := &http.Client{}
        resp, err := client.Do(req)
        // ...
    }
    ```

### Upload request body parameters

| Name | Description |
| --- | --- |
| `api_key` | Required. String. Amplitude project API key. |
| [`events`](#keys-for-the-event-argument) | Required. []. Array of [Events](https://developers.amplitude.com/docs/http-api-v2#definition-Event) to upload. |
| [`options`](#options) | Optional. []. Object. |

??? example "Example upload request body"
    ```json
    {
      "api_key": "my_amplitude_api_key",
      "events": [
        {
          "user_id": "203201202",
          "device_id": "C8F9E604-F01A-4BD9-95C6-8E5357DF265D",
          "event_type": "watch_tutorial",
          "time": 1396381378123,
          "event_properties": {
            "load_time": 0.8371,
            "source": "notification",
            "dates": [
              "monday",
              "tuesday"
            ]
          },
          "user_properties": {
            "age": 25,
            "gender": "female",
            "interests": [
              "chess",
              "football",
              "music"
            ]
          },
          "groups": {
            "company_id": "123",
            "department_id": [
              "abc",
              "xyz"
            ]
          },
          "app_version": "2.1.3",
          "platform": "iOS",
          "os_name": "Android",
          "os_version": "4.2.2",
          "device_brand": "Verizon",
          "device_manufacturer": "Apple",
          "device_model": "iPhone 9,1",
          "carrier": "Verizon",
          "country": "United States",
          "region": "California",
          "city": "San Francisco",
          "dma": "San Francisco-Oakland-San Jose, CA",
          "language": "English",
          "price": 4.99,
          "quantity": 3,
          "revenue": -1.99,
          "productId": "Google Pay Store Product Id",
          "revenueType": "Refund",
          "location_lat": 37.77,
          "location_lng": -122.39,
          "ip": "127.0.0.1",
          "idfa": "AEBE52E7-03EE-455A-B3C4-E57283966239",
          "idfv": "BCCE52E7-03EE-321A-B3D4-E57123966239",
          "adid": "AEBE52E7-03EE-455A-B3C4-E57283966239",
          "android_id": "BCCE52E7-03EE-321A-B3D4-E57123966239",
          "event_id": 23,
          "session_id": 1396381378123,
          "insert_id": "5f0adeff-6668-4427-8d02-57d803a2b841"
        }
      ]
    }
    ```

### Keys for the event argument

You can send these keys in the JSON event object. Note that one of `user_id` or `device_id` is required, as well as the `event_type`.

| <div class="big-column">Name</div>| Description |
| --- | --- |
| `user_id` | Required if `device_id` isn't used. String. ID for the user. Must have a minimum length of 5 characters unless overridden with the `min_user_length` option. |
| `device_id` | Required if `user_id` isn't used. String. A device-specific identifier, such as the Identifier for Vendor on iOS. If a `device_id` isn't sent with the event, then it's set to a hashed version of the `user_id`. |
| `event_type` | Required. String. A unique identifier for your event. The following event names are reserved for Amplitude use: `[Amplitude]` Start Session", `[Amplitude]` End Session", `[Amplitude]` Revenue", `[Amplitude]` Revenue (Verified)", `[Amplitude]` Revenue (Unverified)", and `[Amplitude]` Merged User". |
| `time` | Optional. The timestamp of the event in milliseconds since epoch. If time isn't sent with the event, then it's set to the request upload time. |
| `event_properties` | Optional. Object. A dictionary of key-value pairs that represent additional data to be sent along with the event. You can store property values in an array. Date values are transformed into string values. Object depth may not exceed 40 layers. |
| `user_properties` | Optional. Object. A dictionary of key-value pairs that represent additional data tied to the user. You can store property values in an array. Date values are transformed into string values. Object depth may not exceed 40 layers. |
| `groups` | Optional. Object. This feature is only available to Enterprise customers who have purchased the [Accounts add-on](https://help.amplitude.com/hc/en-us/articles/1150017655322). This field adds a dictionary of key-value pairs that represent groups of users to the event as an event-level group. You can track up to 5 unique group types and 10 total group values. Any groups past that threshold aren't tracked.|
|`$skip_user_properties_sync`|Optional. Boolean. When `true` user properties are not synced. Defaults to `false`. See [Skip user properties sync](../data-backfill-guide.md#skip-user-properties-sync) for more information.| 
| `app_version` | Optional. String. The current version of your application. |
| `platform` | Optional. String. Platform of the device. |
| `os_name` | Optional. String. The name of the mobile operating system or browser that the user is using. |
| `os_version` | Optional. String. The version of the mobile operating system or browser the user is using. |
| `device_brand` | Optional. String. The device brand that the user is using. |
| `device_manufacturer` | Optional. String. The device manufacturer that the user is using. |
| `device_model` | Optional. String. The device model that the user is using. |
| `carrier` | Optional. String. The carrier that the user is using. |
| `country`[^1] | Optional. String. The current country of the user. |
| `region`[^1] | Optional. String. The current region of the user. |
| `city`[^1] | Optional. String. The current city of the user. |
| `dma` [^1]| Optional. String. The current Designated Market Area of the user. |
| `language` | Optional. String. The language set by the user. |
| `price` | Optional. Float. The price of the item purchased. Required for revenue data if the revenue field isn't sent. You can use negative values to indicate refunds. |
| `quantity` | Optional. Integer. The quantity of the item purchased. Defaults to 1 if not specified. |
| `revenue` | Optional. Float. Revenue = (price x quantity). If you send all 3 fields of price, quantity, and revenue, then the revenue value is (price x quantity). Use negative values to indicate refunds. |
| `productId` | Optional. String. An identifier for the item purchased. You must send a price and quantity or revenue with this field. |
| `revenueType` | Optional. String. The type of revenue for the item purchased. You must send a price and quantity or revenue with this field. |
| `location_lat` | Optional. Float. The current Latitude of the user. |
| `location_lng` | Optional. Float. The current Longitude of the user. |
| `ip` | Optional. String. The IP address of the user. Use `$remote` to use the IP address on the upload request. Amplitude uses the IP address to reverse lookup a user's location (city, country, region, and DMA). Amplitude can drop the location and IP address from events after they reach our servers. Contact the Support team to configure this. |
| `idfa`[^2] | Optional. String. (iOS) Identifier for Advertiser. |
| `idfv` | Optional. String. (iOS) Identifier for Vendor. |
| `adid`[^2] | Optional. String. (Android) Google Play Services advertising ID |
| `android_id` | Optional. String. (Android) Android ID (not the advertising ID) |
| `event_id` | Optional. Integer. (Optional) An incrementing counter to distinguish events with the same `user_id` and timestamp from each other. We recommend you send an event_id, increasing over time, especially if you expect events to occur simultaneously. |
| `session_id` | Optional. Long. The start time of the session in milliseconds since epoch (Unix Timestamp), necessary if you want to associate events with a particular system. A `session_id` of -1 is the same as no `session_id` specified. |
| `insert_id` | Optional. String. A unique identifier for the event. Amplitude deduplicates subsequent events sent with the same `device_id` and `insert_id` within the past 7 days. We recommend generating a UUID or using some combination of `device_id`, `user_id`, `event_type`, `event_id`, and time. |
| `plan` | Optional. Object. Tracking plan properties. Amplitude supports only branch, source, version properties. |
| `plan.branch` | Optional. String. The tracking plan branch name. For example: "main". |
| `plan.source` | Optional. String. The tracking plan source. For example: "web". |
| `plan.version` | Optional. String. The tracking plan version. For example: "1", "15". |

[^1]:
    `[Amplitude] Country`, `[Amplitude] City`, `[Amplitude] Region`, and `[Amplitude] DMA` are user properties pulled using GeoIP. We use MaxMind's database, which is widely accepted as the most reliable digital mapping source, to look up location information from the user's IP address.
     For any HTTP API events, if GeoIP information is unavailable, then Amplitude pulls the information from the `location_lat` and `location_lng` keys if those keys are populated. If the location properties are manually set, then Amplitude doesn't change that property.
[^2]:
    These values appear as `null` in Amplitude. For privacy reasons, Amplitude processes the attribution information, but strips the values before saving the event in the system.

### Options

| <div class="big-column">Name</div> | Description |
| --- | --- |
| `min_id_length` | Optional. Integer. Overrides the default minimum length of 5 for `user_id` & `device_id` fields. |

## Response Format

We recommend that you implement retry logic and send an `insert_id` (used for deduplication of the same event) in your events.
 This prevents lost events or duplicated events if the API is unavailable or a request fails.

### 200 Response SuccessSummary

[200 OK](https://tools.ietf.org/html/rfc7231#section-6.3.1): Successful real time event upload. If you don't receive a `200 OK` response, retry your request.

```json
{
  "code": 200,
  "events_ingested": 50,
  "payload_size_bytes": 50,
  "server_upload_time": 1396381378123
}

```

#### Properties

| <div class="big-column">Name</div>    | Description |
| --- | --- |
| `code`    | Integer. 200 success code |
| `events_ingested`    | Integer. The number of events ingested from the upload request. |
| `payload_size_bytes`    | Integer. The size of the upload request payload in bytes. |
| `server_upload_time`  | Long. The time in milliseconds since epoch (Unix Timestamp) that our event servers accepted the upload request. |

### 400 Response InvalidRequestError

[400 Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1). A 400 indicates invalid upload request. Check the response for more information.

Possible reasons for an invalid request:

- The request body isn't valid JSON. The `error` returned is "Invalid JSON request body".
- The request body is missing required fields. The `error` returned is "Request missing required field", and indicates which fields are missing.  
- The events object has invalid fields. `events_with_invalid_fields` maps field names to an array of indexes indicating the events with invalid values.
- Some devices have been silenced.

=== "Invalid fields example"

      ```json
      {
        "code": 400,
        "error": "Request missing required field",
        "missing_field": "api_key",
        "events_with_invalid_fields": {
          "time": [
            3,
            4,
            7
          ]
        },
        "events_with_missing_fields": {
          "event_type": [
            3,
            4,
            7
          ]
        }
      }

      ```

=== "Silenced devices example"

    ```json
    {
        "code": 400,
        "eps_threshold": 100,
        "error": "Events silenced for device_id",
        "exceeded_daily_quota_devices":
        {},
        "silenced_devices":
        [
            "silenced_device_id_1",
            "silenced_device_id_2"
        ],
        "silenced_events":
        [
            5,
            6
        ],
        "throttled_devices":
        {
            "throttled_device_id_1": 0,
            "throttled_device_id_2": 100
        },
        "throttled_events":
        [
            3,
            4
        ]
    }
    ```

#### Properties (invalid or missing JSON)

| <div class="big-column">Name</div>   | Description |
| --- | ---  |
| `code`  | Integer. 400 error code |
| `error`  | String. Error description. Possible values are `Invalid request path`, `Missing request body`, `Invalid JSON request body`, `Request missing required field`, `Invalid event JSON`, `Invalid API key`, `Invalid field values on some events` |
| `missing_field`  |String. Indicates which request-level required field is missing. |
| `events_with_invalid_fields`  | Object. A map from field names to an array of indexes into the events array indicating which events have invalid values for those fields |
| `events_with_missing_fields`  |  Object. A map from field names to an array of indexes into the events array indicating which events are missing those required fields |

#### Properties (SilencedDeviceId)

| <div class="big-column">Name</div>  | Description |
| --- |--- |
| `code`  | Integer. 400 error code |
| `error` | String. Error description. |
| `eps_threshold` |Integer. Your app's current events per second threshold. If you exceed this rate your requests will be throttled. |
| `exceeded_daily_quota_devices` | Object. A map from device_id to its current number of daily events, for all devices that exceed the app's daily event quota. |
| `silenced_devices` | [string]. Array of `device_id`s that have been silenced by Amplitude. |
| `silenced_events` | [integer]. Array of indexes in the events array indicating events whose device_id got silenced. |
| `throttled_devices` | Object. A map from device_id to its current events per second rate, for all devices that exceed the app's current threshold. |
| `throttled_events` | [integer]. Array of indexes in the events array indicating events whose user_id and/or device_id got throttled |

### 413 Response PayloadTooLargeError

[413 Payload Too Large](https://tools.ietf.org/html/rfc7231#section-6.5.11). The payload size is too big (request size exceeds 1MB). Split your events array payload into multiple requests and try again.

```json
{
  "code": 413,
  "error": "Payload too large"
}

```

#### Properties

| Name |  Description |
| --- |  --- |
| `code` | Integer. 413 error code |
| `error` | String. Error description. |

### 429 Response TooManyRequestsForDeviceError

[429 Too Many Requests](https://tools.ietf.org/html/rfc6585#section-4). Too many requests for a user or device. Amplitude throttles requests for users and devices that exceed 30 events per second
 (measured as an average over a recent time window).
  You should pause sending events for that user or device for a period of 30 seconds before retrying and continue retrying until you no longer receive a 429 response.

```json
{
  "code": 429,
  "error": "Too many requests for some devices and users",
  "eps_threshold": 30,
  "throttled_devices": {
    "C8F9E604-F01A-4BD9-95C6-8E5357DF265D": 31
  },
  "throttled_users": {
    "datamonster@amplitude.com": 32
  },
  "throttled_events": [
    3,
    4,
    7
  ]
}

```

#### Properties

| <div class="big-column">Name</div>  | Description |
| --- | --- |
| `code` |Integer. 429 error code |
| `error`  |String. Error description. |
| `eps_threshold` | Integer. Your app's current events per second threshold. If you exceed this rate your requests are throttled. |
| `throttled_devices` |  Object. A map from `device_id` to its current events per second rate, for all devices that exceed the app's current threshold. |
| `throttled_users` |  Object. A map from `user_id` to their current events per second rate, for all users that exceed the app's current threshold. |
| `throttled_events` |  Array of indexes in the events array indicating events whose `user_id` or `device_id` were throttled. |

### Server Error 500, 502, 504

500, 502, and 504  [Server Error](https://tools.ietf.org/html/rfc2616#section-10.5.1). Amplitude encountered an error while handling the request.
 A request with this response may not have been accepted by Amplitude. If you retry the request, the events could be duplicated. To avoid duplication, send an `insert_id` in your requests.

### 503 Service Unavailable

[503 Service Unavailable](https://tools.ietf.org/html/rfc2616#section-10.5.4).  Request failed because of an internal Amplitude issue. Retrying a request with a `503` response doesn't risk duplicating events.
