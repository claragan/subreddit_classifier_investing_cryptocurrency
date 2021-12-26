# Problem Statement
As a new investment company which has two main trading desks - one for traditional securities and another for cryptocurrency, we are looking to automate the monitoring of reddit posts related to investing for new investing leads for both desks. Through this new leads and hot trends, we hope to filter this information to the specific trading desks. As such, we need a model to analyse and categorise the reddit posts for further review & investigation by either the securities or crypto trading desks.

# Background
Reddit [(Source)](https://www.reddit.com/) is home to thousands of communities, endless conversation, and authentic human connection. Whether one is into breaking news, sports, TV fan theories, or a never-ending stream of the internet's cutest animals, there's a community on Reddit for everyone. With approximately 52 million active users and 50 billion monthly views, Reddit is a platform for people of all walks of life to come together and join the communities of their interest.

The subreddits we have chosen to focus on are the Investing and CryptoCurrency subreddits:
    - r/investing [(Source)](https://www.reddit.com/r/investing/)
    - r/CryptoCurrency [(Source)](https://www.reddit.com/r/CryptoCurrency/)
    
In this subreddits & communities, we observe the engagement of authentic daily discussions. The discussions on the Investing is known to accomodate a wide range of financial discussions, from financial news, to market data, and traditonal securities. On the other hand, CryptoCurrency is a digital or virtual currency that is secured by cryptography, which makes it nearly impossible to counterfeit or double-spend. Many cryptocurrencies are decentralized networks based on blockchain technology—a distributed ledger enforced by a disparate network of computers. Hence, this makes CryptoCurrency an emerging form of investment that has been gaining traction for many individuals as well.

# Process
The notebook consists of 3 sections: codes, data and presentation. The codes are divided into 3 folders: data importation & cleaning, exploratory data analysis and modelling.

# Data Importation & Cleaning
1050 non-duplicate & unique posts were created before 25 Oct 2021 GMT +8 2359 from each of the subreddit via the pushshift API. Additionally, empty posts and null posts in the self-text column were not collected.

Regular expression was then used to remove any links, HTML symbols, numbers, special characters and all the text were ensured to be within the Basic Multilingual Plane of Unicode as well. The words in the selftext column was then tokenized, removed of stop words, and then lemmatized. Then, the text for the title of the posts and the selftext of the posts were then combined into 1 'all_text' column. At the end of data cleaning, the only remaining columns were the subreddit column to identify if the post belonged to which subreddit, and the 'all_text' column.

# Data Dictionary
|Feature|Type|Dataset|Description|
|---|---|---|---| 
|subreddit|*int*|combined_df2.csv|The subreddit from Reddit where the data is collected from. The chosen subreddits are r/investing & r/CryptoCurrency and they are indicated by the integers '1' and '0' respectively | 
|all_text|*object*|combined_df2.csv|A list of all the words in the title and selftext for each post in the subreddit| 

# Exploratory Data Analysis
A wordcloud is then generated for each subreddits to give a rough gauge of the words for each subreddits. The word count distrubution plot for each subreddit was generated as well. Finally, ngram visualizations were generated for each subreddits. 
After the ngrams are visualized, for the Count & TF-IDF vectorizers, additional stopwords are added to the existing list of stopwords so that we are able to eliminate words that are so commonly used that they carry very little useful information. Then, the ngrams are populated again for each subreddit and each vectorizer.
The unigrams show that there are common overlapping words for both subreddits. For example, 'buy', 'price' and 'market'. This makes sense as both subredditss involves money. However, for the bigram, there was an increasing divergence in the words. This words allow us to clearly classify which subredddit the post belongs to. For example, 'index funds' is a form of traditional security, while 'shiba inu' is a crypto coin. More organice trends and keywords such as 'roth ira' and 'binance smart chain' were seen in the ngrams as well which show where the latest interest in the subreddits lies in.

# Modelling
Train test split was applied to our dataset and the dataset was ran against a few combination of different classifiers & models. The models chosen were Logistic Regression & Multinomial Naive Bayes. The classifiers were the Count Vectorizer & TF-IDF Vectorizer. The final chosen model is the Logistic Regression model, with the TF-IDF Vectorizer. It was chosen for the following reasons:
    - Lowest overfitting to training data.
        Even though the above results show that all the models were overfitted, the overfitting for TVEC logistic regressison is the least. Therefore, this models should perform better for blind data, such as when collecting new posts.
    - Highest accuracy & lowest misclassification.
        Since our objective is to identify new leads & hot trends for each trading desk, it would be important that the correct information is collected.
        
Our final chosen model has an accuracy of 94%, specificity of 94% and sensitivity of 94%. This means that only around 5% of the posts are misclassified. The ROC score is of 0.98 which is a very respectable score (perfect score is 1.0, worst is 0.5), where the ROC curve plots the highest true positive rate (r\investing) together with the lowest false positive rate (predicting r\investing when it actually belongs to r\CryptoCurrency).

## Feature Importance
From our chosen model, we see the feature importance for each subreddits.

For the investing subreddit's results, there are relatively strong correlations around the terms 'stock', 'company', 'shares' and 'investing' for the r/investing subreddit. This is unsurprising as potential investers are often interested in knowing what are the best performing stocks, companies to look out for and any shares that are worth holding and/or buying.
We also see a wide range of traditional investments being frequently discuessed in the r/investing. From the stock market index tracking the performance of 500 large companies listed on stock exchanges in the United States(i.e. the S&P 500, as seen by 'sp' in the graph above), to the volatile options trading, and even comparatively stable fixed income investment like bonds, this focus words clearly shows us the trend in the r/investing subreddit.

For the cryptocurrency subreddit's results, there are relatively strong correlation around the terms 'coins', 'btc' and 'eth'. It is unsurprising that the people interested in CryptoCurrency would be interested in knowing more about popular blockchains like Ethereum (where Eth is the currency of it), digital currencies like bitcoins & Shiba Inu (as acronym as 'btc' & 'shib' respectively), to undertanding the different networks of blockchain technology. This shows us the hot trends in the r/CryptoCurrency subreddit.

## Misclassification Analysis
Misclassification is often produced due to the lack of contextual knowledge. Since the algorithms applied has no knowledge on natural human language patterns, it takes numerical weights of word distributions to predict the results instead. Such are the limitations of the modeling we have applied onto our datasets.

Hence, we then looked into the False Positive Posts. False positives are datapoints which we had predicted to originate from r/CryptoCurrency but is actually from r/investing. We quote some text below and explain why they may have been misclassified. Some reasons for the misclassified false positive posts could be due to: the user posting a CryptoCurrency question in the r/investing subreddit instead to reach a wider audience, and users who are new to investing and appears to be open for general investment advices. Therefore, it appears that the focus word 'crypto' is a call for concern. Newly interested users of CryptoCurrency may find themselves inclined to discuss about CryptoCurrency in the r/investing subreddit, due to the lack of knowledge and/or awareness of the r/CryptoCurrency subreddit.

Notwithstanding the above, we looked into the False Negative Posts as well. Some reasons for misclassified false negative posts could be due to users making additional remarks regarding crypto regulation which sounds like an for-your-information kind of remarks. 

# Conclusion
Our chosen model of the Logistic Regression on the TF-IDF Vectoriser appears to perform very well at 94% accuracy.

Even though there may be some overlapping words (for example, 'buy', 'money' and 'market) in the unigram, the model performs better with the inclusion of unigram.

The subreddits r/investing and r/CryptoCurrency may seem similar, but the results from the model and the top feature words show us that the discussions on the subreddits differ greatly and we need not worry that we are unable to accurately filter the information from the subreddits to the correct trading desks. Additionally, we can anticipate with expectant hearts for the new leads & hot trends for both traditional securities and CryptoCurrency to be identified accurately.

# Recommendation
1. Be cautious of the word 'crypto' being used loosely.
- People new to investing may be unaware of the difference between traditional securities and CryptoCurrency.

2. Remain up-to-date in knowledge.
- Especially so for CryptoCurrency where there are new coins being discussed frequently, frequent training and knowledge can be shared among the employees of the trading company so that common acronyms that are frquently used would be caught on by the employees of the trading desk. This will prevent any real data that are not wrongly and mistakenly identified as a stopword to be excluded.

3. Expand the data collection beyond the subreddits.
- This subreddits may be a good starting point to identify the general sentiments and areas of interest within traditional securities and CryptoCurrency. However, this limits us to the users who engage in the subreddits only. There is a concern that the results from the subreddits are contributed by the same small group of people only. To ensure there is no bias, perhaps the collection of data from other social media platforms like 'Twitter' and 'YouTube' would allow us to garner a wider range of data from a wider audience as well.

4. Obtain greater computing power or more time to process a greater number of hyperparameters.
- Due to time and technological constraints, our model is ran against a few hyperparameters only. If the trading company has the time & capacity to allow for a more in-depth research to be done for the model and is able to provide greater technological support (like greater computing power), perhaps more hyperparameters can be included. For example, running against a wider rnage of maximum features and ngrams as well.