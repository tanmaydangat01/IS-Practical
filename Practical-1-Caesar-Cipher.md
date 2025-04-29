IS-Practical: Caesar Cipher Implementation

Overview

This repository contains practical implementations of various cryptographic algorithms. This README.md focuses on the Caesar Cipher, a substitution cipher that shifts each letter in the plaintext by a fixed number of positions in the alphabet. The code allows both encryption and decryption with a user-defined shift value.

Practical 1: Caesar Cipher

Description

The Caesar Cipher shifts each alphabetic character by a specified number of positions while preserving case (uppercase/lowercase) and non-alphabetic characters. This implementation:





Takes user input for plaintext, shift value, and operation (encrypt/decrypt).



Displays the result and execution timestamp.

Code

Below is the Python code for the Caesar Cipher. You can copy it directly and run it in your Python environment.

import datetime

def caesar_cipher(text, shift, encrypt=True):
    result = ""
    if not encrypt:
        shift = -shift
    for char in text:
        if char.isalpha():
            shift_base = ord('A') if char.isupper() else ord('a')
            result += chr((ord(char) - shift_base + shift) % 26 + shift_base)
        else:
            result += char
    return result

plaintext = input("Enter text to encrypt or decrypt: ")
shift_value = int(input("Enter shift value (1-25): "))
operation = input("Enter e for encrypt and d for decrypt: ").lower()

if operation == 'e':
    encrypt = True
    result = caesar_cipher(plaintext, shift_value, encrypt)
    print("Encrypted Text:", result)
else:
    encrypt = False
    result = caesar_cipher(plaintext, shift_value, encrypt)
    print("Decrypted Text:", result)

print("Execution Date & Time:", datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S"))
