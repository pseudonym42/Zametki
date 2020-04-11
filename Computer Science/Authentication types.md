# What is LLVM?
---

## Basic
When a user is signing up a password and user_id (this could be a username or email address) are submitted. Then a record in DB table (e.g. accounts) is created which contains user_id (username or email address, this is not DB id) and a password field, there could also be some other additional fields. The password field gets generated based on the hashing algorithm used by an app server, e.g. it could be SHA256. Once the processing is done the password field is saved to DB like this (example):

	algorithm$iterations$salt$hash

In the above example the algorithm is just a string identifying it e.g. “SHA256”, iterations is a number of iterations over the given algorithm, salt is a random hash-like string, hash is the final hash produced by the hashing function.

When user is logging in the password and user_id are submitted. Note that in the basic authentication this data is absolutely unprotected, so HTTPS must be used to protect it from man-in-the-middle attacks. The data is submitted over HTTPS in the POST request body. The body is encrypted. The data should be represented as shown below (this is specified in RFC 7617):

	Authorization: Basic <credentials>

where `<credentials>` is the base64 encoding of user_id and password joined by a single colon (:), for example user_id `some_user@gmail.com` and password `super123pass` would get converted like:

    some_user@gmail.com:super123pass

    ---(converted into Base64)--->

    c29tZV91c2VyQGdtYWlsLmNvbTpzdXBlcjEyM3Bhc3M=

Note that the above means that the user_id itself cannot contain a colon.

Once the above data is submitted to the app server. The server extracts the password and the `user_id` and looks up the user from the DB using the submitted `user_id`, If the user record is found - to authenticate the user the password needs to be verified. To do this the password field from the user record in the DB is taken. This field is going to look something like this (as explained above):

	algorithm$iterations$salt$hash

So the server now knows the algorithm, number of iterations and salt. So the server runs the password through the algorithm <iterations> times using <salt> salt. After this the produced hash is compared to the existing hash (which was originally taken from the DB). If the passwords match the user is allowed access.

## Session

There are a lot of different ways to implement session authentication. For example it could be in memory session tokens, tokens stored in cache, secure ‘cookies’. Tokens could also be stored as files - these would normally be considered as sticky sessions as only the server which authenticated you earlier can verify the user, thus load balancer needs to be aware of this and configured accordingly so the user gets redirected to the right physical server. However this approach is not recommended. The recommended approach is for the servers to share the common storage which would contain all users data. This storage is often a DB (it does not have to be Relational DBMS). Below we consider that user is using relational DB for storing sessions.

In session based authentication (once the user is authenticated) the server creates a token (hash) for the user and saves that token against the user in a separate DB table (e.g. session_keys). This table contains user_id (this is likely to be a FK to for e.g. accounts table) and session token (might be named session_id), table might contain other fields like token expiry for example. Client then received response which will contain the session key. This session key is then stored as a cookie on the client side (e.g. in the browser). Now whenever the browser client sends a request all of the cookies get submitted and the session among them. The server then looks up the user in the corresponding DB table (session_keys) and authenticates the client.


## Token

Token authentication is not the same as session authentication. Sessions (and cookies) are mainly meant for browsers where the browser will take care of sending the cookie with every request to the server. This why the CSRF protection is still needed for session authentication. Token authentication is meant to be used by REST services. There are different ways to generate and authenticate users with Token authentication.
Token for example could be stored in a separate DB table (e.g. tokens) and be used similar to session keys. For example Django Rest framework creates a tokens table and whenever a new token is generated it is stored in the DB against a user field. A token can be generated simply by converting random string to hexadecimal string. When token is submitted it could be simply looked up from the DB table.
But there are more secure ways to create a token and authenticate it. For example Django provides signing functionality. Signing/hashing an object (or a string) can allow you to store additional information inside the token and also allow using hashing. For example, to generate a token which needs to contain string “hello” one could use the following code:

    signing.dumps("hello")

Produces:

    'ImhlbGxvIgt1QaUZC:YIye-ze3TTx7gtSv422nZA4sgmk'

There are two components here, separated by a colon (similar to basic authentication explained above). The first component is a URLsafe base64 encoded string of whatever passed to dumps() i.e. in our case it is string “hello”, it could also be a dict object or something else. The second component is a base64 encoded HMAC/SHA1 hash. This hash consists of two components:

    $first_component:$hash

i.e. the first component and the hash separated by a colon, this hash is generated using django application secret. Note that this implementation does not store salt anywhere in the produced token so same salt is always used, which might be a security risk. To overcome this the default django signing mechanism needs to be overridden accordingly. To unsign and verify the token you would use the following:

    signing.loads('ImhlbGxvIgt1QaUZC:YIye-ze3TTx7gtSv422nZA4sgmk')

this checks the signature and returns the de-serialized object. If the signature fails, a BadSignature exception is raised.
