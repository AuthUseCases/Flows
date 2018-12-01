# Effemoral Storage Usecase
This is modeled on a real world scenario, where Amazon suggests that you use an effemoral locker at WholeFoods as your shipping address.
Arguable they really want to do this so you buy overpriced cheese, but for those of us that have porch theives its a nice service.

# Ask
I would like to store documents in a effemoral cloud locker and send the unlock code via email to a recipient;
The locker has a TTL and upon expiration it and its contents evaporate.


[sequence diagram](http://liveuml.com/diagram/view/5c02b01937ecd3087ea64b1c)

```
@startuml
User -> LockerPortal: Login
LockerPortal->IDP: Login
LockerPortal<-IDP: id_token
== time ==
User->LockerPortal: CreateLocker
LockerPortal->LockerPortal: lockerId = AllocateEffemoralStorage
User->LockerPortal: upload documents
LockerPortal->LockerPortal: store documents in locker
User->LockerPortal: Send locker to recipient

LockerPortal->SercurityTokenService: Mint resource owner access_token (subject:lockerId,claims, etc)
LockerPortal<-SercurityTokenService: access_token
LockerPortal->LockerPortal: build and Shorten url?access_token={{access_token}}
LockerPortal->EmailService: send retrieval url to recipient@mail.com
== time ==
User -> LockerPortal: Logoff
== time ==

recipient->LockerPortal: ?access_token={{access_token}}
LockerPortal->LockerPortal: validate access_token
LockerPortal->LockerPortal: Open Locker
recipient->LockerPortal: Download Content
@enduml
```




