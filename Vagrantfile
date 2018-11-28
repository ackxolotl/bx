# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
	# The most common configuration options are documented and commented below.
	# For a complete reference, please see the online documentation at
	# https://docs.vagrantup.com.

	# Every Vagrant development environment requires a box. You can search for
	# boxes at https://vagrantcloud.com/search.
	config.vm.box = "debian/stretch64"
	config.vm.box_version = "9.5.0"

	# Disable automatic box update checking. If you disable this, then
	# boxes will only be checked for updates when the user runs
	# `vagrant box outdated`. This is not recommended.
	# config.vm.box_check_update = false

	# Create a forwarded port mapping which allows access to a specific port
	# within the machine from a port on the host machine. In the example below,
	# accessing "localhost:8080" will access port 80 on the guest machine.
	# NOTE: This will enable public access to the opened port
	# config.vm.network "forwarded_port", guest: 80, host: 8080

	# Create a forwarded port mapping which allows access to a specific port
	# within the machine from a port on the host machine and only allow access
	# via 127.0.0.1 to disable public access
	# config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

	# Create a private network, which allows host-only access to the machine
	# using a specific IP.
	# config.vm.network "private_network", ip: "192.168.33.10"

	# Create a public network, which generally matched to bridged network.
	# Bridged networks make the machine appear as another physical device on
	# your network.
	# config.vm.network "public_network"

	# Share an additional folder to the guest VM. The first argument is
	# the path on the host to the actual folder. The second argument is
	# the path on the guest to mount the folder. And the optional third
	# argument is a set of non-required options.
	# config.vm.synced_folder "./", "/home/vagrant", type: "rsync"

	# Provider-specific configuration so you can fine-tune various
	# backing providers for Vagrant. These expose provider-specific options.
	# Example for VirtualBox:
	#
	# config.vm.provider "virtualbox" do |vb|
	#   # Display the VirtualBox GUI when booting the machine
	#   vb.gui = true
	#
	#   # Customize the amount of memory on the VM:
	#   vb.memory = "1024"
	# end
	#
	# View the documentation for the provider you are using for more
	# information on available options.

	config.vm.provision "file", source: "get_flag", destination: "/tmp/get_flag"

	# Enable provisioning with a shell script. Additional provisioners such as
	# Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
	# documentation for more information about their specific syntax and use.
	config.vm.provision "shell", inline: <<-SHELL
		HOME=$PWD

		apt-get update
		DEBIAN_FRONTEND=noninteractive apt-get -y -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold" dist-upgrade

		apt-get install -y vim gdb git python3 python3-dev python3-pip rubygems strace

		wget http://security.debian.org/debian-security/pool/updates/main/o/openssl/libssl1.0.0_1.0.1t-1+deb8u9_amd64.deb -O /tmp/libssl1.0.0.deb
		dpkg --install /tmp/libssl1.0.0.deb

		git clone https://github.com/pwndbg/pwndbg $HOME/.pwndbg
		$HOME/.pwndbg/setup.sh
		pip3 install -r $HOME/.pwndbg/requirements.txt
		echo "source ~/.pwndbg/gdbinit.py" > $HOME/.gdbinit

		pip3 install --upgrade git+https://github.com/arthaud/python3-pwntools.git
		pip3 install --upgrade git+https://github.com/sashs/Ropper.git
		gem install one_gadget

		cat <<-EOF | gcc -o get_flag -xc -
		#include <stdio.h>

		int main()
		{
			FILE* fp;
			long unsigned int flag[0x2];
			fp = fopen("/dev/urandom", "r");
			fread(flag, sizeof(flag), 1, fp);
			fclose(fp);

			printf("flag_%016lx%016lx\\n", flag[0], flag[1]);
		}
		EOF

		mv $HOME/get_flag /bin/get_flag
		chmod +x /bin/get_flag

		ln -s /vagrant $HOME/bx
		chown -R -h vagrant:vagrant bx .gdbinit .pwndbg/

		reboot
	    SHELL
end
