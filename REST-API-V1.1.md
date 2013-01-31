# Index 

* [About](#about)
* [Implementation hints](#Implementation-hints)
* Bank
    * [All](#banks)
    * [One](#bank)
    * [Offices](#offices)
* Account
    * [All](#accounts)
    * [One](#account)
* Transaction
    * [All](#transactions)
    * [One](#transaction)
    * [Other account](#other-account)
    * [Other account meta data](#other-account-metadata)
        * [more info](#more-info)
        * [URL](#URL)
        * [image URL](#image-URL)
        * [Open Corporates](#opencorporates-URL)                        
    * Meta data 
        * [Owner comment](#ownercomment)
        * [Comments](#comments)
        * [Tags](#tags)

<span id="about"></span>
# DRAFT

A RESTful API for banks that supports multiple views on transaction data, comments and tags - and a flexible JSON data structure.

The protocol consists of *baseline* and *optional* end points.
For baseline compliance, all the baseline URLs must be implemented.
For full compliance, all the baseline and optional end points must be implemented.

The OBP API returns JSON as specified here: http://www.json.org/ and validated here: http://jsonlint.com/ 

Parameters within the URL are written in CAPITAL_LETTERS

The API may be optionally navigated using the links returned for each end point.
All calls should be prefixed with /obp/v1.1

> Implementation Note: V1.1 is not currently implemented

> See https://github.com/OpenBankProject/OBP-API/wiki/How-links-should-work for a note about links

<span id="Implementation-hints"></span>
### Implementation hints 

Each call can optionally contain a meta data object e.g.: 

    {
        "meta_data": {
            "count": 64,
            "total_count": 411,
            "offset": 200,
            "limit": 100
        }
    }

Each returned call or object can optionally have a list of links relevant to the resource e.g.:

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

<span id="root"></span>
### GET /

Baseline 

Returns information about
* API version
* Hosted by information
* Related links (optional) 

JSON:

    {
        "api": {
            "version": "1.0",
            "git_commit": "4ca0cdbe9e51e41d0105bf26cf463ac5225a50a1",
            "hosted_by": {
                "organisation": "TESOBE",
                "email": "contact@tesobe.com",
                "phone": "+49 (0)30 8145 3994"
            }
        }
    }

#Bank

<span id="banks"></span>
### GET /banks

Baseline 

Returns a list of banks supported on this server:

* ALIAS used as parameter in urls
* Long name of bank
* Related links (optional)

JSON:

    {
        "banks": [
            {
                "bank": {
                    "id": "hsbc",
                    "short_name": "HSBC",
                    "full_name": "The Hongkong and Shanghai Banking Corporation Limited",
                    "logo": "url of internet standard image"
                }
            }
        ]
    }

<span id="bank"></span>
### GET /banks/BANK_ID

Optional

Returns information about the bank specified by BANK_ID including: 
* Name, Web & Email address. 
* Related links (optional) 

JSON: 

    {
        "bank": {
            "id": "postbank",
            "short_name": "Postbank",
            "full_name": "Deutsche Postbank AG (DE)",
            "website": "www.postbank.de"
        }
    }

<span id="offices"></span>
### GET /banks/BANK_ID/offices

Optional

Information returned includes: 
* The list of offices for the BANK_ID
* Related links (optional)

JSON:

    {
        "offices": [
            {
                "address": "address",
                "type": "HQ/branch/call center",
                "BIC_SWIFT": "ISO_9362 BIC / SWIFT code",
                "fax": [
                    {
                        "dept": "customer service",
                        "number": "004401293801"
                    }
                ],
                "phone": [
                    {
                        "dept": "customer service",
                        "number": "004912312302"
                    }
                ]
            }
        ]
    }

#Account

<span id="accounts"></span>
### GET /banks/BANK_ID/accounts

Baseline 

Information returned includes: 
* If the user is authenticated via OAuth, he will get he list of accounts he has access to at the bank specified by * * BANK_ID. Otherwise the list will contains the public accounts.
* Related links (optional). The links include each VIEW_NAME available to the current user. 

JSON:

{
    "accounts": [
        {
            "account": {
                "number": "the account number e.g. 01203123",
                "alias": "public account alias e.g. TESOBE_current",
                "owner_description": "account owners description e.g. TESOBE current account",
                "views_available": [
                    {
                        "view": {
                            "name": "public / team / ...",
                            "description": "e.g. this is the public view of the account"
                        }
                    }
                ]
            }
        }
    ]
}

<span id="account"></span>
### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/account

Baseline

Information returned about an account specified by ACCOUNT_ALIAS.

* Number, owners, balance
* Views of the account that are available
* Related links (optional)

Authentication via OAuth is required if the account is not public.

JSON:

    {
        "account": {
            "number": "01203123",
            "alias": "public account alias e.g. TESOBE_current",
            "owners": [
                {
                    "user_id": "123213",
                    "user_provider": "bank name",
                    "name": "Name of person/Company/..."
                }
            ],
            "type": "banking account type e.g. savings",
            "balance": {
                "currency": "currency in ISO_4217 one of EUR/GBP,USD/...",
                "amount": "+ (depending on the view, this might show the balance or only +/-)"
            },
            "IBAN": "123080FZAFA9124AZE",
            "views_available": [
                {
                    "view": {
                        "name": "view 1 e.g. public",
                        "description": "this is the public view of the account"
                    }
                }
            ]
        }
    }

#Transaction

<span id="transactions"></span>
### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/VIEW_NAME

Baseline

Authentication via OAuth is required if the view is not public.

If VIEW_NAME is null it defaults to owner (the traditional account owners view of the account)

With the following custom headers: 
* obp_sort_by=CRITERIA
* obp_sort_direction=ASC/DESC
* obp_limit=NUMBER
* obp_offset=NUMBER
* obp_from_date=DATE
* obp_to_date=DATE

Returns transactions of the account specified by ACCOUNT_ALIAS and mediated by VIEW_NAME
Other available views might include public, team, shareholders and auditors.

Returns:
* Data specified by fields (default is to show all available fields)
* Links related to pagination / search (optional) 

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
### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/transaction/VIEW_NAME

Authentication via OAuth is required if the view is not public.

Returns information about a specific transaction:
* Information moderated by VIEW_NAME (defaults to owner)
* Related links (optional)

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

<span id="other-account"></span>
### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/other-account/details/VIEW_NAME

Authentication via OAuth is required if the view is not public.

Returns account information of the other party involved in the transaction:
* Information moderated by VIEW_NAME
* Related links (optional)

JSON:

    {
        "number": "19123",
        "holder": "Bob",
        "nationalIdentifier": "the national identifier of the account e.g. aze1239SQx",
        "IBAN":"the international identifier of the account e.g. AZEDSQDA",
        "bankName":"the bank name e.g. POSTBANK",
        "swift_bic":"the international identifier of the bank e.g. 12321SDXSAZEAZD1"

    }

<span id="other-account-metadata"></span>
### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/other-account/metadata/VIEW_NAME

Authentication via OAuth is required if the view is not public.

Returns account meta data of the other party involved in the transaction:
* Information moderated by VIEW_NAME
* Related links (optional)

JSON:

    {
        "moreInfo": "short text explaining who the other party of the transaction is",
        "URL": "a URL related to the other party e.g. the website of the company",
        "imageUrl":"an image URL related to the other party e.g. company logo",
        "openCorporatesUrl":"the company corporate URL in the http://opencorporates.com/ web service "
    }    

<span id="more-info"></span>
### POST /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/other-account/metadata/more-info/

Authentication via OAuth is required.

Save data in the "more-info" field of other account meta data (the other party involved in the transaction):
* Related links (optional)

JSON:

    {
        "moreInfo": "short text explaining who the other party of the transaction is"
    }   

### PUT /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/other-account/metadata/more-info/

Authentication via OAuth is required.

update data in the "more-info" field of other account meta data (the other party involved in the transaction):
* Related links (optional)

JSON:

    {
        "moreInfo": "short text explaining who the other party of the transaction is"
    }       

<span id="URL"></span>
### POST /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/other-account/metadata/url/

Authentication via OAuth is required.

Save data in the "URL" field of other account meta data (the other party involved in the transaction):
* Related links (optional)

JSON:

    {
        "URL": "a URL related to the other party e.g. the website of the company"
    }   

### PUT /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/other-account/metadata/url/

Authentication via OAuth is required.

update data in the "URL" field of other account meta data (the other party involved in the transaction):
* Related links (optional)

JSON:

    {
        "URL": "a URL related to the other party e.g. the website of the company"
    }        

<span id="image-URL"></span>
### POST /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/other-account/metadata/image-url/

Authentication via OAuth is required.

Save data in the "image-URL" field of other account meta data (the other party involved in the transaction):
* Related links (optional)

JSON:

    {
        "imageUrl":"an image URL related to the other party e.g. company logo"
    }   

### PUT /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/other-account/metadata/image-url/

Authentication via OAuth is required.

update data in the "image-URL" field of other account meta data (the other party involved in the transaction):
* Related links (optional)

JSON:

    {
        "imageUrl":"an image URL related to the other party e.g. company logo"
    } 

<span id="opencorporates-URL"></span>
### POST /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/other-account/metadata/opencorporates-url/

Authentication via OAuth is required.

Save data in the "opencorporate-URL" field of other account meta data (the other party involved in the transaction):
* Related links (optional)

JSON:

    {
        "openCorporatesUrl":"the company corporate URL in the http://opencorporates.com/ web service "
    }   

### PUT /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/other-account/metadata/opencorporates-url/

Authentication via OAuth is required.

update data in the "opencorporate-URL" field of other account meta data (the other party involved in the transaction):
* Related links (optional)

JSON:

    {
        "openCorporatesUrl":"the company corporate URL in the http://opencorporates.com/ web service "
    } 

#Transaction Metadata

<span id="ownercomment"></span>
### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/ownercomment/VIEW_NAME

Authentication via OAuth is required if the view is not public. 

Returns the account owner comment about the a specific transaction:
* the comment my be moderated (not visible) by the view
* Related links (optional)

JSON:

    {
        "ownerComment" : "text explaining the purpose of the transaction"
    }

<span id="comments"></span>
### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/comments/VIEW_NAME

VIEW_NAME defaults to owner

Authentication via OAuth is required if the view is not public. 

Returns comments about a specific transaction:
* All the comments are moderated by VIEW_NAME
* Related links (optional)

JSON:

    {
        "comments": [
            {
                "comment": {
                    "id": "id of the comment",
                    "date": "date of posting the comment",
                    "comment": "the comment",
                    "view": "view permalink the comment was made on",
                    "user": {
                        "provider": "name of party that authorised the user e.g. bankname/facebook/twitter",
                        "id": "provider id of the user making the comment",
                        "display_name": "display name of user"
                    },
                    "reply_to": "if this is a reply, the id of the original comment"
                }
            }
        ]
    }

### POST /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/comments/VIEW_NAME

Post a comment about a transaction on a specific view.

OAuth Header is required since the comment are linked with the user.

JSON:
 
    {
        "value" : "the comment",
        "datePosted" : "2012-03-07T00:00:00.001Z"
    }

> Note: user.provider + user.id is unique

<span id="tags"></span>
### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/tags/VIEW_NAME

Authentication via OAuth is required if the view is not public.

Returns tags about a specific transaction:
* All the tags are moderated by VIEW_NAME
* Related links (optional)

JSON:

    {
        "tags": [
            {
                "tag": {
                    "id": "id of the tag",
                    "value": "thetag",
                    "view": "view permalink the tag was made on",
                    "date": "date of posting the tag",
                    "user": {
                        "provider": "name of party that authorised the user e.g. bankname/facebook/twitter",
                        "id": "provider id of the user making the comment",
                        "display_name": "display name of user"
                    }
                }
            }
        ]
    }

### POST /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/tags/VIEW_NAME
OAuth Header is required since the tag is linked with the user.

JSON:

    {
        "value" : "atag",
        "datePosted" : "2012-03-07T00:00:00.001Z"
    }

> Note: the value on the tag MUST NOT contain a white space.


### Note on JSON formatting in this document: 
* Use JSON.stringify({}, null, 4); or http://jsonlint.com to validate the JSON
* Ensure the JSON block is separated from other text by a line and it is indented by 4 spaces

e.g.

JSON:

    {}

Ends 