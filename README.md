# Prerequisites 
# git

## Install and configure salt-master
## =================================

## Check first:
## https://repo.saltstack.com/#bootstrap

## Installation

## BOOTSTRAP - MULTI-PALTFORM

# Install using wget

# Using wget to install your distribution's stable packages:
```
wget -O bootstrap-salt.sh https://bootstrap.saltstack.com
sudo sh bootstrap-salt.sh
```
# Installing a specific version from git using wget:
```
wget -O bootstrap-salt.sh https://bootstrap.saltstack.com
sudo sh bootstrap-salt.sh -P git v2016.11.5
```
# Install using curl
# On the Salt master

# Run these commands on the system that you want to use as the central management point.

curl -L https://bootstrap.saltstack.com -o install_salt.sh
sudo sh install_salt.sh -P -M

# Your Salt master can manage itself, so a Salt minion is installed along with the Salt master. If you do not want to install the minion, also pass the -N option.
# On each Salt minion

# Run these commands on each system that you want to manage using Salt.

curl -L https://bootstrap.saltstack.com -o install_salt.sh
sudo sh install_salt.sh -P

# Fedora
# Packages are available in the standard Fedora repositories. Install the salt-minion, salt-master, or other Salt components:

sudo dnf install -y salt-master \
salt-minion \
salt-ssh \
salt-syndic \
salt-cloud \
salt-api

# Redhat / CentOS 7 

# Installs the latest release. Updating installs the latest release even if it is a new major version.

# Run the following commands to install the SaltStack repository and key:

sudo yum install https://repo.saltstack.com/yum/redhat/salt-repo-latest.el7.noarch.rpm 

# Run 
sudo yum clean expire-cache

# Install the salt-minion, salt-master, or other Salt components:
sudo yum install salt-master \
salt-minion \
salt-ssh \
salt-syndic \
salt-cloud \
salt-api

# (Upgrade only) Restart all upgraded services, for example:
sudo systemctl restart salt-minion

# Ubuntu
# Ubuntu 18 (bionic)

# Installs the latest release. Updating installs the latest release even if it is a new major version.

# Run the following command to import the SaltStack repository key:

wget -O - https://repo.saltstack.com/apt/ubuntu/18.04/amd64/latest/SALTSTACK-GPG-KEY.pub | sudo apt-key add -

# Save the following line to /etc/apt/sources.list.d/saltstack.list:

sudo echo "deb http://repo.saltstack.com/apt/ubuntu/18.04/amd64/latest bionic main" >> /etc/apt/sources.list.d/saltstack.list

# Run 
sudo apt-get update

# Install the salt-minion, salt-master, or other Salt components:
sudo apt-get install -y salt-master \
salt-minion \
salt-ssh \
salt-syndic \
salt-cloud \
salt-api

# (Upgrade only) Restart all upgraded services, for example:
sudo systemctl restart salt-minion

# Other distros
https://docs.saltstack.com/en/latest/topics/installation/index.html#platform-specific-installation-instructions



Configuring Salt
=================================

Salt configuration is very simple. The default configuration for the master will work for most installations and the only requirement for setting up a minion is to set the location of the master in the minion configuration file.

The configuration files will be installed to /etc/salt and are named after the respective components, /etc/salt/master, and /etc/salt/minion.
Master Configuration

By default the Salt master listens on ports 4505 and 4506 on all interfaces (0.0.0.0). To bind Salt to a specific IP, redefine the "interface" directive in the master configuration file, typically /etc/salt/master, as follows:

- #interface: 0.0.0.0
+ interface: 10.0.0.1

After updating the configuration file, restart the Salt master. See the master configuration reference for more details about other configurable options.
Minion Configuration

Although there are many Salt Minion configuration options, configuring a Salt Minion is very simple. By default a Salt Minion will try to connect to the DNS name "salt"; if the Minion is able to resolve that name correctly, no configuration is needed.

If the DNS name "salt" does not resolve to point to the correct location of the Master, redefine the "master" directive in the minion configuration file, typically /etc/salt/minion, as follows:

- #master: salt
+ master: 10.0.0.1

After updating the configuration file, restart the Salt minion. See the minion configuration reference for more details about other configurable options.


Having trouble?

The simplest way to troubleshoot Salt is to run the master and minion in the foreground with log level set to debug:

salt-master --log-level=debug

For information on salt's logging system please see the logging document.

Run as an unprivileged (non-root) user

To run Salt as another user, set the user parameter in the master config file.

Additionally, ownership, and permissions need to be set such that the desired user can read from and write to the following directories (and their subdirectories, where applicable):

    /etc/salt
    /var/cache/salt
    /var/log/salt
    /var/run/salt

More information about running salt as a non-privileged user can be found here.

There is also a full troubleshooting guide available.
Key Identity

Salt provides commands to validate the identity of your Salt master and Salt minions before the initial key exchange. Validating key identity helps avoid inadvertently connecting to the wrong Salt master, and helps prevent a potential MiTM attack when establishing the initial connection.
Master Key Fingerprint

Print the master key fingerprint by running the following command on the Salt master:

salt-key -F master

Copy the master.pub fingerprint from the Local Keys section, and then set this value as the master_finger in the minion configuration file. Save the configuration file and then restart the Salt minion.
Minion Key Fingerprint

Run the following command on each Salt minion to view the minion key fingerprint:

salt-call --local key.finger

Compare this value to the value that is displayed when you run the salt-key --finger <MINION_ID> command on the Salt master.
Key Management

Salt uses AES encryption for all communication between the Master and the Minion. This ensures that the commands sent to the Minions cannot be tampered with, and that communication between Master and Minion is authenticated through trusted, accepted keys.

Before commands can be sent to a Minion, its key must be accepted on the Master. Run the salt-key command to list the keys known to the Salt Master:

[root@master ~]# salt-key -L
Unaccepted Keys:
alpha
bravo
charlie
delta
Accepted Keys:

This example shows that the Salt Master is aware of four Minions, but none of the keys has been accepted. To accept the keys and allow the Minions to be controlled by the Master, again use the salt-key command:

[root@master ~]# salt-key -A
[root@master ~]# salt-key -L
Unaccepted Keys:
Accepted Keys:
alpha
bravo
charlie
delta

The salt-key command allows for signing keys individually or in bulk. The example above, using -A bulk-accepts all pending keys. To accept keys individually use the lowercase of the same option, -a keyname.

See also

salt-key manpage
Sending Commands

Communication between the Master and a Minion may be verified by running the test.ping command:

[root@master ~]# salt alpha test.ping
alpha:
    True

Communication between the Master and all Minions may be tested in a similar way:

[root@master ~]# salt '*' test.ping
alpha:
    True
bravo:
    True
charlie:
    True
delta:
    True

Each of the Minions should send a True response as shown above.




https://docs.saltstack.com/en/latest/ref/configuration/nonroot.html