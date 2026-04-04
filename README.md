# linux-first-100-commands

A practical reference for the 100 Linux commands that sysadmins and DevOps engineers reach for every day.

This is not a tutorial. Each entry shows what the command actually does, one real-world example worth remembering, and one tip that saves time or prevents mistakes. No fluff.

Tested primarily on Debian and Ubuntu, but the commands and examples apply to any Linux distribution.

---

## Why these 100?

These are the commands that come up when you are:

- Diagnosing why a server is slow
- Deploying an application and watching things break
- Hardening a fresh VPS before exposing it to the internet
- Writing a bash script that needs to be reliable in production
- SSHing into a machine you have never seen before and need to understand fast

Knowing these 100 commands well — not just their names, but their real options and failure modes — covers the vast majority of situations you will face.

---

## Table of contents

| File | Topic | Commands |
|---|---|---|
| [01 - Navigation and Files](commands/01-navigation.md) | Moving around the filesystem, finding and managing files | ls, cd, pwd, find, locate, tree, cp, mv, rm, mkdir, rmdir, ln, touch, stat, file |
| [02 - Text Manipulation](commands/02-text.md) | Reading, filtering, and transforming text and streams | cat, less, head, tail, grep, sed, awk, cut, sort, uniq, wc, diff, tr, tee, xargs |
| [03 - Processes and Jobs](commands/03-processes.md) | Monitoring, controlling, and backgrounding processes | ps, top, htop, kill, pkill, jobs, bg, fg, nohup, screen/tmux |
| [04 - Network and Connectivity](commands/04-network.md) | Diagnosing network issues and interacting with remote services | ping, curl, wget, ss, netstat, ip, nmap, traceroute, dig, nslookup, ssh, scp, rsync, tcpdump, ufw |
| [05 - Permissions and Users](commands/05-permissions.md) | Managing file permissions, user accounts, and privilege escalation | chmod, chown, chgrp, sudo, su, whoami, id, useradd, usermod, passwd |
| [06 - Disk and Storage](commands/06-disk.md) | Monitoring disk usage, managing partitions, and working with storage | df, du, lsblk, fdisk, mount, umount, mkfs, dd, iostat, ncdu |
| [07 - Services and systemd](commands/07-services.md) | Managing system services, reading logs, and working with systemd | systemctl start/stop/restart/status/enable/disable/reload/list-units/daemon-reload, journalctl, service |
| [08 - Productivity and Utilities](commands/08-productivity.md) | Tools that make daily work faster and more reliable | history, alias, export, env, which, whereis, man, tldr, watch, crontab, tar, zip/unzip, basename, dirname, date |

---

## How to use this reference

Each command follows the same format:

- **What it does** — one sentence, no jargon
- **Basic usage** — the minimal form of the command
- **Real-world example** — what you would actually type on a production server
- **Tip** — a caveat, a shortcut, or a common mistake to avoid

Read the section relevant to the task you are working on. The examples are designed to be copied and adapted directly.

---

## Notes on distribution coverage

Commands are tested on Debian 12 and Ubuntu 24.04 LTS. A few tools (`htop`, `ncdu`, `tldr`, `tree`) are not installed by default and require `apt install`. This is noted in the relevant entries.

On RHEL/CentOS/Rocky/Fedora systems, use `dnf install` or `yum install` instead of `apt install`. The commands themselves behave identically once installed.

---

## License

MIT License. Copyright 2026 Thomas Rayon. See [LICENSE](LICENSE).
