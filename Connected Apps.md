# Connected Apps

Connected Apps are ways for external applications to integrate with Salesforce
using APIs and protocols like Security Assertion Markup Language (SAML),
OAuth, and OpenID Connect.

Websites/Web Servers can request access to Salesforce Data via a Connected App
that's setup to allow it.

Eg:
If a Web Server wants to get Customer Order Status, first create the Connected App
with OAuth Enabled with scopes specified like:

- Manage user data via APIs (api)
- Manage user data via Web browsers (web)
- Access unique user identifiers (openid)

After the Connected App is saved, a Consumer Key / Secret is generated so users
can request an Authorization Code with it.

## Requesting Authorization Code

As this is a OAuth 2.0 Web Server Flow, Authorization Codes are requested via
Salesforce's authorization endpoint with an HTTP Redirect

```
https://mycompany.my.salesforce.com/services/oauth2/authorize?
client_id=3MVG9IHf89I1t8hrvswazsWedXWY0i1qK20PSFaInvUgLFB6vrcb9bbWFTSIHpO8G2jxBLJA6uZGyPFC5Aejq&
redirect_uri=https://www.mycustomerorderstatus.com/oauth2/callback&
response_type=code
```

`https://mycompany.my.salesforce.com/services/oauth2/authorize`

- This is the SF instance's OAuth 2.0 authorization endpoint where Connected Apps
  send OAuth authorization requests

`client_id=3MVG9IHf89I1t8hrvswazsWedXWY0i1qK20PSFaInvUgLFB6vrcb9bbWFTSIHpO8G2jxBLJA6uZGyPFC5Aejq`

- Client ID = Connected App's consumer key

`redirect_uri=https://www.mycustomerorderstatus.com/oauth2/callback`

- Redirect URI is where users are redirected after a successful authorization (This is setup during Connected App creation)

`response_type=code`

- Tells SF which OAuth 2.0 grant type is the Connected App is requesting.
- Response type 'code' indicates the app is requesting an Authorization code

### Authenticate User

Before SF issues an Authorization code, you must Authenticate yourself first by logging in.

## Receiving a Callback

After the app is Authorized, SF sends a callback to the Connected App with an
Authorization Code

```
https://www.mycustomerorderstatus.com/oauth2/callback?
code=aPrx4sgoM2Nd1zWeFVlOWveD0HhYmiDiLmlLnXEBgX01tpVOQMWVSUuafFPHu3kCSjzk4CUTZg==
```

`https://www.mycustomerorderstatus.com/oauth2/callback`

- Connected App's Callback URL

`code=aPrx4sgoM2Nd1zWeFVlOWveD0HhYmiDiLmlLnXEBgX01tpVOQMWVSUuafFPHu3kCSjzk4CUTZg==`
Authorization code approving the app

## Requesting an Access Token

Connected App now has a valid Authorization Code. It passes it to the SF token
endpoint for an Access Token

```
POST /services/oauth2/token HTTP/1.1
Host: mycompany.my.salesforce.com
Content-length: 307
Content-type: application/x-www-form-urlencoded
grant_type=authorization_code&
code=aPrxhgZ2MIpkSy0aOdn07LjKFvsFOis6RGcWXz7p8JQCjcqfed5NQLe7sxWwMY_JQFuLwHRaRA==&
client_id=3MVG9IHf89I1t8hrvswazsWedXWY0iqK20PSFaInvUgLFB6vrcb9bbWFTSIHpO8G2jxBLJA6uZGyPFC5Aejq&
client_secret=*******************&
redirect_uri=https://www.mycustomerorderstatus.com/oauth2/callback
```

```
POST /services/oauth2/token HTTP/1.1
Host: mycompany.my.salesforce.com
Content-length: 307
Content-type: application/x-www-form-urlencoded
```

- Post request sent to SF's OAuth 2.0 Token endpoint
- Conected Apps send Access and Refresh Token requests here

`grant_type=authorization_code`

- Grant type defines the type of validation that the Connected App can provide to prove its a safe visitor

`code=aPrxhgZ2MIpkSy0aOdn07LjKFvsFOis6RGcWXz7p8JQCjcqfed5NQLe7sxWwMY_JQFuLwHRaRA`

- The Authorization Code received earilier used in exchange for an access token

`client_id=3MVG9IHf89I1t8hrvswazsWedXWY0i1qK20PSFaInvUgLFB6vrcb9bbWFTSIHpO8G2jxBLJA6uZGyPFC5Aejq`

- Connected App's Consumer Key

`client_secret=*******************`

- Connected App's Consumer Secret

`redirect_uri=https://www.mycustomerorderstatus.com/oauth2/callback`

- Connected App's Callback URL

## Receiving an Access Token

SF Validates the Connected Apps:

- Authorization Code
- Consumer Key
- Consumer Secret

Once validated, it sends back an Access Token in JSON format.
It includes associated permissions/scopes and an ID Token

```
{
"access_token": "00DB0000000TfcR!AQQAQFhoK8vTMg_rKA.esrJ2bCs.OOIjJgl.9Cx6O7KqjZmHMLOyVb.U61BU9tm4xRusf7d3fD1P9oefzqS6i9sJMPWj48IK",
"signature": "d/SxeYBxH0GSVko0HMgcUxuZy0PA2cDDz1u7g7JtDHw=",
"scope": "web openid",
"id_token": "eyJraWQiOiIyMjAiLCJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJhdF9oYXNoIjoiSVBRNkJOTjlvUnUyazdaYnYwbkZrUSIsInN1YiI6Imh0dHBzOi8vbG9...",
"instance_url": "https://mycompany.my.salesforce.com",
"id": "https://mydomain.my.salesforce.com/id/00DB0000000TfcRMAS/005B0000005Bk90IAC",
"token_type": "Bearer",
"issued_at": "1558553873237"
}
```
