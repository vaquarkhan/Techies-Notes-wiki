Startup
=======
run pig locally:
$ magicpig -x local script.pig     # doesn't work

expand pig macros:
$ magicpig -dryrun script.pig

Commands
========

Loading
-------
grunt> A = LOAD '/path/to/file' USING PigStorage(':') AS (field1:float, field2:int, …);

Load data from /path/to/file and name the fields field1, field2, … sequentially. Fields are split by ':'.  field1 is loaded as a float, field2 as an integer.  The 'AS' part is optional.  Other basic types are int, long, float, double, bytearray, boolean, chararray.

Pig also supports complex types are: tuple, bag, map.  For example,

grunt> A = LOAD '/path/to/file' USING PigStorage(':') AS (field1:tuple(t1a:int, t1b:int,t1c:int), field2:chararray);

This will load field1 as a tuple with 3 values that can be referenced as field1.t1a, field1.t1b, etc.  Don't worry about bags and maps.

Saving
------
grunt> STORE A INTO '/path/out' USING AvroStorage();

Save all of A's fields into /path/out in the format defined by AvroStorage

Generating
----------
grunt> B = FOREACH A GENERATE $0 + 1, org.apache.pig.tutorial.ExtractHour(field2) as field2hour;

Use this to select the first field and add one to it, and select the hour part of field2 from A and rename it as field2hour.  You have your basic arithmetic operations and %.  You can also use "*" to select all fields.

Here's an example of a nested foreach.  Only distinct, filter, limit, and order are supported inside a nested foreach.
daily    = load 'NYSE_daily' as (exchange, symbol); -- not interested in other fields
grpd     = group daily by exchange;
uniqcnt  = foreach grpd {
           sym      = daily.symbol;
           uniq_sym = distinct sym;
           generate group, COUNT(uniq_sym); };

Here's an example for selecting _ranges_ of fields,

rices = load 'NYSE_daily' as (exchange, symbol, date, open, high, low, close, volume, adj_close);
beginning = foreach prices generate ..open; -- produces exchange, symbol, date, open
middle    = foreach prices generate open..close; -- produces open, high, low, close
end       = foreach prices generate volume..; -- produces volume, adj_close

Here's how to use the "<condition> ? <if true> : <if false>" construct

B = FOREACH A GENERATE field1 > 500 ? 1 : -1


Filtering
---------
grunt> B = FILTER A by $1 > 2;

Remove entries from A whose second field is <= 2.  You can use basic numerical comparisons, along with "is null" and "matches" (for glob matching).  e.g,

grunt> B = FILTER A by (url matches '*.apache.com');

Grouping
--------
grunt> B = GROUP A BY (field1, field2), B by (fieldA, fieldB);

Match entries in A to entries in B if (field1, field2) == (fieldA, fieldB), and return values of both grouped by their respective keys.  If A and B share the same field names, use A.field1 or B.field1.  The key will have alias "group".

grunt> B = GROUP A ALL;

Group all of A into a single group instead of by field

Joining
-------
grunt> C = JOIN A BY field1 LEFT, B BY field2 USING 'replicated' PARALLEL 5;

Joint two variables by field names.  Must be done after a GROUP operation on both A and B.  Uses 'replicated' method to join, alternatives being 'skewed', 'merge', and normal (hash).  Uses 5 reduce tasks.  Does a left join (all rows in A will be kept, but rhs might be null).

There exist multiple types of joins with their own performance characteristics.  They are listed below in order of preference.

TypeAlign smallest toNumber of joinableOther restrictionsHow it worksWhen  to use
replicated
right2+inner/left outer onlyLoads rightmost alias into distributed cacheOne alias is really small
mergeright2aliases must be sorted; inner onlySamples an index of right hand alias, then splits data into mappers who use index to lookup where to start reading from on rightaliases are already sortedskewedleft2Samples right side's keys to determine which keys have too many entries.  All but the heaviest are handled by hash join, and all others are treated similar to a replicated join with the left loaded into memory.
One or more aliases have a heavily skewed distribution over the keys (e.g., one has 100k entries, the other 5)hashleft2+Groups data by key, then sends the leftmost alias to each reducer and loads it into memory, then second etc.  Last alias is streamed through.When you have no other choice.

Flattening
----------
grunt> B = FOREACH A GENERATE flatten(field1) as field1flat, field2;

If the argument to flatten is a tuple, then flatten it like a list: e.g., ((a,b), c) -> (a, b, c).
If the argument is a bag, then make the cross product: e.g. a:{(b,c), (d,e)} -> {(a,b,c), (a,d,e)} 

Unique
------
grunt> B = DISTINCT A;

Use this to turn A into a set.  All fields in A must match to be grouped together

Sorting
-------
grunt> B = ORDER A by (field1, field2);

Cross Product
-------------
grunt> C = CROSS A, B;

Take the cross product of A and B's elements.  Your machine will shit itself if you do this.  You cannot cross an alias with itself due to namespace conflicts.

Splitting
---------
grunt> SPLIT B INTO A1 IF field1 < 3, A2 IF field2 > 7;

Cut B into two parts based on conditions.  Entries can end up in both A1 and A2.

Subsampling
-----------
grunt > B = SAMPLE A 0.01;

Sample 1% of A's entries (in expectation)

Viewing interactively
---------------------
grunt> B = limit A 500;
grunt> DUMP B;
grunt> DESCRIBE B;

Take the first 500 entries in A, and print them to screen, then print out the name of all of B's fields.

Built in Functions
==================

   * Eval Functions
      * AVG
      * CONCAT
      * COUNT
      * COUNT_STAR
      * DIFF
      * IsEmpty
      * MAX
      * MIN
      * SIZE
      * SUM
      * TOKENIZE
   * Math Functions
      * ABS
      * ACOS
      * ASIN
      * ATAN
      * CBRT
      * CEIL
      * COS
      * COSH
      * EXP
      * FLOOR
      * LOG
      * LOG10
      * RANDOM
      * ROUND
      * SIN
      * SINH
      * SQRT
      * TAN
      * TANH
   * String Functions
      * INDEXOF
      * LAST_INDEX_OF
      * LCFIRST
      * LOWER
      * REGEX_EXTRACT
      * REGEX_EXTRACT_ALL
      * REPLACE
      * STRSPLIT
      * SUBSTRING
      * TRIM
      * UCFIRST
      * UPPER
   * Tuple, Bag, Map Functions
      * TOTUPLE
      * TOBAG
      * TOMAP
      * TOP


Macros in Pig
=============

Basic usage example.  Prefix arguments with "$".  You can use aliases and literals, as they're literally subbing text in.
DEFINE group_and_count (A, group_key, reducers) RETURNS B {
   D = GROUP $A BY '$group_key' PARALLEL $reducers;
   $B = FOREACH D GENERATE group, COUNT($A);
}; X = LOAD 'users' AS (user, age, zip);
Y = group_and_count (X, 'user', 20);
Z = group_and_count (X, 'age', 30);Here's an example of one macro calling another.  
DEFINE foreach_count(A, C) RETURNS B {
   $B = FOREACH $A GENERATE group, COUNT($C);
}; DEFINE group_with_parallel (A, group_key, reducers) RETURNS B {
   C = GROUP $A BY $group_key PARALLEL $reducers;
   $B = foreach_count(C, $A);
};Here's an example where a string is transformed into interpreted pig.
/* Get a count of records, return the name of the relation and . */
DEFINE total_count(relation) RETURNS total {
  $total = FOREACH (group $relation all) generate '$relation' as label, COUNT_STAR($relation) as total;
};
/* Get totals on 2 relations, union and return them with labels */
DEFINE compare_totals(r1, r2) RETURNS totals {
  total1 = total_count($r1);
  total2 = total_count($r2);
  $totals = union total1, total2;
};
/* See how many records from a relation are removed by a filter, given a condition */
DEFINE test_filter(original, condition) RETURNS result {
  filtered = filter $original by $condition;
  $result = compare_totals($original, filtered);

};


emails = load '/me/tmp/inbox' using AvroStorage();
out = test_filter(emails, 'date is not null'); Pitfalls
--------
- You can't use parameter substitution in a macro (pass them in explicitly as arguments)
- You can't use any of the debug commands (DESCRIBE, ILLUSTRATE, EXPLAIN, DUMP) in a macro
- If you do a filter on a numeric condition and the input is null, the result is false.  e.g., null > 0 == false

Parameters
==========

Here's how to do direct substitutions with variables in Pig.  Note that "%".

%default parallel_factor 10;
wlogs = load 'clicks' as (url, pageid, timestamp);
grp   = group wlogs by pageid parallel $parallel_factor;
cntd  = foreach grp generate group, COUNT(wlogs);

Functions outside of Pig
========================

There exist a boatload of ways to write [U]ser [D]efined [F]unctions.  Below is a simple EvalFunc<ReturnType> for doing a map operation, but there are also FilterFunc.  EvalFunc<ReturnType> is also used for aggregation operations, but can be more efficient using the Algebraic and Accumulator interfaces.

Java
----

.. code::

    /**
     * A simple UDF that takes a value and raises it to the power of a second
     * value.  It can be used in a Pig Latin script as Pow(x, y), where x and y
     * are both expected to be ints.
     */
    public class Pow extends EvalFunc<Long> {
   
        public Long exec(Tuple input) throws IOException {
            try {
                /* Rather than give you explicit arguments UDFs are always handed
                 * a tuple.  The UDF must know the arguments it expects and pull
                 * them out of the tuple.  These next two lines get the first and
                 * second fields out of the input tuple that was handed in.  Since
                 * Tuple.get returns Objects, we must cast them to Integers.  If
                 * the case fails an exception will be thrown.
                 */
                int base = (Integer)input.get(0);
                int exponent = (Integer)input.get(1);
                long result = 1;
   
                /* Probably not the most efficient method...*/
                for (int i = 0; i < exponent; i++) {
                    long preresult = result;
                    result *= base;
                    if (preresult > result) {
                        // We overflowed.  Give a warning, but do not throw an
                        // exception.
                        warn("Overflow!", PigWarning.TOO_LARGE_FOR_INT);
                        // Returning null will indicate to Pig that we failed but
                        // we want to continue execution
                        return null;
                    }
                }
                return result;
            } catch (Exception e) {
                // Throwing an exception will cause the task to fail.
                throw new IOException("Something bad happened!", e);
            }
        }
   
        public Schema outputSchema(Schema input) {    // Check that we were passed two fields
            if (input.size() != 2) {
                throw new RuntimeException(
                    "Expected (int, int), input does not have 2 fields");
            }
      
      
            try {
                // Get the types for both columns and check them.  If they are
                // wrong figure out what types were passed and give a good error
                // message.
                if (input.getField(0).type != DataType.INTEGER ||
                        input.getField(1).type != DataType.INTEGER) {
                    String msg = "Expected input (int, int), received schema (";
                    msg += DataType.findTypeName(input.getField(0).type);
                    msg += ", ";
                    msg += DataType.findTypeName(input.getField(1).type);
                    msg += ")";
                    throw new RuntimeException(msg);
                }
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
      
      
            // Construct our output schema which is one field, that is a long
            return new Schema(new FieldSchema(null, DataType.LONG));
        }
    }
    


Python
------

- in Pig,

.. code::
    grunt> Register 'test.py' using jython as myfuncs;
    grunt> b = foreach a generate myfuncs.helloworld(), myfuncs.square(3);



- in Python

.. code::

    @outputSchemaFunction("squareSchema")
    def pow(n1, n2):
      return n1**n2
   
    @schemaFunction("squareSchema")
    def squareSchema(input):     # this says int -> int, long -> long, float -> float, etc
      return input



.. code::

    @outputSchema("production:float")
    def production(slugging_pct, onbase_pct):
        return slugging_pct + onbase_pct

Embedding Pig in Python
=======================

Pig doesn't have control structures (if, for, etc), so jobs that inherently have time-varying file names or are repeated until convergence can't be controlled from within Pig.  You can fix this by embedding Pig in Python via Jython.

.. code::

    from org.apache.pig.scripting import *
   
    # Compile a pig job named "scriptname"
    P1 = Pig.compile("scriptname",
      """
      A = LOAD 'input';
      B = FILTER A BY field > $lower_bound;
      STORE B INTO '$outpath';
   
      n_entries = GROUP B BY ALL;
      n_entries = FOREACH n_entries GENERATE COUNT(*);
      STORE n_entries INTO '$count_path';
      """
    )
   
    # pass parameters
    P1_bound = P1.bind(
      {
        'lower_bound': 500,
        'outpath': '/path/to/save',
        'count_path': '/tmp/count'
      }
    )
   
    # Do some non-pig operations
    Pig.fs( "RMR %s /path/to/save" )
   
    # run script
    stats = P1_bound.runSingle()
   
    # check if successful?
    if stats.isSuccessful():
      print 'Yay!  it succeeded!'
   
      # extract alias 'n_entries' from the script and get its first element
      count = float(str(stats.result("n_entries").iterator().next().get(0)))
      print 'Output %d rows' % (count,)


-----------------------------------------------------------------------------

* http://bugra.github.io/work/notes/2014-02-09/pig-not-so-foreign-language-paper-notes/
* http://bugra.github.io/work/notes/2014-02-08/pig-advantages-and-disadvantages/
* http://help.mortardata.com/technologies/pig/learn_pig