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


