# Cryptography
## Challenge 1: C3
For this challenge, we were provided with the cipher text and an encoder which was a python program. 

<img width="643" alt="Pasted Graphic 36" src="https://github.com/user-attachments/assets/2b32d370-fdb3-4814-a2e9-f59cc4098323">


<img width="570" alt="fileinput import input" src="https://github.com/user-attachments/assets/82ce524f-0fa3-496e-91c6-51fab5f7a4dd">


I tried analyzing the code. The string chars is put in a for loop and a line from the input is concatenated at every iteration. The string variables lookup1 and lookup2 hold a bunch of characters. Then there is another for loop iterating through the characters in chars. The variable cur holds the index of the current character, out string is concatenated with a character from lookup2 which is at an index (cur-prev)%40.  Then I decided to write a new python program to decipher the cipher text. I basically had to reverse what had been done in the above code.
```
f=open('ciphertext (1)','r')
text=f.read()

lookup1 = "\n \"#()*+/1:=[]abcdefghijklmnopqrstuvwxyz"
lookup2 = "ABCDEFGHIJKLMNOPQRSTabcdefghijklmnopqrst"

inp = ""
prev = 0

for char in text:
    cur=lookup2.index(char)
    for i in range(0,len(lookup1)):
        if (i-prev)%40==cur:
            inp+=lookup1[i]
            prev=i
            break
    
print(inp)

f.close()
```

The above code gave the following output:

```
#asciiorder
#fortychars
#selfinput
#pythontwo

chars = ""
from fileinput import input
for line in input():
    chars += line
b = 1 / 1

for i in range(len(chars)):
    if i == b * b * b:
        print chars[i] #prints
        b += 1 / 1
```

This was a Python 2 code. So I had to convert it into Python 3 before running it.

```
mahikakapil@Mahikas-MacBook-Air cryptonitetp2_Mahika % 2to3 -w outpt.py  
/Library/Frameworks/Python.framework/Versions/3.12/bin/2to3:3: DeprecationWarning: lib2to3 package is deprecated and may not be able to parse Python 3.10+
  from lib2to3.main import main
RefactoringTool: Skipping optional fixer: buffer
RefactoringTool: Skipping optional fixer: idioms
RefactoringTool: Skipping optional fixer: set_literal
RefactoringTool: Skipping optional fixer: ws_comma
RefactoringTool: Refactored outpt.py
--- outpt.py	(original)
+++ outpt.py	(refactored)
@@ -5,11 +5,11 @@
 
 chars = ""
 from fileinput import input
-for line in input():
+for line in eval(input()):
     chars += line
 b = 1 / 1
 
 for i in range(len(chars)):
     if i == b * b * b:
-        print chars[i] #prints
+        print(chars[i]) #prints
         b += 1 / 1
RefactoringTool: Files that were modified:
RefactoringTool: outpt.py
mahikakapil@Mahikas-MacBook-Air cryptonitetp2_Mahika % 
```

I then made some changes to the code and saved it as outpt.py.

```
#asciiorder
#fortychars
#selfinput
#pythontwo

chars = ""
f=open('org.py','r')
chars =f.read()
b = 1

for i in range(len(chars)):
    if i == b * b * b:
        print(chars[i]) #prints
        b += 1 
f.close()
```

This code gave the following output:

```
=========== RESTART: /Users/mahikakapil/cryptonitetp2_Mahika/outpt.py ==========
a
d
l
i
b
s
```
Then I enclosed the above text ‘adlibs’ in the picoCTF wrapper and it was the correct flag.

### -New concepts
1. I learnt how to decipher some text by analyzing and modifying the encryption scheme which in this case was a python script.

### -Errors and Mistakes
1. I first passed the ciphertext file to the outpt.py program.
```
#asciiorder
#fortychars
#selfinput
#pythontwo


f=open('ciphertext (1)','r')
text=f.read()
b = 1 

for i in range(len(text)):
    if i == b * b * b:
        print(text[i]) #prints
        b += 1
        
f.close()
```
The output was:
```
L
g
H
D
P
t
```
Unfortunately, this wasn’t the flag. Then I noticed #selfinput written in the code. So I saved the original text as org.py file and passed it to the outpt.py program.

### -References:
1. [fileinput Module](https://docs.python.org/3/library/fileinput.html#fileinput.input)
2. [2to3](https://docs.python.org/3.10/library/2to3.html#:~:text=2to3%20is%20a%20Python%20program,your%20own%20fixers%20for%202to3.)

### FLAG: picoCTF{adlibs}

## Challenge 2: Custom Encryption
The challenge description provided an encrypted file and a code file named custom_encryption.py. 
<img width="638" alt="Pasted Graphic 38" src="https://github.com/user-attachments/assets/e3c48903-9891-4aad-971e-2de544769d21">

The code file had the following content:
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
I tried analyzing the code. There were a bunch of functions defined. In the test function, the function dynamic_xor_encrypt is called and its value is stored in the variable semi_cipher and then the encrypt function is called which is stored in the variable cipher. I wrote a python program saved as decrypt.py to decipher the flag.

```
from random import randint
import sys
p = 97
g = 31
a=90
b=26

lst= [61578, 109472, 437888, 6842, 0, 20526, 129998, 526834, 478940, 287364, 0, 567886, 143682,
         34210, 465256, 0, 150524, 588412, 6842, 424204, 164208, 184734, 41052,
         116314, 41052, 177892, 348942, 218944, 335258, 177892, 47894, 82104, 116314]

def generator(g, x, p):
    return pow(g, x) % p

def is_prime(p):
    v = 0
    for i in range(2, p + 1):
        if p % i == 0:
            v = v + 1
    if v > 1:
        return False
    else:
        return True

def decrypt(lst, key):
    semicipher = ""
    for i in lst:
        semicipher+=(chr(i//(key*311)) )
    return semicipher

def dynamic_xor_encrypt(semicipher, text_key):
    cipher_text = ""
    key=''
    key_length = len(text_key)
    for i, char in enumerate(semicipher[::-1]):
        key_char = text_key[i % key_length]
        key+=key_char
        encrypted_char = chr(ord(char) ^ ord(key_char))
        cipher_text += encrypted_char
    print(key)
    return cipher_text    
    
u = generator(g, a, p)
v = generator(g, b, p)
key = generator(v, a, p)
b_key = generator(u, b, p)
cipher = decrypt(lst, b_key)
ogtext = dynamic_xor_encrypt(cipher, "trudeau")
    
print('og text is: ',ogtext)
```

The output was:
```
trudeautrudeautrudeautrudeautrude
og text is:  e~r~TAFntdb~yKu$cd!d`'r_"(rvts5tl
```

This did not seem right. So I tried passing “picoCTF{.“ as the text_key argument for dynamic_xor_encrypt as it would derive the shuffled key.
```
ogtext = dynamic_xor_encrypt(cipher, "picoCTF{")
print('og text is: ',ogtext)
```
The output was:
```
og text is:  aedurtuaynvoxw}Um;u|0oih|q)sf
```
It had aedurtu which was the shuffled Trudeau key. Then, I used aedurtu as the text_key argument for dynamic_xor_encrypt and got the following output. 
```
og text is:  picoCTF{custom_d2cr0pt6d_49fbee5b}
```
This was the flag.

### -New Concepts
1. I learned about XOR cipher.

### -Errors and Mistakes
1. I initially passed trudeau as the key argument while calling the dynamic_xor_encrypt function and wasn’t getting the flag. I had to use something from the flag format like picoCTF{ .

### FLAG:  picoCTF{custom_d2cr0pt6d_49fbee5b}

## Challenge 3: miniRSA
In this challenge, a file containing cyphertext was provided in the description. We were supposed to decrypt this text. The description also mentioned that something seems a bit small. The exponent here is 3 which is a small value.

<img width="646" alt="Pasted Graphic 45" src="https://github.com/user-attachments/assets/8ecbc363-e69e-4f84-a60a-1a2b89c775b0">

I first looked at the hints. The first hint was a link to a site containing info about RSA. Since c=m**e%n and e is small so I tried getting the cube root of ciphertext to find the original text m. I first ran the following python code.
```
c=22053164139311340310746037469282477990301552212525198726496492128676147518484367638012743
6046340617127783805682143711588361916970296350460601756578353720320770775
7768473109845162808575425972525116337319108047893250549462147185741761825125 
e=3
N=29331922499794985782735976045591164936683059380558950386560160105740343201513369
93900630753116592270894961916269862367534903043085954782570899470832180370530945943
80993404277705800644009114318566569019827899482853099561118486869061526644733509404
86507451771223435835260168971210087470894448460745593956840586530527915802541450092
94657469480958488089660131751979444286297747112931978131316184205650171504055596401
18995890028637308686795271844207890105514750678629077390549661831206214072463985180
98981106431219207697870293412176440482900183550467375190239898455201170831410460483
829448603477361305838743852756938687673

import math


B = math.cbrt(c)
    

print(B)
```
But the output was 2.8042946060170113e+29 which wasn’t what I was looking for. So I looked up how to find the cube root of large numbers using python on the internet and wrote the following code.
```
def ip(x,n):
    up = 1
    while up ** n < x:
        up *= 2
    low = up//2
    while low < up:
        mid = (low + up) // 2
        if low < mid and mid**n < x:
            low = mid
        elif up > mid and mid**n > x:
            up = mid
        else:
            return mid
    return mid + 1
c=int("""22053164139311340310746037469282477990301552212525198726496492128676147518484367638012743
6046340617127783805682143711588361916970296350460601756578353720320770775
7768473109845162808575425972525116337319108047893250549462147185741761825125""".replace("\n", ""))
n=3
result=ip(c,n)
print( result)
```
The output was `13016382529449106065894479374027604750406953699090365388202874238148389207291005`. I tried converting it into ASCII but the output was not right. 
<img width="832" alt="Decimal to ASCII Converter" src="https://github.com/user-attachments/assets/75e1f4cd-0204-475b-b524-b0f69fdc3f63">

So then I tried converting it to hexadecimal and then to ASCII and got the flag.
<img width="682" alt="Home › Conversion › Number conversion › Decimal to hexadecimal" src="https://github.com/user-attachments/assets/ae9f5873-4bdc-4384-8bd6-18befd252d7b">
<img width="711" alt="Hex to ASCII Text String Converter" src="https://github.com/user-attachments/assets/761f98c3-ec84-4179-8d11-186b98e382b2">


### -New concepts
1. I learned how RSA encryption works.
2. When e is small, the cipher text becomes c=m^e.

### -Errors and mistakes
1. I could not calculate the cube root precisely initially.

### -References
1. [RSA logic part1](https://www.youtube.com/watch?v=4zahvcJ9glg)
2. [RSA logic part2](https://www.youtube.com/watch?v=oOcTVTpUsPQ)
3. [Cuberoot](https://stackoverflow.com/questions/55436001/cube-root-of-a-very-large-number-using-only-math-library)

### FLAG: picoCTF{n33d_a_lArg3r_e_606ce004}

## Challenge 4: ReadMyCert
A file was provided in the challenge description. I downloaded and checked the file type. 

```
mahikakapil@192 downloads % file readmycert.csr
readmycert.csr: PEM certificate request
```
Then I tried catting the content of the file and grepping it with keywords from the flag but to no avail.
```
mahikakapil@192 downloads % cat readmycert.csr
-----BEGIN CERTIFICATE REQUEST-----
MIICpzCCAY8CAQAwPDEmMCQGA1UEAwwdcGljb0NURntyZWFkX215Y2VydF81YWVi
MGQ0Zn0xEjAQBgNVBCkMCWN0ZlBsYXllcjCCASIwDQYJKoZIhvcNAQEBBQADggEP
ADCCAQoCggEBAMCkf11rmV8rgqPvC2ZiPA6W+5RfOTwU6u3WpGvLA+2YFzocBPut
aATTxTPB+uaN2ZN3Z5J2CTFGmPzI4sUQfSqhZGuAqbfMyDDR8pRswmIYVJ6s0Apc
Toi7H8m3IShSbeE0pZUSIJpbK1a7V6lJqgwFMDI1qrgNhGgZaMA/l+d2J0vC3EYd
AijwSs8APcp6woWbFGYwdw5KaBsjn23oVz2G4h3/TmdB5g5e6Oq+kgi38NEpRDS0
ylXo9mUko3FqS4I6y9gOtDEI4uZaCJZuXHDmBpqZ04MfXbIVlHjF9NMOjDvXLonN
650oaANBm4bhBlgid0Fx48Z36tbtAVivZEcCAwEAAaAmMCQGCSqGSIb3DQEJDjEX
MBUwEwYDVR0lBAwwCgYIKwYBBQUHAwIwDQYJKoZIhvcNAQELBQADggEBAHZx6h9r
G/SE7RCoX6ndk5BOJprRiHpxOqPLAWcDyKHfStln0/HcQZzIrRVRsmoHiOmch+md
PBA1b+M5aj+3BWtPR9jOY4vht+ZmHAKa0WfQxwb2dBxsRPKTTDea0wN2u8BHLlSM
PbWPNuz+TKySL41xfwFuM4VN/ywn58GTvdb7HXgwNZCGgo2N1WhRq/dBMiagXMah
yb6gX4erugCu61T5tyD80hgsNBjaqyIdy/whRfC/Pmn3QHmdkqB5ZCPezwb2OLm4
5RDGv3WOB5q0BofoUGhVq757QE8qhL3oTvV2WlLoi3YWaZkJMCeR3vnH92cKC1Ov
FxdQuLOH8GMvl7U=
-----END CERTIFICATE REQUEST-----
mahikakapil@192 downloads % cat readmycert.csr| grep pico
mahikakapil@192 downloads % cat readmycert.csr| grep flag
mahikakapil@192 downloads % cat readmycert.csr| grep ctf
```
I looked up commands to access the content of a .csr file. Then I ran the command `openssl req -in readmycert.csr  -text` to processes certificate request. The following snippet of the output contains the flag.
```
mahikakapil@192 downloads % openssl req -in readmycert.csr  -text        
Certificate Request:
    Data:
        Version: 1 (0x0)
        Subject: CN=picoCTF{read_mycert_5aeb0d4f}, name=ctfPlayer
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
```


### -New concepts
1. I came across PEM certificate request for the first time. PEM files are used to store SSL certificates and their associated private keys.

### -References
1. [Open SSL commands](https://www.xolphin.com/support/OpenSSL/Frequently_used_OpenSSL_Commands)

### FLAG: picoCTF{read_mycert_5aeb0d4f}

## Challenge 5: Mr Worldwide
This challenge provided a file containing a message from a musician. The content of the flag looked like a group of coordinates plus the name of the challenge was Mr "Worldwide" so it could have something to do with locations.
<img width="634" alt="image" src="https://github.com/user-attachments/assets/39fb4f56-efd3-4c16-ba17-683f34a72c9d">
I searched the coordinates on maps and got the name of the following places: Kyoto, Odesa, Dayton, Fatih, Abu dhabi, Kuala lampur, Addis ababa, Loja, Amsterdam, Sleepy Hollow, Kodiak, Bab Sharqi. So I tried taking the initials of every location to form the flag: picoCTF{KODFAK_ALASKB}. However, this flag was incorrect. I tried getting the names again. After checking again, the new list of cities was: Kyoto, Odesa, Dayton, Istanbul, Abu dhabi, Kuala lampur, Addis ababa, Loja, Amsterdam, Sleepy Hollow, Kodiak, Alexandria. So the new flag would be:  picoCTF{KODIAK_ALASKA}. This was the right flag.

### -Mistakes
1. Initially I noted down wrong names of places which resulted in an incorrect flag.

### FLAG: picoCTF{KODIAK_ALASKA}
