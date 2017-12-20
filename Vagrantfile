$tools = <<SCRIPT
sudo yum -y install vim wget unzip
sudo touch /etc/profile.d/setup.sh
SCRIPT

$docker = <<SCRIPT
DOCKER_VERSION=docker-17.09.1-ce.tgz

if [ "$(ls -la | grep $DOCKER_VERSION)" ]
then
  echo "Docker already installed"
else
  wget https://download.docker.com/linux/static/stable/x86_64/$DOCKER_VERSION
  tar xvzf $DOCKER_VERSION
  chmod +x docker/docker*
  sudo mv docker/docker* /usr/bin
  rm -rf docker
fi

#Create systemd service
sudo sh -c 'echo "[Unit]
Description=Docker Application Container Engine
Documentation=http://docs.docker.io

[Service]
ExecStart=/usr/bin/dockerd \
  --host=unix:///var/run/docker.sock \
  --log-level=error 
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target" > /etc/systemd/system/docker.service'

sudo systemctl enable docker
sudo systemctl start docker

SCRIPT

$java = <<SCRIPT
JAVA_JDK=jdk-8u151-linux-x64.rpm

if [ "$(ls -la | grep $JAVA_JDK)" ]
then
  echo "$JAVA_JDK already installed"
else
  wget https://www.dropbox.com/s/ddo36ulcz7wbh2j/$JAVA_JDK
  sudo yum -y install $JAVA_JDK
  echo "export JAVA_HOME=/usr/java/jdk1.8.0_151" >> /etc/profile.d/setup.sh
  source /etc/profile.d/setup.sh
fi

SCRIPT

$androidtools = <<SCRIPT

#Creating repositories.cfg to avoid warning
SDK_TOOLS=sdk-tools-linux-3859397.zip
echo "Installing sdk tools"

cd /opt

if [ "$(ls -la | grep $SDK_TOOLS)" ]
then
  echo "$SDK_TOOLS already installed"
else
  wget https://dl.google.com/android/repository/$SDK_TOOLS
  unzip $SDK_TOOLS
fi

echo "Installing platform-tools"
echo "Accepting licenses"
yes | /opt/tools/bin/sdkmanager --licenses

/opt/tools/bin/sdkmanager "platform-tools" "platforms;android-27" "build-tools;27.0.2"






SCRIPT



Vagrant.configure("2") do |config|
  config.vm.define "android" do |android|
    android.vm.box = 'centos/7'
    android.vm.hostname = 'android'
    android.vm.network :private_network, ip: "192.168.56.2", virtualbox_hostonly: true
    android.vm.synced_folder ".", "/vagrant", disabled: true
    android.vm.provision "shell", inline: $tools
    android.vm.provision "shell", inline: $docker
    android.vm.provision "shell", inline: $java
    android.vm.provision "shell", inline: $androidtools
    android.vm.provider "virtualbox" do |vm|
    	vm.name = "android"
	vm.customize [ 'modifyvm', :id, '--memory', '2048']
    end
  end
end
