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
  config.vm.box = "bento/ubuntu-16.04"
  # config.vm.box = "box-cutter/ubuntu1604-desktop"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 11311, host: 11311
  # config.vm.network "forwarded_port", guest: 37918, host: 37918

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
  config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "../", "/ouxt"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = true

    # Customize the amount of memory on the VM:
    vb.memory = "4096"

    # virtualboxカスタマイズ
    # https://qiita.com/hidekuro/items/385bcc4b9eb43945751d
    vb.customize [
      "modifyvm", :id,
      "--cpus", "2",
      "--ioapic", "on",
      "--vram", "256",
      "--accelerate3d", "on",  # 3dアクセラレーション
      "--hwvirtex", "on", # HW仮想化
      "--nestedpaging", "on",
      "--largepages", "on",
      "--ioapic", "on",
      "--clipboard", "bidirectional", # クリップボードの共有
      "--draganddrop", "bidirectional" # ドラッグアンドドロップ可能に
    ]
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.

  # basic develop and desktop env
  # reference: https://stackoverflow.com/questions/18878117/using-vagrant-to-run-virtual-machines-with-desktop-environment
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y vim xfce4 virtualbox-guest-dkms virtualbox-guest-utils virtualbox-guest-x11
    # sed -i 's/allowed_users=.*$/allowed_users=anybody/' /etc/X11/Xwrapper.config
    VBoxClient --clipboard
    VBoxClient --draganddrop
    VBoxClient --display
    VBoxClient --checkhostversion
    VBoxClient --seamless
    # dpkg-reconfigure keyboard-configuration  # keybord layout, require interactive session
  SHELL

  # ROS Kinetic for ubuntu 16.04 xenial
  # ros setup with root
  # ref: http://wiki.ros.org/kinetic/Installation/Ubuntu
  # 
  # additional dependency: ignition -> https://github.com/PX4/Firmware/issues/5773#issuecomment-264179990
  config.vm.provision "shell", inline: <<-SHELL
    sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
    apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 421C365BD9FF1F717815A3895523BAEEB01FA116
    apt-get update
    apt-get install -y ros-kinetic-desktop-full python-rosinstall python-rosinstall-generator python-wstool build-essential libignition-math2-dev
    rosdep init
  SHELL
  # ros user setup
  config.vm.provision "shell", :privileged => false, inline: <<-SHELL
    rosdep update
    echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
    echo "export DISPLAY=:0.0" >> ~/.bashrc
    source ~/.bashrc
  SHELL

  # Gazebo 7.0 for ros kinetic
  # gazebo setup
  # http://gazebosim.org/tutorials?cat=install&tut=install_ubuntu&ver=7.0
  config.vm.provision "shell", inline: <<-SHELL
    sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-stable.list'
    wget http://packages.osrfoundation.org/gazebo.key -O - | apt-key add -
    apt-get update
    apt-get install -y gazebo7 libgazebo7-dev
  SHELL

  # always
  config.vm.provision "shell", run: "always", inline: <<-SHELL
    cd /ouxt/catkin_ws
    rosdep install -i -r -y --from-paths src --rosdistro kinetic
    echo hoge
  SHELL
end
