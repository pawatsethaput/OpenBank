# Index 

* [About](#about)
* [Implementation hints](#Implementation-hints)
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
        * [Owner comment](#owner_comment)
        * [Comments](#comments)
        * [Tags](#tags)
    * [Other account](#other_account)
        * [meta data](#other_account-metadata)
            * [more info](#more-info)
            * [URL](#URL)
            * [image URL](#image_url)
            * [Open Corporates](#opencorporates-URL) 



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

**Implementation Note**: V1.1 is not currently implemented

**>** See [note about links](https://github.com/OpenBankProject/OBP-API/wiki/How-links-should-work)


**>** In the below API calls, when the mention "OAuth authentication required" appears that means that OAuth header is required will all the parameters. More details [here](https://github.com/OpenBankProject/OBP-API/wiki/OAuth-1.0-Server)  


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

Returns information about :

* API version
* Hosted by information
 

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

#Banks

<span id="banks"></span>
### GET /banks

Baseline 

Returns a list of banks supported on this server:

* ALIAS used as parameter in URLs
* Short and long name of bank
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
### GET /banks/BANK_ID

Returns information about the bank specified by BANK_ID including:

* Name, Web, full_name, etc.
 

JSON: 

    {
        "bank": {
            "id": "postbank",
            "short_name": "Postbank",
            "full_name": "Deutsche Postbank AG (DE)",
            "logo": "url of internet standard image",            
            "website": "www.postbank.de"
        }
    }

<span id="offices"></span>
### GET /banks/BANK_ID/offices

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

#Accounts

<span id="accounts"></span>
### GET /banks/BANK_ID/accounts

Baseline 

Information returned includes the list of the accounts for a BANK_ID

* If the user is authenticated via OAuth, he will get the list of accounts he has access to at the bank specified by BANK_ID. Otherwise the list will contains only the public accounts.
* The same idea applies to the "views_available" field : if the user is authenticated he will get views that he has access to, otherwise just the public ones. 

JSON:

    {
        "accounts": [
            {
                "account": {
                    "alias": "account alias e.g. tesobe_main", 
                    "views_available": [
                        {
                            "view": {
                                "name": "Public / Team / ...",
                                "id":"some_permalink"
                                "description": "e.g. this is the public view of the account"
                            }
                        }
                    ]
                }
            }
        ]
    }

**Note**: "alias" field is a short surname to use instead of the account number (more friendly), it will be used in the API next URLs.


<span id="account"></span>
### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/account

Baseline

Information returned about an account specified by ACCOUNT_ALIAS as moderated by the view (VIEW_ID) : 

* Number
* Owners
* Type
* Balance
* IBAN
* Views of the account that are available.


Authentication via OAuth is required if the view is not public.

The "views_available" field will contains only the public views if the user is not Authenticated though OAuth.

JSON:

    {
        "account": {
            "number": "01203123",
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

#Transactions

<span id="transactions"></span>
### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/transactions

Baseline

Authentication via OAuth is required if the view is not public.

With the following custom headers: 

* obp_sort_by=CRITERIA ==> default value: "completed" field
* obp_sort_direction=ASC/DESC ==> default value: DESC
* obp_limit=NUMBER ==> default value: 50
* obp_offset=NUMBER ==> default value: 0
* obp_from_date=DATE => default value: date of the oldest transaction registered
* obp_to_date=DATE => default value: date of the newest transaction registered

Returns transactions of the account specified by ACCOUNT_ALIAS and mediated by VIEW_ID. 

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
### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/transactions/TRANSACTION_ID/transaction

Authentication via OAuth is required if the view is not public.

Returns information about a specific transaction.

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

#Transaction Metadata

<span id="owner_comment"></span>
### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/transactions/TRANSACTION_ID/metadata/owner_comment

Authentication via OAuth is required if the view is not public. 

Returns the account owner comment about the a specific transaction. It is moderated by the view.


JSON:

    {
        "owner_comment" : "text explaining the purpose of the transaction"
    }

### POST /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/transactions/TRANSACTION_ID/metadata/owner_comment

Authentication via OAuth is required.

Saves the account owner comment about the a specific transaction. 

JSON:

    {
        "owner_comment" : "text explaining the purpose of the transaction"
    }

### PUT /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/transactions/TRANSACTION_ID/metadata/owner_comment

Authentication via OAuth is required. 

Updates the account owner comment about the a specific transaction.


JSON:

    {
        "owner_comment" : "text explaining the purpose of the transaction"
    }


<span id="comments"></span>
### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/transactions/TRANSACTION_ID/metadata/comments

Authentication via OAuth is required if the view is not public. 

Returns comments about a specific transaction. They are moderated by VIEW_ID.


JSON:

    {
        "comments": [
            {
                "comment": {
                    "id": "id of the comment",
                    "date": "date of posting the comment",
                    "value": "the comment",
                    "user": {
                        "provider": "name of party that authorised the user e.g. bank_name/facebook/twitter",
                        "id": "provider id of the user making the comment",
                        "display_name": "display name of user"
                    },
                    "reply_to": "if this is a reply, the id of the original comment"
                }
            }
        ]
    }

**Note**: user.provider + user.id is unique

### POST /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/transactions/TRANSACTION_ID/metadata/comments

Post a comment about a transaction on a specific view.

OAuth Header is required since the comment are linked with the user.

JSON:
 
    {
        "value" : "the comment",
        "posted_date" : "2012-03-07T00:00:00.001Z"
    }

<span id="tags"></span>
### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/transactions/TRANSACTION_ID/metadata/tags

Authentication via OAuth is required if the view is not public.

Returns tags about a specific transaction. The are moderated by VIEW_ID.


JSON:

    {
        "tags": [
            {
                "tag": {
                    "id": "id of the tag",
                    "value": "thetag",
                    "date": "date of posting the tag",
                    "user": {
                        "provider": "name of party that authorised the user e.g. bank_name/facebook/twitter",
                        "id": "provider id of the user making the comment",
                        "display_name": "display name of user"
                    }
                }
            }
        ]
    }

### POST /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/transactions/TRANSACTION_ID/metadata/tags
OAuth Header is required since the tag is linked with the user.

Post a tag on a transaction.

JSON:

    {
        "value" : "atag",
        "posted_date" : "2012-03-07T00:00:00.001Z"
    }

**Note**: the value on the tag MUST NOT contain a white space.

#Other account

<span id="other_account"></span>
### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/transactions/TRANSACTION_ID/other_account

Authentication via OAuth is required if the view is not public.

Returns account information of the other party involved in the transaction moderated by VIEW_ID.

JSON:

    {
        "uuid": "unique identifier",
        "number": "19123",
        "holder": "Bob",
        "national_identifier": "the national identifier of the account e.g. aze1239SQx",
        "IBAN":"the international identifier of the account e.g. AZEDSQDA",
        "bank_name":"the bank name e.g. POSTBANK",
        "swift_bic":"the international identifier of the bank e.g. 12321SDXSAZEAZD1"

    }

<span id="other_account-metadata"></span>
### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/other_accounts/OTHER_ACCOUNT_UUID/metadata


Authentication via OAuth is required if the view is not public.

Returns account meta data of the other party involved in the transaction ([here](#transaction)) moderated by VIEW_ID.

JSON:

    {
        "more_info": "short text explaining who the other party of the transaction is",
        "URL": "a URL related to the other party e.g. the website of the company",
        "image_URL":"an image URL related to the other party e.g. company logo",
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service "
    }    

<span id="more-info"></span>
### POST /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/other_accounts/OTHER_ACCOUNT_UUID/metadata/more_info

Authentication via OAuth is required.

Save data in the "more-info" field of other account meta data (the other party involved in the transaction [here](#transaction))


JSON:

    {
        "more_info": "short text explaining who the other party of the transaction is"
    }   

### PUT /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/other_accounts/OTHER_ACCOUNT_UUID/metadata/more-info

Authentication via OAuth is required.

update data in the "more-info" field of other account meta data (the other party involved in the transaction [here](#transaction))


JSON:

    {
        "more_info": "short text explaining who the other party of the transaction is"
    }       

<span id="URL"></span>
### POST /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/other_accounts/OTHER_ACCOUNT_UUID/metadata/url

Authentication via OAuth is required.

Save data in the "URL" field of other account meta data (the other party involved in the transaction [here](#transaction))


JSON:

    {
        "URL": "a URL related to the other party e.g. the website of the company"
    }   

### PUT /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/other_accounts/OTHER_ACCOUNT_UUID/metadata/url/

Authentication via OAuth is required.

update data in the "URL" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "URL": "a URL related to the other party e.g. the website of the company"
    }        

<span id="image_url"></span>
### POST /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/other_accounts/OTHER_ACCOUNT_UUID/metadata/image_url

Authentication via OAuth is required.

Save data in the "image_url" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "image_URL":"an image URL related to the other party e.g. company logo"
    }   

### PUT /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/other_accounts/OTHER_ACCOUNT_UUID/metadata/image_url

Authentication via OAuth is required.

update data in the "image_url" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "image_URL":"an image URL related to the other party e.g. company logo"
    } 

<span id="opencorporates-URL"></span>
### POST /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/other_accounts/OTHER_ACCOUNT_UUID/metadata/open_corporates_url

Authentication via OAuth is required.

Save data in the "opencorporate-URL" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service "
    }   

### PUT /banks/BANK_ID/accounts/ACCOUNT_ALIAS/VIEW_ID/other_accounts/OTHER_ACCOUNT_UUID/metadata/open_corporates_url

Authentication via OAuth is required.

update data in the "opencorporate-URL" field of other account meta data (the other party involved in the transaction [here](#transaction))

JSON:

    {
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service "
    } 

### Note on JSON formatting in this document: 
* Use JSON.stringify({}, null, 4); or http://jsonlint.com to validate the JSON
* Ensure the JSON block is separated from other text by a line and it is indented by 4 spaces

e.g.

JSON:

    {}

Ends 