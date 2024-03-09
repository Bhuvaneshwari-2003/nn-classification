# Developing a Neural Network Classification Model

## AIM

To develop a neural network classification model for the given dataset.

## Problem Statement

An automobile company has plans to enter new markets with their existing products. After intensive market research, they’ve decided that the behavior of the new market is similar to their existing market.

In their existing market, the sales team has classified all customers into 4 segments (A, B, C, D ). Then, they performed segmented outreach and communication for a different segment of customers. This strategy has work exceptionally well for them. They plan to use the same strategy for the new markets.

You are required to help the manager to predict the right group of the new customers.

## Neural Network Model

![image](https://github.com/Bhuvaneshwari-2003/nn-classification/assets/94828604/908672ce-f813-4d55-8a7a-00d795a5be7e)


## DESIGN STEPS

### STEP 1:
Import the necessary packages & modules

### STEP 2:
Load and read the dataset

### STEP 3:
Perform pre processing and clean the dataset

### STEP 4:
Encode categorical value into numerical values using ordinal/label/one hot encoding

### STEP 5:
Visualize the data using different plots in seaborn

## PROGRAM

### Name: S.bhuvaneshwari
### Register Number:212221240010
```
import pandas as pd
from sklearn.model_selection import train_test_split
from tensorflow.keras.models import Sequential
from tensorflow.keras.models import load_model
import pickle
from tensorflow.keras.layers import Dense
from tensorflow.keras.layers import Dropout
from tensorflow.keras.layers import BatchNormalization
import tensorflow as tf
import seaborn as sns
from tensorflow.keras.callbacks import EarlyStopping
from sklearn.preprocessing import MinMaxScaler
from sklearn.preprocessing import LabelEncoder
from sklearn.preprocessing import OneHotEncoder
from sklearn.preprocessing import OrdinalEncoder
from sklearn.metrics import classification_report,confusion_matrix
import numpy as np
import matplotlib.pylab as plt

df = pd.read_csv('/customers.csv')
df.head()
df.columns
df.dtypes
df.shape
df.isnull().sum()
df_cleaned=df.dropna(axis=0)
df_cleaned.isnull().sum()
df_cleaned.shape
df_cleaned.dtypes
df_cleaned['Gender'].unique()
df_cleaned['Ever_Married'].unique()
df_cleaned['Graduated'].unique()
df_cleaned['Family_Size'].unique()
df_cleaned['Var_1'].unique()
df_cleaned['Spending_Score'].unique()
df_cleaned['Profession'].unique()
df_cleaned['Segmentation'].unique()

from sklearn.preprocessing import OrdinalEncoder
from sklearn.preprocessing import LabelEncoder
from sklearn.preprocessing import OneHotEncoder

categories_list=[['Male','Female'],
                 ['No','Yes'],
                 ['No','Yes'],
                 ['Healthcare', 'Engineer', 'Lawyer', 'Artist', 'Doctor',
                 'Homemaker', 'Entertainment', 'Marketing', 'Executive'],
                 ['Low','Average','High']
                 ]
enc = OrdinalEncoder(categories = categories_list)

cust_1=df_cleaned.copy()
cust_1[['Gender','Ever_Married','Graduated','Profession','Spending_Score']]=enc.fit_transform(cust_1[['Gender','Ever_Married','Graduated','Profession','Spending_Score']])

cust_1.dtypes


le = LabelEncoder()
     

cust_1['Segmentation'] = le.fit_transform(cust_1['Segmentation'])
     

cust_1.dtypes
     

cust_1 = cust_1.drop('ID',axis=1)
cust_1 = cust_1.drop('Var_1',axis=1)
     

cust_1.dtypes
     

# Calculate the correlation matrix
corr = cust_1.corr()

# Plot the heatmap
sns.heatmap(corr, 
        xticklabels=corr.columns,
        yticklabels=corr.columns,
        cmap="BuPu",
        annot= True)

sns.pairplot(cust_1)

cust_1.describe()
cust_1['Segmentation'].unique()

X=cust_1[['Gender','Ever_Married','Age','Graduated','Profession','Work_Experience','Spending_Score','Family_Size']].values

y1 = cust_1[['Segmentation']].values
one_hot_enc = OneHotEncoder()
one_hot_enc.fit(y1)
y1.shape
y = one_hot_enc.transform(y1).toarray() 
y.shape    
y1[0]
y[0]
X.shape
X_train,X_test,y_train,y_test=train_test_split(X,y,
                                               test_size=0.33,
                                               random_state=50)
X_train[0]
X_train.shape
scaler_age = MinMaxScaler()
scaler_age.fit(X_train[:,2].reshape(-1,1))
X_train_scaled = np.copy(X_train)
X_test_scaled = np.copy(X_test)

# To scale the Age column
X_train_scaled[:,2] = scaler_age.transform(X_train[:,2].reshape(-1,1)).reshape(-1)
X_test_scaled[:,2] = scaler_age.transform(X_test[:,2].reshape(-1,1)).reshape(-1)


# Creating the model
AI=Sequential([
    Dense(units=5,activation='relu',input_shape=[8]),
    Dense(units=3,activation='relu'),
    Dense(units=4,activation='relu'),
    Dense(units=4,activation='softmax')
])

AI.compile(optimizer='adam',
                 loss='categorical_crossentropy',
                 metrics=['accuracy'])
early_stop = EarlyStopping(monitor='val_loss', patience=2)


AI.fit(x=X_train_scaled,y=y_train,
             epochs=2000,batch_size=256,
             validation_data=(X_test_scaled,y_test),
             )
metrics = pd.DataFrame(AI.history.history)

metrics.head()
metrics[['loss','val_loss']].plot()
x_test_predictions = np.argmax(AI.predict(X_test_scaled), axis=1)
x_test_predictions.shape
y_test_truevalue = np.argmax(y_test,axis=1)
y_test_truevalue.shape
print(confusion_matrix(y_test_truevalue,x_test_predictions))
print(classification_report(y_test_truevalue,x_test_predictions))
     
# Saving the Model
AI.save('customer_classification_model.h5')

# Saving the data
with open('customer_data.pickle', 'wb') as fh:
   pickle.dump([X_train_scaled,y_train,X_test_scaled,y_test,cust_1,df_cleaned,scaler_age,enc,one_hot_enc,le], fh)

AI = load_model('customer_classification_model.h5')

# Loading the data
with open('customer_data.pickle', 'rb') as fh:
   [X_train_scaled,y_train,X_test_scaled,y_test,customers_1,customer_df_cleaned,scaler_age,enc,one_hot_enc,le]=pickle.load(fh)
     
#Prediction for a single input
x_single_prediction = np.argmax(AI.predict(X_test_scaled[1:2,:]), axis=1)
print(x_single_prediction)
print(le.inverse_transform(x_single_prediction))
```

## Dataset Information
![image](https://github.com/Bhuvaneshwari-2003/nn-classification/assets/94828604/b4a252c0-0f94-43b6-9cd4-43f6699fc5e6)



## OUTPUT
### Training Loss, Validation Loss Vs Iteration Plot
![image](https://github.com/Bhuvaneshwari-2003/nn-classification/assets/94828604/e82fb0fd-5ff1-49ac-a1bc-e2e94e2bf584)


### Classification Report

![image](https://github.com/Bhuvaneshwari-2003/nn-classification/assets/94828604/b301f744-c9bd-4d79-909e-4a4c5212a004)




### Confusion Matrix


![image](https://github.com/Bhuvaneshwari-2003/nn-classification/assets/94828604/5d77a4d4-43bf-40d1-a8ca-57dde2621f2e)


### New Sample Data Prediction

![image](https://github.com/Bhuvaneshwari-2003/nn-classification/assets/94828604/a8140894-c561-4d79-90ca-7f9d0c080b9c)



## RESULT
A neural network classification model is developed for the given dataset.
