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

## Challenge 4: Verify
I started off by launching the instance There was a checksum given in the description 55b983afdd9d10718f1db3983459efc5cc3f5a66841e2651041e25dec3efd46a. I connected to the instance using the ssh key. Listing the files:
```
ctf-player@pico-chall$ ls
checksum.txt  decrypt.sh  files
```
To verify the checksum I generated checksums of all the files in "files" and verified it by grepping the output with the provided checksum.
```
ctf-player@pico-chall$ sha256sum files/* | grep 55b983afdd9d10718f1db3983459efc5cc3f5a66841e2651041e25dec3efd46a
55b983afdd9d10718f1db3983459efc5cc3f5a66841e2651041e25dec3efd46a  files/2cdcb2de
```
The file name files/2cdcb2de was displayed. This meant that the flag was in this file. Then to decrypt it, I executed the following command:
```
ctf-player@pico-chall$ ./decrypt.sh files/2cdcb2de
picoCTF{trust_but_verify_2cdcb2de}
```
### FLAG: picoCTF{trust_but_verify_2cdcb2de}


## Challenge 5: St3g0
In this challenge, we were given a .png file. i downloaded it and rechecked the file type using the file command.
```
mahikakapil@Mahikas-MacBook-Air ~ % file pico.flag.png
pico.flag.png: PNG image data, 585 x 172, 8-bit/color RGBA, non-interlaced
```
Using steghide to extract hidden data.
```
mahikakapil@Mahikas-MacBook-Air ~ % steghide extract -sf pico.flag.png         
Enter passphrase: 
steghide: the file format of the file "pico.flag.png" is not supported.
mahikakapil@Mahikas-MacBook-Air ~ % file pico.flag.png

pico.flag.png: PNG image data, 585 x 172, 8-bit/color RGBA, non-interlaced
mahikakapil@Mahikas-MacBook-Air ~ % steghide extract -sf pico.flag.bmp
Enter passphrase: 
steghide: could not open the file "pico.flag.bmp".
```
I then tried getting the strings from the file.
```
mahikakapil@Mahikas-MacBook-Air ~ % strings -o pico.flag.png      
     14 IHDR
     43 4JIDATx
    341 8C&_
    367 >Q^}
    402  J7M
    464 B%oE
     ...
```
There were a lot of strings and none of them looked like flag. Then I installed zsteg on my terminal. Running the  `zsteg pico.flag.png ` command yielded the flag.
```
mahikakapil@Mahikas-MacBook-Air ~ % zsteg pico.flag.png  
b1,r,lsb,xy         .. text: "~__B>VG?G@"
b1,rgb,lsb,xy       .. text: "picoCTF{7h3r3_15_n0_5p00n_a9a181eb}$t3g0"
b1,abgr,lsb,xy      .. text: "E2A5q4E%uSA"
b2,b,lsb,xy         .. text: "AAPAAQTAAA"
b2,b,msb,xy         .. text: "HWUUUUUU"
b2,a,lsb,xy         .. file: Matlab v4 mat-file (little endian) ><?P, numeric, rows 0, columns 0
b2,a,msb,xy         .. file: Matlab v4 mat-file (little endian) | <?, numeric, rows 0, columns 0
b3,r,lsb,xy         .. file: gfxboot compiled html help file
b4,r,lsb,xy         .. file: Targa image data (16-273) 65536 x 4097 x 1 +4352 +4369 - 1-bit alpha - right ""
b4,g,lsb,xy         .. file: 0420 Alliant virtual executable not stripped
b4,b,lsb,xy         .. file: Targa image data - Map 272 x 17 x 16 +257 +272 - 1-bit alpha ""
b4,bgr,lsb,xy       .. file: Targa image data - Map 273 x 272 x 16 +1 +4113 - 1-bit alpha ""
b4,rgba,lsb,xy      .. file: Novell LANalyzer capture file
b4,rgba,msb,xy      .. file: Applesoft BASIC program data, first line number 8
b4,abgr,lsb,xy      .. file: Novell LANalyzer capture file
```

### -New concepts
1. I usede the zsteg command for the first time

### -Errors and mistakes
1. Intially I tried extracting the data from the image by converting it itnto a .bmp file and using steghide but it asked for a passphrase which I did not have. 

### FLAG: picoCTF{7h3r3_15_n0_5p00n_a9a181eb}$t3g0

## Challenge 6: Sleuthkit Intro
I started off by launching the instance and downloading the disk image. I installed the Sleuth Kit using homebrew on terminal. Then to find the size of the Linux partition I executed the mmls command. 
```
mahikakapil@Mahikas-MacBook-Air ~ % cd downloads
mahikakapil@Mahikas-MacBook-Air downloads % mmls disk.img
DOS Partition Table
Offset Sector: 0
Units are in 512-byte sectors

      Slot      Start        End          Length       Description
000:  Meta      0000000000   0000000000   0000000001   Primary Table (#0)
001:  -------   0000000000   0000002047   0000002048   Unallocated
002:  000:000   0000002048   0000204799   0000202752   Linux (0x83)
```
Then I connected to the remote checker service and inputted the length of the Linux partition.

```
mahikakapil@Mahikas-MacBook-Air downloads % nc saturn.picoctf.net 65368
What is the size of the Linux partition in the given disk image?
Length in sectors: 0000202752
0000202752
Great work!
picoCTF{mm15_f7w!}
```

### -New concepts
1. I learnt about sleuthkit and how to use it to view the partition table of a disk.
2. Disk Partitioning is the process of dividing a disk into one or more logical areas, often known as partitions, on which the user can work separately.

### -References
1. [Sleuthkit commands](https://wiki.sleuthkit.org/index.php?title=Mmls)

### FLAG: picoCTF{mm15_f7w!}

## Challenge 7: MacroHard WeakEdge
We were provided with a .pptm file named Forensics is fun.pptm. I downloaded it and tried opening it. There was nothing interesting in the ppt. The first slide had the text "Forensics is fun". I renamed the file to foren.pptm and checked the file type.
```
mahikakapil@Mahikas-MacBook-Air downloads % file foren.pptm
foren.pptm: Microsoft PowerPoint 2007+
```
Grepping substrings like flag, pico from the list of strings gave no results.
```
mahikakapil@Mahikas-MacBook-Air downloads % strings foren.pptm| grep pico
mahikakapil@Mahikas-MacBook-Air downloads % strings foren.pptm| grep flag
```
I tried analyzing the ppt in outline view. The 37th slide had some text saying "Not the flag"

<img width="1470" alt="image" src="https://github.com/user-attachments/assets/352bbf95-ac5e-496b-8331-7a9831ba1763">

Then I decided to take a look at the macros. There was a macro named not_flag.

<img width="522" alt="image" src="https://github.com/user-attachments/assets/919e8d6e-28e5-4837-a244-b147d9e46db9">

I clicked on the edit button and the Microsoft Visual Basic window opened.

<img width="1293" alt="image" src="https://github.com/user-attachments/assets/6aaee010-8dc3-4482-8f7e-9ef18dee5667">

I had hoped to find the flag here but I was disappointed. So I looked up .pptm file on google and came across a [page](https://stackoverflow.com/questions/888784/extract-text-from-a-powerpoint-ppt-or-pptx-file) where it was mentioned that ppts are zipped xml. So, I saved the file as a zip file, unzipped it and saved it in a directory named extractedpptm.
```
mahikakapil@Mahikas-MacBook-Air ~ % cd downloads
mahikakapil@Mahikas-MacBook-Air downloads % unzip forensics.zip -d extractedpptm 
```
With the current directory changed to extractedpptm, I ran the following commands to look for the flag.
```
mahikakapil@Mahikas-MacBook-Air extractedpptm % ls -R| grep flag
mahikakapil@Mahikas-MacBook-Air extractedpptm % ls -R| grep pico
mahikakapil@Mahikas-MacBook-Air extractedpptm % ls -R| grep ctf
mahikakapil@Mahikas-MacBook-Air extractedpptm % ls -R| grep hid
hidden
```
A file named hidden was listed. I navigated to the file in finder and opened it.

<img width="1053" alt="image" src="https://github.com/user-attachments/assets/28217319-9e79-4ace-88b1-97e90cbc7ed4">

It had the the text : Z m x h Z z o g c G l j b 0 N U R n t E M W R f d V 9 r b j B 3 X 3 B w d H N f c l 9 6 M X A 1 f Q. It seemed like the text could have some meaning. So, I went to cyberchef and tried decoding it. To my surprise, the flag was yielded.

<img width="1178" alt="image" src="https://github.com/user-attachments/assets/24f72add-750a-4666-92f4-fcb7ee4d8afe">


### -New concepts
1. I found out that .ppt files are zip files.

### -Errors and mistakes
1. Initially I could not figure out where the flag was so I tried going throught the macros but there was nothing there.

### FLAG: picoCTF{D1d_u_kn0w_ppts_r_z1p5}

## Challenge 8: Wireshark doo dooo do doo...
A file named shark1.pcapng was provided in the challenge description. I opened it in Wireshark to analyze the network traffic. In the Conversations tab of statistics, I looked for conversations with the most number of bytes.
<img width="1464" alt="image" src="https://github.com/user-attachments/assets/23ac7c06-b9fe-4ab0-bd7e-2e97644e29cc">

Here, 1MB of bytes were flowing from the source to the destination. So, I set this conversation as the filter. Then I decided to take a look at the Protocol Hierarchy Statistics.
<img width="1464" alt="image" src="https://github.com/user-attachments/assets/bfe14eb4-8d15-40e7-8c9e-74b2d654c510">
Transfer of the most number of bytes took place through the HTTP protocol. Now that I had this info, I headed back to the network traffic.
<img width="1464" alt="image" src="https://github.com/user-attachments/assets/8d277b51-ecd3-4265-813f-c2d39326d54a">
I exported some HTTP obects with the Extract object option in the File menu.

<img width="750" alt="image" src="https://github.com/user-attachments/assets/6ad498be-37c4-440d-9d70-89a20a611112">

These two objects with 47 bytes and 4 bytes caught my eye. They stored text content. After exporting these objects as filewire and instance-action respectively, I opened filewire file.

<img width="637" alt="image" src="https://github.com/user-attachments/assets/f89d6bfd-0d52-49c2-9bd5-02f564518104">

This looked like the flag but it had to be deciphered. I guessed that it could be ROT13 cipher. On converting the text from ROT13 cipher on cyberchef, the flag was yielded.

<img width="1464" alt="image" src="https://github.com/user-attachments/assets/b1be10fa-4566-47da-9acc-27496bb8697b">


### -Learnings 
1. I got more comfortable with wireshark with this challenge.

### FLAG: picoCTF{p33kab00_1_s33_u_deadbeef}

## Challenge 9: What Lies Within
A .png file was provided in the challenge description. 

<img width="700" alt="image" src="https://github.com/user-attachments/assets/679b0ac2-60e3-43b7-bc91-dd8da490cf58">

I downloaded it and used zsteg to get the flag.
```
mahikakapil@Mahikas-MacBook-Air downloads % zsteg buildings.png
b1,r,lsb,xy         .. text: "^5>R5YZrG"
b1,rgb,lsb,xy       .. text: "picoCTF{h1d1ng_1n_th3_b1t5}"
b1,abgr,msb,xy      .. file: PGP Secret Sub-key -
b2,b,lsb,xy         .. text: "XuH}p#8Iy="
b3,abgr,msb,xy      .. text: "t@Wp-_tH_v\r"
b4,r,lsb,xy         .. text: "fdD\"\"\"\" "
b4,r,msb,xy         .. text: "%Q#gpSv0c05"
b4,g,lsb,xy         .. text: "fDfffDD\"\""
b4,g,msb,xy         .. text: "f\"fff\"\"DD"
b4,b,lsb,xy         .. text: "\"$BDDDDf"
b4,b,msb,xy         .. text: "wwBDDDfUU53w"
b4,rgb,msb,xy       .. text: "dUcv%F#A`"
b4,bgr,msb,xy       .. text: " V\"c7Ga4"
b4,abgr,msb,xy      .. text: "gOC_$_@o"
```

### FLAG: picoCTF{h1d1ng_1n_th3_b1t5}


