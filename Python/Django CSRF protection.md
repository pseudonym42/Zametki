# Django CSRF protection (Double-Submit Cookie pattern)

---

Django provides a middleware CSRF token (MCT) and a cookie CSRF token (CCT). MCT is handled by CSRF middleware and is what you store in `{% csrf_token %}` and the CCT is stored as browser cookies file.

CCT is safe and is Django’s default behaviour, but storing it in the session is common practice as well in other web frameworks and therefore sometimes demanded by security auditors. To store CCT in the session you need to set `CSRF_USE_SESSIONS` to True.
The way Django validates POST forms is that it compares CCT against MCT and only if they match the request gets validated, otherwise 403 is returned. Note that the tokens get generated on the base of secret string, but when delivered to client the tokens get ‘salted’ and this salt is removed before validation. That is why MCT and CCT values are different when you look at them on your browser b’cos they are just hashes of a secret and a salt. For security reasons, the value of the secret is changed each time a user logs in i.e. it gets changed from session to session. The MCT though is changed with every request because even though within the session the secret stays the same - the salt gets re-generated with every request so the MCT value changes.
While validating request - the tokens do not get compared against each other directly: the salt gets removed first and then the secrets, not the full tokens, are compared. This allows the use of ever-changing tokens. While each request may use its own token, the secret remains common to all (within one session). This check is done by CSRF middleware.
