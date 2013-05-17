# Version: 1.2

# Status: DRAFT

# Index

* [About](#about)
* [Implementation hints](#implementation-hints)
* Banks
    * [All](#banks)
    * [One](#bank)
    * [Offices](#offices)
* Accounts
    * [All](#accounts)
    * [Public](#accounts-public)
    * [Private](#accounts-private)
    * [One](#account)
    * [Access Control](#account-access-control)
    * Other Accounts
        * [All](#account-other-accounts)
        * [One](#account-other-account)
          * [Meta data](#other_account-metadata)
              * [Alias](#alias)
              * [More info](#more_info)
              * [URL](#URL)
              * [Image URL](#image_url)
              * [Open Corporates URL](#opencorporates-URL)
              * [Corporate Location](#corporate_location)
              * [Physical Location](#physical_location)
* Transactions
    * [All](#transactions)
    * [One](#transaction)
    * Meta data
        * [Narrative](#narrative)
        * [Comments](#comments)
        * [Tags](#tags)
        * [Images](#images)
        * [Where](#where)
    * [Other account](#transaction_other_account)
* [The views](#views)


<span id="about"></span>
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


<span id="implementation-hints"></span>
### Implementation hints and notes.

The implementation of V1.2 is work in progress

<span id="root"></span>
**GET /**

*Baseline*

Returns information about :

* API version
* Hosted by information


JSON:

    {
        "version": "1.2",
        "git_commit": "4ca0cdbe9e51e41d0105bf26cf463ac5225a50a1",
        "hosted_by": {
            "organisation": "TESOBE",
            "email": "contact@tesobe.com",
            "phone": "+49 (0)30 8145 3994"
        }
    }

<span id="banks"></span>
#Banks

**GET /banks**

*Baseline*

Returns a list of banks supported on this server:

* ID used as parameter in URLs
* Short and full name of bank
* Logo URL
* Website


JSON:

    {
        "banks": [
            {
                "id": "hsbc",
                "short_name": "HSBC",
                "full_name": "The Hongkong and Shanghai Banking Corporation Limited",
                "logo": "url of internet standard image",
                "website": "www.postbank.de"
            }
        ]
    }

<span id="bank"></span>
#Bank

**GET /banks/BANK_ID**

*Optional*

Returns information about a single bank specified by BANK_ID including:

* Short and full name of bank
* Logo URL
* Website


JSON:

    {
        "short_name": "Postbank",
        "full_name": "Deutsche Postbank AG (DE)",
        "logo": "url of internet standard image",
        "website": "www.postbank.de"
    }

<span id="offices"></span>
#Offices

**GET /banks/BANK_ID/offices**

*Optional*

Information returned includes:

* The list of offices for the BANK_ID.


JSON:

    {
        "offices": [
            {
                "address": "address",
                "type": "HQ/branch/call center",
                "BIC_SWIFT": "ISO_9362 BIC / SWIFT code",
                "fax": [
                    {
                        "department": "customer service",
                        "number": "004401293801"
                    }
                ],
                "phone": [
                    {
                        "department": "customer service",
                        "number": "004912312302"
                    }
                ]
            }
        ]
    }

<span id="accounts"></span>
#Accounts

**GET /banks/BANK_ID/accounts**

*Baseline*

Returns the list of accounts at BANK_ID that the user has access to.
For each account the API returns the ID and the available views.

If the user is not authenticated via OAuth, the list will contains only the accounts providing public views.

JSON:

    {
        "accounts": [
            {
                "id": "A unique identifier used for ACCOUNT_ID",
                "label" : "account label e.g. TESOBE main account",
                "views_available": [
                    {
                        "id": "A unique identifier used for VIEW_ID",
                        "short_name": "Public / Team / Auditors...",
                        "description": "e.g. this is the public view of the TESOBE account",
                        "is_public": "boolean. true if the public (user not logged in) can see this view."
                    }
                ],
                "bank_id":"the id of the bank where the account is hosted"
            }
        ]
    }

<span id="accounts-public"></span>
#Public Accounts


**GET /banks/BANK_ID/accounts/public**

*Optional*

Returns a list of the public accounts list at BANK_ID. For each account the API returns the ID and the available views.

Authentication via OAuth is not required.

JSON:

    {
        "accounts": [
            {
                "id": "A unique identifier used for ACCOUNT_ID",
                "label" : "account label e.g. TESOBE main account",
                "views_available": [
                    {
                        "id": "A unique identifier used for VIEW_ID",
                        "short_name": "Public / Team / Auditors...",
                        "description": "e.g. this is the public view of the TESOBE account",
                        "is_public": "boolean. true if the public (user not logged in) can see this view."
                    }
                ],
                "bank_id":"the id of the bank where the account is hosted"
            }
        ]
    }

<span id="accounts-private"></span>
#Private Accounts


**GET /banks/BANK_ID/accounts/private**

*Optional*

Returns the list of accounts at BANK_ID that the user has private (non-public) access to.
For each account the API returns the ID and the available views.

Authentication via OAuth is required.

JSON:

    {
        "accounts": [
            {
                "id": "A unique identifier used for ACCOUNT_ID",
                "label" : "account label e.g. TESOBE main account",
                "views_available": [
                    {
                        "id": "A unique identifier used for VIEW_ID",
                        "short_name": "Public / Team / Auditors...",
                        "description": "e.g. this is the public view of the TESOBE account",
                        "is_public": "boolean. true if the public (user not logged in) can see this view."
                    }
                ],
                "bank_id":"the id of the bank where the account is hosted"
            }
        ]
    }

<span id="account"></span>
#Account

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/account**

*Baseline*

Information returned about an account specified by ACCOUNT_ID as moderated by the view (VIEW_ID) :

* Number
* Owners
* Type
* Balance
* IBAN
* Available views

More details about the data moderation by the view [here](#views).

OAuth authentication is required if the "is_public" field in view (VIEW_ID) is not set to "true".

JSON:

    {
        "id": "A unique identifier used for ACCOUNT_ID",
        "label" : "account label e.g. TESOBE main account",
        "number": "account number (moderated by the view)",
        "owners": [
            {
                "id": "123213",
                "provider": "bank name",
                "display_name": "Name of person/Company/..."
            }
        ],
        "type": "e.g. current, savings",
        "balance": {
            "currency": "currency in ISO_4217 one of EUR/GBP,USD/...",
            "amount": "number or +/-"
        },
        "IBAN": "123080FZAFA9124AZE",
        "views_available": [
            {
                "id": "A unique identifier used for VIEW_ID",
                "short_name": "Public / Team / Auditors...",
                "description": "e.g. this is the public view of the TESOBE account",
                "is_public": "boolean. true if the public can see this view."
            }
        ],
        "bank_id":"the id of the bank where the account is hosted"
    }

#Access Control
<span id="account-access-control"></span>

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/account/users**

*Optional*

OAuth authentication is required and the user needs to have access to the owner view.

Returns a list of users and the non-public views they are permitted access to at BANK_ID for account ACCOUNT_ID.

JSON:

    {
        "permissions": [
            {
                "user": {
                        "display_name": "display name of user",
                        "id": "OBP UUID of the user making the comment",
                        "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter"
                },
                "views": [
                    {
                        "description": "e.g. this is the public view of the TESOBE account",
                        "id": "A unique identifier used for the view",
                        "is_public": false,
                        "short_name": "Public / Team / Auditors..."
                    }
                ]
            }
        ]
    }

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/users/USER_ID**

*Optional*

OAuth authentication is required and the user needs to have access to the owner view.

Returns the list of non-public views that user USER_ID is permitted access to at BANK_ID for account ACCOUNT_ID.

JSON:

    {
        "views": [
            {
                "id": "A unique identifier used for the view",
                "short_name": "Team / Auditors...",
                "description": "e.g. this is the public view of the TESOBE account",
                "is_public": false
            }
        ]
    }

**POST /banks/BANK_ID/accounts/ACCOUNT_ID/users/USER_ID/views/VIEW_ID**

*Optional*

OAuth authentication is required and the user needs to have access to the owner view.

Grants the user USER_ID access to the view VIEW_ID at BANK_ID for account ACCOUNT_ID.

Granting access to a public view will return an error message, as the user already has access.

Response:

Header: 201

Body:

    {
        {
            "id": "A unique identifier used for the view",
            "short_name": "Team / Auditors...",
            "description": "e.g. this is the public view of the TESOBE account",
            "is_public": false
        }
    }


**DELETE /banks/BANK_ID/accounts/ACCOUNT_ID/users/USER_ID/views/VIEW_ID**

*Optional*

OAuth authentication is required and the user needs to have access to the owner view.

Revokes the user USER_ID access to the view VIEW_ID at BANK_ID for account ACCOUNT_ID.

Revoking a user access to a public view will return an error message.

<span id="account-other-accounts"></span>
#Other Accounts

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts**

*Optional*

OAuth authentication is required if the view is not public.

Returns data about all the other bank accounts that have been involved with the ACCOUNT_ID.

JSON:

    {
        "other_accounts": [
            {
                "id" : "the other account's id",
                "holder": {
                    "name": "DEUTSCHEPOSTAG, SSCACCS",
                    "is_alias": "true/false"
                },
                "number": "",
                "kind": "",
                "IBAN": "",
                "bank": {
                    "national_identifier": "",
                    "name": ""
                },
                "metadata": {
                    "public_alias":"the public alias of the other account holder",
                    "private_alias":"the private alias of the other account holder",
                    "more_info": "short text explaining who the other party of the transaction is",
                    "URL": "a URL related to the other party e.g. the website of the company",
                    "image_URL": "an image URL related to the other party e.g. company logo",
                    "open_corporates_URL": "the company corporate URL in the http://opencorporates.com/ web service",
                    "corporate_location": {
                            "latitude": 37.423021,
                            "longitude": -122.083739,
                            "date": "date of posting the geo tag",
                            "user": {
                                "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                                "id": "provider id of the user making the tag",
                                "display_name": "display name of user"
                            }
                    },
                    "physical_location": {
                            "latitude": 37.423021,
                            "longitude": -122.083739,
                            "date": "date of posting the geo tag",
                            "user": {
                                "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                                "id": "provider id of the user making the tag",
                                "display_name": "display name of user"
                            }
                    }
                }
            }
        ]
    }

<span id="account-other-account"></span>
#Other Account

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID**

*Optional*

OAuth authentication is required if the view is not public.

Returns data about one other bank accounts (OTHER_ACCOUNT_ID) that have been involved with the ACCOUNT_ID.

JSON:

    {
      "id" : "the other account's id",
        "holder": {
            "name": "DEUTSCHEPOSTAG, SSCACCS",
            "is_alias": "true/false"
        },
        "number": "",
        "kind": "",
        "IBAN": "",
        "bank": {
            "national_identifier": "",
            "name": ""
        },
        "metadata": {
            "public_alias":"the public alias of the other account holder",
            "private_alias":"the private alias of the other account holder",
            "more_info": "short text explaining who the other party of the transaction is",
            "URL": "a URL related to the other party e.g. the website of the company",
            "image_URL": "an image URL related to the other party e.g. company logo",
            "open_corporates_URL": "the company corporate URL in the http://opencorporates.com/ web service",
            "corporate_location": {
                    "latitude": 37.423021,
                    "longitude": -122.083739,
                    "date": "date of posting the geo tag",
                    "user": {
                        "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                        "id": "provider id of the user making the tag",
                        "display_name": "display name of user"
                    }
            },
            "physical_location": {
                    "latitude": 37.423021,
                    "longitude": -122.083739,
                    "date": "date of posting the geo tag",
                    "user": {
                        "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                        "id": "provider id of the user making the tag",
                        "display_name": "display name of user"
                    }
            }
        }
    }


<span id="other_account-metadata"></span>
#Other account metadata

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata**

*Optional*

Authentication via OAuth is required if the view is not public.

Returns account meta data of the other party involved in the transaction ([here](#transaction)) moderated by the [view](#views) (VIEW_ID).

JSON:

    {
        "public_alias":"the public alias of the other account holder",
        "private_alias":"the private alias of the other account holder",
        "more_info": "short text explaining who the other party of the transaction is",
        "URL": "a URL related to the other party e.g. the website of the company",
        "image_URL":"an image URL related to the other party e.g. company logo",
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service",
        "corporate_location": {
                "latitude": 37.423021,
                "longitude": -122.083739,
                "date": "date of posting the geo tag",
                "user": {
                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                    "id": "provider id of the user making the tag",
                    "display_name": "display name of user"
                }
        },
        "physical_location": {
                "latitude": 37.423021,
                "longitude": -122.083739,
                "date": "date of posting the geo tag",
                "user": {
                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                    "id": "provider id of the user making the tag",
                    "display_name": "display name of user"
                }
        }
    }

<span id="alias"></span>
### Alias

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/holder**

*Optional*

OAuth authentication is required if the view is not public.

Gets the holder for other account OTHER_ACCOUNT_ID for view VIEW_ID on account ACCOUNT_ID.

JSON:

    {
        "holder": {
            "name": "Joe Bloggs",
            "is_alias": "true/false"
        }
    }

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/public_alias**

*Optional*

OAuth authentication is required if the view is not public.

Get the public alias for other account OTHER_ACCOUNT_ID for account ACCOUNT_ID.

The VIEW_ID parameter should be a view the caller is permitted to access to and that has permission to view public aliases.

JSON:

    {
        "alias" : "An alias to display instead of the real name"
    }

**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/public_alias**

*Optional*

OAuth authentication is required if the view is not public.

Creates the public alias for other account OTHER_ACCOUNT_ID for account ACCOUNT_ID.

Note: Public aliases are automatically generated for new "other accounts", so this call should only be used if
the public alias was deleted.

The VIEW_ID parameter should be a view the caller is permitted to access to and that has permission to create public aliases.

JSON:

    {
        "alias" : "An alias to display instead of the real name"
    }

Response:

Header: 201

Body:

    {
        "success" : "public alias added"
    }

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/public_alias**

*Optional*

OAuth authentication is required if the view is not public.

Updates the public alias for other account OTHER_ACCOUNT_ID for account ACCOUNT_ID.

The VIEW_ID parameter should be a view the caller is permitted to access to and that has permission to edit public aliases.

JSON:

    {
        "alias" : "An alias to display instead of the real name"
    }

**DELETE /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/public_alias**

*Optional*

OAuth authentication is required if the view is not public.

Deletes the public alias for other account OTHER_ACCOUNT_ID for ACCOUNT_ID.

The VIEW_ID parameter should be a view the caller is permitted to access to and that has permission to delete public aliases.

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/private_alias**

*Optional*

OAuth authentication is required if the view is not public.

Get the private alias for other account OTHER_ACCOUNT_ID for account ACCOUNT_ID.

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to view private aliases.

JSON:

    {
        "alias" : "An alias to display instead of the real name"
    }

**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/private_alias**

*Optional*

OAuth authentication is required if the view is not public.

Creates the private alias for other account OTHER_ACCOUNT_ID for account ACCOUNT_ID.

The VIEW_ID parameter should be a view the caller is permitted to access to and that has permission to create private aliases.

JSON:

    {
        "alias" : "An alias to display instead of the real name"
    }

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/private_alias**

*Optional*

OAuth authentication is required if the view is not public.

Updates the private alias for other account OTHER_ACCOUNT_ID for account ACCOUNT_ID.

The VIEW_ID parameter should be a view the caller is permitted to access to and that has permission to edit private aliases.

JSON:

    {
        "alias" : "An alias to display instead of the real name"
    }

Response:

Header: 201

Body:

    {
        "success" : "private alias added"
    }

**DELETE /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/private_alias**

*Optional*

OAuth authentication is required if the view is not public.

Deletes the private alias for other account OTHER_ACCOUNT_ID for ACCOUNT_ID.

The VIEW_ID parameter should be a view the caller is permitted to access to and that has permission to delete public aliases.

Response:

Header: 204

Body: No content

<span id="more_info"></span>
### More info

**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/more_info**

*Optional*

Authentication via OAuth is required.

Save data in the "more_info" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit more info.


JSON:

    {
        "more_info": "short text explaining who the other party of the transaction is"
    }

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/more_info**

*Optional*

Authentication via OAuth is required.

Update data in the "more_info" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit more info.

JSON:

    {
        "more_info": "short text explaining who the other party of the transaction is"
    }

**DELETE /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/more_info**

*Optional*

Authentication via OAuth is required.

Delete data in the "more_info" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit more info.

Response:

Header: 204

Body: No content
<span id="URL"></span>
### URL

**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/url**

*Optional*

Authentication via OAuth is required.

Save data in the "URL" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit the url.

JSON:

    {
        "URL": "a URL related to the other party e.g. the website of the company"
    }

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/url**

*Optional*

Authentication via OAuth is required.

Update data in the "URL" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit the url.

JSON:

    {
        "URL": "a URL related to the other party e.g. the website of the company"
    }

**DELETE /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/url**

*Optional*

Authentication via OAuth is required.

Delete data in the "URL" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit the url.

Response:

Header: 204

Body: No content

<span id="image_url"></span>
### Image URL
**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/image_url**

*Optional*

Authentication via OAuth is required.

Save data in the "image_url" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit the image url.

JSON:

    {
        "image_URL":"an image URL related to the other party e.g. company logo"
    }

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/image_url**

*Optional*

Authentication via OAuth is required.

Update data in the "image_url" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit the image url.

JSON:

    {
        "image_URL":"an image URL related to the other party e.g. company logo"
    }

**DELETE /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/image_url**

*Optional*

Authentication via OAuth is required.

Delete data in the "image_url" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit the image url.

Response:

Header: 204

Body: No content

<span id="opencorporates-URL"></span>
### Open corporates URL

**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/open_corporates_url**

*Optional*

Authentication via OAuth is required.

Save data in the "opencorporate-URL" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit the open corporates url.

JSON:

    {
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service "
    }

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/open_corporates_url**

*Optional*

Authentication via OAuth is required.

Update data in the "opencorporate-URL" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit the open corporates url.

JSON:

    {
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service "
    }

**DELETE /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/open_corporates_url**

*Optional*

Authentication via OAuth is required.

Delete data in the "opencorporate-URL" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit the open corporates url.

Response:

Header: 204

Body: No content

<span id="corporate_location"></span>
### Corporate location


**POST /banks/BANK_ID/accounts/ACCOUNT_ID/public/other_accounts/OTHER_ACCOUNT_ID/metadata/corporate_location**

*Optional*

Authentication via OAuth is not currently required. (public)

Save data in the "corporate_location" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit the corporate location.

JSON:

    {
        "corporate_location": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/corporate_location**

*Optional*

Authentication via OAuth is only required if the view is not public.

(only public is currently supported)

Update data in the "corporate_location" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit the corporate location.

JSON:

    {
        "corporate_location": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }

**DELETE /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/corporate_location**

*Optional*

Authentication via OAuth is required.

Delete data in the "corporate_location" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit the corporate location.

Response:

Header: 204

Body: No content

<span id="physical_location"></span>
### Physical location


**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/physical_location**

*Optional*

Authentication via OAuth is only required if the view is not public.

(only public is currently supported)

Save data in the "physical_location" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit the physical location.

JSON:

    {
        "physical_location": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/physical_location**

*Optional*

Authentication via OAuth is required if the view is not public

(only public is currently implemented)

Update data in the "physical_location" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit the physical location.

JSON:

    {
        "physical_location": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }

**DELETE /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/physical_location**

*Optional*

Authentication via OAuth is required.

Delete data in the "physical_location" field of other account meta data (the other party involved in the transaction [here](#transaction))

The VIEW_ID parameter should be a view the caller is permitted to access that has permission to edit the physical location.

Response:

Header: 204

Body: No content

<span id="transactions"></span>
#Transactions

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions**

*Baseline*

Authentication via OAuth is required if the view is not public.

With the following custom headers:

* obp_sort_by=CRITERIA ==> default value: "completed" field
* obp_sort_direction=ASC/DESC ==> default value: DESC
* obp_limit=NUMBER ==> default value: 50
* obp_offset=NUMBER ==> default value: 0
* obp_from_date=DATE => default value: date of the oldest transaction registered
* obp_to_date=DATE => default value: date of the newest transaction registered

Returns transactions of the account specified by ACCOUNT_ID and [moderated](#views) by the view (VIEW_ID).

JSON:

    {
        "transactions": [
            {
                "uuid": "A universally unique id e.g. 4f5745f4e4b095974c0eeead",
                "id": "The bank's id for the transaction",
                "this_account": {
                    "id":"ACCOUNT_ID",
                    "holders": [
                        {
                            "name": "MUSIC PICTURES LIMITED",
                            "is_alias": "true/false"
                        }
                    ],
                    "number": "",
                    "kind": "",
                    "IBAN": "",
                    "bank": {
                        "national_identifier": "",
                        "name": ""
                    }
                },
                "other_account": {
                    "id":"123213",
                    "holder": {
                        "name": "DEUTSCHE POST AG, SSC ACC S",
                        "is_alias": "true/false"
                    },
                    "number": "",
                    "kind": "",
                    "IBAN": "",
                    "bank": {
                        "national_identifier": "",
                        "name": ""
                    },
                    "metadata": {
                        "public_alias":"the public alias of the other account holder",
                        "private_alias":"the private alias of the other account holder",
                        "more_info": "short text explaining who the other party of the transaction is",
                        "URL": "a URL related to the other party e.g. the website of the company",
                        "image_URL": "an image URL related to the other party e.g. company logo",
                        "open_corporates_URL": "the company corporate URL in the http://opencorporates.com/ web service",
                        "corporate_location": {
                                "latitude": 37.423021,
                                "longitude": -122.083739,
                                "date": "date of posting the geo tag",
                                "user": {
                                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                                    "id": "provider id of the user making the tag",
                                    "display_name": "display name of user"
                                }
                        },
                        "physical_location": {
                                "latitude": 37.423021,
                                "longitude": -122.083739,
                                "date": "date of posting the geo tag",
                                "user": {
                                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                                    "id": "provider id of the user making the tag",
                                    "display_name": "display name of user"
                                }
                        }
                    }
                },
                "details": {
                    "type": "cash",
                    "label": "transaction label",
                    "posted": "2012-03-07T00:00:00.001Z",
                    "completed": "2012-03-07T00:00:00.001Z",
                    "new_balance": {
                        "currency": "EUR",
                        "amount": "+ (depending on the view, this might show the balance or only +/-)"
                    },
                    "value": {
                        "currency": "EUR",
                        "amount": "-1.45"
                    }
                },
                "metadata": {
                    "narrative": "text explaining the purpose of the transaction",
                    "comments": [
                        {
                            "id": "id of the comment",
                            "date": "date of posting the comment",
                            "value": "the comment",
                            "user": {
                                "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                                "id": "OBP UUID of the user making the comment",
                                "display_name": "display name of user"
                            }
                        }
                    ],
                    "tags": [
                        {
                            "id": "id of the tag",
                            "value": "thetag",
                            "date": "date of posting the tag",
                            "user": {
                                "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                                "id": "OBP UUID of the user making the comment",
                                "display_name": "display name of user"
                            }
                        }
                    ],
                    "images": [
                        {
                            "id": "1239qsxezad0123",
                            "label": "cool image",
                            "URL": "http://www.mysuperimage.com"
                        }
                    ],
                    "where": {
                        "latitude": 37.423021,
                        "longitude": -122.083739,
                        "date": "date of posting the tag",
                        "user": {
                            "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                            "id": "provider id of the user making the tag",
                            "display_name": "display name of user"
                        }
                    }
                }
            }
        ]
    }

<span id="transaction"></span>
#Transaction

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/transaction**

*Optional*

Authentication via OAuth is required if the view is not public.

Returns information [moderated](#views) by the view about a specific transaction

JSON:

    {
        "id": "The bank's id for the transaction",
        "this_account": {
            "id":"ACCOUNT_ID",
            "holders": [
                {
                    "name": "MUSIC PICTURES LIMITED",
                    "is_alias": "true/false"
                }
            ],
            "number": "",
            "kind": "",
            "IBAN": "",
            "bank": {
                "national_identifier": "",
                "name": ""
            }
        },
        "other_account": {
            "id":"123213",
            "holder": {
                "name": "DEUTSCHE POST AG, SSC ACC S",
                "is_alias": "true/false"
            },
            "number": "",
            "kind": "",
            "IBAN": "",
            "bank": {
                "national_identifier": "",
                "name": ""
            },
            "metadata": {
                "public_alias":"the public alias of the other account holder",
                "private_alias":"the private alias of the other account holder",
                "more_info": "short text explaining who the other party of the transaction is",
                "URL": "a URL related to the other party e.g. the website of the company",
                "image_URL": "an image URL related to the other party e.g. company logo",
                "open_corporates_URL": "the company corporate URL in the http://opencorporates.com/ web service",
                "corporate_location": {
                        "latitude": 37.423021,
                        "longitude": -122.083739,
                        "date": "date of posting the geo tag",
                        "user": {
                            "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                            "id": "provider id of the user making the tag",
                            "display_name": "display name of user"
                        }
                },
                "physical_location": {
                        "latitude": 37.423021,
                        "longitude": -122.083739,
                        "date": "date of posting the geo tag",
                        "user": {
                            "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                            "id": "provider id of the user making the tag",
                            "display_name": "display name of user"
                        }
                }
            }
        },
        "details": {
            "type": "cash",
            "label": "transaction label",
            "posted": "2012-03-07T00:00:00.001Z",
            "completed": "2012-03-07T00:00:00.001Z",
            "new_balance": {
                "currency": "EUR",
                "amount": "+ (depending on the view, this might show the balance or only +/-)"
            },
            "value": {
                "currency": "EUR",
                "amount": "-1.45"
            }
        },
        "metadata": {
            "narrative": "text explaining the purpose of the transaction",
            "comments": [
                {
                    "id": "id of the comment",
                    "date": "date of posting the comment",
                    "value": "the comment",
                    "user": {
                        "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                        "id": "OBP UUID of the user making the comment",
                        "display_name": "display name of user"
                    }
                }
            ],
            "tags": [
                {
                    "id": "id of the tag",
                    "value": "thetag",
                    "date": "date of posting the tag",
                    "user": {
                        "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                        "id": "OBP UUID of the user making the comment",
                        "display_name": "display name of user"
                    }
                }
            ],
            "images": [
                {
                    "id": "1239qsxezad0123",
                    "label": "cool image",
                    "URL": "http://www.mysuperimage.com"
                }
            ],
            "where": {
                "latitude": 37.423021,
                "longitude": -122.083739,
                "date": "date of posting the tag",
                "user": {
                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                    "id": "provider id of the user making the tag",
                    "display_name": "display name of user"
                }
            }
        }
    }

#Transaction Metadata

<span id="narrative"></span>
### Narrative

**GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/transactions/TRANSACTION_ID/metadata/narrative**

*Optional*

Authentication via OAuth is required if the view is not public.

Returns the account owner description, [moderated](#views) by the view, of the transaction.


JSON:

    {
        "narrative" : "text explaining the purpose of the transaction"
    }

**POST /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/transactions/TRANSACTION_ID/metadata/narrative**

*Optional*

Authentication via OAuth is required.

Saves the account owner description of the transaction if the [view](#views) allow it.

JSON:

    {
        "narrative" : "text explaining the purpose of the transaction"
    }

**PUT /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/transactions/TRANSACTION_ID/metadata/narrative**

*Optional*

Authentication via OAuth is required.

Updates the account owner description of the transaction if the [view](#views) allow it.


JSON:

    {
        "narrative" : "text explaining the purpose of the transaction"
    }

**DELETE /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/transactions/TRANSACTION_ID/metadata/narrative**

*Optional*

Authentication via OAuth is required.

Deletes the account owner description of the transaction for a [view](#views).

Response:

Header: 204

Body: No content

<span id="comments"></span>
### Comments

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/comments**

*Optional*

Authentication via OAuth is required if the view is not public.

Returns comments about a specific transaction made on a [view](#views) (VIEW_ID).

JSON:

    {
        "comments": [
            {
                "id": "id of the comment",
                "date": "date of posting the comment",
                "value": "the comment",
                "user": {
                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                    "id": "OBP UUID of the user making the comment",
                    "display_name": "display name of user"
                }
            }
        ]
    }

**Note**: user.provider + user.id is unique

**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/comments**

*Optional*

Post a comment about a transaction on a specific [view](#views).

OAuth authentication is required since the comment are linked with the user.

JSON:

    {
        "value" : "the comment"
    }

Response:

Header: 201

Body:

    {
        "id": "id of the comment",
        "date": "date of posting the comment",
        "value": "the comment",
        "user": {
            "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
            "id": "OBP UUID of the user making the comment",
            "display_name": "display name of user"
        }
    }

**DELETE /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/comments/COMMENT_ID**

*Optional*

Authentication via OAuth is required. The user must either have owner privileges for this account, or must be the user that posted the comment.

Delete a comment about a transaction on a specific [view](#views).

Response:

Header: 204

Body: No content

<span id="tags"></span>
### Tags

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/tags**

*Optional*

Authentication via OAuth is required if the view is not public.

Returns tags about a specific transaction made on a [view](#views) (VIEW_ID).


JSON:

    {
        "tags": [
            {
                "id": "id of the tag",
                "value": "thetag",
                "date": "date of posting the tag",
                "user": {
                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                    "id": "OBP UUID of the user making the comment",
                    "display_name": "display name of user"
                }
            }
        ]
    }

**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/tags**

*Optional*

OAuth Header is required since the tag is linked with the user.

Post a tag on a transaction in a [view](#views).

JSON:

    {
        "value": "a_tag"
    }

Response:

Header: 201

Body:

    {
        "id": "id of the tag",
        "value": "thetag",
        "date": "date of posting the tag",
        "user": {
            "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
            "id": "OBP UUID of the user making the comment",
            "display_name": "display name of user"
        }
    }

**Note**: the value on the tag MUST NOT contain a white space.

**DELETE /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/tags/TAG_ID**

*Optional*

Authentication via OAuth is required. The user must either have owner privileges for this account, or must be the user that posted the tag.

Delete a tag on a transaction in a [view](#views).

Response:

Header: 204

Body: No content

<span id="images"></span>
### Images

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/images**

*Optional*

Authentication via OAuth is required if the view is not public.

Returns the images added to a specific transaction made on a [view](#views) (VIEW_ID).


JSON:

    {
        "images": [
            {
                "id": "1239qsxezad0123",
                "label": "cool image",
                "URL":"http://www.mysuperimage.com",
                "date": "date of posting the image",
                "user": {
                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                    "id": "OBP UUID of the user who have posted the image",
                    "display_name": "display name of user"
                }
            }
        ]
    }

**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/images**

*Optional*

OAuth Header is required since the tag is linked with the user.

Post an image on a transaction in a [view](#views).

JSON:

    "image": {
        "label": "cool image",
        "URL":"http://www.mysuperimage.com"
    }

Response:

Header: 201

Body:

    {
        "id": "1239qsxezad0123",
        "label": "cool image",
        "URL":"http://www.mysuperimage.com",
        "date": "date of posting the image",
        "user": {
            "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
            "id": "OBP UUID of the user posting the image",
            "display_name": "display name of user"
        }
    }

**DELETE /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/images/IMAGE_ID**

*Optional*

Authentication via OAuth is required. The user must either have owner privileges for this account, or must be the user that posted the image.

Delete an image on a transaction in a [view](#views).

Response:

Header: 204

Body: No content

<span id="where"></span>
### Where

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/where**

*Optional*

Authentication via OAuth is required if the view is not public.

Returns the "where" Geo tag added to a specific transaction made on a [view](#views) (VIEW_ID).
It represents the location where the transaction has been initiated.

JSON:

    {
        "where": {
            "latitude": 37.423021,
            "longitude": -122.083739,
            "date": "date of posting the tag",
            "user": {
                "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                "id": "provider id of the user making the tag",
                "display_name": "display name of user"
            }
        }
    }

**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/where**

*Optional*

OAuth Header is required since the tag is linked with the user.

Post the "where" Geo tag on a transaction in a [view](#views).

JSON:

    {
        "where": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }


**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/where**

*Optional*

OAuth Header is required since the tag is linked with the user.

Update the "where" Geo tag on a transaction in a [view](#views).

JSON:

    {
        "where": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }

**DELETE /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/where**

*Optional*

Authentication via OAuth is required. The user must either have owner privileges for this account, or must be the user that posted the 'where'.

Delete the where on a transaction in a [view](#views).

Response:

Header: 204

Body: No content

<span id="transaction_other_account"></span>
#Other account

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/other_account**

*Baseline*

Authentication via OAuth is required if the view is not public.

Returns account information, of the other party involved in the transaction, moderated by the [view](#views) (VIEW_ID).

JSON:

    {
      "id" : "the other account's id",
        "holder": {
            "name": "DEUTSCHEPOSTAG, SSCACCS",
            "is_alias": "true/false"
        },
        "number": "",
        "kind": "",
        "IBAN": "",
        "bank": {
            "national_identifier": "",
            "name": ""
        },
        "metadata": {
            "public_alias":"the public alias of the other account holder",
            "private_alias":"the private alias of the other account holder",
            "more_info": "short text explaining who the other party of the transaction is",
            "URL": "a URL related to the other party e.g. the website of the company",
            "image_URL": "an image URL related to the other party e.g. company logo",
            "open_corporates_URL": "the company corporate URL in the http://opencorporates.com/ web service",
            "corporate_location": {
                "latitude": 37.423021,
                "longitude": -122.083739,
                "date": "date of posting the geo tag",
                "user": {
                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                    "id": "provider id of the user making the tag",
                    "display_name": "display name of user"
                }
            },
            "physical_location": {
                "latitude": 37.423021,
                "longitude": -122.083739,
                "date": "date of posting the geo tag",
                "user": {
                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                    "id": "provider id of the user making the tag",
                    "display_name": "display name of user"
                }
            }
        }
    }

<span id="views"></span>
### The views

Views on accounts and transactions filter the underlying data to hide or blur certain fields from certain users. For instance the balance on an account may be hidden from the public or replaced by + or -.

**data:** When a view moderates a set of data, some fields my contain the value null rather than the original value. This indicates either that the user is not allowed to see the original data or the field is empty.

There are currently two exceptions to this rule:

1) The "holder" field in the JSon contains a value which is either an alias or the real name - indicated by the "is_alias" field.

2) The "balance" field (in a transaction or account details) may contain the real amount, a plus sign (+), a minus sign (-) or "unauthorized".

**action:** When a user performs an action like trying to post a comment (with POST API call), if he is not allowed, the repose will be an "unauthorized" HTTP code 401.

**Metadata:**
Transaction metadata like (images, tags, comments, etc.) will appears ONLY on the view where they have been created e.g. comments posted to the public view only appear on the public view.

The other account metadata fields like image_URL, more_info, etc are unique through all the views. Example, if a user edit the "more_info" field in the "team" view, then the view "authorities" will show the new value (if it is allowed to do it).
Except the two Geo tags corporate_location and physical_location which have the same behavior as the transaction metadata.