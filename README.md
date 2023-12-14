# LoL_GameResult_ModelPrediction

by Lewis Weng

***Note***: This is website is for a DSC80 project.

---

## Framing the Problem

This analysis will be focused on using League of Legends in-game statistics to predict the outcome of a game. For the model, I will be building a binary classification model that uses a decision tree to predict a win or loss. This model will be predicting a win or loss because fans care about whether their teams win or lose. This model can help someone understand whether their team is more likely to win or lose. This model will mainly use metrics within the first 20 minutes of the game. The metrics used are xp difference at 15 minutes, gold difference at 15 minutes, first dragon, first baron, first herald, and league. These information will be available to the users while the game is still in progress. Most of this information (baron spawns at 20 minutes) will available by the 20 minute mark, which almost all games surpass. For the metric, I've decided to use accuracy. In League of Legends, there is always a winner and a loser, so models are not incentivized to predict win or lose more than the other. Since this is a game, false positives and false negatives are not important for consdieration, so recall, precision, and F-1 score are not used.

---

## Baseline Model

My baseline model is a decision tree classifier that takes in these columns of the dataset: league, heralds, xpdiffat15, golddiffat15, result, firstdragon, firstbaron. 'League' contains nominal data of the region the game was played in. For my model, I trained it with data from the major regions: LCK, LCS, LEC. The LPL data has missing data, so LPL games cannot be used for model training or testing. 'Heralds' contains the number of heralds a team took that game. It is a numerical data and the values are either 0, 1, or 2. 'xpdiffat15' and 'golddiffat15' are both ratio data. They contain the gold/xp difference at 15 minutes, in which negative values would mean being behind in gold, 0 would mean that there is no gold advantage for either team, and positive values would mean that they are ahead in gold. 'firstdragon' and 'firstbaron' contains boolean values of 0 and 1 and are ratio data. A 0 means that the team did not get first dragon or first baron, and it is a true zero. For my baseline model, I one-hot encoded the league column because it is a categorical column. I also binarized the 'xpdiffat15' column by setting a threshold at 2000. I set this threshold because smaller leads such as 100 xp is not noteworthy and is not meaningful enough to be considered a useful lead. The current model has a training accuracy of 91.9% and a testing accuracy of 77.8%. This is not a good model because it seems to be overfitted to the training data and does not work nearly as well for unseen data.

---

## Final Model

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

## Fairness Analysis

<iframe src="assets/hypothesis_fig.html" width=600 height=450 frameBorder=0></iframe>

This visualization is created by shuffling the 'result' column and getting the distribution of wins with a gold lead at 15 minutes. The red line is the observed statistic, which is not part of the empirical distribution. 

The null hypothesis is: Having a Gold Lead at 15 minutes does not affect winning chances.
The alternate hypothesis is: Having a Gold Lead at 15 minutes does not affect winning chances.


The p-value is 0, so we can reject the null hypothesis that gold leads at 15 minutes do not affect winning chances.

The conclusion is teams that are ahead in gold at the 15 minute mark tend to win those games. While there are other factors that affect game result, such as difference between team strength, the data suggests that getting ahead in the first 15 minutes seems to allow professional teams to reliably convert that lead into a win.

---