# Kafka Crash Course

## Overall Architecture
![kafka architecture](./assets/kafka-architecture.png?raw=true "kafka architecture")

## Requirements
- Java installed Ubuntu VPS. For VPS, you may try <a href="https://aws.amazon.com/lightsail/">AWS Lightsail</a> (which I use) or <a href="https://www.digitalocean.com/products/droplets/"> Droplets on Digital Ocean </a>
- Choose 4G RAM VPS not to encounter memory issues

## Process
- Connect using your own SSH client to VPS with your .pem-file (you have to download it from Lightsail) after having created it:
```
ssh -i YOUR_PEM_FILE.pem YOUR_USER@VPS_IP_ADDESS
```

- Install java
```
sudo apt update && sudo apt -y upgrade
sudo apt -y install openjdk-11-jre-headless
```
- Install binary version kafka on https://kafka.apache.org/downloads: Get the installation link (it suggests the closest server, in my case "https://apache.uib.no/kafka/2.5.0/kafka_2.12-2.5.0.tgz", create and download the file into "Downloads" directory, and finally install it into a new "kafka" directory: 
```
curl https://apache.uib.no/kafka/2.5.0/kafka_2.12-2.5.0.tgz -o Downloads/kafka.tgz
mkdir kafka
cd kafka
tar -xvzf ~/Downloads/kafka.tgz --strip 1
```
- Check the structure in kafka folder:
  - "bin" - all scripts
  - "config" - ".properties" files for configuration
  - "libs" - installed libraries
  - "logs" - logs files
  - "site-docs" - documentation
- After installation, if you try to start the server you will get error, because kafka needs to connect to zookeeper which coordinates brokers:
```
bin/kafka-server-start.sh config/server.properties
```
![ZooKeeper](./assets/zookeper-coordination.png?raw=true "ZooKeeper")

- So start zookeper (default port localhost:2181), then start the server/broker (default port localhost:9092; NB! If you want to run multiple brokers in a single computer you have to run each broker in a distinct port) in a new terminal while zookeper is running:
```
bin/zookeeper-server-start.sh config/zookeeper.properties
```
- Kafka saves logs in "logs" folder but messages are stored in /tmp/kafka-logs (!) whcih are automatically created by kafka server. To check running brokers in kafka-logs:  
```
cat meta.properties

# result something like this
broker.id=0
version=0
cluster.id=4kUvEDRXSBi-u9LwNOX_7g
```
- Create a topic (remember you may specify either bootstrap server or zookeeper, try ```bin/kafka-topics.sh --createv``` to see):
```
bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --topic TOPIC_NAME_1
```

- List existing topics:
```
bin/kafka-topics.sh --list --zookeper localhost:2181
```
- Inside every topic, messages can be spread among several partitions. As you may read in "config/server.properites", the default number of log partitions per topic is 1. More partitions allow greater parallelism for consumption, but this will also result in more files across the brokers.


