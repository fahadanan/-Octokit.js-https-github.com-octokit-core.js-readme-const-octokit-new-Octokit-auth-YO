If you use the [BatchJobService](https://developers.google.com/adwords/api/docs/reference/latest/BatchJobService) in the AdWords API, you probably want to use the `BatchJobHelper` utility in the client library. This utility handles the following interactions with Google Cloud Storage for you transparently:

* Uploading operations for a given `BatchJob`, either incrementally or via a single method call.
* Downloading the results of a `BatchJob` once it completes, and transforming those results into POJOs.

To see the utility in action, check out the code snippets in the [Batch Processing guide](https://developers.google.com/adwords/api/docs/guides/batch-jobs), as well as the [code examples](https://developers.google.com/adwords/api/docs/guides/batch-jobs#code_examples) at the end of the guide.

