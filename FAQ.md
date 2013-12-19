## How do I enable logging?

The client library uses SLF4J for all logging. If you want to turn logging on,
you must include a plugin that bridges SLF4J with a concrete logging framework.
To quickly get you started and to serve as an example of how to do this, this
example distribution uses the log4j framework.

There are the following loggers within the library:

  - com.google.api.ads.adwords.lib.client.AdWordsServiceClient.soapXmlLogger
    com.google.api.ads.dfa.lib.client.DfaServiceClient.soapXmlLogger
    com.google.api.ads.dfp.lib.client.DfpServiceClient.soapXmlLogger

    Logs incoming and outgoing SOAP requests/responses. SOAP requests and
    responses are logged as WARN for exceptions and INFO for all other requests.
    You can configure your logging framework to accept logs on these parameters.
    See the example log4j.properties for more information.

  - com.google.api.ads.adwords.lib.client.AdWordsServiceClient.requestInfoLogger
    com.google.api.ads.dfa.lib.client.DfaServiceClient.requestInfoLogger
    com.google.api.ads.dfp.lib.client.DfpServiceClient.requestInfoLogger

    Logs all requests from the client library along with information such as the
    timestamp, service, method, endpoint URL.

Because the client library uses SLF4J, the behavior of these loggers is highly customizable. Please see the "log4j.properties" or "src/main/resources/log4j.properties" file for details on the default behavior in this example project.

### If you are using jars

To use a different framework than log4j, remove the slf4j-log4j12 jar from your classpath and fetch a different one here: http://www.slf4j.org/download.html

### If you are using maven

To bridge SLF4J for a logging framework, you must do the following:

  1) Include the plugin and logging framework dependencies in the pom.xml file's
     dependencies list.

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

  2) If your logging framework requires a configuration file, you must place it
     in the resources directory. This distribution includes an example
     configuration for log4j named "log4j.properties".

## Using a proxy

It is recommended that the user set JVM arguments to configure this application
for their proxy.

    https.proxyHost      Hostname of proxy server                      web-proxy
    https.proxyPort      Port on server of proxy                       8080
    https.proxyUser      Optional username for proxy authentication    someone
    https.proxyPassword  Optional proxy server password                secret

These properties can be set with java args in your eclipse run configuration:

    -Dhttps.proxyHost=web-proxy -Dhttps.proxyPort=8080 -Dhttps.proxyUser=someone 
    -Dhttps.proxyPassword=secret ...
        
If necessary, set this up in code by doing the following:

    System.setProperty("https.proxyHost", "web-proxy");
    System.setProperty("https.proxyPort", "8080");
    System.setProperty("https.proxyUser", "someone");
    System.setProperty("https.proxyPassword", "secret");


## Where do I submit bug reports, feature requests and patches?

All of these items can be submitted at (https://github.com/googleads/googleads-java-lib/issues)