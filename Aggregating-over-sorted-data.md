E.g.,

    val df = Seq((2, "b c a"), (1, "c a b"), (3, "a c b")).toDF("number",
"letters")
    val df2 =
      df.explode('letters) {
        case Row(letters: String) => letters.split(" ").map(Tuple1(_)).toSeq
      }

    df2
      .select('number, '_1 as 'letter)
      .repartition('number)
      .sortWithinPartitions('number, 'letter)
      .groupBy('number)
      .agg(collect_list('letter))
      .show()

    +------+--------------------+
    |number|collect_list(letter)|
    +------+--------------------+
    |     3|           [a, b, c]|
    |     1|           [a, b, c]|
    |     2|           [a, b, c]|
    +------+--------------------+

I think it should let you do aggregate on sorted data per key.



http://www.spark.tc/
