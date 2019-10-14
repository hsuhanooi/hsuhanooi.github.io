---
layout: post
title: What is LightGBM, How to implement it? How to fine tune the parameters? Updated.
excerpt: I ran across a post on LightGBM which needs a little updating to make work.
tags: lightgbm
---

There isn't a ton of documentation on LightGBM and the first post I ran into was [What is LightGBM, How to implement it? How to fine tune the parameters?](https://medium.com/@pushkarmandot/https-medium-com-pushkarmandot-what-is-lightgbm-how-to-implement-it-how-to-fine-tune-the-parameters-60347819b7fc). 

The post did a great job talking about the parameters but the example written needed updating. In particular, running the example didn't give me the provided results and the code given doesn't actually run. If you just want the changes in the code to make it work it's below. Otherwise the changes I made are listed. Also I've included an alternate way to install on Mac if you choose not to use conda.

```
# libomp is necessary for lightgbm and you'll still get warnings but it will work.
brew install libomp
pip install lightgbm
```

### 1. Update min_data param to 7 instead of 50. There's 225 records in x_train. 50 means stop splitting if you get to a leaf with 50 or less records, which will give you a very shallow tree. I thought 7 seemed more reasonable to get reasonably deep trees.

### 2. Updated the number of iterations to 200. 100 seemed low.

### 3. In the previous post they did for i in range(0,99) which is 0-98, not 0-99. I removed this altogether and just rounded everything so >=0.5 becomes a 1.

### 4. I commented out the feature scaler, since you don't really need it. See [here](https://stats.stackexchange.com/questions/244507/what-algorithms-need-feature-scaling-beside-from-svm) for more details but decision trees based algorithms don't necessarily need it.


```
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
import lightgbm as lgb

def run():
    # Importing the dataset
    dataset = pd.read_csv('../data/example/Social_Network_Ads.csv')
    X = dataset.iloc[:, [2, 3]].values
    y = dataset.iloc[:, 4].values
    # Splitting the dataset into the Training set and Test set
    x_train, x_test, y_train, y_test = train_test_split(X, y, test_size = 0.25, random_state = 0)

    # Feature Scaling - you don't actually NEED to do this, although it can be a good idea. See https://stats.stackexchange.com/questions/244507/what-algorithms-need-feature-scaling-beside-from-svm for more details
    # sc = StandardScaler()
    # x_train = sc.fit_transform(x_train)
    # x_test = sc.transform(x_test)

    # train model
    d_train = lgb.Dataset(x_train, label=y_train)
    params = {}
    params['learning_rate'] = 0.003
    params['boosting_type'] = 'gbdt'
    params['objective'] = 'binary'
    params['metric'] = 'binary_logloss'
    params['sub_feature'] = 0.5
    params['num_leaves'] = 10
    params['min_data'] = 7  # 1.This was updated compared to the previous post, 50 was too high with 125 train and 75 test.
    params['max_depth'] = 10
    clf = lgb.train(params, d_train, 200)  # 2.I also updated the number of iterations here to perform slightly better

    # Prediction
    y_pred_score = clf.predict(x_test)
    y_pred = [round(y) for y in y_pred_score]  # 3.Removed the erroneous rounding in the previous post which had an off by 1 error.

    # Confusion matrix
    from sklearn.metrics import confusion_matrix
    cm = confusion_matrix(y_test, y_pred)
    print('Confusion Matrix: %s' % cm)

    # Accuracy
    from sklearn.metrics import accuracy_score
    accuracy = accuracy_score(y_pred, y_test)
    print('Model Accuracy: %.2f' % accuracy)

if __name__ == '__main__':
    run()
```




After you run the above you should get output similar to this.

```
[LightGBM] [Info] Number of positive: 111, number of negative: 189
[LightGBM] [Info] Total Bins 108
[LightGBM] [Info] Number of data: 300, number of used features: 2
[LightGBM] [Info] [binary:BoostFromScore]: pavg=0.370000 -> initscore=-0.532217
[LightGBM] [Info] Start training from score -0.532217
Confusion Matrix: [[65  3]
 [ 3 29]]
Model Accuracy: 0.94
```