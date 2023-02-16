# Today I Learnt 

### 16 Feb 2023
Create a Pull request using Github Desktop

### 10 Jan 2023
Update a forked repo with the main one 

```
git remote add upstream ORIGINAL_REPOSITORY_URL

# Fetch all branches including master from original repository
git fetch upstream

# Merge this data in local master branch
git merge upstream/master

# Push changes to forked repository
git push origin master
```

### 6 Jan 2023
Installing an old version 1.14 of tensorflow, because it is not found on pip anymore
`pip3 install https://storage.googleapis.com/tensorflow/mac/cpu/tensorflow-1.14.0-py3-none-any.whl`

### 19 Nov 2022
GSDMM topic modelling 
```
from gsdmm import MovieGroupProcess
import gensim

df = preprocess_text(df)
df = df.dropna(subset=['text_cleaned'])

mgp = MovieGroupProcess(K=5, alpha=0.1, beta=0.1, n_iters=5)

docs = [doc.rsplit() for doc in df['text_cleaned'].tolist()]
vocab = set(x for doc in docs for x in doc)
word_dict = gensim.corpora.Dictionary(docs)
n_terms = len(vocab)

topics = mgp.fit(docs, n_terms)

def get_most_important_words_from_topics(cluster_word_distribution, top_cluster, values):
    '''prints the top words in each cluster'''
    for cluster in top_cluster:
        sort_dicts =sorted(cluster_word_distribution[cluster].items(), key=lambda k: k[1], reverse=True)[:values]
        print('Cluster %s : %s'%(cluster,sort_dicts))
        print(' — — — — — — — — —')
	
doc_count = np.array(mgp.cluster_doc_count)
print('Number of documents per topics :', doc_count)
print('*'*20)

# Topics sorted by document inside
top_index = doc_count.argsort()[::-1]
print('Most important clusters (by number of docs inside):', top_index)
print('*'*20)

get_most_important_words_from_topics(mgp.cluster_word_distribution, top_index, 20)
```

### 29 Sept 2022
Linux list the number of lines in multiple files 
```
find . -name '*.json' | xargs wc -l
```

### 26 Sept 2022
Seaborn variables 
```
import seaborn as sns
sns.set_style('white')
sns.set(font_scale=1.2)
```

### 22 Aug 2022 
Quick Python LDA 

```
from sklearn.decomposition import LatentDirichletAllocation as LDA
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.feature_extraction import text

import pandas as pd
import numpy as np

def get_lda_topics(text_array):
#     all_stop_words = text.ENGLISH_STOP_WORDS
#     if len(stop_words) > 0:
#         all_stop_words = text.ENGLISH_STOP_WORDS.union(stop_words)

#     count_vectorizer = CountVectorizer(stop_words=all_stop_words)
    count_vectorizer = CountVectorizer()
    count_data = count_vectorizer.fit_transform(text_array)
    
    number_topics = 5

    lda = LDA(n_components=number_topics, n_jobs=-1)
    lda.fit(count_data)

    lda_component_data = []

    words = count_vectorizer.get_feature_names()
    for topic_idx, topic in enumerate(lda.components_):
        topic_cut = " ".join([words[i]
                        for i in topic.argsort()[:-20 - 1:-1]])
        lda_component_data.append(topic_cut)

    return lda_component_data
    
df = pd.read_csv(CSV_FILE)
df = df[df['message'].notnull()]
messages = df['message'].tolist()

get_lda_topics(messages)
```

### 12 Jul 2022
How to set font sizes and other params in matplotlib
```
fig, ax = plt.subplots()

params = {'legend.fontsize': 'x-large',
          'figure.figsize': (10,10),
         'axes.labelsize': 'x-large',
         'axes.titlesize':'x-large',
         'xtick.labelsize':'x-large',
         'ytick.labelsize':'x-large'}
plt.rcParams.update(params)
```

### 12 Jul 2022
I always forget how to set figure size in matplotlib
```
from matplotlib import pyplot as plt
plt.figure(figsize=(1,1))
```

### 3 Jul 2022 
Youtube-dl library with auto subs 

```
pip install youtube-dl
youtube-dl --write-auto-sub --sub-format srt http://youtube.com... 
```

### 21 May 2022
Matplotlib/seaborn 

```
import matplotlib.pyplot as plt
%matplotlib inline
import seaborn as sns
sns.set()
```

1. Change Figure size: `plt.figure(figsize=(10,7))`
2. Change font size `sns.set(font_scale=1.5)`
3. Change labels 
```
p = sns.scatterplot(data=df, x='x', y='y')
p.set_xlabel('X Score')
p.set_ylabel('Y Score')
```

### 19 Apr 2022
Extend NLTK stopword list 

```
from nltk.corpus import stopwords

stop_words = stopwords.words('english')
stop_words.extend(['rt'])
```

### 19 Apr 2022
Pandas dataframe merge two dataframes on different columns:

`merge_df = pd.merge(cues_df, labels_df, left_on='Author', right_on='id', how='left')`


Rename columns of a pandas dataframe:

`merge_df.rename(columns={'A':'A_renamed'}, inplace=True)`

### 22 Mar 2022
Finding CPU Information on Linux: `cat /proc/cpuinfo `

Finding GPU Information on Linux: `cat /proc/driver/nvidia/gpus/<GPU NUMBER>/information`

### 22 Feb 2022
Ignore hidden files in list directory python
```
import glob
import os

def listdir_nohidden(path):
    return glob.glob(os.path.join(path, '*'))
```

### 12 Feb 2022
Bash find total number of lines of all files of .json type in directory
` find . -type f -name "*.json"|xargs cat |wc -l`

### 11 Feb 2022
Align Word2Vec matrices from different corpuses 

This issue happens because the embeddings for each group are created separately, hence, their geometric positions in vector space are not directly comparable. To compare them, we first have to align the embeddings.
One way to align matrices is called Procrustes alignment, which uses singular value decomposition; Hamilton et al 2016a use this method to align their historical word embeddings. Note that to do this alignment, each matrix first needs to be subset to the common vocabulary and reindexed appropriately.

[Reference Collab](https://colab.research.google.com/drive/16cM5NXedlrvU2mp-HcYKs9OIMkYItTS1?usp=sharing#scrollTo=RbIw-eWTkpVQ)

```
def procrustes(A, B):
    """
    Learn the best rotation matrix to align matrix B to A
    https://en.wikipedia.org/wiki/Orthogonal_Procrustes_problem
    """
    # U, _, Vt = np.linalg.svd(B.dot(A.T))
    U, _, Vt = np.linalg.svd(B.T.dot(A))
    return U.dot(Vt)

def intersect_vocab (idx1, idx2):
  """ Intersect the two vocabularies

  Parameters:
  ===========
  idx1 (dict): the mapping for vocabulary in the first group
  idx2 (dict): the mapping for vocabulary in the second group

  Returns:
  ========
  common_idx, common_iidx (tuple): the common mapping for vocabulary in both groups
  """
  common = idx1.keys() & idx2.keys()
  common_vocab = [v for v in common]

  common_idx, common_iidx = {v:i for i,v in enumerate (common_vocab)}, {i:v for i,v in enumerate (common_vocab)}
  return common_vocab, (common_idx, common_iidx)

def align_matrices (mat1, mat2, idx1, idx2):
  """ Align the embedding matrices and their vocabularies.

  Parameters:
  ===========
  mat1 (numpy.ndarray): embedding matrix for first group
  mat2 (numpy.ndarray): embedding matrix for second group

  index1 (dict): the mapping dictionary for first group
  index2 (dict): the mapping dictionary for the second group

  Returns:
  ========
  remapped_mat1 (numpy.ndarray): the aligned matrix for first group
  remapped_mat2 (numpy.ndarray): the aligned matrix for second group
  common_vocab (tuple): the mapping dictionaries for both the matrices
  """  
  common_vocab, (common_idx, common_iidx) = intersect_vocab (idx1, idx2)
  row_nums1 = [idx1[v] for v in common_vocab]
  row_nums2 = [idx2[v] for v in common_vocab]

  #print (len(common_vocab), len (common_idx), len (common_iidx))
  remapped_mat1 = mat1[row_nums1, :]
  remapped_mat2 = mat2[row_nums2, :]
  #print (mat1.shape, mat2.shape, remapped_mat1.shape, remapped_mat2.shape)
  omega = procrustes (remapped_mat1, remapped_mat2)
  #print (omega.shape)
  # rotated_mat2 = np.dot (omega, remapped_mat2)
  rotated_mat2 = np.dot (remapped_mat2, omega)

  return remapped_mat1, rotated_mat2, (common_idx, common_iidx)
```

### 11 Feb 2022
Using regex in Python to remove @-mentions, hashtags and URLs:
`' '.join(re.sub("(@[A-Za-z0-9]+)|([^0-9A-Za-z \t])|(\w+:\/\/\S+)"," ",x).split())`

### 10 Feb 2022
Dumping out a mongodb database into a json file (becomes json lines):
```
mongoexport --host mongodb1.example.net --port 37017 --username user --password "pass" --collection contacts --db marketing --out mdb1-examplenet.json
```

### 9 Feb 2022
Python implementation of Levenshtein Distance
```
def levenshteinDistance(s1, s2):
    if len(s1) > len(s2):
        s1, s2 = s2, s1

    distances = range(len(s1) + 1)
    for i2, c2 in enumerate(s2):
        distances_ = [i2+1]
        for i1, c1 in enumerate(s1):
            if c1 == c2:
                distances_.append(distances[i1])
            else:
                distances_.append(1 + min((distances[i1], distances[i1 + 1], distances_[-1])))
        distances = distances_
    return float(distances[-1])
```

### 9 Feb 2022
Python implementation of Hamming distance
```
def hamming_distance(string1, string2): 
    distance = 0
    L = len(string1)
    for i in range(L):
        if string1[i] != string2[i]:
            distance += 1
    return distance
```

### 5 Feb 2022
Finding every combination pairs between two lists in Python
```
import itertools
a = [1,2,3]
b = [4,5,6]
for r in itertools.product(a, b):
    print(r[0], r[1])
```

### 29 Jan 2022
When a data file is too huge to read in pandas and you need to process row by row in Python, use csv DictReader and DictWriter

```
for w in tqdm(movies, desc='files'):
    with open(os.path.join(file_dir,w), 'r', encoding='utf-8') as read_obj, \
        open(os.path.join(output_dir, 'movies.csv'), 'w', encoding='utf-8', newline='') as write_obj:

        csv_dict_reader = DictReader(read_obj)
        column_names = csv_dict_reader.fieldnames
        column_names.append('identities')

        csv_dict_writer = DictWriter(write_obj, column_names)
        csv_dict_writer.writeheader()

        for row in tqdm(csv_dict_reader, desc='rows'):
            description = row['a_description']
            row['identities'] = 'me'  # this is how to add an additional column
            csv_dict_writer.writerow(row)
```

### 6 Jan 2022
Find common rows between two dataframes in R
```
common <- intersect(data.frame1$col, data.frame2$col)  
data.frame1[common,] # give you common rows in data frame 1  
data.frame2[common,] # give you common rows in data frame 2
```

Postgresql select count of intersection of two tables - Note the `I` is needed! 
```
SELECT COUNT(*) FROM 
(
	SELECT agent FROM table1
		INTERSECT 
	SELECT agent FROM table2
) I 
```

Postgresql merge the rows of two tables
```
CREATE TABLE table3 AS 
(
	SELECT * FROM table1
	UNION
	SELECT * FROM table2
);
```

### 3 Jan 2022
[Zeygna](https://github.com/nkthiebaut/zeugma) is a easy to use text classification wrapper 
```
from sklearn.linear_model import LogisticRegresion
from zeugma.embeddings import EmbeddingTransformer

glove = EmbeddingTransformer('glove')
x_train = glove.transform(corpus_train)

model = LogisticRegression()
model.fit(x_train, y_train)

x_test = glove.transform(corpus_test)
model.predict(x_test)
```

### 28 Dec 2021
To format a HTML document in visual studio code: `Shift + Alt + F`

### 27 Dec 2021
Remove punctuations from string in python and regex

```
import re
res = re.sub(r'[^\w\s]', '', test_str)
```

### 26 Dec 2021
Current favourite R ggplot theme
```
p2 <- ggplot(data=combined_filtered, aes(x=reorder(x, -count), y=count)) + 
  geom_point(stat='identity') + 
  facet_wrap(~data_type, ncol=1, scales="free") +
  labs(x="x", y="Count") +
  theme_light() +
  theme(axis.text.x = element_text(angle=90,size=8),
        panel.grid.major.x = element_blank(),
        panel.grid.minor.x = element_blank(),
        panel.grid.minor.y = element_blank(),
  )
print(p2)
```

### 25 Dec 2021
You can use the `library(sqldf)` in R to make selection statements from a dataframe.
In this case, I am finding a substring from a column in the dataframe.
```
sqldf("SELECT * FROM df WHERE a_description LIKE '%mother%'")
```

### 23 Dec 2021
Remove URLs from a string in python using regex
```
def remove_URL(text):
    return re.sub(r"http\S+", "", text)
```

### 30 Nov 2021
Zip up subfolders in a directory one by one: `find . -type d -maxdepth 1 -exec zip -r {}.zip {} \;`

### 31 Oct 2021
Python set a train, validation, test split for pandas dataframe 
```
import pandas as pd
from sklearn.model_selection import train_test_split

def split_stratified_into_train_val_test(df_input, stratify_colname='y',
                                         frac_train=0.6, frac_val=0.15, frac_test=0.25,
                                         random_state=None):
    '''
    Splits a Pandas dataframe into three subsets (train, val, and test)
    following fractional ratios provided by the user, where each subset is
    stratified by the values in a specific column (that is, each subset has
    the same relative frequency of the values in the column). It performs this
    splitting by running train_test_split() twice.

    Parameters
    ----------
    df_input : Pandas dataframe
        Input dataframe to be split.
    stratify_colname : str
        The name of the column that will be used for stratification. Usually
        this column would be for the label.
    frac_train : float
    frac_val   : float
    frac_test  : float
        The ratios with which the dataframe will be split into train, val, and
        test data. The values should be expressed as float fractions and should
        sum to 1.0.
    random_state : int, None, or RandomStateInstance
        Value to be passed to train_test_split().

    Returns
    -------
    df_train, df_val, df_test :
        Dataframes containing the three splits.
    '''
#     if frac_train + frac_val + frac_test != 1.0:
#         raise ValueError('fractions %f, %f, %f do not add up to 1.0' % \
#                          (frac_train, frac_val, frac_test))

    if stratify_colname not in df_input.columns:
        raise ValueError('%s is not a column in the dataframe' % (stratify_colname))

    X = df_input # Contains all columns.
    y = df_input[[stratify_colname]] # Dataframe of just the column on which to stratify.

    # Split original dataframe into train and temp dataframes.
    df_train, df_temp, y_train, y_temp = train_test_split(X,
                                                          y,
                                                          stratify=y,
                                                          test_size=(1.0 - frac_train),
                                                          random_state=random_state)

    # Split the temp dataframe into val and test dataframes.
    relative_frac_test = frac_test / (frac_val + frac_test)
    df_val, df_test, y_val, y_test = train_test_split(df_temp,
                                                      y_temp,
                                                      stratify=y_temp,
                                                      test_size=relative_frac_test,
                                                      random_state=random_state)

    assert len(df_input) == len(df_train) + len(df_val) + len(df_test)

    return df_train, df_val, df_test
```
 
### 31 Oct 2021
Plotting nice graphs in R: use `theme_minimal()` for white background minimalistic theme, and use `scale_fill_npg()` or `scale_fill_aaas()` for Nature and Science color contrasts charts (so you don't have to think of your own color palette)
```
library(ggplot2)
library(ggsci)

p = ggplot(timeplot, aes(x = Month, y = Scores))+
  geom_bar(stat = 'identity', aes(fill = Month))+
  facet_wrap(.~Maneuver, scale = 'free') +
  theme_minimal() +
  scale_fill_startrek()

p
```

### 25 Oct 2021
Postgres SQL queries for string contains

```
WHERE foo LIKE '%bar%'

WHERE foo ILIKE '%bar%' --case insensitive

WHERE foo ~* 'bar' --regex

WHERE foo ~* '\ybar\y' --matches bar but not foobar (\y is word boundary)

WHERE foo::tsvector @@ 'bar'::tsquery --matches bar but not foobar
```

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
pip install wheel setuptools
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
stop_words.extend(['rt'])

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

### 31/12/2019 - Update Homebrew Ruby Version
```
$brew install rbenv ruby-build
$rbenv install --list
$rbenv install 2.7.0
$rbenv global 2.7.0
$ruby -v
```

### 27/12/2019 - Setting up vanilla Kubernetes Dashboard offline in Ubuntu 18.04.03 

https://github.com/jax79sg/offlinekubenetesubuntu

### 22/12/2019 - Creating Anaconda environments with different python versions

`conda create -n [NameOfYour VirtualEnvironment] python=3.4.4`

### 1/11/2019 - Dockerfile for Java & Python and multiple commands 

Was trying to construct a dockerfile for [Giveme5W1H](https://github.com/fhamborg/Giveme5W1H)

Problem was: the docker container needed both Java and Python, and the stanford-corenlp needed to be run in the background.

Hence, first put both running commands in a script:
```
giveme5w1h-corenlp &
giveme5w1h-rest
```

Then form the Dockerfile. First install Linux and the necessary apt-libraries, including Java (openjdk8-jre). Then install python. Then copy the script into the docker volume. 
```
FROM alpine:3.7

RUN apk update \
&& apk upgrade \
&& apk add --no-cache bash \
&& apk add --no-cache --virtual=build-dependencies unzip \
&& apk add --no-cache curl gcc g++ python3-dev \
&& apk add --no-cache libxml2-dev libxslt-dev \ 
&& apk add --no-cache openjdk8-jre

RUN apk add --no-cache python3 \
&& python3 -m ensurepip \
&& pip3 install --upgrade pip \
&& rm -r /usr/lib/python*/ensurepip && \
if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip; fi && \
if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
rm -r /root/.cache

RUN pip3 install giveme5w1h
RUN giveme5w1h-corenlp install

COPY giveme5w1h.sh /usr/local/myscripts/giveme5w1h.sh
CMD ["/bin/bash", "/usr/local/myscripts/giveme5w1h.sh"]
```

### 30/10/2019 - CentOS increasing partion space of root

```
df -h
fdisk /dev/sda
vgextend
vgdisplay
fdisk -l 
pvcreate /dev/sda3
reboot
vgdisplay
vgextend centos /dev/sda3
vgdisplay
lvextend -l +<AMONT> /dev/centos/root
xfs_growfs /dev/centos/root
df -h
```

### 26/9/2019 - jq conversion of jsonl to json

`jq -s '.' input.jsonl > output.json`

`jq -c '.[]' input.json > output.jsonl`

### 25/9/2019 - Cypher Queries

Cyper is a language of Neo4J Graph Database

MATCH (me: Person{id: '100000'}) -[:FRIEND_OF]-(mf)-[:FRIEND_OF]-(friend:Person {id:'12323223'}) RETURN friend.id AS id, friend.name AS name

MATCH (me: Person{id: '"100000"'}) -[:FRIEND_OF]-(mf)-[:FRIEND_OF]-(friend:Person {id:'"12323223"'}) RETURN friend.id AS id, friend.name AS name, count (DISTINCT mf) AS mutualFriends

MATCH (n:Person{name: 'A'}) OPTIONAL MATCH (n)-[:FRIEND_OF*0..1]-(friends:Person) WITH collect(distinct ID(friends))AS rr MATCH (friend:Person)-[r:FRIEND_OF]0>(friend_of_a_friend:Person) WHERE ID(friend) IN rr AND ID (friend_of_a_friend) IN rr RETURN friend, r, friend_of_a_friend

CREATE (A:Person{name:'A'}), (B:Person{name:'B'}), (C:Person{name:'C'}), (D:Person{name:'D'}), (A)-[:FRIEND_OF]->(D), (B)-[:FRIEND_OF]->(D), (C)-[:FRIEND_OF]->(D)

MATCH (A:Person{name:'A'})-[:FRIEND_OF]-(D:Person), (B:Person{name:'B'})-[:FRIEND_OF]-(D:Person), (C:Person{name:'C'})-[:FRIEND_OF]-(D:Person) RETURN D

MATCH (n:Person{name:'A'}), (r:Person{name:'B'}) CREATE (n)-[:FRIEND_OF]-(r)

nodes[] empty or size[] empty in return response

MATCH (n) DETACH DELETE n

MATCH (n) OPTIONAL MATCH (n)-[r]-() DELETE n,r

WHERE is a very expensive operation

-> For unidirectional

For bidirectional

### 16/07/2018 - Elastic Search Java VM Max Count 

Elasticsearch uses a mmapfs directory by default to store its indices. The default operating system limits on mmap counts is likely to be too low, which may result in out of memory exceptions.

On Linux, you can increase the limits by running the following command as root:
`sysctl -w vm.max_map_count=262144`
To set this value permanently, update the `vm.max_map_count` setting in `/etc/sysctl.conf`. To verify after rebooting, run sysctl vm.max_map_count.

### 02/06/18 - Docker Remove all Untagged Images

Docker images that are not tagged are dangling images. Here's how to clean them up. `docker rmi -f $(docker images -q --filter "dangling=true")`

### 10/05/18 - dstat

`dstat` is a great command line tool to obtain system metrics in one
command. I was looking for a tool to monitor IO.

On a side note, one can use `top` and `htop` to monitor processes,
`free` to check memory usage quickly, and `iftop` for detailed network
monitoring.

### 04/04/18 - Notepad++ Add to Every Line of File 

Press Ctrl+H to bring up the Find/Replace Dialog.
Choose the Regular expression option near the bottom of the dialog.

To add a word, such as test, at the beginning of each line:
```
Type ^ in the Find what textbox
Type test in the Replace with textbox
Place cursor in the first line of the file to ensure all lines are affected
Click Replace All button
```

To add a word, such as test, at the end of each line:

```
Type $ in the Find what textbox
Type test in the Replace with textbox
Place cursor in the first line of the file to ensure all lines are affected
Click Replace All button
```
### 03/04/18 - Python command line arguments getopts

Generally in python you get your command line arguments by `sys.argv`. But you can add options like `-i`, and use a self-written `getops` function to get the arguments tagged to the options as a dictionary.

```
import sys

def getopts(argv):
    opts = {}  # Empty dictionary to store key-value pairs.
    while argv:  # While there are arguments left to parse...
        if argv[0][0] == '-':  # Found a "-name value" pair.
            opts[argv[0]] = argv[1]  # Add key and value to the dictionary.
        argv = argv[1:]  # Reduce the argument list by copying it starting from index 1.
    return opts

if __name__ == '__main__':
    from sys import argv
    myargs = getopts(argv)
    if '-i' in myargs:  # Example usage.
        print(myargs['-i'])
    print(myargs)
```

### 31/03/18 - Opening iBooks Library in Mac

For ebooks you downloaded to iBooks, to find the actual files, use the terminal to open: `open ~/Library/Mobile\ Documents/iCloud\~com\~apple\~iBooks/Documents`. For ebooks that you've purchased from the Apple Store: `open ~/Library/Containers/com.apple.BKAgentService/Data/Documents/iBooks` [More on Apple StackExchange](https://apple.stackexchange.com/questions/259836/where-are-my-ibooks-stored-in-macos-sierra)

### 27/03/18 - Python open IDLE in virtual environment 

Say you sourced a virtual environment, and we want to open up an IDLE that uses the environment, use `python -m idlelib.idle`. This is for windows though. 

### 17/02/18 - Python Virtual Environments

I keep getting myself confused with virtual environment commands in Windows and Linux. Python virtual environments are awesome. They're these sandbox environments that you can use and `pip install` anything without really messing up your host. To start a virtual environment, do `virtualenv <ENV_NAME>`. If the command isn't found, then you've got to do a global `pip insall virtualenv`. Now here lies the difference. In a windows computer, to start the environment, or they call it source, use `source <ENV_NAME>/env/activate`. If you're using Linux, do `source <ENV_NAME>/bin/activate`. I just keep mixing them up. 

### 13/02/18 - Relearning Git Basics

Git is perfectly easy to use when you're the only one contributing. In such a case, it's also somewhat superfluous. But I digress.

There are cases when you'll want to contribute, but realize that the server's dev branch has changed so drastically that you no longer recognize it. Here's where "git rebase" will save the day.

While in your development branch, type this in:

"git rebase <branch that you want to pull changes from>"

Essentially, "git rebase" takes the base of the branch you want to merge into, and places it into your branch as its new base. Beware of conflicts at this stage, but it's a lot better than manually eyeballing the files for the smallest change.

There are, however, certain cases where git will take issue with your attempts at rebasing, namely when trying to push your shiny new rebased branch on top of your old branch that currently resides in the server. This makes sense - your new branch no longer shares the same tree structure as the old existing branch as it's been rebased - but that doesn't make it any less irritating.

Therefore, I am pleased to report to all highly annoyed users that force is the answer. Push through your changes to shut git up (but first, remember to check that your rebased version actually works, seriously):

"git push --force origin <server branch that you want to push to> <branch that you want the changes to flow from>" 


### 07/02/18 - Observables and Subscriptions in Angular 5

One good thing of Angular 5 is the encapsulation of components. So you can define a dashboard with a tile component, in which the tile component has html, js and css styles that are independent of the overall dashboard styles so they don't conflict/ overwrite each other. But sometimes you need to pass messages through components, in some sort of broadcast style. So they have `Observables` which have `Subject`s or `BehaviourSubject`s which can store data to be broadcasted. In another component, you can use a `Subscription` to get the value of the subject when its changed. 

Here's the tricky part. Do not instantiate the `Service`, in which the `Observable` is constructed in the individual components if you want to pass information through the same `Observable`. At each instantiation, the reference points to a new object. To make this `Observable` global, instatiate the service in the `providers` array in the typescript file of the main app, i.e. `angular.app.ts`. 

Also, in case you're wondering on the difference between an `Observable` and a `Subject`, here's something I borrowed from a site i found.

Observable | BehaviorSubject/Subject
--- | ---
Is just a function, no state  | Has state. Stores data in memory 
Code run for each observer    | Same code run only once for all observers   
Creates only Observable (data producer alone) | Can create and also listen Observable (data producer and consumer)
Usage: Simple Observable with only one Observer | Usage: Store and modify data frequently; Multiple observers listen to data

Alright. End of rant. 

[Further details on passing information through components](https://angularfirebase.com/lessons/sharing-data-between-angular-components-four-methods/#Unrelated-Components-Sharing-Data-with-a-Service)

### 06/02/18 - Porting Angular 5 CLI offline 

To port your Angular 5 command line installer from an online to offline machine, to save the trouble of installing offline using caches and other activities, simply navigate in the online machine to `C:\Users\<Username>\AppData\Roaming\npm`. Copy out the `ng` and `ng.cmd` and `node_modules/@angular`. In the offline computer, place the modules in the corresponding folder. Test the porting by using `ng serve` in the command line. 

If you have problems finding where your angular installation is in (assuming you installed in your online machine via `npm install -g @angular/cli`, use `npm link @angular/cli`. 

Quick note, when you're done with this, you'll realise that Angular 6 is going to be released. :'( 

### 05/02/18 - Clearing device cache does the job
Ran into issues of my logos on my react native android app not working properly. 2 exact same build on two different device yet the device with the older version is working well instead of the newer android version. Reinstalling the apk doesnt solve the issue. Found out that clearing the cache of the device itself actually solve the problem. Who knew the solution will be so straightforward?


### 01/02/18 - NGINX on Red Hat Enterprise Linux (RHEL) 7.4
#### Background
NGINX installed on RHEL 7.4, configured to serve files from root directory ```/var/www/fileserver``` at ```/```.
Ownership and permissions for the directory ```/var/www/fileserver``` looks like this:
```
drwxr-xr-x root root .
drwxr-xr-x root root ..
drwxr-xr-x root root folder1
```
All files in ```folder1``` have the following ownership and permissions:
```
-r-xr--r-- root root someFile.txt
```
```folder1``` was copied into its place by a non-root user using ```sudo```, similar to this:
```
$ sudo cp -r ~/someFolder /var/www/fileserver/folder1
```
This configuration works perfectly fine on Ubuntu.

#### The Problem
Calling the NGINX service through a web browser at ```http://localhost/``` shows the file index, but all
child directories and files (i.e., ```folder1``` itself and any other directories and files in it) are not accessible (i.e., HTTP 403).

#### The Solution
Run the following command:
```
$ sudo restorecon -r /var/www/fileserver
```

#### Explanation
Something to do with SELinux. The command ```restorecon``` "restores the SELinux context".

#### Moral of the Story
RHEL isn't called "Enterprise Linux" for nothing.

### 31/01/18 - tqdm. Easy to implement progress bar on the Python console
There are times when you are just looping around doing something and wondering when it will finish. Printing dots and astericks with the ```print('*',end='')``` aint't really that cool either.
tqdm gives the progress bar, progress count, time taken and time to finish by approximation. Kinda cool when i was processing videos of frames.
```
pbar = tqdm(total=100)
for i in range(100):
    pbar.update(1)
pbar.close()
```

### 29/01/18 - $PS1 generator

You can generate your own `$PS1` (aka custom bash prompt)
[here](http://bashrcgenerator.com/).

For reference, here's mine:

```
PS1="\[$(tput bold)\]\[\033[38;5;10m\]\u@\h\[$(tput sgr0)\]\[$(tput sgr0)\]\[\033[38;5;15m\] [\$?] \[$(tput sg    r0)\]\[\033[38;5;11m\]\w\[$(tput sgr0)\]\[\033[38;5;15m\]\n\\$ \[$(tput sgr0)\]"˧
```

Who knew a two-line `$PS1` would be so useful? It also contains the
exit status of the previous command.

### 29/01/18 - Dotfiles

Way overdue update as I was learning this while setting up my new
ThinkPad. The venerable ArchWiki has a handy [table](https://wiki.archlinux.org/index.php/Bash#Configuration_files), but I'll make it simpler (for limited use cases only). Here's how I
configure my ThinkPad.

- `~/.profile`: exports environment variables.
- `~/.bashrc`: sets color prompts, `$PS1`, exports aliases and functions
- `~/.xsessionrc`: loads `.profile` and sets X session stuff (DPI, terminal colors)
- `/etc/rc.local`: writes values to `/sys/devices/...` to set mouse pointer stuff

Cases:
- login shells: loads `~/.profile` and `~/.bashrc`
- X display manager: loads `~/.xsessionrc`, which in turn loads `~/.profile`
- non-login shells: loads `~/.bashrc` (`~/.profile` would have already been loaded in X)

This is really the simplest configuration I could come up with without
having too many dotfiles while also covering all use cases
(X, SSH, TTY).

### 29/01/18 - Ethernet cables

- Cat 5: 100 Mbps (100 metres)
- Cat 5e: 1 Gbps (100 metres)
- Cat 6: 1 Gbps (100 metres), 10 Gbps (55 metres)
- Cat 6a: 10 Gbps (100 metres)

### 29/01/18 - Gateways on private networks

If you're setting up a private network (e.g. `192.168.0.0/16`), you
don't need a gateway *unless* you need to access a network on another
subnet. This means that you don't need to set a gateway in your network
settings.

### 29/01/18 - Reverting a revert in GitLab

Situation: you've accidentally merged a merge request in GitLab. Then
you click `Revert`. However, when you try to create a new merge request
for the branch to be merged (call it `feature`), you find that there are
no changes between `feature` and the target branch (call it `dev`).

What happened: when you clicked `Revert`, GitLab *added* extra commits
on top of `dev`. These commits are *undo* commits, i.e. they undo the
changes of `feature`. This means that the `feature` tree is still a
*sub-tree* of `dev`, so it's already *merged*.

How to fix it, fast: you can run this:

```
git checkout <commit> -- <files>
```

where `<commit>` the latest commit of `dev` before the merge request
was merged. This stages the changes to *undo the undo*; commit the
changes and this will make `feature` a super-tree of `dev`.

### 29/01/18 - Reading files in Java 8

Enough with `BufferedReader`s and `FileInputStream`s; behold Streams in
Java 8:

```
Stream<String> stream = Files.lines(Paths.get(filePath));
```

### 23/01/18 - Checksums in Windows 

To compute the checksum in windows command line without downloading extra files, use `CertUtil -hashfile C:\myFile.txt MD5`, where `myFile.txt` is the file you want to compute an MD5 sum for. 

More information [here](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc732443(v=ws.11)#BKMK_menu)

### 15/01/18 - Logstash ingestions 

Logstash has been very useful in ingesting data into Elastic Search, after which you can use Kibana to view and visualise the data. Really simple and cool. But here's a few stuff... 

Logstash is meant to ingest logs. So make sure your data file is formatted one entry per line. Which means there should be a new line between each entry. Which means, if you want to do that for a series of entries like json data, do the following: 
```
{.....entry 1.....}
{.....entry 2.....}
````

Shouldn't be too hard to write a few bash/python lines to format your data. Then turn on Elastic Search and Kibana (optional). Do make sure the ELK stack is in the same versioning because it's not really backward compatible :( 

Now you need to write a config file for ingesting the data. And our `conf/json_to_es.conf` goes like this: 
```
input {
	stdin {
	}
}

filter {
	json {
		source => "message"
		remove_field => [ "message", "host" ]
	}
}

output {
    elasticsearch {
		index => "twitter"
    }
}
```

Running the ingestion: `logstash -f ../conf/json_to_es.conf < data_file.json`. Oh do make sure you're in the logstash bin directory if you're using this command directly, otherwise do the necessary path changes. We're feeding the input from `stdin`, which we can read from the config file and the `<` operator. After the json is read, we do some filtering, basically putting the data into a source field called `message`, so that we don't mix up the data with the other meta-data. Then we remove some fields like original `message` field which is just a string of the json object that we fed in without formatting, and `host`, which describes the computer you ran the indexing on. Then we output the result to an index called `twitter`, which will autogenerate and create the fields that are the keys in the json object; saves us time from writing parsers. 

TADA

### 08/01/18 - Bash Double Quotes

Quotations in bash can get tricky. Double quotes dereference variables, while single quotes go literal. 

```
A='123'  
echo "$A"   # Outputs 123
echo '$A'   # Outputs A
```

Also, I came across [this link](https://zwischenzugs.com/2018/01/06/ten-things-i-wish-id-known-about-bash/) while searching for that 

### 08/01/18 - Shell Startup 

I think [this picture](https://zwischenzugs.files.wordpress.com/2018/01/shell-startup-actual.png) is very informative to tell you where a shell goes when it starts up. I.e. which profile directories are entered. 
                                                                              
                                                                              ### 31/12/17 - Powering off USB devices in Linux

Sometimes in my favourite file manager (Thunar), I can only unmount an
external hard drive, but not eject it (aka "safely remove"). This means
the hard drive is not powered off, and unplugging it may cause some
problems (when I was younger, I blew a drive by not safely removing it).

Enter `udisksctl`:

```
udisksctl unmount -b /dev/sdb1  # note it's sdb1!
udisksctl power-off -b /dev/sdb # note it's sdb!
```

### 21/12/17 - Looking up past commands in Git Bash

Holidays are apparently bad for work productivity. I learnt this upon coming back yesterday and realizing that I had forgotten how to commit to the Git server's dev branch. Silly Ryan.

If you need to revisit old commands on Git Bash, all you need to type is 'history'. This will return the list of commands that you have keyed into the console over time (limited to 500 entries by default).

If line-by-line archaeology is not your thing, there's always the Ctrl-R shortcut which triggers reverse search. Just type in the opening words of the command you're interested in (assuming you can remember that much) to have it appear in the console again. However, note that commands which have been keyed in later will appear before those which were added earlier, hence the name 'reverse search'.  

### 17/12/17 - Removing all files in .gitignore

Say you commit some code, and then wrote a gitignore file. That means some files in the previous commit should be removed from the git indexed and ignore. Just run this command and you'll be fine
`git rm -r --cached . `

### 17/12/17 - Assigning emojis as variables in Swift

I learnt that you can assign emojis to variables in Swift. Check out the below code block! However, not all emojis work as Swift identifiers. [Here's a list of what works](https://stackoverflow.com/questions/39188733/which-emoji-characters-work-as-swift-identifiers)

```
let 👩 = "me"
let 😗 = "kiss"
print(😗 + " " + 👩)		// Prints "kiss me"

let 😮 = "oh"
let 💩 = "shit"
print(😮 + " " + 💩)		// Prints "oh shit"

let 🐶🐮 = "dogcow"
let 🐶 = 3
let 🐮 = 🐶 + 2
print(🐮)			// Prints 5
```

### 12/12/17 - Sorting a Hashmap in Java

I never actually learnt how to sort a hashmap in Java.

```
Map<String, Integer> unsortedMap = new HashMap<String, Integer>();

// Add items to unsortedMap here

// Convert unsorted map to a linked list 
List<Map.Entry<String, Integer>> unsortedList = new LinkedList<Map.Entry<String, Integer>>(unsortedMap.entrySet());

// Sort the linked list in reverse order of values
Collections.sort(unsortedList, new Comparator<Map.Entry<String, Integer>>(){

	@Override
	public int compare(Entry<String, Integer>o1, Entry<String, Integer> o2){
		return (o1.getValue()).compareTo(o2.getValue());
	}
}.reversed());

// Put the sorted list back into a linked hashmap
Map<String, Integer> sortedMap = new LinkedHashMap<String, Integer>();
for (Map.Entry<String, Integer> entry: unsortedList) {
	sortedMap.put(entry.getKey(), entry.getValue());
}
```

### 09/12/17 - Regex

I've never actually had a good time with Regex.

\w matches alphanumeric characters, which means a-z, A-Z, and 0-9. It also matches the underscore, _, and the dash, -.

\d matches digits, which means 0-9.

\s matches whitespace characters, which include the tab, new line, carriage return, and space characters.

\S matches non-whitespace characters.

. matches any character except the new line character \n.

+ matches more than 1 character

[Here's More](https://www.dataquest.io/blog/regular-expressions-data-scientists/?imm_mid=0f9342&cmp=em-data-na-na-newsltr_20171206)

### 08/12/17 - Cleaning up Git branches

Recently I've had success in using feature branches for development, but `git checkout [tab autocomplete]` shows a lot of old branches that have already been deleted.

If they've been cleaned up on `remote` and local, you can remove them with `git remote prune origin`.

### 07/12/17 - Python string matching with list 

This uses python. Say you have a list of strings `a = ['hello', 'world']` and a string `str = 'hello world'`. The problem is you want to find whether any strings in `a` appear in `str`. 

If you just want a `True` or `False` boolean, do: `if any(x in str for x in a):`

If you want the first match (with False as a default), do: `match = next((x for x in a if x in str), False)`

If you want to get all matches (including duplicates), do: `matches = [x for x in a if x in str]`

If you want to get all non-duplicate matches (disregarding order), do: `matches = {x for x in a if x in str}`

If you want to get all non-duplicate matches in the right order:
```
matches = []
for x in a:
    if x in str and x not in matches:
        matches.append(x)
```

### 06/12/17 - Calling subdirectories as modules in python

Say you have a file `mainfile.py` and a subdirectory with a python module `subdir/subfile.py`. Add an `__init__.py` in the subdirectory to call functions in `subfile.py`. The directory structure looks as such: 
-mainfile.py
|_ subdir
  - subfile.py
  - __init__.py
  
To call a function in `subfile.py`, do: 
```
In mainfile.py

from subdir import subfile

subfile.function()
```

### 04/12/17 - Memory in Linux

[Help! Linux ate my RAM!](https://www.linuxatemyram.com/)

### 02/12/17 - Remove ip addresses tagged to an ethernet interface 

Remove all ip addresses tagged to `eth0`: `ip addr flush dev eth0`

### 11/11/17 - Python Graph Gallery

[Python Graph Gallery](https://python-graph-gallery.com/?imm_mid=0f7469&cmp=em-data-na-na-newsltr_20171018)

Brilliant collection of graphs that you can make with Seaborn, useful for front end visualisations. Gives you the input and output formats too. 

### 04/11/17 - Debugging the front end web 

Useful debugging tools: 
- `console.log`: Everyone knows this 
- `console.assert`: Yknow javascripts and asserts woohoo
- `console.count(label)`: Counts the number of times you print a particular label, which can also be a count variable i. Haha.
- `console.table()`: Useful in putting a json object in a table form for debug
- `console.group`: Useful in grouping elements 
- `$()`: Document selectors just like in jQuery

[Full article Here](https://blog.pragmatists.com/how-to-debug-front-end-console-3456e4ee5504)

### 01/11/17 - Unicode and friends

Problem: encoding and decoding produces different results on different machines (with different OSes). Analysis of the binary representations of certain fields show that there was a "pointer" error, i.e. the start index (for say, four bytes for an int) was incorrect.

Initial suspicions: must be a decoding problem, because encodings are variable-width, i.e. sometimes more than one byte. As I thought about it I became increasingly convinced, because at some point the program expects a binary input that is converted into characters. What could go wrong?

Let's say the binary input is all ASCII-compliant.

```
0XXXXXX 0XXXXXXX 0XXXXXXX
```

Then the converted characters will be nice, universally recognisable ASCII characters that can be decoded back into binary by my program.

However, it's ridiculous to expect the binary input to be ASCII-compliant. Who knows what the format is like? What if the input is:

```
1110XXXX 10XXXXXX 10XXXXXX
```

Ouch. That's actually **one** character in UTF-8, and three characters in ISO-8859-1 or other funky encodings.

So the sender must encode the binary input into a string using Encoding 1, let the communications transmit the string, and the receiver must decode the encoded string using the same Encoding 1. What could go wrong? *Everything*.

As long as the data is still encoded in Encoding 1, everything that touches the data needs use Encoding 1 for operations. That's hard to guarantee.

To make matters worse, there are binary sequences which are **invalid** sequences in UTF-8 and other encodings. How they are handled depends on the implementation of whatever's handling it...

Solution: I still haven't found the bug, I've only guessed. But it would be interesting to find out where exactly the encoding/decoding screwed up, and this may also explain why "the messages received in the current system have problems": maybe the decoding has been wrong all this while!

If you're lucky enough to be in this situation, please read [this](https://en.wikipedia.org/wiki/Binary-to-text_encoding) and just use Base-64.

Outcome: Will try soon.

Note to self: if the decoding is wrong, the corrupted input may still be recoverable by guessing the encoding. However this is assuming that Implementations X and Y didn't drop invalid code sequences along the way! #undefinedbehaviour

### 01/11/17 - `table-layout`

Problem: I have a (Bootstrap) table and the column widths change depend on the screen size. I want to lock the widths of certain columns.

Solution: [`table-layout: fixed`](https://css-tricks.com/fixing-tables-long-strings/)

Also: `white-space: nowrap`? `text-overflow: ellipsis`? Holy cow I may actually like being a frontend guy now.

Outcome: Will try soon.

Note to self: add other notes on modern CSS goodies (flexbox and grid layout, and to think the latter was only official in March 2017!)

### 27/10/17 - Disable Web Security for Chrome 

This allows you to read local files 

On a mac: `open -a Google\ Chrome --args --disable-web-security --user-data-dir`
On windows: `chrome.exe --disable-web-security`

### 24/10/17 - \[UNIX\] Saving a directory for later

```
pushd . # this will save the current directory into a stack, for easier restoration
popd # pops the top directory out of the same stack, restores you back into that directory
```

### 11/10/17 - Node.JS 'Error: SSL Error: SELF_SIGNED_CERT_IN_CHAIN'

Sometimes when you do an `npm install` you encounter a SELF_SIGNED_CERT error, especially if you're behind a firewall or proxy. Here's the trick: `npm config set strict-ssl false`

### 05/10/17 - Positive Integer Solutions

[I can't solve this. Read it](https://www.quora.com/How-do-you-find-the-positive-integer-solutions-to-frac-x-y%2Bz-%2B-frac-y-z%2Bx-%2B-frac-z-x%2By-4/answer/Alon-Amit)

### 01/10/17 - Pandas Dataframe from Json

You can create a dataframe from a JSON object with the fields you want. 

Say you have a JSON object in which the data is an array of JSON objects within `hits` like follows: 
```
{
    "hits": [
        {
            "created_at": "2017-09-29T13:30:21.000Z",
            "title": "SCION: A Secure Internet Architecture [pdf]",
            "url": "https://www.scion-architecture.net/pdf/SCION-book.pdf",
            "author": "cimnine",
            "points": 1,
            "story_text": null,
...
```

Easily create a pandas dataframe with the fields you want: 
```
df = DataFrame()
requested_keys = ["title", "author", "url"]
data = DataFrame(data["hits"])[requested_keys]
```

### 02/09/17 - The Mathematics of Machine Learning

Perhaps its time to get a little into the heart of machine learning [From the IBM Blog](http://datascience.ibm.com/blog/the-mathematics-of-machine-learning/)

### 13/08/17 - Recursively download files in a directory

`wget -r --no-parent http://donhopkins.com/home/TheSimsDesignDocuments/`

### 10/08/17 - Batch rename .png to .jpg files with Bash

`for i in *.png; do mv $i $Pi%%.png}.jpg; done`

### 05/08/17 - Visual Studio Code Switch between Terminal and Editor

The great thing about Visual Studio Code is how everything is keyboard defined and your fingers don't have to leave the vicinity of the keyboard. But when you want to switch within the Integrated Terminal and Editor, you have to add the following keybindings into your user preferences keybindings json. 

```
{ "key": "ctrl+`", "command": "workbench.action.terminal.focus"},
{ "key": "ctrl+`", "command": "workbench.action.focusActiveEditorGroup", "when": "terminalFocus"}
```

### 24/06/17 - Concatenate smaller text files 

To concatenate smaller text files into one huge big text file, use `ls *.txt | xargs -L 1 cat >> input.txt`

### 24/06/17 - Neural Networks Reading

Just some links for later reading
* [Stanford University Deep Learning Course](https://web.stanford.edu/class/cs20si/syllabus.html?utm_campaign=Artificial%2BIntelligence%2BWeekly&utm_medium=email&utm_source=Artificial_Intelligence_Weekly_62)
* [Convolutional Methods for Text](https://medium.com/@TalPerry/convolutional-methods-for-text-d5260fd5675f?ref=aiweekly)
* [Picasso: A free Visualiser for CNN](https://medium.com/merantix/picasso-a-free-open-source-visualizer-for-cnns-d8ed3a35cfc5)

### 23/06/17 - Sendmail with HTML formatting

TIL we can use the Linux `sendmail` command and attach a .txt file with HTML formatting. Just add the following on the top of the .txt file 
```
Mime-Version:1.0
Content-Type: text/html
```

So you can now use html formatting like `<em> <br> <b>`. You can even do inline styles like `<h3 style="color: #1c70db;">NOW $159</h3>`. Cool stuff. 

### 22/06/17 - Seq2Seq Tensorflow Meetupsg

So we went to Tensorflow MeetupSG held at Google Singapore. Pretty tired so i'll just post some links here 
* Sam Witteveen [Twitter](https://twitter.com/sam_witteveen?lang=en) spoke on Seq2Seq. Here's [his Github](https://github.com/samwit/TensorFlowTalks) on these talks for some of the notebooks used
* [Tensorflow Playground](http://playground.tensorflow.org/#activation=tanh&batchSize=10&dataset=circle&regDataset=reg-plane&learningRate=0.03&regularizationRate=0&noise=0&networkShape=4,2&seed=0.88062&showTestData=false&discretize=false&percTrainData=50&x=true&y=true&xTimesY=false&xSquared=false&ySquared=false&cosX=false&sinX=false&cosY=false&sinY=false&collectStats=false&problem=classification&initZero=false&hideText=false) could potentially be useful for creating visualisations and outputs that you don't really know about, but are mostly a bunch of magic neural networks
* Matin Andrews from Red Cat Labs spoke about Captioning. [Here](https://github.com/mdda/deep-learning-workshop) are a bunch of his deep-learning workshops notebooks. 
* This is Red Cat Labs [Convolution demo](http://redcatlabs.com/2017-03-20_TFandDL_IntroToCNNs/CNN-demo.html#/)
* Flikr30K Feat(Image) was used to featureize all the images using InceptionV3
* The latest deep learning thing is called [Attention is all you need networks](https://arxiv.org/abs/1706.03762). Yes seriously, I need attention. 
* Martin Andrews also spoke about [Named Entity Recognitions](http://redcatlabs.com/2015-08-27_GTC-SA-2015-Poster/) from GPUs

* Random side link: [A Brief History of CNNs in Image Segmentation](https://blog.athelas.com/a-brief-history-of-cnns-in-image-segmentation-from-r-cnn-to-mask-r-cnn-34ea83205de4)

### 03/06/17 - Bash file manipulation

If you want to cut out the first column of a `csv` file, or any file with a delimited for that matter `cat filename.csv | cut -d ',' -f1`. Putting `f1` cuts the first column, `f2` cuts out the second column and so forth. 

Say you want to split a huge file into smaller chunks of n lines each: `split -l n filename.txt`

### 03/06/17 - Find latest file and timing command line

To find in a folder the file created the latest: 

```
ls -Artls | tail -n 1
```

### 15/04/17 - Pretty Print JSON on command line 

TIL how to pretty print JSON on command line using the `jq` library. 
```
cat file.json | jq '' > prettyfile.json
```

Alternatively, you can also use [JSON Lint](http://jsonlint.com)

### 15/04/17 - Setting up Nginx, uwsgi, Django

I have played with the Django basic tutorials for quite some time, but the thing I really dislike about basic "Getting Started" tutorials is the lack of transition guide from development to production environment. So I decided to figure out how exactly we can serve a Django app on a full-fledged web server like Nginx.

The stack that we want to set up is:
```
Web Client <-> Nginx <-> uwsgi <-> Django
```
I did this on RHEL7 VM in virtualbox. I followed the tutorial from [official uwsgi page](http://uwsgi-docs.readthedocs.io/en/latest/tutorials/Django_and_nginx.html), but there are so many things missing in the tutorial that I had to fill in the blanks from googling and stack overflow. This post is about those blanks I had to fill in.

#### 1. Set up Client <-> uwsgi <-> Python stack 

I assume that everyone reading this knows how to setup virtualenv and uwsgi using pip, and install nginx with yum, apt-get, or build from source. Installing pip using uwsgi requires python-dev tools to be installed in the base OS though, something which the tutorials left out.

After the installation is complete, write a simple test.py which gives a http response of "Hello World" and type the following cmd:
```
uwsgi --http :8000 --wsgi-file test.py
```
This initiates the uwsgi to python stack using the http protocol and uwsgi listens on port 8000. When working in RHEL/CentOS, we also need to open the port to firewall on public zone:
```
firewall-cmd --zone=public --add-port=8000/tcp --permanent
```
Now you should be able to visit **http://&lt;your IP address>:8000/** to see **"Hello World!"**

#### 2. Set up Client <-> uwsgi <-> Django stack
This one is straightforward. Just initiate a django project and in the project folder, type:
```
uwsgi --http :8000 --module mysite.wsgi
```
Don't worry about mysite.wsgi file. It is created temporarily (some black magic that I don't understand yet) to serve Django project. You should see the Django welcome page on **http://&lt;your IP address>:8000/**

#### 3. Set up Client <-> nginx <-> static files
This is where things get tricky due to lots of fill in the blanks, so I will try to be more comprehensive. Install nginx with yum, apt-get, build from source, whatever, and start the server with cmd:
```
nginx
```
You should see the **http://&lt;your IP address>:80/** serving an nginx welcome message (you might need to open port 80 in firewall too).

In Django project folder, create the "mysite_nginx.conf" following the tutorial in the link above. Once done, create the symbolic link for this file to /etc/nginx/sites-enabled:
```
sudo ln -s ~/path/to/mysite/mysite_nginx.conf /etc/nginx/sites-enabled
```
What the tutorials failed to tell me was that I actually have to mkdir /etc/nginx/sites-enabled because it doesn't come with nginx installation. They also failed to tell me that I have to create a line in /etc/nginx/nginx.conf to include the folder:
```
http{
  ...
  include /etc/nginx/sites-enabled/*.conf
  ...
}
```
Make all modifications and restart nginx with:
```
nginx -s reload
```
At this stage, you would think you are able to serve the static file locations as configured in mysite_nginx.conf (as the tutorials made me believe)...until you realized **http://&lt;your IP address>:8000/static** gives a **403 Forbidden**.

After 1 day of googling, I realized that the tutorials failed to mention we must set read and execute permissions for the entire path, not just the folder we are serving. So recursively **chmod 755** for all directories in your path to static files and **chmod 644** for files that you are serving.

#### 3. Set up Client <-> nginx <-> uwsgi <-> Django
While nginx can serve static files without any python/django involvement, we do want our applications to handle more complex requests from clients (such as a request for data from DB). This is where nginx need to pass request upstream to django via a Web Server Gateway Interface (wsgi), the standard which any python application talks to web servers.
```
uwsgi --socket :8001 --wsgi-file test.py
```
The above command sets up a webport socket, but the tutorial recommends using unix socket instead. So change the upstream django in "mysite_nginx.conf" to:
```
server unix:///path/to/mysite/mysite.sock;
# server 127.0.0.1:8001; # Comment this out when using unix socket
```
Test the interface with:
```
uwsgi --socket mysite.sock --wsgi-file test.py --chmod-socket=666
```
The socket permission is important.

If the above works, you can run the complete stack with:
```
uwsgi --socket mysite.sock --module mysite.wsgi --chmod-socket=666
```
Of course I would recommend creating a **mysite_uwsgi.ini** instead of typing out the whole command above, but now you have a working stack:
```
Client <-> nginx <-> uwsgi <-> Django
```

### 05/03/17 - Syntax highlighting in Markdown

I read Danqi's submission again and realised it was syntax-highlighted. I don't know why I didn't find this out earlier.

[Documentation here](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#code).

### 04/03/17 - JavaScript Closure

I agree, JavaScript sucks. Part of the reason is because there are too many black magic going on. Closure is one of those black magic that irks me because we can still reference local variables once the function goes out of scope!

```javascript
function derp(){
  var someVar = 0;
  return function(param){
    someVar += param;
    alert(someVar);
  };
}

var myderp = derp();
myderp(10); //Alert 10
myderp(20); //Alert 30, wtf? I can still change someVar?
```

Coming from a background of C, this turns my whole world upside down. However, this black magic is quite useful when making charts reusable in d3.js (So I don't have to write that whole bunch of junk everytime). With closures and getter-setters, d3 charts can be quite reusable and reconfigurable.

```javascript
function chart() {
  var width = 720, // default width
      height = 80; // default height

  function my() {
    // generate chart here, using `width` and `height`
  }

  // This is brilliant. Its a getter if no argument is passed.
  // It is a setter if argument is passed. 
  // But'argument' is another one of those black magic though
  my.width = function(value) {
    if (!arguments.length) return width;
    width = value;
    return my;
  };

  my.height = function(value) {
    if (!arguments.length) return height;
    height = value;
    return my;
  };

  return my;
}

//Instantiate and render chart of width 720, height 80
var myChart = chart()
//Set height 100
myChart.height(100)
//Get height will return 100 instead of 80
myChart.height()
```
[More on reusable charts](https://bost.ocks.org/mike/chart/)

### 01/03/17 - Modern JavaScript

I have much to learn about modern JavaScript in 2017, as many things have changed (for the better, but JavaScript still sucks) since I stopped programming in it for a while.

#### `const` keyword

Does *not* mean that the variable is immutable, just that the *reference* is immutable.

#### `let` keyword

My favourite modern addition. This ensures *block* scope, which is far more saner than the function/global scope that JavaScript has been operating with. A good example will be:

```
for ( let i = 0; i < n; i++ ) {
  ...
}

for ( var j = 0; j < m; j++ ) {
  ...
}

console.log(j) // m-1
console.log(i) // error
```

This ensures that the variable `i` only exists during the `for` loop. Very useful for safe programming. The `let` keyword is reminiscent of the same keyword in ML and Haskell.

#### `for` loops

There's the `for...in` statement that **does not** (I cannot emphasise enough) do what you think it is, i.e. like in Python. This iterates over the **enumerable** properties of an object, **not necessarily in order**. Read this paragraph again.

```
for ( let key in obj ) {
  console.log(obj[key]);
}
```

Note that the iterating variable is the **key**. What happens if you use this for a plain old array?

```
var arr = [100, 200, 300];
for ( let v in arr ) { // using v here, because we all think it's a value, right?
  console.log(v);
}

// 0
// 1
// 2
// what?
```

What if we want Python semantics? Fortunately, there's the `for..of` statement, which iterates over an iterable object (e.g. an array. In JavaScript, an associative array is **not** an array).

```
for ( let value of arr ) {
  console.log(value);
}
```

Extra tip: you cannot have an associative array with numeric keys in JavaScript. This turns it automatically into a numeric array, which can have disastrous consequences:

```
var arr = [];
var id1 = 1;
var id200 = 200;
arr[id1] = "value1";
arr[id200] = "value200";
console.log(arr.length) // definitely not 2
```

The trick is to use `var arr = {}`, i.e. an *object*, rather than declaring it as an array.

### 23/02/17 - git add -p

`git add -p` allows you to stage *parts* of a modified file. This is very useful if you were suddenly overzealous, made many changes, only to realise that you broke the "commit early, commit often" rule.

### 20/02/17 - Adventures in /usr/bin

[Adventures in /usr/bin](http://ablagoev.github.io/linux/adventures/commands/2017/02/19/adventures-in-usr-bin.html) where we can all up our Linux skills 

### 18/02/17 - sudoedit

Once you've set `EDITOR` in your environment (it's `vim`, right? Right?), you can use `sudoedit` to edit files instead of `sudo vim ...`

Why? The first reason is that it keeps your editor customisations (`.vimrc`). The real reason is security: `sudo vim` allows the execution of other commands, which is obviously unsafe. `sudoedit` coupled with your favourite editor i.e. `vim` does not allow this to happen.

### 14/02/17 - Diner's Dilemma and German Tank Problem

* [Unscrupulous Diner's Dilemma](https://en.wikipedia.org/wiki/Unscrupulous_diner%27s_dilemma), also known as n-way Prisoner's Dilemma
* [German Tank Problem](https://en.wikipedia.org/wiki/German_tank_problem)

### 04/02/17 - Some Linux Command lines commands

- Print the last 5 lines of test.txt: `tail -5 test.txt`
- Print all the lines in test.txt containing the string "HELLO": `grep HELLO test.txt`
- Print the number of lines in test.txt containing the string "HELLO": `grep HELLO test.txt | wc -l`
- Print all files in the directory that contain the string "HELLO": `grep -rl * -e HELLO` 
- Print all file names that starts with test: `find . -name "test*"
- Print the contents of test.txt, sorted: `sort test.txt`
- Print number sequence 1 to 100 separated by space: echo {1..100}
- Print all the files in current directory recursively without leading directory path: `find . -type f -printf "%f\n"
- Replace spaces in file name with . in all the files in current directory: `find . -type f -printf "%f\n" | xargs -0 -T {} echo {} | tr ' ' '.'`
- Print the 15th line of the file test.txt: `sed '25q;d' test.txt`

### 29/01/17 - Bottle Python Framework

You can make web microservices with `bottle.py`. That simulates a server. 

```
@get("/")
def index():
	response.content_type = "application/json"
	return json.dumps([{"date":"08-01-2017", "id":1},{"date":"12-01-2017", "id":2},{"date":"19-01-2017","id":3}])

run(host="0.0.0.0", port=4242, reloader=False, debug=False, quiet=True)
```
Then head to `localhost:4242` and you will see the JSON string returned as output.

[More Bottle](https://bottlepy.org/docs/dev/)

### 18/01/17 - System Timings and Services 

THe system time bug bites. When you change the system timing, remember to update the services that use the timing, for an example, cron jobs and sys logs. That's done by: 
```
$sudo service cron restart
$sudo service rsyslog restart
```

Just to be sure that your system time and the cron jobs are running at the respective time, you can always do something like: 
`while [ True ]; do date; cat /var/log/syslog | tail -n 20; sleep 1; done`

### 14/01/17 - Some lessons

- If you are running a network of virtual machines on the same host, they are using a *virtual* switch. The packets never go out of the physical interface. The good thing is that you can throttle the bandwidth and latency to simulate a real network.
- Check everything. Time was wasted because something was not configured properly, but we *thought* it was configured properly and concluded that this configuration doesn't work. We were wrong. I hate XML files. And it was embarrassing.
- If your test conditions are wrong, your test is invalid by definition. A test may work at a small scale but fail at a large scale because you didn't cater for it.
- If you are doing a load test, check all resource usage. CPU, memory, and network. Measure while *slowly increasing* the load, not opening up the graphs when it's at full load and things start burning. This allows you to observe resource usage as a function of load.
- `gnome-system-monitor` is rubbish at measuring throughput. `iftop` and `iperf` are excellent. Trust numbers, not graphs.
- `tc` can add latency, but not throttle bandwidth.
- If there is a acknowledgement storm, the amount of traffic coming back must be large. It wasn't. So there wasn't an acknowledgement storm.
- Don't manually increase your TCP window sizes. Check if your operating system comes with TCP window scaling.
- Use a calculator for calculating network stuff. Conversion from bytes to bits and back is not mentally sustainable.
- Throughput = TCP window size / round trip time.
- Bursty traffic is *very different* from continuous, sustained traffic. The above equation will not work.
- Don't trust ping for RTT. Use Wireshark + `tcptrace`.
- Don't rely on the payload checksum in the TCP header. It's wrong. Google it.
- The best way to test if certain OS settings have been really changed is to restart the machine and check.
- JVisualVM is the undisputed king of monitoring a Java program.
- If something fails to start up, delete all remnants of a prevous invocation (the hard part is to know that they even exist), and start it up again. It's a bad sign if a system cannot handle the existence of remnants, or throws enigmatic error messages that lead users down the wrong path.
- If you have a network issue, especially if you are using readable addresses, the chances are very high that it's because of DNS.

### 13/01/17 - String concatenation in bash script

In bash script, you can concatenate strings with the `+=` operator. If they have new lines, you need to print new lines via echo with the `-e` flag. If you want to print new lines to a txt file, use `"${aString}"`.

```
aString=""
aString+="one\n"
aString+="two"
echo "${aString}" > aFile.txt
```

### 09/01/17 - HTML5 Speech Synthesis API

In HTML5, web apps can talk using the `SpeechSynthesisUtterance`. Like seriously. And there's a whole bunch of different languages and voices. It's really cool, like: 

```
var msg = new SpeechSynthesisUtterance('Hello World');
window.speechSynthesis.speak(msg);
```

Downside, you need to be able to host the web app, i.e. it cannot be a static HTML page. [Google tells you more about the API](https://developers.google.com/web/updates/2014/01/Web-apps-that-talk-Introduction-to-the-Speech-Synthesis-API)

### 02/01/17 - i++ vs ++i

[Is there a performance difference between i++ and ++i in C?](https://stackoverflow.com/questions/24886/is-there-a-performance-difference-between-i-and-i-in-c)

No. But (emphasis mine):

> So the question one should be asking is not which of these two operations is faster, it is **which of these two operations expresses more accurately what you are trying to accomplish**. I submit that if you are not using the value of the expression, there is never a reason to use i++ instead of ++i, because there is never a reason to copy the value of a variable, increment the variable, and then throw the copy away.

### 31/12/16 - Selecting the k-th largest item in O(n)

Let's suppose you need to find the k-th largest item in an array A[0...n-1]. This is trivial for k=n (smallest item) and k=1 (largest item) so we ignore those cases. How do you find the k-th largest item in O(n)?

Solution: Median-of-medians, which is based off quicksort.

The general idea is this - quicksort until the pivot is in the k-th position. If the pivot is not in the k-th position, quicksort the subarray that contains the k-th position.

Clearly, this does not work if pivot selection does not eliminate a proportion of the search array each iteration (This case degenerates to O(n<sup>2</sup>)). Otherwise, this approach is O(n). Here's how median-of-medians selects a suitable pivot to ensure deterministic O(n):

```
fn select_kth_largest(k, array):
   ...

fn select_pivot(subarray):
    if length of subarray <= 5,
        find true median using some sorting algo and return that.
    otherwise
        group_medians = []
        
        foreach group of 5 elements in subarray
            find true median in group.
            add true median to group_medians
        
        return select_kth_largest(# of groups / 2, group_medians) // I.e. find median-of-medians by calling itself.
```

Why does this work?

Observe that at least half of the groups have medians that are less than or equal to the median-of-medians. There are (subarray length / 5) groups, so the number of group medians is 20% of the subarray length. For each group, at least 3 items are less than or equal to their respective group median. Therefore 3 * 10% of the subarray will be less than or equal to the median-of-medians. This means the selected pivot will eliminate at least 30% of the search array each iteration.

### 30/12/16 - Building a reliable protocol

I found myself having to investigate why an implementation of a "reliable protocol" was losing packets like crazy. It turns out that building a reliable protocol on top of an unreliable one is extremely tricky, as I should have realised in my networking classes, and I encourage everyone to view network engineers with greater respect.

TCP is perhaps the most famous reliable protocol of all. But there are *many versions* of TCP, some very recent and some probably in development to adapt to the changing network needs of today. So it is *hard* to develop a reliable protocol, as I realised while investigating and trying to recall my networking stuff from 2.5 years ago.

Almost everything in computer science is about trade-offs. In this case, it's reliability versus performance. A fire-and-forget protocol is really fast and also really unreliable; requiring high reliability requires huge buffers and multiple round-trips for all those acknowledgements and retries, and you will still never get 100% reliability (this is something one must accept from the very beginning).

Here are my observations of what to try:

0. Don't send so many things in the first place
1. Acknowledgements
2. Sequence numbers for ordering (you do want your packets to be ordered, right?)
3. Buffers (i.e. sliding windows in TCP parlance) on transmitter and receiver to support the above
4. Congestion control (max number of unacknowledged packets in flight) to prevent choking the network
5. Flow control (changing the size of sliding windows on both ends in response to receiver's ability to receive) to make sure receiver doesn't get swamped
6. Buy better hardware

As more steps are taken to build The Reliable Protocol, there are extra processing costs and memory costs (but if you can, as I always say, "throw money at the problem"...) in exchange for higher reliability.

Implementing 1 to 4 was sufficient for low loads. A high load destroyed our hopes and wishes because it turns out that we were swamping the poor receiver (or even a poor switch in the middle!) without throttling our sending rate. It is quite counter-intuitive that reducing sending rate increases performance but that's networking engineering...

Implementing flow control showed *no lost packets*, but we were probably just lucky because it could handle our maximum expected load. With some imagination, it is easy to see that there is a point beyond which even flow control cannot help, and it's time to buy better hardware.

Of course, if you can afford not to send so many things in the first place, don't!

Addendum: raw Markdown shows that it's steps 0 to 6. Step 6 was meant to be step `n` but then it wouldn't be rendered properly as a list.

### 29/12/16 - SSH without password in GNOME terminal

If you have put your public key into the server, yet when you try to ssh from a GNOME terminal (i.e. CentOS6) and it doesn't work, add `SSH_AUTH_SOCK=0` before the ssh command. So type `SSH_AUTH_SOCK=0 ssh admin@<IPADDRESS>`. THat should get you in without password

  ### 24/12/16 - More tar

`tar cf` produces an *uncompressed* archive, while `tar czf` produces an archive compressed by `gzip`. It's quite obvious that I don't use `tar` much to compress stuff, but `tar xf` has become muscle memory.

### 22/12/16 - Awk for removing the last column of a file

There was previously cut and paste, here's the awk version for removing the last column of a (space separated) file. `awk '{$(NF--)=""; print $0}' <in >out`. First note the absence of spaces after the carets. Then, NF means number of fields in the line. So $(NF--) means the last field in the line; and you set that to "". Then you print $0 which is the line. 

### 22/12/16 - More jq

So in jq, you can pipe your JSON child element to string operations, but you have to use the jq operations. Here's one: `jq '.[] | {date: (.source.timestamp | gsub("T"; " ") | rtrimstr("+08.00"))}`. Okay, so my date is stored as a string of timestamp in the format 2016-22-12T09:28:00+08.00. First I pipe it to an operation called `gsub` that changes the `T` to a space; note the semicolon (`;`) and double quotes (`"`). Then i pipe it to an operation that trims the string from the right side for the timestamp. Then I will get `{date: 2016-22-12 09:00:00}` for my output.

Now just for the sake of it, I can trim the `+08:00` from a string in bash as such: `echo "2016-22-12T09:28:00+08:00 | sed "s@+08:00@@g"`. Double quotes make a difference. For another time.

### 21/12/16 - Maximum subarray in O(n)

Problem: Suppose you have an array A[0...n-1] and you wish to find a subarray S[l, r] such that sum(S[l...r]) is maximal.

Solution: Kadane's algorithm, which uses dynamic programming

```
Let DP[i - 1] be the maximum subarray that ends at i - 1.
Then DP[i] = max(DP[i - 1] + A[i], A[i])
Then the maximal subarray over A is simply max(DP[0...n - 1]).
```

This algorithm uses O(n) time, O(1) space.

Quiz: How do you find the k-th maximal subarray in O(n + k)?

### 21/12/16 - History timestamps

The `history` command is incredibly useful, but it doesn't come with timestamps... or does it?

If you set `HISTTIMEFORMAT='%F %T '` (note the trailing space!), `history` will record timestamps. Why is this not the default?

### 21/12/16 - Elastic Search Date Queries

ES and Kibana are strange creatures. Assume you have a field in your index that is called `timestamp` of type `date`. If you're searching it 1 month back on Kibana Query bar, use `query: timestamp: [now-1M TO now]`. However, if you send a curl GET request, you can use the following cumbersome JSON format:
```
{
"
query": {
 "range": {
   "timestamp": {
    "gte": "now-1M/M"
   }
  }
}
}
```

[Moer on Range queries](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-range-query.html)

### 21/12/16 - Send mail 

If you have a mail server installed, you can use the `$sendmail` command to send emails.<send mail command> <reciepient email> <subject> <message>

`/usr/sbin/sendmail user01@mail.me.com "Subject: [Read] SendMail" "Hello Sendmail!"`

For a more complex mail liner with a Subject (anything with spaces must be in double quotes), and the message body from a text file, you can use the following command. -t flag denotes the to field while -F flag denotes the from field (left as default user if not specified).
`echo "Subject: [Read] SendMail" | cat - message.txt | /usr/sbin/sendmail -t user01@mail.me.com -F user02@mail.me.com`

[More on Sendmail](https://jpsoft.com/help/sendmail.htm)

### 20/12/16 - More Vim tips

`:x` - save the file if it has changed, and quit (`:wq` or `ZZ` saves the file in any case, and quits)

`%` - find matching brace/bracket/parentheses

`~` - toggle case

`<` - indent left

`>` - indent right

`=` - auto-indent a block of code

`;` - repeat previous `f/F/t/T` action

`,` - reverse previous `f/F/t/T` action

`"+` - clipboard register

`100|` - go to column 100 (alternatively, you can use `099l`, which brings the cursor to the first column, then advances left 99 times)

`ma` - mark `a`

`` `a `` - go to mark `a`

`'a` - go to first non-blank character of line of mark `a`

`*` - find next occurrence of word under cursor

`#` - find previous occurrence of word under cursor

`ga` - get hex/octal code of character under cursor (damn useful)

`%s/\%xYY/substitute/g` - replace characters with hex code `YY` with `substitute` in the whole file

`s/a/b/gc` - substitute `a` with `b` with **c**onfirmation (whoa...)

### 20/12/16 - pgrep and pkill

"I want to kill a Java process, but I don't know the process ID. So I run `ps aux | grep java | less` and use my eyeballs..."

Enter `pgrep java` (or `pgrep -a` to show the command). Or `pgrep -u darren` for all processes by `darren`. If you want to kill *one* particular Java process, you need to use `kill 1234`.

On the other hand, if you want to kill *all* Java processes, there is  `pkill java`, which works the same way and is arguably more powerful (and dangerous! See [here](https://unix.stackexchange.com/questions/91527/whats-the-difference-between-pkill-and-killall)) than `killall java`. Both can select by user, and have a lot of other options.

### 20/12/16 - Linux jq

Today I learnt that I can parse JSON files in command line with the library [jq](https://stedolan.github.io/jq/). PRetty useful for parsing structured JSON data rather than using loads of `sed` and `awk` and going bersek. If you download the binary, you can simply add it to your system by `chmod +x jq && sudo cp jq /usr/bin`

If you have an array of JSON, like this:
```
{'hits':
[
{name: 'Ali', age: 23},
]name: 'Bob', age: 25}
]
}
```
you can iterate them through using jq by: `jq '.hits[]`, where the ending `[]` means iterate through all items in the array. Note the `.` before the hits, because that's how jq identify fields. It's important Now if you want to collect the items and output to another JSON object, do: `jq '.hits[] | {author: .name, yearsOld: .age}`. That outputs: 
```
{author: 'Ali', yearsOld: 23}, {author: 'Bob', yearsOld: 25}
```

If you want to put them in an array, add the array braces:  `[jq '.hits[] | {author: .name, yearsOld: .age}]`. This outputs:
```
[{author: 'Ali', yearsOld: 23}, {author: 'Bob', yearsOld: 25}]
```

### 17/12/16 - Debounce in Javascript

Sometimes you don't want to invoke a function all the time. You can try looking at [Demystifying Debounce](https://john-dugan.com/javascript-debounce/) to understand a `debounce` function that might be helpful.

### 13/12/16 - Cut and paste

`cut -f2 test.csv` extracts the second column of a tab-delimited CSV file. `cut -d, -f2 test.csv` does the same for a comma-delimited CSV file.

I realised that CSV means "comma-separated".

Anyway, `paste -d'\t' file1.csv file2.csv` concatenates both files row-wise with a tab.

### 04/12/16 - Bash add comma at the end of each line

`sed 's/$/,/g' 1.js > 2.js`

I know my file names are ridiculous. `g` is for global, that is add for every line. `,` is the character that you want to add to the end (`$`) of each line. I need to level up my regular expressions.

### 21/11/16 - :-!!! in C is not just a nice little emoticon symbol

It's actually in the Linux kernel to effectively check whether the expression e can be evaluated to be 0, and if not, to fail the build.[Read more here](http://stackoverflow.com/questions/9229601/what-is-in-c-code).

### 21/11/16 - Deploying a static app to Heroku

TIL, we can't deploy a static HTML app to Heroku. But there's a way to mock it as a PHP app. [Here's how](https://gist.github.com/wh1tney/2ad13aa5fbdd83f6a489). THis method may come in useful in the future to mock other apps similarly. 

### 11/11/16 - cron jobs 

If you need to write a cronjob, write it in `crontab -e`. If the job needs to be run as root, use `sudo crontab -e`. 

Here's some abbreviations on writing cronjobs. If the job starts on system reboot, add `@reboot` in front will do the job. i.e. `@reboot ./runThisScript.sh`. If it's hourly, `@hourly` flag works. 

Cron jobs are written as `m h dom mon dow command`, or minute, hour, day of month, month of year, day of week, command. If you want to repeat for every of that time unit, put \*. 
`0 5 * * * ./runThisScript.sh` would be minute 0 of hour 5, every day, every month, every day of week, execute `./runThisScript.sh`. 

If you want to run a cron job every 5 minutes, do `*/5 * * * * ./runThisScript.sh`. Every five hours would be: `0 */5 * * * ./runThisScript.sh`. So `0 0 18 Oct * echo Happy Birthday` would print Happy Birthday on the console at 0000hrs on 18 October. 

If you want a cron job that opens a terminal every minute to run a certain script, that's `* * * * * export DISPLAY=:0 && /usr/bin/gnome-terminal -e "./runThisScript.sh"`

`cron` is named after the Greek work chronos, which means time. [Here's some reading when you have time](http://www.computerhope.com/unix/ucrontab.htm)

### 10/11/16 - find and xargs

`find` and `xargs` have very weird syntax; the number of times I have seen `find . -name X -type f -exec rm -f '{}' \;` as a Stack Exchange example is *far too many*.

Basic `find` is not that hard: `find [directory to search] -name [pattern of file] -type [f for file, d for directory]`. The default action is to print the list of files (forceable with `-print`); a fancier and hopefully self-explanatory way is `-ls`.

`xargs` constructs commands from standard input. My use case today was to calculate the number of lines of *thousands* of files in *nested* subdirectories of a certain directory. `wc -l *` won't cut it. Enter `find` and `xargs`:

```
find . -name *.txt | xargs wc -l
```

The Wikipedia pages for `find` and `xargs` go into tricky cases in fine detail, and I highly recommend reading them. Because one day you may find yourself in such a situation.

And what's `-exec`? Ignore that, stick with `xargs`.

### 10/11/16 - sudoers

The proper way to edit `/etc/sudoers` is to use `visudo`, which strangely does not necessarily launch good ol' `vi` but the default editor of choice (`nano` on Debian).

"But I *just* want to give X sudo powers! What's up with the syntax?"

There is likely to be a `sudo` group in `/etc/sudoers`, so you can just add X to the `sudo` group with `gpasswd -a X sudo`.

### 09/11/16 - cp only subfolders

You can copy only the sub folders with `cp -r folderA/* folderB`. 

If you want to copy and replace folders, use the `-al` flag. Alternatively, `rsync -av source/* dest/*` does the same thing.

### 05/11/16 - Home key in shell

The Home key is a perfectly good substitute for `Ctrl-A` to move the cursor to the start of the line.

Conversely, the End key does the same thing as `Ctrl-E`.

### 03/11/16 - Logging in an Express.js app 

I initially thought that it's quite difficult and possibly impossible to log in an Express.js web app, which has both clients and server side code. But I learnt today through random explorations that you can log the server side to a file using the nodejs package [Winston](http://tostring.it/2014/06/23/advanced-logging-with-nodejs/) which provides colours for different logging levels such as info, warn, fatal, debug and error. For writing the client side to a log file, you can use some middleware called.... logger. Yes, [express.logger()](http://expressjs.com/en/api.html) allows you to redirect the log on the client side to a file. 

### 30/10/16 - tmux

- Start a new session: `tmux`
- Detach from currently attached session: `Ctrl-B d`
- Re-attach a detached session: `tmux attach`
- Split pane horizontally: `Ctrl-B "`
- Split pane vertically: `Ctrl-B %`
- Kill pane: `Ctrl-B x`
- Switch pane: `Ctrl-B o`
- Cycle panes: `Ctrl-B Ctrl-O`
- Navigate panes: `Ctrl-B [arrow key]`
- Re-arrange panes: `Ctrl-B [space]`
- Show pane IDs: `Ctrl-B q`
- Scroll: `Ctrl-B PgUp`, `Ctrl-B PgDn`
- Help I cannot type in my window/pane: `q`
- Create window: `Ctrl-B c`
- Next window: `Ctrl-B n`
- Previous window: `Ctrl-B p`

### 26/10/16 - Introducing Systems Engineering At Enterprise Scale: A Success Story In The Automative Industry

It's a talk by Prof Daniel Krob, the president of Center of Excellence on Systems Architecture, Management, Economy & Strategy (CESAMES).

I learnt you can break a System Engineering into a few levels: 
* Service Engineering - What your system offers to the world
* Component Engineering/ Interface Engineering - What components are required to offer a service 
* Part Engineering - What are the technicalities of the hardware and software to engineer the components, i.e. the nitty gritties

### 24/10/16 - nohup 

To prevent processes from being killed upon logout, `nohup` it. i.e. `nohup python -m SimpleHTTPServer`. 

### 10/10/16 - The Joel Test

[12 Steps to Better Code](http://www.joelonsoftware.com/articles/fog0000000043.html):

1. Do you use source control?
2. Can you make a build in one step?
3. Do you make daily builds?
4. Do you have a bug database?
5. Do you fix bugs before writing new code?
6. Do you have an up-to-date schedule?
7. Do you have a spec?
8. Do programmers have quiet working conditions?
9. Do you use the best tools money can buy?
10. Do you have testers?
11. Do new candidates write code during their interview?
12. Do you do hallway usability testing?

> A score of 12 is perfect, 11 is tolerable, but 10 or lower and you've got serious problems. The truth is that most software organizations are running with a score of 2 or 3, and they need serious help, because companies like Microsoft run at 12 full-time.

My team satisfies 1, 7 (dubious), definitely not 8, a bit of 9, none whatsoever of 10, and 11 will happen the day that I die.

### 10/10/16 - Sorting a comma-delimited CSV file by a certain column

If we want to sort `foo.csv` by the values in its fourth column and then pretty print it in the shell:

```
sort -t',' -nk4 foo.csv | column -s',' -t | less
```

The `-t` option of `sort` and `-s` option of `column` are followed by the delimiter (in this case, a comma).
 
### 08/10/16 - Untar and tar a file

Because I keep forgetting

```
$tar -czf archive.tar.gz /path/to/directory
$tar -xzf archive.tar.gz
```

[And a lot more commands with tar](http://www.howtogeek.com/248780/how-to-compress-and-extract-files-using-the-tar-command-on-linux/)

**Update**: one can save a few keystrokes by using `tar czf` and `tar xf` (both without the hyphen, the latter without `z`). The latter works because the modern versions of tar detect the compression algorithm. See [here](https://www.gnu.org/software/tar/manual/html_section/tar_69.html). 

### 03/10/16 - Apple's first iPhones

[The first iPhones were buggy as hell](http://gizmodo.com/the-iphones-first-demo-was-buggy-as-hell-1441324523 )

> Hours of trial and error had helped the iPhone team develop what engineers called “the golden path,” a specific set of tasks, performed in a specific way and order, that made the phone look as if it worked.

### 01/10/16 - Ping Pong

In case you want to pingtest your network and put a date to the output string and then append that string to a text file:
```
ping 8.8.8.8 | while read poing; do echo $(date):$pong"; done >> pingtest.txt
```
Very useful for `tail -F pingtest.txt` later to check network connectivity. 

### 26/09/16 - NatCat UDP

In other words, *talking between two linux VMs*. Because it is a pain to keep walking up and down but there is an elegant solution.

At the sender side: `netcat -u <host> <port>`. So that's `netcat -u 192.168.101.111 9888`. That's opening a UDP connection to the IP address 192.168.101.111 on port 9888.

At the receiver side: `netcat -ul <port>`. For the sender in the previous paragraph, you would use `netcat -ul 9888` to open a UDP listener at port 9888. So you'll be able to communicate easily. 

Of course `netcat` can be used for way more than this. [Like that](https://www.digitalocean.com/community/tutorials/how-to-use-netcat-to-establish-and-test-tcp-and-udp-connections-on-a-vps)

### 23/09/16 - Turning off IP tables in CentOS6

```
$service iptables save
$service iptables stop
$chkconfig iptables off
```

`chkconfig` saves the configuration. sysadmin skills levelling up... slowly but surely

### 22/09/16 - Replacing words in files

To replace all instances of `apple` with `orange` (technically `apply` can be a regular expression) in `file`, run

```
sed -i 's/apple/orange/g' file
```

The `-i` option means *in-place*. This command is especially useful when combined by `ssh` in a for loop (use case: editing `/etc/hosts` of a machines in a cluster!).

To delete lines containing `re` in `file`, run

```
sed -i '/re/d' file
```

### 18/09/16 - Switching working directories with cd

`cd -` switches you to your previous working directory (`pwd`). Doing it again switches you back!

### 18/09/16 - SSH commands

`ssh user@hostname command` does what it says: it logs in as `user` at `hostname`, executes `command`, and leaves.

For the lazy, having a `~/.ssh/config` file is useful:

```
Host cl
 User cydf2
 HostName sandy.cl.cam.ac.uk
 IdentityFile ~/.ssh/id_rsa
 ServerAliveInterval 60
```

I only have to type `ssh cl`.

### 18/09/16 - Inverse grep

`grep -v pattern` returns lines that *do not* match `pattern`. I had to use it once (and piped it to `wc -l`, of course).

### 18/09/16 - Shell history

We all know `history`. `!42` executes the 42nd entry in `history`. `!!` or `!-1` executes the previous command.

`!*` returns the previous command minus the first word (i.e. just arguments), `!^` returns the first argument of the previous command, and `!$` returns the last argument of the previous command.

### 18/09/16 - Subshells

Let's say you're writing a script which involves a `cd` to some other directory followed by some other commands there. Unfortunately, this will not work.

What needs to be done is:

```
(cd somedir && \
command1 && \
command2 && \
command3)
```

The parentheses causes execution of the commands in a subshell.

### 18/09/16 - File

`file filename` tells you the type of file of `filename`.

### 18/09/16 - Shell variables

```
$ variable=value // no whitespace!
$ export variable
$ export variable=value
$ variable=value command // variable set to value for just this command
```

`set` shows all shell variables. `printenv` shows all exported environment variables.

### 18/09/16 - Job control

`command &` runs the command in the background, with `fg` to bring it into foreground (console) and `Ctrl-Z` (suspend)  followed by `bg` to run it in background. No more opening multiple console windows!

`fg`, `bg`, and `kill` accept process ID, `%+jobnumber`, and `%+commandname` as arguments.

`jobs` shows jobs in the current shell.

### 18/09/16 - Quotes

- Single quotes: suppresses all special character meanings
- Double quotes: suppresses all special character meanings, except for `$`, `\`, and `` ` ``.
- Backslash: suppresses all special character meanings for next character (we all know this already, right?)

In more complicated scripts with variables and string outputs, the use of single quotes, double quotes, and backticks (to evalute commands!) is very important.

I think that we should quote *strictly*, i.e. single quotes for all strings unless there is a need to include variables, special characters, or inline evaluation of commands, in which case double quotes are used.

### 18/09/16 - Expansions

```
$ echo {1..10}
1 2 3 4 5 6 7 8 9 10
$ echo document{A,B,C}.pdf
documentA.pdf documentB.pdf documentC.pdf
$ echo ${HOME}
/home/darren
$ echo $HOME // alternative to ${variablename}
/home/darren
$ echo $(whoami)
darren
$ echo `whoami` // alternative to $(command)
darren
```

### 18/09/16 - Double hyphens

Let's say, for some reason, you have a file that starts with a hyphen, and you want to remove it with `rm`. Unfortunately running `rm` the usual way will probably throw an error because your file name will be parsed as options, not an argument.

`--` signals (in many tools) that subsequent words are arguments, not options, e.g. `rm -- -i` removes the file called `-i`.

### 18/09/16 - File redirection

Most people probably know `>` (send output to file) and `>>` (append output to file). However it must be noted that it is stdout that is sent, not stderr, although both are usually sent to the console.

To send *both* stdout and stderr to the same file, it suffices to add `2>&1` after the first redirection:

```
command > filename 2>&1
```

What's going on? stderr (with file descriptor 2) is sent to (think pointer) stdout (file descriptor 1). File descriptor 0 is stdin.

What if stderr is flooding your console? Use `2>/dev/null`.

### 18/09/16 - Bash sequential commands

A semi-colon executes commands in sequence.

```
command1 ; command2 ; command3
```

A double ampersand (`AND`) or double pipe (`OR`) ([short-circuit evaluation] (https://en.wikipedia.org/wiki/Short-circuit_evaluation)) executes *conditionally* commands in sequence.

```
command1 && command2 // command2 will execute if command1 succeeds
command1 || command2 // command2 will execute if command1 fails
```

### 18/09/16 - Output files in directory in Windows

An equivalent of `ls` in Unix. Shift + Right click on the directory you want and Open Command Window. Then, `dir /b > dir.txt`. dir.txt comes out a list of files in the folder. This is windows. In Linux its just `ls > dir.txt`. Sweet and Simple.

### 15/09/16 - :p in bash

:p is not an emoticon. Type `!sa:p` and you'll see what the previous command you executed that starts with sa is. `:p`, that's colon with a lower case p, would show you the command then you can press the enter key if that's what you want to execute.

Also, [Advancing in the Bash Shell](http://samrowe.com/wordpress/advancing-in-the-bash-shell/)

### 15/09/16 - su hyphen

The difference between `su user` and `su - user` ([ArchWiki](
https://wiki.archlinux.org/index.php/Su)):

> The hyphen has two effects:
>
> 1) switches from the current directory to the home directory of the new user (e.g., to /root in the case of the root user) by logging in as that user
>
> 2) changes the environmental variables to those of the new user as dictated by their ~/.bashrc. That is, if the first argument to su is a hyphen, the current directory and environment will be changed to what would be expected if the new user had actually logged on to a new session (rather than just taking over an existing session).

In fact, avoid `su` if you can `sudo`.

### 14/09/16 - Local NTP server

If you need to keep the clocks of your network computers synchronised, you need to designate one computer as the reference (NTP server); the others are NTP clients. I'm assuming this network is not connected to the Internet, or to put it simply, is not connected to any authoritative low-stratum NTP server like `time.nist.gov`.

#### NTP server

Add the following in `/etc/ntp.conf`:
```
restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap nopeer
...
server 127.127.1.0
fudge 127.127.1.0 stratum 10
```

modifying `192.168.1.0` and `255.255.255.0` to suit your network. The magic IP address is `127.127.1.0`; leave that alone.

Without the last two lines, any connecting clients will see that your server is stratum 16, i.e. lousy.

#### NTP client

Add the following in `/etc/ntp.conf`:
```
server XXX.XXX.XXX.XXX
```

where `XXX.XXX.XXX.XXX` is the IP address (or hostname) of your NTP server.

On the server and client, run `ntpq -p`, `ntpstat`, and `ntpdate -q XXX.XXX.XXX.XXX` for juicy NTP statistics.

### 10/9/16 Watson Developer Cloud and Emotion Analysis

TIL that [Watson Developer Cloud Tone Analyzer](http://www.ibm.com/watson/developercloud/tone-analyzer/api/v3/?python#post-tone) only classifies the tones `Anger, Disgust, Fear, Joy, Sadness`. As compared to the six [Universally Recognised Face Expressions of  Emotions](https://www.kairos.com/blog/the-universally-recognized-facial-expressions-of-emotion) -- Yes I know that Face Emotions and Tone Emotions are slightly different, but for the purposes of this argument, let's leave it first -- Watson does not have surprise. This is because *surprise* is an emotion that is usually very quick, then follower by *fear* or *joy*. 

In summary, TIL that after less than a second of shock, I will be feeling in awe (*joy*) or scared (*fear*). 

###  09/09/16 Writing multiple lines to a file in bash

Due to the need for creation of multiple scripts from one script in bash

```
cat >/home/ubuntu/Desktop/script.sh <<EOL
line 1, ${var1}
line 2, ${var2}

EOL
```

If you don't want the variables to be replaced, surround EOL with single quotes 
```
cat >/home/ubuntu/Desktop/script.sh <<'EOL'
line 1, ${var1}
line 2, ${var2}

EOL
```

### 30/08/16 Verifying RPM packages

Don't have the checksums for a `.rpm` file? Run `rpm -Kv X.rpm` on your favourite RPM-based Linux distro.

### 30/08/16 Transferring files without `scp`

Or you *do* have `scp`, but you're just really lazy to run `ssh-keygen` and stuff.

Go to the folder containing the file you want to share and run `python -m SimpleHTTPServer`. A HTTP server on port 8000 will be created.

Assuming the receiving machine is on the same network, `curl -O 192.168.X.Y:8000/file.extension` will do the job. Yes, you don't have `wget` too.

### 29/08/16 Computing checksums in PowerShell

`Get-FileHash [file] -Algorithm MD5` or `SHA256`

Extra tip: you can open `powershell` in `cmd`. But `bash` is still king!

### 28/08/16 Latency versus Bandwidth

Or "*I Regret Not Paying Attention in My Computer Networks Course*": [It's the Latency, Stupid](https://web.archive.org/web/20141204094820/http://www.stuartcheshire.org/rants/Latency.html)

> Years ago David Cheriton at Stanford taught me something that seemed very obvious at the time -- that if you have a network link with low bandwidth then it's an easy matter of putting several in parallel to make a combined link with higher bandwidth, but if you have a network link with bad latency then no amount of money can turn any number of them into a link with good latency.

### 27/08/16 - You can regex replace an emoji
In Chrome console
```
> '👪'.replace(/👩/g, '👱')
<- "👪"
```

### 26/08/16 - PanChromatic Images 
[Gis answer](http://gis.stackexchange.com/questions/113896/panchromatic-image-association-with-high-spatial-resolution)

> [remote sensing] A single band image generally displayed as shades of gray.
>
> Panchromatic images are created when the imaging sensor is sensitive to a wide range of wavelengths of light, typically spanning a large part of the visible part of the spectrum.

### 25/08/16 - Patching Apollo 14

Or "*What I Don't Want to Ever Have to Do in My Life*".

#### [NASA](https://www.hq.nasa.gov/alsj/a14/a14AbortDiscrete.html)

> The intermittent short circuit of the ABORT button represented the most dramatic moment for the MIT computer folks in the entire Apollo program. They had between three and four hours to find a way for the computer to ignore it, test the fix at Grumman, then test it in Houston before sending it to the crew in time for PDI .

#### [Wikipedia](https://en.wikipedia.org/wiki/Apollo_14#Launch_and_flight_to_lunar_orbit)

> If the problem recurred after the descent engine fired, the computer would think the signal was real and would initiate an auto-abort, causing the ascent stage to separate from the descent stage and climb back into orbit.
>
> NASA and the software teams at the Massachusetts Institute of Technology scrambled to find a solution, and determined the fix would involve reprogramming the flight software to ignore the false signal. The software modifications were transmitted to the crew via voice communication, and Mitchell manually entered the changes (amounting to over 80 keystrokes on the LM computer pad) just in time.

### 25/08/16 - Building resilient systems

#### [Netflix Chaos Monkey](http://techblog.netflix.com/2015/09/chaos-engineering-upgraded.html)

> Several years ago we introduced a tool called Chaos Monkey. This service pseudo-randomly plucks a server from our production deployment on AWS and kills it. At the time we were met with incredulity and skepticism. Are we crazy? In production?!?
>
> ...
>
>  Building on the success of Chaos Monkey, we looked at an extreme case of infrastructure failure. We built Chaos Kong, which doesn’t just kill a server. It kills an entire AWS Region.

#### [Space software](https://www.fastcompany.com/28121/they-write-right-stuff)

> Four identical machines, running identical software, pull information from thousands of sensors, make hundreds of milli-second decisions, vote on every decision, check with each other 250 times a second. A fifth computer, with different software, stands by to take control should the other four malfunction.
>
> ...
>
> This software never crashes. It never needs to be re-booted. This software is bug-free. It is perfect, as perfect as human beings have achieved. Consider these stats : the last three versions of the program — each 420,000 lines long-had just one error each. The last 11 versions of this software had a total of 17 errors.
>
> ...
>
> The group's most important creation is not the perfect software they write — it's the process they invented that writes the perfect software.
>
> ...
>
> The central group breaks down into two key teams: the coders - the people who sit and write code — and the verifiers — the people who try to find flaws in the code.

### 24/08/16 - Visual Studio & Eclipse shortcuts
| Function        | Visual Studio           | Eclipse  |
| ------------- |:-------------:| -----:|
| Go to method | F12 | F3 |
| Run     | F5 | Ctrl + F11 |
| Run with Debugging     | F5     |   F11 |
| Stop  | Shift + F5 | Stop button |
| Continue | F5      |    F8 |
| Step Into | F11 | F5 |
| Step Over | F10 | F6 |
| Step Out  | Shift + F11 | F7 |

### 23/08/16 - Bash keyboard shortcuts

These are very useful when typing long commands.

`Ctrl-C` - cancels current input, and yes it also kills the current process

`Ctrl-L` - clears screen

`Ctrl-U` - deletes everything from cursor to start of line

`Ctrl-K` - deletes everything from cursor to end of line

`Ctrl-A` - moves cursor to start of line

`Ctrl-E` - moves cursor to end of line

`Ctrl-W` - deletes previous word

#### "But I will never use this!"

Arrow keys are for the weak, but I'm still weak for now because I don't use Vi mode in Bash. But I digress.

`Ctrl-C` is not a great command because it creates a new prompt (hence ugly). I use `Ctrl-E` (move cursor to end of line) and `Ctrl-U` to delete the current input. If I'm typing at the end of the line and I want to clear the input, I just use `Ctrl-U`.

`Ctrl-W` is useful if you mistyped an argument. I rarely use `Ctrl-K`, because such situations don't occur often.

`Ctrl-A` is useful if you want to append something to the front of some previous command, say.

"But I can use `!!`" I hear someone scream. Yes, `sudo !!` is very useful. However today I found myself stuck in a SQL shell with no support for `!!`. The stupid way to append `EXPLAIN` to a previous SQL query is to use the left arrow key and traverse back to the very beginning. A smarter way is to use `Ctrl-A` to jump there. The smartest way is still unknown, suspected to be in the mind of some wizened SQL guru in his sunlight-deprived room.

### 21/08/16 - Linux Journey and Vim Game
[Learning Linux Journey](https://linuxjourney.com)
[Vim game](http://vim-adventures.com)

### 16/08/16 - Disassemble a C++ code in Visual Studio 2010
Put a break point. Press F5. Upon break, type CTRL-ALT-D.

### 15/08/16 - Running a script in cron on reboot
- Edit cron commands: `crontab -e`
- Add `@reboot /usr/bin/python /home/user/Desktop/test.py` in the file to run
- Reboot runs on startup and reboot

### 15/08/16 - Echo new line and escape characters in batch files 
`echo.` for new line. `^` is escape character, i.e. `^\`

### 12/8/16 - Disable IP Tables in CentOS
```
$sudo chkconfig iptables off
```

### 13/06/16 - Add a local apt-mirror to Ubuntu
Edit `/etc/apt/sources.list` to change the Ubuntu server with your own IP Address server

### 02/07/16 - Mount a Windows Share in Linux
1. Create <FOLDER_NAME> at C:\
2. Right click > Share With... > Everybody. Give Read/Write permissions
3. Create folder in Linux at path, <LINUX_FOLDER>
4. Mount the drive (Note the lack of spacing after the commas)
```
$sudo mount -t cifs //<IP_ADDRESS>/<FOLDER_NAME> <FOLDER_IN_LINUX>
-o username=<WINDOWS_USERNAME>,password=<WINDOWS_PASSWORD>,
uid=<LINUX_USER>,gid=<LINUX_USER>
```
Example:
```
$sudo mount -t cifs //192.168.14.91/kibana /home/super/Desktop/kibana
-o username=lynnette,password=lynnette,
uid=lynnette,gid=lynnette
```

Note: If you don''t set uid, gid, the folder will be mounted as `root`, and you will need `root` privileges to read/ write to it.

### 04/07/16 - Setting up Git Repo on a Server

```
$sudo apt-get install git-core
$mkdir hellogit.git
$cd hellogit.git
$git init --bare
```

### 04/07/16 - Find all files in which contents contains a specific string
```
$grep -rnl <DIRECTORY> -e <STRING_NAME>
$grep -rnl './' -e '192.168'
```

### 04/07/16 - Which version of Linux is running
```
$cat /etc/system-release
OR (For Ubuntu)
$lsb_release -d
```

### 04/07/16 - Disable Cross Origin Policy in Firefox
In the browser bar, type `about:config`.

Type: `security.fileuri.strict_origin_policy` and set to `False`

### 04/07/16 - Yum pointing to local repository mirror
Yum is a software management system for CentOS systems.
It is similar to aptitude for Ubuntu systems.

You can set up a yum local repository (I don''t know how),
but when you do any `yum install`, you can only install from the compatible versions.
For example, CentOS 6 can only install packages from a mirror that contains CentOS 6 packages
and not CentOS 7 packages.

* `$sudo vim /etc/yum.repos.d/CentOS-Base.repo`
* Comment out all `mirrorlist=http://mirrorlist.centos.org`...
and `baseurl=http://mirror.centos.org/`...
* Add instead your own url, i.e. `baseurl=http://192.168.201.203/repos/centos/6/8/`
* Just keep [base], the rest can delete

### 04/07/16 - Samba shared folder in Linux
1. Install samba: `$sudo yum install samba`
2. Set password for samba: `$sudo smbpasswd -a <USER_NAME>`
3. Create a directory to be shared: `mkdir /home/<USER_NAME>/<FOLDER_NAME>`
4. Edit samba config file:

```
$sudo vim /etc/samba/smb.conf

Add to the end of the file:
[<NAME_OF_SHARE>]
path = /home/<USER_NAME>/<FOLDER_NAME>
guest ok = yes
read only = no
```
Note that <NAME_OF_SHARE> should be less than 12 characters

An example would be:

```
[sharedInd]
path = /home/super/Desktop/sharedInd
guest ok = yes
```

5. Restart samba service: `$sudo smbd reload` for CentOS,
`$sudo service smbd restart` for Ubuntu
6. Test whether the `smb.conf` has any syntax errors: `testparm`
7. Opening the Linux samba share on Windows:
`//<IP_ADDRESS>/<FOLDER_NAME>`, i.e. `//192.168.201.108/sharedInd`
Note that the full path is not required; the folder will be automatically detected
8. Mounting the shared folder on another Linux version (02/07/16 - Mount a Windows Share in Linux)

### 04/07/16 - Kibana, Font Awesome, Bootstrap versions
Kibana 3 uses [Font Awesome 3.2.1](fontawesome.io/3.2.1/icons/), Bootstrap v2.2.1

### 07/07/16 - Windows 10 startup program
If you want to put files (i.e. `.bat`) files to run on Windows startup
1. Windows Key + R
2. Type: shell:startup
3. Put your scripts in the startup folder that opens

### 11/07/16 - Home IP Address
On windows, HOME is `127.0.0.1`. On Linux, HOME is `0.0.0.0`

### 11/07/16 - Download a node package to install offline
1. On Internet machine, `npm install --no-bin-link <PACKAGE_NAME>`
2. `npm pack` to get a tarball.
3. On non-internet machine, `npm install <tarball name>`
OR
2. Copy out the `node_modules` with `.zip/ .rar/ .tar`
3. Extract the bundle in offline environment

### 12/07/16 - Easily print Github markdown
https://gitprint.com

### 12/07/16 - Update a file in .jar file
`$zip -u stuff.jar file.txt`

### 12/07/16 - Update a jar file
`zip -u <JAR_FILE_NAME>.jar <FILE_NAME>`

For example:
`zip -u ParseNIndexCopy.jar parser.properties`

### 12/07/16 - Explode and compact a war file
Explode war file: `jar -xvf A.war`
Compact everything in this directory to a war file: `jar -cvf A.war .`

### 12/07/16 - Copy in Linux and force overwrite
`\cp <SOURCE> <DESTINATION>`

### 13/07/16 - A Gulp Application
(Glup.js)[http://gulpjs.com/] helps automate your web workflow to watch your files for changes and build them. More information can be found on the (Github Repo)[https://github.com/gulpjs/gulp/blob/master/docs/getting-started.md]

```
$npm install -g gulp
$gulp start
```

### 13/07/16 - Jade template Engine
(Jade)[http://jade-lang.com/tutorial/] is an interesting HTML template engine. You don't have to add HTML tags but it is white space sensitive.

### 20/7/16 - To Add an entry into /etc/fstab
You need to use the following format:
```
string="echo $SHAREDFOLDER $SHAREDFOLDERLOCALMOUNT cifs username=$WINDOWSSHAREUSER,password=$WINDOWSSHAREPASSWORD,uid=super,gid=super  >> /etc/fstab"

sudo sh -c "$string"
```

Because if you just `sudo echo <STRING> >> /etc/fstab`,
you will get permission denied. This is because sudo elevates
your permission but >> uses reduced permissions again.

### 26/7/16 - UNIX tail -f vs tail -F
From man page 

With --follow (-f), tail defaults to following the file descriptor, which means that even if a tail'ed file is renamed, tail will continue to track its end. This default behavior is not desirable when you really want to track the actual name of the file, not the file descriptor (e.g., log rotation). Use --follow=name in that case. That causes tail to track the named file in a way that accommodates renaming, removal and creation.

### 26/7/16 - Tail -f a log file from a server 
`ssh user@serverIP sudo tail -f <FILE_NAME>`

If ssh failed due to tty allocation: 
`ssh -t user@serverIP sudo tail -f <FILE_NAME>`

### 27/7/16 - Windows computer get Serial Number
Go to command prompt. Type: `wmic bios get serialnumber`

### 2/8/16 - Bash previous command substitution

`^x^y` to replace first instance of `x` with `y` in previous command.
`!!:gs/x/y/` to replace all instances of `x` with `y` in previous command.

```
$ ./a.out < B-small-practice.in > B-small-practice.out
$ !!:gs/small/large/
  ./a.out < B-large-practice.in > B-large-practice.out
```

### 3/8/16 - Git tree in terminal

```
git log --graph --oneline --all
```

### 3/8/16 - Git clone with username and password
```
git clone https://username:password@github.com/username/repository.git
```

### 4/8/16 - Bash loop through all directories
```
for d in */ ; do
    echo $d
done
```

### 8/8/16 - Installing g++, Git, Vim, and Bash on Windows

- Install Git for Windows to get Git Bash (includes Bash, basic Unix tools and vim, reads `.bashrc` and `.vimrc`)
- Install gVim for a better Windows Vim (reads `.vimrc` and `.gvimrc`)
- Install [Nuwen's MinGW Distro (without Git)](https://nuwen.net/mingw.html) for g++
- Add `PATH=$PATH:c/MinGW/bin` to `.bashrc` to use MinGW binaries in Git Bash (default MinGW shell is terrible)

### 8/8/16 - Mount windows share into /etc/fstab
- Windows share at C:\, i.e. <IP_ADDRESS>/<SHARED_FOLDER_NAME>
- Folder created in Linux machine at <LINUX_SHARE_FOLDER_PATH>
```
$sudo vim /etc/fstab

add to end of file

//<IP_ADDRESS>/<SHARED_FOLDER_NAME> <LINUX_SHARE_FOLDER_PATH> cifs username=username,password=password,file_mod=0777,dir_mod=0777
```
- Eg: `//192.168.201.91/neo4j /home/super/Desktop/mountFolder cifs username=lynnette,password=lynnette,file_mod=0777,dir_mod=0777`
- On Ubuntu you can `sudo mount -a` to reboot, CentOS just reboot
- [Ubuntu's Guide to mounting Window's share](https://help.ubuntu.com/community/MountWindowsSharesPermanently)
- [Wiki guide to fstab](https://en.wikipedia.org/wiki/Fstab)

