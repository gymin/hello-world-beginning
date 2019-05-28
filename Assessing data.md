# Assessing data using the example of Auralin (oral insulin) 

## Types of daa  
* Dirty data (= low quality data): Content issue (e.g. inaccurate, corrupted, duplicated data)
* Messy data (= untidy data): Structural issu (e.g. tidy data: each variable forms a column, each observation forms a row, each observational unit forms a table)

## Types of assessment = visual (e.g. google sheet) vs programmatic (e.g. df.info())
* visual assessment = just check tables (better to use spreadsheet programms, but it might crash if the file is too large) 
* programmatic assessment = assessing using codes 

## Summary of key codes useful for assessment 

To show the summary (good to check the datatype, esp. ones that need calculation or summary)
```
.info()
``` 
>'object' as a result of `.info()` shows that they are string, which can't perform any numerical calcuations 

To show NaN (missing values) - but this might not pick up the correct NaN if a non-numerical value is recorded (e.g. /, NA, -)
```
df[df['address'].isnull()]
```
To show a numerical summary (e.g. count, mean, std, min, max, quartiles)
```
.describe()
```
To show a random sample (e.g. .sample(5) = 5 samples)
```
.sample(number)
```
To find duplicated entries (but this will show all actual examples of duplicated)
```
df[df.address.duplicated()]
```
To show the value in order (e.g. weight from light to heavy)
```
df.weight.sort_values()
```
To show if there is any missing data in Auralin 
```
sum(df.auralin.isnull())
```
To find a sepecfic entry (e.g. surname = 'Zaitseva')
```
df[df.surname == 'Zaitseva']
```
### imagine there are three datasets (patients, treatements, adverse_reactions), but we want to remove duplicated headings 
To create a list of all of the column names across the whole dataset, all three tables worth
```
all_columns = pd.Series(list(patients) + list(treatement) = list(adverse_reactions))
```
Then to see how many headings are duplicated
```
all_columns[all_columns.duplicated()]
```
To show a list of all of the columns in one data frame (e.g. patients)
```
list(patients)
```
