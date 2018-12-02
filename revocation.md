# Revocation

The OIDC/OAuth2 spec talks about revocation, however to close the loop the following is required functionality of a given STS

The assumption is that I am able to request access_token/refresh_token response sets for a given subject many times.
All the access_tokens and refresh_tokens are valid at the same time.


1. Revocation of all refresh_tokens assocatiated with a subject in a single B2B call  
2. Revocation of a single refresh_token (this is part of the spec)  
3. Revocation of an access_token: This means #1 to me, revocation of all refresh_tokens associated with that subject that the access_token contains.  
4. A back-channel feed of all revocation activities.


Revocation activity on the STS DOES NOT invalidate any access_tokens that are in the wild.  

The responsibility of denying access to a service is that of the actual service.  The STS's role is simply to inform that a given subject has been revoked at a given time.
In the case of a single refresh_token being revoked, information about the contents of the access_token that the refresh_token could have minted has to be sent.



