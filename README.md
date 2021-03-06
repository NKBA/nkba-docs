# NKBA API Documentation

## NKBA OAuth Instructions

The NKBA API uses the industry-standard OAuth 2.0 Authorization Code grant type for auth. For detailed information about OAuth and specifically the Authorization Code flow, please refer to references such as https://oauth.net/2/ and https://oauth.net/2/grant-types/authorization-code/

## Prerequisites:

Provide the NKBA with the Redirect URI. This should be a URL on your site that the user will be redirected to after successfully authentication with the NKBA.

## Using NKBA as a OAuth provider:

Build a login page on your site that has a “Login with NKBA” button. This button should link to the following URL: `https://api.nkba.org/oauth/authorize?response_type=code&client_id=XXXXXXX&redirect_uri=http://example.com/redirect&scope=member:me`

Note: Where the `redirect_uri` is the Redirect URI that you have provided and `client_id` is the client identifier you have been given.

The authorize endpoint above will prompt the user to log into their NKBA account if not logged in; otherwise, they will be redirected to your Redirect URI with a code parameter present.

On your Redirect page, read the code parameter from the query string parameter, and exchange it for an access token by POSTing to the following URL:
https://api.nkba.org/oauth/token
Passing form parameters (x-www-form-urlencoded)

```
{ 
  grant_type: "authorization_code",
  client_id: <your client id>
  code: <the code received>
  redirect_uri: <the same redirect_uri as above>
}
```

The endpoint will return a JSON document containing:

```
{
  “access_token”: <token>,
  ”token_type”: ”bearer”,
  ”expires_in”: <secs>
}
```

You can use the above `access_token` to make a request to the NKBA API, by passing it as the `Authorization` header (`"Authorization": "Bearer <access_token>"`). Endpoints are documented using Swagger/OpenAPI here: https://api.nkba.org/swagger/.

The endpoint you will most likely want to use to retrieve information about the member, such as their email address, is the `/v2/member/me` endpoint documented here: https://api.nkba.org/swagger#!/member/getMe

Just hit that endpoint (https://api.nkba.org/v2/member/me) passing the `access_token` as the `Authorization` header, and you’ll retrieve a JSON document containing information such as `email` `address`, `name`, and `number`.

#### Example

```js
// After receiving the access_token from the query string...

const access_token = parseTheQueryString(window.location); // hand waiving, use something like qs or query-string to do this

fetch('https://api.nkba.org/v2/member/me', {
  headers: {
    Authorization: "Bearer " + access_token,
  }
})
.then(response => response.json())
.then(me =>  {
  // do something with the member
  console.log(me)
})
.catch(error => {
  console.log(error)
})
```
