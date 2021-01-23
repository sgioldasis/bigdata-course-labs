# Hadoop program with Maven on IntelliJ

In this tutorial we will understand a way you can write and test your Hadoop Program with Maven on IntelliJ without configuring Hadoop environment on your own machine.

## CREATING A NEW PROJECT

Start IntelliJ, click `New Project` and choose `Maven` then click `Next`

![Image for
post](images/001_new_maven_project.png)

Set your project `Name`, `Location`, `GroupId`, and `ArtifactId`. Leave the version untouched and click `Finish`.

![Image for
post](images/002_maven_project_coordinates.png)

_Now we are ready to configure our project dependencies_

# Configuring Dependencies

Open the `pom.xml` file.

![Image for
post](images/003_pom_xml.png)

In your `pom.xml` file copy the following blocks before the project closing tag `</project>`

```xml
<repositories>
    <repository>
        <id>apache</id>
        <url>http://maven.apache.org</url>
    </repository>
</repositories>
<dependencies>
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

The final `pom.xml` file should look like the following

![Image for
post](images/003_pom_xml_edited.png)

Below is the full `pom.xml` file

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>MyWordCount</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>
    <repositories>
        <repository>
            <id>apache</id>
            <url>http://maven.apache.org</url>
        </repository>
    </repositories>
    <dependencies>
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

</project>
```

_Now we are ready to create classes for our sample test project MyWordCount._

## Creating a WordCount class

On the `Project` pane, right click on the project folder and select `New -> Directory`. On the `New Directory` pop-up menu select `src/main/java`

![Image for
post](images/004_new_directory.png)

Right click the `src -> main -> java` package and create a new Java class

![Image for
post](images/005_new_java_class.png)

Name the class `WordCount` and click and enter

![Image for
post](images/006_name_java_class.png)

Paste the following Java code in your `WordCount` class.

```java
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

![Image for
post](images/007_write_java_class.png)

![Image for
post](images/007_write_java_class2.png)

The wordCount class code includes both the main method, the map class
and the reduce class. It scans all text files in the folder defined by
the first argument, and output the frequencies of all words into a
folder defined by the second argument.

## We are almost ready to run the program...

First we must create our text input file. In your project package create
new folder and name it `input`. Then within the `input` folder/directory
create your txt file (eg. `SampleFile.txt`) or drag one if you already have.

![Image for
post](images/008_create_input_folder.png)

![Image for
post](images/008_create_input_folder2.png)

![Image for
post](images/008_create_input_folder3.png)

![Image for
post](images/009_create_input_file.png)

![Image for
post](images/009_create_input_file2.png)

Copy and paste some text within this file. Make sure you have some repeating words in the text.

![Image for
post](images/009_create_input_file3.png)

_Almost ready be patient…_

We have not set our program arguments. Select `Add Configuration` on the top right.

![Image for
post](images/010_add_configuration.png)

Add a new Application Configuration by selecting “+” then Application.

![Image for
post](images/010_add_configuration2.png)

![Image for
post](images/010_add_configuration3.png)

Set the `Main class` be `WordCount`, set
`CLI arguments` be `input output`. This allows
the program to read from the `input` folder and save the result to the `output` folder. Do not create the `output`
folder, as Hadoop will create the folder automatically. If the folder exists, Hadoop will raise an exception. When done select `Apply` then `OK`.

![Image for
post](images/010_add_configuration4.png)

_Now we are ready to run our program_….

Select `Run` →` Run 'WordCount'` to run the Hadoop program. If you want to re-run the program, delete the `output` folder before re-running otherwise you will get an exception.

![Image for
post](images/011_run_wordcount.png)

An output folder will appear. On each run your results are saved in `output → part-r-00000`.

![Image for
post](images/012_wordcount_result.png)
