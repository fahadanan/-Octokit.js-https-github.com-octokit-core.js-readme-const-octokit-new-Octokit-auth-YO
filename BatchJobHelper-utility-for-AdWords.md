# Overview

If you use the [BatchJobService](https://developers.google.com/adwords/api/docs/reference/latest/BatchJobService) in the AdWords API, you probably want to use an implementation of the
[BatchJobHelperInterface](https://github.com/googleads/googleads-java-lib/blob/master/modules/ads_lib/src/main/java/com/google/api/ads/adwords/lib/utils/BatchJobHelperInterface.java) utility in the client library. This utility handles the following interactions with Google Cloud Storage for you transparently:

* Uploading operations for a given `BatchJob`, either incrementally or via a single method call.
* Downloading the results of a `BatchJob` once it completes, and transforming those results into POJOs.

To see the utility in action, check out the code snippets in the [Batch Processing guide](https://developers.google.com/adwords/api/docs/guides/batch-jobs), as well as the [code examples](https://developers.google.com/adwords/api/docs/guides/batch-jobs#code_examples) at the end of the guide.

# Key objects

## [BatchJobHelperInterface](https://github.com/googleads/googleads-java-lib/blob/master/modules/ads_lib/src/main/java/com/google/api/ads/adwords/lib/utils/BatchJobHelperInterface.java)
  *  Provides methods for uploading batch job operations, either in a single request or incrementally across multiple requests.
  *  Provides method for downloading the results of a completed batch job.
  *  An implementation exists for each AdWords API version, both in Axis and JAX-WS.
     * Axis: `com.google.api.ads.adwords.axis.utils.API_VERSION.batchjob.BatchJobHelper`
     * JAX-WS: `com.google.api.ads.adwords.jaxws.utils.API_VERSION.batchjob.BatchJobHelper`
  *  Implementations are *not* thread safe, so you should not share instances across threads.

## [BatchJobUploadStatus](https://github.com/googleads/googleads-java-lib/blob/master/modules/ads_lib/src/main/java/com/google/api/ads/adwords/lib/utils/BatchJobUploadStatus.java)
  *  Stores state information of the progress of uploads for a given `BatchJob`.
  *  Primarily used for *incremental* uploads.
  *  Immutable and thread safe.
  *  Implements `java.io.Serializable` so you can save this object and deserialize it later to resume the operations upload for a `BatchJob`.

