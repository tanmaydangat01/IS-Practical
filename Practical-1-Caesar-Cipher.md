
## Code:

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
