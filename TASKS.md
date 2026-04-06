# Linux task map

This file is the fast path when you know the problem you need to solve, but not which command section to open first.

| Task | Start here | Useful commands |
|---|---|---|
| Find which filesystem is full | [06 - Disk and Storage](commands/06-disk.md) | `df`, `du`, `ncdu`, `lsblk` |
| Follow app logs during an incident | [07 - Services and systemd](commands/07-services.md) | `journalctl`, `systemctl`, `tail`, `less` |
| Find what is listening on a port | [04 - Network and Connectivity](commands/04-network.md) | `ss`, `netstat`, `lsof`, `ip` |
| Kill a stuck process safely | [03 - Processes and Jobs](commands/03-processes.md) | `ps`, `top`, `htop`, `kill`, `pkill` |
| Search a config tree for one value | [02 - Text Manipulation](commands/02-text.md) | `grep`, `sed`, `awk`, `cut` |
| Compare two config files before deploy | [02 - Text Manipulation](commands/02-text.md) | `diff`, `sort`, `uniq`, `wc` |
| Copy data between servers without breaking perms | [04 - Network and Connectivity](commands/04-network.md) | `rsync`, `scp`, `ssh` |
| Audit who you are and what you can do | [05 - Permissions and Users](commands/05-permissions.md) | `whoami`, `id`, `sudo`, `su` |
| Clean up files by age or pattern | [01 - Navigation and Files](commands/01-navigation.md) | `find`, `rm`, `mv`, `stat` |
| Check if DNS is the real problem | [04 - Network and Connectivity](commands/04-network.md) | `dig`, `nslookup`, `ping`, `traceroute` |
| Package logs before sending them | [08 - Productivity and Utilities](commands/08-productivity.md) | `tar`, `zip`, `basename`, `dirname` |
| Build a quick one-liner for cron or scripts | [08 - Productivity and Utilities](commands/08-productivity.md) | `watch`, `date`, `env`, `export`, `xargs` |
