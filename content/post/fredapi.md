---
title: Macroeconomic Indicators
date: 2018-01-17T01:04:49.000Z
draft: true
categories:
  - Data
  - python
tags:
  - python
  - jupyter notebook
  - pandas
  - matplotlib
keywords:
  - python
---

<!--more-->


...and where can I get them?


Today's artificial intelligent applications, or intelligent "agents" as I'll refer to them, require a lot data from their environment. Think about it, the more sensory input available to the agent, the better, more informed decisions it can make. A good example, although not really relevant to macroeconomic indicators, are today's self driving cars. They have speed, steering input, throttle position, brake pedal position, heading, cameras (visual), LIDAR and/or radar (obstacle location in space) and perhaps engineered inputs that are a function of the one's I've already mentioned (I'm no expert so excuse any naivety, but love to learn :wink:!

***
**Whether your a data scientist/AI engineer coding an intelligent application for some domain or you're an analyst you'll need data that represents the agent's/domain expert's entire environment or at least as much of it as you can.**
***

I currently reside at a manufacturing-centric company that correlates well with some data the census beauro compiles and was curious if I could include them in some of my analyses (as of Jan 2018, I'm an analyst). I hear mutterings of someone in the office that has an expensive subscription to some data service that provides these data along with other industry/competitor numbers, but in true INTP fashion I decide to do some research myself. Turns out, [`fredapi` from the St.Louis Federal Reserve Bank](https://fred.stlouisfed.org) is exactly what I'm in the market for.

## fredapi: Python API for FRED (Federal Reserve Economic Data)

According to the [github readme](https://github.com/mortada/fredapi), which I strongly encourage you to read, here's the basic gist:

>`fredapi` is a Python API for the [FRED](http://research.stlouisfed.org/fred2/) data provided by the Federal Reserve Bank of St. Louis. `fredapi` provides a wrapper in python to the [FRED web service](http://api.stlouisfed.org/docs/fred/), and also provides several conveninent methods for parsing and analyzing point-in-time data (i.e. historic data revisions) from [ALFRED](http://research.stlouisfed.org/tips/alfred/)

Sounds promising! Let's give it a go :smiley:

## The setup

Setting up access to `fredapi` is actually quite painless:

1. Go to https://research.stlouisfed.org and select register at the top right corner
2. Provide some account info req'ed to create the account (FWIW you'll be asked)
    - Follow the prompts from there and copy your API key when provided
3. Install with `pip install fredapi`
    - Or if you're behind a work SSL barrier try `pip install --trusted-host pypi.python.org fredapi`
4. Head into your IDE/code editor of your choice!


Now that we're setup, we need a couple of pre-req's to get going:

```python
import pandas as pd # Required for fredapi
import numpy as np # it's a good habit to include
import matplotlib.pyplot as plt # for plots
%matplotlib inline # for displaying said plots in a Jupyter notebook
from fredapi import Fred # the package for getting macroeconomic data
from scipy.signal import savgol_filter # a smoothing filter I currently like
```


Really the only required imports are `pandas` and `fredapi`, the others are for convenience, really. Now from here on out I'll be pasting code snippets from my [jupyter notebook]() but would love to incorporate that notebook into tutorials like this in the future. If anyone reading *cough-cough* knows how to incorporate jupyter notebooks/nb viewers into hugo templates I'd love to hear it!

From here we can get our first data series! The examples grab the S&P500 index for the last several years so let's confirm we *really* have everything setup and working correctly:

***
 `fred.get_series('sp500').plot()`

![SP&500](/images/fredapi/sp500.png)
***

Woo! It works, we've got the S&P500 index data coming in as a pandas series. Let's take a look at the other series we can bring in. I'm interested in those indicators based on housing. I wonder how many I've got available:

***
```python
housing_series = fred.search('housing')['title'].values
len(housing_series)
```

![search](/images/fredapi/records.png)
***

That seems interesting, 1000 results on the nose. Let's look at the `search` method and see if there's some sort of cap. Sure enough in the [`fred.py`](https://github.com/mortada/fredapi/blob/master/fredapi/fred.py) source code there's a limit argument which has a default value of 1000. I'm not going to dig further, 1K is enough to realize there's a lot of housing specific macroeconomic indicators I have at my disposal! So let's do something with them.

I just want to grab one series and graph it. Let's do [housing starts](https://fred.stlouisfed.org/series/HOUST) for example.

First we'll fetch the appropriate data using the 'HOUST' series_id:

***
```python
housing_starts = fred.get_series('HOUST')
smoothed_starts = pd.Series(savgol_filter(housing_starts, 33, 4))

print(housing_starts.shape)
print(smoothed_starts.shape)
```
![series_length](/images/fredapi/series_length.PNG)

707 Months worth of data here, let's plot it. I've recently come across a smoothing function, rather filter, I like that abstracts some volitility and noise away from time-series to get a better picture of the trends. The `savgol_filter` from  `scipy.signal` does a pretty good job.

```Python
# Plot the Housing Start Data
plt.figure(figsize=(20,10))
plt.style.use('fivethirtyeight')

plt.plot(housing_starts.values, label="Housing Starts")
plt.plot(smoothed_starts.values, label="Smoothed", color='#ff21ff')

plt.title("Housing Starts: Total: New Privately Owned Housing Units Started (HOUST)")
plt.ylabel("Housing Starts in thousands")
plt.legend()
plt.xlabel("Date index from Jan-1959") # Still getting matplotlib figured out..

plt.show()
```
![final_graph](/images/fredapi/final_graph.png)

## Conclusion

I quite like the looks of that :smiley:. From my end that's the nicest matplotlib plot I have put together--I'm proud of that.

My end goal here was to provide another source of data to add to your toolset. We've been able to hook up to `fredapi` and (**buzzword warning**) get macroeconomic data to supplement our analyses/intelligent agents to facilitate a holistic representation of our environment from data!

>Now, this assumes the macroeconomic scale correlates in some way with the problem you're trying to solve. Further analysis is needed to ensure these data improve predictions/analyses in some way for you!

I am certain there are improvements that could be made. Eagle-eyed readers will have realized the x-axis is simply an index and not of date, for example. Also, there could be improvements with embedding code inputs/outputs that I'll work on going forward. We're all learning, just at different stages of the process!

### I'd love to hear your thoughts, really. Improvements, things did well, avenues to check out in the future etc. Leave them below or reach out on twitter, I'm at [cboyrun](https://twitter.com/cboyrun)!
