# 42_born2beroot

<p align="center">
    <img src="https://github.com/alx-sch/42_born2beroot/assets/134595144/5b265781-5233-4e48-97df-7bead21d3054" alt="libft" />
</p>

Configuring a secure virtual machine involves user authentication, firewall rules, and system monitoring, with a focus on Linux system administration and network security. This project serves as a practical exercise in system administration.

Rather than providing another step-by-step installation guide, this documentation highlights certain challenges I noticed during evaluations, which are often overlooked by many online guides for the born2beroot project.

## Features

- **Operating System Configuration:** Set up a Debian server with minimal services and create partitions using LVM.
- **User and Group Management:** Manage users and groups through sudoers configuration for controlled access and privileges.
- **Security Measures:** Implement robust security protocols, covering SSH configuration, root access restriction, UFW setup, and a stringent password policy.
- **System Monitoring Script:** Develop a monitoring.sh bash script to provide regular system information updates on all terminals at predefined intervals.
- **WordPress Website Setup :** Host a WordPress website using lighttpd, MariaDB, and PHP.

## Guides Used
I used these guides, with certain limitations as pointed out the following sections:

- [Born2BeRoot Guide](https://github.com/pasqualerossi/Born2BeRoot-Guide/tree/main) by Pasquale Rossi.
- [born2beroot-42network-cursus](https://github.com/ucefooo/born2beroot) by Youssef Oussama.
- For explaining concepts: [born2beroot](https://github.com/pgomez-a/born2beroot) by Pablo Gómez Álvarez.
  
## Installing Debian OS
- Download the latest version of Debian [here](https://www.debian.org/).
- To install Debian with the specified partitions as mentioned in the bonus section, I followed [this video guide](https://www.youtube.com/watch?v=OQEdjt38ZJA&t=349s) by [Youssef Oussama](https://github.com/ucefooo).   
    - ⚠️ Ensure that you install the server on 42's shared storage space "sgoinfre." Be mindful not to exceed the disk space limits specified by your school's policy (the partition sizes presented in the project's subject are examples; a total size of ~8 GB should work just fine).
    - ⚠️ Note how the volume "var--log" (as listed with `lstblk`) is named "var-log" during setup (see 7:50 timestamp in the [video guide](https://www.youtube.com/watch?v=OQEdjt38ZJA&t=349s)).
![Screenshot from 2023-12-15 15-11-14 (1)](https://github.com/alx-sch/42_born2beroot/assets/134595144/514f57a8-92c4-40c3-8077-2aac9e5db8d9)

## Password Policy

- It is recommended to adhere to the password policy during the OS installation when setting up passwords for both the root and main user. Additionally, consider following the same policy for accessing the encrypted partition, even if it's not explicitly specified in the subject, it is still good practice. By establishing strong passwords from the beginning, you can avoid the need to change them later and mitigate the risk of forgetting about it, as there is no prompt for rule changes.
- ⚠️ Some guides only change settings in `/etc/pam.d/common-password`. However, this doesn't affect password requirements for existing users (root, main), even when changing the password. Ensure that you (also) edit `/etc/security/pwquality.conf` for these changes to take effect.
- ⚠️ After editing the password aging settings in `/etc/login.defs`, make sure to manually apply corresponding policies to existing users using commands like `chage -m 2 -M 30 -W 7 root` or `chage -m 2 -M 30 -W 7 username`. Check password aging settings via `sudo chage -l username`.

## Editing Sudoers
- Use `sudo visudo` instead of, for example, `sudo nano /etc/sudoer`, to access and edit the sudoers file. Visudo checks for correct syntax, helping you avoid errors that could lock you out of sudo access.

## Monitoring Script
- As with everything you find online: Take it with a grain of salt and make sure to understand and test before implementing it into your own project. I've encountered a few monitoring scripts that were not 100% correct (e.g., counting the header line of a table showing TCP connections, therefore always being "1" too much).
- Here is my monitoring script; please do not copy and paste it before testing it yourself! ;)
```
#!/bin/bash

# ARCH
arc=$(uname -a)

# CPU PHYSICAL
cpu_p=$(grep "physical id" /proc/cpuinfo | sort | uniq | wc -l)

# CPU VIRTUAL
cpu_v=$(grep "^processor" /proc/cpuinfo | wc -l)

# RAM
ram_total=$(free -m | awk '$1 == "Mem:" {print $2}')
ram_use=$(free -m | awk '$1 == "Mem:" {print $3}')
ram_perc=$(free | awk '$1 == "Mem:" {printf("%.2f"), $3/$2*100}')

# DISK
disk_total=$(df -BG | grep '^/dev/' | grep -v '/boot$' | awk '{ft += $2} END {print ft}')
disk_use=$(df -BM | grep '^/dev/' | grep -v '/boot$' | awk '{ut += $3} END {print ut}')
disk_perc=$(df -BM | grep '^/dev/' | grep -v '/boot$' | awk '{ut += $3} {ft+= $2} END {printf("%d"), ut/ft*100}')

# CPU LOAD
cpu_l=$(top -bn1 | grep '^%Cpu' | cut -c 9- | xargs | awk '{printf("%.1f%%"), $1 + $3}')

# LAST BOOT
lb=$(who -b | awk '$1 == "system" {print $3 " " $4}')

# LVM USE
lvmu=$(if [ $(lsblk | grep "lvm" | wc -l) -eq 0 ]; then echo no; else echo yes; fi)

# TCP CONNECTIONS
tcpc=$(ss -neopt state established | grep -v "Recv-Q" | wc -l)

# USER LOG
ulog=$(users | wc -w)

# NETWORK
ip=$(hostname -I)
mac=$(ip link show | grep "ether" | awk '{print $2}')

# SUDO COMMANDS                             
cmds=$(journalctl _COMM=sudo | grep COMMAND | wc -l)

# Multiline message
message="
        #Architecture: $arc
        #CPU physical: $cpu_p
        #vCPU: $cpu_v
        #Memory Usage: $ram_use/${ram_total}MB ($ram_perc%)
        #Disk Usage: $disk_use/${disk_total}GB ($disk_perc%)
        #CPU load: $cpu_l
        #Last boot: $lb
        #LVM use: $lvmu
        #Connections TCP: $tcpc ESTABLISHED
        #User log: $ulog
        #Network: IP $ip ($mac)
        #Sudo: $cmds commands"

# Send the message to all logged-in users
echo "$message" | wall`
```
## Setting up a Cron Job

- Many guides set up the cron job to execute the monitoring script like this (`sudo crontab -u root -e`):
  ```bash
  */10 * * * * sh /path/to/script
- ⚠️ While this setup does execute the script every 10 minutes, it does so every "full 10 minutes" on the clock (e.g., 12:00, 12:10, 12:20, ...). The project's subject, however, specifies that the monitoring information is to be shown at server startup and then every 10 minutes. This means that if the server is started at 12:34:56 (HH:MM:SS), the monitoring info is expected to be displayed right away and then at 12:44:56, 12:54:56, 01:04:56, and so on.

