---
title: "Speed up your Testcontainers tests"
date: 2020-09-30T21:22:53+02:00
draft: true
author: Peter

subtitle: "How you can drastically reduce the runtime for your Testcontainers tests"
description: ""
image: "/files/speed-up-your-testcontainers-tests/code-block.png"
showtoc: true
tags: ["testcontainers", "java","spring-boot"]
categories: ["technology"]

---
## Intro
In the previous blog post, [Getting Started with Testcontainers]({{< ref "getting-started-with-testcontainers" >}}), I described what the [Testcontainers](https://www.testcontainers.org/) framework is and how to get started using it in your project. After using Testcontainers for a long time, there are things that need to be adjusted to make it even better and faster. Below I will show some of the things that make a fantastic framework even better.

### Source code
The full source code for my example project can be found here: [https://github.com/merikan/testcontainers-demo)](https://github.com/merikan/testcontainers-demo) and the folder `faster`.


## Use the singleton pattern

I described this in my [previous blog post]({{< ref "getting-started-with-testcontainers" >}}) but it is well worth repeating. There may sometimes be a reason to use the `@Container` annotation to declare Testcontainers in our integration test classes. The disadvantage of this is that our Testcontainers will start and stop between each test class that is run, which can take a very long time. We can instead use the singleton pattern to make our Testcontainers start only once for all our test classes, and then be taken down when they are finished. This of course requires that our tests are self-contained and not affected by the state of the information in the resource, if they are to be run in parallel, or that before each test we can set the resource in the state required by our test.


To use the singleton pattern we can create an abstract class that handles the creation of Testcontainers. 

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

If you now run your tests, you will see that our Testcontainer is only started once, which means that we shorten the time to run our test suites significantly.


## Speed up start time with multiple containers

In my current projects I have multiple containers in my test suites and it will take some time to start, even if it is only done once.

Let us first create an example where we start several test containers and then make some changes to see if we can reduce the start-up time. In order not to get misleading results, we need to run once first to ensure that all images are pulled down and available locally. To keep the example clean and clear I am not using any properties from our started containers
```
static {
    Instant start = Instant.now();

    mariadb1 = (MariaDBContainer) new MariaDBContainer("mariadb:10.5.5")
        .withCommand("--character-set-server=utf8mb4", "--collation-server=utf8mb4_unicode_ci");
    mariadb1.start();
    mariadb2 = (MariaDBContainer) new MariaDBContainer("mariadb:10.5.5")
        .withCommand("--character-set-server=utf8mb4", "--collation-server=utf8mb4_unicode_ci");
    mariadb2.start();
    redis = new GenericContainer("redis:5.0.5")
        .withExposedPorts(6379);
    redis.start();
    sftp = new GenericContainer("atmoz/sftp:latest")
        .withCommand(String.format("%s:%s:::upload", "SFTP_USER", "SFTP_PASSWORD"));
    sftp.start();

    log.info("🐳 TestContainers started in {}", Duration.between(start, Instant.now()));
}
```
If we now run one test I can see that that it takes a little over 19 seconds to start all four Testcontainers.
```
 🐳 TestContainers started in PT19.386613S  
```
Ok, time to make some changes. As we can see in  the code above we are starting our testcontainers one by one, what if we could start them in parallell instead. Let's try that. 
We can use the Stream class for this and it will look like this.
```
static {
    Instant start = Instant.now();

    mariadb1 = (MariaDBContainer) new MariaDBContainer("mariadb:10.5.5")
        .withCommand("--character-set-server=utf8mb4", "--collation-server=utf8mb4_unicode_ci");
    mariadb2 = (MariaDBContainer) new MariaDBContainer("mariadb:10.5.5")
        .withCommand("--character-set-server=utf8mb4", "--collation-server=utf8mb4_unicode_ci");
    redis = new GenericContainer("redis:5.0.5")
        .withExposedPorts(6379);
    sftp = new GenericContainer("atmoz/sftp:latest")
        .withCommand(String.format("%s:%s:::upload", "SFTP_USER", "SFTP_PASSWORD"));
    Stream.of(mariadb1, mariadb2, redis, sftp).parallel().forEach(GenericContainer::start);

    log.info("🐳 TestContainers started in {}", Duration.between(start, Instant.now()));
}
```
If we now run our test again we can see that that it takes a little over 11 seconds to start all four test containers. 

```
🐳 TestContainers started in PT11.473507S
```
So we saved 8 seconds in startup time, it wasn't that bad, was it? Is there anything else we can do to make our tests faster? Yes there is, just keep reading and you will see yet another way.


This a great but but one thing that slows me down is that when I work with one test and want to run it several times, it takes a very long time and I whant a fast feedback loop. So let's dig deeper and see how we can solve that.


## Reuse your Testcontainers

Testcontainers comes with a [preview feature](https://github.com/testcontainers/testcontainers-java/pull/1781) that enables us to reuse an already started Testcontainer. In short, this means that Ryuk will not be started and which in turn means that our Testcontainers will not be taken down after our tests are completed. This feature is really something that saves time during the development process and all you have to remember is to manually stop all Testcontainers when you are done. 

Two things we have to do to enable reuse. First we have to tell our Testcontainer that it should be reused and it is done with the `.withReuse(true)` method. Next we have to create a configuration file for Testcontainers (`~/.testcontainers.properties`)  and add the property `testcontainers.reuse.enable=true`. At the time of writing you can only have this property file in your home directory and not in the classpath, per project. But on the other hand, you probably do not want to impose such behavior on your team members or other environments, such as your CI / CD pipeline. This should definitely be optional and if it becomes possible to add it per project you should add it to `.gitignore`.

This is how our abstract class will look like when enabling `reuse`.
```
static {
    Instant start = Instant.now();

    mariadb1 = (MariaDBContainer) new MariaDBContainer("mariadb:10.5.5")
        .withCommand("--character-set-server=utf8mb4", "--collation-server=utf8mb4_unicode_ci")
    .withReuse(true);
    mariadb2 = (MariaDBContainer) new MariaDBContainer("mariadb:10.5.5")
        .withCommand("--character-set-server=utf8mb4", "--collation-server=utf8mb4_unicode_ci")
        .withReuse(true);
    redis = new GenericContainer("redis:5.0.5")
        .withExposedPorts(6379)
        .withReuse(true);
    sftp = new GenericContainer("atmoz/sftp:latest")
        .withCommand(String.format("%s:%s:::upload", "SFTP_USER", "SFTP_PASSWORD"))
        .withReuse(true);
    Stream.of(mariadb1, mariadb2, redis, sftp).parallel().forEach(GenericContainer::start);

    log.info("🐳 TestContainers started in {}", Duration.between(start, Instant.now()));
}
```

One [problem](https://github.com/testcontainers/testcontainers-java/issues/2961) that you will probably encounter is that the Testcontainer is not unique to this particular project. Since I have several Testcontainers with the same configuration in several projects, this posed a problem for me.


If we take a look at the [source code](https://github.com/testcontainers/testcontainers-java/blob/17b4f6c136f6f2c7dc223bad407221f62a8f0088/core/src/main/java/org/testcontainers/containers/GenericContainer.java#L364-L412), the uniqueness of the started Testcontainer is calculated of the actual configuration. Testcontainers will then search for the hash among all current running container with label `org.testcontainers.hash` to see if match is found, and if so reuse this container. An easy solution to this problem is to add something to the configuration that makes this particular container unique. I have chosen to add my own label with the name `reuse.UUID` with a [unique UUID](https://www.uuidgenerator.net/) as the value.
```
.withLabel ("reuse.UUID", "693b1b21-9817-4307-89d0-567b9769bd8f")
```

This is what it looks like in the abstract class when we activate reuse of our Testcontainers.
```
static {
    Instant start = Instant.now();

    mariadb1 = (MariaDBContainer) new MariaDBContainer("mariadb:10.5.5")
        .withCommand("--character-set-server=utf8mb4", "--collation-server=utf8mb4_unicode_ci")
    .withReuse(true)
    .withLabel("reuse.UUID", "e06d7a87-7d7d-472e-a047-e6c81f61d2a4");
    mariadb2 = (MariaDBContainer) new MariaDBContainer("mariadb:10.5.5")
        .withCommand("--character-set-server=utf8mb4", "--collation-server=utf8mb4_unicode_ci")
        .withReuse(true)
        .withLabel("reuse.UUID", "282b8993-097c-4fd4-98f1-94daf3466dd6");
    redis = new GenericContainer("redis:5.0.5")
        .withExposedPorts(6379)
        .withReuse(true)
        .withLabel("reuse.UUID", "0429783b-c855-4b32-8239-258cba232b63");
    sftp = new GenericContainer("atmoz/sftp:latest")
        .withCommand(String.format("%s:%s:::upload", "SFTP_USER", "SFTP_PASSWORD"))
        .withReuse(true)
        .withLabel("reuse.UUID", "0293a405-e435-4f03-9e4b-b6160d9e60fe");
    Stream.of(mariadb1, mariadb2, redis, sftp).parallel().forEach(GenericContainer::start);

    log.info("🐳 TestContainers started in {}", Duration.between(start, Instant.now()));
}
```
If we start our test again, it takes about 11 seconds to start our test containers the first time. If we run the tests again, our test containers will be reused and it only takes about 1 second to reuse. That's not bad, is it?
```
🐳 TestContainers started in PT1.860885S
```

This is really a performance boost to get faster feedback when running our tests. And now all we have to do, when we are done testing, is to stop our running Testcontainers.

To list all running Testcontainers:
```
docker ps --filter label=org.testcontainers
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                     NAMES
05f921a85189        mariadb:10.5.5      "docker-entrypoint.s…"   50 seconds ago      Up 49 seconds       0.0.0.0:32903->3306/tcp   elated_visvesvaraya
f7212d652482        redis:5.0.5         "docker-entrypoint.s…"   50 seconds ago      Up 49 seconds       0.0.0.0:32901->6379/tcp   tender_mendeleev
3130306e507e        mariadb:10.5.5      "docker-entrypoint.s…"   50 seconds ago      Up 49 seconds       0.0.0.0:32904->3306/tcp   sweet_matsumoto
783987174b84        atmoz/sftp:latest   "/entrypoint SFTP_US…"   50 seconds ago      Up 49 seconds       0.0.0.0:32902->22/tcp     crazy_hawking
```
and to stop all running Testcontainers:
```
docker stop $(docker ps -q --filter label=org.testcontainers)
05f921a85189
f7212d652482
3130306e507e
783987174b84
```


## Runing tests in a docker container

You may be wondering that, if my tests now use docker containers, then can I run my tests in a docker container? Yes you can, it is called [Docker in Docker](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) aka. DinD. With docker in docker, there will be new challenges, but that's not something I intend to talk about here.

To run your tests locally in a docker container. 
```
git clone https://github.com/merikan/testcontainers-demo.git
cd faster
faster $  docker run -it --rm -v $PWD:$PWD -w $PWD -v /var/run/docker.sock:/var/run/docker.sock maven:3 ./mvnw clean test verify
```

## Summary
In this blog post I have showed you some ways to drastically reduce the time to run your tests using Testcontainers.

And finally; I'm just a human being, if you find any ambiguities, typos, errors or possible improvement, please let me know. Until next time, keep coding and never stop learning new things.

### Resources
* Testcontainers home page(https://www.testcontainers.org/)  and source code for the [Java project](https://github.com/testcontainers/testcontainers-java/)    
* The full source code for my example project can be found here: [https://github.com/merikan/testcontainers-demo)](https://github.com/merikan/testcontainers-demo) and the folder `faster`.