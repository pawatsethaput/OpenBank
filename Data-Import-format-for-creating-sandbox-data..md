If you would like to create new banks, accounts and transactions on our sandbox, use this json format and send the file to us at  contact AT openbankproject.com and we'll import it for you.




    {
      "banks": [
        {
          "id": "test-bank",
          "short_name": "TB",
          "full_name": "Test Bank",
          "logo": null,
          "website": null
        }
      ],
      "users": [
        {
          "email": "eric.bloggs@example.com",
          "password": "qwerty",
          "display_name": "Eric Bloggs"
        }
      ],
      "accounts": [
        {
          "id": "abcd-5678",
          "bank": "test-bank", 
          "label": "Eric's Current Account",
          "number": "99828371", 
          "type": "current",
          "balance": {
            "currency": "EUR",
            "amount": "143.34" 
          },
          "IBAN": "00000000000",
          "owners": [
            "eric.bloggs@example.com" 
          ],
          "generate_public_view": false  
        }
      ],
      "transactions": [
        {
          "id": "some-transaction-3432", 
          "this_account": { 
            "id": "abcd-5678",
            "bank": "test-bank"
          },
          "counterparty":"Acme Inc.", 
          "details": {
            "type": "",
            "description": "widget purchase",
            "posted": "2014-10-07T10:00:00.000Z",
            "completed": "2014-10-07T10:00:00.000Z",
            "new_balance": "143.34", 
            "value": "10.00" 
          }
        }
      ]
    }



Notes:

* bank id must be unique
* email must be unique
* account id must be unique for the bank
* accounts.bank must be specified in the top level "banks"
* accounts.number must be unique for the bank
* accounts.balance amount represented as a string
* transactions.id must be unique for the account
* accounts.owners: at least one is required, and must be specified in the top level "users"
* generate_public_view: if this account and its data should be visible to anyone without authentication (some fields will be hidden)
* transactions.this_account: must be specified in the top level "accounts"
* transactions.counterparty: optional, but useful if specified.
* transactions.details.new_balance: represented as a string (the balance of the account after this transaction)
* transactions.details.value: represented as a string (the amount of the transaction, negative if money left the account, positive if it came in)


Once the data has been imported you can view it / manage it using: https://sofisandbox.openbankproject.com
