**Pre-Installation Steps
Download Virtual Box from "https://www.virtualbox.org/"

Download Putty Client from "http://www.putty.org/"

Download Filezilla from "https://filezilla-project.org/"

Download "Bitnami Lamp Stack Virtual Box Image file "bitnami-lampstack-7.1.12-0-r03-linux-debian-8-x86_64.ova" from https://bitnami.com/stacks

Import Lamp Image in Virtual Box.

Start the VM.

**Step1- Enable SSH
# Enable SSH 

# Debian Linux
sudo rm -f /etc/ssh/sshd_not_to_be_run
sudo systemctl enable ssh
sudo systemctl start ssh

# Configure The SSH Server To Support Key-Based Authentication
ssh-keygen

# This command should create two files named id_rsa and id_rsa.pub in the /home/bitnami/.ssh directory.
# Copy the private key file named id_rsa to a secure location. Do not share this private key file.

**Step2 - Http-Https Settings
## Give Write Permission
ls
cd stack
ls
## apache2 directory would be present there.
chmod -R 777 /apache2

## HTTP Port
# Under the default configuration, Apache will wait for requests on port 80. Change that by editing the httpd.conf file and modifying, 
# the value specified in the Port directive. For example:

Listen 8080

ServerName localhost:8080

## Also change the port in installdir/apache2/conf/bitnami/bitnami.conf in the VirtualHost directive:

<VirtualHost _default_:8080>
Restart the Apache server for the change to take effect.

## HTTPS Port
# Apache waits for HTTPS requests on port 443. Change that by editing the installdir/apache2/conf/bitnami/bitnami.conf file and modifying the value specified in the Port directive. For example:

Listen 8443 

<VirtualHost _default_:8443>
Restart the Apache server for the change to take effect.

sudo /opt/bitnami/ctlscript.sh restart apache


**Step3 - Docker Installation Debian 8
# Debian 8 Install Docker

# Remove existing installation.
# If you already have a different version of docker installed, you can run this to remove it.

sudo apt-get purge lxc-docker*
sudo apt-get purge docker.io*

# Update
sudo apt-get update
sudo apt-get dist-upgrade -y

# docker requires apt-transport-https
sudo apt-get install apt-transport-https -y

# now setup the apt repository
sudo apt-key adv --keyserver hkp://pgp.mit.edu:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
sudo echo "deb https://apt.dockerproject.org/repo debian-jessie main" | sudo tee /etc/apt/sources.list.d/docker.list

# Finally install the package.
sudo apt-get update
sudo apt-get install docker-engine -y

# Start the docker service
sudo service docker start

# Add ourselves to the docker group so that we 
# can use 'docker' commands without sudo.
sudo adduser $USER docker

**Step4 - Port Settings
# Open Port Range from 32768 to 65535
sudo /sbin/iptables -A INPUT -p tcp --match multiport --dports 32768:65535 -j ACCEPT

# Open Port 23750
sudo iptables -A INPUT -p tcp --dport 23750 --jump ACCEPT

# Save
iptables-save

# Codevny Docker Start
docker run -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /home/bitnami/htdocs/codenvy:/data codenvy/cli start

