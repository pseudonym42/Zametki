# What are SOAP, REST and RPC?
---

There are a few standards and tools that are designed to achieve platform independent data exchange.

## What is the difference between SOAP and REST?

SOAP is a protocol and REST is an architectural style. So they cannot be really compared.

## SOAP

SOAP - Simple Object Access Protocol, REST - Representational State Transfer
SOAP is an XML-based protocol that lets you exchange data over a particular transportation protocol (e.g. HTTP, SMTP etc) between applications. SOAP wraps requests into SOAP messages, and then SOAP Server parses SOAP message to process it further. All information about the endpoint and request type are inside the message itself.


## REST

REST is not only be XML-based, most of the times it is JSON based (as JSONs are cheaper). REST uses HTTP only for all CRUD operations. REST uses HTTP verbs POST/GET/DELETE/UPDATE etc.

REST reads can be cached, SOAP based reads cannot be cached.

Both REST and SOAP support SSL but SOAP also supports WS-security and that is why SOAP is still used. SOAP is mostly used in the banking industry. As SOAP provides a standard implementation of data integrity and data privacy it does not mean though that it is more secure, it just supports WS-ReliableMessaging.

REST doesn’t have a standard messaging system and expects clients to deal with communication failures by retrying. SOAP has successful/retry logic built in. For example, if I was transferring money from one account to another, I would need to be certain that it completed. Retrying it could be catastrophic if it succeed the first time, but the response failed.

What is WSDL?

WSDL (Web Service Description Language) is an XML-based language and document that ends with `.wsdl` is an XML document that describes a web service and how to access it. It describes the messages, operations, and network transport information used by the service. These web services usually use SOAP, but may use other protocols. They way SOAP and WSDL related can be illustrated as for e.g. class and its methods, where SOAP is a class and WSDL is a list of all the available class methods.

## RPC

What can be compared is REST and RPC. RPC also can be XML or JSON based.

REST is more about resources (i.e. data you pass in your request - for example: name=Dave) and RPC is more about actions. REST operates via CRUD(create, read, update, delete) so it uses POST, GET, PUT, DELETE etc but RPC uses GET and POST. That is why: where REST style being used you could do DELETE /user?name=dave, but in RPC-like style you would POST/DeleteUser and add the user name in the XML/JSON format in the request body. So if you try to implement a large number of functions you might end up creating REST-ful JSON-RPC over HTTP.
