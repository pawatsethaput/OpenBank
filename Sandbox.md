##Dataset 
The sandbox data is available [here](https://demo.openbankproject.com/sandbox/) and the default user login and password are: 

login: joe.bloggs@example.com

password: qwerty

##Application registration 
To use the API with the sandbox data please register your application [here](https://demo.openbankproject.com/sandbox/consumer-registration). You will get a consumer key and consumer secret, they are necessary for the OAuth server.

##OAuth 
You will find a "how to" documentation for the OAuth server [here](https://github.com/OpenBankProject/OBP-API/wiki/OAuth-1.0-Server). 

##API 
The API specification is available for the version 1.1 [here](https://github.com/OpenBankProject/OBP-API/wiki/REST-API-V1.1).

### Note: 
Version 1.1 of the API is still a draft so not all the API calls have been implemented yet and the specification may change.


### Important: 
You will find links to use the real data set,like [https://demo.openbankproject.com/oauth/authorize?oauth_token=NPcudxy0yU5T3tBzho7iCotZ3cnetKwcTIRlX0iwRl0](https://demo.openbankproject.com/oauth/authorize?oauth_token=NPcudxy0yU5T3tBzho7iCotZ3cnetKwcTIRlX0iwRl0) please add "sandbox" in the query string like this: [https://demo.openbankproject.com/sanbox/oauth/authorize?oauth_token=NPcudxy0yU5T3tBzho7iCotZ3cnetKwcTIRlX0iwRl0](https://demo.openbankproject.com/sanbox/oauth/authorize?oauth_token=NPcudxy0yU5T3tBzho7iCotZ3cnetKwcTIRlX0iwRl0) to use the proper set of data otherwise you will have error messages.