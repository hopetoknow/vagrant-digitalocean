DO_BOX_URL = "https://github.com/devopsgroup-io/vagrant-digitalocean/raw/master/box/digital_ocean.box"
PRIVATE_KEY_PATH = ""
TOKEN = ""

Vagrant.configure("2") do |config|
  config.vm.define "droplet1" do |droplet|
    droplet.vm.provider :digital_ocean do |provider, override|
      override.ssh.private_key_path = PRIVATE_KEY_PATH
      override.vm.box = 'digital_ocean'
      override.vm.box_url = DO_BOX_URL
      override.nfs.functional = false
      override.vm.allowed_synced_folder_types = :rsync
      provider.ssh_key_name = 'vagrant'
      provider.token = TOKEN
      provider.image = 'ubuntu-20-04-x64'
      provider.region = 'nyc1'
      provider.size = 's-1vcpu-1gb'
      provider.backups_enabled = false
      provider.private_networking = false
      provider.ipv6 = false
      provider.monitoring = false
    end
  end

 config.vm.provision "shell",
  inline: <<-SHELL
  sudo -i
  snap install doctl
  mkdir /root/.config
  doctl auth init -t #{TOKEN}
  VOLUME_ID=$(doctl compute volume create vagrantvolume --region nyc1 --size 100GiB -o json | awk -F '[:"]' '/id/ {print $5}')
  DROPLET_ID=$(curl -s http://169.254.169.254/metadata/v1/id)
  doctl compute volume-action attach $VOLUME_ID $DROPLET_ID
  mkdir /srv/data
  mkfs.ext4 /dev/sda
  mount -o defaults,nofail,discard,noatime /dev/sda /srv/data
  SHELL

end
