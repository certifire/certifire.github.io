---
sort: 3
---

# ACME Orders

All these requests must be authenticated using a HTTP Basic Authentication header.<br>

- POST **/api/order**

    Creates a new ACME order for certificate issual with the ACME server.<br>
    The body must contain a JSON object that defines `domains` and `account` fields where, 
    `domains` is the list or domain names for which the certificate is to be issued (common name followed by SANs)
    and `account` is the ACME account id (from the JSON response with POST /api/acme during account creation).<br>
    Other optional fields are:<br>
    * type - Type o authentication to be used (dns/http), Default: dns
    * provider - DNS provider, Default: route53
    * email
    * organization
    * organizational_unit
    * country
    * state
    * location
    * csr - CSR for order creation, if not provided, new CSR will be generated from above data
    * key - RSA Private key to be used, if not provided, a new key will be generated
    * reissue - type boolean - if true, the order will be reissued with a new certificate

    email, organization, organizational_unit, country, state, location fields are used for CSR generation (if csr field not provided)
    for the order. If these fields are not provided, defaults will be used from the account data with wich the order is created.

    On new order creation, status code 201 is returned along with JSON object with `id` and `status` fileds
    denoting the order id and order status respectively. A `Location` header contains the URI of the new order.

    If the order with specified parameters already exists and you still try to create it, status code 200 is returned along with 
    JSON object in above mentioned format.

    If the `domains` and `account` fields are absent in the POST data, the request will be aborted with status code 400.

- GET **/api/order/&lt;int:id&gt;**

    Return data associated with order with given id.<br>
    On success a status code 200 is returned. The body of the response contains a JSON object with the requested order.<br>
    On failure status code 400 (bad request) is returned.

    Once the ordering process is finished in the background, the `resolved_cert_id` filed in the response JSON denotes
    the internal certificate id associated with the order. Then you can get the certificate using the /api/certificate/&lt;int:id&gt; endpoint.
    A `Location` header in the response contains the URI of the certificate.


- GET **/api/order**

    Return all the orders associated with the user account authenticating the request.<br>
    On success a status code 200 is returned. The body of the response contains a JSON object with the orders.<br>
    On failure status code 400 (bad request) is returned.

- COPY **/api/order/&lt;int:id&gt;**

    Reorders and Reissues certificates for the order with given id.<br>
    On success a status code 200 is returned.<br>
    On failure status code 400 (bad request) is returned.

