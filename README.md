# How-To-Find-Credentials-From-Pcap-files-and-Wireshark

**Objective Task**
  - I was provided with a .pcap file. This is one of the exam question I get for the role analyst.
 
**The objective was to:**
  - Identify what the attacker successfully obtained (e.g., credentials, files).
  - Determine any other activities or data the attacker accessed.
  - Analyze the attack behavior and summarize findings.
 
**Protocol Involved**
  - FTP (File Transfer Protocol)
  - Server software: vsFTPd 3.0.3
 
**Analysis Summary**
  - Upon inspecting the packet capture using Wireshark, and following relevant TCP streams, I observed an attacker successfully logging into the FTP server and performing the following actions:
 
`ftp.response.code == 230`

![image](https://github.com/user-attachments/assets/e73212d2-4992-4d17-b8a3-daa45512d9ed)
This filter captures the FTP response code 230, which indicates a successful login to an FTP server.

**Follow the TCP stream**
  - Following a TCP stream in Wireshark is a powerful method for analyzing the full context of a TCP connection, particularly useful when investigating the flow of data between two endpoints. 

![image](https://github.com/user-attachments/assets/5a55d49e-cd04-4ddc-a165-ec8b780db273)

---- 
**Credentials Used by Attacker**
  - Username: 0xMM0X
  - Password: 326.axommoxa.a4
  - Confirmed by FTP response: 230 Login successful.
---- 
**Attacker Activity Timeline**

| Step | Action | Command | Server Response | 
|------|--------|---------|-----------------|
| 1 | Connected to FTP | USER 0xMM0X / PASS 326.axommoxa.a4 |✅ 230 Login successful|
| 2 | Checked OS type | SYST | 215 UNIX Type: L8 |
| 3 | Set up active mode data connection | PORT commands |200 PORT command successful|
|4|Listed directory contents|LIST|150 Directory listing, then 226 OK|
|5|Switched to binary mode|TYPE I|200 Switching to Binary mode|
|6|Downloaded a file|RETR youarenotsecure.txt|✅ 226 Transfer complete|
|7|Tried to delete contents|RMD * / DELE *|❌ 550 Operation failed|
|8|Logged out|QUIT|221 Goodbye|

---- 
**File Stolen**
  - Filename: youarenotsecure.txt
  - Size: 32 bytes
  - ✅ Confirmed downloaded successfully via:
  - 226 Transfer complete
---- 
**Unsuccessful Actions by Attacker**
  - Tried to delete all directories and files using:
    - RMD *
    - DELE *
  - Server responded with:
    - 550 Remove/Delete operation failed
----
**Conclusion**
  - The attacker successfully authenticated to the FTP server using compromised credentials.
  - They downloaded a sensitive file (youarenotsecure.txt).
  - Although they attempted to delete files or directories, these delete operations failed.
----
**Recommendations**
  - Immediately disable the user account 0xMM0X.
  - Change all FTP passwords and enforce strong password policies.
  - Disable plain FTP; use FTPS or SFTP instead.
  - Review access logs to identify how the credentials may have been compromised.
  - Isolate the affected system and check the integrity of remaining files.
