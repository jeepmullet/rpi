# DO THIS ALL FROM YOUR ROOT CA DIRECTORY

#Replace EXAMPLE_CERT WITH YOUR USERNAME



## GENERATE-PROTECTED-PRIVATE KEY##
This is the method used to generate a user private/public keypair protected by a password.
This method is much better than using an unencrypted private key, and what you should usually use.
```
openssl genrsa -aes256 \
 -out intermediate/private/EXAMPLE_CERT.key.pem 4096
chmod 400 intermediate/private/EXAMPLE_CERT.key.pem
```
## NON PROTECTED KEY FOR SHITTY DEVICES THAT YOU CANT PROTECT A PRVIATE KEY
In some cases, the private key you are issuing cannot be protected by a password.
Use this command to create a private key without a password
```
openssl genrsa \
 -out intermediate/private/EXAMPLE_CERT.key.pem 4096
chmod 400 intermediate/private/EXAMPLE_CERT.key.pem
```
## CREATE SIGNING REQUEST
If you are creating a signing request for a personal key, the common name does not need to be a fully qualified domain name.
If you are creating a signing request for a server/website key the common name MUST be a fully qualified domain name
```
cd /root/ca
openssl req -config intermediate/openssl.cnf \
-key intermediate/private/EXAMPLE_CERT.key.pem \
-new -sha256 -out intermediate/csr/EXAMPLE_CERT.csr.pem
```
##Sign-PERSONALKEY
Use this to sign a user key not needed to identify a server
```
openssl ca -config intermediate/openssl.cnf \
-extensions usr_cert -days 375 -notext -md sha256 \
-in intermediate/csr/EXAMPLE_CERT.csr.pem \
-out intermediate/certs/EXAMPLE_CERT.cert.pem
```
##Sign SERVER KEY
Use this to sign a key for a server or website.  For example, your FreeRADIUS server would use this sort of key
```
openssl ca -config intermediate/openssl.cnf \
-extensions server_cert -days 375 -notext -md sha256 \
-in intermediate/csr/EXAMPLE_CERT.csr.pem \
-out intermediate/certs/EXAMPLE_CERT.cert.pem
```
##sign-OCSP-KEY
If you are going to create and use an OCSP responder, this is the syntax you need to use to sign the key for the OCSP respoder server
```
openssl ca -config intermediate/openssl.cnf \
-extensions ocsp -days 375 -notext -md sha256 \
-in intermediate/csr/EXAMPLE_CERT.csr.pem \
-out intermediate/certs/EXAMPLE_CERT.cert.pem
```
##EXPORT AS .PFX (to use with iOS devices or other devices that use .pfx files as profiles for connecting by identity)
To get an Apple iOS device to authenticate with a certificate you need to export the user certificate, certificate authority certificate, private key for the user and sometimes the whole certificate chain.
Use this command to export those all as one file.
```
openssl pkcs12 -export -out EXAMPLE_CERT.pfx -inkey intermediate/private/EXAMPLE_CERT.key.pem -in intermediate/certs/EXAMPLE_CERT.cert.pem -certfile intermediate/certs/intermediate.cert.pem
```
##EXPORT AS .PFX (to use with iOS devices or other devices that use .pfx files as profiles for connecting by identity)
```
openssl pkcs12 -export \
-in intermediate/certs/EXAMPLE_CERT.cert.pem \
-inkey intermediate/private/EXAMPLE_CERT.key.pem \
-out EXAMPLE_CERT.pfx \
-certfile intermediate/certs/intermediate.cert.pem
-in intermediate/csr/EXAMPLE_CERT.csr.pem \
```
##COPY TO WEB SHARE
If you want to distribute the keys internally over a web share this will copy the file to a place you can do that from, specifically yourdomain.com/certs
```
cp EXAMPLE_CERT.pfx /var/www/html/certs/
```
##REVOKE CERTIFICATE
```
cd /root/ca
openssl ca -config intermediate/openssl.cnf -revoke intermediate/certs/EXAMPLE_CERT.cert.pem
```

##RECREATE THE CRL AFTER YOU DESTROY CERTS
```
cd /root/ca
openssl ca -config intermediate/openssl.cnf \
-gencrl -out intermediate/crl/intermediate.crl.pem
```

##CHECK CRL
```
openssl crl -in intermediate/crl/intermediate.crl.pem -noout -text
```
##EXPORT KEYSTORE to UNIFI
If you have a Ubiquiti Unifi device and want to put your own server certificate on it, this command will put the cert in the right place and once you restart the Unifi controller will fix certificate errors
```
keytool -importkeystore -srckeystore EXAMPLE_CERT.pfx -srcstoretype PKCS12 -srcstorepass aircontrolenterprise \
-destkeystore /var/lib/unifi/keystore -storepass aircontrolenterprise
```
##DECRYPT-PRIVATE-KEY##
If you need to decrypt a private key this command will do it.
```
openssl rsa -in intermediate/private/EXAMPLE_CERT.key.pem -out intermediate/private/EXAMPLE_CERT.key.pem.DECRYPTED
```
## view contents of decrypted private key
```
nano intermediate/private/EXAMPLE_CERT.key.pem.DECRYPTED
```
##VERIFY OCSP
```
openssl x509 -noout -text \
-in intermediate/certs/EXAMPLE_CERT.cert.pem

X509v3 Key Usage: critical
Digital Signature
X509v3 Extended Key Usage: critical
OCSP Signing
```
##OCSP RESPONDER
````
openssl ocsp -port 127.0.0.1:2560 -text -sha256 \
-index intermediate/index.txt \
-CA intermediate/certs/ca-chain.cert.pem \
-rkey intermediate/private/EXAMPLE_CERT.key.pem \
-rsigner intermediate/certs/EXAMPLE_CERT.cert.pem \
-nrequest 1


openssl ocsp -CAfile intermediate/certs/ca-chain.cert.pem \
-url http://yourocspresponder -resp_text \
-issuer intermediate/certs/intermediate.cert.pem \
-cert intermediate/certs/EXAMPLE_CERT.cert.pem



openssl pkcs12 -export \
-in cert.cer \
-inkey key.pem \
-out certificate.pfx \
-certfile CA.cer



cd /root/ca
openssl genrsa -aes256 \  -out intermediate/private/EXAMPLE_CERT.key.pem 4096
chmod 400 intermediate/private/EXAMPLE_CERT.key.pem
openssl genrsa - \
-out intermediate/private/EXAMPLE_CERT.key.pem 4096
chmod 400 intermediate/private/EXAMPLE_CERT.key.pem


cd /root/ca
openssl genrsa -aes256 \
-out intermediate/private/EXAMPLE_CERT.key.pem 2048




cd /root/ca
openssl req -config intermediate/openssl.cnf \
-key intermediate/private/EXAMPLE_CERT.key.pem \
-new -sha256 -out intermediate/csr/EXAMPLE_CERT.csr.pem

openssl ca -config intermediate/openssl.cnf \
-extensions server_cert -days 375 -notext -md sha256 \
-in intermediate/csr/EXAMPLE_CERT.csr.pem \
-out intermediate/certs/EXAMPLE_CERT.cert.pem

openssl ca -config intermediate/openssl.cnf \
-extensions usr_cert -days 375 -notext -md sha256 \
-in intermediate/csr/EXAMPLE_CERT.csr.pem \
-out intermediate/certs/EXAMPLE_CERT.cert.pem
````
