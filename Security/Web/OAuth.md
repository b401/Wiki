---
title: OAuth2.0
date: M11-02-2022
author: i4
---

# OAuth2.0
**Accesing APIs**

Every user gets redirect to OAuth server and redirected back with a token.

- OAuth does not communicate UserData.
- OpenID Connect does.
  
## Flow
1. User: presses "Log In"
2. App => User: Creates a new secret and hashes it for this particular user (PKCE Code Verifier)

   1. Code Verifier is a random string 43-128 characters long: code_verifier
   2. Code Challenge is the hashed code_verifier string: base64url(sha256(code_verifier))
   3. Create 302 URL
    ```
    https://authorization-server.com/auth?
        response_type=code& < Tells the server that we are doing the authorization code flow
        client_id=CLIENT_ID& < Which app is making this request?
        redirect_uri=REDIRECT_URI& < Which url to redirect after the authentication (Has to match the redirect_uri on the AS)
        scope=photos& < has to match the scope value on the app (Sets the right permissions for the app)
        state=XXXXXXX& < Random value for CSRF protection if PKCE is not supported. Else can be used for redirect (Cart/Checkout redirect)
        code_challenge=xxxxxx < base64url(sha256(code_verifier))
        code_challenge_method=S256 < Method used to hash the code_verifier
    ```
   
   ___FRONT CHANNEL__
3. App => User: Go to the Authorization Server and ask for Access (Take the PKCE Code Challenge with you)
4. User => Authorization Server: I want to log in to {app}, here's the hash (PKCE Code Challenge) 
5. Authorization Server => User: Please log in and allow the app the requested permissions
6. Authorization Server => User: Here is a temporary code the app can use.

   If everything worked fine:
   ```
    https://example-app.com/redirect?
     code=AUTH_CODE_HERE&
     state=xxxxx
   ```

   On error:
   ```
   https://example-app.com/redirect?
     error=access_denied&
     state=XXXX
   ```
   
7. User => App: Here's the temporary code. Please use this to get a token
   ___BACK CHANNEL__ 
8. App => Authorization Server: Here's the code, and the plain-text secret (PKCE Secret). 
   ```
   POST https://authorization-server.com/token
       grant_type=authorization_code& < Tells the server that we are doing the authorization code grant.
       code=AUTH_CODE_HERE&
       redirect_uri=REDIRECT_URI&
       code_verifier=VERIFIER_STRING& < Plaintext secret that was created on step 3
       client_id=CLIENT_ID&
       client_secret=CLIENT_SECRET < For Confidential apps
    ```

    client_secret should be in the POST body or as HTTP AUTH Header
     
9. Authorization Server => App: Let me hash the secret and check it against the initial stored hash... ok here is a token.
   ```
   {
    "token_type": "Bearer",
    "access_token":"vxjclJ@lcjj123V3Ia",
    "expires_in":3600,
    "scope":"photos",
    "refresh_token":"vj90dfsnhievvd"
   }
   ```



## Application Types
Deployed with some sort of credentials
Never put a secret in a mobile app / single app
### Confidential
Deployed with a secret
Example: API Key in the application
Provides a Client Secret

Only possible if the app is securely stored (Server Side Applications) = NO MOBILE APPS

### Public
Can't be created with a secret
## Channels
### Front-Channel (Implicit Flow)
Data movement through the address bar.
- no direct link between application / oauth server.
- Can not really trust if everything arrives to the right destination

Sends Tokens over the address bar
Check <<<<<<Implicit Flow>>>>>>

Only a minute valid

### Back-Channel
HTTPS Connection
- Certificate Validation
- Encrypted
- Trusted Response

Server asks the Authorization server
Only possible due to CORS

## Application Identifier
### Client ID
Flow
1. Buildup URL (Scope,Redirect URL, ClientID)
2. Login at Authorization Server
3. Server sends back a authorization Code to the application
4. Redirects to Authorization Server
## Redirect URI
Location where the authorization server sends the User back.
- Authorization Code gets delivered through the Front channel. 
  Apps shouldn't use custom Moniker as one could create a fake app that takes it over.

## PKCE
PKCE Code Challenge

## Authorization Server
Does know the User

__Password-Grant__
```
POST https://authroization-server/token
  grant_type=password&
  username=USERNAME&
  password=PASSWORD#
  client_id=CLIENT_ID // optionally (see Application type)
```
Also asks for Consent (Right permissions)

This is skipped for first party/confidential clients
## Resource Server
Check only if valid
## Old authentication
- Username/Password
 - Creates Session cookie
 - Limiting for SSO

### Problems
- Trust
Can't trust applications if they ask for credentials. (First/Third party app)
- Only way to revoke access is by resetting password
- Adding MFA is hard.


## Mobile App
Client secrets aren't save in a mobile app (Cause it's in the source code..)

### Redirect URL
Where the AS sends him back.


### iPhone
SFSafariViewController

### Android
Chrome Custom Tabs

## Roles
Resource Owner (The User) v
User Agent (The Device) v
OAuth Client (The Application) v
Authorization Server

### Manages Access
Application don't see the users password.
Gives Access Token to the app. User logs in directly at the Authorization Server

### Resource Server (The API) 

## IOT
=> 
```
POST https://authorization-server.com/device?
client_id=CLIENT_ID&
scope=youtube
```
<=
```
{
"device_code": "xxxxx",
"user_code": "bwdsasd",
"verification_uri":"https://example.com/device",
"expires_in": 1800,
"interval": 5
}
```

## Client credentials
Used for machine-to-machine or service interactions
User is not involved

grant_type=client_credentials

## Hybrid OpenID Connect flow
combined response_type
For example:
`response_type=code id_token`

Bad idea to combine with tokens:
- token
- id_token token
- code token
- code id_token token

`c_hash` used for validation

# OpenID Connect
##Identifying Users#

# JWT (JSON web token)
scope: {SCOPE}+openid
`response_type=id_token`

## Split into three parts

### Header
base64 encoded
### Payload
base64 encoded
`sub` : user identifier
`iss` : issuer
`aud` : audience (For what client is the token)
`iat` : issued at (timestamp when the token was issued)
`exp` : when the token expires

### signature

# Links'n Stuff
## Okta
Issuer URI
https://dev-20353911.okta.com/oauth2/default

### Generating PKCE
https://example-app.com/pkce

__All keys are changed__

Example Authorization request
```
https://dev-20353911.okta.com/oauth2/default/v1/authorize?
      response_type=code&
      scope=photos&
      client_id=adsasasd
      state=asdasdjkl1d2&
      redirect_uri=https://example-app.com/redirect&
      code_challenge=dajil123jivlx0123jvxoqsdj1ifi5J0DVfImBpnV9E&
      code_challenge_method=S256
 ```

Login request to resource
 ```
curl -X POST https://dev-61491274.okta.com/oauth2/default/v1/token \
-d grant_type=authorization_code \
-d redirect_uri=https://example-app.com/redirect \
-d client_id=01v9fjkladjgi29g02d7 \
-d client_secret=vjailvjnh9bv9dk1cv0asdlxcjlvij1lofcga3Yw \
-d code_verifier=cv01jlvxc90bnlwermlcsdj9ojsldcj910pjdlscjiwldmlwefjlf701 \
-d code=vjosdf9bjl_jv90jdflvbj9rg-vj9x0fsdfsdf-vjilt 
 ```

 
### Refresh Tokens
- different specs
`?scope\=offline_access`

Initial refresh token gets send with the first access token (`refresh_token` parameter)

```
POST https://authorization-server.com/token?
    grant_type=refresh_token&
    refresh_token=REFRESH_TOKEN&
    client_id=CLIENT_ID
```

# Vulnerabilities
- Redirect_URL => Wildcard/Partial matching
- Authorization Code Injection (PKCE solves this attack)
- Use the refresh token? (back channel)
  ```
  POST https://authoritzaion-server.com/token
    grant_type=refresh_token&
    refresh_token=REFRESH_TOKEN&
    client_id=CLIENT_ID&
    client_secret=CLIENT_SECRET
  ```
- Server have to ignore parameters they don't recognize    
- XSS stealing tokens
- XSS is not able to access service worker storage

