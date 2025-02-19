# Http Response Codes
- Http Response codes are 3 digit number sent by web server in reponse to a HTTP request by client.
- Indicates result of a request - providing information about whether request successful, encountered an error, or requires further action.

### Various HTTP response codes:
- 1xx: Informational responses
- 2xx: Success
- 3xx: Redirection
- 4xx: Client/Validation error
- 5xx: Server Errors

## 1xx: Information responses
- Interim response to communicate request progress or its status before processing the final request.

| Status Code | Reason     | Mostly Used In | Details |
|:------------|:-----------|:--------|:--------|
| 100         | Continue   | POST | 1) Before sending the request, client check with server if it can handle the request. 2) Client adds Expect:100-continue in request header and other details request to process request (data and headers both). 3) Server reads "Expect" header and knows client is just checking. So server validates everyting. (Authentication, Authorization and other details). 4) If server is okay, returns 100 http status code and client receives it and then invokes same API without "Expect" header(actual request).  |


## 2xx: Success
- Request received from client is processed successfully.

| Status Code | Reason     | Mostly Used In | Details |
|:------------|:-----------|:--------|:--------|
| 200          | OK | GET, POST (Idempotent calls) | Request is successful and response is retured. |
| 201          | Created | POST | Request is successful and new resource is created. |
| 202          | Accepted | POST | Request is successfully accepted by server but response processing is not yet complete. Used in Async operations and batch operations. |
| 204          | No Content | DELETE | Request is successful and response body is empty. |
| 206          | Partial Content | POST | Indicates server has returned partial response to client. Response is typically part of large resource & tells client specific range of data is being sent(in response headers). Eg: 1) Downloading Large files: if downloads fails - client can resume download by requesting specific part of file. 2)Streaming Video |

## 3xx: Redirection
- Client must take additional action to complete the request

| Status Code | Reason     | Mostly Used In | Details |
|:------------|:-----------|:--------|:--------|
| 301          | Moved Permanently | When migrating from legacy to new API (old status code - new one is 308) | All request should be redirected to new URI. |
| 308          | Permanent Redirect | When migrating from legacy to new API | Same as 301, but it do not allow Http method to change while redirect. Eg: if old API call is POST then new API call(redirection call) should also be POST, which is relaxed in 301 |
| 304          | Not Modified | GET | 1) Client makes a GET call, server returns with Last-Modified time in response header with response body. 2) Client cache the response. 3) Client again makes a GET call passing last modified time in "If-Modified-Since" request header. 4) Server check the particular resource last modified time and compares with client passed time. 5) If resource is not updated, server simply returns 304(NOT_MODIFIED). 6) If resource is updated - server process request as usual and returns the response |

> Note: In 301 and 308 response codes, client uses "Location" response header to get the redirection URL. Make sure to include this header while generating response at server.

> Note: Try to avoid using 304 with Patch requests. Eg: We are trying to update name of user, but lets say name is already same in DB, so no update required. In this case, don't throw 304 instead use 204(NO_CONTENT) or 200(OK) - more appropriate.

## 4xx: Client/Validation Errors
- Request failed - due to a invalid/bad request or some business logic failure.

| Status Code | Reason     | Mostly Used In | Details |
|:------------|:-----------|:--------|:--------|
| 400          | Bad Request | GET, POST, PATCH, DELETE | Client is not passing required details to process the request. |
| 401          | Unauthorized | GET, POST, PATCH, DELETE | API requires Auth token and client is trying to access API without Auth Token. |
| 403          | Forbidden | GET, POST, PATCH, DELETE | When the user don't have permission/privilege to access the API. Throw 403 Forbidden error as permission is not present. |
| 404          | Not Found | GET, PATCH, DELETE | When the request resouce requested by client does not exist at server end. |
| 405          | Method Not Allowed | GET, POST, PATCH, DELETE | Eg: Calling GET API with POST method. In SPring Dispatcher Servlet might throw this error, as request does not even reach to controller. |
| 422          | Un-processable Entity | GET, POST, PATCH, DELETE | Some business validation. Like Users outside India are not to register on website(other country not supported for now). Note request is proper here - but business logic fails |
| 429          | Too Many Requests | GET, POST, PATCH, DELETE | Rate Limiting - in case request limit is reached throw this error |
| 409          | Conflict | POST, PATCH, DELETE | If one request is in progess(say Path: /user/123) then same request made by other user will fail with 409 conflict. This can be achieved using locking mechanism either in cache or at DB level. |

## 5xx: Server Errors
- Request fot failed at Server, even though client request was valid . Means something wrong at server.

| Status Code | Reason     | Mostly Used In | Details |
|:------------|:-----------|:--------|:--------|
| 500         | Internal server error | GET, POST, PATCH, DELETE | Generic error code when no more specific error code is suitable. |
| 501         | Not implemented | GET, POST, PATCH, DELETE | API lacks ability to fulfil the request. OR say API is in development & will be available in future to process the requests |
| 502         | Bad Gateway | GET, POST, PATCH, DELETE | Server acting as a proxy & while calling upstream got invalid request/response. Eg: App is behind reverse proxy(Nginx). If NginX not able to talk to App (due to some configuration error - like port misconfiguration), then it will throw 502. |