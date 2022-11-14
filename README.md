# Repository for ECG classification using deep learning 

Original version of this repository can be found from [here](https://github.com/ZhaoZhibin/Physionet2020model). It contains the Pytorch implementation of the ResNet model by *Between_a_ROC_and_a_heart_place*. The model was designed for the PhysioNet/Computing in Cardiology Challenge 2020. The related paper was accepted by the CinC2020 and titled
"[**Adaptive Lead Weighted ResNet Trained With Different Duration Signals for
Classifying 12-lead ECGs**](https://physionetchallenges.github.io/2020/papers/112.pdf)".

This version is refactored for more general analysis.



# Usage

Install the required pip packages from `requirements.txt` file using the following command:

```
pip install -r requirements.txt
```

Recommended Python version 3.10.4 (tested with Python 3.10.4).


# Data

Check out the notebook [Introductions for Data Handling](/notebooks/1_introductions_data_handling.ipynb) in `/notebooks/` for further introductions for downloading, preprocessing and splitting the data.


# In a Nutshell

If you want to preprocess data, you can do it with the script `preprocess_data.py`. This is not mandatory for the use of the repository, but keep in mind that if some transforms (e.g. BandPassFilter) are used during the training phase, training might slow down significantly. To preprocess the data, use the following command

```
python preprocess_data.py
```

Consider checking the `configs` directory for yaml configurations:

* Yaml files in the `data_splitting` directory are used to split the data into csv files for training and testing
* Yaml files in the `training` directory are used to train a model
* Yaml files in the `predicting` directory are used for predictions and evaluation

Two notebooks are available for creating training and testing yaml files based on the data splitting performed with the script `prepare_data.py`: [Yaml files of Database-wise Split for Training and Prediction](/notebooks/2_physionet_DBwise_yaml_files.ipynb) and [Yaml files of Stratified Split for Training and Prediction](/notebooks/2_physionet_stratified_yaml_files.ipynb). Be sure to perform the data splitting first.

1) To split the data for the model to use in training and testing, you'll need one of the following commands

```
python prepare_data.py physionet_stratified_smoke.yaml
python prepare_data.py physionet_DBwise_smoke.yaml
```

where `prepare_data.py` splits the data using either stratified split or database-wise split. On stratified run, `prepare_data.py` uses the implementation of `MultilabelStratifiedShuffleSplit` from `iterative-stratification` package. It makes csv files of the data splits which consists of a training set and a validation set. These csv files are later used in the training phase of the model and have the columns `path` (path for ECG recording in .mat format), `age` , `gender` and all the diagnoses in SNOMED CT codes used as labels in the classification. Csv files of test data are also created. Database-wise split uses the structure of the directory where the data is loaded.

The main structure of csv files are as follows:


| path  | age  | gender  | 10370003  | 111975006 | 164890007 | *other diagnoses...* |
| ------------- |-------------|-------------| ------------- |-------------|-------------|-------------|
| ./Data/A0002.mat | 49.0 | Female | 0 | 0 | 1 | ... |
| ./Data/A0003.mat | 81.0 | Female | 0 | 1 | 1 | ... |
| ./Data/A0004.mat | 45.0 |  Male  | 1 | 0 | 0 | ... |
| ... | ... |  ...  | ... | ... | ... | ... |

2) To train a model, you'll need to use either a yaml file or a directory as an argument and use one of the following commands

```
python train_model.py train_smoke.yaml
python train_model.py train_multiple_smoke
```

where `train_data.yaml` consists of needed arguments for the training in a yaml format, and `train_multiple_smoke` is a directory containing several yaml files. When using multiple yaml files at the same time, each yaml file is loaded and run separately. More detailed information about training is available in the notebook [Introductions for Training a Model](/notebooks/3_introductions_training.ipynb).

3) To test and evaluate a trained model, you'll need one of the following commands

```
python test_model.py predict_smoke.yaml
python test_model.py predict_multiple_smoke
```

where `predict_smoke.yaml` consists of needed arguments for the prediction phase in a yaml format, and `predict_multiple_smoke` is a directory containing several yaml files. When using multiple yaml files at the same time, each yaml file is loaded and run separately. More detailed information about prediction and evaluation is available in the notebook [Introductions for Prediction and Evaluation](/notebooks/4_introductions_prediction_evaluation.ipynb).


# Repository in details

```
.
├── configs                      
│   ├── data_splitting           # Yaml files considering a database-wise split and a stratified split   
│   ├── predicting               # Yaml files considering the prediction and evaluation phase
│   └── training                 # Yaml files considering the training phase
│   
├── data
│   ├── physionet_data_smoke     # Sample of The Physionet Challenge 2021 data for smoke testing
│   └── split_csvs               # Csv files of ECGs, either database-wise or stratified splitted
│
├── notebooks                    # Jupyter notebooks for data exploration and 
│                                  information about the use of the repository
├── src        
│   ├── dataloader 
│   │   ├── __init__.py
│   │   ├── dataset.py           # Script for custom DataLoader for ECG data
│   │   ├── dataset_utils.py     # Script for preprocessing ECG data
│   │   └── transforms.py        # Script for tranforms
│   │
│   └── modeling 
│       ├── models               # All model architectures
│       │   └── seresnet18.py    # PyTorch implementation of the SE-ResNet18 model
│       ├──__init__.py
│       ├── metrics.py           # Script for evaluation metrics
│       ├── predict_utils.py     # Script for making predictions with a trained model
│       └── train_utils.py       # Setting up optimizer, loss, model, evaluation metrics
│                                  and the training loop
│
├── .gitignore
├── LICENSE
├── LICENSE.txt
├── __init__.py
├── prepare_data.py              # Script to perform database-wise data split or 
│                                  split by the cross-validatior Multilabel Stratified ShuffleSplit 
├── preprocess_data_data.py      # Script for preprocessing data
├── README.md
├── requirements.txt             # The requirements needed to run the repository
├── test_model.py                # Script to test and evaluate a trained model
├── train_model.py               # Script to train a model
└── utils.py                     # Script for yaml configuration

```
