# R Code for edX Competition on *"Analytics Edge"* DataScience Course Using Logistic Regression
================================================================================================

#### Author: Fahad Usman
#### Start Date: 21 April 2014

## The Problem Statement: Learn what predicts happiness by using informal polling questions. 

What predicts happiness? In this competition, you'll be using data from Show of Hands, an informal polling platform for use on mobile devices and the web, to see what aspects and characteristics of people's lives predict happiness.

Show of Hands has been downloaded over 300,000 times across Apple and Android app stores, and users have cast more than 75 million votes. In this problem, we'll use data from thousands of users and one hundred different questions to see which responses predict happiness.

## Acknowledgements

This competition is brought to you by 15.071x, edX, and Show of Hands.

## File descriptions

Here is a description of the files you have been provided for this competition:

- **train.csv** - the training set of data that you should use to build your models
- **test.csv**  - the test set that you will be evaluated on. It contains all of the independent variables, but not the dependent variable.
- **sampleSubmission.csv** - a sample submission file in the correct format.
- **Questions.pdf** - the question test corresponding to each of the question codes, as well as the possible answers.

**Note:**  You might be wondering why the dependent variable is not there in the test set because that is what a test set is meant to be in the true sense. Your model might do well on the training data but the challenge is to make it flexible enough so that it can predict well enough on any unseen data.

*For example*, if you are predicting an election winner for the current election based on opinion polls you always do that before the actual outcome comes in i.e. you do not have a dependent variable in your test set(current election) but have the dependent variable in your train set(previous elections).

When you start working with predictions for the first time, you might be working with dependenable variables in both training and testing sets...you might be wondering why we should be given the dependent variable in test set then. That is probably done to make us understand the importance of the fact that what works well on a train set might not work so well on a test set.

## Data fields

- **UserID** - an anonymous id unique to a given user
- **YOB** - the year of birth of the user
- **Gender** - the gender of the user, either Male, Female, or not provided
- **Income** - the household income of the user. Either not provided, or one of "under $25,000", "$25,001 - $50,000", "$50,000 - $74,999", "$75,000 - $100,000", "$100,001 - $150,000", or "over $150,000".
- **HouseholdStatus** - the household status of the user. Either not provided, or one of "Domestic Partners (no kids)", "Domestic Partners (w/kids)", "Married (no kids)", "Married (w/kids)", "Single (no kids)", or "Single (w/kids)".
- **EducationLevel** - the education level of the user. Either not provided, or one of "Current K-12", "High School Diploma", "Current Undergraduate", "Associate's Degree", "Bachelor's Degree", "Master's Degree", or "Doctoral Degree".
- **Party** - the political party of the user. Either not provided, or one of "Democrat", "Republican", "Independent", "Libertarian", or "Other".
- **Happy** - a binary variable, with value 1 if the user said they were happy, and with value 0 if the user said that were neutral or not happy. This is the variable you are trying to predict.
- **Q124742, Q124122, . . . , Q96024** - 101 different questions that the users were asked on Show of Hands. If the user didn't answer the question, there is a blank. For information about the question text and possible answers, see the file Questions.pdf.
- **votes** - the total number of questions that the user responded to, out of the 101 questions included in the data set (this count does not include the happiness question).

**Note** - Dependent variable is ***Happy*** and is a binary variable. This is also classed as *categorical variable* which only takes two possible values either a person is happy (1) or not (0)...

## Linear Regression Vs Logistic Regression


In the **Linear regression** model the dependent variable y is considered continuous, whereas in **Logistic regression** it is categorical, i.e., discrete. In application, the linear regression is used in regression settings while the logistic regression is used for binary classification or multi-class classification (where it is called multinomial logistic regression). 

In other words:

**Linear regression** is regression when the X variable (the predictor or the independent variable) and the Y variable (the response or the dependent variable) are *both* continuous and linearly related, so the response will **increase or decrease** at a *constant ratio* to the *predictor*. you can also have more then one predictor, which give you regression in multiple dimensions, but in the end it comes down to a set increase/decrease in the response for every one unit increase in the predictor(s).

**Logistic regression** is quite different, the predictor/independent variable is continuous, but the response/dependent variable categorical or dichotomous (only 2 options). The logistic regression provides you with the probability of an event occurring. For every one unit increase in the predictor/independent variable, the probability of an even occurring increases/decreases. However what is actually observed is whether or not the event occurs, the logistics regression can only be tested by seeing whether it's predictions come true. 

We have seen linear regression as a way of predicting continuous outcomes. Of course, we can utilize linear regression to predict happiness here, but then we have to round the outcome to 0 or 1. Instead we will use the logistic regression, which is an extension of linear regression, to environments where the dependent variable is categorical. In our case, 0 or 1. to start with as explained above.

Let's see if we can build a logistic regression model in R to better predict happiness...To begin, read in the train file provided:





```r
Train = read.csv("C:/Users/607518069/Documents/R Projects/edXCompetition/Data/train.csv")
Test = read.csv("C:/Users/607518069/Documents/R Projects/edXCompetition/Data/test.csv")
```


Always check the data structure first, So we can take a look at the structure of our new data frame by using the str and summary functions:


```r
str(Train)
```

```
## 'data.frame':	4619 obs. of  110 variables:
##  $ UserID         : int  1 2 5 6 7 8 9 11 12 13 ...
##  $ YOB            : int  1938 1985 1963 1997 1996 1991 1995 1983 1984 1997 ...
##  $ Gender         : Factor w/ 3 levels "","Female","Male": 3 2 3 3 3 2 3 3 2 2 ...
##  $ Income         : Factor w/ 7 levels "","$100,001 - $150,000",..: 1 3 6 5 4 7 5 2 4 6 ...
##  $ HouseholdStatus: Factor w/ 7 levels "","Domestic Partners (no kids)",..: 5 6 5 6 6 6 6 5 5 6 ...
##  $ EducationLevel : Factor w/ 8 levels "","Associate's Degree",..: 1 8 1 7 4 5 4 3 7 4 ...
##  $ Party          : Factor w/ 6 levels "","Democrat",..: 3 2 1 6 1 1 6 3 6 2 ...
##  $ Happy          : int  1 1 0 1 1 1 1 1 0 0 ...
##  $ Q124742        : Factor w/ 3 levels "","No","Yes": 2 1 2 1 2 3 1 2 2 1 ...
##  $ Q124122        : Factor w/ 3 levels "","No","Yes": 1 3 3 3 2 3 1 3 3 1 ...
##  $ Q123464        : Factor w/ 3 levels "","No","Yes": 2 2 2 3 2 2 1 2 2 1 ...
##  $ Q123621        : Factor w/ 3 levels "","No","Yes": 2 3 3 2 2 1 1 3 2 1 ...
##  $ Q122769        : Factor w/ 3 levels "","No","Yes": 2 2 2 1 3 1 1 2 2 2 ...
##  $ Q122770        : Factor w/ 3 levels "","No","Yes": 3 2 2 3 3 1 1 2 3 3 ...
##  $ Q122771        : Factor w/ 3 levels "","Private","Public": 3 3 2 2 3 3 1 3 3 3 ...
##  $ Q122120        : Factor w/ 3 levels "","No","Yes": 2 2 2 2 2 3 1 2 2 2 ...
##  $ Q121699        : Factor w/ 3 levels "","No","Yes": 3 3 3 2 2 3 2 3 3 2 ...
##  $ Q121700        : Factor w/ 3 levels "","No","Yes": 2 3 2 2 3 3 2 2 2 2 ...
##  $ Q120978        : Factor w/ 3 levels "","No","Yes": 1 3 2 3 3 2 2 3 3 3 ...
##  $ Q121011        : Factor w/ 3 levels "","No","Yes": 2 2 2 2 2 3 3 2 3 2 ...
##  $ Q120379        : Factor w/ 3 levels "","No","Yes": 2 3 3 2 3 3 2 2 2 3 ...
##  $ Q120650        : Factor w/ 3 levels "","No","Yes": 3 3 3 3 3 2 3 3 3 3 ...
##  $ Q120472        : Factor w/ 3 levels "","Art","Science": 1 3 3 3 3 2 3 3 2 3 ...
##  $ Q120194        : Factor w/ 3 levels "","Study first",..: 3 2 3 2 2 3 3 3 3 3 ...
##  $ Q120012        : Factor w/ 3 levels "","No","Yes": 2 3 3 1 2 3 2 2 3 3 ...
##  $ Q120014        : Factor w/ 3 levels "","No","Yes": 2 3 2 3 3 1 3 3 2 3 ...
##  $ Q119334        : Factor w/ 3 levels "","No","Yes": 1 3 2 2 2 3 2 3 2 2 ...
##  $ Q119851        : Factor w/ 3 levels "","No","Yes": 3 2 2 3 2 2 3 2 2 3 ...
##  $ Q119650        : Factor w/ 3 levels "","Giving","Receiving": 1 2 2 3 2 1 2 2 2 3 ...
##  $ Q118892        : Factor w/ 3 levels "","No","Yes": 3 3 3 2 3 2 1 3 2 2 ...
##  $ Q118117        : Factor w/ 3 levels "","No","Yes": 3 2 2 3 3 3 1 2 2 2 ...
##  $ Q118232        : Factor w/ 3 levels "","Idealist",..: 2 2 3 3 3 1 1 2 2 3 ...
##  $ Q118233        : Factor w/ 3 levels "","No","Yes": 2 2 2 2 2 2 1 2 3 2 ...
##  $ Q118237        : Factor w/ 3 levels "","No","Yes": 2 3 3 3 2 2 1 2 3 2 ...
##  $ Q117186        : Factor w/ 3 levels "","Cool headed",..: 1 2 2 2 1 3 1 2 3 1 ...
##  $ Q117193        : Factor w/ 3 levels "","Odd hours",..: 1 2 3 2 3 3 1 3 3 3 ...
##  $ Q116797        : Factor w/ 3 levels "","No","Yes": 3 3 2 2 2 1 1 2 2 1 ...
##  $ Q116881        : Factor w/ 3 levels "","Happy","Right": 2 2 3 3 2 2 1 2 2 1 ...
##  $ Q116953        : Factor w/ 3 levels "","No","Yes": 3 3 3 3 1 3 3 3 3 1 ...
##  $ Q116601        : Factor w/ 3 levels "","No","Yes": 3 3 3 2 3 3 1 3 3 1 ...
##  $ Q116441        : Factor w/ 3 levels "","No","Yes": 2 2 2 2 2 2 1 2 2 1 ...
##  $ Q116448        : Factor w/ 3 levels "","No","Yes": 2 3 3 3 2 2 1 2 3 1 ...
##  $ Q116197        : Factor w/ 3 levels "","A.M.","P.M.": 3 2 2 2 2 3 1 2 3 1 ...
##  $ Q115602        : Factor w/ 3 levels "","No","Yes": 3 3 3 3 3 2 1 3 2 1 ...
##  $ Q115777        : Factor w/ 3 levels "","End","Start": 3 2 3 3 3 3 1 3 2 1 ...
##  $ Q115610        : Factor w/ 3 levels "","No","Yes": 3 3 3 3 3 1 1 3 2 1 ...
##  $ Q115611        : Factor w/ 3 levels "","No","Yes": 2 2 3 3 2 2 1 2 2 1 ...
##  $ Q115899        : Factor w/ 3 levels "","Circumstances",..: 2 3 3 2 2 3 1 2 3 1 ...
##  $ Q115390        : Factor w/ 3 levels "","No","Yes": 3 2 2 2 1 2 3 3 2 1 ...
##  $ Q114961        : Factor w/ 3 levels "","No","Yes": 3 3 2 3 2 3 2 2 3 1 ...
##  $ Q114748        : Factor w/ 3 levels "","No","Yes": 3 2 2 2 3 3 3 2 3 1 ...
##  $ Q115195        : Factor w/ 3 levels "","No","Yes": 3 3 3 3 3 2 3 3 3 1 ...
##  $ Q114517        : Factor w/ 3 levels "","No","Yes": 2 3 2 3 2 2 2 2 3 1 ...
##  $ Q114386        : Factor w/ 3 levels "","Mysterious",..: 1 3 3 2 2 3 3 3 3 1 ...
##  $ Q113992        : Factor w/ 3 levels "","No","Yes": 3 1 3 2 2 2 2 2 3 1 ...
##  $ Q114152        : Factor w/ 3 levels "","No","Yes": 3 2 2 2 3 2 2 2 2 1 ...
##  $ Q113583        : Factor w/ 3 levels "","Talk","Tunes": 2 3 2 3 3 3 3 2 3 1 ...
##  $ Q113584        : Factor w/ 3 levels "","People","Technology": 3 2 2 3 2 1 3 2 2 1 ...
##  $ Q113181        : Factor w/ 3 levels "","No","Yes": 2 3 3 3 2 3 3 2 2 1 ...
##  $ Q112478        : Factor w/ 3 levels "","No","Yes": 2 3 3 3 2 1 2 3 2 1 ...
##  $ Q112512        : Factor w/ 3 levels "","No","Yes": 3 2 3 3 3 3 3 3 3 1 ...
##  $ Q112270        : Factor w/ 3 levels "","No","Yes": 1 3 2 3 2 2 2 3 2 1 ...
##  $ Q111848        : Factor w/ 3 levels "","No","Yes": 2 3 3 2 3 3 1 3 2 3 ...
##  $ Q111580        : Factor w/ 3 levels "","Demanding",..: 2 3 3 3 3 3 1 3 2 3 ...
##  $ Q111220        : Factor w/ 3 levels "","No","Yes": 2 2 2 2 2 3 1 2 3 2 ...
##  $ Q110740        : Factor w/ 3 levels "","Mac","PC": 1 1 3 3 2 3 1 2 3 3 ...
##  $ Q109367        : Factor w/ 3 levels "","No","Yes": 2 3 2 2 2 1 2 3 3 1 ...
##  $ Q108950        : Factor w/ 3 levels "","Cautious",..: 2 2 3 2 2 2 1 3 2 1 ...
##  $ Q109244        : Factor w/ 3 levels "","No","Yes": 2 3 2 2 2 1 2 2 2 1 ...
##  $ Q108855        : Factor w/ 3 levels "","Umm...","Yes!": 3 3 3 2 3 3 1 2 3 1 ...
##  $ Q108617        : Factor w/ 3 levels "","No","Yes": 2 1 2 2 2 3 2 2 2 1 ...
##  $ Q108856        : Factor w/ 3 levels "","Socialize",..: 3 1 3 3 3 2 1 2 2 1 ...
##  $ Q108754        : Factor w/ 3 levels "","No","Yes": 2 3 2 2 3 1 3 3 2 1 ...
##  $ Q108342        : Factor w/ 3 levels "","In-person",..: 2 2 2 2 2 2 2 3 3 2 ...
##  $ Q108343        : Factor w/ 3 levels "","No","Yes": 1 3 3 2 2 2 2 2 2 2 ...
##  $ Q107869        : Factor w/ 3 levels "","No","Yes": 3 1 3 2 3 2 2 3 2 2 ...
##  $ Q107491        : Factor w/ 3 levels "","No","Yes": 2 1 3 3 3 3 3 2 3 3 ...
##  $ Q106993        : Factor w/ 3 levels "","No","Yes": 3 1 3 3 3 3 3 3 3 3 ...
##  $ Q106997        : Factor w/ 3 levels "","Grrr people",..: 3 1 3 2 3 1 3 2 3 2 ...
##  $ Q106272        : Factor w/ 3 levels "","No","Yes": 3 1 3 3 2 2 3 2 3 3 ...
##  $ Q106388        : Factor w/ 3 levels "","No","Yes": 2 1 2 2 2 2 2 2 2 2 ...
##  $ Q106389        : Factor w/ 3 levels "","No","Yes": 3 1 2 2 2 2 3 3 3 3 ...
##  $ Q106042        : Factor w/ 3 levels "","No","Yes": 3 1 3 2 2 3 2 3 3 3 ...
##  $ Q105840        : Factor w/ 3 levels "","No","Yes": 1 1 3 2 2 3 2 2 3 2 ...
##  $ Q105655        : Factor w/ 3 levels "","No","Yes": 2 1 3 2 3 3 3 3 3 2 ...
##  $ Q104996        : Factor w/ 3 levels "","No","Yes": 3 1 3 2 3 2 3 3 2 3 ...
##  $ Q103293        : Factor w/ 3 levels "","No","Yes": 2 1 2 3 3 2 2 2 2 3 ...
##  $ Q102906        : Factor w/ 3 levels "","No","Yes": 2 1 2 2 2 2 2 2 2 3 ...
##  $ Q102674        : Factor w/ 3 levels "","No","Yes": 2 1 3 2 2 2 2 2 3 2 ...
##  $ Q102687        : Factor w/ 3 levels "","No","Yes": 3 1 3 3 3 3 2 3 2 3 ...
##  $ Q102289        : Factor w/ 3 levels "","No","Yes": 2 1 2 2 3 3 3 3 2 1 ...
##  $ Q102089        : Factor w/ 3 levels "","Own","Rent": 2 1 2 2 2 3 2 2 2 1 ...
##  $ Q101162        : Factor w/ 3 levels "","Optimist",..: 2 1 2 3 2 2 2 2 2 1 ...
##  $ Q101163        : Factor w/ 3 levels "","Dad","Mom": 1 1 3 3 2 1 3 3 3 1 ...
##  $ Q101596        : Factor w/ 3 levels "","No","Yes": 3 1 3 2 3 2 2 2 2 1 ...
##  $ Q100689        : Factor w/ 3 levels "","No","Yes": 3 2 3 2 2 2 2 2 3 1 ...
##  $ Q100680        : Factor w/ 3 levels "","No","Yes": 2 3 2 2 3 3 3 2 3 1 ...
##  $ Q100562        : Factor w/ 3 levels "","No","Yes": 2 3 3 2 3 2 3 3 3 1 ...
##  $ Q99982         : Factor w/ 3 levels "","Check!","Nope": 3 1 2 3 3 2 3 2 3 1 ...
##   [list output truncated]
```

```r
head(str(Train$UserID))
```

```
##  int [1:4619] 1 2 5 6 7 8 9 11 12 13 ...
```

```
## NULL
```

```r
summary(Train)
```

```
##      UserID          YOB          Gender                     Income    
##  Min.   :   1   Min.   :1900         : 537                      :1215  
##  1st Qu.:1770   1st Qu.:1969   Female:1650   $100,001 - $150,000: 571  
##  Median :3717   Median :1982   Male  :2432   $25,001 - $50,000  : 545  
##  Mean   :3830   Mean   :1979                 $50,000 - $74,999  : 642  
##  3rd Qu.:5674   3rd Qu.:1992                 $75,000 - $100,000 : 567  
##  Max.   :9503   Max.   :2039                 over $150,000      : 536  
##                 NA's   :684                  under $25,000      : 543  
##                     HouseholdStatus               EducationLevel
##                             : 800                        :1091  
##  Domestic Partners (no kids): 118   Bachelor's Degree    : 935  
##  Domestic Partners (w/kids) :  34   Current K-12         : 607  
##  Married (no kids)          : 522   Current Undergraduate: 557  
##  Married (w/kids)           :1226   Master's Degree      : 503  
##  Single (no kids)           :1760   High School Diploma  : 487  
##  Single (w/kids)            : 159   (Other)              : 439  
##          Party          Happy       Q124742    Q124122    Q123464   
##             : 728   Min.   :0.000      :2563      :1613      :1455  
##  Democrat   : 926   1st Qu.:0.000   No :1300   No :1233   No :2966  
##  Independent:1126   Median :1.000   Yes: 756   Yes:1773   Yes: 198  
##  Libertarian: 409   Mean   :0.564                                   
##  Other      : 245   3rd Qu.:1.000                                   
##  Republican :1185   Max.   :1.000                                   
##                                                                     
##  Q123621    Q122769    Q122770       Q122771     Q122120    Q121699   
##     :1524      :1333      :1211          :1201      :1230      :1080  
##  No :1506   No :2019   No :1445   Private: 567   No :2499   No : 936  
##  Yes:1589   Yes:1267   Yes:1963   Public :2851   Yes: 890   Yes:2603  
##                                                                       
##                                                                       
##                                                                       
##                                                                       
##  Q121700    Q120978    Q121011    Q120379    Q120650       Q120472    
##     :1113      :1175      :1134      :1219      :1225          :1276  
##  No :3037   No :1530   No :1563   No :1847   No : 270   Art    :1061  
##  Yes: 469   Yes:1914   Yes:1922   Yes:1553   Yes:3124   Science:2282  
##                                                                       
##                                                                       
##                                                                       
##                                                                       
##         Q120194     Q120012    Q120014    Q119334    Q119851   
##             :1337      :1188      :1322      :1207      :1098  
##  Study first:1910   No :1813   No :1333   No :1748   No :2050  
##  Try first  :1372   Yes:1618   Yes:1964   Yes:1664   Yes:1471  
##                                                                
##                                                                
##                                                                
##                                                                
##       Q119650     Q118892    Q118117          Q118232     Q118233   
##           :1190      :1019      :1092             :1580      :1289  
##  Giving   :2611   No :1331   No :2087   Idealist  :1327   No :2392  
##  Receiving: 818   Yes:2269   Yes:1440   Pragmatist:1712   Yes: 938  
##                                                                     
##                                                                     
##                                                                     
##                                                                     
##  Q118237           Q117186               Q117193     Q116797   
##     :1240              :1429                 :1410      :1338  
##  No :1831   Cool headed:2054   Odd hours     :1299   No :2169  
##  Yes:1548   Hot headed :1136   Standard hours:1910   Yes:1112  
##                                                                
##                                                                
##                                                                
##                                                                
##   Q116881     Q116953    Q116601    Q116441    Q116448    Q116197    
##       :1445      :1412      :1217      :1255      :1304       :1251  
##  Happy:2268   No :1063   No : 578   No :2098   No :1839   A.M.:1172  
##  Right: 906   Yes:2144   Yes:2824   Yes:1266   Yes:1476   P.M.:2196  
##                                                                      
##                                                                      
##                                                                      
##                                                                      
##  Q115602     Q115777     Q115610    Q115611             Q115899    
##     :1239        :1346      :1280      :1120                :1342  
##  No : 719   End  :1346   No : 581   No :2230   Circumstances:1448  
##  Yes:2661   Start:1927   Yes:2758   Yes:1269   Me           :1829  
##                                                                    
##                                                                    
##                                                                    
##                                                                    
##  Q115390    Q114961    Q114748    Q115195    Q114517          Q114386    
##     :1421      :1280      :1132      :1283      :1189             :1309  
##  No :1304   No :1707   No :1494   No :1193   No :2283   Mysterious:1891  
##  Yes:1894   Yes:1632   Yes:1993   Yes:2143   Yes:1147   TMI       :1419  
##                                                                          
##                                                                          
##                                                                          
##                                                                          
##  Q113992    Q114152     Q113583           Q113584     Q113181   
##     :1195      :1422        :1292             :1306      :1262  
##  No :2387   No :2194   Talk :1085   People    :1666   No :1954  
##  Yes:1037   Yes:1003   Tunes:2242   Technology:1647   Yes:1403  
##                                                                 
##                                                                 
##                                                                 
##                                                                 
##  Q112478    Q112512    Q112270    Q111848          Q111580     Q111220   
##     :1372      :1305      :1417      :1173             :1355      :1255  
##  No :1278   No : 645   No :1765   No :1349   Demanding :1158   No :2468  
##  Yes:1969   Yes:2669   Yes:1437   Yes:2097   Supportive:2106   Yes: 896  
##                                                                          
##                                                                          
##                                                                          
##                                                                          
##  Q110740    Q109367             Q108950     Q109244      Q108855    
##     :1246      :1311                :1271      :1399         :1587  
##  Mac:1395   No :1285   Cautious     :2289   No :2353   Umm...:1203  
##  PC :1978   Yes:2023   Risk-friendly:1059   Yes: 867   Yes!  :1829  
##                                                                     
##                                                                     
##                                                                     
##                                                                     
##  Q108617         Q108856     Q108754         Q108342     Q108343   
##     :1336            :1591      :1399            :1369      :1358  
##  No :2884   Socialize: 880   No :2166   In-person:2240   No :1981  
##  Yes: 399   Space    :2148   Yes:1054   Online   :1010   Yes:1280  
##                                                                    
##                                                                    
##                                                                    
##                                                                    
##  Q107869    Q107491    Q106993           Q106997     Q106272    Q106388   
##     :1375      :1302      :1310              :1319      :1321      :1380  
##  No :1468   No : 436   No : 567   Grrr people:1778   No : 937   No :2359  
##  Yes:1776   Yes:2881   Yes:2742   Yay people!:1522   Yes:2361   Yes: 880  
##                                                                           
##                                                                           
##                                                                           
##                                                                           
##  Q106389    Q106042    Q105840    Q105655    Q104996    Q103293   
##     :1429      :1334      :1438      :1229      :1252      :1306  
##  No :1684   No :1723   No :1720   No :1521   No :1652   No :1775  
##  Yes:1506   Yes:1562   Yes:1461   Yes:1869   Yes:1715   Yes:1538  
##                                                                   
##                                                                   
##                                                                   
##                                                                   
##  Q102906    Q102674    Q102687    Q102289    Q102089          Q101162    
##     :1432      :1441      :1315      :1365       :1329            :1381  
##  No :2038   No :2016   No :1609   No :2259   Own :2281   Optimist :2021  
##  Yes:1149   Yes:1162   Yes:1695   Yes: 995   Rent:1009   Pessimist:1217  
##                                                                          
##                                                                          
##                                                                          
##                                                                          
##  Q101163    Q101596    Q100689    Q100680    Q100562       Q99982    
##     :1485      :1368      :1198      :1341      :1349         :1418  
##  Dad:1760   No :2117   No :1346   No :1293   No : 643   Check!:1677  
##  Mom:1374   Yes:1134   Yes:2075   Yes:1985   Yes:2627   Nope  :1524  
##                                                                      
##                                                                      
##                                                                      
##                                                                      
##  Q100010    Q99716     Q99581     Q99480     Q98869     Q98578    
##     :1247      :1355      :1288      :1310      :1476      :1448  
##  No : 653   No :2884   No :2868   No : 738   No : 704   No :1993  
##  Yes:2719   Yes: 380   Yes: 463   Yes:2571   Yes:2439   Yes:1178  
##                                                                   
##                                                                   
##                                                                   
##                                                                   
##         Q98059     Q98078     Q98197     Q96024         votes      
##            :1267      :1517      :1438      :1459   Min.   : 20.0  
##  Only-child: 330   No :1768   No :1895   No :1232   1st Qu.: 45.0  
##  Yes       :3022   Yes:1334   Yes:1286   Yes:1928   Median : 82.0  
##                                                     Mean   : 71.9  
##                                                     3rd Qu.: 99.0  
##                                                     Max.   :101.0  
## 
```

```r
summary(Train$Q124742)
```

```
##        No  Yes 
## 2563 1300  756
```

```r
summary(Train$Q114386)
```

```
##            Mysterious        TMI 
##       1309       1891       1419
```

```r
summary(Train$Q115899)
```

```
##               Circumstances            Me 
##          1342          1448          1829
```


So we have 4619 observations and 110 columns/variables in the training set and 1980 obs. with 109 columns in the test set.

Let's check how many poeple in the given population are happy and how many are not in the training set:


```r

table(Train$Happy)
```

```
## 
##    0    1 
## 2015 2604
```

```r
prop.table(table(Train$Happy))
```

```
## 
##      0      1 
## 0.4362 0.5638
```


56.4% people are happy and 43.6% are not in the training set.

Let's check the male/female proportions now:


```r
table(Train$Gender)
```

```
## 
##        Female   Male 
##    537   1650   2432
```

```r
# proportion of men, women and not answered in the train set
prop.table(table(Train$Gender))
```

```
## 
##        Female   Male 
## 0.1163 0.3572 0.5265
```


Here we have 52.6% male, 35.7% female and 11.6% not provided population in the training set.

Let's check what proportion of these people are happy by the following command:


```r
table(Train$Gender, Train$Happy)
```

```
##         
##             0    1
##           215  322
##   Female  755  895
##   Male   1045 1387
```

```r
prop.table(table(Train$Gender, Train$Happy))
```

```
##         
##                0       1
##          0.04655 0.06971
##   Female 0.16346 0.19376
##   Male   0.22624 0.30028
```


Here we see that for example 1387 males are happy and 1045 are not.. So the proportion of male population which are happy is about 57%. However, the proportion table command doesn't show that because by default it takes each entry in the table and divides by the total number of persons. What we want to see is the row-wise proportion, ie, the proportion of each sex that is happy, as separate groups. So we need to tell the command to give us proportions in the 1st dimension which stands for the rows (using '2' instead would give you column proportions):


```r
prop.table(table(Train$Gender, Train$Happy), 1)
```

```
##         
##               0      1
##          0.4004 0.5996
##   Female 0.4576 0.5424
##   Male   0.4297 0.5703
```

OK, That's better! You can see male proportion who is happy is about 57%, females are about 54% and about 60% people are happy who didnt answer. You can also use tapply function which can show what proportion of Gender population is happy by:


```r
tapply(Train$Happy, Train$Gender, mean)
```

```
##        Female   Male 
## 0.5996 0.5424 0.5703
```


This now shows that 45.7% of all the females are not happy and 54.2% are happy. Similarly, 57% overall male population is happy and 43% is not happy. This shows that male population is a bit more happier than the female population and finally about 60% people who didn't supplied Gender information in the train dataset are happy and 40% are not.

Let's look into the income variable now (just following intuition tbh!):


```r
table(Train$Income, Train$Happy)
```

```
##                      
##                         0   1
##                       531 684
##   $100,001 - $150,000 207 364
##   $25,001 - $50,000   285 260
##   $50,000 - $74,999   276 366
##   $75,000 - $100,000  230 337
##   over $150,000       203 333
##   under $25,000       283 260
```

```r
prop.table(table(Train$Income, Train$Happy), 1)
```

```
##                      
##                            0      1
##                       0.4370 0.5630
##   $100,001 - $150,000 0.3625 0.6375
##   $25,001 - $50,000   0.5229 0.4771
##   $50,000 - $74,999   0.4299 0.5701
##   $75,000 - $100,000  0.4056 0.5944
##   over $150,000       0.3787 0.6213
##   under $25,000       0.5212 0.4788
```

Interestingly, 63.7% of the people earning between $100,001 - $150,000 are happy and 36.2% of people are not happy...62% people are happy earning over $150,000 and 36.2% of people are not happy are earning between $100,001 - $150,000. This category has the least amount of unhappy people.

## Baseline Model

Before building any model, let's consider a simple baseline model....we can compare our predictions to the baseline method of predicting the average/mean outcome for all data points. 

In a classification problem, a standard baseline method is to just predict the most frequent outcome for all observations. So to check the common outcome we can use the table command again:


```r

table(Train$Happy)
```

```
## 
##    0    1 
## 2015 2604
```


We can see 2604 out 4619 people are happy and 2015 are not happy...Since happiness is more common than unhappy, in this case, we would predict that everyone is happy.


```r
2604/nrow(Train)
```

```
## [1] 0.5638
```


If we did this, we would get 2604 out of the 4619 observations correct, or have an accuracy of about 56.4%. So our baseline model has an accuracy of 56.4%. This is what we'll try to beat with our logistic regression model.

As we know that we don't have dependent variable in the test set, So we want to randomly split our data set into a training set and testing set so that we'll have a test set to measure our out-of-sample accuracy.

**NOTE:**  You would require caTools package


```r
install.packages("caTools")
```

```
## Installing package into 'C:/Users/607518069/Documents/R/win-library/3.1'
## (as 'lib' is unspecified)
```

```
## Error: trying to use CRAN without setting a mirror
```

```r
library(caTools)
```


Now, let's use this package to randomly split our data into a training set and testing set. We can split 70% data in training set and 30% in the test set, We can use sample.split() method of caTools library to do so:


```r

split = sample.split(Train$Happy, SplitRatio = 0.7)
```


Sample.split randomly splits the data. But it also makes sure that the outcome variable is well-balanced in each piece. We saw earlier that 56.4% people are happy and 43.6% are unhappy. This function makes sure that in our training set, 56.4% of our population is happy and in our testing set 56.4% of our population
is happy too. We want to do this so that our test set is representative of our training set. split variable we just created has TRUE or FALSE values only... True means put them in training set and false means put those datapoints into test set. Now let's subset the data:


```r

splitTrain = subset(Train, split == TRUE)
splitTest = subset(Train, split == FALSE)
```


## Logistic Regression Model with 3 Variables only

Now, we are ready to build a logistic regression model using three variables Income, HouseholdStatus and EducationLevel as independent variables.


```r

HappyLogRegModel = glm(Happy ~ Income + HouseholdStatus + EducationLevel, data = splitTrain, 
    family = binomial)
```


family=binomial argument tells the glm function to build a logistic regression model.

Now, let's look at our model using the summary function.


```r

summary(HappyLogRegModel)
```

```
## 
## Call:
## glm(formula = Happy ~ Income + HouseholdStatus + EducationLevel, 
##     family = binomial, data = splitTrain)
## 
## Deviance Residuals: 
##    Min      1Q  Median      3Q     Max  
## -1.631  -1.236   0.876   1.067   1.534  
## 
## Coefficients:
##                                            Estimate Std. Error z value
## (Intercept)                                 0.26588    0.09078    2.93
## Income$100,001 - $150,000                   0.09740    0.15119    0.64
## Income$25,001 - $50,000                    -0.48947    0.14911   -3.28
## Income$50,000 - $74,999                    -0.16418    0.14330   -1.15
## Income$75,000 - $100,000                   -0.10814    0.14604   -0.74
## Incomeover $150,000                        -0.08246    0.15262   -0.54
## Incomeunder $25,000                        -0.28898    0.15114   -1.91
## HouseholdStatusDomestic Partners (no kids)  0.45079    0.26160    1.72
## HouseholdStatusDomestic Partners (w/kids)  -0.26643    0.43666   -0.61
## HouseholdStatusMarried (no kids)            0.54658    0.17692    3.09
## HouseholdStatusMarried (w/kids)             0.38952    0.15306    2.54
## HouseholdStatusSingle (no kids)            -0.07630    0.14364   -0.53
## HouseholdStatusSingle (w/kids)             -0.40248    0.23764   -1.69
## EducationLevelAssociate's Degree            0.00826    0.17775    0.05
## EducationLevelBachelor's Degree             0.11230    0.12989    0.86
## EducationLevelCurrent K-12                  0.04775    0.13612    0.35
## EducationLevelCurrent Undergraduate        -0.18296    0.14682   -1.25
## EducationLevelDoctoral Degree               0.00444    0.22306    0.02
## EducationLevelHigh School Diploma          -0.18016    0.15161   -1.19
## EducationLevelMaster's Degree               0.01848    0.15062    0.12
##                                            Pr(>|z|)   
## (Intercept)                                  0.0034 **
## Income$100,001 - $150,000                    0.5194   
## Income$25,001 - $50,000                      0.0010 **
## Income$50,000 - $74,999                      0.2519   
## Income$75,000 - $100,000                     0.4590   
## Incomeover $150,000                          0.5890   
## Incomeunder $25,000                          0.0559 . 
## HouseholdStatusDomestic Partners (no kids)   0.0849 . 
## HouseholdStatusDomestic Partners (w/kids)    0.5418   
## HouseholdStatusMarried (no kids)             0.0020 **
## HouseholdStatusMarried (w/kids)              0.0109 * 
## HouseholdStatusSingle (no kids)              0.5953   
## HouseholdStatusSingle (w/kids)               0.0903 . 
## EducationLevelAssociate's Degree             0.9629   
## EducationLevelBachelor's Degree              0.3873   
## EducationLevelCurrent K-12                   0.7257   
## EducationLevelCurrent Undergraduate          0.2127   
## EducationLevelDoctoral Degree                0.9841   
## EducationLevelHigh School Diploma            0.2347   
## EducationLevelMaster's Degree                0.9023   
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 4429.0  on 3232  degrees of freedom
## Residual deviance: 4330.3  on 3213  degrees of freedom
## AIC: 4370
## 
## Number of Fisher Scoring iterations: 4
```

Apart from Income$25,001 - $50,000, HouseholdStatusMarried (no kids), HouseholdStatusMarried (w/kids), HouseholdStatusSingle (w/kids) Almost all the other variables are insignificant. We see here that the coefficients for HouseholdStatusMarried (no kids) and HouseholdStatusMarried (w/kids) are +ve which means that higher values in these two variables are indicative of happiness. The last thing we want to look at in the output is the AIC value. This is a measure of the quality of the model and is like Adjusted R-squared in that it accounts for the number of variables used compared to the number of observations. Unfortunately, it can only be compared between models on the same data set. But it provides a means for model selection. The preferred model is the one with the minimum AIC.

So let's make our first prediction on the splitTest dataset by:


```r

predictHappiness = predict(HappyLogRegModel, newdata = splitTest, type = "response")
```


Let's take a look at the statistical summary of our predictions.


```r

summary(predictHappiness)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   0.308   0.501   0.566   0.563   0.639   0.735
```


From summary above, we saw that outcome of the logistic regression model is a probability. Since we're expecting probabilities, all of the numbers should be between zero and one. And we see that the minimum value is about 0.33 and the maximum value is 0.7792. Let's see if we're predicting higher probabilities for the actual happy cases as we expect. To do this, use the tapply function, giving as arguments predictHappiness and then splitTest$Happy and then mean.


```r

tapply(predictHappiness, splitTest$Happy, mean)
```

```
##      0      1 
## 0.5487 0.5733
```

This will compute the average prediction for each of the true outcomes. So we see that for all of the true happy cases, we predict an average probability of about 0.58. And all of the true unhappy cases, we predict an average probability of about 0.55. So this is a good sign, because it looks like we're predicting a higher probability for the actual happy cases.

Often, we want to make an actual prediction. Should we predict 1 for happy case, or should we predict 0 for unhappy case? We can convert the probabilities to predictions using what's called a **threshold value, t**.

If the probability of happy case is greater than this threshold value, t, we predict happy case. But if the probability of happy case is less than the threshold value, t, then we predict unhappy case. But what value should we pick for the threshold, t? The threshold value, t, is often selected based on which errors are better. You might be thinking that making no errors is better, which is, of course, true. But it's rare to have a model that predicts perfectly, so you're bound to make some errors.

There are two types of errors that a model can make -- ones where you predict 1, or happy case, but the actual outcome is 0, and ones where you predict 0, or unhappy case, but the actual outcome is 1 or happy case. If we pick a large threshold value t, then we will predict happy case rarely, since the probability of happy case has to be really large to be greater than the threshold. This means that we will make more errors where we say unhappy case, but it's actually happy case. This approach would detect the people who are really happy. On the other hand, if the threshold value, t, is small, we predict happy case frequently, and we predict unhappy case rarely. This means that we will make more errors where we say happy case, but it's actually unhappy case. This approach would detect all people who might be happy cases. 

Some decision-makers often have a preference for one type of error over the other, which should influence the threshold value they pick. If there's no preference between the errors, the right threshold to select is t = 0.5, since it just predicts the most likely outcome. To make this discussion a little more quantitative, we use what's called a **confusion matrix or classification** matrix.

We can compute two outcome measures that help us determine what types of errors we are making. They're called **sensitivity and specificity**.
*Sensitivity* is equal to the true positives divided by the true positives plus the false negatives, and measures the percentage of actual happy cases that we classify correctly. This is often called the true positive rate.

*Specificity* is equal to the true negatives divided by the true negatives plus the false positives, and measures the percentage of actual unhappy cases that we classify correctly. This is often called the true negative rate. 

A model with a higher threshold will have a lower sensitivity and a higher specificity.
A model with a lower threshold will have a higher sensitivity and a lower specificity.

Let's create the confusion matrix by table command:


```r

table(splitTest$Happy, predictHappiness > 0.5)
```

```
##    
##     FALSE TRUE
##   0   184  421
##   1   160  621
```


This will return TRUE if our prediction is greater than 0.5, which means we want to predict happy case, and it will return FALSE if our prediction is less than 0.5, which means we want to predict unhappy case.

Let's compute the sensitivity, or the true positive rate, and the specificity, or the true negative rate.


```r

sensitivity = 625/(625 + 156)
specificity = 172/(172 + 433)
```

The above Sensitivity and specificity are calculated at 0.5 threshold value. But which threshold should we pick? we'll see a nice visualization to help us select a threshold.

A Receiver Operator Characteristic curve, or ROC curve, can help you decide which value of the threshold is best. To generate these ROC curves, you need ROCR package.


```r
install.packages("ROCR")
```

```
## Installing package into 'C:/Users/607518069/Documents/R/win-library/3.1'
## (as 'lib' is unspecified)
```

```
## Error: trying to use CRAN without setting a mirror
```

```r
library(ROCR)
```

```
## Error: there is no package called 'ROCR'
```


Recall that we made predictions on our splitTest and called them predictHappiness. We'll use these predictions to create our ROC curve. Use the prediction function if ROCR package:


```r

ROCRpredHappiness = prediction(predictHappiness, splitTest$Happy)
```

```
## Error: could not find function "prediction"
```

This function takes two arguments, first the prediction variable we created when we were predicting and the second one is the actual outcome.

Now, we need to use the performance function. This defines what we'd like to plot on the x and y-axes of our ROC curve. This function takes the ROCR predection and then what we want to plot on x-axis (in our case we want to plot true +ve rate) and what we want to plot on the y-axis (in our case we want to plot False +ve rate)


```r
ROCRperfHappiness = performance(ROCRpredHappiness, "tpr", "fpr")
```

```
## Error: could not find function "performance"
```


Now plot ROCRperfHappiness


```r
plot(ROCRperfHappiness)
```

```
## Error: object 'ROCRperfHappiness' not found
```


The sensitivity, or true positive rate of the model, is shown on the y-axis. And the false positive rate, or 1 minus the specificity, is given on the x-axis. The line shows how these two outcome measures vary with different threshold values.

The ROC curve always starts at the point (0, 0). This corresponds to a threshold value of 1. If you have a threshold of 1, you will not catch any happy cases, or have a sensitivity of 0. But you will correctly label of all the unhappy 
cases, meaning you have a false positive rate of 0.

The ROC curve always ends at the point (1,1), which corresponds to a threshold value of 0. If you have a threshold of 0, you'll catch all of the happy cases, or have a sensitivity of 1, but you'll label all of the unhappy cases as happy cases too, meaning you have a false positive rate of 1.

The threshold decreases as you move from (0,0) to (1,1). At the point (0.25, 0.4), you're correctly labeling about 40% of the happy cases with a very small false positive rate.

On the other hand, at the point (0.8, 0.9), you're correctly labeling about 90% of the happy cases, but have a false positive rate of 80%.

In the middle, around (0.5, 0.6), you're correctly labeling about 60% of the happy cases, with a 50% false positive rate.

The ROC curve captures all thresholds simultaneously. The higher the threshold, or closer to (0, 0), the higher the specificity and the lower the sensitivity. The lower the threshold, or closer to (1,1), the higher the sensitivity and lower the specificity. **So which threshold value should you pick?** You should select the best threshold for the trade-off you want to make.

If you're more concerned with having a high specificity or low false positive rate, pick the threshold that maximizes the true positive rate while keeping the false positive rate really low. A threshold around (0.59, 0.7) on this ROC curve looks like a good choice in this case.

On the other hand, if you're more concerned with having a high sensitivity or high true positive rate, pick a threshold that minimizes the false positive rate but has a very high true positive rate.

Now, you can add colors by adding one additional argument to the plot function. print.cutoffs.at=seq(0,1,0.05), which will print the threshold values in increments of 0.05. If you want finer increments, just decrease the value of 0.05.


```r
plot(ROCRperfHappiness, colorize = TRUE, print.cutoffs.at = seq(0, 1, 0.05), 
    text.adj = c(-0.2, 1.7))
```

```
## Error: object 'ROCRperfHappiness' not found
```

Using this curve, we can determine which threshold value we want to use depending on our preferences as a decision-maker..

We can now calculate the accuracy of our model by:


```r
table(splitTest$Happy, predictHappiness > 0.5)
```

```
##    
##     FALSE TRUE
##   0   184  421
##   1   160  621
```

```r
(625 + 172)/nrow(splitTest)
```

```
## [1] 0.575
```

```r
(625 + 172)/(172 + 433 + 156 + 625)
```

```
## [1] 0.575
```


The accuracy of our model is about 57.5%

## First Submission on Kaggle

We are now ready for our first Kaggle submission. For this we need to create the model on the Train set (which is provided by the staff of MIT) and predict on the Test set (again, provided by the staff team)


```r

HappyLogRegModel1 = glm(Happy ~ Income + HouseholdStatus + EducationLevel, data = Train, 
    family = binomial)
```


predict on the test set now:



```r

predictHappiness1 = predict(HappyLogRegModel1, newdata = Test, type = "response")
```


Look at the summary:


```r

summary(predictHappiness1)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   0.334   0.493   0.561   0.562   0.638   0.737
```

Now that we have made our predictions, we are ready to submit on Kaggle. But first, we need to prepare the data file in the csv format which we can do by:


```r
# first check the sample provided by the Staff
sample = read.csv("sampleSubmission.csv")
```

```
## Warning: cannot open file 'sampleSubmission.csv': No such file or
## directory
```

```
## Error: cannot open the connection
```

```r
View(sample)
```

```
## Error: cannot coerce class ""function"" to a data.frame
```

```r
# we need to bind userids with our predicted probabilities
submission = cbind(UserID = Test$UserID, Probability1 = predictHappiness1)
# check how it looks
View(submission)
# time to write this dataframe into the csv format by:
write.table(submission, file = "submission1.csv", sep = ",", row.names = F)
```


Now go to Kaggle and submit this csv file and WOLLAH!!!

## Logistic Regression Model with All the independent variables


```r

HappyLogRegModelALL = glm(Happy ~ . - UserID - YOB - votes, data = splitTrain, 
    family = binomial)
```


predict on the test set now:



```r

predictHappinessALL = predict(HappyLogRegModelALL, newdata = splitTest, type = "response")
```


Look at the Model:


```r
summary(HappyLogRegModelALL)
```

```
## 
## Call:
## glm(formula = Happy ~ . - UserID - YOB - votes, family = binomial, 
##     data = splitTrain)
## 
## Deviance Residuals: 
##    Min      1Q  Median      3Q     Max  
## -2.637  -0.906   0.431   0.864   2.819  
## 
## Coefficients:
##                                            Estimate Std. Error z value
## (Intercept)                                -0.12048    0.47171   -0.26
## GenderFemale                                0.11201    0.41641    0.27
## GenderMale                                 -0.05756    0.41273   -0.14
## Income$100,001 - $150,000                   0.03624    0.19445    0.19
## Income$25,001 - $50,000                    -0.24936    0.19180   -1.30
## Income$50,000 - $74,999                    -0.08124    0.18365   -0.44
## Income$75,000 - $100,000                   -0.00854    0.18636   -0.05
## Incomeover $150,000                        -0.37131    0.20169   -1.84
## Incomeunder $25,000                        -0.11669    0.19255   -0.61
## HouseholdStatusDomestic Partners (no kids)  0.81611    0.34667    2.35
## HouseholdStatusDomestic Partners (w/kids)   0.13325    0.53389    0.25
## HouseholdStatusMarried (no kids)            0.83888    0.26903    3.12
## HouseholdStatusMarried (w/kids)             0.65456    0.25060    2.61
## HouseholdStatusSingle (no kids)             0.14431    0.22646    0.64
## HouseholdStatusSingle (w/kids)              0.05286    0.32860    0.16
## EducationLevelAssociate's Degree            0.07081    0.22523    0.31
## EducationLevelBachelor's Degree             0.09817    0.17090    0.57
## EducationLevelCurrent K-12                  0.00102    0.20751    0.00
## EducationLevelCurrent Undergraduate        -0.24788    0.19733   -1.26
## EducationLevelDoctoral Degree              -0.08932    0.28282   -0.32
## EducationLevelHigh School Diploma           0.03511    0.19671    0.18
## EducationLevelMaster's Degree              -0.28125    0.20346   -1.38
## PartyDemocrat                               0.12744    0.22808    0.56
## PartyIndependent                            0.17462    0.22292    0.78
## PartyLibertarian                            0.24445    0.25954    0.94
## PartyOther                                  0.33384    0.28350    1.18
## PartyRepublican                             0.50560    0.22417    2.26
## Q124742No                                  -0.11418    0.13159   -0.87
## Q124742Yes                                 -0.14886    0.15003   -0.99
## Q124122No                                   0.09068    0.17574    0.52
## Q124122Yes                                  0.18885    0.16501    1.14
## Q123464No                                   0.14408    0.21039    0.68
## Q123464Yes                                  0.22076    0.30389    0.73
## Q123621No                                  -0.02091    0.21727   -0.10
## Q123621Yes                                  0.05412    0.22224    0.24
## Q122769No                                  -0.21020    0.28408   -0.74
## Q122769Yes                                 -0.09083    0.28670   -0.32
## Q122770No                                  -0.38485    0.36826   -1.05
## Q122770Yes                                 -0.28354    0.36749   -0.77
## Q122771Private                              0.20239    0.36885    0.55
## Q122771Public                               0.14910    0.34843    0.43
## Q122120No                                   0.13329    0.18844    0.71
## Q122120Yes                                 -0.11302    0.20615   -0.55
## Q121699No                                  -0.23187    0.36654   -0.63
## Q121699Yes                                 -0.17178    0.35880   -0.48
## Q121700No                                   0.12618    0.35198    0.36
## Q121700Yes                                  0.37040    0.37986    0.98
## Q120978No                                  -0.02162    0.21997   -0.10
## Q120978Yes                                 -0.05082    0.21426   -0.24
## Q121011No                                  -0.03263    0.23262   -0.14
## Q121011Yes                                 -0.26597    0.22814   -1.17
## Q120379No                                  -0.00504    0.22754   -0.02
## Q120379Yes                                 -0.07072    0.22320   -0.32
## Q120650No                                   0.09920    0.28988    0.34
## Q120650Yes                                  0.01551    0.21457    0.07
## Q120472Art                                 -0.09260    0.22244   -0.42
## Q120472Science                             -0.03723    0.21386   -0.17
## Q120194Study first                          0.27207    0.20657    1.32
## Q120194Try first                            0.10332    0.21144    0.49
## Q120012No                                   0.14275    0.23969    0.60
## Q120012Yes                                 -0.04331    0.24008   -0.18
## Q120014No                                  -0.59696    0.21151   -2.82
## Q120014Yes                                 -0.09590    0.20568   -0.47
## Q119334No                                  -0.50564    0.19322   -2.62
## Q119334Yes                                  0.01287    0.18932    0.07
## Q119851No                                  -0.03155    0.23174   -0.14
## Q119851Yes                                 -0.01093    0.23233   -0.05
## Q119650Giving                               0.28433    0.20661    1.38
## Q119650Receiving                            0.35542    0.22647    1.57
## Q118892No                                  -0.02574    0.19415   -0.13
## Q118892Yes                                 -0.10232    0.18623   -0.55
## Q118117No                                  -0.25537    0.21559   -1.18
## Q118117Yes                                 -0.07500    0.21588   -0.35
## Q118232Idealist                             0.00200    0.19552    0.01
## Q118232Pragmatist                          -0.04356    0.19401   -0.22
## Q118233No                                   0.21266    0.25613    0.83
## Q118233Yes                                  0.23476    0.27448    0.86
## Q118237No                                   0.51712    0.25709    2.01
## Q118237Yes                                 -0.22646    0.25537   -0.89
## Q117186Cool headed                         -0.09023    0.20796   -0.43
## Q117186Hot headed                          -0.28317    0.22111   -1.28
## Q117193Odd hours                            0.01232    0.21101    0.06
## Q117193Standard hours                       0.12301    0.20118    0.61
## Q116797No                                   0.16635    0.22046    0.75
## Q116797Yes                                 -0.01792    0.22679   -0.08
## Q116881Happy                                0.15800    0.21173    0.75
## Q116881Right                               -0.15117    0.22870   -0.66
## Q116953No                                   0.04197    0.22778    0.18
## Q116953Yes                                  0.08806    0.21517    0.41
## Q116601No                                   0.14380    0.26410    0.54
## Q116601Yes                                 -0.02579    0.22954   -0.11
## Q116441No                                   0.01266    0.24268    0.05
## Q116441Yes                                  0.27952    0.25529    1.09
## Q116448No                                  -0.20612    0.23355   -0.88
## Q116448Yes                                 -0.43653    0.23398   -1.87
## Q116197A.M.                                 0.36468    0.20593    1.77
## Q116197P.M.                                -0.09938    0.19149   -0.52
## Q115602No                                  -0.19383    0.25575   -0.76
## Q115602Yes                                 -0.49426    0.22948   -2.15
## Q115777End                                  0.39765    0.20993    1.89
## Q115777Start                                0.48345    0.20647    2.34
## Q115610No                                  -0.31105    0.26087   -1.19
## Q115610Yes                                  0.07464    0.23259    0.32
## Q115611No                                   0.67265    0.27099    2.48
## Q115611Yes                                  0.71799    0.27821    2.58
## Q115899Circumstances                       -0.51819    0.21027   -2.46
## Q115899Me                                  -0.40068    0.20699   -1.94
## Q115390No                                   0.07233    0.19485    0.37
## Q115390Yes                                 -0.03010    0.18638   -0.16
## Q114961No                                   0.12037    0.22567    0.53
## Q114961Yes                                 -0.16980    0.22160   -0.77
## Q114748No                                  -0.14029    0.24368   -0.58
## Q114748Yes                                 -0.12712    0.23775   -0.53
## Q115195No                                  -0.04099    0.21806   -0.19
## Q115195Yes                                 -0.12104    0.20634   -0.59
## Q114517No                                  -0.08400    0.22995   -0.37
## Q114517Yes                                 -0.10304    0.23812   -0.43
## Q114386Mysterious                          -0.07139    0.19959   -0.36
## Q114386TMI                                 -0.03214    0.20439   -0.16
## Q113992No                                   0.40973    0.19840    2.07
## Q113992Yes                                  0.45622    0.21289    2.14
## Q114152No                                   0.00056    0.19600    0.00
## Q114152Yes                                 -0.04849    0.21006   -0.23
## Q113583Talk                                -0.16084    0.27191   -0.59
## Q113583Tunes                                0.03330    0.26008    0.13
## Q113584People                              -0.13901    0.26670   -0.52
## Q113584Technology                          -0.29588    0.26146   -1.13
## Q113181No                                   0.11369    0.18152    0.63
## Q113181Yes                                  0.24201    0.19180    1.26
## Q112478No                                   0.01380    0.22478    0.06
## Q112478Yes                                  0.06746    0.21720    0.31
## Q112512No                                  -0.35530    0.23991   -1.48
## Q112512Yes                                 -0.35595    0.20909   -1.70
## Q112270No                                  -0.04506    0.17921   -0.25
## Q112270Yes                                 -0.00972    0.18385   -0.05
## Q111848No                                  -0.10654    0.20613   -0.52
## Q111848Yes                                  0.10093    0.19676    0.51
## Q111580Demanding                            0.13791    0.19891    0.69
## Q111580Supportive                           0.25983    0.18759    1.39
## Q111220No                                  -0.18231    0.19193   -0.95
## Q111220Yes                                 -0.09558    0.20871   -0.46
## Q110740Mac                                  0.28058    0.18413    1.52
## Q110740PC                                   0.30883    0.18113    1.71
## Q109367No                                   0.05352    0.19127    0.28
## Q109367Yes                                 -0.09701    0.18403   -0.53
## Q108950Cautious                            -0.13757    0.19810   -0.69
## Q108950Risk-friendly                       -0.18849    0.21005   -0.90
## Q109244No                                   0.11533    0.18801    0.61
## Q109244Yes                                  0.06141    0.20487    0.30
## Q108855Umm...                              -0.29275    0.27731   -1.06
## Q108855Yes!                                -0.16253    0.27370   -0.59
## Q108617No                                   0.01677    0.20229    0.08
## Q108617Yes                                 -0.31113    0.24795   -1.25
## Q108856Socialize                            0.21622    0.28055    0.77
## Q108856Space                                0.08379    0.26218    0.32
## Q108754No                                   0.00231    0.23482    0.01
## Q108754Yes                                  0.24106    0.24551    0.98
## Q108342In-person                            0.09610    0.22525    0.43
## Q108342Online                              -0.15509    0.23907   -0.65
## Q108343No                                   0.17593    0.22295    0.79
## Q108343Yes                                 -0.08983    0.23351   -0.38
## Q107869No                                  -0.20311    0.19052   -1.07
## Q107869Yes                                  0.28204    0.19150    1.47
## Q107491No                                  -0.03995    0.23858   -0.17
## Q107491Yes                                 -0.03569    0.18718   -0.19
## Q106993No                                   0.27443    0.28661    0.96
## Q106993Yes                                  0.19379    0.25804    0.75
## Q106997Grrr people                         -0.13568    0.25818   -0.53
## Q106997Yay people!                         -0.19044    0.26257   -0.73
## Q106272No                                   0.06107    0.25355    0.24
## Q106272Yes                                  0.13067    0.23992    0.54
## Q106388No                                  -0.50796    0.27050   -1.88
## Q106388Yes                                 -0.56956    0.28729   -1.98
## Q106389No                                   0.60619    0.26061    2.33
## Q106389Yes                                  0.47512    0.26333    1.80
## Q106042No                                  -0.13667    0.21828   -0.63
## Q106042Yes                                  0.02186    0.22022    0.10
## Q105840No                                  -0.01785    0.21197   -0.08
## Q105840Yes                                 -0.02003    0.21340   -0.09
## Q105655No                                  -0.07792    0.22552   -0.35
## Q105655Yes                                  0.06285    0.22286    0.28
## Q104996No                                  -0.14840    0.18376   -0.81
## Q104996Yes                                 -0.16999    0.18107   -0.94
## Q103293No                                   0.11653    0.19552    0.60
## Q103293Yes                                  0.04030    0.19730    0.20
## Q102906No                                   0.20635    0.21263    0.97
## Q102906Yes                                 -0.18165    0.21781   -0.83
## Q102674No                                   0.60196    0.26972    2.23
## Q102674Yes                                  0.46005    0.28302    1.63
## Q102687No                                  -0.45802    0.30113   -1.52
## Q102687Yes                                 -0.30578    0.29625   -1.03
## Q102289No                                  -0.63288    0.21919   -2.89
## Q102289Yes                                 -0.21936    0.23072   -0.95
## Q102089Own                                  0.44893    0.20868    2.15
## Q102089Rent                                 0.31481    0.21907    1.44
## Q101162Optimist                             0.13410    0.22479    0.60
## Q101162Pessimist                           -0.68457    0.22998   -2.98
## Q101163Dad                                 -0.03514    0.20440   -0.17
## Q101163Mom                                  0.05912    0.20660    0.29
## Q101596No                                  -0.12933    0.20481   -0.63
## Q101596Yes                                 -0.27507    0.21401   -1.29
## Q100689No                                   0.66222    0.27336    2.42
## Q100689Yes                                  0.48355    0.26818    1.80
## Q100680No                                  -0.29977    0.26039   -1.15
## Q100680Yes                                 -0.61576    0.25126   -2.45
## Q100562No                                  -0.36627    0.25380   -1.44
## Q100562Yes                                  0.00423    0.22294    0.02
## Q99982Check!                                0.55732    0.24430    2.28
## Q99982Nope                                  0.46131    0.24610    1.87
## Q100010No                                   0.37100    0.27948    1.33
## Q100010Yes                                  0.29366    0.25327    1.16
## Q99716No                                   -0.72812    0.24112   -3.02
## Q99716Yes                                  -1.08066    0.28813   -3.75
## Q99581No                                    0.32592    0.26508    1.23
## Q99581Yes                                   0.58094    0.29734    1.95
## Q99480No                                   -0.28098    0.25233   -1.11
## Q99480Yes                                  -0.30832    0.23178   -1.33
## Q98869No                                    0.29090    0.20811    1.40
## Q98869Yes                                   0.62778    0.18177    3.45
## Q98578No                                   -0.31126    0.18867   -1.65
## Q98578Yes                                  -0.29822    0.20034   -1.49
## Q98059Only-child                           -0.30413    0.30668   -0.99
## Q98059Yes                                  -0.19711    0.26515   -0.74
## Q98078No                                    0.01238    0.23980    0.05
## Q98078Yes                                   0.11821    0.24206    0.49
## Q98197No                                   -0.12187    0.23974   -0.51
## Q98197Yes                                  -0.17430    0.25183   -0.69
## Q96024No                                   -0.03322    0.16430   -0.20
## Q96024Yes                                   0.01716    0.15756    0.11
##                                            Pr(>|z|)    
## (Intercept)                                 0.79841    
## GenderFemale                                0.78794    
## GenderMale                                  0.88908    
## Income$100,001 - $150,000                   0.85216    
## Income$25,001 - $50,000                     0.19357    
## Income$50,000 - $74,999                     0.65822    
## Income$75,000 - $100,000                    0.96346    
## Incomeover $150,000                         0.06562 .  
## Incomeunder $25,000                         0.54447    
## HouseholdStatusDomestic Partners (no kids)  0.01856 *  
## HouseholdStatusDomestic Partners (w/kids)   0.80290    
## HouseholdStatusMarried (no kids)            0.00182 ** 
## HouseholdStatusMarried (w/kids)             0.00900 ** 
## HouseholdStatusSingle (no kids)             0.52398    
## HouseholdStatusSingle (w/kids)              0.87219    
## EducationLevelAssociate's Degree            0.75322    
## EducationLevelBachelor's Degree             0.56567    
## EducationLevelCurrent K-12                  0.99609    
## EducationLevelCurrent Undergraduate         0.20904    
## EducationLevelDoctoral Degree               0.75214    
## EducationLevelHigh School Diploma           0.85835    
## EducationLevelMaster's Degree               0.16686    
## PartyDemocrat                               0.57634    
## PartyIndependent                            0.43345    
## PartyLibertarian                            0.34625    
## PartyOther                                  0.23896    
## PartyRepublican                             0.02411 *  
## Q124742No                                   0.38555    
## Q124742Yes                                  0.32110    
## Q124122No                                   0.60586    
## Q124122Yes                                  0.25240    
## Q123464No                                   0.49348    
## Q123464Yes                                  0.46756    
## Q123621No                                   0.92331    
## Q123621Yes                                  0.80760    
## Q122769No                                   0.45933    
## Q122769Yes                                  0.75140    
## Q122770No                                   0.29599    
## Q122770Yes                                  0.44038    
## Q122771Private                              0.58321    
## Q122771Public                               0.66872    
## Q122120No                                   0.47936    
## Q122120Yes                                  0.58351    
## Q121699No                                   0.52700    
## Q121699Yes                                  0.63210    
## Q121700No                                   0.71998    
## Q121700Yes                                  0.32951    
## Q120978No                                   0.92170    
## Q120978Yes                                  0.81253    
## Q121011No                                   0.88844    
## Q121011Yes                                  0.24367    
## Q120379No                                   0.98234    
## Q120379Yes                                  0.75136    
## Q120650No                                   0.73219    
## Q120650Yes                                  0.94239    
## Q120472Art                                  0.67720    
## Q120472Science                              0.86181    
## Q120194Study first                          0.18780    
## Q120194Try first                            0.62511    
## Q120012No                                   0.55145    
## Q120012Yes                                  0.85685    
## Q120014No                                   0.00477 ** 
## Q120014Yes                                  0.64102    
## Q119334No                                   0.00887 ** 
## Q119334Yes                                  0.94581    
## Q119851No                                   0.89172    
## Q119851Yes                                  0.96248    
## Q119650Giving                               0.16876    
## Q119650Receiving                            0.11655    
## Q118892No                                   0.89454    
## Q118892Yes                                  0.58271    
## Q118117No                                   0.23620    
## Q118117Yes                                  0.72826    
## Q118232Idealist                             0.99182    
## Q118232Pragmatist                           0.82236    
## Q118233No                                   0.40638    
## Q118233Yes                                  0.39240    
## Q118237No                                   0.04428 *  
## Q118237Yes                                  0.37520    
## Q117186Cool headed                          0.66436    
## Q117186Hot headed                           0.20031    
## Q117193Odd hours                            0.95345    
## Q117193Standard hours                       0.54088    
## Q116797No                                   0.45052    
## Q116797Yes                                  0.93702    
## Q116881Happy                                0.45553    
## Q116881Right                                0.50861    
## Q116953No                                   0.85380    
## Q116953Yes                                  0.68234    
## Q116601No                                   0.58612    
## Q116601Yes                                  0.91053    
## Q116441No                                   0.95841    
## Q116441Yes                                  0.27357    
## Q116448No                                   0.37748    
## Q116448Yes                                  0.06209 .  
## Q116197A.M.                                 0.07658 .  
## Q116197P.M.                                 0.60379    
## Q115602No                                   0.44852    
## Q115602Yes                                  0.03125 *  
## Q115777End                                  0.05819 .  
## Q115777Start                                0.01921 *  
## Q115610No                                   0.23311    
## Q115610Yes                                  0.74830    
## Q115611No                                   0.01306 *  
## Q115611Yes                                  0.00986 ** 
## Q115899Circumstances                        0.01373 *  
## Q115899Me                                   0.05290 .  
## Q115390No                                   0.71050    
## Q115390Yes                                  0.87168    
## Q114961No                                   0.59377    
## Q114961Yes                                  0.44353    
## Q114748No                                   0.56480    
## Q114748Yes                                  0.59288    
## Q115195No                                   0.85088    
## Q115195Yes                                  0.55747    
## Q114517No                                   0.71488    
## Q114517Yes                                  0.66521    
## Q114386Mysterious                           0.72058    
## Q114386TMI                                  0.87505    
## Q113992No                                   0.03891 *  
## Q113992Yes                                  0.03212 *  
## Q114152No                                   0.99772    
## Q114152Yes                                  0.81746    
## Q113583Talk                                 0.55416    
## Q113583Tunes                                0.89811    
## Q113584People                               0.60220    
## Q113584Technology                           0.25779    
## Q113181No                                   0.53111    
## Q113181Yes                                  0.20703    
## Q112478No                                   0.95104    
## Q112478Yes                                  0.75611    
## Q112512No                                   0.13861    
## Q112512Yes                                  0.08868 .  
## Q112270No                                   0.80147    
## Q112270Yes                                  0.95783    
## Q111848No                                   0.60524    
## Q111848Yes                                  0.60798    
## Q111580Demanding                            0.48809    
## Q111580Supportive                           0.16602    
## Q111220No                                   0.34218    
## Q111220Yes                                  0.64698    
## Q110740Mac                                  0.12756    
## Q110740PC                                   0.08819 .  
## Q109367No                                   0.77961    
## Q109367Yes                                  0.59810    
## Q108950Cautious                             0.48741    
## Q108950Risk-friendly                        0.36954    
## Q109244No                                   0.53957    
## Q109244Yes                                  0.76437    
## Q108855Umm...                               0.29112    
## Q108855Yes!                                 0.55262    
## Q108617No                                   0.93393    
## Q108617Yes                                  0.20955    
## Q108856Socialize                            0.44090    
## Q108856Space                                0.74927    
## Q108754No                                   0.99215    
## Q108754Yes                                  0.32617    
## Q108342In-person                            0.66962    
## Q108342Online                               0.51653    
## Q108343No                                   0.43005    
## Q108343Yes                                  0.70048    
## Q107869No                                   0.28638    
## Q107869Yes                                  0.14081    
## Q107491No                                   0.86701    
## Q107491Yes                                  0.84877    
## Q106993No                                   0.33832    
## Q106993Yes                                  0.45265    
## Q106997Grrr people                          0.59921    
## Q106997Yay people!                          0.46828    
## Q106272No                                   0.80968    
## Q106272Yes                                  0.58600    
## Q106388No                                   0.06040 .  
## Q106388Yes                                  0.04742 *  
## Q106389No                                   0.02002 *  
## Q106389Yes                                  0.07119 .  
## Q106042No                                   0.53123    
## Q106042Yes                                  0.92091    
## Q105840No                                   0.93288    
## Q105840Yes                                  0.92523    
## Q105655No                                   0.72972    
## Q105655Yes                                  0.77794    
## Q104996No                                   0.41935    
## Q104996Yes                                  0.34783    
## Q103293No                                   0.55117    
## Q103293Yes                                  0.83816    
## Q102906No                                   0.33183    
## Q102906Yes                                  0.40429    
## Q102674No                                   0.02563 *  
## Q102674Yes                                  0.10405    
## Q102687No                                   0.12826    
## Q102687Yes                                  0.30200    
## Q102289No                                   0.00388 ** 
## Q102289Yes                                  0.34170    
## Q102089Own                                  0.03145 *  
## Q102089Rent                                 0.15071    
## Q101162Optimist                             0.55081    
## Q101162Pessimist                            0.00291 ** 
## Q101163Dad                                  0.86349    
## Q101163Mom                                  0.77476    
## Q101596No                                   0.52773    
## Q101596Yes                                  0.19868    
## Q100689No                                   0.01541 *  
## Q100689Yes                                  0.07138 .  
## Q100680No                                   0.24964    
## Q100680Yes                                  0.01426 *  
## Q100562No                                   0.14898    
## Q100562Yes                                  0.98487    
## Q99982Check!                                0.02253 *  
## Q99982Nope                                  0.06086 .  
## Q100010No                                   0.18435    
## Q100010Yes                                  0.24627    
## Q99716No                                    0.00253 ** 
## Q99716Yes                                   0.00018 ***
## Q99581No                                    0.21888    
## Q99581Yes                                   0.05072 .  
## Q99480No                                    0.26548    
## Q99480Yes                                   0.18345    
## Q98869No                                    0.16216    
## Q98869Yes                                   0.00055 ***
## Q98578No                                    0.09898 .  
## Q98578Yes                                   0.13660    
## Q98059Only-child                            0.32136    
## Q98059Yes                                   0.45723    
## Q98078No                                    0.95881    
## Q98078Yes                                   0.62531    
## Q98197No                                    0.61121    
## Q98197Yes                                   0.48887    
## Q96024No                                    0.83978    
## Q96024Yes                                   0.91330    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 3784.8  on 2760  degrees of freedom
## Residual deviance: 2970.4  on 2532  degrees of freedom
##   (472 observations deleted due to missingness)
## AIC: 3428
## 
## Number of Fisher Scoring iterations: 4
```

```r
summary(predictHappinessALL)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##  0.0129  0.3530  0.5660  0.5520  0.7750  0.9930
```


Build the confusion matrix


```r

table(splitTest$Happy, predictHappinessALL > 0.5)
```

```
##    
##     FALSE TRUE
##   0   370  235
##   1   215  566
```


Accuracy of the model is:


```r

(598 + 344)/nrow(splitTest)
```

```
## [1] 0.6797
```


So this is better model because our accuracy improved from 57% to 67%. Let's submit this on the kaggle:


```r

HappyLogRegModelALL1 = glm(Happy ~ . - UserID - YOB - votes, data = Train, family = binomial)
```


predict on the test set now:



```r

predictHappinessALL1 = predict(HappyLogRegModelALL1, newdata = Test, type = "response")
```


Look at the Model and summary:


```r
summary(HappyLogRegModelALL1)
```

```
## 
## Call:
## glm(formula = Happy ~ . - UserID - YOB - votes, family = binomial, 
##     data = Train)
## 
## Deviance Residuals: 
##    Min      1Q  Median      3Q     Max  
## -2.732  -0.935   0.448   0.884   2.815  
## 
## Coefficients:
##                                             Estimate Std. Error z value
## (Intercept)                                 2.01e-01   3.93e-01    0.51
## GenderFemale                               -8.43e-02   3.52e-01   -0.24
## GenderMale                                 -2.32e-01   3.50e-01   -0.66
## Income$100,001 - $150,000                   1.12e-01   1.55e-01    0.72
## Income$25,001 - $50,000                    -9.61e-02   1.55e-01   -0.62
## Income$50,000 - $74,999                     8.54e-02   1.48e-01    0.58
## Income$75,000 - $100,000                    1.52e-01   1.52e-01    1.00
## Incomeover $150,000                        -1.55e-01   1.63e-01   -0.95
## Incomeunder $25,000                         9.71e-02   1.55e-01    0.62
## HouseholdStatusDomestic Partners (no kids)  5.90e-01   2.85e-01    2.07
## HouseholdStatusDomestic Partners (w/kids)   1.65e-01   4.44e-01    0.37
## HouseholdStatusMarried (no kids)            7.02e-01   2.16e-01    3.25
## HouseholdStatusMarried (w/kids)             5.85e-01   2.01e-01    2.91
## HouseholdStatusSingle (no kids)             2.37e-02   1.81e-01    0.13
## HouseholdStatusSingle (w/kids)              4.66e-02   2.70e-01    0.17
## EducationLevelAssociate's Degree            1.94e-01   1.84e-01    1.06
## EducationLevelBachelor's Degree            -3.82e-02   1.38e-01   -0.28
## EducationLevelCurrent K-12                 -3.89e-02   1.70e-01   -0.23
## EducationLevelCurrent Undergraduate        -1.78e-01   1.57e-01   -1.13
## EducationLevelDoctoral Degree              -1.30e-01   2.41e-01   -0.54
## EducationLevelHigh School Diploma          -7.57e-02   1.59e-01   -0.48
## EducationLevelMaster's Degree              -3.41e-01   1.66e-01   -2.05
## PartyDemocrat                              -1.50e-03   1.88e-01   -0.01
## PartyIndependent                            4.15e-02   1.84e-01    0.23
## PartyLibertarian                            5.14e-02   2.11e-01    0.24
## PartyOther                                  1.58e-01   2.28e-01    0.69
## PartyRepublican                             2.89e-01   1.84e-01    1.57
## Q124742No                                  -1.07e-01   1.08e-01   -0.99
## Q124742Yes                                 -1.57e-01   1.23e-01   -1.28
## Q124122No                                  -1.66e-02   1.43e-01   -0.12
## Q124122Yes                                 -3.32e-02   1.35e-01   -0.24
## Q123464No                                   1.61e-01   1.65e-01    0.97
## Q123464Yes                                  1.90e-01   2.39e-01    0.79
## Q123621No                                   8.74e-02   1.74e-01    0.50
## Q123621Yes                                  1.37e-01   1.79e-01    0.77
## Q122769No                                  -5.66e-01   2.31e-01   -2.45
## Q122769Yes                                 -4.03e-01   2.34e-01   -1.72
## Q122770No                                  -3.46e-01   2.87e-01   -1.21
## Q122770Yes                                 -1.88e-01   2.86e-01   -0.66
## Q122771Private                              3.84e-01   2.88e-01    1.33
## Q122771Public                               3.52e-01   2.71e-01    1.30
## Q122120No                                   7.84e-02   1.55e-01    0.51
## Q122120Yes                                 -6.51e-02   1.69e-01   -0.39
## Q121699No                                  -8.11e-02   2.85e-01   -0.28
## Q121699Yes                                 -1.64e-01   2.77e-01   -0.59
## Q121700No                                   1.57e-01   2.72e-01    0.58
## Q121700Yes                                  4.67e-01   2.98e-01    1.57
## Q120978No                                   1.14e-02   1.82e-01    0.06
## Q120978Yes                                  3.24e-02   1.78e-01    0.18
## Q121011No                                   6.59e-02   1.88e-01    0.35
## Q121011Yes                                 -1.88e-01   1.84e-01   -1.02
## Q120379No                                   1.07e-01   1.77e-01    0.61
## Q120379Yes                                  7.43e-02   1.76e-01    0.42
## Q120650No                                   1.23e-01   2.30e-01    0.53
## Q120650Yes                                 -3.27e-02   1.71e-01   -0.19
## Q120472Art                                 -1.66e-01   1.81e-01   -0.92
## Q120472Science                             -1.13e-01   1.71e-01   -0.66
## Q120194Study first                          3.88e-01   1.60e-01    2.43
## Q120194Try first                            1.70e-01   1.63e-01    1.04
## Q120012No                                   1.66e-01   1.92e-01    0.86
## Q120012Yes                                 -5.86e-02   1.91e-01   -0.31
## Q120014No                                  -5.15e-01   1.73e-01   -2.98
## Q120014Yes                                 -1.34e-01   1.67e-01   -0.80
## Q119334No                                  -4.51e-01   1.56e-01   -2.89
## Q119334Yes                                 -3.38e-03   1.54e-01   -0.02
## Q119851No                                  -8.91e-02   1.84e-01   -0.48
## Q119851Yes                                 -4.93e-02   1.86e-01   -0.27
## Q119650Giving                               2.98e-01   1.65e-01    1.81
## Q119650Receiving                            1.47e-01   1.82e-01    0.81
## Q118892No                                   2.59e-02   1.55e-01    0.17
## Q118892Yes                                 -1.20e-01   1.48e-01   -0.81
## Q118117No                                  -1.53e-01   1.73e-01   -0.89
## Q118117Yes                                 -7.21e-02   1.75e-01   -0.41
## Q118232Idealist                            -9.62e-02   1.58e-01   -0.61
## Q118232Pragmatist                          -8.99e-02   1.56e-01   -0.58
## Q118233No                                   1.43e-01   2.03e-01    0.70
## Q118233Yes                                  5.51e-02   2.17e-01    0.25
## Q118237No                                   5.44e-01   2.08e-01    2.61
## Q118237Yes                                 -2.65e-01   2.07e-01   -1.29
## Q117186Cool headed                         -3.32e-02   1.69e-01   -0.20
## Q117186Hot headed                          -1.72e-01   1.79e-01   -0.96
## Q117193Odd hours                            7.41e-02   1.68e-01    0.44
## Q117193Standard hours                       2.05e-01   1.62e-01    1.27
## Q116797No                                   1.79e-01   1.73e-01    1.03
## Q116797Yes                                 -2.62e-02   1.79e-01   -0.15
## Q116881Happy                               -1.20e-03   1.74e-01   -0.01
## Q116881Right                               -1.88e-01   1.88e-01   -1.00
## Q116953No                                  -5.10e-02   1.83e-01   -0.28
## Q116953Yes                                  9.91e-02   1.72e-01    0.58
## Q116601No                                   2.52e-01   2.12e-01    1.19
## Q116601Yes                                  1.84e-01   1.84e-01    1.00
## Q116441No                                  -2.75e-01   1.97e-01   -1.39
## Q116441Yes                                  5.15e-02   2.08e-01    0.25
## Q116448No                                  -6.87e-02   1.87e-01   -0.37
## Q116448Yes                                 -2.06e-01   1.89e-01   -1.09
## Q116197A.M.                                 2.43e-01   1.69e-01    1.44
## Q116197P.M.                                 4.68e-02   1.57e-01    0.30
## Q115602No                                  -9.95e-05   2.04e-01    0.00
## Q115602Yes                                 -1.07e-01   1.82e-01   -0.59
## Q115777End                                  2.05e-01   1.73e-01    1.19
## Q115777Start                                2.93e-01   1.70e-01    1.73
## Q115610No                                  -3.20e-01   2.14e-01   -1.49
## Q115610Yes                                 -6.25e-02   1.90e-01   -0.33
## Q115611No                                   3.66e-01   2.11e-01    1.73
## Q115611Yes                                  4.67e-01   2.18e-01    2.14
## Q115899Circumstances                       -4.88e-01   1.71e-01   -2.85
## Q115899Me                                  -2.66e-01   1.70e-01   -1.57
## Q115390No                                   1.88e-01   1.59e-01    1.18
## Q115390Yes                                  7.41e-03   1.51e-01    0.05
## Q114961No                                   2.70e-02   1.81e-01    0.15
## Q114961Yes                                 -2.37e-01   1.78e-01   -1.33
## Q114748No                                   4.65e-02   1.96e-01    0.24
## Q114748Yes                                  1.71e-01   1.92e-01    0.89
## Q115195No                                  -1.30e-01   1.72e-01   -0.76
## Q115195Yes                                 -1.29e-01   1.63e-01   -0.79
## Q114517No                                  -1.53e-01   1.85e-01   -0.83
## Q114517Yes                                 -1.37e-01   1.93e-01   -0.71
## Q114386Mysterious                           1.23e-02   1.62e-01    0.08
## Q114386TMI                                  1.85e-01   1.67e-01    1.11
## Q113992No                                   3.46e-01   1.62e-01    2.14
## Q113992Yes                                  3.14e-01   1.74e-01    1.81
## Q114152No                                  -9.56e-02   1.60e-01   -0.60
## Q114152Yes                                 -1.44e-01   1.71e-01   -0.84
## Q113583Talk                                -1.80e-01   2.15e-01   -0.84
## Q113583Tunes                                5.44e-02   2.06e-01    0.26
## Q113584People                              -1.61e-01   2.10e-01   -0.76
## Q113584Technology                          -3.44e-01   2.09e-01   -1.65
## Q113181No                                   9.70e-02   1.48e-01    0.66
## Q113181Yes                                  1.01e-01   1.56e-01    0.65
## Q112478No                                  -1.32e-01   1.85e-01   -0.72
## Q112478Yes                                 -1.54e-01   1.78e-01   -0.87
## Q112512No                                  -1.69e-01   1.93e-01   -0.88
## Q112512Yes                                 -6.41e-02   1.69e-01   -0.38
## Q112270No                                  -2.06e-02   1.47e-01   -0.14
## Q112270Yes                                  1.67e-02   1.51e-01    0.11
## Q111848No                                  -5.44e-02   1.67e-01   -0.33
## Q111848Yes                                  1.22e-01   1.62e-01    0.75
## Q111580Demanding                            8.07e-02   1.63e-01    0.50
## Q111580Supportive                           1.02e-01   1.53e-01    0.67
## Q111220No                                  -2.24e-01   1.56e-01   -1.44
## Q111220Yes                                 -2.01e-01   1.70e-01   -1.18
## Q110740Mac                                  2.49e-01   1.46e-01    1.71
## Q110740PC                                   2.48e-01   1.44e-01    1.72
## Q109367No                                   8.91e-02   1.60e-01    0.56
## Q109367Yes                                 -8.37e-02   1.52e-01   -0.55
## Q108950Cautious                            -1.19e-01   1.61e-01   -0.74
## Q108950Risk-friendly                       -1.62e-01   1.73e-01   -0.94
## Q109244No                                   9.06e-02   1.54e-01    0.59
## Q109244Yes                                  9.42e-02   1.69e-01    0.56
## Q108855Umm...                              -2.77e-01   2.28e-01   -1.21
## Q108855Yes!                                -7.27e-02   2.24e-01   -0.32
## Q108617No                                   2.10e-02   1.69e-01    0.12
## Q108617Yes                                 -2.64e-01   2.06e-01   -1.28
## Q108856Socialize                            2.82e-01   2.32e-01    1.22
## Q108856Space                                9.48e-02   2.16e-01    0.44
## Q108754No                                  -1.30e-01   1.91e-01   -0.68
## Q108754Yes                                  6.36e-02   2.00e-01    0.32
## Q108342In-person                            1.75e-01   1.85e-01    0.95
## Q108342Online                              -2.21e-02   1.95e-01   -0.11
## Q108343No                                   6.34e-02   1.86e-01    0.34
## Q108343Yes                                 -1.89e-01   1.97e-01   -0.96
## Q107869No                                  -4.99e-01   1.53e-01   -3.26
## Q107869Yes                                  8.32e-02   1.54e-01    0.54
## Q107491No                                   2.18e-01   1.92e-01    1.13
## Q107491Yes                                  2.20e-01   1.49e-01    1.48
## Q106993No                                   2.05e-01   2.25e-01    0.91
## Q106993Yes                                  1.55e-01   2.02e-01    0.77
## Q106997Grrr people                         -1.54e-01   2.03e-01   -0.76
## Q106997Yay people!                         -1.65e-01   2.06e-01   -0.80
## Q106272No                                   2.69e-01   2.06e-01    1.31
## Q106272Yes                                  2.26e-01   1.97e-01    1.15
## Q106388No                                  -4.04e-01   2.23e-01   -1.81
## Q106388Yes                                 -4.39e-01   2.36e-01   -1.86
## Q106389No                                   4.32e-01   2.14e-01    2.01
## Q106389Yes                                  3.62e-01   2.17e-01    1.67
## Q106042No                                  -1.33e-01   1.81e-01   -0.73
## Q106042Yes                                 -3.76e-02   1.82e-01   -0.21
## Q105840No                                   1.13e-02   1.76e-01    0.06
## Q105840Yes                                  2.17e-02   1.77e-01    0.12
## Q105655No                                  -1.89e-01   1.87e-01   -1.01
## Q105655Yes                                 -2.19e-01   1.85e-01   -1.18
## Q104996No                                  -1.78e-01   1.48e-01   -1.21
## Q104996Yes                                 -1.54e-01   1.47e-01   -1.04
## Q103293No                                   1.72e-01   1.57e-01    1.10
## Q103293Yes                                  8.26e-02   1.58e-01    0.52
## Q102906No                                   1.69e-01   1.72e-01    0.99
## Q102906Yes                                 -1.35e-01   1.77e-01   -0.76
## Q102674No                                   6.54e-01   2.17e-01    3.01
## Q102674Yes                                  4.27e-01   2.29e-01    1.87
## Q102687No                                  -5.92e-01   2.39e-01   -2.48
## Q102687Yes                                 -3.70e-01   2.35e-01   -1.57
## Q102289No                                  -6.11e-01   1.77e-01   -3.46
## Q102289Yes                                 -5.61e-02   1.88e-01   -0.30
## Q102089Own                                  3.00e-01   1.69e-01    1.77
## Q102089Rent                                 2.43e-01   1.78e-01    1.36
## Q101162Optimist                             2.21e-01   1.80e-01    1.23
## Q101162Pessimist                           -5.14e-01   1.85e-01   -2.78
## Q101163Dad                                  3.08e-02   1.61e-01    0.19
## Q101163Mom                                  7.82e-02   1.62e-01    0.48
## Q101596No                                  -1.28e-01   1.66e-01   -0.77
## Q101596Yes                                 -2.30e-01   1.75e-01   -1.32
## Q100689No                                   3.61e-01   2.18e-01    1.66
## Q100689Yes                                  2.55e-01   2.14e-01    1.19
## Q100680No                                  -5.20e-02   2.04e-01   -0.25
## Q100680Yes                                 -3.32e-01   1.97e-01   -1.68
## Q100562No                                  -3.22e-01   2.02e-01   -1.59
## Q100562Yes                                  8.03e-02   1.78e-01    0.45
## Q99982Check!                                2.89e-01   1.98e-01    1.46
## Q99982Nope                                  2.83e-01   2.01e-01    1.41
## Q100010No                                   1.25e-01   2.24e-01    0.56
## Q100010Yes                                  1.89e-01   2.04e-01    0.93
## Q99716No                                   -3.99e-01   1.87e-01   -2.13
## Q99716Yes                                  -6.23e-01   2.30e-01   -2.71
## Q99581No                                    1.64e-01   2.07e-01    0.79
## Q99581Yes                                   3.76e-01   2.37e-01    1.59
## Q99480No                                   -1.96e-01   2.01e-01   -0.98
## Q99480Yes                                  -1.41e-01   1.87e-01   -0.76
## Q98869No                                    8.33e-02   1.71e-01    0.49
## Q98869Yes                                   4.20e-01   1.47e-01    2.86
## Q98578No                                   -1.24e-01   1.57e-01   -0.79
## Q98578Yes                                  -9.51e-02   1.64e-01   -0.58
## Q98059Only-child                           -2.16e-01   2.50e-01   -0.86
## Q98059Yes                                  -1.80e-01   2.14e-01   -0.84
## Q98078No                                    1.14e-01   1.93e-01    0.59
## Q98078Yes                                   1.32e-01   1.94e-01    0.68
## Q98197No                                   -2.00e-01   1.90e-01   -1.05
## Q98197Yes                                  -1.01e-01   2.01e-01   -0.50
## Q96024No                                    1.46e-02   1.34e-01    0.11
## Q96024Yes                                   5.19e-02   1.27e-01    0.41
##                                            Pr(>|z|)    
## (Intercept)                                 0.60963    
## GenderFemale                                0.81070    
## GenderMale                                  0.50842    
## Income$100,001 - $150,000                   0.47082    
## Income$25,001 - $50,000                     0.53568    
## Income$50,000 - $74,999                     0.56325    
## Income$75,000 - $100,000                    0.31683    
## Incomeover $150,000                         0.33974    
## Incomeunder $25,000                         0.53224    
## HouseholdStatusDomestic Partners (no kids)  0.03801 *  
## HouseholdStatusDomestic Partners (w/kids)   0.71022    
## HouseholdStatusMarried (no kids)            0.00116 ** 
## HouseholdStatusMarried (w/kids)             0.00366 ** 
## HouseholdStatusSingle (no kids)             0.89564    
## HouseholdStatusSingle (w/kids)              0.86299    
## EducationLevelAssociate's Degree            0.29136    
## EducationLevelBachelor's Degree             0.78166    
## EducationLevelCurrent K-12                  0.81895    
## EducationLevelCurrent Undergraduate         0.25938    
## EducationLevelDoctoral Degree               0.58882    
## EducationLevelHigh School Diploma           0.63344    
## EducationLevelMaster's Degree               0.04057 *  
## PartyDemocrat                               0.99361    
## PartyIndependent                            0.82156    
## PartyLibertarian                            0.80771    
## PartyOther                                  0.48963    
## PartyRepublican                             0.11667    
## Q124742No                                   0.32156    
## Q124742Yes                                  0.20143    
## Q124122No                                   0.90765    
## Q124122Yes                                  0.80665    
## Q123464No                                   0.33111    
## Q123464Yes                                  0.42740    
## Q123621No                                   0.61606    
## Q123621Yes                                  0.44315    
## Q122769No                                   0.01429 *  
## Q122769Yes                                  0.08502 .  
## Q122770No                                   0.22677    
## Q122770Yes                                  0.51093    
## Q122771Private                              0.18355    
## Q122771Public                               0.19497    
## Q122120No                                   0.61304    
## Q122120Yes                                  0.69925    
## Q121699No                                   0.77583    
## Q121699Yes                                  0.55294    
## Q121700No                                   0.56503    
## Q121700Yes                                  0.11674    
## Q120978No                                   0.95018    
## Q120978Yes                                  0.85514    
## Q121011No                                   0.72605    
## Q121011Yes                                  0.30638    
## Q120379No                                   0.54435    
## Q120379Yes                                  0.67195    
## Q120650No                                   0.59362    
## Q120650Yes                                  0.84830    
## Q120472Art                                  0.35757    
## Q120472Science                              0.50868    
## Q120194Study first                          0.01523 *  
## Q120194Try first                            0.29718    
## Q120012No                                   0.38848    
## Q120012Yes                                  0.75892    
## Q120014No                                   0.00284 ** 
## Q120014Yes                                  0.42317    
## Q119334No                                   0.00385 ** 
## Q119334Yes                                  0.98247    
## Q119851No                                   0.62901    
## Q119851Yes                                  0.79060    
## Q119650Giving                               0.07036 .  
## Q119650Receiving                            0.42043    
## Q118892No                                   0.86739    
## Q118892Yes                                  0.41653    
## Q118117No                                   0.37516    
## Q118117Yes                                  0.67957    
## Q118232Idealist                             0.54403    
## Q118232Pragmatist                           0.56380    
## Q118233No                                   0.48209    
## Q118233Yes                                  0.79947    
## Q118237No                                   0.00901 ** 
## Q118237Yes                                  0.19876    
## Q117186Cool headed                          0.84466    
## Q117186Hot headed                           0.33529    
## Q117193Odd hours                            0.66028    
## Q117193Standard hours                       0.20548    
## Q116797No                                   0.30137    
## Q116797Yes                                  0.88359    
## Q116881Happy                                0.99450    
## Q116881Right                                0.31812    
## Q116953No                                   0.78027    
## Q116953Yes                                  0.56359    
## Q116601No                                   0.23575    
## Q116601Yes                                  0.31677    
## Q116441No                                   0.16331    
## Q116441Yes                                  0.80445    
## Q116448No                                   0.71386    
## Q116448Yes                                  0.27707    
## Q116197A.M.                                 0.15090    
## Q116197P.M.                                 0.76620    
## Q115602No                                   0.99961    
## Q115602Yes                                  0.55595    
## Q115777End                                  0.23473    
## Q115777Start                                0.08424 .  
## Q115610No                                   0.13594    
## Q115610Yes                                  0.74152    
## Q115611No                                   0.08279 .  
## Q115611Yes                                  0.03204 *  
## Q115899Circumstances                        0.00434 ** 
## Q115899Me                                   0.11752    
## Q115390No                                   0.23657    
## Q115390Yes                                  0.96081    
## Q114961No                                   0.88097    
## Q114961Yes                                  0.18431    
## Q114748No                                   0.81237    
## Q114748Yes                                  0.37392    
## Q115195No                                   0.45018    
## Q115195Yes                                  0.42941    
## Q114517No                                   0.40611    
## Q114517Yes                                  0.47784    
## Q114386Mysterious                           0.93960    
## Q114386TMI                                  0.26683    
## Q113992No                                   0.03221 *  
## Q113992Yes                                  0.07105 .  
## Q114152No                                   0.55114    
## Q114152Yes                                  0.39903    
## Q113583Talk                                 0.40354    
## Q113583Tunes                                0.79199    
## Q113584People                               0.44526    
## Q113584Technology                           0.09923 .  
## Q113181No                                   0.51078    
## Q113181Yes                                  0.51673    
## Q112478No                                   0.47408    
## Q112478Yes                                  0.38609    
## Q112512No                                   0.38155    
## Q112512Yes                                  0.70431    
## Q112270No                                   0.88830    
## Q112270Yes                                  0.91165    
## Q111848No                                   0.74505    
## Q111848Yes                                  0.45096    
## Q111580Demanding                            0.61992    
## Q111580Supportive                           0.50508    
## Q111220No                                   0.15118    
## Q111220Yes                                  0.23723    
## Q110740Mac                                  0.08781 .  
## Q110740PC                                   0.08563 .  
## Q109367No                                   0.57685    
## Q109367Yes                                  0.58295    
## Q108950Cautious                             0.46052    
## Q108950Risk-friendly                        0.34955    
## Q109244No                                   0.55777    
## Q109244Yes                                  0.57725    
## Q108855Umm...                               0.22454    
## Q108855Yes!                                 0.74560    
## Q108617No                                   0.90094    
## Q108617Yes                                  0.20018    
## Q108856Socialize                            0.22396    
## Q108856Space                                0.66114    
## Q108754No                                   0.49727    
## Q108754Yes                                  0.75001    
## Q108342In-person                            0.34403    
## Q108342Online                               0.90988    
## Q108343No                                   0.73328    
## Q108343Yes                                  0.33567    
## Q107869No                                   0.00110 ** 
## Q107869Yes                                  0.58975    
## Q107491No                                   0.25640    
## Q107491Yes                                  0.13856    
## Q106993No                                   0.36114    
## Q106993Yes                                  0.44149    
## Q106997Grrr people                          0.44614    
## Q106997Yay people!                          0.42288    
## Q106272No                                   0.19144    
## Q106272Yes                                  0.25087    
## Q106388No                                   0.07063 .  
## Q106388Yes                                  0.06294 .  
## Q106389No                                   0.04415 *  
## Q106389Yes                                  0.09514 .  
## Q106042No                                   0.46304    
## Q106042Yes                                  0.83671    
## Q105840No                                   0.94881    
## Q105840Yes                                  0.90252    
## Q105655No                                   0.31268    
## Q105655Yes                                  0.23709    
## Q104996No                                   0.22812    
## Q104996Yes                                  0.29625    
## Q103293No                                   0.27293    
## Q103293Yes                                  0.60054    
## Q102906No                                   0.32411    
## Q102906Yes                                  0.44600    
## Q102674No                                   0.00257 ** 
## Q102674Yes                                  0.06176 .  
## Q102687No                                   0.01328 *  
## Q102687Yes                                  0.11606    
## Q102289No                                   0.00054 ***
## Q102289Yes                                  0.76581    
## Q102089Own                                  0.07626 .  
## Q102089Rent                                 0.17347    
## Q101162Optimist                             0.21997    
## Q101162Pessimist                            0.00537 ** 
## Q101163Dad                                  0.84853    
## Q101163Mom                                  0.62981    
## Q101596No                                   0.43963    
## Q101596Yes                                  0.18786    
## Q100689No                                   0.09727 .  
## Q100689Yes                                  0.23228    
## Q100680No                                   0.79936    
## Q100680Yes                                  0.09204 .  
## Q100562No                                   0.11118    
## Q100562Yes                                  0.65232    
## Q99982Check!                                0.14539    
## Q99982Nope                                  0.15785    
## Q100010No                                   0.57505    
## Q100010Yes                                  0.35408    
## Q99716No                                    0.03297 *  
## Q99716Yes                                   0.00667 ** 
## Q99581No                                    0.42859    
## Q99581Yes                                   0.11225    
## Q99480No                                    0.32940    
## Q99480Yes                                   0.44982    
## Q98869No                                    0.62677    
## Q98869Yes                                   0.00428 ** 
## Q98578No                                    0.42960    
## Q98578Yes                                   0.56297    
## Q98059Only-child                            0.38844    
## Q98059Yes                                   0.39890    
## Q98078No                                    0.55525    
## Q98078Yes                                   0.49635    
## Q98197No                                    0.29150    
## Q98197Yes                                   0.61605    
## Q96024No                                    0.91320    
## Q96024Yes                                   0.68302    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 5391.6  on 3934  degrees of freedom
## Residual deviance: 4309.6  on 3706  degrees of freedom
##   (684 observations deleted due to missingness)
## AIC: 4768
## 
## Number of Fisher Scoring iterations: 4
```

```r
summary(predictHappinessALL1)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##  0.0046  0.3560  0.5790  0.5550  0.7620  0.9930
```

Now that we have made our predictions, we are ready to submit on Kaggle. But first, we need to prepare the data file in the csv format which we can do by:


```r
# first check the sample provided by the Staff
sample = read.csv("sampleSubmission.csv")
```

```
## Warning: cannot open file 'sampleSubmission.csv': No such file or
## directory
```

```
## Error: cannot open the connection
```

```r
View(sample)
```

```
## Error: cannot coerce class ""function"" to a data.frame
```

```r
# we need to bind userids with our predicted probabilities
submission = cbind(UserID = Test$UserID, Probability1 = predictHappinessALL1)
# check how it looks
View(submission)
# time to write this dataframe into the csv format by:
write.table(submission, file = "submission2.csv", sep = ",", row.names = F)
```




## Linear Regression

Linear regression discards observations with missing data, so we will remove all such observations from the training and testing sets. Later, we will learn about imputation, which deals with missing data by filling in missing values with plausible information.

The following commands can be used to remove observations with any missing value from happinessTrain and happinessTest:


```r
hap.Train.Without.Missing.Values = na.omit(happinessTrain)
```

```
## Error: object 'happinessTrain' not found
```

```r
hap.Test.Without.Missing.Values = na.omit(happinessTest)
```

```
## Error: object 'happinessTest' not found
```


check the structure now:


```r
str(hap.Train.Without.Missing.Values)
```

```
## Error: object 'hap.Train.Without.Missing.Values' not found
```

```r
summary(hap.Train.Without.Missing.Values)
```

```
## Error: object 'hap.Train.Without.Missing.Values' not found
```

Let's build our first Linear Regression model using all variables except the UserID, YOB and votes:


```r

LinearRegModel = lm(Happy ~ . - UserID - YOB - votes, data = hap.Train.Without.Missing.Values)
```

```
## Error: object 'hap.Train.Without.Missing.Values' not found
```

```r
summary(LinearRegModel)
```

```
## Error: object 'LinearRegModel' not found
```


The training-set RMSE can be computed by first computing the SSE:

```r
SSE = sum(LinearRegModel$residuals^2)
```

```
## Error: object 'LinearRegModel' not found
```

and then dividing by the number of observations and taking the square root:

```r
RMSE = sqrt(SSE/nrow(hap.Train.Without.Missing.Values))
```

```
## Error: object 'SSE' not found
```

A alternative way of getting this answer would be with the following command:

```r
sqrt(mean(LinearRegModel$residuals^2))
```

```
## Error: object 'LinearRegModel' not found
```


We can now try removing insignificant variables one by one to see if we can improve our model.  When we remove insignificant variables, the "Multiple R-squared" will always be worse, but only slightly worse. This is due to the nature of a linear regression model. It is always possible for the regression model to make a coefficient zero, which would be the same as removing the variable from the model. The fact that the coefficient is not zero in the intial model means it must be helping the R-squared value, even if it is only a very small improvement. So when we force the variable to be removed, it will decrease the R-squared a little bit. However, this small decrease is worth it to have a simpler model.

On the contrary, when we remove insignificant variables, the "Adjusted R-squred" will frequently be better. This value accounts for the complexity of the model, and thus tends to increase as insignificant variables are removed, and decrease as insignificant variables are added.


## Automatically Building the Model

We have many variables in this problem, and many are insignificant...R provides a function, **step**, that will automate the procedure of trying different combinations of variables to find a good compromise of model simplicity and R2. This trade-off is formalised by the Akaike information criterion (AIC) - it can be informally thought of as the quality of the model with a penalty for the number of variables in the model.

The step function has one argument - the name of the initial model. It returns a simplified model. Use the step function in R to derive a new model, with the full model as the initial model.


```r
LinearRegStepModel = step(LinearRegModel)
```

```
## Error: object 'LinearRegModel' not found
```

```r
summary(LinearRegStepModel)
```

```
## Error: object 'LinearRegStepModel' not found
```


It is interesting to note that the step function does not address the collinearity of the variables, except that adding highly correlated variables will not improve the R2 significantly. The consequence of this is that the step function will not necessarily produce a very interpretable model - just a model that has balanced quality and simplicity for a particular weighting of quality and simplicity (AIC).

