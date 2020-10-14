# File Encryptor
  - performed symmetric encryption and decryption
  - demonstrated CPA security
  - implemented password-based encryption
  - designed for changes in recommended key length and algorithms
  
### Part 1: Perform symmetric encryption and decryption
Extended the existing FileEncryptor.java to allow the user to specify:
- Encryption or decryption operation.
- The secret key in Base64 encoding (for decryption mode).
- Input file and output file paths

The encryption operation is indicated by the keyword enc and the decryption operation is indicated by the keyword dec.
A secret key is randomly generated and output using Base64 encoding (Base64 JavaDoc). The secret key can also be provided for decryption encoded using Base64.
The cipher mode will remain the same as in the example code AES/CBC/PKCS5PADDING.

### Encryption
```
javac FileEncryptor.java 
java FileEncryptor enc plaintext.txt ciphertext.enc 
```
Secret key is ((base64 encoded key))
IV is ((base 64 IV)

This will encrypt the file plaintext.txt as ciphertext.enc and printout the randomly
generated secret key encoded as a base64 string.

plaintext.txt
```This is some random text in plaintext.txt file
```
```
javac FileEncryptor.java 
java FileEncryptor enc plaintext.txt ciphertext.enc
```
output
```File successfully encrypted.
```

ciphertext.enc
```
AESAES/CBC/PKCS5PADDINGQ�3�f��0�
Ng�v}݅֙�@�]�!�5�#9披�p�Ϭ�ܖ�ExY�D�nW㏍J�f�eh�KY�,�?�}�@��myQ�~�:�˼�)\�3a͹�YmX��R
```

### Decryption
```
javac FileEncryptor.java 
java FileEncryptor dec ((base 64 encoded key)) ((base 64 IV)) ciphertext.enc plaintext.txt
```
This will decrypt the file ciphertext.enc as plaintext.txt.

ciphertext.enc
```
AESAES/CBC/PKCS5PADDINGQ�3�f��0� Ng�v}݅֙�@�]�!�5�#9披�p�Ϭ�ܖ�ExY�D�nW㏍J�f�eh�KY�,�?�}�@��myQ�~�:�˼�)\�3a͹�YmX��R
```
base64 encoded key  -> ```Y2hhcmxpZQ==```

```
javac FileEncryptor.java 
java FileEncryptor dec Y2hhcmxpZQ== ciphertext.enc plaintext.txt
```
output
```File successfully decrypted.```
plaintext.txt
```This is some random text in plaintext.txt file```


### Part 2: Demonstrate Chosen-plaintext (CPA) Security 
The code above incorporates an IV to provide chosen-plaintext (CPA) security for our files but because the key is randomly chosen it is impossible to demonstrate.
Extended my program as follows:
- Can specify the secret key as a base 64 string
- No longer need to specify the IV when decrypting.

### Encryption

```java FileEncryptor enc ((base 64 encoded key)) plaintext.txt ciphertext.enc
```
This will encrypt the file plaintext.txt as ciphertext.enc.

plaintext.txt
```This is some random text in plaintext.txt file```

base64 encoded key -> ```Y2hhcmxpZQ==```

```
javac FileEncryptor.java 
java FileEncryptor enc Y2hhcmxpZQ== plaintext.txt ciphertext.enc
```
output
```File successfully encrypted.```
ciphertext.enc
```
AESAES/CBC/PKCS5PADDINGQ�3�f��0�
Ng�v}݅֙�@�]�!�5�#9披�p�Ϭ�ܖ�ExY�D�nW㏍J�f�eh�KY�,�?�}�@��myQ�~�:�˼�)\�3a͹�YmX��R
```

### Decryption
```java FileEncryptor dec ((base 64 encoded key)) ciphertext.enc plaintext.txt```
This will decrypt the file ciphertext.enc as plaintext.txt
ciphertext.enc
```
AESAES/CBC/PKCS5PADDINGQ�3�f��0�
Ng�v}݅֙�@�]�!�5�#9披�p�Ϭ�ܖ�ExY�D�nW㏍J�f�eh�KY�,�?�}�@��myQ�~�:�˼�)\�3a͹�YmX��R
```
base64 encoded key -> ```Y2hhcmxpZQ==```

```
javac FileEncryptor.java 
java FileEncryptor dec Y2hhcmxpZQ== ciphertext.enc plaintext.txt
```
output
```File successfully decrypted.```
plaintext.txt
```This is some random text in plaintext.txt file```

### Part 3: Generating a Secret Key from a Password
Keys are hard to remember so using a password is a better option. You could generate a  directly from this but the user might choose a password with low entropy making defeating the encryption scheme trivial. A better approach is password-based encryption where you add salt, iterate, and hash repeatedly. Java provides algorithms to make this easy, see here for a code example. This follows NIST recommendations (found here PBKDF2 and here RFC2898). You should also read the OWASP advice on password storage. This requires adding an additional option to FileEncryptor to allow a password to be specified. We also ask that you continue to output the secret key as generated from the password for marking purposes.

### Encryption
```java FileEncryptor enc "my password" plaintext.txt ciphertext.enc```
This will encrypt the file plaintext.txt as ciphertext.enc to derive a secret key from "my password" and printout the randomly generated secret key encoded as a base64 string.

plaintext.txt
```This is some random text in plaintext.txt file```
my password
```password123```
```
javac FileEncryptor.java 
java FileEncryptor enc password123 plaintext.txt ciphertext.enc
```
output
```File successfully encrypted.```
ciphertext.enc
```AESAES/CBC/PKCS5PADDINGշ�g�pd^e�"�&�F`$̼�ׁ�ćxR�/.��e�ק�_�3�>94PA#�ԥ� W[(�"�p-G�z4�"�&8�4��>|��R```

### Decryption
```java FileEncryptor dec "my password" ciphertext.enc plaintext.txt```
This will decrypt the file ciphertext.enc as plaintext.txt using a secret key generated from "my password".

ciphertext.enc
```AESAES/CBC/PKCS5PADDINGշ�g�pd^e�"�&�F`$̼�ׁ�ćxR�/.��e�ק�_�3�>94PA#�ԥ� W[(�"�p-G�z4�"�&8�4��>|�R```

my password
```password123```
```
javac FileEncryptor.java 
java FileEncryptor dec password123 ciphertext.enc plaintext.txt
```
output
```File successfully decrypted.```
plaintext.txt
```This is some random text in plaintext.txt file```

### Part 4: Designing for changes in recommended key length and algorithms

Algorithms and recommended key lengths change over time but you may have legacy files on
your system.
Extended my program to:
- Allow the user to specify the algorithm (Blowfish and AES) as well as the key length.
- Embed metadata recording the algorithm and key length used to encrypt the file.
- Use the metadata when decrypting to pick the correct algorithm and key length to use.
- Allow the user to query the metadata embedded in the file.

This requires adding two additional options to FileEncryptor to allow the algorithm and key
length to be specified when encrypting a file and another option info for printing the metadata.

### Encryption
```java FileEncryptor enc AES 128 "my password" plaintext.txt ciphertext.enc```
Secret key is ((base64 encoded key))
This will encrypt the file plaintext.txt as ciphertext.enc to generate a 128-bit key
compatible with AES based on "my password" and printout the randomly generated secret key
encoded as a base64 string.

plaintext.txt
```This is some random text in plaintext.txt file```
my password
```password123```
```
javac FileEncryptor.java 
java FileEncryptor enc AES password123 plaintext.txt ciphertext.enc
```
output
```File successfully encrypted.```
ciphertext.enc
```	AESAES/CBC/PKCS5PADDINGyV�N�E�٩�oS�W��IJ�)�*�o,�O�=ϻ���```

### Decryption
```java FileEncryptor dec "my password" ciphertext.enc plaintext.txt```
This will decrypt the file ciphertext.enc as plaintext.txt using "my password" derived
secret key and the metadata embedded in ciphertext.enc.

ciphertext.enc
```	AESAES/CBC/PKCS5PADDINGyV�N�E�٩�oS�W��IJ�)�*�o,�O�=ϻ���```
my password
```password123```
```
javac FileEncryptor.java 
java FileEncryptor dec password123 ciphertext.enc 
```
### Metadata
```java FileEncryptor info ciphertext.enc AES 128```
This will print the metadata contained within ciphertext.enc
```
javac FileEncryptor.java
java FileEncryptor info ciphertext.enc AES 128
```
output
```
Encrypted File Detected: 
Encryption type: AES | AES/CBC/PKCS5PADDING
Key Size: 128
Cipher Salt: wv1xR5nIoefhdQUeitCj6A==
MAC Salt: mIIxzxu2TAAlA32zD6URIQ==
IV: fT3eVXQQyJ9J5ujyVR8AFw==
MAC: 07ZqabVeDCH4gYlQ4sFyPRqELE4ZWY+KJsVN0svp9L4=
```

### IMPORTANT NOTE REGARDING FILE FORMAT:
  - 0x09 (1 byte) Block size (1 byte) Key size (1 byte) <length of algorithm string> (1 byte) algorithm string (length bytes long) <length of cipher string> cipher (length bytes long) salt for cipher key (16 bytes) salt for MAC key (16 bytes) IV (blocksize bytes long) MAC of plaintext (32 bytes)

## The rationale for this file format is as follows:
  - The 0x09 guard is just to make it so that more files are rejected early rather than causing IO errors or decrypt errors. Not foolproof, obviously, a random file has a 1/256 chance of having 0x9 on its own. - Block and key size are only one byte as they themselves are in bytes A block size of 255 bytes would be over two thousand bits, which is not something we're likely to see.
  - Separate salts for cipher key and mac key as it's a convenient way to produce two different keys from the same master secret - MAC at the beginning so we can read it without dropping out of the cyrpto stream - MAC is of plaintext because mac-then-encrypt is about as secure as encrypt-than-mac, but the code complexity of mac-then-encrypt is much smaller, only requiring the file to be read once. (even though encrypt-than-mac is more efficient compute-wise)

