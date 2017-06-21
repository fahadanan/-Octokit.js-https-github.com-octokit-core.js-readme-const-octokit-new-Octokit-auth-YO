## How do I enable logging?

Check out the [Logging page](https://github.com/googleads/googleads-java-lib/wiki/Logging).

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