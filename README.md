# Config

## Setup VM (Debian)

### SSH (client)

```bash
ssh-keygen -t rsa -b 4096
```

Copy .pub to server

Connect with username and password:

```bash
ssh root@45.32.7.185
```

### Configure User

Create user

```bash
adduser user
```

Enter password.

Add user to sudo group

```bash
usermod -aG sudo user
```

Switch to user

```bash
su - user
```

### SSH Server

```bash
sudo apt-get install openssh-server
```

Clean up existing keys (optional), if needed:

```bash
rm /etc/ssh/ssh_host_*
```

```bash
sudo dpkg-reconfigure openssh-server
```

Copy a local SSH key to the server:

```bash
sudo ssh-copy-id -i ~/.ssh/id_rsa.pub user@$remote_host
```

Remove the entry for the server in ~/.ssh/known_host.

On the client add the ssh config:

```bash
Host 45.32.7.185
  User user
  IdentityFile ~/.ssh/id_rsa_vultr
```

Connect to the server with user:

```bash
ssh user@$remote_host
```

On the server edit the `sshd_config`:

```bash
sudo vim /etc/ssh/sshd_config
```

```
ChallengeResponseAuthentication no
PasswordAuthentication no
UsePAM no
PermitRootLogin no
```

Restart SSH server:

```bash
sudo systemctl reload ssh
```

Reconnect to server.

### Install Dependencies

```bash
sudo apt-get update
sudo apt install git -y
```

### Github

Add SSH key to Github:

```bash
ssh-keygen -t rsa -b 4096
```

Add SSH config (from /dotfiles/ssh_config).

Start the ssh-agent in the background:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa_github
```

And add this to `.bashrc` to automatically start the ssh-agen and add the keys:

```
if [ ! -S ~/.ssh/ssh_auth_sock ]; then
  eval `ssh-agent`
  ln -sf "$SSH_AUTH_SOCK" ~/.ssh/ssh_auth_sock
fi
export SSH_AUTH_SOCK=~/.ssh/ssh_auth_sock
ssh-add -l > /dev/null || ssh-add ~/.ssh/id_rsa_github
```

Make dir `~/code`:

```bash
mkdir code
```

Clone repo:

```bash
git clone git@github.com:phil-lgr/config.git
```

### Dot Files

Copy dotfiles:

```enable
chmod 755 ./scripts/*.sh
```

fs.inotify.max_user_watches=524288

### Node

```
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.2/install.sh | bash
```

### Docker

Follow docs https://docs.docker.com/install/linux/docker-ce/debian/

sudo groupadd docker

newgrp docker

sudo usermod -aG docker \$USER

sudo systemctl enable docker
