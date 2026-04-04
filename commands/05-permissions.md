# Permissions and Users

10 commands for managing file permissions, user accounts, and privilege escalation.

---

### chmod

**What it does:** Changes the read, write, and execute permissions of a file or directory.

**Basic usage:**
```bash
chmod 644 /etc/nginx/nginx.conf
```

**Real-world example:**
```bash
# Secure a private SSH key (required by ssh, which refuses to use world-readable keys)
chmod 600 ~/.ssh/id_rsa

# Make a script executable
chmod +x /opt/scripts/deploy.sh

# Set correct permissions on a web root directory recursively
# Files: 644 (rw-r--r--), Directories: 755 (rwxr-xr-x)
find /var/www/html -type f -exec chmod 644 {} \;
find /var/www/html -type d -exec chmod 755 {} \;
```

**Tip:** The numeric shorthand: `7 = rwx`, `6 = rw-`, `5 = r-x`, `4 = r--`, `0 = ---`. First digit = owner, second = group, third = others.

---

### chown

**What it does:** Changes the owner and/or group of a file or directory.

**Basic usage:**
```bash
chown www-data:www-data /var/www/html/
```

**Real-world example:**
```bash
# Give ownership of the web root to the nginx user after a deployment
chown -R www-data:www-data /var/www/myapp/

# Transfer ownership of a file to a specific user and group
chown deploy:deploy /opt/app/config/production.env
```

**Tip:** `-R` applies the change recursively. Without it, only the top-level item is changed.

---

### chgrp

**What it does:** Changes only the group ownership of a file or directory.

**Basic usage:**
```bash
chgrp developers /opt/app/config/
```

**Real-world example:**
```bash
# Allow the "devops" group to read application logs without changing the owner
chgrp -R devops /var/log/myapp/
chmod -R g+r /var/log/myapp/
```

**Tip:** `chown user:group` can do what both `chown` and `chgrp` do. Use `chgrp` when you only need to change the group.

---

### sudo

**What it does:** Executes a command with elevated privileges as root (or another user), per the sudoers policy.

**Basic usage:**
```bash
sudo systemctl restart nginx
```

**Real-world example:**
```bash
# Edit a system file using your preferred editor with sudo
sudo -e /etc/hosts

# Run a command as a specific user (e.g., the postgres system user)
sudo -u postgres psql -c "SELECT version();"

# Check which sudo privileges your account has
sudo -l
```

**Tip:** `sudo -i` opens a root shell with root's environment. `sudo -s` opens a root shell with your current environment. Avoid staying in a root shell longer than necessary.

---

### su

**What it does:** Switches to another user account in the current terminal session.

**Basic usage:**
```bash
su - deploy
```

**Real-world example:**
```bash
# Switch to the postgres user to run database commands without a password prompt
sudo su - postgres
psql

# Switch to root (requires knowing the root password)
su -
```

**Tip:** `su -` (with the dash) loads the target user's full environment. `su` without the dash keeps the current environment, which can cause unexpected behavior.

---

### whoami

**What it does:** Prints the username of the currently active user.

**Basic usage:**
```bash
whoami
```

**Real-world example:**
```bash
# Verify which user a script is running as before doing privileged operations
if [ "$(whoami)" != "root" ]; then
  echo "This script must be run as root. Exiting."
  exit 1
fi
```

---

### id

**What it does:** Displays the current user's UID, GID, and all group memberships.

**Basic usage:**
```bash
id
```

**Real-world example:**
```bash
# Verify that a user is in the "docker" group (required to run docker without sudo)
id thomas | grep docker

# Check the identity of another user
id www-data
```

**Tip:** A user added to a group must log out and back in for the new group membership to take effect in their session.

---

### useradd

**What it does:** Creates a new user account on the system.

**Basic usage:**
```bash
useradd -m -s /bin/bash deploy
```

**Real-world example:**
```bash
# Create a service account with no login shell and no home directory
useradd --system --no-create-home --shell /usr/sbin/nologin myapp

# Create a regular user with a home directory, a shell, and a comment
useradd -m -s /bin/bash -c "Thomas Rayon" -G sudo thomas
```

**Tip:** `-m` creates a home directory. `-s` sets the login shell. `--system` marks it as a system account (UID below 1000, no aging). Always use `nologin` for service accounts.

---

### usermod

**What it does:** Modifies an existing user account — groups, shell, home directory, and more.

**Basic usage:**
```bash
usermod -aG docker thomas
```

**Real-world example:**
```bash
# Add a user to multiple groups at once
usermod -aG sudo,docker,www-data thomas

# Lock a user account (disable login without deleting the account)
usermod -L former-employee

# Change a user's login shell
usermod -s /bin/zsh thomas
```

**Tip:** The `-a` flag in `-aG` is critical — it appends to existing groups. Without `-a`, you would replace all current group memberships.

---

### passwd

**What it does:** Sets or changes a user's password, or manages password expiry policy.

**Basic usage:**
```bash
passwd thomas
```

**Real-world example:**
```bash
# Force a user to change their password at next login
passwd -e thomas

# Lock a user account by disabling their password
passwd -l former-contractor

# Set a password non-interactively from a script (use with caution)
echo "thomas:S3cur3P@ss!" | chpasswd
```

**Tip:** Avoid hardcoding passwords in scripts. Use `chpasswd` in provisioning scripts only when the password is sourced from a secrets manager or environment variable, not inline.
