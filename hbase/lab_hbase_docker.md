# HBase Docker Lab
Before you run the lab make sure HBase is running. To do that open a terminal and run the following:

```
cd ~/projects/bigdata-docker-infra/hbase
```

You can now run HBase either in standalone or local distributed mode:

### Standalone
To run standalone HBase:
```
docker-compose -f docker-compose-standalone.yml up -d
```
The deployment is the same as in [quickstart HBase documentation](https://hbase.apache.org/book.html#qu>).
It can be used for testing/development, connected to Hadoop cluster.

### Local distributed
To run local distributed HBase:
```
docker-compose -f docker-compose-distributed-local.yml up -d
```

This deployment will start Zookeeper, HMaster and HRegionserver in separate containers.

After that, you need to connect to the running `hbase` container in order to get a terminal. 

```
docker exec -it hbase bash
```

Inside the hbase container you can start an hbase shell:
    
```
hbase shell
```

Inside the hbase shell you can run the following commands:

## General Commands

- Show the status of the system including the details of the servers running on the system.

    ```
    status
    ```

- Show the version of HBase used in your system.
    ```
    version
    ```

- Show a guide on how to use table-referenced commands
    ```
    table_help
    ```

- Show the current HBase user
    ```
    whoami
    ```

## Creating a Table

You can create a table using the create command. Here you must specify the table name and the Column Family name. The syntax to create a table in HBase shell is shown below.

```
create ‘<table name>’,’<column family>’ 
```

- Create the `emp` table
    ```
    create 'emp', 'personal data', 'professional data'
    ```

- List the existing tables
    ```
    list
    ```

## Enabling/Disabling a Table

To delete a table or change its settings, you need to first disable the table using the disable command. You can re-enable it using the enable command.

- Disable the `emp` table
    ```
    disable 'emp'
    ```

`NOTE:`  After disabling the table, you can still sense its existence through list and exists commands. You cannot scan it. If you try:

    ```
    scan 'emp'
    ```

it will give you the following:

`ERROR: emp is disabled.`


You can also check whether a table is disabled:

    ```
    is_disabled 'emp'
    ```

Finally, you can re-enable the table:

    ```
    enable 'emp'
    ```

## Manipulating a Table

- Show the description of the `emp` table

    ```
    describe 'emp'
    ```

- Change the maximum number of cells of a column family:

    ```
    alter 'emp', NAME => 'personal data', VERSIONS => 5
    ```

- Add a column family:

    ```
    alter 'emp', 'newcf'
    ```

- Delete a column family:

    ```
    alter 'emp', 'delete' => 'newcf'
    ```

- Verify the existence of a table:

    ```
    exists 'emp'
    ```

- Delete/Drop a table (before dropping a table, you have to disable it)

    ```
    disable 'emp'
    drop 'emp'
    ```

- Disable, drop and recreate a table (truncate)

    ```
    truncate 'emp'
    ```

## Inserting Data

Using the `put` command, you can insert rows into a table. Its syntax is as follows:

```
put '<table name>', 'row1','<colfamily:colname>', '<value>'
```

- Insert the first row values into the emp table:

    ```
    put 'emp','1','personal data:name','Savas'
    put 'emp','1','personal data:city','Athens'
    put 'emp','1','professional data:designation','manager'
    put 'emp','1','professional data:salary','50000'

    put 'emp','2','personal data:name','George'
    put 'emp','2','personal data:city','Thessaloniki'
    put 'emp','2','professional data:designation','team lead'
    put 'emp','2','professional data:salary','40000'

    put 'emp','3','personal data:name','Nick'
    put 'emp','3','personal data:city','Patra'
    put 'emp','3','professional data:designation','junior developer'
    put 'emp','3','professional data:salary','30000'

    ```

- Verify the data was inserted:

    ```
    scan 'emp'
    ```

## Updating Data

You can also update the table using the `put` command above. Try it:

- Update the salary for employee 1:

    ```
    put 'emp','1','professional data:salary','60000'
    ```

- Verify the data was updated:

    ```
    scan 'emp'
    ```

## Reading data

We already saw examples of the `scan` command. Using the `get` command, you can get a single row of data at a time. Its syntax is as follows:

```
get '<table name>', 'row1'
```

- Read the row for employee 1:

    ```
    get 'emp', '1'
    ```

Below is the syntax to read a specific column using the get method:

```
get 'table name', 'rowid', {COLUMN ⇒ 'column family:column name'}
```

- Read the name of employee 1:

    ```
    get 'emp', '1', {COLUMN => 'personal data:name'}
    ```

## Deleting a Specific Cell in a Table

Using the delete command, you can delete a specific cell in a table. The syntax of delete command is as follows:

```
delete '<table name>', '<row>', '<column name>', '<time stamp>'
```


- Read the city of employee 1:

    ```
    delete 'emp', '1', 'personal data:city'
    ```

Using the `deleteall` command, you can delete all the cells in a row. Given below is the syntax of `deleteall` command:

```
deleteall '<table name>', '<row>'
```

- Delete employee 1:

    ```
    deleteall 'emp', '1'
    ```

- Verify by counting the number of rows after the operation:

    ```
    count 'emp'
    ```



## Exit the HBase shell

Exit the hbase shell:
    
```
exit
```

Exit the hbase container:
    
```
exit
```

Shutdown all containers:

```
docker-compose -f docker-compose-standalone.yml down
```

Verify

```
docker-compose -f docker-compose-standalone.yml ps
```



