# How Django deals with DB connections?

---

Nowadays each request that comes through web server (e.g. Apache) and a WSGI server to some django controller works like this (even though this setup can be changed in the web server and WSGI server config files):

* a new process is created by a web server for a connection and is kept alive, because of HTTP 1.1 when Keep-Alive was set to 1 min by default
* this process initiates a WSGI server, which can run one or multiple processes each with its own threads (* see note below)

Now a WSGI server thread calls a controller, which might make a DB call. If DB call is made then a DB connection is created and if django server configured correctly (in settings.py CONN_MAX_AGE is set to a positive number of seconds, default 0) then a persistent DB connection created. Persistent connections avoid the overhead of re-establishing a connection to the database in each request.
Django automatically opens a connection to the database whenever it needs one and doesn’t have one already — either because this is the first connection, or because the previous connection was closed. It keeps this connection open and reuses it in subsequent requests.



> \* - because of the multiple threads which share the same memory, sometimes it can cause an undesired behaviour: while a connection from a user to web server is still open, user might send an Ajax request which might need to access same API which is used by the controller but that API might cache the info and provide stale data. See the example below.

Imagine you have these 2 endpoints and callable that can be used by both main controller and the Ajax controller:

```python
import functools


@functools.lru_cache(maxsize=2)
def get_latest_data(params):
    # ...
    return data

def main(params):
    return get_latest_data(params)

def ajax(params):
    # below will return stale data, as the thread that runs this
    # function shares the same memory with the thread running above
    # controller, so if main() runs first it then makes get_latest_data
    # to cache results because of lru_cache, thus this ajax call will
    # return stale data
    return get_latest_data(params)
```
