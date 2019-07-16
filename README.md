# Motivation
## Behavioral Finance
<p align="justify">Our project focuses on predicting future stock changes using the attitude of social media posts (specifically Twitter). During the 20th century, the main investment theory was the Efficient Market Hypothesis (EMH). This theory states that the market reflects all the available information and that investors act rationally. However, the financial crises and the bubbles have demonstrated that sentiments and irrationality play a significant role in financial decision making. The Nobel Prize Robert Shiller challenged the EMH in an article in 1982 by comparing the US economy performances and the stock market during the 1920s. Several other distinguished economists have proved the presence of behavioral biases in stock market fluctuations. A new theory called “The Behavioral Finance Theory” attempts to explain these irrational components.</p>
<p align="justify">Today, the fact that there is a part of irrationality in the stock market fluctuations is widely accepted. In order to try to capture public sentiment, investors daily use confidence indexes, polls and surveys.</p>

## The choice of Twitter
<p align="justify">Meanwhile, social networks appear to be a relevant and innovative way to evaluate public mood. Whereas surveys are limited on the number of respondents, social networks enable to have access to the mood of millions of people at a very low cost. 
We chose to use Twitter rather than another social network for several reasons. First, Twitter is one of most visited websites in the world where people express their moods and opinions. Thus, we can assume that Twitter better reflects public sentiment rather than another website. Then, Twitter’s content is public and classified by hashtags which make the collection of the tweets related to a topic easier.
Moreover, several recent tweets had a significant impact on stock market. For instance, on August 7th, 2018, Elon Musk’s announcement about turning Tesla into a private company lead to a rise of Tesla’s stock price.
</p>
<p>
<img src="images/Musk_stock_tweet_turning_to_private.png" alt="Tweet about turning Tesla to private" width="600" align="center" class="inline"/>
</p>
<p align="justify">Thus, our team is interested in using social networks like Twitter to improve stock market prediction.</p>



## Our thesis
<p align="justify">The movement of the market can be predicted by augmenting rational economic data, standard micro and macroeconomic predictive indexes, with a model quantifying irrational behavior, a sentiment analysis of social media.</p>


# Approach

## Overview

<img src="images/cx4240_flowchart.png" alt="Functional Flowchart" width="600" align="center" class="inline"/>

## Gathering the Data

### Predictive Stock Market Indices
<p align="justify">Using the Alpha Vantage API, we had access to over 50 technical market indicators for any given company. Using all of these would have been impractical, as we didn't have the computing power to produce a predicive model using that many features in a reasonable amount of time. In order to lessen the load on our models, we set out to us SVD analysis to find the minimum number of indicies that encompass the maximum possible variance of our data. For our analysis, we chose two large companies with low stock variance, Microsoft and Google, and two small companies with high stock variance, Qumu Corporation and Social Reality Incorporated.

After our inital analysis, we found that for every one of our four test companies, the domininant principal direction (associated with the largest eigenvalue) was signifigantly larger than any other direction, with this direction a factor of ~1000x larger than the second largest principal direction. This meant that the dominant principal direction accounted for over 99.8% of the variance for every one of our test companies. </p>

<img src="images/Eigenvalues.png" alt="Eigenvalues"  align="center" class="inline"/>

<p align="justify">Taking the second largest principal direction in account pushed our lost variance to 10<sup>-6</sup> %, meaning that we could reach a final verdict by only considering the top two largest principal directions for each company. With this information, our next step was to consider the dominance of each measurement in these principal directions. We considered a measurement to be dominant if it's absolute value in the normalized vector exceeded 0.1. We first gather information on the top principal directions alone.</p>

<img src="images/t_Eigenvector.png" alt="Top Eigenvectors"  align="center" class="inline"/>

<p align="justify">This analysis revealed that there are only two technical measurements that appeared as outliers in the top principal direction for every company: HT_Sine (Index 53) and Quadrature (Index 58). When looking at only the large companies, Microsoft and Google, an additonal two measurments appear as outliers: APO (Index 21) and Aroon Down (Index 29). Applying the same process to the smaller companies revealed two more common outliers: Real Middle Band (Index 41) and Midpoint (Index 43). Finally, while OBV (Index 50) was not an outlier in the top eigenvector for any company, it did appear dominant in the secondary eigenvector for every company. Taking this list of seven measurements, we had encompassed the majority of the variance of our data, while cutting the number of features by over 80%. </p>

Final List of Meaurements:
<ul>
  <li> APO (Index 21) <a href = "https://www.fidelity.com/learning-center/trading-investing/technical-analysis/technical-indicator-guide/apo">More Info</a> </li>
  <li> Aroon Down (Index 29) <a href = "https://www.investopedia.com/terms/a/aroon.asp">More Info</a> </li>
  <li> Real Middle Band (Index 41) <a href = "https://www.investopedia.com/terms/b/bollingerbands.asp">More Info</a> </li>
  <li> Midpoint (Index 43) <a href = "https://www.tradingtechnologies.com/xtrader-help/x-study/technical-indicator-definitions/midpoint-midpnt/">More Info</a> </li>
  <li> OBV (Index 50) <a href = "https://www.investopedia.com/terms/o/onbalancevolume.asp">More Info</a> </li>
  <li> HT_Sine (Index 53) <a href = "http://www2.wealth-lab.com/WL5Wiki/HTLeadSin.ashx">More Info</a> </li>
  <li> Quadrature (Index 58) <a href = "https://en.wikipedia.org/wiki/In-phase_and_quadrature_components">More Info</a> </li>
</ul>

#### Additional Stock Data

<p align="justify">In addition to the technical indicators specific to a company, we also chose to add several general market indicators to account for events affecting the entire market. To accompish this, we included the open, close, high, low, and volume values of the NASDAQ.</p>

### Social Media Sentiment Analysis

<p align="justify">The first step in our sentiment analysis was to select a medium to pull data from. Twitter was selected as the preferred choice for two primary reasons. First, Twitter data is easily organized. Existing filters such as hashtags and verified users allow for flexible selection criteria. Second, individual tweets are short and succinct. This simplifies the analysis step as the model doesn’t need to interpret complex concepts such as the theme of a longer post. </p>

<p align="justify">After choosing to examine Twitter data, the next step was to select a model to analyze each tweet. As the focus of this project was determining the influence social media sentiment had on the market rather than the methods of quantifying social media sentiment, a strong preference was placed on “out of the box” models. The model selected for sentiment analysis was the Valence Aware Dictionary and Sentiment Reasoner (henceforward referred to as VADER) developed by C.J. Hutto and Eric Gilbert. Again, there were several reasons for selection. First, this analysis tool is designed specifically to quantify social media sentiment. As a result, it has a number of particularly useful features, including the ability to understand slang, acronyms, and emojis. Second, VADER analysis supports sentiment intensity quantification meaning that degree modifiers, capitalization, and punctuation all play a role in the outputted result. Lastly, VADER quantification is easy to implement. Simply by calling a python package on the content of a tweet, VADER returns a composite score ranging from -1 to 1, where -1 corresponds to an extremely negative sentiment and 1 corresponds to an extremely positive sentiment. </p>

#### Feature Engineering

<p align="justify">Once the model was selected, tweets for the relevant companies were scraped. Tweets were selected based on relevant hashtags, whether the user was verified, and whether the tweet was in English. Due to rate limitations, only tweets from January 1st, 2018 to the present were accessible. For each day the markets were open, the mean sentiment score, standard deviation sentiment score, and volume of tweets were calculated. On days the market was close, the scores and volume were assigned to the last day the market was open. For example, tweets on Saturday and Sunday were assigned to Friday, as Friday’s data would be used to predict Monday’s closing price. In order to account for the case that there were no tweets two binary features were created to correspond to the mean and standard deviation. The binary features were set to one if data was found for the given day, and zero otherwise. In the case that data was not found, the mean and standard deviation were imputed from the overall mean and standard deviation of all tweets pulled for the given company. </p>

### Combining the data and Introducing Lag

After combining the data to form a multivariate time series, more feature engineering was required. In order to assist the model in its predictions, lags were introduced as additional features. Lagging appends the features for n previous days to the current day, for example given a need to predict the stock price for day t + 1, lagging allows access to not only data from day t but also data from days t - 1, t - 2, ... up to t - n. For the models analyzed, a lag of three was introduced.

# Methods



# Results and analysis

<!--<img src="images/msft_adbnusvr_nt.png" alt="MSFT No Twitter Test" height = 275 width = 275 align="center" class="inline"/> 

<img src="images/msft_adbnusvr.png" alt="MSFT Test" height = 275 width = 275 align="center" class="inline"/>

<img src="images/googl_adbnusvr_nt.png" alt="GOOGL No Twitter Test"  height = 275 width = 275 align="center" class="inline"/> <img src="images/googl_adbnusvr.png" alt="GOOGL Test"  height = 275 width = 275 align="center" class="inline"/> 

<img src="images/aapl_adbnusvr_nt.png" alt="AAPL No Twitter Test" height = 275 width = 275 align="center" class="inline"/> <img src="images/aapl_adbnusvr.png" alt="AAPL Test" height = 275 width = 275 align="center" class="inline"/>-->

<center><h6>The graphs above display the results of testing the AdaBoost NuSVR model with 296 days of training data and 74 days of test data.</h6><center>
  
Looking at the charts show below, it can be seen that the use of Twitter data in the AdaBoost NuSVR model to predict the stock value of Microsoft improves the average Mean Squared Error (MSE) by 39% and the average Explained Variance by 15%.

<!--<img src="images/msft_mse_comp.png" alt="MSFT MSE Comparison" height = 400 width = 400 align="center" class="inline"/> <img src="images/msft_var_comp.png" alt="MSFT Exp. Var. Comparison" height = 400 width = 400 align="center" class="inline"/>--> 
  
# Conclusions
## Limits of Twitter data
<p align="justify">This study has shown that Twitter can improve stock prediction. However, the use of Twitter for stock market prediction has some limits. First, Twitter is particularly used in the United States and therefore reflects more American public sentiment rather than the world opinion. Moreover, it is sometimes difficult to extract enough relevant tweets related to a company. Usually when it is a large company, it is more likely to find many tweets. But it is not a general truth. For example, we did not find numerous tweets related to AMD. As Delta Airlines is concerned, many tweets were consumer complaints or discussions with the customer service. In that case, those tweets had to be filtered.</p>

## Next steps

### Twitter Analysis

Currently, the model used to calculate the sentiment of tweets in thie project has only one numerical indicator applied to each tweet. This model could be improved by introducing several more complex indicators to provide more information towards the overall sentiment towards the given company.

### Neural Networks

The machine learning models used in this project were all relatively simple compared to those availible for use. Given more time and computing power, signifigantly more complex models could be produced that would provide either more accurate predictions or a broader range of applicable companies for which the model would be useful.

# References
[1] J. Bollen and H. Mao. Twitter mood as a stock market predictor . IEEE Computer,
44(10):91–94.

[2] Carolyn Campbell, et al. Predicting Volatility in Equity Markets Using Macroeconomic
News . CS 229 Final Project, cs229.stanford.edu/proj2015/202_report.pdf.

[3] Shynkevich, Yauheniya & Mcginnity, T.M. & Coleman, Sonya & Belatreche, Ammar.
(2015). Predicting Stock Price Movements Based on Different Categories of News
Articles . 10.1109/SSCI.2015.107.

[4] Alexander Porshnev, et al. Machine Learning in Prediction of Stock Market Indicators
Based on Historical Data and Data from Twitter Sentiment Analysis . 2013 IEEE 13th
International Conference on Data Mining Workshops.

