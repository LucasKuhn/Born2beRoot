# Born2beRoot

[subject.pdf](https://cdn.intra.42.fr/pdf/pdf/32299/en.subject.pdf)

# Preamble

- What is a Virtual Machine?
    
    [https://www.youtube.com/watch?v=wX75Z-4MEoM](https://www.youtube.com/watch?v=wX75Z-4MEoM)
    
- Tutoriais prontos
    - 🇺🇸 Tutorial from Baigaal → [Medium](https://baigal.medium.com/born2beroot-e6e26dfb50ac)
    - 🇧🇷 PDF do Matheus → [Github](https://github.com/Matth0s/42_born2beroot/blob/master/Born2beroot.pdf)
        
        [Born2beroot.pdf](Born2beRoot%201d94279536f54cc0a95ef45c3d4f3c9c/Born2beroot.pdf)
        
    - 🇧🇷 Tutorial da Soraya (esse é bom) → [Born2beroot](https://www.notion.so/Born2beroot-d22e0203fc144c38a8f809f0432ee5d7)
    - 🇧🇷 Notion do Alexandre e Jorge → [Born2beRoot](https://www.notion.so/Born2beRoot-8807844bf7b1438ebc212c8f29eb5665)
        
        [Born2beRoot%201d94279536f54cc0a95ef45c3d4f3c9c/1_-_Guia_Born2BeRoot.pdf](Born2beRoot%201d94279536f54cc0a95ef45c3d4f3c9c/1_-_Guia_Born2BeRoot.pdf)
        
    - 🇧🇷 Virtualização: Conceitos e Aplicações em Segurança [PDF]
        
        [2008-sbseg-mc.pdf](Born2beRoot%201d94279536f54cc0a95ef45c3d4f3c9c/2008-sbseg-mc.pdf)
        
    

# Step-by-step

- Install the virtual machine on VirtualBox
    
    I suggest the in-depth [installation tutorial from Baigaal](https://baigal.medium.com/born2beroot-e6e26dfb50ac) (At the end of his Medium post) with one small addition:
    
    - When creating the new machine, after selecting the .iso, change network from NAT to Bridge. I highly recommend doing this before starting the machine and installing Debian
    
    ![Untitled](Born2beRoot%201d94279536f54cc0a95ef45c3d4f3c9c/Untitled.png)
    
- Run an update because why not
    
    ```bash
    apt-get update
    apt-get upgrade -y
    ```
    
- Install Sudo
    
    ```bash
    # If not logged in as root
    su -
    
    apt install sudo 
    usermod --group sudo --append YOURUSER
    
    # Check if your user belongs to sudo
    getent group sudo
    ```
    
- Changing default message on login (message of the day)
    
    ```bash
    sudo nano /etc/motd
    ```
    
- Install SSH
    
    ```bash
    # Install the ssh server and client
    sudo apt-get install openssh-server
    sudo apt-get install openssh-client
    
    # Start the service
    sudo service ssh start
    
    # Check the status 
    sudo systemctl status ssh
    
    # Edit the configuration file
    nano /etc/ssh/sshd_config
    
    # Port 4242 required by subject
    # RootLogin is **FORBIDDEN** on subject. 
    # I left it on for now for convenience when connecting via SSH
    ```
    
    ![Screen Shot 2021-11-05 at 3.26.27 PM.png](Born2beRoot%201d94279536f54cc0a95ef45c3d4f3c9c/Screen_Shot_2021-11-05_at_3.26.27_PM.png)
    
    ```bash
    # Restart ssh
    sudo service ssh restart
    ```
    
- Setup static IP
    - 
- Connect via SSH
    
    ```bash
    # Figure out the IP address of your machine
    ip addr
    ```
    
    ![Screen Shot 2021-11-05 at 3.47.48 PM.png](Born2beRoot%201d94279536f54cc0a95ef45c3d4f3c9c/Screen_Shot_2021-11-05_at_3.47.48_PM.png)
    
    ```bash
    # Connect to your virtual machine from your computer via ssh 🤯
    ssh root@YOUR-IP-HERE -p 4242
    
    # Follow on from your pretty-dope terminal instead of linux
    ```
    
- Setup Sudoers
    
    ```bash
    # Create a logfile for recording sudo actions 
    mkdir /var/log/sudo
    touch /var/log/sudo/logfile
    
    # Comment out the Defaults secure_path under sudoers
    sudo visudo 
    
    # Create a custom file for sudo rules defined in subject
    sudo visudo --file=/etc/sudoers.d/born2beroot
    ```
    
    Add the following rules required by the subject 
    
    ```bash
    # Authentication using sudo has to be limited to 3 attempts in the event of an incorrect password.
    Defaults passwd_tries=3
    
    # A custom message of your choice has to be displayed if an error due to a wrong password occurs when using sudo.
    Defaults badpass_message="Wrong, you are. Try again, you must!"
    
    # Each action using sudo has to be archived, both inputs and outputs. 
    # (The default I/O log directory is **/var/log/sudo-io**)
    Defaults log_output
    Defaults log_input
    
    # The log file has to be saved in the /var/log/sudo/ folder.
    Defaults logfile="/var/log/sudo/logfile"
    
    # (optional) And info on year and host machine on the logfile 
    Defaults  log_year, log_host
    
    # The TTY mode has to be enabled for security reasons.
    Defaults requiretty
    
    # For security reasons too, the paths that can be used by sudo must be restricted
    Defaults secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
    ```
    
    - References
        
        [1] [pros of file under sudoers.d](https://superuser.com/questions/869144/why-does-the-system-have-etc-sudoers-d-how-should-i-edit-it/869185) 
        [2] [Reference on log_input, log_output](https://www.tecmint.com/sudoers-configurations-for-setting-sudo-in-linux/) ( The default I/O log directory is **/var/log/sudo-io )**
        
    
    Test your rules
    
    ```bash
    # Check if your file has no mistakes 
    sudo visudo --check -f /etc/sudoers.d/born2beroot
    
    # Logout and then login as non-root
    logout
    
    # Then try any sudo command, and type a wrong password
    sudo visudo
    ```
    
- Setup AppArmor
    
    ```bash
    # Check the status
    sudo aa-status
    
    # Enable it 
    sudo systemctl enable apparmor
    
    # Check again
    sudo aa-status
    ```
    
- Setup UFW
    
    ```bash
    # Install UFW
    sudo apt install ufw
    
    # Enable it 
    sudo ufw enable
    
    # Allow port 4242
    sudo ufw allow 4242
    
    # Check status
    sudo ufw status verbose
    
    ```
    
- Password Policy
    
    ```bash
    # Edit settings on login.defs 
    nano /etc/login.defs
    # ( go to line 160 )
    
    # Your password has to expire every 30 days
    PASS_MAX_DAYS 30
    # The minimum number of days allowed before the modification of a password will be set to 2.
    PASS_MIN_DAYS 2
    # The user has to receive a warning message 7 days before their password expires.
    PASS_WARN_AGE 7
    ```
    
    However, this will not apply to previously set users. To fix this:
    
    ```bash
    # Check your user policy
    chage --list your_user
    
    # Edit the user
    chage your_user --maxdays 30 --mindays 2 --warndays 7
    
    # Check again
    chage --list your_user
    ```
    
    - Sidenote on shadow file
        
        ```bash
        # (Shadow file is only accessible for root user)
        cat /etc/shadow
        ```
        
        ```bash
        mark:$6$.n.:17736:0:99999:7:::
        [--] [----] [---] - [---] ----
        |      |      |   |   |   |||+-----------> 9. Unused
        |      |      |   |   |   ||+------------> 8. Expiration date
        |      |      |   |   |   |+-------------> 7. Inactivity period
        |      |      |   |   |   +--------------> 6. Warning period
        |      |      |   |   +------------------> 5. Maximum password age
        |      |      |   +----------------------> 4. Minimum password age
        |      |      +--------------------------> 3. Last password change
        |      +---------------------------------> 2. Encrypted Password
        +----------------------------------------> 1. Username
        ```
        
        `etc/passwd` file vs `etc/shadow` file 
        
        > passwd contains the users' public information (UID, full name, home directory), while **shadow contains the hashed password and the password expiry data**
        > 
        
        ```bash
        man 5 shadow
        ```
        
- Password Quality
    
    We will install a [PAM (Pluggable Authentication Modules)](https://en.wikipedia.org/wiki/Pluggable_Authentication_Modules) that ensures password quality called `libpam-pwquality`
    
    > libpwquality's purpose is to provide common functions for password quality checking and also scoring them based on their apparent randomness. The library also provides a function for generating random passwords with good pronounceability.
    > 
    
    ```bash
    # Install the package
    sudo apt install libpam-pwquality
    
    # Check the installation 
    apt-cache policy *pam-pwquality*
    
    # Updates it's config file
    nano /etc/security/pwquality.conf
    
    # Your password must be at least 10 characters long.
    minlen = 10 
    # It must contain an uppercase letter and a number.
    ucredit = -1
    # Also, it must not contain more than 3 consecutive identical characters.
    maxrepeat = 3
    # The password must not include the name of the user.
    usercheck = 1
    # (optional) Don't allow even small parts of the username 
    usersubstr = 4
    # The following rule does not apply to the root password: The password must haveat least 7 characters that are not part of the former password.
    difok = 7
    # Of course, your root password has to comply with this policy.
    enforce_for_root
    # (optional) Make this checking enforced
    enforcing = 1 
    ```
    
    About [enforce_for_root](https://manpages.debian.org/testing/libpwquality-common/pwquality.conf.5.en.html#enforce_for_root)
    
    > The module will return error on failed check even if the user changing the password is root. This option is off by default which means that just the message about the failed check is printed but root can change the password anyway. Note that root is not asked for an old password so the checks that compare the old and new password are not performed.
    > 
    
- Creating group user42 and a new user
    
    ```bash
    # Create a group 
    sudo addgroup user42
    
    # Check it 
    getent group user42
    ```
    
    ```bash
    # Create the new user
    sudo adduser new_user
    
    # Check the user
    getent passwd new_user
    # or
    cat /etc/passwd | grep new_user
    
    # (optional) change the password 
    passwd new_user
    
    # Check the user password policy 
    chage --list new_user
    
    # Add the user to the group 
    usermod --group user42 --append new_user
    
    # Check the group
    getent group user42
    ```
    
- CRON
    
    
    ```bash
    # Create a [monitoring.sh](http://monitoring.sh) file that will be run by cron
    nano /usr/local/bin/monitoring.sh
    ```
    
    Example of my script:
    
    ```bash
    # Get variables
    architecture=$(uname -a)
    physical_cores=$(cat /proc/cpuinfo | grep "cpu cores" | awk '{print $NF}')
    virtual_cores=$(cat /proc/cpuinfo | grep "processor" | wc -l)
    memory_total=$(free --mega | grep Mem | awk '{print $2}')
    memory_used=$(free --mega | grep Mem | awk '{print $3}')
    disk_total=$(df --total --human-readable | tail -1 | awk '{print $2}')
    disk_used=$(df --total --human-readable  | tail -1 | awk '{print $3}')
    cpu_load=$(mpstat -P ALL | grep all | awk '{print 100-$NF}')
    boot_time=$(who -b | awk '{print $3 " " $4}')
    lvm_count=$(lsblk | grep lvm | wc -l)
    using_lvm=$( if [ $lvm_count -eq 0 ]; then echo "no"; else echo "yes"; fi)
    tcp_connections=$(netstat --tcp | grep tcp | wc -l)
    users_count=$(who | wc -l)
    ip=$(hostname -I)
    mac=$(ip a | grep ether | awk '{printf $2}')
    sudo_count=$(cat /var/log/sudo/logfile | grep COMMAND | wc --line)
    
    # Function to get the percentage of two numbers
    percentage () {
      result=$(awk "BEGIN {printf \"%.2f\",$1/$2}")
      echo $result
    }
    
    # Format and print to all users
    wall <<-BASH
      #Architecture : $architecture
      #CPU physical :$physical_cores
      #vCPU : $virtual_cores
      #Memory Usage : $memory_used/$memory_total ($(percentage $memory_used $memory_total)%)
      #Disk Usage : $disk_used/$disk_total ($(percentage $disk_used $disk_total)%)
      #CPU load : $cpu_load %
      #Last boot : $boot_time
      #LVM use: $using_lvm
      #Connexions TCP : $tcp_connections ESTABLISHED
      #User log : $users_count
      #Network : IP $ip ($mac)
      #Sudo : $sudo_count cmd
    BASH
    ```
    
    ```bash
    # Test the script 
    bash /usr/local/bin/monitoring.sh
    
    # Crontab has a pretty good manual
    man 5 crontab 
    
    # Create a crontab
    sudo crontab -e
    
    # Make the command run every 10 minutes from boot
    */10 * * * *  sleep $(who -b | grep -o '.$')m; bash /usr/local/bin/monitoring.sh
    ```
    
- Bonus
    
    I didn't do it, so I'm not sure. Here are a few links from people who did the bonus part:
    [https://githubmemory.com/repo/hanshazairi/42-born2beroot](https://githubmemory.com/repo/hanshazairi/42-born2beroot)
    
    [https://github.com/HCastanha/42sp-cursus-born2beroot](https://github.com/caroldaniel/42sp-cursus-born2beroot)
    
    [https://github.com/willianlim/Born2beroot/blob/master/CentOS.md](https://github.com/willianlim/Born2beroot/blob/master/CentOS.md)
    
    [https://www.how2shout.com/linux/install-wordpress-on-lighttpd-web-server-ubuntu/](https://www.how2shout.com/linux/install-wordpress-on-lighttpd-web-server-ubuntu/)
    
- Defense
    1. Make sure you don't allow root login via ssh
    
    ```bash
    nano /etc/ssh/sshd_config
    #PermitRootLogin yes
    reboot
    
    /etc/os-release
    groups
    groupdel
    groupadd
    sudo hostname
    lsblk
    sudo --version
    sudo ufw allow 8080
    sudo systemctl disable cron.service
    ```
    

**LEMBRAR: Bloquear usuario root no /etc/ssh/sshd_config no final** 

### References

[How to enforce password complexity on Linux](https://www.networkworld.com/article/2726217/how-to-enforce-password-complexity-on-linux.html)

[pwquality.conf(5) - libpwquality-common - Debian testing - Debian Manpages](https://manpages.debian.org/testing/libpwquality-common/pwquality.conf.5.en.html#enforce_for_root)

[4.11. Providing secure user access](https://www.debian.org/doc/manuals/securing-debian-manual/ch04s11.en.html)

[/etc/pam.d/common-password](https://gist.github.com/shichao-an/ec32d280d57b61dc6dbe)

[Paulo Kretcheu](https://www.youtube.com/channel/UCQTTe8puVKqurziI6Do-H-Q)

[The TTY demystified](https://www.linusakesson.net/programming/tty/index.php)

[Debian - Port 80 is blocked, but I don't know by what](https://serverfault.com/questions/488486/debian-port-80-is-blocked-but-i-dont-know-by-what)
