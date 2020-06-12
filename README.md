# Project: Gather and Clean WeRateDogs Data
## Wrangle Report
Haoming Jin

## Introduction
In this project, we collect, assess and clean the data from the WeRateDogs archive. We will use the tweepy API to collect the data from Twitter and then we will create a pandas DataFrame. We will find out the issues with this DataFrame in both tidiness and quality and we will fix issues we find.

## Gather

We gathered the data from the following three sources, and stored in three separate files:
1. WeRateDogs Twitter archive, which is sent to Udacity and enhanced by the instructors, and we manually downloaded this file. This data includes the following information: tweet_id, in_reply_to_status_id, in_reply_to_user_id, timestamp, source (the source url), text (the tweet text), retweeted_status_id, retweeted_status_user_id, retweeted_status_timestamp, expanded_urls (the tweet url),rating_numerator, rating_denominator, name(name of the dog), and four columns titled doggo, floofer, pupper, puppo describing the dog stage of the dog.

2. The image prediction file, which is programatically downloaded from the Udacity server. This file contains a prediction of dog types using machine learning on tweet images from WeRateDogs twitter. The data includes the following information: tweet_id, jpg_url, img_num, and three prediction of the item in the pictures, if they are dogs and their corresponding confidance, titled pi, pi_dog, pi_conf.

3. All the tweets' JSON data of WeRateDogs tweets, downloaded by using the tweepy library to query the Twitter API. The twitter id are from the first set of data. This data includes: tweet_id, retweet_count, favorite_count. This data is save to tweet_json.txt.

## Assessing and Cleaning

We decided to take the approach of assessing and cleaning each dataset separately and then put them together.

**1. WeRateDogs Twitter Archive**

This dataset has the following issues:
1. There are 181 retweets (retweet_status_id etc is not NAN) (quality)
2. Missing data from expanded_url, showing 59 missing data. (quality)
3. The timestamp column is in string format, and should be datetime.(quality)
4. There are 4 columns of dog stages, there should only be one, and should be category type instead of string type. Also, most rows are missing this information. (quality and tidiness)
5. The minimum rating_denominator is 0 which is not correct. (quality)
6. The name column doesn't have missing values but many of them are 'None'. (quality)

These issues are dealth with in the following ways:
1. The retweet rows are dropped.
2. The rows with missing expanded_url is to be dropped, but we found that they will automatically be dropped later when merging files, so no action taken.
3. The timestamp column is converted to datetime type.
4. The 4 columns are merged into a single dog_stages column, and we convert it to a single category type value, the rows missing this data is marked as NaN.
5. We observed the issues with ratings that has rating_denominator unequal to 10, and found most are invalid entries and the amount of data isn't worth the effort fixing, so they are dropped.
6. We replaced the 'None' names with NaN.

**2. Image Prediction Data**
This dataset has the following issue:
- There are three different predictions for each row, but we only need one prediction to present. (tidiness)

We dealth with this problem by doing this:
We checked the accuracies by manually compare the picture to the prediction in four different cases: 1. p1 predicts dog. 2. p1 predicts not dog, but p2 predicts dog. 3. p1 and p2 predicts dog, p3 predicts dog. 4. p1, p2, p3 all predicts not dog.

We found that in case 1 and 2, the first dog prediction is usually pretty accurate. But with case 3 and 4, the picture is usually not a dog, or of very low quality with no way of telling the type of the dog. So we merged all these rows to a single prediction row, which equals the first dog prediction in p1 or p2.

**3. Extra Data From Tweet JSON**
This set of data seems to be in good shape, no duplicate or missing data.

**4. Merging Datasets**
After viewing these datasets, we think that all these information belongs to the same table, so we should merge them into a single table. (tidiness)

What we did:
1. First we merged the Twitter Archive data and the Image Prediction data. Because we want only entries with both ratings and image prediction, we used inner join.
2. We merged the dataset we got in the first step with the extra data from tweet json. Because it is quite OK to have a data with everything else except retweet and favorite count, but data with only retweet and favorite count doesn't mean much. So we used left join.
