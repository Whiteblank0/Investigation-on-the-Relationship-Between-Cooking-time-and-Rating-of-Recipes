# Investigation on the Relationship Between Cooking Times and Average Ratings of Recipes

Authors: Yaoxi Li

## Introduction

Our lives revolve around food, and for many people, cooking is both a necessity and a joy. A recipe's perceived quality and preparation time are two important considerations when choosing what to prepare. Since the community views recipes with greater ratings as tried and tested, they typically receive more attention. However, to what extent does a recipe's cooking time affect its rating? The purpose of this analysis is to look into the connection between average recipe ratings and cooking times. In particular, we want to know if people's ratings of a recipe are significantly impacted by cooking times. In order to investigate this subject, we are examining a dataset of food.com recipes and user reviews that were gathered for Majumder et al.'s research work on recommender systems, Generating Personalized Recipes from Historical User Preferences. We can learn more about how time investment and user pleasure are related thanks to this dataset, which offers a wealth of information on preparation times, cooking times, and user comments.

The first dataset, `recipe`, contains 83782 rows, indicating 83782 unique recipes, with 10 columns recording the following information:

| Column             | Description                                                                                                                                                                                       |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `'name'`           | Recipe name                                                                                                                                                                                       |
| `'id'`             | Recipe ID                                                                                                                                                                                         |
| `'minutes'`        | Minutes to prepare recipe                                                                                                                                                                         |
| `'contributor_id'` | User ID who submitted this recipe                                                                                                                                                                 |
| `'submitted'`      | Date recipe was submitted                                                                                                                                                                         |
| `'tags'`           | Food.com tags for recipe                                                                                                                                                                          |
| `'nutrition'`      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `'n_steps'`        | Number of steps in recipe                                                                                                                                                                         |
| `'steps'`          | Text for recipe steps, in order                                                                                                                                                                   |
| `'description'`    | User-provided description                                                                                                                                                                         |
| `'ingredients'`    | Text for recipe ingredients                                                                                                                                                                       |
| `'n_ingredients'`  | Number of ingredients in recipe                                                                                                                                                                   |

The second dataset, `interactions`, contains 731927 rows and each row contains a review from the user on a specific recipe. The columns it includes are:

| Column        | Description         |
| :------------ | :------------------ |
| `'user_id'`   | User ID             |
| `'recipe_id'` | Recipe ID           |
| `'date'`      | Date of interaction |
| `'rating'`    | Rating given        |
| `'review'`    | Review text         |

## Data Cleaning and Exploratory Data Analysis

To make our analysis of the dataset more efficient and convenient, we conducted the following data cleaning steps.

1. Fill all ratings of 0 with np.nan.

   - A rating of 0 might not signify an actual rating but could instead indicate that the item was not rated or no feedback was provided. Replacing 0 with np.nan helps differentiate between a true zero rating (if it exists) and missing data.

1. Add column `'average_rating'` containing average rating per recipe.

   - To provide a more thorough idea of a recipe's rating, we average all of the ratings because a recipe may have many ratings from various individuals.

1. Split values in the nutrition column to individual columns of floats.

   - The values in the nutrition column are objects that behave like strings, despite the fact that they appear to be a list. We may infer the meaning of each individual value included in brackets from the description of the recipe dataset's columns. We used a lambda function to divide the data and then changed the columns to floats. We will be able to perform numerical computations on the columns thanks to it.

1. Left merge the recipes and average_rating series on id and recipe_id.

   - This step helps match the unique recipes with their average rating.

#### Result
Here are all the columns of the cleaned df.

| Column                  | Description    |
| :---------------------- | :------------- |
| `'id'`                  | int64          |
| `'minutes'`             | int64          |
| `'submitted'`           | datetime64[ns] |
| `'tags'`                | object         |
| `'n_steps'`             | int64          |
| `'n_ingredients'`       | int64          |
| `'average rating'`      | object         |
| `'calories (#)'`        | float64        |
| `'total fat (PDV)'`     | float64        |
| `sugar (PDV)'`          | float64        |
| `'sodium (PDV)'`        | float64        |
| `'protein (PDV)'`       | float64        |
| `'saturated fat (PDV)'` | float64        |
| `'carbohydrates (PDV)'` | float64        |


Our cleaned dataframe ended up with 83782 rows and 27 columns.

### Univariate Analysis

<iframe
  src="aa.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis

<iframe
  src="bb.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Assessment of Missingness

Three columns, `'date'`, `'rating'`, and `'review'`, in the merged dataset have a significant amount of missing values, so we decided to assess the missingness on the dataframe.

### NMAR Analysis

We believe that the missingness of the `'review'` column is NMAR, because if people feel indifferent about the recipe, they are less likely to leave a review for it since they would feel like they have nothing significant to talk about. People usually will leave a review only if they have stronger emotions towards the recipe. Their emotions would motivate them to go onto the page, click multiple buttons to leave, and take some time out of their day to write a review. For example, people who enjoyed the recipe would be willing to do all the work to leave a good review for the recipe.

## Hypothesis Testing

In this investigation, we look into the connection between dishes' average ratings and cooking times. In particular, we want to determine if recipes with longer cooking times typically receive lower average ratings. We used the following hypotheses, test statistic, and significance level in a permutation test to investigate this question.

**Null Hypothesis:** A recipe's average rating is unaffected by how long it takes to cook.

**Alternative Hypothesis:** Average ratings are lower for recipes that require longer cooking times.

**Test Statistic:** The difference in mean between average ratings of recipes with long cooking times and recipes with non-long cooking times.

**Significance Level:** 0.05

Since we don't know the population distribution, we decided to utilize a **permutation test**, which enables us to determine whether the observed difference in average ratings between the two groups is statistically significant. While the directional structure of the hypothesis reflects our curiosity in whether recipes with longer cooking durations are rated lower, maybe due to more effort or inconvenience, the focus on average rating lets us capture user sentiment across recipes.

To conduct the test, we divided the data into two groups:

- **Long-cooking recipes**, with cooking times above the mean cooking time.
- **Non-long-cooking recipes**, with cooking times below the mean.

We then computed the **observed test statistic**, which is the mean difference in average ratings between the two groups. The observed statistic was **0.0244**, indicating that recipes with shorter cooking times have slightly higher average ratings on average.

Next, we shuffled the average ratings across the dataset 1,000 times to generate a distribution of mean differences under the null hypothesis. This allowed us to calculate the **p-value**, which represents the proportion of simulations where the test statistic is at least as extreme as the observed value. The resulting **p-value** was **0.000**, indicating a highly statistically significant result.

<iframe
  src="hy_test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

#### Conclusion of Permutation Test

Since the **p-value (0.000)** is less than the significance level of 0.05, we **reject the null hypothesis**. This indicates that the average rating of recipes is not independent of their cooking time. Specifically, recipes with longer cooking times tend to have lower average ratings. A plausible explanation for this result could be that users are less likely to rate recipes favorably if they require significant time and effort to prepare.

## Framing a Prediction Problem

We plan to **predict the preparation time (in minutes) of a recipe**, framing this as a **regression problem**. The response variable is `minutes`, representing the preparation time for each recipe. This prediction task is important because it helps users estimate the time required for cooking and select recipes that suit their schedules.

We chose `minutes` as the response variable because it is an informative measure of recipe complexity. Additionally, features like the number of ingredients (`n_ingredients`), the number of steps (`n_steps`), and other recipe characteristics such as nutritional values (`calories`, `protein_PDV`, etc.) are hypothesized to influence preparation time.

To evaluate our model, we use **Mean Absolute Error (MAE)** as the metric. MAE provides a clear interpretation of the average prediction error in terms of minutes. It is less sensitive to outliers than Root Mean Squared Error (RMSE), making it suitable since some recipes have extremely long preparation times.

All features used for this prediction task are present in the dataset and relate to recipe characteristics, ensuring they are logically available at the time of prediction.

---

## Baseline Model

For the baseline model, we used a **Random Forest Regressor** and split the data into training and testing sets. The features used were:
- `n_steps`: A numerical column containing the number of steps in the recipe.
- `n_ingredients`: A numerical column representing the number of ingredients.

Both features were left as-is, as they are already numerical and straightforward to interpret. Using these two features, we trained the Random Forest Regressor with default hyperparameters.

**Baseline Model Performance**:
- **MAE**: 18.74 minutes

This indicates that, on average, the model's predictions are off by about 18.74 minutes compared to the actual preparation times. While this performance provides a starting point, we aim to improve it by incorporating additional features and tuning the model.

---

## Final Model

For the final model, we incorporated additional features and transformed the data for improved performance. The features used were:

### Features
1. **`n_steps`**
   - The number of steps in a recipe reflects complexity. Recipes with more steps are likely to take longer to prepare.
   - **Transformation**: Standardized using `StandardScaler`.

2. **`n_ingredients`**
   - The number of ingredients also indicates complexity. Recipes with more ingredients often take longer.
   - **Transformation**: Standardized using `StandardScaler`.

3. **Interaction Feature: `complexity`**
   - Created by multiplying `n_steps` and `n_ingredients`. This captures the combined effect of both variables.

### Modeling Algorithm
We used a **Gradient Boosting Regressor** and performed a grid search to tune hyperparameters:
- **`max_depth`**: Controls the depth of the decision trees to prevent overfitting.
- **`n_estimators`**: Sets the number of boosting rounds.
- **`learning_rate`**: Shrinks the contribution of each tree to control complexity.

**Best Hyperparameters**:
- `max_depth`: 5
- `n_estimators`: 150
- `learning_rate`: 0.1

### Final Model Performance
- **MAE**: 17.63 minutes
- This is a significant improvement from the baseline model's MAE of 18.74 minutes.

---

## Fairness Analysis

### Problem Definition
For the fairness analysis, we split recipes into two groups:
- **High Ingredients**: Recipes with `n_ingredients > 7` (median).
- **Low Ingredients**: Recipes with `n_ingredients <= 7`.

### Evaluation Metric
We used **Mean Absolute Error (MAE)** to evaluate the model’s fairness for each group.

### Hypotheses
- **Null Hypothesis**: The model is fair. Its MAE for recipes with fewer ingredients and more ingredients are roughly the same, and any differences are due to random chance.
- **Alternative Hypothesis**: The model is unfair. Its MAE for recipes with fewer ingredients is higher than its MAE for recipes with more ingredients.

### Test Statistic
The difference in MAE between the two groups:
\[
\text{Test Statistic} = \text{MAE for fewer ingredients} - \text{MAE for more ingredients}
\]
