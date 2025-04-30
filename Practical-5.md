# Practical 5: DES-Algorithm

## Overview:
The Data Encryption Standard (DES) is a symmetric-key block cipher that encrypts data in 64-bit blocks using a 56-bit key. This practical demonstrates a simplified version of DES encryption and decryption.

## Code:

```python
def text_to_binary(text):
    return ''.join(format(ord(c), '08b') for c in text)

def binary_to_text(binary):
    return ''.join(chr(int(binary[i:i+8], 2)) for i in range(0, len(binary), 8))

def rotate_string(text, shift):
    return text[shift % len(text):] + text[:shift % len(text)]

def generate_round_keys(key):
    round_keys = []
    temp_key = key
    for i in range(16):
        temp_key = rotate_string(temp_key, (i + 1))
        round_keys.append(temp_key[:8])
    return round_keys

def feistel_function(right, round_key):
    binary_key = text_to_binary(round_key)[:len(right)]
    return ''.join(str(int(a) ^ int(b)) for a, b in zip(right, binary_key))

def simple_des_encrypt(plaintext, key):
    if len(key) != 8:
        raise ValueError("Key must be exactly 8 characters.")
    plaintext = plaintext.ljust(8)[:8]
    binary_text = text_to_binary(plaintext)
    left, right = binary_text[:32], binary_text[32:]
    round_keys = generate_round_keys(key)
    for rk in round_keys:
        left, right = right, ''.join(str(int(a) ^ int(b)) for a, b in zip(left, feistel_function(right, rk)))
    return binary_to_text(right + left), len(plaintext.strip())

def simple_des_decrypt(ciphertext, key, original_len):
    if len(key) != 8:
        raise ValueError("Key must be exactly 8 characters.")
    binary_text = text_to_binary(ciphertext)
    left, right = binary_text[:32], binary_text[32:]
    round_keys = generate_round_keys(key)[::-1]
    left, right = right, left
    for rk in round_keys:
        left, right = right, ''.join(str(int(a) ^ int(b)) for a, b in zip(left, feistel_function(right, rk)))
    return binary_to_text(right + left)[:original_len]

if __name__ == "__main__":
    key = input("Enter 8-character key: ")
    plaintext = input("Enter plaintext (up to 8 characters): ")
    encrypted, orig_len = simple_des_encrypt(plaintext, key)
    print("Encrypted:", encrypted)
    decrypted = simple_des_decrypt(encrypted, key, orig_len)
    print("Decrypted:", decrypted)
