---
sort: 1
---

# Users and Authentication

- POST **/api/users**

    Register a new user.<br>
    The body must contain a JSON object that defines `username` and `password` fields.<br>
    On success a status code 201 is returned. The body of the response contains a JSON object with the newly added user. A `Location` header contains the URI of the new user.<br>
    On failure status code 400 (bad request) is returned.<br>
    Notes:
    - The password is hashed before it is stored in the database. Once hashed, the original password is discarded.
    - In a production deployment secure HTTP must be used to protect the password in transit.

- GET **/api/users/&lt;int:id&gt;**

    Return a user.<br>
    On success a status code 200 is returned. The body of the response contains a JSON object with the requested user.<br>
    On failure status code 400 (bad request) is returned.

- GET **/api/token**

    Return an authentication token.<br>
    This request must be authenticated using a HTTP Basic Authentication header.<br>
    On success a JSON object is returned with a field `token` set to the authentication token for the user and a field `duration` set to the (approximate) number of seconds the token is valid.<br>
    On failure status code 401 (unauthorized) is returned.

- GET **/api/resource**

    Return a protected resource. (Just to test the authentication). <br>
    This request must be authenticated using a HTTP Basic Authentication header. Instead of username and password, the client can provide a valid authentication token in the username field. If using an authentication token the password field is not used and can be set to any value.<br>
    On success a JSON object with data for the authenticated user is returned.<br>
    On failure status code 401 (unauthorized) is returned.

### Example

The following `curl` command registers a new user with username `admin` and password `changeme`:

    $ curl -u admin:changeme -i -X POST -H "Content-Type: application/json" -d '{"username":"hawk","password":"newpwd"}' https://api.certifire.xyz/api/users
    HTTP/1.1 201 CREATED
    Server: nginx/1.18.0 (Ubuntu)
    Date: Thu, 24 Dec 2020 02:33:36 GMT
    Content-Type: application/json
    Content-Length: 19
    Connection: keep-alive
    Location: http://api.certifire.xyz/api/users/2

    {"username":"hawk"}

These credentials can now be used to access protected resources:

    $ curl -u hawk:newpwd -i -X GET https://api.certifire.xyz/api/resource
    HTTP/1.1 200 OK
    Server: nginx/1.18.0 (Ubuntu)
    Date: Thu, 24 Dec 2020 02:37:35 GMT
    Content-Type: application/json
    Content-Length: 24
    Connection: keep-alive

    {"data":"Hello, hawk!"}

Using the wrong credentials the request is refused:

    $ curl -u hawk:loremipsum -i -X GET https://api.certifire.xyz/api/resource
    HTTP/1.1 401 UNAUTHORIZED
    Server: nginx/1.18.0 (Ubuntu)
    Date: Thu, 24 Dec 2020 02:38:54 GMT
    Content-Type: text/html; charset=utf-8
    Content-Length: 19
    Connection: keep-alive
    WWW-Authenticate: Basic realm="Authentication Required"

    Unauthorized Access

Finally, to avoid sending username and password with every request an authentication token can be requested:

    $ curl -u admin:changeme -i -X GET https://api.certifire.xyz/api/token
    HTTP/1.1 200 OK
    Server: nginx/1.18.0 (Ubuntu)
    Date: Thu, 24 Dec 2020 02:40:05 GMT
    Content-Type: application/json
    Content-Length: 153
    Connection: keep-alive
    token: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpZCI6MSwiZXhwIjoxNjA4Nzc4MjA1LjY1Njc2NzZ9.3zQ9CSAiu5pdVXc_cWaby1WGTpTTF-dCEjDpvOuvyxg

    {
        "duration":600,
        "token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpZCI6MSwiZXhwIjoxNjA4Nzc4MjA1LjY1Njc2NzZ9.3zQ9CSAiu5pdVXc_cWaby1WGTpTTF-dCEjDpvOuvyxg"
    }

And now during the token validity period there is no need to send username and password to authenticate anymore:

    $ curl -u eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpZCI6MSwiZXhwIjoxNjA4Nzc4MjA1LjY1Njc2NzZ9.3zQ9CSAiu5pdVXc_cWaby1WGTpTTF-dCEjDpvOuvyxg:x -i -X GET https://api.certifire.xyz/api/resource
    HTTP/1.1 200 OK
    Server: nginx/1.18.0 (Ubuntu)
    Date: Thu, 24 Dec 2020 02:42:29 GMT
    Content-Type: application/json
    Content-Length: 25
    Connection: keep-alive

    {"data":"Hello, admin!"}

Once the token expires it cannot be used anymore and the client needs to request a new one. Note that in this last example the password is arbitrarily set to `x`, since the password isn't used for token authentication.

An interesting side effect of this implementation is that it is possible to use an unexpired token as authentication to request a new token that extends the expiration time. This effectively allows the client to change from one token to the next and never need to send username and password after the initial token was obtained.

