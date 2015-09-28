Welcome to the OBP-API wiki!

* [REST API V1.2.1 Stable](https://github.com/OpenBankProject/OBP-API/wiki/REST-API-V1.2.1)

* [Sandbox](https://github.com/OpenBankProject/OBP-API/wiki/Sandbox)

* [Overview diagram of OBP Architecture](https://github.com/OpenBankProject/OBP-API/wiki/Open-Bank-Project-Architecture)

* [OAuth 1.0a Server](https://github.com/OpenBankProject/OBP-API/wiki/OAuth-1.0-Server)

####Application registration
OBP protected resources use OAuth. You can use one of our sandboxes [(register here)](https://apisandbox.openbankproject.com/consumer-registration) to test.
You will get a consumer key and consumer secret for the calls requiring OAuth authentication.

####OAuth
The best way to get started with OBP and OAuth is probably to fork one of our Starter SDKs. They take care of the basic OAuth flow:

* [Hello-OBP-OAuth1.0a-Python](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Python)
* [Hello-OBP-OAuth1.0a-Django](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Django)
* [Hello-OBP-OAuth1.0a-Node](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Node)
* [Hello-OBP-OAuth1.0a-Mac](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Mac)
* [Hello-OBP-OAuth1.0a-IOS](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-IOS)
* [Hello-OBP-OAuth1.0a-Android](https://github.com/OpenBankProject/Hello-OBP-OAuth1.0a-Android)

We're sometimes asked about OAuth 1 and 2. So far we stick with OAuth 1.0a because it's stable ([RFC](https://tools.ietf.org/html/rfc5849)) is used by the likes of Twitter and Mastercard and according to the lead author of OAuth is more secure than OAuth2. For more on the topic, see [OAuth 2 and the road to hell](http://hueniverse.com/2012/07/26/oauth-2-0-and-the-road-to-hell/) or [this stack overflow article](http://stackoverflow.com/questions/4113934/how-is-oauth-2-different-from-oauth-1)


