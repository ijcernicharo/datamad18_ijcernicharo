# FINAL PROJECT - IJCERNICHARO
## Index
* [1. Description](#1\.-description)
* [2. Data Acquisition](#2\.-data-acquisition)
* [3. Data Exploration](#3\.-data-exploration)
* [4. Data cleaning and wrangling](#4\.-data-cleaning-and-wrangling)
* [5. Model Training and Evaluation](#5\.-model-training-and-evaluation)
* [6. Model Validation](#6\.-model-validation)
* [7. Conclusion](#7\.-conclusion)
<br>

___
## 1. Description

**SOURCE**: [PAMAP2 Physical Activity Monitoring Data Set](https://archive.ics.uci.edu/ml/datasets/PAMAP2+Physical+Activity+Monitoring)

Data Set Information:

The PAMAP2 Physical Activity Monitoring dataset contains data of 18 different physical activities (such as walking, cycling, playing soccer, etc.), performed by 9 subjects wearing 3 inertial measurement units and a heart rate monitor. The dataset can be used for activity recognition and intensity estimation, while developing and applying algorithms of data processing, segmentation, feature extraction and classification.

**Sensors**<br>
3 Colibri wireless inertial measurement units (IMU):
- sampling frequency: 100Hz
- position of the sensors:
- 1 IMU over the wrist on the dominant arm
- 1 IMU on the chest
- 1 IMU on the dominant side's ankle
HR-monitor:
- sampling frequency: ~9Hz

**Data collection protocol**<br>
Each of the subjects had to follow a protocol, containing 12 different activities. The folder `Protocol` contains these recordings by subject.
Furthermore, some of the subjects also performed a few optional activities. The folder `Optional` contains these recordings by subject.

**Data files**<br>
Raw sensory data can be found in space-separated text-files (.dat), 1 data file per subject per session (protocol or optional). Missing values are indicated with NaN. One line in the data files correspond to one timestamped and labeled instance of sensory data. The data files contain 54 columns: each line consists of a timestamp, an activity label (the ground truth) and 52 attributes of raw sensory data.

Attribute Information:

The 54 columns in the data files are organized as follows:
* 1     timestamp (s)
* 2     activityID (see below for the mapping to the activities)
* 3     heart rate (bpm)
* 4-20  IMU hand
* 21-37 IMU chest
* 38-54 IMU ankle

The IMU sensory data contains the following columns:
* 1      temperature (Â°C)
* 2-4    3D-acceleration data (ms-2), scale: Â±16g, resolution: 13-bit
* 5-7    3D-acceleration data (ms-2), scale: Â±6g, resolution: 13-bit
* 8-10   3D-gyroscope data (rad/s)
* 11-13  3D-magnetometer data (Î¼T)
* 14-17  orientation (invalid in this data collection)

List of activityIDs and corresponding activities:
* 1 lying
* 2 sitting
* 3 standing
* 4 walking
* 5 running
* 6 cycling
* 7 Nordic walking
* 9 watching TV
* 10 computer work
* 11 car driving
* 12 ascending stairs
* 13 descending stairs
* 16 vacuum cleaning
* 17 ironing
* 18 folding laundry
* 19 house cleaning
* 20 playing soccer
* 24 rope jumping
* 0 other (transient activities)

**Relevant Papers:**

The following two publications describe the dataset and provide a baseline benchmark on various tasks of physical activity recognition and intensity estimation:

[1] A. Reiss and D. Stricker. Introducing a New Benchmarked Dataset for Activity Monitoring. The 16th IEEE International Symposium on Wearable Computers (ISWC), 2012.

[2] A. Reiss and D. Stricker. Creating and Benchmarking a New Dataset for Physical Activity Monitoring. The 5th Workshop on Affect and Behaviour Related Assistance (ABRA), 2012. 
<br>

___
## 2. Data Acquisition
First of all is to download the dataset from the URL I have provided below and decompress the data. What we find is that we have 2 folders of data (`Protocol` and `Optional`) and 4 documents describing the dataset, the activities and some factors to take in count.<br><br>
Protocol and Optional files together make 1.7 GB of data. Since I do not have a workstation and I have limited RAM (8GB + 2GB of SWAP partition) I have to be careful when loading data. Avoid duplicities and just have the needed data in my work enviroment each moment. <br><br>
My workflow open these files in a loop and concatenate them into a two final dataframes, `final_data` and `final_data_optional`
<br>

___
## 3. Data Exploration
One of the best factors of this dataset is that all our Features are numerical, and only 2 of them are categorical. These makes reference to the `user ID` (This one has been defined by myself for analysis purpouses) and to the `activity ID`. <br><br>
For the numerical Features, we have 12 of them that are invalid to this study, does not make any sense. These features are the one for the orientation and in my workflow are named as `Hand O1`,`Hand O2`,`Hand O3`,`Hand O4`,`Chest O1`,`Chest O2`,`Chest O3`,`Chest O4`,`Ankle O1`,`Ankle O2`,`Ankle O3`, and `Ankle O4`. This step reduces from 55 to 43 Features.<br><br>
Next step is to see the distribution of our variables:<br><br>
**Protocol Dataframe Features**<br>
![Protocol Dataframe](Figures/01_1-Protocol_DF_Distribution.png)<br><br>
**Optional Dataframe Features**<br>
![Optional Dataframe](Figures/01_2-Optional_DF_Distribution.png)<br><br>
As we can observe, `Activity ID == 0` has the major number of registers. We can see the difference of activities in each dataframe, for Optional dataframe there are less activities and registers. Also, we can observe the Heart Ratio has very low number of registers compared to the rest of the Features.
<br>

___
## 4. Data cleaning and wrangling
First step is to interpolate the `Heart Rate` Feature to fill the 90% of missing data for this Feature (These are taken with a lower frequency so that is the why are less values).<br><br>
As you can see in the next 2 figures, once **interpolated** the results fits with the activities that subjects are told to do.<br><br>
**Protocol Activities Time Series**<br>
![Protocol Activities Time Series](Figures/01_3-time_series_protocol_dataset.png)<br><br>
**Optional Activities Time Series**<br>
![Optional Activities Time Series](Figures/01_4-time_series_optional_dataset.png)<br><br>
Registred time is higher in Protocol Activities Dataset than in Optional Activities dataset<br><br>
Next step is to deal with missing values. We are goint to check how many values are missing and how they affect to the dataframes if we consider that one register with one missing value, taking in count that we have 43 Features, is consider as null.<br><br>
```
Protocol DF >> N NaN rows for Heart Rate:  46  | Total rows:  2872533  | % of NaN rows:  0.0016
Protocol DF >> NaN rows:  27665  | Original rows:  2872533  | % of NaN rows:  0.96

Optional DF >> N NaN rows for Heart Rate:  54  | Total rows:  977972  | % of NaN rows:  0.0055
Optional DF >> NaN rows:  3493  | Original rows:  977972  | % of NaN rows:  0.36
```
My choice is to delete all these `NaN` values. Of course they could be filled with other options. **I am open to any suggestion for this step**.<br><br>
Once I have managed to drop all `NaN` values I have to drop even more. Why? Because the next note of the authors of the original Study:<br>
*Data labeled with activityID=0 should be discarded in any kind of analysis. This data mainly covers transient activities between performing different activities, e.g. going from one location to the next activity's location, or waiting for the preparation of some equipment. Also, different parts of one subject's recording (in the case when the data collection was aborted for some reason) was put together during these transient activities (noticeable by some “jumping” in the HR-data).*<br><br>
This step is going to remove many data. This Activity has many registers involved as you can see below:<br>
```
Protocol DF rows for activity 0:  923391  > Total rows of the DF:  2844822  > 32.46 % of bad Data
Optional DF rows for activity 0:  194846  > Total rows of the DF:  974425   > 20.0 % of bad Data
```
<br>
Also, I have made dropped the Users ID because is not longer needed in the study.<br><br>

**Collinearity Study**
Once I have cleaned it all from `NaN`, next step is to study the collinearity using the code below:<br>
```
df.corr()
```
These are the results with a heatmap, via `Seaborn` and `Matplotlib`<br>
![Corr Heatmap](Figures/01_5-corr_before_feature_removal.png)<br><br>
**All correlations below 0.6 will not appear in the figure above**<br><br>
I have observed that we have correlation between hand and chest temperature. ALso I observe that all acceletartion sensors have a high collinearity with the second pair of sensors for each part of the body. Acceleration sensors indexed as 1 (or wihout index)
are correlated with the sensors indexed as 2. But just with the sensors of he same body place. <br>
I also have collinearity between Time and Heart Rate Features, between Chest Temp, Hand Temp and Ankle Temp, between Chest magnetic Z axis (Chest MZ), Chest acceleration Z axis sensors 1 and 2 (Chest AZ/AZ2).<br><br>
Next step is to remove all these features that are shown below:<br>
```
Features to Drop:
['Time (s)', 'Chest Temp (°C)', 'Chest MZ', 'Hand AX2', 'Hand AY2', 'Hand AZ2', 'Chest AX2', 'Chest AY2', 'Chest AZ2', 'Ankle AX2', 'Ankle AY2', 'Ankle AZ2']

Number of Features droped:  12
Final Features of columns:  30
Final number of registers:  2701010
```
And finally lets check our correlation matrix is Fine to work in the further analysis and modeling (Figure below):<br>
![Corr Heatmap 2](Figures/01_6-corr_after_feature_removal.png)<br><br>
**Nice and smooth for Step 5 - Model Training and Evaluation**<br><br>
To avoid **RAM** Overflow I save the cleaned data to a pickle file so I can open it whenever I want without running the first workflow
```
data.to_pickle('physical_activity.pkl')
```

<br>

___
## 5. Model Training and Evaluation
My first thought was to use unsupervised learning, **KMEANS**, to clusterize data. To do that, I am going to create a new Feature with only one purpouse, check if the clusterization has any meaning.<br>
So first step, creating a JSON file (dictionary) with all the activities and activities ID to relate them to any kind of labelization. I have proposed the variable Intensity3 `from 1-3 Scale` and Intensity5 `from 1-5 Scale`. The porpouse of these variables are just to check how good have been the clusterization and to simplify the 18 different types of Activities.<br><br>
```
ID | Name             | intesity3 | intensity5
1    lying                  1           1
2    sitting                1           1
3    standing               1           2
4    walking                2           2
5    running                3           5
6    cycling                3           4
7    Nordic walking         3           3
9    watching TV            1           1
10   computer work          1           2
11   car driving            2           2
12   ascending stairs       2           4
13   descending stairs      2           3
16   vacuum cleaning        2           3
17   ironing                1           2
18   folding laundry        1           2
19   house cleaning         2           3
20   playing soccer         3           4
24   rope jumping           3           5
```
Once this has been done, I proceed to calculate the Features `Intensity3` and `Intensity5` with the values of the dictionary
```
if sum([type(item)==str for item in act_lab.keys()])!=0:
    df['Intensity3'] = list(map(lambda x: act_lab[str(x)]['intensity3'],df['Activity ID']))
    df['Intensity5'] = list(map(lambda x: act_lab[str(x)]['intensity5'],df['Activity ID']))
else:
    df['Intensity3'] = list(map(lambda x: act_lab[x]['intensity3'],df['Activity ID']))
    df['Intensity5'] = list(map(lambda x: act_lab[x]['intensity5'],df['Activity ID']))
```
and I drop the `Activity ID` Feature. Then, I use the `Standard Scaler` from sklearn module to scale all data not related with categorical Features (`Intensity3` and `Intensity5`).<br><br>
Once this is done, I proceed to apply KMEANS to the data and plot results for 4 diffirent number of clusters [2,3,4,5]:<br><br>
**2 CLUSTERS**<br>
![2 clusters](Figures/02-Kmeans_2_clusters.png)<br><br>
**3 CLUSTERS**<br>
![3 clusters](Figures/02-Kmeans_3_clusters.png)<br><br>
**4 CLUSTERS**<br>
![4 clusters](Figures/02-Kmeans_4_clusters.png)<br><br>
**5 CLUSTERS**<br>
![5 clusters](Figures/02-Kmeans_5_clusters.png)<br><br>

**KMEANS** Results: **Not** satisfactory!<br><br>
None of these clusterizations are good to me, I think KMEANS is not capable to correctly clusterize my data so I proceed to use Random Forest.<br><br>
First I am going to check If random forest could do the job and compare the results when it has 3 categories for the target, `Intensity3`, tp the case when it has 5 categories, `Intensity5` and print the confusion matrix with its Accuracies.<br><br>

* **3 Categories:** `Intensity3`<br>
![3 Cats](Figures/Random_forest_3_categories.png)<br><br>
* **5 Categories:** `Intensity5`<br>
![5 Cats](Figures/Random_forest_5_categories.png)<br><br>

Accuracy for both are great but having in mind that for crossvalidation and grid search I need proficiency in computing I am going to select `Intensity3` Feature as target and forget from `Intensity5` Feature.<br><br>


<br>

___
## 6. Model Validation
As we have seen, **KMEANS** is not a good unsupervised model for this dataset. So once I have proven that **Random Forest** is a good candidate 
```

Num of categories of Intensity: 3

	>> Accuracy:  0.9997719373331181

RandomForestClassifier(bootstrap=True, class_weight=None, criterion='entropy',
            max_depth=None, max_features='auto', max_leaf_nodes=None,
            min_impurity_decrease=0.0, min_impurity_split=None,
            min_samples_leaf=1, min_samples_split=2,
            min_weight_fraction_leaf=0.0, n_estimators=10, n_jobs=8,
            oob_score=False, random_state=42, verbose=0, warm_start=False)


Num of categories of Intensity: 5

	>> Accuracy:  0.9997334332465017

RandomForestClassifier(bootstrap=True, class_weight=None, criterion='entropy',
            max_depth=None, max_features='auto', max_leaf_nodes=None,
            min_impurity_decrease=0.0, min_impurity_split=None,
            min_samples_leaf=1, min_samples_split=2,
            min_weight_fraction_leaf=0.0, n_estimators=10, n_jobs=8,
            oob_score=False, random_state=42, verbose=0, warm_start=False)

CPU times: user 17min 28s, sys: 6.89 s, total: 17min 35s
Wall time: 3min 23s

```
I have prepared a grid search routine to find the best model for 3 categories inside the target Feature (`Intensity3`).<br><br>

**Random Forest** with `random_state = None`:<br>
```
GridSearchCV(cv=5, error_score='raise-deprecating',
       estimator=RandomForestClassifier(bootstrap=True, class_weight=None, criterion='gini',
            max_depth=None, max_features='auto', max_leaf_nodes=None,
            min_impurity_decrease=0.0, min_impurity_split=None,
            min_samples_leaf=1, min_samples_split=2,
            min_weight_fraction_leaf=0.0, n_estimators='warn', n_jobs=None,
            oob_score=False, random_state=None, verbose=0,
            warm_start=False),
       fit_params=None, iid='warn', n_jobs=-1,
       param_grid={'max_features': [3, 10], 'n_estimators': [10, 20], 'criterion': ['entropy']},
       pre_dispatch='2*n_jobs', refit=True, return_train_score='warn',
       scoring=None, verbose=0)

GridSearchCV took 2873.80 seconds for 4 candidate parameter settings.

Model with rank: 1
Mean validation score: 0.783 (std: 0.042)
Parameters: {'criterion': 'entropy', 'max_features': 3, 'n_estimators': 20}

Model with rank: 2
Mean validation score: 0.775 (std: 0.041)
Parameters: {'criterion': 'entropy', 'max_features': 3, 'n_estimators': 10}

Model with rank: 3
Mean validation score: 0.760 (std: 0.060)
Parameters: {'criterion': 'entropy', 'max_features': 10, 'n_estimators': 10}


CPU times: user 5min 51s, sys: 2.78 s, total: 5min 54s
Wall time: 47min 53s
```
<br>

___
## 7. Conclusion
Well, seems that the best model for this dataset is the Random Forest with: 
```
Model with rank: 1
Mean validation score: 0.783 (std: 0.042)
Parameters: {'criterion': 'entropy', 'max_features': 3, 'n_estimators': 20}
```
This could be better optimezed with time and better equipment so finnally we can use this model to predict which activity is going on and guide our users in a trainning course to improve his physical skills.<br><br>
I would recomend this dataset only if you have a lot of time and a good computer to analyze the information due to the sampling frequency and its inherent vast volume of data.<br><br>
I will be pleased to hear your thoughts and comments to improve this study. <br>Best regards,<br>Ivan
<br>

___

