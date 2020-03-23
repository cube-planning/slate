# Errors

> Error responses will use the following structure:

```json
{
    "error": "Error message"
}
```

> For some endpoints (i.e. `/access_token`) an error code may also be provided:

```json
{
    "error": "Error message",
    "code": "ERROR_CODE"
}
```

The API uses the following HTTP error codes:

Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request is invalid.
401 | Unauthorized -- Your API access token is wrong.
500 | Internal Server Error -- We had a problem with our server. Try again later.
