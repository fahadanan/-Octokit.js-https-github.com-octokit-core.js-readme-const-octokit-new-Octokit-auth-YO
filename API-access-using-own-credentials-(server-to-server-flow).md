This guide will walk you through how to setup OAuth2 for API access using your
own credentials using [server to server flow](https://developers.google.com/identity/protocols/OAuth2#serviceaccount). These
steps only need to be done once, unless you revoke or delete your OAuth2
credentials.

### Step 1 - Creating OAuth2 credentials

Follow the steps for the product you're using to generate a **service account ID
and a *JSON key file**, then come back to this page.

>   If you're an AdWords user, please note that this flow requires a
    [Google Apps Domain](https://apps.google.com).

*   [AdWords](https://developers.google.com/adwords/api/docs/guides/authentication#setting_up_service_account_access)
*   [DFP](https://developers.google.com/doubleclick-publishers/docs/authentication#service)

### Step 2 - Setting up the client library

#### AdWords

In your `ads.properties` file, set the full path to your JSON key file, and the
email address of the user to impersonate:

```
api.adwords.jsonKeyFilePath=INSERT_PATH_TO_JSON_KEY_FILE_HERE
api.adwords.serviceAccountUser=INSERT_SERVICE_ACCOUNT_USER_HERE
```

#### DFP

In your `ads.properties` file, set the full path to your JSON key file:

```
api.dfp.jsonKeyFilePath=INSERT_PATH_TO_JSON_KEY_FILE_HERE
```