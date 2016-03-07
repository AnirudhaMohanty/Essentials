# Foundational Processing with MapReduce

**GOAL** - Build and execute a simple MapReduce job to calculate the 
percentage of risky events by city

**PREREQUISITE** - [Ingesting Data into HDFS](../ingestion/README.md)

**SEE ALSO** - This demo is based on the publicly-available 
[Introducing Apache Hadoop to Java Developers](http://hortonworks.com/hadoop-tutorial/introducing-apache-hadoop-developers/ "Tutorial: Hadoop Intro to Java Developers") 
Hortonworks tutorial

**RECORDED DEMO**

<a href="http://www.youtube.com/watch?feature=player_embedded&v=nr6fmpSo6oI" target="_blank"><img src="http://img.youtube.com/vi/nr6fmpSo6oI/0.jpg" 
alt="Streaming into HDFS" width="240" height="180" border="10" /></a>

## Use Case

The input file for the MapReduce job is `/user/maria_dev/geolocation/geolocation.csv` that was loaded in the 
[Ingesting Data into HDFS](../ingestion/README.md) tutorial. Among other
data elements, the record definition includes an event type and the city
where the event occurred.

The goal is to output the total number of events as well as the total number
of risky events for each city across all drivers along with the calculated
"risky event average" by city.

The `Mapper` class will be responsible for raising the city name as the 
**key** and the event type as the **value**.

The `Reducer` class will calculate the total number of events for a given
city, the number of risky events within those, and an average percentage of
risk events for the city.  It will bundle these statistics together as the 
**value** with the **key** continuing to be the city name.

## Build Source Code

Create and HDFS home directory for `it1` and then construct the local 
Linux folders that will house the MapReduce source code.

```
HW10653-2:~ lmartin$ ssh root@127.0.0.1 -p 2222
root@127.0.0.1's password: 
Last login: Sun Mar  6 17:02:40 2016 from 10.0.2.2
[root@sandbox ~]# su - hdfs
[hdfs@sandbox ~]$ hdfs dfs -mkdir /user/it1
[hdfs@sandbox ~]$ hdfs dfs -chown it1 /user/it1
[hdfs@sandbox ~]$ exit
logout
[root@sandbox ~]# su - it1
[it1@sandbox ~]$ pwd
/home/it1
[it1@sandbox ~]$ mkdir mr-example
[it1@sandbox ~]$ cd mr-example/
[it1@sandbox mr-example]$ pwd
/home/it1/mr-example
[it1@sandbox mr-example]$ mkdir -p src/main/java/hwu/ess/mr
```

Create a `pom.xml` file in the `/home/it1/mr-example` Linux directory with the contents sourced from [pom.xml](./mr-example/pom.xml).

NOTE: An easy way to do that with this setup is as follows:

* Copy the source contents to the clipboard
* In the Linux folder where the file needs to be created, type `vi` followed by the file name
* Type `i` to go into insert mode
* Paste the source contents from the clipboard
* Hit ESC twice to exit insert mode
* Type `:` and then `wq` to write the file and then quick the editor

Type `cd ~/mr-example/src/main/java/hwu/ess/mr` to get into the Linux folder
where the Java source code needs to be created.  Using the same approach described
above, create the following source files from the content identified after each.

* `CityEventMapper.java` - [CityEventMapper.java](./mr-example/src/main/java/hwu/ess/mr/CityEventMapper.java)
* `RiskyAverageByCityReducer.java` - [RiskyAverageByCityReducer.java](./mr-example/src/main/java/hwu/ess/mr/RiskyAverageByCityReducer.java)
* `RiskyAverageCalculator.java` - [RiskyAverageCalculator.java](./mr-example/src/main/java/hwu/ess/mr/RiskyAverageCalculator.java)

NOTE: Take the time to review the `Mapper` and `Reducer` source code.

Type the following to kick off the build.

```
[it1@sandbox mr]$ cd ~/mr-example
[it1@sandbox mr-example]$ mvn install
```

It should end with _something_ like the following.

```
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 12.540 s
[INFO] Finished at: 2016-03-07T05:37:44+00:00
[INFO] Final Memory: 14M/188M
[INFO] ------------------------------------------------------------------------
```

## Run Job & Verify Results

Kick off the MapReduce job.

```
[it1@sandbox mr-example]$ cd ~/mr-example/target
[it1@sandbox target]$ yarn jar essentials-mapreduce-0.0.1-SNAPSHOT.jar hwu.ess.mr.RiskyAverageCalculator /user/maria_dev/geolocation/geolocation.csv avg-output
```

The output should include _something_ like the following, followed by a number
of counters, to indicate a successful completion. 

```
16/03/07 05:44:15 INFO mapreduce.Job: Job job_1457121884140_0061 running in uber mode : false
16/03/07 05:44:15 INFO mapreduce.Job:  map 0% reduce 0%
16/03/07 05:44:34 INFO mapreduce.Job:  map 100% reduce 0%
16/03/07 05:44:55 INFO mapreduce.Job:  map 100% reduce 100%
16/03/07 05:44:57 INFO mapreduce.Job: Job job_1457121884140_0061 completed successfully
16/03/07 05:44:57 INFO mapreduce.Job: Counters: 49
```

Type `hdfs dfs -cat avg-output/part-r-00000` to view the job output which
should **begin** with _something_ like the following.

```
Antelope	{315,19,0.06031746031746032}
Apple Valley	{315,25,0.07936507936507936}
Aptos	{236,9,0.038135593220338986}
Arbuckle	{236,21,0.08898305084745763}
```

These results above indicate that the city of Arbuckle had 21 risky events out
of a total of 236 which yielded a 8.9% "risky event average".