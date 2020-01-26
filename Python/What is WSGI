# What is WSGI?
---


As a python web developer one comes across WSGI sooner or later. WSGI is a PEP 3333 standard i.e. it is a list of rules for web servers (e.g. Apache, Nginx etc) and python web applications (e.g. Flask, Django).

For web servers the WSGI standard identifies the rules on how they need to communicate to the python web applications and for the python web applications it identifies the rules for communicating with web servers. But why do we even need these rules?

Web servers cannot directly communicate to python applications, so community developed the standard. If you had all of the web servers WSGI compatible and all of the python web applications WSGI compatible then you could use any web server with any python web application. The only think you would need now is a WSGI server, which would get a request from a web server, pass it on to an application and wait for a response.

There are different types of WSGI servers, the most famous ones are uWSGI and Green Unicorn, there’s also a popular WSGI module called mod_wsgi which was developed for Apache server.

So how does this whole setup works? When an HTTP request for a dynamic content comes in to a web server, the web server passes it on the WSGI server, which creates an application consumable data in the form of object and a callable. The data is passed to the web application together with the callable. When the data is processed and a response is created then the application calls the passed callable to send the data back to the WSGI server.
