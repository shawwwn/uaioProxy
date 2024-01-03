# µProxy
A minimal, memory-efficient HTTP/HTTPS proxy server designed to run in memory-constraint environments.\
Originally written for MicroPython, now compatible with CPython.

## Usage (MicroPython):

```py
import uasyncio as asyncio
import uproxy
proxy = uproxy.uProxy(ip='0.0.0.0', port=8765)
asyncio.run(proxy.run())
```

## Usage (CPython):

`cproxy.py` is a CPython-compatible wrapper of `uproxy.py` for running uproxy in console.

```
cproxy.py [-h] [-v] [--ip IP] [--port PORT] [--bind BIND]
          [--bufsize BUFSIZE] [--maxconns N] [--backlog M]
          [--timeout TIMEOUT] [--loglevel LOGLEVEL]
          [--auth AUTH] [--upstream UPSTREAM]
```

See [next section](#api-docs) for parameter usage.

```console
$ python3 cproxy.py --ip 0.0.0.0 --port 8765
Listening on 0.0.0.0:8765
CONNECT 192.168.1.230:54309     ==>     ifconfig.me:443
GET     192.168.1.230:54312     ==>     ifconfig.me:80
CONNECT 192.168.1.230:54315     ==>     www.google.com:443
```

To use `cproxy.py` in code:

```py
import asyncio
import cproxy
proxy = cproxy.uProxy()
asyncio.run(proxy.run())
```

## API docs:

* **`uproxy.uProxy(ip='0.0.0.0', port=8765, bind=None, bufsize=8192, maxconns=0, backlog=100, timeout=30, ssl=None, loglevel=1, acl_callback=None, auth=None, upstream=None)`**

  Initialize proxy server

  * **ip** - server ip
  * **port** - server port
  * **bind** - ip address for outgoing connections to bind to
  * **bufsize** - buffer size of each connection, in bytes
  * **maxconns** - max number of ***accepted*** connections server can handle, 0 to disable
  * **backlog** - max number of ***unaccepted*** connections waiting to be processed
  * **timeout** - connection timeout, in seconds
  * **loglevel** - log level (0-quiet, 1-info, 2-debug)
  * **ssl** - a SSLContext object to start a HTTPS server
  * **acl_callback** - access control callback function
  * **auth** - an 'user:password' pair that clients need to provide in order to authenticate with server
  * **upstream** - an 'ip:port' pair to connect to as an upstream HTTP proxy

* **`uProxy.run()`**

  Start proxy server.\
  Need to run in an asyncio event loop

* **`uProxy.acl_callback`**

  The access control callback function takes a 4-tuple input (source ip/port and destination ip/port).\
  Return `True` to allow current connection to pass, return `False` to block it.\
  Default value `None` means always allow all connection to pass.
  ```py
  def acl_callback(src_ip: str, src_port: int, dst_ip: str, dst_port: int) -> bool
  ```

## Other:

+ To use it with MicroPython, only need to copy `uproxy.py`.
+ To use it with CPython, copy both files and use `cproxy.py`.
+ `cproxy.py` replaces some core logic of `uproxy.py`, making it run much faster, at the expense of 2x memory consumption.
+ For detail usage, please refer to `examples/`

## Todo:
- [X] Authorization
- [X] Forward to upstream proxy
- [X] HTTPS server
