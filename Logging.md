# Contents

* [Overview](#overview)
* [Using log4j with the example projects](#using-log4j-with-the-example-projects)
* [Setting up your own project](#setting-up-your-own-project)

# Overview
The client library uses [SLF4J](http://www.slf4j.org/) for all logging. If you want to turn on logging,
you must [include a plugin that bridges SLF4J with a concrete logging framework](http://www.slf4j.org/manual.html#swapping).

To quickly get you started and to serve as an example of how to do this, each
example distribution uses the [log4j](http://logging.apache.org/log4j/1.2/) framework, and includes a sample `log4j.properties` file that shows how you might configure logging if you decide to use log4j as your concrete logging framework.

The library includes the following loggers:

**SOAP loggers**

    com.google.api.ads.adwords.lib.client.AdWordsServiceClient.soapXmlLogger
    com.google.api.ads.dfa.lib.client.DfaServiceClient.soapXmlLogger
    com.google.api.ads.dfp.lib.client.DfpServiceClient.soapXmlLogger

Logs incoming and outgoing SOAP requests/responses. SOAP requests and
responses are logged as WARN for exceptions and INFO for all other requests.
You can configure your logging framework to accept logs on these parameters.

**Request info loggers**

    com.google.api.ads.adwords.lib.client.AdWordsServiceClient.requestInfoLogger
    com.google.api.ads.dfa.lib.client.DfaServiceClient.requestInfoLogger
    com.google.api.ads.dfp.lib.client.DfpServiceClient.requestInfoLogger

Logs a single line summary of each request from the client library that includes information such as the
timestamp, service, method, endpoint URL, and success or failure of the request.

**Product-specific loggers**

**AdWords** has the following additional loggers.

* [Reporting](https://developers.google.com/adwords/api/docs/guides/reporting):

    `com.google.api.ads.adwords.lib.utils.report_download`

* [BatchJob](https://developers.google.com/adwords/api/docs/guides/batch-jobs) requests:

    `com.google.api.ads.adwords.lib.utils.batch_job`

  Operation uploads:
  * One-line summary to `INFO` for successful uploads, and `WARN` for failed uploads
  * Complete upload contents to `DEBUG` for all uploads

  Batch job result downloads:
  * One-line summary to `INFO` for successful downloads, and `WARN` for failed downloads

Because the client library uses SLF4J, the behavior of these loggers is highly customizable.

# Using log4j with the example projects
To make it easier to enable logging in the example projects, each example project includes a sample `log4j.properties` file, as well as the log4j dependencies required at runtime.

## If you are using jars
If you downloaded a file like `adwords-axis-jars-and-examples-v.vv.vv.tar.gz`, it will already include:

   * log4j dependencies in its `src/lib` folder
   * a default `log4j.properties` config file in its `src` folder

Simply make sure that both the jars in `src/lib` and the `src/log4j.properties` file are in your classpath.

## If you are using maven
If you downloaded a file like `adwords-axis-maven-and-examples-v.vv.vv.tar.gz`, it will already include:

   * log4j dependencies in its `pom.xml` file
   * a default `log4j.properties` config file in its `src/main/resources` folder

Maven should automatically include the log4j dependencies and the config file in your classpath, so logging will be enabled by default.

# Setting up your own project
This is where the flexibility of SLF4J proves useful. You simply need to:

1. Include the SLF4J dependencies that bridge SLF4J to your logging framework of choice
2. Configure the [relevant loggers](#overview) in the config file for your logging framework

The [SLF4J manual](http://www.slf4j.org/manual.html#swapping) contains a list of bindings for common logging frameworks.

## If you are using jars

To use a different framework than log4j, remove the `slf4j-log4j12-v.v.v.jar` from your classpath and fetch a different one from <http://www.slf4j.org/download.html>.

## If you are using maven

To bridge SLF4J for a logging framework, you must include the plugin and logging framework dependencies
in the dependencies list of your `pom.xml file`.

For example, if you want to use the log4j logging framework, add the following to your `pom.xml` file.

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
       <version>1.7.7</version>
     </dependency>

If you want to use JDK logging, add the following to your `pom.xml` file.

     <!-- Adds the log4j framework -->
     <dependency>
       <groupId>log4j</groupId>
       <artifactId>log4j</artifactId>
       <version>1.2.16</version>
     </dependency>
     <!-- Make SLF4J use JDK logging as the logging framework -->
     <dependency>
       <groupId>org.slf4j</groupId>
       <artifactId>slf4j-jdk14</artifactId>
       <version>1.7.12</version>
     </dependency>