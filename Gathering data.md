## Aim: Using the data from Rotten tomatoes (film review website) - data given by the instructor, downloadable as tsv file  

`read_csv` can read any flat files including tsv files, but put the sep parameter, which we want to be a tab `\t`
```
import pandas as pd 
df = pd.read_csv('bestofrt.tsv', sep='\t')
```

### Web scraping: extracting data from websites using code

**how**: Download HTML files and parse them using `Beautiful Soup` (Python)
```
import requests 
url = 'https://www.rottentomatoes.com/m/et_the_extraterrestrial'
response = requests.get(url)
```

Save HTML to file (only one file)
```
with open("et_the_extraterrestrial.html", mode='wb') as file:
  file.write(response.content)
```

To download all 100 files, put the code in loop (work with HTML in memory), and to use the BeautifulSoup HTML parser to help you work with the response content directly, add `lxml`
```
from bs4 import BeautifulSoup
soup = BeautifulSoup(response.content, 'lxml)
```

We downloaded all html file in a folder called, `rt_html`

First need to make a soup
```
from bs4 import BeautifulSoup
with open('rt_html/et_theextraterrestrial.html') as file:
  soup = BeautifulSoup(file, 'lxml')
soup
```
> This will bring the html file (it looks like, but it's tidy) 

To find/search anything in the file 
```
soup.find('title')
```
> But this will give you a result like below 

```#output:
<title>E.T. The Extra-Terrestrial (1982) - Rotten Tomatoes</title>
```

But we don't want those tags, but only title - then use 'slicing' like below 
```
soup.find('title').contents[0][:-len(' - Rotten Tomatoes')]
```

Because the list is one item long, we can never access it using the index 0 (e.g.[0])
The one starts with `[:-len]` is what string slicing looks like to get only title and year of the film 
This string splicing just grabbed everything from the first charater in our string to the 18th last character: why 18th? because...
```
len(' - Rotten Tomatoes')
```
> The answer is 18

Anyway the answer to the previous code would be... 
```#output: 
'E.T. The Extra-Terrestrial\xa0 (1982)'
```
> Here `\xa0` is unicode for non-breaking space

If we wish to remove the '- Rotten Tomatoes' part from all film titles... 

First create an empty list, df_list, to which dictionaries will be appended. This list will be later convereted to pandas DataFrame
```
from bs4 import BeautifulSoup
import os
import pandas as pd 

df_list = []
folder = 'rt_html'
for movie_html in os.listdir(folder):
  with open(os.path.join(folder, movie_html)) as file:
    soup = BeautifulSoup(file, 'lxml')
    title = soup.find('title').contents[0][:-len(' - Rotten Tomatoes')]
    audience_score = soup.find('div', class_ = 'audience-score meter').find('span').contents[0][:-1]
    num_audience_ratings = soup.find('div', class_ = 'audience-info hidden-xs superPageFontColor')
    num_audience_ratings = num_audience_ratings.find_all('div')[1].contents[2].strip().replace(',', '')
    df_list.append({'title': title, 'audience_score': int(audience_score), 'number_of_audience_ratings': int(num_audience_ratings)})
df = pd.DataFrame(df_list, columns = ['title', 'audience_score', 'number_of_audience_ratings'])
```

### Requests: how to download files from internet 
```
import requests 
import os

folder_name = 'ebert_reviews'
if not os.path.exists(folder_name):
  os.makedirs(folder_name)
  
url = 'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/....'
response = requests.get(url)

response.content 
```
> This will show the text file 

To download one individual file 
```
with open(os.path.join(folder_name, url.split('/')[-1]), mode='wb') as file:
  file.write(response.content)
os.listdir(folder_name)
```
> The answer would be `['.DS_Store', '11_e.t.-the-extra-terrestrial.txt']` DS_store is a hidden folder 

If we have to download lots of files? you can use a for loop for repetation. 
```
import requests 
import os
ebert_review_urls = ['all list of url addresses', ...]

folder_name = 'ebert_reviews'
if not os.path.exists(folder_name):
  os.makedirs(folder_name)
for url in ebert_review_urls:
  response = requests.get(rul)
  with open(os.path.join(folder_name, url.split('/')[-1]), mode = 'wb') as file:
    file.write(response.content)
```

To check the contents of this folder 
```
os.listdir(folder_name)
```
> This will give a list of all text file titles, or to know how many of them are there...
```
len(os.listdir(folder_name))
```

`os.listdir` is good if you want to open every file in the folder 
But alternative `glob` library allows for Unix-style pathname pattern expansion = to specify sets of filenames  
`glob.glob` = returns a list of path name that match pathname

```
import glob 

for ebert_review in glob.glob('ebert_reviews/*.txt'):
  print(ebert_review)
```
> This will print out the paths to all of these files here using glob

In python you should always open the file with an explicit encoding
```
for ebert_review in glob.glob('ebert_reviews/*.txt'):
  with open(ebert_review, encoding = 'utf-8') as file: 
    print(file.read())
    break 
```
> This will give the text that's on the website 

To extract the movie title, roger ebert review URL, and the review in each text file and append each trio as a dictionary to df_list 
```
import glob
import pandas as pd 

df_list = []
for elbert_review in glob.glob('ebert_reivews/*.txt'):
  with open(ebert_review, encoding = 'utf=8') as file:
    title = file.readline()[:-1]
    review_url = file.readline()[:-1]
    review_text = file.read()
    df_list.append({'title': title, 'review_url': review_url, 'review_text': review_text})
df = pd.DataFrame(df_list, columns = ['title', 'review_url', 'review_text'])
df 
```
> This will give us a table with three columns 

### APIs (Application Programming Interfaces): application interface to get images: open source - MediaWiki (e.g. screen scraping)
```
import rtsimple as rt 
rt.API_KEY = 'YOUR API KEY HERE'
movie = rt.Movies('10489')
# 10489 = movie id
movie.ratings['audience_score']
```
> The answer would be the audience score of E.T, which is 72

`page` and wptools will automagically fetch extracts, images, infobox data, wikidata and other metadata via the MediaWiki, Wikidata, and RESTBAse APIs

```
page = wptools.page('page_name_url').get()
page.data['image']
```
> This will return a list of data for all images on this specific wikipedia page

Most data from APIs comes in JSON or XML format 
Accessing Json array is the same as Python 
```
infobox_json
infobox_jason['value_name'][]
```

To download image files 
```
import requests
r = requests.get(url)
with open(folder_name + '/' + filename, 'wb') as f:
  f.write(r.content)
```

But this may cause an error becuase the file can be damaged. In order to prevent this error, you are better to access the response body as bytes, for non-text requests, to create an image from binary data returned by a request 
```
import requests 
from PIL import Image
from io import ByteIO
r = requests.get(url)
i = image.open(BytesIO(r.content))
```

### Storing data to csv file 
```
df.to_csv('dataset_name.csv', index=False)
```
