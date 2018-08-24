## Overview

DoubleClick for Publishers was [recently renamed](https://www.blog.google/products/admanager/introducing-google-ad-manager/) to Google Ad Manager. To be consistent with these changes, we've updated our example code and refactored type names throughout this library. You'll only need to make a few small changes to upgrade your existing code. 


## What changed?



*   The package `com.google.api.ads.dfp `was renamed to `com.google.api.ads.admanager`
*   The property keys prefixed with `api.dfp` were renamed to `api.admanager`
*   Classes containing "Dfp" were renamed to "AdManager". See the 

[complete list ](#class-name-changes)of class name changes at the end of this post.

Note: The maven package names are not changing and will continue to be dfp_axis and dfp_appengine<span style="color:#0d904f;">.</span>


## How to migrate

1. In your [ads.properties](https://github.com/googleads/googleads-java-lib/blob/master/examples/admanager_axis/src/main/resources/ads.properties) or other configuration file, change the prefix of `ads.dfp` properties to `ads.admanager`. The property values should remain unchanged. 

Failure to make this change will cause an error like:


```
Invalid configuration in the ads.properties file. Exception: Client ID must be set as api.admanager.clientId in ads.properties.
```


2. Change import statements from `import com.google.api.ads.dfp` to `import com.google.api.ads.admanager`.

Failure to make this change will cause an error like:


```
Cannot resolve symbol dfp
```


3. Change usage of `DfpServices` and `DfpSession` to `AdManagerServices` and `AdManagerSession`. If you are using other classes that have been 

[renamed](#class-name-changes), update those too. You can use a tool like `grep `to find usage of the old class names. Copy the old names into a file like `oldnames.txt` and run a command like the following in your project's root directory:


```
grep -r -f oldnames.txt .
```


Failure to make this change will cause an error like:


```
Cannot resolve symbol 'DfpServices'
```


4. When instantiating Credential objects with OfflineCredentials, change `Api.DFP` to `Api.AD_MANAGER`.


```
Credential oAuth2Credential = new OfflineCredentials.Builder()
    .forApi(Api.AD_MANAGER).fromFile().build().generateCredential();
```


Failure to make this change will cause an error like:


```
Cannot resolve symbol 'DFP'
```


<span style="color:#212121;">For questions about this or any other API changes, reach out to us on the [Ad Manager API forums](https://groups.google.com/forum/#!forum/google-doubleclick-for-publishers-api)<span style="color:#212121;">.</span></span>


## Class name changes


<table>
  <tr>
   <td><strong>Old name</strong>
   </td>
   <td><strong>New name</strong>
   </td>
  </tr>
  <tr>
   <td>
<code>com.google.api.ads.dfp.lib.factory.DfpServiceClientFactory</code>
<code>com.google.api.ads.dfp.lib.factory.DfpServicesInterface</code>
<code>com.google.api.ads.dfp.lib.factory.BaseDfpServices</code>
<code>com.google.api.ads.dfp.lib.factory.helper.DfpServiceClientFactoryHelper</code>
<code>com.google.api.ads.dfp.axis.DfpAxisHeaderHandler</code>
<code>com.google.api.ads.dfp.axis.DfpAxisModule</code>
<code>com.google.api.ads.dfp.jaxws.DfpJaxWsHeaderHandler</code>
<code>com.google.api.ads.dfp.jaxws.DfpJaxWsModule</code>
<code>com.google.api.ads.dfp.lib.DfpModule</code>
<code>com.google.api.ads.dfp.lib.DfpSoapModule</code>
<code>com.google.api.ads.dfp.lib.DfpProductFrameworkModule</code>
<code>com.google.api.ads.dfp.lib.soap.DfpHttpHeaderHandler</code>
<code>com.google.api.ads.dfp.lib.utils.DfpInternals</code>
<code>com.google.api.ads.dfp.lib.conf.DfpApiConfiguration</code>
<code>com.google.api.ads.dfp.lib.conf.DfpApiConfigurationModule</code>
<code>com.google.api.ads.dfp.lib.conf.DfpLibConfiguraiton</code>
<code>com.google.api.ads.dfp.lib.client.DfpServiceDescriptor</code>
<code>com.google.api.ads.dfp.jaxws.factory.DfpServices</code>
<code>com.google.api.ads.dfp.lib.client.DfpServiceClient</code>
<code>com.google.api.ads.dfp.axis.factory.DfpServices</code>
<code>com.google.api.ads.dfp.lib.client.DfpSession</code>
   </td>
   <td>
<code>com.google.api.ads.admanager.lib.factory.AdManagerServiceClientFactory</code>
<code>com.google.api.ads.admanager.lib.factory.AdManagerServicesInterface</code>
<code>com.google.api.ads.admanger.lib.factory.BaseAdManagerServices</code>
<code>com.google.api.ads.admanager.lib.factory.helper.AdManagerServiceClientFactoryHelper</code>
<code>com.google.api.ads.admanager.axis.AdManagerAxisHeaderHandler</code>
<code>com.google.api.ads.admanager.axis.AdManagerAxisModule</code>
<code>com.google.api.ads.admanager.jaxws.AdManagerJaxWsHeaderHandler</code>
<code>com.google.api.ads.admanager.jaxws.AdManagerJaxWsModule</code>
<code>com.google.api.ads.admanager.lib.AdManagerModule</code>
<code>com.google.api.ads.admanager.lib.AdManagerSoapModule</code>
<code>com.google.api.ads.admanager.lib.AdManagerProductFrameworkModule</code>
<code>com.google.api.ads.admanager.lib.soap.AdManagerHeaderHandler</code>
<code>com.google.api.ads.admanager.lib.utils.AdManagerInternals</code>
<code>com.google.api.ads.admanager.lib.conf.AdManagerApiConfiguration</code>
<code>com.google.api.ads.admanager.lib.conf.AdManagerApiConfigurationModule</code>
<code>com.google.api.ads.admanager.lib.conf.AdManagerLibConfiguraiton</code>
<code>com.google.api.ads.admanager.lib.client.AdManagerServiceDescriptor</code>
<code>com.google.api.ads.admanager.jaxws.factory.AdManagerServices</code>
<code>com.google.api.ads.admanager.lib.client.AdManagerServiceClient</code>
<code>com.google.api.ads.admanager.axis.factory.AdManagerServices</code>
<code>com.google.api.ads.admanager.lib.client.AdManagerSession</code>
   </td>
  </tr>
</table>

