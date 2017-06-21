# Documentation

## Matchmaking and Live: A Semi-Centralized Solution

Matchmaking and Xboxlive-like services can be implemented quickly, efficiently, and productively with modern technologies. Hosting solutions for realtime game critical services, such as the game simulation, can be provided as a semi-decentralized pool of hosts. One solution to create matchmaking and Xboxlive-like services is a semi-stateless RESTful backend using the crossplatform and opensource ASP Core. Services like Xboxlive require the capability of pushing state changes or events to clients. Doing this over raw HTTP would often require long polling or polling which is non-intuitive, error prone and difficult to get right. A new revitalized technology compatible with ASP Core called SignalR can fill this gap. The ability to broadcast changes and/or events to connected clients via an interested managed/grouped system means that events such as “Friend has logged in” or “3 more people found for matchmaking group” can be pushed to the interested clients in a seemingly unprovoked fashion over TCP. These two simple technologies combined will allow for the creation of the basic functions of matchmaking and xboxlive-like services.

**Authentication:** Using JWT via OAuth2 we can authenticate users that can then authorize themselves on various distributed services. The JWT can be obtained by sending a POST to the below endpoint with the authentication details.

|Method:|POST|
|---|---|
|Authorization:|No|
|Content-Type:|application/x-www-form-urlencoded|
|Endpoint:|{auth-baseurl}/api/auth|
|Body:|grant_type=password&username={user}&password={password}|
|Response:|[JWTModel](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.Authentication.Common/Models/JWTModel.cs) as JSON body|

**Authorization:** To preform actions or query endpoints that require authorization or roles you should use the Authentication JWT. Simply insert the AccessToken into the Request headers.

|Method:|ANY|
|---|---|
|Authorization:| Authorization: Bearer {[JWTModel.AccessToken](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.Authentication.Common/Models/JWTModel.cs)} |



