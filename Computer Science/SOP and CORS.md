# What are CSRF, SOP, CORS?

---


## CSRF
Imagine you have two websites open: mybank.com and evil.com. If you login to your bank and your session token for validating some transactions is just stored as cookie and there’s no CSRF token then a request can be forged (i.e. faked) from evil.com to your mybank.com to send some money to the evil.com owner. This could happen because the browser by default submits all the cookies that originate from a website when a request to that website is submitted. So the request would be valid (also notice here how browsers allow form submissions from any origin to any other origin, this is normal behaviour for a browser). This is where CSRF tokens get used, CSRF token is just a random hash which gets generated for each POST form and is included as a hidden input field value, its value gets validated against some secret string stored on server when that form is submitted. But CSRF token on its own would not protect from request forgery as an attacker from evil.com can steal or guess (which is highly unlikely though) the secret hash the server uses for generating CSRFs, and then just forge a valid POST request form. That is why additional mechanisms are required to protect users from attackers who attempt to submit fake forms - the CSRF protection needs “help” of SOP (same origin policy), which does NOT allow requests from other origins, we will talk about SOP below, but let’s see now at two main patterns for implementing CSRF protection.
Each of these patterns uses two tokens which get compared against each other: Double-Submit Cookie (DSC) and Synchronizer Token (ST).

Django uses DSC method and it relies on submission of 2 tokens and then comparing them against each other, read in more details in next chapter.
ST approach is the same as DSC but it stores the second token as session key (like a simple authorisation token) in the database, unlike DSC which stores it as browser cookie file on user machine.
Because ST relies on a database record instead of a cookie, it’s a bit more secure and not vulnerable to cookie modifying attack which could be done via XSS scripting attack. However one “site” (here site means all the pages delivered from same origin) cannot (in any of “normal” browsers) edit cookies set by another “site” directly, so either of the patterns is quite safe and if cookie modifying attack happens on website that uses DCS pattern then they should prevent XSS attack as it is more serious attack than request forgery.

# SOP
Now, what is SOP? SOP (same origin policy) protects the response data received from the server to be accessed by other website (cross-domain). Why this is important?
Imagine you visit a malicious website. This website has a Javascript which makes a GET request to `www.facebook.com/messages`. Since you are probably logged into Facebook, without SOP, this malicious Javascript would be able to read your Facebook messages. Another example with POST request: let’s refer to evil.com attacker again, imagine attacker manages to send a request to mybank.com because you did not implement the CSRF protection, even though the request is going to be successful (which is bad and purely down to not having CSRF implemented - so you need to make sure you always have CSRF protection for POST requests to fail forged requests) the response is not going to be available by default to the evil.com attacker thanks to SOP. The attacker is going to get:

    No <Access-Control-Allow-Origin> header is present on the requested resource

error. This is what SOP is about, to access the response data the request should come from the same origin so (even though web browsers allow cross-domain AJAX requests) you cannot access the response data by default (read further to see how to enable this).
Thus SOP forbids cross-domain read (by default): it will not allow the attacker to crawl the HTML in the response to get the token and also it will not allow the attacker to read data from a GET request (e.g. to request a page with CSRF token in it) which could be done just before making POST request and use that valid token (from the server response to the previous GET request) in the cross-domain AJAX request attack, because the attacker cannot read the data received from GET request. SOP also does not let the attacker embed iframe into attacker page so if the user is logged into mybank.com and evil.com has iframe which should display data from mybank.com (because user is logged in and has session cookie) then the data is not going to be available in that iframe, unless explicitly allowed by “X-Frame-Options” response header from the server.
Note that when you do an AJAX request (from your own page, same origin i.e. not attack)  that changes state on the server site (e.g. POST request) you do not have to (say in case of DSC pattern) send both tokens for validation with every AJAX POST request. What you can do is set a custom header and send only token from cookie. This is implemented for example in Django. This is done purely as a convenience to make AJAX requests easier from the same domain. The custom header that is used for that is `X-Requested-With` (or `X-Requested-By`), with value `XmlHttpRequest`. So on the server side the corresponding controller should have CSRF check enabled and it will check the token validity only (rather than comparing token secret values against each other) if `X-Requested-With` custom header is present.
The important thing to note here is that custom headers cannot be sent cross-domain (unless specifically enabled). Attempt to do so with a modern browser will trigger a CORS preflight request (see next section for more details), older browsers (IE 8 and 9) can send cross-domain requests, but custom headers are not supported at all. That is why you can “loose security” slightly when making AJAX POST requests from your own domain and send/validate only one token (e.g. cookie CSRF token in Django).

# CORS
So what is CORS, what are CORS preflight requests and how to enable cross-domain custom-headers and cross-domain response access?
CORS stands for Cross Origin Resource Sharing. First of all we need to make clear that CORS is just a browser policy (i.e. other clients ignore it) and SOP is just a security mechanism supported by a browser, and even though most modern browsers support it - the very important things to understand here are:

We rely here on browsers for implementing SOP securely
CORS is a relaxing of SOP and it only works on browsers

When you want to configure CORS support on a server then the only thing you need to do is to configure your server to send the <Access-Control-Allow-Origin> response header (and other CORS related response headers). This does not make the server block or not block any requests. The only thing this does is as said above it adds the specific headers. It is the client that uses them and acts accordingly, and as we said previously the only client that uses it is browser. So all clients (including browsers) continue to get responses from the server just as they normally do. But the difference is that curl or other native apps or some other client will not prevent your client code from accessing the response if it does not include the `<Access-Control-Allow-Origin>` response header, but browsers will. And as said earlier, even when you see an error in say your browser devtools that a cross-origin request from your frontend JavaScript code failed, you’ll still be able to see the response in browser devtools, but as was said earlier - browser can see the response but it will not expose it to your requesting JavaScript code from a different origin (i.e. different website e.g. evil.com).
Browsers only expose responses from cross-origin requests to your frontend code running at a particular origin if the server the request was sent to opts-in to allowing the request by responding with an `Access-Control-Allow-Origin` header that allows that origin.
So browsers are the only clients which implement the CORS protocol. So If you want to block requests to a resource from non-browser clients, you need to do it using something other than CORS configuration.

So when CORS used? Sometimes you need to enable it when you want to allow to make a cross-domain request and read the response, or if you host a CDN that provide JavaScript/CSS libraries and for people to use that CDN you need to allow the resources to be accessed cross-domain. If CORS is enabled on the server then cross-domain requests will be able to send requests with custom headers and read the cross-domain response data.

Now, what are CORS preflight requests? These are requests which browser sends before sending the actual request: if for example cross-domain AJAX request is being made and it is neither POST or GET (e.g. DELETE or PATCH) or cross-domain AJAX request being made with custom headers then CORS preflight request is sent first. These preflight requests are OPTIONS request which include `Origin` header which matches the `Origin` header value of the actual request, the server must verify if the origin allowed or not by including an `Access-Control-Allow-Origin` header which contains either * or the value of the preflight request “Origin” header value. If it doesn't, the browser simply discards the response and returns an error to the Javascript callback.