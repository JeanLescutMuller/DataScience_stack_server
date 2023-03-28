# Data Science stack server
This set of "bootstrap" scripts are designed to automatically install (on a raw, naked server) the necessary stack (set of services) necessary for a Data Scientist to work (or at least me !)

## Details of the stack :
All the IDE are cloud-based. This stack is :
- Some basics Unix tool & helpers
- Nginx
- **Jupyterhub, Anaconda and Python 3**
- **R and R Studio**
- **Spark**
- Shell-in-a-box, a cloud-based Terminal server
- Codiad and gc++ compiler for C++ development (to be used for the most adventurous of us !)
Even if it looks like `install.sh` can do all the work alone, I recommend to follow the scripts step by step.

## Compatibility :

### Infrastructure :
- Works on On-premise bareservers, VM & VPS (for `👨‍💻 Perso` server, for example)
- Works on AWS for EC2 or EMR clusters (`🔶 AWS EC2` for example)
- Works on GCP Compute Engine VMs (`🌀 GCP VertexAI VM` for example)

Note: I used these scripts to configure my "Luke" Personal Server

### OS :
Any common Linux distro : `🔵 Debian`, `🟢 Ubuntu`, `🔴 CentOS`, `🟡 RHEL` ...

## License
This script is Open-source, and I shared that freely under [Creative Common License](https://en.wikipedia.org/wiki/Creative_Commons_license) under "Attribution alone" terms. (code `BY`). Please click on the link if you're not familiar with these terms yet, in particular the clause of *Attribution*.
As long as the source (this repo) and the author (myself, Jean Lescut-Muller) is clearly displayed 
   - Any usage of these scripts, professional or not, for Commercial use or not **is allowed**
   - Any modification of the code **is allowed**
   - Any copy of distribution of the code **is allowed**

## Detailled usage :

### 1. Connect to the VM
- SSH (example `ssh -i ~/.ssh/jlescutmuller_rsa_passphrase.pem $admin_user@10.2.227.16`)
- AWS EC2 Serial Console
- ...

### 2. Install basic binaries & Clone this project

```bash
sudo su
cd # go to home
```

Installing base tools (Although only git is necessary at this stage)
<table><tr><td>

`🔵 Debian` `🟢 Ubuntu`
```bash
apt update && apt install -y git vim tree telnet wget
```

</td><td>

`🔴 CentOS` `🟡 RHEL`
```bash
yum update && yum install -y git vim tree telnet wget
```

</td></tr></table>

```bash
git clone https://github.com/JeanLescutMuller/DataScience_stack_server.git
cd ./DataScience_stack_server
```

### 3. Setting up .bashrc for users :

<table><tr><td>

`👨‍💻 Perso`
```bash export admin_user='enrices'```

</td><td>

`🌀 GCP VertexAI VM` 
```bash
export admin_user='jupyter'
```

</td></tr><tr><td>

`🔶🔴 AWS EC2 CentOS`
```bash
export admin_user='centos'
```

</td><td>

`🔶🔶 AWS EMR`
```bash
export admin_user='hadoop'
```

</td></tr></table>

```bash
chmod +x ./01_unix_helpers/root/usr/sbin/adduser2
chmod +x ./01_unix_helpers/root/usr/bin/configurebashrc
cp -R ./01_unix_helpers/root/* /
configurebashrc # root
source ~/.bashrc
sudo -u $admin_user configurebashrc # non-root (main user)
```












### Setting up VM (from a local shell)


<table><tr><td>
  
GCP VertexAI VM :
```bash
export admin_user='jupyter' # main user, with sudo privileges
export os='debian'
```

AWS EC2 perso :
```bash
export admin_user='enrices' # main user, with sudo privileges
export os='debian'
```

</td></tr></table>

# Setting up .bashrc for users :
chmod +x ./01_unix_helpers/root/usr/sbin/adduser2
chmod +x ./01_unix_helpers/root/usr/bin/configurebashrc
cp -R ./01_unix_helpers/root/* /
configurebashrc # root
source ~/.bashrc
sudo -u $admin_user configurebashrc # non-root (main user)
```

Only on GCP Vertex AI :
```bash
# Make the JupyterLab Theme dark (to have a black Shell background in the Terminals)
# Or maybe just do that manually ?
exit
source ~/.bashrc
mkdir -p  ~/.jupyter/lab/user-settings/@jupyterlab/apputils-extension
echo '{"theme": "JupyterLab Dark"}' > ~/.jupyter/lab/user-settings/@jupyterlab/apputils-extension/themes.jupyterlab-settings
```

### Installing JupyterHub,etc... on a naked server (from a remote shell)

<table><tr><td>
  
AWS EMR Cluster :
```bash
export admin_user='hadoop'
export os='centos'
export tempdir='/dev/shm'
export service_system="SystemV"
export home_dir='/data'
```

</td><td>

AWS EC2 Instance :
```bash
export admin_user='centos'
export os='centos'
export tempdir="/tmp"
export service_system="SystemD"
export home_dir='/data'
```
  
</td></tr></table>

```bash
# Connect to the instance :
ssh -i ~/.ssh/jlescutmuller_rsa_passphrase.pem $admin_user@10.2.227.16 # Enter personal password
# Or something like ssh -i ~/.ssh/id_rsa.pem $admin_user@10.23.3.137
sudo su
```

<table>
<tr><td>
  
AWS EC2 Instance :

(Optional) Mounting an EBS Volume to /data :
It is advised to create a ´/data´ folder on the machine and mount a persistent EBS volume on it.
More info on how-to at https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html
```bash
mkfs -t xfs /dev/nvme1n1
yum update -y && yum upgrade -y && yum install -y xfsprogs
mkdir /data
mount /dev/nvme1n1 /data
```
  
</td></tr></table>



```bash
cd # go to home
yum update && yum install -y git vim tree telnet wget # Installing base tools (Although only git is necessary at this stage)
git clone https://github.com/JeanLescut/DataScience_stack_server.git
cd ./DataScience_stack_server

./01_unix_helpers/main.sh
source ~/.bashrc
sudo -u $admin_user configurebashrc

# Adding users :
./02_ssh_access/main.sh
# adduser2 pass these arguments to adduser. on CentOS, adduser can take "-p" argument to take the encrypted password.
# On such system (not on Debian-based), "man adduser" will give more information : using "crypt" to retrieve this code from clear/plain password
adduser2 jlescutmuller -d $home_dir/jlescutmuller -G wheel -p '$6$DKFej1xka8DYxrhi$HnlSzi4 ...... QGkHFpVK34zam2K8fFWbFu2AYvtLokqEJQtBxnWS8Mn9l71O1'
# adduser2 user2 -d $home_dir/user2 -G wheel -p '...' etc...
```

And : 

```bash
# Setting up reverse proxy :
export services_ports=(80 8080) # for Nginx (We prefer to open it on 2 ports, for redundancy)
./03_nginx/main.sh

# Installing Jupyterhub :
anaconda_location="/opt/anaconda3" ./04_jupyterhub/main.sh
usermod -aG jupyterhub_users jlescutmuller
# usermod -aG jupyterhub_users user2... etc..

# Installing R and RStudio :
./05_rstudio/main.sh
```


Palet : 🔴🟠🟡🟢🔵🟣🟤⚫⚪
