# Practical 1: Caesar Cipher

## Overview:
The Caesar Cipher is a substitution cipher that shifts each letter in the plaintext by a fixed number of positions down or up the alphabet. This practical demonstrates basic encryption and decryption using a user-defined shift key.

## Code:

```python
def caesar_cipher_enc(txt, shift):
    alphabet_lower = "abcdefghijklmnopqrstuvwxyz"
    alphabet_upper = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    encrypted_text = ""
    for char in txt:
        if char.islower():
            position = alphabet_lower.index(char)
            new_pos = (position + shift) % 26
            encrypted_text += alphabet_lower[new_pos]
        elif char.isupper():
            position = alphabet_upper.index(char)
            new_pos = (position + shift) % 26
            encrypted_text += alphabet_upper[new_pos]
        else:
            encrypted_text += char
    return encrypted_text

def caesar_cipher_dec(txt, shift):
    alphabet_lower = "abcdefghijklmnopqrstuvwxyz"
    alphabet_upper = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    decrypted_text = ""
    for char in txt:
        if char.islower():
            position = alphabet_lower.index(char)
            new_pos = (position - shift) % 26
            decrypted_text += alphabet_lower[new_pos]
        elif char.isupper():
            position = alphabet_upper.index(char)
            new_pos = (position - shift) % 26
            decrypted_text += alphabet_upper[new_pos]
        else:
            decrypted_text += char
    return decrypted_text

# Example usage
text = "Hello, World!"
shift = 3
encrypted = caesar_cipher_enc(text, shift)
decrypted = caesar_cipher_dec(encrypted, shift)
print("Original:", text)
print("Encrypted:", encrypted)
print("Decrypted:", decrypted)
