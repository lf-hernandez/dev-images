Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-20.04"
  config.vm.hostname = "dev-vm"

  config.vm.network "public_network", bridge: "Default Switch"

  config.vm.provider "hyperv" do |h|
    h.vmname = "dev-vm"
    h.memory = 2048
    h.cpus = 2
    h.enable_virtualization_extensions = true
    h.linked_clone = true
    h.vm_integration_services = {
      guest_service_interface: true
    }
  end

  config.vm.provision "shell", inline: <<-SHELL
    set -eux

    apt-get update -y
    apt-get install -y curl wget git build-essential unzip python3-pip ca-certificates lsb-release gnupg

    su - vagrant -c '
      export NVM_DIR="$HOME/.nvm"
      mkdir -p "$NVM_DIR"
      curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
      source "$NVM_DIR/nvm.sh"
      nvm install --lts
    '

    install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    chmod a+r /etc/apt/keyrings/docker.asc

    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
      https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" \
      | tee /etc/apt/sources.list.d/docker.list > /dev/null

    apt-get update -y
    apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    usermod -aG docker vagrant

    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    unzip -q awscliv2.zip
    ./aws/install
    rm -rf aws awscliv2.zip

    echo "Development environment ready!"
  SHELL
end
