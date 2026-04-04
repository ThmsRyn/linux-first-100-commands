# Processes and Jobs

10 commands for monitoring, controlling, and managing running processes.

---

### ps

**What it does:** Snapshots the currently running processes and their details.

**Basic usage:**
```bash
ps aux
```

**Real-world example:**
```bash
# Find all processes belonging to the www-data user (nginx/apache workers)
ps aux | grep www-data

# Show the process tree to understand parent-child relationships
ps auxf

# Find a process by name and get its PID
ps aux | grep "[n]ginx"
```

**Tip:** The `[n]ginx` bracket trick in grep excludes the grep process itself from the results.

---

### top

**What it does:** Shows a live, updating view of system resource usage — CPU, memory, and running processes.

**Basic usage:**
```bash
top
```

**Real-world example:**
```bash
# Run top non-interactively, take 3 snapshots, and save to a file for later analysis
top -b -n 3 > /tmp/top-snapshot-$(date +%F-%H%M).txt
```

**Tip:** Inside `top`, press `M` to sort by memory usage, `P` to sort by CPU, `k` to kill a process by PID, and `q` to quit.

---

### htop

**What it does:** An interactive, color-coded process viewer with mouse support — the modern replacement for `top`.

**Basic usage:**
```bash
htop
```

**Real-world example:**
```bash
# Monitor processes for a specific user only
htop -u www-data
```

**Tip:** Not installed by default. Install with `apt install htop`. Use F6 to sort by column, F9 to send a signal, and F10 to quit.

---

### kill

**What it does:** Sends a signal to a process by its PID — most commonly used to terminate processes.

**Basic usage:**
```bash
kill 3721
```

**Real-world example:**
```bash
# Gracefully ask nginx to reload its config without downtime (SIGHUP)
kill -HUP $(cat /var/run/nginx.pid)

# Force-kill a stuck process that ignores SIGTERM
kill -9 3721
```

**Tip:** Always try `kill PID` (SIGTERM) first. Use `kill -9` only if the process refuses to terminate — it cannot be caught or ignored.

---

### pkill

**What it does:** Sends a signal to processes matched by name — no need to look up the PID first.

**Basic usage:**
```bash
pkill nginx
```

**Real-world example:**
```bash
# Kill all processes matching the name "gunicorn"
pkill -f gunicorn

# Send SIGHUP to all processes named "haproxy" to trigger a config reload
pkill -HUP haproxy
```

**Tip:** `-f` matches the full command line, not just the process name. Use it when the process is launched with a full path or arguments.

---

### jobs

**What it does:** Lists all jobs (background and stopped) running in the current shell session.

**Basic usage:**
```bash
jobs
```

**Real-world example:**
```bash
# Start a long-running command, suspend it, check its job number, then resume it in the background
ping google.com > /tmp/ping.log
# Press Ctrl+Z to suspend
jobs
# Output: [1]+  Stopped   ping google.com > /tmp/ping.log
bg %1
```

---

### bg

**What it does:** Resumes a stopped job and runs it in the background.

**Basic usage:**
```bash
bg %1
```

**Real-world example:**
```bash
# Suspend a running rsync with Ctrl+Z then push it to background to free the terminal
# Ctrl+Z
# [1]+  Stopped    rsync -avz /data/ user@backup:/data/
bg %1
```

---

### fg

**What it does:** Brings a background or stopped job back to the foreground.

**Basic usage:**
```bash
fg %1
```

**Real-world example:**
```bash
# List all jobs, then bring job number 2 back to the foreground
jobs
fg %2
```

---

### nohup

**What it does:** Runs a command that keeps running after you log out, immune to the SIGHUP signal.

**Basic usage:**
```bash
nohup ./long-running-script.sh &
```

**Real-world example:**
```bash
# Start a Python data processing script that must survive SSH session logout
nohup python3 /opt/scripts/process_data.py > /var/log/process_data.log 2>&1 &
echo "Started with PID: $!"
```

**Tip:** Output goes to `nohup.out` by default. Always redirect explicitly to a named log file.

---

### screen / tmux

**What it does:** Multiplexers that let you run persistent terminal sessions that survive disconnections and support multiple windows/panes.

**Basic usage:**
```bash
# Start a new tmux session
tmux new -s mysession

# Detach from the session (leaves it running)
# Ctrl+B then D

# Reattach to the session later
tmux attach -t mysession
```

**Real-world example:**
```bash
# Start a deployment in a named tmux session so you can detach and reattach safely
tmux new -s deploy
./deploy.sh
# Ctrl+B then D  -- detach, session keeps running
# Later:
tmux attach -t deploy
```

**Tip:** `tmux ls` lists all active sessions. `tmux kill-session -t mysession` terminates one. For `screen`, the equivalent of detach is `Ctrl+A then D`.
