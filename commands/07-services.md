# Services and systemd

10 commands for managing system services, reading logs, and working with the init system.

---

### systemctl start / stop / restart

**What it does:** Starts, stops, or restarts a systemd service.

**Basic usage:**
```bash
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
```

**Real-world example:**
```bash
# Restart a service and immediately follow its logs to check for errors
systemctl restart postgresql && journalctl -u postgresql -f

# Stop a service that is consuming too many resources
systemctl stop elasticsearch

# Restart Docker without affecting running containers (live-restore must be configured)
systemctl restart docker
```

**Tip:** `restart` = stop + start. If you only changed a config file, check if `reload` is supported instead — it applies the config without downtime.

---

### systemctl status

**What it does:** Shows the current state of a service — running/stopped, recent log output, PID, and memory usage.

**Basic usage:**
```bash
systemctl status nginx
```

**Real-world example:**
```bash
# Check the status of a service and see the last 50 log lines
systemctl status -l --no-pager nginx

# Quickly check whether a service is active in a script
if systemctl is-active --quiet nginx; then
  echo "nginx is running"
else
  echo "nginx is down — restarting"
  systemctl start nginx
fi
```

**Tip:** `systemctl is-active` and `systemctl is-enabled` return exit codes usable in scripts (0 = yes, non-zero = no).

---

### systemctl enable / disable

**What it does:** Configures a service to start automatically at boot (enable) or prevents it from starting at boot (disable).

**Basic usage:**
```bash
systemctl enable nginx
systemctl disable nginx
```

**Real-world example:**
```bash
# Enable a service and start it immediately in one command
systemctl enable --now postgresql

# Disable a legacy service that conflicts with a newer one
systemctl disable --now apache2
```

**Tip:** `enable --now` is equivalent to `enable` + `start`. `disable --now` is `disable` + `stop`. This saves two commands.

---

### systemctl reload

**What it does:** Asks a service to reload its configuration file without stopping the process.

**Basic usage:**
```bash
systemctl reload nginx
```

**Real-world example:**
```bash
# Apply an nginx config change with zero downtime
nginx -t && systemctl reload nginx
```

**Tip:** Not all services support `reload`. Check the unit file (`systemctl cat nginx`) to see if `ExecReload` is defined. If it is not, you must use `restart`.

---

### systemctl list-units

**What it does:** Lists all loaded systemd units (services, sockets, timers, mounts, etc.) and their current states.

**Basic usage:**
```bash
systemctl list-units --type=service
```

**Real-world example:**
```bash
# Show all services that have failed
systemctl list-units --type=service --state=failed

# Show all active services
systemctl list-units --type=service --state=active

# Show all enabled services that will start at boot
systemctl list-unit-files --type=service --state=enabled
```

**Tip:** `list-units` shows runtime state. `list-unit-files` shows install state (enabled/disabled/static). Use both to get the full picture.

---

### systemctl daemon-reload

**What it does:** Reloads the systemd manager configuration — required after creating or modifying a unit file.

**Basic usage:**
```bash
systemctl daemon-reload
```

**Real-world example:**
```bash
# Create a new service unit, reload systemd, then enable and start it
cat > /etc/systemd/system/myapp.service << 'EOF'
[Unit]
Description=My Application
After=network.target

[Service]
User=deploy
WorkingDirectory=/opt/myapp
ExecStart=/opt/myapp/bin/myapp --config /etc/myapp/config.yml
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable --now myapp
```

**Tip:** If you modify an existing unit file, systemd will warn you to run `daemon-reload` before the changes take effect. Never skip this step.

---

### journalctl

**What it does:** Queries and displays logs from the systemd journal — the central log store for all services.

**Basic usage:**
```bash
journalctl -u nginx
```

**Real-world example:**
```bash
# Follow live logs for a specific service
journalctl -u nginx -f

# Show logs for a service since the last boot
journalctl -u postgresql -b

# Show logs for the last hour across all services
journalctl --since "1 hour ago"

# Show kernel messages (equivalent to dmesg)
journalctl -k

# Find all ERROR-level messages in the last 24 hours
journalctl --since "24 hours ago" -p err
```

**Tip:** `-u` filters by unit. `-f` follows in real time. `-b` shows only the current boot. `-p` filters by priority (emerg, alert, crit, err, warning, notice, info, debug).

---

### journalctl --disk-usage / vacuum

**What it does:** Shows how much disk space the journal is consuming and allows cleaning old journal entries.

**Basic usage:**
```bash
journalctl --disk-usage
```

**Real-world example:**
```bash
# Check journal size
journalctl --disk-usage

# Keep only the last 2 weeks of logs
journalctl --vacuum-time=2weeks

# Keep journal size under 500MB
journalctl --vacuum-size=500M
```

**Tip:** On servers with high log volume, the journal can grow to several GB. Set `SystemMaxUse` in `/etc/systemd/journald.conf` to cap it permanently.

---

### systemctl cat

**What it does:** Displays the content of a unit file and all its drop-in overrides.

**Basic usage:**
```bash
systemctl cat nginx
```

**Real-world example:**
```bash
# Inspect a service unit before modifying it
systemctl cat sshd

# Review the full resolved unit (including drop-ins) to understand how a service is configured
systemctl cat postgresql@16-main
```

**Tip:** Use `systemctl edit servicename` to create a drop-in override without touching the original unit file — safer than editing files in `/lib/systemd/system/`.

---

### service (legacy)

**What it does:** The older SysV-compatible command for managing services — still widely used in documentation and scripts.

**Basic usage:**
```bash
service nginx status
```

**Real-world example:**
```bash
# Legacy systems or scripts may use service instead of systemctl
service ssh restart
service cron status
```

**Tip:** On modern systemd systems, `service nginx restart` is silently forwarded to `systemctl restart nginx`. Use `systemctl` directly in your own scripts for clarity and full feature access.
