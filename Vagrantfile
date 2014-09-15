# -*- mode: ruby -*-
# vi: set ft=ruby :

$script = <<SCRIPT
ln -s /dev/null /etc/udev/rules.d/80-net-setup-link.rules

sudo pacman -Syu --noconfirm

sudo pacman -S --noconfirm nfs-utils

sudo pacman -S --noconfirm --needed postgresql python2-virtualenv

sudo -u vagrant virtualenv2 venv
#echo "source ~vagrant/venv/bin/activate" >> ~vagrant/.profile

cd /srv/samba
sudo -u vagrant PATH=~vagrant/venv/bin/:$PATH ./buildtools/bin/waf configure --enable-developer
sudo -u vagrant PATH=~vagrant/venv/bin/:$PATH ./buildtools/bin/waf -j4
sudo PATH=~vagrant/venv/bin/:$PATH ./buildtools/bin/waf install
/usr/local/samba/bin/samba-tool domain provision --realm=vagrant.lan --domain=VAGRANT --server-role=dc --use-rfc2307 --adminpass=Password1
SCRIPT

VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "archlinux-x86_64"
  config.vm.box_url = "http://cloud.terry.im/vagrant/archlinux-x86_64.box"
  config.vm.provision "shell", inline: $script
  config.vm.network "private_network", type: "dhcp"
  config.vm.synced_folder "../samba", "/srv/samba"#, type: "rsync"
  config.vm.synced_folder "./pkgbuild", "/srv/pkgbuild", type: "rsync"
  config.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 4
  end
end
