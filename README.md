# Study-DataWrangling (Python + R)

 > 1. Gathering
 > 2. Assessing
 > 3. Cleaning

__Data:__ '19000 Armenian_Online_Job_Postings' from Kaggle(https://www.kaggle.com/udacity/armenian-online-job-postings) 
 - The online job market is a good indicator of overall demand for labor in an economy. This dataset consists of 19,000 job postings from 2004 to 2015 posted on CareerCenter, an Armenian human resource portal. Since postings are text documents and tend to have similar structures, text mining can be used to extract features like posting date, job title, company name, job description, salary, and more. Postings that had no structure or were not job-related were removed. The data was originally scraped from a Yahoo! mailing group.
 - The following features were extracted from each job posting, if included:
   - jobpost: The original job post.
   - date: The date it was posted in the group.
   - Title: Job title.
   - Company: Employer name. 
   - AnnouncementCode: Announcement code, which is some internal code and is usually missing.
   - Term: Full-Time, Part-time, etc.
   - Eligibility: Eligibility of the candidates.
   - Audience: Who can apply? 
   - StartDate: Start date of work.
   - Duration: Duration of the employment.
   - Location: Employment location.
   - JobDescription: Job Description.
   - JobRequirment: Job requirements.
   - RequiredQual: Required qualifications.
   - Salary: Job salary.
   - ApplicationP: Application procedure.
   - OpeningDate: Opening date of the job announcement.
   - Deadline: Deadline for the job announcement. 
   - Notes: Additional notes.
   - AboutC: About the company.
   - Attach: Attachments.
   - Year: Year of the announcement (derived from the field date). 
   - Month: Month of the announcement (derived from the field date). 
   - IT: TRUE if the job is an IT job. This variable is created by a simple search of IT job titles within the "Title" column.

### Intro: Gathering example
```
import zipfile
import pandas as pd
```
Extract all contents from zip file ('filename', 'mode' we want)
```
with zipfile.ZipFile('online-job-postings.csv.zip', 'r') as myzip:
    myzip.extractall()
```
Read CSV (comma-separated) file into DataFrame
```
df = pd.read_csv('online-job-postings.csv')
```
<img src="https://user-images.githubusercontent.com/31917400/36513316-ffa98dfc-1766-11e8-8f26-b8fd224eaa19.jpg" />

### Intro: Assessing example
 - It's about detecting the problems searching for Quality and Tidiness.
   - `df.head()`, `df.info()`, `df.describe()`, `df.value_counts()`
 - **Quality** (inconsistency, inaccuracy, NaN) 
   - Check: Missing values (NaN)
   - Check: value inconsistency, accuracy
   - Check: Non-descriptive column headers
 
 - **Tidiness** (clean columns, clean rows)
   - Check: Each variable is a column? Each observation is a row? Each type of observational unit is a table?
   - Check: Duplicated representation of data (ex..'Date' column...'Year' and 'Month' also have their own column ?) 

### Intro: Cleaning example
 - a) Define your plan in writing
 - b) Write your code and test it
 
For example:
 - Using `rename()`, Select all nondescriptive and misspelled column headers(ApplicationP, AboutC, RequiredQual, JobRequirment) and replace them with full words (ApplicationProcedure, AboutCompany,..etc) 
```
df_clean = df.copy()
df_clean = df_clean.rename(columns={'ApplicationP': 'ApplicationProcedure',
                                    'RequiredQual': 'RequiredQualification',
                                    'JobRequirment': 'JobRequirement',
                                    'AboutC': 'AboutCompany'})
df_clean.StartDate.value_counts()
```
 - As you know, 'Series' is the column of pandas' dataframe !
 - Using series`.replace(to_replace, value, inplace)`, Select all records in the 'StartDate' column that have 'As soon as possible', 'immediately', etc and replace the text in those cells with 'ASAP'.
 - Using `assert` and series`.values` returning Series as numpy ndarray, Test your code.
```
asap_list = ['Immediately', 'As soon as possible', 'Upon hiring',
             'Immediate', 'Immediate employment', 'As soon as possible.', 'Immediate job opportunity',
             '"Immediate employment, after passing the interview."',
             'ASAP preferred', 'Employment contract signature date',
             'Immediate employment opportunity', 'Immidiately', 'ASA',
             'Asap', '"The position is open immediately but has a flexible start date depending on the candidates earliest availability."',
             'Immediately upon agreement', '20 November 2014 or ASAP',
             'immediately', 'Immediatelly',
             '"Immediately upon selection or no later than November 15, 2009."',
             'Immediate job opening', 'Immediate hiring', 'Upon selection',
             'As soon as practical', 'Immadiate', 'As soon as posible',
             'Immediately with 2 months probation period',
             '12 November 2012 or ASAP', 'Immediate employment after passing the interview',
             'Immediately/ upon agreement', '01 September 2014 or ASAP',
             'Immediately or as per agreement', 'as soon as possible',
             'As soon as Possible', 'in the nearest future', 'immediate',
             '01 April 2014 or ASAP', 'Immidiatly', 'Urgent',
             'Immediate or earliest possible', 'Immediate hire',
             'Earliest  possible', 'ASAP with 3 months probation period.',
             'Immediate employment opportunity.', 'Immediate employment.',
             'Immidietly', 'Imminent', 'September 2014 or ASAP', 'Imediately']

for i in asap_list:
    df_clean['StartDate'].replace(i, 'ASAP', inplace=True)
    
for i in asap_list:
    assert i not in df_clean.StartDate.values         
```
> Q. What's the percentage of job postings have 'urgent' start date ? 
 - numerator ? df.column.value_counts()[entry]
 - denominater ? df.column.count()
 - numerator / denominater 
 - What about plotting ? 
```
import matplotlib.pyplot as plt
%matplotlib inline

df_clean.StartDate.value_counts().plot(kind='pie')
```
So messy labels..remove them except 'ASAP'
```
import numpy as np
labelsss = np.full(len(df_clean.StartDate.value_counts()), "", dtype=object)
labelsss[0] = 'ASAP'

df_clean.StartDate.value_counts().plot(kind='pie', labels=labelsss)

```
<img src="https://user-images.githubusercontent.com/31917400/36513679-af1f0b80-1768-11e8-9725-631168fb5444.jpg" width="600" height="160" /> 

----------------------------------------------------------------------------------------------------------------------
## 1. Gathering
> Gathering data varies from project to project. Sometimes you're just given data. Sometimes you need to search for the right data for your project. Sometimes the data you need isn't readily available, and you need to generate it yourself somehow. When you do find your data, it's not unusual for it to be spread across several different sources and file formats, which makes things tricky when organizing the data in your programming environment.
 - Learn about the structure of each file format.
 - Learn how to handle that file format using Python and its libraries.
 - Gather each piece of data to later join together to create your master dataset.

#### command prompt
 - HOW to execute commands like changing the working folder, viewing the contents of a directory, creating and renaming folders, copying, deleting files and folders, and launching any application from the Command Prompt ?
 - `cd..`, `dir`, `md`, `ren`
 - copy a file: `copy location\filename.extension newlocation\newname.extension`(If you are copying within the same directory you don't have to put the path in command)
 - copy a folder and its files, use the 'XCOPY' command followed by the “/s /i” operators: 
   - `xcopy /s /i d:\bird c:\Backup_bird`
     - `/s` parameter ensures all the directories and subdirectories will be copied
     - `/i` parameter creates a new directory if the destination folder doesn't exist and will copy all the files.
 - Get rid of a file: `del`
   - `del *.DOC` : delete all files with the DOC extension
   - `del Test*.*` : delete all files beginning with Test;
   - `del *.*` : delete ALL files from the current folder.
 - Get rid of a folder: `rd`
 - Launch an application: navigate to the folder that contains the executable and type the program’s name.
   - for example, `cd /d c:\windows\System32\` Then write program's name and press Enter. 
     - `/d` parameter is used to change the current drive to a specific folder from another disk volume.

#### Flat files
 - CSV, TSV, 
   - (+) They're text files and therefore human readable.
   - (+) Software that can read/write text files is ubiquitous
   - (+) Great for small datasets
   - (-) Not great for large datasets
   - (-) Sharing data can be cumbersome
 - pandas has one main function for parsing flat files and it is `pd.read_csv()`

#### Web Scraping




































































