# Social Network Analysis of COVID-19 Vaccination Sentiments in NYC

*Data preparation for analyzing COVID-19 vaccine-related tweets*

COVID-19 vaccine hesitancy is a major public health challenge. It is valuable to understand how anti/pro-vaccination messaging propagate through social media networks. We use time-series analysis and social network analysis to understand how COVID-19 vaccine sentiments propagate through communities in New York City. Our findings demonstrate that anti-vaccine sentiments in NYC are weak and temporarily spike on negative news events. No large network communities strongly correspond to ‘anti-vaccine’ sentiment classes, but we find that sentiment propagation is mediated by several key opinion leaders. 

## Data

Twitter Academic Research API was used to collect tweets from March 20th, 2020 to April 2nd, 2021. We used the keyword ‘NYC’ or 20 mile radius from NYC geographical centroid as the geographical criteria, and predefined a set of vaccine-related keywords and hashtags for the custom query. For each tweet scraped that has a reference tweet, we used the reference tweet’s ID to look up the full tweet. Our dataset contains 270,395 tweets, of which 18.6% are original tweets without quotes, 2.8% have quotes, 13.6% are replies to other’s tweets and 64.9% are retweets.

A significant bottleneck in sentiment analysis of tweets is the lack of labelled data. To perform supervised learning of tweet sentiments, we randomly sampled 1,600 tweets (~0.6%) for hand labelling. Each tweet was labelled twice by a different person and differences were resolved manually. A .csv file containing labelled tweets is available for public use. Most tweets (57.19%) were classified as ‘neutral’, whereas only a small minority (6.13%) had obvious ‘anti-vaccine’ sentiments (Figure 1). As the data is highly skewed, machine classifiers were trained on unbalanced, undersampled and oversampled datasets.

## Sentiment Classification

Previous work studying MMR vaccines ([Yuan & Crooks, 2018](https://dl.acm.org/doi/10.1145/3217804.3217912)) used a linear support vector classifier (SVC) to machine label sentiments, which achieved a best accuracy of 74.64% overall. This is used as a benchmark for our machine classification models. Another recent effort ([Hussain et al., 2021](https://www.jmir.org/2021/4/e26627/)) to classify COVID-19 vaccine sentiments reported a 50-70% accuracy using an ensemble model.

![Baseline Measures for Sentiment Classification](/images/previous_work.png)

### Using VADER Polarity Score Features

We customised NLTK’s in-built VADER polarity score by extracting the automatically generated ‘positive’, ‘negative’ ‘neutral’ and ‘compound’ scores as features. Count of words uniquely associated with sentiments were also used as additional features. We trained Linear SVM, K-Nearest Neighbor, Decision Tree Classifier, and Random Forest Classifier on the unbalanced, undersampled, and oversampled data. The random forest classifier trained on oversampled data outperformed the baseline based on precision, recall, and F1-scores.

### Using Pre-trained BERT Model

Google's BERT is a language representation model that has achieved state-of-the-art performance on multiple benchmarks. We used HuggingFace's transformers library to fine-tune a pretrained BERT model for the sentiment classification task. The optimal parameters are batch size of 16, a learning rate of 0.00005. Trained on oversampled data, the BERT classifier achieved an accuracy of 78.77% and an F1-score of 79.55%, outperforming both the baseline comparison and our random forest classifier. The pre-trained BERT classifier was used to label the remaining 261,840 tweets for further time-series and network analysis.

Metrics | Linear SVM | K-Nearest Neighbors | Decision Tree Classifier | Random Forest Classifier | Pre-trained BERT Classifier
---------- | ---------- | ---------- | ---------- | ---------- | ----------
Accuracy | 64.45% | 68.03% | 70.59% | 72.63% | 78.77%
Precision | 68.28% | 69.06% | 73.61% | 76.68% | 79.90%
Recall | 68.41% | 71.67% | 73.82% | 74.85% | 79.22%
F1-score | 62.21% | 69.97% | 73.41% | 75.06% | 79.55%

## Time-series Analysis

Using the complete dataset of labelled tweets, time-series analysis of sentiment trends was done. Results were comparable to work done by Hussein et al. (2021), and generally corresponded to key global and local vaccine-related events.

![Time-Series Analysis](/images/ts_events.png)
![ARIMA Predictions](/images/ts_compare.png)

## Mentions

The sentiment classification and time-series analysis components were completed as part of a wider study on the `Social Network Analysis of COVID-19 Vaccination Sentiments in New York City` at the Center for Urban Science and Progress, in April 2021. The project members are David Zhao, Jiaqi Dong, Lazarus Chok, Yuanzhe Luo.
