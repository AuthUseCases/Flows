# IDP-SomeService Bearer Token Flow 

In this use case a mobile app logins in a user via a federated IDP.  The only thing requested is an identity.  There is no intent.
Later the MobileApp intends to call a gated service and must trade in a valid id_token for access_tokens to the service.
The FederatedIDP has no knowledge of the final intent.  The service is the only entity that makes an association between an identity and the allowed services.

The following [POC call](https://p7identityserver4.azurewebsites.net/docs/arbitrary_resource_owner.md) is used to mint the access_token to some service.

 
![Sequence Diagram](15a645957682.png)

# REQUEST
```
https://p7identityserver4.azurewebsites.net/connect/token
HEADERS:
  Content-Type:application/x-www-form-urlencoded
BODY:
  grant_type:arbitrary_resource_owner
  client_id:arbitrary-resource-owner-client
  client_secret:secret
  scope:offline_access some_service
  arbitrary_claims:{"role": ["full_access"]}
  subject:userid:1234
  access_token_lifetime:3600
```
# RESPONSE
```
{
    "access_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IkZENkFGOTIyQTAyNTM4NzE5RjhBQjVBRTM0NjdCMjA1MEU2QUExMkUiLCJ0eXAiOiJKV1QiLCJ4NXQiOiJfV3I1SXFBbE9IR2Zpcld1TkdleUJRNXFvUzQifQ.eyJuYmYiOjE1NDM3MTU1NjIsImV4cCI6MTU0MzcxOTE2MiwiaXNzIjoiaHR0cHM6Ly9wN2lkZW50aXR5c2VydmVyNC5henVyZXdlYnNpdGVzLm5ldCIsImF1ZCI6WyJodHRwczovL3A3aWRlbnRpdHlzZXJ2ZXI0LmF6dXJld2Vic2l0ZXMubmV0L3Jlc291cmNlcyIsInNvbWVfc2VydmljZSJdLCJjbGllbnRfaWQiOiJhcmJpdHJhcnktcmVzb3VyY2Utb3duZXItY2xpZW50Iiwic3ViIjoidXNlcmlkOjEyMzQiLCJhdXRoX3RpbWUiOjE1NDM3MTU1NjIsImlkcCI6ImxvY2FsIiwicm9sZSI6ImZ1bGxfYWNjZXNzIiwiY2xpZW50X25hbWVzcGFjZSI6IkRhZmZ5IER1Y2siLCJzY29wZSI6WyJzb21lX3NlcnZpY2UiLCJvZmZsaW5lX2FjY2VzcyJdLCJhbXIiOlsiYXJiaXRyYXJ5X3Jlc291cmNlX293bmVyIl19.aa3xsL2PXRTGpPAqAAW6W8yPPCtydMUSMlE9TYIzOVDLI_gC7nMK-2U5L2PoPVHH4Km6anIABFvPwO_nv2GJGJjH-OdymEz9LsiNR53FQ_1UA5Wc9Qra-hoo09E0R4uSm4xghF38Um8T5jO6psHytWvIfoeJ7kh0LI_0DDd8pqUNhUoSLrvx2JBY0JC0UBOqV5H4Unx5hLfwRrK3O3AB9iGCVxIvQR4_EqiE7yBpS7PEtUJXF9a4Is2L_LZg2k2VDiRe6w1n0awh8Ceg9X-2piecbctLNi4j2BqkGhJ-AgdUyk5B6Ww-eCAD8BwPypEbwawspOqfdd-2VU0riS2clw",
    "expires_in": 3600,
    "token_type": "Bearer",
    "refresh_token": "CfDJ8MVlmnGGAk1Ah5xhnpEOsbEerhG-ZUNfhT4-dPfw5tNLVD6QV4-tPQcOymTcD90zloCjYt9UxojNKngT0xnZPRMI9nHv7ETTDMbSk2rkhFW9GrTtnADc2qJm1PRAQZAb-mcEvAMP-WPFJvTrmO_fXdsS41KAHr4FUBXneTf0EIdQkORmVrSNKynGVrsr0qYSyNfW1qhT1tZPKtncK9pttwA"
}
```

# PLANTUML 
```
@startuml
MobileApp -> FederatedIdp: Login
MobileApp <- FederatedIdp: id_token
== time ==
MobileApp->SomeService: Bind(id_token)
SomeService->SomeService: Validate(id_token)
SomeService->SomeService: Lookup User and Build Claims
SomeService->SercurityTokenService: Mint resource owner access_token (subject:userId,claims, etc)
SomeService<-SercurityTokenService: access_token,refresh_token
MobileApp<-SomeService: access_token,refresh_token
== time ==
MobileApp->SomeService: /Some/Api  Authorization: Bearer {{access_token}}
SomeService->SomeService: Validate(access_token)
MobileApp<-SomeService: {"data":{}}
== time ==
MobileApp->SercurityTokenService: /connect/token grant_type:refresh {{refresh_token}}
MobileApp<-SercurityTokenService: access_token,refresh_token
== time ==
SomeService->SercurityTokenService: /revocation subject=userId
@enduml
```




