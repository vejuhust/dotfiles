# Ubuntu Linux Server Setup


## SSH

Set password for **root**:
```bash
sudo passwd root
```

### Server Setup
Install OpenSSH server:     
```bash
sudo apt-get install openssh-server
```

Edit `/etc/ssh/sshd_config` if you want to login as **root** with password via SSH:
```
# Authentication:
LoginGraceTime 120
PermitRootLogin yes 
StrictModes yes
```

Restart the service:
```bash
sudo service ssh restart
```

Rename this server if necessary:
```bash
vim /etc/hostname
```

Remove deprecated hostname in ~/.ssh/known_hosts if necessary: 
```bash
ssh-keygen -R DEPRECATED_HOSTNAME
```

### Password-free Login

Don't forget to create an alias for the server on local machine by adding a line in `/etc/hosts`.

#### OS X : bash
```bash
REMOTESVR=192.168.158.136
ssh-keygen -t rsa -b 2048
scp ~/.ssh/id_rsa.pub root@"$REMOTESVR":/tmp/id_rsa.pub
ssh root@"$REMOTESVR" "mkdir ~/.ssh/; cat /tmp/id_rsa.pub >> ~/.ssh/authorized_keys"
```

Or use this if **root** isn't allowed to login with password:
```bash
REMOTESVR=168.63.206.45
scp ~/.ssh/id_rsa.pub azureuser@"$REMOTESVR":/tmp/id_rsa.pub
ssh -t azureuser@"$REMOTESVR" "sudo mkdir /root/.ssh/; sudo cat /tmp/id_rsa.pub | sudo tee -a /root/.ssh/authorized_keys > /dev/null"
```

#### Linux : bash
```bash
REMOTESVR=192.168.158.136
ssh-keygen -t rsa -b 2048
ssh-copy-id -i ~/.ssh/id_rsa.pub root@"$REMOTESVR"
```

Or try this if **root** isn't allowed to login with password:
```bash
REMOTESVR=168.63.206.45
cat ~/.ssh/id_rsa.pub | ssh azureuser@"$REMOTESVR" "sudo mkdir /root/.ssh/; sudo tee -a /root/.ssh/authorized_keys > /dev/null"
```

#### Windows : cmder with msysgit
Install cmder: http://bliker.github.io/cmder/       
```bash
REMOTESVR=192.168.158.136
ssh-keygen -t rsa -b 2048
scp /c/Users/weye/.ssh/id_rsa.pub root@"$REMOTESVR":/tmp/id_rsa.pub
ssh root@"$REMOTESVR" "mkdir ~/.ssh/; cat /tmp/id_rsa.pub >> ~/.ssh/authorized_keys"
```



## Aliases

Append the following lines to `~/.bashrc` if it isn't there:
```bash
if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi
```

Overwrite local `~/.bash_aliases`:
```bash
curl -s https://raw.githubusercontent.com/vejuhust/dotfiles/master/bash/bash_aliases.sh > ~/.bash_aliases
```



## Tools

Consider mirror repositories in China, i.e. `vim /etc/apt/sources.list` -- e.g. [163](http://mirrors.163.com/.help/ubuntu.html)

### Utility
```bash
apt-get install --assume-yes --force-yes vim git screen zip unzip p7zip-full iftop vnstat fail2ban lynx
```

### Developer
```bash
apt-get install --assume-yes --force-yes gcc g++ build-essential python-dev python-pip python-setuptools python3-dev python3-pip python3-setuptools
```



## Configuration

### git
Reference: https://github.com/vejuhust/dotfiles/tree/master/git

Import **.gitconfig** file from GitHub: 
```bash
curl -s https://raw.githubusercontent.com/vejuhust/dotfiles/master/git/lin.gitconfig > ~/.gitconfig
```

Generate a new SSH key for GitHub:
```bash
ssh-keygen -t rsa -b 4096 -C "$(git config --global user.email)"
```

### SSoHH
Reference: https://github.com/vejuhust/Server-Status-over-HTTP-HEAD

On the reporting server, execute:
```bash
cp ~/ssohh/client_set.sh /usr/share/nginx/ssohh/
```

Run this on the new Linux server:
```bash
cd /tmp; curl http://service.yewei.me/client_set.sh -O; chmod +x client_set.sh; ./client_set.sh l; crontab -e
```

Go back to the reporting server, clean up:
```bash
rm -f /usr/share/nginx/ssohh/client_set.sh
```

