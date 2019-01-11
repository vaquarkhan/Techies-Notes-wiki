I have a spark data frame with following structure

 id  flag price date
  a   0    100  2015
  a   0    50   2015
  a   1    200  2014
  a   1    300  2013
  a   0    400  2012
I need to create a data frame with recent value of flag 1 and updated in the flag 0 rows.

      id  flag price date new_column
      a   0    100  2015    200
      a   0    50   2015    200
      a   1    200  2014    null
      a   1    300  2013    null
      a   0    400  2012    null
We have 2 rows having flag=0. Consider the first row(flag=0),I will have 2 values(200 and 300) and I am taking the recent one 200(2014). And the last row I don't have any recent value for flag 1 so it is updated with null.




https://jaceklaskowski.gitbooks.io/mastering-apache-spark/content/spark-sql-windows.html
https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/1023043053387187/3170497669323442/2840265927289860/latest.html

