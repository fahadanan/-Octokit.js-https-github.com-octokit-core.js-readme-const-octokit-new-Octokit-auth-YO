An authorization method is a schema the client application uses to gain access to account information. AdWords, DFP, and DFA support several authorization methods including ClientLogin and OAuth2.0. If you are still using an email address and a password to access the API, you are using the ClientLogin method which is now [deprecated and is scheduled for sunset](https://developers.google.com/accounts/docs/AuthForInstalledApps).

Previously, in a [blog post](http://googleadsdeveloper.blogspot.com/2012/08/oauth-in-ads-apis.html) we've covered general aspects of OAuth2.0 authorization and its benefits. This page focuses on how to use OAuth2.0 methods within the [Java client library](https://github.com/googleads/googleads-java-lib).

##Migrating to OAuth2.0 from ClientLogin

If you are coming from ClientLogin, you will need to ask yourself:

  1. (**Installed Application**) Is my application going to run offline or only ever use one user to log into the API?
  1. (**Web Application**) Is my application going to be a web application and request the user log in?

If the answer to (1) is yes, then you will be using offline credentials with an installed application flow. Specifically, you will be using the OfflineCredentials utility in this library.

If the answer to (2) is yes, then you will be setting up a web server to listen for redirects as the user logs in. You will need to register an application in the developers console that can do this.

For a screencast on how to do this, see: 

[![IMAGE ALT TEXT HERE](http://img.youtube.com/vi/82_fsNg2768/0.jpg)](http://www.youtube.com/watch?v=82_fsNg2768)

##Creating an application identifier

**Note:** The examples below are for DFP. Equivalent examples for AdWords can be found in the [auth folder of the AdWords module](https://github.com/googleads/googleads-java-lib/tree/master/examples/adwords_axis/src/main/java/adwords/axis/auth).

In order to use the OAuth2.0 authorization with Google services, you need to create an application identifier and secret (also known as client ID and client secret).

Visit [Google Developers Console](https://console.developers.google.com)

**You do not need to enable any APIs for AdWords, DFA, and DFP in the console.**

1. Create a new project (or use an existing project)
1. Click the project to open
1. On the left panel, click on **APIs & auth** to expand the menu, and then click on **Credentials**
1. Click on **CREATE NEW CLIENT ID**
1. Choose either **Installed Application** or **Web Application** depending on the style of your application
    * If you wish to use our sample code or the OfflineCredentials utility, i.e. [dfp/axis/auth/GetRefreshToken.java](https://github.com/googleads/googleads-java-lib/blob/master/examples/dfp_axis/src/main/java/dfp/axis/auth/GetRefreshToken.java) to generate a refresh token, then you have to choose **Installed Application**.
    * If you choose **Web Application**, you will also need to write your own web application that can complete the OAuth 2.0 flow. See [AdvancedCreateCredentialFromScratch.java](https://github.com/googleads/googleads-java-lib/blob/master/examples/dfp_axis/src/main/java/dfp/axis/auth/AdvancedCreateCredentialFromScratch.java) for an example of how to do that.
1. Click **Create Client ID** to complete the steps. Client ID and client secret will be available under the new application.

    <img src="https://developers.google.com/adwords/api/images/oauth2-client-id-secret.png" />

1. Make sure all of the required fields are filled and saved on the **Consent Screen**.

    <img src="https://developers.google.com/adwords/api/images/oauth2-consent-screen-config.png" />

The Client ID and secret values are the parameters you will need in the next step.

##Setting up the client library

All required settings can be configured via the relevant API configuration file [ads.properties](https://github.com/googleads/googleads-java-lib/blob/master/examples/dfp_axis/src/main/resources/ads.properties). For the example projects, you will find these in the root of the project (for the jars distribution) or in the src/main/resources directory (for the maven distribution).

The required parameters are:
```
# If you do not have a client ID or secret, please create a project in the
# Developers console. See the following link for more information:
# https://github.com/googleads/googleads-java-lib/wiki/Using-OAuth2.0
api.dfp.clientId=INSERT_CLIENT_ID_HERE
api.dfp.clientSecret=INSERT_CLIENT_SECRET_HERE
...
```

If you are using the **OfflineCredentials** utility, you will need to set the api.dfp.refreshToken property as well. You will generate it using the [dfp/axis/auth/GetRefreshToken.java](https://github.com/googleads/googleads-java-lib/blob/master/examples/dfp_axis/src/main/java/dfp/axis/auth/GetRefreshToken.java) example.

##Using offline credentials (OfflineCredentials)

To make it easy to use our library offline (i.e. like a service account would), we offer the OfflineCredentials utility. This utility uses a refresh token that needs to be generated once. The general flow is:

  1. Create an application identifier
  1. Retrieve your client ID and secret (which should have a redirect URI of urn:ietf:wg:oauth:2.0:oob)
  1. Enter your client ID and secret into the ads.properties file.
  1. Run GetRefreshToken.java
  1. Enter your refresh token into the ads.properties file.
  1. Use the OfflineCredentials utility as shown in our [examples](https://github.com/googleads/googleads-java-lib/blob/master/examples/dfp_axis/src/main/java/dfp/axis/v201411/networkservice/GetCurrentNetwork.java#L52)

When you run GetRerefreshToken.java, you will get an authentication URL and a  prompt for the verification string when running this example from the command line. Copy and paste the auth URL into a browser to obtain the verification string. You will need to log in with your account credentials that you want to make API calls with.

<img src="https://lh3.googleusercontent.com/-JT98Oob54ro/UGBKTDeCjkI/AAAAAAAAAHM/oU7D0D1nJc0/s716/blog1.png" />

Once you type (or copy) the verification string back to the example, you should see the query result. With this result, you should be able to update your [ads.properties](https://github.com/googleads/googleads-java-lib/blob/master/examples/dfp_axis/src/main/resources/ads.properties) with the correct `refreshToken`.

###<a name="ServiceAccounts"></a>Service Accounts

You may be tempted to use a service account to access the API. However, to use
a service account, you must be a Google Apps for Business customer, with the
ability to **impersonate users of your network**. If you are, then you can use the
AdvancedCreateCredentialFromScratch example as a starting point for constructing
a service account Credential. See
https://code.google.com/p/google-api-java-client/wiki/OAuth2#Service_Accounts
on how to do this.

If you are unable to use service accounts, but still want to only perform the
OAuth2 flow once, consider creating an offline credential using the
OfflineCredentials utility.

For general information on the service accounts flow, please see the [OAuth2 Documentation](https://developers.google.com/accounts/docs/OAuth2ServiceAccount).
