### Version: 1.2

### Status: Stable

# Index

* [About](#about)
* [Implementation hints](#implementation-hints)
* [Root](#root)
* Banks
    * [All](#banks)
    * [One](#bank)
* Accounts
    * [All](#accounts)
    * [Public](#accounts-public)
    * [Private](#accounts-private)
    * [One](#account)
    * [Views](#views)
    * Permissions
        * [All](#permissions)
        * [One](#permission)
        * [Grant](#grant-permission)
        * [Revoke](#revoke-permission)
    * Other Accounts
        * [All](#account-other-accounts)
        * [One](#account-other-account)
            * [Meta data](#other_account-metadata)
                * [Public Alias](#public-alias)
                * [Private Alias](#private-alias)
                * [More info](#more_info)
                * [URL](#URL)
                * [Image URL](#image_url)
                * [Open Corporates URL](#opencorporates-URL)
                * [Corporate Location](#corporate_location)
                * [Physical Location](#physical_location)
* Transactions
    * [All](#transactions)
    * [One](#transaction)
    * Meta data
        * [Narrative](#narrative)
        * [Comments](#comments)
        * [Tags](#tags)
        * [Images](#images)
        * [Where](#where)
    * [Other account](#transaction_other_account)


<a name="about"></a>>
### About

The Open Bank Project API is a RESTful API for banks that supports multiple views on transaction data, data enrichment and data blurring.

The protocol consists of *baseline* and *optional* end points.
For baseline compliance, all the baseline URLs must be implemented.
For full compliance, all the baseline and optional end points must be implemented.

 * All calls should be prefixed with /obp/v1.2

 * The OBP API returns JSON as specified [here](http://www.json.org/) and validated [here](http://jsonlint.com/).

 * Parameters within the URL are written in CAPITAL_LETTERS.

 * In some calls you will see the mention "OAuth authentication is required", that means that to access to the protected resource, the OAuth header is required with all the parameters like oauth_token, oauth_nonce, etc. ([See here for more details](https://github.com/OpenBankProject/OBP-API/wiki/OAuth-1.0-Server)).

 * If information is not available (whether missing or blocked by access control), its value will be null. This applies to normal values (Strings, Numbers, etc.) and to Arrays/Objects.

 Example:

    {
        "info" : "Some info",
        "info2" : {
            "info3" : "Some more info"
        }
    }

  could be returned as:

    {
        "info" : null,
        "info2" : null
    }


<a name="implementation-hints"></a>>
### Implementation hints and notes.

The implementation of V1.2 is stable.

<a name="root"></a>>
#Root
*Baseline*

Returns information about:

* API version
* Hosted by information

**Request:**
Verb: GET
URL: /

**Response:**
HTTP code: 200
Body:

    {
        "version": "1.2",
        "git_commit": "4ca0cdbe9e51e41d0105bf26cf463ac5225a50a1",
        "hosted_by": {
            "organisation": "TESOBE",
            "email": "contact@tesobe.com",
            "phone": "+49 (0)30 8145 3994"
        }
    }

<a name="banks"></a>>
#Banks
*Baseline*

Returns a list of banks supported on this server:

* ID used as parameter in URLs
* Short and full name of bank
* Logo URL
* Website

**Request:**
Verb: GET
URL: /banks

**Response:**
HTTP code: 200
Body:

    {
        "banks": [
            {
                "id": "hsbc",
                "short_name": "HSBC",
                "full_name": "The Hongkong and Shanghai Banking Corporation Limited",
                "logo": "url of internet standard image",
                "website": "www.postbank.de"
            }
        ]
    }

<a name="bank"></a>>
#Bank
*Optional*

Returns information about a single bank specified by BANK_ID including:

* Short and full name of bank
* Logo URL
* Website

**Request:**
Verb: GET
URL: /banks/BANK_ID

**Response:**
HTTP code: 200
Body:

    {
        "short_name": "Postbank",
        "full_name": "Deutsche Postbank AG (DE)",
        "logo": "url of internet standard image",
        "website": "www.postbank.de"
    }

<a name="accounts"></a>>
#Accounts
*Baseline*

Returns the list of accounts at BANK_ID that the user has access to.
For each account the API returns the account ID and the available views.

If the user is not authenticated via OAuth, the list will contains the accounts providing public views. If the user is
authenticated, the list will contain accounts the user has non-public permissions for (i.e. can access non-public views) BUT
WILL NOT INCLUDE public accounts that the user has no special access to. This behaviour was not originally intended but has been
preserved to maintain compability with any applications expecting this behaviour. This call in version 1.2.1 has a different behaviour,
namely that public accounts to which the user has no special access to will be included when authenticated,
so please make note of this when upgrading.

NOTE: the view json returned from the API contains the field hide_metadata_if_alias, which does not match the field name of hide_metadata_if_alias_used that is expected when creating or updating a view. This is fixed in newer versions of the API.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts

**Response:**
HTTP code: 200
Body:

    {
        "accounts": [
            {
                "id": "A unique identifier used for ACCOUNT_ID",
                "label" : "account label e.g. TESOBE main account",
                "views_available": [
                    {
                        "id": "A unique identifier used for VIEW_ID",
                        "short_name": "Public / Team / Auditors...",
                        "description": "e.g. this is the public view of the TESOBE account",
                        "is_public": "boolean. true if the public (user not logged in) can see this view."
                        "which_alias_to_use": "public/private/none",
                        "hide_metadata_if_alias" : true/false,
                        "can_see_transaction_this_bank_account" : true/false,
                        "can_see_transaction_other_bank_account" : true/false,
                        "can_see_transaction_metadata" : true/false,
                        "can_see_transaction_label" : true/false,
                        "can_see_transaction_amount" : true/false,
                        "can_see_transaction_type" : true/false,
                        "can_see_transaction_currency" : true/false,
                        "can_see_transaction_start_date" : true/false,
                        "can_see_transaction_finish_date" : true/false,
                        "can_see_transaction_balance" : true/false,
                        "can_see_comments" : true/false,
                        "can_see_narrative" : true/false,
                        "can_see_tags" : true/false,
                        "can_see_images" : true/false,
                        "can_see_bank_account_owners" : true/false,
                        "can_see_bank_account_type" : true/false,
                        "can_see_bank_account_balance" : true/false,
                        "can_see_bank_account_currency" : true/false,
                        "can_see_bank_account_label" : true/false,
                        "can_see_bank_account_national_identifier" : true/false,
                        "can_see_bank_account_swift_bic" : true/false,
                        "can_see_bank_account_iban" : true/false,
                        "can_see_bank_account_number" : true/false,
                        "can_see_bank_account_bank_name" : true/false,
                        "can_see_other_account_national_identifier" : true/false,
                        "can_see_other_account_swift_bic" : true/false,
                        "can_see_other_account_iban" : true/false,
                        "can_see_other_account_bank_name" : true/false,
                        "can_see_other_account_number" : true/false,
                        "can_see_other_account_metadata" : true/false,
                        "can_see_other_account_kind" : true/false,
                        "can_see_more_info" : true/false,
                        "can_see_url" : true/false,
                        "can_see_image_url" : true/false,
                        "can_see_open_corporates_url" : true/false,
                        "can_see_corporate_location" : true/false,
                        "can_see_physical_location" : true/false,
                        "can_see_public_alias" : true/false,
                        "can_see_private_alias" : true/false,
                        "can_add_more_info" : true/false,
                        "can_add_url" : true/false,
                        "can_add_image_url" : true/false,
                        "can_add_open_corporates_url" : true/false,
                        "can_add_corporate_location" : true/false,
                        "can_add_physical_location" : true/false,
                        "can_add_public_alias" : true/false,
                        "can_add_private_alias" : true/false,
                        "can_delete_corporate_location" : true/false,
                        "can_delete_physical_location" : true/false,
                        "can_edit_narrative" : true/false,
                        "can_add_comment" : true/false,
                        "can_delete_comment" : true/false,
                        "can_add_tag" : true/false,
                        "can_delete_tag" : true/false,
                        "can_add_image" : true/false,
                        "can_delete_image" : true/false,
                        "can_add_where_tag" : true/false,
                        "can_see_where_tag" : true/false,
                        "can_delete_where_tag" : true/false
                    }
                ],
                "bank_id":"the id of the bank where the account is hosted"
            }
        ]
    }

<a name="accounts-public"></a>>
#Public Accounts
*Optional*

Returns a list of the public accounts at BANK_ID. For each account the API returns the ID and the available views.

Authentication via OAuth is not required.

NOTE: the view json returned from the API contains the field hide_metadata_if_alias, which does not match the field name of hide_metadata_if_alias_used that is expected when creating or updating a view. This is fixed in newer versions of the API.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/public

**Response:**
HTTP code: 200
Body:

    {
        "accounts": [
            {
                "id": "A unique identifier used for ACCOUNT_ID",
                "label" : "account label e.g. TESOBE main account",
                "views_available": [
                    {
                        "id": "A unique identifier used for VIEW_ID",
                        "short_name": "Public / Team / Auditors...",
                        "description": "e.g. this is the public view of the TESOBE account",
                        "is_public": "boolean. true if the public (user not logged in) can see this view."
                        "which_alias_to_use": "public/private/none",
                        "hide_metadata_if_alias" : true/false,
                        "can_see_transaction_this_bank_account" : true/false,
                        "can_see_transaction_other_bank_account" : true/false,
                        "can_see_transaction_metadata" : true/false,
                        "can_see_transaction_label" : true/false,
                        "can_see_transaction_amount" : true/false,
                        "can_see_transaction_type" : true/false,
                        "can_see_transaction_currency" : true/false,
                        "can_see_transaction_start_date" : true/false,
                        "can_see_transaction_finish_date" : true/false,
                        "can_see_transaction_balance" : true/false,
                        "can_see_comments" : true/false,
                        "can_see_narrative" : true/false,
                        "can_see_tags" : true/false,
                        "can_see_images" : true/false,
                        "can_see_bank_account_owners" : true/false,
                        "can_see_bank_account_type" : true/false,
                        "can_see_bank_account_balance" : true/false,
                        "can_see_bank_account_currency" : true/false,
                        "can_see_bank_account_label" : true/false,
                        "can_see_bank_account_national_identifier" : true/false,
                        "can_see_bank_account_swift_bic" : true/false,
                        "can_see_bank_account_iban" : true/false,
                        "can_see_bank_account_number" : true/false,
                        "can_see_bank_account_bank_name" : true/false,
                        "can_see_other_account_national_identifier" : true/false,
                        "can_see_other_account_swift_bic" : true/false,
                        "can_see_other_account_iban" : true/false,
                        "can_see_other_account_bank_name" : true/false,
                        "can_see_other_account_number" : true/false,
                        "can_see_other_account_metadata" : true/false,
                        "can_see_other_account_kind" : true/false,
                        "can_see_more_info" : true/false,
                        "can_see_url" : true/false,
                        "can_see_image_url" : true/false,
                        "can_see_open_corporates_url" : true/false,
                        "can_see_corporate_location" : true/false,
                        "can_see_physical_location" : true/false,
                        "can_see_public_alias" : true/false,
                        "can_see_private_alias" : true/false,
                        "can_add_more_info" : true/false,
                        "can_add_url" : true/false,
                        "can_add_image_url" : true/false,
                        "can_add_open_corporates_url" : true/false,
                        "can_add_corporate_location" : true/false,
                        "can_add_physical_location" : true/false,
                        "can_add_public_alias" : true/false,
                        "can_add_private_alias" : true/false,
                        "can_delete_corporate_location" : true/false,
                        "can_delete_physical_location" : true/false,
                        "can_edit_narrative" : true/false,
                        "can_add_comment" : true/false,
                        "can_delete_comment" : true/false,
                        "can_add_tag" : true/false,
                        "can_delete_tag" : true/false,
                        "can_add_image" : true/false,
                        "can_delete_image" : true/false,
                        "can_add_where_tag" : true/false,
                        "can_see_where_tag" : true/false,
                        "can_delete_where_tag" : true/false
                    }
                ],
                "bank_id":"the id of the bank where the account is hosted"
            }
        ]
    }

<a name="accounts-private"></a>>
#Private Accounts
*Optional*

Returns the list of accounts private (non-public) at BANK_ID that the user has access to.
For each account the API returns the ID and the available views.

Authentication via OAuth is required.

NOTE: the view json returned from the API contains the field hide_metadata_if_alias, which does not match the field name of hide_metadata_if_alias_used that is expected when creating or updating a view. This is fixed in newer versions of the API.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/private

**Response:**
HTTP code: 200
Body:

    {
        "accounts": [
            {
                "id": "A unique identifier used for ACCOUNT_ID",
                "label" : "account label e.g. TESOBE main account",
                "views_available": [
                    {
                        "id": "A unique identifier used for VIEW_ID",
                        "short_name": "Public / Team / Auditors...",
                        "description": "e.g. this is the public view of the TESOBE account",
                        "is_public": "boolean. true if the public (user not logged in) can see this view."
                        "which_alias_to_use": "public/private/none",
                        "hide_metadata_if_alias" : true/false,
                        "can_see_transaction_this_bank_account" : true/false,
                        "can_see_transaction_other_bank_account" : true/false,
                        "can_see_transaction_metadata" : true/false,
                        "can_see_transaction_label" : true/false,
                        "can_see_transaction_amount" : true/false,
                        "can_see_transaction_type" : true/false,
                        "can_see_transaction_currency" : true/false,
                        "can_see_transaction_start_date" : true/false,
                        "can_see_transaction_finish_date" : true/false,
                        "can_see_transaction_balance" : true/false,
                        "can_see_comments" : true/false,
                        "can_see_narrative" : true/false,
                        "can_see_tags" : true/false,
                        "can_see_images" : true/false,
                        "can_see_bank_account_owners" : true/false,
                        "can_see_bank_account_type" : true/false,
                        "can_see_bank_account_balance" : true/false,
                        "can_see_bank_account_currency" : true/false,
                        "can_see_bank_account_label" : true/false,
                        "can_see_bank_account_national_identifier" : true/false,
                        "can_see_bank_account_swift_bic" : true/false,
                        "can_see_bank_account_iban" : true/false,
                        "can_see_bank_account_number" : true/false,
                        "can_see_bank_account_bank_name" : true/false,
                        "can_see_other_account_national_identifier" : true/false,
                        "can_see_other_account_swift_bic" : true/false,
                        "can_see_other_account_iban" : true/false,
                        "can_see_other_account_bank_name" : true/false,
                        "can_see_other_account_number" : true/false,
                        "can_see_other_account_metadata" : true/false,
                        "can_see_other_account_kind" : true/false,
                        "can_see_more_info" : true/false,
                        "can_see_url" : true/false,
                        "can_see_image_url" : true/false,
                        "can_see_open_corporates_url" : true/false,
                        "can_see_corporate_location" : true/false,
                        "can_see_physical_location" : true/false,
                        "can_see_public_alias" : true/false,
                        "can_see_private_alias" : true/false,
                        "can_add_more_info" : true/false,
                        "can_add_url" : true/false,
                        "can_add_image_url" : true/false,
                        "can_add_open_corporates_url" : true/false,
                        "can_add_corporate_location" : true/false,
                        "can_add_physical_location" : true/false,
                        "can_add_public_alias" : true/false,
                        "can_add_private_alias" : true/false,
                        "can_delete_corporate_location" : true/false,
                        "can_delete_physical_location" : true/false,
                        "can_edit_narrative" : true/false,
                        "can_add_comment" : true/false,
                        "can_delete_comment" : true/false,
                        "can_add_tag" : true/false,
                        "can_delete_tag" : true/false,
                        "can_add_image" : true/false,
                        "can_delete_image" : true/false,
                        "can_add_where_tag" : true/false,
                        "can_see_where_tag" : true/false,
                        "can_delete_where_tag" : true/false
                    }
                ],
                "bank_id":"the id of the bank where the account is hosted"
            }
        ]
    }

<a name="account"></a>>
#Account
*Baseline*

Information returned about an account specified by ACCOUNT_ID as moderated by the view (VIEW_ID) :

* Number
* Owners
* Type
* Balance
* IBAN
* Available views

More details about the data moderation by the view [here](#views).

OAuth authentication is required if the "is_public" field in view (VIEW_ID) is not set to "true".

NOTE: the view json returned from the API contains the field hide_metadata_if_alias, which does not match the field name of hide_metadata_if_alias_used that is expected when creating or updating a view. This is fixed in newer versions of the API.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/account

**Response:**
HTTP code: 200
Body:

    {
        "id": "A unique identifier used for ACCOUNT_ID",
        "label" : "account label e.g. TESOBE main account",
        "number": "account number (moderated by the view)",
        "owners": [
            {
                "id": "123213",
                "provider": "bank name",
                "display_name": "Name of person/Company/..."
            }
        ],
        "type": "e.g. current, savings",
        "balance": {
            "currency": "currency in ISO_4217 one of EUR/GBP,USD/...",
            "amount": "number or +/-"
        },
        "IBAN": "123080FZAFA9124AZE",
        "views_available": [
            {
                "id": "A unique identifier used for VIEW_ID",
                "short_name": "Public / Team / Auditors...",
                "description": "e.g. this is the public view of the TESOBE account",
                "is_public": "boolean. true if the public (user not logged in) can see this view."
                "which_alias_to_use": "public/private/none",
                "hide_metadata_if_alias" : true/false,
                "can_see_transaction_this_bank_account" : true/false,
                "can_see_transaction_other_bank_account" : true/false,
                "can_see_transaction_metadata" : true/false,
                "can_see_transaction_label" : true/false,
                "can_see_transaction_amount" : true/false,
                "can_see_transaction_type" : true/false,
                "can_see_transaction_currency" : true/false,
                "can_see_transaction_start_date" : true/false,
                "can_see_transaction_finish_date" : true/false,
                "can_see_transaction_balance" : true/false,
                "can_see_comments" : true/false,
                "can_see_narrative" : true/false,
                "can_see_tags" : true/false,
                "can_see_images" : true/false,
                "can_see_bank_account_owners" : true/false,
                "can_see_bank_account_type" : true/false,
                "can_see_bank_account_balance" : true/false,
                "can_see_bank_account_currency" : true/false,
                "can_see_bank_account_label" : true/false,
                "can_see_bank_account_national_identifier" : true/false,
                "can_see_bank_account_swift_bic" : true/false,
                "can_see_bank_account_iban" : true/false,
                "can_see_bank_account_number" : true/false,
                "can_see_bank_account_bank_name" : tru
                "can_see_other_account_national_identifier" : true/false,
                "can_see_other_account_swift_bic" : true/false,
                "can_see_other_account_iban" : true/false,
                "can_see_other_account_bank_name" : true/false,
                "can_see_other_account_number" : true/false,
                "can_see_other_account_metadata" : true/false,
                "can_see_other_account_kind" : true/false,
                "can_see_more_info" : true/false,
                "can_see_url" : true/false,
                "can_see_image_url" : true/false,
                "can_see_open_corporates_url" : true/false,
                "can_see_corporate_location" : true/false,
                "can_see_physical_location" : true/false,
                "can_see_public_alias" : true/false,
                "can_see_private_alias" : true/false,
                "can_add_more_info" : true/false,
                "can_add_url" : true/false,
                "can_add_image_url" : true/false,
                "can_add_open_corporates_url" : true/false,
                "can_add_corporate_location" : true/false,
                "can_add_physical_location" : true/false,
                "can_add_public_alias" : true/false,
                "can_add_private_alias" : true/false,
                "can_delete_corporate_location" : true/false,
                "can_delete_physical_location" : true/false,
                "can_edit_narrative" : true/false,
                "can_add_comment" : true/false,
                "can_delete_comment" : true/false,
                "can_add_tag" : true/false,
                "can_delete_tag" : true/false,
                "can_add_image" : true/false,
                "can_delete_image" : true/false,
                "can_add_where_tag" : true/false,
                "can_see_where_tag" : true/false,
                "can_delete_where_tag" : true/false
            }
        ],
        "bank_id":"the id of the bank where the account is hosted"
    }

<a name="views"></a>>
#Views

### How views work

Views on accounts and transactions filter the underlying data to hide or blur certain fields from certain users. For instance the balance on an account may be hidden from the public. The way to know what is possible on a view is determined in the following JSON.

**data:** When a view moderates a set of data, some fields my contain the value null rather than the original value. This indicates either that the user is not allowed to see the original data or the field is empty.

There is currently one exception to this rule, the "holder" field in the JSON contains always a value which is either an alias or the real name - indicated by the "is_alias" field.

**action:** When a user performs an action like trying to post a comment (with POST API call), if he is not allowed, the body repose will contains an error message.

**Metadata:**
Transaction metadata like (images, tags, comments, etc.) will appears *ONLY* on the view where they have been created e.g. comments posted to the public view only appear on the public view.

The other account metadata fields like image_URL, more_info, etc are unique through all the views. Example, if a user edits the "more_info" field in the "team" view, then the view "authorities" will show the new value (if it is allowed to do it).

#### all
*Optional*

Returns the list of the views created for account ACCOUNT_ID at BANK_ID.

OAuth authentication is required and the user needs to have access to the owner view.

NOTE: the view json returned from the API contains the field hide_metadata_if_alias, which does not match the field name of hide_metadata_if_alias_used that is expected when creating or updating a view. This is fixed in newer versions of the API.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/views

**Response:**
HTTP code: 200
Body:

    {
        "views": [
            {
                "id": "A unique identifier used for VIEW_ID",
                "short_name": "Public / Team / Auditors...",
                "description": "e.g. this is the public view of the TESOBE account",
                "is_public": "boolean. true if the public (user not logged in) can see this view."
                "which_alias_to_use": "public/private/none",
                "hide_metadata_if_alias" : true/false,
                "can_see_transaction_this_bank_account" : true/false,
                "can_see_transaction_other_bank_account" : true/false,
                "can_see_transaction_metadata" : true/false,
                "can_see_transaction_label" : true/false,
                "can_see_transaction_amount" : true/false,
                "can_see_transaction_type" : true/false,
                "can_see_transaction_currency" : true/false,
                "can_see_transaction_start_date" : true/false,
                "can_see_transaction_finish_date" : true/false,
                "can_see_transaction_balance" : true/false,
                "can_see_comments" : true/false,
                "can_see_narrative" : true/false,
                "can_see_tags" : true/false,
                "can_see_images" : true/false,
                "can_see_bank_account_owners" : true/false,
                "can_see_bank_account_type" : true/false,
                "can_see_bank_account_balance" : true/false,
                "can_see_bank_account_currency" : true/false,
                "can_see_bank_account_label" : true/false,
                "can_see_bank_account_national_identifier" : true/false,
                "can_see_bank_account_swift_bic" : true/false,
                "can_see_bank_account_iban" : true/false,
                "can_see_bank_account_number" : true/false,
                "can_see_bank_account_bank_name" : tru
                "can_see_other_account_national_identifier" : true/false,
                "can_see_other_account_swift_bic" : true/false,
                "can_see_other_account_iban" : true/false,
                "can_see_other_account_bank_name" : true/false,
                "can_see_other_account_number" : true/false,
                "can_see_other_account_metadata" : true/false,
                "can_see_other_account_kind" : true/false,
                "can_see_more_info" : true/false,
                "can_see_url" : true/false,
                "can_see_image_url" : true/false,
                "can_see_open_corporates_url" : true/false,
                "can_see_corporate_location" : true/false,
                "can_see_physical_location" : true/false,
                "can_see_public_alias" : true/false,
                "can_see_private_alias" : true/false,
                "can_add_more_info" : true/false,
                "can_add_url" : true/false,
                "can_add_image_url" : true/false,
                "can_add_open_corporates_url" : true/false,
                "can_add_corporate_location" : true/false,
                "can_add_physical_location" : true/false,
                "can_add_public_alias" : true/false,
                "can_add_private_alias" : true/false,
                "can_delete_corporate_location" : true/false,
                "can_delete_physical_location" : true/false,
                "can_edit_narrative" : true/false,
                "can_add_comment" : true/false,
                "can_delete_comment" : true/false,
                "can_add_tag" : true/false,
                "can_delete_tag" : true/false,
                "can_add_image" : true/false,
                "can_delete_image" : true/false,
                "can_add_where_tag" : true/false,
                "can_see_where_tag" : true/false,
                "can_delete_where_tag" : true/false
            }
        ]
    }

#### Create a view
*Optional*

Create a view on bank account

OAuth authentication is required and the user needs to have access to the owner view.

* The "alias" filed in the JSON can take there values:
_public_: to use the public alias if there is one specified for the other account.
_private_: to use the public alias if there is one specified for the other account.
_""(empty string)_: to use no alias, the view shows the real name of the other account.
* The "hide_metadata_if_alias_used" field in the JSON can take boolean values.
If it is set to true and there is an alias on the other account then the other accounts
metadata like more_info, url, image_url, open_corporates_url, etc will be hidden.
Otherwise the metadata will be shown.
* the "allowed_actions" field is a list containing the name of the actions allowed on this view,
all the actions contained will be set to true on the view creation, the rest will be set to false.

Here are the action names that the list can contain:

    can_see_transaction_this_bank_account
    can_see_transaction_other_bank_account
    can_see_transaction_label
    can_see_transaction_amount
    can_see_transaction_type
    can_see_transaction_currency
    can_see_transaction_start_date
    can_see_transaction_finish_date
    can_see_transaction_balance
    can_see_transaction_metadata
        can_see_narrative
        can_edit_narrative
        can_see_comments
        can_add_comment
        can_delete_comment
        can_see_tags
        can_add_tag
        can_delete_tag
        can_see_images
        can_add_image
        can_delete_image
        can_see_where_tag
        can_add_where_tag
        can_delete_where_tag
    can_see_bank_account_owners
    can_see_bank_account_type
    can_see_bank_account_balance
    can_see_bank_account_currency
    can_see_bank_account_label
    can_see_bank_account_national_identifier
    can_see_bank_account_swift_bic
    can_see_bank_account_iban
    can_see_bank_account_number
    can_see_bank_acother_account_national_identifier
    can_see_other_account_swift_bic
    can_see_other_account_iban
    can_see_other_account_bank_name
    can_see_other_account_number
    can_see_other_account_kind
    can_see_other_account_metadata
        can_see_more_info
        can_see_url
        can_see_image_url
        can_see_open_corporates_url
        can_see_corporate_location
        can_see_physical_location
        can_see_public_alias
        can_see_private_alias
        can_add_more_info
        can_add_url
        can_add_image_url
        can_add_open_corporates_url
        can_add_corporate_location
        can_add_physical_location
        can_add_public_alias
        can_add_private_alias
        can_delete_corporate_location
        can_delete_physical_location

**Notes:**
 - if the list contains some transaction metadata actions like can_see_narrative, can_add_comment, it **MUST** contain can_see_transaction_metadata.
 - if the list contains some other account metadata actions like can_see_more_info, can_see_physical_location, it **MUST** contain can_see_other_account_metadata.

NOTE: the view json returned from the API contains the field hide_metadata_if_alias, which does not match the field name of hide_metadata_if_alias_used that is expected when creating or updating a view. This is fixed in newer versions of the API.

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/views
Body:

    {
        "name": "the view name",
        "description": "a little description.",
        "is_public": "Boolean to specify if the view can be accessible to not logged in users",
        "which_alias_to_use": "public/private/none",
        "hide_metadata_if_alias_used" : true/false,
        "allowed_actions":  [
            "can_see_transaction_this_bank_account",
            "can_see_transaction_label",
            "can_see_transaction_other_bank_account"
        ]
    }

#### Update a view
*Optional*

Update an existing view on a bank account

OAuth authentication is required and the user needs to have access to the owner view.

The json sent is the same as during view creation (above), with one difference: the "name" field
of a view is not editable (it is only set when a view is created)

NOTE: the view json returned from the API contains the field hide_metadata_if_alias, which does not match the field name of hide_metadata_if_alias_used that is expected when creating or updating a view. This is fixed in newer versions of the API.

**Request:**
Verb: PUT
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/views/VIEW_ID
Body:

    {
        "description": "a little description.",
        "is_public": "Boolean to specify if the view can be accessible to not logged in users",
        "which_alias_to_use": "public/private/none",
        "hide_metadata_if_alias_used" : true/false,
        "allowed_actions":  [
            "can_see_transaction_this_bank_account",
            "can_see_transaction_label",
            "can_see_transaction_other_bank_account"
        ]
    }

**Response:**
HTTP code: 200
Body:

    {
        "id": "A unique identifier used for VIEW_ID",
        "short_name": "Public / Team / Auditors...",
        "description": "e.g. this is the public view of the TESOBE account",
        "is_public": "boolean. true if the public (user not logged in) can see this view."
        "which_alias_to_use": "public/private/none",
        "hide_metadata_if_alias_used" : true/false,
        "can_see_transaction_this_bank_account" : true/false,
        "can_see_transaction_other_bank_account" : true/false,
        "can_see_transaction_metadata" : true/false,
        "can_see_transaction_label" : true/false,
        "can_see_transaction_amount" : true/false,
        "can_see_transaction_type" : true/false,
        "can_see_transaction_currency" : true/false,
        "can_see_transaction_start_date" : true/false,
        "can_see_transaction_finish_date" : true/false,
        "can_see_transaction_balance" : true/false,
        "can_see_comments" : true/false,
        "can_see_narrative" : true/false,
        "can_see_tags" : true/false,
        "can_see_images" : true/false,
        "can_see_bank_account_owners" : true/false,
        "can_see_bank_account_type" : true/false,
        "can_see_bank_account_balance" : true/false,
        "can_see_bank_account_currency" : true/false,
        "can_see_bank_account_label" : true/false,
        "can_see_bank_account_national_identifier" : true/false,
        "can_see_bank_account_swift_bic" : true/false,
        "can_see_bank_account_iban" : true/false,
        "can_see_bank_account_number" : true/false,
        "can_see_bank_account_bank_nam
        "can_see_other_account_national_identifier" : true/false,
        "can_see_other_account_swift_bic" : true/false,
        "can_see_other_account_iban" : true/false,
        "can_see_other_account_bank_name" : true/false,
        "can_see_other_account_number" : true/false,
        "can_see_other_account_metadata" : true/false,
        "can_see_other_account_kind" : true/false,
        "can_see_more_info" : true/false,
        "can_see_url" : true/false,
        "can_see_image_url" : true/false,
        "can_see_open_corporates_url" : true/false,
        "can_see_corporate_location" : true/false,
        "can_see_physical_location" : true/false,
        "can_see_public_alias" : true/false,
        "can_see_private_alias" : true/false,
        "can_add_more_info" : true/false,
        "can_add_url" : true/false,
        "can_add_image_url" : true/false,
        "can_add_open_corporates_url" : true/false,
        "can_add_corporate_location" : true/false,
        "can_add_physical_location" : true/false,
        "can_add_public_alias" : true/false,
        "can_add_private_alias" : true/false,
        "can_delete_corporate_location" : true/false,
        "can_delete_physical_location" : true/false,
        "can_edit_narrative" : true/false,
        "can_add_comment" : true/false,
        "can_delete_comment" : true/false,
        "can_add_tag" : true/false,
        "can_delete_tag" : true/false,
        "can_add_image" : true/false,
        "can_delete_image" : true/false,
        "can_add_where_tag" : true/false,
        "can_see_where_tag" : true/false,
        "can_delete_where_tag" : true/false
    }


<a name="permissions"></a>>
#Permissions

*Optional*

Returns the list of the permissions at BANK_ID for account ACCOUNT_ID, with each time a pair composed of the user and the views that he has access to.

OAuth authentication is required and the user needs to have access to the owner view.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/permissions

**Response:**
HTTP code: 200
Body:

    {
        "permissions": [
            {
                "user": {
                        "display_name": "display name of user",
                        "id": "OBP UUID of the user making the comment",
                        "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter"
                },
                "views": [
                    {
                        "id": "A unique identifier used for VIEW_ID",
                        "short_name": "Public / Team / Auditors...",
                        "description": "e.g. this is the public view of the TESOBE account",
                        "is_public": "boolean. true if the public (user not logged in) can see this view."
                        "which_alias_to_use": "public/private/none",
                        "hide_metadata_if_alias_used" : true/false,
                        "can_see_transaction_this_bank_account" : true/false,
                        "can_see_transaction_other_bank_account" : true/false,
                        "can_see_transaction_metadata" : true/false,
                        "can_see_transaction_label" : true/false,
                        "can_see_transaction_amount" : true/false,
                        "can_see_transaction_type" : true/false,
                        "can_see_transaction_currency" : true/false,
                        "can_see_transaction_start_date" : true/false,
                        "can_see_transaction_finish_date" : true/false,
                        "can_see_transaction_balance" : true/false,
                        "can_see_comments" : true/false,
                        "can_see_narrative" : true/false,
                        "can_see_tags" : true/false,
                        "can_see_images" : true/false,
                        "can_see_bank_account_owners" : true/false,
                        "can_see_bank_account_type" : true/false,
                        "can_see_bank_account_balance" : true/false,
                        "can_see_bank_account_currency" : true/false,
                        "can_see_bank_account_label" : true/false,
                        "can_see_bank_account_national_identifier" : true/false,
                        "can_see_bank_account_swift_bic" : true/false,
                        "can_see_bank_account_iban" : true/false,
                        "can_see_bank_account_number" : true/false,
                        "can_see_bank_account_bank_name" : true/false,
                        "can_see_other_account_national_identifier" : true/false,
                        "can_see_other_account_swift_bic" : true/false,
                        "can_see_other_account_iban" : true/false,
                        "can_see_other_account_bank_name" : true/false,
                        "can_see_other_account_number" : true/false,
                        "can_see_other_account_metadata" : true/false,
                        "can_see_other_account_kind" : true/false,
                        "can_see_more_info" : true/false,
                        "can_see_url" : true/false,
                        "can_see_image_url" : true/false,
                        "can_see_open_corporates_url" : true/false,
                        "can_see_corporate_location" : true/false,
                        "can_see_physical_location" : true/false,
                        "can_see_public_alias" : true/false,
                        "can_see_private_alias" : true/false,
                        "can_add_more_info" : true/false,
                        "can_add_url" : true/false,
                        "can_add_image_url" : true/false,
                        "can_add_open_corporates_url" : true/false,
                        "can_add_corporate_location" : true/false,
                        "can_add_physical_location" : true/false,
                        "can_add_public_alias" : true/false,
                        "can_add_private_alias" : true/false,
                        "can_delete_corporate_location" : true/false,
                        "can_delete_physical_location" : true/false,
                        "can_edit_narrative" : true/false,
                        "can_add_comment" : true/false,
                        "can_delete_comment" : true/false,
                        "can_add_tag" : true/false,
                        "can_delete_tag" : true/false,
                        "can_add_image" : true/false,
                        "can_delete_image" : true/false,
                        "can_add_where_tag" : true/false,
                        "can_see_where_tag" : true/false,
                        "can_delete_where_tag" : true/false
                    }
                ]
            }
        ]
    }

<a name="permission"></a>>
#Permission
*Optional*

Returns the list of the views at BANK_ID for account ACCOUNT_ID that a USER_ID has access to.

OAuth authentication is required and the user needs to have access to the owner view.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/permissions/USER_ID

**Response:**
HTTP code: 200
Body:

    {
        "views": [
            {
                "id": "A unique identifier used for VIEW_ID",
                "short_name": "Public / Team / Auditors...",
                "description": "e.g. this is the public view of the TESOBE account",
                "is_public": "boolean. true if the public (user not logged in) can see this view."
                "which_alias_to_use": "public/private/none",
                "hide_metadata_if_alias_used" : true/false,
                "can_see_transaction_this_bank_account" : true/false,
                "can_see_transaction_other_bank_account" : true/false,
                "can_see_transaction_metadata" : true/false,
                "can_see_transaction_label" : true/false,
                "can_see_transaction_amount" : true/false,
                "can_see_transaction_type" : true/false,
                "can_see_transaction_currency" : true/false,
                "can_see_transaction_start_date" : true/false,
                "can_see_transaction_finish_date" : true/false,
                "can_see_transaction_balance" : true/false,
                "can_see_comments" : true/false,
                "can_see_narrative" : true/false,
                "can_see_tags" : true/false,
                "can_see_images" : true/false,
                "can_see_bank_account_owners" : true/false,
                "can_see_bank_account_type" : true/false,
                "can_see_bank_account_balance" : true/false,
                "can_see_bank_account_currency" : true/false,
                "can_see_bank_account_label" : true/false,
                "can_see_bank_account_national_identifier" : true/false,
                "can_see_bank_account_swift_bic" : true/false,
                "can_see_bank_account_iban" : true/false,
                "can_see_bank_account_number" : true/false,
                "can_see_bank_account_bank_name" : tru
                "can_see_other_account_national_identifier" : true/false,
                "can_see_other_account_swift_bic" : true/false,
                "can_see_other_account_iban" : true/false,
                "can_see_other_account_bank_name" : true/false,
                "can_see_other_account_number" : true/false,
                "can_see_other_account_metadata" : true/false,
                "can_see_other_account_kind" : true/false,
                "can_see_more_info" : true/false,
                "can_see_url" : true/false,
                "can_see_image_url" : true/false,
                "can_see_open_corporates_url" : true/false,
                "can_see_corporate_location" : true/false,
                "can_see_physical_location" : true/false,
                "can_see_public_alias" : true/false,
                "can_see_private_alias" : true/false,
                "can_add_more_info" : true/false,
                "can_add_url" : true/false,
                "can_add_image_url" : true/false,
                "can_add_open_corporates_url" : true/false,
                "can_add_corporate_location" : true/false,
                "can_add_physical_location" : true/false,
                "can_add_public_alias" : true/false,
                "can_add_private_alias" : true/false,
                "can_delete_corporate_location" : true/false,
                "can_delete_physical_location" : true/false,
                "can_edit_narrative" : true/false,
                "can_add_comment" : true/false,
                "can_delete_comment" : true/false,
                "can_add_tag" : true/false,
                "can_delete_tag" : true/false,
                "can_add_image" : true/false,
                "can_delete_image" : true/false,
                "can_add_where_tag" : true/false,
                "can_see_where_tag" : true/false,
                "can_delete_where_tag" : true/false
            }
        ]
    }

<a name="grant-permission"></a>>
#Grant Permission
#### One
*Optional*

Grants the user USER_ID access to the view VIEW_ID at BANK_ID for account ACCOUNT_ID.

OAuth authentication is required and the user needs to have access to the owner view.

Granting access to a public view will return an error message, as the user already has access.

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/permissions/USER_ID/views/VIEW_ID

**Response:**
HTTP code: 201
Body:

    {
        {
            "id": "A unique identifier used for VIEW_ID",
            "short_name": "Public / Team / Auditors...",
            "description": "e.g. this is the public view of the TESOBE account",
            "is_public": "boolean. true if the public (user not logged in) can see this view."
            "which_alias_to_use": "public/private/none",
            "hide_metadata_if_alias_used" : true/false,
            "can_see_transaction_this_bank_account" : true/false,
            "can_see_transaction_other_bank_account" : true/false,
            "can_see_transaction_metadata" : true/false,
            "can_see_transaction_label" : true/false,
            "can_see_transaction_amount" : true/false,
            "can_see_transaction_type" : true/false,
            "can_see_transaction_currency" : true/false,
            "can_see_transaction_start_date" : true/false,
            "can_see_transaction_finish_date" : true/false,
            "can_see_transaction_balance" : true/false,
            "can_see_comments" : true/false,
            "can_see_narrative" : true/false,
            "can_see_tags" : true/false,
            "can_see_images" : true/false,
            "can_see_bank_account_owners" : true/false,
            "can_see_bank_account_type" : true/false,
            "can_see_bank_account_balance" : true/false,
            "can_see_bank_account_currency" : true/false,
            "can_see_bank_account_label" : true/false,
            "can_see_bank_account_national_identifier" : true/false,
            "can_see_bank_account_swift_bic" : true/false,
            "can_see_bank_account_iban" : true/false,
            "can_see_bank_account_number" : true/false,
            "can_see_bank_account_bank_name" :
            "can_see_other_account_national_identifier" : true/false,
            "can_see_other_account_swift_bic" : true/false,
            "can_see_other_account_iban" : true/false,
            "can_see_other_account_bank_name" : true/false,
            "can_see_other_account_number" : true/false,
            "can_see_other_account_metadata" : true/false,
            "can_see_other_account_kind" : true/false,
            "can_see_more_info" : true/false,
            "can_see_url" : true/false,
            "can_see_image_url" : true/false,
            "can_see_open_corporates_url" : true/false,
            "can_see_corporate_location" : true/false,
            "can_see_physical_location" : true/false,
            "can_see_public_alias" : true/false,
            "can_see_private_alias" : true/false,
            "can_add_more_info" : true/false,
            "can_add_url" : true/false,
            "can_add_image_url" : true/false,
            "can_add_open_corporates_url" : true/false,
            "can_add_corporate_location" : true/false,
            "can_add_physical_location" : true/false,
            "can_add_public_alias" : true/false,
            "can_add_private_alias" : true/false,
            "can_delete_corporate_location" : true/false,
            "can_delete_physical_location" : true/false,
            "can_edit_narrative" : true/false,
            "can_add_comment" : true/false,
            "can_delete_comment" : true/false,
            "can_add_tag" : true/false,
            "can_delete_tag" : true/false,
            "can_add_image" : true/false,
            "can_delete_image" : true/false,
            "can_add_where_tag" : true/false,
            "can_see_where_tag" : true/false,
            "can_delete_where_tag" : true/false
        }
    }

#### Several
*Optional*

Grants the user USER_ID access to a list of views at BANK_ID for account ACCOUNT_ID.

OAuth authentication is required and the user needs to have access to the owner view.

**Request:**
Send a list of views Ids
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/permissions/USER_ID/views
Body:

    {
        "views" : [
            "view_one_id",
            "view_two_id",
            "view_theree_id"
        ]
    }
**Response:**
Returns the list the of the views granted access
HTTP code: 201
Body:

    {
        "views" : [
            {
                "id": "view_one_id",
                "short_name": "Public / Team / Auditors...",
                "description": "e.g. this is the public view of the TESOBE account",
                "is_public": "boolean. true if the public (user not logged in) can see this view."
                "which_alias_to_use": "public/private/none",
                "hide_metadata_if_alias_used" : true/false,
                "can_see_transaction_this_bank_account" : true/false,
                "can_see_transaction_other_bank_account" : true/false,
                "can_see_transaction_metadata" : true/false,
                "can_see_transaction_label" : true/false,
                "can_see_transaction_amount" : true/false,
                "can_see_transaction_type" : true/false,
                "can_see_transaction_currency" : true/false,
                "can_see_transaction_start_date" : true/false,
                "can_see_transaction_finish_date" : true/false,
                "can_see_transaction_balance" : true/false,
                "can_see_comments" : true/false,
                "can_see_narrative" : true/false,
                "can_see_tags" : true/false,
                "can_see_images" : true/false,
                "can_see_bank_account_owners" : true/false,
                "can_see_bank_account_type" : true/false,
                "can_see_bank_account_balance" : true/false,
                "can_see_bank_account_currency" : true/false,
                "can_see_bank_account_label" : true/false,
                "can_see_bank_account_national_identifier" : true/false,
                "can_see_bank_account_swift_bic" : true/false,
                "can_see_bank_account_iban" : true/false,
                "can_see_bank_account_number" : true/false,
                "can_see_bank_account_bank_name" : tru
                "can_see_other_account_national_identifier" : true/false,
                "can_see_other_account_swift_bic" : true/false,
                "can_see_other_account_iban" : true/false,
                "can_see_other_account_bank_name" : true/false,
                "can_see_other_account_number" : true/false,
                "can_see_other_account_metadata" : true/false,
                "can_see_other_account_kind" : true/false,
                "can_see_more_info" : true/false,
                "can_see_url" : true/false,
                "can_see_image_url" : true/false,
                "can_see_open_corporates_url" : true/false,
                "can_see_corporate_location" : true/false,
                "can_see_physical_location" : true/false,
                "can_see_public_alias" : true/false,
                "can_see_private_alias" : true/false,
                "can_add_more_info" : true/false,
                "can_add_url" : true/false,
                "can_add_image_url" : true/false,
                "can_add_open_corporates_url" : true/false,
                "can_add_corporate_location" : true/false,
                "can_add_physical_location" : true/false,
                "can_add_public_alias" : true/false,
                "can_add_private_alias" : true/false,
                "can_delete_corporate_location" : true/false,
                "can_delete_physical_location" : true/false,
                "can_edit_narrative" : true/false,
                "can_add_comment" : true/false,
                "can_delete_comment" : true/false,
                "can_add_tag" : true/false,
                "can_delete_tag" : true/false,
                "can_add_image" : true/false,
                "can_delete_image" : true/false,
                "can_add_where_tag" : true/false,
                "can_see_where_tag" : true/false,
                "can_delete_where_tag" : true/false
            },
            {
                "id": "view_two_id",
                "short_name": "Public / Team / Auditors...",
                "description": "e.g. this is the public view of the TESOBE account",
                "is_public": "boolean. true if the public (user not logged in) can see this view."
                "which_alias_to_use": "public/private/none",
                "hide_metadata_if_alias_used" : true/false,
                "can_see_transaction_this_bank_account" : true/false,
                "can_see_transaction_other_bank_account" : true/false,
                "can_see_transaction_metadata" : true/false,
                "can_see_transaction_label" : true/false,
                "can_see_transaction_amount" : true/false,
                "can_see_transaction_type" : true/false,
                "can_see_transaction_currency" : true/false,
                "can_see_transaction_start_date" : true/false,
                "can_see_transaction_finish_date" : true/false,
                "can_see_transaction_balance" : true/false,
                "can_see_comments" : true/false,
                "can_see_narrative" : true/false,
                "can_see_tags" : true/false,
                "can_see_images" : true/false,
                "can_see_bank_account_owners" : true/false,
                "can_see_bank_account_type" : true/false,
                "can_see_bank_account_balance" : true/false,
                "can_see_bank_account_currency" : true/false,
                "can_see_bank_account_label" : true/false,
                "can_see_bank_account_national_identifier" : true/false,
                "can_see_bank_account_swift_bic" : true/false,
                "can_see_bank_account_iban" : true/false,
                "can_see_bank_account_number" : true/false,
                "can_see_bank_account_bank_name" : tru
                "can_see_other_account_national_identifier" : true/false,
                "can_see_other_account_swift_bic" : true/false,
                "can_see_other_account_iban" : true/false,
                "can_see_other_account_bank_name" : true/false,
                "can_see_other_account_number" : true/false,
                "can_see_other_account_metadata" : true/false,
                "can_see_other_account_kind" : true/false,
                "can_see_more_info" : true/false,
                "can_see_url" : true/false,
                "can_see_image_url" : true/false,
                "can_see_open_corporates_url" : true/false,
                "can_see_corporate_location" : true/false,
                "can_see_physical_location" : true/false,
                "can_see_public_alias" : true/false,
                "can_see_private_alias" : true/false,
                "can_add_more_info" : true/false,
                "can_add_url" : true/false,
                "can_add_image_url" : true/false,
                "can_add_open_corporates_url" : true/false,
                "can_add_corporate_location" : true/false,
                "can_add_physical_location" : true/false,
                "can_add_public_alias" : true/false,
                "can_add_private_alias" : true/false,
                "can_delete_corporate_location" : true/false,
                "can_delete_physical_location" : true/false,
                "can_edit_narrative" : true/false,
                "can_add_comment" : true/false,
                "can_delete_comment" : true/false,
                "can_add_tag" : true/false,
                "can_delete_tag" : true/false,
                "can_add_image" : true/false,
                "can_delete_image" : true/false,
                "can_add_where_tag" : true/false,
                "can_see_where_tag" : true/false,
                "can_delete_where_tag" : true/false
            },
            {
                "id": "A unique identifier used for VIEW_ID",
                "short_name": "Public / Team / Auditors...",
                "description": "e.g. this is the public view of the TESOBE account",
                "is_public": "boolean. true if the public (user not logged in) can see this view."
                "which_alias_to_use": "public/private/none",
                "hide_metadata_if_alias_used" : true/false,
                "can_see_transaction_this_bank_account" : true/false,
                "can_see_transaction_other_bank_account" : true/false,
                "can_see_transaction_metadata" : true/false,
                "can_see_transaction_label" : true/false,
                "can_see_transaction_amount" : true/false,
                "can_see_transaction_type" : true/false,
                "can_see_transaction_currency" : true/false,
                "can_see_transaction_start_date" : true/false,
                "can_see_transaction_finish_date" : true/false,
                "can_see_transaction_balance" : true/false,
                "can_see_comments" : true/false,
                "can_see_narrative" : true/false,
                "can_see_tags" : true/false,
                "can_see_images" : true/false,
                "can_see_bank_account_owners" : true/false,
                "can_see_bank_account_type" : true/false,
                "can_see_bank_account_balance" : true/false,
                "can_see_bank_account_currency" : true/false,
                "can_see_bank_account_label" : true/false,
                "can_see_bank_account_national_identifier" : true/false,
                "can_see_bank_account_swift_bic" : true/false,
                "can_see_bank_account_iban" : true/false,
                "can_see_bank_account_number" : true/false,
                "can_see_bank_account_bank_name" : tru
                "can_see_other_account_national_identifier" : true/false,
                "can_see_other_account_swift_bic" : true/false,
                "can_see_other_account_iban" : true/false,
                "can_see_other_account_bank_name" : true/false,
                "can_see_other_account_number" : true/false,
                "can_see_other_account_metadata" : true/false,
                "can_see_other_account_kind" : true/false,
                "can_see_more_info" : true/false,
                "can_see_url" : true/false,
                "can_see_image_url" : true/false,
                "can_see_open_corporates_url" : true/false,
                "can_see_corporate_location" : true/false,
                "can_see_physical_location" : true/false,
                "can_see_public_alias" : true/false,
                "can_see_private_alias" : true/false,
                "can_add_more_info" : true/false,
                "can_add_url" : true/false,
                "can_add_image_url" : true/false,
                "can_add_open_corporates_url" : true/false,
                "can_add_corporate_location" : true/false,
                "can_add_physical_location" : true/false,
                "can_add_public_alias" : true/false,
                "can_add_private_alias" : true/false,
                "can_delete_corporate_location" : true/false,
                "can_delete_physical_location" : true/false,
                "can_edit_narrative" : true/false,
                "can_add_comment" : true/false,
                "can_delete_comment" : true/false,
                "can_add_tag" : true/false,
                "can_delete_tag" : true/false,
                "can_add_image" : true/false,
                "can_delete_image" : true/false,
                "can_add_where_tag" : true/false,
                "can_see_where_tag" : true/false,
                "can_delete_where_tag" : true/false
            }
        ]
    }

<a name="revoke-permission"></a>>
#Revoke Permission
#### one view
*Optional*

Revokes the user USER_ID access to the view VIEW_ID at BANK_ID for account ACCOUNT_ID.

Revoking a user access to a public view will return an error message.

OAuth authentication is required and the user needs to have access to the owner view.

**Request:**
Verb: DELETE
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/permissions/USER_ID/views/VIEW_ID

**Response:**
HTTP code: 204
Body: No content

#### all views
*Optional*

Revokes the user USER_ID access to all the views at BANK_ID for account ACCOUNT_ID.

OAuth authentication is required and the user needs to have access to the owner view.

**Request:**
Verb: DELETE
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/permissions/USER_ID/views

**Response:**
HTTP code: 204

<a name="account-other-accounts"></a>>
#Other Accounts
*Optional*

Returns data about all the other bank accounts that have shared at least one transaction with the ACCOUNT_ID at BANK_ID.

OAuth authentication is required if the view VIEW_ID is not public.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts

**Response:**
HTTP code: 200
Body:

    {
        "other_accounts": [
            {
                "id" : "the other account's id",
                "holder": {
                    "name": "DEUTSCHEPOSTAG, SSCACCS",
                    "is_alias": "true/false"
                },
                "number": "",
                "kind": "",
                "IBAN": "",
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
                                "id": "provider id of the user making the tag",
                                "display_name": "display name of user"
                            }
                    },
                    "physical_location": {
                            "latitude": 37.423021,
                            "longitude": -122.083739,
                            "date": "date of posting the geo tag",
                            "user": {
                                "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                                "id": "provider id of the user making the tag",
                                "display_name": "display name of user"
                            }
                    }
                }
            }
        ]
    }

<a name="account-other-account"></a>>
#Other Account
*Optional*

Returns data about one other bank account (OTHER_ACCOUNT_ID) that had shared at least one transaction with ACCOUNT_ID at BANK_ID.

OAuth authentication is required if the view is not public.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID

**Response:**
HTTP code: 200
Body:

    {
      "id" : "the other account's id",
        "holder": {
            "name": "DEUTSCHEPOSTAG, SSCACCS",
            "is_alias": "true/false"
        },
        "number": "",
        "kind": "",
        "IBAN": "",
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
                        "id": "provider id of the user making the tag",
                        "display_name": "display name of user"
                    }
            },
            "physical_location": {
                    "latitude": 37.423021,
                    "longitude": -122.083739,
                    "date": "date of posting the geo tag",
                    "user": {
                        "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                        "id": "provider id of the user making the tag",
                        "display_name": "display name of user"
                    }
            }
        }
    }


<a name="other_account-metadata"></a>>
###Other account metadata
*Optional*

Returns only the metadata about one other bank account (OTHER_ACCOUNT_ID) that had shared at least one transaction with ACCOUNT_ID at BANK_ID.

Authentication via OAuth is required if the view is not public.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata

**Response:**
HTTP code: 200
Body:

    {
        "public_alias":"the public alias of the other account holder",
        "private_alias":"the private alias of the other account holder",
        "more_info": "short text explaining who the other party of the transaction is",
        "URL": "a URL related to the other party e.g. the website of the company",
        "image_URL":"an image URL related to the other party e.g. company logo",
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service",
        "corporate_location": {
                "latitude": 37.423021,
                "longitude": -122.083739,
                "date": "date of posting the geo tag",
                "user": {
                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                    "id": "provider id of the user making the tag",
                    "display_name": "display name of user"
                }
        },
        "physical_location": {
                "latitude": 37.423021,
                "longitude": -122.083739,
                "date": "date of posting the geo tag",
                "user": {
                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                    "id": "provider id of the user making the tag",
                    "display_name": "display name of user"
                }
        }
    }
<a name="public-alias"></a>>
### Public Alias
####Get the Public Alias
*Optional*

Returns the public alias of the other account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/public_alias

**Response:**
HTTP code: 200
Body:

    {
        "alias" : "An alias to display instead of the real name"
    }

####Create a Public Alias
*Optional*

Creates the public alias for the other account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

Note: Public aliases are automatically generated for new "other accounts", so this call should only be used if
the public alias was deleted.

The VIEW_ID parameter should be a view the caller is permitted to access to and that has permission to create public aliases.

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/public_alias
Body:

    {
        "alias" : "An alias to display instead of the real name"
    }

**Response:**
HTTP code: 201
Body:

    {
        "success" : "public alias added"
    }

####Update the Public Alias

**PUT /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/public_alias**

*Optional*

Updates the public alias of the other account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

**Request:**
Verb: PUT
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/public_alias
Body:

    {
        "alias" : "An alias to display instead of the real name"
    }

**Response:**
HTTP code: 200
Body:

    {
        "success" : "public alias updated"
    }

####Delete the Public Alias
*Optional*

Deletes the public alias of the other account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

**Request:**
Verb: DELETE
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/public_alias

**Response:**
HTTP code: 204
Body: No Content

<a name="private-alias"></a>>
### Private Alias
####Get the Private Alias
*Optional*

Returns the private alias of the other account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/private_alias

**Response:**
HTTP code: 200
Body:

    {
        "alias" : "An alias to display instead of the real name"
    }

####Create the Private Alias
**POST /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/private_alias**
*Optional*

Creates an private alias for the other account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/private_alias
Body:

    {
        "alias" : "An alias to display instead of the real name"
    }

**Response:**
HTTP code: 201
Body:

    {
        "success" : "private alias added"
    }

####Update the Private Alias
*Optional*

Updates the private alias of the other account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

**Request:**
Verb: PUT
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/private_alias
Body:

    {
        "alias" : "An alias to display instead of the real name"
    }

**Response:**
HTTP code: 200
Body:

    {
        "success" : "private alias added"
    }


####Delete the Private Alias
*Optional*

Deletes the private alias of the other account OTHER_ACCOUNT_ID.

OAuth authentication is required if the view is not public.

**Request:**
Verb: DELETE
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/private_alias

**Response:**
HTTP code: 204
Body: No Content

<a name="more_info"></a>>
### More info
####Create
*Optional*

Saves data in the "more_info" field of the other account (OTHER_ACCOUNT_ID) metadata (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/more_info
Body:

    {
        "more_info": "short text explaining who is the other party of the transaction is"
    }
**Response:**
HTTP code: 201
Body:

    {
        "success": "more info added"
    }

####Update
*Optional*

Updates the data in the "more_info" field of the other account (OTHER_ACCOUNT_ID) metadata (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**
Verb: PUT
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/more_info
Body:

    {
        "more_info": "short text explaining who is the other party of the transaction is"
    }
**Response:**
HTTP code: 200
Body:

    {
        "success": "more info updated"
    }

####Delete
*Optional*

Deletes the data in the "more_info" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**
Verb: DELETE
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/more_info

**Response:**
HTTP code: 204
Body: No Content

<a name="URL"></a>>
### URL
####Create
*Optional*

Authentication via OAuth is required.

Saves data in the "URL" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/url
Body:

    {
        "URL": "a URL related to the other party e.g. the website of the company"
    }
**Response:**
HTTP code: 201
Body:

    {
        "success": "url added"
    }

####Update
*Optional*

Update data in the "URL" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**
Verb: PUT
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/url
Body:

    {
        "URL": "a URL related to the other party e.g. the website of the company"
    }
**Response:**
HTTP code: 200
Body:

    {
        "success": "url updated"
    }

####Delete
*Optional*

Deletes the data in the "URL" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**
Verb: DELETE
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/url

**Response:**
HTTP code: 204
Body: No Content

<a name="image_url"></a>>
### Image URL
####Create
*Optional*

Authentication via OAuth is required.

Saves data in the "image_url" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/image_url
Body:

    {
        "image_URL":"an image URL related to the other party e.g. company logo"
    }
**Response:**
HTTP code: 201
Body:

    {
        "success": "image url added"
    }

####Update
*Optional*

Update data in the "image_url" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**
Verb: PUT
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/image_url
Body:

    {
        "image_URL":"an image URL related to the other party e.g. company logo"
    }
**Response:**
HTTP code: 200
Body:

    {
        "success": "image url updated"
    }

####Delete
*Optional*

Deletes the data in the "image_url" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**
Verb: DELETE
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/image_url

**Response:**
HTTP code: 204
Body: No Content

<a name="opencorporates-URL"></a>>
### Open corporates URL
####Create
*Optional*

Authentication via OAuth is required.

Saves data in the "opencorporate-URL" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/open_corporates_url
Body:

    {
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service "
    }
**Response:**
HTTP code: 201
Body:

    {
        "success": "open corporate url added"
    }

####Update
*Optional*

Update data in the "opencorporate-URL" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**
Verb: PUT
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/open_corporates_url
Body:

    {
        "open_corporates_URL":"the company corporate URL in the http://opencorporates.com/ web service "
    }
**Response:**
HTTP code: 200
Body:

    {
        "success": "open corporate url updated"
    }

####Delete
*Optional*

Deletes the data in the "opencorporate-URL" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**
Verb: DELETE
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/open_corporates_url

**Response:**
HTTP code: 204
Body: No Content

<a name="corporate_location"></a>>
### Corporate location
####Create
*Optional*

Authentication via OAuth is required.

Saves data in the "corporate_location" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/public/other_accounts/OTHER_ACCOUNT_ID/metadata/corporate_location
Body:

    {
        "corporate_location": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }
**Response:**
HTTP code: 201
Body:

    {
        "success": "corporate location added"
    }

####Update
*Optional*

Update data in the "corporate_location" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**
Verb: PUT
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/corporate_location
Body:

    {
        "corporate_location": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }
**Response:**
HTTP code: 200
Body:

    {
        "success": "corporate location updated"
    }

####Delete
*Optional*

Deletes the data in the "corporate_location" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**
Verb: DELETE
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/corporate_location

**Response:**
HTTP code: 204
Body: No Content

<a name="physical_location"></a>>
### Physical location
####Create
*Optional*

Authentication via OAuth is required.

Saves data in the "physical_location" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/physical_location
Body:

    {
        "physical_location": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }
**Response:**
HTTP code: 201
Body:

    {
        "success": "physical location added"
    }

####Update
*Optional*

Update data in the "physical_location" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**
Verb: PUT
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/physical_location
Body:

    {
        "physical_location": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }
**Response:**
HTTP code: 200
Body:

    {
        "success": "physical location updated"
    }

####Delete
*Optional*

Deletes the data in the "physical_location" field of the other account (OTHER_ACCOUNT_ID) meta data (the other party involved in the transaction [here](#transaction))

Authentication via OAuth is required.

**Request:**
Verb: DELETE
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/other_accounts/OTHER_ACCOUNT_ID/metadata/physical_location

**Response:**
HTTP code: 204
Body: No Content

<a name="transactions"></a>>
#Transactions
*Baseline*

Returns transactions list of the account specified by ACCOUNT_ID and [moderated](#views) by the view (VIEW_ID).

Authentication via OAuth is required if the view is not public.

Possible custom headers for pagination:

* obp_sort_by=CRITERIA ==> default value: "completed" field
* obp_sort_direction=ASC/DESC ==> default value: DESC
* obp_limit=NUMBER ==> default value: 50
* obp_offset=NUMBER ==> default value: 0
* obp_from_date=DATE => default value: date of the oldest transaction registered
* obp_to_date=DATE => default value: date of the newest transaction registered

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions

**Response:**
HTTP code: 200
Body:

    {
        "transactions": [
            {
                "uuid": "A universally unique id e.g. 4f5745f4e4b095974c0eeead",
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
                                    "id": "provider id of the user making the tag",
                                    "display_name": "display name of user"
                                }
                        },
                        "physical_location": {
                                "latitude": 37.423021,
                                "longitude": -122.083739,
                                "date": "date of posting the geo tag",
                                "user": {
                                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                                    "id": "provider id of the user making the tag",
                                    "display_name": "display name of user"
                                }
                        }
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
                                "id": "OBP UUID of the user making the comment",
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
                                "id": "OBP UUID of the user making the comment",
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
                            "id": "provider id of the user making the tag",
                            "display_name": "display name of user"
                        }
                    }
                }
            }
        ]
    }

<a name="transaction"></a>>
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
                            "id": "provider id of the user making the tag",
                            "display_name": "display name of user"
                        }
                },
                "physical_location": {
                        "latitude": 37.423021,
                        "longitude": -122.083739,
                        "date": "date of posting the geo tag",
                        "user": {
                            "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                            "id": "provider id of the user making the tag",
                            "display_name": "display name of user"
                        }
                }
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
                        "id": "OBP UUID of the user making the comment",
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
                        "id": "OBP UUID of the user making the comment",
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
                    "id": "provider id of the user making the tag",
                    "display_name": "display name of user"
                }
            }
        }
    }

#Transaction Metadata
<a name="narrative"></a>>
### Narrative
#### Get
*Optional*

Returns the account owner description of the transaction [moderated](#views) by the view.

Authentication via OAuth is required if the view is not public.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/narrative

**Response:**
HTTP code: 200
Body:

    {
        "narrative" : "text explaining the purpose of the transaction"
    }

#### Create
*Optional*

Creates a description of the transaction TRANSACTION_ID.

Authentication via OAuth is required if the view is not public.

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/narrative
Body:

    {
        "narrative" : "text explaining the purpose of the transaction"
    }

**Response:**
HTTP code: 201
Body:

    {
        "success" : "narrative added"
    }

#### Update
*Optional*

Updates the description of the transaction TRANSACTION_ID.

Authentication via OAuth is required if the view is not public.

**Request:**
Verb: PUT
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/narrative
Body:

    {
        "narrative" : "text explaining the purpose of the transaction"
    }

**Response:**
HTTP code: 200
Body:

    {
        "success" : "narrative updated"
    }

####Delete
*Optional*

Deletes the description of the transaction TRANSACTION_ID.

Authentication via OAuth is required if the view is not public.

**Request:**
Verb: DELETE
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/narrative

**Response:**
HTTP code: 204
Body: No Content

<a name="comments"></a>>
### Comments
#### Get
*Optional*

Returns the transaction TRANSACTION_ID comments made on a [view](#views) (VIEW_ID).

Authentication via OAuth is required if the view is not public.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/comments

**Response:**
HTTP code: 200
Body:

    {
        "comments": [
            {
                "id": "id of the comment",
                "date": "date of posting the comment",
                "value": "the comment",
                "user": {
                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                    "id": "OBP UUID of the user making the comment",
                    "display_name": "display name of user"
                }
            }
        ]
    }

**Note**: user.provider + user.id is unique

#### Create
*Optional*

Posts a comment about a transaction TRANSACTION_ID on a [view](#views) VIEW_ID.

OAuth authentication is required since the comment is linked with the user.

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/comments
Body:

    {
        "value" : "the comment"
    }
**Response:**
HTTP code: 201
Body:

    {
        "id": "id of the comment",
        "date": "date of posting the comment",
        "value": "the comment",
        "user": {
            "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
            "id": "OBP UUID of the user making the comment",
            "display_name": "display name of user"
        }
    }

####Delete
*Optional*

Delete the comment COMMENT_ID about the transaction TRANSACTION_ID made on [view](#views).

Authentication via OAuth is required. The user must either have owner privileges for this account, or must be the user that posted the comment.

**Request:**
Verb: DELETE
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/comments/COMMENT_ID

**Response:**
HTTP code: 204
Body: No Content

<a name="tags"></a>>
### Tags
#### Get
*Optional*

Returns the transaction TRANSACTION_ID tags made on a [view](#views) (VIEW_ID).

Authentication via OAuth is required if the view is not public.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/tags

**Response:**
HTTP code: 200
Body:

    {
        "tags": [
            {
                "id": "id of the tag",
                "value": "thetag",
                "date": "date of posting the tag",
                "user": {
                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                    "id": "OBP UUID of the user making the comment",
                    "display_name": "display name of user"
                }
            }
        ]
    }

**Note**: user.provider + user.id is unique

#### Create
*Optional*

Posts a tag about a transaction TRANSACTION_ID on a [view](#views) VIEW_ID.

OAuth authentication is required since the tag is linked with the user.

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/tags
Body:

    {
        "value": "a_tag"
    }
**Response:**
HTTP code: 201
Body:

    {
        "id": "id of the tag",
        "value": "thetag",
        "date": "date of posting the tag",
        "user": {
            "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
            "id": "OBP UUID of the user making the comment",
            "display_name": "display name of user"
        }
    }

**Note**: the value on the tag MUST NOT contain a white space.
####Delete
*Optional*

Deletes the tag TAG_ID about the transaction TRANSACTION_ID made on [view](#views).

Authentication via OAuth is required. The user must either have owner privileges for this account, or must be the user that posted the tag.

**Request:**
Verb: DELETE
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/tags/TAG_ID

**Response:**
HTTP code: 204
Body: No Content

<a name="images"></a>>
### Images
#### Get
*Optional*

Returns the transaction TRANSACTION_ID images made on a [view](#views) (VIEW_ID).

Authentication via OAuth is required if the view is not public.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/images

**Response:**
HTTP code: 200
Body:

    {
        "images": [
            {
                "id": "1239qsxezad0123",
                "label": "cool image",
                "URL":"http://www.mysuperimage.com",
                "date": "date of posting the image",
                "user": {
                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                    "id": "OBP UUID of the user who have posted the image",
                    "display_name": "display name of user"
                }
            }
        ]
    }

**Note**: user.provider + user.id is unique

#### Create
*Optional*

Posts an image about a transaction TRANSACTION_ID on a [view](#views) VIEW_ID.

OAuth authentication is required since the image is linked with the user.

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/images
Body:

    "image": {
        "label": "cool image",
        "URL":"http://www.mysuperimage.com"
    }
**Response:**
HTTP code: 201
Body:

    {
        "id": "1239qsxezad0123",
        "label": "cool image",
        "URL":"http://www.mysuperimage.com",
        "date": "date of posting the image",
        "user": {
            "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
            "id": "OBP UUID of the user posting the image",
            "display_name": "display name of user"
        }
    }

####Delete
*Optional*

Deletes the image IMAGE_ID about the transaction TRANSACTION_ID made on [view](#views).

Authentication via OAuth is required. The user must either have owner privileges for this account, or must be the user that posted the image.

**Request:**
Verb: DELETE
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/images/IMAGE_ID

**Response:**
HTTP code: 204
Body: No Content

<a name="where"></a>>
### Where
#### Get
*Optional*

Returns the "where" Geo tag added to the transaction TRANSACTION_ID made on a [view](#views) (VIEW_ID).
It represents the location where the transaction has been initiated.

Authentication via OAuth is required if the view is not public.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/where

**Response:**
HTTP code: 200
Body:

    {
        "where": {
            "latitude": 37.423021,
            "longitude": -122.083739,
            "date": "date of posting the tag",
            "user": {
                "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                "id": "provider id of the user making the tag",
                "display_name": "display name of user"
            }
        }
    }

**Note**: user.provider + user.id is unique

#### Create
*Optional*

Creates a "where" Geo tag on a transaction TRANSACTION_ID in a [view](#views).

OAuth authentication is required since the geo tag is linked with the user.

**Request:**
Verb: POST
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/where
Body:

    {
        "where": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }
**Response:**
HTTP code: 201
Body:

    {
        "success": "where tag added"
    }

#### Update
*Optional*

Updates the "where" Geo tag on a transaction TRANSACTION_ID in a [view](#views).

OAuth authentication is required since the geo tag is linked with the user.

**Request:**
Verb: PUT
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/where
Body:

    {
        "where": {
            "latitude": 37.423021,
            "longitude": -122.083739
        }
    }
**Response:**
HTTP code: 201
Body:

    {
        "success": "where tag updated"
    }

####Delete
*Optional*

Deletes the where tag of the transaction TRANSACTION_ID made on [view](#views).

Authentication via OAuth is required. The user must either have owner privileges for this account, or must be the user that posted the geo tag.

**Request:**
Verb: DELETE
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/metadata/where

**Response:**
HTTP code: 204
Body: No Content

<a name="transaction_other_account"></a>>
#Other account
*Baseline*

Returns details of the other party involved in the transaction, moderated by the [view](#views) (VIEW_ID).

Authentication via OAuth is required if the view is not public.

**Request:**
Verb: GET
URL: /banks/BANK_ID/accounts/ACCOUNT_ID/VIEW_ID/transactions/TRANSACTION_ID/other_account

**Response:**
HTTP code: 200
Body:

    {
        "id" : "the other account's id",
        "holder": {
            "name": "DEUTSCHEPOSTAG, SSCACCS",
            "is_alias": "true/false"
        },
        "number": "",
        "kind": "",
        "IBAN": "",
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
                    "id": "provider id of the user making the tag",
                    "display_name": "display name of user"
                }
            },
            "physical_location": {
                "latitude": 37.423021,
                "longitude": -122.083739,
                "date": "date of posting the geo tag",
                "user": {
                    "provider": "name of party that authorized the user e.g. bank_name/facebook/twitter",
                    "id": "provider id of the user making the tag",
                    "display_name": "display name of user"
                }
            }
        }
    }