## DO THIS ALL FROM YOUR ROOT CA DIRECTORY
## DO THIS ALL FROM YOUR ROOT CA DIRECTORY
##Replace EXAMPLE_CERT WITH YOUR USERNAME



##GENERATE-PROTECTED-PRIVATE KEY##

 openssl genrsa -aes256 \
      -out intermediate/private/EXAMPLE_CERT.key.pem 4096
 chmod 400 intermediate/private/EXAMPLE_CERT.key.pem


 ## NON PROTECTED KEY FOR SHITTY DEVICES THAT YOU CANT PROTECT A PRVIATE KEY
 openssl genrsa \
      -out intermediate/private/EXAMPLE_CERT.key.pem 4096
 chmod 400 intermediate/private/EXAMPLE_CERT.key.pem
## CREATE SIGNING REQUEST

 cd /root/ca
	openssl req -config intermediate/openssl.cnf \
      -key intermediate/private/EXAMPLE_CERT.key.pem \
      -new -sha256 -out intermediate/csr/EXAMPLE_CERT.csr.pem

##Sign-PERSONALKEY

openssl ca -config intermediate/openssl.cnf \
-extensions usr_cert -days 375 -notext -md sha256 \
-in intermediate/csr/EXAMPLE_CERT.csr.pem \
-out intermediate/certs/EXAMPLE_CERT.cert.pem

##Sign SERVER KEY
	openssl ca -config intermediate/openssl.cnf \
-extensions server_cert -days 375 -notext -md sha256 \
-in intermediate/csr/EXAMPLE_CERT.csr.pem \
-out intermediate/certs/EXAMPLE_CERT.cert.pem

##sign-OCSP-KEY
 openssl ca -config intermediate/openssl.cnf \
      -extensions ocsp -days 375 -notext -md sha256 \
      -in intermediate/csr/EXAMPLE_CERT.csr.pem \
      -out intermediate/certs/EXAMPLE_CERT.cert.pem
##EXPORT AS .PFX

 openssl pkcs12 -export -out EXAMPLE_CERT.pfx -inkey intermediate/private/EXAMPLE_CERT.key.pem -in intermediate/certs/EXAMPLE_CERT.cert.pem -certfile intermediate/certs/intermediate.cert.pem

##EXPORT AS .PFX

openssl pkcs12 -export \
-in intermediate/certs/EXAMPLE_CERT.cert.pem \
-inkey intermediate/private/EXAMPLE_CERT.key.pem \
-out EXAMPLE_CERT.pfx \
-certfile intermediate/certs/intermediate.cert.pem
-in intermediate/csr/EXAMPLE_CERT.csr.pem \

##COPY TO WEB SHARE
 cp EXAMPLE_CERT.pfx /var/www/html/certs/

##REVOKE CERTIFICATE

	cd /root/ca
	openssl ca -config intermediate/openssl.cnf -revoke intermediate/certs/EXAMPLE_CERT.cert.pem


##RECREATE THE CRL AFTER YOU DESTROY CERTS

     cd /root/ca
	 openssl ca -config intermediate/openssl.cnf \
     -gencrl -out intermediate/crl/intermediate.crl.pem



##CHECK CRL

 openssl crl -in intermediate/crl/intermediate.crl.pem -noout -text

##EXPORT KEYSTORE to UNIFI

keytool -importkeystore -srckeystore unifi.pfx -srcstoretype PKCS12 -srcstorepass aircontrolenterprise \
-destkeystore /var/lib/unifi/keystore -storepass aircontrolenterprise

##DECRYPT-PRIVATE-KEY##

openssl rsa -in intermediate/private/EXAMPLE_CERT.key.pem -out intermediate/private/EXAMPLE_CERT.key.pem.DECRYPTED

## view contents of decrypted private key

nano intermediate/private/EXAMPLE_CERT.key.pem.DECRYPTED

##VERIFY OCSP

 openssl x509 -noout -text \
      -in intermediate/certs/EXAMPLE_CERT.cert.pem

    X509v3 Key Usage: critical
        Digital Signature
    X509v3 Extended Key Usage: critical
        OCSP Signing

##OCSP RESPONDER##
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
