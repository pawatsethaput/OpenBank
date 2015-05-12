####Hello,

This is a special page for the **BNP Paribas** OBP API sandbox created for the [BNP Paribas International Hackathon](http://www.bnpparibas.com/) on June 12-14 2015 in Brussels, Istanbul, Paris, Rome and San Fransisco.   


####Status
This document is DRAFT. The sandbox is in the process of being created and some data / endpoints will change prior to the hackathon!

####Overview
Open Bank Project is an open source API for banks that provides a RESTful interface for developers to build customer facing applications without needing to code for each bank and/or account type differently. You can use it as a flexible toolbox of data and services to help realise (a.k.a. hack!) your ideas together. For the hackathons developers have access to *simulated* transaction data for imaginary customers that match certain customer profiles.   

####What sort of applications can I build with the OBP API?
Customer facing retail banking and fintech applications for consumers, SMEs, associations, charities, governments and NGOs; including (but not limited to!) Personal Finance Management (PFM) Solutions, online accounting integration, financial widgets, Savings Apps, Education Apps, Gamification, Peace of Mind Apps, Transparency Apps, Crowd funding, onboarding, CRM etc.. 

####What data and services can I access?
● Access account information, balance and transaction history of a bank account
● Enrich bank transactions with metadata (tags, comments, urls and geolocation) for example to link a
receipt or video to a transaction.
● Create/Access different views on accounts. Each view grants a subset of the data to a restricted
group of users. For example, a customer could offer special views on his account to his
accountants, auditors or regulators. A charity might open their accounts to the public.
● Initiate payments
● Send messages to Customers
● List credit/debit cards held by a customer
● Access open data related to branches, ATMs and financial products 

Once complete this OBP instance will contain five banks: 
BNP Paribas - France
BNP Paribas FORTIS - Belgium
Bank of the West - USA
TEB - Turkey 
BNL - Italy

Here is the current list of banks on the sandbox (will change before the hackathon):
[https://bnp-paribas.openbankproject.com/obp/v1.2.1/banks](https://bnp-paribas.openbankproject.com/obp/v1.2.1/banks)


####Public Data
Some sandbox accounts have public views available. For example, here is a list of public accounts on the sandbox: 
[https://bnp-paribas.openbankproject.com/obp/v1.2.1/banks/bnpparibas-fr/accounts](https://bnp-paribas.openbankproject.com/obp/v1.2.1/banks/bnpparibas-fr/accounts)

And here is a public view of one account's transactions:
[https://bnp-paribas.openbankproject.com/obp/v1.2.1/banks/bnpparibas-fr/accounts/26d39ef8-f2bc-4cab-816f-1d15f4b81fa1/public/transactions](https://bnp-paribas.openbankproject.com/obp/v1.2.1/banks/bnpparibas-fr/accounts/26d39ef8-f2bc-4cab-816f-1d15f4b81fa1/public/transactions)


For other API calls, see the API Documentation (link below).

####Application registration
If your App needs access to private data and services, you will need to [register your application here](https://bnp-paribas.openbankproject.com/consumer-registration).
You will get a consumer key and consumer secret for the calls requiring OAuth authentication.

####OAuth
To get started with OBP and OAuth you can use (and fork) one of our Starter SDKs:

* [Python](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Python)
* [Django](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Django)
* [Node](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Node)
* [Mac](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Mac)
* [IOS](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-IOS)
* [Android](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Android)
 
**Note**: Many examples in the docs / SDKs use the general OBP sandbox domain. Make sure you use the correct domain in all calls i.e. bnp-paribas.openbankproject.com !

Technical details about the OAuth flow including getting a request token, redirecting the user, getting an access token and accessing protected resources are available [here](https://github.com/OpenBankProject/OBP-API/wiki/OAuth-1.0-Server).


####API documentation
For the current **stable** API version [see 1.2.1](https://github.com/OpenBankProject/OBP-API/wiki/REST-API-V1.2.1). 

####Customer logins

During the OAuth login, the user of your app will be asked for a customer username/password.

Here are two example logins (more will be provided later)

* username: jean-paul@example.com password: 045b947b

* username: rosalie@example.com password: 76eb7686

Note: Each customer has multiple accounts but only some accounts have transactions so far.

####Social Finance application
You can test the logins above, and browse the sandbox data through the Social Finance applicaiton (which uses the OBP API) [here](https://bnp-paribas-sofi.openbankproject.com/).

Note: Social Finance (Scala / Lift) is also open sourced on [github](https://github.com/OpenBankProject/Social-Finance)

####Questions?
* For questions about the Open Bank Project API please email:
 
 		contact AT openbankproject DOT com 
 		
 	or tweet us at **@OpenBankProject**
