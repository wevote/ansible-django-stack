# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/xenial64"

  config.vm.define "wevoteapi.local", primary: true do |app|
    app.vm.hostname = "wevoteapi"

    # app.vm.network "private_network", type: "dhcp"
  end

  config.vm.network :forwarded_port, guest: 80, host: 8888
  config.vm.network :forwarded_port, guest: 443, host: 8889

  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--name", "WeVoteAPI", "--memory", "2048"]
  end

  config.vm.provider "docker" do |d, override|
    override.vm.box = nil

    d.name = "wevoteapi"
    d.build_dir = "docker"
    d.create_args = ["--publish-all", "--security-opt=seccomp:unconfined",
                     "--tmpfs=/run", "--tmpfs=/run/lock", "--tmpfs=/tmp",
                     "--volume=/sys/fs/cgroup:/sys/fs/cgroup:ro"]
    d.has_ssh = true
  end

  # For local development, uncommenting and editing the line below will enable
  # a folder in the host machine containing your local git repo to be synced to
  # the guest machine. Ensure the Ansible playbook variable "setup_git_repo" is
  # set to "no" (in env_vars/vagrant.yml) when enabling this.
  config.vm.synced_folder "../WeVoteServer", "/webapps/wevoteserver/WeVoteServer"

  # Ansible provisioner.
  #
  # NOTE: To access sensitive variables, you must
  #   1. Store your Ansible Vault password in `.ansible_vault_pass` in this dir
  #   2. Uncomment the `vault_password_file` line below
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "vagrant.yml"
    ansible.host_key_checking = false
    ansible.verbose = "vv"
    # ansible.vault_password_file = "./.ansible_vault_pass"
  end
end
