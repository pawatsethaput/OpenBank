V1.4.5 Transfers is work in progress: https://github.com/OpenBankProject/OBP-API/tree/challenge_payments

This is not yet included in any sandboxes.


<a name="transfers"></a>
#Transfers (Payments)

*Optional*

Getting available transfer methods for an account

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/transfer-methods

**Response:**  
HTTP code: 200  
Body:
    
    {
        "transfer_methods": [
            {
                "id": "sandbox",
                "resource_URL": "http://localhost:8080/obp/1.3.0/banks/BANK_ID/accounts/ACCOUNT_ID/transfer-methods/sandbox",
                "description": "Transfers for the OBP sandbox",
                "body": {
                    "to" : {
                        "account_id" : "Id of the OBP sandbox account to send the payment to (at bank_id specified below)",
                        "bank_id": "Id of the OBP sandbox bank of the account to send the payment to"
                    },
                    "amount": "The transaction amount as a string, e.g. 12.43"
                }
            }, ...
        ]
    }


The "body" parameter describes the JSON expected as an argument when initiating a transfer at ("resource_url" + "/transfers").

*Optional*

Getting sandbox transfers:

**Request:**  
Verb: GET  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/transfer-methods/sandbox/transfers  


**Response:**
HTTP code: 200
Body:

    {
      "id":"8192-axmp-6125-xxui",
      "from": {
        "bankId": "FROM_BANK_ID",
        "accountId": "FROM_ACCOUNT_ID"
      },
      "transactionId": null | "9283-rerw-3ghs-4sfe",
      "status":"INITIATED | COMPLETED | CHALLENGES_PENDING | FAILED",
      "start_date": Date,
      "end_date": Date,
      "challenge_set" : {
          "allowed_attempts" : 2
          "challenges" : [
              {
                "id": "jmlk-0091-mlox-8196",
                "question": "Please provide TAN"
              },
              {
                "id": "osns-32sf-4faa-dds4",
                "question": "What was the name of your first pet?",
                "start_date":date
              }
          ]
      }
    }

If status is not COMPLETED, transactionId will be null, as no transaction has been created.  

Initiating transfers:

Security challenge responses may be required before the transaction can proceed leading to the two work flows below, illustrated with the case of sandbox transfers.
Other transfer types will differ only in the content of the body of the initiation POST request.  


**Sandbox Transfers:**

This is an experimental call, currently only implemented in the OBP sandbox instance. It is currently very minimal, and will almost certainly change.

This will only work if account to pay exists at the bank specified in the json, and if that account has the same currency as that of the payee.

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/transfer-methods/sandbox/transfers

Body:

    {
        "to" : {
          "account_id" : "Id of the account to send the payment to (at bank_id specified below)",
          "bank_id": "Id of the bank of the account to send the payment to"
        },
        "amount": "The transaction amount as a string, e.g. 12.43"
    }

**Case 1 - No security challenge is required**

**Response:**    

Headers:

      http code 201 Created  
      location: /banks/BANK_ID/accounts/ACCOUNT_ID/transfer-methods/sandbox/transfers/8192-axmp-6125-xxui/  
Body: 

    {
      "id":"8192-axmp-6125-xxui",
      "from": {
        "bankId": "FROM_BANK_ID",
        "accountId": "FROM_ACCOUNT_ID"
      },
      "transactionId": null | "9283-rerw-3ghs-4sfe",
      "status":"INITIATED | COMPLETED",
      "start_date": Date,
      "end_date": Date,
      "challenge_set" : null
    }


**Case 2 - Security challenges required before the transaction can proceed**

**Response:**

Headers:

    http code: 202 Accepted   
    location: /banks/BANK_ID/accounts/ACCOUNT_ID/transfer-methods/sandbox/transfers/8192-axmp-6125-xxui/  
    
Body: 

    {
      "id":"8192-axmp-6125-xxui",
      "from": {
        "bankId": "FROM_BANK_ID",
        "accountId": "FROM_ACCOUNT_ID"
      },
      "transactionId": null,
      "status": "CHALLENGES_PENDING",
      "start_date": Date,
      "end_date": Date,
      "challenge_set" : {
        "allowed_attempts" : 2
        "challenges" : [
            {
              "id": "jmlk-0091-mlox-8196",
              "question": "Please provide TAN"
            },
            {
              "id": "osns-32sf-4faa-dds4",
              "question": "What was the name of your first pet?",
              "start_date":date
            }
        ]
      }
    }

Step B: Resolve challenges

**Request:**
POST /banks/BANK_ID/accounts/ACCOUNT_ID/transfer-methods/sandbox/transfers/8192-axmp-6125-xxui/challenges/answers

Body:

    [
      {
        "id": "jmlk-0091-mlox-8196",
        "answer": "19282"
      },
      {
        "id": "osns-32sf-4faa-dds4",
        "answer": "What was the name of your first pet?",
      }
    ]


**Response 1 (good answer):**

Headers:
  
    http code: 204
    location: /obp/v1.3.0/banks/BANK_ID/accounts/ACCOUNT_ID/transfer-methods/sandbox/transfers/8192-axmp-6125-xxui

Body:

    {}

**Response 2 (bad answer):**

Headers:

    http code: 400

Body:

    {
      "error" : {
        "code" : 1
        "message" : "Transfer failed."
      }
    }

The possible values for the error code are:

1 : Transfer failed.
2 : Incorrect answer to one or more questions. You may try to answer the challenges again.