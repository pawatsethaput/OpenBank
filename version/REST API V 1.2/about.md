### About

The Open Bank Project API is a RESTful API for banks that supports multiple views on transaction data, data enrichment and data blurring.

The protocol consists of *baseline* and *optional* end points.
For baseline compliance, all the baseline URLs must be implemented.
For full compliance, all the baseline and optional end points must be implemented.

 * All calls should be prefixed with /obp/v1.2

 * The OBP API returns JSON as specified [here](http://www.json.org/) and validated [here](http://jsonlint.com/).

 * Parameters within the URL are written in CAPITAL_LETTERS.

 * In some calls you will see the mention "OAuth authentication is required", that means that to access to the protected resource, the OAuth header is required with all the parameters like oauth_token, oauth_nonce, etc. ([See here for more details](https://github.com/OpenBankProject/OBP-API/wiki/OAuth-1.0-Server)).

 * If information is not available (whether missing or blocked by access control), its value will be null. This applies to normal values (Strings, Numbers, etc.) and to Arrays/Objects.

 Example:

    {
        "info" : "Some info",
        "info2" : {
            "info3" : "Some more info"
        }
    }

  could be returned as:

    {
        "info" : null,
        "info2" : null
    }