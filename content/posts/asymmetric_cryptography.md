+++
title = 'Asymmetric Cryptography with Python'
tags = ["cryptography", "python"]
date = 2024-03-22
+++

### RSA (Rivest-Shamir-Adleman) Cryptography

RSA cryptography, named after its inventors Ron Rivest, Adi Shamir, and Leonard Adleman, is a widely used asymmetric encryption algorithm. It is a fundamental tool in modern cryptography and is used for secure data transmission, digital signatures, and various other cryptographic applications. RSA relies on the mathematical properties of prime numbers for its security.

Here's a simplified explanation of how RSA cryptography works:

#### Key Generation:
- The first step is to generate a pair of keys: a public key and a private key.
- The public key is meant to be shared with anyone who wants to send you encrypted data. It contains two components: the modulus (usually represented as 'n') and the public exponent (usually represented as 'e').
- The private key is kept secret and should never be shared. It contains the modulus and a private exponent (usually represented as 'd').

#### Encryption:
- When someone wants to send you an encrypted message, they use your public key to encrypt it.
- The encryption process involves converting the plaintext message into a numeric value, raising it to the power of 'e', and taking the result modulo 'n'.

#### Decryption:
- You, as the recipient, use your private key to decrypt the message.
- The decryption process involves taking the encrypted numeric value, raising it to the power of 'd', and taking the result modulo 'n'.

The following example will utilize the pycryptodome package. You can read more about the package here: [https://pycryptodome.readthedocs.io/en/latest/index.html](https://pycryptodome.readthedocs.io/en/latest/index.html)

You will likely need to install the package before usage. `$ pip install pycryptodome`

```python
# Importing necessary modules
from Crypto.Cipher import PKCS1_OAEP
from Crypto.PublicKey import RSA
from binascii import hexlify
```

`PKCS1_OAEP` is the RSA based cipher using OAEP (Optimal Asymmetric Encryption Padding) padding to bring in non-deterministic and more security to encryption. The `RSA` class is used to generate the public-private key pairs.

### Creating Our Keys

Using the RSA class, we will generate a random private key of length 1024-bits using the `generate()` function.

```python 
# Generating private key (RsaKey object) of key length of 1024 bits
private_key = RSA.generate(1024)
```

The public key is derived from the private key.

```python
# Generating the public key from the private key
public_key1 = private_key.publickey()
```

We can view the keys by converting them to strings.

```python
private_key_str = private_key.export_key().decode()
print(private_key_str)
```
```
-----BEGIN RSA PRIVATE KEY-----
MIICWwIBAAKBgQC5ZNpAtmb8P6o9dgOZdwS9uuYRj6VaKRMzVJZiVF/Nh4QFrC7A
776shyy8DiMJbiPxizz45OaHicQd5BDHa7Td8qwHmSgcWav1Kqenn6X6n7ULiQ2t
tXhmLGuCPVpiSkFWfZicoCt1jaeNyK2GFLwTqwgYWfbk2m6n2otrPeXvAQIDAQAB
AoGAUFVhNVVUfs1fiU5P9PnbthL8inOCJPVTepSWrXj+ImMsVADuKXA5YS0Zt0sw
528waAP7oaYeNnD96C3hD2iecCjVx5pwngC+Stup03ZyteO99Kx4rI5p53RNSGGd
w2XUcxHfwmryfjM/neOZa84q5o9cn05BMRAv8xfJNziluzkCQQDJ+zW1oTusz+OT
SlJM7aLbpzt+JFTTYnWpCr6SM/edFPBdaze/52hpuO4TypJ2aNpinrR+xcByG9Lu
JwMzPoKnAkEA6vn6uTW0gkV9jUxJCuhDFP1P3hQDfGVAj+I+36aGVlhUHIKGw9fd
RkekmYGLP+9DEgrR0/CMg90Iq5d06nZ+FwJANJsOARFORpMalakcyFZ4PTdQIml6
Alg5ht56hf+s9SeX9uzO51dw9WAp+dOf0+E5R8hIAGCm39FpXYehqL4WLwJAKc1d
AFQAj+hi5J88o1cckABclAqFcDznFnHOc6VBYt0F4aiK5w5hDB60tqZoKnCbQvtv
xr+Vj+Pjpfskzo1T8wJAHdw+znGu+BfZ8E12osvAhG3NdFtEmHbvypC+wPMM19ks
E7sMmAWeC7qVTPiCIjRcSzostSeGsZ7ojZEw1TpYLA==
-----END RSA PRIVATE KEY-----
```

```python
public_key_str = public_key1.export_key().decode()
print(public_key_str)
```
```
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC5ZNpAtmb8P6o9dgOZdwS9uuYR
j6VaKRMzVJZiVF/Nh4QFrC7A776shyy8DiMJbiPxizz45OaHicQd5BDHa7Td8qwH
mSgcWav1Kqenn6X6n7ULiQ2ttXhmLGuCPVpiSkFWfZicoCt1jaeNyK2GFLwTqwgY
Wfbk2m6n2otrPeXvAQIDAQAB
-----END PUBLIC KEY-----
```

### Store Our Keys as a File

Now we will store our keys in a file such that we can save, share, and manage them.

```python
#Writing down the private and public keys to 'key' files
with open('private.key', 'w') as private_file:
    private_file.write(private_key_str)
    
with open('public.key', 'w') as public_file:
    public_file.write(public_key_str)
```

### Importing the Stored Key

We can import the keys back as a `RsaKey` objects by reading the files and using the `import_key()` function.

```python
#Importing keys from files, converting it into the RsaKey object   
pr_key = RSA.import_key(open('private.key', 'r').read())
pu_key = RSA.import_key(open('public.key', 'r').read())
```

### Finally, the Encryption Part

Let's find a message to encrypt.

```python
# The message to be encrypted
message = b'Mike the Tiger is the mascot of Louisiana State University.  Boom!'
```

Instantiate an object from `PKCS1_OAEP.new()` by taking in the argument public key `pu_key` so as to encrypt the message with the public key of the receiver and later the receiver can decrypt the encrypted message using their private key.

```python
#Instantiating PKCS1_OAEP object with the public key for encryption
cipher = PKCS1_OAEP.new(key=pu_key)
#Encrypting the message with the PKCS1_OAEP object
cipher_text = cipher.encrypt(message)

print(hexlify(cipher_text))
```
```
b'1a460bfc2f4bc17d19f9be576bbe83cb47664279cbc8d20436757b0bc423b0f5d9849428de70f38be831813407b2306ae1d9c241411e5e41dfa5991ff6208fd92280c5439553162b9a7be42f5d9347c14f7e485793649a3a7a6824271ece81b470d4e23f4d8c293cc723a7680f995aee116620cc45bdced4943d61e86abda9a6'
```

The encrypted message is sent to receiver after the encrypting the message using the receiver’s public key. So, the receiver can decrypt the encrypted message using their private key.

For decryption, instantiate `new()` funciton from `PKCS1_OAEP` with the private key as the argument. With the `decrypt()` method, taking in the encrypted message as the argument, we can get the original message back as follows.

```python
#Instantiating PKCS1_OAEP object with the private key for decryption
decrypt = PKCS1_OAEP.new(key=pr_key)

#Decrypting the message with the PKCS1_OAEP object
decrypted_message = decrypt.decrypt(cipher_text)

print(decrypted_message)
```
```
b'Mike the Tiger is the mascot of Louisiana State University.  Boom!'
```