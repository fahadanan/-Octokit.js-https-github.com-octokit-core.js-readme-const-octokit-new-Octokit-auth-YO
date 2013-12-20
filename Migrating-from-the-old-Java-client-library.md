This page will describe how to migrate from the old [AdWords API Java client library](http://code.google.com/p/google-api-adwords-java/) and [DFP API Java client library](http://code.google.com/p/google-api-dfp-java/) (hereafter referred to as the “old library”) to the rewritten [Ads APIs client library](https://github.com/googleads/googleads-java-lib) hosted here (hereafter referred to as the “new library”).  The following code snippets will use the AdWords product, but you could substitute AdWords for DFP or DFA and the process will be the same.

#Obtaining the library jars

Whereas the old library only supported jars, the new library supports maven. When migrating, you may want to consider switching to Maven, which greatly simplifies dependency management.

## Getting the jars

If you still want to use jars, you will find a _product_-axis-jars-and-examples (i.e. ```adwords-axis-jars-and-examples.tar.gz```) download in the [releases](https://github.com/googleads/googleads-java-lib/releases) section. In the tarball, you will find all the jars you need in the lib/ folder.

When migrating from the old library to the new library, please make sure to remove all jars and files that the old library was using, particularly the client-config.wsdd (which axis will read from the classpath).  While none of the packages will conflict, it's better to remove these from your classpath to ensure you completely migrate.

## Using maven

If you've chosen to upgrade to Maven, we suggest using the _product_-axis-maven-and-examples (i.e. ```adwords-axis-maven-and-examples.tar.gz```) download in the [releases](https://github.com/googleads/googleads-java-lib/releases) section. You can import this project using [m2eclipse](http://www.eclipse.org/m2e/) or use the pom.xml as a starting point.

#Making requests

The biggest change with the new library is the setup required to make requests. Aside from package names being slightly different, all the objects you’ll be working with will be nearly identical to the old library. Different SOAP toolkits may generate code slightly differently for the same WSDL.

##Configuring AdWords/DfpSession

Where the old library had an AdWordsUser or DfpUser, the new library now uses an AdWordsSession. We’ve also moved to using a [pattern](http://en.wikipedia.org/wiki/Builder_pattern builder) to construct this object.  In the old library, you could instantiate an AdWordsUser like this:
```
AdWordsUser user = new AdWordsUser();
```
This would pull in properties from `~/adwords.properties`. It was also possible to pass in a `Properties` object or specify the various parameters as arguments to the constructor.

In the new library, you’ll use a Builder to construct the session. You can still pull values from an ads.properties file (although the property names have changed) like in this example:
```
AdWordsSession session =  new AdWordsSession.Builder()
    .fromFile()
    .withOAuth2Credential(oAuth2Credential)
    .build();
```
The AdWordsSessionfromFile() call tries to locate an `ads.properties` file in the user home directory, the current classpath or the system classpath (in that order). This is the default filename used for all products. You can also pass in a `String` path, a `File` object or a `URL` to specify the location of this file or a `Configuration` object with your settings. If you’d prefer not to use an `ads.properties` file, you can also use the builder to configure the session like this:
```
AdWordsSession session = new AdWordsSession.Builder()
    .withOAuth2Credential(oAuth2Credential)
    .withDeveloperToken("INSERT_DEVELOPER_TOKEN_HERE")
    .build();
```
The `.build()` call will validate your settings to make sure you are ready to make requests.  You can change the account being targeted by requests by calling the `setClientCustomerId` on the session - any services created with this session will target the new customer account.
```
session.setClientCustomerId("INSERT_CLIENT_CUSTOMER_ID_HERE");
```
In both of the above examples, you’ll note that we specify the authentication information separately.  We’ll discuss that next.

##Authentication

With the new library, you will need to move to OAuth2.0 as well. Although ClientLogin is still supported, it is deprecated.

### Using OAuth2.0

The new library now has support for OAuth 2.0.  This was a widely requested feature of the old library. If you are coming from using ClientLogin, we've added a few features to make it extremely easy to switch over.

**If you haven't yet, read [Using OAuth2] (Using-OAuth-2) first**

Once you've generated a refresh token using the GetRefreshToken.java example, and you've copied it into your `ads.properties` file, you'll be able to create a refreshable token with the OfflineCredentials utility.
```
Credential oAuth2Credential = new OfflineCredentials.Builder()
    .forApi(Api.DFP)
    .fromFile()
    .build()
    .generateCredential();
```
Once authorized, you can set the `Credential` object into the builder or session:
```
AdWordsSession session = new AdWordsSession.Builder()
    .fromFile()
    .withOAuth2Credential(oAuth2Credential)
    .build();
```
OAuth2 will now be used when making API calls.

### ClientLogin (Deprecated)

If you absolutely need to use ClientLogin, you will find a few things have changed. In the old library, you would specify a username and password in your `adwords.properties` file and the library would automagically request a ClientLogin AuthToken for you on your first request. In the new library we've decided to give the developer more control over this process. You can now obtain a ClientLogin token using our ClientLoginTokens helper class like this:
```
String clientLoginToken = new ClientLoginTokens.Builder()
    .forApi(ClientLoginTokens.Api.ADWORDS)
    .fromFile()
    .build()
    .requestToken();
```
The `fromFile()` call works the same way as for `AdWordsSession.Builder`.  This idiom puts you, the developer, in conscious control of when and how ClientLogin tokens are requested. You can immediately handle CAPTCHA challenges when you request the token, as opposed to when the first request is made. Since these tokens are valid for two weeks, we strongly encourage you to cache them and re-use them when creating your `AdWordsSession` objects.

##Making a request

In the old library, you would get a reference to the Service class by passing a constant into the user.getService method like this:
```
CampaignServiceInterface campaignService =
    user.getService(AdWordsService.V201109.CAMPAIGN_SERVICE);
```
In the new library, we provide a utility class to perform this task, and you pass in the interface you would like, as below:
```
AdWordsServices adWordsServices = new AdWordsServices();
CampaignServiceInterface campaignService =
    adWordsServices.get(session, CampaignServiceInterface.class);
```
The AdWordsServices object is lightweight, but we recommend that it be instantiated once and reused as much as possible. Please note that this class is [thread-safe](Thread-Safety). As it retains no state from the session you pass in, you can reuse it with multiple sessions.

**Important**: the service objects themselves retain a reference to your session. You can modify the session and the associated service object will start using those changes. These service objects are NOT threadsafe.

Now you can make requests with the service class and associated objects as you would have with the old library.

**Important**: The main difference after this is the package name for the generated class will look like `com.google.api.ads.adwords.axis.v201109.cm.Campaign` instead of `com.google.api.adwords.v201109.cm.Campaign`.

##A word on ads.properties

With the new library, we've changed the property names used in the `ads.properties` file. By adding a product-specific prefix, you could use the same ads.properties file with two separate products. The same property names listed below can be used if you want to create a `PropertyConfiguration` by hand and pass that rather than a file path or using the builder setters.  The old `adwords.properties` file looked like this:
```
# clientId and useSandbox are optional and may be commented out.
email=INSERT_LOGIN_EMAIL_HERE
password=INSERT_PASSWORD_HERE
# clientCustomerId also works.
clientId=INSERT_ID_HERE
useragent=INSERT_COMPANY_NAME
developerToken=INSERT_DEVELOPER_TOKEN_HERE
useSandbox=false
partialFailure=false
returnMoneyInMicros=false
```
The new `ads.properties` file for AdWords looks like this:
```
# OfflineCredentials authentication properties.
# A refresh token can be acquired using the GetRefreshToken example.
api.adwords.refreshToken=INSERT_REFERSH_TOKEN_HERE
# If you do not have a client ID or secret, please create one in the
# API console: https://code.google.com/apis/console#access
api.adwords.clientId=INSERT_CLIENT_ID_HERE
api.adwords.clientSecret=INSERT_CLIENT_SECRET_HERE

api.adwords.clientCustomerId=INSERT_CLIENT_CUSTOMER_ID_HERE
api.adwords.userAgent=INSERT_USERAGENT_HERE
api.adwords.developerToken=INSERT_DEVELOPER_TOKEN_HERE

api.adwords.isPartialFailure=false
api.adwords.returnMoneyInMicros=true

# Report download connect/read timeout defaults to 3 minutes if omitted.
# Specify value in milliseconds.
# api.adwords.reportDownloadTimeout=180000

# Enable/disable automatic OAuth2 token refreshing. Default is enabled.
# api.adwords.refreshOAuth2Token=true

# DEPRECATED:
#
# ClientLogin is now deprecated. Please use OAuth2 instead. See GetRefreshToken
# example for more information.
# Either email and password or clientLoginToken should be provided
# api.adwords.email=INSERT_EMAIL_HERE
# api.adwords.password=INSERT_PASSWORD_HERE
# Optional, for cached AuthTokens.
# api.adwords.clientLoginToken=INSERT_AUTH_TOKEN_HERE
```
And for comparison purposes, here’s the DFP version of `ads.properties`:
```
# Credentials to use for accessing the DFP API
# Detailed descriptions of these properties can be found at:
# https://developers.google.com/doubleclick-publishers/docs/soap_xml

# OfflineCredentials authentication properties.
# A refresh token can be acquired using the GetRefreshToken example.
api.dfp.refreshToken=INSERT_REFERSH_TOKEN_HERE
# If you do not have a client ID or secret, please create one in the
# API console: https://code.google.com/apis/console#access
api.dfp.clientId=INSERT_CLIENT_ID_HERE
api.dfp.clientSecret=INSERT_CLIENT_SECRET_HERE

# Any application name of your choosing.
api.dfp.applicationName=INSERT_APPLICATION_NAME_HERE

# The header networkCode is needed for every request except
# NetworkService.makeTestNetwork and NetworkService.getAllNetworks. Comment
# out the line below when calling either of those methods.
api.dfp.networkCode=INSERT_NETWORK_CODE_HERE

# Enable/disable automatic OAuth2 token refreshing. Default is enabled.
# api.dfp.refreshOAuth2Token=true

# Change the DFP API endpoint server. Optional.
# api.dfp.endpoint=https://www.google.com/

# DEPRECATED:
#
# ClientLogin is now deprecated. Please use OAuth2 instead. See GetRefreshToken
# example for more information.
# Either email and password or clientLoginToken should be provided
# api.dfp.email=INSERT_EMAIL_HERE
# api.dfp.password=INSERT_PASSWORD_HERE
# Optional, for cached AuthTokens.
# api.dfp.clientLoginToken=INSERT_AUTH_TOKEN_HERE
```

##Utilities

Some of our utilities have changed if you are coming from the old library. Here is a mapping of old utility to new one:
 
###DFP

| Old class   | New class   |
| ----------- | ----------- |
| com.google.api.ads.dfp.lib.utils.CsvUtils |  [com.google.api.ads.common.lib.utils.CsvFiles](https://github.com/googleads/googleads-java-lib/blob/master/modules/ads_lib/src/main/java/com/google/api/ads/common/lib/utils/CsvFiles.java) |
| com.google.api.ads.dfp.lib.utils.MapUtils | [com.google.api.ads.common.lib.utils.Maps](https://github.com/googleads/googleads-java-lib/blob/master/modules/ads_lib/src/main/java/com/google/api/ads/common/lib/utils/Maps.java) |
| com.google.api.ads.dfp.lib.utils.MediaUtils | [com.google.api.ads.common.lib.utils.Media](https://github.com/googleads/googleads-java-lib/blob/master/modules/ads_lib/src/main/java/com/google/api/ads/common/lib/utils/Media.java) |
| com.google.api.ads.dfp.lib.utils.XmlUtils | No longer exists |
| com.google.api.ads.dfp.lib.utils.v201311.DateTimeUtils | [com.google.api.ads.dfp.axis.utils.v201311.DateTimes](https://github.com/googleads/googleads-java-lib/blob/master/modules/dfp_axis/src/main/java/com/google/api/ads/dfp/axis/utils/v201311/DateTimes.java ) |
| com.google.api.ads.dfp.lib.utils.v201311.DateUtils | [com.google.api.ads.dfp.axis.utils.v201311.DateTimes](https://github.com/googleads/googleads-java-lib/blob/master/modules/dfp_axis/src/main/java/com/google/api/ads/dfp/axis/utils/v201311/DateTimes.java) (Use the .getDate() function from the resulting DateTime) |
| com.google.api.ads.dfp.lib.utils.v201311.PqlUtils | [com.google.api.ads.dfp.axis.utils.v201311.Pql](https://github.com/googleads/googleads-java-lib/blob/master/modules/dfp_axis/src/main/java/com/google/api/ads/dfp/axis/utils/v201311/Pql.java) |
| com.google.api.ads.dfp.lib.utils.v201311.ReportUtils | [com.google.api.ads.dfp.axis.utils.v201311.ReportDownloader](https://github.com/googleads/googleads-java-lib/blob/master/modules/dfp_axis/src/main/java/com/google/api/ads/dfp/axis/utils/v201302/ReportDownloader.java) |
| com.google.api.ads.dfp.lib.utils.v201311.StatementBuilder | [com.google.api.ads.dfp.axis.utils.v201311.StatementBuilder](https://github.com/googleads/googleads-java-lib/blob/master/modules/dfp_axis/src/main/java/com/google/api/ads/dfp/axis/utils/v201302/StatementBuilder.java) |

###AdWords

| Old class   | New class   |
| ----------- | ----------- |
| com.google.api.adwords.lib.utils.ChoiceUtils |  [com.google.api.ads.common.lib.utils.Choices](https://github.com/googleads/googleads-java-lib/blob/master/modules/ads_lib/src/main/java/com/google/api/ads/common/lib/utils/Choices.java) |
| com.google.api.ads.adwords.lib.utils.CsvUtils |  [com.google.api.ads.common.lib.utils.CsvFiles](https://github.com/googleads/googleads-java-lib/blob/master/modules/ads_lib/src/main/java/com/google/api/ads/common/lib/utils/CsvFiles.java) |
| com.google.api.ads.adwords.lib.utils.MapUtils | [com.google.api.ads.common.lib.utils.Maps](https://github.com/googleads/googleads-java-lib/blob/master/modules/ads_lib/src/main/java/com/google/api/ads/common/lib/utils/Maps.java) |
| com.google.api.adwords.lib.utils.ImageUtils | [com.google.api.ads.common.lib.utils.Media](https://github.com/googleads/googleads-java-lib/blob/master/modules/ads_lib/src/main/java/com/google/api/ads/common/lib/utils/Media.java) |
| com.google.api.adwords.lib.utils.XmlUtils | No longer exists |
| com.google.api.adwords.lib.utils.v201309.ReportUtils | [com.google.api.ads.adwords.lib.utils.v201309.ReportDownloader](https://github.com/googleads/googleads-java-lib/blob/master/modules/ads_lib/src/main/java/com/google/api/ads/adwords/lib/utils/v201309/ReportDownloader.java) |


## Logging

The new library uses SLF4J. To see how to enable it, read [How do I enable logging?](https://github.com/googleads/googleads-java-lib/wiki/FAQ#how-do-i-enable-logging).