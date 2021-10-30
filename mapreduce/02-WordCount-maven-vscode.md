# MapReduce with Docker and VS Code

In this tutorial you will explore a way you can write and test a WordCount MapReduce program and then build it with Maven on VS Code without installing anything (but git and docker) on your own machine. You will also find out how you can run this program on a Hadoop cluster.

Before you run the lab make sure you have cloned the following Git repositories. 

- [Bigdata Docker Infrastructure](https://github.com/sgioldasis/bigdata-docker-infra)
- [Bigdata Course Labs](https://github.com/sgioldasis/bigdata-course-labs)

If you don't have git you can click the above links, click on the green `Code` button and choose `Download ZIP`. You then need to unzip into the following folders:

- /tmp/bigdata-docker-infra
- /tmp/bigdata-course-labs


Alternatively, ff you have `git` installed you can just open a terminal and run the following:
```
cd /tmp
git clone https://github.com/sgioldasis/bigdata-docker-infra.git
git clone https://github.com/sgioldasis/bigdata-course-labs.git
```

Now it's time to start the Hadoop cluster. To do that run the following in your terminal:

```
cd /tmp/bigdata-docker-infra/hdfs/docker-hadoop-cluster
docker-compose up -d
```

## Opening the WordCount project in VS Code

To open the WordCount project in VS Code you need to type the following:

```
cd /tmp/bigdata-course-labs/mapreduce/wordcount/
code .
```

Once VS Code is started, in the lower right corner you will see this:

![Image for
post](images/101_Reopen_in_container.png)

Click `Reopen in Container` and after the container has built you will see this:

![Image for
post](images/102_Project_reopened_in_container.png)

_Notice in the lower left corner that VS Code is now connected to `Dev Container: Java 8`_

# Check the `pom.xml` file

Open the `pom.xml` file.

![Image for
post](images/103_pom_xml.png)

In your `pom.xml` file notice the following Hadoop dependencies:

```xml
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-core</artifactId>
        <version>1.2.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>3.2.0</version>
    </dependency>    
  </dependencies>
```

Also notice the Java version:

```xml
  <properties>
    <jdk.version>8</jdk.version>
    <maven.compiler.source>8</maven.compiler.source>
    <maven.compiler.target>8</maven.compiler.target>
  </properties>
```

Below is the full `pom.xml` file

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>wordcount</artifactId>
  <packaging>jar</packaging>
  <version>1.0</version>
  <name>wordcount</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.13.1</version>
      <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-core</artifactId>
        <version>1.2.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>3.2.0</version>
    </dependency>    
  </dependencies>
  <properties>
    <jdk.version>8</jdk.version>
    <maven.compiler.source>8</maven.compiler.source>
    <maven.compiler.target>8</maven.compiler.target>
  </properties>
  <build>
    <pluginManagement>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>3.0.0-M3</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>
```

_Now we are ready to create classes for our sample test project MyWordCount._

## Check the `WordCount` class

Open the left pane, expand `src/main/java/com/mycompany/app` folder and click on the `WordCount.java` file to open it.

![Image for
post](images/104_WordCount.png)

The file should contain the following code:

```java
package com.mycompany.app;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class WordCount {

    public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable> {

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
        ) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
                word.set(itr.nextToken());
                context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text, IntWritable, Text, IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                           Context context
        ) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
                sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```


The wordCount class code includes both the main method, the map class and the reduce class. It scans all text files in the folder defined by the first argument, and output the frequencies of all words into a folder defined by the second argument.

## Check the `input` folder

Now it is time to check our sample input file. There should be a text file within the `input` folder/directory called `SampleFile.txt`. Open this file and have a look at the contents:

![Image for
post](images/105_Input_file.png)

You can also put your own sample text within this file. Make sure that you have some repeating words in the text and that you save your changes.

## Run locally

Now it is time to run our program to count the words of the input file. To do this you first click on the `WordCount.java` tab. On the top right you should see a `Run` triangle. If you hover over it you should see `Run Java`. Click the triangle and you should see the program running and producing output in the `DEBUG CONSOLE` window:

![Image for
post](images/106_Run_locally.png)

The program will create a new `output` folder. Inside this folder you will find a file `part-r-00000`. Click on it and you will see the word counts for each word:

![Image for
post](images/107_Run_locally_output.png)


## Build a JAR file

Now that we have tested our program locally it is time to build it and package it in a JAR file so we can run it in a Hadoop cluster.

We do that by selecting `MAVEN` on the left pane, then expanding `Lifecycle` under `wordcount` and clicking the `Run` arrow on the right of `package` lifecycle method. We should see output produced in the terminal window and after some time the message `BUILD SUCCESS` should appear:

![Image for
post](images/108_Maven_Build.png)

At this point you should see also check that the file `wordcount-1.0.jar` has been created in the `target` folder.

At this point we are done with development, build and packaging so we can close VS Code.

## Running on the Hadoop cluster

Now we want to run our program on the Hadoop cluster. First thing we need to do is copy our application's JAR file into the `master` server of the cluster:

```bash
cd /tmp/bigdata-course-labs/mapreduce/wordcount/

docker cp target/wordcount-1.0.jar master:/tmp
```

We also need to copy our application's input file into the `master` server of the cluster:

```bash
docker cp input/SampleFile.txt master:/tmp
```

Now we can get a bash shell into the `master` server of the cluster:

```
docker exec -it master bash
```

Now we can create an `input` folder in HDFS and copy the `SampleFile.txt` file into it.

```
hadoop fs -mkdir /input
hadoop fs -copyFromLocal /tmp/SampleFile.txt /input
```

Finally, we can run our application:

```
hadoop jar /tmp/wordcount-1.0.jar com.mycompany.app.WordCount /input /output
```

When the application has finished, we can check the output:

```
hadoop fs -ls /output
hadoop fs -cat /output/part-r-00000
```

Now you can exit the container:

```
exit
```

> **Congratulations**: You have successfully used VS Code to create and test a MapReduce WordCount application and run it in a Hadoop cluster. 


## Cleanup

Before you run away make sure you cleanup. To do that open a terminal and run the following:

```
cd /tmp/bigdata-docker-infra/hdfs/docker-hadoop-cluster
docker-compose down
docker rm $(docker ps -aq)
docker ps -a
```


