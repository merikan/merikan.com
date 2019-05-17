---
title: "JVM in a Container"
date: 2019-04-03T16:45:26+02:00
author: Peter

subtitle: "How different Java version fits in a container"
description: "How different Java version fits in a container"
#image: ""
tags: ["java", "docker"]
categories: ["technology"]
showtoc: true
lastmod: 2019-05-17T14:14:39+0200
---

# tl:dr
Until Java 8u131 and Java 9 the JVM did not recognize memory or cpu limits set by the container. First implementation was a experimental feature and had its flaws but in Java 10, memory limits are automatically recognized and enforced. This feature was then backported to Java-8u191.

# Introduction
Yesterday I had to troubleshoot a Java application in a Kubernetes cluster. The application acted very strange and it looked like we had a 
out of memory situation. The docker image had a hard coded value for maximum and minimum heap size and of course it was not good, I can only blame it on the post-it note that had fallen down. Anyhow, it was time to implement it as an environment variable instead so I could set the value 
in the manifest at deploy time. 

The application uses Java 8 and I did know about the `UnlockExperimentalVMOptions` and `UseCGroupMemoryLimitForHeap` flags 
but by an accident I saw that Java 8 Update 191 has backported the Java 10 feature and I will show you that later on. 

Until Java 8u131 and Java 9 the JVM did not recognize memory or cpu limits set by the container. In Java 10, memory limits are automatically recognized and enforced. This feature was then backported to Java-8u191.

# Java 8u131 and Java 9
[Java 8u131](https://www.oracle.com/technetwork/java/javase/8u131-relnotes-3565278.html) first implemented a experimental feature called `UseCGroupMemoryLimitForHeap`. It was a first attempt and had its flaws. Let's have a look what it looks 
like in Java 8 before Java 8u131.

Let's see how Java 8u121 looks like if we start the container with 100MB available memory 
```bash
➜ docker run -m 100MB openjdk:8u121 java -XshowSettings:vm -version
VM settings:
    Max. Heap Size (Estimated): 1.73G
    Ergonomics Machine Class: server
    Using VM: OpenJDK 64-Bit Server VM

openjdk version "1.8.0_121"
OpenJDK Runtime Environment (build 1.8.0_121-8u121-b13-1~bpo8+1-b13)
OpenJDK 64-Bit Server VM (build 25.121-b13, mixed mode)
```
So the container has 100MB available memory and the max heap size was set to 1.73G. This tells us that the JVM has no clue that it is running in container 
with 100MB available memory.

Jdk8u131+ and 9 [support detecting cpu and memory limits](https://blogs.oracle.com/java-platform-group/java-se-support-for-docker-cpu-and-memory-limits) to set heap and core usage. So let's first start a Java 8u131 image and see how it behaves with 100MB available memory.

```bash
➜ docker run -m 100MB openjdk:8u131 java \
  -XshowSettings:vm -version

VM settings:
    Max. Heap Size (Estimated): 1.73G
    Ergonomics Machine Class: server
    Using VM: OpenJDK 64-Bit Server VM

openjdk version "1.8.0_131"
OpenJDK Runtime Environment (build 1.8.0_131-8u131-b11-2-b11)
OpenJDK 64-Bit Server VM (build 25.131-b11, mixed mode)
```
This doesn’t look so good either since it's still using Max Heap Size 1.73G when we only have 100MB available memory.

Let’s use the  `XX:+UnlockExperimentalVMOptions` flag so we can use the `-XX:+UseCGroupMemoryLimitForHeap` flag so the JVM can check the Control Group memory limit and calculate a maximum heap size.
```bash
➜ docker run -m 100MB openjdk:8u131 java \
  -XX:+UnlockExperimentalVMOptions \
  -XX:+UseCGroupMemoryLimitForHeap \
  -XshowSettings:vm -version
VM settings:
    Max. Heap Size (Estimated): 44.50M
    Ergonomics Machine Class: server
    Using VM: OpenJDK 64-Bit Server VM

openjdk version "1.8.0_131"
OpenJDK Runtime Environment (build 1.8.0_131-8u131-b11-2-b11)
OpenJDK 64-Bit Server VM (build 25.131-b11, mixed mode)
```
That is better and we will not use more memory than is available. As you can see we will only use 44.50MB out of 100MB but it’s not good enough.
We can use the `-XX:MaxRAMFraction` flag to help calculate a better heap size. 
```bash
➜ docker run -m 100MB openjdk:8u131 java \
          -XX:+UnlockExperimentalVMOptions \
          -XX:+PrintFlagsFinal -version \
          | grep -E "UnlockExperimentalVMOptions | UseCGroupMemoryLimitForHeap | MaxRAMFraction | InitialRAMPercentage | MaxRAMPercentage | MinRAMPercentage"
    uintx MaxRAMFraction                            = 4                                   {product}
     bool UnlockExperimentalVMOptions              := true                                {experimental}
     bool UseCGroupMemoryLimitForHeap               = false                               {experimental}
```

The default value for `MaxRAMFraction` is `4` but unfortunately it’s a fraction rather than a percentage making it hard to set a value that would make efficient use of the available memory
But why don’t we just set `MaxRAMFraction` to `1` and use 100% of available memory, isn't that what we want? Probably not, because there might be other processes running in the container or that we want to connect to the container with a shell to do troubleshooting or just inspect the container.   
Let's analyze the use of `MaxRAMFraction` and start the container with memory limit set to 1GB, the result is as follows:

> -XX:MaxRAMFraction=1 => maximum heap size = 1GB   
> -XX:MaxRAMFraction=2 => maximum heap size ~ 500MB   
> -XX:MaxRAMFraction=3 => maximum heap size ~ 333MB   
> -XX:MaxRAMFraction=4 => maximum heap size ~ 250MB   

This is what it looks like.

```bash
# MaxRAMFraction default value (4)
➜ docker run -m 1GB openjdk:8u131 java \
  -XX:+UnlockExperimentalVMOptions \
  -XX:+UseCGroupMemoryLimitForHeap \
  -XshowSettings:vm -version
VM settings:
    Max. Heap Size (Estimated): 228.00M
    Ergonomics Machine Class: server
    Using VM: OpenJDK 64-Bit Server VM

# MaxRAMFraction=1
➜ docker run -m 1GB openjdk:8u131 java \
  -XX:+UnlockExperimentalVMOptions \
  -XX:+UseCGroupMemoryLimitForHeap \
  -XX:MaxRAMFraction=1 \
  -XshowSettings:vm -version
VM settings:
    Max. Heap Size (Estimated): 910.50M
    Ergonomics Machine Class: server
    Using VM: OpenJDK 64-Bit Server VM

# MaxRAMFraction=2
➜ docker run -m 1GB openjdk:8u131 java \
  -XX:+UnlockExperimentalVMOptions \
  -XX:+UseCGroupMemoryLimitForHeap \
  -XX:MaxRAMFraction=2 \
  -XshowSettings:vm -version
VM settings:
    Max. Heap Size (Estimated): 455.50M
    Ergonomics Machine Class: server
    Using VM: OpenJDK 64-Bit Server VM

# MaxRAMFraction=3
➜ docker run -m 1GB openjdk:8u131 java \
  -XX:+UnlockExperimentalVMOptions \
  -XX:+UseCGroupMemoryLimitForHeap \
  -XX:MaxRAMFraction=3 \
  -XshowSettings:vm -version
VM settings:
    Max. Heap Size (Estimated): 304.00M
    Ergonomics Machine Class: server
    Using VM: OpenJDK 64-Bit Server VM

```

Conclusion is that `MaxRAMFraction` is hard to work with since it’s a fraction so you must choose your value wisely.

# Java 10+
With Java 10 came better support for Container environment. If you run your Java application in a Linux container the JVM will automatically detect the Control Group memory limit with the `UseContainerSupport` option.
You can then control the memory with the following options, `InitialRAMPercentage`, `MaxRAMPercentage` and `MinRAMPercentage`. As you can see we have percentage instead of fraction, which is nice and much more useful.

Later on you will see that this  behavior was backported to Java 8u191

Let’s look at the default values for the new java version
```bash
➜ docker run -m 1GB openjdk:10 java \
          -XX:+PrintFlagsFinal -version \
          | grep -E "UseContainerSupport | InitialRAMPercentage | MaxRAMPercentage | MinRAMPercentage"

   double InitialRAMPercentage                     = 1.562500                                 {product} {default}
   double MaxRAMPercentage                         = 25.000000                                {product} {default}
   double MinRAMPercentage                         = 50.000000                                {product} {default}
     bool UseContainerSupport                      = true                                     {product} {default}

openjdk version "10.0.2" 2018-07-17
OpenJDK Runtime Environment (build 10.0.2+13-Debian-2)
OpenJDK 64-Bit Server VM (build 10.0.2+13-Debian-2, mixed mode)
```

We can see that `UseContainerSupport` is activated by default. It looks like `MaxRAMPercentage` is `25%` and `MinRAMPercentage` is `50%`.
Let’s see what heap size is calculated when we give the container 1GB of memory.

```bash
➜ docker run -m 1GB openjdk:10 java \
          -XshowSettings:vm \
          -version

VM settings:
    Max. Heap Size (Estimated): 247.50M
    Using VM: OpenJDK 64-Bit Server VM
```

The JVM calculated 247.50M when we have 1GB available and it’s because the default value for MaxRAMPercentage is 25%. Why they decided to use 25% is a mystery for me and MinRAMPercentage is 50%.
Let’s bump up RAMPercentage a bit
```bash
➜ docker run -m 1GB openjdk:10 java \
          -XX:MinRAMPercentage=50 \
          -XX:MaxRAMPercentage=80 \
          -XshowSettings:vm \
          -version

VM settings:
    Max. Heap Size (Estimated): 792.69M
    Using VM: OpenJDK 64-Bit Server VM
```
That is much better and with this configuration we managed to control our JVM to start at 500MB and then grow to maximum 792.69MB

# Backported to Java 8
As we said earlier, option `UseContainerSupport` was [backported to Java 8u191](https://www.oracle.com/technetwork/java/javase/8u191-relnotes-5032181.html) and activated by default.
```bash
➜ docker run -m 1GB openjdk:8u191-alpine java \
           -XX:+PrintFlagsFinal -version \
          | grep -E "UseContainerSupport | InitialRAMPercentage | MaxRAMPercentage | MinRAMPercentage"


   double InitialRAMPercentage                      = 1.562500                            {product}
   double MaxRAMPercentage                          = 25.000000                           {product}
   double MinRAMPercentage                          = 50.000000                           {product}
     bool UseContainerSupport                       = true                                {product}
```
Let’s use those options as we did with Java 10
```bash
➜ docker run -m 1GB openjdk:8u191-alpine java \
          -XX:MinRAMPercentage=50 \
          -XX:MaxRAMPercentage=80 \
          -XshowSettings:vm \
          -version

Improperly specified VM option 'MinRAMPercentage=50'
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
```
What?!  What is this? `Improperly specified VM option 'MinRAMPercentage=50’`?
We know that RAMPercentage is a double so let’s add the decimal value.

```bash
➜ docker run -m 1GB openjdk:8u191-alpine java \
          -XX:MinRAMPercentage=50.0 \
          -XX:MaxRAMPercentage=80.0 \
          -XshowSettings:vm \
          -version

VM settings:
    Max. Heap Size (Estimated): 792.69M
    Ergonomics Machine Class: server
    Using VM: OpenJDK 64-Bit Server VM
```
Nice, much better.  We can now use the `UseContainerSupport` option with our Java 8u191+ based container.
But be aware that experimental feature `UseCGroupMemoryLimitForHeap` is still available but deprecated and you should stop using that immediately.


# Set the memory cap
So we now know that the JVM is container aware and we should set the provided amount of memory in our runtime environment. You are probable running your apps in Kubernetes and this how we set the memory in the Kubernetes manifest.
```bash
resources: 
    limits:
      memory: 512Mi
    requests:
      memory: 256Mi
```
`Limits` is the maximum memory and `requests` is the minimum memory.

# Bonus
When running a JVM in a docker container it is probably wise to use the `HeapDumpOnOutOfMemoryError` option so if you ever run out of memmory the jvm will write a dump of the heap to disk.

By default the heap dump is created in a file called java_pid.hprof in the working directory of the VM. You can specify an alternative file name or directory with the -XX:HeapDumpPath= option. For example -XX:HeapDumpPath=/disk2/dumps will cause the heap dump to be generated in the /disk2/dumps directory.

Your working directory can be found via the pwdx <PID> command. First do a ps -ef | grep java, find your PID for your java app, then run pwdx <PID>. It'll tell you the working directory.

This is how it could look like in a deployment manifest for Kubernetes.
```bash
...
env:
- name: JAVA_OPTS
  value: "-XX:MinRAMPercentage=60.0 -XX:MaxRAMPercentage=90.0 -XX:-HeapDumpOnOutOfMemoryError"
...
resources: 
    limits:
        memory: 512Mi
    requests:
        memory: 256Mi
```


That's it. Now you know how to set the Heap Size for a Java Container.

Hopefully I have enough energy one day so I can write a blog post about how I build a docker image and provides environment variables when starting the java application. 