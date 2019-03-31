# Errors

> Error responses will use the following structure:

```json
{
    "error": "Error message"
}
```

The API uses the following error codes:

Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request is invalid.
401 | Unauthorized -- Your API key is wrong.
500 | Internal Server Error -- We had a problem with our server. Try again later.
