**Developers: This page specifies a Transaction Request approach that is work in progress. It is not currently available on the sandboxes!**

**Developers: This feature is not currently available on the sandboxes!**


<a name="payments"></a>
<a name="transactionRequests"></a>
#Transaction Requests (Payments)

**Overview:**

In order to initiate a payment or transaction(s) we initiate a transaction request.

A transaction request may require a security challenge to be answered before it proceeds.

A successful transaction request will result in one or more transactions being created.

Different Transaction Request Types exist to allow a similar interface onto SEPA, BitCoin payments etc. The Transaction Request Types resource describes the url that must be used and the body that must be submitted for a transaction request of that type to succeed. All other logic including security challenges should be common. 

*Optional*

### Getting a list of the available transaction request types (or methods) for an account

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/transaction-request-types

**Response:**  
HTTP code: 200  
Body:
    
    {
        "transaction_request_types": [
            {
                "type": "sandbox",
                "resource_url": "http://localhost:8080/obp/1.4.5/banks/BANK_ID/accounts/ACCOUNT_ID/transaction-request-types/sandbox",
                "description": "Transaction Requests for the OBP sandbox",
                "body": {
                    "to": {
                        "account_id": "Id of the OBP sandbox account to send the payment to (at bank_id specified below)",
                        "bank_id": "Id of the OBP sandbox bank of the account to send the payment to"
                    },
                    "value": {
                        "currency": "EUR",
                        "amount": "The transaction amount as a string, e.g. 12.43"
                    },
                    "description": "The basic description of the transaction"
                },
                "supported_challenge_types": ["SMS_TAN", "INDEXED_TAN", "SANDBOX_TAN"]
            },...
        ]
    }


The "body" parameter describes the JSON expected as an argument when initiating a transaction request at ("resource_url" + "/transaction-requests").

*Optional*

### Getting transaction requests:

**Request:**  
Verb: GET  

URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transaction-requests

**Response:**
HTTP code: 200
Body:

    {
        "transaction-requests": [
            {
                "id": "8192-axmp-6125-xxui",
                "type": "sandbox",
                "from": {
                    "bank_id": "FROM_BANK_ID",
                    "account_id": "FROM_ACCOUNT_ID"
                },
                "body": {
                    "to": {
                        "account_id": "Id of the OBP sandbox account to send the payment to (at bank_id specified below)",
                        "bank_id": "Id of the OBP sandbox bank of the account to send the payment to"
                    },
                    "value": {
                        "currency": "EUR",
                        "amount": "The transaction amount as a string, e.g. 12.43"
                    },
                    "description": "The basic description of the transaction"
                },
                "transaction_ids": [
                    "9283-rerw-3ghs-4sfe"
                ],
                "status": "INITIATED | COMPLETED | CHALLENGES_PENDING | FAILED",
                "start_date": "Date",
                "end_date": "Date",
                "challenge": {
                    "id": "jmlk-0091-mlox-8196",
                    "allowed_attempts": 2,
                    "challenge_type": "DUMMY_TAN"
                }
            }
        ]
    }


If status is not COMPLETED, transaction_ids will be null, as no transaction(s) have been created.  

### Initiating transaction requests:

Security challenge responses may be required before the creation of transaction(s) can proceed leading to the two work flows below, illustrated with the case of sandbox transaction requests.
Other transaction request types will differ only in the content of the body of the initiation POST request.  


**Sandbox Transaction Requests:**

This call is only implemented in the OBP sandbox instances.

This will only work if account to pay exists at the bank specified in the JSON, and if that account has the same currency as that of the payee.

Step A

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transaction-request-types/sandbox/transaction-requests

Body:

    {
        "to": {
            "account_id": "Id of the account to send the payment to (at bank_id specified below)",
            "bank_id": "Id of the bank of the account to send the payment to"
        },
        "value": {
            "currency": "EUR",
            "amount": "The transaction amount as a string, e.g. 12.43"
        },
        "description": "The sender's description of the transaction (e.g. My rent)",
        "challenge_type" : "SANDBOX_TAN"
    }

**Case 1 - No security challenge is required**

**Response:**    

Headers:

      http code 201 Created  
      location: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transaction-request-types/sandbox/transaction-requests/8192-axmp-6125-xxui  
Body: 

    {
      "id":"8192-axmp-6125-xxui",
      "type" : "sandbox",
      "from": {
        "bank_id": "FROM_BANK_ID",
        "account_id": "FROM_ACCOUNT_ID"
      },
      
      "transaction_ids": ["9283-rerw-3ghs-4sfe"],
      "status":"COMPLETED",
      "start_date": Date,
      "end_date": Date,
      "challenge" : null
    }


**Case 2 - Security challenges required before the transaction(s) can proceed**

**Response:**

Headers:

    http code: 202 Accepted   
    location: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transaction-request-types/sandbox/transaction-requests/8192-axmp-6125-xxui  
    
Body: 

    {
        "id": "8192-axmp-6125-xxui",
        "type": "sandbox",
        "from": {
            "bankId": "FROM_BANK_ID",
            "account_id": "FROM_ACCOUNT_ID"
        },
        "body": {
            "to": {
                "account_id": "Id of the OBP sandbox account to send the payment to (at bank_id specified below)",
                "bank_id": "Id of the OBP sandbox bank of the account to send the payment to"
            },
            "value": {
                "currency": "EUR",
                "amount": "The transaction amount as a string, e.g. 12.43"
            },
            "description": "The basic description of the transaction"
        },
        "transaction_ids": null,
        "status": "CHALLENGE_PENDING",
        "start_date": "Date",
        "end_date": "Date",
        "challenge": {
            "id": "jmlk-0091-mlox-8196",
            "allowed_attempts": 2,
            "challenge_type": "SANDBOX_TAN"
        }
    }

Step B: Resolve challenge

**Request:**
POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transaction-request-types/sandbox/transaction-requests/8192-axmp-6125-xxui/challenge/answer

Body:

      {
        "id": "jmlk-0091-mlox-8196",
        "answer": "19282"
      }


**Response 1 (good answer):**

Headers:
  
    http code: 204
    location: /obp/v1.3.0/banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transaction-request-types/sandbox/transaction-requests/8192-axmp-6125-xxui

Body:

    {}

**Response 2 (bad answer):**

Headers:

    http code: 400

Body:

    {
      "error" : {
        "code" : 1
        "message" : "Transaction Request failed."
      }
    }

The possible values for the error code are:

1 : Transaction Request failed.
2 : Incorrect answer to one or more questions. You may try to answer the challenge again.