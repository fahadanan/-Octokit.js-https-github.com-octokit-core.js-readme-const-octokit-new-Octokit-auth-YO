# Overview
If you use the AdWords API, you'll need to create a [Selector](https://developers.google.com/adwords/api/docs/reference/latest/CampaignService.Selector) for each of your `get` service requests. Creating a `Selector` consists of some common steps:

1. Set the [fields](https://developers.google.com/adwords/api/docs/reference/latest/CampaignService.Selector#fields) list
1. Add any [predicates](https://developers.google.com/adwords/api/docs/reference/latest/CampaignService.Selector#predicates)
1. Add [ordering](https://developers.google.com/adwords/api/docs/reference/latest/CampaignService.Selector#ordering)
1. Set up [paging](https://developers.google.com/adwords/api/docs/reference/latest/CampaignService.Selector#paging)

All of this code tends to look similar from case to case, so the client library includes a `SelectorBuilder` for both Axis and JAX-WS to streamline the creation of `Selectors`.

* [Axis SelectorBuilder](https://github.com/googleads/googleads-java-lib/blob/master/modules/adwords_axis/src/main/java/com/google/api/ads/adwords/axis/utils/v201710/SelectorBuilder.java)
* [JAX-WS SelectorBuilder](https://github.com/googleads/googleads-java-lib/blob/master/modules/adwords_appengine/src/main/java/com/google/api/ads/adwords/jaxws/utils/v201710/SelectorBuilder.java)

# Sample usage
Let's take a look at a common use case - creating a `Selector` to issue a [CampaignService.get](https://developers.google.com/adwords/api/docs/reference/latest/CampaignService#get) request. In this example, we want to fetch all `Campaigns` using the following `Selector` setup:

* **fields**: `[Id, Name, StartDate, EndDate, Status, Amount]`
* **predicates**:
   * `Status IN ['ENABLED','PAUSED']`
   * `Amount < 100000000` (budget amount < $100)
* **ordering**: `Name ASCENDING`
* **paging**: `100` results per page

Creating the `Selector` for this request using a `SelectorBuilder` is very straightforward. Simply chain together the calls to set each of the `Selector` attributes, then call `build()` to create a new `Selector` with the attributes of the builder.

    int pageSize = 100;
    int offset = 0;

    // Create the SelectorBuilder.
    SelectorBuilder builder = new SelectorBuilder()
        .fields(
            CampaignField.Id,
            CampaignField.Name,
            CampaignField.StartDate,
            CampaignField.EndDate,
            CampaignField.Status)
        .in(CampaignField.Status, "ENABLED", "PAUSED")
        .lessThan(CampaignField.Amount, 100 * 1000000L)
        .orderAscBy(CampaignField.Name)
        .offset(offset)
        .limit(pageSize);

    CampaignPage page = null;
    do {
      // Get campaigns. Invoke build() on the builder for each get request
      // to apply any offset changes.
      page = campaignService.get(builder.build());

      // Display campaigns.
      ...

      // Increment the offset to prepare for the next get request
      builder.increaseOffsetBy(pageSize);

      // Keep track of the offset so the loop will exit when all pages
      // have been retrieved.
      offset += pageSize;
    } while (offset < page.getTotalNumEntries());

That's all there is to it! Compare the code above to the same code to build a `Selector` directly, and you'll see that the builder-based approach requires far less code and is much more readable.

# Further resources
The example above used just a few of the methods for setting `Predicates` (`in` and `lessThan`). The `SelectorBuilder` supports other types of `Predicates` as well, such as `equals`, `startsWith`, `containsAny`, etc. There's even an `equalsId(Long id)` method for filtering by `Id` (a common requirement). For the complete `SelectorBuilder` API, check out the Axis and JAX-WS implementations here:

* [Axis SelectorBuilder](https://github.com/googleads/googleads-java-lib/blob/master/modules/adwords_axis/src/main/java/com/google/api/ads/adwords/axis/utils/v201710/SelectorBuilder.java)
* [JAX-WS SelectorBuilder](https://github.com/googleads/googleads-java-lib/blob/master/modules/adwords_appengine/src/main/java/com/google/api/ads/adwords/jaxws/utils/v201710/SelectorBuilder.java)

Both implementations are also included in the client library's [JavaDoc](http://googleads.github.io/googleads-java-lib).

Finally, all of the AdWords API [examples](https://github.com/googleads/googleads-java-lib/tree/master/examples/adwords_axis/src/main/java/adwords/axis) in the client library now use `SelectorBuilders` for `get` requests.
