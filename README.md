# 🚨 🔍 IDS Enhancement: Suricata with Splunk Integration

This module demonstrates how to enhance threat detection using **Suricata** for real-time network intrusion detection and **Splunk** for advanced log analysis and alerting. It simulates reconnaissance activity (e.g., **Nmap SYN scans**) and validates detection visibility through Splunk dashboards.

---

### 🎯 Goals

* Configure Suricata to detect suspicious network activity (e.g., scans, exploits)
* Forward Suricata’s JSON logs (`eve.json`) to Splunk
* Simulate a network scan using **Nmap**
* Visualize alerts and generate detections inside Splunk

---

### 🛠️ Tools Involved

| ToolPurpose |                                         |
| ----------- | --------------------------------------- |
| Suricata    | Network-based IDS engine                |
| Splunk      | SIEM/log analysis platform              |
| Nmap        | Network scan simulation (attack vector) |
| Linux VM    | Hosting Suricata and simulation scripts |

---

### 🏗️ Data Flow Architecture

```
less
```

CopyEdit

`[ Attacker (Nmap) ] | v [ Suricata Sensor (eve.json logs) ] | v [ Splunk Indexer + Dashboard ]`

---

### ⚙️ Setup Guide

#### 1. Install Suricata on Linux

```
bash
```

CopyEdit

`sudo apt update sudo apt install suricata -y`

* Configure `suricata.yaml`:

  * Set the correct network interface (e.g., `eth0`)
  * Enable `eve.json` output under `outputs:` section

```
yaml
```

CopyEdit

`outputs: - eve-log: enabled: yes filetype: regular filename: /var/log/suricata/eve.json`

#### 2. Set Up Splunk

* Install Splunk on the same or a separate VM
* Configure a data input to monitor `/var/log/suricata/eve.json`

Example input configuration (`inputs.conf`):

```
ini
```

CopyEdit

`[monitor:///var/log/suricata/eve.json] sourcetype = suricata index = ids`

Use either the Universal Forwarder or directly ingest logs for small-scale testing.

#### 3. Simulate Attack with Nmap

Run the following from a Kali or attacker VM:

```
bash
```

CopyEdit

`nmap -sS <target-ip> -p 1-1000`

Suricata should trigger alerts such as:

* `ET SCAN Nmap -sS window scan`
* `ET SCAN Nmap Scripting Engine User-Agent Detected`

---

### 📈 Detecting & Searching in Splunk

Use the following SPL query to detect Nmap or scanning behavior:

```
spl
```

CopyEdit

`index=ids sourcetype=suricata "ET SCAN" OR "Nmap"`

#### Recommended Dashboards:

* 📊 Top Source IPs by Alert Count
* 📊 Alert Types (Scan, Exploit, Brute-force)
* 📊 Time-based Scan Activity
* 📊 Alert Severity Breakdown

---

### 🧪 Use Case Summary

| Attack ScenarioDetection MechanismTool |                                 |          |
| -------------------------------------- | ------------------------------- | -------- |
| Nmap SYN Scan                          | ET SCAN -sS rule                | Suricata |
| Malicious Traffic                      | Signature-based packet matching | Suricata |
| Log Correlation                        | Indexed and visualized          | Splunk   |

---

### ✅ Outcome

This IDS integration validates how reconnaissance and malicious behavior can be detected at the network layer using Suricata and analyzed in Splunk. It's ideal for blue team training, SOC simulations, or home lab demonstrations of SIEM capabilities.

---

&#x20;

