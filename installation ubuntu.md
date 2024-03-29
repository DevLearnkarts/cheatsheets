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
                              
#### Install Container runtime
Installing Docker runtime:
Add repo and Install packages

sudo apt update
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install -y containerd.io docker-ce docker-ce-cli

#### Create required directories
sudo mkdir -p /etc/systemd/system/docker.service.d

#### Create daemon json config file
````                                           
sudo tee /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
````
#### Start and enable Services
sudo systemctl daemon-reload 
sudo systemctl restart docker
sudo systemctl enable docker

#### Ensure you load modules
sudo modprobe overlay
sudo modprobe br_netfilter

#### Set up required sysctl params
````
sudo tee /etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
````                                           
#### Reload sysctl
sudo sysctl --system                              
                              
#### Initialize master node
Login to the server to be used as master and make sure that the br_netfilter module is loaded:
````
lsmod | grep br_netfilter
````                                           

#### Enable kubelet service.
sudo systemctl enable kubelet

#### Initialize kubeadm
kubeadm init

#### Troubleshoot kubeadm
					   
rm /etc/containerd/config.toml
systemctl restart containerd
kubeadm init
					   
#### Configure kubectl using commands in the output:
````
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
````
#### Install network plugin on Master
In this we’ll use Calico. You can choose any other supported network plugins.

kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml                              
                              
## Helm Installation in ubuntu 
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3

chmod 700 get_helm.sh

./get_helm.sh
                                           

#### helm uninstallation
which helm ( to see which folder its installed )
rm -rf /usr/local/bin/helm                                            
                                                                                                                       
## TIME OUT 
   timeout(time: 1, unit: 'HOURS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }                 
                                           
 ## Set up insecure registry                                          
                                           
    to setup Insecure Registries. to do that we need to edit or if not present create a file /etc/docker/daemon.json in that file add details of nexus
````
{ "insecure-registries":["nexus_machine_ip:8083"] }
````                                           
once that's  done we need to execute systemctl restart docker this is to apply new changes, also we can verify whether registry is added or not by executing docker info once this is done from jenkins host you can try docker login -u nexus_username -p nexus_pass nexus_ip:8083
  
## Configure email server 
````
  post {
		always {
			mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "sudheergiri142@gmail.com";  
		}
	}
````   
	
## Creating Helm hosted repository in Nexus and Pushing the helm charts
````	
 curl -u admin:$docker_password http://nexus_machine_ip:8081/repository/helm-hosted/ --upload-file myapp-${helmversion}.tgz -v
````	

## Connecting jenkins with kubernetes cluster
	
````

	stage('connecting to k8s cluster'){
		  steps{
	            script{
	    		withCredentials([kubeconfigFile(credentialsId: 'kubernetes-config', variable: 'KUBECONFIG')]) {
			  dir ("kubernetes/"){  
				sh 'helm list'
				sh 'helm upgrade --install --set image.repository="nexus_ip:8083/springapp" --set image.tag="${VERSION}" myjavaapp myapp/ ' 
			  }
		       } 
		    }		
		  }
		}

````

## Mail Input configure 
````
input(id: "Deploy Gate", message: "Deploy ${params.project_name}?", ok: 'Deploy')
````	

## Configuring PR based trigger in Jenkins
	
Branch Specifier (blank for 'any') is : ${ghprbActualCommit}

The refspec used is: +refs/pull/${ghprbPullId}/*:refs/remotes/origin/pr/${ghprbPullId}/*
