[update cert](https://serverfault.com/questions/394815/how-to-update-curl-ca-bundle-on-redhat)
---
```
yum update curl
yum install ca-certificates
update-ca-trust
curl -k https://curl.haxx.se/ca/cacert.pem -o /etc/pki/tls/certs/ca-bundle.crt
```
