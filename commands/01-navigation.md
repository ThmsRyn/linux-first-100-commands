# Navigation and File Management

15 commands for moving around the filesystem and managing files.

---

### ls

**What it does:** Lists the contents of a directory, with optional details on permissions, sizes, and timestamps.

**Basic usage:**
```bash
ls -la
```

**Real-world example:**
```bash
# Show all files sorted by modification time, newest first
ls -lt /var/log/nginx/
```

**Tip:** `ls -lah` adds human-readable file sizes. Combine with `| head -20` to avoid flooding the terminal on large directories.

---

### cd

**What it does:** Changes the current working directory.

**Basic usage:**
```bash
cd /etc/nginx/
```

**Real-world example:**
```bash
# Jump to the previous directory without typing its path
cd -
```

**Tip:** `cd ~` always takes you back to your home directory. `cd ..` goes up one level.

---

### pwd

**What it does:** Prints the absolute path of the current working directory.

**Basic usage:**
```bash
pwd
```

**Real-world example:**
```bash
# Confirm which directory a script is running from inside a cron job
echo "Running from: $(pwd)" >> /var/log/myscript.log
```

---

### find

**What it does:** Searches for files and directories matching given criteria — name, size, age, permissions, and more.

**Basic usage:**
```bash
find /var/log -name "*.log"
```

**Real-world example:**
```bash
# Find all files modified in the last 24 hours under /etc
find /etc -type f -mtime -1

# Find files larger than 500MB anywhere on disk
find / -type f -size +500M 2>/dev/null
```

**Tip:** Always add `2>/dev/null` when running find as a non-root user to suppress permission denied noise.

---

### locate

**What it does:** Finds files by name instantly using a prebuilt database index.

**Basic usage:**
```bash
locate nginx.conf
```

**Real-world example:**
```bash
# Update the index then find all files with "authorized_keys" in the name
updatedb && locate authorized_keys
```

**Tip:** `locate` is fast but only reflects the last `updatedb` run. Use `find` when you need real-time results.

---

### tree

**What it does:** Displays the directory structure as a visual tree.

**Basic usage:**
```bash
tree /etc/ssh/
```

**Real-world example:**
```bash
# Show only directories, limited to 2 levels deep
tree -d -L 2 /var/www/
```

**Tip:** Not installed by default on all distros. Install with `apt install tree` or `yum install tree`.

---

### cp

**What it does:** Copies files or directories from one location to another.

**Basic usage:**
```bash
cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak
```

**Real-world example:**
```bash
# Recursively copy a directory and preserve timestamps and permissions
cp -rp /var/www/html/ /backup/www-html-$(date +%F)/
```

**Tip:** Use `-p` to preserve ownership, timestamps, and permissions. Critical when copying config files.

---

### mv

**What it does:** Moves or renames files and directories.

**Basic usage:**
```bash
mv oldname.conf newname.conf
```

**Real-world example:**
```bash
# Archive all .log files older than 7 days by moving them to an archive directory
find /var/log/app/ -name "*.log" -mtime +7 -exec mv {} /var/log/app/archive/ \;
```

---

### rm

**What it does:** Removes files or directories permanently.

**Basic usage:**
```bash
rm /tmp/tempfile.txt
```

**Real-world example:**
```bash
# Remove all .tmp files in /tmp without prompting
rm -f /tmp/*.tmp

# Remove a directory and all its contents
rm -rf /var/cache/apt/archives/
```

**Tip:** There is no recycle bin. Double-check the path before running `rm -rf`. Never run as root without thinking.

---

### mkdir

**What it does:** Creates one or more directories.

**Basic usage:**
```bash
mkdir /opt/myapp
```

**Real-world example:**
```bash
# Create a nested directory structure in one command
mkdir -p /opt/myapp/{logs,config,data,backups}
```

**Tip:** `-p` creates all missing parent directories and does not error if the directory already exists.

---

### rmdir

**What it does:** Removes empty directories.

**Basic usage:**
```bash
rmdir /tmp/empty-dir/
```

**Real-world example:**
```bash
# Remove a chain of empty parent directories
rmdir -p /tmp/project/build/output/
```

**Tip:** `rmdir` fails if the directory contains any files. Use `rm -rf` only when you are sure of what is inside.

---

### ln

**What it does:** Creates hard links or symbolic links between files.

**Basic usage:**
```bash
ln -s /etc/nginx/sites-available/mysite.conf /etc/nginx/sites-enabled/mysite.conf
```

**Real-world example:**
```bash
# Create a symlink so that a versioned binary is accessible as a generic name
ln -s /usr/local/bin/python3.11 /usr/local/bin/python3
```

**Tip:** Always use absolute paths with symlinks. Relative paths break when the link is accessed from a different directory.

---

### touch

**What it does:** Creates an empty file or updates the timestamp of an existing file.

**Basic usage:**
```bash
touch /var/lock/myscript.lock
```

**Real-world example:**
```bash
# Create a lock file at the start of a cron job to prevent overlapping runs
touch /tmp/backup.lock
# ... do backup work ...
rm /tmp/backup.lock
```

---

### stat

**What it does:** Displays detailed metadata about a file: size, permissions, inode, timestamps.

**Basic usage:**
```bash
stat /etc/passwd
```

**Real-world example:**
```bash
# Check the exact last modification time of a config file after a deployment
stat /etc/nginx/nginx.conf
```

**Tip:** `stat` shows three timestamps: access (atime), modify (mtime), and change (ctime). `ls -l` only shows mtime.

---

### file

**What it does:** Determines the actual type of a file by inspecting its contents, not its extension.

**Basic usage:**
```bash
file /bin/bash
```

**Real-world example:**
```bash
# Identify what a binary or unknown file actually is before executing it
file /tmp/suspicious-download
```

**Tip:** A file named `.jpg` can contain a shell script. `file` tells you what it really is.
