def create_playfair_matrix(key):
    # Create a 5x5 matrix with unique characters from the key and the alphabet
    alphabet = "ABCDEFGHIKLMNOPQRSTUVWXYZ"  # J is excluded
    key = key.upper().replace("J", "I")
    matrix = []
    used_chars = set()

    for char in key:
        if char not in used_chars and char in alphabet:
            matrix.append(char)
            used_chars.add(char)

    for char in alphabet:
        if char not in used_chars:
            matrix.append(char)

    return [matrix[i:i + 5] for i in range(0, 25, 5)]


def preprocess_text(text):
    # Remove non-alphabetic characters and make it uppercase
    text = text.upper().replace("J", "I")
    text = ''.join(filter(str.isalpha, text))

    # Insert X between repeating characters and add X if odd length
    i = 0
    result = []
    while i < len(text):
        result.append(text[i])
        if i + 1 < len(text) and text[i] == text[i + 1]:
            result.append('X')
        i += 1
    if len(result) % 2 != 0:
        result.append('X')

    return result


def find_position(matrix, char):
    for i, row in enumerate(matrix):
        if char in row:
            return i, row.index(char)


def playfair_encrypt_decrypt(text, matrix, encrypt=True):
    result = []
    step = 1 if encrypt else -1

    for i in range(0, len(text), 2):
        char1, char2 = text[i], text[i + 1]
        row1, col1 = find_position(matrix, char1)
        row2, col2 = find_position(matrix, char2)

        if row1 == row2:  # Same row
            result.append(matrix[row1][(col1 + step) % 5])
            result.append(matrix[row2][(col2 + step) % 5])
        elif col1 == col2:  # Same column
            result.append(matrix[(row1 + step) % 5][col1])
            result.append(matrix[(row2 + step) % 5][col2])
        else:  # Rectangle
            result.append(matrix[row1][col2])
            result.append(matrix[row2][col1])

    return ''.join(result)


# User Input
key = input("Enter the secret key: ")
plaintext = input("Enter text to encrypt: ")

# Create the matrix and preprocess plaintext
matrix = create_playfair_matrix(key)
processed_text = preprocess_text(plaintext)

# Encrypt and Decrypt
encrypted_text = playfair_encrypt_decrypt(processed_text, matrix, encrypt=True)
decrypted_text = playfair_encrypt_decrypt(encrypted_text, matrix, encrypt=False)

print("Encrypted Text:", encrypted_text)
print("Decrypted Text:", decrypted_text)
