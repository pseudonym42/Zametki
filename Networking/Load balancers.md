# Load balancers

---

Load balancers (LBs) as known distribute load between servers. LBs keep checking the health of the servers so to know which instances are available. There are different approaches used to make a decision to which of the available servers the request should go to. 
There are different load balancing approaches:
* Round Robin - this is when a request gets sent to a “next” server i.e. each server gets a request when its turn comes
* Least loaded, here LB sends a request to a server with the fewest number of requests i.e. least loaded, note that this approach is working slower as LB needs to make calculations to know which server is least loaded.
* IP Hash, in this case - user IP address is used to determine which server receives the request

**Sticky session**:
This principle is about allowing LB to keep doing its job but still make sure that the user session tokens can still be used. For example, say you have two web servers and a LB, a user logs in and gets a session token from server A and next request from the same user goes to server B, which did not generate the session token. If the tokens were kept locally by servers then the users will be forced to always re-login every time a request is sent to a server which does not have the session details about the user locally. That is why nowadays all the session data is shared and normally stored on some DB from where servers can get the session data to verify requests.

**SSL**
LBs can store and deal with SSL certificates so the servers do not have to do that. There are two main approaches here though:
SSL Termination is the practice of terminating/decrypting an SSL connection at the load balancer, and sending unencrypted connections to the backend servers. This means the load balancer is responsible for decrypting an SSL connection - a relatively slow and CPU intensive process. 
SSL Passthrough, which sends SSL connections directly to the servers. With SSL Passthrough, the SSL connection is terminated at each server, distributing the CPU load across those servers. However, you lose the ability to add or edit HTTP headers at the load balancer, as the connection is simply routed through the load balance. This means your application servers will lose the ability to get the X-Forwarded-* headers, which may include the client's IP address, port and scheme used

SSL Termination is the most popular approach, but sometimes for compliance reasons (e.g. HIPAA) you have to implement an end-to-end encryption then you either have to use SSL Passthrough which places limits on what you can do on your LB or use SSL Termination at the LB and then establish a new secure connection with a server to send the data further, note that it is a little bit slower overall because you have to establish a new secure connection and slightly more complex in configuration. Also you will need SSL certificate installed on the servers as well, which could be something like a self-signed cert since only the LB is going to see that certificate, not the client/user.


