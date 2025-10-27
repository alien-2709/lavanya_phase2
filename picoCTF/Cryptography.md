# 1.Custom Encryption:
> Can you get sense of this code file and write the function that will decode the given encrypted file content.
> Find the encrypted file here flag_info and code file might be good to analyze and get the flag.

## Solution:
1. Using `strings -a enc_flag ` in the terminal , I obtained a base64 encoded message which I decoded twice to obtain `wpjvJAM{jhlzhy_k3jy9wa3k_i204hkj6}`
```
$ strings -a enc_flag
YidkM0JxZGtwQlRYdHFhR3g2YUhsZmF6TnFlVGwzWVROclgya3lNRFJvYTJvMmZRPT0nCg==
$ echo 'YidkM0JxZGtwQlRYdHFhR3g2YUhsZmF6TnFlVGwzWVROclgya3lNRFJvYTJvMmZRPT0nCg==' | base64 --decode
b'd3BqdkpBTXtqaGx6aHlfazNqeTl3YTNrX2kyMDRoa2o2fQ=='
$ echo 'd3BqdkpBTXtqaGx6aHlfazNqeTl3YTNrX2kyMDRoa2o2fQ=='|base64 --decode
wpjvJAM{jhlzhy_k3jy9wa3k_i204hkj6}
```
2. Next , I tried to decode the encrypted flag that I recieved `wpjvJAM{jhlzhy_k3jy9wa3k_i204hkj6}` but I could do so using Caesars cipher but that gave me a flag but it was the wrong flag.
3. The information that I recieved from enc_flag when I opened it in the editor:
 ```
a = 88
b = 26
cipher is: [97965, 185045, 740180, 946995, 1012305, 21770, 827260, 751065, 718410, 457170, 0, 903455, 228585, 54425, 740180, 0, 239470, 936110, 10885, 674870, 261240, 293895, 65310, 65310, 185045, 65310, 283010, 555135, 348320, 533365, 283010, 76195, 130620, 185045]
```
4. The encryption code in the python file was as follows :
`custom_encryption.py`
```
from random import randint
import sys


def generator(g, x, p):
        return pow(g, x) % p


def encrypt(plaintext, key): 
        cipher = []
        for char in plaintext:
                cipher.append(((ord(char) * key*311)))
        return cipher


def is_prime(p):
        v = 0
        for i in range(2, p + 1):
                if p % i == 0:
                        v = v + 1
        if v > 1:
                return False
        else:
                return True


def dynamic_xor_encrypt(plaintext, text_key):
        cipher_text = ""
        key_length = len(text_key)
        for i, char in enumerate(plaintext[::-1]):
                key_char = text_key[i % key_length]
                encrypted_char = chr(ord(char) ^ ord(key_char))
                cipher_text += encrypted_char
        return cipher_text


def test(plain_text, text_key):
        p = 97
        g = 31
        if not is_prime(p) and not is_prime(g):
                print("Enter prime numbers")
                return
        a = randint(p-10, p)
        b = randint(g-10, g)
        print(f"a = {a}")
        print(f"b = {b}")
        u = generator(g, a, p)
        v = generator(g, b, p)
        key = generator(v, a, p)
        b_key = generator(u, b, p)
        shared_key = None
        if key == b_key:
                shared_key = key
        else:
                print("Invalid key")
                return
        semi_cipher = dynamic_xor_encrypt(plain_text, text_key)
        cipher = encrypt(semi_cipher, shared_key)
        print(f'cipher is: {cipher}')


if __name__ == "__main__":
        message = sys.argv[1]
        test(message, "trudeau")
```
- here text-key in the xor algorithm = `trudeau`
- and we can obtain shared key used in `encrypt(semi_cipher, shared_key)` from `test`
- the randomly generated integers ` a = randint(p-10, p) ` and `b = randint(g-10, g)` are provided in `enc_flag` i.e `a=88` and `b=26`.
- the cipher (set of integers) also given : `cipher is: [97965, 185045, 740180, 946995, 1012305, 21770, 827260, 751065, 718410, 457170, 0, 903455, 228585, 54425, 740180, 0, 239470, 936110, 10885, 674870, 261240, 293895, 65310, 65310, 185045, 65310, 283010, 555135, 348320, 533365, 283010, 76195, 130620, 185045]`
- the algorithm basically goes ` x = y*xor*text-key x (shared-key * 311)`
- I didn't know how to build the decryption algorithm myself as I a still learning python , so I took help from A.I to obtain the following:
`custom_decryption.py`
```
from custom_encryption import is_prime, generator


def leak_shared_key(a, b):
    p = 97
    g = 31
    if not is_prime(p) and not is_prime(g):
        print("Enter prime numbers")
        return
    u = generator(g, a, p)
    v = generator(g, b, p)
    key = generator(v, a, p)
    b_key = generator(u, b, p)
    shared_key = None
    if key == b_key:
        shared_key = key
    else:
        print("Invalid key")
        return

    return shared_key


def decrypt(ciphertext, key):
    semi_ciphertext = []
    for num in ciphertext:
        semi_ciphertext.append(chr(round(num / (key * 311))))
    return "".join(semi_ciphertext)


def dynamic_xor_decrypt(semi_ciphertext, text_key):
    plaintext = ""
    key_length = len(text_key)
    for i, char in enumerate(semi_ciphertext):
        key_char = text_key[i % key_length]
        decrypted_char = chr(ord(char) ^ ord(key_char))
        plaintext += decrypted_char
    return plaintext[::-1]


if __name__ == "__main__":

    a = 88
    b = 26
    ciphertext_arr = [97965, 185045, 740180, 946995, 1012305, 21770, 827260, 751065, 718410, 457170, 0, 903455, 228585, 54425, 740180, 0, 239470, 936110, 10885, 674870, 261240, 293895, 65310, 65310, 185045, 65310, 283010, 555135, 348320, 533365, 283010, 76195, 130620, 185045]
    text_key = "trudeau"

    shared_key = leak_shared_key(a, b)


    semi_ciphertext = decrypt(ciphertext_arr, shared_key)

    
    plaintext = dynamic_xor_decrypt(semi_ciphertext, text_key)

   
    print(plaintext)
```
- upon running this file in terminal :
```
$ python3 custom_decryption.py
picoCTF{custom_d2cr0pt6d_019c831c}
```
## Flag:
`picoCTF{custom_d2cr0pt6d_019c831c}`

# 2.miniRSA
> Let's decrypt this: ciphertext? Something seems a bit small.

## Solution:
- First , I found out what RSA was exactly using Google.
- The RSA algorithm involves four steps: key generation, key distribution, public-key operation (used for encryption or verifying a signature), and private key operation (used for decryption or signing a message).
- The integers n and e form the public key and d is the private key. The modular exponentiation to the power of e is used in encryption and in verifying signatures, and exponentiation to the power of d is used in decryption and in signing messages.
- I opened the ciphertext file in the editor and obtained the following:
```
N: 29331922499794985782735976045591164936683059380558950386560160105740343201513369939006307531165922708949619162698623675349030430859547825708994708321803705309459438099340427770580064400911431856656901982789948285309956111848686906152664473350940486507451771223435835260168971210087470894448460745593956840586530527915802541450092946574694809584880896601317519794442862977471129319781313161842056501715040555964011899589002863730868679527184420789010551475067862907739054966183120621407246398518098981106431219207697870293412176440482900183550467375190239898455201170831410460483829448603477361305838743852756938687673
e: 3
ciphertext (c): 2205316413931134031074603746928247799030155221252519872650073010782049179856976080512716237308882294226369300412719995904064931819531456392957957122459640736424089744772221933500860936331459280832211445548332429338572369823704784625368933 
```
- Here , N and e form the public key and c is the ciphertext.
- Method 1: I used an online decoder in order to obtain the flag and was successful but I also did it manually just to know how to go about it.
- encryption function for RSA: `c≡m^e(modN)` but here since e is very small `3` then `c=m^3` and decryption would thus be: `m=(c)^1/3`
- upon doing so in the calculator , the value I recieved for m was `13016382529449106065894479374027604750406953699090365388203708028670029596145277`.
- from reading up , I found out that RSA treats messages as big integers. To read the text:
  * Convert m to hex.
  * Convert hex → bytes (big-endian).
  * Decode bytes as ASCII/UTF-8.
- next , I converted it into hex - `m = 0x7069636f4354467b6e3333645f615f6c41726733725f655f63636161373737367d`
- then , into bytes (big-endian) and decoded as ascii to obtain: `picoCTF{n33d_a_lArg3r_e_ccaa7776}`
  
## Flag:
`picoCTF{n33d_a_lArg3r_e_ccaa7776}`

## Resources:
- https://en.wikipedia.org/wiki/RSA_cryptosystem
- https://www.freecodecamp.org/news/the-cryptography-handbook-rsa-algorithm/#heading-low-exponent-attacks
- https://www.dcode.fr/rsa-cipher
