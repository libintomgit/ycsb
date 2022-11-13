# YCSB
YCS BENCHMARKING - [Original Referance - brianfrankcooper YCSB](https://github.com/brianfrankcooper/YCSB)

# Install python2 and make it default
### Assuming python2 is installed

## Below steps to make python2 default
```sh
ls /usr/bin/python*
sudo update-alternatives --list python
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 2
sudo update-alternatives --list python
sudo update-alternatives --config python

## now enter the selection number of python 2 to make it default
```

# Install Java (not clearly verified - hope this is done in the class)
- Install Java
```sh
sudo apt install default-jdk
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

# Now run the YCSB workload on Redis DB
- LOAD THE DATA
```sh
./bin/ycsb load redis -s -P workloads/workloada -p readcount=1000000 -p operationcount=1000000 -p "redis.host=127.0.0.1" -p "redis.port=6379" > radis_load_out.dat

### This runs 1M operations of 1M readcount and stores the output to radis_load_out.dat
```
- RUN THE DATA
```sh
./bin/ycsb run redis -s -P workloads/workloada -p readcount=1000000 -p operationcount=1000000 -p "redis.host=127.0.0.1" -p "redis.port=6379" > radis_run_out.dat

### This runs 1M operations of 1M readcount and stores the output to radis_run_out.dat
```
- Belwo is the example of the output
> This out out can be used to compare with
> other databases

```sh
[OVERALL], RunTime(ms), 521
[OVERALL], Throughput(ops/sec), 1919.3857965451057
[TOTAL_GCS_Copy], Count, 4
[TOTAL_GC_TIME_Copy], Time(ms), 10
[TOTAL_GC_TIME_%_Copy], Time(%), 1.9193857965451053
[TOTAL_GCS_MarkSweepCompact], Count, 0
[TOTAL_GC_TIME_MarkSweepCompact], Time(ms), 0
[TOTAL_GC_TIME_%_MarkSweepCompact], Time(%), 0.0
[TOTAL_GCs], Count, 4
[TOTAL_GC_TIME], Time(ms), 10
[TOTAL_GC_TIME_%], Time(%), 1.9193857965451053
[CLEANUP], Operations, 1
[CLEANUP], AverageLatency(us), 653.0
[CLEANUP], MinLatency(us), 653
[CLEANUP], MaxLatency(us), 653
[CLEANUP], 95thPercentileLatency(us), 653
[CLEANUP], 99thPercentileLatency(us), 653
[INSERT], Operations, 1000
[INSERT], AverageLatency(us), 355.182
[INSERT], MinLatency(us), 97
[INSERT], MaxLatency(us), 29791
[INSERT], 95thPercentileLatency(us), 1399
[INSERT], 99thPercentileLatency(us), 4183
[INSERT], Return=OK, 1000
```
# Now download and run [mongo db - Original installation guide](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/)

- Install gnupg [GNU Privacy Guard]: https://gnupg.org/ 
```sh
sudo apt-get install gnupg
```
- Add apt key for Mongo DB
```sh
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -
```
- Check your OS Release details
```sh
lsb_release -dc

###output would be
ubuntu@ip-172-31-42-28:~$ lsb_release -dc
Description:    Ubuntu 22.04.1 LTS
Codename:       jammy
```
- Below change jammy to your distribution name
```sh
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
```sh
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
# Now run the YCSB workload on Mongo DB
### LOAD THE DATA - ASYNC

- This command will LOAD the records with Asynchronos
```sh
./bin/ycsb load mongodb-async -s -P workloads/workloada -p readcount=1000000 -p operationcount=1000000 -p mongodb.url=mongodb://localhost:27017/ycsb?w=0 > ./mongo_load_out.dat
```

### RUN THE OPERATIONS - ASYNC
- This command will RUN the records with Asynchronos
```sh
./bin/ycsb run mongodb-async -s -P workloads/workloada -p readcount=1000000 -p operationcount=1000000 -p mongodb.url=mongodb://localhost:27017/ycsb?w=0 > ./mongo_run_out.dat
```

### LOAD THE DATA - SYNC
- This command will LOAD the records with Synchronos
```sh
./bin/ycsb load mongodb -s -P workloads/workloada -p readcount=1000000 -p operationcount=1000000 -p mongodb.url=mongodb://localhost:27017/ycsb?w=0 > ./mongo_load_out.dat
```
### RUN THE OPERATIONS - SYNC
- This command will RUN the records with Synchronos
```SH
./bin/ycsb run mongodb -s -P workloads/workloada -p readcount=1000000 -p operationcount=1000000 -p mongodb.url=mongodb://localhost:27017/ycsb?w=0 > ./mongo_run_out.dat
```
