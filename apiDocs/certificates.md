---
sort: 4
---

# Certificates

All these requests must be authenticated using a HTTP Basic Authentication header.<br>

- GET **/api/certificate/&lt;int:id&gt;**

    Return data associated with certificate with given id.<br>
    On success a status code 200 is returned. The body of the response contains a JSON object with the requested certificate.<br>
    On failure status code 400 (bad request) is returned.

- GET **/api/certificate**

    Return all the certificates associated with the user account authenticating the request.<br>
    On success a status code 200 is returned. The body of the response contains a JSON object with the certificates.<br>
    On failure status code 400 (bad request) is returned.

- DELETE **/api/certificate/&lt;int:id&gt;**

    Revokes the certificate.<br>
    On success a status code 200 is returned.<br>
    On failure status code 400 (bad request) is returned.

- PURGE **/api/certificate/&lt;int:id&gt;**

    Same as DELETE, Revokes the certificate, but also deletes all related data from internal database.<br>
    On success a status code 200 is returned.<br>
    On failure status code 400 (bad request) is returned.