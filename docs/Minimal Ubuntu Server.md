# Setting up Server

## Basic Setup
### Configuring Ethernet

Run the commands bellow to find your network card and bring it UP, since when installing linux server without internet it defaults to DOWN.

```bash
ip link
sudo ip link set <iface> up
```
#### Setup Netplan

Now that the network card is UP, for it to receive an ip, you have to configure Netplan, which is an utility that abstracts the network configuration over systemd-networkd(default ubuntu) and NetworkManager.

When installing, if you don't connect to the internet, via cable or wifi, you won't have a Netplan configuration. To check run the command:

```bash
netplan get
```

You should see no output, if you do you, and your network card is up you probably have an ip already. You can also check the dir **/etc/netplan/**.

For the creation of a configuration file run these commands:

```bash
sudo netplan set --origin-hint <file-name> ethernets.<iface>.dhcp4=true
sudo netplan try
```

After that you'll have an basic Netplan configuration.
ex:
```yaml
network:
  version: 2
  ethernets:
    enp19s0:
      dhcp4: true
```
#### Static IP

For setting up a static ip, edit the Netplan configuration like such:
```bash
network:
  version: 2
  ethernets:
    enp19s0:
      addresses:
      - "192.168.1.15/24"
      nameservers:
	    addresses:
		- 8.8.8.8
		- 1.1.1.1
      dhcp4: false
      routes:
      - to: "default"
        via: "192.168.1.254"

```

Find your routers ip via the command `ip route`, and make sure to chose an ip that's not been used and is in your home network.

### Update the server

Update the server before installing any packages
```bash
sudo apt update && sudo apt upgrade -y
```

### Disabling Root
```bash
passwd -l root
usermod -s /sbin/nologin root
```

The first command locks the root password, effectively disabling direct password-based login. The second command sets the root user’s shell to `/sbin/nologin`, preventing interactive root login via any means.

### Disabling Hibernation

For laptop only, so the notebook does't hibernate when is closed.
First install a text editor of choice, mine being vim, and edit the **logind.conf**.
```bash
sudo apt install vim
sudo vim /etc/systemd/logind.conf
```

In the file, change these parameters, so when the lid closes the notebook will stay operational:
```bash
HandleLidSwitch=ignore
HandleLidSwitchExternalPower=ignore
HandleLidSwitchDocked=ignore
```

Then just restart the **logind** service.
	`sudo systemctl restart systemd-logind`

### Remove Snap

```bash
sudo systemctl stop snapd
sudo systemctl stop snapd.socket
sudo systemctl stop snapd.seeded.service

sudo systemctl mask snapd
```

### Setting up Automatic Security Updates

```bash
sudo apt install unattended-upgrades
sudo dpkg-reconfigure unattended-upgrades
sudo vim /etc/apt/apt.conf.d/50unattended-upgrades 
```

### Secure SSH

```bash
sudo vim /etc/ssh/sshd_config

PermitRootLogin no
PasswordAuthentication no
PasswordAuthentication no
PubkeyAuthentication yes
AllowAgentForwarding no
AllowTcpForwarding no
X11Forwarding no
AllowUsers <user>
MaxAuthTries 3
DebianBanner no
Banner no
ClientAliveInterval 300
ClientAliveCountMax 2

sudo systemctl restart ssh
```

### Firewall

I'm using `ufw`, with the default of blocking every incoming request and allowing outgoing request, then allowing more requests as needed, such as ssh. Remember to allow ssh before enabling the firewall otherwise you'll be blocked from the server.

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw enable
```

### Fail2Ban

Autoban IPs that check passwords.

```bash
sudo apt install fail2ban
systemctl enable --now fail2ban

sudo vim /etc/fail2ban/jail.local
```
Setup a basic configuration at jail.local.
    ```
    [DEFAULT]
    chain = input
    
    [sshd]
    enabled = true
    port = 22
    maxretry = 3
    bantime = 1h
    findtime = 10m
    ```


## Install Docker

### Add Docker's official GPG key:
```bash
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

### Add the repository to Apt sources:
```bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

After that is done you can just install docker with the command:

`sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`
