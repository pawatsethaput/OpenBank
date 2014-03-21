

Authentication via OAuth is required if the view is not public.

**Request:**  
Verb: POST  
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/

**Data:** 

{ 
"bank_id":"id of other bank",
"account_id": "id of account we're sending currency to",
"amount": "1.45",
}


**Response:**  
HTTP code: 201  
Body:
