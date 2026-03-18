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
    src="./assets/baron_plot.html"
    width="800"
    height="450"
    frameborder="0">
</iframe>

The distribution shows that most games have a small baron difference (0–1), meaning baron control is usually close between teams, with large differences being relatively rare.

---

## Dragon Advantage Distribution

<iframe 
    src="./assets/dragon_plot.html"
    width="800"
    height="450"
    frameborder="0">
</iframe>

The dragon difference is more spread out, suggesting that teams are able to build larger advantages in dragons compared to barons over the course of a game.

## Relationship Between Objectives and Winning

<iframe 
    src="./assets/box_plot.html"
    width="800"
    height="450"
    frameborder="0">
</iframe>

Winning teams tend to secure significantly more total objectives than losing teams, as shown by the higher median and overall distribution in the box plot.

---

<iframe 
    src="./assets/line_plot.html"
    width="800"
    height="450"
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

### Assessment of Missingness

The primary columns in this dataset with missing values are `atakhans` and `elders`. However, I do not believe that either of these columns is MNAR (Missing Not At Random).

For both `atakhans` and `elders`, the missing values appear to be caused by data collection inconsistencies rather than the underlying values themselves. In particular, many of the missing entries are concentrated in specific leagues, such as the LPL or broader Asia-based competitions, suggesting that the issue arises from incomplete or inconsistent recording of these statistics in certain regions.

Because the missingness is tied to external factors like league or data source, rather than depending on the actual (unobserved) values of `atakhans` or `elders`, it is unlikely to be MNAR. Instead, the missingness is more consistent with missing at random (MAR), since it can be explained by an observed variable (league), or possibly MCAR if the recording issues are entirely arbitrary.

To better understand and potentially model this missingness, additional metadata such as region, data provider, or collection methodology would be useful. This would allow us to directly verify whether certain leagues omit these values.

Overall, while these columns contain missing data, the evidence suggests that the missingness is driven by data collection differences rather than the values themselves, so they are unlikely to be MNAR.

### Missingness Permutation Tests

To investigate whether missingness in the `atakhans` column is related to other variables, I conducted permutation tests.

First, I tested whether missingness depends on **league**. The observed test statistic (difference between the maximum and minimum proportion of missing values across leagues) was 1.0. The permutation test produced a p-value of approximately 0.001, which is very small. This provides strong evidence that missingness in `atakhans` is dependent on league.

<iframe 
    src="./assets/missing_league_plot.html"
    width="800"
    height="450"
    frameborder="0">
</iframe>


I test whether Baron objective advantage has a stronger impact on win rate than Dragon objective advantage.

## Hypothesis Testing

**Null Hypothesis:**  
The difference in win rate advantage between Baron and Dragon objectives is due to random chance.

**Alternative Hypothesis:**  
Baron objective advantage leads to a larger win rate difference than Dragon objective advantage.

**Test Statistic:**  
The difference between win rate gaps:  
(WinRate when ahead − WinRate when behind) for Baron minus the same quantity for Dragon.

**Significance Level:**  
0.05

**Results:**  
Observed test statistic ≈ 0.149  
p-value ≈ 0.0002  

<iframe
    src="assets/hypothesis_plot.html"
    width="800"
    height="450"
    style="border:none;">
</iframe>

**Conclusion:**  
Since the p-value is much smaller than 0.05, there is strong evidence against the null hypothesis. This suggests that Baron objective advantage is associated with a larger increase in win probability compared to Dragon advantage. This aligns with gameplay intuition, as Baron provides a powerful late-game buff that can directly enable teams to close out games, whereas Dragons provide more gradual benefits.

## Framing a Prediction Problem
The goal of this prediction task is to predict whether a team will win a game based on in-game statistics. This is a **binary classification problem**, since the response variable only has two possible outcomes: win or loss.

**Response Variable:**  
The response variable is `result`, where 1 indicates a win and 0 indicates a loss. This is a natural choice because the primary objective in a game is to win, and it allows us to directly evaluate how different features contribute to success.

**Features (at time of prediction):**  
The model uses features that would reasonably be available during the game, such as objective counts (e.g., barons, dragons, heralds), and other aggregated statistics. These features are chosen because they reflect a team’s performance up to that point without using any information from the future.

**Evaluation Metric:**  
I use accuracy as the primary evaluation metric, which measures the proportion of correctly predicted outcomes. Accuracy is appropriate here because the dataset is relatively balanced between wins and losses, so there is no strong class imbalance that would require metrics like F1-score.

**Justification:**  
This setup reflects a realistic prediction scenario, where we want to estimate a team’s probability of winning based on current game state information. By restricting features to those available at the time of prediction, the model avoids data leakage and better represents how such a model would be used in practice.

## Baseline Model

I build a baseline model to predict whether a team wins using a **logistic regression classifier**.

**Features:**
- `barons_diff` (quantitative): difference in number of barons taken
- `dragons_diff` (quantitative): difference in number of dragons taken

Both features are numerical and represent the relative objective advantage between teams.

**Response Variable:**
- `result` (binary): 1 for win, 0 for loss

**Preprocessing:**
Since both features are quantitative, I apply **standardization** using `StandardScaler` to normalize their scales before fitting the model. No categorical encoding is needed because there are no nominal or ordinal features in this model.

**Model:**
I use a **logistic regression model**, which is appropriate for binary classification problems and provides an interpretable relationship between objective differences and win probability.

**Performance:**
The model achieves an accuracy of approximately **0.89** on the test set.

**Evaluation:**
The model performs well, indicating that objective differences (barons and dragons) are strong predictors of game outcomes. However, this model is relatively simple and only uses two features, so it may not capture all aspects of the game. While the high accuracy suggests good predictive power, further improvements could be made by incorporating additional features such as gold difference or other objective statistics.

Overall, this baseline model provides a strong starting point while remaining simple and interpretable, and it demonstrates the importance of objective control in determining game outcomes.

## Final Model

To improve upon the baseline model, I expanded the feature set to better capture different aspects of objective control throughout the game.

**Features Added:**
- `barons_diff` (quantitative)
- `dragons_diff` (quantitative)
- `heralds_diff` (quantitative)
- `void_grubs_diff` (quantitative)
- `total_obj_diff` (quantitative)
- `early_obj_diff` (quantitative)

These features are all numerical and represent differences in objectives between teams. They are useful because they capture multiple stages of the game: early-game pressure (heralds, void grubs), mid-game objectives (dragons), and late-game power spikes (barons). By combining them, the model better reflects how different objectives contribute to winning at different points in the game.

**Model Choice:**
I continue using a **logistic regression classifier**, since the task is binary classification and the model remains interpretable while handling multiple features effectively.

**Hyperparameter Tuning:**
I use **GridSearchCV with 5-fold cross-validation** to select the best regularization strength. The parameter grid tested different values of `C` (0.01, 0.1, 1, 10) with L2 regularization. The best-performing hyperparameters were:

- `C = 10`
- `penalty = l2`

A higher value of `C` corresponds to weaker regularization, which allows the model to better fit the data when multiple informative features are included.

**Performance:**
The final model achieves an accuracy of approximately **0.91** on the test set.

**Improvement Over Baseline:**
Compared to the baseline model (accuracy ≈ 0.89), the final model performs better. This improvement is expected because the additional features provide a more complete representation of in-game dynamics, rather than relying only on barons and dragons.

**Evaluation:**
The model generalizes well to unseen data, as shown by its strong performance on the test set. By incorporating multiple objective types and tuning hyperparameters, the model captures more nuanced patterns in how teams win games. While there is still room for improvement, this model demonstrates a meaningful step up from the baseline in both performance and representation of the data-generating process.

## Fairness Analysis

To evaluate whether the model performs differently across leagues, I compare performance between two groups:

- **Group X (Major Leagues):** LCK, LEC, PCS, VCS, MSI, Worlds  
- **Group Y (Non-Major Leagues):** All other leagues (excluding LPL due to data issues)

**Evaluation Metric:**  
I use **accuracy** as the evaluation metric, computed separately for each group, since the model is a binary classifier and accuracy directly reflects prediction correctness.

**Null Hypothesis (H₀):**  
The model performs equally well across major and non-major leagues.

**Alternative Hypothesis (H₁):**  
The model performs differently between major and non-major leagues.

**Test Statistic:**  
The difference in accuracy between the two groups:
  
\[
\text{Accuracy}_{major} - \text{Accuracy}_{non-major}
\]

**Significance Level:**  
\[
\alpha = 0.05
\]

**Observed Statistic:**  
The observed difference in accuracy is approximately **-0.0305**, indicating slightly lower performance on major leagues.

**p-value:**  
Using a permutation test with 5000 simulations, the resulting p-value is approximately **0.016**.

**Conclusion:**  
Since the p-value is less than 0.05, we reject the null hypothesis. This suggests that there is statistically significant evidence that the model performs differently between major and non-major leagues. However, this does not imply a large or practically meaningful difference, only that the observed difference is unlikely to be due to random chance.