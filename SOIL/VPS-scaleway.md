# VPS @ scaleway = 2.99 EUR/month for 2 GB RAM

Signup at https://www.scaleway.com

Then generate a ssh key (for windows see https://www.scaleway.com/docs/configure-new-ssh-key/):

```
ssh-keygen -t rsa  
# name: scaleway_sshkey_01
mv scaleway_sshkey_01* ~/.ssh
cat ~/.ssh/scaleway_sshkey_01.pub
```
copypaste into https://cloud.scaleway.com/#/credentials

create server: https://cloud.scaleway.com/#/servers/new  
VC1S - 2 x86 64bit Cores, 2GB memory (2.99 EUR/month)  
name it friendly & short  
choose an image: Debian Jessie  
wait ~1 minute  

### connect:  
```
ssh root@163.172.162.229
```
use *your* shown IP address, of course. 

(whenever I say `ssh`, for Windows use [putty.exe](http://www.putty.org/) to connect to e.g. `163.172.162.229` with username `root`)

Welcome to your own virtual server!  

### swap file
If RAM runs out, there will be a swapfile:
```
dd if=/dev/zero of=/swapfile bs=1M count=4096
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo "/swapfile none swap defaults 0 0" >> /etc/fstab
```


### updating & dependencies
```
sudo apt-get update && sudo apt-get -y upgrade
sudo apt-get install -y curl git libgmp3-dev build-essential 
```

### golang
```
curl -O https://storage.googleapis.com/golang/go1.6.3.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.6.3.linux-amd64.tar.gz
rm go1.6.3.linux-amd64.tar.gz
```

### new user ```soil```
```
adduser soil
login soil
```

### install gsoiltestnet
Now continue with [gsoiltestnet.md](gsoiltestnet.md).

---

(C) Andreas Krueger 2016, support me: [SOIL] 0x82e6fac28f2c0d89b442f4b7c0d72fa47c54efa7
