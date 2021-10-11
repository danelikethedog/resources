# Openssl Commands and Concepts

## Useful Links

- [OpenSSL Command Docs](https://wiki.openssl.org/index.php/Command_Line_Utilities#Commands)

## Generating Hashes for Files

In order to generate a hash for a file you can use the following:

```bash
openssl dgst -<sha1><md5> <filename>
```

You can also use the explicit hash alg to generate the hash:

```bash
openssl <sha1><md5> <filename>
```

## Self Signed Cert

```bash
openssl ecparam -out device_ec_key.pem -name prime256v1 -genkey
openssl req -new -days 30 -nodes -x509 -key device_ec_key.pem -out device_ec_cert.pem -subj "/CN=foo_device"
openssl x509 -noout -text -in device_ec_cert.pem
cat device_ec_cert.pem device_ec_key.pem > device_cert_store.pem

openssl x509 -noout -fingerprint -in device_ec_cert.pem | sed 's/://g'| sed 's/\(SHA1 Fingerprint=\)//g' | tee fingerprint.txt
```

## CA

```bash
openssl ecparam -out <ca_key>_key.pem -name prime256v1 -genkey

openssl req -new -days 3650 -nodes -x509 -key <ca_name>_key.pem -out <name>_cert.pem -subj "/CN=CA Group 1"
```

## Validation - replace CN below:

```bash
openssl ecparam -out validation_ec_key.pem -name prime256v1 -genkey

openssl req -new -key validation_ec_key.pem -out validation_ec.csr -subj "/CN=12345"

openssl x509 -req -in validation_ec.csr -CA <ca_name>_cert.pem -CAkey <ca_key>_key.pem -CAcreateserial -out validation_ec_cert.pem -extensions client_auth -extfile ./x509_config.cfg -days 30 -sha256
```

## Intermediate 1

```bash
openssl ecparam -out i1_ec_key.pem -name prime256v1 -genkey

openssl req -new -key i1_ec_key.pem -out i1_ec.csr -subj "/CN=Intermediate 1 Group 1"

openssl x509 -req -in i1_ec.csr -CA <ca_name>_cert.pem -CAkey <ca_key>_key.pem -CAcreateserial -out i1_ec_cert.pem -days 3650 -sha256
```

## Intermediate 2

```bash
openssl ecparam -out i2_ec_key.pem -name prime256v1 -genkey

openssl req -new -key i2_ec_key.pem -out i2_ec.csr -subj "/CN=Intermediate 2 Group 1"

openssl x509 -req -in i2_ec.csr -CA i1_ec_cert.pem -CAkey i1_ec_key.pem -CAcreateserial -out i2_ec_cert.pem -days 3650 -sha256
```

## Device

```bash
openssl ecparam -out device_ec_key.pem -name prime256v1 -genkey

openssl req -new -key device_ec_key.pem -out device_ec.csr -subj "/CN=myDevice1"

openssl x509 -req -in device_ec.csr -CA i2_ec_cert.pem -CAkey i2_ec_key.pem -CAcreateserial -out device_ec_cert.pem -days 365 -sha256 -extensions client_auth -extfile ./x509_config.cfg
```

## Verify CSR: 

```bash
openssl req -text -noout -in i1_ec.csr
```
