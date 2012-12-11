A RESTful API for banks that supports multiple views on transaction data, comments and tags - and a flexible JSON data structure.

The protocol consists of *baseline* and *optional* end points.
For baseline compliance, all the baseline URLs must be implemented.
For full compliance, all the baseline and optional end points must be implemented.

The OBP API returns JSON as specified here: http://www.json.org/ and validated here: http://jsonlint.com/ 

Parameters within the URL are written in CAPITAL_LETTERS

The API may be optionally navigated using the links returned for each end point.

All calls should be prefixed with /obp/v1.0

> None of these calls has been tested with OAUTH yet. The default "owner" view is not currently implemented for any of the calls. -E.S.


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

> TODO: banks / bank should have permalink, short_name, full_name. See if there is a standard for this.

JSON:

    {
        "banks": [
            {
                "alias": "HSBC",
                "name": "The Hongkong and Shanghai Banking Corporation Limited",
                "logo": "url of logo.png 512 X 512 pixels",
                "links": [
                    {
                        "rel": "bank",
                        "href": "/BANK_ALIAS/bank",
                        "method": "GET",
                        "title": "Get information about the bank identified by BANK_ALIAS"
                    }
                ]
            }
        ]
    }
> To discuss: What do alias and name mean? Currently alias is the bank's permalink (e.g. postbank) and the name is the "name" field of the bank (e.g. POSTBANK). Also, we have no logo urls at the moment. -E.S.

### GET /BANK_ALIAS/bank

Optional

Returns information about the bank specified by BANK_ALIAS including: 
* Name, Web & Email address. 
* Related links (optional) 

JSON: 

    {
        "bank": {
            "name": "Post Bank",
            "website": "www.postbank.de",
            "email": "email@bank.com"
        },
        "links": [
            {
                "rel": "accounts",
                "href": "/BANK_ALIAS/accounts",
                "method": "GET",
                "title": "Get list of accounts available"
            },
            {
                "rel": "offices",
                "href": "/BANK_ALIAS/offices",
                "method": "GET",
                "title": "Get list of offices"
            }
        ],

    }

> Call currently exists but we currently aren't storing website or email so these fields are empty. -E.S.

> This json format for a bank conflicts with the format defined in /banks. Do we really want two different formats or can we change one of both of them so that they share a common format? If we don't want the same format, then why does this particular information belong here and not in the other api call, and vice versa? -E.S.

### GET /BANK_ALIAS/offices

Optional

Information returned includes: 
* The list of offices for the BANK_ALIAS
* Related links (optional)

JSON:

    {
	    "offices": [
	        {
                    "address": "address",
	            "type": "HQ/branch/call center",
	            "BIC_SWIFT": "ISO_9362 BIC / SWIFT code",
	            "staff": [
	                {
	                    "name": "joe brown",
	                    "email": "aze@aze.com",
	                    "phone": "1234"
	                }
	            ],
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

> Currently not supported. How do we propose to get this data? Getting a list of staff is probably out of the question without (or even with) bank integration of the API. -E.S.

### GET /BANK_ALIAS/accounts

Baseline 

Information returned includes: 
* The list of accounts the user has access to at the bank specified by BANK_ALIAS
* Related links (optional)

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
                        "href": "/BANK_ALIAS/account/ACCOUNT_ALIAS/account/VIEW_NAME",
                        "method": "GET",
                        "title": "Get information about one account"
                    }
                ]
            }
        ]
    }

> Exists, but owner_description is not implemented. -E.S.

### GET /BANK_ALIAS/accounts/ACCOUNT_ALIAS/account/VIEW_NAME

Baseline

Information returned about an account specified by ACCOUNT_ALIAS moderated by VIEW_NAME.
If VIEW_NAME is not specified, it defaults to "owner" i.e. the traditional account owner's view of the account.

* Number, date_opened, owners, balance
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
            "date_opened": "01/02/2010",
            "views_available": [
                {
                    "name": "view 1 e.g. anonymous",
                    "description": "this is the public view of the account"
                }
            ],
            "links": [
                {
                    "rel": "owner",
                    "href": "/BANK_ALIAS/accounts/ACCOUNT_ALIAS/transactions",
                    "method": "GET",
                    "title": "Account owner's perspective of the account. The full privileges view."
                },
                {
                    "rel": "VIEW_NAME",
                    "href": "/BANK_ALIAS/accounts/ACCOUNT_ALIAS/transactions/VIEW_NAME",
                    "method": "GET",
                    "title": "Transactions of the account as mediated by VIEW_NAME"
                }
            ]
        }
    }

> Owners do not yet have ids (is this necessary?), date_opened does not exist yet. -E.S.

### GET /BANK_ALIAS/accounts/ACCOUNT_ALIAS/transactions/VIEW_NAME

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
            },
            "links": [
                {
                    "rel": "next",
                    "href": "next URL",
                    "method": "GET",
                    "title": "Next transactions in list"
                },
                {
                    "rel": "previous",
                    "href": "previous URL",
                    "method": "GET",
                    "title": "Previous transactions in list"
                }
            ]
        }
    ]
    }

> Currently obp_fields is not implemented. The obp_sort_by header is not implemented for security reasons. To make this query efficient we would need to incorporate the sort in the mongodb query, but alias names are not currently in the obp_envelope "schema" as they are not transaction data but rather metadata. In general we can't sort on any fields unless we have full view permissions on that field.

> I'm not sure how the pagination in links should work if we are including offset and limit as headers rather than url parameters. The links would have to assume the same headers. In any case, they aren't implemented yet. Also, shouldn't the "links" json actually be _outside_ of the array of transactions? -E.S.

### GET /BANK_ALIAS/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/transaction/VIEW_NAME

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
> I'm not sure what is meant by the links here. We can't get the transactions for the other account unless it happens to be an OBP enabled account, and even then there would be issues with views/privileges. -E.S.

> As discussed before we will want to get rid of type_en and type_de. Should the type even be translated? Account types might not translate between languages/countries.  -E.S.

### GET /BANK_ALIAS/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/comments/VIEW_NAME

VIEW_NAME defaults to owner

Returns comments about a specific transaction:
* All the comments are moderated by VIEW_NAME
* Related links (optional)

JSON:

    {
        "comments": [
            {
                "id": "id of the comment",
                "comment": "the comment",
                "view": "view the comment was made on",
                "user_provider": "name of party that authorised the user e.g. bankname/facebook/twitter",
                "user_id": "id of the user making the comment",
                "user_name": "name of user",
                "reply_to": "if this is a reply, the id of the original comment"
            }
        ],
        "links": []
    }
> We don't currently support replying to comments. As well, the meanings of the user_ fields above are not clear. If we have multiple user providers, then does this mean user_id is unique only for that provider? -E.S.

### GET /BANK_ALIAS/accounts/ACCOUNT_ALIAS/transactions/TRANSACTION_ID/tags/VIEW_NAME

Returns tags about a specific transaction:
* All the tags are moderated by VIEW_NAME
* Related links (optional)

JSON:

    {
        "tags": [
            {
                "id": "id of the tag",
                "tag": "the tag",
                "type": "type of tag e.g. url,text",
                "view": "view the tag was made on",
                "user_provider": "name of party that authorised the user e.g. bank,facebook,twitter",
                "user_id": "id of the tagging user",
                "user_name": "name of user"
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