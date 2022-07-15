## JAVA 8 installation in ubuntu 18.04
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre
set path
cat >> /etc/environment <<EOL
JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
JRE_HOME=/usr/lib/jvm/java-8-openjdk-amd64/jre
EOL
execute command - sudo update-alternatives --config java ( select which ever java version you need )

## JAVA 11 INSTALLATION IN UBUNTU
sudo apt update
sudo apt install openjdk-11-jdk -y
java --version 

## JENKINS INSTALLATION IN UBUNTU
sudo wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins

## DOCKER INSTALLATION IN UBUNTU
sudo apt-get update -y
sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common -y
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-key fingerprint 0EBFCD88
sudo apt-get update -y
sudo apt-get install docker-ce docker-ce-cli containerd.io -y 

## NEXUS INSTALLATION IN UBUNTU
apt-get install wget ( install if you dont have wget )
java -version ( make sure java is installed which should be java 8 or higher version )
wget https://download.sonatype.com/nexus/3/latest-unix.tar.gz
tar -xvf latest-unix.tar.gz
cd nexus-3.35.0-02/bin
./nexus start ( starts the nexus artifactory )
./nexus status ( by this you check the status of nexus artifactory )
To access this use http://ip_Address:8081 ( by deafault which will be running on 8081)
intial password will be present in /opt/sonatype-work/nexus3/admin.password

                              
## INSTALLING KUBEADM ON GCP MACHINE
Install kubelet, kubeadm and kubectl
sudo apt update
sudo apt -y install curl apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
                             
Then install required packages.
sudo apt update
sudo apt -y install vim git curl wget kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
                              
Confirm installation by checking the version of kubectl.
kubectl version --client && kubeadm version                              
                              
                              
#### Disable Swap
Turn off swap.

sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
sudo swapoff -a

#### Enable kernel modules and configure sysctl.
Enable kernel modules

sudo modprobe overlay
sudo modprobe br_netfilter

#### Add some settings to sysctl
sudo tee /etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

#### Reload sysctl
sudo sysctl --system                              
                              
                              
                              
                              
                              
                              
