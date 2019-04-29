## Step 1. Gatering data (download a file > open a jupyter notebook > unzip the zip file using python > import the csv file into a pandas DafaFrame in the Jupyter Notebook 
# unzip the file 
import zipfile 
# Extract all contents from zip file 
with zipfile.ZipFile('filename.zip', 'mode - w for write/ r for read') as myzip:
  myzip.extractall()
# if want to rewrite/read
  myzip.write('file.txt')
# for this project, we downloaded a file called, Armenian-Online-Job-Postings.zip
# Read CSV file into dataframe 
import pandas as pd 
import zipfile 
df = pd.read_csv('filename.csv')

## Step 2. Assessing data 
df 
# to get a concise summary of dataframe 
df.info()
# to disply the first five rows of the DF using .head
df.head(5)
# to display the last five rows of the DF using .tail
df.tail(5)
# to display the entry counts for the Year column using .value_counts
df.Year.value_counts()

## Step 3. Cleaning data: Improving quality and tidiness 
# to have a consistenet writing for each cell (e.g. ASAP, as soon as possible, immediately)
# first make a copy of dF so that later we can access the original file later 
df_clean = df.copy()
# to replace the column name 
# use the rename function and refer the columns to be erenamed. Not all the columns have to be renamed
df_clean = df_clean.rename(columns={'oldname1':'newname1', 'oldname2':'newname2'})
# OR
df.rename(columns={'oldname1:'newname1', 'oldname2':'newname2'}, inplace=True)

# to replace the cell data (valeus in panda series)
# first find the unique item of the cell value 
df_clean.StartDate.value_counts()
# to repace, but because there are so many similar versions, use panda's 'replace' method within for loop  
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

for phrases in asap_list:
    df_clean.StartDate.replace(phrase,'ASAP', inplace=True)

# to test if all codes have worked 
df_clean
# to see if the hidden columns from df_clean worked
df_clean.info()
df_clean.StartDate.value_counts()
# but still some can be hidden in collapsed rows: you can use 'assert' statement, but still need to use the loop
for phrase in asap_list:
  assert 'ASAP' not in df_clean.StartDate.values
# python will say it's assertion error 

## Step 4. Reassess and if you like it, store it

## Step 5. Analysis and Visualisation 
# e.g. if we want to find the percentage of people based on start date 
# numerator = number of 'ASAP' start dates 
asap_counts = df_clean.StartDate.value_counts()['ASAP']
asap_counts 
# the answer will be 6856
# denominator = number of non-empty start dates 
non_empty_counts = df_clean.StartDate.count()
non_empty_counts 
# the answer will be 9675 
# percentage of positions with an urgent start date i.e. 'asap' 
asap_coutns / non_empty_counts 
# the answer will be 0.708630.....

# to visualise this result using a pie chart 
%matplotlib inline 
df_clean.StartDate.value_counts().plot(kind="pie")
# but the pie chart would look messy due to many labels of all entries. 
# to give customised labels 
import numpy as np
labels = np.full(len(df_clean.StartDate.value_counts()), "", dtype = object)
labels[0] = 'ASAP'
df_clean.StartDate.value_counts().plot(kind="pie", labels = labels)
# this will only show a label of 'ASAP', which would look cleaner 

