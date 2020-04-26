---
layout: default
title:  "Quartz"
---

# Quartz
{:.no_toc}

* TOC
{:toc}

## Hello World

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

## Terminology
<dl>
    <dt>Scheduler</dt><dd>The main API for interacting with the scheduler.</dd>
    <dt>Job</dt><dd>An interface to be implemented by components that you wish to have executed by the Scheduler.</dd>
    <dt>JobDetail</dt><dd>Used to define instances of Jobs.</dd>
    <dt>JobBuilder</dt><dd>Used to define/build JobDetail instances, which define instances of Jobs.</dd>
    <dt>Trigger</dt><dd>A component that defines the schedule upon which a given Job will be executed.</dd>
    <dt>TriggerBuilder</dt><dd>Used to define/build Trigger instances.</dd>
</dl>
