# Analysis on the Relationship Between Carbohydrates and Recipe Ratings

Authors: Maya Sissel & Trinity Truong

## Overview

This is a data science project for the DSC 80 course at UCSD. The project focuses
on analyzing the relationship between the amount of carbohydrates in a recipe 
and the rating of the recipe.

## Introduction

As a part of living, food is something every person needs. Food provides us with energy and nutrients to support body functions. Deciding what to cook can be a hobby or a challenge in everyday life. Foods high in carbohydrates are critical in maintaining a healthy diet because they provide the body with glucose. However, consuming large amounts of refined simple carbohydrates has been linked to increased risk of metabolic, cardiovascular diseases and certain types of cancers. These risks are highlighted in studies done on the "Burden of Carbohydrates in Health and Disease", conducted by the national center for Biotechhnology Information.

With this information in mind, we want to analyze whether people rate recipes with higher proportions of carbohydrates lower than recipes without high proportions of carbohydrates. To do this, we will explore 2 data sets containing recipes and reviews posted by users on [food.com](https://www.food.com/) since 2008. These data sets were originally scraped and used by Bodhisattwa Prasad Majumder, Shuyang Li, Jianmo Ni and Julian McAuley for a recipe reccommender research paper, [Generating Personalized Recipes from Historical User Preferences](https://cseweb.ucsd.edu/~jmcauley/pdfs/emnlp19c.pdf).

In this first dataset we will be looking into, `recipe`, this contains 83782 rows of unique recipes and 12 columns describing different aspects of each recipe:

| Column             | Desciption                               |
| :------------------| :----------------------------------------|
| `'name'`           | Name of the recipe                       |
| `'id'`             | Recipe ID                                |
| `'minutes'`        | Minutes needed to prepare recipe         |
| `'contributor_id'` | User ID who submitted this recipe        |
| `'submitted'`      | Date the recipe was submitted            |
| `'tags'`           | Food.com tags that describe the recipe   |
| `'nutrition'`      | Nutrition information in the format of [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV),saturated fat (PDV), carbohydrates (PDV)] where PDV represents "percentage of daily value" |
| `'n_steps'`        | Number of steps in a recipe              |
| `'steps'`          | Text for recipe steps, in the order to be followed|
| `'description'`    | User-provided desciption of the recipe   |
| `'ingredients'`    | The ingredients needed for a recipe      |
| `'n_ingredients'`  | The number of ingredients in a recipe    |

The second dataset we will be looking into, `interactions`, contains 731927 rows of reviews from a user on a specific recipe and 5 columns descibing different aspects of the review:

| Column                | Desciption                               |
| :---------------------| :----------------------------------------|
| `'user_id'`           | User ID                                  |
| `'recipe_id'`         | Recipe ID                                |
| `'date'`              | Date of when this review was posted      |
| `'rating'`            | Rating given of the recipe               |
| `'review'`            | The text of the review given by user     |

Using these two datasets, we will analyze whether people rate recipes with higher proportions of carbohydrates lower than recipes without high proportions of carbohydrates.

## Data Cleaning and Exploratory Data Analysis

For us to analyze our main question, we would need to clean our given datasets for better efficiency and readability. To do this we followed the steps outlined below:

1. Left merge the `recipes` and `interactions` datasets together.

    -This step allows for us to view all reviews of a given recipe along with the information about the recipe.

    -To merge the `recipes` and `interactions` datasets toegther, we did a left merge on `id` from `recipes` and `recipe_id` from `interactions` which matches the recipe ID to the review for the given recipe ID.

2. Verify data types of all columns in the merged dataset

    -This step allows for us to understand what data is contained in each column and verify the data type to ensure efficiency in later calculations.

    -This step also allows us to view what columns may need a data type conversion later.

    -To verify data types of all the columns, we printed the merged dataset `recipe_reviews` datatypes.

    -The columns and data type are as shown below:

    | Column             | Desciption  |
    | :------------------| :-----------|
    | `'name'`           | object      |
    | `'id'`             | int64       |
    | `'minutes'`        | float64     |
    | `'contributor_id'` | int64       |
    | `'submitted'`      | object      |
    | `'tags'`           | object      |
    | `'nutrition'`      | object      |
    | `'n_steps'`        | int64       |
    | `'steps'`          | object      |
    | `'description'`    | object      |
    | `'ingredients'`    | object      |
    | `'n_ingredients'`  | int64       |
    | `'user_id'`        | float64     |
    | `'recipe_id'`      | float64     |
    | `'date'`           | object      |
    | `'rating'`         | float64     |
    | `'review'`         | object      |

3. Fill all ratings of 0 with `np.nan`

    -This step allows for us to differetiate between missing ratings and negative ratings of a given recipe.

    -In addition this step allows us to verify ratings on a scale of 1 to 5.

    -To follow this step, we used the replace function on the `rating` column of our merged dataset `recipe_reviews` to replace all ratings of `0` with `np.nan`.

4. Find the average rating per recipe and adding it as a new column in the merged dataset

    -This step is because each recipe can have multiple reviews with varying ratings, by getting the average we can get a general understanding of the rating of a specific recipe.

    -To follow this step, we grouped the merged dataset `recipe_reviews` by `id` then took the mean values of the `rating` column. This gives us a series which we then assigned to a new column `avg_rating` into our merged dataset.

5. Expand `nutrition` column into individual columns of float64 values

    -This step allows for us to interact with individual objects within the `nutrition` column

    -To follow this step, we first split the `nutrition` column of our merged dataset, this allows for the values of the list to be seperated. Next we made this new series into a DataFrame called `nutrition_df`. After getting a DataFrame of this, we merged `nutrition_df` with `recipe_reviews`

6. Convert `submitted` and `date` to datetime format

    -This step allows for us to work with these columns as datetime to investigate relationships of other columns over a period in time.

    -To follow this step we reassigned the `submitted` and `date` columns in our merged DataFrame using pd.to_datetime.

7. Add `carb_prop` to our merged DataFrame `recipe_reviews`

    -This step allows for us to work with the proportion of carbohydrates in a given recipe to help answer our main question of intrest.

    -To calculate the proportion of carbohydrates in a given recipe, we would first need to convert the values in `carbohydrates (PDV)` to grams. To do this, we first divide the values in the `carbohydrates (PDV)` column by 100 to get decimal values. Next we multiply by 300 to convert our decimal values into grams because 300 grams is 100% of the recommended daily value of carbohydrate intake. Then we take this value and multiply it by 4 because there are 4 grams per calorie. This then allows for us to divide the resulting value by the number of `calories` in a recipe to get the proportion of carbohydrates.

8. Add `carb_heavy` to our merged DataFrame `recipe_reviews`

    -This step allows for us to differenciate recipes with a high proportion of carbohydrates which is 65% or above from recipes with a low amount of carbohydrates.

    -To do this, I assigned a new column `carb_heavy` to be a boolean checking if the value in `carb_prop` is more than or equal to 65% (.65).

9. Add `missing_rating` to our merged DataFrame `recipe_reviews`

    -This step allows for us to differenciate between ratings that  missing and ratings that are present.

    -This column `missing_rating` is a boolean column that checks the values of `rating` to see if it is `np.nan` or missing.

### Result

This is our resulting merged DataFrame `recipe_reviews`

| Column             | Desciption  |
| :------------------| :-----------|
| `'name'`           | object      |
| `'id'`             | int64       |
| `'minutes'`        | float64     |
| `'contributor_id'` | int64       |
| `'submitted'`      | object      |
| `'tags'`           | object      |
| `'nutrition'`      | object      |
| `'n_steps'`        | int64       |
| `'steps'`          | object      |
| `'description'`    | object      |
| `'ingredients'`    | object      |
| `'n_ingredients'`  | int64       |
| `'user_id'`        | float64     |
| `'recipe_id'`      | float64     |
| `'date'`           | object      |
| `'rating'`             | float64     |
| `'review'`             | object              |
| `'review'`             | object              |
| `'avg_rating'`         | float64             |
| `'calories (#)'`       | float64             |
| `'total_fat (PDV)'`    | float64             |
| `'sugar (PDV)'`        | float64             |          
| `'sodium (PDV)'`       | float64             | 
| `'protein (PDV)'`      | float64             |
| `'saturated_fat (PDV)'`| float64             |
| `'carbohydrates (PDV)'`| float64             |
| `'carb_prop'`          | float64             |
| `'carb_heavy'`         | bool                |

Our cleaned DataFrame `recipe_reviews` ended up with 234429 rows and 27 columns. Below is the head of our merged DataFrame. Because there are many coluumns we picked the columns most relevant to our main question. 

| name                                 |     id |   minutes |   n_ingredients |   rating |   avg_rating |   calories (#) |   carbohydrates (PDV) |   carb_prop | carb_heavy   |
| :----------------------------------- | :----- | :-------- | :-------------- | :------- | :----------- | :------------- | :-------------------- | :---------- | :----------- |
| 1 brownies in the world    best ever | 333281 |        40 |               9 |        4 |            4 |          138.4 |                     6 |    0.476879 | False        |
| 1 in canada chocolate chip cookies   | 453467 |        45 |              11 |        5 |            5 |          595.1 |                    26 |    0.480591 | False        |
| 412 broccoli casserole               | 306168 |        40 |               9 |        5 |            5 |          194.8 |                     3 |    0.169405 | False        |
| 412 broccoli casserole               | 306168 |        40 |               9 |        5 |            5 |          194.8 |                     3 |    0.169405 | False        |
| 412 broccoli casserole               | 306168 |        40 |               9 |        5 |            5 |          194.8 |                     3 |    0.169405 | False        |


### Univariate Analysis

For univariate analysis, we looked into the distribution of the proportion of carbohydtrates in recipes. In the graph below, the distribution is skewed to the middle, heavily leaning right side, indicating to us that a majority of recipes have a low proportion of carbohydrates. This skew also indicates to us that as the carbohydrate proportion rises, the less recipes there are on food.com.

<iframe
  src="assets/univariate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
### Bivariate Analysis

For bivariate analysis, we looked into the distribution of ratings between recipes with a high carbohydrate proportion (of 65% or higher) to recipes with a low carbohydrate proportion. In the graph below, we found that recipes with a higher amount of carbohydrates have ratings of 4-5 while recipes with lower carbohydrtaes have ratings spread 1-5 with more in 4 and 5.

<iframe
  src="assets/bivariate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
### Interesting Aggregates

For this section, we investigated the relationship between the number of steps in a recipe with the proportion of carbohydrates in the recipe. To do this, we first made a smaller dataframe containing the columns `n_steps` and 'carb_prop'. This allows for us to isolate the data we want to focus on. Next we then grouped this dataframe by 'n_steps' and aggregated the remaining carbohydtare proportion column to find the mean, median, min and max to understand whether carbohydrate proportion has any influence on the number of steps a recipe takes.

|   n_steps |   mean_carb |   median_carb |   min_carb |   max_carb |
|----------:|------------:|--------------:|-----------:|-----------:|
|         1 |    0.391294 |      0.358737 |          0 |    1.15183 |
|         2 |    0.398178 |      0.384615 |          0 |    1.12245 |
|         3 |    0.389381 |      0.384615 |          0 |    1.39241 |
|         4 |    0.372284 |      0.355476 |          0 |    1.313   |
|         5 |    0.349412 |      0.330259 |          0 |    1.20879 |

From this graph we found that as the number of steps increases, the proportion of carbohydrates in a given recipe varies. Based on this plot, recipes with 40 or more steps contain a lower proportion of carbohydrates than recipes that have 0-40 steps. This is highlighted by the lines having a negative slope downwards as the number of steps increases.

<iframe
  src="assets/mini_recipes.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
## Assessment of Missingness

In the Dataframe `recipe_reviews` there are 3 columns with a significant number of missing values. These columns are `rating`, `review` and `description`. Because of this, we want to investighate the missingness on the DataFrame.

### NMAR Analysis

We believe that the misssingess of the `rating` column is NMAR because a person is less likely to return to the website to leave a rating if they hate or fail to follow through with the recipe properly. The same can be said for people who would feel neutral about a recipe but do not bother returning to the website and leave any ratings. People usually leave a rating if they have tried a recipe or follow through with creating it (without failing) and enjoyed it. A person's feelings of enjoying the recipe would then lead them to take the time to return to the website and leave a positive rating.

### Missingness Dependency

We wanted to look into the missingness of the `rating` column in our merged `recipes_reviews` DataFrame by testing whether `rating` is dependent on other existing columns. To do this, we test whether or not the missingness of the `rating` column is dependent on `carb_prop` which is the proportion of carbohydrtaes in a given recipe. We would also test whether or not the missingness of `rating` column is dependent on the amount of calories there are in a given recipe.

To test whether or not the missingness of the `rating` column is dependent on `carb_prop` which is the proportion of carbohydrtaes in a given recipe, we ran a permutation test by shuffling the missingness of the `rating` column 1000 times to collect 1000 simulated mean differences between the distribution with missing data and without missing data.

To investigate this goal, we ran a permutation test following the guide below:

Null Hypothesis: The missingness of ratings does not depend on the proportion of carbohydrates

Alternate Hypothesis: The missingness of ratings does depend on the proportion of carbohydrates

Test Statistic: The absolute difference of mean in the proportion of carbohydrates between the distribution of the group without missing ratings and the distribution of the grouop with missing ratings

Significance Level: 0.05
<iframe
  src="assets/distr_rating_carb.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The observed difference of means for `missing_rating` and `carb_prop` we got is roughly 0.003209, indicated by the red line on the graph. Because the p_value we found (0.1) is greater than 0.05, we fail to reject the null hypothesis. Concluding that the missingness of the `rating` column is not dependent on `carb_prop`.
<iframe
  src="assets/emp_rating_carb.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

To test whether or not the missingness of the `rating` column is dependent on `n_ingredients` which is the number of calories in a given recipe, we ran a permutation test by shuffling the missingness of the `rating` column 1000 times to collect 1000 simulated mean differences between the distribution with missing data and without missing data.

To investigate this goal, we ran a permutation test following the guide below:

Null Hypothesis: The missingness of ratings does not depend on the calories in a recipe

Alternate Hypothesis: The missingness of ratings does depend on the calories of steps in a recipe

Test Statistic: The absolute difference of mean in the calories between the distribution of the group without missing ratings and the distribution of the grouop with missing ratings

Significance Level: 0.05
<iframe
  src="assets/distr_rating_n_ingredients.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The observed difference of means for `missing_rating` and `n_ingredients` we got is roughly 0.160737, indicated by the red line on the graph. Because the p_value we found (0.0) is less than 0.05, we reject the null hypothesis. Concluding that the missingness of the `rating` column is dependent on `n_ingredients`
<iframe
  src="assets/emp_rating_n_ingredients.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Hypothesis Testing

Our goal is to look into whether people rate recipes with high carbohydrate proportion lower than recipes with not high carbohydrate proportions. To find this we set that a high proportion of carbohydrates in a recipe is roughly 65% (0.65).

Because we are not provided with any information regarding the population, we chose to run a permutation test to understand our goal. We wanted to look into this goal because we believe that people would rate recipes with higher proportion of carbohydrates lower due to the stigma around eating recipes with high amounts of carbohydrates. Because our goal is to test if people rate recipes with higher amounts of carbohydrates, this would make our testing a directional testing. To correct this, we do not take the absolute mean of ratings but isntead the mean ratings.

To investigate our main goal, we ran a permutation test following the guide below:

Null Hypothesis: People rate recipes all the same

Alternate Hypothesis: People rate recipes with high carbohydrates lower than recipes with not high carbohydrtaes

Test Statistic: The absolute difference of mean bwteeen rating of high carb recipes and not high carb recipes

Significance Level: 0.5
<iframe
  src="assets/hypothesis.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

#### Conclusion of Permutation Test

The observed difference of means for `rating` and `carb_heavy` we got is roughly -0.0067145, indicated by the red line on the graph. Because the p_value we found (0.903) is more than 0.05, we fail reject the null hypothesis. Concluding that people rate recipes equally. One plausible reason for this finding could be that people do not mind the proportion of carbohydrates in a recipe.

## Framing a Prediction Problem

In this project we plan to predict the number of minutes a recipe would take and it would be a regression problem because it is a continuous numerical value. Where we cannot limit the cooking time to a fixed set of values. For us to predict the number of minutes a recipe would take, we will build a regression model.

We want to predict the number of minutes it takes for a recipe (`minutes` column) because the number of minutes can be helpful in deciding what to cook and if the person has enough time to follow the recipe. To evaluate our model, we plan to use root mean squared error because it penalizes larger errors more heavily and it's also expressed in minutes allowing for us to interpret results easier.

The information we have prior to making our prediction model are columns in our merged Dataframe, using a mix of columns from the `recipes` Dataframe and the `interactions`. We want to use these columns in creating our prediction model because they contain features that can be related to how long the cooking time takes.

## Baseline Model

## Final Model

## Fairness Analysis