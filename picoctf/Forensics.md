# Forensics 
## Challenge 1: Trivial Flag Transfer Protocol 
This challenge required us to figure out how the flag was moved. I first downloaded the given file ‘tftp.pcapng’. Then in order to check the file type and other related information, I ran the command `file tftp.pcapng` which gave the following output:
`tftp.pcapng: pcapng capture file - version 1.0`
The file was a pcapng capture file. I decided to use Wireshark.
<img width="1470" alt="Pasted Graphic" src="https://github.com/user-attachments/assets/da93ef06-d7ba-4c18-b1a2-512aac059e9e">

I then went to the statistics menu and clicked on the conversations option which allowed me to analyze network conversations, which are basically streams of data exchanged between two endpoints. 
<img width="1213" alt="Pasted Graphic 7" src="https://github.com/user-attachments/assets/fbbca69a-57d4-4080-bbcd-2461834c456e">

The most number of bytes were flowing through the first result so I selected it and set it as the filter.
After applying the filter, the following results were displayed. 
<img width="1470" alt="Pasted Graphic 2" src="https://github.com/user-attachments/assets/c6444597-f98a-4924-b534-436f18e0fa01">

The protocol here was TFTP and destination file was plan. So, I decided to check where this data was flowing to by finding the plan file. I selected the first result, clicked on files option followed by Extract object option to extract the file. This resulted in the following prompt:
￼<img width="791" alt="10 10 10 11" src="https://github.com/user-attachments/assets/1ef2517c-a317-4904-86d1-783d2a237f8e">

I saved all files on my system and viewed them one by one.
<img width="806" alt="Pasted Graphic 4" src="https://github.com/user-attachments/assets/59a060b8-8c37-4d60-9408-e88cd2f8db4e">

3 of them were random images but the instructions.txt and plan files caught my eye. They had some meaningless text which had to be deciphered.

<img width="615" alt="Pasted Graphic 5" src="https://github.com/user-attachments/assets/c019a7e4-d16c-44ef-895e-b2b86e43db43">
<img width="642" alt="Pasted Graphic 6" src="https://github.com/user-attachments/assets/a90620db-962e-4081-af8b-df7ccc844bce">

The text seemed to be decoded by rot13 decipher. So I tried deciphering it online which gave the following text for plan file and instructions.txt file repectively: `IUSEDTHEPROGRAMANDHIDITWITH-DUEDILIGENCE.CHECKOUTTHEPHOTOS` 
`TFTPDOESNTENCRYPTOURTRAFFICSOWEMUSTDISGUISEOURFLAGTRANSFER.FIGUREOUTAWAYTOHIDETHEFLAGANDIWILLCHECKBACKFORTHEPLAN` 
By adding spaces it becomes: `I USED THE PROGRAM AND HID IT WITH-DUEDILIGENCE. CHECK OUT THE PHOTOS.` and
`TFTP DOESNT ENCRYPT OUR TRAFFIC SO WE MUST DISGUISE OUR FLAGT RANSFER. FIGURE OUT A WAY TO HIDE THE FLAG AND I WILL CHECK BACK FOR THE PLAN`. 
Here the text WITH-DUEDILIGENCE seems like a clue as there is a hyphen after with. Also, the pictures which earlier seemed uninteresting have something hidden in them. The pictures had a .bmp extension so I used a bmp file decoder online, uploaded the files and got the following characters as results:
Ú  
/ å V
Ì l 
These characters did not look useful. There was also a file named program.deb which I earlier overlooked. I ran the command  `ar x program.deb` to extract the .deb package followed by `tar -xvf data.tar.xz` to extract the data.  This gave the following output:
```
mahikakapil@Mahikas-MacBook-Air ~ % ar x program.deb

mahikakapil@Mahikas-MacBook-Air ~ % tar -xvf data.tar.xz

x ./
x ./usr/
x ./usr/share/
x ./usr/share/doc/
x ./usr/share/doc/steghide/
x ./usr/share/doc/steghide/ABOUT-NLS.gz
x ./usr/share/doc/steghide/LEAME.gz
x ./usr/share/doc/steghide/README.gz
x ./usr/share/doc/steghide/changelog.Debian.gz
x ./usr/share/doc/steghide/changelog.Debian.amd64.gz
x ./usr/share/doc/steghide/changelog.gz
x ./usr/share/doc/steghide/copyright
x ./usr/share/doc/steghide/TODO
x ./usr/share/doc/steghide/HISTORY
x ./usr/share/doc/steghide/CREDITS
x ./usr/share/doc/steghide/BUGS
x ./usr/share/man/
x ./usr/share/man/man1/
x ./usr/share/man/man1/steghide.1.gz
x ./usr/share/locale/
x ./usr/share/locale/ro/
x ./usr/share/locale/ro/LC_MESSAGES/
x ./usr/share/locale/ro/LC_MESSAGES/steghide.mo
x ./usr/share/locale/fr/
x ./usr/share/locale/fr/LC_MESSAGES/
x ./usr/share/locale/fr/LC_MESSAGES/steghide.mo
x ./usr/share/locale/de/
x ./usr/share/locale/de/LC_MESSAGES/
x ./usr/share/locale/de/LC_MESSAGES/steghide.mo
x ./usr/share/locale/es/
x ./usr/share/locale/es/LC_MESSAGES/
x ./usr/share/locale/es/LC_MESSAGES/steghide.mo
x ./usr/bin/
x ./usr/bin/steghide
```
I noticed several files named steghide. I searched it online and found that steghide is a steganography program that is able to hide data in various kinds of image- and audio-files. This gave a clue that the pictures hid something in them. Since I did not have steghide by default, I installed it. I then tried running the following commands with ‘DUEDILIGENCE’ as the passphrase.
```
mahikakapil@Mahikas-MacBook-Air ~ % steghide extract -sf picture1.bmp  -p DUEDILIGENCE 
steghide: could not open the file "picture1.bmp".
mahikakapil@Mahikas-MacBook-Air ~ % steghide extract -sf picture2.bmp -p DUEDILIGENCE 
steghide: could not open the file "picture2.bmp".
mahikakapil@Mahikas-MacBook-Air ~ % steghide extract -sf picture3.bmp -p DUEDILIGENCE   
wrote extracted data to "flag.txt".
```
The third picture had the flag. Then I ran the command `cat ./flag.txt` to read the file and I finally got the flag: 
```
mahikakapil@Mahikas-MacBook-Air ~ % cat ./flag.txt
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
```

### -New concepts:
1.  Wireshark which is a network protocol analyzer that captures and displays network traffic in real time can be used for pcapng file type. 
2. We need to analyze the data transfer and conversations, especially the number of bytes transferred, to narrow down our search.
3. rot13 is a simple letter substitution cipher which replaces a letter with the 13th letter in the English alphabet.
4. I worked with a .deb file for the first time. I looked up how to open it on terminal on macOS and came across `ar x <filename>` and `tar -xvf data.tar.xz` commands.


### -Errors:
1. Initially, I did not know how to use Wireshark so I was not properly analyzing the network traffic but after referring to some videos, I figured out how to filter the search. 
2. Since I was working on macOS, I tried downloading steghide using Homebrew but it gave errors. So I installed it from sourceforge. I also had to installed macports and set it up in order to use the steghide command.

### -References: [Forensics intro](https://trailofbits.github.io/ctf/forensics/)
[Wireshark intro](https://www.youtube.com/watch?v=ZNS115MPsO0 )
[Steghide](https://medium.com/the-kickstarter/steganography-on-kali-using-steghide-7dfd3293f3fa)

### FLAG: picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}

## Challenge 2: m00nwalk
In this challenge, we were supposed to decode the file. The file had a .wav extension which meant that it was an audio file. Also there was a hint given 'How did pictures from the moon landing get sent back to Earth?'. I looked it up and found SSTV format which transmits image data using low frequency signals. This could mean that the message.wav file has an image in it. I searched for some SSTV decoders and came across qsstv. I tried downloading it using `brew install qsstv' but then qsstv was not available for MacOS. So I downloaded a software called Black Cat SSTV and uploaded the file there to get an image. 
<img width="1023" alt="image" src="https://github.com/user-attachments/assets/5d56c875-9a88-477c-b9c6-443735bb0663">
The second hint for this challenge was 'What is the CMU mascot?, that might help select a RX option'. So I looked it up and got Scottish Highland Terrier aka Scotty as the result. Pretty random but a hint is a hint. I set the SSTV code to Scottie 1 and tried playing around with the image adjustments to get a clue. 
<img width="1023" alt="image" src="https://github.com/user-attachments/assets/c38894bc-ec11-4b3e-b347-c35c0c0105b6">
The flag was visible in the image.

### -New Concepts
1. I found the concept of how SSTV (Slow Scan Television) uses frequency modulation to convert a picture into an image pretty interesting.

### -References:[SSTV](https://www.scopeofwork.net/how-slow-scan-tv-shaped-the-moon/)
### FLAG: picoctf{beep_boop_im_in_space}

## Challenge 3: tunn3l v1s10n
The challenge description provided a file. I opened it and it had a bunch of unintelligible characters. 
```
mahikakapil@Mahikas-MacBook-Air cryptonitetp2_Mahika % file tunn3l_v1s10n
tunn3l_v1s10n: data
```
Since I had no clue about what to do with this file, I just tried searching for lines starting with p as it is the first letter of the flag format picoCTF{}.  
```
mahikakapil@Mahikas-MacBook-Air cryptonitetp2_Mahika % cat tunn3l_v1s10n|grep p
Binary file (standard input) matches
```
Okay so the file is a binary file. I tried reading the file but it gave no output. 
```
f = open('tunn3l_v1s10n', "rb")
s = f.read()
print(s)
f.close()
```
This made me think that it could be an image which is converted to a binary file. So, I decided to use the exitfool tool.
```
mahikakapil@Mahikas-MacBook-Air cryptonitetp2_Mahika % exiftool tunn3l_v1s10n
ExifTool Version Number         : 13.00
File Name                       : tunn3l_v1s10n
Directory                       : .
File Size                       : 2.9 MB
File Modification Date/Time     : 2024:11:09 01:06:36+05:30
File Access Date/Time           : 2024:11:09 01:06:36+05:30
File Inode Change Date/Time     : 2024:11:09 01:08:17+05:30
File Permissions                : -rw-r--r--
File Type                       : BMP
File Type Extension             : bmp
MIME Type                       : image/bmp
BMP Version                     : Unknown (53434)
Image Width                     : 1134
Image Height                    : 306
Planes                          : 1
Bit Depth                       : 24
Compression                     : None
Image Length                    : 2893400
Pixels Per Meter X              : 5669
Pixels Per Meter Y              : 5669
Num Colors                      : Use BitDepth
Num Important Colors            : All
Red Mask                        : 0x27171a23
Green Mask                      : 0x20291b1e
Blue Mask                       : 0x1e212a1d
Alpha Mask                      : 0x311a1d26
Color Space                     : Unknown (,5%()
Rendering Intent                : Unknown (826103054)
Image Size                      : 1134x306
Megapixels                      : 0.347
```
The file was a bmp file. I tried saving it with a .bmp extension and opening it but it wouldn’t open. I then looked for information on bitmap files on the internet. I then got the bmp headers using terminal.
```
mahikakapil@Mahikas-MacBook-Air cryptonitetp2_Mahika % xxd -l 64 tunn3l_v1s10n.bmp
00000000: 424d 8e26 2c00 0000 0000 bad0 0000 bad0  BM.&,...........
00000010: 0000 6e04 0000 3201 0000 0100 1800 0000  ..n...2.........
00000020: 0000 5826 2c00 2516 0000 2516 0000 0000  ..X&,.%...%.....
00000030: 0000 0000 0000 231a 1727 1e1b 2920 1d2a  ......#..'..) .*
```

I then tried using an online hex editor. I opened the file using https://hexed.it/ and the headers were displayed.
<img width="1065" alt="Pasted Graphic 39" src="https://github.com/user-attachments/assets/f457df0a-4793-475b-8876-b07cf02d0193">

I had no idea what to do with this data so I searched the internet regarding bmp headers. I found the specifications for the size of a bmp file and changed BA to 28 and D0 to 00. Then I saved it as a .bmp file and opened it.
<img width="703" alt="Pasted Graphic 40" src="https://github.com/user-attachments/assets/b0c2b348-c811-4410-a150-52ae8532a986">

This image had a false flag. So, In the info header I made some more changes to the width, height, planes and bits per pixel. I set 6E to 10, 04 to 00, 32 to 10, 01 to 00 and 18 to 10. But this gave a very zoomed-in image.
<img width="703" alt="Pasted Graphic 41" src="https://github.com/user-attachments/assets/58a1fac6-1121-4589-942c-81d1e82af65e">

So I reverted the specifications to their original values and just tried changing the height specifications. When I set
height to 10 04 00 00 a bigger image with the flag was produced.
<img width="1039" alt="Pasted Graphic 42" src="https://github.com/user-attachments/assets/ed8aa5c7-cdf3-4e24-bcb6-fc82a71fb69f">
<img width="700" alt="Pasted Graphic 43" src="https://github.com/user-attachments/assets/ffc93921-749b-410d-9332-93ca9806f52d">

The flag was visible in the picture.


### -New Concepts
1. I came across the xxd command which coverts binary data into hexadecimal notations.
2. I used exitfool to analyze the bmp file details.
3. I learned about the headers of a BMP file and how to modify them.

### -Errors and Mistakes
1. I was stuck for some time trying to understand what to do with the given binary data.
2. I entered the wrong specifications for the info header and couldn’t view the image properly.

### -References
1. [BMP file format](https://www.ece.ualberta.ca/~elliott/ee552/studentAppNotes/2003_w/misc/bmp_file_format/bmp_file_format.htm)
2. [Specifications](https://www.donwalizerjr.com/understanding-bmp/)

### FLAG: picoCTF{qu1t3_a_v13w_2020}

