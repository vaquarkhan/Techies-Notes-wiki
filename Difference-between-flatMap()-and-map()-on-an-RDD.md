Map and flatMap are similar, in the sense they take a line from the input RDD and apply a function on it. The way they differ is that the function in map returns only one element, while function in flatMap can return a list of elements (0 or more) as an iterator.

Also, the output of the flatMap is flattened. Although the function in flatMap returns a list of elements, the flatMap returns an RDD which has all the elements from the list in a flat way (not a list).


**Difference:**

**map(func)**
Return a new distributed dataset formed by passing each element of the source through a function func.

**flatMap(func)**
Similar to map, but each input item can be mapped to 0 or more output items (so func should return a Seq rather than a single item).

**The transformation function:**
map: One element in -> one element out.
flatMap: One element in -> 0 or more elements out (a collection).


**Example1:- **
    sc.parallelize([3,4,5]).map(lambda x: range(1,x)).collect()
    **Output:**
    [[1, 2], [1, 2, 3], [1, 2, 3, 4]]

     sc.parallelize([3,4,5]).flatMap(lambda x: range(1,x)).collect()
    **Output: ** notice o/p is flattened out in a single list
    [1, 2, 1, 2, 3, 1, 2, 3, 4] 

**Example 2:**

    sc.parallelize([3,4,5]).map(lambda x: [x,  x*x]).collect() 
    **Output:**
    [[3, 9], [4, 16], [5, 25]]

    sc.parallelize([3,4,5]).flatMap(lambda x: [x, x*x]).collect() 
    Output: notice flattened list
    [3, 9, 4, 16, 5, 25]

   **Example 3: **
    There is a file greetings.txt in HDFS with following lines:
    Good Morning
    Good Evening
    Good Day
    Happy Birthday
    Happy New Year


    lines = sc.textFile("greetings.txt")
    lines.map(lambda line: line.split()).collect()
    Output:-
    [['Good', 'Morning'], ['Good', 'Evening'], ['Good', 'Day'], ['Happy', 'Birthday'], ['Happy', 'New', 'Year']]


     lines.flatMap(lambda line: line.split()).collect()
    **Output:-**
    ['Good', 'Morning', 'Good', 'Evening', 'Good', 'Day', 'Happy', 'Birthday', 'Happy', 'New', 'Year']


    **We can do wordcount of file using flatMap:-**
    lines = sc.textFile("greetings.txt")
    sorted(lines.flatMap(lambda line: line.split()).map(lambda w: (w,1)).reduceByKey(lambda v1, v2: v1+v2).collect())

**Output:-**
    [('Birthday', 1), ('Day', 1), ('Evening', 1), ('Good', 3), ('Happy', 2), ('Morning', 1), ('New', 1), ('Year', 1)]

LikeDifference between map and flatMap transformations in Spark (pySpark)Comment



[https://www.quora.com/What-is-the-difference-between-Map-and-FlatMap-in-Apache-Spark](https://www.quora.com/What-is-the-difference-between-Map-and-FlatMap-in-Apache-Spark)



This example below demonstrates the difference b/w map() & flatMap() operation in RDD using Scala Shell. A flatMap flattens multiple Array into one Single Array

    vk:~/sbook$ cat words.txt 
    line1 word1
    line2 word2 word1 
    line3 word3 word4
    line4 word1

    scala> val lines = sc.textFile("words.txt");
    scala> lines.map(_.split(" ")).take(3)
    res4: Array[Array[String]] = Array(Array(line1, word1), Array(line2, word2, word1), Array(line3, word3, word4))

A flatMap() flattens multiple list into one single List

    scala> lines.flatMap(_.split(" ")).take(3)
    res5: Array[String] = Array(line1, word1, line2)
