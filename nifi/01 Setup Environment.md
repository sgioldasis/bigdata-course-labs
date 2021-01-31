![nifi-logo](images/000_nifi_logo.png)

# Setup Environment

As part of this session we will see how to setup environment to practice NiFi in a single node cluster with Hadoop and Spark.

- Start NiFi
- Understanding NiFi Layout
- Validating NiFi

## Start NiFi

NiFi is already setup in your machine. To start NiFi type the following on your terminal:

```
start-nifi.sh
```

It will take a bit for NiFi to start. Be patient!

## Understanding NiFi Layout

Here are the details about NiFi software layout in our environment.

- It is configured under **/home/savas/nifi**
- Configurations are available under **/home/savas/nifi/conf**
  - bootstrap.conf
  - nifi.properties
- Executables are under **/home/savas/nifi/bin**
- Logs are being generated under **/home/savas/nifi/logs**
- The default file is **/home/savas/nifi/logs/nifi-app.log**

## Validating NiFi

We can manage NiFi using the following shell scripts:

- Starting NiFi - `start-nifi.sh`
- Stopping NiFi - `stop-nifi.sh`
- Checking Status - `nifi-status.sh`
- Make sure to check the log messages generated at **/home/savas/nifi/logs/nifi-app.log** so that we get comfortable in case of any errors at a later point in time.
- Once NiFi is started we can go to the `http://localhost:8080/nifi/` and see the NiFi Web UI.

## Running a Simple Pipeline

Let us run a simple pipeline as well to ensure that NiFi is working as expected. First, some preparation:

```bash
# Start HDFS
start-dfs.sh

# Remove retail_db folder from HDFS (in case it exists from previous runs)
hdfs dfs -rm -R -skipTrash /user/savas/retail_db

# Copy the data into /tmp folder
cp -r /home/savas/data/retail_db/ /tmp
```

- Download this [NiFi Simple Pipeline](xml/Simple_Pipeline_Demo.xml).
- Upload it using NiFi UI
- Review processors and the locations that are used against the setup.
- Run and validate to see if the data is flowing as expected.
