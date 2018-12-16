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

<br>

___
## 4. Data cleaning and wrangling

<br>

___
## 5. Model Training and Evaluation

<br>

___
## 6. Model Validation

<br>

___
## 7. Conclusion

<br>

___

