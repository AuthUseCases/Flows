# Deeply Nested Identity

Sometimes a simple claim;
```
{role:"admin"}
```
is simply not enough.

JWTs allow for what appears to be a claim the looks like the following;
```
{
	"custom_payload": {
		"some_string": "data",
		"some_number": 1234,
		"some_object": {
			"some_string": "data",
			"some_number": 1234
		},
		"some_array": [{
			"a": "b"
		}, {
			"b": "c"
		}]
	}
}
``` 

The following is basically and IoT registration where the IoT is a highly configurable application.  In this case the application has a lot of features that can be turned on and off and whitebox branded to a given partner.

When the application starts up it gathers a bunch of identifying information.  Everything from the applications SKU, OS information, and injected partner data that came with the initial install.  In the real world this is a person going to rental car agency  and identifying themselves as either;
1. A private customer, where partner code_id: none
2. A customer where the partner code_id: SkyNet inc.

Same person, but not to the registration service.  Its 2 unique individual accounts.


The following [POC call](https://p7identityserver4.azurewebsites.net/docs/arbitrary_identity.md) is used to mint and arbitrarily large id_token

 
![Sequence Diagram](5f54b8471192.png)




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
