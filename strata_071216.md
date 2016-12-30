# Strata x DSSG - Real World Data Science
Link:            https://www.meetup.com/DataScience-SG-Singapore/events/235591393/
Date:            07 Dec 2016 (Wed)
Time:            1845hrs – 2100hrs
Location:        Suntec Singapore Convention & Exhibition Centre Summit 1

## AGENDA
###	Deploy Spark ML TensorFlow AI Models from Notebook to Hybrid-Cloud
**Chris Fregly, Research Scientist at PipelineIO**

- AWS vs Google Cloud Computing 
- PMML: Predictive Model Markup Language

### Natural language processing using AWS Lambda
**Arun Veettil, Principal Data Scientist, Starbucks**

- Understanding the voice of the customer using NLP in the cloud
- Alert if new theme appears in channels by geolocation, weather
  - When a topic is detected, compare new vector with exisiting set of topics (defined by centroids)
  - Alert when centroid father than current centroid vectors
- Automatic topic prediction
  - Create rules to categorize text 
  - Identify word themes and use them to detect topics
  - LDA as base model in parallel with different parameters chosen randomly: clustering; significant topics: cluster has minimum variance
  - Topics are represented by centroid
  - Gibbs sampling
- Text data on Amazon S3 -> LDA -> Multiple LDA process runs on lambda AWS function -> Expectation Maximisation on topic vectors -> Identify distinct topics -> Identify and alert new topics
- OpenNLP

### How to build a text analytics engine at scale
**Yongzheng (Tiger) Zhang, Senior Staff, Analytics at Linkedin**

- Speaking about [Voices Text Analytics Machine](https://engineering.linkedin.com/blog/2016/06/voices--a-text-analytics-platform-for-understanding-member-feedb) at Linkedin 
- Also, [How Linkedln built a text analytics platform at scale](http://conferences.oreilly.com/strata/strata-ca-2016/public/schedule/detail/47498)

- Scalable and effective platform to understand unstructured text from: member info, social data, customer feedback and survey results
- Relevance solution using binary text classifier; text classification using SVM; topic mining 
- Produces what's trending, products, sentiments, value propositions

- Removes duplicate topics using Jaccard index, synonyms, co-occurence, domain specific words

### Interactive visualizations
**Michael Freeman, University of Washington**
- Visualisation Big Data on Viz Hub
- Devise interaction that allow you to ask questions
- Expose data slowly to require user to consider carefully
- People won't remember what you tell them but will remember how you feel
- [Michael Freeman webpage](http://mfviz.com/)

### OpenStreet Map Talk
**Yantisa Akhadi, Project Manager, Humanitarian OpenStreetMap Team**
- Twitter handle @iyan31
-overpass-turbo.en
-Github: [Working example of Contextual Bandit](https://github.com/allenday/contextual-bandit)

