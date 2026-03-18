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

| league   |   result | position   |   totalgold |   barons |   dragons |   heralds |   void_grubs |   total_obj |
|:---------|---------:|:-----------|------------:|---------:|----------:|----------:|-------------:|------------:|
| LFL2     |        0 | team       |       42255 |        0 |         0 |         0 |            0 |           0 |
| LFL2     |        1 | team       |       53936 |        1 |         2 |         1 |            6 |          10 |
| LFL2     |        1 | team       |       64669 |        1 |         3 |         1 |            6 |          11 |
| LFL2     |        0 | team       |       50679 |        0 |         2 |         0 |            0 |           2 |
| LFL2     |        0 | team       |       51389 |        0 |         0 |         0 |            2 |           2 |

## Baron Advantage Distribution

<iframe 
    src="assets/baron_plot.html"
    width="800"
    height="600"
    frameborder="0">
</iframe>
The distribution shows that most games have a small baron difference (0–1), meaning baron control is usually close between teams, with large differences being relatively rare.

---

## Dragon Advantage Distribution

<iframe 
    src="assets/dragon_plot.html"
    width="800"
    height="600"
    frameborder="0">
</iframe>
The dragon difference is more spread out, suggesting that teams are able to build larger advantages in dragons compared to barons over the course of a game.

## Relationship Between Objectives and Winning

<iframe 
    src="assets/box_plot.html"
    width="800"
    height="600"
    frameborder="0">
</iframe>
Winning teams tend to secure significantly more total objectives than losing teams, as shown by the higher median and overall distribution in the box plot.

---

<iframe 
    src="assets/line_plot.html"
    width="800"
    height="600"
    frameborder="0">
</iframe>
There is a strong positive relationship between objective difference and win rate, with teams gaining more objectives becoming increasingly likely to win the game.

### Grouped Table: Win Rate by Baron Difference

|   barons_diff |   games |   win_rate |
|--------------:|--------:|-----------:|
|             0 |    4340 |       0.5  |
|             1 |    5998 |       0.91 |
|             2 |     962 |       0.88 |
|             3 |     102 |       0.86 |
|             4 |       4 |       0.75 |

This grouped table summarizes how win rate changes as a team’s baron advantage increases. Teams with no baron advantage win about half the time, while teams with a baron advantage of 1 win about 91% of the time, showing that even a small edge in barons is strongly associated with winning. Interestingly, the win rate slightly decreases for larger baron differences, which may suggest that barons become less impactful as the game progresses, since teams may already have large leads or other objectives that play a bigger role in determining the outcome.