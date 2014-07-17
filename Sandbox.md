##Intro

Hello and welcome to the OBP API sandbox wiki page!

The following instructions will help you to use the Open Bank API sandbox.
The purpose of the sandbox is to reproduce the same conditions of a production API with real banking data so the transition to a production environment will be easy.

1. ####Public Data
Some sandbox data has a public view available, which does not require OAuth to access. For example, here is a list of transactions: 
https://apisandbox.openbankproject.com/obp/v1.2/banks/rbs/accounts/savings-kids-john/public/transactions
For other API calls, see API Documentation below.

2. ####Application registration
First, so that the client application can communicate with the API (especially the calls requiring OAuth header), you will need to register your application [here](https://apisandbox.openbankproject.com/consumer-registration).
You will get a consumer key and consumer secret, they are necessary for the calls requiring OAuth authentication.

3. ####OAuth
More details about the OAuth requirements: getting a request token, redirecting the user, getting an access token and accessing protected resources are available [here](https://github.com/OpenBankProject/OBP-API/wiki/OAuth-1.0-Server).
<br />
**Important**:You will find links to use the real data set,like [https://api.openbankproject.com/oauth/authorize?oauth_token=NPcudxy0yU5T3tBzho7iCotZ3cnetKwcTIRlX0iwRl0](https://api.openbankproject.com/oauth/authorize?oauth_token=NPcudxy0yU5T3tBzho7iCotZ3cnetKwcTIRlX0iwRl0) **you MUST replace the sub domain "api" with "apisandbox"** in the URL like this: [https://**apisandbox**.openbankproject.com/oauth/authorize?oauth_token=NPcudxy0yU5T3tBzho7iCotZ3cnetKwcTIRlX0iwRl0](https://apisandbox.openbankproject.com/oauth/authorize?oauth_token=NPcudxy0yU5T3tBzho7iCotZ3cnetKwcTIRlX0iwRl0) to use the proper set of data otherwise you will have error messages.

4. ####API documentation
The API specification is available for the version 1.1 [here](https://github.com/OpenBankProject/OBP-API/wiki/REST-API-V1.1) and for the version 1.2 [there](https://github.com/OpenBankProject/OBP-API/wiki/REST-API-V1.2).

5. ####Data access
During the authentication process, the user will redirected by the client application to login and granting access, for the sandbox you can use the following credentials:
<br />
login: joe.bloggs@example.com
<br />
password: qwerty

6. ####Social Finance Application
The sandbox data is available also available through the Social Finance Application [here](https://sofisandbox.openbankproject.com/) and the default user credentials are the same as above.