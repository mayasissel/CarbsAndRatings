# Analysis on the Relationship Between Carbohydrates and Recipe Ratings

Authors: Maya Sissel & Trinity Truong

## Overview

This is a data science project for the DSC 80 course at UCSD. The project focuses
on analyzing the relationship between the amount of carbohydrates in a recipe 
and the rating of the recipe.

hello

## Introduction

## Data Cleaning and Exploratory Data Analysis

### Univariate Analysis

For univariate analysis, we looked into the distribution of the proportion of carbohydtrates in recipes. In the graph below, the distribution is skewed to the middle, heavily leaning right side, indicating to us that a majority of recipes have a low proportion of carbohydrates. This skew also indicates to us that as the carbohydrate proportion rises, the less recipes there are on food.com.

### Bivariate Analysis

For bivariate analysis, we looked into the distribution of ratings between recipes with a high carbohydrate proportion (of 65% or higher) to recipes with a low carbohydrate proportion. In the graph below, we found that recipes with a higher amount of carbohydrates have ratings of 4-5 while recipes with lower carbohydrtaes have ratings spread 1-5 with more in 4 and 5.

### Interesting Aggregates

For this section, we investigated the relationship between the number of steps in a recipe with the proportion of carbohydrates in the recipe. To do this, we first made a smaller dataframe containing the columns 'n_steps' and 'carb_prop'. This allows for us to isolate the data we want to focus on. Next we then grouped this dataframe by 'n_steps' and aggregated the remaining carbohydtare proportion column to find the mean, median, min and max to understand whether carbohydrate proportion has any influence on the number of steps a recipe takes.

## Assessment of Missingness

### NMAR Analysis

We believe that the misssingess of the 'rating'column is NMAR because a person is less likely to return to the website to leave a rating if they hate or fail to follow through with the recipe properly. The same can be said for people who would feel neutral about a recipe but do not bother returning to the website and leave any ratings. People usually leave a rating if they have tried a recipe or follow through with creating it (without failing) and enjoyed it. A person's feelings of enjoying the recipe would then lead them to take the time to return to the website and leave a positive rating.

We wanted to look into the missingness of the 'rating' column in our merged 'recipes_revies' DataFrame by testing whether 'rating' is dependent on other existing columns. To do this, we test whether or not the missingness of the 'rating' column is dependent on 'carb_prop' which is the proportion of carbohydrtaes in a given recipe. We would also test whether or not the missingness of 'rating' column is dependent on the amount of calories there are in a given recipe.

To test whether or not the missingness of the 'rating' column is dependent on 'carb_prop' which is the proportion of carbohydrtaes in a given recipe, we ran a permutation test by shuffling the missingness of the 'rating' column 1000 times to collect 1000 simulated mean differences between the distribution with missing data and without missing data.

To investigate this goal, we ran a permutation test following the guide below:

Null Hypothesis: The missingness of ratings does not depend on the proportion of carbohydrates

Alternate Hypothesis: The missingness of ratings does depend on the proportion of carbohydrates

Test Statistic: The absolute difference of mean in the proportion of carbohydrates between the distribution of the group without missing ratings and the distribution of the grouop with missing ratings

Significance Level: 0.05


The observed difference of means for 'missing_rating' and 'carb_prop' we got is roughly 0.003209, indicated by the red line on the graph. Because the p_value we found (0.1) is greater than 0.05, we fail to reject the null hypothesis. COncluding that the missingness of the 'rating' column is not dependent on 'carb_prop'

To test whether or not the missingness of the 'rating' column is dependent on 'calories (#)' which is the number of calories in a given recipe, we ran a permutation test by shuffling the missingness of the 'rating' column 1000 times to collect 1000 simulated mean differences between the distribution with missing data and without missing data.

To investigate this goal, we ran a permutation test following the guide below:

Null Hypothesis: The missingness of ratings does not depend on the calories in a recipe

Alternate Hypothesis: The missingness of ratings does depend on the calories of steps in a recipe

Test Statistic: The absolute difference of mean in the calories between the distribution of the group without missing ratings and the distribution of the grouop with missing ratings

Significance Level: 0.05

The observed difference of means for 'missing_rating' and 'calories (#)' we got is roughly 69.007228, indicated by the red line on the graph. Because the p_value we found (0.0) is less than 0.05, we reject the null hypothesis. Concluding that the missingness of the 'rating' column is dependent on 'calories (#)'

## Hypothesis 

Our goal is to look into whether people rate recipes with high carbohydrate proportion lower than recipes with not high carbohydrate proportions. To find this we set that a high proportion of carbohydrates in a recipe is roughly 65% (0.65).

Because we are not provided with any information regarding the population, we chose to run a permutation test to understand our goal. We wanted to look into this goal because we believe that people would rate recipes with higher proportion of carbohydrates lower due to the stigma around eating recipes with high amounts of carbohydrates. Because our goal is to test if people rate recipes with higher amounts of carbohydrates, this would make our testing a directional testing. To correct this, we do not take the absolute mean of ratings but isntead the mean ratings.

To investigate our main goal, we ran a permutation test following the guide below:

Null Hypothesis: People rate recipes all the same

Alternate Hypothesis: People rate recipes with high carbohydrates lower than recipes with not high carbohydrtaes

Test Statistic: The absolute difference of mean bwteeen rating of high carb recipes and not high carb recipes

Significance Level: 0.05

The observed difference of means for 'rating' and 'carb_heavy' we got is roughly -0.0067145, indicated by the red line on the graph. Because the p_value we found (0.903) is more than 0.05, we fail reject the null hypothesis. Concluding that people rate recipes equally.

## Framing a Prediction Problem

## Baseline Model

## Final Model

## Fairness Analysis