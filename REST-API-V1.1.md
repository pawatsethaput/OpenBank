# DRAFT

A RESTful API for banks that supports multiple views on transaction data, comments and tags - and a flexible JSON data structure.

The protocol consists of *baseline* and *optional* end points.
For baseline compliance, all the baseline URLs must be implemented.
For full compliance, all the baseline and optional end points must be implemented.

The OBP API returns JSON as specified here: http://www.json.org/ and validated here: http://jsonlint.com/ 

Parameters within the URL are written in CAPITAL_LETTERS

The API may be optionally navigated using the links returned for each end point.
All calls should be prefixed with /obp/v1.1

> Note: see https://github.com/OpenBankProject/OBP-API/issues/ for open implementation issues.

> See https://github.com/OpenBankProject/OBP-API/wiki/How-links-should-work for a note about links

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

Each returned call or object can optionally have a list of links relavent to the resource e.g.:

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

### GET /banks/BANK_ID/accounts

Baseline 

Information returned includes: 
* The list of accounts the user has access to at the bank specified by BANK_ID
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
                            "name": "view 1 e.g. anonymous",
                            "description": "this is the public view of the account"
                        }
                    }
                ]
            }
        }
    ]
}

### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/account

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
                        "name": "view 1 e.g. anonymous",
                        "description": "this is the public view of the account"
                    }
                }
            ]
        }
    }

### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/VIEW_NAME

Baseline

If VIEW_NAME is null it defaults to owner (the traditional account owners view of the account)

With the following custom headers: 
* obp_sort_by=CRITERIA
* obp_sort_direction=ASC/DESC
* obp_limit=NUMBER
* obp_offset=NUMBER
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

### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/transaction/VIEW_NAME

Optional

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

> Note: transactions_by_other_account is a filter

### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/comments/VIEW_NAME

VIEW_NAME defaults to owner

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
                    "external_link": "url for related image etc.",
                    "view": "view the comment was made on",
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

> Note: user.provider + user.id is unique

### GET /banks/BANK_ID/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/tags/VIEW_NAME

Returns tags about a specific transaction:
* All the tags are moderated by VIEW_NAME
* Related links (optional)

JSON:

    {
        "tags": [
            {
                "tag": {
                    "id": "id of the tag",
                    "tag": "#thehashtag",
                    "user": {
                        "provider": "name of party that authorised the user e.g. bankname/facebook/twitter",
                        "id": "provider id of the user making the comment",
                        "display_name": "display name of user"
                    }
                }
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