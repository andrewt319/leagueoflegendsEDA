# League of Legends Exploratory Data Analysis

## Introduction:
The League of Legends dataset loaded above give insight on games from the 2022 season. It specifies information and statistics for each player for each specific game, including bans, kills, deaths, damage taken per minute, wards placed, etc. As an absolute novice to this game who has questions on what plays a factor into winning, I want to analyze the decision that I’ve made so far and what I can do to best improve my chances of winning. Naturally, this led me to ask the question – is Veigar (my favorite champion from the 5 games I've played) more likely to win or lose any given match? This same analysis I’ve done on Veigar can be applied to any champion, and it helps to answer, using data, if a champion is more statistically likely to win or lose a game at the highest level.

I am using the League of Legends Competitive Matches 2022 dataset (https://oracleselixir.com/tools/downloads). This original dataset includes 149232 rows and 123 columns, but I filtered it down to only include the rows where Veigar was played. This filtered dataset includes 508 rows and 123 columns. Some notes about the data: each “gameid” corresponds to up to 12 rows – one for each of the 5 players on both teams and 2 containing summary data for the two teams. 


## Cleaning and EDA (Exploratory Data Analysis):
### Data Cleaning:
This League of Legends Competitive Matches 2022 dataset had a lot of missing data, and as such, had to be filled in accordingly. For columns like “url” and “split”, I filled in the NaN values as “unknown” because these aren’t fields that could be filled in using other values in that column. I filled in the NaN values for columns “playername”, “playerid”, and “champion” with “n/a”. It seemed like the only values missing for these columns were the rows were the ones that contained summary data for that specific game. I dropped columns such as “firstdragon”, “firstbarron”, “elders”, etc. since those are more team related stats, and my analysis is focused on individual players. For the other columns containing numerical values such as “doublekills”, “triplekills”, “totalgold”, “earnedgold”, etc., I imputed the NaN values with the median for each respective column. Finally, for columns “result” and “playoff”, I changed the type of the variables from 1/0 to True/False. The first few rows of the new cleaned dataset with a few chosen columns can be seen here:

| gameid                | datacompleteness   | url     | playoffs   |   kills |   deaths |   assists |
|:----------------------|:-------------------|:--------|:-----------|--------:|---------:|----------:|
| ESPORTSTMNT01_2690210 | complete           | unknown | False      |       2 |        3 |         2 |
| ESPORTSTMNT01_2690210 | complete           | unknown | False      |       2 |        5 |         6 |
| ESPORTSTMNT01_2690210 | complete           | unknown | False      |       2 |        2 |         3 |
| ESPORTSTMNT01_2690210 | complete           | unknown | False      |       2 |        4 |         2 |
| ESPORTSTMNT01_2690210 | complete           | unknown | False      |       1 |        5 |         6 |


Since my analysis had mostly to do with games that Veigar appeared in, I filtered out a new dataframe including only rows that Veigar was chosen as the champion. The first few rows of this dataset with a few chosen columns can also be seen here:

| gameid                | datacompleteness   | url     | playoffs   |   kills |   deaths |   assists |
|:----------------------|:-------------------|:--------|:-----------|--------:|---------:|----------:|
| NA1_4173680959        | complete           | unknown | False      |       4 |        1 |        14 |
| NA1_4173721657        | complete           | unknown | False      |       2 |        7 |         2 |
| ESPORTSTMNT01_2701158 | complete           | unknown | False      |       8 |        1 |         8 |
| ESPORTSTMNT04_2090500 | complete           | unknown | False      |       4 |        0 |         4 |
| ESPORTSTMNT04_2140153 | complete           | unknown | False      |       6 |        1 |         8 |


### Univariate Analysis:
<iframe src="assets/veigar_kills_plot.html" width=800 height=600 frameBorder=0></iframe>
Let’s take a look at Veigar’s kill statistics in all of the competitive matches that he appeared in. It seems like Veigar averaged consistently around 4 kills. This is above the average for all legends in the dataset (which is around 3). This isn’t to say that Veigar is inherently a stronger champion to get kills with, but more so, that it is more of an attacking based character. It does show though, that Veigar can hold his own as an attacking character, at least amongst the pros.

### Bivariate Analysis:
<iframe src="assets/goldspent_to_visionscore_plot.html" width=800 height=600 frameBorder=0></iframe>
Let’s look at some other statistics regarding Veigar – namely, the correlation between gold spent and vision score. It seems that with the more gold spent on items in the shop, the higher the vision score. 

### Interesting Aggregates:

| result (won?)   |   kills |   deaths |   assists |   damagetochampions |
|:----------------|--------:|---------:|----------:|--------------------:|
| False           | 2.66182 |  3.77091 |   3.2     |             13310.1 |
| True            | 5.71245 |  1.71245 |   7.32189 |             17151.8 |


Amongst the games that Veigar has played in, let’s compare the average kills, deaths, assists, and damage to champions in won and lost games. It seems like Veigar makes a huge difference statistics-wise on games that they have won or lost. On average, there are 3 more kills, 2 less deaths, and 4 more assists in games where Veigar has won.


## Assessment of Missingness:
### NMAR Analysis:
In my cleaned dataset for League of Legends 2022 Competitive Matches, I don’t believe there is a column that is NMAR. From looking at the NaN values inside the dataset, I would reason that they fall into three categories
1. Unknown – there isn’t known information in this column for this specific game, such as the url and split
2. N/A - the data is intentionally left blank because this column doesn’t apply to a specific player – it’s a team statistic.
3. Dependent on another column - data such as “triplekills” and “goldat10” seem to be missing according to whenever the game is partial, and not complete. It doesn’t seem like the NaN values in numerical columns are left blank intentionally. It doesn’t seem like they are trying to hide a player having 0 double kills to spike the mean upwards, since there are plenty of other 0s existing in that column. I also don’t see the creators of the dataset having sorted it in any way and losing part of the data, especially if this is a collection of game matches that could probably be found again. 
Because of all these reasons, I don’t believe that any column in the dataset is NMAR.

### Missingness Dependency:
<iframe src="assets/empirical_distribution_doublekills_kills.html" width=800 height=600 frameBorder=0></iframe>
One column that I chose to analyze further on its missingness is “doublekills”. I ran a permutation test on the distribution of “kills” when “doublekills” was and was not missing. My test statistic for this was the absolute difference of means. I shuffled the “doublekills” column repeatedly for 500 repetitions, and grouped by missingness. I proceeded to calculate the test statistic for each of these repetitions. I computed the test statistic on the original cleaned data frame as well. After running the test and plotting the results, it shows that there is definitely a correlation between the  missingness of “doublekills” and “kills” itself. The observed is way beyond what was seen in the 500 repetitions, so it was likely not due to random chance alone. 

<iframe src="assets/empirical_distribution_doublekills_earnedgold.html" width=800 height=600 frameBorder=0></iframe>
I also ran a permutation test on the distribution of “earnedgold” when “doublekills” was and not missing. My test statistic was also the absolute difference of means. And similarly, I shuffled the “doublekills” column repeatedly for 500 repetitions, and grouped by missingness. After running the permutation test and plotting the result, it shows that there is probably no correlation between the missingness of “doublekills” and “earnedgold” itself. What was found in the observed is right in line with the test statistics when we shuffled the missingness of “doublekills”. This is seen from how the red line (observed) is in the middle of the histogram plotted.

So what does this tell us about our initial question -- is Veigar (my favorite champion from the 5 games I've played) more likely to win or lose any given match? It means that if we are doing any statistical analysis regarding the “doublekills” column, to be careful. The NaN values aren’t necessarily missing completely at random (as seen through the permutation test), and therefore, may need more accurate imputations. 



## Hypothesis Testing:
<iframe src="assets/hypothesis_test_num_veigar_wins.html" width=800 height=600 frameBorder=0></iframe>
As a reminder, the question that I am seeking to answer is – is Veigar (my favorite champion from the 5 games I've played) more likely to win or lose any given match)? To answer this, I will conduct a hypothesis test with a significance level cutoff of 0.05. My hypotheses are as follows:
- Null Hypothesis: There is a fair chance for Veigar to win or lose any given match, and that any difference is attributed to random chance.
- Alternative Hypothesis: There is not a fair chance for Veigar to win or lose any given match. 

I will run 100,000 iterations, where for each iteration, I will calculate the test statistic of how many wins there are out of 508 appearances (the number of Veigar appearances in the dataframe). This number of wins will be calculated assuming the null is true – that there is a 50-50 chance of winning or losing.

The p-value for this hypothesis test was 0.03, which is below the significance level of 0.05. This means that we reject the null, and that the difference we see in our observed statistic is not due to random chance, and that there is not a fair chance for Veigar to win or lose any given match. In competitive matches at least, it seems that the odds are stacked against Veigar players as they are more likely to lose than to win. 