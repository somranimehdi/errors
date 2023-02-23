# Kafka - The Cluster ID X doesn't match stored clusterId Some(Y) in meta.properties.

After installing kafka on distributed machines, Kafka shuts down immedietly after starting with an error in log  saying `Kafka - The Cluster ID X doesn't match stored clusterId Some(Y) in meta.properties`. 

The solution to this error is pretty easy 

Stop all kafka services 
with 

```bash
sudo systemctl stop kafka
```
or with

```bash
kafka-server-stop.sh
```

Search for the `meta.properties` file using this command as root 

```bash
sudo find / -name meta.properties
```

Then delete the file using 
```bash
sudo rm -rf /path/to/meta.properties
```

After deleting the file, start the Kafka broker and it should create a new `meta.properties`.

If the file is not found, it's possible that the data directory has been changed or the Kafka broker is not able to create the meta.properties file for some reason. In that case, you can try stopping the Kafka broker and deleting the existing data directory (assuming you have no important data in there), and then starting the Kafka broker again.

To delete the Kafka data directory, you can run the following command:


```bash
sudo rm -rf /tmp/kafka-logs
```
After deleting the data directory, start the Kafka again broker and it should create a new `meta.properties` file.

