# Documentation

## Matchmaking and Live: A Semi-Centralized Solution

Matchmaking and Xboxlive-like services can be implemented quickly, efficiently, and productively with modern technologies. Hosting solutions for realtime game critical services, such as the game simulation, can be provided as a semi-decentralized pool of hosts. One solution to create matchmaking and Xboxlive-like services is a semi-stateless RESTful backend using the crossplatform and opensource ASP Core. Services like Xboxlive require the capability of pushing state changes or events to clients. Doing this over raw HTTP would often require long polling or polling which is non-intuitive, error prone and difficult to get right. A new revitalized technology compatible with ASP Core called SignalR can fill this gap. The ability to broadcast changes and/or events to connected clients via an interested managed/grouped system means that events such as “Friend has logged in” or “3 more people found for matchmaking group” can be pushed to the interested clients in a seemingly unprovoked fashion over TCP. These two simple technologies combined will allow for the creation of the basic functions of matchmaking and xboxlive-like services.

**Authorization:** To preform actions or query endpoints that require authorization or roles you should use the Authentication JWT. Simply insert the AccessToken into the Request headers.

|Method:|ANY|
|---|---|
|Authorization:| Authorization: Bearer {[JWTModel.AccessToken](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.Authentication.Common/Models/JWTModel.cs)} |

## Diagrams

![auth](http://i.imgur.com/MZG1vI1.png "Auth Diagram")

**A:** Authentication for the issue of a JWT via OAuth. Autnenticates users via their auth credentials, username and password, for preforming actions on various distributed services. The JWT can be obtained by sending a POST to the below endpoint with the authentication details.

|Method:|POST|
|---|---|
|Authorization:|No|
|Content-Type:|application/x-www-form-urlencoded|
|Endpoint:|{auth-baseurl}/api/auth|
|Body:|grant_type=password&username={user}&password={password}|
|Response:|[JWTModel](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.Authentication.Common/Models/JWTModel.cs) as JSON body|

**B:** The authentication JSON response that contains the [JWTModel](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.Authentication.Common/Models/JWTModel.cs) in the body.

**C:** A [SignalR](https://github.com/aspnet/SignalR) realtime web event requesting authentication. Sends JSON object [RealtimeHubAuthorizationEventModel](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.Authorization.Common/Models/RealtimeHubAuthorizationRequestModel.cs) that, for now, contains the connection ID of the hub client.

**D:** Authorization/signin POST HTTP request sent to the arbitrary realtime backend service to authorize the hub connection to recieve events for the account encoded in the JWT authorization header. This request passes along the [RealtimeHubAuthorizationEventModel](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.Authorization.Common/Models/RealtimeHubAuthorizationEventModel.cs) sent to the client.

|Method:|POST|
|---|---|
|Authorization:| Authorization: Bearer {[JWTModel.AccessToken](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.Authentication.Common/Models/JWTModel.cs)} |
|Content-Type:|application/javascript|
|Endpoint:|{baseurl}/api/signin|
|Body:|JSON [RealtimeHubAuthorizationEventModel](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.Authorization.Common/Models/RealtimeHubAuthorizationEventModel.cs)|
|Response:|[RealtimeHubAuthorizationResponseModel](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.Authorization.Common/Models/RealtimeHubAuthorizationResponseModel.cs) as JSON body|

**E:** Authorization/signin response HTTP response sent back containing the result of the authorization attempt for the [SignalR](https://github.com/aspnet/SignalR) hub. Contains [RealtimeHubAuthorizationResponseModel](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.Authorization.Common/Models/RealtimeHubAuthorizationResponseModel.cs) as JSON in the response body.
