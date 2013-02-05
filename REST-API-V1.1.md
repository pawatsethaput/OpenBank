# Version: 1.1

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
    * [One](#account)
* Transactions
    * [All](#transactions)
    * [One](#transaction)
    * Meta data 
        * [Narrative](#narrative)
        * [Comments](#comments)
        * [Tags](#tags)
    * [Other account](#other_account)
        * [meta data](#other_account-metadata)
            * [more info](#more_info)
            * [URL](#URL)
            * [image URL](#image_url)
            * [Open Corporates](#opencorporates-URL) 
* [The views](#views)



<span id="about"></span>
### About

The Open Bank Project API is a RESTful API for banks that supports multiple views on transaction data, data enrichment and data blurring.

The protocol consists of *baseline* and *optional* end points.
For baseline compliance, all the baseline URLs must be implemented.
For full compliance, all the baseline and optional end points must be implemented.

All calls should be prefixed with /obp/v1.1

The OBP API returns JSON as specified here: http://www.json.org/ and validated here: http://jsonlint.com/ 

Parameters within the URL are written in CAPITAL_LETTERS

Some calls require OAuth headers [See here for more details](https://github.com/OpenBankProject/OBP-API/wiki/OAuth-1.0-Server)  

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
### GET /

Baseline 

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

#Banks

<span id="banks"></span>

**GET /banks**

Baseline 

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
**GET /banks/BANK_ID**

Returns information about a single bank specified by BANK_ID including:

* Short Name, Full Name, etc.
 

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
**GET /banks/BANK_ID/offices**

Information returned includes:

* The list of offices for the BANK_ID


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

#Accounts

<span id="accounts"></span>
**GET /banks/BANK_ID/accounts**

Baseline 

Returns accounts held at BANK_ID that the user has access to.
If the user is not authenticated via OAuth, the list will contains only the accounts providing a public view.

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
**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/account**

Baseline

Information returned about an account specified by ACCOUNT_ID as moderated by the view (VIEW_ID) : 

* Number
* Owners
* Type
* Balance
* IBAN
* Views of the account that are available.

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
            "IBAN": "123080FZAFA9124AZE",
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

#Transactions

<span id="transactions"></span>
**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions**

Baseline

Authentication via OAuth is required if the view is not public.

With the following custom headers: 

* obp_sort_by=CRITERIA ==> default value: "completed" field
* obp_sort_direction=ASC/DESC ==> default value: DESC
* obp_limit=NUMBER ==> default value: 50
* obp_offset=NUMBER ==> default value: 0
* obp_from_date=DATE => default value: date of the oldest transaction registered
* obp_to_date=DATE => default value: date of the newest transaction registered

Returns transactions of the account specified by ACCOUNT_ID and [moderated](#views) by VIEW_ID. 

JSON:

     {
        "transactions": [
            {
                "transaction": {
                    "uuid": "A universally unique id e.g. 4f5745f4e4b095974c0eeead",
                    "id": "The bank's id for the transaction",
                    "this_account": {
                        "holder": {
                            "name": "MUSIC PICTURES LIMITED",
                            "is_alias": "true/false"
                        },
                        "number": "",
                        "type": "",
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
                        "type": "",
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
**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/transaction**

Authentication via OAuth is required if the view is not public.

Returns information [moderated](#views) by the view about a specific transaction.

JSON:


    {
        "transaction": {
            "uuid": "A universally unique id e.g. 4f5745f4e4b095974c0eeead",
            "id": "The bank's id for the transaction",
            "this_account": {
                "holder": {
                    "name": "MUSIC PICTURES LIMITED",
                    "is_alias": "true/false"
                },
                "number": "",
                "type": "",
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
                "type": "",
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

Authentication via OAuth is required if the view is not public. 

Returns the account owner description, [moderated](#views) by the view, of the transaction.


JSON:

    {
        "narrative" : "text explaining the purpose of the transaction"
    }

**POST /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/transactions/TRANSACTION_ID/metadata/narrative**

Authentication via OAuth is required.

Saves the account owner description of the transaction if the [view](#views) allow it.

JSON:

    {
        "narrative" : "text explaining the purpose of the transaction"
    }

**PUT /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/transactions/TRANSACTION_ID/metadata/narrative**

Authentication via OAuth is required. 

Updates the account owner description of the transaction if the [view](#views) allow it.


JSON:

    {
        "narrative" : "text explaining the purpose of the transaction"
    }


<span id="comments"></span>
**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/comments**

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

Post a comment about a transaction on a specific [view](#views).

OAuth authentication is required since the comment are linked with the user.

JSON:
 
    {
        "value" : "the comment",
        "posted_date" : "2012-03-07T00:00:00.001Z"
    }

<span id="tags"></span>
**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/tags**

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
                        "id": "provider id of the user making the comment",
                        "display_name": "display name of user"
                    }
                }
            }
        ]
    }

**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/tags**
OAuth Header is required since the tag is linked with the user.

Post a tag on a transaction in a [view](#views).

JSON:

    {
        "value": "a_tag",
        "posted_date": "2012-03-07T00:00:00.001Z"
    }

**Note**: the value on the tag MUST NOT contain a white space.

#Other account

<span id="other_account"></span>
**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/other_account**

Authentication via OAuth is required if the view is not public.

Returns account information, of the other party involved in the transaction, moderated by the [view](#views) (VIEW_ID).

JSON:

    {
        "id": "unique identifier",
        "number": "19123",
        "holder": "Bob",
        "national_identifier": "the national identifier of the account e.g. aze1239SQx",
        "IBAN":"the international identifier of the account e.g. AZEDSQDA",
        "bank_name":"the bank name e.g. POSTBANK",
        "swift_bic":"the international identifier of the bank e.g. 12321SDXSAZEAZD1"

    }

<span id="other_account-metadata"></span>
**GET /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata**


Authentication via OAuth is required if the view is not public.

Returns account meta data of the other party involved in the transaction ([here](#transaction)) moderated by the [view](#views) (VIEW_ID).

JSON:

    {
        "more_info": "short text explaining who the other party of the transaction is",
        "URL": "a URL related to the other party e.g. the website of the company",
        "image_URL":"an image URL related to the other party e.g. company logo",
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service "
    }    

<span id="more_info"></span>
**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/more_info**

Authentication via OAuth is required.

Save data in the "more_info" field of other account meta data (the other party involved in the transaction [here](#transaction))


JSON:

    {
        "more_info": "short text explaining who the other party of the transaction is"
    }   

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/more_info**

Authentication via OAuth is required.

update data in the "more_info" field of other account meta data (the other party involved in the transaction [here](#transaction))


JSON:

    {
        "more_info": "short text explaining who the other party of the transaction is"
    }       

<span id="URL"></span>
**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/url**

Authentication via OAuth is required.

Save data in the "URL" field of other account meta data (the other party involved in the transaction [here](#transaction))


JSON:

    {
        "URL": "a URL related to the other party e.g. the website of the company"
    }   

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/url/**

Authentication via OAuth is required.

update data in the "URL" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "URL": "a URL related to the other party e.g. the website of the company"
    }        

<span id="image_url"></span>
**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/image_url**

Authentication via OAuth is required.

Save data in the "image_url" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "image_URL":"an image URL related to the other party e.g. company logo"
    }   

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/image_url**

Authentication via OAuth is required.

update data in the "image_url" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "image_URL":"an image URL related to the other party e.g. company logo"
    } 

<span id="opencorporates-URL"></span>
**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/open_corporates_url**

Authentication via OAuth is required.

Save data in the "opencorporate-URL" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service "
    }   

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/open_corporates_url**

Authentication via OAuth is required.

update data in the "opencorporate-URL" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service "
    } 

<span id="views"></span>
### The Views
Views on accounts and transactions filter the underlying data to hide or blur certain fields from certain users. For instance the balance on an account may be hidden from the public or replaced by + or -.

**data** : 
When a view moderates a set of data, some fields my contain the value "unauthorized" rather than the original value. This indicates that the user is not allowed to see the original data.

There are currently two exceptions to this rule: 

1) The "holder" field in the JSon contains a value which is either an alias or the real name - indicated by the "is_alias" field.

2) The "balance" field (in a transaction or account details) may contain the real amount, a plus sign (+), a minus sign (-) or "unauthorized".
 
**action:** 
When a user performs an action like trying to post a comment (with POST API call), if he is not allowed, the repose will be an "unauthorized" http code 401.


**comments and tags:**
The comments and tags added to a transaction in a view will appears ONLY on this view. e.g. comments posted to the public view only appear on the public view.

### Note on JSON formatting in this document: 
* Use JSON.stringify({}, null, 4); or http://jsonlint.com to validate the JSON
* Ensure the JSON block is separated from other text by a line and it is indented by 4 spaces

e.g.

JSON:

    {}

Ends 