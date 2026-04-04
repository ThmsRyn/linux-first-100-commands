# Text Manipulation

15 commands for reading, filtering, and transforming text files and streams.

---

### cat

**What it does:** Concatenates and prints file contents to stdout.

**Basic usage:**
```bash
cat /etc/os-release
```

**Real-world example:**
```bash
# Quickly read a config file without opening an editor
cat /etc/ssh/sshd_config

# Concatenate two log files into one
cat /var/log/app/app.log.1 /var/log/app/app.log > /tmp/combined.log
```

**Tip:** For large files, use `less` instead. `cat` on a 2GB log will flood your terminal.

---

### less

**What it does:** Opens a file for paginated reading — navigate forward and backward without loading the whole file.

**Basic usage:**
```bash
less /var/log/syslog
```

**Real-world example:**
```bash
# Follow a log file and search for ERROR occurrences
less +F /var/log/nginx/error.log
# Press Ctrl+C to stop following, then type /ERROR to search
```

**Tip:** Inside `less`, press `G` to jump to the end, `g` to jump to the beginning, and `/pattern` to search.

---

### head

**What it does:** Prints the first N lines of a file (default: 10).

**Basic usage:**
```bash
head -20 /var/log/syslog
```

**Real-world example:**
```bash
# Check the header of a CSV before processing it
head -5 /data/exports/users.csv

# Verify that a log rotation happened and the new file starts fresh
head -3 /var/log/nginx/access.log
```

---

### tail

**What it does:** Prints the last N lines of a file, with an option to follow new output in real time.

**Basic usage:**
```bash
tail -50 /var/log/auth.log
```

**Real-world example:**
```bash
# Watch application logs live during a deployment
tail -f /var/log/myapp/production.log

# Watch multiple log files simultaneously
tail -f /var/log/nginx/access.log /var/log/nginx/error.log
```

**Tip:** `tail -f` is your best friend during deployments. Press `Ctrl+C` to stop.

---

### grep

**What it does:** Searches for lines matching a pattern in files or stdin.

**Basic usage:**
```bash
grep "ERROR" /var/log/app/app.log
```

**Real-world example:**
```bash
# Find all failed SSH login attempts in auth.log
grep "Failed password" /var/log/auth.log | tail -50

# Recursively search all config files for a specific value
grep -r "max_connections" /etc/postgresql/

# Count how many 500 errors occurred in the last nginx access log
grep " 500 " /var/log/nginx/access.log | wc -l
```

**Tip:** `-i` makes the search case-insensitive. `-n` adds line numbers. `-v` inverts the match (show lines that do NOT match).

---

### sed

**What it does:** Stream editor — finds and replaces text, or performs transformations on a file line by line.

**Basic usage:**
```bash
sed 's/old-value/new-value/g' config.conf
```

**Real-world example:**
```bash
# Replace a domain name in a config file in place
sed -i 's/staging.example.com/production.example.com/g' /etc/nginx/sites-available/myapp.conf

# Delete all blank lines from a file
sed -i '/^$/d' /etc/hosts
```

**Tip:** Always test without `-i` first to see the output before modifying the file in place.

---

### awk

**What it does:** Processes and transforms text column by column — ideal for structured output like logs, CSVs, and command output.

**Basic usage:**
```bash
awk '{print $1, $4}' /var/log/nginx/access.log
```

**Real-world example:**
```bash
# Print only the IP address and response code from nginx access logs
awk '{print $1, $9}' /var/log/nginx/access.log

# Sum the size of all files listed by ls -l
ls -l /var/log/ | awk '{sum += $5} END {print sum " bytes"}'

# Print lines where the 5th column is greater than 1000
awk '$5 > 1000' /var/log/app/metrics.log
```

**Tip:** `$0` is the entire line. `$1` is the first field. `NF` is the number of fields. The last field is `$NF`.

---

### cut

**What it does:** Extracts specific columns or character ranges from each line of a file.

**Basic usage:**
```bash
cut -d: -f1 /etc/passwd
```

**Real-world example:**
```bash
# Extract just the usernames from /etc/passwd
cut -d: -f1 /etc/passwd

# Get the first 15 characters of each line in a fixed-width log
cut -c1-15 /var/log/app/events.log
```

**Tip:** Use `-d` to set the delimiter and `-f` to select the field number. For CSV parsing, `awk` handles edge cases better.

---

### sort

**What it does:** Sorts lines of text alphabetically or numerically.

**Basic usage:**
```bash
sort /etc/hosts
```

**Real-world example:**
```bash
# Find the top 10 most frequent IPs in nginx access logs
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -10
```

**Tip:** `-n` sorts numerically. `-r` reverses the order. `-k2` sorts by the second column. `-u` removes duplicates.

---

### uniq

**What it does:** Removes or counts consecutive duplicate lines. Must be used after `sort` to catch all duplicates.

**Basic usage:**
```bash
sort /tmp/ips.txt | uniq
```

**Real-world example:**
```bash
# Count how many times each HTTP status code appears in access logs
awk '{print $9}' /var/log/nginx/access.log | sort | uniq -c | sort -rn
```

**Tip:** `uniq -c` prefixes each line with the count of occurrences. `uniq -d` shows only lines that appear more than once.

---

### wc

**What it does:** Counts lines, words, or characters in a file or stdin.

**Basic usage:**
```bash
wc -l /var/log/syslog
```

**Real-world example:**
```bash
# Count how many active connections are in ESTABLISHED state
ss -tn | grep ESTABLISHED | wc -l

# Check how many users are defined in /etc/passwd
wc -l /etc/passwd
```

**Tip:** `-l` counts lines, `-w` counts words, `-c` counts bytes. Use `-l` 95% of the time.

---

### diff

**What it does:** Compares two files line by line and shows what changed.

**Basic usage:**
```bash
diff /etc/nginx/nginx.conf.bak /etc/nginx/nginx.conf
```

**Real-world example:**
```bash
# Compare a config file before and after a change to review the diff
diff -u /etc/ssh/sshd_config.bak /etc/ssh/sshd_config

# Compare two directories recursively
diff -rq /opt/app/v1.2/ /opt/app/v1.3/
```

**Tip:** `-u` produces a unified diff (like git shows). `-r` compares directories recursively.

---

### tr

**What it does:** Translates or deletes individual characters in a stream.

**Basic usage:**
```bash
echo "Hello World" | tr '[:upper:]' '[:lower:]'
```

**Real-world example:**
```bash
# Replace all colons with tabs to reformat /etc/passwd for a spreadsheet
cat /etc/passwd | tr ':' '\t'

# Remove carriage returns from a Windows-formatted file
tr -d '\r' < windows-file.txt > unix-file.txt
```

**Tip:** `tr` operates on characters, not strings. Use `sed` when you need to replace multi-character patterns.

---

### tee

**What it does:** Reads from stdin and writes to both stdout and a file simultaneously.

**Basic usage:**
```bash
echo "deployment started" | tee /var/log/deploy.log
```

**Real-world example:**
```bash
# Run a command, see output live, and save it to a log file at the same time
./deploy.sh 2>&1 | tee /var/log/deploy-$(date +%F).log

# Append to a log file instead of overwriting
tail -f /var/log/app.log | grep "ERROR" | tee -a /var/log/app-errors.log
```

**Tip:** Use `-a` to append instead of overwrite. Without `-a`, `tee` truncates the file on each run.

---

### xargs

**What it does:** Builds and executes commands from stdin — passes a list of arguments to another command.

**Basic usage:**
```bash
find /tmp -name "*.tmp" | xargs rm -f
```

**Real-world example:**
```bash
# Delete all .log files older than 30 days
find /var/log/app/ -name "*.log" -mtime +30 | xargs rm -f

# Run a command on each result with the filename visible
find /etc -name "*.conf" | xargs grep -l "max_connections"
```

**Tip:** Use `-I {}` to insert the argument at a specific position: `find . -name "*.bak" | xargs -I {} mv {} /archive/`.
