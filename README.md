# Today I Learnt 2021

### 24/09/2021
Permutate a list in python, with two in each set 
```
import itertools
permutations_of_two = list(itertools.permutations(hashtag_list,2))
```

To get the unique permutations: 
```
unique_combinations = list(set(tuple(i) for i in map(sorted, event_permutations)))
```

### 18/09/2021
Intersection of two lists in python 
```
list(set(list1) & set(list2))
```

### 05/09/2021
Unzip a bunch of .gz files 
```
To keep the .gz file: gunzip -k *.gz
Without keeping .gz file: gunzip *.gz
```

### 01/09/2021
Generate requirements.txt for a python project in a folder
```
pip install pipreqs

pipreqs /path/to/project
```

### 14/08/2021
Get elements from list2 that is not in the first list 
```
import numpy as np
main_list = np.setdiff1d(list_2,list_1)
# yields the elements in `list_2` that are NOT in `list_1`
```

### 07/08/2021
Add a cursor to every line in Visual Studio Code
- Ctrl A to select all text
- Alt Shift I

### 04/08/2021
Pandas groupby authorid and find percentage change of succeeding row for a certain column
```
df['posts_perc_change'] = df.groupby('authorid')['post_pred_score'].apply(pd.Series.pct_change)
df['posts_diff_change'] = df.groupby('authorid')['post_pred_score'].apply(pd.Series.diff)

```

### 02/08/2021
Unzip .gz files to another folder
```
for f in *.json.gz; do gunzip -c "$f" > <FOLDERNAME>/"${f%.*}"; done
```

### 26/07/2021
Add blue text to Overleaf 
```
\usepackage[table]{xcolor}
\newcommand\lyn[1]{\textcolor{blue}{#1}}
```

### 22/07/2021
Read pandas dataframe, empty places as string instead of annoying NaN's
```
pd.read_csv('test.csv', keep_default_na=False)
```

### 14/07/2021
Creating a wheel file; ref https://www.realpythonproject.com/how-to-create-a-wheel-file-for-your-python-package-and-import-it-in-another-project/
```
python setup.py bdist_wheel
```

### 06/07/2021
Find and replace all using bash
```
sed -i "s/'/ /g" FILENAME.csv
```

### 06/07/2021

Split a large file into many small files, keeping the header line for each smaller file

```
#!/bin/bash
awk -v l=11000 '(NR==1){header=$0;next}
                (NR%l==2) {
                   close(file); 
                   file=sprintf("%s.%0.5d.csv",FILENAME,++c)
                   sub(/csv[.]/,"",file)
                   print header > file
                }
                {print > file}' FILENAME.csv
```                

### 30/06/2021
TQDM import in jupyter notebook:
```
from tqdm.notebook import tqdm
```

### 30/06/2021
Clean Twitter texts - take away anything with @mentions, urls and punctuations
```
from nltk.corpus import stopwords
stop_words = stopwords.words('english')

def preprocess_text(df):
    df = df.dropna(subset=['text'])

    for idx, row in df.iterrows():
        try:
            text = row['text']
            text_cleaned = ' '.join(re.sub("(@[A-Za-z0-9]+)|([^0-9A-Za-z \t])|(\w+:\/\/\S+)"," ", text).split())
            text_cleaned = re.sub(r'^https?:\/\/.*[\r\n]*', '', text_cleaned)
            text_cleaned = ' '.join([w for w in text_cleaned.split() if not w.lower() in stop_words])
            df.at[idx, 'text_cleaned'] = text_cleaned
        except:
            print(idx)

    return df
```

### 25/06/2021
Split a string column into multiple columns, eg dob to day month year columns
```
df[["day", "month", "year"]] = df["dob"].str.split("-", expand = True)
```

### 24/06/2021
Check if all elements in a nested array are positive 
```
[all(n > 0 for n in i) for i in my_array]
```

### 19/06/2021
Add string to every line in a file using bash
```
sed -e 's/$/string after each line/' -i filename
```

### 14/06/2021
Move all files with a certain suffix to a directory. ie suffix is `_convertedtov1`, filename is `v1conversion`
```
find . -name '*_convertedtov1.json.gz' -exec mv {} v1conversion \;
```

### 04/06/2021
Merge all files in directory:
```
$ cat * > merged-file
```

### 01/06/2021
Split a file into smaller files of n lines, eg n=10000
```
split -l 10000 <FILENAME>
```

### 01/06/2021

Find files in a directory without an extension and add it 
```
find /path -type f -not -name "*.*" -exec mv "{}" "{}".json \;
```

### 20/05/2021
Sklearn classification report to pandas dataframe, that you can output to a csv
```
report = classification_report(y_test, y_pred, output_dict=True)
df = pandas.DataFrame(report).transpose()
```

### 19/05/2021
Read pandas dataframe with a specific dtype for a specific column
```
df_agent_values = pd.read_csv(ora_agent_union_filename, dtype={'Node ID': 'int64'})

```

To read csv and ignore bad lines, add
```
error_bad_lines=False
```

To ignore the warnings, add
```
warn_bad_lines=False
```

### 18/05/2021
Seaborn correlation plot
```
f, ax = plt.subplots(figsize=(10, 8))
sns.heatmap(corr_plot, mask=np.zeros_like(corr_plot, dtype=np.bool), cmap=sns.diverging_palette(220, 10, as_cmap=True),
            square=True, ax=ax, xticklabels=corr_plot.columns.values,
            yticklabels=corr_plot.columns.values)
```            

### 13/05/2021
Plotting with seaborn
```
import seaborn as sns 
ax = plt.figure(figsize=(5,5), dpi=300)
ax = sns.lineplot(data=df_degree_group, x='degree', y='influence_per_neighbour')
ax.set(xlabel='number of hops (degree) from agent node', ylabel='influence per neighbour')
```

To remove gridlines and set white background: `sns.set_style(style='white')`

### 13/05/2021
Read a bunch of csv files into a dataframe
```
import pandas as pandas
import os 

# Read Communication networks 
all_comms_directory = '../allcomms/'
all_comms_file = os.listdir(all_comms_directory)

df_links_array = []

for file in all_comms_file:
    if file.endswith('.csv'):
        full_filename = os.path.join(all_comms_directory, file)
        df = pd.read_csv(full_filename)
        df_links_array.append(df)

df_links = pd.concat(df_links_array, axis=0, ignore_index=True)
```

### 13/05/2021
When pandas `pd.read_json()` somehow doesn't work: 
```
import json
import pandas as pd

with open(filename) as f:
  data = pd.DataFrame(json.loads(line) for line in f)
```

### 05/07/2020

Merge all files in Unix without the header line 
`find -name "*.csv" | xargs -n 1 tail -n +2 > merged_files.csv`

### 19/03/2020

Git add all files of a type: `git add *.py`. Git add all files of a type recursively: `**/*.py`.

### 01/01/2020

Pytorch `einsum` is very powerful. It can do lots of matrix manipulation. 
Numpy has something similar, but not as powerful. Here's something on this comparison. https://stackoverflow.com/questions/55894693/understanding-pytorch-einsum
