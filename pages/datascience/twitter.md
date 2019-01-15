---
layout: post
title: "Getting Twitter data with Python"
date: 2018-11-14
description: A journey of a million steps begins with one... and so I begin my with my first post in the world of data science! In this tutorial, I will demonstrate how to scrape, or obtain, data from Twitter, and do some simple analysis on the results. 

---

A journey of a million steps begins with one... and so I begin my with my first post in the world of data science! In this tutorial, I will demonstrate how to scrape, or obtain, data from Twitter, and do some simple analysis on the results. 

Twitter is a rich source of textual data and metadata, which contains myriad complex information about users' interests, preferences, interactions, locations, and behaviour; it's therefore very useful and interesting to be able to mine this resource. While the [Twitter API](https://developer.twitter.com/content/developer-twitter/en.html) itself is already friendly to use, python wrappers make the experience even easier: by running simple python commands we can query Twitter to obtain users' tweets and associated metadata. Here I demonstrate the most basic interaction with the Twitter API through a python wrapper, `python-twitter`. 

The tutorial contains the following sections:

* TOC
{:toc}
<!-- toc -->

---

### 1. Getting started
First, we need the following libraries: `python-twitter` and `json`. We can install these to our virtual environment using `pip` as always (I highly recommend you to use your python in a virtual environment... please see my (upcoming) tutorial for this!):

```
pip install python-twitter
```
`python-twitter` is a python wrapper for Twitter's API, which allows you to interact and query Twitter data directly using python code. With the installation in place we are ready to go!

---

### 2. Getting authorization to access Twitter data
To access Twitter data, you need to first of all create a developer account. This can be easily done through their developer platform. The reason for this is that you will need to login with your Twitter account (of course you need to make one if you haven't already got one) to access the data. To apply for a developer account, follow the instructions on their [developer website](https://developer.twitter.com/content/developer-twitter/en.html). You will need to answer a few questions about why you want to set one up; I chose 'study/student projects' as that was most applicable. The rest of the steps were quite straightforward. 

Once the account is set up, you will need to create an 'app': this allows you to obtain four important pieces of information: the `consumer key`, the `consumer secret`, the `access key`, and the `access secret`. You'll need to fill in some more information (such as giving a URL for your website; any will do). Finally, with these four unique tokens, you will be able to access the Twitter API. Keep the tokens safe, and treat them like you would any other password. 

---

### 3. Make an API query
First we need to instantiate the twitter API with our unique tokens:

```python
import twitter

CONSUMER_KEY = 'yourvalues' # replace these with your unique values
CONSUMER_SECRET = 'yourvalues'
OAUTH_TOKEN = 'yourvalues'
OAUTH_TOKEN_SECRET = 'yourvalues'

twitter_api = twitter.Api(consumer_key=CONSUMER_KEY,
                         consumer_secret=CONSUMER_SECRET,
                         access_token_key=OAUTH_TOKEN,
                         access_token_secret=OAUTH_TOKEN_SECRET)
```

If this code block runs successfully, you will have successfully used your credientials to gain authorization to query the Twitter API. So, let's make our first API query:

```python
statuses = twitter_api.GetUserTimeline(screen_name='nadanai263',count=1)
```
Here I call the `GetUserTimeline()` method of the API. This queries the timeline of a user, which in this case is myself; we ask for the latest item on my (very old) timeline. The `statuses` object returned is a list of `Status()` objects associated with the `python-twitter` wrapper. Each `Status()` object is a tweet and all its associated metadata; in this case, we have asked for only the latest tweet, which we can first convert to a dict() and view:

```python
statusdict = [s.AsDict() for s in statuses]
print(statusdict[0])

Out: 
{'created_at': 'Sat Jan 04 16:34:17 +0000 2014',
 'hashtags': [{'text': 'Bangkok'}, {'text': 'Thailand'}],
 'id': 419507083726180352,
 'id_str': '419507083726180352',
 'lang': 'en',
 'media': [{'display_url': 'pic.twitter.com/whArUIBEce',
   'expanded_url': 'https://twitter.com/nadanai263/status/419507083726180352/photo/1',
   'id': 419507083734568960,
   'media_url': 'http://pbs.twimg.com/media/BdJjeFLCUAAUWJm.jpg',
   'media_url_https': 'https://pbs.twimg.com/media/BdJjeFLCUAAUWJm.jpg',
   'sizes': {'large': {'h': 640, 'resize': 'fit', 'w': 960},
    'medium': {'h': 640, 'resize': 'fit', 'w': 960},
    'small': {'h': 453, 'resize': 'fit', 'w': 680},
    'thumb': {'h': 150, 'resize': 'crop', 'w': 150}},
   'type': 'photo',
   'url': 'http://t.co/whArUIBEce'}],
 'source': '<a href="http://twitter.com" rel="nofollow">Twitter Web Client</a>',
 'text': 'What is happening to my country? #Bangkok #Thailand http://t.co/whArUIBEce',
 'urls': [],
 'user': {'created_at': 'Sat Dec 15 16:45:36 +0000 2012',
  'default_profile': True,
  'description': 'Scientist and musician',
  'followers_count': 60,
  'friends_count': 90,
  'id': 1013539350,
  'id_str': '1013539350',
  'lang': 'en',
  'listed_count': 1,
  'location': 'Cambridge',
  'name': 'Nadanai L',
  'profile_background_color': 'C0DEED',
  'profile_background_image_url': 'http://abs.twimg.com/images/themes/theme1/bg.png',
  'profile_background_image_url_https': 'https://abs.twimg.com/images/themes/theme1/bg.png',
  'profile_banner_url': 'https://pbs.twimg.com/profile_banners/1013539350/1361963151',
  'profile_image_url': 'http://pbs.twimg.com/profile_images/3314384686/f84461bb253ffa004d6a94a1477ad22c_normal.jpeg',
  'profile_image_url_https': 'https://pbs.twimg.com/profile_images/3314384686/f84461bb253ffa004d6a94a1477ad22c_normal.jpeg',
  'profile_link_color': '1DA1F2',
  'profile_sidebar_border_color': 'C0DEED',
  'profile_sidebar_fill_color': 'DDEEF6',
  'profile_text_color': '333333',
  'profile_use_background_image': True,
  'screen_name': 'nadanai263',
  'statuses_count': 25},
 'user_mentions': []}
```
As you can see, each tweet is associated with a rich set of metadata, which will be invaluable for later analysis. 

We've successfully used the Twitter API to query a user and obtain their latest tweet. Now let's get a wider picture of what's going on in the Twitter universe by looking at trends. 

---

### 4. Finding out what's trending
We can query 'trends', or popular topics. These can be localised to specific places using a system called the Yahoo! Where On Earth ID (WOEID). For example, the following query
```python
WORLD_WOEID_ID = 1
UK_WOEID_ID = 23424975
world_trends = twitter_api.GetTrendsWoeid(WORLD_WOEID_ID) 
uk_trends = twitter_api.GetTrendsWoeid(UK_WOEID_ID)
```
returns a list of the 10 most popular hashtags (as measured by tweet volume), specific to a particular location as given by the WOEID. Each object can be converted to a dictionary, and then printed in an intelligible way using the `json` package:
```python
import json

print(json.dumps([s.AsDict() for s in world_trends], indent=4))
print(json.dumps([s.AsDict() for s in uk_trends], indent=4))

Out:
{
        "name": "#NationalPickleDay",
        "query": "%23NationalPickleDay",
        "timestamp": "2018-11-14T22:18:23Z",
        "url": "http://twitter.com/search?q=%23NationalPickleDay",
        "tweet_volume": 24475
    },...
```
Each object now contains a name, which represents the hashtag, and other associated information like the tweet volume, or number of times it has been tweeted. We can select only one the name, and return those in a python set:
```python
uk_trends_set = set([names.AsDict()['name'] for names in uk_trends]) 
print(uk_trends_set)

Out:
{'Jacob Rees Mogg', '#HouseOfTheYear', '#NorthernMarketingAwards', '#watchdoglive', '#WithdrawalAgreement', 'Downing Street', '#MummyDiaries', 'Fabian Delph', '#DevonHour', '#MaddoxPrize', 'Bergh', '#IrishaSongorMovie', '#AHSApocalypse', '#channel4news', '#ClaxtonsWhisky', 'Theresa May', 'Chequers', 'Greenland', '#handmadehour', 'Cilic', '#TheApprentice', 'My Brain', '#McrCultureAwards', '#POGDOGS', '#LTHEchat', '#NSA2018', '#weddinghour', '#mastercheftheprofessionals', '#jamesbulger', 'Political Declaration', 'David Mundell', 'Number 10', '#DarkHeart', '#NXTUK', '#woofwoofwednesday', 'Sammy Wilson', '#TomorrowToday', '#somecomms', 'Tim Stockdale', '#UKGFA', 'Michel Barnier', '#BrexitChaos', '#StripWithLittleMix', 'United Kingdom', '#selfieforseb', 'Jack Reacher', 'Euratom', '#WalesNOTY2018', '#AHFODfilm', '#CoronationStreet'}
```
So... these are the trending topics in the UK on the evening of the 14th November, 2018! If you run your code you're sure to find different results. 

---

### 5. Simple analysis: number of words per tweet
Let's select one hashtag to investigate further... how about the esteemed 'Jacob Rees Mogg'?! By calling the `GetSearch()` method, we can find tweets containing that phrase:
```python
query = 'Jacob Rees Mogg'
search_results = twitter_api.GetSearch(term=query, count=100)
```
Here, we return `search_results`, which is a list of 100 `Status()`, or tweet, objects, containing the phrase 'Jacob Rees Mogg'. We can view all of them if we want, by printing them one by one:
```python
tweets_set = set([status.AsDict()['text'] for status in search_results]) 
for elem in tweets_set:
    print(elem)
    print('--')
```
This results in a tremendous number of tweet text. But what may be more interesting than tweets about Jacob Rees Mogg is an analysis of them. Let's count the average number of tweets in the text:
```python
total_words = []
total_chars = []
for elem in tweets_set:
    total_words.append(len(elem.split()))
    total_chars.append(len(elem))

import numpy as np
print(np.mean(total_words))
print(np.mean(total_chars))

Out: 
17.54
123.3
```
So, tweets about Jacob Rees Mogg on average contain around 18 words and 123 characters, which is significantly longer than the [average tweet length](https://smk.co/article/the-average-tweet-length-is-28-characters-long-and-other-interesting-facts) of 28 characters. Deeper analysis and interpretation on the literacy of people likely to tweet about Jacob Rees Mogg will be left to another post...

---

### 6. Conclusions

As you can see, interacting with Twitter is straightforward. The API is simple yet powerful, and I hope to be able to show much more sophisticated analysis in the future. 

A lot of the material in this tutorial came from Matthew Russell's book [Mining the Social Web](https://www.oreilly.com/library/view/mining-the-social/9781449368180/ch01.html), as well as documentation for [`python-twitter`](https://python-twitter.readthedocs.io/en/latest/index.html). A jupyter notebook is available for this tutorial on my [GitHub repository](https://github.com/nadanai263/datasciportfolio).
