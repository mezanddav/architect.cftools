# CFTools Architect Debian 12 setup guide

[CFTools Cloud Discord](https://discord.com/invite/k7Zdw6cXSH)

[Architect download & License](https://discord.com/channels/373098389174484992/1312066884467953775)

[I used Hetzner for hosting](https://www.hetzner.com/)

This guide assumes you already have Debian 12 installed
with SSH keys set up and ready to use. We won’t cover
the installation of Debian or the setup of SSH keys here,
but there are plenty of great tutorials available online
to guide you through those steps.


Your first command is to update Debian to latest version:\
`sudo apt update && sudo apt upgrade -y`


Install basic tools that might be required during the installation:\
`sudo apt install -y build-essential curl wget git gnupg lsb-release ca-certificates`\
`sudo apt install software-properties-common -y`


Ensure the universe, multiverse, and restricted repositories are enabled:\
`sudo add-apt-repository contrib`\
`sudo add-apt-repository non-free`\
`sudo apt update`

We have to configure UFW:\
`sudo apt install ufw`\
`sudo ufw enable`\
`sudo ufw allow ssh`\
`sudo ufw allow 80/tcp`\
`sudo ufw allow 443/tcp`\
`sudo ufw status`



