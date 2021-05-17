---
jupyter:
  jupytext:
    formats: ipynb,md
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.10.3
  kernelspec:
    display_name: Python 3
    language: python
    name: python3
---

# Working with Data


>Using the data below, answer the following questions:

>1) Which entities (top 5) had the largest population density in 2020? (Sort and use .head)
>2) Which entities have more water area than land area?
>3) Which entities increased in population the most in the last 10 years? (Top 3)
>4) What state bird accounts for the largest population as of 2020? Land area?
>5) How many entities' largest city is their capital city?
>6) Which entity has the largest percent drop from their largest city to their 5th largest? 100*(1st largest - 5th largest)/(1st largest)

```python
import pandas as pd
```

```python
facts = pd.read_csv('../data/state_facts.tsv',delimiter="\t")
facts.head(5)
```

>Using the "state_dates.tsv" data, answer the remaining questions. You will need to merge the two data sets together:

>7) Of the states that joined the United States before 1790, what is the most common state flower?
>8) Which has the larger population density, the most dense US Territory or the least dense state?
>9) Make a graph that plots the populations of the largest city in each entity in the order in which they joined the US. Make the bars black
>10) Make two additional graphs like the one above but one for land area (green bars) and one for water area (blue bars)


Hint: `pd.read_csv('../data/state_dates.tsv',delimiter="\t")`

Hint: You likely want to convert the Date column to datetime. You might have to correct errors in the data as well.

Hint: `states['Date']<pd.datetime(1790,1,1)`

Hint: `pd.merge(****,****,left_on='USPS_code',right_on='Abbreviation',how='outer')`

```python
# Sample code to help with the plots

#import matplotlib as plt
#%config InlineBackend.figure_format ='retina' #This makes your plot clearer


#plot = *your df by date*[[*column*,'Abbreviation']].plot(kind='bar',figsize=(10,4))
#plot.set_xticklabels(*your df by date*['Abbreviation']);
```

# Question 1)
>1) Which entities (top 5) had the largest population density in 2020? (Sort and use .head)

```python
facts['PopDensity2020'] = facts['Pop_2020']/facts['Area_land'] 
q1 = facts.sort_values(by='PopDensity2020',ascending=False)
q1.head()
```

# Question 2)
>2) Which entities have more water area than land area?

```python
facts[facts['Area_land']<facts['Area_water']].sort_values(by='Area_water',ascending=False)
```

```python

```

# Question 3)
>3) Which entities increased in population the most in the last 10 years? (Top 3)

```python
facts['pop_change'] = facts['Pop_2020']-facts['Pop_2010']
```

```python
facts.sort_values(by='pop_change',ascending=False)[['State','Pop_2020','Pop_2010','pop_change']].head()
```

# Question 4
>4) What state bird accounts for the largest population as of 2020? Land area?


```python
birds1 = facts[['Pop_2020','State_bird']].groupby(by='State_bird').sum()
birds1.sort_values(by='Pop_2020',ascending=False).head()
```

```python
birds2 = facts[['Area_land','State_bird']].groupby(by='State_bird').sum()
birds2.sort_values(by='Area_land',ascending=False).head()
```

# Question 5
>5) How many entities' largest city is their capital city?


```python
len(facts[facts['Capital']==facts['City_1']][['State','Capital']])
```

# Question 6
>6) Which entity has the largest percent drop from their largest city to their 5th largest? 100*(1st largest - 5th largest)/(1st largest)

```python
facts['p_drop'] = 100*(facts['city_1_pop']-facts['city_5_pop'])/facts['city_1_pop']
```

```python
facts[['State','City_1','city_1_pop','City_5','city_5_pop','p_drop']].sort_values(by='p_drop',ascending=False).head()
```

# Question 7
>7) Of the states that joined the United States before 1790, what is the most common state flower?

```python
dates = pd.read_csv('../data/state_dates.tsv',delimiter="\t")
dates.head()
```

```python
facts.head()
```

```python
states = pd.merge(facts,dates,left_on='USPS_code',right_on='Abbreviation',how='outer')
```

```python
states.head()
```

```python
states['Date']=pd.to_datetime(states['Date'])
```

```python
states[states['Date']<pd.datetime(1790,1,1)]['State_flower'].value_counts()
```

# Question 8
>8) Which has the larger population density, the most dense US Territory or the least dense state?

```python
ter = states[states['Status']=='Territory']
```

```python
sta = states[states['Status']=='State']
```

```python
ter['PopDensity2020'] = ter['Pop_2020']/ter['Area_land'] 
ter = ter.sort_values(by='PopDensity2020',ascending=False)
ter[['State','PopDensity2020']].head(1)
```

```python
sta['PopDensity2020'] = sta['Pop_2020']/sta['Area_land'] 
sta = sta.sort_values(by='PopDensity2020',ascending=True)
sta[['State','PopDensity2020']].head(1)
```

# Question 9
>9) Make a graph that plots the populations of the largest city in each entity in the order in which they joined the US. Make the bars black

```python
statesbydate = states.sort_values(by='Date')
```

```python
# Sample code to help with the plots

import matplotlib as plt
%config InlineBackend.figure_format ='retina' #This makes your plot clearer


plot = statesbydate[['city_1_pop','Abbreviation']].plot(kind='bar',figsize=(10,4),color='black')
plot.set_xticklabels(statesbydate['Abbreviation']);
```

# Question 10
>10) Make two additional graphs like the one above but one for land area (green bars) and one for water area (blue bars)

```python
# Sample code to help with the plots

import matplotlib as plt
%config InlineBackend.figure_format ='retina' #This makes your plot clearer


plot = statesbydate[['Area_land','Abbreviation']].plot(kind='bar',figsize=(10,4),color='green')
plot.set_xticklabels(statesbydate['Abbreviation']);
```

```python
# Sample code to help with the plots

import matplotlib as plt
%config InlineBackend.figure_format ='retina' #This makes your plot clearer


plot = statesbydate[['Area_water','Abbreviation']].plot(kind='bar',figsize=(10,4),color='blue')
plot.set_xticklabels(statesbydate['Abbreviation']);
```

```python

```
