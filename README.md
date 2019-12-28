# wallapop_secret
Wallapop's API X-Signature Generator

In order to authenticate requests to Wallapop's REST API, you need a client side generated token, namely X-Signature, that gets generated from the current timestamp, a secret key, HTTP method used and the endpoint you are using.

The basic token structure is (pseudocode):
`Base64DecodeFromHex( HMACSHA256( Base64DecodeFromUTF8('UTI5dVozSmhkSE1zSUhsdmRTZDJaU0JtYjNWdVpDQnBkQ0VnUVhKbElIbHZkU0J5WldGa2VTQjBieUJxYjJsdUlIVnpQeUJxYjJKelFIZGhiR3hoY0c5d0xtTnZiUT09'), "/api/v3/{ENDPOINT}+#+{METHOD}+#+{TIMESTAMP}+#+") )`

The fully functional python code needed to generate the signature is the following:
```python
import hmac
import hashlib
import time
import base64
import codecs

def generate_xsignature(endpoint, method, timestamp):
    secret_key = b"UTI5dVozSmhkSE1zSUhsdmRTZDJaU0JtYjNWdVpDQnBkQ0VnUVhKbElIbHZkU0J5WldGa2VTQjBieUJxYjJsdUlIVnpQeUJxYjJKelFIZGhiR3hoY0c5d0xtTnZiUT09"
    total_params = b"/api/v3/"+ endpoint.encode() +b"+#+"+ method.encode() +b"+#+" + timestamp.encode() + b"+#+"
    signature= hmac.new(base64.b64decode(secret_key), total_params, hashlib.sha256).digest()
    return str(codecs.encode(signature, 'base64').decode()).strip()
```

The timestamp can be generated without problems using `time` as follows:
```python
timestamp = str(time.time()).split(".")[0]
```

Everything was reverse-engineered using Firefox Devtools, Frida and Charles Proxy
