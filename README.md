# Markov-Chain
Markov Chain application in health care (ANAP2020 Database) to predict French medical institution status .
This is an implementation of the Markov chain algorithm for the health care industry, to predict whether medical institution
in France are progressing in there activity in the medicine fields such as Medicine, Surgery, Obstetrics, ext...
from the publicly available database ANAP2020 and after data preparation and integration with business intelligence tools 
I used python to predict the "Bed Use / Occupancy Rate in Obstetrics" which is noted "A15" indicators in the source database.
trained on 7 years of data from 2008 to 2014, tested on 2015, and forecasted for the year 2020 this algorithm has proven 
to be efficient .
## Import Our Librairies
```python
import pyodbc
```
Is the python sql driver to get our data from the sql server

## Create sql connexion

## Table from database to dataframe
Load ActiviteFait  table wich contains our indicators .

Fk_Date : Year  : 1->2008 2->2009 3->2010 4->2011 5->2012 6->2013 7->2014 8->2015

Fk_Hopt  : Represent a hospital we don't need hospitals name

PS:Each Hospital can have from 2 to 8 years of each indicator(database has some NA columun we change them to zero because they don't effect us is this case)

## Description to our ActFact Dataframe
Indicators   : From A1_bis to A15 .

There are many indicators that concern frensh medical institution in this database, check this link (Sorry because it's in French)
https://www.data.gouv.fr/s/resources/hospi-diag/20150817-171102/Hospi-Diag_Presentation_et_Principes_utilisation_aout_2015.pdf

But as mentioned in the README I choosed the activity axis and precisely A15 "Bed Use / Occupancy Rate in Obstetrics"

## Seaborn pairplot to detect any clusters
We could have used some clustering method such as k-means but this is not the purpose of this notebook and we will see later that with the partition I got some pretty good result

I needed to divide my data into Blocks to define my states for the markov chain, you see that we have 3 clusters of points when you analyse the seaborn pairplot graphe.

We have then 3 States 

A under 30 ,
B between 30 and 60 ,
C over 60

Then We transform our quantitative variables to categorical variables.

The markov chain States needs to be discrete that's why we need this transformation .

## So Now let's Define What's a markov chain
Very Simple : A Markov chain is a stochastic process, but it differs from a general stochastic process in that a Markov chain must be "memory-less". That is, (the probability of)  future *actions are not dependent upon the steps that led up to the  present   state.

Now to the implementation ,first we need to define a dictionary we call it  MarkovList  to append each hospital as key  and  "A15" indicator from the year 2008 to 2014 (we keep 2015 for the test) as  value represented in an array respectively to each hospital our goal is to see how the indicator evolve in time from A  : Bad activity to  B  : good activity and finaly C  very good activity  .

```python
for row in ActFactUp.itertuples(): # iterate over dataframe on each step it returns a row

if row[9] not in MarkovList: # row[9] : Fk_Hopt
        MarkovList[row[9]] = []# if hospital key don't existe create key in the dectionary
        
```
```python
for row in ActFactUp.itertuples():
    MarkovList[row[9]].append(row[7]) # append row[7] : A15 to the array for each hospital row[9] : Fk_hopt
```
## Now let's build our Transition matrix
Is a square matrix used to describe the transitions of a Markov chain Each of its entries is a nonnegative real number representing a probability .

## When testing

You can see that the test result are significant we didn't show the data from 2015 to the chain but it did predict that in the year 2015 49.99% of hospital activity will have the rate ** A ** wich means bad and for the real data we have 45% the same for ** B ** and ** C ** we have 26% ** B ** predicted for 21.5% in the real data and for ** C ** from 31.8% predicted compared to 33.5% 

it's pretty close Right :p
Now we can even forecast for years to come to see how the activity of hospitals will evolve,and we can see that from 2016 to 2020 ** A ** will decrease which mean less hospitals will have bad activity and ** B ** will increase significantly, which is logical due to the decrease of ** A ** that means more hospitals will have a good activity and finally for ** C ** we can say that it will be stable
## Finally 
This algorithm can be applied to different indicators in this big database, if you have any questions don't hesitate to contact me.

Good Read.
