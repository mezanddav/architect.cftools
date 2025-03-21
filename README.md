# CFTools Architect Debian 12 setup guide

[CFTools Cloud Discord](https://discord.com/invite/k7Zdw6cXSH)

[Architect download & License](https://discord.com/channels/373098389174484992/1312066884467953775)

[I used Hetzner for hosting](https://www.hetzner.com/)

[Architect Docs](https://help.cftools.com/en/architect)

This guide assumes you already have Debian 12 installed with SSH keys   
set up and ready to use. We won’t cover the installation of Debian or  
the setup of SSH keys here, but plenty of great tutorials are available  
online to guide you through those steps.  


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
_Debian’s package repositories often update slowly, meaning the  
available Go version may be outdated. Since the Agent requires GUM,  
which depends on a newer version of Go, it must be installed manually._  

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

Verify installation:  
`reboot`  
`go version`  
Expected output: `go version go1.24.1 linux/amd64`  
`gum --version`   
Expected output: `gum version v0.15.2 (d1fc051)`  

_NOTE: If the versions are not coming up, there is a good chance you messed up the paths  
(or this guide is shit)  
Anyway, `nano ~/.bashrc` and try to fix it._

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
`sudo ufw allow in proto tcp to any port 8090`  
`sudo ufw allow out proto tcp to any port 8090`  


## Install Architect agent

This x64 executable is compatible with all x64 Linux distributions that  
follow the standard Linux folder structure (e.g., existing /opt, /var/log).  
The system must also support the following essential utilities:  
`lsb_release`
`uname`
`nice`
`bash` 
`taskset`  
These utilities are generally available on any non-minimal Linux distribution.

### Debian Consideration  
Debian is referenced in this guide because CFTools officially supports it.  
However, the installation process may be adapted for other  
compatible distributions, provided they meet the necessary requirements.  

### Running in Docker
The executable can also be run inside a Docker container for added flexibility.


Run the download command:  
[Grab the download link on Discord](https://discord.com/channels/373098389174484992/1312066884467953775/1316064473097699419).  
(for me) `bash <(curl -s https://architect.cftools.com/releases/1.0.12/install.sh)`  

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
Step by step install the agent.  

#### License
 The license is on [Discord](https://discord.com/channels/373098389174484992/1312066884467953775/1316064473097699419).

Pricing details provided by: _Philipp_

_One-time purchase: €140 (includes 2 agents).  
Subscription: €6 per month per agent.  
Volume licensing: Custom pricing for 10+ agents or resellers.  
No limits on the number of servers per agent.  
Final pricing details will be available in the CFCloud panel upon release._

#### Password

Take note of the Agent installation path: `/opt/cftools/architect/agent/`

`cd /opt/cftools/architect/agent/`

You need your Architect root password:  
`sudo nano /opt/cftools/architect/agent/root.txt`  

_The default root user password for architect (not the system user) is a  
randomized uuid4 (122 bit entropy). The password is meant to be changed.  
Don't use weak passwords. All agent credentials are to be treated as  
confidential API keys. If your use case requires it, you can disable  
remote root user access and only allow it from localhost eg. 127.0.0.1._


and config file:  
`sudo nano /opt/cftools/architect/agent/config.toml`  

_NOTE: If you install the Architect agent in a different directory, a new root pass will be created for you._

How do you start the Agent later:  
`systemctl start ArchitectAgent`  


## DayZ port config  
Don't forget to open the ports you need. The ports below are the Architect default ports.  
I suggest you put the server name in the comment to keep things clean:  
eg.: `sudo ufw allow 2403/udp comment "KarmaKrew Cherna Game Port UDP Outbound"`

Game port  
`sudo ufw allow 2302/udp comment "Game Port UDP Outbound"`

Query port  
`sudo ufw allow 2303/udp comment "Query Port UDP Outbound"`

RCon port  
`sudo ufw allow 2305/udp comment "RCon Port UDP Outbound"`

Reload UFW  
`sudo ufw reload`


## Connecting with the Manager
[Grab the download link on Discord](https://discord.com/channels/373098389174484992/1312066884467953775/1316064473097699419).  
1. Install
2. Register Agent: `root` user and your password from `/opt/cftools/architect/agent/root.txt`
3. Titles -> Kebab menu -> Download
4. Deploy Server -> Follow Steps

[CFTools Architect Manager video tutorial](https://www.youtube.com/watch?v=7cre0XxOaiM)

# Webhooks
WebHooks accept raw body payloads instead of having everything pre-defined and  
they are not Discord-specific. So you need to formulate the raw payload that Discords accepts.   
  
Simple message for example:   
```
{
  "content": "{{server}} started"
}
```
[Discord Webhook Resource / JSON Params](https://discord.com/developers/docs/resources/webhook#execute-webhook-jsonform-params)

Eample:  
```
{
  "username": "Mod Updates",
  "content": "Server Updated: {{server}} | Mod: {{mod}} | Time: {{date}}",
  "avatar_url": ""
}
```
