Data is pubic.
# Python_Spark_ML_SGD
Use SGD algorithm to machine learning, find the best mode to predict the validation of website.

Running environment is Spark + Hadoop + PySpark    
Used the algorithm is Stochastic Gradient Descent (SGD).     
Used the library is pyspark.mllib. 

# Stage1:  Read data
Placed the tsv on hadoop. Built 3 data sets: (1) Train data, (2) Validation data, (3) Sub_test data.


## Compare the parameters
"numIterations"
~~~
    num_iterations_list = [5, 15, 20, 50, 100]
    step_size_list = [10]
    mini_batch_fraction_list = [1]
~~~
![image](https://user-images.githubusercontent.com/75282285/194131970-37be45bf-dcc8-4ac6-a52e-e2e411346601.png)


"stepSize"
~~~
    num_iterations_list = [15]
    step_size_list = [10, 50, 100, 200]
    mini_batch_fraction_list = [0.5, 0.8, 1]
~~~
![image](https://user-images.githubusercontent.com/75282285/194132258-339e74ad-a243-4652-836a-8d49783c321d.png)


"miniBatchFraction".
~~~
    num_iterations_list = [15]
    step_size_list = [10]
    mini_batch_fraction_list = [0.5, 0.8, 1]
~~~
![image](https://user-images.githubusercontent.com/75282285/194132553-c1165925-9e03-4428-aac9-3549852a9262.png)



# Stage2: Train and evaluate   
Created the model using train data set.   
Calculated the AUC using validation data set.
Sorted the metrics.    
Found the best parameters includ the best AUC and the best model.   
![image](https://user-images.githubusercontent.com/75282285/194131381-d1165b01-d03c-4b8d-ba75-81ef9eb4601d.png)


# Stage3: Test
Used the sub_test data set and the best model to calculate the AUC. If testing AUC is similare as the best AUC, it is OK.
![image](https://user-images.githubusercontent.com/75282285/194131330-67a56a36-75d5-4501-b8f7-6bdcefb960ba.png)


# Stage4: Predict
Use the test data (in Hadoop, test.tsv) and the model (calculated after Stage2) to predict.
~~~
def predict_data(best_model):
    raw_data_with_header = sc.textFile(path + "test.tsv")
    header = raw_data_with_header.first()
    raw_data = raw_data_with_header.filter(lambda x: x != header)
    r_data = raw_data.map(lambda x: x.replace("\"", ""))
    lines_test = r_data.map(lambda x: x.split('\t'))
    data_rdd = lines_test.map(lambda x: (x[0], extract_features(x, categories_map, len(x))))
    dic_desc = {
        0: 'temp web',
        1: 'evergreen web'
    }
    for data in data_rdd.take(10):
        result_predict = best_model.predict(data[1])
        print(f"web:{data[0]}, \n predict:{result_predict}, desc: {dic_desc[result_predict]}")
~~~
![image](https://user-images.githubusercontent.com/75282285/194131514-1b1213e2-785b-43c9-a1f5-5f0fe3fc750e.png)

# Spark monitor
![image](https://user-images.githubusercontent.com/75282285/192587362-ac4c79f9-f87c-4da9-9acc-b67412eb2fa5.png)
![image](https://user-images.githubusercontent.com/75282285/192587799-e3b653f6-4d73-4b33-8126-a1debb838366.png)
![image](https://user-images.githubusercontent.com/75282285/192587445-b66c945a-929d-4b42-80c5-5ab5df2d35c1.png)

# DebugString
Print the DebugString.
~~~
print(model.toDebugString())
~~~

![image](https://user-images.githubusercontent.com/75282285/192617611-b294921c-5be5-4393-9073-96793e3c46b4.png)





