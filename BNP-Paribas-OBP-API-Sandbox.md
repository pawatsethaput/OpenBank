####Hello!

This is a special page for the **BNP Paribas** OBP API sandbox created for the [BNP Paribas International Hackathon](http://www.international-hackathon.com/) on June 12-14 2015 in Brussels, Istanbul, Paris, Rome and San Fransisco.  


####Status
This document may be updated. The sandbox is in the process of being created and some data / endpoints may change.

####Overview
Open Bank Project is an open source API for banks that provides a RESTful interface for developers to build customer facing applications without needing to code for each bank and/or account type differently. You can use it as a flexible toolbox of data and services to help realise (a.k.a. hack!) your ideas together. For the hackathons, developers have access to *simulated* transaction data for imaginary customers that match certain customer profiles.   

####What sort of applications can I build with the OBP API?
Customer facing retail banking and fintech applications for consumers, SMEs, associations, charities, governments and NGOs; including (but not limited to!) Personal Finance Management (PFM) Solutions, online accounting integration, financial widgets, Savings Apps, Education Apps, Gamification, Peace of Mind Apps, Transparency Apps, Crowd funding, on boarding, CRM etc.. 

####What data and services can I access?
* Account information, balance and transaction history of multiple bank accounts
* Enrich bank transactions with metadata (tags, comments, urls and geolocation) for example to link a receipt or video to a transaction
* Create/Access different views on accounts. Each view grants a subset of the data to a restricted group of users. For example, a customer could offer special views on his account to his accountants, auditors or regulators. A charity might open their accounts to the public
* Initiate payments
* Access Customer messages
* List credit/debit cards held by a customer
* Access data related to branches, ATMs and financial products 

####This OBP instance contains five banks: 

* BNP Paribas - France
* BNP Paribas FORTIS - Belgium
* Bank of the West - USA
* TEB - Turkey
* BNL - Italy
 

For the API documentation, please see the link below. 

####Application registration 
You will need to register your application [here](https://bnp-paribas.openbankproject.com/consumer-registration). You will get a consumer key and consumer secret for the calls requiring **OAuth** authentication.


####OAuth
To get started with OBP and OAuth you can use (and fork) one of our Starter SDKs:

* [Python](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Python)
* [Django](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Django)
* [Node](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Node)
* [Mac](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Mac)
* [IOS](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-IOS)
* [Android](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Android)
 
**Note**: Many examples in the docs / SDKs use the general OBP sandbox domain. Make sure you use the correct domain in all calls i.e. bnp-paribas.openbankproject.com !

Technical details about the OAuth flow including getting a request token, redirecting the user, getting an access token and accessing protected resources are available [here](https://github.com/OpenBankProject/OBP-API/wiki/OAuth-1.0-Server). Please ask us (see below) if you are stuck with this.


####API documentation
* For the current **stable** API version [see 1.2.1](https://github.com/OpenBankProject/OBP-API/wiki/REST-API-V1.2.1). 

* For the latest draft [see 1.4.0](https://github.com/OpenBankProject/OBP-API/wiki/REST-API-V1.4.0)

####Customer logins

During the OAuth login, the user of your app will be asked for a customer username/password.

Here is an example login to test your OAuth flow:

    username : obptest.x.a1@example.com
    password : 2d7b7353

Note: In order to access the proper simulated accounts, please ask a member of the Open Bank Project team for a login. You can use this [application](https://bnp-paribas-sofi.openbankproject.com) which also uses OAuth to browse your transaction data (use the above username/password).

####Questions / Contact?
* For questions about the hackathon events please see:
 
 		http://www.international-hackathon.com/

* To contact Open Bank Project use:

    Email: bnpp@openbankproject.com 

    Skype: OBP-Support
 		
    Twitter: **[@OpenBankProject](https://twitter.com/openbankproject)**

    Hackpad (for notes): https://hackpad.com/OBP-BNPP-slveCQz1Ptt 

 