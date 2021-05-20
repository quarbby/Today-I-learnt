# Today I Learnt 2021

### 19/05/2021
Read pandas dataframe with a specific dtype for a specific column
```
df_agent_values = pd.read_csv(ora_agent_union_filename, dtype={'Node ID': 'int64'})

```

To read csv and ignore bad lines, add
```
error_bad_lines=False
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
