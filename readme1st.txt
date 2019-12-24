Installing OS 
-Downloading image
1. Visit Downloading OS page (For Ubuntu is https://ubuntu.com/download/desktop)
2. Choose OS version and download .iso image-
-Installing Ubuntu on VM
1. Download Virtual Box from official site https://www.virtualbox.org/wiki/Downloads
2. Install VB 
3. Launch VB 
4. Create new Virtual Machine: Machine -> CReate -> (Set Vm name, Folder, Type OS, Version OS) -> Next -> (Set RAM volume) -> Next -> (Set disk type and disk volume) -> Create
5. Launch created VM
FOR UBUNTU:
1.Choose instalation language
2.Set Keyboard Layout
3.Set Type of Instalation "Normal Instalation" and press NEXT
4.Click the desktop shortcut “Install Ubuntu” to bring up the Ubuntu installation wizard.
Follow the wizard until it asks you to choose where to install Ubuntu. Select created space.
6.When everything’s done, click Install Now and confirm to format the partitions (Pay attention on which partitions to be formatted).
7.Set user-name and password
8.Click continue and finish the wizard. 

Update Ubuntu to last version
1. Use this commands 
$ sudo apt update
$ sudo apt upgrade
This makes sure your Ubuntu is up to date.
2. $ sudo apt dist-upgrade
This handles changing software dependencies with new versions of packages.
3.$ sudo apt-get autoremove 
This removes dependencies from uninstalled applications. 
4. $ sudo apt install update-manager-core
5. $ sudo do-release-upgrade
This will start upgrading your system.

Installation Terraform
1. Download Terraform archive using wget 
wget https://releases.hashicorp.com/terraform/0.12.18/terraform_0.12.18_linux_amd64.zip
2. Unzip archive 
$ unzip terraform_0.12.18_linux_amd64.zip
3. Move Terraform to bin folder
sudo mv terraform /usr/local/bin/

Creating new sudo user
1. Create new user 
$ add user username
2. Add new user to sudo group
$ sudo usermod -aG sudo username

Using sudo without password
1. Use 
sudo visudo
2. Add privelege
ALL=(ALL) NOPASSWD:ALL

Installation ZSH
1. Install ZSH 
? $ sudo apt install zsh
2. Set user shell
 $ sudo usermod -s /usr/bin/zsh username

Creating Systemd unit
1. create service file /etc/systemd/system/serv_name.service
$ sudo touch /etc/systemd/system/serv_name.service
$ sudo chmod 664 /etc/systemd/system/serv_name.service
2. Open file and add settings, which allows  controll service by using systemctl
[Unit]
Description=Foo

[Service]
ExecStart=/usr/sbin/serv_name

[Install]
WantedBy=multi-user.target
3. Reload systemd 
$ sudo systemctl daemon-reload

Instalation LAMP stack
1. Create a directory where we would be creating the instance:

mkdir -p ~/Vagrant/lamp
cd  ~/Vagrant/lamp
2. Now we are required to initialize the Vagrant box. Here we are left with two options.

2.1. One, we can re-use Ubuntu 12.04 LTS (Precise Pangolin) box, which we have already downloaded in the previous tutorial. To use this, just do:

vagrant init precise32
2.2. Second option we have is to do a fresh addition of the box by downloading the Ubuntu 12.04 LTS (Precise Pangolin) Vagrant box, as:

vagrant box add precise32 http://files.vagrantup.com/precise32.box
And then do:

vagrant init precise32
Please note- #2.2 is only for those who have not downloaded the Ubuntu 12.04 LTS (Precise Pangolin) Vagrant box previously.

3. Now do:

vagrant up
This will create a VagrantFile. Edit the VagrantFile as below:

Vagrant.configure(2) do |config|
config.vm.box = "precise32"

# Mentioning the SSH Username/Password:
config.ssh.username = "vagrant"
config.ssh.password = "vagrant"
# Begin Configuring
config.vm.define "lamp" do|lamp|

lamp.vm.hostname = "lamp" # Setting up hostname
lamp.vm.network "private_network", ip: "192.168.205.10" # Setting up machine's IP Address
lamp.vm.provision :shell, path: "script.sh" # Provisioning with script.sh
end

# End Configuring
end

Please note- I have deleted the commented lines to avoid confusion.

You can download this file from here.

4. Let us now start provisioning the LAMP installation. For that, create a simple shell script named script.sh using your favorite text editor as:

#!/bin/bash

# Updating repository

sudo apt-get -y update

# Installing Apache

sudo apt-get -y install apache2

# Installing MySQL and it's dependencies, Also, setting up root password for MySQL as it will prompt to enter the password during installation

sudo debconf-set-selections <<< 'mysql-server-5.5 mysql-server/root_password password rootpass'
sudo debconf-set-selections <<< 'mysql-server-5.5 mysql-server/root_password_again password rootpass'
sudo apt-get -y install mysql-server libapache2-mod-auth-mysql php5-mysql

# Installing PHP and it's dependencies
sudo apt-get -y install php5 libapache2-mod-php5 php5-mcrypt

You can download this file from here.

5. After saving the script.sh, run:

vagrant up

It will do lot of things. You would be able to see as what it is doing on the stdout. To give you an overview, it will start with importing the precise32 base box, then it will SSH into the box, then it will set the hostname of the machine(we have specified this in the Vagrantfile), then it start updating and installing the LAMP stack.

Please note- While installing mysql-server, it will set it's root password to 'rootpass' since we have mentioned this in the script.sh file.

6. Now after the vagrant is done with installation, you are ready to go. SSH into the vagrant box as:

vagrant ssh






