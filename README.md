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

## Installing Debian OS
- Download the latest version of Debian [here](https://www.debian.org/).
- To install Debian with the specified partitions as mentioned in the bonus section, I followed [this video guide](https://www.youtube.com/watch?v=OQEdjt38ZJA&t=349s) by [Youssef Oussama](https://github.com/ucefooo).   
    - ⚠️ Ensure that you install the server on 42's shared storage space "sgoinfre." Be mindful not to exceed the disk space limits specified by your school's policy (the partition sizes presented in the project's subject are examples; a total size of ~8 GB should work just fine).
    - ⚠️ Note how the volume "var--log" (as listed with `lstblk`) is named "var-log" during setup (see 7:50 timestamp in the [video guide](https://www.youtube.com/watch?v=OQEdjt38ZJA&t=349s)).
![Screenshot from 2023-12-15 15-11-14 (1)](https://github.com/alx-sch/42_born2beroot/assets/134595144/514f57a8-92c4-40c3-8077-2aac9e5db8d9)

