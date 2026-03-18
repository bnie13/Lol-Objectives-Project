## Introduction

This project analyzes professional League of Legends esports data from the 2025 season to understand how objective control influences match outcomes. In competitive play, teams compete for key objectives such as dragons, barons, heralds, atakhans, and void grubs, all of which provide important advantages that can impact the result of a game.

The central question of this project is: **how well can objective control predict the winner of a match?** In particular, I investigate whether differences in objectives secured between teams can be used to accurately predict match outcomes using a machine learning model.

This question is important because it helps identify which aspects of gameplay are most impactful in determining success. Understanding the role of objectives can provide insight for players, analysts, and teams when making strategic decisions during matches.

The dataset consists of professional League of Legends matches from 2025 and contains **120,636 rows and 165 columns**, where each row represents a single game.

The relevant columns used in this analysis include:

- `result`: The match outcome (1 = win, 0 = loss)
- `league`: The league in which the match was played
- `position`: The role of the player in the row, or team if it is a team row
- `barons`: Number of barons secured
- `elders`: Number of Elder Dragons secured
- `dragons`: Number of dragons secured
- `heralds`: Number of Rift Heralds secured
- `void_grubs`: Number of void grubs secured

These variables capture different types of objective control across matches, allowing us to analyze how securing objectives relates to winning outcomes.

## Data Cleaning and Exploratory Data Analysis

Before conducting any analysis, I performed several data cleaning steps to ensure the dataset was consistent and usable for modeling.

First, I removed the `atakhans` column entirely. This column contained a large number of missing or inconsistent values and was not directly relevant to the core objective of this project, which focuses on major in-game objectives such as dragons, barons, heralds, and void grubs. Keeping this column would have introduced unnecessary noise into the analysis without providing meaningful predictive value.

Next, I dropped all rows where the `elders` column contained missing (`NaN`) values. Since Elder Dragon is an important late-game objective, missing values in this column likely indicate incomplete or improperly recorded matches. Including these rows could lead to inaccurate conclusions or bias in the model, so removing them ensured that all remaining data points had complete information for key objectives.

These cleaning steps helped improve the overall quality and reliability of the dataset by removing irrelevant features and incomplete observations. As a result, the final dataset used for analysis contains only matches with consistent and meaningful objective data, which leads to more accurate modeling and interpretation.

Below is a preview of the cleaned dataset:
| league   |   result | position   |   barons |   dragons |   total_obj |
|:---------|---------:|:-----------|---------:|----------:|------------:|
| LFL2     |        0 | team       |        0 |         0 |           0 |
| LFL2     |        1 | team       |        1 |         2 |          10 |
| LFL2     |        1 | team       |        1 |         3 |          11 |
| LFL2     |        0 | team       |        0 |         2 |           2 |
| LFL2     |        0 | team       |        0 |         0 |           2 |