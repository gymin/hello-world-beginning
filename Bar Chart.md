# Creating a bar chart using an example of Pokemon data 
_background preparation_
```
import numpy as np
import pandas as pd 
import matplotlib.pyplot as plt
import seaborn as sb 
%matplotlib inline
```
_read the dataset 
```
pokemon = pd.read_csv('pokemon.csv')
print(pokemon.shape)
pokemon.head(10)
```
> Output: (807, 14) and table with id, identifier, generation_id, height, weight, base_experience, type_1, type_2, etc. 

## create a bar chart based on `generation_id`
```
sb.countplot(data = pokemon, x = 'generation_id'); 
```
> ouptupt will show the x as generation_id and y as count with different colours 
```
sb.color_palette()
```
To show all colour used in the table (returns a list of RGB tuples. Each tuple consists of 3 digits specifying the red, green and blue values)
```
base_color = sb.color_palette()[0]
```
> this will set one colour (in this case the first row of the sb.color_palette(): blue) as a base color

Then we specify the color in the 'countplot' above 
```
sb.countplot(data = pokemon, x = 'generation_id', color = base_color, order = gen_order);
```
But what if we want to show a bar chart that has the highest frequency to lowest?
* First we write a code to obtain that order programmatically 
```
gen_order = pokemon['genration_id'].value_counts().index 
```
> This will set the values in descending orde: refer back to the code in row 19

## Based on pokemon type?
type_order = pokemon['type_1'.value_counts().index 
sb.countplot(data = pokemon, x = 'type_1', color = base_color); 
plt.xticks(rotation = 90); ## this will rotate the category name of 'type 1' to vertical, so that all names are visible 
## OR alternatively, we can create a horizontal bar chart 
sb.countplot(data = pokemon, y = ' type_1', color = base_color, order = type_order); 

## For ordinal-type data, we need to sort the bars in order of the variables by converting the column into an ordered categorical data type 
## by default, pandas reads in string data as object types, and will plot the bars in the order in which the unique values were seen
level_order = ['alpha', 'beta', 'gamma', 'delta']
ordered_cat = pd.apt.types.CategoricalDtype(order = Ture, categories = level_order)
df['cat_var'] = df['cat_var'].astype(ordered_cat) 
## OR if pandas version is earlier than or equal to v0.20.3 
df['cat_var'] = df['cat_var'].astype('category', ordered = True, categories = level_order)
sb.countplot(data = df, x = 'cat_var', color = base_color) 
## output will show x axis cat_var but from alpha to delta, and y as count 

# Absolute vs. Relative Frequency: back to pokemon data...
pkmn_types = pokemon.melt(id_vars = ['id, 'identifier'], 
                          value_vars = ['type_1', 'type_2'], 
                          var_names = 'type_level', value_names = 'type').dropna()
     ## this creates a new table (pkmn_types), which shows only id, identifier, type_level (which shows type_1 or type_2), and type (e.g. fire, electric, etc.)
type_counts = pkmn_types['type'].value_counts()
type_order = type_counts.index
base_colo= sb.color_palette()[0]
sb.countplot(data = pkmn_types, y = 'type', color = base_color, order = type_order); 
## this will show a horizontal bar chart based on the order of frequency (absolute frequency): e.g. water on top with the highest frequency
# to change this to relative frequency bar chart... 
n_pokemon = pokemon.shape[0] ## total number 
max_type_count = type_counts[0]  ## max count number, e.g. frequency of 'water' 
max_drop = max_type_count / n_pokemon ## the proportion 
print(max_prop) 
## output: 0.1623296... (the proportion of the longest bar)
## now use numpy's arrange function to prouduce a set of evenly spaced proportioned values 
tick_props = np.arrange(0, max_prop, 0.02) 
## ouptput: array([0. , 0.02, 0.04, 0.06, 0.08, 0.1, 0.12, 0.14, 0.16])
## then to create an additional variable tick names to apply to the tick labels 
tick_names = ['{:0.2f}'.format(v) for v in tick_props] ## '{:0.2f}': the tick proportions formatted as strings to 2 decimal places
# now drop a bar chart based on relative frequencey 
sb.countplot(data = pkmn_types, y = 'type', color = base_color, order = type_order);
plt.xticks(tick_props * n_pokemon, tick_names)
plt.xlabel('proportion');
## ouptput: a horizonal bar chart,  y as type, and x as proportion, starting from 0.00 to 0.16 with 0.02 interval 
## if we want to show the % of each bar next to the bar?
sb.countplot(data = pkmn_types, y = 'type', color = base_color, order = type_order);
for i in range(type_counts.shape[0]):     ## we use a loop to place text elements one by one 
      count = type_counts[i]
      plt.text(count+1, i, pct_string, va = 'center');           ## first argument of the text function: the x position (+1: just after the end of the bar), 
                                  ## second argument: y position (starts with 0, incremented with loop)
                                  ## third argument: the string to be printed, the percentage)
                                  ## optional parameter VA to center the vertical alignment of text on each bar
## ouptput: horizontal bar chart, y as type, x as count but each bar shows a % next to the bar 

## Cat example 
# create the plot
base_color = sb.color_palette()[0]
sb.countplot(data = df, x = 'cat_var', color = base_color)

# add annotations
n_points = df.shape[0]
cat_counts = df['cat_var'].value_counts()
locs, labels = plt.xticks() # get the current tick locations and labels

# loop through each pair of locations and labels
for loc, label in zip(locs, labels):

    # get the text property for the label to get the correct count
    count = cat_counts[label.get_text()]   ## .get_text() to obtain the category name
    pct_string = '{:0.1f}%'.format(100*count/n_points)

    # print the annotation just below the top of the bar
    plt.text(loc, count-8, pct_string, ha = 'center', color = 'w')
## output: a vertical bar chart with x as cat_var (gamma, delta, beta, alpha), and y as count, on top of each bar % in one decimal

# Counting missing data
## create a table with the number of missing values in each column
df.isna().sum()
## output: two columns with one variable and another with the number of missing values 
## to create a bar chart based on the result...
na_counts = df.isna().sum()
base_color = sb.color_palette()[0]
sb.barplot(na_counts.index.values, na_counts, color = base_color)  ## first argument: x values, second argument: y values 
## ouptput: a vertical bar chart, x with variable names, y with counts 



      
      



