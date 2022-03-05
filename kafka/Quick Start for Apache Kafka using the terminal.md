[![](https://assets.confluent.io/m/1661ef5e4ff82d3d/original/20200122-PNG-web-dev-logo-denim.png)](http://confluent.io)

# Quick Start for Apache Kafka using the terminal

Use this quick start to get up and running with Confluent Platform and
its main components in a development environment. This quick start uses
the terminal for topic
management and event stream processing using ksqlDB.

In this quick start, you create Apache Kafka® topics, use the terminal
to generate mock data to those topics, and create ksqlDB streaming
queries on those topics.

## Step 1: Start Confluent Platform

Start Confluent Platform using the Confluent CLI [confluent local services start](/confluent-cli/current/command-reference/local/services/confluent_local_services_start.html)
command. This command starts all of the Confluent Platform components, including Kafka, ZooKeeper, Schema Registry, HTTP RESTProxy for Kafka, Kafka Connect, ksqlDB, and Control Center. Type the following in your terminal:

    start-kafka.sh

Your output should resemble:

    Starting Zookeeper
    Zookeeper is [UP]
    Starting Kafka
    Kafka is [UP]
    Starting Schema Registry
    Schema Registry is [UP]
    Starting Kafka REST
    Kafka REST is [UP]
    Starting Connect
    Connect is [UP]
    Starting KSQL Server
    KSQL Server is [UP]
    Starting Control Center
    Control Center is [UP]

## Step 2: Create Kafka Topics

In this step, you create Kafka topics using [Confluent Control Center]. Confluent Control Center provides the functionality for building and monitoring production data pipelines and event streaming applications.

1. Open a terminal.

2. Change to `kafka` directory.

   ```bash
   cd kafka
   ```

3. Create the `quickstart-events` topic.

   ```bash
   bin/kafka-topics.sh --create --topic quickstart-events --bootstrap-server localhost:9092
   ```

   You should see the following output:

   ```
   Created topic quickstart-events.
   ```

4. All of Kafka's command line tools have additional options: run the kafka-topics.sh command without any arguments to display usage information. For example, it can also show you details such as the partition count of the new topic:

   ```bash
   bin/kafka-topics.sh --describe --topic quickstart-events --bootstrap-server localhost:9092
   ```

   You should see the following output:

   ```
   Topic: quickstart-events	PartitionCount: 1	ReplicationFactor: 1	Configs: segment.bytes=1073741824
   Topic: quickstart-events	Partition: 0	Leader: 0	Replicas: 0	Isr: 0
   ```

## Step 3: Use the terminal to Generate Sample Data

In this step, you use the terminal to manually create sample event data. A Kafka client communicates with the Kafka brokers via the network for writing (or reading) events. Once received, the brokers will store the events in a durable and fault-tolerant manner for as long as you need (even forever).

### WRITE SOME EVENTS INTO THE TOPIC:

Run the console producer client to write a few events into your topic. By default, each line you enter will result in a separate event being written to the topic. In your terminal type the following:

```bash
bin/kafka-console-producer.sh --topic quickstart-events --bootstrap-server localhost:9092
This is my first event
This is my second event
```

You can stop the producer client with Ctrl-C at any time.

### READ THE EVENTS:

Open another terminal session and run the console consumer client to read the events you just created:

```bash
bin/kafka-console-consumer.sh --topic quickstart-events --from-beginning --bootstrap-server localhost:9092
```

You should see the following output:

```
This is my first event
This is my second event
```

You can stop the consumer client with Ctrl-C at any time.

Feel free to experiment: for example, switch back to your producer terminal (previous step) to write additional events, and see how the events immediately show up in your consumer terminal.

Because events are durably stored in Kafka, they can be read as many times and by as many consumers as you want. You can easily verify this by opening yet another terminal session and re-running the previous command again.

## Step 4: Create and Write to a Stream and Table using ksqlDB

### Create Streams and Tables

In this step, you use ksqlDB to create a streams and tables based on a topic. In your second terminal (the one running the `kafka-console-consumer.sh`) stop the consumer client with Ctrl-C. Then type `ksql` to start ksqlDB CLI:

```bash
ksql
```

Now to create a stream type:

```sql
CREATE STREAM MOVEMENTS (PERSON VARCHAR KEY, LOCATION VARCHAR)
    WITH (VALUE_FORMAT='JSON', PARTITIONS=1, KAFKA_TOPIC='movements');
```

You should see the following output:

```
 Message
----------------
 Stream created
----------------
```

### Write Queries

In this step, you create ksqlDB queries against the stream you created above. In your terminal (where you just created the stream) type the following query:

```sql
SELECT TIMESTAMPTOSTRING(ROWTIME,'yyyy-MM-dd HH:mm:ss','Europe/London') AS EVENT_TS,
       PERSON,
       LOCATION
  FROM MOVEMENTS
  EMIT CHANGES;
```

You should see the following output:

```
+------------------------+------------------------+------------------------+
|EVENT_TS                |PERSON                  |LOCATION                |
+------------------------+------------------------+------------------------+

Press CTRL-C to interrupt
```

Now, in your other terminal (the one running the `kafka-console-producer.sh`) stop the producer client with Ctrl-C. Then type `ksql` to start ksqlDB CLI and insert some data by typing the following:

```sql
INSERT INTO MOVEMENTS VALUES ('robin', 'York');
INSERT INTO MOVEMENTS VALUES ('robin', 'Leeds');
INSERT INTO MOVEMENTS VALUES ('robin', 'Ilkley');
```

Notice that in the other terminal (the one running the query - let's call it `[Window 2]` from now on) the new records have appeared:

```
+------------------------+------------------------+------------------------+
|EVENT_TS                |PERSON                  |LOCATION                |
+------------------------+------------------------+------------------------+
|2022-03-05 10:12:09     |robin                   |York                    |
|2022-03-05 10:12:09     |robin                   |Leeds                   |
|2022-03-05 10:12:09     |robin                   |Ilkley                  |

Press CTRL-C to interrupt
```

Now, in `[Window 2]` cancel the query (CTRL-C) and show the topics by typing:

```sql
SHOW TOPICS;
```

You can see the new topic `movements` in the list. To dump the topic contents, you can type:

```sql
PRINT movements FROM BEGINNING;
```

You should see the following output:

```
Key format: KAFKA_STRING
Value format: JSON or KAFKA_STRING
rowtime: 2022/03/05 10:12:09.437 Z, key: robin, value: {"LOCATION":"York"}
rowtime: 2022/03/05 10:12:09.468 Z, key: robin, value: {"LOCATION":"Leeds"}
rowtime: 2022/03/05 10:12:09.505 Z, key: robin, value: {"LOCATION":"Ilkley"}
PRINT movements FROM BEGINNING;

Press CTRL-C to interrupt
```

Now in `[Window 2]`, you can cancel the PRINT (CTRL-C) and query the stream with a predicate:

```sql
SET 'auto.offset.reset' = 'earliest';
SELECT TIMESTAMPTOSTRING(ROWTIME,'yyyy-MM-dd HH:mm:ss','Europe/London') AS EVENT_TS,
       PERSON,
       LOCATION
  FROM MOVEMENTS
  WHERE LCASE(LOCATION)='leeds'
  EMIT CHANGES;
```

You should see the only the records from the stream that match the predicate (`LCASE(LOCATION)='leeds'`):

```
Successfully changed local property 'auto.offset.reset' to 'earliest'. Use the UNSET command to revert your change.
+------------------------+------------------------+------------------------+
|EVENT_TS                |PERSON                  |LOCATION                |
+------------------------+------------------------+------------------------+
|2022-03-05 10:12:09     |robin                   |Leeds                   |

Press CTRL-C to interrupt
```

Now, leave the query running and insert some more data in `[Window 1]`

```sql
INSERT INTO MOVEMENTS VALUES ('robin', 'Sheffield');
INSERT INTO MOVEMENTS VALUES ('robin', 'Leeds');
INSERT INTO MOVEMENTS VALUES ('robin', 'Wakefield');
INSERT INTO MOVEMENTS VALUES ('robin', 'Leeds');
```

Notice in `[Window 2]` we have some more output reflecting the new records which match the predicate:

```
+------------------------+------------------------+------------------------+
|EVENT_TS                |PERSON                  |LOCATION                |
+------------------------+------------------------+------------------------+
|2022-03-05 10:12:09     |robin                   |Leeds                   |
|2022-03-05 10:26:28     |robin                   |Leeds                   |
|2022-03-05 10:26:29     |robin                   |Leeds                   |

Press CTRL-C to interrupt
```

### Stream/Table duality

In this step, we will show the difference between stream and table - but note that it's the _same_ Kafka topic underneath. First stop the query running in `[Window 2]` (CTRL-C). Now create a `TABLE` by typing:

```sql
CREATE TABLE MOVEMENTS_T (PERSON VARCHAR PRIMARY KEY, LOCATION VARCHAR)
    WITH (VALUE_FORMAT='JSON', KAFKA_TOPIC='movements');
```

You should see the following output:

```
 Message
---------------
 Table created
---------------
```

Type the following in `[Window 2]` to see the **TABLE**:

```sql
SELECT TIMESTAMPTOSTRING(ROWTIME,'yyyy-MM-dd HH:mm:ss','Europe/London') AS EVENT_TS, PERSON, LOCATION
    FROM MOVEMENTS_T EMIT CHANGES;
```

You should see the following output:

```
+------------------------+------------------------+------------------------+
|EVENT_TS                |PERSON                  |LOCATION                |
+------------------------+------------------------+------------------------+
|2022-03-05 10:26:29     |robin                   |Leeds                   |
```

Now, type the following in `[Window 1]` to see the **STREAM**:

```sql
SET 'auto.offset.reset' = 'earliest';
SELECT TIMESTAMPTOSTRING(ROWTIME,'yyyy-MM-dd HH:mm:ss','Europe/London') AS EVENT_TS,
       PERSON,
       LOCATION
  FROM MOVEMENTS
  WHERE LCASE(LOCATION)='leeds'
  EMIT CHANGES;
```

You should see the following output:

```
+--------------------------+--------------------------+--------------------------+
|EVENT_TS                  |PERSON                    |LOCATION                  |
+--------------------------+--------------------------+--------------------------+
|2022-03-05 10:12:09       |robin                     |Leeds                     |
|2022-03-05 10:26:28       |robin                     |Leeds                     |
|2022-03-05 10:26:29       |robin                     |Leeds                     |
```

See the difference?

### Aggregates

First stop running queries in both terminals (CTRL-C).

Now, in `[Window 2]` type the following:

```sql
SET 'auto.offset.reset' = 'earliest';

CREATE TABLE PERSON_STATS WITH (VALUE_FORMAT='AVRO') AS
SELECT PERSON,
       LATEST_BY_OFFSET(LOCATION) AS LATEST_LOCATION,
       COUNT(*) AS LOCATION_CHANGES,
       COUNT_DISTINCT(LOCATION) AS UNIQUE_LOCATIONS
  FROM MOVEMENTS
GROUP BY PERSON
EMIT CHANGES;

SELECT PERSON, LATEST_LOCATION, LOCATION_CHANGES, UNIQUE_LOCATIONS
  FROM PERSON_STATS
  EMIT CHANGES;
```

You should see the following output:

```
+------------------+------------------+------------------+------------------+
|PERSON            |LATEST_LOCATION   |LOCATION_CHANGES  |UNIQUE_LOCATIONS  |
+------------------+------------------+------------------+------------------+
|robin             |Leeds             |7                 |5                 |

Press CTRL-C to interrupt
```

In `[Window 1]` insert some more data by typing the following:

```sql
INSERT INTO MOVEMENTS VALUES ('robin', 'Leeds');
INSERT INTO MOVEMENTS VALUES ('robin', 'London');
```

Notice the changes in `[Window 2]`:

```
+------------------+------------------+------------------+------------------+
|PERSON            |LATEST_LOCATION   |LOCATION_CHANGES  |UNIQUE_LOCATIONS  |
+------------------+------------------+------------------+------------------+
|robin             |Leeds             |7                 |5                 |
|robin             |London            |9                 |6                 |
```

Now, we will show a pull query in action. In `[Window 2]` stop the running query (CTRL-C) and type the following to run a pull query:

```sql
SELECT PERSON, LATEST_LOCATION, LOCATION_CHANGES, UNIQUE_LOCATIONS
  FROM PERSON_STATS
 WHERE PERSON='robin';
```

We can also run the pull query using the REST API. To do that, in `[Window 2]` type `exit` to exit the ksqlDB CLI. Then in your terminal prompt, type the following:

## [source,bash]

```bash
curl -s -X "POST" "http://localhost:8088/query" \
     -H "Content-Type: application/vnd.ksql.v1+json; charset=utf-8" \
     -d '{"ksql":"SELECT PERSON, LATEST_LOCATION, LOCATION_CHANGES, UNIQUE_LOCATIONS FROM PERSON_STATS WHERE PERSON='\''robin'\'';"}'|jq '.[].row'
```

You should see the following output:

```json
{
  "columns": ["robin", "London", 9, 6]
}
```

## Step 6: Stop Confluent Platform

When you are done working with this tutorial, you can stop Confluent Platform.

1.  Stop Confluent Platform:

        stop-kafka.sh

2.  Destroy the data in the Confluent Platform instance:.

        clear-kafka.sh
