# The Sweetness of Peoples Ratings

## Introduction 
 Our data consists of ratings and recipes taken from food.com. We were given two datasets, one containing the recipes themselves (called recipes), and another containing user reviews on those recipes (called interactions). Throughout our exploration of the dataset, we realized that the main question people want to have answered about food is: How good does it taste? There is a column in our data that helps us answer this question: the rating column! By figuring which recipes have the best ratings, we can make some sort of judgement on what the best recipe (and food!) is. We noticed that the recipes had certain tags associated with them, one of those tags being the "desserts" tag. So we decided to test **whether or not dessert recipes had lower average ratings than non-dessert recipes**. Our analysis could be useful to any user browsing food.com looking at recipes that are labled as desserts. Why? It would show them that desserts might possibly not be the best recipes to get from food.com if they end up having lower average ratings than non-dessert recipes. 

### Question
Do dessert recipes have lower average ratings than non-dessert recipes?


### Dataset
 Our dataset ended up containing 234429 rows and 15 columns. Relevant columns include the "tags" column, "nutrition" column, and "mean_rating" column.
 The tags column includes information about what type of food the recipe makes, for example a relevant tag we looked for was "dessert". Other tags include "for-large-groups", "60-minutes-or-less", and "american". The nutrition column includes a lot of the nutrition facts of the food, including calories, fat, sugar, sodium, protein and carbohydrates. The mean_rating column tells us the average rating of the recipe from all the users who rated it.


## Cleaning and EDA

### Data Cleaning
 To clean our data, we first had to figure out a way to merge the recipes dataset with the user reviews dataset. We accomplished this by renaming the "recipe_id" column in the interactions to "id" so we could merge the two datasets together. We then performed a left merge between the two DataFrames to create our 234429 row dataset we used for the rest of our analysis. We filled in any rating of 0 with NaN because it's impossible to give a rating of 0 stars on food.com. The only way a review is "0" is if they didn't leave a review at all, so we filled any zeroes with Nans. We calculated the average ratings for each recipe by grouping by "id" (because each id corresponds to one recipe) and aggregating by mean. We took the rating column from that grouped dataframe, then merged it back into the original dataframe with the new column being named "mean_rating". We dropped the "date" and "review" columns because they become irrelevant after merging. 

 We converted the nutrition column to a list, and made every element in the list its own column in the dataframe. We dropped the rating column because we didn't have a use for it after we created the "mean_rating" column. We removed duplicate recipes from the dataframe, which dropped our rows down to 83782 from 234429. We found one row in the datafame with a missing recipe name. Through some research on the ingredients column, we were able to figure out that the missing recipe name was Salad Dressing. The description was also missing from this specific row so we were able to find the correct description by looking at the other recipes the author put on food.com. All of her other descriptions were the same, so we just copied one over to replace the missing value. 

 We replaced all NaN description values with "No Description", just to remove any NaN values in our dataset. For the mean_rating value, any recipe that had a NaN mean_rating was replaced by a zero, because if no one reviewed the recipe then we assumed no one liked it enough to even deserve a review. A NaN user value was found, we replaced it with "No User Found" just so we could remove all NaN values from our dataset. 

 Recipes that were above the 97th percentile of calories count or minutes needed were removed. This was done to eliminate extreme outliers that would skew the mean and graphs heavily.

 We also added a column that would say True if the recipe had the "dessert" tag associated with it, or False if it did not have the "dessert" tag associated with it. 

**Head of DataFrame**

|    | name                                 |     id |   minutes |   contributor_id | submitted   | tags                                                                                                                                                                                                                                                                                               | nutrition                                                   |   n_steps | steps                    | description                                                                                                                                                                                                                                                                                                                                                                       | ingredients                                                                                                                                                                                                                             |   n_ingredients |          user_id |   mean_rating |   calories |   total fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated fat (PDV) |   carbohydrates (PDV) | dessert   |
|---:|:-------------------------------------|-------:|----------:|-----------------:|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------|----------:|:-------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|-----------------:|--------------:|-----------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|:----------|
|  0 | 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings']                                                                        | ['138.4', '10.0', '50.0', '3.0', '3.0', '19.0', '6.0']      |        10 | Steps Omitted (Too Long) | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make.....sereiously! there's no doubt that these will be your fav brownies ever for you can add things to them or make them plain.....either way they're pure heaven!                                                                                                              | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour']                                                          |               9 | 386585           |             4 |      138.4 |                10 |            50 |              3 |               3 |                    19 |                     6 | True      |
|  1 | 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | ['60-minutes-or-less', 'time-to-make', 'cuisine', 'preparation', 'north-american', 'for-large-groups', 'canadian', 'british-columbian', 'number-of-servings']                                                                                                                                      | ['595.1', '46.0', '211.0', '22.0', '13.0', '51.0', '26.0']  |        12 | Steps Omitted (Too Long) | this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the best chocolate chip cookies i have ever had! if you don't have margarine or don't like it, then just use butter (softened) instead.                                                                                                                                            | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']                                                                             |              11 | 424680           |             5 |      595.1 |                46 |           211 |             22 |              13 |                    51 |                    26 | False     |
|  2 | 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                                                                                               | ['194.8', '20.0', '6.0', '32.0', '22.0', '36.0', '3.0']     |         6 | Steps Omitted (Too Long) | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']                                                                   |               9 |  29782           |             5 |      194.8 |                20 |             6 |             32 |              22 |                    36 |                     3 | False     |
|  3 | millionaire pound cake               | 286009 |       120 |           461724 | 2008-02-12  | ['time-to-make', 'course', 'cuisine', 'preparation', 'occasion', 'north-american', 'desserts', 'american', 'southern-united-states', 'dinner-party', 'holiday-event', 'cakes', 'dietary', 'christmas', 'thanksgiving', 'low-sodium', 'low-in-something', 'taste-mood', 'sweet', '4-hours-or-less'] | ['878.3', '63.0', '326.0', '13.0', '20.0', '123.0', '39.0'] |         7 | Steps Omitted (Too Long) | why a millionaire pound cake?  because it's super rich!  this scrumptious cake is the pride of an elderly belle from jackson, mississippi.  the recipe comes from "the glory of southern cooking" by james villas.                                                                                                                                                                | ['butter', 'sugar', 'eggs', 'all-purpose flour', 'whole milk', 'pure vanilla extract', 'almond extract']                                                                                                                                |               7 | 813055           |             5 |      878.3 |                63 |           326 |             13 |              20 |                   123 |                    39 | True      |
|  4 | 2000 meatloaf                        | 475785 |        90 |          2202916 | 2012-03-06  | ['time-to-make', 'course', 'main-ingredient', 'preparation', 'main-dish', 'potatoes', 'vegetables', '4-hours-or-less', 'meatloaf', 'simply-potatoes2']                                                                                                                                             | ['267.0', '30.0', '12.0', '12.0', '29.0', '48.0', '2.0']    |        17 | Steps Omitted (Too Long) | ready, set, cook! special edition contest entry: a mediterranean flavor inspired meatloaf dish. featuring: simply potatoes - shredded hash browns, egg, bacon, spinach, red bell pepper, and goat cheese.                                                                                                                                                                         | ['meatloaf mixture', 'unsmoked bacon', 'goat cheese', 'unsalted butter', 'eggs', 'baby spinach', 'yellow onion', 'red bell pepper', 'simply potatoes shredded hash browns', 'fresh garlic', 'kosher salt', 'white pepper', 'olive oil'] |              13 |      2.20436e+06 |             5 |      267   |                30 |            12 |             12 |              29 |                    48 |                     2 | False     |


### Univariate Analysis 

**Sugar Analysis**
The plot below shows the number of recipes associated with each sugar value in the data. As the Percent Daily Value of sugar increases, the number of recipes associated with that sugar value decreases, which shows us that a bulk of the recipes contains less than 50 Percent Daily Value of sugar. 
<iframe src="assets/uni1.html" width=800 height=600 frameBorder=0></iframe>

**Mean_Rating Analysis**
The plot below shows the distribution of mean_ratings of all recipes in the data. As shown in the plot, there are a lot more highly rated reviews than lower rated reviews (more than half of the recipes have a 4.9 - 5.0 star rating).
<iframe src="assets/uni2.html" width=800 height=600 frameBorder=0></iframe>

**Calories Analysis**
The plot below shows the distribution of calories from the recipes in the data. A bulk of the recipes have less than 500 calories.
<iframe src="assets/fig2.html" width=800 height=600 frameBorder=0></iframe>

**Statistics**
|             |     Mean |   Median |   Standard Deviation |
|:------------|---------:|---------:|---------------------:|
| Sugar (PDV) |  41.5452 |     22   |             50.4455  |
| Mean Rating |   4.4841 |      5   |              1.01452 |
| Calories    | 385.586  |    301.9 |            336.977   |

### Bivariate Analysis

**Mean_Rating vs. Sugar**
Below plot is a scatter plot of mean rating vs. sugar, showing a slightly positive correlation between the two values.
<iframe src="assets/bi_analysis1.html" width=800 height=600 frameBorder=0></iframe>

**Dessert vs. Non-Dessert**
Below plot shows the distribution between recipes that have the "desserts" tag associated with them vs. the recipes that do not have the "desserts" tag. The recipes distribution with the "desserts" tag looks to have lower ratings than the other distribution without the "desserts" tag.
<iframe src="assets/bi_analysis2.html" width=800 height=600 frameBorder=0></iframe>


### Interesting Aggregates

The below grouped table shows the difference of means between the distribution of recipes that are dessert and the distribution of recipes that are not desserts. 

| dessert   |    mean |   count |
|:----------|--------:|--------:|
| False     | 4.50572 |   69145 |
| True      | 4.36899 |   12986 |


## Assessment of Missingness

### NMAR Analysis
After loading in and inspecting the dataset, there were two columns that have missing values that were considered to be important to assess the missingness in the dataset. These were the 'description' and the 'mean_rating' columns, of which the former is considered to be Not Missing At Random (NMAR).

Upon closer inspection, the rows with missing descriptions were selected and other columns were selected as well. The group found that these missing descriptions were related to the age of the recipes when they were submitted. The newest date in these recipes was in the year 2011, and some recipes with missing descriptions were as old as 2008. This would make the NMAR values of the 'description' column to be MAR as there could be a possibility that older recipes did not have any descriptions when they were written.

| recipe id | submitted   |   description |
|------:|:------------|--------------:|
| 22467 | 2011-03-02  |           nan |
| 15652 | 2011-03-02  |           nan |
| 69723 | 2011-03-01  |           nan |
| 64915 | 2011-03-01  |           nan |
| 16909 | 2011-03-01  |           nan |

### Missingness Dependency
The other column with missing values, 'mean_rating' is chosen for analysis to determine its missingness mechanism. To test this, a new dataframe was created that had four quantitative columns selected (minutes,n_steps,n_ingredients,calories) in order to test whether mean_rating is Missing Completely at Random (MCAR)(no explanation in the dataset as to why mean_ratings is missing) or Missing at Random (MAR) (the missingness of mean_ratings is related to another column in the dataset).

In order to check whether these chosen columns had an influence on the missingness of mean_rating or not, a permutation test was conducted to check whether the means of the (minutes,n_steps,n_ingredients,calories) of the recipe are different based on whether a mean_rating is present for that recipe or not. The chosen test statistic for this test is the Kolmogorov-Smirnov test statistic, as the distributions for each attribute have different shapes, but centered around the same location.

<iframe src="assets/calories_graph.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="assets/ingredients_graph.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="assets/minutes_graph.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="assets/steps_graph.html" width=800 height=600 frameBorder=0></iframe>


After executing the permutation test for these columns against mean_rating missingness, it was found out that the missingness of mean_ratings was MAR based on minutes,n_steps, and calories. The difference in these columns for missing and non-missing values in mean_rating is significant enough to suggest that mean_ratings is MAR based on these columns. 

| column        |   K-S Statistic | Reject Null?   |
|:--------------|----------------:|:---------------|
| calories      |     3.16339e-07 | Yes            |
| n_steps       |     5.29815e-12 | Yes            |
| minutes       |     5.3682e-26  | Yes            |
| n_ingredients |     0.0540379   | No             |

On the other hand, the permutation test did not yield significant results for the n_ingredients column, which means that the missingness of mean_ratings is not dependent on this column.

## Hypothesis Testing 

**Null Hypothesis:** In the population, mean ratings of recipes that make desserts and recipes that do not make desserts have the same distribution, and the observed differences in our samples are due to random chance.

**Alternate Hypothesis:** In the population, recipes that make desserts have a lower mean rating than non-dessert recipes, on average. The observed difference in our samples cannot be explained by random chance alone.

**Test Statistic:** Difference in group means. In the context of our data: mean rating of dessert recipes - mean rating of non-dessert recipes. We used difference of group means because we looked at the two distributions visually and in an agreggated and found that there were differences between the two distributions, which allowed us to use the difference of group means as a test statistic.
Observed test statistic: -0.13748308266376963

**Significance Level:** We are choosing a significance level of 0.01 because we want to make sure that if our test ends up rejecting the null hypothesis, we have less room for error. If we use a significance level of 0.01, we only have a 1% risk of concluding that a difference exists when there is no actual difference.

We then performed a permutation test, shuffling the mean_rating then computing the test statistic. We performed 500 repetitions of this test.

**Resulting P-Value:** Our P-Value ended up being 0, which is below the significance level we set for this test. 

**Conclusion:** Under our null hypothesis, it is extremely rare to see a difference of -0.13748308266376963 that we found in our observed statistic. We reject the null hypothesis that the two groups came from the same distribution. So, it is a possibility that dessert recipes recieve lower average ratings than non-dessert recipes. That means it is possible that people on food.com do not like dessert as much as non-dessert food. 







