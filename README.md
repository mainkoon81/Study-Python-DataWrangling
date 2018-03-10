# Study-DataWrangling (Python + R)

 > 1. Gathering
 > 2. Assessing
 > 3. Cleaning

__command prompt summary__
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
> Gathering data varies from project to project. Sometimes you're just given data. Sometimes you need to search for the right data for your project. Sometimes the data you need isn't readily available, and you need to generate it yourself somehow. When you do find your data, it's not unusual for it to be spread across several different sources and file formats, which makes things tricky when organizing the data in your programming environment.
 - Learn about the structure of each file format.
 - Learn how to handle that file format using Python and its libraries.
 - Gather each piece of data to later join together to create your master dataset.
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
> It's about detecting the problems searching for Quality and Tidiness.
   - `df.head(4)`, `df.sample(4)`, `df.info()`, `df.describe()`, `df['col'].value_counts()`, `df['col'].sort_values()`, `df[condition].loc[]`, `df[df['col'].duplicated()]` `sum(df['col'].isnull())` 
 - **Quality** (content issues: NaN, inaccuracy, inconsistency) 
   - Check: Missing values (NaN)
   - Check: value inconsistency, accuracy
   - Check: Non-descriptive column headers, duplicates
 
 - **Tidiness** (structural issues of col and row: too wide ?? multiple meanings on one table)
   - Check: Each variable is a column? Each observation is a row? Each type of observational unit is a table? Each obv unit should form a individual table! like a relational database ?
   - Check: Duplicated representation of data (ex..'Date' column...'Year' and 'Month' also have their own column ?)
<img src="https://user-images.githubusercontent.com/31917400/36759813-89bb4e56-1c10-11e8-9a5a-85ee07bee9dd.jpg" width="600" height="160" /> 

### Intro: Cleaning example***
> Two things
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
# 1. Gathering

> Flat files
 - CSV, TSV, 
   - (+) They're text files and therefore human readable.
   - (+) Software that can read/write text files is ubiquitous
   - (+) Great for small datasets
   - (-) Not great for large datasets
   - (-) Sharing data can be cumbersome
 - pandas has one main function for parsing flat files and it is `pd.read_csv()`

> Web Scraping
 - BeautifulSoup is HTML parser(soup-constructor) written in Python. 
 - For a file handler and For a soup-constructor
```
import requests 
from bs4 import BeautifulSoup
```
### A. How to access to the HTML ?
 - __Case_1:__ Reading a single HTML file stored in the RAM 

Passing the 'url' to our HTML file into a **file handler** then passing this **file handler**(with adding`.content`) into the 'BeautifulSoup constructor'. And specify the **parser name**.
```
url = 'https://www.rottentomatoes.com/m/et_the_extraterrestrial'
res = requests.get(url) 

soup = BeautifulSoup(res.content, 'lxml'); soup  
```
<img src="https://user-images.githubusercontent.com/31917400/37093783-7b0cca80-2208-11e8-84f5-b34a95e185a6.jpg" width="600" height="120" /> 

 - __Case_2:__ Download a single HTML file and save in our local machine (**where the current ipython file is located**).

Passing the 'url' to our HTML file into a **file handler** then passing this **file handler**(with adding`.content`) into the 'python file writer', matching up this with our made up a **file information**(name, path, etc).
```
url = 'https://www.rottentomatoes.com/m/et_the_extraterrestrial'
res = requests.get(url) 

with open('folder_we_want/et_the_extraterrestrial.html', mode='wb') as f:
    f.write(res.content)
```
Of course we can open the file by clicking it, but we need to open it in our python environment. Then we pass the **file information**(name, path, etc) into the 'BeautifulSoup constructor' with the **parser name**. 
```
with open('folder_we_want/et_the_extraterrestrial.html') as f: 
    soup2 = BeautifulSoup(f, 'lxml'); soup2    
```
<img src="https://user-images.githubusercontent.com/31917400/37093783-7b0cca80-2208-11e8-84f5-b34a95e185a6.jpg" width="600" height="120" /> 

 - __Case_3:__ What if having so many files to download ?

Always start with making a folder first.
```
import os

folder_name = 'ebert_reviews'
if not os.path.exists(folder_name):
    os.makedirs(folder_name)
```
Automatic Downloading and saving...
```    
urls = ['https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9900_1-the-wizard-of-oz-1939-film/1-the-wizard-of-oz-1939-film.txt',
        'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9901_2-citizen-kane/2-citizen-kane.txt',
        'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9901_3-the-third-man/3-the-third-man.txt',
        'https://d17h27t6h515a5.cloudfront.ne.............................................    
                     
for i in urls:
    res = requests.get(i)
    with open(os.path.join(folder_name, i.split('/')[-1]), mode = 'wb')as f: 
        f.write(res.content)                                          
```
Check the size of our folder. How many files did we download ? 
```
len(os.listdir(folder_name))
```
 - Find and extract a certain data from HTML ? Well...`soup.find()`, `soup.find_all()`...need to .. FUXX!! So many..
 - Know all decendents of the HTML file 
```
for i in soup.descendants:
    print(i)
    print('---------------')
```
<img src="https://user-images.githubusercontent.com/31917400/37095375-a7af95a4-220d-11e8-96de-b63240376642.jpg" width="600" height="100" /> 

 - Know all tags of the HTML file
```
for i in soup.find_all(True):
    print(i.name)
```
<img src="https://user-images.githubusercontent.com/31917400/37095379-a9e5e99a-220d-11e8-83e7-9e958b9b4b38.jpg" width="600" height="100" /> 

 - Automatic Generation of a DataFrame
```
df_list = []
folder = 'rt_html' # here we already saved all files in our folder. 
for i in os.listdir(folder):
        # this is where we specify the folder to save our files
    with open(os.path.join(folder, i)) as file:
        soup = BeautifulSoup(file, 'lxml') # parsing each file
        
        title = soup.find('title').contents[0][:-len('- Rotten Tomatoes')]
#        print(title)
        audience_score = soup.find('div', class_='audience-score meter').find('span').contents[0][:-1]
#        print(audience_score)
        num_audience_ratings = soup.find('div', class_='audience-info hidden-xs superPageFontColor')
        num_audience_ratings = num_audience_ratings.find_all('div')[1].contents[2].strip().replace(',','')        
#        print(num_audience_ratings)
        
        # Append to list of dictionaries
        df_list.append({'title': title,
                        'audience_score': int(audience_score),
                        'number_of_audience_ratings': int(num_audience_ratings)})
import pandas as pd

df = pd.DataFrame(df_list, columns = ['title', 'audience_score', 'number_of_audience_ratings'])
```
<img src="https://user-images.githubusercontent.com/31917400/37102722-5e74e70a-2220-11e8-9e69-23b20ce80358.jpg" width="600" height="100" /> 

### B. Using API and the [Access Library*]
When do we hit the API? image data ? Screen Scraping ? 

The API of each website provides 'access' to only 'certain information' allowed by the website. For example, by implementing the API of the Rotten Tomatoes, we can get 'critic,audience score', 'critic reviews'. And Rotten Tomatoes' access library(or client library) is called `rtsimple`.

There are a bunch of different **'Access Libraries'** for 'MediaWiki' to satisfy the variety of programming languages that exist.
 - Wikipedia-API : Easy to use Python 3 library. 
 - **wptools** : Wikipedia tools (for Humans).
 - Pywikibot : A collection of python scripts and a powerful library for bot writing.
 - wikitools : Provides several layers of abstraction around the API. Doesn't support Python 3.
 - mwclient : A Python library that makes most of the API functions accessible....
 - etc....

### Human Readable.. ?
For a MediaWiki, the most up to date and [human readable] one in Python is called `wptools`. For example, the analogous relationship for Twitter is:
 - MediaWiki API → wptools
 - Twitter API → tweepy
 - wptools guideline: (https://github.com/siznax/wptools) 
```
import wptools
```
### JSON and Hierarchical data
Most data from APIs comes in **Json** or **XML** format. In many situation, we are limited in what we can represent in tabular data. Sometimes we have data with fields that have multiple entries (human oriented dataset: wide format). Json is great for accessing these complicated data hierarchies(serialized data). Json is built on 2 key structures.

 - Dictionary: Json object(a collection of key value pairs) `{..dictionary within dictionary..}`
 - List: Json array(an ordered list of values) `[...vector, sequence...]`

**Read and Write JSON:** So far, JSON data was sourced from an API. That isn't always the case, though! Sometimes you're given a text file with human readable JSON within it. For this situation, the **'json library'** is indispensable. It can parse 'JSON' from strings or files and it can parse 'JSON' into a Python dictionary or list. It can also convert Python dictionaries or lists into 'JSON strings'.

 - **Writing JSON** to a File: transforming Python dict object in to the serialized JSON string.
   - `json.dump()`: It writes the dict object to the 'text file' in JSON format.
   - `json.dumps()`: A slight variation on the `json.dump()`. It returns the actual JSON string and gives some more control in Json str.
```
import json

data = {}  
data['people'] = []  
data['people'].append({'name': 'Scott', 'website': 'stackabuse.com', 'from': 'Nebraska'})
data['people'].append({'name': 'Larry', 'website': 'google.com', 'from': 'Michigan'})
data['people'].append({'name': 'Tim', 'website': 'apple.com', 'from': 'Alabama'})

with open('data.txt', 'w') as out_f:  
    json.dump(data, out_f)
```
 - **Reading JSON** from a file: 
   - `json.load()`: It reads the string from the file, parses the JSON data, populates a Python dict with the data and returns it back.
   - `json.loads()`: A slight variation on the `json.load()`. It lets us deal with str directly(since many times you probably won't have a file-like object that contains your JSON). 
```
with open('data.txt') as json_f:  
    data = json.load(json_f)
    for p in data['people']:
        print('Name: ' + p['name'])
        print('Website: ' + p['website'])
        print('From: ' + p['from'])
        print('')
```
### Page Object from the website
We can get the 'page' object from  Wikipedia. Simply calling `get()` on a page will automagically fetch extracts, images, infobox data, wikidata, and other metadata via the MediaWiki, Wikidata, and RESTBase APIs.

To access Wikipedia page data via the MediaWiki API with `wptools`, you need each movie's Wikipedia page title, i.e., what comes
right after the last slash in en.wikipedia.org/wiki/....blahblahblahblah.... in the URL. Here, 'Mahatma_Gandhi' it is. 
```
page = wptools.page('Mahatma_Gandhi').get()
```
page now has the following attributes, which can be accessed using dot notation through `.data`. `page.data['image']`, for example, would return a list of data for 6 images on this specific Wikipedia page.
<img src="https://user-images.githubusercontent.com/31917400/37098852-dc257a7a-2216-11e8-9805-8c54ad5da83d.jpg" /> 

How big is the first image ?
```
page.data['image'][0]['size']
```
2951123

[NOTE]######################################################################################
As for Image Files, use the `PIL` library(Pillow) and `io` library for **non-text** requests. For example, 
```
import requests
from PIL import Image
from io import BytesIO

res = requests.get(url)
i = Image.open(BytesIO(res.content))
```
############################################################################################

#1. We're going to query the MediaWiki API, using `wptools` to get a **'movie poster URL'** via each page object's image attribute.
#2. Using that URL, we'll programmatically download that image into a folder called 'bestofrt_posters'.

Here the list of the movie title from wikipedia (by order of a movie ranking). Let's say...
```
title_list = ['The_Wizard_of_Oz_(1939_film)', 'Citizen_Kane', 'The_Third_Man',...................]

import pandas as pd
import requests   #general web scraping to RAM package 
import wptools   #'wikipedia-specific' web scraping package
import os   #filepath, folder editing package
from PIL import Image 
from io import BytesIO
```
Make directory if it doesn't already exist.
```
folder_name = 'bestofrt_posters'

if not os.path.exists(folder_name):
    os.makedirs(folder_name)
```
Create a List of dictionaries to build and convert to a DataFrame later. Also..we made a dict only for error! what we need from each movie wikipedia website are 'ranking', 'title', 'poster_url'. 
```
df_list = []

image_errors = {}

for t in title_list:
    try:
        # This cell is slow so print ranking to gauge time remaining
        ranking = title_list.index(t) + 1
        print(ranking)
        pagee = wptools.page(t, silent=True)
        images = pagee.get().data['image']

        # First image is usually the poster. That's the image we want. Get the Url.
        first_image_url = images[0]['url']
        
        #Now we obtain the image file in our RAM !
        res = requests.get(first_image_url)
        
        # Download movie poster image
        i = Image.open(BytesIO(res.content))
        image_file_format = first_image_url.split('.')[-1]
        i.save(folder_name + "/" + str(ranking) + "_" + t + '.' + image_file_format)
        
        # Append to list of dictionaries
        df_list.append({'ranking': int(ranking),
                        'title': t,
                        'poster_url': first_image_url})
    
    # Not best practice to catch all exceptions but fine for this short script
    except Exception as e:
        print(str(ranking) + "_" + t + ": " + str(e))
        image_errors[str(ranking) + "_" + t] = images
```
Take a look at the 'image_errors' dict. 
```
for i in image_errors.keys(): 
    print(i)
```
64_Dr._Strangelove / 22_A_Hard_Day%27s_Night_(film) / 53_12_Angry_Men_(1957_film) / 72_Rosemary%27s_Baby_(film)

Create a DataFrame from list of dictionaries.
```
df = pd.DataFrame(df_list, columns = ['ranking', 'title', 'poster_url'])
df = df.sort_values('ranking').reset_index(drop=True); df
```
<img src="https://user-images.githubusercontent.com/31917400/37120354-0fb25404-2252-11e8-9b1e-6d370d9efb45.jpg" width="600" height="180" /> 

# 2. Assessing 
 - Dataset: 'Phase II clinical trial dataset' for a new oral insulin called 'Auralin'. This dataset comes in three tables: 
   - **'patients'**: some basic information of individuals - id, name, location, appearance, BMI, etc.  
   - **'treatments'**: 350 patients participated in this clinical trial. None of the patients were using 'Novodra' (a popular injectable insulin) or 'Auralin' (the new oral insulin being researched) as their primary source of insulin before. **All were already experiencing elevated HbA1c levels.** All 350 patients were treated with 'Novodra' first to establish a baseline HbA1c level and insulin dose. After 4 weeks, (which isn’t enough time to capture all the change in HbA1c that can be attributed by the switch to Auralin or Novodra):
     - 175 patients switched to 'Auralin' for 24 weeks !
     - 175 patients continued using 'Novodra' for 24 weeks !
   - **'adverse_reactions'**: the adverse reaction reported by the patient
<img src="https://user-images.githubusercontent.com/31917400/37153167-bbc6b9b6-22d3-11e8-819f-592fec4744f7.jpg" /> 

 - 1) If we merge these 3 datasets, investigate the **duplicate** columns: `list(df)` returns a column list..
```
all_columns = pd.Series(list(patients) + list(treatments) + list(adverse_reactions))
all_columns[all_columns.duplicated()]
```
14 given_name / 15 surname / 21 given_name / 22 surname

 - 2) Findings
   - `df.head(4)`, `df.sample(4)`, `df.info()`, `df.describe()`, `df['col'].value_counts()`, `df['col'].sort_values()`, `df[condition].loc[]`, `df[df['col'].duplicated()]` `sum(df['col'].isnull())` 
   - **[Tidiness]** (structure issue):
     - In patients table, **'Contact'** column should be split into phone number and email.
     - In patients table, **'Given name'** and **'surname'** columns are duplicated in treatments and adverse_reactions tables
     - In treatments table, Three variables in two columns ('treatment', 'start dose' and 'end dose')
     - Adverse reaction should be part of the treatments table
     
   - **[Quality]** (contents issue):
     - In patients table:
       - **'Zip code'**
         - is a float(should be a string)
         - has four digits sometimes(then should start from'0')
       - **'height'**
         - Tim Neudorf height is 27(put 72 instead)
       - **'weight'**
         - kgs instead of lbs for Zaitseva weight
       - **'state'**
         - fullnames sometimes, abbreviations other times(should be consistent)
       - **'contact'**
         - Multiple phone number formats
         - Missing demographic information __can't clean__
       - TYPO..Dsvid Gustafsson
       - Erroneous datatypes ('assigned_sex', 'state', 'zip_code', and 'birthdate')
       - Default John Doe data
       - Multiple records for Jakobsen, Gersten, Taylor
       
     - In treatments table
       - Missing in **'HbA1c_changes'**       
       - Inaccurate **'HbA1c_changes'** (leading 4s mistaken as 9s)
       - Lowercase **'given_names'** and **'surnames'**
       - The letter 'u' in starting and ending doses for **'auralin'** and **'novodra'**
       - Erroneous datatypes in **'auralin'** and **'novodra'**
       - Nulls represented as dashes (-) in **'auralin'** and **'novodra'** 
       - Missing records (should be 350 instead of 280)       

     - In adverse_reactions table
       - Lowercase in **'given_names'** and **'surnames'**

# 3. Cleaning
```
patients_clean = patients.copy()
treatments_clean = treatments.copy()
adverse_reactions_clean = adverse_reactions.copy()
```
## > Cleaning for [Tidiness]
### 1. In patients table, **'Contact'** column should be split into **'phone_number'** and **'email'**.
<img src="https://user-images.githubusercontent.com/31917400/37227028-b871318c-23d3-11e8-995a-db6e4f9161be.jpg" width="150" height="300" /> 

https://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.str.extract.html
  - Let's play with this column(phone_number) !
    - `str.extract(regEx, expand=True)` return a new DataFrame with n columns(defined by the number of group in regEx).
    - `(`
    - `(?:` :  left paren will not count as a group result.
    - `\+\d{1,2}\s)?`
    - `\(?`
    - `\d{3}`
    - `\)?`
    - `[\s.-]?\d{3}[\s.-]?\d{4}`
    - `)`
```
patients_clean['phone_number'] = patients_clean.contact.str.extract('((?:\+\d{1,2}\s)?\(?\d{3}\)?[\s.-]?\d{3}[\s.-]?\d{4})', expand=True)

patients_clean['phone_number'].head(3)
```
<img src="https://user-images.githubusercontent.com/31917400/37216043-af59b1a4-23b1-11e8-9983-defba2199a38.jpg" /> 

 - Let's play with this column(email) !
   - `str.extract(regEx, expand=True)` return a new DataFrame with n columns(defined by the number of group in regEx).
   - `(`
   - `[a-zA-Z][a-zA-Z0-9_.+-]+`
   - `@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+[a-zA-Z]`
   - `)`
```
patients_clean['email'] = patients_clean.contact.str.extract('([a-zA-Z][a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+[a-zA-Z])', expand=True)

patients_clean['email'].sample(3)
```
<img src="https://user-images.githubusercontent.com/31917400/37228600-1c5238e0-23d9-11e8-855d-769ea3095bb2.jpg" /> 

 - Drop the original
```
patients_clean = patients_clean.drop('contact', axis=1)
```
### 2. In treatments table, Three variables in two columns ('treatment', 'start dose' and 'end dose')
 - From Wide to Long
 - `df2 = pd.melt(df, id_vars=['unaffected_col', 'unaffected_col', ..], var_name="categorical_col", value_name="numerical_col")`
 - `str.split(str='', num=n)`
   - str: This is any delimeter, by default it is space.
   - num: this is number of lines minus one
```
treatments_clean = pd.melt(treatments_clean, id_vars=['given_name', 'surname', 'hba1c_start', 'hba1c_end', 'hba1c_change'],
                           var_name='treatment', value_name='dose')
                           
treatments_clean = treatments_clean[treatments_clean.dose != "-"]
treatments_clean['dose_start'], treatments_clean['dose_end'] = treatments_clean['dose'].str.split(' - ', 1).str
treatments_clean = treatments_clean.drop('dose', axis=1)

treatments_clean.head(3)
```
<img src="https://user-images.githubusercontent.com/31917400/37232616-bb0dae7a-23e7-11e8-81fe-656bcbc6629b.jpg" /> 

### 3. Adverse reaction table should be part of the treatments table
https://chrisalbon.com/python/data_wrangling/pandas_join_merge_dataframe/
 - `concat()`: when sharing **the same column-names**
   - `df_new = pd.concat([df_a, df_b])`: stacking vertically.
   - `df_new = pd.concat([df_a, df_b], axis=1)`: stacking horizontally(..so repeated columns)
 - `merge()`: when having **different column-names**. It requires the common'key'(ID) columns.
   - `df_final = pd.merge(df_new, df_c, on='id', how='inner'/'outer'/'left'/'right')`
```
treatments_clean = pd.merge(treatments_clean, adverse_reactions_clean, on=['given_name', 'surname'], how='left')
treatments_clean.head(3)
```
<img src="https://user-images.githubusercontent.com/31917400/37233897-0c90f54a-23ed-11e8-89f8-a7dc596ec349.jpg" /> 

### 4. In patients, treatments and adverse_reactions tables, **'Given name'** and **'surname'** columns are duplicated.
https://erikrood.com/Python_References/dropping_rows_cols_pandas.html
 - `df.drop(['row_a', 'row_b'])`: drop a row by name
 - `df.drop(df.index[0], inplace=True)`: drop a row by index
 - `df.drop(['col_c'], axis = 1, inplace = True)`: drop a column by name
```
id_names = patients_clean[['patient_id', 'given_name', 'surname']]
id_names.given_name = id_names.given_name.str.lower()
id_names.surname = id_names.surname.str.lower()

treatments_clean = pd.merge(treatments_clean, id_names, on=['given_name', 'surname'])
treatments_clean = treatments_clean.drop(['given_name', 'surname'], axis=1)

treatments_clean.head(3)
```
<img src="https://user-images.githubusercontent.com/31917400/37233919-24a1c89e-23ed-11e8-950d-fe7441a561da.jpg" /> 

Patient ID should be the only duplicate column. Let's test.
```
all_columns = pd.Series(list(patients_clean) + list(treatments_clean))
all_columns[all_columns.duplicated()]
```

## > Cleaning for [Quality]
### 1. Cleaning for missing data
 - a. in 'treatments' dataset: **"Missing records (280 instead of 350)."** Let's say the missing treatments records are stored in a df named 'treatments_cut'. Of course it has the same fileds.
   - => Import the cut treatments into a DataFrame and concatenate it with the original treatments DataFrame.
```
treatments_clean = pd.concat([treatments_clean, treatments_cut], ignore_index=True)
```
<img src="https://user-images.githubusercontent.com/31917400/37241122-f93be68a-244b-11e8-9969-9c7e991fc8d5.jpg" /> 

 - b. in 'treatments' dataset: **"Inaccurate HbA1c changes"**
   - => Recalculate the hba1c_change column: hba1c_start minus hba1c_end.
```
treatments_clean.hba1c_change = (treatments_clean.hba1c_start - treatments_clean.hba1c_end)
```
<img src="https://user-images.githubusercontent.com/31917400/37241112-c268092c-244b-11e8-93b9-8419f3280bb5.jpg" /> 

### 2. Fixing datatype
 - a. in 'patients' dataset: **Zip code is a float and Zip code has four digits sometimes.**
   - => Convert the zip code column's datatype from a float to a **string** using `astype()`.
   - => Remove the '.0' using string slicing, and pad four digit zip codes with a leading 0.
```
patients_clean.zip_code = patients_clean.zip_code.astype(str).str[:-2].str.pad(5, fillchar='0')
```
<img src="https://user-images.githubusercontent.com/31917400/37241368-ca3a2352-244f-11e8-960d-5dfa8ea0cd60.jpg" /> 

   - => Reconvert NaNs entries that were converted to '0000n' by code above
```
patients_clean.zip_code = patients_clean.zip_code.replace('0000n', np.nan)

patients_clean.head(3)
```
<img src="https://user-images.githubusercontent.com/31917400/37241259-4b72794e-244e-11e8-97bb-c4f40aa1a94c.jpg" /> 














