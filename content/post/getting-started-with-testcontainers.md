---
title: "Getting started with Testcontainers"
date: 2020-10-06T08:46:18+02:00
draft: true
author: Peter

subtitle: "What is Testcontainers and why should I use it?"
description: ""
image: "/files/getting-started-with-testcontainers/code-block.png"
showtoc: true
tags: ["testcontainers", "java","spring-boot"]
categories: ["technology"]

---
This is the first part of a two part series about [Testcontainers](https://www.testcontainers.org/). In this first part I will explain what it is, 
what problems it tries to solve, how it works and finally how you can use it in your own projects. In the second part we will see if we can 
reduce startup time for our Testcontainers.


## Introduction
Earlier this spring I held a skills meeting for my colleagues that was about [JUnit 5](https://junit.org/junit5) and [Testcontainers](https://www.testcontainers.org/). 
I should have written a blog post about it but for various reasons it was not done, but now it's finally time and it is about Testcontainers. This will be a trilogy in 
two parts, Getting started with Testcontainers and  [Speed up your Testcontainers tests]({{< ref "speed-up-your-testcontainers-tests" >}}).

### Source code
The full source code for my example project can be found here: [https://github.com/merikan/testcontainers-demo)](https://github.com/merikan/testcontainers-demo) and it has two directories named `origin` and `solution`.

## What is Testcontainers
So let's start with the obvious question; what is Testcontainers and what are the problems it is trying to solve. Or is it just another cool tool in our development stack?

On Testcontainers home page you can read the following;
>Testcontainers is a Java library that supports JUnit tests, providing lightweight, throwaway instances of common databases, Selenium web browsers, or anything else that can run in a Docker container.   

How cool isn't that? This means that if the software can run inside a docker container we can use it in our tests.

Testcontainers was started as an open source project for the Java platform ([testcontainers-java](https://github.com/testcontainers/testcontainers-java)) but has gradually received support for more languages such as Go ([testcontainers-go](https://golang.testcontainers.org/)), Rust ([testcontainers-rust](https://github.com/testcontainers/testcontainers-rs)), dotnet ([testcontainers-dotnet](https://github.com/testcontainers/testcontainers-dotnet)), node ([testcontainers-node](https://github.com/testcontainers/testcontainers-node)) and a few more. There are varying quality of the projects but many of them can be used without problems in your applications. 


_A personal note; After using Testcontainers a couple of years, for both my Java and Go projects, I do think they should restructure the Testcontainers main site and embrase the other projects and let them become first class citizens. Projects not ready for prime time could be incubators._


## What problem does Testcontainers solve

So back to the question that we should ask; What are the problems that Testcontainers could help us solve?

If we first take a look at different test strategies and different layers, usually explained as a [test-pyramide](https://martinfowler.com/articles/practical-test-pyramid.html), that Mike Cohn described in his book [Succeeding with Agile](https://www.amazon.com/Succeeding-Agile-Software-Development-Using/dp/0321579364). 
![Test Pyramide](/files/getting-started-with-testcontainers/test-pyramide.png)

At the bottom we have unit tests. Must of us can agree on what a [unit test](https://www.martinfowler.com/bliki/UnitTest.html) is in a [TDD](https://www.martinfowler.com/bliki/TestDrivenDevelopment.html) development workflow. A unit test is _"a way of testing a unit, the smallest piece of code that can be logically isolated in a system"_. It focuses on a single component and replaces all dependencies this component interacts with. In Java this is usually a Class and all collaborators are replaced with [Test Doubles](https://en.wikipedia.org/wiki/Test_double) with the help of libaries like [Mockito](https://site.mockito.org/) and [EasyMock](https://easymock.org/). 

If we look above the Unit test layer in the test-pyramid we will find some type of integration test which means that we will have more then one component and sometimes we need to integrate with a external resource, a.k.a [backing service](https://12factor.net/backing-services). A backing service is any service that our application or module consumes over the network and it is now Testcontainers shines. Before we look at Testcontainers, let's first discuss some of the problems we've had with external resources. 

## Problems 
Over the years we have tried to solve the problem of interacting with external resources in different ways, depending on the type of backing service. To make these resources available it's a balance between cost (license, hardware, etc.) and the degree of automation and ease of setting them up and maintaining them. Then several issues arise such as; should we use a shared resource on a remote server, or a locally installed process or a embedded in-memory service? Each solution to our first problem has its own problems and challenges, below you can see some of these challenges.

**A shared resource on a remote server;**   
* is usually expensive as it requires one or more extra servers and the environment must be maintained.
* it is shared by several people and maybe different projects which requires quite a lot of administration
* you will probably need to provide the service in several versions which makes it more cumbersome

**Locally installed resource;**
* can be cumbersome to install and maintain. Of course, we can use virtual servers locally to facilitate this work.
* depending on the license, it can be expensive

**Embedded and In-Memory;** 
* usually more lightweight than other solutions but is only available for a few types
* not the same codebase as the real resource, it only emulates the real target environment
* we are not testing with real connections over the wire
* not configured as target environment, security, cluster etc.

Every strategy has it's own pros and cons and today, embedded resource is probably the most widely used method. It solved many of the previous problems but unfortunately it introduced some new. Two problems that I have encountered from time to time are, it only emulates the target resource, H2 -> MariaDb and Flabdoodle -> MongoDb, and the second is the security settings are different from the target environment. We will of course find these differences in the end, but the earlier in the development process the better.

### Testcontainers to the rescue

One great solution to many of these problems is [Testcontainers](https://www.testcontainers.org/). With Testcontainers we can start and stop one or more docker containers with the desired resource used in our production environment, with a configuration as similar as possible. Later on I will show how easy it is to use Testcontainers in a Spring Boot application with Junit 5, but it can of course be used in any Java application.

Not everyone agrees that you should start up resources locally, but it usually depends on what conditions you have in your project. If your system landscape is based on microservice architecture and perhaps consists of hundreds of microservices where some are needed as a backing service, it is probably not conceivable to try to start these locally. However, most of us are not in that situation with so many microservices that form a system landscape, so for us Testcontainers probably works very well. If you are more interested in this topic then [Cindy Sridharan](https://twitter.com/copyconstruct) has written several great blog posts on the topic, [Testing Microservices, the sane way](https://medium.com/@copyconstruct/testing-microservices-the-sane-way-9bb31d158c16) and some little more controversial blog posts about [testing in production](https://medium.com/@copyconstruct/testing-in-production-the-safe-way-18ca102d0ef1), which I really appreciate and find interesting.

## More about Testcontainers

Now we now that Testcontainers is a open source project that we can use in our tests to start and stop anything that can run in a docker container. Let's delve deeper into the subject.

### Type of Testcontainer

![modules](/files/getting-started-with-testcontainers/modules.png)

Testontainers consist of two types, [GenericContainer](https://www.testcontainers.org/features/creating_container/) and Specialized Containers aka. [Modules](https://github.com/testcontainers/testcontainers-java/tree/master/modules). GenericContainer is used as basis to create your own Testcontainer of any docker container. A module is a specialized container that is designed for a specific product such as MariaDb, Kafka etc. and we can see them as specialized Testcontainers with extended convenience methods and functionality. One module that differs from the others is DockerCompose, that allows you to start several containers at the same time and of the type you want.


### How does it work
All you need to do in your tests is to declare that you want to use one or more Testcontainers, of a certain type. Then the Testcontainers framework will download the image, if it doesn't exists locally, and handle its lifecycle during the tests by starting the container, make sure it is ready and then stop the container after all tests have finished. This is done by annotating the test class with `@Testcontainers`.

```
@Testcontainers
public class TodoServiceImplIT {
```

The next step is to add the `@Container` annotation to specify which Testcontainer we want to use and to be started by the Testcontainer framework. To make sure we have reproducible builds we should never rely on `latest`, instead we should always declare what version we want to use and in this case it is `mariadb:10.3.6` and `redis:5.0.5`
```
@Container
public static MariaDBContainer mariaDB = new MariaDBContainer("mariadb:10.3.6")

@Container
public static GenericContainer redis = new GenericContainer("redis:5.0.5")
                                        .withExposedPorts(6379);
```
This is one way to start Testcontainers but later on I will show you another and, in my opinion, better way to start them.

Our containers are started with dynamic port numbers and some other default values. You can of course set these and other values yourself when you start a Testcontainer. You can then easily retrieve information about the started container through various methods such as:
```
@DynamicPropertySource
static void properties(DynamicPropertyRegistry registry) {
    registry.add("spring.datasource.url", mariadb::getJdbcUrl);
    registry.add("spring.datasource.username", mariadb::getUsername);
    registry.add("spring.datasource.password", mariadb::getPassword);
    registry.add("spring.redis.host", redis::getContainerIpAddress);
    registry.add("spring.redis.port",  () -> redis.getMappedPort(6379));
}
```

When all the tests have finished our little friend, named [Ryuk](https://github.com/testcontainers/moby-ryuk), will make sure that your Testcontainers are taken down. Ryuk is started as a container at the same time as your other Testcontainers are started.

### A better way

Earlier I said that there was a better way to start our Testcontainers instead of using the `@Container` annotation. 

Using the `@Container` annotation is an easy way to add a Testcontainer but the problem is that your Testcontainers will be started and stopped for each test class. A better way is to use the singleton pattern and use an abstract class to start our Testcontainers. The singleton container is started only once when the base class is loaded and the container can then be reused by all inheriting test classes. At the end of the test suite the Ryuk container, that is started by Testcontainers core, will take care of stopping the singleton container.


This is what the abstract class looks like to be used to start Testcontainers
```
@SpringBootTest(webEnvironment = RANDOM_PORT)
@ActiveProfiles("integrationtest")
@Testcontainers
public abstract class AbstractIntegrationTest {

    static final MariaDBContainer mariadb;
    private static GenericContainer redis;

    static {
        mariadb = new MariaDBContainer("mariadb:10.3.6");
        mariadb.start();
        redis = new GenericContainer("redis:5.0.5")
            .withExposedPorts(6379);
        redis.start();
    }

    @DynamicPropertySource
    static void properties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", mariadb::getJdbcUrl);
        registry.add("spring.datasource.username", mariadb::getUsername);
        registry.add("spring.datasource.password", mariadb::getPassword);
        registry.add("spring.redis.host", redis::getContainerIpAddress);
        registry.add("spring.redis.port",  () -> redis.getMappedPort(6379));
    }
}
```

Each integration test class should then inherit from this abstract class to make use of Testcontainers.
```
public class TodoServiceImplIT extends AbstractIntegrationTest {

    @Autowired
    private TodoService uut;

    @Autowired
    private TodoRepository repository;

    @BeforeEach
    public void setUp() throws Exception {
        repository.deleteAll();
    }
```

If you now run your tests, you will see that our Testcontainers are only started once, which means that we have shorten the time to run our test suites significantly.


## Spring Boot and Testcontainers

In the following example I will be using Spring Boot, Junit 5 and Maven. You can find the source code on Github (https://github.com/merikan/testcontainers-demo). It contains two directories, [origin](https://github.com/merikan/testcontainers-demo/tree/master/origin) and [solution](https://github.com/merikan/testcontainers-demo/tree/master/solution) which represents before and after our changes.

The first thing we need to do is to turn off any embedded resource started by Spring Boot and instead use Testcontainers.

Remove `h2` as an embedded resource so that Spring Boot does not attempt to start it. 
```
<dependency>
  <groupId>com.h2database</groupId>
  <artifactId>h2</artifactId>
  <scope>runtime</scope>
</dependency>
```
and add Testcontainers instead. 
Our project uses `MariaDb` so let's add MariaDb as a Testcontainer. Since we are using Junit 5 we will also add the `junit-jupiter` module for Testcontainers. First we will import Testcontainers BOM and then add our dependencies.
```
<properties>
  ....
  <testcontainers.version>1.14.3</testcontainers.version>
</properties>

<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.testcontainers</groupId>
      <artifactId>testcontainers-bom</artifactId>
      <version>${testcontainers.version}</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>

<dependencies>
  ....
  <dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>testcontainers</artifactId>
    <scope>test</scope>
  </dependency>
  <dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>junit-jupiter</artifactId>
    <scope>test</scope>
  </dependency>
  <dependency>  
    <groupId>org.testcontainers</groupId>
    <artifactId>mariadb</artifactId>
    <scope>test</scope>
  </dependency>
</dependencies>
```
Next we will create an abstract class to be used by all integration test classes.
```
@SpringBootTest(webEnvironment = RANDOM_PORT)
@ActiveProfiles("integrationtest")
@Testcontainers
public abstract class AbstractIntegrationTest {

    static final MariaDBContainer mariadb;

    static {
        mariadb = (MariaDBContainer) new MariaDBContainer("mariadb:10.5.5")
            .withCommand("--character-set-server=utf8mb4", "--collation-server=utf8mb4_unicode_ci");
        mariadb.start();
    }

    @DynamicPropertySource
    static void properties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", mariadb::getJdbcUrl);
        registry.add("spring.datasource.username", mariadb::getUsername);
        registry.add("spring.datasource.password", mariadb::getPassword);
    }
}
```

Now it is time  to make sure our integration test classes inherits from `AbstractIntegrationTest`.
```
@Slf4j
public class TodoServiceImplIT extends AbstractIntegrationTest {
```
As you can see, we do not need to use the `@SpringBootTest` annotation in our test classes as it is already declared in our abstract class. We can of course still use the annotation in our test class as desired to overlay certain functionality.


This is all we have to do to get started with Testcontainers in our Spring Boot projects. As you can see it's pretty straightforward and very easy to do. If you want to run the tests yourself you can do the following, assuming you have Git and Java already installed.
```
$ git clone https://github.com/merikan/testcontainers-demo.git
$ cd testcontainers-demo/solution
solution $ ./mvnw clean test verify
```

If, after running your tests, you are fast and run the `docker ps` command you can see that your containers might still be running together with the Ryuk container but after a short while they are all stopped.
```
solution $ ./mvnw clean test verify
solution $ docker ps
CONTAINER ID        IMAGE                               COMMAND                  CREATED             STATUS              PORTS                     NAMES
d7407da0ab76        mariadb:10.5.5                      "docker-entrypoint.s…"   23 seconds ago      Up 22 seconds       0.0.0.0:32854->3306/tcp   naughty_buck
688d0f467852        testcontainersofficial/ryuk:0.3.0   "/app"                   24 seconds ago      Up 22 seconds       0.0.0.0:32853->8080/tcp   testcontainers-ryuk-590a9893-ce67-4be0-afd4-cb08c0aee4cc
```

Since we removed the support for embedded H2 database, which was used when we also ran the application locally with Maven, we now want to use a MariaDb instead and start it as a docker container. I have added a `docker-compose.yml` file we can use to start MariaDb locally. When we then start our application with the `spring-boot-maven-plugin`, the spring profile named `development` will be activated, see property file `application-development.yml`.

These are the steps you have to take; start MariaDB, start the application, ctrl+c to stop the application and then stop the MariaDb container. Don't forget to give MariaDb some time to get started before running the application.
```
solution $ docker-compose up -d
solution $ ./mvnw spring-boot:run
solution $ docker-compose stop
```
If your start a new terminal while the application is running you can curl to the endpoint like this.
```
solution $ curl --request POST \
  --url http://localhost:8080/todos \
  --header 'content-type: application/json' \
  --data '{
	"title": "a bug todo",
	"note": "is it a bug or a feature"
}' 
```


## Summary
In this blog post, I have explained what the [Testcontainers](https://www.testcontainers.org/) framework is and what problems it tries to solve. I have also briefly explained how it works and how you can easily get started using it in your own projects.


In my next blog post, I will show a little more advanced usage of Testcontainers to, among other things, speed up your tests.

And finally;  
I'm just a human being, if you find any ambiguities, typos, errors or possible improvement, please let me know.  
If you like this blog post, please add a comment and feel free to share this blog post using your favorite social media platform! There should be a few icons below to get you started.

Until next time, keep coding and never stop learning new things.

### Resources
* Testcontainers [home page](https://www.testcontainers.org/)  and source code for the [Java project](https://github.com/testcontainers/testcontainers-java/)    
* The full source code for my example project can be found here: [https://github.com/merikan/testcontainers-demo)](https://github.com/merikan/testcontainers-demo)

This blog post was first published on [Callista Enterprise](https://callistaenterprise.se)