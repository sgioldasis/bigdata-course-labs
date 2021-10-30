# HBase Docker Lab
Before you run the lab make sure HBase is running. To do that open a terminal and run the following:

```
cd ~/projects/bigdata-docker-infra/hdfs/docker-hadoop-cluster
docker-compose up -d
```

After that, you need to connect to the running `master` container in order to get a terminal:

```
docker exec -it master bash
```

Then you can run the commands for each task described below in your terminal.

- Download the file `bank.csv` in local filesystem. 
    
    ```
    curl "https://s3.amazonaws.com/apache-zeppelin/tutorial/bank/bank.csv" -o "bank.csv"
    ```

- List the hdfs root folder
    ```
    hdfs dfs -ls /
    ```

- Create a data folder under root folder
    ```
    hdfs dfs -mkdir /data
    ```

- Copy the file `bank.csv` from the local file system to HDFS
    ```
    hdfs dfs -put bank.csv /data
    ```

- Show the disk usage of the hdfs `data` folder
    ```
    hdfs dfs -du /data
    hdfs dfs -du -s /data
    ```

- Print statistics about the file `bank.csv` in the HDFS folder `data`
    ```
    hdfs dfs -stat "type:%F perm:%a %u:%g size:%b mtime:%y atime:%x name:%n" /data/bank.csv
    ```

- Use fsck command for the file `bank.csv` in the hdfs `data` folder show files, blocks locations
    ```
    hdfs fsck /data/bank.csv -files -blocks -locations
    ```

- Show the first few lines of the file `bank.csv` in the hdfs `data` folder
    ```
    hdfs dfs -head /data/bank.csv
    ```

- Count the lines of the file `bank.csv` in the hdfs `data` folder
    ```
    hdfs dfs -cat /data/bank.csv | wc -l
    ```

- List the metadata for the file `bank.csv` in the hdfs `data` folder
    ```
    hdfs dfs -ls /data/bank.csv
    hdfs dfs -ls hdfs://master:9000/data/bank.csv
    ```

- Change the replication factor for the file `bank.csv` in the hdfs `data` folder
    ```
    hdfs dfs -setrep 2 /data/bank.csv
    hdfs fsck /data/bank.csv -files -blocks -locations
    ```

Now it's time for you to try and complete the following tasks:

- Checkout http://localhost:9870/ and try to find the file `bank.csv`
- Find on which datanodes it is available.
- Kill one of the datanodes using `docker kill` command. 
- Check if the file is still available

---
**OPTIONAL:**

In order to perform the following you first have to setup and configure a local hadoop client on your machine. Instructions on how to do that are beyond the scope of this document. However, if you are in a Linux machine and you already have Java installed, you can use the following:

```
export JAVA_HOME=/usr/lib/jvm/java-14-openjdk-amd64

export tmp=/tmp
cp -r ~/bigdata/bigdata-docker-infra/hdfs/hadoop-3.2.0 $tmp

export HADOOP_OPTS=-Djava.library.path=$tmp/hadoop-3.2.0/lib/native
export HADOOP_INSTALL=$tmp/projects/hadoop-3.2.0
export HADOOP_MAPRED_HOME=$tmp/hadoop-3.2.0
export HADOOP_COMMON_HOME=$tmp/hadoop-3.2.0
export HADOOP_HOME=$tmp/hadoop-3.2.0
export HADOOP_HDFS_HOME=$tmp/hadoop-3.2.0
export HADOOP_COMMON_LIB_NATIVE_DIR=$tmp/hadoop-3.2.0/lib/native
export PATH=$tmp/hadoop-3.2.0/sbin:$tmp/hadoop-3.2.0/bin:$PATH
```

---

- Start a second HDFS cluster using the following commands:
```
cd ~/projects/bigdata-docker-infra/hdfs/docker-hadoop-cluster-2
docker-compose up -d
```

- List the files in cluster-1
```
hdfs dfs -ls hdfs://localhost:9000/
```

- List the files in cluster-2
```
hdfs dfs -ls hdfs://localhost:9001/
```

- Copy the folder `data` from cluster-1 to cluster-2
```
hadoop distcp hdfs://localhost:9000/data hdfs://localhost:9001/
```

- Remove the folder `data` from cluster-2
```
hadoop fs -rm -r hdfs://localhost:9001/data
```

- Run the examples in the second HDFS cluster using your local HDFS client


