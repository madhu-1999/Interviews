+ SNS login integration done using OAuth2.0. 
+ Login redirects to login page of SNS (like Google), after login success, a token is returned to app from SNS, which can be used to access SNS account according to permissions granted. When app makes access request to SNS server, it checks whether token is valid. before providing access.
# Terminology
## Resource
Anything which needs to be accessed by external service that needs authorization.
## Resource Owner
Owner of resource
## Resource Server
Server which serves/hosts/stores the protected resource
## Client
Service/Application which is accessing resource through resource owner
## Authorization Server
Server that generates and validates access tokens for clients and permission of users

# OIDC (OpenID Connect)
+ Protocol built on top of Oauth2.0
+ Acts as a identity layer. 
	+ Access token which client uses to access resource is a long alphanumeric string of characters which provides no context or information about the user.
	+ OIDC sends ID token + access token. ID token contains basic user info like first name, last name, email, username etc
	+ ID token is used to verify user info while access token verifies that client has necessary permission to access resource.