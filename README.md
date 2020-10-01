# linux-server-start


## Create user, setup SSH

Connect through SSH to remote Debian server and update repositories and install some initial needed packages:

```
sudo apt-get update && apt upgrade; \
sudo apt-get install -y vim mosh tmux htop git curl wget unzip zip gcc build-essential make
```

### Configure SSH:

On your PC:
```
ssh-keygen
sudo ssh-copy-id -i /home/USERNAME_YOUR_PC/.ssh/id_rsa_xasky_dev www@SERVER_IP`
```
On server:
```
addgroup wheel
useradd -d /home/www -m -G wheel www -s /bin/bash
```
If cannot work with sudo under www then:
```
usermod -a -G'wheel' www
```

```
sudo vim /etc/ssh/sshd_config
    AllowUsers www
    PermitRootLogin no
    PasswordAuthentication no
```

Restart SSH server:

```
sudo service ssh restart
```

# Configure Firewalls
## UFW

Allow ssh connection:
```
sudo ufw allow 22/tcp
```
Enable UFW:
```
sudo ufw enable
```

CONFIGURE DOCKER_CONTAINERS HERE

## Install ufw-docker

Download `ufw-docker` script

    sudo wget -O /usr/local/bin/ufw-docker \
      https://github.com/chaifeng/ufw-docker/raw/master/ufw-docker
    chmod +x /usr/local/bin/ufw-docker

Then using the following command to modify the `after.rules` file of `ufw`

    sudo ufw-docker install

This command does the following things:
- Back up the file `/etc/ufw/after.rules`
- Append the rules of UFW and Docker at the end of the file

For more info about ufw-docker:
https://github.com/chaifeng/ufw-docker/edit/master/README.md
