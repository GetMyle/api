# Authentication

> Example of a mylet URL request

```
https://mymylet.example.com/ui?session=<A SESSION TOKEN>
```

There are two cases when your mylet execution is triggered by MYLE:

 1. A user navigates to the mylet's URL
 2. An event happens related to the mylet, so mylet's hook URL is requested

Both of these cases have a `session` query parameter supplied. Your mylet exchanges the [session token](#session-token) for a [user ticket](#user-ticket) and uses the latter to access user's data.

## Session token

Both UI and hook mylet URLs are supplied with a `session` query parameter. The session token is valid for an hour and is exchanged for a [user ticket](#user-ticket).

The exchange is done using [`/v1/ticket` endpoint](#get-user-ticket) and pursues two goals:

 1. To make sure that a mylet request is triggered by MYLE
 2. To generate a [user ticket](#user-ticket) that is used to authenticate subsequent MYLE API calls

A session token is converted into a [user ticket](#user-ticket).

## User ticket

> Example of a user ticket

```json
{
    "exp": 1478832837390,
    "app": "com.example.mymylet",
    "scope": ["mylet"],
    "grant": 10,
    "user": 100,
    "key": "-6b18XP5TDwlp9RxOhfZv4-5Zvm1n_LQ",
    "algorithm": "sha256",
    "ext": {
        "dbs": "2e2ed05a-4f63-4850-9297-5b8384397dd0"
    },
    "id": "<A TICKET IDENTIFIER>"
}
```

In order to be able to request user's data you first have to receive [a user ticket](https://github.com/hueniverse/oz#ticket-response) based on a [session token](#session-token) and mylet credentials. [`/v1/ticket`](#get-user-ticket) endpoint is used for that.

Generated user ticket is valid for an hour and at any time can be exchanged for a new one using [`/v1/ticket/refresh`](#refresh-user-ticket) endpoint. You can check its `exp` property for expiration (that is time in milliseconds since `1970-01-01`).

A user ticket is used to generate a request [`Authorization` header](#authorization-header).

## Authorization header

```javascript
const Hawk = require('hawk');

// example mylet credentials
const myletCred = {
    "id": "com.example.mymylet",
    "key": "DacoNO/pKaigvMJqzh86vX71j7y6cwAl",
    "algorithm": "sha256"
};

// function that calculates Hawk authorization header
function getHawkHeader(url, method, credentials) {
    const options = {
        credentials: credentials,
        app: credentials.app
    };
    return Hawk.client.header(url, method, options);
}

// example of an authorization header for '/v1/ticket' endpoint
let headers = {
    Authorization: getHawkHeader('https://api.getmyle.com/v1/ticket', 'POST', myletCred).field
};

// example of an authorization header for `/v1/query` endpoint
headers = {
    Authorization: getHawkHeader('https://api.getmyle.com/v1/query', 'POST', getUserTicket()).field
}
```

`Authorization` header is required to be included with a request to MYLE API server. That is generated based on URL and method of the request as well as HAWK credentials specific to the request endpoint. The credentials are one of the two types:

 1. Mylet credentials

    That is only required for [`/v1/ticket` endpoint](#get-user-ticket). That consists of a mylet ID and a secret key. See example on the right.

 2. [User ticket](#user-ticket)

    That is required for any other API endpoints.

[Hawk](https://github.com/hueniverse/hawk) is used to generate an authorization request header. That is NodeJS library. Other platform [implementations](https://github.com/hueniverse/hawk/blob/master/README.md#implementations) are also available:

- [.NET - Logibit Hawk](https://github.com/logibit/logibit.hawk/)
- [Ruby - Tent Hawk](https://github.com/tent/hawk-ruby)
- [Java - Wealdtech](https://github.com/wealdtech/hawk)
- [Python - Kumar's Mohawk](https://github.com/kumar303/mohawk/)
- [Go - Hiyosi](https://github.com/hiyosi/hawk)
- [PHP - Dflydev](https://github.com/dflydev/dflydev-hawk)