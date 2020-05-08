# What is Cipher suite?
---

Cipher suite is a set of methods (technically known as algorithms) needed to secure a network connection through SSL (Secure Sockets Layer) / TLS (Transport Layer Security).
One cipher suite typically consists of one 1 key exchange, 1 authentication, 1 bulk encryption, and 1 MAC algorithm.

* key exchange algorithm: by which symmetric keys will be exchanged, e.g. RSA, DH, ECDH. So for e.g. client will be using RSA function to encrypt message+sym.key using server public key: RSA(pub key, message+sym.key) and server will be decrypting it using same algorithm but private key.
* authentication algorithm: how server authentication and client authentication will be carried out. For e.g. which algorithm client is going to use to prove the the server that the client is genuinely who saying he is, so if client server agreed to use ECDSA then client will use ECDSA algorithm to encrypt some data using private key so server could identify that the client is genuine by decrypting using the same algorithm and client’s public key
* bulk encryption algorithm: this is basically the symmetric key algorithm e.g. AES, 3DES etc
* Message Authentication Code (MAC) algorithm: basically the hash function which will be used to carry out data integrity checks, e.g. SHA, MD5 etc

Here's an example of a typical cipher suite:
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384.
* TLS simply indicates the protocol;
* ECDHE signifies the key exchange algorithm;
* ECDSA signifies the authentication algorithm;
* AES_256_CBC indicates the bulk encryption algorithm, here 256-bit AES operating in CBC (cipher block chaining) mode
* SHA384 indicates the MAC algorithm.
