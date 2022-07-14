# JAVA 11 INSTALLATION IN UBUNTU
$ sudo apt update
$ sudo apt install openjdk-11-jdk -y
$java --version 

# JENKINS INSTALLATION IN UBUNTU
$ sudo wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
$ sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
$ sudo apt update
$ sudo apt install jenkins
$ sudo systemctl start jenkins
$ sudo systemctl status jenkins

