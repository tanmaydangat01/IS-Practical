# Practical 5: DES-Algorithm

## Overview:
The Data Encryption Standard (DES) is a symmetric-key block cipher that encrypts data in 64-bit blocks using a 56-bit key. This practical demonstrates a simplified version of DES encryption and decryption.

## Code:

```python
import binascii

# Initial Permutation Table
IP = [58, 50, 42, 34, 26, 18, 10, 2,
      60, 52, 44, 36, 28, 20, 12, 4,
      62, 54, 46, 38, 30, 22, 14, 6,
      64, 56, 48, 40, 32, 24, 16, 8,
      57, 49, 41, 33, 25, 17, 9, 1,
      59, 51, 43, 35, 27, 19, 11, 3,
      61, 53, 45, 37, 29, 21, 13, 5,
      63, 55, 47, 39, 31, 23, 15, 7]

# Final Permutation Table (Inverse of Initial Permutation)
IP_INV = [40, 8, 48, 16, 56, 24, 64, 32,
          39, 7, 47, 15, 55, 23, 63, 31,
          38, 6, 46, 14, 54, 22, 62, 30,
          37, 5, 45, 13, 53, 21, 61, 29,
          36, 4, 44, 12, 52, 20, 60, 28,
          35, 3, 43, 11, 51, 19, 59, 27,
          34, 2, 42, 10, 50, 18, 58, 26,
          33, 1, 41, 9, 49, 17, 57, 25]

# Expansion Table (E)
E = [32, 1, 2, 3, 4, 5,
     4, 5, 6, 7, 8, 9,
     8, 9, 10, 11, 12, 13,
     12, 13, 14, 15, 16, 17,
     16, 17, 18, 19, 20, 21,
     20, 21, 22, 23, 24, 25,
     24, 25, 26, 27, 28, 29,
     28, 29, 30, 31, 32, 1]

# Permutation Table (P)
P = [16, 7, 20, 21, 29, 12, 28, 17,
     1, 15, 23, 26, 5, 18, 31, 10,
     2, 8, 24, 14, 32, 27, 3, 9,
     19, 13, 30, 6, 22, 11, 4, 25]

# Substitution Boxes (S-boxes)
S_BOX = [
    # S1
    [[14, 4, 13, 1, 2, 15, 11, 8, 3, 10, 6, 12, 5, 9, 0, 7],
     [0, 15, 7, 4, 14, 2, 13, 1, 10, 6, 12, 11, 9, 5, 3, 8],
     [4, 1, 14, 8, 13, 6, 2, 11, 15, 12, 9, 7, 3, 10, 5, 0],
     [15, 12, 8, 2, 4, 9, 1, 7, 5, 11, 3, 14, 10, 0, 6, 13]],
    # S2
    [[15, 1, 8, 14, 6, 11, 3, 4, 9, 7, 2, 13, 12, 0, 5, 10],
     [3, 13, 4, 7, 15, 2, 8, 14, 12, 0, 1, 10, 6, 9, 11, 5],
     [0, 14, 7, 11, 10, 4, 13, 1, 5, 8, 12, 6, 9, 3, 2, 15],
     [13, 8, 10, 1, 3, 15, 4, 2, 11, 6, 7, 12, 0, 5, 14, 9]],
    # S3
    [[10, 0, 9, 14, 6, 3, 15, 5, 1, 13, 12, 7, 11, 4, 2, 8],
     [13, 7, 0, 9, 3, 4, 6, 10, 2, 8, 5, 14, 12, 11, 15, 1],
     [13, 6, 4, 9, 8, 15, 3, 0, 11, 1, 2, 12, 5, 10, 14, 7],
     [1, 10, 13, 0, 6, 9, 8, 7, 4, 15, 14, 3, 11, 5, 2, 12]],
    # S4
    [[7, 13, 14, 3, 0, 6, 9, 10, 1, 2, 8, 5, 11, 12, 4, 15],
     [13, 8, 11, 5, 6, 15, 0, 3, 4, 7, 2, 12, 1, 10, 14, 9],
     [10, 6, 9, 0, 12, 11, 7, 13, 15, 1, 3, 14, 5, 2, 8, 4],
     [3, 15, 0, 6, 10, 1, 13, 8, 9, 4, 5, 11, 12, 7, 2, 14]],
    # S5
    [[2, 12, 4, 1, 7, 10, 11, 6, 8, 5, 3, 15, 13, 0, 14, 9],
     [14, 11, 2, 12, 4, 7, 13, 1, 5, 0, 15, 10, 3, 9, 8, 6],
     [4, 2, 1, 11, 10, 13, 7, 8, 15, 9, 12, 5, 6, 3, 0, 14],
     [11, 8, 12, 7, 1, 14, 2, 13, 6, 15, 0, 9, 10, 4, 5, 3]],
    # S6
    [[12, 1, 10, 15, 9, 2, 6, 8, 0, 13, 3, 4, 14, 7, 5, 11],
     [10, 15, 4, 2, 7, 12, 9, 5, 6, 1, 13, 14, 0, 11, 3, 8],
     [9, 14, 15, 5, 2, 8, 12, 3, 7, 0, 4, 10, 1, 13, 11, 6],
     [4, 3, 2, 12, 9, 5, 15, 10, 11, 14, 1, 7, 6, 0, 8, 13]],
    # S7
    [[4, 11, 2, 14, 15, 0, 8, 13, 3, 12, 9, 7, 5, 10, 6, 1],
     [13, 0, 11, 7, 4, 9, 1, 10, 14, 3, 5, 12, 2, 15, 8, 6],
     [1, 4, 11, 13, 12, 3, 7, 14, 10, 15, 6, 8, 0, 5, 9, 2],
     [6, 11, 13, 8, 1, 4, 10, 7, 9, 5, 0, 15, 14, 2, 3, 12]],
    # S8
    [[13, 2, 8, 4, 6, 15, 11, 1, 10, 9, 3, 14, 5, 0, 12, 7],
     [1, 15, 13, 8, 10, 3, 7, 4, 12, 5, 6, 11, 0, 14, 9, 2],
     [7, 11, 4, 1, 9, 12, 14, 2, 0, 6, 10, 13, 15, 3, 5, 8],
     [2, 1, 14, 7, 4, 10, 8, 13, 15, 12, 9, 0, 3, 5, 6, 11]]
]

# Permuted Choice 1 (PC-1)
PC_1 = [57, 49, 41, 33, 25, 17, 9,
        1, 58, 50, 42, 34, 26, 18,
        10, 2, 59, 51, 43, 35, 27,
        19, 11, 3, 60, 52, 44, 36,
        63, 55, 47, 39, 31, 23, 15,
        7, 62, 54, 46, 38, 30, 22,
        14, 6, 61, 53, 45, 37, 29,
        21, 13, 5, 28, 20, 12, 4]

# Permuted Choice 2 (PC-2)
PC_2 = [14, 17, 11, 24, 1, 5,
        3, 28, 15, 6, 21, 10,
        23, 19, 12, 4, 26, 8,
        16, 7, 27, 20, 13, 2,
        41, 52, 31, 37, 47, 55,
        30, 40, 51, 45, 33, 48,
        44, 49, 39, 56, 34, 53,
        46, 42, 50, 36, 29, 32]

# Number of left shifts for each round
SHIFT_SCHEDULE = [1, 1, 2, 2, 2, 2, 2, 2, 1, 2, 2, 2, 2, 2, 2, 1]

def hex_to_bin(hex_str, num_bits):
    """Convert hexadecimal string to binary string with leading zeros"""
    return bin(int(hex_str, 16))[2:].zfill(num_bits)

def bin_to_hex(bin_str):
    """Convert binary string to hexadecimal string"""
    return hex(int(bin_str, 2))[2:].upper()

def str_to_bin(text):
    """Convert string to binary representation"""
    return ''.join(format(ord(c), '08b') for c in text)

def bin_to_str(bin_str):
    """Convert binary string to text"""
    return ''.join(chr(int(bin_str[i:i+8], 2)) for i in range(0, len(bin_str), 8))

def pad_text(text):
    """Pad text to be a multiple of 64 bits (8 bytes) using PKCS#5 padding"""
    pad_len = 8 - (len(text) % 8)
    return text + chr(pad_len) * pad_len

def unpad_text(text):
    """Remove PKCS#5 padding from text"""
    pad_len = ord(text[-1])
    return text[:-pad_len]

def permute(block, table):
    """Permute the input block using the specified table"""
    return ''.join(block[i-1] for i in table)

def left_shift(bits, n):
    """Left shift the bits by n positions"""
    return bits[n:] + bits[:n]

def generate_subkeys(key):
    """Generate the 16 subkeys for each round of DES"""
    # Convert key to binary and permute using PC-1
    key_bin = hex_to_bin(key, 64)
    key_perm = permute(key_bin, PC_1)
    
    # Split into left and right halves
    left = key_perm[:28]
    right = key_perm[28:]
    
    subkeys = []
    for shift in SHIFT_SCHEDULE:
        # Left shift both halves
        left = left_shift(left, shift)
        right = left_shift(right, shift)
        
        # Combine and permute with PC-2 to get subkey
        combined = left + right
        subkey = permute(combined, PC_2)
        subkeys.append(subkey)
    
    return subkeys

def xor(bits1, bits2):
    """Perform XOR operation on two bit strings"""
    return ''.join(str(int(b1) ^ int(b2)) for b1, b2 in zip(bits1, bits2))

def f_function(right, subkey):
    """The Feistel function used in each round of DES"""
    # Expand right half from 32 to 48 bits
    expanded = permute(right, E)
    
    # XOR with subkey
    xored = xor(expanded, subkey)
    
    # Apply S-boxes
    s_box_output = ''
    for i in range(8):
        # Get 6-bit chunk
        chunk = xored[i*6 : (i+1)*6]
        row = int(chunk[0] + chunk[5], 2)
        col = int(chunk[1:5], 2)
        
        # Get S-box value (4 bits)
        val = S_BOX[i][row][col]
        s_box_output += format(val, '04b')
    
    # Permute with P table
    return permute(s_box_output, P)

def des_round(left, right, subkey):
    """Perform one round of DES encryption"""
    new_left = right
    new_right = xor(left, f_function(right, subkey))
    return new_left, new_right

def des_encrypt_block(block, subkeys):
    """Encrypt a single 64-bit block using DES"""
    # Initial permutation
    block = permute(block, IP)
    
    # Split into left and right halves
    left = block[:32]
    right = block[32:]
    
    # Perform 16 rounds
    for i in range(16):
        left, right = des_round(left, right, subkeys[i])
    
    # Combine and perform final permutation
    combined = right + left
    ciphertext = permute(combined, IP_INV)
    
    return ciphertext

def des_decrypt_block(block, subkeys):
    """Decrypt a single 64-bit block using DES"""
    # Initial permutation
    block = permute(block, IP)
    
    # Split into left and right halves
    left = block[:32]
    right = block[32:]
    
    # Perform 16 rounds in reverse order
    for i in range(15, -1, -1):
        left, right = des_round(left, right, subkeys[i])
    
    # Combine and perform final permutation
    combined = right + left
    plaintext = permute(combined, IP_INV)
    
    return plaintext

def des_encrypt(plaintext, key):
    """Encrypt plaintext using DES"""
    # Generate subkeys
    subkeys = generate_subkeys(key)
    
    # Convert plaintext to binary
    if isinstance(plaintext, str):
        plaintext = str_to_bin(pad_text(plaintext))
    else:
        plaintext = hex_to_bin(plaintext, 64)
    
    # Split into 64-bit blocks
    blocks = [plaintext[i:i+64] for i in range(0, len(plaintext), 64)]
    
    # Encrypt each block
    ciphertext = ''
    for block in blocks:
        # Pad block if it's less than 64 bits
        if len(block) < 64:
            block = block.ljust(64, '0')
        ciphertext += des_encrypt_block(block, subkeys)
    
    # Return as hex string
    return bin_to_hex(ciphertext)

def des_decrypt(ciphertext, key):
    """Decrypt ciphertext using DES"""
    # Generate subkeys
    subkeys = generate_subkeys(key)
    
    # Convert ciphertext to binary
    ciphertext = hex_to_bin(ciphertext, len(ciphertext)*4)
    
    # Split into 64-bit blocks
    blocks = [ciphertext[i:i+64] for i in range(0, len(ciphertext), 64)]
    
    # Decrypt each block
    plaintext = ''
    for block in blocks:
        plaintext += des_decrypt_block(block, subkeys)
    
    # Convert to text and unpad
    plaintext = bin_to_str(plaintext)
    return unpad_text(plaintext)

def main():
    print("DES Algorithm Implementation")
    print("=" * 30)
    
    # Example usage
    key = "133457799BBCDFF1"  # 64-bit key (56 bits + 8 parity bits)
    plaintext = input("Enter message to encrypt: ")
    
    print("\nOriginal Text:", plaintext)
    
    # Encrypt
    ciphertext = des_encrypt(plaintext, key)
    print("Encrypted (hex):", ciphertext)
    
    # Decrypt
    decrypted = des_decrypt(ciphertext, key)
    print("Decrypted Text:", decrypted)

if __name__ == "__main__":
    main()
