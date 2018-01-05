## Thread safety in the library

Special care must be taken when using objects in multiple threads. In
general, we don’t recommend sharing instances of our classes in multiple
threads unless they are explicitly marked with the `@ThreadSafe` annotation.
Below is some code with comments explaining why.

```java
// The credential object is thread-safe.
Credential credential = //...;

// The session object is not thread-safe.
AdWordsSession session = new AdWordsSession.Builder() // Builders are not thread-safe.
    .fromFile()
    .withCredential(credential) // Uses thread-safe credential
    .build(); // /build() returns a brand new object every time.

// Threadsafe and lightweight.
AdWordsServices adWordsServices = AdWordsServices.getInstance();

// The Service “stubs” are NOT thread-safe.
// Stubs retain a reference to the session they are constructed for and will see any
// changes made to the session within the same thread. When used in multiple threads it
// is possible to see the object in an inconsistent state, causing bugs.
MediaServiceInterface mediaService =
    adWordsServices.get(session, MediaServiceInterface.class);

Image image = new Image();
// Utilities such as Media are usually static and therefore thread-safe.
image.setData(Media.getMediaDataFromUrl("https://goo.gl/3b9Wfh"));
image.setType(MediaMediaType.IMAGE);

// ReportDownloader retains a reference to the session and is not thread-safe.
ReportDownloadResponse response =
    adWordsServices.getUtility(session, ReportDownloaderInterface.class)
        .downloadReport(reportDefinition);
```


## Service stubs
Service stubs are the objects used to invoke webservice calls. CampaignService
and CompanyService are two examples for AdWords and DFP respectively. These
objects *are not* thread-safe and should have unique objects used per thread.

## Service locators
Each product has a Services object such as AdWordsServices and DfpServices.
These objects **are** thread-safe and can be re-used across threads. However, as
mentioned above, the Service stubs returned by these classes **are not**.

Note that when you request a service stub from a service locator, you provide
the session that will be used to make requests. If you make changes to the
session, they will be used for all successive API calls.

# Sessions

AdWordsSession and DfpSession hold information such as credentials and options
used when making requests. These objects **are not** thread-safe. It is
possible to make writes to this object (`.setClientCustomerId` in
AdWordsSession, for example) which might let other threads view this object in
an inconsistent state.

### OAuth2
The Credential object **is** thread-safe. Therefore, you can reuse the same
credential object for different sessions across multiple threads.

### AdWords
Here’s a pattern than can be use to perform the same operation over multiple
accounts:

```java
AdWordsSession.Builder builder = new AdWordsSession.Builder()
    .withDeveloperToken(token)
    .withCredential(credential)
    .withUserAgent(userAgent);
for (String customerId: customerIds) {
  new FooThread(builder.withClientCustomerId(customerId).build()).start();
}
```

### DFP
Here’s a pattern than can be use to perform different operations with the same
account:

```
DfpSession.Builder builder = new DfpSession.Builder()
    .withNetworkCode(networkCode)
    .withCredential(credential)
    .withUserAgent(userAgent);

new FooThread(builder.build()).start();
new BarThread(builder.build()).start();
new BazThread(builder.build()).start();
```

## Utilities
Most of the client library utility classes are static and therefore thread-safe.
Classes such as the AdWords ReportDownloader are lightweight but **not**
thread-safe (as it stores a reference to the session).

