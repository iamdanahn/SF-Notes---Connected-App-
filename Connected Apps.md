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
