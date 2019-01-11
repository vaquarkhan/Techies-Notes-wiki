* DataFrame: Spark ML uses DataFrame from Spark SQL as an ML dataset, which can hold a variety of data types. E.g., a DataFrame could have different columns storing text, feature vectors, true labels, and predictions.

* Transformer: A Transformer is an algorithm which can transform one DataFrame into another DataFrame. E.g., an ML model is a Transformer which transforms DataFrame with features into a DataFrame with predictions.A transformer consists of learned models and feature transformers. It is an abstraction and is created by adding one or more columns. For instance, a feature transformer can take a dataset, read one of its columns, convert it into a new one, add this new column to the original dataset, and then finally output the updated dataset. Technically, a transformer uses the transform() method to convert one DataFrame to another. 

* Estimator: An Estimator is an algorithm which can be fit on a DataFrame to produce a Transformer. E.g., a learning algorithm is an Estimator which trains on a DataFrame and produces a model.
Estimator works by abstracting a learning algorithm concept or any other algorithm concept that trains or fits on data. From the technical standpoint, an estimator uses the fit () method to accept a DataFrame and produce a transformer.For instance, the LogisticRegression learning algorithm is an estimator that calls the fit () method and trains a LogisticRegressionModel, which is a transformer.

* Pipeline: A Pipeline chains multiple Transformers and Estimators together to specify an ML workflow.
 Pipeline running an algorithms sequence for processing and learning from data is common in machine learning. 

![](https://static.wixstatic.com/media/4c8fd9_d511bc0df5bb4e0f952193b18e69f618~mv2.png/v1/fill/w_688,h_176,al_c,usm_0.66_1.00_0.01/4c8fd9_d511bc0df5bb4e0f952193b18e69f618~mv2.png)

* Parameter: All Transformers and Estimators now share a common API for specifying parameters.Spark ML API is param is the uniform API to specify parameters for estimators and transformers. It contains self-contained documentation and is a named parameter. A ParamMap represents a set of (parameter, value) pairs. You can pass parameters to an algorithm using two methods.The first method is by setting parameters for instance. For example, you can call the given method for LogisticRegression’s instance lr. This will make lr.fit() use at most ten iterations. This type of API is similar to the API used in MLlib.The other method is by passing a ParamMap to transform () or fit (). All parameters in this ParamMap will override the parameters that have been formerly specified using setter methods.

Note that parameters are related to the specific instances of transformers and estimators.




Spark ML Algo :

![](https://www.safaribooksonline.com/library/view/spark-for-python/9781784399696/graphics/B03986_04_02.jpg)

![](https://image.slidesharecdn.com/machinelearningwithspark-mahedikaysar-161023205800/95/large-scale-machine-learning-with-spark-32-638.jpg?cb=1477256850)


* https://spark.apache.org/docs/preview/ml-guide.html
* https://dzone.com/articles/distingish-pop-music-from-heavy-metal-using-apache
* https://mapr.com/blog/apache-spark-machine-learning-tutorial/
* https://mapr.com/blog/predicting-loan-credit-risk-using-apache-spark-machine-learning-random-forests/
