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
            "swift_bic":"",
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
            "swift_bic":"",
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
                            "id": "ID (given by the user's provider) of the user making the comment",
                            "display_name": "display name of user"
                        }
                },
                "physical_location": {
                        "latitude": 37.423021,
                        "longitude": -122.083739,
                        "date": "date of posting the geo tag",
                        "user": {
                            "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                            "id": "ID (given by the user's provider) of the user making the comment",
                            "display_name": "display name of user"
                        }
                }
            }
        },
        "details": {
            "type": "cash",
            "description": "transaction description",
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
                        "id": "ID (given by the user's provider) of the user making the comment",
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
                        "id": "ID (given by the user's provider) of the user making the comment",
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
                    "id": "ID (given by the user's provider) of the user making the comment",
                    "display_name": "display name of user"
                }
            }
        }
    }

