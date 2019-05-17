# Errors

The API uses the following error codes:

Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request is invalid.
403 | Forbidden -- Your API key is wrong or HMAC signature does not match our check.
405 | Method Not Allowed -- You tried to access an invalid method.
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.
