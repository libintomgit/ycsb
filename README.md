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


# Now run the YCSB workload on Redis DB
LOAD THE DATA
./bin/ycsb load redis -s -P workloads/workloada -p readcount=1000000 -p operationcount=1000000 -p "redis.host=127.0.0.1" -p "redis.port=6379" > radis_load_out.dat

RUN THE DATA
./bin/ycsb run redis -s -P workloads/workloada -p readcount=1000000 -p operationcount=1000000 -p "redis.host=127.0.0.1" -p "redis.port=6379" > radis_run_out.dat

# Now download and run mongo db https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/
sudo apt-get install gnupg

wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -

check your release
lsb_release -dc

below change focal to your distribution name

echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list

sudo apt-get update

sudo apt-get install -y mongodb-org

Change the bindign in the configuration file (bindIp: 0.0.0.0)

sudo nano /etc/mongod.conf

sudo systemctl start mongod

sudo systemctl status mongod

mongo

> show dbs
admin   0.000GB
config  0.000GB
local   0.000GB

# Now run the YCSB workload on Mongo DB
LOAD THE DATA - ASYNC

./bin/ycsb load mongodb-async -s -P workloads/workloada -p readcount=1000000 -p operationcount=1000000 -p mongodb.url=mongodb://localhost:27017/ycsb?w=0 > ./mongo_load_out.dat

RUN THE OPERATIONS - ASYNC
./bin/ycsb run mongodb-async -s -P workloads/workloada -p readcount=1000000 -p operationcount=1000000 -p mongodb.url=mongodb://localhost:27017/ycsb?w=0 > ./mongo_run_out.dat

LOAD THE DATA - SYNC

./bin/ycsb load mongodb -s -P workloads/workloada -p readcount=1000000 -p operationcount=1000000 -p mongodb.url=mongodb://localhost:27017/ycsb?w=0 > ./mongo_load_out.dat

RUN THE OPERATIONS - SYNC

./bin/ycsb run mongodb -s -P workloads/workloada -p readcount=1000000 -p operationcount=1000000 -p mongodb.url=mongodb://localhost:27017/ycsb?w=0 > ./mongo_run_out.dat
