[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/u8FyG16T)
[![Open in Codespaces](https://classroom.github.com/assets/launch-codespace-2972f46106e565e64193e422d61a12cf1da4916b45550586e14ef0a7c637dd04.svg)](https://classroom.github.com/open-in-codespaces?assignment_repo_id=16676305)
# 1. Initial State Analysis

### Dataset Overview
- **Name**: messy_population_data.csv
- **Rows**: 125718
- **Columns**: income_groups, age,gender,year,population

### Column Details
| Column Name | Data Type | Non-Null Count | Unique Values |  Mean  |
|-------------|-----------|----------------|---------------|--------|
| [income_groups]  | [object] | [119412] | [9] | [NA] |
| [age]  | [float64] | [119495] | [102] | [50.007038] |
| [gender]  | [float64] | [119811] | [4] | [1.578578] |
| [year]  | [float64] | [119516] | [170] | [2025.068049] |
| [population]  | [float64] | [119378] | [114926] | [1.112983e+08] |

### Identified Issues

1. **How come gender has 4 different values?**
   - Description: Gender column has these values: nan,1,2,3. nan is missing data for the gender column but what is 3? Is 3 potentially LGBTQ+
   - Affected Column(s): Gender
   - Example: Gender column has these values: nan,1,2,3
   - Potential Impact: Using this data would give us an inaccurate analysis if we want to draw any conclusions in terms of gender

2. **Missing values in all columns**
   - Description: All columsn having missing values, as seen from the output of the .isnull().sum() command
   - Affected Column(s): gender, age, year, population, income_groups
   - Example: Example income_groups has 6306 missing values
   - Potential Impact: If we analyze this data with these missing values our assesmnet wouldn't be correct because we won't be accounting for all the null values which would skew our data
  
3. **How come age has the the value of 0**
   - Description: Age column has multiple values, that don't make sense. nan has a 0 count but 0 has a 1175 count and age 1 has 1173 count
   - Affected Column(s): age, income_groups
   - Example: How can someone of age 0 have high_income?
   - Potential Impact: Using this data would give us an inaccurate analysis if we want to draw any conclusions in terms of age

4. **How come income_groups have typo values?**
   - Description: the word typo was seen in income_groups by using the .value_counts() command
   - Affected Column(s): income_groups
   - Example: high_income_typo has 1475 counts while high_income has a 28343 count. Hence typos value need to be adjusted in our column
   - Potential Impact: [Using this data would give us an inaccurate analysis if we want to draw any conclusions in terms of income_groups]

5. **Delete duplicated data**
   - Description: The .duplicated.sum()command has the outpit of 2950
   - Affected Column(s): age, income_groups, population, year, gender
   - Example: Data is being duplicated for all of the column types
   - Potential Impact: Using this data would give us an inaccurate analysis if we want to draw any conclusions in terms of any of the columns and it would skew our analysis


## 2. Data Cleaning Process

### Issue 1: How come gender has 4 different values?
- **Cleaning Method**: Removing all gender values that take value 3
- **Implementation**:
  
  ```rawdata = rawdata.drop(rawdata[rawdata["gender"]== 3.0].index)```
- **Justification**: 
  
  `for value in rawdata["gender"].unique():`
    
    `print(value, ":", sum(rawdata["gender"]== value))`
    
    `Cell Output:`

    `1.0 : 56777`

    `3.0 : 6286`

    `nan : 0`

    `2.0 : 56748`
    
    We don't know what the value 3 means in terms of gender so we can't analyze for it
- **Impact**: 
  - Rows affected: 6286
  - Data distribution change: 
  
  `for value in rawdata["gender"].unique():`
    
    `print(value, ":", sum(rawdata["gender"]== value))`

    `Cell Ouput: `

    `1.0 : 56777`

    `nan : 0`

    `2.0 : 56748`

    We removed 6286 values of 3 from our dataset. The mean from the orignal dataset was 1.578578 but the cleaned dataset has the mean of 1.499872  for the gender column. The sd also changed from  
    0.590559 to 0.500002. 

    
    ### Issue 2 : Missing values in all columns
- **Cleaning Method**: Removing all null values
- **Implementation**:
  ```rawdata = rawdata.dropna()```
- **Justification**:

  `rawdata.isnull().sum()`

   `Cell Output:`

   `income_groups    6306`

   `age              6223`

   `gender           5907`

   `year             6202`

   `population       6340`
   
   `dtype: int64`
    
    We need to remove all null values in order to not skew our data assessment
- **Impact**: 
  - Rows affected: 119432- 92512= 26,920 
  - Data distribution change:
  
   `rawdata.isnull().sum()`
   
   ` Cell Ouput: `
    
    `income_groups    0`
    
    `age              0`

    `year             0`

    `population       0`

    `dtype: int64`
    
    We removed all the null values from our dataset. 
 

 ### Issue 3: How come age has the the value of 0 ?
- **Cleaning Method**: Removing ages from 0:10 since they skew our data in terms income_groups
- **Implementation**:

   `for drop_age in [0.0,1.0,2.0,3.0,4.0,5.0,6.0,7.0,8.0,9.0,10.0]:
         rawdata = rawdata.drop(rawdata[rawdata["age"]== drop_age].index)`
- **Justification**:
  
   `rawdata.head()`

   `Cell Output:`

   `# income_groups	age	gender	year	population`

   `0	high_income	0.0	1.0	1950.0	7798286.0`

   `1	high_income	0.0	1.0	1951.0	7739711.0`

   `2	high_income	0.0	3.0	1952.0	7713905.0`

   `3	high_income	0.0	1.0	1953.0	7722053.0`

   `4	high_income	0.0	1.0	1954.0	7756149.0`

    We can't have someone of age 0 have high_income, this data isn't accurate 
- **Impact**: 
  - Rows affected: 92512 - 82450 = 10062
  - Data distribution change: 
  
  `print(rawdata.describe())`

   `Cell Output:`
  
  `                age        gender          year    population`

   `count  82450.00000  82450.000000  82450.000000  8.245000e+04`

   `mean      55.52980      1.501019   2025.108939  9.941250e+07`

   `std       25.97717      0.500002     43.542682  1.162277e+09`
   
   `min       11.00000      1.000000   1950.000000  2.200000e+01`
   
   `25%       33.00000      1.000000   1987.000000  1.774970e+06`
   
   `50%       56.00000      2.000000   2025.000000  6.843176e+06`
   
   `75%       78.00000      2.000000   2063.000000  1.324970e+07`
   
   `max      100.00000      2.000000   2119.000000  3.218242e+10`

   When we removed ages from 0 to 10 from the dataset the mean age changed from 50.034871 to 55.52980. The std changed from 29.153472 to 25.97717. 


### Issue 4: How come income_groups have typo values?
- **Cleaning Method**: Converting all typos to their respective income group
- **Implementation**: 
  
  `typo_correction = {
    "high_income_typo":"high_income", 
    "low_income_typo":"low_income", 
    "lower_middle_income_typo":"lower_middle_income", 
    "upper_middle_income_typo":"upper_middle_income"
}`

`rawdata["income_groups"]=rawdata["income_groups"].replace(typo_correction)`
- **Justification**: 
  
  `for value in rawdata["income_groups"].unique():`
    
    `print(value,":", sum(rawdata["income_groups"]== value))`
    
    `print("percentage : ", sum(rawdata["income_groups"]== value)/82450)`
    
    `Cell Output:`
    
    `high_income : 19594`
   
   `percentage :  0.2376470588235294`
   
   `high_income_typo : 1033`
   
   `percentage :  0.012528805336567616`
   
   `low_income : 19621`
   
   `percentage :  0.23797453001819285`
   
   `low_income_typo : 1050`
   
   `percentage :  0.012734990903577926`
   
   `lower_middle_income : 19569`
   
   `percentage :  0.2373438447543966`
   
   `lower_middle_income_typo : 1038`
   
   `percentage :  0.012589448150394178`
   
   `upper_middle_income : 19539`
   
   `percentage :  0.23697998787143723`
   
   `upper_middle_income_typo : 1006`
   
   `percentage :  0.012201334141904184`


    We have typos in all income categories so we must fix these typos by categorizing the typo to it's actual category
- **Impact**: 
  - Rows affected: 1033+ 1050+1038+1006 = 4127
  - Data distribution change: 
  
  `for value in rawdata["income_groups"].unique():`

    `print(value,":", sum(rawdata["income_groups"]== value))`

    `print("percentage : ", sum(rawdata["income_groups"]== value)/82450)`

    `Cell Output:`
    
    `high_income : 20627`
   
   `percentage :  0.250175864160097`
   
   `low_income : 20671`
   
   `percentage :  0.2507095209217708`
   
   `lower_middle_income : 20607`
   
   `percentage :  0.24993329290479077`
   
   `upper_middle_income : 20545`
   
   `percentage :  0.249181322013341`

    We converted all the typos in the income category. Now we have an almost uniform distribution.


### Issue 5 : Delete duplicated data
- **Cleaning Method**: Removing all duplicated rows
- **Implementation**:
  
  `cleaned_data= rawdata.drop_duplicates().reset_index(drop=True)`
- **Justification**: 
  
  `rawdata.duplicated().sum()`

    `Cell Output:`

    `np.int64(2182)`
    
    We need to remove all duplicated values in order to better analyze our dataset
- **Impact**: 
  - Rows affected: 82450 - 80268 = 2182
  - Data distribution change:
   
   `print(cleaned_data.info())`
   
   `print(cleaned_data.describe())`
   
   `Cell Output:`
   
   `RangeIndex: 80268 entries, 0 to 80267`
   
   `Data columns (total 5 columns):`
   
   `   Column         Non-Null Count  Dtype  `
   
   `---  ------         --------------  -----`
   
   `0   income_groups  80268 non-null  object` 
   
   `1   age            80268 non-null  float64`
   
   `2   gender         80268 non-null  float64`
   
   `3   year           80268 non-null  float64`
   
   `4   population     80268 non-null  float64`
   
   `dtypes: float64(4), object(1)`
   
   `memory usage: 3.1+ MB`
   
   `None`
   
   `               age        gender          year    population`
   
   `count  80268.000000  80268.000000  80268.000000  8.026800e+04`
   
   `mean      55.520282      1.500934   2025.106630  1.018876e+08`
   
   `std       25.975358      0.500002     43.558664  1.177870e+09`
   
   `min       11.000000      1.000000   1950.000000  2.200000e+01`
   
   `25%       33.000000      1.000000   1987.000000  1.781476e+06`
   
   `50%       56.000000      2.000000   2025.000000  6.848320e+06`
   
   `75%       78.000000      2.000000   2063.000000  1.328162e+07`
   
   `max      100.000000      2.000000   2119.000000  3.218242e+10`
    
    We removed all the duplicated rows and our mean and sd changed very little to reflect this enhancement

