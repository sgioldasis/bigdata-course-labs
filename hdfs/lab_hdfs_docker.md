# HDFS Docker Lab
Before you run the lab make sure HDFS is running. To do that open a terminal and run the following:
```
cd ~/projects/docker-hadoop-cluster
docker-compose up -d
```

Then run the commands for each task described below in your terminal.

- Find the file `bank.csv` in local filesystem. If you don't have it you can download it from [here](https://www.kaggle.com/janiobachmann/bank-marketing-dataset)
    
    ```
    find ~ -name bank.csv
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
    hdfs dfs -put /home/savas/Downloads/bank.csv /data
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
    ```

- Change the replication factor for the file `bank.csv` in the hdfs `data` folder
    ```
    hdfs dfs -setrep 2 /data/bank.csv
    hdfs fsck /data/bank.csv -files -blocks -locations
    hdfs dfs -setrep 1 /data/bank.csv
    ```

Now it's time for you to try and complete the following tasks:

- Checkout http://localhost:9870/ and try to find the file `bank.csv`
- Find on which datanodes it is available.
- Kill one of the datanodes using `docker kill` command. 
- Check if the file is still available
- Start a second HDFS cluster using the following commands:
```
cd ~/projects/docker-hadoop-cluster-2
docker-compose up -d
```
- Run the above examples in the second HDFS cluster using the same HDFS client

