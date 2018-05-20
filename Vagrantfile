$script = <<SCRIPT

echo *******************************
echo *
echo * Start Server Provisioning...
echo *
echo *******************************
sudo su -
yum -y install git vim zip unzip wget telnet

echo -----------------------
echo - Docker CE Setup
echo -----------------------
# Install yum-utils if necessary
yum -y install yum-utils device-mapper-persistent-data lvm2

# Add the Docker repository
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# Install Docker CS Engine
yum -y install docker-ce

groupadd docker
usermod -aG docker vagrant
systemctl enable docker
service docker start
sleep 3
echo '{
  "insecure-registries": ["docker.nhp.dt.local:2022"]
}' > /etc/docker/daemon.json
service docker restart

echo -----------------------
echo - Docker Compose Setup
echo -----------------------
curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

echo -----------------------
echo - Terraform Setup
echo -----------------------
if [[ ! -f /usr/bin/terraform ]]; then
  wget https://releases.hashicorp.com/terraform/0.11.5/terraform_0.11.7_linux_amd64.zip
  unzip *.zip
  rm -f *.zip
  mv -f terraform /usr/bin/
fi
terraform --version

yum -y update

echo *************************
echo *
echo * Provisioning Completed
echo *
echo *************************
echo .
echo .
echo .
echo ------------------------------------------
echo - Type "vagrant ssh" to login into the VM
echo ------------------------------------------

SCRIPT

# Vagrant configuration
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.hostname = "docker-box"
  config.vm.network "forwarded_port", guest: 22, host: 2221
  config.vm.network "forwarded_port", guest: 4440, host: 4440
  config.vm.network "forwarded_port", guest: 4441, host: 4441
  config.vm.network "forwarded_port", guest: 4442, host: 4442
  config.vm.network "forwarded_port", guest: 2376, host: 2376
  config.vm.synced_folder ".", "/vagrant_data", type: "virtualbox", disabled: true

  config.vm.provider "virtualbox" do |vb|
    vb.customize ["guestproperty", "set", :id, "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold", 10000]
    vb.memory = "1024"
  end

  # Enable provisioning with a shell script.
  config.vm.provision "shell", inline: $script
end

