# Documentation

## Matchmaking and Live: A Semi-Centralized Solution

Matchmaking and Xboxlive-like services can be implemented quickly, efficiently, and productively with modern technologies. Hosting solutions for realtime game critical services, such as the game simulation, can be provided as a semi-decentralized pool of hosts. One solution to create matchmaking and Xboxlive-like services is a semi-stateless RESTful backend using the crossplatform and opensource ASP Core. Services like Xboxlive require the capability of pushing state changes or events to clients. Doing this over raw HTTP would often require long polling or polling which is non-intuitive, error prone and difficult to get right. A new revitalized technology compatible with ASP Core called SignalR can fill this gap. The ability to broadcast changes and/or events to connected clients via an interested managed/grouped system means that events such as “Friend has logged in” or “3 more people found for matchmaking group” can be pushed to the interested clients in a seemingly unprovoked fashion over TCP. These two simple technologies combined will allow for the creation of the basic functions of matchmaking and xboxlive-like services.

**Authorization:** To preform actions or query endpoints that require authorization or roles you should use the Authentication JWT. Simply insert the AccessToken into the Request headers.

|Method:|ANY|
|---|---|
|Authorization:| Authorization: Bearer {[JWTModel.AccessToken](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.Authentication.Common/Models/JWTModel.cs)} |

## Diagrams

### Authentication

![auth](http://i.imgur.com/tXlDKpz.png "Auth Diagram")

**A:** Authentication request for the issue of a JWT via OAuth. Autnenticates users via their auth credentials, username and password, for preforming actions on various distributed services. The JWT can be obtained by sending a POST to the below endpoint with the authentication details.

|Method:|POST|
|---|---|
|Authorization:|No|
|Content-Type:|application/x-www-form-urlencoded|
|Endpoint:|{auth-baseurl}/api/auth|
|Body:|grant_type=password&username={user}&password={password}|
|Response:|[JWTModel](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.Authentication.Common/Models/JWTModel.cs) as JSON body|

**B:** The authentication JSON response that contains the [JWTModel](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.Authentication.Common/Models/JWTModel.cs) in the body.

**C:** A [SignalR](https://github.com/aspnet/SignalR) realtime web request to authorize the hub connection for a user. Sends an empty request with an Authorization header that the endpoint hub can use to associate and authorize the hub connection with the authenticated account.

**D:** Authorization/signin response sent back containing the result of the authorization attempt for the [SignalR](https://github.com/aspnet/SignalR) hub. Contains [RealtimeHubAuthorizationResponseModel](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.Authorization.Common/Models/RealtimeHubAuthorizationResponseModel.cs) as JSON in the response body.

### Service Discovery

![service discovery](http://i.imgur.com/KiPhNEw.png "Service Discovery")

**A:** Service endpoint discovery request over HTTP. When clients need to know the endpoint for a service they can request it from the service discovery service. Clients don't know any endpoints but the service discovery. This gives an oppurtunity for naive loadbalancing, region based service resolution and just allows for the changing of endpoints post-deployment without patching.

|Method:|POST|
|---|---|
|Authorization:|No|
|Content-Type:|application/javascript|
|Endpoint:|{baseurl}/api/ServiceDiscovery/Discover|
|Body:|JSON [ResolveServiceEndpointRequestModel](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.NameResolution.Common/Models/ResolveServiceEndpointRequestModel.cs)|
|Response:|[ResolveServiceEndpointResponseModel](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.NameResolution.Common/Models/ResolveServiceEndpointResponseModel.cs) as JSON body|

**B:** Service discovery response as an HTTP response. When the client requests a service endpoint we can respond with it, if it's available, or indicate that it's a service we don' know about or is unavailable. The response is sent as [ResolveServiceEndpointResponseModel](https://github.com/HaloLive/HaloLive.Library/blob/master/src/HaloLive.Models.NameResolution.Common/Models/ResolveServiceEndpointResponseModel.cs).
