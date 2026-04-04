# Productivity and Utilities

15 commands and tools that make day-to-day sysadmin and DevOps work faster.

---

### history

**What it does:** Shows the list of previously run commands in the current user's shell history.

**Basic usage:**
```bash
history
```

**Real-world example:**
```bash
# Find the exact command you used to configure something last month
history | grep "certbot"

# Re-run the last command that started with "rsync"
!rsync

# Re-run the 423rd command from history
!423
```

**Tip:** `Ctrl+R` opens a reverse incremental search through history — type part of a command and the last match appears. Press `Ctrl+R` again to cycle through matches.

---

### alias

**What it does:** Creates a shortcut name for a longer command or command with options.

**Basic usage:**
```bash
alias ll='ls -lah'
```

**Real-world example:**
```bash
# Add frequently used aliases to ~/.bashrc or ~/.zshrc
alias ll='ls -lah'
alias gs='git status'
alias k='kubectl'
alias dps='docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"'
alias tf='terraform'

# View all currently defined aliases
alias
```

**Tip:** Aliases defined in the shell only last for the session. Add them to `~/.bashrc` (bash) or `~/.zshrc` (zsh) to persist them.

---

### export

**What it does:** Sets environment variables and makes them available to child processes.

**Basic usage:**
```bash
export DATABASE_URL="postgresql://user:pass@localhost:5432/mydb"
```

**Real-world example:**
```bash
# Set variables needed for a deployment script
export APP_ENV=production
export AWS_REGION=eu-west-1
export TF_VAR_domain=myapp.example.com
./deploy.sh

# Make a custom binary directory available in PATH
export PATH="$PATH:/opt/myapp/bin"
```

**Tip:** Variables set with `export` only last for the current session. Add them to `~/.bashrc` or use a `.env` file loaded by your deployment tooling.

---

### env

**What it does:** Prints all currently set environment variables, or runs a command with a modified environment.

**Basic usage:**
```bash
env
```

**Real-world example:**
```bash
# Check which environment variables are set in the current shell
env | grep -i database

# Run a command with a specific variable overridden, without polluting the current shell
env NODE_ENV=production node /opt/app/server.js

# Run a command with a clean environment (useful for testing)
env -i PATH=/usr/bin:/bin bash --login
```

---

### which

**What it does:** Shows the full path of the executable that would run when you type a command.

**Basic usage:**
```bash
which python3
```

**Real-world example:**
```bash
# Verify which version of a tool is being used when multiple are installed
which node
which python
which pip

# Check if a command exists before calling it in a script
if ! which jq &>/dev/null; then
  apt-get install -y jq
fi
```

---

### whereis

**What it does:** Locates the binary, source code, and man page for a command.

**Basic usage:**
```bash
whereis nginx
```

**Real-world example:**
```bash
# Find all files associated with a command to understand where it was installed from
whereis openssl

# Find only the binary locations
whereis -b curl
```

**Tip:** `which` finds what your shell would execute. `whereis` finds all related files including docs. Use `which` for scripting, `whereis` for investigation.

---

### man

**What it does:** Opens the manual page for a command — the authoritative reference for every option and behavior.

**Basic usage:**
```bash
man rsync
```

**Real-world example:**
```bash
# Read the manual for a command before using an unfamiliar option
man curl

# Search all man pages for a keyword
man -k "disk usage"
```

**Tip:** Inside `man`, press `/` to search, `n` to jump to the next match, `q` to quit. When in doubt, read the man page before running a destructive command.

---

### tldr

**What it does:** Shows simplified, practical examples for a command — the short version of `man`.

**Basic usage:**
```bash
tldr tar
```

**Real-world example:**
```bash
# Quickly recall the syntax for a command you use infrequently
tldr rsync
tldr dd
tldr openssl
```

**Tip:** Not installed by default. Install with `apt install tldr` or `npm install -g tldr`. Run `tldr --update` periodically to refresh the examples database.

---

### watch

**What it does:** Runs a command repeatedly at a fixed interval and displays the output — useful for live monitoring.

**Basic usage:**
```bash
watch -n 2 df -h
```

**Real-world example:**
```bash
# Monitor disk usage on /var every 5 seconds during a log-heavy operation
watch -n 5 "df -h /var"

# Watch the number of established connections to port 443 in real time
watch -n 1 "ss -tn state established | grep :443 | wc -l"

# Monitor a directory for new files during a transfer
watch -n 2 "ls -lth /mnt/incoming/ | head -20"
```

**Tip:** `-d` highlights differences between refreshes, making it easy to spot changes.

---

### crontab

**What it does:** Manages scheduled tasks (cron jobs) for the current user.

**Basic usage:**
```bash
crontab -e
```

**Real-world example:**
```bash
# Open the crontab editor and add a daily backup job at 2:30 AM
crontab -e
# Add this line:
# 30 2 * * * /opt/scripts/backup.sh >> /var/log/backup.log 2>&1

# View the current user's cron jobs
crontab -l

# List root's cron jobs
sudo crontab -l

# Remove all cron jobs for the current user (use with caution)
crontab -r
```

**Tip:** Cron format: `minute hour day-of-month month day-of-week`. Use https://crontab.guru to verify your expressions. Always redirect output to a log file — silent failures are impossible to debug.

---

### tar

**What it does:** Creates or extracts archive files, optionally with compression.

**Basic usage:**
```bash
tar -czf archive.tar.gz /path/to/directory/
```

**Real-world example:**
```bash
# Create a compressed archive of a directory
tar -czf /backup/www-$(date +%F).tar.gz /var/www/html/

# Extract an archive to a specific directory
tar -xzf /backup/www-2026-04-01.tar.gz -C /var/www/

# List contents of an archive without extracting
tar -tzf archive.tar.gz

# Extract a single file from an archive
tar -xzf archive.tar.gz var/www/html/index.php
```

**Tip:** `-c` = create, `-x` = extract, `-z` = gzip compression, `-f` = filename, `-v` = verbose. The mnemonic: "create zero files" (czf) vs "extract zero files" (xzf).

---

### zip / unzip

**What it does:** Creates and extracts ZIP archives — the cross-platform standard for sharing compressed files.

**Basic usage:**
```bash
zip -r archive.zip /path/to/directory/
unzip archive.zip
```

**Real-world example:**
```bash
# Create a ZIP archive of a web application for deployment
zip -r /tmp/myapp-v2.1.zip /opt/myapp/ -x "*.log" -x ".git/*"

# Extract a ZIP to a specific directory
unzip /tmp/myapp-v2.1.zip -d /opt/myapp/

# List the contents of a ZIP file without extracting
unzip -l archive.zip
```

**Tip:** Use `tar.gz` for Linux-to-Linux transfers and backups. Use `zip` when sharing with Windows users or systems without `tar`.

---

### basename

**What it does:** Strips the directory path from a filename, returning only the file name.

**Basic usage:**
```bash
basename /var/log/nginx/access.log
```

**Real-world example:**
```bash
# Extract just the filename when processing a list of full paths in a script
for filepath in /var/log/nginx/*.log; do
  filename=$(basename "$filepath")
  echo "Processing: $filename"
done

# Strip the extension as well
basename /opt/app/deploy.sh .sh
# Output: deploy
```

---

### dirname

**What it does:** Returns the directory portion of a path, stripping the filename.

**Basic usage:**
```bash
dirname /etc/nginx/nginx.conf
```

**Real-world example:**
```bash
# In a script, get the directory where the script itself is located
SCRIPT_DIR=$(dirname "$(realpath "$0")")
source "$SCRIPT_DIR/lib/functions.sh"
```

**Tip:** `dirname` and `basename` together let you split any path cleanly. Use `realpath` to resolve symlinks before applying them.

---

### date

**What it does:** Displays or formats the current date and time, or performs date arithmetic.

**Basic usage:**
```bash
date
```

**Real-world example:**
```bash
# Generate a timestamp for a backup filename
BACKUP_FILE="db-backup-$(date +%F-%H%M%S).sql.gz"

# Get the date 7 days ago (for log rotation scripts)
date -d "7 days ago" +%F

# Convert a Unix timestamp to a human-readable date
date -d @1743500400

# Log the current date and time in ISO 8601 format
echo "Job started at: $(date --iso-8601=seconds)" >> /var/log/myjob.log
```

**Tip:** `%F` = `%Y-%m-%d` (ISO date). `%T` = `%H:%M:%S` (time). `%s` = Unix timestamp (seconds since epoch). These three cover 90% of use cases in scripts.
