# Practical 4: RSA-Algorithm

## Overview:
The RSA algorithm is an asymmetric cryptographic algorithm that uses a pair of keys (public and private) for encryption and decryption. This practical demonstrates key generation, encryption, and decryption using RSA

## Code:

```python
import random

def gen_keys():
    p = 17
    q = 23
    n = p * q
    phi_n = (p - 1) * (q - 1)
    e = random.randrange(2, phi_n)
    while gcd(e, phi_n) != 1:
        e = random.randrange(2, phi_n)
    d = modInverse(e, phi_n)
    return (e, n), (d, n)

def gcd(a, b):
    while b != 0:
        a, b = b, a % b
    return a

def modInverse(e, phi_n):
    for d in range(2, phi_n):
        if (e * d) % phi_n == 1:
            return d
    return -1

def encrypt(plaintext, public_key):
    e, n = public_key
    return [pow(ord(char), e, n) for char in plaintext]

def decrypt(ciphertext, private_key):
    d, n = private_key
    return ''.join(chr(pow(char, d, n)) for char in ciphertext)

public_key, private_key = gen_keys()
message = input("Enter message to encrypt: ")
cipher = encrypt(message, public_key)
decrypted_message = decrypt(cipher, private_key)
print("Public Key:", public_key)
print("Private Key:", private_key)
print("Cipher Text:", cipher)
print("Decrypted Text:", decrypted_message)
