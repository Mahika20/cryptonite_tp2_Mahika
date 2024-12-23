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

I searched the coordinates on maps and got the names of the following places: Kyoto, Odesa, Dayton, Fatih, Abu dhabi, Kuala lampur, Addis ababa, Loja, Amsterdam, Sleepy Hollow, Kodiak, Bab Sharqi. So I tried taking the initials of every location to form the flag: picoCTF{KODFAK_ALASKB}. However, this flag was incorrect. I tried getting the names again. After checking again, the new list of places was: Kyoto, Odesa, Dayton, Istanbul, Abu dhabi, Kuala lampur, Addis ababa, Loja, Amsterdam, Sleepy Hollow, Kodiak, Alexandria. So the new flag would be:  picoCTF{KODIAK_ALASKA}. This was the right flag.

### -Mistakes
1. Initially I noted down wrong names of places which resulted in an incorrect flag.

### FLAG: picoCTF{KODIAK_ALASKA}

## Challenge 6: Mini RSA
In this challenge we were given a file containing the N, e and c values for the RSA encryption. It was specified that the plaintext was padded. I wrote the following python code to get the flag.
```
import math
from Crypto.Util.number import *

c=int("""122001231858887188613252475789888442217453455805559371330908830491
02739910735547326599771339806853708992578501219708124057007937105466740621542
375448401776167468056686663174811408726056537684848672921381399490761029073998
319988275676452309863454559156928630943647975264973020827349559037550506381552
0289059980814727660578288981377299291889840040802606764246414188506737961491843
7023839625205930332182990301333585691581437573708925507991608699550931884734959475
780164693178925308303420298715231388421829397209435815583140323329070684583974607064056
215836529244330562254568162453025117819569708767522400676415959028292550922595255396
203239357606521218664984826377129270592358124859832816717406984802489441913267065210
67408774368505816453982262381083175484590066023041695032156352372395923276609429290554
32741077128674865906461616284021980492215677741735780885273670848439248432663611348422
690344595606123607633835472513787936413041510
38512392821572406034926965112582374825926358165795831789031647600129008730""".replace("\n", ""))

n=int("""161576568432146305407822605195988788423367831773489290174076332113521363679607546240195027460240509513858989808742833775844501328148896686607335571077186467172699191870655807123126697642718467380022525453169592854127254638514649573642026181569381054458981110496782935446149117820012609966190965416354266154169940483964403517744509298895261491842431708238017438381902558507620664199347932657618079354432119435701891621511300974265440859708372450816921618200844969391722749781316544437220151754178898992546171106782568194794747100139084377474644269973938692328580102268545122126101079883764692809227755619814566292469180303288004049276244256149776068993360178140161708660059348212746565539084136115402589067975751406045610310419925591716467816197273585893946479096044834598894148149905024867312865650805528503709002643968384
72665362831601420716430154348134734634697331121823286787067021160540366182775069976665345678467639386923350699557552444384153779
33440029498378955355877502743215305768814857864433151287""".replace("\n", ""))

e=3
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

for i in range(10000):
    flag = long_to_bytes(ip(i*n+c,3))
    if b'pico' in flag:
      f = str(flag)[2:-1].lstrip(" ")
      print(f)
      break
```

### -New concepts
1. Used long_to_bytes function from Crypto.util.number package to convert the long int value to bytes.

### -Errors and mistakes
1. Initially I thought that finding the (1/e)th power of c would yield the flag but that didn't work as there was padding.

### -References
1. [Crypto.util.number](https://pycryptodome.readthedocs.io/en/latest/src/util/util.html#module-Crypto.Util.number)

### FLAG: picoCTF{e_sh0u1d_b3_lArg3r_85d643d5}

## Challenge 7: Mind your Ps and Qs
In this challenge, we had a small value for n which meant that we could find the values of p and q by finding the prime factors of n.
<img width="796" alt="image" src="https://github.com/user-attachments/assets/3cb6bb56-5eeb-41a6-a8c8-8e0c49b07c60" />
Then I wrote the following python code
```
from Crypto.Util.number import *
c = 8533139361076999596208540806559574687666062896040360148742851107661304651861689
n = 769457290801263793712740792519696786147248001937382943813345728685422050738403253
e = 65537
p = 475693130177488446807040098678772442581573
q = 1617549722683965197900599011412144490161
phi = (p - 1) * (q - 1)

def extended_gcd(a, b):
    if b == 0:
        return a, 1, 0
    gcd, x1, y1 = extended_gcd(b, a % b)
    x = y1
    y = x1 - (a // b) * y1
    return gcd, x, y


gcd, d, _ = extended_gcd(e, phi)
if gcd != 1:
    print("e and phi are not coprime, decryption is not possible.")
else:
    d = d % phi  
    print(f"Private exponent d: {d}")
    m = pow(c, d, n)  
    print(f"Decrypted message (m): {m}")
flag = long_to_bytes(m)
f = str(flag).lstrip(" ")
print(f)
```
This gave the following output:
```
Private exponent d: 582402748221564772374696843202153883711652351188297188998024166320268538694734273
Decrypted message (m): 13016382529449106065927291425342535437996222135352905256639629442503647501498237
b'picoCTF{sma11_N_n0_g0od_45369387}'
```
### -New concepts
1. I learned how to use extended euclidean algorithm to calculate d.
   
### -References
1. [extended euclidean algo](https://en.wikipedia.org/wiki/Extended_Euclidean_algorithm)

### FLAG: picoCTF{sma11_N_n0_g0od_45369387}

## Challenge 8: Pixelated
In this challenge we were given 2 images and we had to make a flag out of them. I stacked the images using the following code:
```
from PIL import Image

im1 = Image.open("scrambled1.png").convert('L') 
im2 = Image.open("scrambled2.png").convert('L')

im3 = Image.blend(im1, im2, 0.5) 
im3.show()
```
However, there was nothing visible in the image except a tiny blotch. So I used an online image magnifier to analyze the image and I could see the flag.
<img width="1449" alt="image" src="https://github.com/user-attachments/assets/24f9b952-3402-4cf1-b81c-741a072aa9af" />

### -New concepts
1. I learnt how to use the blend method to stack 2 images.

### -References
1. [Blend](https://www.geeksforgeeks.org/python-pil-blend-method/)

### FLAG: picoCTF{0542dc1d}

## Challenge 9: basic-mod1
We were given a message to decrypt. The decryption scheme was: `Take each number mod 37 and map it to the following character set: 0-25 is the alphabet (uppercase), 26-35 are the decimal digits, and 36 is an underscore.` I wrote the following python code.

```
dig = [350, 63, 353, 198, 114, 369, 346, 184, 202, 322, 94, 235, 114, 110, 185, 188, 225, 212, 366, 374, 261, 213]


alphabet = [chr(i) for i in range(ord('A'), ord('Z') + 1)]
digits = [str(i) for i in range(0, 10)]
underscore = ["_"]
charset = alphabet + digits + underscore

l = []
for i in dig:
    j= i%37  
    l.append(charset[j])  

print("Decoded message:", "".join(l))    
```
The output was:
```
Decoded message: R0UND_N_R0UND_ADD17EC2
```
### FLAG: picoCTF{R0UND_N_R0UND_ADD17EC2}

## Challenge 10: substitution0
For this challenge, were given a scrambled message and the key for the substitution. I executed the following decryption code.
```
key = "OHNFUMWSVZLXEGCPTAJDYIRKQB"  
alphabet = [chr(i) for i in range(ord('a'), ord('z') + 1)]  
d = {}

for k, a in zip(key, alphabet):
    d[k.lower()] = a  

flag = "pvncNDM{5YH5717Y710G_3I0XY710G_03055505}".lower()

decrypted = ""
for i in flag:
    if i in d:
        decrypted += d[i]  
    else:
        decrypted += i   

print(decrypted)
```
Here, a dictionary is being created with the characters from the key as the dictionary keys and the alphabet as the values and the substitution takes place to yield the flag.

### FLAG: picoctf{5ub5717u710n_3v0lu710n_03055505}

## Challenge 11: credstuff
In this challenge we had to find the password of the user cultiris and decrypt it. Executing the following code printed the encrypted flag.
```
with open("usernames.txt", "r") as user_file, open("passwords.txt", "r") as pass_file:
    usernames = user_file.readlines()
    passwords = pass_file.readlines()

usernames = [username.strip() for username in usernames]
passwords = [password.strip() for password in passwords]
d = {usernames[i]: passwords[i] for i in range(len(usernames))}

for i in d:
    if (i=="cultiris"):
        print(d[i])
```
The encrypted flag was: cvpbPGS{P7e1S_54I35_71Z3}. Now moving on to its decryption. It looked like a ROT13 cypher so I went to cyberchef and deciphered the flag.
<img width="1167" alt="image" src="https://github.com/user-attachments/assets/e3a047f3-64ca-4709-9e59-ea18f63e219c" />

### FLAG: picoCTF{C7r1F_54V35_71M3}

## Challenge 12: b00tl3gRSA3
We were given n, c and e values for this challenge. 
```
mahikakapil@192 downloads % nc jupiter.challenges.picoctf.org 4557
c: 784809518605445723601291179860864945414133076364335654740573947199270956328796510542241025122834672453778497294247352116943239010818966002011440742119328820433222106078831512566761742616907763256554101048251833649490396424327038080153767363724222674825894600323339702512647198887792362421530061305979055730188680495986618859618608503245676397
n: 2804691435511051860797280517440247988221796975741077739705494449807374039942993600529711824888658391129549193545780329632766734643515893889223248781454753843147796230023560687060862081674578296996686011241753379051427724896757431126666164955848453859297996145733532092758073831403184687617635705224521109016220561390666851290073810611847164351
e: 65537
```
In the hint it was mentioned that there are more prime factors than p and q. This was a multi-prime RSA. So, n is the product of all the factors and the totient would be of the form (p1-1)(p2-1)(p3-1)... where p1, p2... are n's prime factors. I then searched for tools to get the prime factors of a large integer. I came across [this](https://www.alpertron.com.ar/ECM.HTM) and it calculated the totient.
```
from Crypto.Util.number import long_to_bytes
import math
c = int("""895494143535747820938426493223242105057292088950863989572
28606270926796922641801559285647337218891675750814339189045029
94263067629121235511188567699331427469399025582436209104911677439
52595097302152017841962465775219423525556298255543783162351130486
822681518393808544658758527764395554
793835055375966773234550034861819127127538191616450989496""".replace("\n", ""))
n = int("""24561332319932489421516591544840741457374140366621807
754375809265030716728136993397923285099452373017385391826537870
5653770224409080912635281339459918199294050052685402516846078359
330383273315441514167584318244040484179385035186846173928153183481
77382408952820563268485406038629444296521644298669896
84330650933021247065274426096231719934039183""".replace("\n", ""))
e = 65537
phi=int("""2456133224766494142946886094212663265365337405541697660879928000388398264944135069298570503249203
4933339632674754823784913554760602217354768621506658874035675198626410041398474405599360055776504939070494067
530308299035197649225623202573093577227036410993786288928558068783781620997905721908050669946461839416301930143
81649854464000000000000000000000""".replace("\n",""))

d = pow(e, -1, phi)
m=pow(c,d,n)
text=long_to_bytes(m)
print(text)
```
This gave the output: `b'picoCTF{too_many_fact0rs_4025135}'`

### -New concepts
1. I learnt about multi prime RSA.

### FLAG: picoCTF{too_many_fact0rs_4025135}

## Challenge 13: b00tl3gRSA2
The values for c, n and e are given as follows:
```
mahikakapil@192 ~ %  nc jupiter.challenges.picoctf.org 18243
c: 19315231434468148807377582486996072914640073009469866238412974245399884658402417762419408744639813246399381966275905398321660213759227565539591211608735821483040096660538441653589491003727976783556453067227117602403482955688718744480619703444233429400429339451667524067583916003081825764692278739447272533223
n: 67121271813004986494917223849544062613024114865584951299559119555581253503867719511061440466518883865737699962802627104070958602887589366294886109429527982381101874084952132427636635125333013740807554640184040275813751847256350350574309108738111779141685144162300020804576801538752402785494685201924494180411
e: 19315231434468148807377582486996072914640073009469866238412974245399884658402417762419408744639813246399381966275905398321660213759227565539591211608735821483040096660538441653589491003727976783556453067227117602403482955688718744480619703444233429400429339451667524067583916003081825764692278739447272533223
```
The hint for this challenge was `What is e generally?`. This made me think that the d and e values might have been swapped. e is usually 65537 so in this case, d is 65537. Also, here n is a prime number. Executing the folllowing code yielded the flag.

```
from Crypto.Util.number import inverse, long_to_bytes
from factordb.factordb import FactorDB

c = 19315231434468148807377582486996072914640073009469866238412974245399884658402417762419408744639813246399381966275905398321660213759227565539591211608735821483040096660538441653589491003727976783556453067227117602403482955688718744480619703444233429400429339451667524067583916003081825764692278739447272533223
n= 67121271813004986494917223849544062613024114865584951299559119555581253503867719511061440466518883865737699962802627104070958602887589366294886109429527982381101874084952132427636635125333013740807554640184040275813751847256350350574309108738111779141685144162300020804576801538752402785494685201924494180411
e=64640723110270682555300068408135458013351496250078493084390097666211618092018418861721513267991201232678257063220590043386767050997912673050364478029886286719506206299422664708061154045703967294941312600371965681940855687763884098821414899340738163043589134558088494080111528033493190742975525967006389536473

d = 65537
m = pow(c, d, n)
decrypted_message = long_to_bytes(m)

print(decrypted_message)
```

### FLAG: picoCTF{bad_1d3a5_4783252}


## Challenge 14: RSA pop quiz
In this challenge we had to answer a quiz.
```
mahikakapil@192 ~ % nc jupiter.challenges.picoctf.org 1981
Good morning class! It's me Ms. Adleman-Shamir-Rivest
Today we will be taking a pop quiz, so I hope you studied. Cramming just will not do!
You will need to tell me if each example is possible, given your extensive crypto knowledge.
Inputs and outputs are in decimal. No hex here!
#### NEW PROBLEM ####
q : 60413
p : 76753
##### PRODUCE THE FOLLOWING ####
n
IS THIS POSSIBLE and FEASIBLE? (Y/N):Y
#### TIME TO SHOW ME WHAT YOU GOT! ###
n: 4636878989
Outstanding move!!!


#### NEW PROBLEM ####
p : 54269
n : 5051846941
##### PRODUCE THE FOLLOWING ####
q
IS THIS POSSIBLE and FEASIBLE? (Y/N):y
#### TIME TO SHOW ME WHAT YOU GOT! ###
q: 93089
Outstanding move!!!


#### NEW PROBLEM ####
e : 3
n : 12738162802910546503821920886905393316386362759567480839428456525224226445173031635306683726182522494910808518920409019414034814409330094245825749680913204566832337704700165993198897029795786969124232138869784626202501366135975223827287812326250577148625360887698930625504334325804587329905617936581116392784684334664204309771430814449606147221349888320403451637882447709796221706470239625292297988766493746209684880843111138170600039888112404411310974758532603998608057008811836384597579147244737606088756299939654265086899096359070667266167754944587948695842171915048619846282873769413489072243477764350071787327913
##### PRODUCE THE FOLLOWING ####
q
p
IS THIS POSSIBLE and FEASIBLE? (Y/N):n
Outstanding move!!!


#### NEW PROBLEM ####
q : 66347
p : 12611
##### PRODUCE THE FOLLOWING ####
totient(n)
IS THIS POSSIBLE and FEASIBLE? (Y/N):y
#### TIME TO SHOW ME WHAT YOU GOT! ###
totient(n): 836623060
Outstanding move!!!


#### NEW PROBLEM ####
plaintext : 6357294171489311547190987615544575133581967886499484091352661406414044440475205342882841236357665973431462491355089413710392273380203038793241564304774271529108729717
e : 3
n : 29129463609326322559521123136222078780585451208149138547799121083622333250646678767769126248182207478527881025116332742616201890576280859777513414460842754045651093593251726785499360828237897586278068419875517543013545369871704159718105354690802726645710699029936754265654381929650494383622583174075805797766685192325859982797796060391271817578087472948205626257717479858369754502615173773514087437504532994142632207906501079835037052797306690891600559321673928943158514646572885986881016569647357891598545880304236145548059520898133142087545369179876065657214225826997676844000054327141666320553082128424707948750331
##### PRODUCE THE FOLLOWING ####
ciphertext
IS THIS POSSIBLE and FEASIBLE? (Y/N):y
#### TIME TO SHOW ME WHAT YOU GOT! ###
ciphertext: 256931246631782714357241556582441991993437399854161372646318659020994329843524306570818293602492485385337029697819837182169818816821461486018802894936801257629375428544752970630870631166355711254848465862207765051226282541748174535990314552471546936536330397892907207943448897073772015986097770443616540466471245438117157152783246654401668267323136450122287983612851171545784168132230208726238881861407976917850248110805724300421712827401063963117423718797887144760360749619552577176382615108244813
Outstanding move!!!


#### NEW PROBLEM ####
ciphertext : 107524013451079348539944510756143604203925717262185033799328445011792760545528944993719783392542163428637172323512252624567111110666168664743115203791510985709942366609626436995887781674651272233566303814979677507101168587739375699009734588985482369702634499544891509228440194615376339573685285125730286623323
e : 3
n : 27566996291508213932419371385141522859343226560050921196294761870500846140132385080994630946107675330189606021165260590147068785820203600882092467797813519434652632126061353583124063944373336654246386074125394368479677295167494332556053947231141336142392086767742035970752738056297057898704112912616565299451359791548536846025854378347423520104947907334451056339439706623069503088916316369813499705073573777577169392401411708920615574908593784282546154486446779246790294398198854547069593987224578333683144886242572837465834139561122101527973799583927411936200068176539747586449939559180772690007261562703222558103359
##### PRODUCE THE FOLLOWING ####
plaintext
IS THIS POSSIBLE and FEASIBLE? (Y/N):n
Outstanding move!!!


#### NEW PROBLEM ####
q : 92092076805892533739724722602668675840671093008520241548191914215399824020372076186460768206814914423802230398410980218741906960527104568970225804374404612617736579286959865287226538692911376507934256844456333236362669879347073756238894784951597211105734179388300051579994253565459304743059533646753003894559
p : 97846775312392801037224396977012615848433199640105786119757047098757998273009741128821931277074555731813289423891389911801250326299324018557072727051765547115514791337578758859803890173153277252326496062476389498019821358465433398338364421624871010292162533041884897182597065662521825095949253625730631876637
e : 65537
##### PRODUCE THE FOLLOWING ####
d
IS THIS POSSIBLE and FEASIBLE? (Y/N):y
#### TIME TO SHOW ME WHAT YOU GOT! ###
d: 1405046269503207469140791548403639533127416416214210694972085079171787580463776820425965898174272870486015739516125786182821637006600742140682552321645503743280670839819078749092730110549881891271317396450158021688253989767145578723458252769465545504142139663476747479225923933192421405464414574786272963741656223941750084051228611576708609346787101088759062724389874160693008783334605903142528824559223515203978707969795087506678894006628296743079886244349469131831225757926844843554897638786146036869572653204735650843186722732736888918789379054050122205253165705085538743651258400390580971043144644984654914856729
Outstanding move!!!


#### NEW PROBLEM ####
p : 153143042272527868798412612417204434156935146874282990942386694020462861918068684561281763577034706600608387699148071015194725533394126069826857182428660427818277378724977554365910231524827258160904493774748749088477328204812171935987088715261127321911849092207070653272176072509933245978935455542420691737433
ciphertext : 18031488536864379496089550017272599246134435121343229164236671388038630752847645738968455413067773166115234039247540029174331743781203512108626594601293283737392240326020888417252388602914051828980913478927759934805755030493894728974208520271926698905550119698686762813722190657005740866343113838228101687566611695952746931293926696289378849403873881699852860519784750763227733530168282209363348322874740823803639617797763626570478847423136936562441423318948695084910283653593619962163665200322516949205854709192890808315604698217238383629613355109164122397545332736734824591444665706810731112586202816816647839648399
e : 65537
n : 23952937352643527451379227516428377705004894508566304313177880191662177061878993798938496818120987817049538365206671401938265663712351239785237507341311858383628932183083145614696585411921662992078376103990806989257289472590902167457302888198293135333083734504191910953238278860923153746261500759411620299864395158783509535039259714359526738924736952759753503357614939203434092075676169179112452620687731670534906069845965633455748606649062394293289967059348143206600765820021392608270528856238306849191113241355842396325210132358046616312901337987464473799040762271876389031455051640937681745409057246190498795697239
##### PRODUCE THE FOLLOWING ####
plaintext
IS THIS POSSIBLE and FEASIBLE? (Y/N):y
#### TIME TO SHOW ME WHAT YOU GOT! ###
plaintext: 14311663942709674867122208214901970650496788151239520971623411712977120586163535880168563325
Outstanding move!!!


If you convert the last plaintext to a hex number, then ascii, you'll find what you need! ;)
```
Then I ran the following code to get the flag.
```
from Crypto.Util.number import inverse, long_to_bytes

p=153143042272527868798412612417204434156935146874282990942386694020462861918068684561281763577034706600608387699148071015194725533394126069826857182428660427818277378724977554365910231524827258160904493774748749088477328204812171935987088715261127321911849092207070653272176072509933245978935455542420691737433

q=156408916769576372285319235535320446340733908943564048157238512311891352879208957302116527435165097143521156600690562005797819820759620198602417583539668686152735534648541252847927334505648478214810780526425005943955838623325525300844493280040860604499838598837599791480284496210333200247148213274376422459183
c=18031488536864379496089550017272599246134435121343229164236671388038630752847645738968455413067773166115234039247540029174331743781203512108626594601293283737392240326020888417252388602914051828980913478927759934805755030493894728974208520271926698905550119698686762813722190657005740866343113838228101687566611695952746931293926696289378849403873881699852860519784750763227733530168282209363348322874740823803639617797763626570478847423136936562441423318948695084910283653593619962163665200322516949205854709192890808315604698217238383629613355109164122397545332736734824591444665706810731112586202816816647839648399

n=p*q
phi=(p-1)*(q-1)
e=65537
d=pow(e,-1,phi)
m=pow(c,d,n)
flag=long_to_bytes(m)
print(flag)
```

### FLAG: picoCTF{wA8_th4t$_ill3aGal..ode01e4bb}

## Challenge 15: No Padding, No Problem
In this challenge we had to use the multiplicative property of RSA where product of two ciphertexts is equal to the encryption of the product of the respective plaintexts.
```
from Crypto.Util.number import inverse, long_to_bytes
from factordb.factordb import FactorDB
c = 47207910499040477661408246705650722954642586871532294625983560934181542173129355386870775164557318521833776841062373170302597802966201295766423370428620821175799159092544614214663489837129363866234443533942782433258287762459117556653542385624320114738299011404151652910510381108404825149985648777539017501341
n= 143495403031679909625697053743610186130873231517106244478090544991033280309555063082310940577191201030371153542286410826549032359981200739954415687676619406645665398607629746559931108384905289390469264772611912127841685187830616977439703376646054191986121832132312643882949420248691907053435971819163402806827
e=65537
l=2
c3=c*(pow(l,e,n))
print(c3)
```

Here, c3 is the product of two cipher texts. I entered it in oracle to decrypt it. 

```
Give me ciphertext to decrypt: 4379954293395465869591340541712154389160538242249705382009230664245020597987117955069211318395281053011780221292176210431771882305014446452583493130003373051349173922290884994799980667551846022288550933047240796301897963138012600403184358210816413520629045646893543598574337269805193927787509977287844777960655791177840210698976548386200559027410856778347667709964416286838520475731518637913558547783166654965762896770516027959519986977592943557441600217919141181734133829416867187504344756241081224273732519177300074123005816860719143274210387614251484217890443969462838672616231465928756655939272570524009946324787
Here you go: 580550060391700078946913236734911770139931497702556153513487440893406629034802718534645538074938502890768709712035489670906
```

This was the product(p) of the two plain texts so I ran the following code to get the flag from the original plain text by dividing the product by l.

```
from Crypto.Util.number import inverse, long_to_bytes
c = 47207910499040477661408246705650722954642586871532294625983560934181542173129355386870775164557318521833776841062373170302597802966201295766423370428620821175799159092544614214663489837129363866234443533942782433258287762459117556653542385624320114738299011404151652910510381108404825149985648777539017501341
n= 143495403031679909625697053743610186130873231517106244478090544991033280309555063082310940577191201030371153542286410826549032359981200739954415687676619406645665398607629746559931108384905289390469264772611912127841685187830616977439703376646054191986121832132312643882949420248691907053435971819163402806827
e=65537
l=2
p=580550060391700078946913236734911770139931497702556153513487440893406629034802718534645538074938502890768709712035489670906
m=p//l
text=long_to_bytes(m)
print(text)
```
### -New Concepts
1. I learnt about the homomorphic property of RSA.
2. I came acorss oracle which is a system or service that provides answers to specific queries made by an attacker.

### FLAG: picoCTF{m4yb3_Th0se_m3s54g3s_4r3_difurrent_0801973}

## Challenge 16: triple-secure
In this challenge, we were required to break RSA not once, but three times. First, I analyzed the encryption code. There were 3 prime numbers p, q and r. Using factordb, I got their values.
```
mahikakapil@192 ~ % factordb 15192492059814175574941055248891268822162533520576381643453916855435310880285336743521199057138647926712835561752909538944229702432795423884081992987060760867003375755338557996965825324749221386675061886921763747311599846248565297387814717840084998677273427776535730840343260681623323972936404815862969684384733188827100528542007213405382537935243645704237369770300643318878176739181891072725262069278646319502747718264711249767568106460533935904219027313131270918072460753061248221785076571054217566164086518459844527639082962818865640864990672033657423448004651989761933295878220596871163544315057550871764431562609
119660120407416342093521198875970200503652030026184999838840951544471188235057764512149622436334754517070092115889922087976143409261665862157884453930404483415351610238154321433871054239568905273137919725917526056473901359312949883646592913381637999260828599289383860301717085920912559762712295164989106012643 126963703597214242111055793388455179890379067770512076858587717197146928847759121114335398860091528260297687323794942479532566444647858389461128295471609299505781851499310733099158304584543771415239918097328230929655601250453281393102266829307661217314893414986784482323832179578849867366834284687012845412763
mahikakapil@192 ~ % factordb 15896482259608901559307142941940447232781986632502572991096358742354276347180855512281737388865155342941898447990281534875563129451327818848218781669275420292448483501384399236235069545630630803245125324540747189305877026874280373084005881976783826855683894679886076284892158862128016644725623200756074647449586448311069649515124968073653962156220351541159266665209363921681260367806445996085898841723209546021525012849575330252109081102034217511126192041193752164593519033112893785698509908066978411804133407757110693612926897693360335062446358344787945536573595254027237186626524339635916646549827668224103778645691
119660120407416342093521198875970200503652030026184999838840951544471188235057764512149622436334754517070092115889922087976143409261665862157884453930404483415351610238154321433871054239568905273137919725917526056473901359312949883646592913381637999260828599289383860301717085920912559762712295164989106012643 132846951895793538897077555403967847542050766700952197146228251113081712319440889155149846202888542648969351063239105740434095718011829001684551658508591803707420131965877374781379009502046474415909376904718002094203010990824838428607725944298259738507797326637681632441750845743202171364832477389321609195337
```
I used the following code to decrypt the flag:
```
from Crypto.Util.number import inverse, long_to_bytes
c=5527557130549486626868355638343164556636640645975070563878791684872084568660950949839392805902757480207470630636669246237037694811318758082850684387745430679902248681495009593699928689084754915870981630249821819243308794164014262751330197659053593094226287631278905866187610594268602850237495796773397013150811502709453828013939726304717253858072813654392558403246468440154864433527550991691477685788311857169847773031859714215539719699781912119479668386111728900692806809163838659848295346731226661208367992168348253106720454566346143578242135426677554444162371330348888185625323879290902076363791018691228620744490
n1=15192492059814175574941055248891268822162533520576381643453916855435310880285336743521199057138647926712835561752909538944229702432795423884081992987060760867003375755338557996965825324749221386675061886921763747311599846248565297387814717840084998677273427776535730840343260681623323972936404815862969684384733188827100528542007213405382537935243645704237369770300643318878176739181891072725262069278646319502747718264711249767568106460533935904219027313131270918072460753061248221785076571054217566164086518459844527639082962818865640864990672033657423448004651989761933295878220596871163544315057550871764431562609
n2=15896482259608901559307142941940447232781986632502572991096358742354276347180855512281737388865155342941898447990281534875563129451327818848218781669275420292448483501384399236235069545630630803245125324540747189305877026874280373084005881976783826855683894679886076284892158862128016644725623200756074647449586448311069649515124968073653962156220351541159266665209363921681260367806445996085898841723209546021525012849575330252109081102034217511126192041193752164593519033112893785698509908066978411804133407757110693612926897693360335062446358344787945536573595254027237186626524339635916646549827668224103778645691
n3=16866741024290909515057727275216398505732182398866918550484373905882517578053919415558082579015872872951000794941027637288054371559194213756955947899010737036612882434425333227722062177363502202508368233645194979635011153509966453453939567651558628538264913958577698775210185802686516291658717434986786180150155217870273053289491069438118831268852205061142773994943387097417127660301519478434586738321776681183207796708047183864564628638795241493797850819727510884955449295504241048877759144706319821139891894102191791380663609673212846473456961724455481378829090944739778647230176360232323776623751623188480059886131
e=65537

p=119660120407416342093521198875970200503652030026184999838840951544471188235057764512149622436334754517070092115889922087976143409261665862157884453930404483415351610238154321433871054239568905273137919725917526056473901359312949883646592913381637999260828599289383860301717085920912559762712295164989106012643

q=126963703597214242111055793388455179890379067770512076858587717197146928847759121114335398860091528260297687323794942479532566444647858389461128295471609299505781851499310733099158304584543771415239918097328230929655601250453281393102266829307661217314893414986784482323832179578849867366834284687012845412763

r=132846951895793538897077555403967847542050766700952197146228251113081712319440889155149846202888542648969351063239105740434095718011829001684551658508591803707420131965877374781379009502046474415909376904718002094203010990824838428607725944298259738507797326637681632441750845743202171364832477389321609195337


phi1=(p-1)*(q-1)
phi2=(p-1)*(r-1)
phi3=(q-1)*(r-1)


d1 = pow(e,-1,phi1)
d2 = pow(e,-1,phi2)
d3 = pow(e,-1,phi3)


moduli = [n3, n2, n1]
ds = [d3, d2, d1]

l=[(d3,n3),(d2,n2),(d1,n1)]

for d,n in l:
    c = int(pow(c, d, n))

text=long_to_bytes(c)
print(text)
```



### -New concepts
1. getPrime(N, randfunc=None) function returns a random N-bit prime number.

### -Errors and mistakes
1. Initially my code was giving weird output, I wasted a LOT of time trying to understand what went wrong. It turned our that I had assigned the variable c to a totient and ciphertext both so the output was incorrect.

### FLAG: picoCTF{1_gu3ss_tr1pl3_rs4_1snt_tr1pl3_s3cur3!!!!!!}

## Challenge 17: Sum-O-Primes
In this challenge we were given the sum of primes, product of primes and the cipher text in hexadecimal form. I started off by analyzing the encryption code. Then I wrote the following code to get the flag.
```
from Crypto.Util.number import long_to_bytes
from gmpy2 import isqrt, gcd
import math

x = int("152a1447b61d023bebab7b1f8bc9d934c2d4b0c8ef7e211dbbcf841136d030e3c829f222cec318f6f624eb529b54bcda848f65574896d70cd6c3460d0c9064cd66e826578c2035ab63da67d069fa302227a9012422d2402f8f0d4495ef66104ebd774f341aa62f493184301debf910ab3d1e72e357a99c460370254f3dfccd9ae", 16)
n = int("6fc1b2be753e8f480c8b7576f77d3063906a6a024fe954d7fd01545e8f5b6becc24d70e9a5bc034a4c00e61f8a6176feb7d35fe39c8c03617ea4552840d93aa09469716913b58df677c785cd7633d1b7d31e2222cab53be235aa412ac5c5b07b500cf3fd5d6b91e2ddc51bff1e6eec2cb68723af668df36e10e332a9cbb7f3e2df9593fa0e553ed58afec2aa3bc4ae8ef1140e4779f61bdeae4c0b46136294cf151622e83c3d71b97c815b542208baa28207225f134c5a4feac998aeb178a5552f08643717819c10e8b5ec7715696c3bf4434fbea8e8a516dfd90046a999e24a0fb10d27291eb29ef3f285149c20189e7d0190417991094948180196543b8c91", 16)
c = int("16acf84a73cefd321ed491a15c640a495b09050cdce435ec37442faf9a694775e1ebffb6dbad6133cbc54e3f641506b0613f711625594fcb467f915f2708714b4c9936f5f4752c3299157cff4eb68eb82c0054dae351314829974f4feadaf126cda92b97e348dbef2640ec3a729a064e615df73d644700f93bf87579683e253d29622525bea3644f59aac8e0b2553bfea48d99e9b323e03cbf55166659974eb8c51cc7b2c2c5d6aa6c01b056a8ed7283d96656a3496f266726605af1be139d586f208d4d7c59c2771dc8036d490d3672ee4513301002775d7c39eac421c6cb4f01344e061549a4cb11c057accef1726572e447501004c772ec91b4a55811280f", 16)

e = 65537
rdisc = isqrt((x ** 2) - (4 * n))
p = (x + rdisc) // 2
q = (x - rdisc) // 2

m =math.lcm(p - 1, q - 1)
d = pow(e, -1, m)
FLAG = pow(c, d, n)

print(long_to_bytes(FLAG))
```

### FLAG: picoCTF{pl33z_n0_g1v3_c0ngru3nc3_0f_5qu4r35_3921def5}


## Challenge 18: RSA-oracle
I started off by reading password.enc and message.enc
```
mahikakapil@192 downloads % cat password.enc
4228273471152570993857755209040611143227336245190875847649142807501848960847851973658239485570030833999780269457000091948785164374915942471027917017922546%
mahikakapil@192 downloads % cat secret.enc 
Salted__?rF7?]?{O?e3L?b?A??h??,?Ո;u{?^?a??X?7?0??ծ0???2|l8O&7%
```
Secret.enc was an openssl encrypted file. To access its content, we needed the decrypted password but oracle wouldn't decrypt the password. So, I took a random number l=2 and encrypted it. 
```
mahikakapil@192 downloads % nc titan.picoctf.net 57745
*****************************************
****************THE ORACLE***************
*****************************************
what should we do for you? 
E --> encrypt D --> decrypt. 
E
enter text to encrypt (encoded length must be less than keysize): 2
2

encoded cleartext as Hex m: 32

ciphertext (m ^ e mod n) 4707619883686427763240856106433203231481313994680729548861877810439954027216515481620077982254465432294427487895036699854948548980054737181231034760249505
```
I took the product of the above ciphertext and the encrypted password and decrypted it.
```
from Crypto.Util.number import long_to_bytes
import math

e=65537
l=2
c=4707619883686427763240856106433203231481313994680729548861877810439954027216515481620077982254465432294427487895036699854948548980054737181231034760249505
pw=4228273471152570993857755209040611143227336245190875847649142807501848960847851973658239485570030833999780269457000091948785164374915942471027917017922546
pdt=c*pw

print(pdt)
```
```
Enter text to decrypt: 19905104266461674438427749120035481333430848061235340017498754943327360989060077822957076637919794979148279735224896131374733698562836139551270909235193224215437233163101052237584916525504858889351339402233392061749581481707426238265002525969310836283110708030433599547806496303497230632152297549255724839730
10778181998225decrypted ciphertext as hex (c ^ d mod n): 139afb6b2d22
decrypted ciphertext: ûk-"

```
Then I divided this decrypted hex by 32 on this [website](https://www.calculator.net/hex-calculator.html?number1=139afb6b2d22&c2op=%2F&number2=32&calctype=op&x=Calculate)

<img width="772" alt="image" src="https://github.com/user-attachments/assets/7a455c56-b111-462c-9bbe-3ac9debb084e" />

On converting it to ascii form I got the password: da099. I decrypted secret.enc using the following command.
```
mahikakapil@192 downloads % openssl enc -aes-256-cbc -d -in secret.enc -out decrypt4.txt
enter AES-256-CBC decryption password:
*** WARNING : deprecated key derivation used.
Using -iter or -pbkdf2 would be better.
```

<img width="599" alt="image" src="https://github.com/user-attachments/assets/ff1e0d42-49c0-4b45-8083-9c7b38e3633c" />

### -Errors and mistakes
1. I wasted a lot of time in trying to figure out why my password wasn't working. It turned out I was using the hex value instead of the ascii one.

### FLAG: picoCTF{su((3ss_(r@ck1ng_r3@_da099d93}
