# linux-server-start


## Create user, setup SSH

Connect through SSH to remote Debian server and update repositories and install some initial needed packages:

```
sudo apt-get update && apt upgrade; \
sudo apt-get install -y vim mosh tmux htop git curl wget unzip zip gcc build-essential make
```

### Configure SSH:

On server:
```
useradd -d /home/www -m -G sudo www -s /bin/bash
```
If cannot work with sudo under www then:
```
usermod -a -G 'sudo' www
```
Enter new password and deny connect as root
```
sudo passwd www

sudo vim /etc/ssh/sshd_config
    AllowUsers www
    PermitRootLogin no
    PasswordAuthentication no
```

Restart SSH server:

```
sudo service ssh restart
```
On your PC:
```
ssh-keygen
sudo ssh-copy-id -i /home/USERNAME_YOUR_PC/.ssh/id_rsa_xasky_dev www@SERVER_IP`
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
    sudo chmod +x /usr/local/bin/ufw-docker

Then using the following command to modify the `after.rules` file of `ufw`

    sudo ufw-docker install

This command does the following things:
- Back up the file `/etc/ufw/after.rules`
- Append the rules of UFW and Docker at the end of the file

Expose the port 80 of the container httpd

    ufw-docker allow httpd 80


For more info about ufw-docker:
https://github.com/chaifeng/ufw-docker/blob/master/README.md

# PG BACKUP

Let pg_dump write to a file inside the Docker container, then copy that out to the host:

### correct dumping procedure:
```
docker exec -ti my_postgres_container bash -c 'pg_dump -Fc -U postgres > /db.dump'
docker cp my_postgres_container:/db.dump db.dump
```
### correct restoring procedure:
```
docker cp db.dump my_postgres_container:/db.dump
docker exec -ti my_postgres_container pg_restore -U postgres -c -d postgres db.dump
```

postgre_backup.sh

```
#!/usr/bin/env bash

pg_user="postgres"
container_name="db"
username="vic"

docker exec -ti $container_name bash -c "pg_dump -Fc -U $pg_user > /db.dump"
mkdir "/home/$username/backups/"
cp "/home/$username/backups/new.dump" "/home/$username/backups/old.dump"
docker cp $container_name:/db.dump "/home/$username/backups/new.dump"
echo `date +"%Y%m%d%H%M"`
```

### push shedule task in cron linux

crontab -e

add at the end:

```
# m h  dom mon dow   command
0 3 * * * /home/username/backups/backup.sh
# empty line
```
