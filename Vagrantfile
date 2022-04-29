Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 40080
  # config.vm.network "forwarded_port", guest: 5432, host: 45432
  # config.vm.network "forwarded_port", guest: 30000, host: 30000

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allohisto w access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.56.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "./apps", "/vagrant_data/apps"
  config.vm.synced_folder "./k8s-deployments", "/vagrant_data/k8s"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
    vb.memory = "4096"
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # execute code before reload
  config.vm.provision "shell", reboot: true, inline: <<-SHELL
    apt update
    # install docker
    apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    apt install -y docker-ce docker-ce-cli containerd.io
    usermod -aG docker vagrant
    # install k8s
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
    echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" >> ~/kubernetes.list
    mv ~/kubernetes.list /etc/apt/sources.list.d
    apt update
    apt install -y kubeadm kubelet kubectl kubernetes-cni
    swapoff -a
  SHELL

  # execute code after reload
  config.vm.provision "shell", inline: <<-SHELL
    # install kubernetes
    kubeadm init
    sleep 25
    mkdir -p $HOME/.kube
    cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    chown $(id -u):$(id -g) $HOME/.kube/config
    mkdir -p /home/vagrant/.kube
    cp -i /etc/kubernetes/admin.conf /home/vagrant/.kube/config
    chown -R vagrant:vagrant /home/vagrant
    # install k8s tools
    git clone https://github.com/ahmetb/kubectx /usr/local/kubectx
    ln -s /usr/local/kubectx/kubectx /usr/local/bin/kubectx
    ln -s /usr/local/kubectx/kubens /usr/local/bin/kubens
    source <(kubectl completion bash) # setup autocomplete in bash into the current shell, bash-completion package should be installed first.
    echo "source <(kubectl completion bash)" >> /vagrant/.bashrc # add autocomplete permanently to your bash shell.
    echo "alias k=kubectl" >> /home/vagrant/.bash_aliases
    runuser -l vagrant -c "complete -F __start_kubectl k"
    kubectl taint node --all node-role.kubernetes.io/master:NoSchedule- # allow to run pods on master
    # install int k8s network coponents
    kubectl apply -f https://projectcalico.docs.tigera.io/manifests/calico.yaml
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.2/deploy/static/provider/cloud/deploy.yaml
  
    chown -R vagrant:vagrant /home/vagrant
  SHELL
end
