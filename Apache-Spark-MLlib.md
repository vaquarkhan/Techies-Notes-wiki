- ML Algorithms: common learning algorithms such as classification, regression, clustering, and collaborative filtering
- Featurization: feature extraction, transformation, dimensionality reduction, and selection
- Pipelines: tools for constructing, evaluating, and tuning ML Pipelines
- Persistence: saving and load algorithms, models, and Pipelines
- Utilities: linear algebra, statistics, data handling, etc.

> **Note : As of Spark 2.0, the RDD-based APIs in the spark.mllib package have entered maintenance mode. The primary Machine Learning API for Spark is now the DataFrame-based API in the spark.ml package.**


What is “Spark ML”?

“Spark ML” is not an official name but occasionally used to refer to the MLlib DataFrame-based API. This is majorly due to the org.apache.spark.ml Scala package name used by the DataFrame-based API, and the “Spark ML Pipelines” term we used initially to emphasize the pipeline concept.

> **Note : MLlib uses the linear algebra package Breeze, which depends on netlib-java for optimised numerical processing. If native libraries1 are not available at runtime, you will see a warning message and a pure JVM implementation will be used instead.**

> **Due to licensing issues with runtime proprietary binaries, we do not include netlib-java’s native proxies by default. To configure netlib-java / Breeze to use system optimised binaries, include com.github.fommil.netlib:all:1.1.2 (or build Spark with -Pnetlib-lgpl) as a dependency of your project and read the netlib-java documentation for your platform’s additional installation instructions.**

> **To use MLlib in Python, you will need NumPy version 1.4 or newer**


-------------------------------------------------
- Correlation 

Calculating the correlation between two series of data is a common operation in Statistics.

Introduction: What Is Correlation and Why Is It Useful?

The term "correlation" refers to a mutual redepartment lationship or association between quantities. In almost any business, it is useful to express one quantity in terms of its relationship with others.

For example, sales might increase when the marketing spends more on TV advertisements, or a customer's average purchase amount on an e-commerce website might depend on a number of factors related to that customer. Often, correlation is the first step to understanding these relationships and subsequently building better business and statistical models.

**Correlation measure how two observed variables are related to each other . It has been used in many different ways in data science.**

- Correlation is used im univariate analysis to identify which feature is more predictive for classification of regression task.
- To identify multicollinearity in the feature set . Multicollinearity reduse the accuracy of model.
- Identify casual relation ship between variables.
- Their are many other extension like cca canonical correlation analysis.

-----------------------------------------------------------------

         import org.apache.spark.ml.linalg.{Matrix, Vectors}
         import org.apache.spark.ml.stat.Correlation
         import org.apache.spark.sql.Row

         val data = Seq(
         Vectors.sparse(4, Seq((0, 1.0), (3, -2.0))),
         Vectors.dense(4.0, 5.0, 0.0, 3.0),
         Vectors.dense(6.0, 7.0, 0.0, 8.0),
         Vectors.sparse(4, Seq((0, 9.0), (3, 1.0)))
          )

        val df = data.map(Tuple1.apply).toDF("features")
        val Row(coeff1: Matrix) = Correlation.corr(df, "features").head
        println("Pearson correlation matrix:\n" + coeff1.toString)

        val Row(coeff2: Matrix) = Correlation.corr(df, "features", "spearman").head
        println("Spearman correlation matrix:\n" + coeff2.toString)



Result :+1: 

                    Pearson correlation matrix:
                    1.0                   0.055641488407465814  NaN  0.4004714203168137  
                    0.055641488407465814  1.0                   NaN  0.9135958615342522  
                    NaN                   NaN                   1.0  NaN                 
                    0.4004714203168137    0.9135958615342522    NaN  1.0                 
                    Spearman correlation matrix:
                    1.0                  0.10540925533894532  NaN  0.40000000000000174  
                    0.10540925533894532  1.0                  NaN  0.9486832980505141   
                    NaN                  NaN                  1.0  NaN                  
                    0.40000000000000174  0.9486832980505141   NaN  1.0   
               
                 import org.apache.spark.ml.linalg.{Matrix, Vectors}
                 import org.apache.spark.ml.stat.Correlation
                 import org.apache.spark.sql.Row
                 data: Seq[org.apache.spark.ml.linalg.Vector] =
                 List(
                 (4,[0,3],[1.0,-2.0]),
                    [4.0,5.0,0.0,3.0], 
                    [6.0,7.0,0.0,8.0], 
                 (4,[0,3],[9.0,1.0]))

                 df: org.apache.spark.sql.DataFrame = [features: vector]
                 coeff1: org.apache.spark.ml.linalg.Matrix =
                 1.0                   0.055641488407465814  NaN  0.4004714203168137
                 0.055641488407465814  1.0                   NaN  0.9135958615342522
                 NaN                   NaN                   1.0  NaN
                0.4004714203168137    0.9135958615342522    NaN  1.0

               coeff2: org.apache.spark.ml.linalg.Matrix =
               1.0                  0.10540925533894532  NaN  0.40000000000000174
               0.10540925533894532  1.0                  NaN  0.9486832980505141
               NaN                  NaN                  1.0  NaN
              0.40000000000000174  0.9486832980505141   NaN  1.0


-------------------------------------------------------------
https://github.com/vaquarkhan/Apache-Spark-2x-Machine-Learning-Cookbook



https://mapr.com/blog/using-apache-spark-sql-explore-sp-500-and-oil-stock-prices/



----------------------------------------------------------

**Main concepts in Pipelines**

MLlib standardizes APIs for machine learning algorithms to make it easier to combine multiple algorithms into a single pipeline, or workflow. This section covers the key concepts introduced by the Pipelines API, where the pipeline concept is mostly inspired by the scikit-learn project.

- DataFrame: This ML API uses DataFrame from Spark SQL as an ML dataset, which can hold a variety of data types. E.g., a -
 DataFrame could have different columns storing text, feature vectors, true labels, and predictions.

- Transformer: A Transformer is an algorithm which can transform one DataFrame into another DataFrame. E.g., an ML model is a Transformer which transforms a DataFrame with features into a DataFrame with predictions.

- Estimator: An Estimator is an algorithm which can be fit on a DataFrame to produce a Transformer. E.g., a learning algorithm is an Estimator which trains on a DataFrame and produces a model.

- Pipeline: A Pipeline chains multiple Transformers and Estimators together to specify an ML workflow.

- Parameter: All Transformers and Estimators now share a common API for specifying parameters.

