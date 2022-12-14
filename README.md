# YCSB
YCS BENCHMARKING - [Original Referance - brianfrankcooper YCSB](https://github.com/brianfrankcooper/YCSB)

# Install python2 and make it default
- Update - Advanced Package Tool (APT) library (a package management system for Linux distributions)
```sh
sudo apt update
```
- Install Python2
```sh
sudo apt install python2

- Make python2 default
```sh
ls /usr/bin/python*
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 2
sudo update-alternatives --list python
sudo update-alternatives --config python

## now enter the selection number of python 2 to make it default
```

# Install Java
- Update the apt-get
```sh
sudo apt-get update
```
- Install Java
```sh
sudo apt install -y default-jdk
```
- Check java version
```sh
javac -version
```
# Install Maven (not clearly verified - hope this is done in the class)

- Install maven
```sh
sudo apt install maven
```
- Confirm the installation by checking the version
```sh
mvn -version
```
- configure MAVEN environments
```sh
sudo vim /etc/profile.d/maven.sh

##insert the below data
export JAVA_HOME=/usr/lib/jvm/default-java
export M2_HOME=/opt/maven
export MAVEN_HOME=/opt/maven
export PATH=${M2_HOME}/bin:${PATH}
```
- make the file executable
```sh
sudo chmod +x /etc/profile.d/maven.sh
```
- activate the variables
```sh
source /etc/profile.d/maven.sh
```

# Download and extract YCSB
- Download
```sh
curl -O --location https://github.com/brianfrankcooper/YCSB/releases/download/0.17.0/ycsb-0.17.0.tar.gz
```
- Extract
```sh
tar xfvz ycsb-0.17.0.tar.gz
```
- change directory to ycsb configs just downloaded 
```sh
cd ycsb-0.17.0
```
# Downalod and Install [Radis DB - Original installation guide](https://redis.io/docs/getting-started/installation/install-redis-on-linux/)
- Install the LSB Linux Standard Base
```sh
sudo apt install lsb-release
```
- Now install REDIS using SNAP (Reffer the above link for guide from Radis)
```sh
sudo snap install redis
```
- check if its working
```sh
redis-cli ping

### this should respod PONG - then its wroking
> ubuntu@ip-172-31-42-28:~/ycsb_assignment/actual-db-output/ycsb$ redis-cli ping
> PONG
```
- If not working do this
```sh
curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list

sudo apt-get update

sudo apt-get install redis
```
- Clread data in redis memory using the below command
```sh
redis-cli FLUSHALL

## It should return OK
```
- Then check if the system memory is clreared
```sh
free -m
```

# Now run the YCSB workload on Redis DB
- LOAD THE DATA
```sh
./bin/ycsb load redis -s -P workloads/workloada -p readcount=1000000 -p operationcount=1000000 -p "redis.host=127.0.0.1" -p "redis.port=6379" > radis_load_out.dat
## if this is not changing the operationcount, then hardcode the value (1000000) in the workloads/workloada file, then remove the parameter from the command.

### This runs 1M operations of 1M readcount and stores the output to radis_load_out.dat
```
- RUN THE DATA
```sh
./bin/ycsb run redis -s -P workloads/workloada -p readcount=1000000 -p operationcount=1000000 -p "redis.host=127.0.0.1" -p "redis.port=6379" > radis_run_out.dat

## if this is not changing the operationcount, then hardcode the value (1000000) in the workloads/workloada file, then remove the parameter from the command.

### This runs 1M operations of 1M readcount and stores the output to radis_run_out.dat
```
- Belwo is the example of the output
> This out out can be used to compare with
> other databases

# Download and run [mongo db - Original installation guide](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/)

- Install gnupg [GNU Privacy Guard]: https://gnupg.org/ 
```sh
sudo apt-get install gnupg
```
- Insall libssl1.1
```sh
echo "deb http://security.ubuntu.com/ubuntu focal-security main" | sudo tee /etc/apt/sources.list.d/focal-security.list

sudo apt-get update

```
- GPG
```sh
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -
```
- Add 
```sh
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
```
```sh
sudo apt-get update
```
- Now Install the Mongo DB
```sh
sudo apt-get install -y mongodb-org
```
- Change the bindign in the configuration file (bindIp: 0.0.0.0)
```sh
sudo nano /etc/mongod.conf

## Update the line with bindIP to bindIp: 0.0.0.0
```
- Start the mongo deamon process
```sh
sudo systemctl start mongod
```
- Check the status of the service
```sh
sudo systemctl status mongod
```
- Use the mongo db directly from the terminal
```sh
## Enter mongo in the terminal and it should open the mogo shell

mongo

## In the mongo shell 
>show dbs

## Will list all the dbs in the db
admin   0.000GB
config  0.000GB
local   0.000GB

## Select db
>use db_name

## Delete DB
### Within the selected DB enter
> db.dropDatabase()
```
# Run the YCSB workload on Mongo DB
### LOAD THE DATA - ASYNC

- This command will LOAD the records with Asynchronos
```sh
./bin/ycsb load mongodb-async -s -P workloads/workloada -p recordcount=1000000 -p operationcount=1000000 -p mongodb.url=mongodb://localhost:27017/ycsb?w=0 > ./mongo_load_out.dat

## if this is not changing the operationcount, then hardcode the value (1000000) in the workloads/workloada file, then remove the parameter from the command.
```

### RUN THE OPERATIONS - ASYNC
- This command will RUN the records with Asynchronos
```sh
./bin/ycsb run mongodb-async -s -P workloads/workloada -p recordcount=1000000 -p operationcount=1000000 -p mongodb.url=mongodb://localhost:27017/ycsb?w=0 > ./mongo_run_out.dat

## if this is not changing the operationcount, then hardcode the value (1000000) in the workloads/workloada file, then remove the parameter from the command.
```

### LOAD THE DATA - SYNC
- This command will LOAD the records with Synchronos
```sh
./bin/ycsb load mongodb -s -P workloads/workloada -p recordcount=1000000 -p operationcount=1000000 -p mongodb.url=mongodb://localhost:27017/ycsb?w=0 > ./mongo_load_out.dat

## if this is not changing the operationcount, then hardcode the value (1000000) in the workloads/workloada file, then remove the parameter from the command.
```
### RUN THE OPERATIONS - SYNC
- This command will RUN the records with Synchronos
```SH
./bin/ycsb run mongodb -s -P workloads/workloada -p recordcount=1000000 -p operationcount=1000000 -p mongodb.url=mongodb://localhost:27017/ycsb?w=0 > ./mongo_run_out.dat

## if this is not changing the operationcount, then hardcode the value (1000000) in the workloads/workloada file, then remove the parameter from the command.
```
# Install MEMCACHED
- Install memcachd
```sh
sudo apt-get install memcached
```
# Run the YCSB workload on MEMCACHED
- LOAD
```sh
./bin/ycsb load memcached -s -P workloads/workloada -p recordcount=10000 -p operationcount=10000 -p "memcached.hosts=127.0.0.1" > outputLoad.txt
```
- RUN
```sh
./bin/ycsb run memcached -s -P workloads/workloada -p recordcount=10000 -p operationcount=10000 -p "memcached.hosts=127.0.0.1" > outputRUN.txt
```