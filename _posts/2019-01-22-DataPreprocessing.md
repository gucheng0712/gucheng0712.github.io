---
layout: post
title:  "Data Preprocessing Techniques (ML)"
date:   2019-1-22 1:00:05 +0000
image: /assets/images/post24.png
---

In order to start machine learning training process, the algorithms needs to make the data be formatted in certain way. This is called ***Data Preprocessing***.

There are many ways of doing data preprocessing, the most common ways are ***Binarization, Mean Removal, Scaling***, and ***Normalization***.


---
#### 1. Binarization

Binarization is the process of convert the numerical values into boolean values based on a threshold value. 

>For example:

{% highlight python %} 
    import numpy as np  # numpy library
    from sklearn import preprocessing  # sklearn library
   	#inputs
    input_data = np.array([
        [5.1, -2.9, 3.3],
        [-1.2, 7.8, -6.1],
        [3.9, 0.4, 2.1],
        [7.3, -9.9, -4.5]])
        
    # Binarize Data based on teh threshold 2.1, every element above the threshold will be 1, else 0
    data_binarized = preprocessing.Binarizer(threshold = 2.1).transform(input_data)
    print("\nBinarized Data:\n",data_binarized)
{% endhighlight %}

> Output:

```
   Binarized data:
    [[ 1.  0.  1.]
    [ 0.  1.  0.]
    [ 1.  0.  0.]
    [ 1.  0.  0.]]
```

---
#### 2. Mean Removal

Removing the mean is commonly used in machine learning. It usually useful to remove the mean from out feature vector, so that each feature is centered on zero. We do this in order to remove bias from the features in our feature vector.

>For example:

{% highlight python %} 
    import numpy as np  # numpy library
    from sklearn import preprocessing  # sklearn library
   	#inputs
    input_data = np.array([
        [5.1, -2.9, 3.3],
        [-1.2, 7.8, -6.1],
        [3.9, 0.4, 2.1],
        [7.3, -9.9, -4.5]])
        
    # Standard Deviation: The size of the fluctuations in the data set
    # Print mean and standard deviation
    print("\nDataBeforeMeanRemoval: \n", input_data)
    print("\nBEFORE:")
    print("Mean = ", input_data.mean(axis=0))  # axis represents the mean's dimension 0 -> vertical   1 -> horizontal
    print("Std Deviation = ", input_data.std(axis=0))
    print("Mean = ", input_data.mean(axis=1))
    print("Std Deviation = ", input_data.std(axis=1))

    # Remove Mean by scale function
    data_scaled = preprocessing.scale(input_data)
    print("\nDataAfterMeanRemoval: \n", data_scaled)
    print('\nAFTER:')
    print('Mean = ', data_scaled.mean(axis=0))
    print("Std Deviation = ", data_scaled.std(axis=0))
{% endhighlight %}

> Output:

```
    DataBeforeMeanRemoval: 
     [[ 5.1 -2.9  3.3]
     [-1.2  7.8 -6.7]
     [ 3.9  0.4  2.1]
     [ 7.3 -9.9 -4.5]]

    BEFORE:
    Mean =  [ 3.775 -1.15  -1.45 ]
    Std Deviation =  [3.12039661 6.36651396 4.24352448]
    Mean =  [ 1.83333333 -0.03333333  2.13333333 -2.36666667]
    Std Deviation =  [3.42669261 5.97680703 1.42906341 7.18207646]

    DataAfterMeanRemoval: 
     [[ 0.42462551 -0.2748757   1.11935256]
     [-1.59434861  1.40579288 -1.23717915]
     [ 0.04005901  0.24346134  0.83656876]
     [ 1.12966409 -1.37437851 -0.71874217]]

    AFTER:
    Mean =  [1.11022302e-16 0.00000000e+00 5.55111512e-17]
    Std Deviation =  [1. 1. 1.]
```


---
#### 3. Rescale Data

The process of normalization is to modify the values in the vector so that we can measure them on a common scale. 

There are 2 common normalization techniques:
1. ***L1 Normalization***, which refers to Least Absolute Deviations, works by making sure that the sum of absolute values is 1 in each row. 
2. ***L2 Normalization***, which refers to least squares, works by making sure that the sum of squares is 1.

>For example:

{% highlight python %} 
    import numpy as np  # numpy library
    from sklearn import preprocessing  # sklearn library
   	#inputs
    input_data = np.array([
        [5.1, -2.9, 3.3],
        [-1.2, 7.8, -6.1],
        [3.9, 0.4, 2.1],
        [7.3, -9.9, -4.5]])
        
    # Min max scaling
    data_scaler_minmax = preprocessing.MinMaxScaler(feature_range=(0, 1))  # create a (0,1) data scalar
    data_scaled_minmax = data_scaler_minmax.fit_transform(input_data)  # scalar the data by using scalar
    print("\nMin max scaled data:\n", data_scaled_minmax)
{% endhighlight %}

> Output:

```
    Min max scaled data:
     [[0.74117647 0.39548023 1.       ]
     [0.         1.         0.        ]
     [0.6        0.5819209  0.88      ]
     [1.         0.         0.22      ]]
```
---
#### 4. Normalization

In vector, the value of each feature can vary between many random values, so rerange(scaling) the values are important.
It is often rescaled into the range between 0 and 1. This is useful for optimization algorithms, such as like gradient descent, weight inputs.
>For example:

{% highlight python %} 
    import numpy as np  # numpy library
    from sklearn import preprocessing  # sklearn library
   	#inputs
    input_data = np.array([
        [5.1, -2.9, 3.3],
        [-1.2, 7.8, -6.1],
        [3.9, 0.4, 2.1],
        [7.3, -9.9, -4.5]])
        
    # Normalize Data

    # L1 Normalization ==> the sum of absolute value is 1 in each row (Least Absolute Deviation)
    # L2 Normalization ==> the sum of squares is 1
    data_normalized_l1 = preprocessing.normalize(input_data, norm='l1')
    data_normalized_l2 = preprocessing.normalize(input_data, norm='l2')  # default more stable
    print("\nL1 Normalized Data:\n", data_normalized_l1)
    print("\nL2 Normalized Data:\n", data_normalized_l2)
{% endhighlight %}

> Output:

```
    L1 Normalized Data:
     [[ 0.45132743 -0.25663717  0.2920354 ]
     [-0.07643312  0.49681529 -0.42675159]
     [ 0.609375    0.0625      0.328125  ]
     [ 0.33640553 -0.4562212  -0.20737327]]

    L2 Normalized Data:
     [[ 0.75765788 -0.43082507  0.49024922]
     [-0.11591633  0.75345616 -0.64719952]
     [ 0.87690281  0.08993875  0.47217844]
     [ 0.55734935 -0.75585734 -0.34357152]]
```



---

>**End --Cheng Gu**

