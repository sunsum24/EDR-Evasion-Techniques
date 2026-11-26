# 🥷 EDR Evasion Techniques (Red Team)

<a href="https://attack.mitre.org/tactics/TA0005/">
  <img src="https://img.shields.io/badge/Red_Team-Evasion-red?style=for-the-badge" alt="Red Team Evasion" />
</a>
<a href="https://attack.mitre.org/techniques/T1562/">
  <img src="https://img.shields.io/badge/MITRE_ATT%26CK-T1562-blue?style=for-the-badge" alt="MITRE T1562" />
</a>

## 📌 Executive Summary
During a Red Team engagement, delivering a payload is only half the battle. Modern organizations deploy Endpoint Detection and Response (EDR) solutions (e.g., CrowdStrike, SentinelOne, Defender for Endpoint) that analyze process behavior, memory, and API calls.

This repository documents the fundamental techniques used by Red Teams to bypass EDR and evade detection (MITRE ATT&CK **TA0005 - Defense Evasion**).

## 🛠️ Techniques & Payloads

### 1. API Unhooking (User-Land Evasion)
**Concept:** EDRs place hooks in user-land APIs (like `ntdll.dll`) to monitor what a program is doing. Unhooking removes these EDR hooks from memory before executing the payload.
**Technique:** Manually mapping a clean copy of `ntdll.dll` from the disk into memory, overwriting the EDR's hooks.
**MITRE:** T1562.001 (Impair Defenses: Disable or Modify Tools)

### 2. Direct System Calls (Syscalls)
**Concept:** Instead of calling Windows APIs (which are hooked by the EDR), the malware communicates directly with the Windows Kernel. 
**Technique:** Using SysWhispers or Hell's Gate to dynamically resolve system call numbers and execute them directly.
**MITRE:** T1106 (Native API)

### 3. Process Injection (Remote Thread)
**Concept:** Hiding malicious code inside a legitimate, trusted process (e.g., `explorer.exe` or `notepad.exe`).
**Technique:** Allocating memory in a remote process using `VirtualAllocEx`, writing the payload with `WriteProcessMemory`, and executing it with `CreateRemoteThread`.
**MITRE:** T1055 (Process Injection)

### 4. AMSI Bypass (PowerShell)
**Concept:** The Antimalware Scan Interface (AMSI) scans scripts before execution. Bypassing it allows malicious PowerShell to run undetected.
**Technique:** Patching the `amsi.dll` in memory to force it to always return `AMSI_RESULT_CLEAN`.
**MITRE:** T1562.001

### 5. Payload Obfuscation (Sleeping & Encryption)
**Concept:** EDRs scan memory for known malicious byte signatures (like Meterpreter shells). 
**Technique:** Encrypting the payload in memory and only decrypting it right before execution. Using "Sleep" techniques to keep the payload encrypted while not in use.
**MITRE:** T1027 (Obfuscated Files or Information)

---

## 🛡️ Defensive Countermeasures (Blue Team)
To defend against these advanced evasion techniques, organizations should:
1. **Enable Kernel-Level Telemetry:** EDRs must be configured to monitor Kernel-level events (ETW) rather than just user-land API hooks.
2. **Behavioral Analytics:** Focus on detecting the *behavior* (e.g., a Word document spawning a PowerShell shell) rather than the specific payload signature.
3. **Memory Scanning:** Use tools that scan unbacked executable memory regions (where malicious payloads often hide).

## ⚠️ Disclaimer
This documentation is for authorized Red Team engagements and educational purposes only. 
