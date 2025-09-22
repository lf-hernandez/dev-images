Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
  config.vm.hostname = "dev-vm"

  config.vm.provider "virtualbox" do |v|
    v.name = "dev-vm"
    v.memory = 2048
    v.cpus = 2
  end

  config.vm.provider "hyperv" do |h|
    h.vmname = "dev-vm"
    h.memory = 2048
    h.cpus = 2
    h.enable_virtualization_extensions = true
  end

  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y curl wget git build-essential unzip python3-pip

    # Install Node.js with nvm
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
    export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
    nvm install --lts

    # Install Docker Engine
    # Add Docker's official GPG key:
    apt-get update
    apt-get install ca-certificates curl
    install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    chmod a+r /etc/apt/keyrings/docker.asc

    # Add the repository to Apt sources:
    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
      $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    apt-get update
    apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

    # Install AWS CLI
    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    unzip awscliv2.zip
    ./aws/install
    rm -rf aws awscliv2.zip

    # Install awslocal
    pip3 install awscli-local

    # Install LocalStack CLI
    pip3 install localstack

    # Install ngrok
    curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null
    echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | tee /etc/apt/sources.list.d/ngrok.list
    apt-get update
    apt-get install -y ngrok

    echo "Development environment ready!"
    echo "Installed tools:"
    echo "- Node.js LTS (via nvm)"
    echo "- Docker Engine & Docker Compose"
    echo "- AWS CLI"
    echo "- awslocal"
    echo "- LocalStack"
    echo "- ngrok"
  SHELL
end
