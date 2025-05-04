+++
title = "Setting Up my Environment"
date = "2025-04-17T23:42:20+05:30"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Abir Dey"
authorTwitter = "AD2011" #do not include @
cover = ""
tags = ["linux", "environment"]
keywords = ["", ""]
description = "Things I do while setting up my Environment"
showFullContent = false
readingTime = false
hideComments = false
+++

- xrdp remove thinclient_devices folder
    
    ```bash
    > Edit /etc/xrdp/sesman.ini
    		[Chansrv]
    		FuseMountName=/tmp/%u/thinclient_drives
    ```
    
- Add Kali repo to ubuntu
    
    ```bash
    sudo sh -c "echo 'deb [https://http.kali.org/kali](https://http.kali.org/kali) kali-rolling main non-free contrib' > /etc/apt/sources.list.d/kali.list"
    sudo sh -c "echo 'Package: *'>/etc/apt/preferences.d/kali.pref; echo 'Pin: release a=kali-rolling'>>/etc/apt/preferences.d/kali.pref; echo 'Pin-Priority: 50'>>/etc/apt/preferences.d/kali.pref"
    
    wget https://kali.download/kali/pool/main/k/kali-archive-keyring/kali-archive-keyring_2024.1_all.deb
    sudo dpkg -i kali-archive-keyring_2024.1_all.deb
    sudo rm kali-archive-keyring_2024.1_all.deb
    ```
    
- Add xtradeb repo to ubuntu
    
    ```bash
    wget https://launchpad.net/~xtradeb/+archive/ubuntu/apps/+files/xtradeb-apt-source_0.3_all.deb
    sudo dpkg -i xtradeb-apt-source_0.3_all.deb
    sudo rm xtradeb-apt-source_0.3_all.deb
    ```
    
- OpenVPN client config
    
    ```bash
     # In .ovpn file:
     # To exclude an IP from tunnel
     route <IP> <Mask> net_gateway  # route 13.234.188.97 255.255.255.255 net_gatweway
     # To block IPv6 traffic
     ipconfig-ipv6 fd15:53b6:dead::2/64 fd15:53b6:dead::1
     redirect-gateway ipv6
     block-ipv6
     # Embed username and password in conf
     auth-user-pass <credfile> # auth-user-pass vpn-pass.txt
     # In password file eg: vpn-pass.txt
    	 <username>
    	 <password>
    	 #Eg :
    	 ULw87NOo4tRcEZAE
    	 F7TdD7LQFriZhd5D2JKehDUGzN8THhel
    	 
    # Set DNS:
    dhcp-option DNS 1.1.1.1
    ```
    
- Internal Port Forwarding using iptables
    
    ```bash
    sudo iptables -A PREROUTING -t nat -i eth0 -p tcp --dport 18187 -j REDIRECT --to-port 5900
    sudo iptables -A PREROUTING -t nat -i eth0 -p tcp --dport 18188 -j REDIRECT --to-port 22
    # In this case any incoming requests to host on port 18187 redirects to port 5900 on the host on interface eth0
    # Saving the rule for persisting reboots
    sudo sh -c "iptables-save > /etc/iptables/rules.v4"
    ```
    
- Allow all port in firewall with persistance
    
    ```bash
    sudo su
    
    iptables -P INPUT ACCEPT
    iptables -P OUTPUT ACCEPT
    iptables -P FORWARD ACCEPT
    iptables -F
    
    ip6tables -P INPUT ACCEPT
    ip6tables -P OUTPUT ACCEPT
    ip6tables -P FORWARD ACCEPT
    ip6tables -F
    
    sudo sh -c '/sbin/iptables-save > /etc/iptables/rules.v4'
    sudo sh -c '/sbin/iptables6-save > /etc/iptables/rules.v6'
    ```
    
- Remove Dual Boot from Computer
    
    ```powershell
    Change default boot order F2 or Fn+F2 while booting.
    
    Partition Manager:
    
    https://www.fcportables.com/aomei-partition-assistant-technician-winpe/
    
    Remove last two partitions (total size of both should be around 64GB)
    
    Note: The first 4 partitions: 2 before C, C, 1 after C are windows partitions.
    
    Remove old boot entries:
    
    bcdedit /enum firmware
    
    find required indentifier by checking description and copy it
    
    bcdedit /delete "{478b0f56-cadb-11ed-8d1a-806e6f6e6963}"
    
    double quotes is necessary to delete the boot entry
    ```
    
- Add current logged in user to sudoers list [to run sudo without sudo password]
    
    ```bash
    echo "$USER ALL=(ALL:ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/$USER
    ```
    
- SSH Hardening
    
    ```bash
    sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
    sudo nano /etc/ssh/sshd_config
    sudo sshd -T
    
    PermitRootLogin no
    MaxAuthTries 3
    LoginGraceTime 20
    PermitEmptyPasswords no
    PasswordAuthentication no
    
    sudo systemctl restart ssh
    sudo systemctl restart sshd
    ```
    
- XRDP
    
    ```bash
    sudo apt install xrdp
    
    echo "xfce4-session" | tee .xsession
    sudo systemctl restart xrdp
    ```
- ZSH Autocomplete    

    ```bash
    sudo apt install zsh-autosuggestions zsh-syntax-highlighting zsh
    ```

- Linux PATH locations

    ```bash
    sudo visudo
    ```

- Linux Change Shell

   ```bash
   sudo chsh -s $(which zsh)
   sudo vi /etc/passwd
   ```

- GO Environment Variables

   ```bash
   GOROOT
   GOBIN
   GOPATH
   ```

- Python allow external libraries

   ```bash
   sudo rm /usr/lib/python3.*/EXTERNALLY-MANAGED
   ```
