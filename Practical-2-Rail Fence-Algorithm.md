# Practical 2: Rail-Fence-Cipher

## Overview:
The Rail Fence Cipher is a transposition cipher that arranges the plaintext in a zigzag pattern and reads it row-wise to produce the ciphertext. This practical demonstrates encryption and decryption using a specified depth.

## Code:

```python
import datetime

def rail_fence_encrypt(text, depth):
    if depth <= 1:
        return text

    rail = [['\n' for _ in range(len(text))] for _ in range(depth)]
    direction_down = False
    row, col = 0, 0

    for char in text:
        if row == 0 or row == depth - 1:
            direction_down = not direction_down
        rail[row][col] = char
        col += 1
        row += 1 if direction_down else -1

    encrypted_text = ''.join(char for row in rail for char in row if char != '\n')
    return encrypted_text

def rail_fence_decrypt(cipher_text, depth):
    if depth <= 1:
        return cipher_text

    rail = [['\n' for _ in range(len(cipher_text))] for _ in range(depth)]
    direction_down = None
    row, col = 0, 0

    for _ in range(len(cipher_text)):
        if row == 0:
            direction_down = True
        elif row == depth - 1:
            direction_down = False
        rail[row][col] = '*'
        col += 1
        row += 1 if direction_down else -1

    index = 0
    for i in range(depth):
        for j in range(len(cipher_text)):
            if rail[i][j] == '*' and index < len(cipher_text):
                rail[i][j] = cipher_text[index]
                index += 1

    result = []
    row, col = 0, 0
    for _ in range(len(cipher_text)):
        if row == 0:
            direction_down = True
        elif row == depth - 1:
            direction_down = False
        result.append(rail[row][col])
        col += 1
        row += 1 if direction_down else -1

    return ''.join(result)

# User Input
plaintext = input("Enter text to encrypt or decrypt: ")
depth = int(input("Enter depth (rails): "))
operation = input("Enter e for encrypt and d for decrypt: ").lower()

if operation == 'e':
    encrypted_text = rail_fence_encrypt(plaintext, depth)
    print(f"Encrypted Text: {encrypted_text}")
else:
    decrypted_text = rail_fence_decrypt(plaintext, depth)
    print(f"Decrypted Text: {decrypted_text}")

print("Execution Date & Time:", datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S"))
