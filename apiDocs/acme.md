---
sort: 2
---

# ACME Account

All these requests must be authenticated using a HTTP Basic Authentication header.<br>

- POST **/api/acme**

    Registers a new account with the specified ACME Authority with given parameters.<br>
    The body must contain a JSON object that defines `email` field.<br>
    Other vaild fields are:<br>
    * server - ACME Server URL, if not provided, uses default from config.py
    * organization
    * organizational_unit
    * country
    * state
    * location
    * key - RSA Private key to be used, if not provided, a new key will be generated

    organization, organizational_unit, country, state, location fields are used to set defaults for 
    CSR generation data for orders created from this account. If these fields are not provided, defaults will be used from config.py

    On new account creation, status code 201 is returned along with JSON object with `id` and `status` fileds
    denoting the account id and account status respectively. A `Location` header contains the URI of the new user.

    If the account already exists and you still try to create it, status code 200 is returned along with 
    JSON object in above mentioned format.

    If the `email` field is absent in the POST data, the request will be aborted with status code 400.

- GET **/api/acme/&lt;int:id&gt;**

    Return data associated with ACME account with given id.<br>
    On success a status code 200 is returned. The body of the response contains a JSON object with the requested account.<br>
    On failure status code 400 (bad request) is returned.

- GET **/api/acme**

    Return all the ACME accounts associated with the user account authenticating the request.<br>
    On success a status code 200 is returned. The body of the response contains a JSON object with the acme accounts.<br>
    On failure status code 400 (bad request) is returned.

- DELETE **/api/acme/&lt;int:id&gt;**

    Revokes all the certificates issued from this ACME account and deregisters the account from the authority ACME server.<br>
    On success a status code 200 is returned.<br>
    On failure status code 400 (bad request) is returned.
