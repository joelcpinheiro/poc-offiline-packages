# poc-offiline-packages
PoC with commands to install some packages on SO


First step is download all the files in one VM like bastion with internet connectivity to be able to access:

All the commands was tested on a virtual machine with centos 8 box created by vagrant

Fixing CentsOS 8 repo:

cd /etc/yum.repos.d/;
sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*;
sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*

dnf install wget -y

### WARNING ### - SELINUX is enabled, please set to permissive
Offline installation:

VM CICD:

Python + Ansible + Jenkins

#### Installing python38:
rpm -Uvh https://vault.centos.org/centos/8/BaseOS/x86_64/os/Packages/chkconfig-1.19.1-1.el8.x86_64.rpm
rpm -ivh https://vault.centos.org/centos/8/AppStream/x86_64/os/Packages/python38-pip-wheel-19.3.1-4.module_el8.5.0+896+eb9e77ba.noarch.rpm
rpm -ivh https://vault.centos.org/centos/8/AppStream/x86_64/os/Packages/python38-setuptools-wheel-41.6.0-5.module_el8.5.0+896+eb9e77ba.noarch.rpm
rpm -ivh https://vault.centos.org/centos/8/AppStream/x86_64/os/Packages/python38-libs-3.8.8-4.module_el8.5.0+896+eb9e77ba.x86_64.rpm
rpm -ivh https://vault.centos.org/centos/8/AppStream/x86_64/os/Packages/python38-3.8.8-4.module_el8.5.0+896+eb9e77ba.x86_64.rpm

#### Installing Ansible:
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


### Installing Jenkins: 
https://www.server-world.info/en/note?os=CentOS_8&p=jenkins
https://mohitgoyal.co/2019/02/16/install-jenkins-in-offline-mode-on-centos-rhel/


Steps:

mkdir -p /usr/local/java && cd /usr/local/java/
wget --no-check-certificate  https://nadwey.eu.org/java/11/jdk-11.0.17/jdk-11.0.17_linux-x64_bin.tar.gz -O java.tar.gz
tar -xzvf java.tar.gz

export JAVA_HOME=/usr/local/java/jdk-11.0.17
export CLASSPATH=$:CLASSPATH:$JAVA_HOME/lib/
export PATH=$PATH:$JAVA_HOME/bin

wget https://get.jenkins.io/war-stable/2.346.3/jenkins.war -O /usr/local/bin/jenkins.war

Install manually to enable fonts on Jenkins and, ignore JWT error:

rpm -ivh  https://vault.centos.org/centos/8/BaseOS/x86_64/os/Packages/fontpackages-filesystem-1.44-22.el8.noarch.rpm;
rpm -ivh https://vault.centos.org/centos/8/BaseOS/x86_64/os/Packages/dejavu-fonts-common-2.35-7.el8.noarch.rpm
rpm -ivh https://vault.centos.org/centos/8/BaseOS/x86_64/os/Packages/dejavu-sans-fonts-2.35-7.el8.noarch.rpm
rpm -ivh https://vault.centos.org/centos/8/BaseOS/x86_64/os/Packages/fontconfig-2.13.1-4.el8.x86_64.rpm


vi /etc/systemd/system/jenkins.service

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


systemctl daemon-reload

firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --zone=public --add-service=http --permanent
firewall-cmd --reload


cat /root/.jenkins/secrets/initialAdminPassword

cat /var/lib/jenkins/secrets/initialAdminPassword(Original)


######## VM 192.168.10.122 - i-test-scenario-sistemas-cicd
Nexus: https://www.programmerall.com/article/933969578/
Steps:

##### Commands to install Nexus

setenforce 0

mkdir -p /usr/local/java && cd /usr/local/java/
wget --no-check-certificate https://nadwey.eu.org/java/8/jdk-8u201/jdk-8u201-linux-x64.tar.gz -O java.tar.gz

export JAVA_HOME=/usr/local/java/jdk1.8.0_201
export CLASSPATH=$:CLASSPATH:$JAVA_HOME/lib/
export PATH=$PATH:$JAVA_HOME/bin


source /etc/profile
wget https://download.sonatype.com/nexus/3/nexus-3.23.0-03-unix.tar.gz
wget https://download.sonatype.com/nexus/3/nexus-3.44.0-01-unix.tar.gz
groupadd nexus;
useradd nexus -d /home/nexus -s /bin/sh -g nexus;
chown -R nexus:nexus /opt/nexus-3.23.0-03;
chmod -R 750 /opt/nexus-3.23.0-03/;
chown -R nexus:nexus /opt/sonatype-work/;
chmod -R 750 /opt/sonatype-work/;

grep -i "INSTALL4J_JAVA_HOME_OVERRIDE" /opt/nexus-3.23.0-03/bin/nexus
cp /opt/nexus-3.23.0-03/bin/nexus /opt/nexus-3.23.0-03/bin/nexus.bak

sed -i "s%# INSTALL4J_JAVA_HOME_OVERRIDE=%INSTALL4J_JAVA_HOME_OVERRIDE=/usr/local/java/jdk1.8.0_201%g" /opt/nexus-3.23.0-03/bin/nexus

vi /etc/systemd/system/nexus.service

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

systemctl daemon-reload
systemctl enable nexus.service
systemctl start nexus.service



