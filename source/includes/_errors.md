# Errors

MYLE uses conventional HTTP response codes to indicate the success or failure of an API request. In general, codes in the 2xx range indicate success, codes in the 4xx range indicate an error that failed given the information provided (e.g., a required parameter was omitted, etc.), and codes in the 5xx range indicate an error with MYLE's servers.

### Error response attributes

Attribute | Description
--------- | -----------
statusCode | The HTTP status code (typically 4xx or 5xx)
error | The HTTP status message (e.g. `Bad Request`, `Internal Server Error`) derived from statusCode
message | The error message if specified

### Status codes

Status Code | Meaning
---------- | -------
200 | OK -- Everything worked as expected
400 | Bad Request -- The request was unacceptable, often due to missing a required parameter
401 | Unauthorized -- `Expired ticket`, `Mismatching application id`, `Invalid application`, `Invalid grant`
403 | Forbidden -- The request is authenticated but does not have the necessary permissions for the resource
404 | Not Found -- 	The requested resource doesn't exist
500 | Internal Server Error -- We had a problem with our server; try again later
503 | Service Unavailable -- We're temporarially offline for maintanance; try again later
