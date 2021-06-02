I was asked how to generate the `public_key.pem` and `public_key_hex` from a `secret_key.pem` file. 

This is only for default ed25519 keys.

`openssl pkey -in secret_key.pem -pubout -out generated_public_key.pem`

`{ echo -n 01; openssl pkey -outform DER -pubout -in "secret_key.pem" | tail -c +13 | openssl base64 | openssl base64 -d | hexdump -ve '/1 "%02x" ' | tr -d "/n"; } > generated_public_key_hex`


