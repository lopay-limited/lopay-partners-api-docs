# Errors

The Lopay Partner API can return the following error codes:

Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request is invalid.
422 | Unprocessable entity -- Your request is valid, but we are unable to process it.
401 | Unauthorized -- Your API key is missing or incorrect.
404 | Not Found -- Resource not found
406 | Not Acceptable -- You requested a format that isn't json.
429 | Too Many Requests -- Try again in a bit
500 | Internal Server Error -- We had a problem with our server. Try again later.

All error responses will be in the following format:

<aside class="pre">
{
  "httpCode": 400,
  "errorCode": "LO_PAY_ERR",
  "errorMessage": "Invalid request"
}
</aside>
