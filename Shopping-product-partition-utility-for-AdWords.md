# Overview
Are you using the AdWords API to manage your [Shopping Campaigns](//www.google.com/ads/shopping/shopping-campaigns.html)?

Do you want an easier solution for managing your [ProductPartition](//developers.google.com/adwords/api/docs/reference/latest/AdGroupCriterionService.ProductPartition) criteria
(**Product Groups**)?

If yes, then this guide is for you! The
[Google Ads APIs Client Library for Java](//github.com/googleads/googleads-java-lib)
now includes a number of utilities that will greatly simplify the process
of maintaining your `ProductPartition` trees.

# Key classes
* [ProductPartitionNode](//googleads.github.io/googleads-java-lib/3.12.0/com/google/api/ads/adwords/axis/utils/v201802/shopping/ProductPartitionNode.html)
    * Represents a node in the `ProductPartition` tree of a Shopping Campaign's AdGroup.
    * Includes methods for manipulating nodes (setting bids, setting tracking URL templates,
    modifying custom parameters, changing a node's type to/from `SUBDIVISION` or `UNIT`, excluding a
    node, etc.)
    * All methods return the mutated `ProductPartitionNode` to allow you to chain together multiple method calls.
* [ProductPartitionTree](//googleads.github.io/googleads-java-lib/3.12.0/com/google/api/ads/adwords/axis/utils/v201802/shopping/ProductPartitionTree.html)
    * Container that holds on to the root `ProductPartitionNode` of an AdGroup's `ProductPartition` tree.
    * Tracks changes made to the tree structure, and returns the collection of
    [AdGroupCriterionOperation](//developers.google.com/adwords/api/docs/reference/latest/AdGroupCriterionService.AdGroupCriterionOperation)
    objects that will modify the AdGroup to match the current state of the tree.
* [ProductDimensions](//googleads.github.io/googleads-java-lib/3.12.0/com/google/api/ads/adwords/axis/utils/v201802/shopping/ProductDimensions.html)
    * Static utility class with factory methods for each available subclass of
    [ProductDimension](//developers.google.com/adwords/api/docs/reference/latest/AdGroupCriterionService.ProductDimension).

# Sample use case
Let's say you have the partition nodes below in your tree, and you want to:

* Change the bid on the `ROOT/ProductType Level1 shoes/ProductTypeLevel2 athletic shoes/Condition used`
node from $2.50 to $2.00.
* Collapse the `ROOT/ProductType Level1 shoes/ProductTypeLevel2 walking shoes`
node, removing all of its children and setting its bid to $3.25.

```
    ROOT
      ProductType Level1 shoes
        ProductType Level2 athletic shoes
          Condition new $2.50
          Condition used $1.00
          Other - exclude from bidding
        ProductType Level2 walking shoes
          Condition new $3.50
          Condition used $1.25
          Other $1.00
```

### 1. Create a new `ProductPartitionTree` object for your AdGroup
The `ProductPartitionTree` class has a `createAdGroupTree` factory method that
will return a new instance of the class for a specified AdGroup ID.

    // Build a new ProductPartitionTree using the ad group's current set of criteria.
    ProductPartitionTree partitionTree =
        ProductPartitionTree.createAdGroupTree(adWordsServices, session, adGroupId);

### 2. Get the tree's root `ProductPartitionNode`

    ProductPartitionNode rootNode = partitionTree.getRoot();

### 3. Navigate the tree and modify nodes
To make your code more concise, add a static import of all of the methods on
`ProductDimensions`. You'll use these to create `ProductDimension` objects. This
will save you from typing a significant amount of boilerplate code.

    import static com.google.api.ads.adwords.axis.utils.v201802.shopping.ProductDimensions.*;

Using the `rootNode` from the previous step, change the bid for
`ROOT/ProductType Level1 shoes/ProductTypeLevel2 athletic shoes/Condition used`
with the following code.

    // The createType and createCanonicalCondition methods below are from
    // the static import of ProductDimensions.
    root
        .getChild(
            createType(ProductDimensionType.PRODUCT_TYPE_L1, "shoes"))
        .getChild(
            createType(ProductDimensionType.PRODUCT_TYPE_L2, "athletic shoes"))
        .getChild(
            createCanonicalCondition(ProductCanonicalConditionCondition.NEW))
        .setBid(2000000L);

Collapse
`ROOT/ProductType Level1 shoes/ProductTypeLevel2 walking shoes` and set its bid
as follows:

    root
        .getChild(
            createType(ProductDimensionType.PRODUCT_TYPE_L1, "shoes"))
        .getChild(
            createType(ProductDimensionType.PRODUCT_TYPE_L2, "walking shoes"))
        .asBiddableUnit()
        .setBid(3250000L);

### 4. Get the mutate operations to apply your changes
Now that you've modified the `ProductPartitionTree` and its nodes, use the
`getMutateOperations` method to get all of the corresponding
[AdGroupCriterionOperation](//developers.google.com/adwords/api/docs/reference/latest/AdGroupCriterionService.AdGroupCriterionOperation)s.

    List<AdGroupCriterionOperation> mutateOperations =
        tree.getMutateOperations();

This method performs a lot of the heavy lifting for you. It compares the original
partition tree to the current state of the tree, then generates the operations
required to apply the differences to your AdGroup.

### 5. Submit the operations
This is just like any other AdGroup criteria modification. Simply pass the
operations from the previous step in your
[AdGroupCriterionService.mutate](//developers.google.com/adwords/api/docs/reference/latest/AdGroupCriterionService#mutate)
call.

    // Get the ad group criterion service.
    AdGroupCriterionServiceInterface adGroupCriterionService =
        adWordsServices.get(session, AdGroupCriterionServiceInterface.class);
    adGroupCriterionService.mutate(mutateOperations.toArray(new AdGroupCriterionOperation[0]));

### 6. Use `createAdGroupTree` to get the updated `ProductPartitionTree`
Just as in the first step, use `createAdGroupTree` to get a new instance of
`ProductPartitionTree` that reflects the changes you just made.

    // The request was successful, so create a new ProductPartitionTree based on the updated state
    // of the ad group.
    partitionTree = ProductPartitionTree.createAdGroupTree(adWordsServices, session, adGroupId);

**Important:** Don't forget this step! If you don't get a new instance of
`ProductPartitionTree` after your `mutate` call, the utility will not generate
the proper set of [AdGroupCriterionOperation](//developers.google.com/adwords/api/docs/reference/latest/AdGroupCriterionService.AdGroupCriterionOperation)s
the next time you call `ProductPartitionTree.getMutateOperations()`.

### 7. Rinse and repeat
Repeat steps 2-6 to make further changes to the AdGroup's partition tree.

# Additional use cases
Don't forget to include steps 4-6 above after mutating your tree. Otherwise,
nothing will actually change in your AdGroup.

### Convert a UNIT node to a SUBDIVISION node
The example below will convert the node at
`ROOT/ProductType Level1 shoes/ProductType Level2 walking shoes'
to a SUBDIVISION node, then add the following child UNIT nodes:

* Condition = NEW $1.00
* Condition = USED $1.50
* Condition = `null` (the *All other* node) exclude from bidding

In addition, this will set the tracking URL template and custom parameters for
the NEW child UNIT node.

**Tip:** Tracking URL template and custom parameters are only supported on
biddable UNIT nodes.

```
    ProductPartitionNode walkingShoes = root
        .getChild(
            createType(ProductDimensionType.PRODUCT_TYPE_L1, "shoes"))
        .getChild(
            createType(ProductDimensionType.PRODUCT_TYPE_L2, "walking shoes"))
        .asSubdivision();
    walkingShoes.addChild(
            createCanonicalCondition(ProductCanonicalConditionCondition.NEW))
        .asBiddableUnit().setBid(1000000L)
        .setTrackingUrlTemplate(
            "http://www.example.com/tracking?season={_season}&promocode={_promocode}&u={lpurl}")
        .putCustomParameter("season", "holidays")
        .putCustomParameter("promocode", "NYC123");
    walkingShoes
        .addChild(
            createCanonicalCondition(ProductCanonicalConditionCondition.USED))
        .asBiddableUnit().setBid(1500000L);
    walkingShoes.addChild(createCanonicalCondition(null)).asExcludedUnit();
```

### Exclude a node from bidding
The example below will exclude the node at
`ROOT/ProductType Level1 shoes/ProductType Level2 walking shoes/Condition USED`
from bidding.

    root
        .getChild(
            createType(ProductDimensionType.PRODUCT_TYPE_L1, "shoes"))
        .getChild(
            createType(ProductDimensionType.PRODUCT_TYPE_L2, "walking shoes"))
        .getChild(
            createCanonicalCondition(ProductCanonicalConditionCondition.USED)
        .asExcludedUnit();

### Remove all nodes from the tree and set its bid

    root.removeAllChildren().asBiddableUnit().setBid(1500000L);

### Create a `ProductPartitionTree` from a collection of `AdGroupCriterion` objects
The `createAdGroupTree` method is a convenient way to construct a `ProductPartitionTree`,
but what if you don't want to delegate the
[AdGroupCriterionService.get](//developers.google.com/adwords/api/docs/reference/latest/AdGroupCriterionService#get)
call to the utility?

Fear not! The `createAdGroupTree` method is overloaded so you can pass your
criteria objects.

**Note:** If you use this approach, make sure that your `Selector` includes all of
the fields in `ProductPartitionTree.REQUIRED_SELECTOR_FIELDS`. Otherwise,
your `AdGroupCriterion` objects may be missing attributes that the utility
requires.

    List<String> selectorFields = Lists.newArrayList(
        ProductPartitionTree.REQUIRED_SELECTOR_FIELDS);
    // Add any other fields you'd like to retrieve.
    selectorFields.add(...);
    selectorFields.add(...);

    Selector selector = new SelectorBuilder()
        .fields(ProductPartitionTree.REQUIRED_SELECTOR_FIELDS.toArray(new String[0]))
        ... // set other criteria and selector options
        .build();

    // Retrieve the AdGroupCriterion objects using the mechanism of your choice.
    List<AdGroupCriterion> adGroupCriteria = ...;

    // Use the overload that takes an AdGroup ID, BiddingStrategyConfiguration,
    // and AdGroupCriterion collection.
    ProductPartitionTree tree = ProductPartitionTree
        .createAdGroupTree(adGroupId,
            adGroup.getBiddingStrategyConfiguration(), adGroupCriteria);


# Code example
The
[AddProductPartitionTree code example](//github.com/googleads/googleads-java-lib/blob/master/examples/adwords_axis/src/main/java/adwords/axis/v201802/shoppingcampaigns/AddProductPartitionTree.java)
uses the `ProductPartitionTree` to replace the `ProductPartition` criteria of an AdGroup.
