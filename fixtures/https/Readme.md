# Contents

- **rootCA.key** is the key used to self-signed rootCA.pem
- **rootCA.pem** is the certificate that will act as the **StackLight Root Authority**

The certificate has the following information:
```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 10160165599701850419 (0x8d0028c8355f5933)
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=FR, ST=Rhone-Alpes, L=Grenoble, O=Mirantis, OU=StackLight, CN=StackLight Root Authority/emailAddress=mirantis@example.com
        Validity
            Not Before: Jun 23 14:43:30 2016 GMT
            Not After : Oct 25 14:43:30 3015 GMT
        Subject: C=FR, ST=Rhone-Alpes, L=Grenoble, O=Mirantis, OU=StackLight, CN=StackLight Root Authority/emailAddress=mirantis@example.com
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (4096 bit)
    ...
```

# Client settings

To be able to validate the certificate generated by the **StackLight Root
Authority** you need to download it into your web browser or pass it to
the client by using the correct option.

# Create a certificate

Follow these steps to generate a new certificate that can be used to enable
HTTPS for the StackLight plugins.

- Generate the key for the plugin _my-plugin_.
```
openssl genrsa -out my-plugin.key 2048
```

- Create the certificate signing request.
```
openssl req -new -key my-plugin.key -out my-plugin.csr
```
Here is an example on how to fill the fields for the Grafana plugin where
the choosen FQDN is _grafana.fuel.local_:
```
Country Name (2 letter code) [AU]:FR
State or Province Name (full name) [Some-State]:Rhone-Alpes
Locality Name (eg, city) []:Grenoble
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Mirantis
Organizational Unit Name (eg, section) []:Fuel plugins
Common Name (e.g. server FQDN or YOUR name) []:grafana.fuel.local
Email Address []:mirantis@example.com
Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```
For _challenge password_ and _optional company_ just press enter.

- Sign it with the CA root key.
```
openssl x509 -req -in my-plugin.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out my-plugin.crt -days 500 -sha256
```

- Concatenate the certificate and the private key into a single file.
```
cat my-plugin.crt my-plugin.key > my-plugin.pem
```