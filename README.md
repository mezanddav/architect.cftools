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

Install GO
`sudo apt install golang -y`\
`export PATH=$PATH:$(go env GOPATH)/bin`
`echo 'export PATH=$PATH:$(go env GOPATH)/bin' >> ~/.bashrc`
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
`sudo ufw enable`\
`sudo ufw allow ssh`\
`sudo ufw allow 80/tcp`\
`sudo ufw allow 443/tcp`\
`sudo ufw status`\
You should see your open ports. 

`reboot` and let's install the agent.


## Install Architect agent

Run the download command:\
`bash <(curl -s https://architect.cftools.com/releases/1.0.7/install.sh)`\

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

## PORT Config
`sudo ufw allow 8090/tcp`\
`sudo ufw allow 666/tcp`

#### Whitelist FQDNs (Domain Names)
`sudo apt install iptables-persistent`\

`sudo iptables -A OUTPUT -p tcp -d cc-global.gameserver.cloud --dport 666 -j ACCEPT`
`sudo iptables -A INPUT -p tcp -s cc-global.gameserver.cloud --sport 666 -j ACCEPT`

`sudo netfilter-persistent save`

#### Verify Firewall Rules\
`sudo ufw status verbose`\
`sudo iptables -L -v -n`\

