### aim: to clean data (trial data on oral insulin - three datasets provided: 'patients', 'treatments', and 'adverse_reactions'

## Step 1: Make a copy of each piece of data so that later can view the original dirty and/or messy dataset
df_clean = df.copy()

## Step 2: Find Missing data (completeness) first 
# 'treatments' table
# - missing HbA1c changes
# - missing records (280 instead of 350)
# 'patients' table 
# - missing demographic information (address-contact columns) (but this might be difficult to solve)

# to tackle 'missing records'
# if there is an extra file that stores missing records (e.g. 'treatements_cut.csv'), 
# define: to concartenate two files 
treatments_cut = pd.read_csv('treatments_cut.csv')
treatments_clean = pd.concat('treatements_clean, treatements_cut], ignore_index=True)
# test (to see if there are total 350 rows)
treatments_clean.head()
treatments_clean.tail()

# to tackle 'missing HbA1c change' 
# define: recalculate the 'bha1c_change' column: hba1c_start - hba1c_end
treatments_clean.hba1c_change = (treatements_clean.hba1c_start - tratements_clean.hba1c_end)
# test 
treatments_clean.hba1c_change.head()

## Step 3: check 'Tidiness' (because once it's tidy, it's easy to manupulate - fix the structural issue)
# 'patients' table 
# - contact column should be split into phone number and email 
# - given name and surname columns duplicated in 'treatments' and 'adverse_reactions' tables 
# 'treatments' table 
# - three variables in two columns (treatment, start dose and end dose) 
# - adverse reaction should be part of the 'treatments' table 

# to tackle 'contact colum should be split into phone number and email' 
# define: extract (using .str.extract) the phone number and email variables from the 'contact' column using regular expressions and pandas' str.extract method
#         and drop the 'contact' column when done 
patients_clean['phone_number'] = patients_clean.contact.str.extract('((?:\+\d{1,2}\s)?\(?\d{3}\)?[\s.-]?\d{3}[\s.-]?\d{4})', expand=True)
# [a-zA-Z] to signify emails in this dataset all start and end with letters
patients_clean['email'] = patients_clean.contact.str.extract('([a-zA-Z][a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+[a-zA-Z])', expand=True)
# Note: axis=1 denotes that we are referring to a column, not a row
patients_clean = patients_clean.drop('contact', axis=1)
# test (to confirm contact column is gone + if two newly created columns look right - sample + if no emails start with an integer)
list(patients_clean)
patients_clean.phone_number.sample(25)
patients_clean.email.sort_values().head()

# to tackle 'three variables' in the treatments table 
# define: melt the auraline and novodra columns to two new 'treatment' and 'dose' column
#         then split the dose column on '-' to obtain two extra columns - 'start_dose' and 'end_dose' 
#         and drop the dose column 
treatments_clean = pd.melt(treatments_clean, id_vars=['given_name', 'surname', 'hba1c_start', 'hba1c_end', 'hba1c_change'], 
                            var_name='treatment', value_name='dose')
treatments_clean = treatments_clean[treatments_clean.dose != "-"]
treatments_clean['dose_start'], treatments_clean['dose_end'] = treatments_clean['dose'].str.split(' - ', 1).str
treatments_clean = treatments_clean.drop('dose', axis=1)
# test 
treatments_clean.head()

# to tackle 'adverse reason be part of the 'treatments' table 
# define: merge the adverse_reaction column to the 'treatments' table, joining on given name and surname 
treatments_clean = pd.merge(treatments_clean, adverse_reactions_clean, on=['given_name', 'surname'], how='left')
# test 
treatments_clean 

# to tackle 'duplicated given_name and surname in three tables, and lowercase given names and surnames 
# define: ignore adverse_reaction table (merged to the 'treatments' table)
#         isolate the patient ID and names in the patients table, then convert these names to lower case to join with 'treatments'
#         then dropthe given name and surname columns in the treatments table 
id_names = patients_clean[['patient_id', 'given_name', 'surname]]
id_names.given_name = id_names.given_name.str.lower()
id_names.surname = id_names.surname.str.lower()
treatments_clean = pd.merge(treatments_clean, id_names, on=['given_name', 'surname'])
treatments_clean = treatments_clean.drop(['given_name', 'surname'], axis=1)
# test (to confirm the merge was executed correctly + make sure 'patient id' is the only duplicate column )
treatments_clean 
all_columns = pd.Series(list(patients_clean) + list(treatments_clean))
all_columns[all_columns.duplicated()]

## Step 3: remaining quality issues 

# to tackle 'zip code is a float not a string and zip code has 4 digits sometimes' 
# define: convert the zip code column's datatype from a float to a string using 'astype'
#         and remove the '.0' using string slicing, and pad 4 digit zip codes with a leading 0
#         and reconvert NaNs entries that were convered to '0000n' by code above
patients_clean.zip_code = patients_clean.zip_code.astype(str).str[:-2].str.pad(5, fillchar='0')
patients_clean.zip_code = patients_clean.zip_code.replace('000n', np.nan)
# test 
patients_clean.zip_code.head()

# to tackle the entry 'Tim Neudorf' height is 27 instead of 72in 
# NB. how do we know it's 72 instead of 27? use another information provided in the dataset (e.g. BMI of the entry = 26.1)
weight_lbs = 192.3
height_in = 27
703 * weight_lbs / (height_in * height_in)
# (note: 703 (= a conversion factor)). the answer is a BMI of 185, which doesn't make sense. Hence we know it's not a correct entry
# then we can think of the possiblity that the person who entered the data might have switched the number (72 > 27). 
# test it with 'height_in = 72', the result is 26.07777, which corroborates on the table here. 
# define: replace height from 27 to 72 in the 'patients' table 
patients_clean.height = patients_clean.height.replace(27, 72)
# test: to check if 27 is gone + to confirm the replacement worked 
patients_clean[patients_clean.height == 27]
patients_clean[patients_clean.surname == 'Neudorf']

# to tackle 'full state names sometiems, abbreviations other times' 
# define: apply a function that converts full state name to state abbreviation for California, New York, Illinois, Florida, and Nebraska 
          (first map from full state name to abbreviation, then function to apply) 
state_abbrev = {'Califonia': 'CA', 'New York':'NY, 'Illinois': 'IL', 'Florida': 'FL', 'Nebraska': 'NE'}
def abbreviate_state(patient):
  if patient['state'] in state_abbrev.keys():
      abbrev = state_abbrev[patient['state']]
      return abbrev
  else:
      return patient['state']
patient_clean['state'] = patients_clean.apply(abbreviate_state, axis=1)
# test 
patients_clean.state.value_counts()

# to tackle wrong spelling of 'Dsvid Gustafsson' 
# define: replace given name of 'Dsvid' with 'David' 
patients_clean.given_name = patients_clean.given_name.replace('Dsvid', 'David')
# test 
patients_clean[patients_clean.surname == 'Gustafsson'] 

# to tackle erroneous dataypes (assigned sex, state, zip_code, and birthdate columns/ auralin and novodra columns)
# define: convert assigned sex and state to categorical data types (zip code is already done)
patients_clean.assigned_sex = patients_clean.assigned_sex.astype('category')
patients_clean.state = patients_clean.state.astype('category')
# define: convert birthdate to datetime datatype
patients_clean.birthdate = pd.to_datetime(patients_clean.birthdate)
# define: strip teh letter 'u' in start dose and end dose and covert those columns to datatype 'integer' 
treatments_clean.dose_start = treatments_clean.dose_start.str.strip('u').astype(int)
treatments_clean.dose_end = treatments_clean.dose_end.str.strip('u').astype(int)
# test 
patients_clean.info()
treatments_clean.info()

# to tackle multiple phone number formats 
# define: strip all "", "-", "(", ")", and "+" and store each number without any formatting 
#         pad the phone number with a 1 if the length of the number is 10 digits (we want country code)
patients_clean.phone_number = patients_clean.phone_number.str.replace(r'\D+', '').str.pad(11, fillchar='1')
# test 
patients_clean.phone_number.head()

# to tackle 'default John Doe data' 
# define: remove the non-recoverable John Doe recordes from the 'patients' table 
patients_clean = patients_clean[patients_clean.surname != 'Doe']
# test (there should be no 'Doe' & '123 Main Street' records
patients_clean.surname.value_counts()
patients_clean.address.value_counts()

# to tackle 'multiple records for Jakobsen, gersten, Taylor' 
# define: remove the Jake Jakobsen, Pat Gersten, and Sandy Taylor rows from the 'patients' table, 
#         as these are nicknames; not in the 'treatments' table; all the second occurrence of the duplicate; the only occurances of non_null duplicate addresses 
patients_clean = patients_clean[~((patients_clean.address.duplicated()) & patients_clean.address.notnull())]
# test: to check if all those records are gone 
patients_clean[patients_clean.surname == 'Jakobsen']
patients_clean[patients_clean.surname == 'Gersten']
patients_clean[patients_clean.surname == 'Taylor']

# to tackle 'kgs instead of lbs for Zaitseva weight' 
# (background: patients.weight.sort_values() result shows the min. weight waas 48.8lbs, which seems not correct. 
#              but this could be recorded as kgs, instead of lbs
#              this detail was tested using the following codes against the entry's BMI by conversing lbs to kgs (* 2.20462, conversion factor)
weight_lbs = patients[patients.surname == 'Zaitseva'].weight * 2.20462
height_in = patients[patients.surname == 'Zaitseva'].height
bmi_check = 703 * weight_lbs / (height_in * height_in)
bmi_check
# the answer is around 19.055827. compare this with actual data recorded for this person 
patients[patients.surname == 'Zaitseva'].bmi
# the answer is 19.1. Therefore, we know that the entry data is in kgs, not lbs)
# define: use 'advanced indexing' to isolate the row where the surname is Zaitseva and convert the entry in ints weight field from kg to lbs
weight_kg = patients_clean.weight.min()
mask = patients_clean.surname == 'Zaitseva'
column_name = 'weight'
patients_clean.loc[mask, column_name] = weight_kg * 2.20462
# test: check if 48.8 is not the lowest 
patients_clean.weight.sort_values()

## Step 4: answer the question (e.g. Auralin vs. Novodra: Clincal Trial Key Metrics - is oral insulin (Auralin) would be as effecrtive as Novodra (injection based)


##### EXTRA EXAMPLES #####
# to remove 'bb' (first two letters in front of the animal name) before every animal name using string slicing 
df['Animal'] = df['Animal'].str[2:]
# to replace ! with .in body weight and brain weight column 
df['Body Weight (kg)'] = df['Body Weight (kg)'].str.replace('!', '.')
df['Brain Weight (g)'] = df['Brain Weight (g)'].str.replace('!', '.') 


