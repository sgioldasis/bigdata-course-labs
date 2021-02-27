# HDFS Lab
Before you run the lab make sure HDFS is running. To do that open a terminal and run the following:
```
start-dfs.sh
```

Then run the commands for each task described below in your terminal.

- Find file bank.csv in local filesystem
    
    ```
    locate bank.csv
    ```

- List the hdfs root folder
    ```
    hdfs dfs -ls /
    ```

- List the hdfs user folder
    ```
    hdfs dfs -ls /user
    ```

- List the hdfs user folder for user savas
    ```
    hdfs dfs -ls /user/savas
    ```

- Show the disk usage of the hdfs user folder for user savas
    ```
    hdfs dfs -du /user/savas
    hdfs dfs -du -s /user/savas
    ```

- Print statistics about the file bank.csv in the hdfs user folder for user savas
    ```
    hdfs dfs -stat "type:%F perm:%a %u:%g size:%b mtime:%y atime:%x name:%n" /user/savas/bank.csv
    ```

- Use fsck command for the file bank.csv in the hdfs user folder for user savas to show files, blocks locations
    ```
    hdfs fsck /user/savas/bank.csv -files -blocks -locations
    ```

- Show the contents of the file bank.csv in the hdfs user folder for user savas
    ```
    hdfs dfs -cat /user/savas/bank.csv
    ```

- Show the last few lines of the file bank.csv in the hdfs user folder for user savas
    ```
    hdfs dfs -tail /user/savas/bank.csv
    ```

- List the metadata for the file bank.csv in the hdfs user folder for user savas
    ```
    hdfs dfs -ls /user/savas/bank.csv
    ```

- Change the replication factor for the file bank.csv in the hdfs user folder for user savas
    ```
    hdfs dfs -setrep 2 /user/savas/bank.csv
    hdfs fsck /user/savas/bank.csv -files -blocks -locations
    hdfs dfs -setrep 1 /user/savas/bank.csv
    ```

Now it's time for you to try and complete the following tasks:

- In HDFS create a new folder `test` inside the existing `/user` folder
- In HDFS list the contents of the new folder `/user/test` and make sure it is empty
- In HDFS list the contents of the existing folder `/user` and make sure it contains your `test` folder
- Copy the file `/user/savas/bank.csv` from HDFS to your local filesystem. The local filename should also be `bank.csv`
- List your local filesystem and make sure the file `bank.csv` is there
- Copy the file `bank.csv` from your local filesystem to the HDFS folder `/user/test`. The HDFS filename should also be `bank.csv`
- In HDFS list the contents of the folder `/user/test` and make sure it contains the file `bank.csv`
- Display the all contents of the file `/user/test/bank.csv` stored in HDFS
- Display only the last few lines of the file `/user/test/bank.csv` stored in HDFS


Useful links: 
- https://www.tutorialspoint.com/hadoop/hadoop_command_reference.htm
- https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#stat
