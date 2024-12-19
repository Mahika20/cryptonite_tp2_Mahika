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
