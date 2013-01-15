A RESTful API for banks that supports multiple views on transaction data, comments and tags - and a flexible JSON data structure.

The protocol consists of *baseline* and *optional* end points.
For baseline compliance, all the baseline URLs must be implemented.
For full compliance, all the baseline and optional end points must be implemented.

The OBP API returns JSON as specified here: http://www.json.org/ and validated here: http://jsonlint.com/ 

Parameters within the URL are written in CAPITAL_LETTERS

The API may be optionally navigated using the links returned for each end point.

All calls should be prefixed with /obp/v1.0

> Note: see https://github.com/OpenBankProject/OBP-API/issues/ for open implementation issues.

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
        },
        "links": [
            {
                "rel": "banks",
                "href": "/banks",
                "method": "GET",
                "title": "Returns a list of banks supported on this server"
            }
        ]
    }

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
                "permalink": "hsbc",
                "abbreviation": "HSBC",
                "name": "The Hongkong and Shanghai Banking Corporation Limited",
                "logo": "url of internet standard image",
                "links": [
                    {
                        "rel": "bank",
                        "href": "/BANK_PERMALINK/bank",
                        "method": "GET",
                        "title": "Get information about the bank identified by BANK_PERMALINK"
                    }
                ]
            }
        ]
    }

### GET /BANK_PERMALINK/bank

Optional

Returns information about the bank specified by BANK_PERMALINK including: 
* Name, Web & Email address. 
* Related links (optional) 

JSON: 

    {
        "bank": {
            "permalink": "postbank",
            "abbreviation": "Postbank",
            "name": "Deutsche Postbank AG (DE)",
            "website": "www.postbank.de",
            "email": "email@bank.com"
        },
        "links": [
            {
                "rel": "accounts",
                "href": "/BANK_PERMALINK/accounts",
                "method": "GET",
                "title": "Get list of accounts available"
            },
            {
                "rel": "offices",
                "href": "/BANK_PERMALINK/offices",
                "method": "GET",
                "title": "Get list of offices"
            }
        ],

    }

### GET /BANK_PERMALINK/offices

Optional

Information returned includes: 
* The list of offices for the BANK_PERMALINK
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

### GET /BANK_PERMALINK/accounts

Baseline 

Information returned includes: 
* The list of accounts the user has access to at the bank specified by BANK_PERMALINK
* Related links (optional). The links include each VIEW_NAME available to the current user. 

JSON:

    {
        "accounts": [
            {
                "number": "the account number e.g. 01203123",
                "account_alias": "public account alias e.g. TESOBE_current",
                "owner_description": "account owners description e.g. TESOBE current account",
                "views_available": [
                    {
                        "name": "view 1 e.g. anonymous",
                        "description": "this is the public view of the account"
                    }
                ],
                "links": [
                    {
                        "rel": "account",
                        "href": "/BANK_PERMALINK/account/ACCOUNT_ALIAS/account/VIEW_NAME",
                        "method": "GET",
                        "title": "Get information about one account"
                    }
                ]
            }
        ]
    }

### GET /BANK_PERMALINK/accounts/ACCOUNT_ALIAS/account/VIEW_NAME

Baseline

Information returned about an account specified by ACCOUNT_ALIAS moderated by VIEW_NAME.
If VIEW_NAME is not specified, it defaults to "owner" i.e. the traditional account owner's view of the account.

* Number, owners, balance
* Views of the account that are available
* Related links (optional)

JSON:

    {
        "account": {
            "number": "01203123",
            "owners": [
                {
                    "id": "123213",
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
                    "name": "view 1 e.g. anonymous",
                    "description": "this is the public view of the account"
                }
            ],
            "links": [
                {
                    "rel": "owner",
                    "href": "/BANK_PERMALINK/accounts/ACCOUNT_ALIAS/transactions",
                    "method": "GET",
                    "title": "Account owner's perspective of the account. The full privileges view."
                },
                {
                    "rel": "VIEW_NAME",
                    "href": "/BANK_PERMALINK/accounts/ACCOUNT_ALIAS/transactions/VIEW_NAME",
                    "method": "GET",
                    "title": "Transactions of the account as mediated by VIEW_NAME"
                }
            ]
        }
    }

### GET /BANK_PERMALINK/accounts/ACCOUNT_ALIAS/transactions/VIEW_NAME

Baseline

If VIEW_NAME is null it defaults to owner (the traditional account owners view of the account)

With the following custom headers: 
* obp_sort_by=CRITERIA
* obp_sort_direction=ASC/DESC
* obp_offset=NUMBER
* obp_limit=NUMBER
* obp_from_date=DATE
* obp_to_date=DATE
* obp_fields=LIST

Returns transactions of the account specified by ACCOUNT_ALIAS and mediated by VIEW_NAME
Other available views might include anonymous, team, shareholders and auditors.

Returns:
* Data specified by fields (default is to show all available fields)
* Links related to pagination / search (optional) 

JSON:

    {
        "transactions": [
            {
                "view": "the view of this transaction e.g. owner/anonymous/owner etc.",
                "uuid": "A universally unique id e.g. 4f5745f4e4b095974c0eeead",
                "bank_id": "The bank's id for the transaction",
                "this_account": {
                    "holder": {
                        "name": "MUSIC PICTURES LIMITED",
                        "alias": "no (if no, this is the real name of the account, if yes, the name is an alias)"
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
                        "alias": "no"
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
                    "type_en": "",
                    "type_de": "Lastschrift",
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
        ],
        "links": []
    }

### GET /BANK_PERMALINK/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/transaction/VIEW_NAME

Optional

Returns information about a specific transaction:
* Information moderated by VIEW_NAME (defaults to owner)
* Related links (optional)

JSON:

    {
    "transaction": {
        "view": "the view of this transaction e.g. owner/anonymous/owner etc.",
        "uuid": "A universally unique id e.g. 4f5745f4e4b095974c0eeead",
        "bank_id": "The bank's id for the transaction",
        "this_account": {
            "holder": {
                "name": "MUSIC PICTURES LIMITED",
                "alias": "no (if no, this is the real name of the account, if yes, the name is an alias)"
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
                "alias": "no"
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
            "type_en": "",
            "type_de": "Lastschrift",
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
        "links": [
            {
                "rel": "transactions_by_other_account",
                "href": "/transactions by other account URL",
                "method": "GET",
                "title": "Transactions related to other account"
            }
        ]
    }
    }

> Note: transactions_by_other_account is a filter

### GET /BANK_PERMALINK/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/comments/VIEW_NAME

VIEW_NAME defaults to owner

Returns comments about a specific transaction:
* All the comments are moderated by VIEW_NAME
* Related links (optional)

JSON:

    {
        "comments": [
            {
                "id": "id of the comment",
                "date": "date of posting the comment",   
                "comment": "the comment",
                "external_link": "url for related image etc."
                "view": "view the comment was made on",
                "user": {
                    "provider": "name of party that authorised the user e.g. bankname/facebook/twitter",
                    "id": "provider id of the user making the comment",
                    "display_name": "display name of user"
                },
                "reply_to": "if this is a reply, the id of the original comment"
            }
        ],
        "links": []
    }

> Note: user.provider + user.id is unique

### GET /BANK_PERMALINK/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/tags/VIEW_NAME

Returns tags about a specific transaction:
* All the tags are moderated by VIEW_NAME
* Related links (optional)

JSON:

    {
        "tags": [
            {
                "id": "id of the tag",
                "tag": "#thehashtag",
                "user": {
                    "provider": "name of party that authorised the user e.g. bankname/facebook/twitter",
                    "id": "provider id of the user making the comment",
                    "display_name": "display name of user"
                }
            }
        ],
                "links": [
            {
                "rel": "transactions_by_tags",
                "href": "transactions by tags",
                "method": "GET",
                "title": "Related transactions by tags"
            }
        ]
    }

### Note on JSON formatting in this document: 
* Use JSON.stringify({}, null, 4); or http://jsonlint.com to validate the JSON
* Ensure the JSON block is separated from other text by a line and it is indented by 4 spaces

e.g.

JSON:

    {}

Ends 