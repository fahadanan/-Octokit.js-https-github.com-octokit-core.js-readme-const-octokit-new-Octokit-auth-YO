This guide will walk you through how to setup OAuth2 for API access on behalf of your clients using [web flow](https://developers.google.com/identity/protocols/OAuth2#webserver).

### Step 1 - Creating OAuth2 credentials

Follow the steps for the product you're using to generate a **client ID and secret**, then come back to this page.

* [AdWords](https://developers.google.com/adwords/api/docs/guides/authentication)
* [DFP](https://developers.google.com/doubleclick-publishers/docs/authentication)

### Step 2 - Setting up the client library

If you authorizing on behalf of multiple users then you need to build OAuth2 sign-in flow into your application as part of adding OAuth2 support for your application. This involves three steps:

1. Implement an AbstractAuthorizationCodeServlet

   See the [authorization code flow](https://developers.google.com/api-client-library/java/google-oauth-java-client/oauth2#authorization_code_flow) here for more details. 

1. Register the servlet's URL as a valid OAuth2 callback URL in the Developer Console.

1. Configure your AdsSession object to use the OAuth2 Credential.

You can refer to the advanced OAuth example for the product you're using for a full example:

* [AdWords](https://github.com/googleads/googleads-java-lib/blob/master/examples/adwords_axis/src/main/java/adwords/axis/auth/AdvancedCreateCredentialFromScratch.java)
* [DFP](https://github.com/googleads/googleads-java-lib/blob/master/examples/dfp_axis/src/main/java/dfp/axis/auth/AdvancedCreateCredentialFromScratch.java)