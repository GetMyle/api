# Mylet

## Integration points

In order to integrate with MYLE, a mylet exposes two URLs:

 1. UI URL - to present a mylet to a user
 2. Hook URL - to notify a mylet about an event on MYLE side

## UI URL

> Example of mylet UI URL

```
https://mymylet.example.com/ui?session=<SESSION>
```

Every time a user taps on your mylet to open it up, MYLE calls the mylet's UI URL with [a session query parameter](#session-token) attached.

It is a good idea on the mylet's backend to call[`/v1/ticket` endpoint](#get-user-ticket) right away to get [a user ticket](#user-ticket). That also ensures that the call was initiated by MYLE backend.

## Hook URL

> Example of mylet hook URL

```
https://mymylet.example.com/hook?session=<SESSION>
```

> Example of hook body

```json
{
  "type": "record.associated",
  "record": {
    "id": 3400,
    "raw": "Go to meeting tomorrow at 5 PM",
    "phrase": "Go to meeting tomorrow at 5 PM",
    "time": "2016-11-09T07:23:48.000Z",
    "timezoneOffset": -420,
    "location": {
      "longitude": null,
      "latitude": null,
      "altitude": null
    },
    "language": "eng-USA"
  },
  "mylet": {
    "name": "mymylet",
    "rdns": "com.example.mymylet",
    "title": "My Mylet",
    "keywords": {
      "eng-USA": "book"
    },
    "version": "0.0.1",
    "build": 1
  }
}
```

Every time there is an event related to a mylet, MYLE calls the mylet's hook URL with [a session query parameter](#session-token) attached and a body representing the event.

It is a good idea on the mylet's backend to call [`/v1/ticket` endpoint](#get-user-ticket) right away to get [a user ticket](#user-ticket). That also ensures that the call was initiated by MYLE backend.

### Hook event types

Type | Description
--------- | -----------
record.associated | A record was associated with a mylet (whether by a keyphrase or manually by a user)
