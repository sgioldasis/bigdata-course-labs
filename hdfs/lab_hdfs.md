# HDFS Lab

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

Useful links: 
- https://www.tutorialspoint.com/hadoop/hadoop_command_reference.htm
- https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#stat
