# Employee_Attrition_Business_Analytics_Project
# Business Analytics Project on Employee Attrition of a Company

'PERFORMING ANALYSIS USING LOGISTIC REGRESSION MACHINE LEARNING TECHNIQUE'
'Prepared by - SHIVAM NEGI'

# IMPORT LIBRARIES
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split

# IMPORT AND READ DATASET
mydata = pd.read_csv('HR_Attrition.csv')
mydata.head()

# Check for data types and for null values
mydata.info()
mydata.isnull().sum()
# No data is missing

# ONE HOT ENCODING
# CONVERT CATEGORICAL COLUMNS INTO DUMMIES
# Because the ML model can be run on numeric values 

Attrition_1 = pd.get_dummies(mydata['Attrition'],drop_first=True)
Attrition_1.rename(columns={'Yes':'Attrition_1'},inplace=True)

BusinessTravel_1 = pd.get_dummies(mydata['BusinessTravel'],drop_first=True)

Department_1 = pd.get_dummies(mydata['Department'],drop_first=True)

Education_1 = pd.get_dummies(mydata['Education'],drop_first=True)

EducationField_1 = pd.get_dummies(mydata['EducationField'],drop_first=True)

EnvironmentSatisfaction_1 = pd.get_dummies(mydata['EnvironmentSatisfaction'],drop_first=True)

Gender_1 = pd.get_dummies(mydata['Gender'],drop_first=True)

JobInvolvement_1 = pd.get_dummies(mydata['JobInvolvement'],drop_first=True)

JobRole_1 = pd.get_dummies(mydata['JobRole'],drop_first=True)

JobSatisfaction_1 = pd.get_dummies(mydata['JobSatisfaction'],drop_first=True)

MaritalStatus_1 = pd.get_dummies(mydata['MaritalStatus'],drop_first=True)

OverTime_1 = pd.get_dummies(mydata['OverTime'],drop_first=True)

PerformanceRating_1 = pd.get_dummies(mydata['PerformanceRating'],drop_first=True)

RelationshipSatisfaction_1 = pd.get_dummies(mydata['RelationshipSatisfaction'],drop_first=True)

WorkLifeBalance_1 = pd.get_dummies(mydata['WorkLifeBalance'],drop_first=True)

# Drop older columns whose dummies are created
mydata = mydata.drop(['Attrition','BusinessTravel','Department','EnvironmentSatisfaction','Education','EducationField','Gender','JobInvolvement','JobRole','JobSatisfaction','MaritalStatus','OverTime','PerformanceRating','RelationshipSatisfaction','WorkLifeBalance'],axis=1,inplace=True)
# Concatenate these dummy columns in main dataset
mydata = pd.concat([mydata,Department_1,Education_1,EducationField_1,Gender_1,JobRole_1,MaritalStatus_1,OverTime_1,PerformanceRating_1,WorkLifeBalance_1 ],axis=1)
mydata = pd.concat([mydata,Attrition_1,BusinessTravel_1,Department_1,Education_1,EducationField_1,MaritalStatus_1,OverTime_1 ],axis=1)

mydata.head(5)
# Splitting into dependent and independent variables
x = mydata.drop('Attrition_1',axis=1).values
y = mydata['Attrition_1'].values

#Split into Training and testing
from sklearn.model_selection import train_test_split
X_train,X_test,y_train,y_test = train_test_split(x,y,test_size=0.2,random_state=45)

# LOGISTIC-REGRESSION
from sklearn.linear_model import LogisticRegression
logmodel = LogisticRegression()
logmodel.fit(X_train,y_train)

# Predicting y value for X_test values
y_pred = logmodel.predict(X_test)

from sklearn.metrics import confusion_matrix
confusion_matrix(y_test, y_pred)

# Accuracy of Model Before Backward Elimination = 81.97 %
 
# Automatic Backward Elimination

#Seperating the dependent and independent variable

import numpy as np
import statsmodels.api as sm
x = np.append(arr = np.ones((len(x), 1)).astype(int), values = x, axis = 1)

def backwardElimination(x, sl):
    numVars = len(x[0])
    for i in range(0, numVars):
        regressor_OLS = sm.OLS(y, x).fit()
        maxVar = max(regressor_OLS.pvalues).astype(float)
        if maxVar > sl:
            for j in range(0, numVars - i):
                if (regressor_OLS.pvalues[j].astype(float) == maxVar):
                    x = np.delete(x, j, 1)
    regressor_OLS.summary()
    return x

SL = 0.05
x_opt = x

x_Modeled = backwardElimination(x_opt, SL)

# Check the Model Accuracy after Backward Elimination
x_Modeled_1 = pd.DataFrame(x_Modeled)
x1 = x_Modeled_1.drop([0],axis=1).values
y1 = x_Modeled_1[0].values

#Split into Training and testing
from sklearn.model_selection import train_test_split
X_train,X_test,y_train,y_test = train_test_split(x1,y1,test_size=0.2,random_state=45)


from sklearn.linear_model import LogisticRegression
logmodel = LogisticRegression()
logmodel.fit(X_train,y_train)

# Predicting y value for X_test values
y_pred = logmodel.predict(X_test)

from sklearn.metrics import confusion_matrix
confusion_matrix(y_test, y_pred)

# Accuracy of model after Backward Elimination = 88.77 % 

