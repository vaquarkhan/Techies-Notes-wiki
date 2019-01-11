## Indexes can increase read performance and decrease write performance.

The index itself occupies space on disk and memory (when used). So, if space or memory are issues or expensive then too many indexes could be a problem. When data is inserted/updated/deleted, then the index needs to be maintained as well as the original data. This slows down updates and locks the tables (or parts of the tables), which can affect query processing.

A small number of indexes on each table are reasonable. These should be designed with the typical query load in mind. If you index every column in every table, then data modifications would slow down. If your data is static, then this is not an issue. However, eating up all the memory with indexes could be an issue.

In perfect world, you'd like to index columns, that appear in WHERE clause or JOIN condition,If the keys of your index and filters in your query are not selective enough, then the index will be ignored (regardless of what's in your INCLUDE columns).

Every index you create has overhead for INSERT and UPDATE statements; more so for "bigger" indexes. (Bigger applies to INCLUDE columns as well.)

So while you could in theory create a multitude of big indexes with include columns to match all the permutations of access paths: it would be very counter-productive.

## Guidelines for choosing columns for composite indexes

Consider creating a composite index on columns that are frequently used together in WHERE clause conditions combined with AND operators, especially if their combined selectivity is better than the selectivity of either column individually. Consider indexing columns that are used frequently to join tables in SQL statements.


* Better Selectivity:
	Sometimes two or more columns, each with poor selectivity, can be combined to form a composite index with good selectivity.

* Adding Data Storage:
	If all the columns selected by the query are in the composite index return these values from the index without accessing the table. However in this case, it's better to use an IOT (Index Only Table).



## Creating Key Indexes

Creating a key index is a good way to improve query performance when data is partitioned using a key or a field value.

There are two issues to note with key indexes:

* The key index is not sorted. Without sorting, you can only do equality tests. Other comparisons are not possible. To obtain a sorted index on your primary keys, create a functional index on the attribute used as the primary key.

* The query service is not automatically aware of the relationship between the region values and keys. For this, you must create the key index.
Note: Using a key-index with an explicit type=‘range’ in the cache.xml will lead to an exception. Key indexes will not be used in 'range’ queries.

> create index --name=test_Index --expression=test_Index --type=range --region=/test_region


## Map index (Range)

To assist with the quick lookup of multiple values in a Map (or HashMap) type field, you can create an index (sometimes referred to as a “map index”) on specific (or all) keys in that field.

For example, you could create a map index to support the following query:

SELECT * FROM /users u WHERE u.name['first'] = 'John' OR u.name['last'] = 'Smith'


The map index extends regular range indexes created on single key by maintaining indexes for other specified keys, or for all keys if * is used. The underlying structure of the map index can be thought of as a wrapper around all these indexes.

## Creating Hash Indexes

Hash indexes are deprecated (Never use it).

-------------------------------------------------------

## Using Query Index Hints

You can use the hint keyword to allow GemFire’s query engine to prefer certain indexes.

In cases where one index is hinted in a query, the query engine filters off the hinted index (if possible) and then iterates and filters from the resulting values.

Example:

<HINT 'IDIndex'> SELECT * FROM /Portfolios p WHERE p.ID > 10 AND p.owner = 'XYZ'
If multiple indexes are added as hints, then the query engine will try to use as many indexes as possible while giving a preference for the hinted indexes.

-------------------------------------------------------


## Multiple index 

If you are creating multiple indexes on the same region, first define your indexes and then create the indexes all at once to avoid iterating over the region multiple times. See Creating Multiple Indexes at Once for details.


**Tips for Writing Queries that Use Indexes**

* In general an index will improve query performance if the FROM clauses of the query and index match exactly.

* The query evaluation engine does not have a sophisticated cost-based optimizer. It has a simple optimizer which selects best index (one) or multiple indexes based on the index size and the operator that is being evaluated.

* For AND operators, you may get better results if the conditions that use indexes and conditions that are more selective come before other conditions in the query.

* Indexes are not used in expressions that contain NOT, so in a WHERE clause of a query, qty >= 10 could have an index on qty applied for efficiency. However, NOT(qty < 10) could not have the same index applied.

* Whenever possible, provide a hint to allow the query engine to prefer a specific index. See Using Query Index Hints

-------------------------------------------------------------------------------

**Create Index Example :**

Using gfsh:


    gfsh> create index --name=myIndex --expression=status --region=/exampleRegion
    gfsh> create index --name=myKeyIndex --type=key --expression=id --region=/exampleRegion
    gfsh> create index --name=myHashIndex --type=hash --expression=mktValue --region=/exampleRegion




Using Java API:

     QueryService qs = cache.getQueryService();
     qs.createIndex("myIndex", "status", "/exampleRegion");
     qs.createKeyIndex("myKeyIndex", "id", "/exampleRegion");
     qs.createHashIndex("myHashIndex", "mktValue", "/exampleRegion");


Using cache.xml:

    <region name=exampleRegion>
        <region-attributes . . . >
    </region-attributes>
      <index name="myIndex" from-clause="/exampleRegion" expression="status"/>
      <index name="myKeyIndex" from-clause="/exampleRegion" expression="id" key-index="true"/>
      <index name="myHashIndex" from-clause="/exampleRegion p" expression="p.mktValue" type="hash"/>
      ...
     </region>

Spring : 

    <gfe:index id="myIndex" expression="someField" from="/someRegion"/>



**Listing and remove Indexes**

      gfsh> list indexes
      gfsh> list indexes --with-stats

      gfsh> destroy index
      gfsh> destroy index --name=myIndex
      gfsh> destroy index --region=/exampleRegion

-------------------------------------------------------------------------------

**Key Indexes**

When data is partitioned using a key or a field value

    create index --name=myKeyIndex --expression=id --region=/exampleRegion

xml:

    <region name=exampleRegion>
      <region-attributes . . . >
    </region-attributes>
     <index name="myKeyIndex" from-clause="/exampleRegion" expression="id" key-index="true"/>
    ...
    </region>


**Hash Indexes**

      gfsh> create index --name=myHashIndex --expression=mktValue --region=/exampleRegion


      <region name=exampleRegion>
         <region-attributes . . . >
      </region-attributes>
      <index name="myHashIndex" from-clause="/exampleRegion p" expression="p.mktValue" type="hash"/>
       ...
     </region>



**Map Indexes**

    gfsh>create index --name="IndexName" --expression="p.positions['PVTL', 'VMW']" --region="/portfolio p"

    gfsh>create index --name="IndexName" --expression="p.positions[*]" --region="/portfolio p"

In order to create or query a map index, you must use the bracket notation to list the map field keys you wish to index or query. For example: [*], ['keyX1','keyX2’]. Note that using p.pos.get('keyX1') will not create or query the map index.


----------------------------------------------------

## Referance 

- https://gemfire.docs.pivotal.io/93/geode/developing/query_index/query_index.html
- https://gemfire.docs.pivotal.io/96/geode/developing/query_index/creating_map_indexes.html
- https://gemfire.docs.pivotal.io/96/geode/tools_modules/lucene_integration.html
- https://gemfire.docs.pivotal.io/96/geode/developing/partitioned_regions/colocating_partitioned_region_data.html
- https://gemfire.docs.pivotal.io/96/geode/developing/function_exec/chapter_overview.html

