# CFTools Architect Debian 12 setup guide

[CFTools Cloud Discord](https://discord.com/invite/k7Zdw6cXSH)

[Architect download & License](https://discord.com/channels/373098389174484992/1312066884467953775)

[I used Hetzner for hosting](https://www.hetzner.com/)

This guide assumes you already have Debian 12 installed
with SSH keys set up and ready to use. We won’t cover
the installation of Debian or the setup of SSH keys here,
but there are plenty of great tutorials available online
to guide you through those steps.


## Debian first steps

Your first command is to update Debian to the latest version:\
`sudo apt update && sudo apt upgrade -y`

Install basic tools that might be required during the installation:\
`sudo apt install -y build-essential curl wget git gnupg lsb-release ca-certificates`\
`sudo apt install software-properties-common -y`\
`sudo apt install mc -y`

Ensure the universe, multiverse, and restricted repositories are enabled:\
`sudo add-apt-repository contrib`\
`sudo add-apt-repository non-free`\
`sudo apt update`

## Install GO  
[Go to the Go download page](https://go.dev/dl/)  
Stable versions -> Grab the link (Right click and Copy link address) for: Archive	Linux	x86-64  
(for me) `wget https://go.dev/dl/go1.24.1.linux-amd64.tar.gz`  
(for me) `rm -rf /usr/local/go && tar -C /usr/local -xzf go1.24.1.linux-amd64.tar.gz`  
`echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc`  
`mkdir -p /root/go`  
`echo 'export GOPATH=/root/go' >> ~/.bashrc`  
`echo 'export PATH=$PATH:$GOPATH/bin' >> ~/.bashrc`  
`source ~/.bashrc`  
`go version`  


### Install GUM
`go install github.com/charmbracelet/gum@latest`

Verify installation:\
`gum --version`\
Expected output: `gum version v0.15.2 (d1fc051)`

## Install UFW
`sudo apt install ufw`

_ATTENTION: DO NOT Enable UFW without enabling SSH and port 22.\
Otherwise, you will lock yourself out._

Basic UFW config\
`sudo ufw allow ssh`\
`sudo ufw allow 80/tcp`\
`sudo ufw allow 443/tcp`

Enable: `sudo ufw enable`\
Check UFW status: `sudo ufw status`\
The output should be a list of ports you just opened. 

Useful: `sudo ufw reload`

`reboot` and let's install the agent.

## TCP/UDP cc-global.gameserver.cloud for port 666  
Ensure dnsutils is installed:  
`sudo apt install ufw dnsutils -y`

Create a script that:  
`sudo nano /usr/local/bin/update_firewall.sh`

Add the following:  
```
#!/bin/bash

# Define the domain to resolve
DOMAIN="cc-global.gameserver.cloud"

# Perform NS lookup and extract the first resolved IP
IP=$(nslookup $DOMAIN | awk '/^Address: / { print $2 }' | tail -n1)

# Check if the lookup was successful
if [[ -z "$IP" ]]; then
    echo "[$(date)] ERROR: Could not resolve $DOMAIN"
    exit 1
fi

# Check if UFW already allows this IP
if ! sudo ufw status | grep -q "$IP"; then
    # Delete old rules for this domain (optional)
    sudo ufw delete allow in proto tcp to any port 666
    sudo ufw delete allow out proto tcp to any port 666

    # Add new rules
    sudo ufw allow in from "$IP" to any port 666 proto tcp comment "Allow inbound for $DOMAIN"
    sudo ufw allow out to "$IP" from any port 666 proto tcp comment "Allow outbound for $DOMAIN"

    echo "[$(date)] Updated firewall rules for $DOMAIN ($IP)"
else
    echo "[$(date)] Firewall rules already set for $DOMAIN ($IP)"
fi
```
Make the Script Executable  
`sudo chmod +x /usr/local/bin/update_firewall.sh`  

Edit the crontab:  
`sudo crontab -e`  

Add this line at the end:  
`*/10 * * * * /usr/local/bin/update_firewall.sh >> /var/log/firewall_update.log 2>&1`


## Architect port config
`sudo ufw allow in proto tcp to any port 8090`\
`ufw allow in proto tcp *:8090`
asdasd.com
`sudo ufw allow out proto tcp to any port 8090`


## Install Architect agent

Run the download command:\
[Grab the download link on Discord](https://discord.com/channels/373098389174484992/1312066884467953775/1316064473097699419).\
(for me) `bash <(curl -s https://architect.cftools.com/releases/1.0.8/install.sh)`

You should see this now:
```
 * All set up!
 *   /$$$$$$  /$$$$$$$$ /$$$$$$$$                  /$$
 *  /$$__  $$| $$_____/|__  $$__/                 | $$
 * | $$  \__/| $$         | $$  /$$$$$$   /$$$$$$ | $$  /$$$$$$$
 * | $$      | $$$$$      | $$ /$$__  $$ /$$__  $$| $$ /$$_____/
 * | $$      | $$__/      | $$| $$  \ $$| $$  \ $$| $$|  $$$$$$
 * | $$    $$| $$         | $$| $$  | $$| $$  | $$| $$ \____  $$
 * |  $$$$$$/| $$         | $$|  $$$$$$/|  $$$$$$/| $$ /$$$$$$$/
 *  \______/ |__/         |__/ \______/  \______/ |__/|_______/
```

Step by step install the agent. The license is on [Discord](https://discord.com/channels/373098389174484992/1312066884467953775/1316064473097699419).

Take note of the Agent installation path: `/opt/cftools/architect/agent/`

`cd /opt/cftools/architect/agent/`

You need your Architect root password and config file:\
`sudo nano /opt/cftools/architect/agent/config.toml`


## Dayz port config
Don't forget to open the ports you need. The ports below are the Architect default ports.\
I suggest you put the server name in the comment to keep things clean:\
eg.: `sudo ufw allow out 2403/udp comment "KarmaKrew Cherna Game Port UDP Outbound"`

Game port\
`sudo ufw allow out 2403/udp comment "Game Port UDP Outbound"`

Query port\
`sudo ufw allow out 2406/udp comment "Query Port UDP Outbound"`

RCon port\
`sudo ufw allow out 2409/udp comment "RCon Port UDP Outbound"`

Reload UFW\
`sudo ufw reload`
