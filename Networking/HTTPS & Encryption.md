# HTTPS / Encryption

---


When HTTP works with SSL/TLS for secure connection then it is abbreviated as HTTPS. To establish a secure connection between client and server there are 2 main methods: 
1) asymmetric encryption: used to privately share the session key this is done first and then
2) symmetric encryption: used to exchange the information securely

So throughout the whole session you use symmetric encryption while asymmetric encryption is used to establish a connection and share a session key. The process is as follows:

when browser sends a request to server it first agrees with server what is the agreed level of SSL/TLS to be used (client/browser sends a list of cipher suites that it supports and the server replies with the cipher suite that it has selected from the client's list), after that browser gets a digital certificate back from the server, this certificate is created by trusted CA (certificate authority, they get paid for verifying that server owner is genuine and then issue certificate) but owned by the server owner. So this certificate contains a few things (serial number, validity period etc) and among them the server public key and digital signature (hash fingerprint which also additionally encrypted using CA private key). The browser now decides either to trust the certificate straight away (it can do this as all browsers contain a list of certificates of the trustworthy CAs themselves -like Symantec, Comodo and GoDaddy with their corresponding public keys and hash fingerprints) or verify it using the provided digital signature. The signature is encrypted as said above using that authority private key, so can be decrypted by browser using authority public key and then it can be verified that it content of the certificate is genuine (by hashing the content of the certificate and comparing to the digital signature, if the same then genuine). Thus the browser makes sure that the certificate is trustworthy and hence is the server public key in the certificate is genuine. When browser is happy with the cert it generates the session key, encrypts it using public key from the server and sends it back. To decrypt something encrypted with a public key you need a private key, and this private key is only known to server. Now server decrypts the message and gets the session key. Now both server and client know the session key and can exchange the data securely just using this session key, this is where asymmetric encryption is finished and symmetric encryption is started.
https://www.youtube.com/watch?v=3p_e00tEZM8
http://robertheaton.com/2014/03/27/how-does-https-actually-work/

