# ycsb
YCS BENCHMARKING

# Install python2 and make it default
assuming python2 is installed

below steps to make python2 default
ls /usr/bin/python*
sudo update-alternatives --list python
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 2
sudo update-alternatives --list python
sudo update-alternatives --config python
now enter the selection number of python 2 to make it default


# Install Java (not clearly verified - hope this is done in the class)
sudo apt install default-jdk
javac -version

# Install Maven (not clearly verified - hope this is done in the class)
sudo apt install maven
mvn -version

configure MAV environments

sudo vim /etc/profile.d/maven.sh

insert the below data
export JAVA_HOME=/usr/lib/jvm/default-java
export M2_HOME=/opt/maven
export MAVEN_HOME=/opt/maven
export PATH=${M2_HOME}/bin:${PATH}

make the file executable
sudo chmod +x /etc/profile.d/maven.sh

activate the variables
source /etc/profile.d/maven.sh

# Download and extract YCSB
Download
curl -O --location https://github.com/brianfrankcooper/YCSB/releases/download/0.17.0/ycsb-0.17.0.tar.gz

Extract
tar xfvz ycsb-0.17.0.tar.gz

change directory to ycsb configs just downloaded 
cd ycsb-0.17.0

# Redis Downalod and run
sudo apt install lsb-release

sudo snap install redis

check if its working
redis-cli ping

this should respod PONG - then its wroking


# Now run the YCSB workload
LOAD THE DATA
./bin/ycsb load redis -s -P workloads/workloada -p custom_load.dat -p "redis.host=127.0.0.1" -p "redis.port=6379" > radis_load_out.dat

RUN THE DATA
./bin/ycsb run redis -s -P workloads/workloada -p custom_load.dat -p "redis.host=127.0.0.1" -p "redis.port=6379" > radis_run_out.dat