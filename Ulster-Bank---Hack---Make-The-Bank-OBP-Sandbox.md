####Intro

Hello, 

This page relates to the *Ulster Bank* OBP API sandbox created for the [Hack Make the Bank hackathons](http://www.hackmakethebank.com) in Dublin and Belfast.

The following instructions will help you to use the Open Bank API sandbox. 
####Public Data

Some sandbox accounts have public views available, which do not require OAuth to access. For example, here is a list of transactions: 
https://ulsterbank.openbankproject.com/obp/v1.2.1/banks/ulster/accounts/account1/public/transactions

For other API calls, see API Documentation below.
####Application registration
First, so that the client application can communicate with the API (especially the calls requiring OAuth header), you will need to register your application [here](https://apisandbox.openbankproject.com/consumer-registration).
You will get a consumer key and consumer secret, they are necessary for the calls requiring OAuth authentication.
####OAuth
More details about the OAuth requirements: getting a request token, redirecting the user, getting an access token and accessing protected resources are available [here](https://github.com/OpenBankProject/OBP-API/wiki/OAuth-1.0-Server).
<br />
**Important**: Make sure you use the correct domain in all calls e.g. [https://ulsterbank.openbankproject.com/oauth/authorize?oauth_token=NPcudxy0yU5T3tBzho7iCotZ3cnetKwcTIRlX0iwRl0](https://ulsterbank.openbankproject.com/oauth/authorize?oauth_token=NPcudxy0yU5T3tBzho7iCotZ3cnetKwcTIRlX0iwRl0)

####API documentation
At time of writing, the current **stable** API version is [1.2.1](https://github.com/OpenBankProject/OBP-API/wiki/REST-API-V1.2.1). The active **draft** version is [1.3](https://github.com/OpenBankProject/OBP-API/wiki/REST-API-V1.3.0).

####Data access
During the authentication process, the user is redirected by the client application to login and grant access to the App. For the sandbox you can use the following credentials:
<br />
Customer number: 0000094300000
<br />
Answers to PIN/password questions are: "1", "2", "3", "a", "b", "c"

####Social Finance Application
The sandbox data is also available through a web application called Social Finance [here](https://ulsterbank-sofi.openbankproject.com/) and the default user credentials are the same as above.

Note: Social Finance is also open sourced on [github] (https://github.com/OpenBankProject/Social-Finance).