# Part 1 — Installing & Configuring Wazuh (SIEM + XDR)
![Wazuh Logo](./Screenshots/wazuh.jpeg)

**Series:** [SOC Home Lab](../README.md) | **Part:** 1 of N

---

## 📖 Table of Contents

1. [What is SIEM?](#what-is-siem)
2. [What is XDR?](#what-is-xdr)
3. [What is Wazuh?](#what-is-wazuh)
4. [Lab Network Overview](#lab-network-overview)
5. [Step 1 — Enable Virtualization](#step-1--enable-virtualization)
6. [Step 2 — Install VMware Workstation](#step-2--install-vmware-workstation)
7. [Step 3 — Download Wazuh OVA](#step-3--download-wazuh-ova)
8. [Step 4 — Import Wazuh into VMware](#step-4--import-wazuh-into-vmware)
9. [Step 5 — Configure VM Hardware](#step-5--configure-vm-hardware)
10. [Step 6 — Boot Wazuh & Assign Static IP](#step-6--boot-wazuh--assign-static-ip)
11. [Step 7 — Configure Host Network Adapter](#step-7--configure-host-network-adapter)
12. [Step 8 — Access the Wazuh Dashboard](#step-8--access-the-wazuh-dashboard)
13. [Troubleshooting](#troubleshooting)

---

## What is SIEM?

**SIEM (Security Information and Event Management)** is a solution that collects, aggregates, and analyzes log data from across your environment — endpoints, servers, firewalls, applications — in one centralized place.

Think of it as the **nerve center** of a SOC. Instead of logging into 20 different systems to investigate an alert, a SIEM pulls everything together so analysts can detect threats, correlate events, and respond faster.

Key capabilities:
- Log collection and normalization
- Real-time alerting and threat detection
- Compliance reporting
- Security event correlation

---

## What is XDR?

**XDR (Extended Detection and Response)** takes things further than traditional endpoint detection. Where EDR (Endpoint Detection and Response) focuses only on endpoints, XDR extends visibility across:

- Endpoints
- Networks
- Cloud environments
- Applications

It correlates data across all these sources to give a fuller picture of an attack — detecting threats that would otherwise slip through the cracks of siloed tools.

---

![Wazuh Logo](./Screenshots/Wazuh%20SIEM%20+%20XDR.png)

## What is Wazuh?

**Wazuh** is a free, open-source security platform that combines **SIEM and XDR capabilities** in a single solution. It's widely used in both enterprise environments and home labs because of its power and flexibility.

Core features include:
- **Threat detection** — rule-based alerting engine with thousands of out-of-the-box rules
- **Log data analysis** — collects and analyzes logs from agents and external sources
- **Integrity monitoring** — detects unauthorized changes to files and configurations
- **Vulnerability detection** — identifies known CVEs on monitored systems
- **Incident response** — active response capabilities to contain threats automatically

Official site: [https://wazuh.com](https://wazuh.com)

---

## Lab Network Overview

All lab traffic runs inside a contained VMware virtual network:

| Interface | Type | Network | Purpose |
|-----------|------|---------|---------|
| `VMnet8` | NAT | — | Internet access |
| `VMnet1` | Host-Only | `150.1.7.0/24` | Internal lab network |

**IP Assignments:**

| Machine | IP Address |
|---------|-----------|
| Wazuh Server | `150.1.7.99` |
| Host Machine (VMnet1) | `150.1.7.100` |

---

## Step 1 — Enable Virtualization

Before anything else, make sure hardware virtualization (Intel VT-x or AMD-V) is enabled on your machine.

**To check:** Open Task Manager → Performance tab → CPU → look for **Virtualization: Enabled**

If it shows **Disabled**, you'll need to enable it in your BIOS/UEFI. Follow this guide:
🎬 [How to Enable Virtualization in BIOS](https://www.youtube.com/watch?v=XzD8JIAOk2I)

---

## Step 2 — Install VMware Workstation

Download VMware Workstation Pro for free from the official VMware website:
🔗 [https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion](https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion)

> **Tip:** You can create a free account using a temporary email if you don't want to use your personal one.

If you need a walkthrough for the download and install process:
🎬 [VMware Workstation Installation Guide](https://www.youtube.com/watch?v=XzD8JIAOk2I)

---

## Step 3 — Download Wazuh OVA

Download the latest Wazuh OVA (pre-built virtual machine image):
🔗 [https://packages.wazuh.com/4.x/vm/wazuh-4.14.5.ova](https://packages.wazuh.com/4.x/vm/wazuh-4.14.5.ova)

> **Note:** If the link above is outdated, always find the latest version from the official documentation:
> 🔗 [https://documentation.wazuh.com/current/deployment-options/virtual-machine/virtual-machine.html](https://documentation.wazuh.com/current/deployment-options/virtual-machine/virtual-machine.html)

![Vmware Home Page](./Screenshots/VMware%20Home%20Tab.png)

---

## Step 4 — Import Wazuh into VMware

1. Open **VMware Workstation**
2. Click **File** → **Open**
3. Browse to where you saved the `.ova` file and select it
4. Choose an import location and name the VM **Wazuh**
5. Make sure you have at least **50GB of free disk space** at the import location
6. Click **Import** and wait for it to finish



---

## Step 5 — Configure VM Hardware

Once the import is complete, configure the VM hardware before booting:

**Memory:**
- Double-click on **Memory** and set it to at least **4GB (4096 MB)**
- More is better if your host machine has it to spare

**Processor:**
- Click on **Processors**
- Make sure **Intel VT-x/EPT** is checked

**Add a Second Network Adapter:**
- Click **Add** → select **Network Adapter** → click **Finish**
- Set **Network Adapter 1** to **NAT (VMnet8)**
- Set **Network Adapter 2** to **VMnet1**

![Adding Network Adapters and more.jpeg](./Screenshots/Adding%20Network%20Adapters%20and%20more.jpeg)

**Upgrade Virtual Hardware:**
- Click **Upgrade this virtual machine**
- Click **Next**
- Select the latest VMware Workstation version
- Select **Alter this VM**
- Click **Finish**

![Upgrading VM.jpeg](./Screenshots/Upgrading%20VM.jpeg)

---

## Step 6 — Boot Wazuh & Assign Static IP

**Boot the VM:**

Start the Wazuh VM and wait for it to fully load. Then log in with the default credentials:

```
Username: wazuh-user
Password: wazuh
```

![Wazuh Terminal After login.jpeg](./Screenshots/Wazuh%20Terminal%20After%20login.jpeg)

**Switch to root:**

```bash
sudo su
```

**Configure eth0 (NAT interface — DHCP):**

```bash
nano /etc/systemd/network/20-eth0.network
```

Delete everything in the file and enter the following:

```
[Match]
Name=eth0

[Network]
DHCP=ipv4
```

Save and exit: `Ctrl+O` → `Enter` → `Ctrl+X`

![NAT Settings.jpeg](./Screenshots/NAT%20Settings.jpeg)

**Configure eth1 (Internal interface — Static IP):**

```bash
nano /etc/systemd/network/30-eth1.network
```

Enter the following:

```
[Match]
Name=eth1

[Network]
Address=150.1.7.99/24
```

Save and exit: `Ctrl+O` → `Enter` → `Ctrl+X`

![VMNet1 Settings.jpeg](./Screenshots/VMNet1%20Settings.jpeg)

**Reboot:**

```bash
reboot
```

Log back in after rebooting and verify the IP was assigned:

```bash
ifconfig
```

You should see `150.1.7.99` assigned to `eth1`.

![Confirming Network Settings after reboot.jpeg](./Screenshots/Confirming%20Network%20Settings%20after%20reboot.jpeg)

---

## Step 7 — Configure Host Network Adapter

Now configure your host machine so it can communicate with the Wazuh VM over VMnet1:

1. Open **Control Panel** → **Network and Internet** → **Network and Sharing Center**
2. Click **Change adapter settings** (left panel)
3. Double-click **VMnet1**
4. Click **Properties**
5. Double-click **Internet Protocol Version 4 (TCP/IPv4)**
6. Select **Use the following IP address** and enter:

```
IP Address:   150.1.7.100
Subnet Mask:  255.255.255.0
```

7. Click **OK**

---

## Step 8 — Access the Wazuh Dashboard

Open your browser and navigate to:

```
https://150.1.7.99
```

![Finding vmnet on base machine.jpeg](./Screenshots/Finding%20vmnet%20on%20base%20machine.jpeg)

> If your browser warns that the site isn't secure, click **Advanced** → **Continue to site**. This is expected since Wazuh uses a self-signed certificate by default.

Wait a minute or two for the dashboard to fully initialize. Once the login page appears, enter:

![Finding vmnet on base machine.jpeg](./Screenshots/Wazuh%20Login%20page.png)

```
Username: admin
Password: admin
```

You're in! 🎉

![Wazuh Dashboard.png](./Screenshots/Wazuh%20Dasboard.png)

---

## Troubleshooting

**"Intel VT-x is not enabled" error on boot:**

🎬 [Fix Intel VT-x Error in VMware](https://www.youtube.com/watch?v=jmZlToxUJKY)

---

**"Wazuh dashboard is not ready yet" — page won't load:**

Wait a few minutes and reload. If it still doesn't load, SSH into the Wazuh VM and restart the services:

```bash
sudo systemctl restart wazuh-manager
sudo systemctl restart wazuh-indexer
sudo systemctl restart wazuh-dashboard
```

Then reload the browser page and wait a few minutes before trying to log in again.

---

**API error after logging in:**

Restart all three services and refresh:

```bash
sudo systemctl restart wazuh-manager
sudo systemctl restart wazuh-indexer
sudo systemctl restart wazuh-dashboard
```

Reload the page, wait a few minutes, and log in again.

---

## ✅ What We Covered

By the end of this part you have:

- A solid understanding of what SIEM, XDR, and Wazuh are
- VMware Workstation installed and configured
- Wazuh running as a VM with a static IP on an isolated internal network
- The Wazuh dashboard accessible from your host browser

---

## ➡️ Next Part

*Part 2 coming soon — stay tuned!*

[← Back to Series Home](../README.md)
