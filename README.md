Abstract
--------

This project focuses on using basic survival analysis techniques to
determine factors influencing career length of NFL running backs,
employing Kaplan-Meier esimates and Cox Proportional Hazards modeling
procedures with the aid of RStudio and its
[survival](https://github.com/cran/survival) library. We extract data
from pro-football-reference.com using .csv files for career statistics
and [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/) for
physical measurements and maintain our dataset using
[pandas](http://pandas.pydata.org/). We present our results in a
visually appealing and easily comprehensible manner through the use of
[ggplot2](https://github.com/tidyverse/ggplot2), in addition to other R
libraries employed for analytical purposes.

Contributors
------------

-   Brian Luu
-   Kevin Wang
-   John Randazzo

Motivation
==========

## Runningbacks don't last very long in the NFL

<img src='https://github.com/johnrandazzo/surv_nflrb/blob/master/figure-markdown_strict/giphy%20(1).gif' >

<img src='https://github.com/johnrandazzo/surv_nflrb/blob/master/figure-markdown_strict/giphy-tumblr.gif' >

<img src='https://github.com/johnrandazzo/surv_nflrb/blob/master/figure-markdown_strict/statistic_id240102_average-length-of-player-careers-in-the-nfl.png' >

Methodology- Web Scraping
=========================

The data was primarily gathered from here(<http://www.pro-football-reference.com/draft/>) by simply downloading the csv of drafted players. Some additional data about player height and weight was also scraped using BeautifulSoup on the same website.

The final data looked like this:

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Year</th>
      <th>Rnd</th>
      <th>Pick</th>
      <th>Player</th>
      <th>Pos</th>
      <th>DrAge</th>
      <th>Tm</th>
      <th>From</th>
      <th>To</th>
      <th>AP1</th>
      <th>...</th>
      <th>Att</th>
      <th>Yds</th>
      <th>TD</th>
      <th>Rec</th>
      <th>Yds.1</th>
      <th>TD.1</th>
      <th>College/Univ</th>
      <th>PFR_URL</th>
      <th>Height</th>
      <th>Weight</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>35</th>
      <td>2015</td>
      <td>5</td>
      <td>138</td>
      <td>David Cobb</td>
      <td>RB</td>
      <td>22.0</td>
      <td>TEN</td>
      <td>2015.0</td>
      <td>2015.0</td>
      <td>0</td>
      <td>...</td>
      <td>52.0</td>
      <td>146.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>-2.0</td>
      <td>0.0</td>
      <td>Minnesota</td>
      <td>http://www.pro-football-reference.com/players/...</td>
      <td>71</td>
      <td>229</td>
    </tr>
    <tr>
      <th>37</th>
      <td>2015</td>
      <td>5</td>
      <td>155</td>
      <td>Karlos Williams</td>
      <td>RB</td>
      <td>22.0</td>
      <td>BUF</td>
      <td>2015.0</td>
      <td>2015.0</td>
      <td>0</td>
      <td>...</td>
      <td>93.0</td>
      <td>517.0</td>
      <td>7.0</td>
      <td>11.0</td>
      <td>96.0</td>
      <td>2.0</td>
      <td>Florida St.</td>
      <td>http://www.pro-football-reference.com/players/...</td>
      <td>73</td>
      <td>225</td>
    </tr>
    <tr>
      <th>40</th>
      <td>2015</td>
      <td>6</td>
      <td>205</td>
      <td>Josh Robinson</td>
      <td>RB</td>
      <td>23.0</td>
      <td>IND</td>
      <td>2015.0</td>
      <td>2015.0</td>
      <td>0</td>
      <td>...</td>
      <td>17.0</td>
      <td>39.0</td>
      <td>0.0</td>
      <td>6.0</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>Mississippi St.</td>
      <td>http://www.pro-football-reference.com/players/...</td>
      <td>69</td>
      <td>215</td>
    </tr>
    <tr>
      <th>43</th>
      <td>2015</td>
      <td>7</td>
      <td>231</td>
      <td>Joey Iosefa</td>
      <td>FB</td>
      <td>24.0</td>
      <td>TAM</td>
      <td>2015.0</td>
      <td>2015.0</td>
      <td>0</td>
      <td>...</td>
      <td>15.0</td>
      <td>51.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Hawaii</td>
      <td>http://www.pro-football-reference.com/players/...</td>
      <td>72</td>
      <td>245</td>
    </tr>
    <tr>
      <th>45</th>
      <td>2014</td>
      <td>2</td>
      <td>54</td>
      <td>Bishop Sankey</td>
      <td>RB</td>
      <td>21.0</td>
      <td>TEN</td>
      <td>2014.0</td>
      <td>2015.0</td>
      <td>0</td>
      <td>...</td>
      <td>199.0</td>
      <td>762.0</td>
      <td>3.0</td>
      <td>32.0</td>
      <td>272.0</td>
      <td>1.0</td>
      <td>Washington</td>
      <td>http://www.pro-football-reference.com/players/...</td>
      <td>70</td>
      <td>209</td>
    </tr>
  </tbody>
</table>
<p>5 rows x 25 columns</p>
</div>


Methodology- Analyis
====================

Our analysis will employ the theory of Survival Analysis, which measures
survival probability and instantaneous rate of hazard for an event of
interest over a given time period.

We are interested in the amount of games (our time variable) it takes
for an NFL runningback's professional career to end (our event of
interest). Thanks to our web scraping process, we now have a large,
informative and (somewhat) tidy dataset. 

A Brief Overview of Survival Analysis
-------------------------------------

-   Two primary variables of interest for building models:
    -   Duration of time until event or censoring
    -   Binary indicator of event for each observation
        -   0; censored (left the study or did not experience event
            during study)
        -   1; experienced the event
-   Let T = Failure time (in the context of our study, T = games played
    until retirement)

Kaplan-Meier Estimates:
-----------------------

We estimate the survival function using Kaplan-Meier Estimation. These are best used when
considering the entire subject population's aggregate survival with no
additional covariates, or with a few discrete valued covariate levels.
We make use of the ggsurv function here to create aesthetically pleasing
survival plots:

<img src='https://github.com/johnrandazzo/surv_nflrb/blob/master/figure-markdown_strict/unnamed-chunk-10-3.png' >

<img src='https://github.com/johnrandazzo/surv_nflrb/blob/master/figure-markdown_strict/unnamed-chunk-10-4.png' >





Cox Models:
-----------

The KM Estimator is limited because it only considers a single
homogenous population at a time. We use the Cox Proportional Hazards
model to measure the effects of particular covariates on career
survival, or more specifically, the instantaneous rates of hazard. With
this very handy tool, we are able to judge the level at which covariates
such as BMI influence a player's career length. 

Using AIC/BIC as a criterion for our model we find that our three most
significant covariates are BMI, Yards/Carry and Draft Age. Our model is
given by:

*h*(*t*, *X*)=*h*<sub>0</sub>(*t*)\**e**x**p*(( − .0770 \* *B**M**I*)+(−.2042 \* *Y**P**C*)+(.1749 \* *D**r**a**f**t**A**g**e*))

The Proportional Hazards Assumption
-----------------------------------

The key assumption for the Cox Model is that covariate effects on
survival are independent of time. We can test this using Schoenfeld
Residuals. We are looking for a mean of 0 for the entire time duration,
which suggests that errors are evenly distributed over time. Further,
there is a p-value associated to each covariate. The hypotheses yielding
each probability measure are:

*H*<sub>0</sub>: The covariate's effect is independent of time
*H*<sub>1</sub>: The covariate's effect exhibits time-dependency

A low p-value indicates that we should consider omitting the associated
covariate.
<img src='https://github.com/johnrandazzo/surv_nflrb/blob/master/figure-markdown_strict/unnamed-chunk-12-1.png' >

We are thrilled with these results. Our model very much aligns with the
Proportional Hazards assumption.

Examining Our Model's Fit
-------------------------

Now that we have a legitimate model in our hands, we can visualize the
effects of different covariate levels on career survival:

<img src='https://github.com/johnrandazzo/surv_nflrb/blob/master/figure-markdown_strict/bmi.png' >
<img src='https://github.com/johnrandazzo/surv_nflrb/blob/master/figure-markdown_strict/npc.png' >
<img src='https://github.com/johnrandazzo/surv_nflrb/blob/master/figure-markdown_strict/drage.png' >

Summary and Resources
---------------------

For this project, our goal was to examine the statistical effects of
career statistics, accolades and physical measurements on the career
lengths of runningbacks in the NFL. Upon obtaining the dataset via
Python methods, we switched to R and implemented theoretical tools of survival
analysis such as the Kaplan Meier estimator and the Cox Proportional
Hazards model. We found that there are three highly significant and
time-independent covariates which can tell us a great deal about an NFL
running back's potential career length: the age at which a player was
drafted, the player's BMI, and the player's Yards per Carry statistic.

Key Results
--------------------------

1.  Our estimated career survival curve for NFL RBs fits a Generalized
    Gamma Distribution with parameters (mu = 4.3805, sigma = .7168, Q
    = 1.694).

2.  BMI is highly significant in predicting career length, having the
    largest magnitude of the three predictors in our model. Players with
    a higher computed BMI will last longer in the league. Since BMI
    loses predictive power in terms of indicating obesity when
    considering extremely muscular and athletic individuals (such as
    NFL RBs) the measure becomes one of body density. Specifically, the
    stockier a RB's build is, the longer we can expect them to last in
    the league.

3.  Draft Age is highly significant in predicting career length. On
    average, the earlier a player enters the NFL, the longer it will
    take for their athletic ability to decrease to the point of seeking
    retirement from the league.

4.  Yards per carry is also very significant. As an average measure, it
    is a prime indicator of how *good* an NFL RB is in their career. Of
    course, players with higher YPC can be expected to have lasted
    longer in the league.

5.  All-Pro and Pro-Bowl status are indicators of a player's quality of
    play through their career. Perhaps trivially, players with these
    accolades lasted far longer than those who never acheived them. We
    found that All-Pro was a significantly better predictor of career
    longevity than Pro-Bowl.

Future Work
---------------
Normally, this is where you would put possible improvements to your model or things that you are currently working on (ie. what you plan to get done in time for project showcaase). However, this is a finished project, so it has no future work to add.

Acknowledgments
---------------

<http://stackoverflow.com/questions/26296020/github-displays-all-code-chunks-from-readme-rmd-despite-include-false>

<https://www.r-statistics.com/2013/07/creating-good-looking-survival-curves-the-ggsurv-function/>

<https://www.stat.ubc.ca/~rollin/teach/643w04/lec/node69.html>
