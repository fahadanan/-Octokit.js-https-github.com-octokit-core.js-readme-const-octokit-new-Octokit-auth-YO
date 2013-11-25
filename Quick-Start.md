##Really quick start

Before you begin, make sure retrieve your *client ID and secret* from
https://code.google.com/apis/console#access

1. Download and extract example project (e.g. `dfp-axis-examples-1.5.0.tar.gz`) from [downloads](http://code.google.com/p/google-api-ads-java/downloads/list)
2. Extract project and _cd_ into dfp-examples/dfp-axis/ (or similar)
3. Edit _src/main/resources/ads.properties_ to include your OAuth2 information (client ID and client secret) and basic account information
4. Generate OAuth2 credentials to be used with other examples
5. $ mvn -X compile
6. $ mvn -X exec:java -Dexec.mainClass="dfp.axis.auth.GetRefreshToken"
7. Edit _src/main/resources/ads.properties_ to include the refresh token
8. $ mvn -X compile
9. $ mvn -X exec:java -Dexec.mainClass="dfp.axis.v201306.networkservice.GetCurrentNetwork"

##More explanation

The first step is to set up your development environment.

* Determine if you want to use **Maven** or use jars directly.
  * If you will be using maven (recommended), download one of our example projects in the [downloads](http://code.google.com/p/google-api-ads-java/downloads/list) section and extract it.
  * If you will not be using maven, download the example project mentioned above, along with the dependency jars which are named like `dfp-axis-examples-1.3.0-dependencies.tar.gz`

**Note: you will not need the dependencies tarball if you are using Maven. This is only for developers who wish to use jars.**

At this point, you will need to configure your IDE to work with the project. If you are using Maven, we suggest using eclipse as detailed in our [setup guide](http://code.google.com/p/google-api-ads-java/wiki/Setup?ts=1337893420&updated=Setup#Eclipse_With_Maven).

###The code project

You should now have an example project tree that looks like:

```
dfp-examples/
  dfp-axis/
    README
    pom.xml
    src/
      main/
        resources/
          ads.properties
          log4j.properties        
        java/
          dfp/
            axis/
              v201306/
                networkservice/
                  GetCurrentNetworks.java
                ...
              ...        
        ...
```

The **pom.xml** at the root of the tree shows you how you would set the modules need to run an example. We suggest not editing this to begin with.

The **properties** within the resources/ folder can be used to configure your project to work against your account. You will be editing these shortly before running your first example.

The **examples** are stored in the src/main/java folder within the product and framework folders. Some examples may require you edit them before running.

###Running your first example

First move into your examples project path:

`$ cd path/to/dfp-examples/dfp-axis`

Then modify the **ads.properties** file to add your OAuth2 information (client ID and secret) and account information. Located at `src/main/resources/ads.properties`

**Note: you can also move (not copy) ads.properties into your home directory to have it automatically picked up when the library initializes**

Once you have your basic OAuth2 information entered, generate an OAuth2 refresh token which will be used for authentication on each request:

```
    $ mvn -X compile
    $ mvn -X exec:java -Dexec.mainClass="dfp.axis.auth.GetRefreshToken"
```

Copy the refresh token into your ads.properties file. You can now run an example given that you have also included all necessary account information in the ads.properties file (i.e. networkCode, clientCustomId, or username).

```
    $ mvn -X compile
    $ mvn -X exec:java -Dexec.mainClass="dfp.axis.v201204.GetAllNetworks"
```

###Whats next?

Now that you have a *hello-world* example running you can start experimenting with the library. Try running other examples and chaining them together.

You may also want to explore some of the utilities we include in the library which can be found in the _`*`.`*`.`*`.utils_ packages in our code base.

Finally, you will want to configure your logging. We have included an example _log4j.properties_ file which you can modify to meet your needs (including only logging exceptions or logging all SOAP requests during your initial development phase).

If you have any questions, please ask them on the products respective forums:

  * AdWords: https://groups.google.com/forum/?fromgroups#!forum/adwords-api
  * DFA: https://groups.google.com/forum/?fromgroups#!forum/google-doubleclick-for-advertisers-api
  * DFP: https://groups.google.com/forum/?fromgroups#!forum/google-doubleclick-for-publishers-api