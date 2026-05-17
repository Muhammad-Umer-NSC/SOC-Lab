# 🛡️ SOC Home Lab

A hands-on, multi-part series where I build a fully functional Security Operations Center (SOC) environment from scratch using open-source tools — all inside a contained virtual network on a single machine.

Every step in this series has been personally performed and verified, with screenshots included in each part. Whether you're studying for a security certification, building your portfolio, or just want to understand how enterprise SOC tools actually work — this series is for you.

---

## 🧱 Lab Architecture

The entire lab runs inside **VMware Workstation** using two virtual network interfaces to keep all lab traffic isolated from your main network:

| Interface | Type | Network | Purpose |
|-----------|------|---------|---------|
| `VMnet8` | NAT | — | Internet access for VMs |
| `VMnet1` | Host-Only | `150.1.7.0/24` | Isolated internal lab network |

**IP Assignments:**

| Machine | Role | IP Address |
|---------|------|-----------|
| Wazuh Server | SIEM + XDR | `150.1.7.99` |
| Host Machine | Management | `150.1.7.100` |
| Windows 10 VM | Wazuh Agent | `150.1.7.102` |

---

## 📚 Series Roadmap

| Part | Title | Status |
|------|-------|--------|
| [Part 1](./part-01-wazuh-setup/) | Installing & Configuring Wazuh (SIEM + XDR) | ✅ Published |
| [Part 2](./part-02-agent-deployment/) | Deploying a Windows 10 Agent & Sysmon | ✅ Published |
| Part 3 | Integrating VirusTotal for Malware Alerting | 🔜 Coming Soon |

> New parts are added as the series progresses. Star or watch the repo to get notified.

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|------|---------|
| **VMware Workstation** | Hypervisor — runs all virtual machines |
| **Wazuh** | Open-source SIEM + XDR platform |
| **Sysmon** | Advanced Windows event logging |
| **VirusTotal** | Threat intelligence and malware detection *(Part 3)* |

---

## 📋 Prerequisites

Before starting the series, make sure your host machine meets the following:

- Virtualization enabled (Intel VT-x or AMD-V) — confirmed via Task Manager
- At least **8GB RAM** on the host (4GB minimum allocated to Wazuh VM)
- At least **100GB free disk space** (50GB per VM)
- A stable internet connection for downloading ISOs, OVA files, and packages

---

## 📁 Repository Structure

```
soc-home-lab/
│
├── README.md                        ← You are here — series overview
│
├── part-01-wazuh-setup/
│   ├── README.md                    ← Full Part 1 guide
│   └── screenshots/
│
├── part-02-agent-deployment/
│   ├── README.md                    ← Full Part 2 guide
│   └── screenshots/
│
└── part-03-virustotal/              ← Coming soon
```

---

## 🚀 Getting Started

If you're new to the series, start at **Part 1** — each part builds on the previous one.

**Recommended order:**

1. [Part 1 — Install and configure Wazuh](./SOC-Lab-Part1)
2. [Part 2 — Deploy a Windows 10 agent and Sysmon](./SOC-Lab-Part2)
3. Part 3 — Integrate VirusTotal *(coming soon)*

---

## 👤 Author

Built and documented hands-on — every command, every config, every screenshot is from a real working lab environment.

---

## ⭐ Support

If this series is helping you, consider starring the repo — it helps others in the security community find it.
