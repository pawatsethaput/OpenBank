####Hello,

This is a special page for the **Ulster Bank** OBP API sandbox instance created for the [Hack Make the Bank](http://www.hackmakethebank.com) hackathons in Dublin and Belfast.
 
####Public Data
Some sandbox accounts have public views available. For example, here is a list of public accounts on the sandbox: 
[https://ulsterbank.openbankproject.com/obp/v1.2.1/banks/ulster/accounts](https://ulsterbank.openbankproject.com/obp/v1.2.1/banks/ulster/accounts)

And here is a list of transactions from a sandbox charity account:
[https://ulsterbank.openbankproject.com/obp/v1.2.1/banks/ulster/accounts/charity1/public/transactions](https://ulsterbank.openbankproject.com/obp/v1.2.1/banks/ulster/accounts/charity1/public/transactions)

For hackathon purposes, here is an **owner** view that has been made **public**.
[https://ulsterbank.openbankproject.com/obp/v1.2.1/banks/ulster-ni/accounts/current8/owner/transactions]
(https://ulsterbank.openbankproject.com/obp/v1.2.1/banks/ulster-ni/accounts/current8/owner/transactions)

Here is a list of Ulster Bank Branches (open data)
[https://ulsterbank.openbankproject.com/obp/v1.4.0/banks/ulster/branches](https://ulsterbank.openbankproject.com/obp/v1.4.0/banks/ulster/branches)

For other API calls, see API Documentation below.

####Application registration
If your App needs access to private data and services, you will need to [register your application here](https://ulsterbank.openbankproject.com/consumer-registration).
You will get a consumer key and consumer secret for the calls requiring OAuth authentication.

####OAuth
Then, the best way to get started with OBP and OAuth is probably to fork one of our Starter SDKs:

* [Hello-OBP-OAuth1.0a-Python](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Python)
* [Hello-OBP-OAuth1.0a-Django](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Django)
* [Hello-OBP-OAuth1.0a-Node](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Node)
* [Hello-OBP-OAuth1.0a-Mac](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Mac)
* [Hello-OBP-OAuth1.0a-IOS](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-IOS)
* [Hello-OBP-OAuth1.0a-Android](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Android)
 
Many more details about the OAuth requirements: getting a request token, redirecting the user, getting an access token and accessing protected resources are available [here](https://github.com/OpenBankProject/OBP-API/wiki/OAuth-1.0-Server).
<br />
**Important**: Make sure you use the correct domain in all calls e.g. [https://ulsterbank.openbankproject.com/oauth/authorize?oauth_token=NPcudxy0yU5T3tBzho7iCotZ3cnetKwcTIRlX0iwRl0](https://ulsterbank.openbankproject.com/oauth/authorize?oauth_token=NPcudxy0yU5T3tBzho7iCotZ3cnetKwcTIRlX0iwRl0)

####API documentation
For the current **stable** API version [see 1.2.1](https://github.com/OpenBankProject/OBP-API/wiki/REST-API-V1.2.1). The active **draft** version [is 1.3](https://github.com/OpenBankProject/OBP-API/wiki/REST-API-V1.3.0).

####Data access
During the authentication process, the user is redirected by the client application to login and grant access to the App. For the sandbox you can use the following credentials:

ROI Dublin
<br />
Customer number: 0000094300000
<br />
Answers to PIN/password questions are: "1", "2", "3", "a", "b", "c"

NI Belfast

<br />
Customer Number: 9430 (charity, loan)
<br />
Customer Number: 1161 (current, deposit)
<br />
Customer Number: 1520 (social business, deposit)

Answers to PIN/password questions are: "1", "2", "3", "a", "b", "c"


Other credentials are available on request from the OBP team on site.

####Social Finance Application
The sandbox data is also available through a web application called Social Finance [here](https://ulsterbank-sofi.openbankproject.com/) and the default user credentials are the same as above.

Note: Social Finance (Scala / Lift) is also open sourced on [github] (https://github.com/OpenBankProject/Social-Finance).