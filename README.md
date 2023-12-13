A project for DSC80 at UCSD in which we develop a model to predict the positions(Top vs. Support) of a Champion based on 2022 League of Legends data and discuss its fairness.

## Framing the Problem 
Within this project, we investigate a prediction problem related to the positions of Champions in League of Legends. More specifically, the prediction problem we focus on is “predicting which position between Top laner or Support a Champion was played as given their past post-game data”. We chose to predict between these two groups in particular because Top and Support are distinct positions that require different areas of skills. Thus, these predictions would give us the ability to determine which position a Champion should be played in, contributing to the preferred outcome of game play. 

To address the posed objective, we built a classification model that performs **binary classification** based on past tournament data of Champions. To evaluate the performance of our classifier we used the **accuracy** of predictions. We chose this metric because the most valued aspect of performance for this prediction problem was the correct classification of data points. To further explain, being incorrectly classified as ‘Top’ or ‘Support’ does not come with negative or positive connotations and so both outcomes are equally desired.

### More on Data
Our dataset was a collection of data on Champions played in 2022 League of Legends International and North American competitive matches found on Oracle’s Elixir. When cleaning the data, we first removed any row where a Champion had a value of zero within the `P%` column, indicating they had not been selected for any games. We then dropped any row with a different position than the specified two, and changed the types of the remaining columns when needed. More information on specific data cleaning can be found <a href= "https://cristinadlt.github.io/League_of_Legends_Positions_Analysis/" > here </a>. 

Our resulting dataset consisted of 28 datasets and contained 1,393 rows and 11 columns.  The columns relevant to the construction of our model included `Champion`, `Pos`, `GP`, `P%`, `W%`, `K`, `D`, `A`, ` KDA`, `KP`, and `DTH%`. These columns all described the in-game performance of Champions, data that may correlate to positions, along with our two specified positions. In relation to these selections, the time of prediction would be pre-game and so realistically we usedprevious game play data of all the included columns for predictions. 

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



