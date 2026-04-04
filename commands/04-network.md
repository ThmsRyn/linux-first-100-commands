# Network and Connectivity

15 commands for diagnosing, monitoring, and interacting with the network.

---

### ping

**What it does:** Tests basic connectivity to a host by sending ICMP echo requests.

**Basic usage:**
```bash
ping 8.8.8.8
```

**Real-world example:**
```bash
# Test connectivity with a limited count and timestamp output
ping -c 5 -D 192.168.1.1

# Quickly test if a remote host is reachable from a script
if ping -c 1 -W 2 10.0.0.5 &>/dev/null; then
  echo "Host is up"
fi
```

**Tip:** `-c` limits the number of packets. `-W` sets the timeout in seconds. Firewalls may block ICMP, so a failed ping does not always mean the host is down.

---

### curl

**What it does:** Transfers data to or from a URL — supports HTTP, HTTPS, FTP, and many other protocols.

**Basic usage:**
```bash
curl https://api.example.com/health
```

**Real-world example:**
```bash
# Test an API endpoint with a POST request and JSON body
curl -s -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"name": "thomas", "role": "admin"}' | jq .

# Check HTTP response code only (useful in scripts)
curl -s -o /dev/null -w "%{http_code}" https://example.com/health

# Follow redirects and resolve the final URL
curl -L -I https://example.com
```

**Tip:** `-s` silences progress output. `-o /dev/null` discards the body. `-w` formats the output. These three combined are ideal for health checks in scripts.

---

### wget

**What it does:** Downloads files from the web, with support for recursive downloads and resuming interrupted transfers.

**Basic usage:**
```bash
wget https://example.com/file.tar.gz
```

**Real-world example:**
```bash
# Download a file to a specific directory and resume if interrupted
wget -c -P /opt/downloads/ https://releases.example.com/app-v2.1.0.tar.gz

# Mirror a website for offline access
wget --mirror --convert-links --no-parent https://docs.example.com/
```

**Tip:** Use `curl` for scripting and API calls. Use `wget` for downloading large files or mirroring — it handles resuming better.

---

### ss

**What it does:** Displays socket statistics — which ports are listening, which connections are established, and which processes own them.

**Basic usage:**
```bash
ss -tlnp
```

**Real-world example:**
```bash
# Find which process is listening on port 443
ss -tlnp | grep :443

# Show all established TCP connections
ss -tn state established

# Count active connections per remote IP
ss -tn state established | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -rn
```

**Tip:** `ss` is the modern replacement for `netstat`. `-t` = TCP, `-u` = UDP, `-l` = listening, `-n` = numeric (no DNS resolution), `-p` = show process name and PID.

---

### netstat

**What it does:** Displays network connections, routing tables, and interface statistics (legacy tool, largely replaced by `ss`).

**Basic usage:**
```bash
netstat -tlnp
```

**Real-world example:**
```bash
# Show all listening ports with the process that owns them
netstat -tlnp

# Check if something is already bound to port 8080
netstat -tlnp | grep :8080
```

**Tip:** Prefer `ss` on modern systems. `netstat` may not be installed by default on newer Debian/Ubuntu.

---

### ip

**What it does:** Manages network interfaces, routing, and addresses — the modern replacement for `ifconfig` and `route`.

**Basic usage:**
```bash
ip addr show
```

**Real-world example:**
```bash
# Show all network interfaces and their IP addresses
ip addr show

# Add a temporary IP address to an interface
ip addr add 192.168.1.100/24 dev eth0

# Show the routing table to debug connectivity issues
ip route show

# Bring an interface up or down
ip link set eth0 down
ip link set eth0 up
```

**Tip:** Changes made with `ip` are not persistent across reboots. For permanent config, edit `/etc/netplan/` (Ubuntu) or `/etc/network/interfaces` (Debian).

---

### nmap

**What it does:** Scans networks and hosts to discover open ports, services, and OS information.

**Basic usage:**
```bash
nmap -sV 192.168.1.1
```

**Real-world example:**
```bash
# Scan a host for open ports and detect service versions
nmap -sV -p 22,80,443,3306 10.0.0.5

# Scan an entire subnet to find all live hosts
nmap -sn 192.168.1.0/24

# Scan for open ports without triggering too many alerts (slower but quieter)
nmap -sS -T2 10.0.0.5
```

**Tip:** Not installed by default. Install with `sudo apt-get install nmap`. Only scan networks and hosts you own or have explicit permission to scan. `-sn` is a ping scan (no port scan). `-sV` detects service versions.

---

### traceroute

**What it does:** Shows the network path (hops) packets take to reach a destination, with latency per hop.

**Basic usage:**
```bash
traceroute 8.8.8.8
```

**Real-world example:**
```bash
# Trace the route to a remote host and see where packets are being dropped
traceroute -n api.example.com

# Use TCP instead of UDP to bypass firewalls that block ICMP/UDP
traceroute -T -p 443 api.example.com
```

**Tip:** Not installed by default. Install with `sudo apt-get install traceroute`. `*` means the hop did not respond (firewall or configured to not reply). It does not necessarily mean the route is broken.

---

### dig

**What it does:** Queries DNS servers and returns detailed information about DNS records.

**Basic usage:**
```bash
dig example.com
```

**Real-world example:**
```bash
# Check the A record for a domain
dig A example.com +short

# Query a specific DNS server to verify propagation
dig @8.8.8.8 example.com A

# Check MX records to debug email delivery issues
dig MX example.com

# Perform a reverse DNS lookup
dig -x 93.184.216.34 +short
```

**Tip:** `+short` strips all the extra metadata and returns only the answer. Essential for scripting.

---

### nslookup

**What it does:** Performs DNS lookups to resolve hostnames to IPs and vice versa.

**Basic usage:**
```bash
nslookup example.com
```

**Real-world example:**
```bash
# Verify that a new DNS record has propagated to a specific resolver
nslookup app.example.com 1.1.1.1
```

**Tip:** `dig` is more powerful and scriptable. `nslookup` is useful for quick interactive checks when `dig` is not available.

---

### ssh

**What it does:** Opens a secure encrypted shell session to a remote host.

**Basic usage:**
```bash
ssh user@192.168.1.10
```

**Real-world example:**
```bash
# Connect using a specific private key and a non-standard port
ssh -i ~/.ssh/id_rsa_prod -p 2222 admin@10.0.0.5

# Forward a remote port to localhost (access a remote DB locally)
ssh -L 5432:localhost:5432 admin@db-server.example.com

# Run a single command remotely without opening an interactive shell
ssh admin@10.0.0.5 "systemctl status nginx"
```

**Tip:** Use `~/.ssh/config` to store host aliases, keys, and port settings so you never have to type long ssh commands again.

---

### scp

**What it does:** Copies files securely between hosts over SSH.

**Basic usage:**
```bash
scp /local/file.txt user@remote:/remote/path/
```

**Real-world example:**
```bash
# Copy a backup from a remote server to local storage
scp -i ~/.ssh/id_rsa_prod admin@10.0.0.5:/backups/db-2026-04-01.tar.gz /mnt/nas/backups/

# Recursively copy a directory to a remote host
scp -r /var/www/html/ admin@web01:/var/www/html/
```

**Tip:** For large or repeated transfers, prefer `rsync` — it transfers only changed data and can resume interrupted transfers.

---

### rsync

**What it does:** Synchronizes files between two locations efficiently — transfers only the differences.

**Basic usage:**
```bash
rsync -avz /local/dir/ user@remote:/remote/dir/
```

**Real-world example:**
```bash
# Daily incremental backup of /var/www to a backup server
rsync -avz --delete /var/www/ admin@backup01:/backups/www/

# Copy files over SSH using a specific key and port
rsync -avz -e "ssh -i ~/.ssh/id_rsa_prod -p 2222" /data/ admin@10.0.0.5:/data/

# Dry run to see what would be transferred without actually doing it
rsync -avzn --delete /var/www/ admin@backup01:/backups/www/
```

**Tip:** `--delete` removes files on the destination that no longer exist on the source. Always test with `-n` (dry run) before using `--delete`.

---

### tcpdump

**What it does:** Captures and analyzes raw network traffic on a given interface.

**Basic usage:**
```bash
tcpdump -i eth0
```

**Real-world example:**
```bash
# Capture HTTP traffic on port 80 and save it to a file for analysis in Wireshark
tcpdump -i eth0 port 80 -w /tmp/capture-$(date +%F).pcap

# Watch DNS queries in real time
tcpdump -i eth0 -n port 53

# Capture only traffic to/from a specific host
tcpdump -i eth0 host 10.0.0.5
```

**Tip:** `-n` disables hostname resolution for faster output. `-w` saves to a `.pcap` file you can open in Wireshark for deep inspection.

---

### ufw

**What it does:** Manages the system firewall with a simplified interface over iptables.

**Basic usage:**
```bash
ufw status
```

**Real-world example:**
```bash
# Allow SSH and HTTPS, deny everything else
ufw default deny incoming
ufw default allow outgoing
ufw allow 22/tcp
ufw allow 443/tcp
ufw enable

# Allow a specific IP to connect to PostgreSQL
ufw allow from 10.0.0.20 to any port 5432

# Check the current rules with line numbers (for deletion)
ufw status numbered
```

**Tip:** Always ensure port 22 is allowed before enabling `ufw` on a remote server — or you will lock yourself out.
