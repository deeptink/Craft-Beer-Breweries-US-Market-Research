---
title: "CRAFT BEER BREWERIES US MARKET RESEARCH"
author: "Deepti Kunupudi & Johnny Gipson"
date: "October 20, 2018"
output: 
  html_document:
    keep_md: true
---

###Summary
Craft beer has taken over the U.S. landscape.  There are more than over 500 breweries that have created more than 2000 different kind of craft beers just in the last decade.  People are now giving anecdote about craft beer like they do fine wines.  Our craft enthusiast has taken it one step further by looking at quantitative variables like ABV (Alcohol by Volume of Beer), IBU (International Bitterness Units of Beer), and Gravity to help steer the novices beer drinker to their perfect beer. Our company is providing you with information of the craft beer landscape to help you make an inform decision about enetring the craft beer business. 

###R libraries

These libraries are needed to help perform the market analysis for craft beers and breweries.

```r
library(tidyr)
library(plyr)
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:plyr':
## 
##     arrange, count, desc, failwith, id, mutate, rename, summarise,
##     summarize
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library(bitops)
library(RCurl)
```

```
## 
## Attaching package: 'RCurl'
```

```
## The following object is masked from 'package:tidyr':
## 
##     complete
```

```r
library(xml2)
library(rvest)
library(XML)
```

```
## 
## Attaching package: 'XML'
```

```
## The following object is masked from 'package:rvest':
## 
##     xml
```

```r
library(ggplot2)
library(gcookbook)
library(data.table)
```

```
## 
## Attaching package: 'data.table'
```

```
## The following objects are masked from 'package:dplyr':
## 
##     between, first, last
```

```r
library(sqldf)
```

```
## Loading required package: gsubfn
```

```
## Loading required package: proto
```

```
## Warning in doTryCatch(return(expr), name, parentenv, handler): unable to load shared object '/Library/Frameworks/R.framework/Resources/modules//R_X11.so':
##   dlopen(/Library/Frameworks/R.framework/Resources/modules//R_X11.so, 6): Library not loaded: /opt/X11/lib/libSM.6.dylib
##   Referenced from: /Library/Frameworks/R.framework/Resources/modules//R_X11.so
##   Reason: image not found
```

```
## Warning in system2("/usr/bin/otool", c("-L", shQuote(DSO)), stdout = TRUE):
## running command ''/usr/bin/otool' -L '/Library/Frameworks/R.framework/
## Resources/library/tcltk/libs//tcltk.so'' had status 1
```

```
## Could not load tcltk.  Will use slower R code instead.
```

```
## Loading required package: RSQLite
```

###Datasets
The information was provided into 2 files -

* Beers.csv
* Breweries.csv

Beers.csv is a comma separated file with data for different beers and its different attributes based on brewery, style, alcohol content and ounces.

Breweries.csv is a comma separated file with information pertaining to brewery name and its geographic details by city and state in US.

###The Landscape of U.S Breweries by State 

The following steps are taken to read the datasets into R and further explore the data for analysis.

* Read the CSV file which is located on desktop. The path represents the file on individual computer/system. Later, create the dataframe "Breweries".

* Use R functions - *dim*, *str*, *head*, *tail* to examine the "Breweries" dataframe.  This allow to know the dimension, the type of variables, variable names, and scan the first and last ten row of the data.

* R functions - *summarize* and *count*  are used to count the breweries by state. The new data frame "Breweries_state" is created to store our results.



```r
Breweries<-read.csv(file='//Users//dee//Data Science//Case Study 1//Breweries.csv')
dim(Breweries) # dimensions
```

```
## [1] 558   4
```

```r
str(Breweries) # structure
```

```
## 'data.frame':	558 obs. of  4 variables:
##  $ Brew_ID: int  1 2 3 4 5 6 7 8 9 10 ...
##  $ Name   : Factor w/ 551 levels "10 Barrel Brewing Company",..: 355 12 266 319 201 136 227 477 59 491 ...
##  $ City   : Factor w/ 384 levels "Abingdon","Abita Springs",..: 228 200 122 299 300 62 91 48 152 136 ...
##  $ State  : Factor w/ 51 levels " AK"," AL"," AR",..: 24 18 20 5 5 41 6 23 23 23 ...
```

```r
head(Breweries,10) # Top 10 data
```

```
##    Brew_ID                         Name          City State
## 1        1           NorthGate Brewing    Minneapolis    MN
## 2        2    Against the Grain Brewery    Louisville    KY
## 3        3     Jack's Abby Craft Lagers    Framingham    MA
## 4        4    Mike Hess Brewing Company     San Diego    CA
## 5        5      Fort Point Beer Company San Francisco    CA
## 6        6        COAST Brewing Company    Charleston    SC
## 7        7 Great Divide Brewing Company        Denver    CO
## 8        8             Tapistry Brewing      Bridgman    MI
## 9        9             Big Lake Brewing       Holland    MI
## 10      10   The Mitten Brewing Company  Grand Rapids    MI
```

```r
tail(Breweries,10) # Bottom 10 data
```

```
##     Brew_ID                          Name          City State
## 549     549      Prescott Brewing Company      Prescott    AZ
## 550     550      Mogollon Brewing Company     Flagstaff    AZ
## 551     551    Wind River Brewing Company      Pinedale    WY
## 552     552             Silverton Brewery     Silverton    CO
## 553     553         Mickey Finn's Brewery  Libertyville    IL
## 554     554           Covington Brewhouse     Covington    LA
## 555     555               Dave's Brewfarm        Wilson    WI
## 556     556         Ukiah Brewing Company         Ukiah    CA
## 557     557       Butternuts Beer and Ale Garrattsville    NY
## 558     558 Sleeping Lady Brewing Company     Anchorage    AK
```

```r
Breweries_state<-summarise(group_by(Breweries,State), count=n())
Breweries_state # Look at the summarized the data
```

```
## # A tibble: 51 x 2
##    State count
##    <fct> <int>
##  1 " AK"     7
##  2 " AL"     3
##  3 " AR"     2
##  4 " AZ"    11
##  5 " CA"    39
##  6 " CO"    47
##  7 " CT"     8
##  8 " DC"     1
##  9 " DE"     2
## 10 " FL"    15
## # ... with 41 more rows
```

```r
Breweries_state<-data.frame(Breweries_state) # converting into data frame.
dim(Breweries_state) # dimensions
```

```
## [1] 51  2
```

```r
str(Breweries_state) # structure
```

```
## 'data.frame':	51 obs. of  2 variables:
##  $ State: Factor w/ 51 levels " AK"," AL"," AR",..: 1 2 3 4 5 6 7 8 9 10 ...
##  $ count: int  7 3 2 11 39 47 8 1 2 15 ...
```

```r
head(Breweries_state,10) # Top 10 data
```

```
##    State count
## 1     AK     7
## 2     AL     3
## 3     AR     2
## 4     AZ    11
## 5     CA    39
## 6     CO    47
## 7     CT     8
## 8     DC     1
## 9     DE     2
## 10    FL    15
```

* Visual presentation of Breweries by state.


```r
Breweries_graph<-ggplot(Breweries_state, aes(x=State,y=count, fill=State)) + geom_bar(stat="identity", colour="black", position=position_dodge(0.7))
Breweries_graph + ggtitle ("Number of Breweries per State ") + xlab("States") +ylab ("Number of Breweries")  + theme(axis.text.x = element_text(vjust=0.3,hjust=0.3, angle=90))+theme(plot.title=element_text(hjust=0.5,size=16,face="bold"))
```

![](CS_20181019_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

Based on the bar plot, Colorado has the most the breweries followed by California and interestingly states like West Virginia, South Dakota, North Dakota and the District of Columbia have only one brewery.


###Data Consolidation & Analytics on Breweries and Beers

* Data will be merged for Breweries and Beers datasets for further analysis.

* It will be merged using key column in both datasets.
    + Join - Brew_ID from Breweries and Brewery_id from Beers.

* A new dataframe "Brew_beer" is created for consolidated data.

* R functions - *dim*, *str*, *head*, *tail* to examine the "Brew_beer" dataframe.
  


```r
Beers<-read.csv(file='//Users//dee//Data Science//Case Study 1//Beers.csv')
dim(Beers) # Dimensions
```

```
## [1] 2410    7
```

```r
str(Beers) # Structure
```

```
## 'data.frame':	2410 obs. of  7 variables:
##  $ Name      : Factor w/ 2305 levels "#001 Golden Amber Lager",..: 1638 577 1704 1842 1819 268 1160 758 1093 486 ...
##  $ Beer_ID   : int  1436 2265 2264 2263 2262 2261 2260 2259 2258 2131 ...
##  $ ABV       : num  0.05 0.066 0.071 0.09 0.075 0.077 0.045 0.065 0.055 0.086 ...
##  $ IBU       : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ Brewery_id: int  409 178 178 178 178 178 178 178 178 178 ...
##  $ Style     : Factor w/ 100 levels "","Abbey Single Ale",..: 19 18 16 12 16 80 18 22 18 12 ...
##  $ Ounces    : num  12 12 12 12 12 12 12 12 12 12 ...
```

```r
head(Beers, 10) # top 10 rows
```

```
##                   Name Beer_ID   ABV IBU Brewery_id
## 1             Pub Beer    1436 0.050  NA        409
## 2          Devil's Cup    2265 0.066  NA        178
## 3  Rise of the Phoenix    2264 0.071  NA        178
## 4             Sinister    2263 0.090  NA        178
## 5        Sex and Candy    2262 0.075  NA        178
## 6         Black Exodus    2261 0.077  NA        178
## 7  Lake Street Express    2260 0.045  NA        178
## 8              Foreman    2259 0.065  NA        178
## 9                 Jade    2258 0.055  NA        178
## 10        Cone Crusher    2131 0.086  NA        178
##                             Style Ounces
## 1             American Pale Lager     12
## 2         American Pale Ale (APA)     12
## 3                    American IPA     12
## 4  American Double / Imperial IPA     12
## 5                    American IPA     12
## 6                   Oatmeal Stout     12
## 7         American Pale Ale (APA)     12
## 8                 American Porter     12
## 9         American Pale Ale (APA)     12
## 10 American Double / Imperial IPA     12
```

```r
tail(Beers,10) # bottom 10 rows
```

```
##                             Name Beer_ID   ABV IBU Brewery_id
## 2401                 Worthy Pale    1511 0.060  50        200
## 2402          Patty's Chile Beer    1345 0.042  NA        425
## 2403   Colorojo Imperial Red Ale    1316 0.082  NA        425
## 2404         Wynkoop Pumpkin Ale    1045 0.055  NA        425
## 2405 Rocky Mountain Oyster Stout    1035 0.075  NA        425
## 2406                   Belgorado     928 0.067  45        425
## 2407               Rail Yard Ale     807 0.052  NA        425
## 2408             B3K Black Lager     620 0.055  NA        425
## 2409         Silverback Pale Ale     145 0.055  40        425
## 2410        Rail Yard Ale (2009)      84 0.052  NA        425
##                         Style Ounces
## 2401  American Pale Ale (APA)     12
## 2402               Chile Beer     12
## 2403      American Strong Ale     12
## 2404              Pumpkin Ale     12
## 2405           American Stout     12
## 2406              Belgian IPA     12
## 2407 American Amber / Red Ale     12
## 2408              Schwarzbier     12
## 2409  American Pale Ale (APA)     12
## 2410 American Amber / Red Ale     12
```

```r
Brew_beer<-merge(Breweries,Beers, by.x=c("Brew_ID"),by.y=c("Brewery_id"))
dim(Brew_beer) # Dimensions
```

```
## [1] 2410   10
```

```r
str(Brew_beer) # Structure 
```

```
## 'data.frame':	2410 obs. of  10 variables:
##  $ Brew_ID: int  1 1 1 1 1 1 2 2 2 2 ...
##  $ Name.x : Factor w/ 551 levels "10 Barrel Brewing Company",..: 355 355 355 355 355 355 12 12 12 12 ...
##  $ City   : Factor w/ 384 levels "Abingdon","Abita Springs",..: 228 228 228 228 228 228 200 200 200 200 ...
##  $ State  : Factor w/ 51 levels " AK"," AL"," AR",..: 24 24 24 24 24 24 18 18 18 18 ...
##  $ Name.y : Factor w/ 2305 levels "#001 Golden Amber Lager",..: 1640 1926 1525 802 1258 2185 71 458 1218 43 ...
##  $ Beer_ID: int  2689 2688 2687 2692 2691 2690 2683 2686 2685 2684 ...
##  $ ABV    : num  0.06 0.06 0.056 0.045 0.049 0.048 0.042 0.08 0.125 0.077 ...
##  $ IBU    : int  38 25 47 50 26 19 42 68 80 25 ...
##  $ Style  : Factor w/ 100 levels "","Abbey Single Ale",..: 83 22 57 16 77 48 18 12 46 77 ...
##  $ Ounces : num  16 16 16 16 16 16 16 16 16 16 ...
```

```r
head(Brew_beer, 6) # top 6 rows
```

```
##   Brew_ID             Name.x        City State        Name.y Beer_ID   ABV
## 1       1 NorthGate Brewing  Minneapolis    MN       Pumpion    2689 0.060
## 2       1 NorthGate Brewing  Minneapolis    MN    Stronghold    2688 0.060
## 3       1 NorthGate Brewing  Minneapolis    MN   Parapet ESB    2687 0.056
## 4       1 NorthGate Brewing  Minneapolis    MN  Get Together    2692 0.045
## 5       1 NorthGate Brewing  Minneapolis    MN Maggie's Leap    2691 0.049
## 6       1 NorthGate Brewing  Minneapolis    MN    Wall's End    2690 0.048
##   IBU                               Style Ounces
## 1  38                         Pumpkin Ale     16
## 2  25                     American Porter     16
## 3  47 Extra Special / Strong Bitter (ESB)     16
## 4  50                        American IPA     16
## 5  26                  Milk / Sweet Stout     16
## 6  19                   English Brown Ale     16
```

```r
tail(Brew_beer, 6) # bottom 6 rows
```

```
##      Brew_ID                        Name.x          City State
## 2405     556         Ukiah Brewing Company         Ukiah    CA
## 2406     557       Butternuts Beer and Ale Garrattsville    NY
## 2407     557       Butternuts Beer and Ale Garrattsville    NY
## 2408     557       Butternuts Beer and Ale Garrattsville    NY
## 2409     557       Butternuts Beer and Ale Garrattsville    NY
## 2410     558 Sleeping Lady Brewing Company     Anchorage    AK
##                         Name.y Beer_ID   ABV IBU                   Style
## 2405             Pilsner Ukiah      98 0.055  NA         German Pilsener
## 2406         Porkslap Pale Ale      49 0.043  NA American Pale Ale (APA)
## 2407           Snapperhead IPA      51 0.068  NA            American IPA
## 2408         Moo Thunder Stout      50 0.049  NA      Milk / Sweet Stout
## 2409  Heinnieweisse Weissebier      52 0.049  NA              Hefeweizen
## 2410 Urban Wilderness Pale Ale      30 0.049  NA        English Pale Ale
##      Ounces
## 2405     12
## 2406     12
## 2407     12
## 2408     12
## 2409     12
## 2410     12
```

Based on the initial data scan, we have NAs in IBU and ABV columns. It is an indication of missing values.

*NA - Not Available*

**NA Metrics in Consolidated Data**

Scanning the data for any NAs after merging is always a good practice.

Following R function provides the count of NAs based on each column. It is a custom function created to find the count by column.


```r
sum(is.na(Brew_beer))
```

```
## [1] 1067
```

```r
sapply(Brew_beer,function(x)sum(is.na(x)))
```

```
## Brew_ID  Name.x    City   State  Name.y Beer_ID     ABV     IBU   Style 
##       0       0       0       0       0       0      62    1005       0 
##  Ounces 
##       0
```

We currently have 1067 NAs in the entire dataframe.  1005 of 1067 NAs lie in the IBU (International Bitterness Units) column and the rest of the NAs are in the ABV (Alcohol by Volume) column, respectively.

###Compute the median ABV and IBU for each state and Visualize

* R functions - *tapply* and *aggregate* are used.

* New dataframes "State_ABV" and "State_IBU" are created for individual median computation for IBU and ABV.

* Consolidated dataframe - "State_beer" is created for visualization.


```r
median_woNAs <- function(x) {median(x[which(!is.na(x))])}
State_ABV<-tapply(Brew_beer$ABV, Brew_beer$State,median_woNAs)
State_IBU<-tapply(Brew_beer$IBU, Brew_beer$State,median_woNAs)
State_ABV<-aggregate(Brew_beer$ABV, list(Brew_beer$State),median_woNAs)
State_IBU<-aggregate(Brew_beer$IBU, list(Brew_beer$State),median_woNAs)
colnames(State_ABV) <-c("State","ABV_Median")
colnames(State_IBU) <-c("State","IBU_Median")
State_beer<-merge(State_ABV,State_IBU, by= "State")
dim(State_beer)# Dimensions
```

```
## [1] 51  3
```

```r
str(State_beer)# Structure
```

```
## 'data.frame':	51 obs. of  3 variables:
##  $ State     : Factor w/ 51 levels " AK"," AL"," AR",..: 1 2 3 4 5 6 7 8 9 10 ...
##  $ ABV_Median: num  0.056 0.06 0.052 0.055 0.058 0.0605 0.06 0.0625 0.055 0.057 ...
##  $ IBU_Median: num  46 43 39 20.5 42 40 29 47.5 52 55 ...
```

```r
head(State_beer)# Top 6 rows
```

```
##   State ABV_Median IBU_Median
## 1    AK     0.0560       46.0
## 2    AL     0.0600       43.0
## 3    AR     0.0520       39.0
## 4    AZ     0.0550       20.5
## 5    CA     0.0580       42.0
## 6    CO     0.0605       40.0
```

```r
tail(State_beer)# bottom 6 rows
```

```
##    State ABV_Median IBU_Median
## 46    VA     0.0565       42.0
## 47    VT     0.0550       30.0
## 48    WA     0.0555       38.0
## 49    WI     0.0520       19.0
## 50    WV     0.0620       57.5
## 51    WY     0.0500       21.0
```

* Visualization of median ABV and IBU -


```r
States_graphabv<-ggplot(State_beer, aes(x=State,y=ABV_Median, fill=State)) + geom_bar(stat="identity", colour="black", position=position_dodge(0.7))
States_graphabv + ggtitle ("ABV Median by State") + xlab("States") +ylab ("ABV Median") + theme(axis.text.x = element_text(vjust=0.3,hjust=0.3, angle=90))+theme(plot.title=element_text(hjust=0.5,size=16,face="bold"))
```

![](CS_20181019_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

```r
States_graphibu<-ggplot(State_beer, aes(x=State,y=IBU_Median, fill=State)) + geom_bar(stat="identity", colour="black", position=position_dodge(0.7))
States_graphibu + ggtitle ("IBU Median by State") + xlab("States") +ylab ("IBU Median") + theme(axis.text.x = element_text(vjust=0.3,hjust=0.3, angle=90))+theme(plot.title=element_text(hjust=0.5,size=16,face="bold"))
```

```
## Warning: Removed 1 rows containing missing values (geom_bar).
```

![](CS_20181019_files/figure-html/unnamed-chunk-6-2.png)<!-- -->

State South Dakota (SD) breweries have no IBUs in the data set. 


###State with Maximum ABV


```r
sqldf("select State from Brew_beer where ABV=(Select max(ABV) from Brew_beer)")
```

```
##   State
## 1    CO
```

Colorado reported the maximum ABV (Alcohol by Volume).

###State with Most IBU

```r
sqldf("select State from Brew_beer where IBU=(Select max(IBU) from Brew_beer)")
```

```
##   State
## 1    OR
```

Oregon reported the most IBU (International Bitter Units).

###Summary statistics for the ABV Variable

* The ABV variables has median and mean of 0.056 and .0558, respectively.

* With the mean and median close to together we may have normal distribution. 



```r
summary(State_beer$ABV)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
## 0.04000 0.05500 0.05600 0.05585 0.05800 0.06250
```

```r
Brew_beer<-na.omit(Brew_beer)
summary(Brew_beer$ABV)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
## 0.02700 0.05000 0.05700 0.05991 0.06800 0.12500
```

* The summary function shows both datasets to have close to the same mean and median.

* The ABV medians are 0.056 and 0.057 for the "State_beer" and "Beer_brew" dataframes respectively.

**Box Plot**

* Visually representing the data through Box plot.


```r
boxplot(Brew_beer$ABV,data=Brew_beer)
```

![](CS_20181019_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

**Histogram**


```r
hist(Brew_beer$ABV)
```

![](CS_20181019_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

##Relationship Between ABV and IBU

**Scatter Plot**


```r
ggplot(Brew_beer, aes(x=IBU, y= ABV)) +
  geom_point(shape=1) +
  theme(axis.text.x=element_text(size=rel(1.0)))+
ggtitle("Correlation between IBU and ABV ") +
  labs(x="IBU",y="ABV")
```

![](CS_20181019_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

* The scatter plot visually shows that there is some correlation between ABV and IBU.

* Adding a regression line to the data evidently shows the relationship.


```r
ggplot(Brew_beer, aes(x=IBU, y= ABV)) +
  geom_point(shape=1) +
   geom_smooth(method=lm) + 
  theme(axis.text.x=element_text(size=rel(1.0)))+
ggtitle("Correlation between IBU and ABV - with Regression Line ") +
  labs(x="IBU",y="ABV")
```

![](CS_20181019_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

* The regression line above, shows that it is a positive linear relationship between ABV and IBU.

* Executing the Pearson's Correlation Coefficient on variables ABV and IBU.


```r
cor(Brew_beer$IBU, Brew_beer$ABV ,method="pearson") 
```

```
## [1] 0.6706215
```

* Pearson's Correlation Coefficient r = *0.6706215*. It indicates that the correlation between ABV and IBU for this dataset.  

* Conducting a correlation test shows more evidence about the association between paired samples(ABV and IBU).


```r
cor.test(Brew_beer$ABV,Brew_beer$IBU,method="pearson")
```

```
## 
## 	Pearson's product-moment correlation
## 
## data:  Brew_beer$ABV and Brew_beer$IBU
## t = 33.863, df = 1403, p-value < 2.2e-16
## alternative hypothesis: true correlation is not equal to 0
## 95 percent confidence interval:
##  0.6407982 0.6984238
## sample estimates:
##       cor 
## 0.6706215
```

* At 95 % confidence interval, *t-statistic: 33.863* and *p-value: <2.2e-16*

* With p-value < 2.2e-16, we reject the null hypothesis, indicating that there is relationship between ABV and IBU statistically.



```r
x1<-lm(ABV~IBU,data=Brew_beer)
summary(x1)
```

```
## 
## Call:
## lm(formula = ABV ~ IBU, data = Brew_beer)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.033288 -0.005946 -0.001595  0.004022  0.052006 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 4.493e-02  5.177e-04   86.79   <2e-16 ***
## IBU         3.508e-04  1.036e-05   33.86   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.01007 on 1403 degrees of freedom
## Multiple R-squared:  0.4497,	Adjusted R-squared:  0.4493 
## F-statistic:  1147 on 1 and 1403 DF,  p-value: < 2.2e-16
```

* The coefficient of determination , *r-squared is 0.4497*, indicating that the 44.97& ~ 45% of the variation in the ABV value is reduced by taking into account IBV or it can also be exaplined as 45% of the variation in ABV is 'due to' or 'explained by' IBV.

* Additionally to add more support to evidence, the below plots show more information.


```r
par(mfrow=c(2,2))
plot(x1)
```

![](CS_20181019_files/figure-html/unnamed-chunk-17-1.png)<!-- -->

* We could explain 45% of variation but what about the 55%? 

* Let’s dig further into aspects on the data. Based on the styles data in Beers.csv file, let us categorize the styles in different categories -
    + APA
    + IPA
    + Lager
    + Other
    + Other Ale
    
* Upload the new data to R environment and explore further in one of the options of influencers. It is just a preliminary.



```r
BeersStyle<-read.csv(file='//Users//dee//Data Science//Case Study 1//Beers_Type.csv')
head(BeersStyle,n=2) # top 2 records
```

```
##          Name Beer_ID   ABV IBU Brewery_id                   Style Ounces
## 1    Pub Beer    1436 0.050  NA        409     American Pale Lager     12
## 2 Devil's Cup    2265 0.066  NA        178 American Pale Ale (APA)     12
##    Type
## 1 Lager
## 2   APA
```

* Scatter plot based on types indicates that there might be an evidence that style type may also influence the correlation.


```r
p1<-ggplot(BeersStyle,aes(x=IBU, y=ABV,color=Type))+geom_point()
p1 + geom_smooth(method=lm)
```

```
## Warning: Removed 1005 rows containing non-finite values (stat_smooth).
```

```
## Warning: Removed 1005 rows containing missing values (geom_point).
```

![](CS_20181019_files/figure-html/unnamed-chunk-19-1.png)<!-- -->

###Conclusion

With p-value of <2.23-16 we can conclude that there is relationship between ABV and IBU statistically. Based on coefficient of determination, we could attribute to 45% of the variation in the ABV value is reduced by taking into account IBV **or** it can also be explained as 45% of the variation in ABV is 'due to' or 'explained by' IBV.

The remaining 55% can be attributed to other parameters which are clearly depicted -

  + **Data completeness in question**
      + From the datasets we can see that we have missing data for values which are attributed to finding the relationship between ABV and IBU.
      + 2.5% of ABV attribute values are missing.
      + 41% of IBU attribute values are missing. 
        
  + **ABV and IBU relationship**
      + Do we have to consider any other attributes or any other data needs to be considered?
        
  + **Influencers**
      + Do styles have any influence on ABV and IBV? 
      + Does geographical patterns / Cultural impact need to be considered?
        
The analysis generated more questions on further variables which may need to be considered but the initial analysis provides evidence that there is a positive correlation between ABV and IBU(just an association but not casuation).


###References

https://www.craftbeer.com/beer/what-is-craft-beer

