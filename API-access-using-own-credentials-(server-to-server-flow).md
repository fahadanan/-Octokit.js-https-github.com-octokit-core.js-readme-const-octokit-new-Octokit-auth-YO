This guide will walk you through how to setup OAuth2 for API access using your
own credentials using [server to server flow]
(https://developers.google.com/identity/protocols/OAuth2#serviceaccount). These
steps only need to be done once, unless you revoke or delete your OAuth2
credentials.

### Step 1 - Creating OAuth2 credentials

Follow the steps for the product you're using to generate a **service account ID
and a *JSON key file**, then come back to this page.

>   If you're an AdWords user, please note that this flow requires a
    [Google Apps Domain](https://apps.google.com).

*   [AdWords](https://developers.google.com/adwords/api/docs/guides/service-accounts#steps)
*   [DFP](https://developers.google.com/doubleclick-publishers/docs/authentication)

### Step 2 - Setting up the client library

1.  In your `ads.properties` file, set the full path to your JSON key file
    ```
    api.dfp.jsonKeyFilePath=INSERT_PATH_TO_JSON_KEY_FILE_HERE
    ```