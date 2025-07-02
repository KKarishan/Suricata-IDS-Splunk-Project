# Generate & Transfer Sample PCAP for Suricata Testing

This guide covers:
1. **Creating a clean PCAP** (Wireshark on Kali)
2. **Transferring it to Ubuntu** (SCP)
3. **Running Suricata** (Ubuntu)

> **🔧 Prerequisites**  
> - Kali Linux (attacker VM)  
> - Ubuntu server (Suricata host)  
> - SSH access between them  

---

## 🎬 Step 1: Generate Sample PCAP on Kali

### 1. Open Wireshark
   - Launch as root:  
     ```bash
     sudo wireshark
     ```
   - Select your active interface (e.g., `eth0`).

### 2. Start Capture
   - Click the **shark fin icon** (or use `Ctrl+E`).
   - *(Optional)* Filter noise:  
     ```
     host 8.8.8.8 or host example.com
     ```

### 3. Generate Traffic
   In a terminal, run:  
   ```bash
   ping -c 3 8.8.8.8          # ICMP
   dig example.com             # DNS
   curl http://example.com     # HTTP
   ```

### 4. Save the PCAP
   - Stop capture after ~10 sec (**red square button**).
   - Save as:  
     ```
     /home/kali/Suricata-IDS-Splunk-Project/pcaps/sample.pcap
     ```
---

## 📤 Step 2: Transfer PCAP to Ubuntu

### 1. From Kali, run:
   ```bash
   scp /home/kali/Suricata-IDS-Splunk-Project/pcaps/sample.pcap \
   ubuntu@192.168.1.50:~/Suricata-IDS-Splunk-Project/pcaps/
   ```
   - Replace `192.168.1.50` with your Ubuntu server’s IP.

### 2. Enter Ubuntu password when prompted.
---

## 🖥️ Step 3: Run Suricata on Ubuntu

### 1. SSH into Ubuntu:
   ```bash
   ssh ubuntu@192.168.1.50
   ```

### 2. Verify PCAP:
   ```bash
   ls ~/Suricata-IDS-Splunk-Project/pcaps/
   ```

### 3. Process with Suricata:
   ```bash
   cd ~/Suricata-IDS-Splunk-Project
   sudo suricata -r pcaps/sample.pcap -l logs/
   ```

### 4. Check logs:
   ```bash
   cat logs/eve.json | jq 'select(.event_type=="alert")'
   ```
---

## ❓ Troubleshooting

| Issue | Solution |
|-------|----------|
| Wireshark permissions | Run `sudo wireshark` or add user to `wireshark` group |
| SCP fails | Check SSH service (`sudo systemctl status sshd`) and firewall |
| No alerts in `eve.json` | Test with `-v` flag: `sudo suricata -r pcaps/sample.pcap -v` |

