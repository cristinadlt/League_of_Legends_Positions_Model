by Kathleen Nguyen and Cristina De La Torre

## Framing the Problem 
Within this project, we investigate a prediction problem related to the positions of Champions in League of Legends. More specifically, the prediction problem we focus on is “predicting which position between Top laner or Support a Champion was played as given their past post-game data”. We chose to predict between these two groups in particular because Top and Support are distinct positions that require different areas of skills. Thus, these predictions would give us the ability to determine which position a Champion should be played in, contributing to the preferred outcome of game play. 

To address the posed objective, we built a classification model that performs **binary classification** based on past tournament data of Champions. To evaluate the performance of our classifier we used the **accuracy** of predictions. We chose this metric because the most valued aspect of performance for this prediction problem was the correct classification of data points. To further explain, being incorrectly classified as ‘Top’ or ‘Support’ does not come with negative or positive connotations and so both outcomes are equally desired.

### More on Data
Our dataset was a collection of data on Champions played in 2022 League of Legends International and North American competitive matches found on Oracle’s Elixir. When cleaning the data, we first removed any row where a Champion had a value of zero within the `P%` column, indicating they had not been selected for any games. We then dropped any row with a different position than the specified two, and changed the types of the remaining columns when needed. More information on specific data cleaning can be found <a href= "https://cristinadlt.github.io/League_of_Legends_Positions_Analysis/" > here </a>. 

Our resulting dataset consisted of 28 datasets and contained 1,393 rows and 11 columns.  The columns relevant to the construction of our model included `Champion`, `Pos`, `GP`, `P%`, `W%`, `K`, `D`, `A`, `KDA`, `KP`, and `DTH%`. These columns all described the in-game performance of Champions, data that may correlate to positions, along with our two specified positions. In relation to these selections, the time of prediction would be pre-game and so realistically we usedprevious game play data of all the included columns for predictions. 

### Columns
- `Champion`: Name of champion
- `Pos`: Position of champion (only Top-laner and Support)
- `GP`: Games played
- `P%`: Percentage of games champion was picked
- `W%`: Win percentage
- `K`: Total kills
- `D`: Total deaths
- `A`: Total assists
- `KDA`: Total Kill/Death/Assist ratio
- `KP`: Kill participation percentage of team's kills
- `DTH%`: Average share of team’s total deaths

### Head of our cleaned DataFrame

| Champion   | Pos     |   GP |    P% |   W% |   K |   D |   A |   KDA |    KP |   DTH% |
|:-----------|:--------|-----:|------:|-----:|----:|----:|----:|------:|------:|-------:|
| Aatrox     | Top     |   24 | 0.511 | 0.71 |  89 |  63 | 148 | 0.038 | 0.578 |  0.228 |
| Alistar    | Support |   11 | 0.234 | 0.36 |   2 |  42 |  87 | 0.021 | 0.636 |  0.23  |
| Amumu      | Support |   13 | 0.277 | 0.77 |  17 |  51 | 158 | 0.034 | 0.668 |  0.291 |
| Braum      | Support |    5 | 0.106 | 0.2  |   3 |  19 |  29 | 0.017 | 0.615 |  0.216 |
| Camille    | Top     |    4 | 0.085 | 0.25 |  12 |  20 |  15 | 0.014 | 0.563 |  0.263 |

## Baseline  Model
For our baseline model, we used a decision tree classifier because we are performing binary classification. All the columns described in the previous section were used as features in our model with the `Pos` column as our response variable. Our features and their types were the following:

**Quantitative**: `GP`, `P%`, `W%`, `K`, `D`, `A`, `KDA`, `KP`, `DTH%`

**Nominal**: `Champion`, `Pos`

We one-hot encoded the `Champion` column since it was originally categorical data. We also standardized the `K`, `D`, and `A` columns because they had a large range and we wanted to minimize the impact of outliers. Standardizing also made comparisons between the columns easier. As for the rest of the features, we kept them as is since they were numerical data. 

### Performance
The baseline model had an accuracy of approximately 0.79 on our test set. Thus, we believed our model was good to some extent. Our model performed well since we would expect Champions of distinct positions to perform differently and have varying statistics. For example, Champions with the position ‘Support’ may have had more kills than Champions who were played as ‘Top’. The difference in performance would lead to making predictions on positions less complicated.

## Final Model 
When creating our final model, the features added to our classifier are described below: 
- `K` / `GP` : average kills per games played
- `D` / `GP`:  average deaths per games played
- `A` / `GP`: average assists per games played
- `KP` - `DTH%`: difference between the kill participation and average share of team total deaths 

We chose to use the proportion `K`/`GP` as a feature because `K` describes cumulative kills, as it is a total, which would vary depending on the amount of games played as specified by `GP`. Due to this variation being based on the number of games played, calculating the rate of kills per game would describe kills in relation to games played. The inclusion of this feature improved the classifier’s performance as it bolstered the differences between positions as seen in patterns of kills. In a similar way, the other components of `KDA` were converted to rates in relation to `GP` for the same reason as they also described total overall quantities rather than values in an individual match.

In addition to these, we also introduced the difference between `KP` and `DTH%` as a feature because the distance between these values was indicative of a type of performance. The importance of kills in relation to deaths may have varied between these two positions. In relation to the performance of the model, including this feature improved predictions as the distances reflected the in-game areas of skills helping to distinguish between the two positions. 

We tested a decision tree classifier and a logistic regression model separately to determine the best modeling algorithm for our final model. After determining the best hyperparameters for both, we ultimately picked the logistic regression model because it returned a higher accuracy for both our training and testing sets. 

For our final model using logistic regression, our hyperparameters were 'max_iter' and 'solver'. We tuned 'solver' because we wanted to check if different solvers fit our data better than the default of 'lbfgs'. We also tuned 'max_iter' to determine the best number of iterations for our solver to converge, which impacts the precision of our model. If we used a 'max_iter' that was too high, our model could potentially overfit the training data. We used GridSearchCV in order to find the best hyperparameters. The result of our search for the best hyperparameters was 160 for the 'max_iter' and 'liblinear' for the 'solver'.

### Performance
Our final model’s accuracy was about 0.92 on the test set. Compared to our baseline model’s accuracy of 0.79 on the same test set, the performance of our final model greatly improved over our baseline model’s performance. The new features we engineered as well as our decision to use logistic regression resulted in the improvement between the two models. Additionally, our search for best hyperparameters further refined our predictions which increased our model’s accuracy.

## Fairness Analysis
To determine the fairness of our model, we address the question “Does our model perform better for Champions that are popular than it does for Champions that are not?”. Specifically, the groups we are comparing are popular Champions and unpopular Champions. 

Using the `GP` column to determine the number of games played, we found the mean number of games played to be about 5 games. As a result, we define popular Champions to be those that were played in 6 or more games in a single tournament. On the other hand, Champions grouped as unpopular are those that were selected to play in 5 or less games in a single tournament.

**Evaluation Metric**: Accuracy

**Null Hypothesis**: Our model is fair. The accuracy of our model for popular and unpopular Champions are roughly the same. Any observed differences are due to random chance alone.

**Alternative Hypothesis**: Our model is unfair. The accuracy of our model for popular Champions is higher than its accuracy for unpopular Champions. Observed differences cannot be due to random chance alone.

**Test Statistic**: Accuracy of Model for Popular Champions - Accuracy of Model for Unpopular Champions

**Significance Level**: 0.05

<iframe src="assets/empirical-distribution-of-difference-in-accuracy.html" width=800 height=600 frameBorder=0></iframe>

The results of our permutation test are shown above. According to the chart, our observed difference in accuracy was not close to a majority of differences found by our permutation test. 

**P-value** = 0.0001

### Conclusion
At a significance level of 0.05, we reject the null hypothesis that our model is fair. It is highly likely that the observed difference in the accuracy of our groups cannot be due to random chance alone and that the accuracy of our model for popular Champions is higher than our model's accuracy for unpopular Champions.

