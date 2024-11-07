# Forensics 
## Trivial Flag Transfer Protocol 
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

New concepts:
1.  Wireshark which is a network protocol analyzer that captures and displays network traffic in real time can be used for pcapng file type. 
2. We need to analyze the data transfer and conversations, especially the number of bytes transferred, to narrow down our search.
3. rot13 is a simple letter substitution cipher which replaces a letter with the 13th letter in the English alphabet.
4. I worked with a .deb file for the first time. I looked up how to open it on terminal on macOS and came across `ar x <filename>` and `tar -xvf data.tar.xz` commands.


Errors:
1. Initially, I did not know how to use Wireshark so I was not properly analyzing the network traffic but after referring to some videos, I figured out how to filter the search. 
2. Since I was working on macOS, I tried downloading steghide using Homebrew but it gave errors. So I installed it from sourceforge. I also had to installed macports and set it up in order to use the steghide command.

References: [Forensics intro](https://trailofbits.github.io/ctf/forensics/)
[Wireshark intro](https://www.youtube.com/watch?v=ZNS115MPsO0 )
[Steghide](https://medium.com/the-kickstarter/steganography-on-kali-using-steghide-7dfd3293f3fa)

Flag link: picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
