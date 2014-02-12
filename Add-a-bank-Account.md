# Index

* [Introduction](#introduction)
* [Application registration](#application-registration)
* [User authentication service](#user-authentication-service)
* OAuth flow
    * [Step 1: Obtaining a request token](#request-token)
    * [Step 2: Redirecting the user](#user-authentication)
    * [Step 3: Obtaining an access token](#access-token)
* [User authentication service](#user-authentication-service)

<span id="introduction"></span>
### Introduction

The following steps will explain how to connect to the Open Bank project API and add real bank accounts during the user authentication. The further described authentication mechanism is very similar to the one described [here](https://github.com/OpenBankProject/OBP-API/wiki/OAuth-1.0-Server) so please refer to it for more details when needed.

The **BASE_URL** alias in the following URLs must be replaced by __https://api.openbankproject.com/login__ 


<span id="application-registration"></span>
### Application registration

Before to start interacting with the API using real data, third party applications needs to get OAuth keys (consumer key and secret key). You can register your application [here](https://api.openbankproject.com/consumer-registration) to get those keys.

The "User authentication URL" field in the form specifies the URL of the service that will authenticate the user.
If this field is left blank then the user will be asked to login with their Open Bank Project account (that they can create [here](https://api.openbankproject.com/user_mgt/sign_up)) during the second step of the OAuth flow see [Step 2: Redirecting the user](#user-authentication).

If the field is set with a valid URL, the user will be redirect during the authentication (see [Step 2: Redirecting the user](#user-authentication)) to that URL for login before being able to enter their banking credentials for adding the bank account. In that case the authentication service needs to satisfy a certain specification explained in more details [below](#user-authentication-service)


<span id="request-token"></span>
### Step 1: Obtaining a request token

To start the sign-in flow, the application must obtain a request token by sending a signed message to

POST **BASE_URL**/oauth/initiate with all the OAuth headers (more details [here](https://github.com/OpenBankProject/OBP-API/wiki/OAuth-1.0-Server))

<span id="user-authentication"></span>
### Step 2: Redirecting the user

In this step the OAuth client should redirect the user so that he can: 

1. authenticate and grant the application access to its data  
3. add its bank account details

In details: 

1. #### Authenticate: 
The OAuth client should redirect the user to:  
** BASE_URL**/oauth/authorize?oauth_token=REQUEST_TOKEN  
OR  
** BASE_URL**/oauth/authorize?oauth_token=REQUEST_TOKEN&delegate-authentication=true  
where REQUEST_TOKEN is the request token that the OAuth client received in [step 1](#request-token).  
**The difference** between the two URLs is the optional query parameter "delegate-authentication".  
If the query parameter is present and set to true (like in the second URL) then the user will not see the default Open Bank Project login page but instead he will be redirect to the a third party authentication service (this service could the OAuth client itself). The URL of this authentication service was already set previously during the OAuth client registration (see [Application registration](#application-registration)).

2. #### Banking details:  
Once the user authentication done (either on the open bank login page or on a third party service then redirected back ), he will be asked to enter his banking credentials by selecting the country and the bank, also felling in the account number and the pin code fields.  
Upon a successful authentication, the callback URL of the OAuth client would receive a request containing the oauth_token and oauth_verifier parameters. The application should verify that the token matches the request token received in [step 1](request-token).  
If the callback URL was not specified (oob) than the verifier will be shown in the page and the user has to enter it into the application manually.

TODO: add pictures

<span id="access-token"></span>
### Step 3: Obtaining an access token

This the final step of the OAuth flow, the application must make a:  
_POST **BASE-URL**/oauth/token_ request with a header containing the oauth_verifier (value obtained in step 2) and the oauth_token (received after the step 1) parameters. More details [here](https://github.com/OpenBankProject/OBP-API/wiki/OAuth-1.0-Server).


<span id="user-authentication-service"></span>
### User authentication service

In the second step of the OAuth flow, if the user is redirect to the third party service to authenticate, a query parameter named **token** will be appended to the URL. Its value should be saved by the authentication service to be reused later.

Once the user in the login screen of the third party service (which could be the OAuth client itself) and the authentication completed the user should be redirect back to the Open Bank project API by that same service with:  

_POST **BASE-URL**/oauth/callback_ plus two parameters **user_id** and **token** in the body.

* user_id should contain the user Id at that third party service. This Id MUST be unique for each user at this service and the same after each login. 
* token should contain the value present as a query parameter in the initial redirection (see upper).

Please note that the call back to the Open Bank Project MUST be done with a **POST** request, so the parameters MUST be part of the body and not the URL.