# Project: Investigating Medical Appointments Scheduling in Brazil

## Table of Contents
<ul>
<li><a href="#intro">Introduction</a></li>
<li><a href="#wrangling">Data Wrangling</a></li>
<li><a href="#eda">Exploratory Data Analysis</a></li>
<li><a href="#conclusions">Conclusions</a></li>
</ul>

<a id='intro'></a>
## Introduction

The purpose of this project is to investigate a dataset containing records of scheduled medical appointments in Brazil. The goal of the project is to understand how certain factors could possibly affect whether or not people show up for scheduled appointments. We focus on some variables of interest to help us achieve our set goal. 

At the end of this project, we want to be able to answer the following questions:

- How does gender affect number of appointments missed?
- Do patients who receive the Bolsa Família Scholarship tend to show up to more appointments than patients who do not?
- How do Hypertension patients tend to respond to medical appointments?
- Do patients who have one or more SMS messages sent to them tend to show up to more or less appointments than patients who do not?
- How does Scholarship affect male and female missed appointment rates?
- How do SMS reminders affect the missed appointment rates of Hypertension patients?

To begin the project, we start by importing the relevant Python modules that will be used.


```python
# Import relevant packages
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
import seaborn as sns
```

After successfully importing all necessary packages for this project, we then move on to the data wrangling section

<a id='wrangling'></a>
## Data Wrangling

In this section, we will load the data to be investigated, check for cleanliness and clean the dataset, ensuring it is ready for analysis.

### Load the Data

We begin by loading the dataset to be investigated. This dataset can be downloaded [here](https://www.kaggle.com/joniarroba/noshowappointments). We will also take a quick glance at the first 5 columns to get a feel for what the data looks like. The code block to do these is found in the cell below.


```python
# Load data and view the first 5 columns
df = pd.read_csv('appointments.csv')
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PatientId</th>
      <th>AppointmentID</th>
      <th>Gender</th>
      <th>ScheduledDay</th>
      <th>AppointmentDay</th>
      <th>Age</th>
      <th>Neighbourhood</th>
      <th>Scholarship</th>
      <th>Hipertension</th>
      <th>Diabetes</th>
      <th>Alcoholism</th>
      <th>Handcap</th>
      <th>SMS_received</th>
      <th>No-show</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2.987250e+13</td>
      <td>5642903</td>
      <td>F</td>
      <td>2016-04-29T18:38:08Z</td>
      <td>2016-04-29T00:00:00Z</td>
      <td>62</td>
      <td>JARDIM DA PENHA</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>No</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5.589978e+14</td>
      <td>5642503</td>
      <td>M</td>
      <td>2016-04-29T16:08:27Z</td>
      <td>2016-04-29T00:00:00Z</td>
      <td>56</td>
      <td>JARDIM DA PENHA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>No</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4.262962e+12</td>
      <td>5642549</td>
      <td>F</td>
      <td>2016-04-29T16:19:04Z</td>
      <td>2016-04-29T00:00:00Z</td>
      <td>62</td>
      <td>MATA DA PRAIA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>No</td>
    </tr>
    <tr>
      <th>3</th>
      <td>8.679512e+11</td>
      <td>5642828</td>
      <td>F</td>
      <td>2016-04-29T17:29:31Z</td>
      <td>2016-04-29T00:00:00Z</td>
      <td>8</td>
      <td>PONTAL DE CAMBURI</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>No</td>
    </tr>
    <tr>
      <th>4</th>
      <td>8.841186e+12</td>
      <td>5642494</td>
      <td>F</td>
      <td>2016-04-29T16:07:23Z</td>
      <td>2016-04-29T00:00:00Z</td>
      <td>56</td>
      <td>JARDIM DA PENHA</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>No</td>
    </tr>
  </tbody>
</table>
</div>



We notice something interesting in the PatientId column. The records in this column look a bit odd. They seem to be in the wrong format. 

We also see that the No-show column is currently categorical. This is not ideal in this case. 

These anomalies will be addressed in the next two sub-sections.

#### Column Descriptions

01 - PatientId
Identification of a patient

02 - AppointmentID
Identification of each appointment

03 - Gender
Male or Female . Female is the greater proportion, woman takes way more care of they health in comparison to man.

04 - DataMarcacaoConsulta
The day of the actuall appointment, when they have to visit the doctor.

05 - DataAgendamento
The day someone called or registered the appointment, this is before appointment of course.

06 - Age
How old is the patient.

07 - Neighbourhood
Where the appointment takes place.

08 - Scholarship
True of False . Observation, this is a broad topic, consider reading this article https://en.wikipedia.org/wiki/Bolsa_Fam%C3%ADlia

09 - Hipertension
True or False

10 - Diabetes
True or False

11 - Alcoholism
True or False

12 - Handcap
True or False

13 - SMS_received
1 or more messages sent to the patient.

14 - No-show
True or False.

(Source: Kaggle)

### Check for cleanliness

There are three common factors (amongst others) to look into when determining the cleanliness of a dataset. These are:

- Duplication in rows
- Missing values and the columns that have them
- Incorrect Data types in columns

First, we identify these major issues, then we check for any possible minor issues, before we finally clean the data itself. 


```python
# Checking for duplicates
print('DUPLICATE VALUES IN EACH COLUMN')
print(' ')
for i,v in enumerate(df.columns):
    print(v+':', sum(df[v].duplicated()))
    
print(' ')
print('NUMBER OF ROW LEVEL DUPLICATES: ', sum(df.duplicated()))
```

    DUPLICATE VALUES IN EACH COLUMN
     
    PatientId: 48228
    AppointmentID: 0
    Gender: 110525
    ScheduledDay: 6978
    AppointmentDay: 110500
    Age: 110423
    Neighbourhood: 110446
    Scholarship: 110525
    Hipertension: 110525
    Diabetes: 110525
    Alcoholism: 110525
    Handcap: 110522
    SMS_received: 110525
    No-show: 110525
     
    NUMBER OF ROW LEVEL DUPLICATES:  0


The only duplicate values that might need adjusting would be values in the PatientId column. This will be addressed later.


```python
# Checking for null/missing values in each column. There are no missing values
for i, v in enumerate(df.columns):
    if sum(df[v].isnull()) == 0:
        print(i,v)
```

    0 PatientId
    1 AppointmentID
    2 Gender
    3 ScheduledDay
    4 AppointmentDay
    5 Age
    6 Neighbourhood
    7 Scholarship
    8 Hipertension
    9 Diabetes
    10 Alcoholism
    11 Handcap
    12 SMS_received
    13 No-show


There were no null/missing values found.


```python
# Checking for incorrect data types in each column. Some columns have incorrect data types
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 110527 entries, 0 to 110526
    Data columns (total 14 columns):
     #   Column          Non-Null Count   Dtype  
    ---  ------          --------------   -----  
     0   PatientId       110527 non-null  float64
     1   AppointmentID   110527 non-null  int64  
     2   Gender          110527 non-null  object 
     3   ScheduledDay    110527 non-null  object 
     4   AppointmentDay  110527 non-null  object 
     5   Age             110527 non-null  int64  
     6   Neighbourhood   110527 non-null  object 
     7   Scholarship     110527 non-null  int64  
     8   Hipertension    110527 non-null  int64  
     9   Diabetes        110527 non-null  int64  
     10  Alcoholism      110527 non-null  int64  
     11  Handcap         110527 non-null  int64  
     12  SMS_received    110527 non-null  int64  
     13  No-show         110527 non-null  object 
    dtypes: float64(1), int64(8), object(5)
    memory usage: 11.8+ MB


Some columns have incorrect data types.

From our brief cleanliness check on the common cleanliness factors, we can identify the following issues:
- PatientId (multiple appointments for the same patient): More on this in the Data Cleaning subsection
- PatientId (data type should be object - string specifically)
- AppointmentId (data type should be object - string specifically)
- ScheduledDay (data type should be datetime)
- AppointmentDay (data type should be datetime)
- No-show (values should be 1 and 0 not 'Yes' and 'No'; data type should also be int not object)
- Spelling errors in Hipertension column (should be spelled Hypertension) and Handcap column (should be Handicap)
- Some scheduled dates and/or times seem to be after the Appointment dates and/or times for some patients. Logically this does not make any sense. Hence, we have to decide how to deal with those (more on this in the next subsection).

### Data Cleaning 
In this subsection, we address the cleanliness problems found earlier in an organized manner as shown in the next few cells. Remember the four issues found: Duplicates, Incorrect data types, column spelling errors and a logical error with the dates.

Firstly, let's address incorrect data types.


```python
# Change the data types
df['PatientId'] = df['PatientId'].astype(str)
df['AppointmentID'] = df['AppointmentID'].astype(str)
df['ScheduledDay'] = pd.to_datetime(df['ScheduledDay'])
df['AppointmentDay'] = pd.to_datetime(df['AppointmentDay'])

# Map No-show column from "Yes" and "No" to 1 and 0, then change the data type
df['No-show'] = df['No-show'].map(lambda x: 1 if x == 'Yes' else 0)
df['No-show'] = df['No-show'].astype(int)
df.head(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PatientId</th>
      <th>AppointmentID</th>
      <th>Gender</th>
      <th>ScheduledDay</th>
      <th>AppointmentDay</th>
      <th>Age</th>
      <th>Neighbourhood</th>
      <th>Scholarship</th>
      <th>Hipertension</th>
      <th>Diabetes</th>
      <th>Alcoholism</th>
      <th>Handcap</th>
      <th>SMS_received</th>
      <th>No-show</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>29872499824296.0</td>
      <td>5642903</td>
      <td>F</td>
      <td>2016-04-29 18:38:08+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>62</td>
      <td>JARDIM DA PENHA</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>558997776694438.0</td>
      <td>5642503</td>
      <td>M</td>
      <td>2016-04-29 16:08:27+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>56</td>
      <td>JARDIM DA PENHA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4262962299951.0</td>
      <td>5642549</td>
      <td>F</td>
      <td>2016-04-29 16:19:04+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>62</td>
      <td>MATA DA PRAIA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>867951213174.0</td>
      <td>5642828</td>
      <td>F</td>
      <td>2016-04-29 17:29:31+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>8</td>
      <td>PONTAL DE CAMBURI</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>8841186448183.0</td>
      <td>5642494</td>
      <td>F</td>
      <td>2016-04-29 16:07:23+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>56</td>
      <td>JARDIM DA PENHA</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>95985133231274.0</td>
      <td>5626772</td>
      <td>F</td>
      <td>2016-04-27 08:36:51+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>76</td>
      <td>REPÚBLICA</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>733688164476661.0</td>
      <td>5630279</td>
      <td>F</td>
      <td>2016-04-27 15:05:12+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>23</td>
      <td>GOIABEIRAS</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>7</th>
      <td>3449833394123.0</td>
      <td>5630575</td>
      <td>F</td>
      <td>2016-04-27 15:39:58+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>39</td>
      <td>GOIABEIRAS</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>8</th>
      <td>56394729949972.0</td>
      <td>5638447</td>
      <td>F</td>
      <td>2016-04-29 08:02:16+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>21</td>
      <td>ANDORINHAS</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>78124564369297.0</td>
      <td>5629123</td>
      <td>F</td>
      <td>2016-04-27 12:48:25+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>19</td>
      <td>CONQUISTA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



In the cell above, we changed the data types for the PatientId, AppointmentID, ScheduledDay, AppointmentDay and No-show columns. We also changed the values in the No-show column from categorical to numerical values, while still retaining it's meaning.

Let's check the data types using df.info() to make sure we got the changes we wanted.


```python
# Check datatypes
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 110527 entries, 0 to 110526
    Data columns (total 14 columns):
     #   Column          Non-Null Count   Dtype              
    ---  ------          --------------   -----              
     0   PatientId       110527 non-null  object             
     1   AppointmentID   110527 non-null  object             
     2   Gender          110527 non-null  object             
     3   ScheduledDay    110527 non-null  datetime64[ns, UTC]
     4   AppointmentDay  110527 non-null  datetime64[ns, UTC]
     5   Age             110527 non-null  int64              
     6   Neighbourhood   110527 non-null  object             
     7   Scholarship     110527 non-null  int64              
     8   Hipertension    110527 non-null  int64              
     9   Diabetes        110527 non-null  int64              
     10  Alcoholism      110527 non-null  int64              
     11  Handcap         110527 non-null  int64              
     12  SMS_received    110527 non-null  int64              
     13  No-show         110527 non-null  int64              
    dtypes: datetime64[ns, UTC](2), int64(8), object(4)
    memory usage: 11.8+ MB


Looks good to me! Next, let's address duplicates by taking a closer look at the PatientId column. We can take 2 samples where this column is duplicated an examine the other columns for each duplicated row.


```python
df[df['PatientId'].duplicated()].sample(2)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PatientId</th>
      <th>AppointmentID</th>
      <th>Gender</th>
      <th>ScheduledDay</th>
      <th>AppointmentDay</th>
      <th>Age</th>
      <th>Neighbourhood</th>
      <th>Scholarship</th>
      <th>Hipertension</th>
      <th>Diabetes</th>
      <th>Alcoholism</th>
      <th>Handcap</th>
      <th>SMS_received</th>
      <th>No-show</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39827</th>
      <td>62512452985942.0</td>
      <td>5709233</td>
      <td>F</td>
      <td>2016-05-17 13:20:13+00:00</td>
      <td>2016-05-20 00:00:00+00:00</td>
      <td>26</td>
      <td>PRAIA DO SUÁ</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>100591</th>
      <td>78516287729263.0</td>
      <td>5755307</td>
      <td>F</td>
      <td>2016-06-01 07:16:30+00:00</td>
      <td>2016-06-08 00:00:00+00:00</td>
      <td>14</td>
      <td>REDENÇÃO</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
df[df['PatientId'] == '3725179745141.0']
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PatientId</th>
      <th>AppointmentID</th>
      <th>Gender</th>
      <th>ScheduledDay</th>
      <th>AppointmentDay</th>
      <th>Age</th>
      <th>Neighbourhood</th>
      <th>Scholarship</th>
      <th>Hipertension</th>
      <th>Diabetes</th>
      <th>Alcoholism</th>
      <th>Handcap</th>
      <th>SMS_received</th>
      <th>No-show</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>14494</th>
      <td>3725179745141.0</td>
      <td>5581655</td>
      <td>F</td>
      <td>2016-04-14 08:24:05+00:00</td>
      <td>2016-05-24 00:00:00+00:00</td>
      <td>47</td>
      <td>BENTO FERREIRA</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>27506</th>
      <td>3725179745141.0</td>
      <td>5226266</td>
      <td>F</td>
      <td>2016-01-07 09:49:06+00:00</td>
      <td>2016-05-19 00:00:00+00:00</td>
      <td>47</td>
      <td>BENTO FERREIRA</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>27727</th>
      <td>3725179745141.0</td>
      <td>5557144</td>
      <td>F</td>
      <td>2016-04-07 13:55:00+00:00</td>
      <td>2016-05-03 00:00:00+00:00</td>
      <td>47</td>
      <td>BENTO FERREIRA</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>35202</th>
      <td>3725179745141.0</td>
      <td>5651053</td>
      <td>F</td>
      <td>2016-05-03 08:22:17+00:00</td>
      <td>2016-05-16 00:00:00+00:00</td>
      <td>47</td>
      <td>BENTO FERREIRA</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
df[df['PatientId'] == '99976271368587.0']
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PatientId</th>
      <th>AppointmentID</th>
      <th>Gender</th>
      <th>ScheduledDay</th>
      <th>AppointmentDay</th>
      <th>Age</th>
      <th>Neighbourhood</th>
      <th>Scholarship</th>
      <th>Hipertension</th>
      <th>Diabetes</th>
      <th>Alcoholism</th>
      <th>Handcap</th>
      <th>SMS_received</th>
      <th>No-show</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>57601</th>
      <td>99976271368587.0</td>
      <td>5683648</td>
      <td>M</td>
      <td>2016-05-11 07:44:08+00:00</td>
      <td>2016-05-11 00:00:00+00:00</td>
      <td>16</td>
      <td>NOVA PALESTINA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>94046</th>
      <td>99976271368587.0</td>
      <td>5756332</td>
      <td>M</td>
      <td>2016-06-01 08:12:18+00:00</td>
      <td>2016-06-01 00:00:00+00:00</td>
      <td>16</td>
      <td>NOVA PALESTINA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



From our examination, we can see that there isn't much of a difference between the duplicated rows. The differences we see are in the ScheduledDay, AppointmentDay and SMS_received columns. 

Since these differences aren't too significant to the dependent variable 'No-show' (remains unchanged) and too maintain fairness for our analysis, we opt to drop all rows with duplicate PatientId values. This will ensure that every patient entry is unique.


```python
# Drop rows with PatientId duplicates and confirm
df.drop_duplicates(subset = 'PatientId', inplace = True)
print('PatientId duplicates: ',sum(df['PatientId'].duplicated()))
```

    PatientId duplicates:  0


Note: The action in the cell above should have reduced the number of rows in our dataset by 48228. So, the new number of rows should be 62299


```python
# Check for number of rows
print('Number of Rows: ',df.shape[0])
```

    Number of Rows:  62299


Next, we address the incorrectly spelled column names, after which we drop rows that have Scheduled days and/or times entered as after Appointment days and/or times. We then check these two corrections using df.head()


```python
# Replace the incorrectly spelled column names
df.rename(columns= {'Hipertension': 'Hypertension', 'Handcap': 'Handicap'}, inplace = True)
```


```python
# Drop the columns with scheduled dates and/or times showing as after appointment dates and/or times
df_discard = df[df['ScheduledDay'] > df['AppointmentDay']]
df = df.drop(df_discard.index)
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PatientId</th>
      <th>AppointmentID</th>
      <th>Gender</th>
      <th>ScheduledDay</th>
      <th>AppointmentDay</th>
      <th>Age</th>
      <th>Neighbourhood</th>
      <th>Scholarship</th>
      <th>Hypertension</th>
      <th>Diabetes</th>
      <th>Alcoholism</th>
      <th>Handicap</th>
      <th>SMS_received</th>
      <th>No-show</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5</th>
      <td>95985133231274.0</td>
      <td>5626772</td>
      <td>F</td>
      <td>2016-04-27 08:36:51+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>76</td>
      <td>REPÚBLICA</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>733688164476661.0</td>
      <td>5630279</td>
      <td>F</td>
      <td>2016-04-27 15:05:12+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>23</td>
      <td>GOIABEIRAS</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>7</th>
      <td>3449833394123.0</td>
      <td>5630575</td>
      <td>F</td>
      <td>2016-04-27 15:39:58+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>39</td>
      <td>GOIABEIRAS</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>9</th>
      <td>78124564369297.0</td>
      <td>5629123</td>
      <td>F</td>
      <td>2016-04-27 12:48:25+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>19</td>
      <td>CONQUISTA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>734536231958495.0</td>
      <td>5630213</td>
      <td>F</td>
      <td>2016-04-27 14:58:11+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>30</td>
      <td>NOVA PALESTINA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



Next, we renumber the indexes for organizational purposes. Then, we do another check to make sure that worked.


```python
# Renumber indexes
df = df.reset_index(drop = True)
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PatientId</th>
      <th>AppointmentID</th>
      <th>Gender</th>
      <th>ScheduledDay</th>
      <th>AppointmentDay</th>
      <th>Age</th>
      <th>Neighbourhood</th>
      <th>Scholarship</th>
      <th>Hypertension</th>
      <th>Diabetes</th>
      <th>Alcoholism</th>
      <th>Handicap</th>
      <th>SMS_received</th>
      <th>No-show</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>95985133231274.0</td>
      <td>5626772</td>
      <td>F</td>
      <td>2016-04-27 08:36:51+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>76</td>
      <td>REPÚBLICA</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>733688164476661.0</td>
      <td>5630279</td>
      <td>F</td>
      <td>2016-04-27 15:05:12+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>23</td>
      <td>GOIABEIRAS</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3449833394123.0</td>
      <td>5630575</td>
      <td>F</td>
      <td>2016-04-27 15:39:58+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>39</td>
      <td>GOIABEIRAS</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>78124564369297.0</td>
      <td>5629123</td>
      <td>F</td>
      <td>2016-04-27 12:48:25+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>19</td>
      <td>CONQUISTA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>734536231958495.0</td>
      <td>5630213</td>
      <td>F</td>
      <td>2016-04-27 14:58:11+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>30</td>
      <td>NOVA PALESTINA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



It worked! Next let's check info to verify that everything is good.


```python
# Verify cleanliness
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 40756 entries, 0 to 40755
    Data columns (total 14 columns):
     #   Column          Non-Null Count  Dtype              
    ---  ------          --------------  -----              
     0   PatientId       40756 non-null  object             
     1   AppointmentID   40756 non-null  object             
     2   Gender          40756 non-null  object             
     3   ScheduledDay    40756 non-null  datetime64[ns, UTC]
     4   AppointmentDay  40756 non-null  datetime64[ns, UTC]
     5   Age             40756 non-null  int64              
     6   Neighbourhood   40756 non-null  object             
     7   Scholarship     40756 non-null  int64              
     8   Hypertension    40756 non-null  int64              
     9   Diabetes        40756 non-null  int64              
     10  Alcoholism      40756 non-null  int64              
     11  Handicap        40756 non-null  int64              
     12  SMS_received    40756 non-null  int64              
     13  No-show         40756 non-null  int64              
    dtypes: datetime64[ns, UTC](2), int64(8), object(4)
    memory usage: 4.4+ MB


Our final, cleaned dataset now has 40756 entries with unique Patient IDs, correct data types and a logical sequence of Scheduled Dates and Appointment dates. To do a quick visual check. Let's take a random sample of 30 rows to see if we find anything else.


```python
df.sample(30)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PatientId</th>
      <th>AppointmentID</th>
      <th>Gender</th>
      <th>ScheduledDay</th>
      <th>AppointmentDay</th>
      <th>Age</th>
      <th>Neighbourhood</th>
      <th>Scholarship</th>
      <th>Hypertension</th>
      <th>Diabetes</th>
      <th>Alcoholism</th>
      <th>Handicap</th>
      <th>SMS_received</th>
      <th>No-show</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>11384</th>
      <td>77546455559.0</td>
      <td>5618824</td>
      <td>M</td>
      <td>2016-04-26 07:28:37+00:00</td>
      <td>2016-05-20 00:00:00+00:00</td>
      <td>73</td>
      <td>CARATOÍRA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1652</th>
      <td>8363345122194.0</td>
      <td>5634951</td>
      <td>F</td>
      <td>2016-04-28 12:17:35+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>57</td>
      <td>SÃO JOSÉ</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>18588</th>
      <td>868853846963671.0</td>
      <td>5536708</td>
      <td>F</td>
      <td>2016-04-01 09:00:09+00:00</td>
      <td>2016-05-11 00:00:00+00:00</td>
      <td>53</td>
      <td>JOANA D´ARC</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>36578</th>
      <td>48189387745797.0</td>
      <td>5683515</td>
      <td>F</td>
      <td>2016-05-11 07:35:48+00:00</td>
      <td>2016-06-01 00:00:00+00:00</td>
      <td>23</td>
      <td>DO QUADRO</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>23443</th>
      <td>756511948667794.0</td>
      <td>5593416</td>
      <td>F</td>
      <td>2016-04-18 09:16:38+00:00</td>
      <td>2016-05-03 00:00:00+00:00</td>
      <td>26</td>
      <td>CENTRO</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>10969</th>
      <td>54716163642926.0</td>
      <td>5640128</td>
      <td>F</td>
      <td>2016-04-29 10:09:17+00:00</td>
      <td>2016-05-12 00:00:00+00:00</td>
      <td>14</td>
      <td>GURIGICA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2595</th>
      <td>333342651143415.0</td>
      <td>5697073</td>
      <td>F</td>
      <td>2016-05-13 14:53:07+00:00</td>
      <td>2016-05-16 00:00:00+00:00</td>
      <td>47</td>
      <td>REPÚBLICA</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>22361</th>
      <td>61249787964379.0</td>
      <td>5576000</td>
      <td>F</td>
      <td>2016-04-13 07:51:42+00:00</td>
      <td>2016-05-03 00:00:00+00:00</td>
      <td>79</td>
      <td>JARDIM DA PENHA</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>14796</th>
      <td>34674525743675.0</td>
      <td>5638985</td>
      <td>F</td>
      <td>2016-04-29 08:38:21+00:00</td>
      <td>2016-05-03 00:00:00+00:00</td>
      <td>58</td>
      <td>DO MOSCOSO</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>28590</th>
      <td>62985433492282.0</td>
      <td>5607913</td>
      <td>F</td>
      <td>2016-04-20 12:51:23+00:00</td>
      <td>2016-05-31 00:00:00+00:00</td>
      <td>9</td>
      <td>SÃO CRISTÓVÃO</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9132</th>
      <td>6735943441535.0</td>
      <td>5710875</td>
      <td>F</td>
      <td>2016-05-18 06:41:53+00:00</td>
      <td>2016-05-20 00:00:00+00:00</td>
      <td>62</td>
      <td>ILHA DAS CAIEIRAS</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>955</th>
      <td>18494835315599.0</td>
      <td>5590005</td>
      <td>F</td>
      <td>2016-04-15 13:25:20+00:00</td>
      <td>2016-04-29 00:00:00+00:00</td>
      <td>42</td>
      <td>BENTO FERREIRA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3130</th>
      <td>68772965821192.0</td>
      <td>5694814</td>
      <td>F</td>
      <td>2016-05-13 09:01:21+00:00</td>
      <td>2016-05-18 00:00:00+00:00</td>
      <td>6</td>
      <td>SANTO ANTÔNIO</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3785</th>
      <td>67898769152493.0</td>
      <td>5722045</td>
      <td>F</td>
      <td>2016-05-20 07:01:47+00:00</td>
      <td>2016-05-24 00:00:00+00:00</td>
      <td>47</td>
      <td>JARDIM CAMBURI</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>32648</th>
      <td>597916443831136.0</td>
      <td>5651085</td>
      <td>M</td>
      <td>2016-05-03 08:25:01+00:00</td>
      <td>2016-05-24 00:00:00+00:00</td>
      <td>4</td>
      <td>CENTRO</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4995</th>
      <td>1362565939574.0</td>
      <td>5658541</td>
      <td>F</td>
      <td>2016-05-04 11:19:28+00:00</td>
      <td>2016-05-06 00:00:00+00:00</td>
      <td>55</td>
      <td>PRAIA DO CANTO</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>28920</th>
      <td>374329593456597.0</td>
      <td>5714299</td>
      <td>F</td>
      <td>2016-05-18 11:41:53+00:00</td>
      <td>2016-05-24 00:00:00+00:00</td>
      <td>55</td>
      <td>JARDIM CAMBURI</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>35335</th>
      <td>52687715372661.0</td>
      <td>5736347</td>
      <td>M</td>
      <td>2016-05-25 08:13:31+00:00</td>
      <td>2016-06-01 00:00:00+00:00</td>
      <td>5</td>
      <td>CONQUISTA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>9860</th>
      <td>6555869942483.0</td>
      <td>5694284</td>
      <td>M</td>
      <td>2016-05-13 08:26:12+00:00</td>
      <td>2016-05-18 00:00:00+00:00</td>
      <td>41</td>
      <td>SANTO ANDRÉ</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>30759</th>
      <td>198941363787548.0</td>
      <td>5676147</td>
      <td>F</td>
      <td>2016-05-09 15:04:26+00:00</td>
      <td>2016-05-25 00:00:00+00:00</td>
      <td>46</td>
      <td>MARIA ORTIZ</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>25556</th>
      <td>546134239978493.0</td>
      <td>5687676</td>
      <td>F</td>
      <td>2016-05-11 16:10:36+00:00</td>
      <td>2016-05-17 00:00:00+00:00</td>
      <td>15</td>
      <td>SÃO PEDRO</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4914</th>
      <td>4862789637461.0</td>
      <td>5682417</td>
      <td>F</td>
      <td>2016-05-10 16:32:43+00:00</td>
      <td>2016-05-11 00:00:00+00:00</td>
      <td>2</td>
      <td>JESUS DE NAZARETH</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>29525</th>
      <td>117633221617617.0</td>
      <td>5657709</td>
      <td>M</td>
      <td>2016-05-04 09:48:04+00:00</td>
      <td>2016-05-09 00:00:00+00:00</td>
      <td>26</td>
      <td>SANTA LUÍZA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>40565</th>
      <td>54231928275.0</td>
      <td>5755436</td>
      <td>M</td>
      <td>2016-06-01 07:22:41+00:00</td>
      <td>2016-06-03 00:00:00+00:00</td>
      <td>57</td>
      <td>ESTRELINHA</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>18709</th>
      <td>46999636733569.0</td>
      <td>5720637</td>
      <td>F</td>
      <td>2016-05-19 14:14:48+00:00</td>
      <td>2016-05-20 00:00:00+00:00</td>
      <td>37</td>
      <td>CARATOÍRA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>29831</th>
      <td>74888896989985.0</td>
      <td>5656960</td>
      <td>F</td>
      <td>2016-05-04 08:47:34+00:00</td>
      <td>2016-05-19 00:00:00+00:00</td>
      <td>22</td>
      <td>CONSOLAÇÃO</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>26637</th>
      <td>127528842289285.0</td>
      <td>5697617</td>
      <td>F</td>
      <td>2016-05-13 16:47:09+00:00</td>
      <td>2016-05-19 00:00:00+00:00</td>
      <td>13</td>
      <td>SÃO CRISTÓVÃO</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>28241</th>
      <td>89818877559575.0</td>
      <td>5591559</td>
      <td>M</td>
      <td>2016-04-15 19:08:16+00:00</td>
      <td>2016-05-25 00:00:00+00:00</td>
      <td>51</td>
      <td>JARDIM CAMBURI</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>36159</th>
      <td>714979958184865.0</td>
      <td>5722587</td>
      <td>M</td>
      <td>2016-05-20 07:27:27+00:00</td>
      <td>2016-06-06 00:00:00+00:00</td>
      <td>77</td>
      <td>SANTA LÚCIA</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>19609</th>
      <td>861156191865827.0</td>
      <td>5706226</td>
      <td>F</td>
      <td>2016-05-17 08:01:09+00:00</td>
      <td>2016-05-31 00:00:00+00:00</td>
      <td>17</td>
      <td>JABOUR</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



Looks good to me! Of course, data cleaning is an iterative process, and we may not have caught everything on the first try, especially for such a large dataset. But for the purpose of this project's setup, this would do. Now we can move on the the Exploratory Data Analysis phase of the project.

<a id='eda'></a>
## Exploratory Data Analysis

> **Tip**: Now that you've trimmed and cleaned your data, you're ready to move on to exploration. Compute statistics and create visualizations with the goal of addressing the research questions that you posed in the Introduction section. It is recommended that you be systematic with your approach. Look at one variable at a time, and then follow it up by looking at relationships between variables.


### Summary Statistics
Before addressing the questions asked in the Introduction section, let's examine some summary statistics


```python
# Summary statistics
df.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age</th>
      <th>Scholarship</th>
      <th>Hypertension</th>
      <th>Diabetes</th>
      <th>Alcoholism</th>
      <th>Handicap</th>
      <th>SMS_received</th>
      <th>No-show</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>40756.000000</td>
      <td>40756.000000</td>
      <td>40756.000000</td>
      <td>40756.000000</td>
      <td>40756.000000</td>
      <td>40756.000000</td>
      <td>40756.000000</td>
      <td>40756.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>38.378815</td>
      <td>0.087742</td>
      <td>0.208779</td>
      <td>0.073363</td>
      <td>0.023334</td>
      <td>0.019089</td>
      <td>0.500393</td>
      <td>0.281676</td>
    </tr>
    <tr>
      <th>std</th>
      <td>23.329187</td>
      <td>0.282922</td>
      <td>0.406441</td>
      <td>0.260735</td>
      <td>0.150964</td>
      <td>0.151963</td>
      <td>0.500006</td>
      <td>0.449822</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>19.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>39.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>57.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>115.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>4.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



From the summary statistics above, we see that the most prevalent patients that scheduled appointments were Hypertension patients (20%). We also see that about 50% of patients that scheduled appointments received SMS reminders. 28% of people with scheduled appointments did not show up for their appointments. About 9% of patients who scheduled appointments received the Bolsa Família Scholarship.

The gender column is omitted from summary statistics because it is a categorical variable (although we could change it to an integer column by assigning 0 and 1 values for male and female or vice-versa, we opt to keep it as categorical for the purpose of table clarity). The Neighborhood column is also omitted for the same reason.

Let's examine the distribution of each numeric variable visually.


```python
df.hist(figsize = (10,8));
```


![png](output_44_0.png)


From the histograms above, we get a more visual look at eack variable's distribution. This complements the summary statistics table we saw previously.

Moving to the research questions, we will be tackling the following questions in this section:

- How does gender affect number of appointments missed?
- Do patients who receive the Bolsa Família Scholarship tend to show up to more or less appointments than patients who do not?
- How do Hypertension patients tend to respond to medical appointments?
- Do patients who have one or more SMS messages sent to them tend to show up to more or less appointments than patients who do not?

Our dependent variable is 'No-shows'. Each question above addresses how various independent variables could relate with the dependent variable.

#### Note: The above statement does not imply causation.

### plot_bar function
For ease of single bar chart plotting, we write a plot_bar function to be used subsequently.


```python
# Bar chart plot function
def plot_bar(heights, labels, plot_title, x, y):
    loc = list(range(1,len(heights) + 1))
    plt.bar(loc, heights, tick_label = labels)
    plt.title(plot_title)
    plt.xlabel(x)
    plt.ylabel(y)
```

Note: Some cells in this section will output some values. These values are described using print functions. Follow along.

### Research Question 1: 
How does gender affect number of appointments missed?


```python
# Count of males and Females
males_count = len(df[df['Gender'] == 'M'])
females_count = len(df[df['Gender'] == 'F'])
print('Number of Males: ', males_count)
print('Number of Females: ', females_count)
```

    Number of Males:  14113
    Number of Females:  26643



```python
# Appointments missed by males and females
male_missed = len(df[(df['Gender'] == 'M') & (df['No-show'] == 1)])
female_missed = len(df[(df['Gender'] == 'F') & (df['No-show'] == 1)])
print('Males missed: ', male_missed)
print('Females missed: ', female_missed)
```

    Males missed:  4048
    Females missed:  7432



```python
# Proportions
prop_male = round(male_missed/males_count, 2)
prop_female = round(female_missed/females_count, 2)
print('Proportion of males that missed appointments: ', prop_male)
print('Proportion of females that missed appointments: ', prop_female)
```

    Proportion of males that missed appointments:  0.29
    Proportion of females that missed appointments:  0.28



```python
# Plot proportions of males and females missed
plot_title = 'Proportions of Males and Females that missed appointments'
x = 'Gender'
y = 'Proportions'
gender_props = plot_bar([prop_male,prop_female],['Male','Female'], plot_title, x, y)
```


![png](output_54_0.png)


### Research Question 2: 
Do patients who receive the Bolsa Família Scholarship tend to show up to more or less appointments than patients who do not?


```python
# Count of Scholarship and non-scholarship recipients
schol_count = len(df[df['Scholarship'] == 1])
non_schol_count = len(df[df['Scholarship'] == 0])
print('Number of Scholarship Recipients: ', schol_count)
print('Number of Non-Scholarship Recipients: ', non_schol_count)
```

    Number of Scholarship Recipients:  3576
    Number of Non-Scholarship Recipients:  37180



```python
# Appointments missed by scholarship recipients and non-scholarship
schol_missed = len(df[(df['Scholarship'] == 1) & (df['No-show'] == 1)])
no_schol_missed = len(df[(df['Scholarship'] == 0) & (df['No-show'] == 1)])
print('Scholarship Recipients Missed: ', schol_missed)
print('No scholarship Missed: ', no_schol_missed)
```

    Scholarship Recipients Missed:  1232
    No scholarship Missed:  10248



```python
# Proportions
prop_schol = round(schol_missed/schol_count, 2)
prop_no_schol = round(no_schol_missed/non_schol_count, 2)
print('Proportion of scholarship recipients that missed appointments: ', prop_schol)
print('Proportion of people with no scholarships that missed appointments: ', prop_no_schol)
```

    Proportion of scholarship recipients that missed appointments:  0.34
    Proportion of people with no scholarships that missed appointments:  0.28



```python
# Plot of proportions
plot_title = 'Proportions of missed appointments based on Scholarship'
x = 'Scholarship'
y = 'Proportions'
schol_props = plot_bar([prop_schol,prop_no_schol],['Scholarship Received','No Scholarship Received'], plot_title, x, y)

```


![png](output_59_0.png)


### Research Question 3: 
How do Hypertension patients tend to respond to medical appointments?


```python
# Count of Hypertension and non-hypertension patients
hyp_count = len(df[df['Hypertension'] == 1])
non_hyp_count = len(df[df['Hypertension'] == 0])
print('Number of Hypertension Patients: ', hyp_count)
print('Number of Non-Hypertension Patients: ', non_hyp_count)
```

    Number of Hypertension Patients:  8509
    Number of Non-Hypertension Patients:  32247



```python
# Appointments missed by Hypertension and non-hypertension patieents
hyp_missed = len(df[(df['Hypertension'] == 1) & (df['No-show'] == 1)])
non_hyp_missed = len(df[(df['Hypertension'] == 0) & (df['No-show'] == 1)])
print('Hypertension patients Missed: ', hyp_missed)
print('Non-Hypertension patients Missed: ', non_hyp_missed)
```

    Hypertension patients Missed:  1903
    Non-Hypertension patients Missed:  9577



```python
# Proportions
prop_hyp = round(hyp_missed/hyp_count, 2)
prop_no_hyp = round(non_hyp_missed/non_hyp_count, 2)
print('Proportion of Hypertension patients that missed appointments: ', prop_hyp)
print('Proportion of Non-Hypertension patients that missed appointments: ', prop_no_hyp)
```

    Proportion of Hypertension patients that missed appointments:  0.22
    Proportion of Non-Hypertension patients that missed appointments:  0.3



```python
# Plot of proportions
plot_title = 'Proportions of missed appointments based on Hypertension'
x = 'Hypertension'
y = 'Proportions'
alcohol_props = plot_bar([prop_hyp,prop_no_hyp],['Hypertension','No Hypertension'], plot_title, x, y)
```


![png](output_64_0.png)


### Research Question 4: 
Do patients who have one or more SMS messages sent to them tend to show up to more or less appointments than patients who do not?


```python
# Count of SMS Received and No SMS Received
sms_count = len(df[df['SMS_received'] == 1])
no_sms_count = len(df[df['SMS_received'] == 0])
print('Number of patients who received SMS: ', sms_count)
print('Number of patients who did not receive SMS: ', no_sms_count)
```

    Number of patients who received SMS:  20394
    Number of patients who did not receive SMS:  20362



```python
# Appointments missed by sms recipients and no sms 
sms_missed = len(df[(df['SMS_received'] == 1) & (df['No-show'] == 1)])
no_sms_missed = len(df[(df['SMS_received'] == 0) & (df['No-show'] == 1)])
print('SMS Recipients Missed: ', sms_missed)
print('No SMS received Missed: ', no_sms_missed)
```

    SMS Recipients Missed:  5491
    No SMS received Missed:  5989



```python
# Proportions
prop_sms = round(sms_missed/sms_count, 2)
prop_no_sms = round(no_sms_missed/no_sms_count, 2)
print('Proportion of SMS Recipients that missed appointments: ', prop_sms)
print('Proportion of patients with No SMS received that missed appointments: ', prop_no_sms)
```

    Proportion of SMS Recipients that missed appointments:  0.27
    Proportion of patients with No SMS received that missed appointments:  0.29



```python
# Plot of proportions
plot_title = 'Proportions of missed appointments based on SMS Messages'
x = 'SMS Messages'
y = 'Proportions'
alcohol_props = plot_bar([prop_sms,prop_no_sms],['SMS Received','No SMS Received'], plot_title, x, y)
```


![png](output_69_0.png)


### Research Question 5:
How does Scholarship affect male and female missed appointment rates?


```python
# Males and Females with Scholarship
males_scholarship = len(df[(df['Gender'] == 'M') & (df['Scholarship'] == 1)])
females_scholarship = len(df[(df['Gender'] == 'F') & (df['Scholarship'] == 1)])
print('Males with Scholarship: ', males_scholarship)
print('Females with Scholarship: ', females_scholarship)
```

    Males with Scholarship:  643
    Females with Scholarship:  2933



```python
# Males and Females with Scholarship and missed appointments
males_schol_missed = len(df[(df['Gender'] == 'M') & (df['Scholarship'] == 1) & (df['No-show'] == 1)])
females_schol_missed = len(df[(df['Gender'] == 'F') & (df['Scholarship'] == 1) & (df['No-show'] == 1)])
print('Males with Scholarship who missed appointments: ', males_schol_missed)
print('Females with Scholarship who missed appointments: ', females_schol_missed)
```

    Males with Scholarship who missed appointments:  221
    Females with Scholarship who missed appointments:  1011



```python
# Males and Females without Scholarship 
males_no_scholarship = len(df[(df['Gender'] == 'M') & (df['Scholarship'] == 0)])
females_no_scholarship = len(df[(df['Gender'] == 'F') & (df['Scholarship'] == 0)])
print('Males without Scholarship: ', males_no_scholarship)
print('Females without Scholarship: ', females_no_scholarship)
```

    Males without Scholarship:  13470
    Females without Scholarship:  23710



```python
# Males and Females without Scholarship and missed appointments
males_no_schol_missed = len(df[(df['Gender'] == 'M') & (df['Scholarship'] == 0) & (df['No-show'] == 1)])
females_no_schol_missed = len(df[(df['Gender'] == 'F') & (df['Scholarship'] == 0) & (df['No-show'] == 1)])
print('Males without Scholarship who missed appointments: ', males_no_schol_missed)
print('Females without Scholarship who missed appointments: ', females_no_schol_missed)
```

    Males without Scholarship who missed appointments:  3827
    Females without Scholarship who missed appointments:  6421



```python
# Proportions of males and females without scholarships who missed appointments
prop_male_no_schol_missed = round(males_no_schol_missed/males_count, 2)
prop_female_no_schol_missed = round(females_no_schol_missed/females_count, 2)
print('Proportion of Males without Scholarship who missed appointments: ', prop_male_no_schol_missed)
print('Proportion of Females without Scholarship who missed appointments: ', prop_female_no_schol_missed)
```

    Proportion of Males without Scholarship who missed appointments:  0.27
    Proportion of Females without Scholarship who missed appointments:  0.24



```python
# Proportions of males and females with scholarship who missed appointments
prop_male_schol_missed = round(males_schol_missed/males_count, 2)
prop_female_schol_missed = round(females_schol_missed/females_count, 2)
print('Proportion of Males with Scholarship who missed appointments: ', prop_male_schol_missed)
print('Proportion of Females with Scholarship who missed appointments: ', prop_female_schol_missed)
```

    Proportion of Males with Scholarship who missed appointments:  0.02
    Proportion of Females with Scholarship who missed appointments:  0.04



```python
# Plot proportions
sns.set_style("darkgrid")
ind = np.array([1, 2])
width = 0.2
male_proportions = [prop_male_schol_missed, prop_male_no_schol_missed]
female_proportions = [prop_female_schol_missed, prop_female_no_schol_missed]
male_bars = plt.bar(ind, male_proportions, width, color='b', alpha=.7, label='Males')
female_bars = plt.bar(ind + width, female_proportions, width, color='r', alpha=.7, label='Females')

# title and labels
plt.ylabel('Proportions')
plt.xlabel('Missed Appointments')
plt.title('Missed Appointments by Gender Based on Scholarship')
locations = ind + width/2 # xtick locations
labels = ['Scholarship Received', 'No Scholarship Received']  # xtick labels
plt.xticks(locations, labels)

# legend
plt.legend();
```


![png](output_77_0.png)


### Research Question 6:
How do SMS reminders affect the missed appointment rates of Hypertension patients?


```python
# Hypertension Patients and non-Hypertension patients who received SMS Messages
hyp_sms = len(df[(df['Hypertension'] == 1) & (df['SMS_received'] == 1)])
non_hyp_sms = len(df[(df['Hypertension'] == 0) & (df['SMS_received'] == 1)])
print('Hypertension Patients who received SMS: ', hyp_sms)
print('Non-Hypertension Patients who received SMS: ', non_hyp_sms)
```

    Hypertension Patients who received SMS:  3924
    Non-Hypertension Patients who received SMS:  16470



```python
# Hypertension Patients and non-Hypertension patients who received SMS but missed appointments
hyp_sms_missed = len(df[(df['Hypertension'] == 1) & (df['SMS_received'] == 1) & (df['No-show'] == 1)])
non_hyp_sms_missed = len(df[(df['Hypertension'] == 0) & (df['SMS_received'] == 1) & (df['No-show'] == 1)])
print('Hypertension Patients who received SMS but missed appointments: ', hyp_sms_missed)
print('Non-Hypertension Patients who received SMS but missed appointments: ', non_hyp_sms_missed)
```

    Hypertension Patients who received SMS but missed appointments:  790
    Non-Hypertension Patients who received SMS but missed appointments:  4701



```python
# Hypertension Patients and non-Hypertension patients who did not receive SMS and missed appointments
hyp_missed_no_sms = len(df[(df['Hypertension'] == 1) & (df['SMS_received'] == 0) & (df['No-show'] == 1)])
non_hyp_missed_no_sms = len(df[(df['Hypertension'] == 0) & (df['SMS_received'] == 0) & (df['No-show'] == 1)])
print('Hypertension Patients who did not receive SMS and missed appointments: ', hyp_missed_no_sms)
print('Non-Hypertension Patients who did not receive SMS and missed appointments: ', non_hyp_missed_no_sms)
```

    Hypertension Patients who did not receive SMS and missed appointments:  1113
    Non-Hypertension Patients who did not receive SMS and missed appointments:  4876



```python
# Proportions of Hypertension and non-Hypertension Patients who did not receive SMS and missed appointments
prop_hyp_missed_no_sms = round(hyp_missed_no_sms/hyp_count, 2)
prop_non_hyp_missed_no_sms = round(non_hyp_missed_no_sms/non_hyp_count, 2)
print('Proportion of Hypertension Patients who did not receive SMS and missed appointments: ', prop_hyp_missed_no_sms)
print('Proportion of Non-Hypertension Patients who did not receive SMS and missed appointments: ', prop_non_hyp_missed_no_sms)

```

    Proportion of Hypertension Patients who did not receive SMS and missed appointments:  0.13
    Proportion of Non-Hypertension Patients who did not receive SMS and missed appointments:  0.15



```python
# Proportions of Hypertension and non-Hypertension Patients who received SMS but missed appointments
prop_hyp_missed_sms = round(hyp_sms_missed/hyp_count, 2)
prop_non_hyp_missed_sms = round(non_hyp_sms_missed/non_hyp_count, 2)
print('Proportion of Hypertension Patients who received SMS but missed appointments: ', prop_hyp_missed_sms)
print('Proportion of Non-Hypertension Patients who received SMS but missed appointments: ', prop_non_hyp_missed_sms)
```

    Proportion of Hypertension Patients who received SMS but missed appointments:  0.09
    Proportion of Non-Hypertension Patients who received SMS but missed appointments:  0.15



```python
# plot bars
ind = np.array([1, 2])
width = 0.2
hyp_proportions = [prop_hyp_missed_sms, prop_hyp_missed_no_sms]
non_hyp_proportions = [prop_non_hyp_missed_sms, prop_non_hyp_missed_no_sms]
hyp_bars = plt.bar(ind, hyp_proportions, width, color='b', alpha=.7, label='Hypertension')
non_hyp_bars = plt.bar(ind + width, non_hyp_proportions, width, color='r', alpha=.7, label='No Hypertension')

# title and labels
plt.ylabel('Proportion')
plt.xlabel('Missed appointments')
plt.title('Missed Appointments by Hypertension patients based on SMS Reminders')
locations = ind + width/2 # xtick locations
labels = ['SMS', 'No SMS']  # xtick labels
plt.xticks(locations, labels)

# legend
plt.legend();
```


![png](output_84_0.png)


<a id='conclusions'></a>
## Conclusions


Six questions where asked and addressed in this project with four independent variables and one dependent variable examined.

We see from the dataset that more males tended to miss appointments than females, though the reverse is the case for the males and females who received the Bolsa Família Scholarship. We also see that there was a higher proportion of both males and females missing appointments when they did not receive the Scholarship. However, when Scholarship is isolated, we see something interesting. On average, Scholarship recipients missed more appointments than those who did not receive scholarships. Given the purpose of the scholarship, this is unexpected. However, it is important to make it clear that this analysis is by no means absolute, and further statistical tests will be required to prove this.

From the dataset, we see that non-hypertension patients missed more appointments than hypertension patients. We see the same trend when SMS reminders are sent. However, on average, the SMS reminders did not seem to affect the non-hypertension patients' missed appointment rate as it stayed the same. We do see that on average, less patients with hypertension missed appointments when SMS reminders were sent. Isolating SMS reminders, we see the same trend.

It is important to reiterate that this exploratory data analysis doesn't give the full picture, hence the term "exploratory". All that was done here was examining trends seen between different variables. Therefore, we cannot state with any certainty that because we observed some trends here, they would be similar in other datasets. To make such predictions with some level of certainty, more complex statistical analysis such as Hypothesis testing and regression (amongst others) would need to be carried out.
