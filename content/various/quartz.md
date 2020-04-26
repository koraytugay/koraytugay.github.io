---
layout: default
title:  "Quartz"
---

# Quartz
{:.no_toc}

* TOC
{:toc}

## Hello World
This is a very simple hello world using `RAMJobStore`. 


### Files

Directory Layout

```
.
├── pom.xml
└── src
    └── main
        ├── java
        │   └── biz
        │       └── tugay
        │           ├── MyQuartzJob.java
        │           └── QuartzHelloWorld.java
        └── resources
            └── quartz.properties
```

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>biz.tugay</groupId>
    <artifactId>my-quartz</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>org.quartz-scheduler</groupId>
            <artifactId>quartz</artifactId>
            <version>2.3.2</version>
        </dependency>
    </dependencies>
</project>
```

quartz.properties

```properties
org.quartz.scheduler.instanceName = My-Scheduler-Instance
org.quartz.threadPool.threadCount = 1
org.quartz.jobStore.class = org.quartz.simpl.RAMJobStore
```

MyQuartzJob.java

```java
import org.quartz.Job;
import org.quartz.JobExecutionContext;

public class MyQuartzJob implements Job {
    public void execute(JobExecutionContext context) {
        System.out.println("hello world");
    }
}
```

QuartzHelloWorld.java

```java
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;

public class QuartzHelloWorld {
    public static void main(String[] args) throws Exception {
        new QuartzHelloWorld().run();
    }

    void run() throws Exception {
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();

        JobDetail job = JobBuilder.newJob(MyQuartzJob.class)
                .withIdentity("job1", "group1")
                .build();

        Trigger trigger = TriggerBuilder.newTrigger()
                .withIdentity("trigger1", "group1")
                .startNow()
                .withSchedule(SimpleScheduleBuilder
                        .simpleSchedule()
                        .withIntervalInMilliseconds(2000)
                        .repeatForever())
                .build();

        scheduler.scheduleJob(job, trigger);

        scheduler.start();
        Thread.sleep(5000);
        scheduler.shutdown();
    }
}
```

## Example with JobStoreTX
Making the above example work with a PostgreSQL database. Create tables using the scripts [here](https://github.com/quartz-scheduler/quartz/blob/v2.3.2/quartz-core/src/main/resources/org/quartz/impl/jdbcjobstore/tables_postgres.sql). Database I used in this example is named `my-quartz`. 

### Files

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>biz.tugay</groupId>
    <artifactId>my-quartz</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>org.quartz-scheduler</groupId>
            <artifactId>quartz</artifactId>
            <version>2.3.2</version>
        </dependency>
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <version>42.2.12</version>
        </dependency>
    </dependencies>
</project>
```

quartz.properties

```properties
org.quartz.scheduler.instanceName=My-Scheduler-Instance
org.quartz.threadPool.threadCount=1
org.quartz.jobStore.class=org.quartz.impl.jdbcjobstore.JobStoreTX

org.quartz.jobStore.dataSource=myDS

org.quartz.jobStore.driverDelegateClass=org.quartz.impl.jdbcjobstore.PostgreSQLDelegate
org.quartz.dataSource.myDS.driver=org.postgresql.Driver
org.quartz.dataSource.myDS.URL=jdbc:postgresql://localhost:5432/my-quartz
org.quartz.dataSource.myDS.user=postgres
org.quartz.dataSource.myDS.password=""
```

QuartzHelloWorld.java

```java
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;

public class QuartzHelloWorld {
    public static void main(String[] args) throws Exception {
        new QuartzHelloWorld().run();
    }

    void run() throws Exception {
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();

        JobKey jobKey = new JobKey("job1", "group1");

        // You can not keep scheduling the same job since job now lives in DB
        if (!scheduler.checkExists(jobKey)) {
            JobDetail job = JobBuilder.newJob(MyQuartzJob.class)
                    .withIdentity(jobKey)
                    .build();

            Trigger trigger = TriggerBuilder.newTrigger()
                    .withIdentity("trigger1", "group1")
                    .startNow()
                    .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                            .withIntervalInMilliseconds(2000)
                            .withMisfireHandlingInstructionNextWithExistingCount()
                            .repeatForever())
                    .build();

            scheduler.scheduleJob(job, trigger);
        }

        scheduler.start();
        Thread.sleep(5000);
        scheduler.shutdown();
    }
}
```

## Terminology
<dl>
    <dt>Scheduler</dt><dd>The main API for interacting with the scheduler.</dd>
    <dt>Job</dt><dd>An interface to be implemented by components that you wish to have executed by the Scheduler.</dd>
    <dt>JobDetail</dt><dd>Used to define instances of Jobs.</dd>
    <dt>JobBuilder</dt><dd>Used to define/build JobDetail instances, which define instances of Jobs.</dd>
    <dt>Trigger</dt><dd>A component that defines the schedule upon which a given Job will be executed.</dd>
    <dt>TriggerBuilder</dt><dd>Used to define/build Trigger instances.</dd>
</dl>

## References
- [How to Setup Databases](https://github.com/quartz-scheduler/quartz/wiki/How-to-Setup-Databases)
- [PostgreSQL Schema](https://github.com/quartz-scheduler/quartz/blob/v2.3.2/quartz-core/src/main/resources/org/quartz/impl/jdbcjobstore/tables_postgres.sql)
