If you would like to create new banks, accounts and transactions on our sandbox, use this json format and send the file to us at  contact AT openbankproject.com and we'll import it for you.


{
  "banks": [
    {
      "id": "test-bank", //must be unique
      "short_name": "TB",
      "full_name": "Test Bank",
      "logo": null,
      "website": null
    }
  ],
  "users": [
    {
      "email": "eric.bloggs@example.com", //must be unique
      "password": "qwerty",
      "display_name": "Eric Bloggs"
    }
  ],
  "accounts": [
    {
      "id": "abcd-5678", // must be unique for the bank
      "bank": "test-bank", //must be specified in the top level "banks"
      "label": "Eric's Current Account",
      "number": "99828371", //must be unique for the bank
      "type": "current",
      "balance": {
        "currency": "EUR",
        "amount": "143.34" //represented as a string
      },
      "IBAN": "00000000000",
      "owners": [
        "eric.bloggs@example.com" //at least one is required, and must be specified in the top level "users"
      ],
      "generate_public_view": false //if this account and its data should be visible to anyone without authentication (some fields will be hidden) 
    }
  ],
  "transactions": [
    {
      "id": "some-transaction-3432", // must be unique for the account
      "this_account": { //must be specified in the top level "accounts"
        "id": "abcd-5678",
        "bank": "test-bank"
      },
      "counterparty":"Acme Inc.", //optional, but useful if specified.
      "details": {
        "type": "",
        "description": "widget purchase",
        "posted": "2014-10-07T10:00:00.000Z",
        "completed": "2014-10-07T10:00:00.000Z",
        "new_balance": "143.34", //represented as a string (the balance of the account after this transaction)
        "value": "10.00" //represented as a string (the amount of the transaction, negative if money left the account, positive if it came in)
      }
    }
  ]
}