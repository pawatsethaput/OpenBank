# Version: 1.1

# Status: Stable

# Index

* [About](#about)
* [Implementation hints](#implementation-hints)
* Banks
    * [All](#banks)
    * [One](#bank)
    * [Offices](#offices)
* Accounts
    * [All](#accounts)
    * [One](#account)
* Transactions
    * [All](#transactions)
    * [One](#transaction)
    * Meta data
        * [Narrative](#narrative)
        * [Comments](#comments)
        * [Tags](#tags)
        * [Images](#images)
        * [Where](#where)
    * [Other account](#other_account)
        * [Meta data](#other_account-metadata)
            * [More info](#more_info)
            * [URL](#URL)
            * [Image URL](#image_url)
            * [Open Corporates URL](#opencorporates-URL)
            * [Corporate Location](#corporate_location)
            * [Physical Location](#physical_location)




* [The views](#views)



<span id="about"></span>
### About

The Open Bank Project API is a RESTful API for banks that supports multiple views on transaction data, data enrichment and data blurring.

The protocol consists of *baseline* and *optional* end points.
For baseline compliance, all the baseline URLs must be implemented.
For full compliance, all the baseline and optional end points must be implemented.

 * All calls should be prefixed with /obp/v1.1

 * The OBP API returns JSON as specified [here](http://www.json.org/) and validated [here](http://jsonlint.com/).

 * Parameters within the URL are written in CAPITAL_LETTERS.

 * Some calls require OAuth headers ([See here for more details](https://github.com/OpenBankProject/OBP-API/wiki/OAuth-1.0-Server)) to access to protected resources.

<span id="implementation-hints"></span>
### Implementation hints and notes.

The implementation of V1.1 is work in progress

The API may be optionally navigated using the links returned for each end point / object e.g.:

    {
        "links": [
            {
                "link": {
                    "rel": "banks",
                    "href": "/banks",
                    "method": "GET",
                    "title": "Returns a list of banks supported on this server"
                }
            }
        ]
    }

See [this discussion about how links may work](https://github.com/OpenBankProject/OBP-API/wiki/How-links-should-work)



<span id="root"></span>
**GET /**

*Baseline*

Returns information about :

* API version
* Hosted by information


JSON:

    {
        "api": {
            "version": "1.1",
            "git_commit": "4ca0cdbe9e51e41d0105bf26cf463ac5225a50a1",
            "hosted_by": {
                "organisation": "TESOBE",
                "email": "contact@tesobe.com",
                "phone": "+49 (0)30 8145 3994"
            }
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
                "bank": {
                    "id": "hsbc",
                    "short_name": "HSBC",
                    "full_name": "The Hongkong and Shanghai Banking Corporation Limited",
                    "logo": "url of internet standard image",
                    "website": "www.postbank.de"
                }
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
        "bank": {
            "short_name": "Postbank",
            "full_name": "Deutsche Postbank AG (DE)",
            "logo": "url of internet standard image",
            "website": "www.postbank.de"
        }
    }

<span id="offices"></span>
#Offices

**GET /banks/BANK_ID/offices**

*Optional*

Information returned includes:

* The list of offices for the BANK_ID.


JSON:

    {
        "offices": [{
            "office" :{
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
        }]
    }

<span id="accounts"></span>
#Accounts

**GET /banks/BANK_ID/accounts**

*Baseline*

Returns accounts list held at BANK_ID that the user has access to.
For each account the API return the ID and the available views.

If the user is not authenticated via OAuth, the list will contains only the accounts providing public views.

JSON:

    {
        "accounts": [
            {
                "account": {
                    "id": "A unique identifier used for ACCOUNT_ID",
                    "views_available": [
                        {
                            "view": {
                                "id": "A unique identifier used for VIEW_ID",
                                "short_name": "Public / Team / Auditors...",
                                "description": "e.g. this is the public view of the TESOBE account",
                                "is_public": "boolean. true if the public (user not logged in) can see this view."
                            }
                        }
                    ]
                }
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
        "account": {
            "number": "account number (moderated by the view)",
            "owners": [
                {
                    "user_id": "123213",
                    "user_provider": "bank name",
                    "display_name": "Name of person/Company/..."
                }
            ],
            "type": "e.g. current, savings",
            "balance": {
                "currency": "currency in ISO_4217 one of EUR/GBP,USD/...",
                "amount": "number or +/-"
            },
            "IBAN": "International Bank Account Number e.g. 123080FZAFA9124AZE",
            "views_available": [
                {
                    "view": {
                        "id": "A unique identifier used for VIEW_ID",
                        "short_name": "Public / Team / Auditors...",
                        "description": "e.g. this is the public view of the TESOBE account",
                        "is_public": "boolean. true if the public can see this view."
                    }
                }
            ]
        }
    }

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
                "transaction": {
                    "uuid": "A universally unique id e.g. 4f5745f4e4b095974c0eeead",
                    "id": "The bank's id for the transaction",
                    "this_account": {
                        "holder": [
                             {
                                "name": "MUSIC PICTURES LIMITED",
                                "is_alias": "true/false"
                             }
                        ],
                        "number": "",
                        "kind": "",
                        "bank": {
                            "IBAN": "",
                            "national_identifier": "",
                            "name": ""
                        }
                    },
                    "other_account": {
                        "holder": {
                            "name": "DEUTSCHE POST AG, SSC ACC S",
                            "is_alias": "true/false"
                        },
                        "number": "",
                        "kind": "",
                        "bank": {
                            "IBAN": "",
                            "national_identifier": "",
                            "name": ""
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
        "transaction": {
            "uuid": "A universally unique id e.g. 4f5745f4e4b095974c0eeead",
            "id": "The bank's id for the transaction",
            "this_account": {
                "holder": [
                    {
                        "name": "MUSIC PICTURES LIMITED",
                        "is_alias": "true/false"
                    }
                ],
                "number": "",
                "kind": "",
                "bank": {
                    "IBAN": "",
                    "national_identifier": "",
                    "name": ""
                }
            },
            "other_account": {
                "holder": {
                    "name": "DEUTSCHE POST AG, SSC ACC S",
                    "is_alias": "true/false"
                },
                "number": "",
                "kind": "",
                "bank": {
                    "IBAN": "",
                    "national_identifier": "",
                    "name": ""
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
                "comment": {
                    "id": "id of the comment",
                    "date": "date of posting the comment",
                    "value": "the comment",
                    "user": {
                        "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                        "id": "provider id of the user making the comment",
                        "display_name": "display name of user"
                    },
                    "reply_to": "if this is a reply, the id of the original comment"
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
        "value" : "the comment",
        "posted_date" : "2012-03-07T00:00:00.001Z"
    }


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
                "tag": {
                    "id": "id of the tag",
                    "value": "thetag",
                    "date": "date of posting the tag",
                    "user": {
                        "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                        "id": "provider id of the user making the tag",
                        "display_name": "display name of user"
                    }
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
        "value": "a_tag",
        "posted_date": "2012-03-07T00:00:00.001Z"
    }

**Note**: the value on the tag MUST NOT contain a white space.

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
                "image": {
                    "id": "1239qsxezad0123",
                    "label": "cool image",
                    "URL":"http://www.mysuperimage.com",
                    "date": "date of posting the tag",
                    "user": {
                        "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                        "id": "provider id of the user posting the image",
                        "display_name": "display name of user"
                    }
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

<span id="other_account"></span>
#Other account

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/other_account**

*Baseline*

Authentication via OAuth is required if the view is not public.

Returns account information, of the other party involved in the transaction, moderated by the [view](#views) (VIEW_ID).

JSON:

    {
        "id": "unique identifier",
        "number": "19123",
        "holder": {
            "name": "Bob",
            "is_alias": false
        },
        "national_identifier": "the national identifier of the account e.g. aze1239SQx",
        "IBAN":"the International Bank Account Number e.g. AZEDSQDA",
        "bank_name":"the bank name e.g. POSTBANK",
        "swift_bic":"the international identifier of the bank (ISO 9362) e.g. COBADEFFXXX"
    }

<span id="other_account-metadata"></span>
#Other account metadata

**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata**

*Optional*

Authentication via OAuth is required if the view is not public.

Returns account meta data of the other party involved in the transaction ([here](#transaction)) moderated by the [view](#views) (VIEW_ID).

JSON:

    {
        "more_info": "short text explaining who the other party of the transaction is",
        "URL": "a URL related to the other party e.g. the website of the company",
        "image_URL":"an image URL related to the other party e.g. company logo",
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service ",
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

<span id="more_info"></span>
### More info

**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/more_info**

*Optional*

Authentication via OAuth is required if the view is not public.

Save data in the "more_info" field of other account meta data (the other party involved in the transaction [here](#transaction))


JSON:

    {
        "more_info": "short text explaining who the other party of the transaction is"
    }

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/more_info**

*Optional*

Authentication via OAuth is required if the view is not public.

update data in the "more_info" field of other account meta data (the other party involved in the transaction [here](#transaction))


JSON:

    {
        "more_info": "short text explaining who the other party of the transaction is"
    }

<span id="URL"></span>
### URL

**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/url**

*Optional*

Authentication via OAuth is required if the view is not public.

Save data in the "URL" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "URL": "a URL related to the other party e.g. the website of the company"
    }

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/url/**

*Optional*

Authentication via OAuth is required if the view is not public.

update data in the "URL" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "URL": "a URL related to the other party e.g. the website of the company"
    }

<span id="image_url"></span>
### Image URL
**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/image_url**

*Optional*

Authentication via OAuth is required if the view is not public.

Save data in the "image_url" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "image_URL":"an image URL related to the other party e.g. company logo"
    }

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/image_url**

*Optional*

Authentication via OAuth is required if the view is not public.

update data in the "image_url" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "image_URL":"an image URL related to the other party e.g. company logo"
    }

<span id="opencorporates-URL"></span>
### Open corporates URL

**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/open_corporates_url**

*Optional*

Authentication via OAuth is required if the view is not public.

Save data in the "opencorporate-URL" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service "
    }

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/open_corporates_url**

*Optional*

Authentication via OAuth is required if the view is not public.

update data in the "opencorporate-URL" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service "
    }

<span id="corporate_location"></span>
### Corporate location


**POST /banks/BANK_ID/accounts/ACCOUNT_ID/public/other_accounts/OTHER_ACCOUNT_ID/metadata/corporate_location**

*Optional*

Authentication via OAuth is required.

Save data in the "corporate_location" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "corporate_location": {
                "latitude": 37.423021,
                "longitude": -122.083739
        }
    }

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/corporate_location**

*Optional*

Authentication via OAuth is required.

update data in the "corporate_location" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "corporate_location": {
                "latitude": 37.423021,
                "longitude": -122.083739
        }
    }

<span id="physical_location"></span>
### Physical location

**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/physical_location**

*Optional*

Authentication via OAuth is required.

Save data in the "physical_location" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "physical_location": {
                "latitude": 37.423021,
                "longitude": -122.083739
        }
    }

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/physical_location**

*Optional*

Authentication via OAuth is required.

update data in the "physical_location" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "physical_location": {
                "latitude": 37.423021,
                "longitude": -122.083739
        }
    }


<span id="views"></span>
### The views

Views on accounts and transactions filter the underlying data to hide or blur certain fields from certain users. For instance the balance on an account may be hidden from the public or replaced by + or -.

**data:** When a view moderates a set of data, some fields my contain the value "unauthorized" rather than the original value. This indicates that the user is not allowed to see the original data.

There are currently two exceptions to this rule:

1) The "holder" field in the JSON contains a value which is either an alias or the real name - indicated by the "is_alias" field.

2) The "balance" field (in a transaction or account details) may contain the real amount, a plus sign (+), a minus sign (-) or "unauthorized".

**action:** When a user performs an action like trying to post a comment (with POST API call), if he is not allowed, the repose will be an "unauthorized" http code 401.

**Metadata:**
The comments and tags added to a transaction in a view will appears ONLY on this view. e.g. comments posted to the public view only appear on the public view.

The other account metadata fields like image_URL, more_info etc are unique through all the views. Example, if a user edit the "more_info" field in the "team" view, then the view "authorities" will show the new value (if it is allowed to do it).


### Note on JSON formatting in this document:
* Use JSON.stringify({}, null, 4); or http://jsonlint.com to validate the JSON
* Ensure the JSON block is separated from other text by a line and it is indented by 4 spaces

e.g.

JSON:

    {}

Ends