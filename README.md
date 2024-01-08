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
- Some guides only change settings in `/etc/pam.d/common-password`. However, this doesn't affect password requirements for existing users (root, main), even when changing the password. Ensure that you (also) edit `/etc/security/pwquality.conf` for these changes to take effect.
- After editing the password aging settings in `/etc/login.defs`, make sure to manually apply corresponding policies to existing users using commands like `chage -m 2 -M 30 -W 7 root` or `chage -m 2 -M 30 -W 7 username`.


