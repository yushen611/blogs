---
title: 'python数据分析与机器学习'
date: 2022-09-19 12:21:21
tags: [python]
published: true
hideInList: true
feature: 
isTop: false
---
<meta name="referrer" content="no-referrer"/>

# PART1:数据分析

## 导入常用库

```py
import pandas as pd
import numpy as np 
import matplotlib.pyplot as plt
```



## 读取与保存数据

读取数据

```py
import pandas as pd
data=pd.read_csv('./Datas/XXX.csv')
```

展示数据

```py
data.head(5)
data.tail(5)
data.info()
data.describe()
data.dtypes # Show the data type of each column.
data.columns # Show all the column labels of the dataframe.
```

数据值分布计数

```py
# return each categorical variable's number
print(churn.Gender.value_counts())
print(churn.Education_Level.value_counts())
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311113118651.png" alt="image-20230311113118651" style="zoom:80%;" />

dataframe改列名

```py
# (a) Change the following columns header: ‘Region label’ to ‘Region’, ‘Area label’ to ‘Area’
# and ‘Urban label’ to ‘Urban’.
data_3_1a = data
data_3_1a = data_3_1a.rename(columns={"Region_label": "Region", "Area_label": "Area","Urban_label":"Urban"})
print(data_3_1a.columns)
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311194059824.png" alt="image-20230311194059824" style="zoom:80%;" />

dataframe深拷贝

```py
data1 = data.copy()
```



保存数据

```python
data.to_csv("xxx.csv")
```

## 离散值处理

convert strings to integers

```py
# convert strings to integers
attrition = {'Existing Customer': 0, 'Attrited Customer': 1}
gender = {'M': 0, "F": 1}
education = {'Uneducated': 1, 'High School': 2, 'College': 3, 'Graduate': 4, 'Post-Graduate': 5, 'Doctorate': 6, 'Unknown': 7}
martial = {'Single': 1, 'Married': 2, 'Divorced': 3, 'Unknown': 4}
income = {'Less than $40K': 1, '$40K - $60K': 2, '$60K - $80K': 3, '$80K - $120K': 4, '$120K +': 5, 'Unknown': 6}
card = {'Blue': 9436, 'Silver': 555, 'Gold': 116, 'Platinum': 20}

churn.Attrition_Flag = [attrition[item] for item in churn.Attrition_Flag]
churn.Gender = [gender[item] for item in churn.Gender]
churn.Education_Level = [education[item] for item in churn.Education_Level]
churn.Marital_Status = [martial[item] for item in churn.Marital_Status]
churn.Income_Category = [income[item] for item in churn.Income_Category]
churn.Card_Category = [card[item] for item in churn.Card_Category]
```





## 标准化与归一化(preprocess_pipeline)

例子1

```pytho
#特征处理 选择连续的数据项进行归一化
NumData = data[["Opnprc","Hiprc","Loprc","Clsprc","Dnshrtrd","ChangeRatio",]]
#z-score标准化
NumData = (NumData-NumData.mean())/(NumData.std())  
#min-max归一化
NumData = (NumData-NumData.min())/(NumData.max()-NumData.min())
```



例子2：利用PipeLine进行 **Drop null values & Standardize values**

PipeLine原理：有模型的时候当模型来用，没模型的时候当transformer来用

1. **连续值的pipeline**

```py
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler
from sklearn import set_config

set_config(display='diagram') # shows the pipeline structure graphically

num_pipeline = Pipeline([
        ("imputer", SimpleImputer(strategy="median")),
        ("scaler", StandardScaler())
    ])
```

2. **离散值处理**

   ```py
   from sklearn.preprocessing import OneHotEncoder
   cat_pipeline = Pipeline([
           ("imputer", SimpleImputer(strategy="most_frequent")),
           ("cat_encoder", OneHotEncoder(sparse=False, drop="first"))
       ])
   ```

3. **pipeline分类别合并**

num与cat参数的构建的两种方式

方式1

```py
from sklearn.compose import ColumnTransformer
from sklearn.preprocessing import StandardScaler

num_attribs = ["Customer_Age", "Dependent_count", "Months_on_book", 
               "Total_Relationship_Count", "Months_Inactive_12_mon",
               "Contacts_Count_12_mon", "Credit_Limit", "Total_Revolving_Bal",
               "Avg_Open_To_Buy", "Total_Amt_Chng_Q4_Q1", "Total_Trans_Amt",
               "Total_Trans_Ct", "Total_Ct_Chng_Q4_Q1", "Avg_Utilization_Ratio"]
cat_attribs = ["Gender", "Education_Level", "Marital_Status", "Income_Category",
               "Card_Category"]
```

方式2

```py
from sklearn.compose import  make_column_selector
num_attribs = make_column_selector(dtype_include=np.number)
cat_attribs = make_column_selector(dtype_include='object')
```

pipeline合并

```py


# The following step patches SimpleImputer to produce feature names
SimpleImputer.get_feature_names_out = StandardScaler.get_feature_names_out

preprocess_pipeline = ColumnTransformer([ # handle each type of column with appropriate pipeline
        ("num", num_pipeline, num_attribs),
        ("cat", cat_pipeline, cat_attribs),])

preprocess_pipeline
```

![image-20230311101254435](https://gitee.com/yushen611/img/raw/master/image-20230311101254435.png)



## 缺失值/重复值处理

空值检测

```py
# 检查数据中是否有缺失值
print(data.isnull().any())
data.isna().sum()
```

```py
import missingno as msno
msno.matrix(data)
msno.matrix(data)
```

![image-20230311152054694](https://gitee.com/yushen611/img/raw/master/image-20230311152054694.png)



```python
#删除包含缺失值的行
data.dropna(how='any', axis=0, inplace=True)
#去除重复值
data.drop_duplicates(inplace=True)
```

去除指定标识值

```py
# drop unknown records
unknown_rows = churn[(churn['Education_Level'] == 'Unknown') | 
                     (churn['Marital_Status'] == 'Unknown') | 
                     (churn['Income_Category'] == 'Unknown')].index
churn = churn.drop(unknown_rows)
```

去除指定行的空值

```py
null_rows_idx = housing["total_bedrooms"].isnull()
housing[null_rows_idx] 
```

```py
data.dropna(subset=['DISTRICT', 'DISTRICT_NAME', 'STREET'], inplace=True)
```

填充空值

```py
data["Rate"].fillna(0,inplace=True)
```





## 数值统计

```py
data.Age.mean() # 统计Age的均值
data.Age.max() # 统计Age的最大值
data.Age.min() # 统计Age的最大值
data.Age.count() # 统计Age的数量
data.Age.unique() # 统计Age的非重复数量
```

按某列进行排序

```py
data.sort_values(by="Age" , inplace=True, ascending=False)  #按Age这列进行降序
```





## 数据筛选

**dataframe.query()**

```py
data_query = data.query("Salary < 120000 and Year < 12")
print(data_query)
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311001057195.png" alt="image-20230311001057195" style="zoom:70%;" />

**中括号查询**

&

```py
crime2022_tidy = crime2022_tidy[(crime2022_tidy.Lat != 0.000000) & (crime2022_tidy.Long != 0.000000) & (crime2022_tidy.Location != (0, 0))]
```

|

```py
data_2_2_c=data[(data["Region_label"] == "Nelson") | (data["Urban_label"] == "Nelson")]
```

contains

```py
accidents[accidents['Date'].str.contains('2019')].head(5)
```

**dataframe.iloc()** 定位

```py
 # Show the 100-110th elements of the column ‘Region label’. (Include 100th and 110th,counted from 1)
data["Region_label"].iloc[100:111]
```

datafram按某列排序

```py
data.sort_values(by="Rate",axis=0,ascending=False,inplace=True)
```



## 离群值检测

```py
def detect_outliers(lineDataArr,threshold=3):
    mean_d = np.mean(lineDataArr)
    std_d = np.std(lineDataArr)
    outliers = []

    for i in range(len(lineDataArr)):
        y = lineDataArr[i]
        z_score= (y - mean_d)/std_d 
        if np.abs(z_score) > threshold:
            outliers.append(i)
    return outliers
def detect_outliers_byname(data,columename):
    res = detect_outliers(data[columename])
    return res
def detect_outliers_bylist(columenames):
    for name in columenames:
        res = detect_outliers_byname(data,name)
        print("name:{},num:{}".format(name,len(res)))
```



## 初步可视化

### **X-Y折线图**

dataframe折线图

```python
#初步可视化
NumData[["ChangeRatio","Opnprc"]].plot()
NumData[["ChangeRatio","Hiprc"]].plot()
NumData[["ChangeRatio","Loprc"]].plot()
NumData[["ChangeRatio","Clsprc"]].plot()
NumData[["ChangeRatio","Dnshrtrd"]].plot()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230310223906262.png" alt="image-20230310223906262" style="zoom:63%;" />

plt折线图

```py
sum_data = new_data.groupby(['year']).sum()
year_names = list(set(new_data['year']))
year_names.sort()
    
X=year_names
Y=sum_data['data_val'] 
fig = plt.figure()
plt.plot(X,Y,color="blue")
plt.title("Total gas emissions in each year")
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230310235520189.png" alt="image-20230310235520189" style="zoom:80%;" />



###  **散点图**

**例子0：dataframe自带的plot**

```py
data.plot(kind="scatter",x="LIMIT_BAL",y="default payment next month",alpha=0.1)
```

![image-20230311005748312](https://gitee.com/yushen611/img/raw/master/image-20230311005748312.png)

```py
housing.plot(kind="scatter", x="longitude", y="latitude", grid=True, alpha=0.1)
plt.show()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311114259332.png" alt="image-20230311114259332" style="zoom:80%;" />



**例子1**

```py
#数据分布初步可视化
x1 = data['年份']
y1 = data['地方预算内财政收入Y']

x2 = data['年份']
y2 = data['国内生产总值(GDP)X']

plt.scatter(x1,y1, label='local income', color='blue', s=25, marker="o")
plt.scatter(x2,y2, label='GDP', color='yellow', s=25, marker="o")

plt.xlabel('year')
plt.ylabel('local income /GDP')

plt.title('local income /GDP by year',fontsize=20,color='black')
plt.legend()
plt.show()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230310232942410.png" alt="image-20230310232942410" style="zoom:50%;" />



**例子2**

```py
changeRatio = NumData['ChangeRatio']
x_name_list = ["Opnprc","Hiprc","Loprc","Clsprc","Dnshrtrd"]
colors = ['c', 'b', 'g', 'r', 'm', 'y', 'k', 'w']
for index,x_name in enumerate(x_name_list):
    plt.scatter(NumData[x_name],changeRatio, label=x_name, color=colors[index], s=25, marker="o")
    plt.title('Scatter plot '+ 'Outcome  vs'+ x_name,fontsize=20,color='black')
    plt.legend()
    plt.show()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230310224444526.png" alt="image-20230310224444526" style="zoom:67%;" />

例子3 散点图矩阵

```py
import numpy as np
import matplotlib.pyplot as plt
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

X = df[['SepalLengthCm','SepalWidthCm','PetalLengthCm','PetalWidthCm']]
Y = df[['Cluster','Species']]

#可视化展现
grr=pd.plotting.scatter_matrix(X,c=np.squeeze(Y[['Cluster']]),figsize=(8,8),marker="o",hist_kwds={'bins':20},s=60,alpha=.8,cmap=plt.cm.Paired)
plt.show()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311003805226.png" alt="image-20230311003805226" style="zoom:67%;" />



### 子图

例子1

```py
#c Split array, plot two in same figure

import matplotlib.pyplot as plt

plt.figure()

y1 = i(1800,1810)
y2 = i(1900,1910)
y=[y1,y2]
color = ['r','b']
titles=['1800','1900']

for plt_index in range(1,3):
    
    plt.subplot(1, 2, plt_index)
    
    plt.title(titles[plt_index-1])
    
    plt.plot(y[plt_index-1],color[plt_index-1])

#e save
plt.show()
plt.savefig('pic-c.png')
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230310232644543.png" alt="image-20230310232644543" style="zoom:67%;" />

例子2

```py
import matplotlib.pyplot as plt
import numpy as np
#创建自定义图像
fig = plt.figure()
#第一个子图 1行2列
ax1 = fig.add_subplot(1, 2, 1)
#第二个子图 1行2列
ax2 = fig.add_subplot(1, 2, 2)
#设置图1
ax1.hist(np.random.randn(100), bins=20, color='K')
#设置图2
ax2.scatter(np.arange(30), np.arange(30) + 3 * np.random.randn(30))
plt.show()
```

![image-20230311000328984](https://gitee.com/yushen611/img/raw/master/image-20230311000328984.png)



例子3

```py
sort_meanFiles = film_means.sort_values(by="fee",ascending=False)
plt.subplot(211)
plt.bar(film_means['name'], film_means['fee'])
plt.subplot(212)
plt.bar(sort_meanFiles['name'], sort_meanFiles['fee'])
plt.show()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311000403797.png" alt="image-20230311000403797" style="zoom:80%;" />



### 饼图

```py
weeks = np.array(['Mon','Tue','Wed','Thu','Fri','Sat','Sun'])
plt.pie(books[:7], labels=weeks)
plt.show()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230310235143901.png" alt="image-20230310235143901" style="zoom:80%;" />

### 柱状图

例子0：dataframe自带的画图

```py
dataframe.plot.bar()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311190124188.png" alt="image-20230311190124188" style="zoom:80%;" />



例子1

```py
plt.bar(film_means['name'], film_means['fee'])
plt.show()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311000018189.png" alt="image-20230311000018189" style="zoom:80%;" />

例子2

```py
sum_data = new_data.groupby(['region']).sum()
region_names = list(set(new_data['region']))
region_names.sort()
    
X=region_names
Y=sum_data['data_val'] 
fig = plt.figure()
plt.bar(X,Y,0.4,color="blue")
plt.xticks(rotation=300)
plt.title("2021 greenhouse gas emissions") 
plt.show()  
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230310235324609.png" alt="image-20230310235324609" style="zoom:67%;" />

例子3：水平与垂直柱状图

```py
#随机生成6组 每组4项的数据
df = pd.DataFrame(np.random.rand(6, 4), index=['one', 'two', 'three', 'four', 'five', 'six'],columns=pd.Index(['A', 'B','C','D']))
#水平柱状图
df.plot.bar()
#垂直柱状图
df.plot.barh(stacked=True, alpha=0.5)
#显示图像
plt.show()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311000146693.png" alt="image-20230311000146693" style="zoom:80%;" />

例子4：分类变量的柱状图

```py
# catplot of gender
sns.catplot(x = 'Gender', hue = 'Attrition_Flag', data = churn, kind="count")
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311110610380.png" alt="image-20230311110610380" style="zoom:80%;" />

```py
# catplot of education
sns.catplot(x = 'Education_Level', hue = 'Attrition_Flag', data = churn, 
            kind="count", order = ['Uneducated', 'High School', 'College', 
                                   'Graduate', 'Post-Graduate', 'Doctorate', 
                                   'Unknown'], 
            aspect = 1.5)
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311110727635.png" alt="image-20230311110727635" style="zoom:67%;" />



### 频率分布直方图

```python
#histograms
data.hist(color='r', alpha=0.5, bins=50)
plt.show()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311001415999.png" alt="image-20230311001415999" style="zoom:67%;" />

```py
# Customer_Age
# change graph type (area/distribution)
plt.figure(figsize=(12, 6))
axis_name="percentage of customers"
gp_age = churn.groupby("Customer_Age")["Attrition_Flag"].value_counts()/len(churn)
gp_age = gp_age.to_frame().rename({"Attrition_Flag": axis_name}, axis=1).reset_index()
sns.barplot(x='Customer_Age', y= axis_name, hue='Attrition_Flag', data=gp_age)
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311112923289.png" alt="image-20230311112923289" style="zoom:80%;" />



### 相关矩阵

相关系数矩阵

```py
corr = data.corr() # Compute the correlation matrix 
sns.heatmap(corr, cmap='RdBu', vmin=-1, vmax=1)
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311110257616.png" alt="image-20230311110257616" style="zoom:67%;" />

相关矩阵

```py
from pandas.plotting import scatter_matrix
plt.clf()
attributes = ["median_house_value", "median_income", "total_rooms",
              "housing_median_age"]
scatter_matrix(housing[attributes], figsize=(12, 10))
plt.show()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311114441094.png" alt="image-20230311114441094" style="zoom:67%;" />

相关性图片

```py
sns.pairplot(data,hue='TenYearCHD')
```

![image-20230311190413258](https://gitee.com/yushen611/img/raw/master/image-20230311190413258.png)



### 箱线图

```py
sns.boxplot(x = 'Attrition_Flag', y = 'Customer_Age', data = churn)
```

![image-20230311110502811](https://gitee.com/yushen611/img/raw/master/image-20230311110502811.png)





## 回归分析

### OLS

主要涉及**statsmodels**库的使用

**Use** **`statsmodels`** **to regress mpg on all other variables. Note you can tell** **`ols()`** **to treat a variable as categorical by enclosing the variable in** **`C()`**

OLS模型构建

```py
model3 = smf.ols(formula="mpg ~ cylinders + displacement + weight + acceleration +C(year) +origin", data=data).fit()
```

```py
print(model3.params)
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311002703177.png" alt="image-20230311002703177" style="zoom:67%;" />

```py
print(model3.summary())
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311002848287.png" alt="image-20230311002848287" style="zoom:80%;" />

```py
#回归结果
fig = plt.figure(figsize=(15,8))
fig = sm.graphics.plot_regress_exog(model3,"origin", fig =fig)
plt.show()
```

![image-20230311003110639](https://gitee.com/yushen611/img/raw/master/image-20230311003110639.png)

plot_partregress_grid 绘制多元偏回归图，展示包括截距项在内对多个自变量与因变量间的关系。并同时加上线性拟合线展示对收盘价对影响。

```py
fig = plt.figure(figsize=(20,12))
fig = sm.graphics.plot_partregress_grid(stock_models,  fig=fig)
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311003328796.png" alt="image-20230311003328796" style="zoom:67%;" />

### VIF

**Get the VIF of the independent variables**

```PY
from statsmodels.stats.outliers_influence import variance_inflation_factor

def getVifAndMulticoPredictors(x):
    vif=[variance_inflation_factor(x.values,x.columns.get_loc(i)) for i in x.columns]
    MulticoPredictors = []
    for i in range(len(vif)):
        if(vif[i]>10):
            MulticoPredictors.append(list(x.columns)[i])
    return vif,MulticoPredictors
X = boston_df[x_columns]
vif,MulticoPredictors = getVifAndMulticoPredictors(X)
vif
```

![image-20230311200654137](https://gitee.com/yushen611/img/raw/master/image-20230311200654137.png)



# PART2:机器学习

## 常用库函数

```python
#import lib
import pandas as pd
import pandas_datareader.data as web
import numpy as np
from datetime import datetime 
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error
from sklearn.metrics import mean_absolute_error
from sklearn.neighbors import KNeighborsClassifier
from sklearn.linear_model import LogisticRegression
from math import sqrt
from sklearn.metrics import classification_report
from sklearn.metrics import confusion_matrix
from sklearn import svm, datasets
from sklearn.metrics import roc_curve, auc  ###计算roc和auc
from sklearn import model_selection 
from sklearn.metrics import roc_auc_score
import warnings
from pandas.core.common import SettingWithCopyWarning
from sklearn.preprocessing import PolynomialFeatures
from sklearn.tree import DecisionTreeClassifier 
from sklearn.model_selection import cross_val_score 
```



```py
plt.rcParams['axes.unicode_minus']=False #用来正常显示负号
warnings.simplefilter(action="ignore", category=SettingWithCopyWarning)
```



## 数据集划分

例子1

```py
x = np.array(NumData[["Opnprc","Dnshrtrd"]])
y = np.array(NumData['ChangeRatio'])
x_train,x_test,y_train,y_test = train_test_split(x,y,test_size=0.4)
```

例子2

```py
df_X = train_data.drop("Attrition_Flag", axis=1)
df_y = train_data["Attrition_Flag"]
print(df_X.head())  # to check that 'y' isn't included

X_train = preprocess_pipeline.fit_transform(df_X)
y_train = df_y.values
X_test = preprocess_pipeline.transform(test_data)
y_test = test_data["Attrition_Flag"]

```

补充 dataframe.drop()

```py
data.drop(columns=["Region_code","Area_code","Urban_code"])
```







## 模型构建与训练

**模型构建**

单一model构建

```py
clf = LogisticRegression()
clf = KNeighborsClassifier(n_neighbors=5)
clf = RandomForestClassifier()
```

pipeline：结合transformer与model

```py
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import cross_val_score
from sklearn.pipeline import make_pipeline

clf = make_pipeline(preprocess_pipeline, LogisticRegression())
clf = make_pipeline(preprocess_pipeline, SVC(gamma="auto"))
clf = make_pipeline(preprocess_pipeline, RandomForestClassifier(n_estimators=100, random_state=42))
clf = make_pipeline(preprocess_pipeline, DecisionTreeClassifier(random_state=42))
```

pipeline的另一种构建方式

```py
from sklearn.feature_selection import SequentialFeatureSelector

selection_pipeline = Pipeline([
    ('preprocessing', preprocess_pipeline),
    ('select', SequentialFeatureSelector(LogisticRegression(), n_features_to_select=1.0)),
])

selection_pipeline
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311112338499.png" alt="image-20230311112338499" style="zoom:80%;" />

```py
clf = make_pipeline(selection_pipeline, LogisticRegression())
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311112512105.png" alt="image-20230311112512105" style="zoom:67%;" />



**模型训练**

```py
clf.fit(X_train, y_train)
```



## 模型参数调优

例子1：

只用单一模型，不使用pipeline时。以网格搜索为例。

```py
parameters = {
    'max_depth':np.arange(5,14,2),
    'min_samples_leaf':np.arange(1,3),
    'criterion':['gini','entropy']
}
model = RandomForestClassifier()

grid_search = GridSearchCV(model,parameters,scoring='roc_auc',cv=5)

grid_search.fit(X1test, Y1test)

grid_search.best_params_
```

【OUT】 : {'criterion': 'entropy', 'max_depth': 7, 'min_samples_leaf': 1}

```py
grid_search.best_score_
```

【OUT】: 0.618511781446462



==例子2：pipeline model **Grid Search**==



首先需要看一下模型有哪些参数

```py
print(preprocess_pipeline.get_feature_names_out()) # check the column names produced by the pipeline
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311102645350.png" alt="image-20230311102645350" style="zoom:67%;" />

```py
from sklearn.model_selection import GridSearchCV
from sklearn.ensemble import RandomForestClassifier

full_pipeline = Pipeline([
    ("preprocessing", preprocess_pipeline),
    ("random_forest", RandomForestClassifier(n_estimators=100, random_state=42)),
])
param_grid = [ 
    {'random_forest__max_depth': [2, 5, 10], 
    'random_forest__min_samples_leaf': [2, 5, 10], }, 
    ]
grid_search = GridSearchCV(full_pipeline, param_grid, cv=3, scoring='balanced_accuracy')
grid_search.fit(df_X, df_y)

grid_search.best_estimator_
cv_res = pd.DataFrame(grid_search.cv_results_)
cv_res.sort_values(by="mean_test_score", ascending=False, inplace=True)
cv_res.head()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311104517212.png" alt="image-20230311104517212" style="zoom:80%;" />

```py
grid_search.best_params_
```

【OUT】：{'random_forest__max_depth': 10, 'random_forest__min_samples_leaf': 2}

```py
final_model = grid_search.best_estimator_  # includes preprocessing
final_predictions = final_model.predict(X_test)
```



例子3：**Random Search**

由于网格搜索对每个参数都有搜索一遍，特别耗时，所以有了随机搜索

```py
from sklearn.model_selection import RandomizedSearchCV
from sklearn.ensemble import RandomForestClassifier
from scipy.stats import randint

full_pipeline = Pipeline([
    ("preprocessing", preprocess_pipeline),
    ("random_forest", RandomForestClassifier(n_estimators=100, random_state=42)),
])
param_distribs = [ 
    {'random_forest__max_depth': randint(2, 100), 
    'random_forest__min_samples_leaf': randint(2, 100), }, 
    ]
random_search = RandomizedSearchCV(full_pipeline, param_distribs, n_iter=20, cv=3, 
                                 scoring='balanced_accuracy', random_state=42)
random_search.fit(df_X, df_y)
random_search.best_estimator_
cv_res = pd.DataFrame(random_search.cv_results_)
cv_res.sort_values(by="mean_test_score", ascending=False, inplace=True)
cv_res.head()
```

![image-20230311105127159](https://gitee.com/yushen611/img/raw/master/image-20230311105127159.png)

```py
random_search.best_params_
```

【OUT】：{'random_forest__max_depth': 54, 'random_forest__min_samples_leaf': 3}

```py
final_model = random_search.best_estimator_  # includes preprocessing
final_predictions = final_model.predict(X_test)
```



## 模型预测

```py
y_pred = rf_clf.predict(X_test)
```

y_pred 与 y_test的**对比可视化**

例子1

```py
plt.title(u'预测值与实际值比较（一元线性回归）')
plt.ylabel(u'日均票房收入\万元')
plt.plot(range(len(y_pred)),y_pred,'red', linewidth=2.5,label=u"预测值")
plt.plot(range(len(y_test)),y_test,'green',label=u"测试值")
plt.legend(loc=2)
#显示预测值与测试值曲线
plt.show()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311000717755.png" alt="image-20230311000717755" style="zoom:80%;" />



例子2

```py
#模型预测结果可视化
x1 = x_test
y1 = y_test

x2 = x_test
y2 = y_pred

plt.scatter(x1,y1, label='test', color='blue', s=25, marker="o")
plt.scatter(x2,y2, label='predict', color='yellow', s=25, marker="o")

plt.xlabel('local income')
plt.ylabel('GDP')

plt.title('GDP by local income',fontsize=20,color='black')
plt.legend()
plt.show()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230310233058288.png" alt="image-20230310233058288" style="zoom:80%;" />







## 模型评估

### 不同模型的比较箱型图

```py
#分数的计算举例
dt_scores = cross_val_score(clf, X_train, y_train, cv=10, scoring='balanced_accuracy')

# 画图
plt.figure(figsize=(8, 4))
plt.plot([1]*10, svc_scores, ".")
plt.plot([2]*10, lr_scores, ".")
plt.plot([3]*10, knn_scores, ".")
plt.plot([4]*10, rf_scores, ".")
plt.plot([5]*10, dt_scores, ".")

plt.boxplot([svc_scores, lr_scores, knn_scores, rf_scores, dt_scores],
            labels=("SVM", "Logistic Regression", "kNN", "Random Forest", "Decision Tree"))
plt.ylabel("Accuracy")
plt.show()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311112005679.png" alt="image-20230311112005679" style="zoom:80%;" />

或者

```py
scores = {"SVM": svc_scores, "Logistic Regression": lr_scores, "kNN": knn_scores, "Random Forest": rf_scores, "Decision Tree": dt_scores}
scores_df = pd.DataFrame(data=scores)
```

```py
sns.catplot(data=scores_df, kind='box', aspect=1.5, palette='Blues').set(title='Performance of different classifiers', ylabel='Accuracy')
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311112132147.png" alt="image-20230311112132147" style="zoom:80%;" />



### classification_report

```py
from sklearn.metrics import classification_report
print(classification_report(y_test, y_pred))
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311111159042.png" alt="image-20230311111159042" style="zoom:67%;" />



### **f1_score**,**precision_score**,**recall_score**,**accuracy_score**

```py
# Updates model score to f1_dict
f1_dict["RandomForest"] = f1_score(y_pred, y_test)
precision_dict["RandomForest"] = precision_score(y_pred, y_test)
recall_dict["RandomForest"] = recall_score(y_pred, y_test)
accuracy_dict['RandomForest'] = accuracy_score(y_pred, y_test)
```

### 交叉验证

```py
from sklearn.model_selection import cross_val_score

forest_scores = cross_val_score(forest_clf, X_train, y_train, cv=10, scoring='balanced_accuracy')
forest_scores.mean().round(3)
```





###  **误差分析**

```pyth
#误差分析
#Mean Absolute Error (MAE), Mean Squared Error
# (MSE) and Root Mean Squared Error (RMSE). 
y_pred = rf.predict(x_test)
MAE = mean_absolute_error(y_test, y_pred)
MSE = mean_squared_error(y_test, y_pred)
RMSE = sqrt(MSE)
print("MAE:{},MSE:{},RMSE:{}".format(MAE,MSE,RMSE))
```



### **混淆矩阵**

例子1

```py
from sklearn.metrics import ConfusionMatrixDisplay

ConfusionMatrixDisplay.from_predictions(y_test, y_pred);
```

![image-20230311111025073](https://gitee.com/yushen611/img/raw/master/image-20230311111025073.png)



例子2：自定义画图

```py
#混淆矩阵

def continue2class(continue_y,coefficient):
    assert(coefficient>=0 and coefficient <= 1 )
    Max = np.max(continue_y)
    Min = np.min(continue_y)
    threshold = coefficient*(Max-Min) + Min
    classy = []
    for v in continue_y:
        if v>= threshold:
            classy.append(1)
        else:
            classy.append(0)
    return classy
y_true = continue2class(y_test,0.5)
y_pred = continue2class(y_pred,0.5)
def ConfuseMatrix(y_pred,y_true):
    C = confusion_matrix(y_true, y_pred) 

    plt.matshow(C, cmap=plt.cm.Reds) # 根据最下面的图按自己需求更改颜色
    # plt.colorbar()

    for i in range(len(C)):
        for j in range(len(C)):
            plt.annotate(C[j, i], xy=(i, j), horizontalalignment='center', verticalalignment='center')

    # plt.tick_params(labelsize=15) # 设置左边和上面的label类别如0,1,2,3,4的字体大小。

    plt.ylabel('True label')
    plt.xlabel('Predicted label')
    # plt.ylabel('True label', fontdict={'family': 'Times New Roman', 'size': 20}) # 设置字体大小。
    # plt.xlabel('Predicted label', fontdict={'family': 'Times New Roman', 'size': 20})
    # plt.xticks(range(0,5), labels=['a','b','c','d','e']) # 将x轴或y轴坐标，刻度 替换为文字/字符
    # plt.yticks(range(0,5), labels=['a','b','c','d','e'])
    plt.show()
    
```



```python
y_pred = y_pred 
y_true = y_test
# 对上面进行赋值
ConfuseMatrix(y_true,y_pred)
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230310231008770.png" alt="image-20230310231008770" style="zoom:70%;" />



### ROC曲线

例子1

```py
from sklearn.metrics import RocCurveDisplay
RocCurveDisplay.from_estimator(forest_clf, X_t, y_test);
```

![image-20230311111308994](https://gitee.com/yushen611/img/raw/master/image-20230311111308994.png)

例子2

```py
from sklearn.metrics import roc_curve
fpr, tpr, thresholds_keras = roc_curve(y_test, y_pred1)
plt.figure()
plt.plot([0, 1], [0, 1], 'k--')
plt.plot(fpr, tpr, label='(ROC curve )')
plt.xlabel('False positive rate')
plt.ylabel('True positive rate')
plt.title('ROC curve')
plt.legend(loc='best')
plt.show()
```

<img src="https://gitee.com/yushen611/img/raw/master/image-20230311191158981.png" alt="image-20230311191158981" style="zoom:80%;" />



例子3：自己画ROC曲线

```py
#ROC曲线
def acu_curve(y,prob):
    fpr,tpr,threshold = roc_curve(y,prob) ###计算真正率和假正率
    roc_auc = auc(fpr,tpr) ###计算auc的值
 
    plt.figure()
    lw = 2
    plt.figure(figsize=(10,10))
    plt.plot(fpr, tpr, color='darkorange',
             lw=lw, label='ROC curve (area = %0.3f)' % roc_auc) ###假正率为横坐标，真正率为纵坐标做曲线
    plt.plot([0, 1], [0, 1], color='navy', lw=lw, linestyle='--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('Receiver operating characteristic example')
    plt.legend(loc="lower right")
 
    plt.show()

acu_curve(y_true,y_pred) #下面两张图分别是两个模型训练的结果
```



<img src="https://gitee.com/yushen611/img/raw/master/image-20230310231206725.png" alt="image-20230310231206725" style="zoom:67%;" />![image-20230310231248729](https://gitee.com/yushen611/img/raw/master/image-20230310231248729.png)

<img src="https://gitee.com/yushen611/img/raw/master/image-20230310231206725.png" alt="image-20230310231206725" style="zoom:67%;" />![image-20230310231248729](https://gitee.com/yushen611/img/raw/master/image-20230310231248729.png)



