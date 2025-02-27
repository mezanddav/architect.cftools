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

Your first command is to update Debian to latest version:\
`sudo apt update && sudo apt upgrade -y`

Install basic tools that might be required during the installation:\
`sudo apt install -y build-essential curl wget git gnupg lsb-release ca-certificates`\
`sudo apt install software-properties-common -y`\
`sudo apt install mc -y`

Install GO\
`sudo apt install golang -y`\
`export PATH=$PATH:$(go env GOPATH)/bin`\
`echo 'export PATH=$PATH:$(go env GOPATH)/bin' >> ~/.bashrc`\
`source ~/.bashrc`

Verify installation:\
`gum --version`\
Expected output: `gum version v0.15.2 (d1fc051)`

#### Do we need this? Don't think so.
Ensure the universe, multiverse, and restricted repositories are enabled:\
`sudo add-apt-repository contrib`\
`sudo add-apt-repository non-free`\
`sudo apt update`

We have to install and config UFW:\
`sudo apt install ufw`\
`sudo ufw allow ssh`\
`sudo ufw allow 80/tcp`\
`sudo ufw allow 443/tcp`\
`sudo ufw allow in 2302/tcp` OR whatever is your Game Port\
`sudo ufw allow in 2303/tcp` OR whatever is your Query Port\
`sudo ufw allow in 2305/tcp` or whatever is your RCon Port\
`sudo ufw enable`\
`sudo ufw status`\
You should see your open ports. 

Useful: `sudo ufw reload`

_ATTENTION: DO NOT Enable UFW without enabling SSH and port 22.\
Otherwise you will lock yourself out._

`reboot` and let's install the agent.


## PORT Config
`sudo ufw allow in proto tcp to any port 8090`\
`sudo ufw allow out proto tcp to any port 8090`


## Install Architect agent

Run the download command:\
`bash <(curl -s https://architect.cftools.com/releases/1.0.7/install.sh)`

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

Step by step install the agent. License is on [Discord](https://discord.com/channels/373098389174484992/1312066884467953775).

Take note of the Agent installation path: `/opt/cftools/architect/agent/`

`cd /opt/cftools/architect/agent/`

You need your Architect root password and config file:\
`sudo nano /opt/cftools/architect/agent/config.toml`


# Game Port
`sudo ufw allow out 2403/tcp comment "Game Port TCP Outbound"`\
`sudo ufw allow out 2403/udp comment "Game Port UDP Outbound"`

# Query Port
`sudo ufw allow out 2406/tcp comment "Query Port TCP Outbound"`\
`sudo ufw allow out 2406/udp comment "Query Port UDP Outbound"`

# RCon Port
`sudo ufw allow out 2409/tcp comment "RCon Port TCP Outbound"`\
`sudo ufw allow out 2409/udp comment "RCon Port UDP Outbound"`

# Reload UFW
`sudo ufw reload`
