# PoC with commands to install some packages on SO

First step is download all the files in one VM like bastion with internet connectivity to be able to access:

All the commands was tested on a virtual machine with centos 8 box created by vagrant

Fixing CentsOS 8 repo:

```sh
cd /etc/yum.repos.d/;
sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*;
sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
```

Install wget to download the files:
```sh
dnf install wget -y
```

### WARNING - SELINUX is enabled, please set to permissive running this command:<br>
```sh
setenforce 0
```


### VM CICD:

### Python + Ansible + Jenkins

#### To install python38, you need to download these files via WGET before and, run these commands on Target VM:

Before, copy the files via scp;<br> 

```sh
rpm -Uvh https://vault.centos.org/centos/8/BaseOS/x86_64/os/Packages/chkconfig-1.19.1-1.el8.x86_64.rpm
rpm -ivh https://vault.centos.org/centos/8/AppStream/x86_64/os/Packages/python38-pip-wheel-19.3.1-4.module_el8.5.0+896+eb9e77ba.noarch.rpm
rpm -ivh https://vault.centos.org/centos/8/AppStream/x86_64/os/Packages/python38-setuptools-wheel-41.6.0-5.module_el8.5.0+896+eb9e77ba.noarch.rpm
rpm -ivh https://vault.centos.org/centos/8/AppStream/x86_64/os/Packages/python38-libs-3.8.8-4.module_el8.5.0+896+eb9e77ba.x86_64.rpm
rpm -ivh https://vault.centos.org/centos/8/AppStream/x86_64/os/Packages/python38-3.8.8-4.module_el8.5.0+896+eb9e77ba.x86_64.rpm
```

#### Installing Ansible, you need to download these files via WGET before and, run these commands on Target VM:

Before, copy the files via scp;<br> 

```sh
rpm -ivh https://vault.centos.org/centos/8/BaseOS/x86_64/os/Packages/python3-ply-3.9-9.el8.noarch.rpm
rpm -ivh https://vault.centos.org/centos/8/BaseOS/x86_64/os/Packages/python3-pycparser-2.14-14.el8.noarch.rpm
rpm -ivh https://vault.centos.org/centos/8/BaseOS/x86_64/os/Packages/python3-cffi-1.11.5-5.el8.x86_64.rpm
rpm -ivh https://vault.centos.org/centos/8/BaseOS/x86_64/os/Packages/python3-cryptography-3.2.1-5.el8.x86_64.rpm
rpm -ivh https://vault.centos.org/centos/8/AppStream/x86_64/os/Packages/python3-pytz-2017.2-9.el8.noarch.rpm
rpm -ivh https://vault.centos.org/centos/8/AppStream/x86_64/os/Packages/python3-babel-2.5.1-7.el8.noarch.rpm
rpm -ivh https://vault.centos.org/centos/8/AppStream/x86_64/os/Packages/python3-markupsafe-0.23-19.el8.x86_64.rpm
rpm -ivh https://vault.centos.org/centos/8/AppStream/x86_64/os/Packages/python3-jinja2-2.10.1-3.el8.noarch.rpm
rpm -ivh http://mirror.nsc.liu.se/centos-store/8.5.2111/configmanagement/x86_64/ansible-29/Packages/s/sshpass-1.06-8.el8.x86_64.rpm
rpm -ivh http://mirror.nsc.liu.se/centos-store/8.5.2111/configmanagement/x86_64/ansible-29/Packages/a/ansible-2.9.27-1.el8.noarch.rpm
```

### To install Jenkins, you need to download these files via WGET before and, run these commands on Target VM: 

#### Steps on Bastion:

```sh
wget https://get.jenkins.io/war-stable/2.346.3/jenkins.war -O jenkins.war
wget --no-check-certificate  https://nadwey.eu.org/java/11/jdk-11.0.17/jdk-11.0.17_linux-x64_bin.tar.gz -O java.tar.gz
```
#### Steps on Target VM:

Before, copy the files via scp;<br> 

```sh

mkdir -p /usr/local/java && cd /usr/local/java/
Copy the java file java.tar.gz downloaded before;
tar -xzvf java.tar.gz

export JAVA_HOME=/usr/local/java/jdk-11.0.17
export CLASSPATH=$:CLASSPATH:$JAVA_HOME/lib/
export PATH=$PATH:$JAVA_HOME/bin
```

#### Copy Jenkins file downloaed on Bastion on /usr/local/bin/ directory;

#### Install manually to enable fonts on Jenkins and, ignore JWT error:
```sh

rpm -ivh  https://vault.centos.org/centos/8/BaseOS/x86_64/os/Packages/fontpackages-filesystem-1.44-22.el8.noarch.rpm
rpm -ivh https://vault.centos.org/centos/8/BaseOS/x86_64/os/Packages/dejavu-fonts-common-2.35-7.el8.noarch.rpm
rpm -ivh https://vault.centos.org/centos/8/BaseOS/x86_64/os/Packages/dejavu-sans-fonts-2.35-7.el8.noarch.rpm
rpm -ivh https://vault.centos.org/centos/8/BaseOS/x86_64/os/Packages/fontconfig-2.13.1-4.el8.x86_64.rpm
```

#### Create Jenkins service file on SystemD:

```sh
vi /etc/systemd/system/jenkins.service
```
Add this content:

```sh

[Unit]
Description=Jenkins Service
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/local/java/jdk-11.0.17/bin/java -Djava.awt.headless=true -jar /usr/local/bin/jenkins.war
Restart=on-abort

[Install]
WantedBy=multi-user.target

```

```sh
systemctl daemon-reload && systemctl enable jenkins.service && systemctl start jenkins.service
```

See the first password to add on Jenkins:

```sh
cat /root/.jenkins/secrets/initialAdminPassword
```


### VM SISTEMAS:

### Nexus

### Steps on Bastion:

```sh
wget --no-check-certificate https://nadwey.eu.org/java/8/jdk-8u201/jdk-8u201-linux-x64.tar.gz -O java.tar.gz 
wget https://download.sonatype.com/nexus/3/nexus-3.44.0-01-unix.tar.gz
```

##### Commands to install Nexus

```sh
mkdir -p /usr/local/java && cd /usr/local/java/
```
Copy the java.tar.gz file on **/usr/local/java/** <br>

```sh
export JAVA_HOME=/usr/local/java/jdk1.8.0_201
export CLASSPATH=$:CLASSPATH:$JAVA_HOME/lib/
export PATH=$PATH:$JAVA_HOME/bin

source /etc/profile

```

Some commands to configure Nexus service:

```sh
groupadd nexus;
useradd nexus -d /home/nexus -s /bin/sh -g nexus;
chown -R nexus:nexus /opt/nexus-3.23.0-03;
chmod -R 750 /opt/nexus-3.23.0-03/;
chown -R nexus:nexus /opt/sonatype-work/;
chmod -R 750 /opt/sonatype-work/;

```
Check if Java version is configured on nexus file:<br>
```sh
grep -i "INSTALL4J_JAVA_HOME_OVERRIDE" /opt/nexus-3.23.0-03/bin/nexus
```
Do a backup of Nexus original config file:<br>

```sh
cp /opt/nexus-3.23.0-03/bin/nexus /opt/nexus-3.23.0-03/bin/nexus.bak
```

Change the line with the Java version installed before:<br>
```sh
sed -i "s%# INSTALL4J_JAVA_HOME_OVERRIDE=%INSTALL4J_JAVA_HOME_OVERRIDE=/usr/local/java/jdk1.8.0_201%g" /opt/nexus-3.23.0-03/bin/nexus
```

#### Create Nexus service file on SystemD:

```sh
vi /etc/systemd/system/nexus.service
```

Add this content:

```sh

[Unit]
Description=nexus service
After=network.target

[Service]
Type=forking
LimitNOFILE=65536
ExecStart=/opt/nexus-3.23.0-03/bin/nexus start
ExecStop=/opt/nexus-3.23.0-03/bin/nexus stop
User=nexus
Restart=on-abort
TimeoutSec=600

[Install]
WantedBy=multi-user.target


```

```sh
systemctl daemon-reload && systemctl enable nexus.service && systemctl start nexus.service
```
