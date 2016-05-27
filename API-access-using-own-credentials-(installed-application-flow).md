This guide will walk you through how to setup OAuth2 for API access using your
own credentials using [installed application flow]
(https://developers.google.com/identity/protocols/OAuth2#installed). These steps
only need to be done once, unless you revoke, delete, or need to change the
allowed scopes for your OAuth2 credentials.

### Step 1 - Creating OAuth2 credentials

Follow the steps for the product you're using to generate a
**client ID and secret**, then come back to this page.

* [AdWords](https://developers.google.com/adwords/api/docs/guides/authentication#installed)

### Step 2 - Setting up the client library

1.  In your `ads.properties` file, set the client ID and secret.

    ```
    api.adwords.clientId=INSERT_CLIENT_ID_HERE
    api.adwords.clientSecret=INSERT_CLIENT_SECRET_HERE
    ```

1.  In a terminal, navigate to the **GetRefreshToken.java** example for your
    product.

   * [AdWords GetRefreshToken.java](https://github.com/googleads/googleads-java-lib/blob/master/examples/adwords_axis/src/main/java/adwords/axis/auth/GetRefreshToken.java)

1.  Run this example.

    >   **This is an interactive example, which will require you to provide
        input.** This example will read the client ID and secret from the
        `ads.properties` file we edited in the step above. If your
        `ads.properties` file is not in the default location, you will need to
        edit the example to pass the path to your `ads.properties` file when it
        constructs the `GoogleClientSecrets` object.

    *   **If using Maven with Eclipse**

        Navigate in your project to
        `src/main/java/adwords/axis/auth/GetRefreshToken.java`
        and run the example.

    *   **If using Maven from the command line**

        ```
        $ cd path/to/extracted/distribution
        $ mvn -X compile
        $ mvn -X exec:java -Dexec.mainClass="adwords.axis.auth.GetRefreshToken"
        ```

    *   **If using jars**

        Navigate in your project to `src/adwords/axis/auth/GetRefreshToken.java`
        and run the example.

1.  The example will prompt you to visit a URL where you will need to allow the
    OAuth2 credentials to access your AdWords account on your behalf.

    ```
    Paste this url in your browser:
    https://accounts.google.com/o/oauth2/auth?access_type=offline&client_id=****...
    ```

    Navigate to the URL in a private browser session or an incognito window. Log
    in with the same Google account you use to access AdWords. Click
    **Allow** on the OAuth2 consent screen.

    ![Consent screen allow](https://developers.google.com/ads/images/oauth2-consent-allow.png)

1.  An authorization code will be shown to you. Copy and paste the verification
    code into the Eclipse console or **command line** where you're running the
    **GetRefreshToken.java** example and press enter. The example should
    complete and display an offline refresh token.

    ![Authorization code](https://developers.google.com/ads/images/oauth2-authorization-code.png)

    ```
    Type the code you received here:
    ****
    Your refresh token is: ****
    In your ads.properties file, modify:

    api.adwords.refreshToken=****
    ```

1.  Copy the refresh token into your `ads.properties` file.
