# Strata DataScience Meet Up 2017

It's been a year!! 

### Zen Desk
* [The Question Answering Problem](https://en.wikipedia.org/wiki/Question_answering)
* [Bidirectional LSTM](https://machinelearningmastery.com/develop-bidirectional-lstm-sequence-classification-python-keras/)
* Tensorflow now has [Gradient Clipping](https://www.tensorflow.org/versions/r0.12/api_docs/python/train/gradient_clipping)

### Tensorflow on Machine Learning 
* Author: [@greenexecutive](https://twitter.com/greenexecutive)
* [Counterpoint by Convolution](https://ismir2017.smcnus.org/wp-content/uploads/2017/10/187_Paper.pdf) 
* [Sketch-RNN](https://magenta.tensorflow.org/assets/sketch_rnn_demo/index.html) generalises not memorises using latent space interpolation
* Neural photo editing with introspective adversarial networks 
* Deep Learning on anonymized datasets by [@yufengG](https://twitter.com/yufengG), who also has a channel for his AI adventures [here](bit.ly/ai-adventures)

### Considerations for data at Speed 
* Speaker: Ted Malaska, Blizzard BNet Architect 
* Types of speed: latency, on board data feeds, time to find data, time for new data submitted 

#### 1. Everything is strongly typed 
* Reasons: Easier access, better compression, better regulation (GDPR, DPO), relationships between groups 

#### 2. No code to make new table/ new data feed 
* The deepest code is probably JSON
* The schema keeps evolving, but it wont break existing joins of tables
* Optimise the ways of viewing, Make onboarding simple to register, Make defining new data simple 
* Full traceability of data: where is the data going, where did the data come from, retention of data 
  * All data that goes into the system is tagged, so one can identify tables, i.e. what to join to get value 
  
#### 3. Destinations matter: Classification of storage systems
* Pick the right data store for your problem
* File based, NoSQL: deduplication at file level, good for long time trending machine learning 
* NoSQL-SQL: e.g. COckroachDb
* Time series databases: good for streaming
* Document based databases: e.g. ElasticSearch, Solr, good for searching 
* Customer 360 approach: understand the experience of a single person to define the database 

#### 4. Monitor, Debug and Protect
* Dynamic scaling, sampling data, catch everything, throw nothing
* Spark/ Flink not so good for dynamic scaling, microservices are better 

#### 5. Partner with data modelling
* Communication is required for open dialogue between many different users
* Balance between speed and producing/ gathering values 
* Tagging, nested solution, know what to sample & aggregate 
