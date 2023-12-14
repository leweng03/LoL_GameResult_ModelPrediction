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

In my final model, I added two more features in the heralds and golddiffat15 column. I squared the heralds column so that instead of containg 0's, 1's, and 2's, it'll contain 0's, 1's, and 4's. I made this change because I beleive that getting 2 heralds is a much more indicative sign of winning than getting 1 herald, so the difference between 0 and 1 should not be treated the same as the difference between 1 and 2 (note: 0, 1, or 2 heralds can be taken in a game, teams can choose to not take herald). The next feature I implemented was standardizing the golddiffat15 column. By standardizing the golddiffat15 column, it will be easier to see whether a lead is true significant or not, relative to the other numbers in the dataset. For instance, instead of seeing the value 1000 in the golddiffat15 column, seeing a number such as 0.2 would indicate that this gold lead is above the mean (0 is the mean), and we can see how far a this number is from the mean in terms of standard deviations.


For my final model, I continued to use the decision tree classifier, but I changed the hyperparameters. In the baseline model, I used entropy as the criterion, set the max depth at 15, and set min_samples_split at 10. As can be seen by the difference in model accuracy, the model was overfitted because the max depth was too high. In my new model, the criterion is still entropy, but the max depth is now set at 3 and the min_samples_split is set at 2. After changing the hyperparameters and encoding two new features, my new model's training accuracy is 83.6% and its testing accuracy is 83.2%. While the training accuracy for my model decreased, the testing accuracy increased by 6%. This is an improvement because high training accuracy can simply mean overfitting, but higher testing accuracy means that the model is more generalizable and works better on unseen data, which is what I want in this model. 

---

## Fairness Analysis

To see whether the model works better or worse on different groups, I decided to compare its performance on the LEC/LCS vs its performance on the LCK. I decided to combine the LEC and LCS because their performance at international events are relatively the same and they have similar playstyles. For my evaluation metric, I will use accuracy. The null hypothesis is that the model is fair and any differences are due to random chance. The alternate hypothesis is that the model is not fair and any differences are not due to random chance. To test this, I will use a t-test set at a significance level of 0.05.

My model's test accuracy for the LEC/LCS is 81.6%, while its test accuracy for the LCK is 85.0%. When I run permutation tests, I get a p-value of 0.163, meaning that 16.3% of the simulated results for the LEC/LCS are greater than 85.0%. With a significance p-level set at 0.05, we fail to reject the null hypothesis and cannot conclude that my final model is unfair.

---