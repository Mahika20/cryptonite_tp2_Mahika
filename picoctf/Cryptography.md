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

The above code the following output:

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

-New concepts
1. I learnt how to decipher some text by analyzing and modifying the encryption scheme which in this case was a python script.

-Errors and Mistakes
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

References:
1. [fileinput Module](https://docs.python.org/3/library/fileinput.html#fileinput.input)
2. [2to3](https://docs.python.org/3.10/library/2to3.html#:~:text=2to3%20is%20a%20Python%20program,your%20own%20fixers%20for%202to3.)

FLAG: picoCTF{adlibs}
