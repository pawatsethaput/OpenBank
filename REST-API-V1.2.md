# Version: 1.2

# Status: DRAFT

# Index

* [About](#about)
* [Implementation hints](#implementation-hints)
* [Root](#root)
* Banks
    * [All](#banks)
    * [One](#bank)
* Accounts
    * [All](#accounts)
    * [Public](#accounts-public)
    * [Private](#accounts-private)
    * [One](#account)
    * [Views](#views)
    * Permissions
        * [All](#permissions)
        * [One](#permission)
        * [Grant](#grant-permission)
        * [Revoke](#revoke-permission)
    * Other Accounts
        * [All](#account-other-accounts)
        * [One](#account-other-account)
            * [Meta data](#other_account-metadata)
                * [Holder](#holder)
                * [Public Alias](#public-alias)
                * [Private Alias](#private-alias)
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
* [How views works ?](#how-views)


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
#Root
*Baseline*

Returns information about:

* API version
* Hosted by information

**Request:**  
Verb: GET  
URL: /

**Response:**  
HTTP code: 200  
Body:

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
*Baseline*

Returns a list of banks supported on this server:

* ID used as parameter in URLs
* Short and full name of bank
* Logo URL
* Website

**Request:**  
Verb: GET  
URL: /banks

**Response:**  
HTTP code: 200  
Body:

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
*Optional*

Returns information about a single bank specified by BANK_ID including:

* Short and full name of bank
* Logo URL
* Website

**Request:**  
Verb: GET  
URL: /banks/BANK_ID

**Response:**  
HTTP code: 200  
Body:

    {
        "short_name": "Postbank",
        "full_name": "Deutsche Postbank AG (DE)",
        "logo": "url of internet standard image",
        "website": "www.postbank.de"
    }

<span id="accounts"></span>
#Accounts
*Baseline*

Returns the list of accounts at BANK_ID that the user has access to.  
For each account the API returns the account ID and the available views.

If the user is not authenticated via OAuth, the list will contains only the accounts providing public views.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts

**Response:**  
HTTP code: 200  
Body:

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
*Optional*

Returns a list of the public accounts at BANK_ID. For each account the API returns the ID and the available views.

Authentication via OAuth is not required.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/public

**Response:**  
HTTP code: 200  
Body:

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
*Optional*

Returns the list of accounts private (non-public) at BANK_ID that the user has access to.  
For each account the API returns the ID and the available views.

Authentication via OAuth is required.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/private

**Response:**  
HTTP code: 200  
Body:

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

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/account

**Response:**  
HTTP code: 200  
Body:

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

<span id="views"></span>
#Views
*Optional*

Returns the list of the views created for account ACCOUNT_ID at BANK_ID.

OAuth authentication is required and the user needs to have access to the owner view.

**Request:**
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/views  

**Response:**  
HTTP code: 200  
Body:

    {
        "views_available": [
                {
                    "id": "A unique identifier used for VIEW_ID",
                    "short_name": "Public / Team / Auditors...",
                    "description": "e.g. this is the public view of the TESOBE account",
                    "is_public": "boolean. true if the public can see this view."
                }
            ]
    }

<span id="permissions"></span>
#Permissions

*Optional*

Returns the list of the permissions at BANK_ID for account ACCOUNT_ID, with each time a pair composed of the user and the views that he has access to.

OAuth authentication is required and the user needs to have access to the owner view.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/users

**Response:**  
HTTP code: 200  
Body:

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

<span id="permission"></span>
#Permission
*Optional*

Returns the list of the views at BANK_ID for account ACCOUNT_ID that a USER_ID has access to.

OAuth authentication is required and the user needs to have access to the owner view.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/users/USER_ID

**Response:**  
HTTP code: 200  
Body:

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

<span id="grant-permission"></span>
#Grant Permission
#### One
*Optional*

Grants the user USER_ID access to the view VIEW_ID at BANK_ID for account ACCOUNT_ID.

OAuth authentication is required and the user needs to have access to the owner view.

Granting access to a public view will return an error message, as the user already has access.

**Request:**  
Verb: POST  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/users/USER_ID/views/VIEW_ID

**Response:**  
HTTP code: 201  
Body:

    {
        {
            "id": "A unique identifier used for the view",
            "short_name": "Team / Auditors...",
            "description": "e.g. this is the public view of the TESOBE account",
            "is_public": false
        }
    }

#### Several
*Optional*

Grants the user USER_ID access to a list of views at BANK_ID for account ACCOUNT_ID.

OAuth authentication is required and the user needs to have access to the owner view.

**Request:**  
Send a list of views Ids  
Verb: POST  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/users/USER_ID/views  
Body: 

    {
        "views" : [
            "view_one_id",
            "view_two_id",
            "view_theree_id"
        ]
    }
**Response:**  
Returns the list the of the views granted access  
HTTP code: 201  
Body:

    {
        "views" : [
            {
                "id": "view_one_id",
                "short_name": "Team / Auditors...",
                "description": "e.g. this is the public view of the TESOBE account",
                "is_public": false
            },
            {
                "id": "view_two_id",
                "short_name": "Team / Auditors...",
                "description": "e.g. this is the public view of the TESOBE account",
                "is_public": false
            },
            {
                "id": "view_theree_id",
                "short_name": "Team / Auditors...",
                "description": "e.g. this is the public view of the TESOBE account",
                "is_public": false
            }
        ]
    }

<span id="revoke-permission"></span>
#Revoke Permission
#### one view
*Optional*

Revokes the user USER_ID access to the view VIEW_ID at BANK_ID for account ACCOUNT_ID.

Revoking a user access to a public view will return an error message.

OAuth authentication is required and the user needs to have access to the owner view.

**Request:**  
Verb: DELETE  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/users/USER_ID/views/VIEW_ID

**Response:**  
HTTP code: 204  
Body: No content

#### all views
*Optional*

Revokes the user USER_ID access to all the views at BANK_ID for account ACCOUNT_ID.

OAuth authentication is required and the user needs to have access to the owner view.

**Request:**
Verb: DELETE  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/users/USER_ID/views  

**Response:**
HTTP code: 204

<span id="account-other-accounts"></span>
#Other Accounts
*Optional*

Returns data about all the other bank accounts that have shared at least one transaction with the ACCOUNT_ID at BANK_ID.

OAuth authentication is required if the view VIEW_ID is not public.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts

**Response:**  
HTTP code: 200  
Body:

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
*Optional*

Returns data about one other bank account (OTHER_ACCOUNT_ID) that had shared at least one transaction with ACCOUNT_ID at BANK_ID.

OAuth authentication is required if the view is not public.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID

**Response:**  
HTTP code: 200  
Body:

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
###Other account metadata
*Optional*

Returns only the metadata about one other bank account (OTHER_ACCOUNT_ID) that had shared at least one transaction with ACCOUNT_ID at BANK_ID.

Authentication via OAuth is required if the view is not public.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata

**Response:**  
HTTP code: 200  
Body:

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

<span id="holder"></span>
### Holder
*Optional*

Returns the holder name of the other bank account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/holder

**Response:**  
HTTP code: 200  
Body:

    {
        "holder": {
            "name": "Joe Bloggs",
            "is_alias": true
        }
    }

<span id="public-alias"></span>
### Public Alias
####Get the Public Alias
*Optional*

Returns the public alias of the other account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/public_alias

**Response:**  
HTTP code: 200  
Body:

    {
        "alias" : "An alias to display instead of the real name"
    }

####Create a Public Alias
*Optional*

Creates the public alias for the other account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

Note: Public aliases are automatically generated for new "other accounts", so this call should only be used if
the public alias was deleted.

The VIEW_ID parameter should be a view the caller is permitted to access to and that has permission to create public aliases.

**Request:**  
Verb: POST  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/public_alias  
Body:

    {
        "alias" : "An alias to display instead of the real name"
    }

**Response:**  
HTTP code: 201  
Body:

    {
        "success" : "public alias added"
    }

####Update the Public Alias

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/public_alias**

*Optional*

Updates the public alias of the other account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

**Request:**  
Verb: PUT  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/public_alias  
Body:

    {
        "alias" : "An alias to display instead of the real name"
    }

**Response:**  
HTTP code: 200
Body:

    {
        "success" : "public alias updated"
    }

####Delete the Public Alias
*Optional*

Deletes the public alias of the other account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

**Request:**  
Verb: DELETE  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/public_alias  

**Response:**  
HTTP code: 204  
Body: No Content

<span id="private-alias"></span>
### Private Alias
####Get the Private Alias
*Optional*

Returns the private alias of the other account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/private_alias

**Response:**  
HTTP code: 200
Body:

    {
        "alias" : "An alias to display instead of the real name"
    }

####Create the Private Alias
**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/private_alias**
*Optional*

Creates an private alias for the other account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

**Request:**  
Verb: POST  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/private_alias  
Body: 

    {
        "alias" : "An alias to display instead of the real name"
    }

**Response:**  
HTTP code: 201  
Body:

    {
        "success" : "private alias added"
    }

####Update the Private Alias
*Optional*

Updates the private alias of the other account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

**Request:**  
Verb: PUT  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/private_alias  
Body: 

    {
        "alias" : "An alias to display instead of the real name"
    }

**Response:**  
HTTP code: 200  
Body:

    {
        "success" : "private alias added"
    }


####Delete the Private Alias
*Optional*

Deletes the private alias of the other account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

**Request:**  
Verb: DELETE  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/private_alias  

**Response:**  
HTTP code: 204  
Body: No Content

<span id="more_info"></span>
### More info
####Create
*Optional*

Saves data in the "more_info" field of the other account (OTHER_ACCOUNT_ID) metadata (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**  
Verb: POST  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/more_info  
Body: 

    {
        "more_info": "short text explaining who is the other party of the transaction is"
    }
**Response:**  
HTTP code: 201  
Body: 

    {
        "success": "more info added"
    }

####Update
*Optional*

Updates the data in the "more_info" field of the other account (OTHER_ACCOUNT_ID) metadata (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**  
Verb: PUT  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/more_info  
Body: 

    {
        "more_info": "short text explaining who is the other party of the transaction is"
    }
**Response:**  
HTTP code: 200  
Body: 

    {
        "success": "more info updated"
    }

####Delete
*Optional*

Deletes the data in the "more_info" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**  
Verb: DELETE  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/more_info  

**Response:**  
HTTP code: 204  
Body: No Content

<span id="URL"></span>
### URL
####Create
*Optional*

Authentication via OAuth is required.

Saves data in the "URL" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

**Request:**  
Verb: POST  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/url  
Body:  

    {
        "URL": "a URL related to the other party e.g. the website of the company"
    }
**Response:**  
HTTP code: 201  
Body:  

    {
        "success": "url added"
    }

####Update
*Optional*

Update data in the "URL" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**  
Verb: PUT  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/url  
Body:  

    {
        "URL": "a URL related to the other party e.g. the website of the company"
    }
**Response:**  
HTTP code: 200  
Body:  

    {
        "success": "url updated"
    }

####Delete
*Optional*

Deletes the data in the "URL" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**  
Verb: DELETE  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/url  

**Response:**  
HTTP code: 204  
Body: No Content

<span id="image_url"></span>
### Image URL
####Create
*Optional*

Authentication via OAuth is required.

Saves data in the "image_url" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

**Request:**  
Verb: POST  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/image_url  
Body:  

    {
        "image_URL":"an image URL related to the other party e.g. company logo"
    }
**Response:**  
HTTP code: 201  
Body:  

    {
        "success": "image url added"
    }

####Update
*Optional*

Update data in the "image_url" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**  
Verb: PUT  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/image_url  
Body:  

    {
        "image_URL":"an image URL related to the other party e.g. company logo"
    }
**Response:**  
HTTP code: 200  
Body:  

    {
        "success": "image url updated"
    }

####Delete
*Optional*

Deletes the data in the "image_url" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**  
Verb: DELETE  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/image_url  

**Response:**  
HTTP code: 204  
Body: No Content

<span id="opencorporates-URL"></span>
### Open corporates URL
####Create
*Optional*

Authentication via OAuth is required.

Saves data in the "opencorporate-URL" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

**Request:**  
Verb: POST  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/open_corporates_url  
Body:  

    {
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service "
    }
**Response:**  
HTTP code: 201  
Body:  

    {
        "success": "open corporate url added"
    }

####Update
*Optional*

Update data in the "opencorporate-URL" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**  
Verb: PUT  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/open_corporates_url  
Body:  

    {
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service "
    }
**Response:**  
HTTP code: 200  
Body:  

    {
        "success": "open corporate url updated"
    }

####Delete
*Optional*

Deletes the data in the "opencorporate-URL" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**  
Verb: DELETE  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/open_corporates_url  

**Response:**  
HTTP code: 204  
Body: No Content

<span id="corporate_location"></span>
### Corporate location
####Create
*Optional*

Authentication via OAuth is required.

Saves data in the "corporate_location" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

**Request:**  
Verb: POST  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/public/other_accounts/OTHER_ACCOUNT_ID/metadata/corporate_location  
Body:  

    {
        "corporate_location": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }
**Response:**  
HTTP code: 201  
Body:  

    {
        "success": "corporate location added"
    }

####Update
*Optional*

Update data in the "corporate_location" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**  
Verb: PUT  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/corporate_location  
Body:  

    {
        "corporate_location": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }
**Response:**  
HTTP code: 200  
Body:  

    {
        "success": "corporate location updated"
    }

####Delete
*Optional*

Deletes the data in the "corporate_location" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**  
Verb: DELETE  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/corporate_location  

**Response:**  
HTTP code: 204  
Body: No Content

<span id="physical_location"></span>
### Physical location
####Create
*Optional*

Authentication via OAuth is required.

Saves data in the "physical_location" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

**Request:**  
Verb: POST  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/physical_location  
Body:  

    {
        "physical_location": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }
**Response:**  
HTTP code: 201  
Body:  

    {
        "success": "physical location added"
    }

####Update
*Optional*

Update data in the "physical_location" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**  
Verb: PUT  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/physical_location  
Body:  

    {
        "physical_location": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }
**Response:**  
HTTP code: 200  
Body:  

    {
        "success": "physical location updated"
    }

####Delete
*Optional*

Deletes the data in the "physical_location" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**  
Verb: DELETE  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/physical_location  

**Response:**  
HTTP code: 204  
Body: No Content

<span id="transactions"></span>
#Transactions
*Baseline*

Returns transactions list of the account specified by ACCOUNT_ID and [moderated](#views) by the view (VIEW_ID).

Authentication via OAuth is required if the view is not public.

Possible custom headers for pagination:

* obp_sort_by=CRITERIA ==> default value: "completed" field
* obp_sort_direction=ASC/DESC ==> default value: DESC
* obp_limit=NUMBER ==> default value: 50
* obp_offset=NUMBER ==> default value: 0
* obp_from_date=DATE => default value: date of the oldest transaction registered
* obp_to_date=DATE => default value: date of the newest transaction registered

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions  

**Response:**  
HTTP code: 200  
Body:

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
*Optional*

Returns one transaction specified by TRANSACTION_ID of the account ACCOUNT_ID and [moderated](#views) by the view (VIEW_ID).

Authentication via OAuth is required if the view is not public.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/transaction  

**Response:**  
HTTP code: 200  
Body:

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
#### Get
*Optional*

Returns the account owner description of the transaction [moderated](#views) by the view.

Authentication via OAuth is required if the view is not public.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/narrative  

**Response:**  
HTTP code: 200  
Body:

    {
        "narrative" : "text explaining the purpose of the transaction"
    }

#### Create
*Optional*

Creates a description of the transaction TRANSACTION_ID.

Authentication via OAuth is required if the view is not public.

**Request:**  
Verb: POST  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/narrative  
Body:

    {
        "narrative" : "text explaining the purpose of the transaction"
    }

**Response:**  
HTTP code: 201  
Body:

    {
        "success" : "narrative added"
    }

#### Update
*Optional*

Updates the description of the transaction TRANSACTION_ID.

Authentication via OAuth is required if the view is not public.

**Request:**  
Verb: PUT  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/narrative  
Body:

    {
        "narrative" : "text explaining the purpose of the transaction"
    }

**Response:**  
HTTP code: 200  
Body:

    {
        "success" : "narrative updated"
    }

####Delete
*Optional*

Deletes the description of the transaction TRANSACTION_ID.

Authentication via OAuth is required if the view is not public.

**Request:**  
Verb: DELETE  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/narrative  

**Response:**  
HTTP code: 204  
Body: No Content

<span id="comments"></span>
### Comments
#### Get
*Optional*

Returns the transaction TRANSACTION_ID comments made on a [view](#views) (VIEW_ID).

Authentication via OAuth is required if the view is not public.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/comments  

**Response:**  
HTTP code: 200  
Body:

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

#### Create
*Optional*

Posts a comment about a transaction TRANSACTION_ID on a [view](#views) VIEW_ID.

OAuth authentication is required since the comment is linked with the user.

**Request:**  
Verb: POST  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/comments  
Body:

    {
        "value" : "the comment"
    }
**Response:**  
HTTP code: 201  
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

####Delete
*Optional*

Delete the comment COMMENT_ID about the transaction TRANSACTION_ID made on [view](#views).

Authentication via OAuth is required. The user must either have owner privileges for this account, or must be the user that posted the comment.

**Request:**  
Verb: DELETE  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/comments/COMMENT_ID  

**Response:**  
HTTP code: 204  
Body: No Content

<span id="tags"></span>
### Tags
#### Get
*Optional*

Returns the transaction TRANSACTION_ID tags made on a [view](#views) (VIEW_ID).

Authentication via OAuth is required if the view is not public.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/tags  

**Response:**  
HTTP code: 200  
Body:

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

**Note**: user.provider + user.id is unique

#### Create
*Optional*

Posts a tag about a transaction TRANSACTION_ID on a [view](#views) VIEW_ID.

OAuth authentication is required since the tag is linked with the user.

**Request:**  
Verb: POST  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/tags  
Body:

    {
        "value": "a_tag"
    }
**Response:**  
HTTP code: 201  
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
####Delete
*Optional*

Deletes the tag TAG_ID about the transaction TRANSACTION_ID made on [view](#views).

Authentication via OAuth is required. The user must either have owner privileges for this account, or must be the user that posted the tag.

**Request:**  
Verb: DELETE  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/tags/TAG_ID  

**Response:**  
HTTP code: 204  
Body: No Content

<span id="images"></span>
### Images
#### Get
*Optional*

Returns the transaction TRANSACTION_ID images made on a [view](#views) (VIEW_ID).

Authentication via OAuth is required if the view is not public.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/images  

**Response:**  
HTTP code: 200  
Body:

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

**Note**: user.provider + user.id is unique

#### Create
*Optional*

Posts an image about a transaction TRANSACTION_ID on a [view](#views) VIEW_ID.

OAuth authentication is required since the image is linked with the user.

**Request:**  
Verb: POST  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/images  
Body:

    "image": {
        "label": "cool image",
        "URL":"http://www.mysuperimage.com"
    }
**Response:**  
HTTP code: 201  
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

####Delete
*Optional*

Deletes the image IMAGE_ID about the transaction TRANSACTION_ID made on [view](#views).

Authentication via OAuth is required. The user must either have owner privileges for this account, or must be the user that posted the image.

**Request:**  
Verb: DELETE  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/images/IMAGE_ID  

**Response:**  
HTTP code: 204  
Body: No Content

<span id="where"></span>
### Where
#### Get
*Optional*

Returns the "where" Geo tag added to the transaction TRANSACTION_ID made on a [view](#views) (VIEW_ID).  
It represents the location where the transaction has been initiated.

Authentication via OAuth is required if the view is not public.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/where  

**Response:**  
HTTP code: 200  
Body:

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

**Note**: user.provider + user.id is unique

#### Create
*Optional*

Creates a "where" Geo tag on a transaction TRANSACTION_ID in a [view](#views).

OAuth authentication is required since the geo tag is linked with the user.

**Request:**  
Verb: POST  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/where  
Body:

    {
        "where": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }
**Response:**  
HTTP code: 201  
Body:

    {
        "success": "where tag added"
    }

#### Update
*Optional*

Updates the "where" Geo tag on a transaction TRANSACTION_ID in a [view](#views).

OAuth authentication is required since the geo tag is linked with the user.

**Request:**  
Verb: PUT  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/where  
Body:

    {
        "where": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }
**Response:**  
HTTP code: 201  
Body:

    {
        "success": "where tag updated"
    }

####Delete
*Optional*

Deletes the where tag of the transaction TRANSACTION_ID made on [view](#views).

Authentication via OAuth is required. The user must either have owner privileges for this account, or must be the user that posted the geo tag.

**Request:**  
Verb: DELETE  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/where  

**Response:**  
HTTP code: 204  
Body: No Content

<span id="transaction_other_account"></span>
#Other account
*Baseline*

Returns details of the other party involved in the transaction, moderated by the [view](#views) (VIEW_ID).

Authentication via OAuth is required if the view is not public.

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/other_account  

**Response:**  
HTTP code: 200  
Body:

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

<span id="how-views"></span>
### How views works ?

Views on accounts and transactions filter the underlying data to hide or blur certain fields from certain users. For instance the balance on an account may be hidden from the public or replaced by + or -.

**data:** When a view moderates a set of data, some fields my contain the value null rather than the original value. This indicates either that the user is not allowed to see the original data or the field is empty.

There are currently two exceptions to this rule:

1) The "holder" field in the JSON contains a value which is either an alias or the real name - indicated by the "is_alias" field.

2) The "balance" field (in a transaction or account details) may contain the real amount, a plus sign (+) or a minus sign (-).

**action:** When a user performs an action like trying to post a comment (with POST API call), if he is not allowed, the repose will be an "unauthorized" HTTP code 401.

**Metadata:**
Transaction metadata like (images, tags, comments, etc.) will appears ONLY on the view where they have been created e.g. comments posted to the public view only appear on the public view.

The other account metadata fields like image_URL, more_info, etc are unique through all the views. Example, if a user edit the "more_info" field in the "team" view, then the view "authorities" will show the new value (if it is allowed to do it).