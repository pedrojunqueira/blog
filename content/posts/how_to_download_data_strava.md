---
title: "How to download data from Strava with Python"
date: 2020-08-27T12:01:22+09:30
draft: false
toc: false
images:
tags:
  - api
  - python
  - pandas
  - requests
  - strava
---

I am a cyclist enthusiast and you may be if you are reading this blog post. 

Strava has a nice [API](https://developers.strava.com/docs/reference/) which gives access to the data recorded on the application.

Strava does not make it easy for the normal human being that no nothing about coding to access the data, on the other hand it makes it amazing for someone that knows code and is a developer.
The  [API](https://en.wikipedia.org/wiki/API), which stands for Application Programming Interface allows to interact with Strava application and do lots of operations like extract data and also post, if enough permission is given by the user.

The Strava API story is very close to my heart when it comes to learn how to program and it was a huge driver for me. 

When I first went through the documentation of the API it was really hard to understand as I had nearly zero programming knowledge. Then it drove me to really start learning programming and the whole ecosystem around programming like web, networking, deployment and etc.

I did some research and found a package `stravaio` that really helped me on how to download the data and it was the first time I had to look into a package source code, because I found a bug and posted an issue that the maintainer of the package end up [fixing it](https://github.com/sladkovm/stravaio/issues/4).

I remember that it was the first time I had to use `defaultdictionary` from `collections` to parse a `JSON` of nested dictionaries to build a data structure that `pandas` would take to render a `DataFrame` object.

So this post will briefly show you how you can use this `stravaio` package to download your activities. You can do much more than that, just look for the ["documentation"](https://github.com/sladkovm/stravaio).

Here are the steps and I will walk through the code and post the whole code at the end

1. Import the packages

```python
import os
from collections import defaultdict
from datetime import datetime

import pandas as pd
from stravaio import StravaIO, strava_oauth2
```

2. Get your secrets from environment variables

```python
client_id = os.getenv('STRAVA_CLIENT_ID')
client_secret = os.getenv('STRAVA_CLIENT_SECRET')
```

To make API calls to Strava you need to have a registered application. This can be easily done on your [Strava account](https://developers.strava.com/docs/getting-started/#account). Here is the instructions to get your CLIENT_ID and CLIENT_SECRET. Please keep this with you.

3. Generate a token by Authenticating with Strava oauth2

```python
token = strava_oauth2(client_id,client_secret)
access_token = token['access_token']
```

This was a part that it took me a while to get my head around. Strava improved the documentation a lot since the first time I read. They even drew a chart flow for [absolute dummies](https://developers.strava.com/docs/getting-started/#oauth).

What it actually does is a 2 way authentication.

First you make a request to have access to a athlete data then the athlete log to his account by giving you permission you asked for on the request then it return you a JSON with a temporary access token and a refresh token where you then can make calls to the API and get data. 
The permission given to you is based on the context you asked (e.g. only read or read and write)
In this example you would be doing it with your own account and I am sure you will allow yourself do access your own data.

4. Create a client object and call get_logged_in_athlete_activities methods

```python
client = StravaIO(access_token=access_token)
date_from = int(datetime(2020,7,1).timestamp())
activities = client.get_logged_in_athlete_activities(after=date_from)
```
Now that you have the token you can create a client object from the StravaIO class which handles the session and you can then call the method that fetch the athlete activity. For more details about the class here is the [source code](https://github.com/sladkovm/stravaio/blob/master/stravaio.py)

5. Finally transform into a pandas DataFrame and export to csv or other format you like.

```python
df_data = defaultdict(list)

for activity in activities:
    for k, v in activity.to_dict().items():
        df_data[k].append(v)

df = pd.DataFrame(df_data)

df.to_csv("my_activities.csv",index=False)
```
The `activity` variable is a `Stravaio` object which has a method `to_dict()` that makes life much easier.
It returns a list of dictionaries. This is perfect scenario to use defaultdict as a list so it can be easily ingested by pandas

Here is the full script

```python
import os
from collections import defaultdict
from datetime import datetime

import pandas as pd
from stravaio import StravaIO, strava_oauth2


client_id = os.getenv('STRAVA_CLIENT_ID')
client_secret = os.getenv('STRAVA_CLIENT_SECRET')

token = strava_oauth2(client_id,client_secret)
access_token = token['access_token']

client = StravaIO(access_token=access_token)
date_from = int(datetime(2020,7,1).timestamp())
activities = client.get_logged_in_athlete_activities(after=date_from)

df_data = defaultdict(list)

for activity in activities:
    for k, v in activity.to_dict().items():
        df_data[k].append(v)

df = pd.DataFrame(df_data)

df.to_csv("my_activities.csv",index=False)
```

Even though the above script is relatively simple it is not for the common citizen that does not code and is not enthusiast about Python like you and me

For this I am currently developing a Web app that downloads your data into a csv file in 3 simple clicks.

You can access it [here](http://www.download-data.com/).