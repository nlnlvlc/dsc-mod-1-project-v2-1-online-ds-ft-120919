
# In this notebook I plan on loading in my data, structuring it and getting it ready for EDA, there will be no EDA in this notebook


```python
import os # in case I need to do anything with path/dir

import numpy as np
import pandas as pd

from glob import glob
```

### Load in the data using glob


```python
csv_files = glob("zippedData/*")
csv_files
```




    ['zippedData/imdb.title.crew.csv.gz',
     'zippedData/tmdb.movies.csv.gz',
     'zippedData/tmdb.movies.csv',
     'zippedData/imdb.title.crew.csv',
     'zippedData/rt.reviews.tsv',
     'zippedData/tn.movie_budgets.csv',
     'zippedData/imdb.title.ratings.csv',
     'zippedData/imdb.name.basics.csv',
     'zippedData/imdb.title.akas.csv.gz',
     'zippedData/imdb.title.ratings.csv.gz',
     'zippedData/imdb.name.basics.csv.gz',
     'zippedData/imdb.title.principals.csv',
     'zippedData/imdb.title.akas.csv',
     'zippedData/bom.movie_gross.csv',
     'zippedData/imdb.title.basics.csv',
     'zippedData/rt.movie_info.tsv',
     'zippedData/rt.reviews.tsv.gz',
     'zippedData/imdb.title.basics.csv.gz',
     'zippedData/rt.movie_info.tsv.gz',
     'zippedData/tn.movie_budgets.csv.gz',
     'zippedData/bom.movie_gross.csv.gz',
     'zippedData/imdb.title.principals.csv.gz']




```python
filename1 = csv_files[0]
```


```python
filename1
```


```python
csv_files_dict = {}
for filename in csv_files:
    filename_cleaned = os.path.basename(filename).replace(".csv", "").replace(".", "_")
    filename_df = pd.read_csv(filename, index_col=0)
    csv_files_dict[filename_cleaned] = filename_df
```


```python
df_tmdb_movies = csv_files_dict['tmdb_movies']
```


```python
df_tmdb_movies.head()
```

### Load files into sqlite database


```python
import sqlite3
```


```python
# this will create a sqlite file in your directory
conn = sqlite3.connect("movies_db.sqlite")
```


```python
# this converts a dataframe to a sqlite table, with the ame 'tmdb_movies'
# just let sql create the schema itself

df_tmdb_movies.to_sql("tmdb_movies", conn)
```


```python
# Write a function that converts the dataframe to a sqlite table
def create_sql_table_from_df(df, name, conn):
    # Use try except
    # it will try to make a table
    # if a table exists the function will execute whatever you put in the except part
    try:
        df.to_sql(name, conn)
        print(f"Created table {name}")
    
    # if the table exists t will tell you, and won't cause an error
    except Exception as e:
        print(f"could not make table {name}")
        print(e)
```


```python
# Looping through the dictionary from earlier, that contains the keys and dataframes of all the files
# We can create the tables programmatically
for name, table in csv_files_dict.items():
    create_sql_table_from_df(table, name, conn)
```


```python
# Now create a .gitignore file that will ignore the files that you unzipped and the sqlite file
# look at mine for a reference
# you cannot push files bigger than 100 MB to github from your computer

# you can create your file here in jupyter and open it from jupyter
with open("./.gitignore", "w+") as f:
    f.write("*.sqlite") # put files you want to ignore here
    f.write("\n") # insert a new line after each file
    f.write("zippedData/")
    f.write("\n")
    f.write("zippedData/*.csv")
    f.write("\n")
    f.write("zippedData/*.gz")
```


```python

```
