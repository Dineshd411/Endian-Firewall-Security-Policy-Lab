# Endian Firewall UTM — Installation, Configuration & Security Policy Lab

A hands-on lab covering the full lifecycle of a **UTM firewall appliance**: bare-metal installation of **Endian Firewall Community** on repurposed desktop hardware, GREEN/RED zone network setup, custom firewall rules, HTTP proxy and category-based web filtering, per-client access policies, and live log verification — built and tested end-to-end on physical hardware.

---

## 📌 Project Objective

Turn a spare desktop PC into a fully functional **Unified Threat Management (UTM) firewall** using Endian Firewall Community: install the OS from bootable media, configure the GREEN (LAN) and RED (WAN/uplink) network zones, write and test outbound firewall rules (including an ICMP block), enable the HTTP proxy with category-based web filtering plus a custom domain blacklist, apply an IP-specific access policy, and confirm every control is actually working from both the appliance's live logs and a real client machine.

---

## 🖧 Environment Overview

| Component | Details |
|---|---|
| Appliance Hardware | Repurposed desktop PC (Acer, additional NIC installed for multi-zone networking) |
| Firewall OS | Endian Firewall Community, release 3.3.2 (64-bit) |
| Hostname | `Lab_Test` |
| GREEN Zone (LAN) | `eth1` — `192.168.0.15/24`, Web GUI: `https://192.168.0.15:10443` |
| RED Zone (Uplink) | `eth2` — DHCP, `10.10.11.108/24` |
| Bridge Interface | `br0` |
| Test Client | Windows PC on the GREEN network (`192.168.0.30` / `192.168.0.34`) |
| Services Enabled | HTTP Proxy, Web Filter, Firewall (Outgoing traffic), Logs and Reports |

---

## ⚙️ Step 1 — Hardware Preparation & Boot Media

Repurposed a desktop PC as the firewall appliance, wiring the onboard NIC and an added NIC card for separate GREEN/RED interfaces, then booted from the Endian installer USB and stepped through language selection, the install warning, and disk partitioning.

![Hardware Back Panel and NIC Wiring](images/01-hardware-back-panel.jpg)
![Installer Language Selection](images/02-installer-language-selection.jpg)
![Installer Welcome Screen](images/03-installer-welcome.jpg)
![Installer Detecting Disks](images/04-installer-detecting-disks.jpg)
![Installer Disk Warning](images/05-installer-disk-warning.jpg)
![Installer Partitioning](images/06-installer-partitioning.jpg)

---

## ⚙️ Step 2 — Base Installation

Assigned the initial GREEN IP during setup, reviewed the post-install procedures, confirmed the serial console prompt, and completed the base install.

![Installer GREEN IP Entry](images/07-installer-green-ip-entry.jpg)
![Installer Post-Install Procedures](images/08-installer-post-install-procedures.jpg)
![Installer Serial Console Prompt](images/09-installer-serial-console-prompt.jpg)
![Installer Congratulations Screen](images/10-installer-congratulations.jpg)

---

## ⚙️ Step 3 — First Boot & CLI Network Wizard

Booted the freshly installed appliance to an empty configuration, ran the CLI network wizard from the console, and confirmed the final interface summary — GREEN zone on `eth1` (`192.168.0.15/24`) and the RED uplink active via DHCP on `eth2` (`10.10.11.108/24`).

![Console First Boot, Empty Config](images/11-console-first-boot-empty-config.jpg)
![Console CLI Network Wizard](images/12-console-cli-network-wizard.jpg)
![Console Final Network Summary](images/13-console-final-summary.jpg)

---

## ⚙️ Step 4 — Web GUI Network Setup Wizard

Logged into the Endian web console and completed the 5-step Network Setup Wizard: chose Routed mode with a DHCP uplink, defined the network zones, and set GREEN/RED interface and DNS preferences.

![Wizard Step 1 — Network Mode & Uplink Type](images/14-wizard-step1-network-mode.png)
![Wizard Step 2 — Zone Configuration](images/15-wizard-step2-zones.png)
![Wizard Step 3 — GREEN Preferences](images/16-wizard-step3-green-prefs.png)
![Wizard Step 4 — RED Preferences](images/17-wizard-step4-red-prefs.png)
![Wizard Step 5 — DNS Settings](images/18-wizard-step5-dns.png)

---

## ⚙️ Step 5 — Dashboard & System Verification

Verified the appliance from the Dashboard: version, uptime, hardware utilization, all four network interfaces (`eth0`, `eth1`, `eth2`, `br0`) up, and the active/managed main uplink.

![Dashboard Overview](images/19-dashboard-overview.png)

---

## ⚙️ Step 6 — Outgoing Firewall Rules (ICMP Block)

Created an outgoing firewall rule dropping ICMP from GREEN to RED, added it to the existing outbound service rule set (HTTP, HTTPS, FTP, SMTP, POP, IMAP, DNS, PING), and validated the change from a GREEN-side client: `ping 8.8.8.8` succeeded before the rule was applied and returned "destination port unreachable" from the firewall itself once the DROP rule was active.

![Creating the ICMP Firewall Rule](images/20-firewall-icmp-rule-create.png)
![Current Outgoing Firewall Rules](images/21-firewall-rules-list.png)
![Client Ping Test — Before vs After the Rule](images/22-client-ping-test-before-after.png)
![Client Ping Test Photo Evidence](images/22b-client-ping-test-photo.jpg)

---

## ⚙️ Step 7 — HTTP Proxy Configuration

Enabled the HTTP proxy on the GREEN interface (non-transparent, port `8080`) so client web traffic could be inspected and filtered before reaching the RED uplink.

![HTTP Proxy Configuration](images/23-proxy-http-config.png)

---

## ⚙️ Step 8 — Web Filtering (Categories & Custom Blacklist)

Configured the Web Filter profile to block categories such as **Gambling** and **Games**, then layered a custom blacklist on top to explicitly block `www.youtube.com` and `www.instagram.com` regardless of category.

![Web Filter Profile — Category Selection](images/24-webfilter-profile-categories.png)
![Custom Black- and Whitelists](images/25-webfilter-blacklist.png)

---

## ⚙️ Step 9 — Per-Client Access Policy

Built an HTTP Access Policy scoped to a single client IP (`192.168.0.34`), tied it to the custom Web Filter profile, and placed it at the top of the policy order so it takes precedence over the default rule.

![Creating the Access Policy](images/26-access-policy-create.png)
![Current Access Policy List](images/27-access-policy-list.png)

---

## ✅ Step 10 — Live Logs & Verification

Watched the appliance's live log stream across Firewall, Web Server (proxy), System, and Intrusion Prevention feeds in real time, then reviewed the structured firewall log table to confirm the ICMP drops and other policy hits were being recorded correctly.

![Live Logs — Overview](images/30-live-logs-overview.png)
![Live Logs — Filtered View](images/31-live-logs-filtered.png)
![Firewall Logs Table](images/32-firewall-logs-table.png)

---

## ✅ Results

- Installed **Endian Firewall Community 3.3.2** from bootable media onto repurposed desktop hardware, wired for separate GREEN and RED zones.
- Configured the network from both the **CLI setup wizard** and the **web GUI setup wizard**, bringing up a stable LAN (GREEN) and DHCP-based internet uplink (RED).
- Wrote and validated an **outgoing firewall rule** blocking ICMP, confirmed via live before/after ping tests from a client machine.
- Enabled the **HTTP proxy** and applied **category-based web filtering** plus a **custom domain blacklist**.
- Built a **per-client access policy** scoping the web filter to a specific IP address.
- Verified every control end-to-end using the appliance's **live logs** and the **structured firewall log table**.

---

## 🛠️ Skills Demonstrated

`Endian Firewall Community` `UTM Firewall Administration` `Network Zoning (GREEN/RED)` `Firewall Rule Configuration` `HTTP Proxy` `Content/Web Filtering` `Access Policy Management` `Log Analysis` `Network Troubleshooting` `Linux-Based Appliance Installation`
