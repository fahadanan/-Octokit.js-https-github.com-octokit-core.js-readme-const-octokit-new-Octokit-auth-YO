##How do I enable logging?

The client library uses [SLF4J](http://www.slf4j.org/) for all logging. If you want to turn logging on, you must include a plugin that bridges SLF4J with a concrete logging framework. Many of our example projects use the [log4j](http://logging.apache.org/log4j/1.2/) framework for logging and can be used as a reference for how to do so.

To bridge SLF4J for a logging framework, you must do the following:
  * Include the plugin and logging framework dependencies in the pom.xml file's dependencies list. For example, log4j can be used by adding the following dependencies:
  ```
     <!-- Adds the log4j framework -->
     <dependency>
       <groupId>log4j</groupId>
       <artifactId>log4j</artifactId>
       <version>1.2.16</version>
     </dependency>
     <!-- Make SLF4J use log4j as the logging framework -->
     <dependency>
       <groupId>org.slf4j</groupId>
       <artifactId>slf4j-log4j12</artifactId>
       <version>1.6.2</version>
     </dependency>
  ```
  * If your logging framework requires a configuration file, you must place it in the `src/main/resources` directory. 

##What if I am behind an HTTP Proxy server?

It is recommended that the user set JVM arguments to configure this application
for their proxy.
```
    https.proxyHost      Hostname of proxy server                      web-proxy
    https.proxyPort      Port on server of proxy                       8080
    https.proxyUser      Optional username for proxy authentication    someone
    https.proxyPassword  Optional proxy server password                secret
```

**Note** that https is used instead of http.

These properties can be set within the command line such as:
```
    $ mvn -Dhttps.proxyHost=web-proxy -Dhttps.proxyPort=8080 -Dhttps.proxyUser=someone -Dhttps.proxyPassword=secret ...
```
If necessary, set this up in code by doing the following:
```
    System.setProperty("https.proxyHost", "web-proxy");
    System.setProperty("https.proxyPort", "8080");
    System.setProperty("https.proxyUser", "someone");
    System.setProperty("https.proxyPassword", "secret");
```