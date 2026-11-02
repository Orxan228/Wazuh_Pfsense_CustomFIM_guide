# SOC Lab Build Guide

A step-by-step guide to building a small **Security Operations Center (SOC) lab** on one computer with VMware Workstation. You set up a pfSense firewall, a Wazuh SIEM that collects logs from every machine, and your own Python file-integrity monitor (FIM) that sends alerts into it.

The guide is written for students: short sentences, copy-paste commands, real screenshots and annotated drawings of each screen.

**Open the guide:** download [`soc-lab-guide.html`](soc-lab-guide.html) and open it in any browser. It is one file and works offline. Use the **EN / RU** switch at the top to change the language.

> Руководство доступно на английском и русском языках — переключатель **EN / RU** вверху страницы.

---

## What you build

```
                Internet (VMware NAT)
                        │
                ┌───────┴────────┐
                │  pfSense CE    │  firewall · routing · DHCP · DNS
                └───┬────────┬───┘
     LAN1 192.168.10.0/24    LAN2 192.168.20.0/24
        │                       │
  ┌─────┴─────┐  ┌──────────┐   ┌┴───────────────┐
  │ Windows 11│  │   Kali   │   │ Wazuh 4.14 SIEM│
  │ .10.10    │  │  .10.20  │   │ .20.10         │
  └───────────┘  └──────────┘   └────────────────┘
   agent + FIM    agent + FIM    collects all logs
```

- Two isolated networks (VMware LAN segments) joined only by pfSense.
- Firewall rules with aliases: workstations may reach the SIEM only on the ports it needs, and everything else is blocked and logged.
- Wazuh agents on Kali and Windows, plus pfSense firewall logs over syslog.
- Custom detection rules for Windows brute force, SSH brute force and port scans.
- Your own Python FIM (watchdog + SHA-256 + JSON log), with a `FIM-INTEGRITY-VIOLATION` alert in Wazuh.
- An end-to-end demo: a changed file becomes a SIEM alert.

## Software versions

| Software | Version |
|---|---|
| VMware Workstation Pro | 26H1 (or 17.6.x) |
| pfSense CE | 2.8.1 |
| Wazuh | 4.14 (all-in-one) |
| Ubuntu Server | 22.04 LTS |
| Kali Linux | 2026.x VMware image |
| Windows | 11 Enterprise evaluation (or 10 22H2) |
| Python | 3.12–3.14 (not 3.15 yet) |

## Your computer

| RAM | What to expect |
|---|---|
| **16 GB** | Recommended. Runs pfSense, Wazuh and one client at a time comfortably. |
| **8 GB** | Possible: build the lab in stages and run one client at a time. The guide has a dedicated "Only 8 GB RAM?" plan. Slower. |
| **Less than 8 GB** | Not enough for the full lab on one machine. The guide lists options. |

You also need about **150 GB of free disk space**, preferably on an SSD.

## Contents of the guide

| Step | Topic |
|---|---|
| 00 | The big picture |
| 01 | Virtual machines and networks (incl. Windows 11 install, admin PowerShell, VMware Tools) |
| 02 | Install and configure pfSense |
| 03 | Fixed IPs on Kali and Windows |
| 04 | Firewall rules |
| 05 | Install the Wazuh SIEM |
| 06 | Connect the agents |
| 07 | Send pfSense logs to Wazuh |
| 08 | Search events like a SOC analyst |
| 09 | Write your own detection rules |
| 10 | Build your Python FIM (no code given: this part is yours) |
| 11 | Connect your FIM to Wazuh |
| 12 | End-to-end demo |
| 13 | Bonus tasks |
| 14 | Report and checklist (progress is saved in your browser) |

## Testing

The core lab (steps 01–12) was built and tested end to end in November 2026 on VMware Workstation 26H1, with pfSense 2.8.1, Wazuh 4.14.9, Ubuntu 22.04.5, Kali 2026.3, Windows 11 Enterprise evaluation and Python 3.14. Fixes found during testing are included. Two examples:
- A decoder that lets Wazuh read pfSense 2.8 firewall logs.
- A corrected FIM base rule.

Not tested:
- The 8 GB RAM plan.
- The bonus tasks.
- Screens labelled as **drawings** in the guide. They imitate the real screen for clarity, so small differences are possible.

## Safe use

This lab is for learning in an isolated environment that you own.
- Run the scans, failed-login tests and file changes only against your own lab VMs.
- Never point them at networks or systems you do not have permission to test.
- Use your own passwords. The ones shown in the guide are defaults or examples.

---

*Lab guide for a network-security course project · checked November 2026.*
