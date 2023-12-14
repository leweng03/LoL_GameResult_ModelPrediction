# LoL_GameResult_ModelPrediction

by Lewis Weng

***Note***: This is website is for a DSC80 project.

---

## Introduction

This analysis is based on the League of Legends Competitive Matches dataset, which can be found on the website "Oracle's Elixir". In this dataset, there are 149400 rows and 123 columns. The data includes the champions picked and banned, the teams that played, their regions, objectives taken, game time, and differences in gold and experience at the 15 minute mark. Every row of the column equates to one player. Every 10 columns there are 2 columns that contains information about the 2 teams. Every match is 12 rows.

This project will be focusing on these columns: league, firstdragon, firstherald, firsttower, golddiffat15, and result. New columns that are based on the existing columns will be created for the purposes of this project. The 'league' refers to the league in which the game was played in. 'firstdragon' refers to whether a team has claimed first dragon (0 for No, 1 for Yes). 'firstherald' refers to whether a team has taken the first herald (0 for No, 1 for Yes). 'firsttower' refers to whether a team has taken the first tower (0 for No, 1 for Yes). 'golddiffat15' refers to the gold difference betweeen a player and their opposing counterpart, or a team and their opponent. The 'result' column contains data on whether a team or player won the game (0 for Loss, 1 for Win). 

The goal of this project is to determine whether having a gold lead in the early game leads to winning games. This dataset contains game information from professional League of Legends esports games from 2022. Other League of Legends players may be interested in this information because it can help them identify what can help them win more games. People that bet on League of Legends esports games can also use this information to evaluate the strength of teams or determine to likelihood of their bets succeeding while the game is in progress. 

---

## Cleaning and EDA

For my analysis, I convert the columns that consist of 0's and 1's to boolean values of True and False. These columns are 'firstdragon', 'firstherald', 'firsttower', 'result'. I also removed the rows in the dataset that contained null values. These rows were missing information that were needed for the analysis. I added a column named 'goldlead@15'. If golddiffat15 was over 0, goldlead@15 is True. Otherwise, goldlead@15 is False. This column helps me with my analysis by identifing the presence of gold lead.

| league   | firstdragon   | firstherald   | firsttower   |   golddiffat15 | result   | goldlead@15   |
|:---------|:--------------|:--------------|:-------------|---------------:|:---------|:--------------|
| LCK      | False         | True          | True         |           4757 | False    | True          |
| LCK      | True          | False         | False        |          -4757 | True     | False         |
| LCK      | False         | True          | True         |          -1045 | False    | False         |
| LCK      | True          | False         | False        |           1045 | True     | True          |
| LCK      | True          | True          | False        |           1309 | True     | True          |

This is an example of what the dataset looked like after doing data cleaning and keeping relevant columns.

<iframe src="assets/goldfig15.html" width=600 height=450 frameBorder=0></iframe>

From this visualization we can see that the chances of winning are highly elevated when a team has a gold lead at 15 minutes. Teams that have a gold lead at 15 minutes have won 72% of the time, while teams that didn't have a gold lead at 15 minutes won 28% of the time.


<iframe src="assets/gold15_tower_fig.html" width=600 height=450 frameBorder=0></iframe>

This visualization shows the overlapping distribution for the gold difference at 15 minutes when first tower was taken vs first tower was not taken with the orange distribution being when first tower was not taken, the blue distribution being when first tower was taken, and the red in the center being the overlapping part of the distributions. This graph suggests that teams that have taken first tower tend to have a bigger gold lead.


| goldlead@15   |   result |   firstherald |   firstdragon |   firsttower |
|:--------------|---------:|--------------:|--------------:|-------------:|
| False         | 0.271654 |      0.359252 |      0.462598 |     0.195866 |
| True          | 0.728346 |      0.640748 |      0.537402 |     0.804134 |

These are some aggregate statistics that show the relation between goldlead@15 and other columns. Interestingly, taking first dragon does not necessarily mean that will be ahead in gold at 15 minutes. Other factor are more indicative, such as firsttower and firstherald. This suggests that taking first dragon may not be as useful as taking first herald or first tower.

---

## Assessment of Missingness

| league   | towermissing=False | towermissing=True |
|:---------|-------------------:|------------------:|
| LCK      |   0.459646         | 0.238363          |
| LCS      |   0.301181         | 0.156186          |
| LEC      |   0.239173         | 0.12403           |
| LPL      |   nan              | 0.481421          |

| league   | towermissing=False | towermissing=True |
|:---------|-------------------:|------------------:|
| LCK      | 0.259107           | 0.245952          |
| LCS      | 0.216218           | 0.240873          |
| LEC      | 0.281739           | 0.255403          |
| LPL      | 0.242935           | 0.257773          |

<iframe src="assets/LCK_tower_fig.html" width=600 height=450 frameBorder=0></iframe>


From the table, the distribution looks different. In the permutation test, we get a p-value of 0, which means that we can reject the null hypothesis that they come from the distribution. The towermissing column is NMAR in relation to the league column.

<iframe src="assets/side_vs_towermissing.html" width=600 height=450 frameBorder=0></iframe>

In the dataset, 50% of the towermissing was on red side and the other 50% of the tower missing was on blue side (there are two sides, 1 for each team). When I shuffle the 'towermissing' column and run permutation tests, I get this visualization, and the p-value is 0.524, which means we fail to reject the null hypothesis and the towermissing column MAR in relation to side. 

---

## Hypothesis Testing

<iframe src="assets/hypothesis_fig.html" width=600 height=450 frameBorder=0></iframe>

This visualization is created by shuffling the 'result' column and getting the distribution of wins with a gold lead at 15 minutes. The red line is the observed statistic, which is not part of the empirical distribution. 

The null hypothesis is: Having a Gold Lead at 15 minutes does not affect winning chances.
The alternate hypothesis is: Having a Gold Lead at 15 minutes does not affect winning chances.


The p-value is 0, so we can reject the null hypothesis that gold leads at 15 minutes do not affect winning chances.

The conclusion is teams that are ahead in gold at the 15 minute mark tend to win those games. While there are other factors that affect game result, such as difference between team strength, the data suggests that getting ahead in the first 15 minutes seems to allow professional teams to reliably convert that lead into a win.

---