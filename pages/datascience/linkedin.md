---
layout: page
title: "Getting LinkedIn data with Python"
date: 2018-11-15
description: In the same way as for Twitter, we can also get data from LinkedIn. Here I show how to interface with LinkedIn's API to access their data. 
---

In the same way as for Twitter, we can also get data from LinkedIn. Here I show how to interface with LinkedIn's API to access their data. 

LinkedIn is a bit more difficult than Twitter to access; this is because the data shared there is more sensitive, and as of today (November 2018), it is not possible to access most of the data casually, without becoming a certified developer on their network. Like Twitter, LinkedIn uses something called OAuth to verify and authorise access. This process is somewhat involved (if you're interested have a look at the [OAuth website](https://oauth.net)), but basically is a way to allow multiple users to access protected data using the credentials of one registered user, without needing to share usernames and passwords. Most of this tutorial is focused on setting up the authentication, which is the most difficult part.

The tutorial contains the following sections:

* TOC
{:toc}
<!-- toc -->

---

### 1. Getting started
First, we need the following libraries: [`python3-linkedin`](https://github.com/ozgur/python-linkedin):
```
pip install python3-linkedin
```

---

### 2. Initial authorisation
You will then need to register and create an app on the LinkedIn [developer platform](https://oauth.net). You'll need to supply some information like your business contact details and a logo(!). At the end of the process, you will end up at a page where two authentication keys are shown: a 'client ID', and a 'client secret' key. You'll also be able to set default permissions for your app, such as allowing access to the basic profile, email address, etc. For the time being, we will only select the basic profile, by ticking `r_basicprofile`. 

You'll have to then specify a redirect URL for OAuth 2.0: this can be any valid URL beginning with 'https://'. The purpose of this will become clear later on. I set it to my GitHub pages site. 

Then, we're ready to start the authentication process. This is a multi-step process, which can be broken down into two parts:

* First, you ask LinkedIn for an authentication code, using your client and secret API keys. 
* Then you use the code to obtain an access token, which can authenticate your app.

---

#### 2.1 Asking for authentication code
In python, the first step is to initialise the authorisation with the two API keys:  

```python
API_KEY = 'yourapikey'
API_SECRET = 'yourapisecret'
RETURN_URL = 'https://nadanai263.github.io'

auth = linkedin.LinkedInAuthentication(API_KEY,
                                    API_SECRET,
                                    RETURN_URL,
                                    {'r_basicprofile':'BASIC_PROFILE'}) # permissions can be changed

print(auth.authorization_url)  # open this url on your browser
application = linkedin.LinkedInApplication(auth)
```
We set the permissions manually, to allow access to the basic profile. The permissions set here must match that set in the app configuration. What this code block does is to instantiate an application object using the API key pair as authorisation. The URL for authorisation will be outputted; you must then point your browser to that URL to make an authorisation query. An example URL output is shown below:

```
https://www.linkedin.com/uas/oauth2/authorization?response_type=code&client_id=yourapikey&scope=r_basicprofile&state=bf1b9ab2950896b5e7d3f5a3dc42a718&redirect_uri=https%3A//nadanai263.github.io
```
This URL can be broken up into a few parts:
```
https://www.linkedin.com/uas/oauth2/authorization # Ask LinkedIn for authorisation
response_type=code # Ask for an authorisation code
client_id=yourapikey # This will be the same as your API key, which identifies who you are
scope=r_basicprofile # This is the permissions requested
state=bf1b9ab2950896b5e7d3f5a3dc42a718 # This is a unique ID corresponding to the 
redirect_uri=https%3A//nadanai263.github.io # This is where you'll be redirected after the query.
```
After this is sent, you're redirected to the redirect URL page (my home page in this example), and the results of the query are appended to my site's URL:

```
https://nadanai263.github.io/?code=CODE&state=bf1b9ab2950896b5e7d3f5a3dc42a718
```
This response contains two items: a new code for access, and another state. Make sure this value is the same as the state shown earlier; the purpose of this ID is to enable you to check that no one else has intercepted the request. 

#### 2.2 Using the code to get the access token
We can enter the code and ask for our access token:
```python
auth.authorization_code='CODE'
auth.get_access_token()
Out:
'TOKEN'
```
The output can then be used to finally authenticate our application:
```python
application = linkedin.LinkedInApplication(token='TOKEN')
```
The token is valid for 60 days, and so you don't have to repeat the procedure above for a while: you can just run the application directly using this token. Phew!

---

### 3. Using the API to make queries
Finally we can use the API to make queries. You can look at your own LinkedIn profile:
```python
application.get_profile()
```
This returns a dictionary with your basic profile information. You can also make a search query:
```python
application.search_company(selectors=[{'companies': ['name']}],params={'keywords': 'apple', 'count':20})
Out:
{'companies': {'_count': 20,
  '_start': 0,
  '_total': 28350,
  'values': [{'name': 'Apple'},
   {'name': 'Apple Basic'},
   {'name': 'Apple & Associates'},
   {'name': 'Apple Vacations'},
   {'name': 'Apple Leisure Group'},
   {'name': 'Apple American Group LLC'},
   {'name': 'apple tree communications'},
   {'name': 'Apple Authorised Distributor Portugal'},
   {'name': 'Apple Store'},
   {'name': 'Apple Search & Selection'},
   {'name': 'Apple Express'},
   {'name': 'Apple Growth Partners'},
   {'name': 'Apple Federal Credit Union'},
   {'name': 'Apple Aviation'},
   {'name': 'Apple&Ink'},
   {'name': 'Apple Rehab'},
   {'name': 'Apple & Eve LLC'},
   {'name': 'Apple Bank'},
   {'name': 'Apple Rock Displays'},
   {'name': 'Apple Premium Reseller - Iplace'}]}}
```
---

### 4. Conclusions

Like Twitter, once you have the authentication in place, API calls are straightforward. For me, getting the authentication to work was the hardest part, especially as the procedure has changed slightly over the last few years. For instance, LinkedIn now uses OAuth 2.0, while many tutorials available only describe the old OAuth 1.0. The original `python-linkedin` library does not work with Python 3, necessitating the new `python3-linkedin` version. Finally, the lack of full accessibility with a basic developer account means that the amount of information you are able to get to is limited. In any case, this was a useful exercise in learning about OAuth 2.0. 

A jupyter notebook is available for this tutorial on my [GitHub repository](https://github.com/nadanai263/datasciportfolio).
