# HR Analytics Job Change of Data Scientists
Intorduction
Context and Content

A company which is active in Big Data and Data Science wants to hire data scientists among people who successfully pass some courses which conduct by the company.

Many people signup for their training. Company wants to know which of these candidates are really wants to work for the company after training or looking for a new employment because it helps to reduce the cost and time as well as the quality of training or planning the courses and categorization of candidates.

Information related to demographics, education, experience are in hands from candidates signup and enrollment.

## 1. Data Sources
### 1.1 Enrollies' data
As enrollies are submitting their request to join the course via Google Forms, we have the Google Sheet that stores data about enrolled students, containing the following columns:

enrollee_id: unique ID of an enrollee
full_name: full name of an enrollee
city: the name of an enrollie's city
gender: gender of an enrollee
**Source:** https://docs.google.com/spreadsheets/d/1VCkHwBjJGRJ21asd9pxW4_0z2PWuKhbLR3gUHm-p4GI/edit?usp=sharing
### 1.2  Enrollies' education
After enrollment everyone should fill the form about their education level. This form is being digitalized manually.
The file enrollies_education.xlsx has been provided in this project. Please feel free to download and use it as needed.
### 1.3 Enrollies' working experience
Another survey that is being collected manually by educational department is about working experience.
The work_experience.xlsx file is also included in this project. You can download it here.

### 1.4 Training Hours

This dataset records the total number of hours each student has spent on training programs, collected directly from the LMS system's MySQL database.  
It serves as a key indicator to measure learning engagement and evaluate the effectiveness of training initiatives across the organization.

| Parameter        | Value              |
|------------------|--------------------|
| **Database type** | MySQL              |
| **Host**          | `112.213.86.31`    |
| **Port**          | `3360`             |
| **Login**         | `etl_practice`     |
| **Password**      | `550814`           |
| **Database name** | `company_course`   |
| **Table name**    | `training_hours`   |

### 1.5 City development index
Measures the development level of cities — useful in predicting employment motivation.  

**Source:** [City Development Index](https://sca-programming-school.github.io/city_development_index/index.html)

### 1.6 Employment
Retrieved from **LMS MySQL database** — marks whether a student was employed after training.  

**Database Credentials:**
- Type: MySQL  
- Host: `112.213.86.31`  
- Port: `3360`  
- Login: `etl_practice`  
- Password: `550814`  
- Database: `company_course`  
- Table: `employment`

## Import Libraries
import pandas as pd
!pip install pymysql
from sqlalchemy import create_engine
import pymysql

## 2. Extract Data
### 2.1 Enrollies' data
google_sheet_id = '1VCkHwBjJGRJ21asd9pxW4_0z2PWuKhbLR3gUHm-p4GI'
url = 'https://docs.google.com/spreadsheets/d/' + google_sheet_id + '/export?format=xlsx'
enrollies_data = pd.read_excel(url, sheet_name='enrollies')

see random 5 row of the table:
enrollies_data.head()

| enrollee_id | full_name      | city     | gender |
|-------------|---------------|----------|--------|
| 8949        | Mike Jones    | city_103 | Male   |
| 29725       | Laura Jones   | city_40  | Male   |
| 11561       | David Miller  | city_21  | NaN    |
| 33241       | Laura Davis   | city_115 | NaN    |
| 666         | Alex Martinez | city_162 | Male   |


### 2.2 Enrollies' education
import requests
excel_url = 'https://drive.google.com/drive/u/0/folders/1CIwCOHtn1ZYRb2BhC-UtmCToSykpkuhw'
excel_response = requests.get(excel_url)
with open('enrollies_education.xlsx', 'wb') as file:
    file.write(excel_response.content)

enrollies_education.head()
| enrollee_id | enrolled_university      | education_level     | major_discipline |
|-------------|--------------------------|---------------------|------------------|
| 8949        | no_enrollment            | Graduate	           | STEM             |
|29725		  | no_enrollment            | Graduate            | STEM             |
| 11561		  | Full time course         | Graduate            | STEM             |
| 33241		  | NaN                      | Graduate            | Business Degree  |
| 666	      | no_enrollmentz           | Masters             |STEM              |

### 2.3 Enrollies' working experience
import requests
csv_url = 'https://drive.google.com/drive/u/0/folders/1CIwCOHtn1ZYRb2BhC-UtmCToSykpkuhw'
csv_response = requests.get(csv_url)
with open('work_experience.csv', 'wb') as file:
    file.write(csv_response.content)

work_experience.head()
| enrollee_id | relevent_experience     | experience | company_size | company_type   | last_new_job |
|-------------|------------------------|------------|--------------|----------------|--------------|
| 8949        | Has relevent experience | >20        | NaN          | NaN            | 1            |
| 29725       | No relevent experience  | 15         | 50-99        | Pvt Ltd        | >4           |
| 11561       | No relevent experience  | 5          | NaN          | NaN            | never        |
| 33241       | No relevent experience  | <1         | NaN          | Pvt Ltd        | never        |
| 666         | Has relevent experience | >20        | 50-99        | Funded Startup | 4            |

### 2.4 Training Hours
enegine = create_engine('mysql+pymysql://etl_practice:550814@112.213.86.31:3360/company_course')
enegine = create_engine('mysql+pymysql://etl_practice:550814@112.213.86.31:3360/company_course')
training_hours = pd.read_sql_table('training_hours', enegine)

training_hours.head()

|enrollee_id |	training_hours|
|------------|----------------|
|	8949	 |        36      |
|	29725	 |        47      |
|	11561	 |        83      |
|	33241	 |        52      |
|	666	     |         8      |

### 2.5 City development index

tables = pd.read_html('https://sca-programming-school.github.io/city_development_index/index.html')
city = tables[0]

city.head()

|City	        |City Development Index |
|---------------|-----------------------|
|	city_103	|        0.920          |
|	city_40	    |        0.776          |
|	city_21	    |        0.624          |
|	city_115	|        0.789          |
|	city_162	|        0.767          |


### 2.6 Employment
employment = pd.read_sql_table('employment', enegine)

employment.head()

|enrollee_id	|employed  |
|---------------|----------|
|	1	        |    0.0   |
|	2	        |    1.0   |
|	4	        |    0.0   |
|	5	        |    0.0   |    
|	7	        |    0.0   |


## 3. Tranform Data

### 3.1 Enrollies' data
enrollies_data.info()
View version information.

| **#** | **Column**     | **Non-Null Count** | **Dtype** |
|-------|----------------|--------------------|-----------|
| 0     | enrollee_id    | 19158 non-null     | int64     |
| 1     | full_name      | 19158 non-null     | object    |
| 2     | city           | 19158 non-null     | object    |
| 3     | gender         | 14650 non-null     | object    |

#### 3.1.1 Fix data type
Based on the table information, we can see there is a total of 19,158 entries. There are 4 columns: enrollee_id, full_name, city, and gender. Among them, the full_name and gender columns currently have the wrong data type. I have changed the data type using the following command:
enrollies_data['full_name'] = enrollies_data['full_name'].astype('string')
enrollies_data['gender'] = enrollies_data['gender'].astype('string')
Changed from type object to string.
Changed Column city from type object to category:
enrollies_data['city'] = enrollies_data['city'].astype('category')




#### 3.1.2 Fill missing values
The gender column has only 14,650 non-null values out of a total of 19,158, so I have filled all missing values with the mode value.

gender_mode = enrollies_data['gender'].mode()[0]
enrollies_data['gender'].fillna(gender_mode, inplace=True)

Check the updated results.
| #  | Column       | Non-Null Count | Dtype    |
|----|--------------|----------------|----------|
| 0  | enrollee_id  | 19158 non-null | int64    |
| 1  | full_name    | 19158 non-null | string   |
| 2  | city         | 19158 non-null | category |
| 3  | gender       | 19158 non-null | string   |

### 3.2 Enrollies' education
Similarly, as above, I also used the info command to view all the table information.
enrollies_education.info()
| #  | Column               | Non-Null Count | Dtype  |
|----|----------------------|----------------|--------|
| 0  | enrollee_id          | 19158 non-null | int64  |
| 1  | enrolled_university  | 18772 non-null | object |
| 2  | education_level      | 18698 non-null | object |
| 3  | major_discipline     | 16345 non-null | object |

#### 3.2.1 Fix data type
This table has 4 columns: enrollee_id, enrolled_university, education_level, and major_discipline, with a total of 19,158 entries. Among them, 3 columns — enrolled_university, education_level, and major_discipline — have the data type object.
Use the command to convert the data type.
enrollies_education['enrolled_university'] = enrollies_education['enrolled_university'].astype('string')
enrollies_education['education_level'] = enrollies_education['education_level'].astype('string')
enrollies_education['major_discipline'] = enrollies_education['major_discipline'].astype('string')

#### Fill missing values
Similar to above, I used the mode value to fill the missing values.
enrolled_university_mode = enrollies_education['enrolled_university'].mode()[0]
enrollies_education['enrolled_university'].fillna(enrolled_university_mode, inplace=True)

education_level_mode = enrollies_education['education_level'].mode()[0]
enrollies_education['education_level'].fillna(education_level_mode, inplace=True)

major_discipline_mode = enrollies_education['major_discipline'].mode()[0]
enrollies_education['major_discipline'].fillna(major_discipline_mode, inplace=True)

Check the updated results.
| # | Column               | Non-Null Count | Dtype  |
|---|----------------------|----------------|--------|
| 0 | enrollee_id          | 19158          | int64  |
| 1 | enrolled_university  | 19158          | string |
| 2 | education_level      | 19158          | string |
| 3 | major_discipline     | 19158          | string |



### 3.3 Enrollies' working experience

work_experience.info()
| #   | Column              | Non-Null Count | Dtype  |
|-----|---------------------|----------------|--------|
| 0   | enrollee_id         | 19158          | int64  |
| 1   | relevent_experience | 19158          | object |
| 2   | experience          | 19158          | string |
| 3   | company_size        | 13220          | object |
| 4   | company_type        | 13018          | object |
| 5   | last_new_job        | 18735          | object |

#### 3.3.1 Fix data type 
In this table, the data type does not need to be converted.

#### 3.3.2 Fill missing values

work_experience_mode = work_experience['experience'].mode()[0]
work_experience['experience'].fillna(work_experience_mode, inplace=True)

Check the updated results.
| #  | Column              | Non-Null Count | Dtype  |
|----|---------------------|----------------|--------|
| 0  | enrollee_id         | 19158 non-null | int64  |
| 1  | relevent_experience | 19158 non-null | object |
| 2  | experience          | 19158 non-null | string |
| 3  | company_size        | 13220 non-null | object |
| 4  | company_type        | 13018 non-null | object |
| 5  | last_new_job        | 18735 non-null | object |


### 3.4 Training Hour

training_hours.info()
| #  | Column         | Non-Null Count | Dtype |
|----|---------------|----------------|-------|
| 0  | enrollee_id   | 19158 non-null | int64 |
| 1  | training_hours| 19158 non-null | int64 |

In this table, there is no data that needs to be transformed.

### 3.5 City Development Index

city.info()
| #  | Column                | Non-Null Count | Dtype   |
|----|-----------------------|----------------|---------|
| 0  | City                  | 123 non-null   | object  |
| 1  | City Development Index| 123 non-null   | float64 |

### 3.6 Employment

employment.info()
| #  | Column                | Non-Null Count | Dtype   |
|----|-----------------------|----------------|---------|
| 0  | enrollee_id           | 19158 non-null | int64   |
| 1  | employed              | 19158 non-null | float64 |

## 4. Load Data


db_path = 'data_warehouse.db'
engine = create_engine(f'sqlite:///{db_path}')

tables = [
    ('dim_enrollies_data', enrollies_data),
    
    ('fact_enrollies_education', enrollies_education),
    
    ('dim_work_experience', work_experience),
    
    ('dim_traning_hours', training_hours),
    
    ('dim_city', city),
    
    ('fact_employment', employment)
]

for table_name, dataframe in tables:
    dataframe.to_sql(table_name, engine, if_exists='replace', index=False)




