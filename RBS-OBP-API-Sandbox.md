####Hello!

This is a special page for the **Royal Bank of Scotland** OBP API sandbox created for the [Hack Make the Bank Hackathon](http://www.hackmakethebank.com/hmtb/rbs1/) in Edinburgh, October 9th - 11th.  


####Overview
Open Bank Project is an open source API for banks providing a common RESTful interface for developers to build customer facing applications. You can use it as a flexible toolbox of data and services to help realise (a.k.a. hack!) your ideas together. For the hackathons, developers have access to *simulated* transaction data for imaginary customers.   

####What sort of applications can I build with the OBP API?
Customer facing retail banking and fintech applications for consumers, SMEs, associations, charities, governments and NGOs; including (but not limited to!) Personal Finance Management (PFM) Solutions, online accounting integration, financial widgets, Savings Apps, Education Apps, Gamification, Peace of Mind Apps, Transparency Apps, Crowd funding, on boarding, CRM etc.. 

####What data and services can I access?
* Account information, balance and transaction history of multiple bank accounts
* Enrich bank transactions with metadata (tags, comments, urls and geolocation) for example to link a receipt or video to a transaction
* Create/Access different views on accounts. Each view grants a subset of the data to a restricted group of users. For example, a customer could offer special views on his account to his accountants, auditors or regulators. A charity might open their accounts to the public
* Initiate payments
* Access Customer messages
* Access data related to branches, ATMs and financial products

####API Explorer:
Use the [OBP API Explorer](https://rbs-sofi.openbankproject.com/api-explorer) to browse and test the API.


####This OBP instance contains test accounts for: 
* Royal Bank of Scotland
* Natwest Bank 



####Important Note to Developers:
In order to build and test your Apps, you will use two different logins:

* 1) Your developer login to get your API keys (see Application registration below)
* 2) One or more simulated customer logins to access transaction data etc. (See customer logins below)


####Application registration 
Please register your application [here](https://rbs.openbankproject.com/consumer-registration). You will get a consumer key and consumer secret for the calls requiring **OAuth** authentication.


####OAuth
To get started with OBP and OAuth we recomend you use (and fork) one of our Starter SDKs:

* [Python](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Python)
* [Django](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Django)
* [Node](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Node)
* [Mac](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Mac)
* [IOS](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-IOS)
* [Android](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Android)
* [PHP](https://github.com/solonas/OBP-PHP-HelloWorld)
 
**Note**: Many examples in the docs / SDKs use the general OBP sandbox domain. Make sure you use the correct domain in all calls i.e. ***rbs.openbankproject.com*** !

####FAQ:

*   Q: I'm getting a 401 even if I enter the right consumer key and secret. Is the endpoint: apisandbox...  ?

    A: No, its rbs.openbankproject.com

*   Q: I'm getting 404's / errors

    A: Avoid trailing slashes:

    https://rbs.openbankproject.com/obp/v1.4.0
    200 OK

    https://rbs.openbankproject.com/obp/v1.4.0/
    404 Not Found

    https://rbs.openbankproject.com/obp/v1.4.0/banks
    200 OK

    https://rbs.openbankproject.com/obp/v1.4.0/banks/
    200 OK {"error":"error"}

    https://rbs.openbankproject.com/obp/v1.4.0/banks/rbs-rbs-c
    200 OK

    https://rbs.openbankproject.com/obp/v1.4.0/banks/rbs-rbs-c/
    404 Not Found

*   Q: I'm getting 404s when POSTing

    A: Check you have the correct JSON body, headers and encoding. If in doubt, see an example [here](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Python/blob/master/hello_payments.py) 

*   Q: Why am I not seeing any data for the test user?

    A: Make sure you are using the correct bank_id/account_id for the user. Use the OBP API Explorer to [check](https://rbs-sofi.openbankproject.com/api-explorer)

*   Q: It still doesn't work. Why?

    A: Double check parameters are spelt correctly (including http vs https etc.)

    A: Check your encoding (use UTF8)

 

Technical details about the OAuth flow including getting a request token, redirecting the user, getting an access token and accessing protected resources are available [here](https://github.com/OpenBankProject/OBP-API/wiki/OAuth-1.0-Server). Please ask us (see below) if you are stuck with this.


####API documentation
* For the current **stable** API version [see 1.2.1](https://github.com/OpenBankProject/OBP-API/wiki/REST-API-V1.2.1). 

* For the latest draft [see 1.4.0](https://github.com/OpenBankProject/OBP-API/wiki/REST-API-V1.4.0)


* Try the [OBP API Explorer](https://rbs-sofi.openbankproject.com/api-explorer) to browse and test the API.


####Customer logins

During the OAuth login, the user of your app will be asked for a customer username/password.

Here are example logins to test your OAuth flow:

    Robert
    username : robert.r.c@example.com
    password : e9470e20

    Susan
    username : susan.r.c@example.com
    password : e00ef2ca

    Anil
    username : anil.r.c@example.com
    password : 40f2a819

    Ellie
    username : ellie.r.c@example.com
    password : f92b9eb4



To get different logins just for you / your team please ask a member of the Open Bank Project team. 

You can use this [application](https://rbs-sofi.openbankproject.com) which also uses OAuth to browse your transaction data (use the above username/password).

####Questions / Contact?
* For questions about the hackathon events please see:
 
 		http://www.hackmakethebank.com/

* To contact Open Bank Project use:

    Email: contact@openbankproject.com 
 		
    Twitter: **[@OpenBankProject](https://twitter.com/openbankproject)**

    Hackpad (for notes): https://hackpad.com/RBS-Hack-Make-the-Bank-Hackathon-1xLIdvsGrV7 

 