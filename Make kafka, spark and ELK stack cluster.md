To make a functional cluster with one master and two slaves, you will need to perform the following steps on the two slave VMs:


## Kafka 

1. Configure the hostnames: Make sure that each VM has a unique hostname. You can do this by editing the `/etc/hostname` file.

2. Configure the hosts file: Edit the `/etc/hosts` file on each slave VM to include the hostnames and IP addresses of all three VMs.

   For example, on the first slave VM, the `/etc/hosts` file should look something like this:
```bash
127.0.0.1 localhost
192.168.100.1 master
192.168.100.2 slave1
192.168.100.3 slave2
```

3. Install Java, Scala and SSH: Install Java, Scala, and SSH on the two slave VMs using the same steps you used for the master VM.

### Java 
```bash
$ sudo apt-get install openjdk-8-jdk
```

### Scala 

Install the .deb file from the official website : 

`https://www.scala-lang.org/download/2.12.0.html`

### SSH
```bash
$ sudo apt install openssh-client
```

4. Install ZooKeeper: Install ZooKeeper on the two slave VMs by downloading the same version of ZooKeeper that you installed on the master VM and configuring it with the same settings.

5. Configure the ZooKeeper ensemble: Edit the `zookeeper.properties` file on each slave VM to include the IP addresses of all three VMs in the `server.` configuration.

For example, on the first slave VM, the `zookeeper.properties` file should look something like this:
```
tickTime=2000
dataDir=/var/lib/zookeeper
clientPort=2181
initLimit=5
syncLimit=2
server.1=master:2888:3888
server.2=slave1:2888:3888
server.3=slave2:2888:3888
```
6. Install Kafka: Install Kafka on the two slave VMs by downloading the same version of Kafka that you installed on the master VM and configuring it with the same settings.

7. Configure Kafka broker: Edit the `server.properties` file on each slave VM to include the IP address of the master VM in the `broker.id` and `zookeeper.connect` configuration.

For example, on the first slave VM, the `server.properties` file should look something like this:

```
broker.id=2
zookeeper.connect=master:2181,slave1:2181,slave2:2181
```

## Spark 

Here are the general steps to configure Spark on a master and  slaves:

Download the latest version of Spark from the official website: https://spark.apache.org/downloads.html

Extract the Spark archive to a directory on both the master and slaves machines.

Set the `SPARK_HOME` environment variable to the path where Spark is extracted on both machines. For example, in a  shell, you can use the command `export SPARK_HOME=/path/to/spark`.

On the master machine, edit the conf/spark-env.sh file in the Spark directory and add the following lines:


```
export SPARK_MASTER_HOST=<master-ip>
export SPARK_MASTER_PORT=7077
export SPARK_LOCAL_IP=<master-ip>
Replace <master-ip> with the IP address of the master machine.
```
On the slaves machine, edit the conf/spark-env.sh file in the Spark directory and add the following lines:


```
export SPARK_WORKER_INSTANCES=1
export SPARK_WORKER_CORES=2
export SPARK_WORKER_MEMORY=2g
export SPARK_WORKER_DIR=/tmp/spark-worker
export SPARK_LOCAL_IP=<slave-ip>
```
Replace <slave-ip> with the IP address of the slave machine.

Start the master by running the following command on the master machine:


```
./sbin/start-master.sh
```
Start the slaves by running the following command on the slave machine:


```
./sbin/start-worker.sh spark://<master-ip>:7077
```
Replace <master-ip> with the IP address of the master machine.

You can now run Spark jobs on the master and the slave. To test the installation, you can run the Spark shell on the master by running the following command:


```
./bin/spark-shell --master spark://<master-ip>:7077
```
Replace <master-ip> with the IP address of the master machine.

### ELK stack 
  If you installed the ELK stack using the package manager apt, the configuration files for Logstash and Elasticsearch can be found in the /etc/ directory. Specifically:

The Logstash configuration file is located at /etc/logstash/conf.d. You can create your own configuration file in this directory, or modify the existing logstash.conf file.

The Elasticsearch configuration file is located at /etc/elasticsearch/elasticsearch.yml. You can modify this file to change Elasticsearch settings such as cluster name, node name, network settings, etc.

As for the Logstash hosts and Elasticsearch hosts, it depends on your specific use case and how you want to configure your ELK stack. By default, Logstash will attempt to send data to Elasticsearch running on the same machine as Logstash, so you don't need to specify a hosts parameter in the Logstash configuration file.

If you want to send data to Elasticsearch running on a different machine, you can specify the Elasticsearch hosts in the Logstash configuration file using the hosts parameter, like this:


```
output {
  elasticsearch {
    hosts => ["http://elasticsearch_host:9200"]
  }
}
```
Replace elasticsearch_host with the IP address or hostname of the machine running Elasticsearch.

Similarly, if you want to configure Elasticsearch to be part of a cluster, you can specify the hosts in the elasticsearch.yml configuration file using the discovery.seed_hosts parameter, like this:


```
cluster.name: my_cluster
node.name: my_node
network.host: 0.0.0.0
discovery.seed_hosts: ["elasticsearch_host_1", "elasticsearch_host_2", "elasticsearch_host_3"]
Replace elasticsearch_host_1, elasticsearch_host_2, and elasticsearch_host_3 with the IP addresses or hostnames of the machines running Elasticsearch in your cluster.
```
#### Logstash 

To configure Logstash to connect to the Elasticsearch node running on the master VM, you will need to modify the Logstash configuration file. The location of the configuration file depends on your specific installation and operating system, but it is usually located in the /etc/logstash/conf.d/ directory.

Open the Logstash configuration file in a text editor. For example, on a Ubuntu system, you can use the following command to open the default Logstash configuration file in nano:
bash
```
$ sudo nano /etc/logstash/conf.d/logstash.conf
```
Add the following elasticsearch output section to the configuration file:

```
output {
  elasticsearch {
    hosts => ["Host_IP:9200"]  # Replace with the IP address of the master VM
  }
}
```
Replace Host_IP with the IP address of the master VM that is running Elasticsearch. This will configure Logstash to send data to the Elasticsearch node running on the master VM.

Save and close the configuration file.

Restart Logstash to apply the changes:

```
sudo systemctl restart logstash
  If you installed the ELK stack using the package manager apt, the configuration files for Logstash and Elasticsearch can be found in the /etc/ directory. Specifically:

The Logstash configuration file is located at /etc/logstash/conf.d. You can create your own configuration file in this directory, or modify the existing logstash.conf file.

The Elasticsearch configuration file is located at /etc/elasticsearch/elasticsearch.yml. You can modify this file to change Elasticsearch settings such as cluster name, node name, network settings, etc.

As for the Logstash hosts and Elasticsearch hosts, it depends on your specific use case and how you want to configure your ELK stack. By default, Logstash will attempt to send data to Elasticsearch running on the same machine as Logstash, so you don't need to specify a hosts parameter in the Logstash configuration file.

If you want to send data to Elasticsearch running on a different machine, you can specify the Elasticsearch hosts in the Logstash configuration file using the hosts parameter, like this:


```
output {
  elasticsearch {
    hosts => ["http://elasticsearch_host:9200"]
  }
}
```
Replace elasticsearch_host with the IP address or hostname of the machine running Elasticsearch.

Similarly, if you want to configure Elasticsearch to be part of a cluster, you can specify the hosts in the elasticsearch.yml configuration file using the discovery.seed_hosts parameter, like this:


```
cluster.name: my_cluster
node.name: my_node
network.host: 0.0.0.0
discovery.seed_hosts: ["elasticsearch_host_1", "elasticsearch_host_2", "elasticsearch_host_3"]
Replace elasticsearch_host_1, elasticsearch_host_2, and elasticsearch_host_3 with the IP addresses or hostnames of the machines running Elasticsearch in your cluster.
```
#### Logstash 

To configure Logstash to connect to the Elasticsearch node running on the master VM, you will need to modify the Logstash configuration file. The location of the configuration file depends on your specific installation and operating system, but it is usually located in the /etc/logstash/conf.d/ directory.

Open the Logstash configuration file in a text editor. For example, on a Ubuntu system, you can use the following command to open the default Logstash configuration file in nano:
bash
```
$ sudo nano /etc/logstash/conf.d/logstash.conf
```
Add the following elasticsearch output section to the configuration file:

```
output {
  elasticsearch {
    hosts => ["Host_IP:9200"]  # Replace with the IP address of the master VM
  }
}
```
Replace Host_IP with the IP address of the master VM that is running Elasticsearch. This will configure Logstash to send data to the Elasticsearch node running on the master VM.

Save and close the configuration file.

Restart Logstash to apply the changes:

```
sudo systemctl restart logstash
Now Logstash is configured to ingest data and send it to Elasticsearch running on the master VM.

#### Kibana

To configure Kibana to connect to Elasticsearch running on the master VM, you will need to modify the Kibana configuration file. The location of the configuration file depends on your specific installation and operating system, but it is usually located in the /etc/kibana/ directory.

Open the Kibana configuration file in a text editor. For example, on a Ubuntu system, you can use the following command to open the default Kibana configuration file in nano:
bash
```
sudo nano /etc/kibana/kibana.yml
```
Find the elasticsearch.hosts property in the configuration file. If it is commented out, remove the # symbol at the beginning of the line. Then, set the value of the property to the IP address of the master VM running Elasticsearch, like this:

```
elasticsearch.hosts: ["http://Host_IP:9200"]
```
Replace Host_IP with the IP address of the master VM that is running Elasticsearch.

Save and close the configuration file.

Restart Kibana to apply the changes:

```
sudo systemctl restart kibana
```
Now Kibana is configured to connect to Elasticsearch running on the master VM, and you can use it to visualize and explore data in Elasticsearch.
Now Logstash is configured to ingest data and send it to Elasticsearch running on the master VM.

#### Kibana

To configure Kibana to connect to Elasticsearch running on the master VM, you will need to modify the Kibana configuration file. The location of the configuration file depends on your specific installation and operating system, but it is usually located in the /etc/kibana/ directory.

Open the Kibana configuration file in a text editor. For example, on a Ubuntu system, you can use the following command to open the default Kibana configuration file in nano:

```
sudo nano /etc/kibana/kibana.yml
```
Find the elasticsearch.hosts property in the configuration file. If it is commented out, remove the # symbol at the beginning of the line. Then, set the value of the property to the IP address of the master VM running Elasticsearch, like this:

```
elasticsearch.hosts: ["http://Host_IP:9200"]
```
  
Replace Host_IP with the IP address of the master VM that is running Elasticsearch.

Save and close the configuration file.

Restart Kibana to apply the changes:

```
sudo systemctl restart kibana
```
Now Kibana is configured to connect to Elasticsearch running on the master VM, and you can use it to visualize and explore data in Elasticsearch.

