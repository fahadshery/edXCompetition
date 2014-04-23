# R Code for edX Competition on *"Analytics Edge"* DataScience Course
=====================================================================

#### Author: Fahad Usman
#### Date: 21 April 2014

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

We have seen linear regression as a way of predicting continuous outcomes. Of course, we can utilize linear regression to predict happiness here, but then we have to round the outcome to 0 or 1. Instead we will use the logistic regression, which is an extension of linear regression, to environments where the dependent variable is categorical. In our case, 0 or 1. to start with.

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
str(Train$Q124742)
```

```
##  Factor w/ 3 levels "","No","Yes": 2 1 2 1 2 3 1 2 2 1 ...
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


So we have 4619 observations and 110 columns/variables.

Lets check how many poeple in the given population are happy and how many are not in the training set:


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


Here we have 52.6% male, 35.7% female and 11.6% not answered population in the training set.

Let's check what proportion of these people are happy by the following command:


```r
prop.table(table(happinessTrain$Gender, happinessTrain$Happy))
```

```
## Error: object 'happinessTrain' not found
```


Well that's not very clean, the proportion table command by default takes each entry in the table and divides by the total number of passengers. What we want to see is the row-wise proportion, ie, the proportion of each sex that is happy, as separate groups. So we need to tell the command to give us proportions in the 1st dimension which stands for the rows (using '2' instead would give you column proportions):


```r
prop.table(table(happinessTrain$Gender, happinessTrain$Happy), 1)
```

```
## Error: object 'happinessTrain' not found
```

OR you can use tapply function which can show what proportion of Gender population is happy by:


```r
tapply(happinessTrain$Happy, happinessTrain$Gender, mean)
```

```
## Error: object 'happinessTrain' not found
```


This now shows that 45.7% of all the females are not happy and 54.2% are happy. Similarly, 57% overall male population  is happy and 43% is not happy. This shows that male population is a bit more happier than the female population and finally about 60% people who didn't supplied Gender information in the train dataset are happy and 40% are not.

Let's look into the income variable now (just following intuition tbh!):


```r
prop.table(table(happinessTrain$Happy, happinessTrain$Income), 1)
```

```
## Error: object 'happinessTrain' not found
```

Interestingly, 26% of the people earning between $100,001 - $150,000 are happy and equally amount of people are not happy...Almost similar results are found in other Income ranges...So this doesn't tell much really if the income is related with the happiness...


## Baseline Predictions
we can compare our predictions to the baseline method of predicting the average/mean outcome for all data points. 

In a classification problem, a standard baseline method is to just predict the most frequent outcome for all observations. So to check the common outcome we can use the table command:


```r

table(happinessTrain$Happy)
```

```
## Error: object 'happinessTrain' not found
```


Since happiness is more common than not happy, in this case, we would predict that everyone is happy.


```r
2604/nrow(happinessTrain)
```

```
## Error: object 'happinessTrain' not found
```


If we did this, we would get 2604 out of the 4619 observations correct, or have an accuracy of about 56.4%. This is what we'll try to beat with our first linear regression model.

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

