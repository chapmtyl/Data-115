# Data-115 Personal Project
Tyler Chapman

11573259

tyler.chapman@wsu.edu

### Motivation

One of the most accepted phenomenons in all of sports is that of home field advantage.  Home field advantage in simple terms is the likelyhood of the home team to win the game based on several different factors such as fans, and familiarity with the field.  However, recently the world has been plagued with the worst pandemic in a century, and while sports have found ways to continue, the stadiums have been empty of fans for over a year now.  Being a fan of soccer myself, I was curious to know what impact the pandemic and lack of fans has had on home field advantage, and whether or not home field advantage is still applicable even without the fans.  The dataset that I have created has the total amount of games won by the home team, the away team, or draw games that occured each season in the English Premier League for the last 13 seasons.
  
### Processing and Cleaning

To collect the data, the following sites were used...

https://en.wikipedia.org/wiki/2009%E2%80%9310_Premier_League

https://en.wikipedia.org/wiki/2010%E2%80%9311_Premier_League

https://en.wikipedia.org/wiki/2011%E2%80%9312_Premier_League

https://www.soccerstats.com/homeaway.asp?league=england

... and I created a .csv file with the total amount of home wins, away wins, and draw games that occured for each of the past 13 seasons.  No cleaning or processing was done for any season with the exception of this current 2020/21 season.  The reasoning behind this was that the current season is not quite over, as each season contains 380 games, and as of 4/23/2021 only 321 games had been played. To account for this, I did the following to the data.

As of 4/23/21, there were 120 home wins, 75 draws and 126 away wins totaling to 321/380 games played

321/380 = 1.1838

120 * 1.1838 = 142 Projected Home Wins

75 * 1.1838 = 89 Projected Draws

126 * 1.1838 = 149 Projected Away Wins

The .csv file can be found as Soccer.csv within this github repository

### Visualizations

The following libraries were used in RStudio to create the visualizations and analysis plots.

library(tidyverse)

library(RColorBrewer)

![image](https://user-images.githubusercontent.com/77804166/116005235-e9cda280-a5ba-11eb-8a95-2e8adaf7b1ba.png)

ggplot(Soccer, aes(x = Result, y = Total)) +
  geom_violin(trim = FALSE) +
  stat_summary(fun.y = mean, geom = "point", shape = 19, size = 2, color = "red") +
  stat_summary(fun.y = median, geom = "point", shape = 19, size = 2, color = "blue") + 
  xlab("Game Result") +
  ylab("Total # of Games") +
  ggtitle("Season Results of the English Premier League") +
  theme_classic()
  
To explore the data, I firstly created a violin plot to visualize the distribution density of the different game results over the last 13 years.  At first glance, it appears that the total away wins and draws have some outliers that are rather far from the normal distributions.  The home wins also appears to have a potential outlier as well, though nothing visually as dramatic as the other game result columns.  I also added in a blue dot and a red dot, for  the median and mean respectively.  Given that the mean in all three distributions is skewed more towards the potential outliers relative to the median, I decided to try to visualize the data in another way.

![image](https://user-images.githubusercontent.com/77804166/116005422-b0496700-a5bb-11eb-9179-05909091302a.png)

colors <- colorRampPalette(brewer.pal(13, "Set3"))

ggplot(Soccer, aes(x = Result, y = Total, color = Year)) +
  geom_point() +
  scale_fill_manual(values = colors) + 
  xlab("Game Result") +
  ylab("Total # of Games") +
  ggtitle("Season Results of the English Premier League") +
  theme_classic()

This plot is a scatterplot of each specific value from all 13 years analyzed.  Though the color scheme is difficult to interperet, it is apparent that both the away and home wins have an outlier, while the draw column appear to have 2 outliers.  From this point, I decided to determine whether these points are statistically significant outliers and if so, which season they occured in.

### Analysis

At this point, in order to determine which points were in fact an outlier, and which value that outlier was, I decided to create individual boxplots of each of the columns.  Furthermore, each point in the column would be evaluated to determine if it's value was more or less than 1.5 quartiles away from the median.  Any points that satisfied this would be colored purple, and labeled as an outlier.  This then allows us to pull out the outlier value to determine which year it came from.

![image](https://user-images.githubusercontent.com/77804166/116005751-31edc480-a5bd-11eb-9f72-227a89d699e1.png)

Home <- Soccer[seq(1, 36, 3), ]

Q1 <- quantile(Home$Total, 0.25)
Q3 <- quantile(Home$Total, 0.75)
IQR <- Q3-Q1
Outlier <- which(Home$Total < Q1 - (1.5 * IQR) | Home$Total > Q3 + (1.5 * IQR))
outliervalues1 <- Home$Total[Outlier]

ggplot(Home, aes(y = Total)) + 
  geom_boxplot(outlier.color = "purple", outlier.shape = 19, outlier.size = 5) +
  theme_classic() +
  ylab("Total # of Games") + 
  ggtitle("Home Wins Outliers")
  
outliervalues1

For the home wins column, one outlier was identified.  the value of the outlier was determined to be 172, which occured in the 2019/20 year of the Premier League.  This season concluded at the beginning of covid however, so it was interesting to see such a high number of home wins relative to all other seasons.  I was unable to find many good reasons why this season had such a high number of home wins, though I did come up with a hypothesis.  The team that won the Premier League that season, Liverpool, did so in historic fashion, as they broke the record for most total wins and home wins in a single season.

![image](https://user-images.githubusercontent.com/77804166/116006334-9b6ed280-a5bf-11eb-9dc9-773d6e68c921.png)

Draw <- Soccer[seq(2, 36, 3), ]

Q1 <- quantile(Draw$Total, 0.25)
Q3 <- quantile(Draw$Total, 0.75)
IQR <- Q3-Q1
Outlier <- which(Draw$Total < Q1 - (1.5 * IQR) | Draw$Total > Q3 + (1.5 * IQR))
outliervalues2 <- Draw$Total[Outlier]

ggplot(Draw, aes(y = Total)) + 
  geom_boxplot(outlier.color = "purple", outlier.shape = 19, outlier.size = 5) +
  theme_classic() +
  ylab("Total # of Games") + 
  ggtitle("Draw Outliers")

outliervalues2

For the draw column, two outliers were identified.  The values of the outliers were determined to be 92 and 89, coming from the 2019/20 and 2020/21 seasons respectively.  Given the analysis from the home wins, it is reasonable that the 2019/20 season did not have many draws, as Liverpool had a record number of wins.  However, the 2020/21 season having relatively low numbers of draws does not initially make sense, especially since there was not a outlier of home wins for the 2020/21 season.  The explanation to this finding can be seen however, when examining the away wins column.

![image](https://user-images.githubusercontent.com/77804166/116006442-2b148100-a5c0-11eb-9a84-34b47052baab.png)

Away <- Soccer[seq(3, 36, 3), ]

Q1 <- quantile(Away$Total, 0.25)
Q3 <- quantile(Away$Total, 0.75)
IQR <- Q3-Q1
Outlier <- which(Away$Total < Q1 - (1.5 * IQR) | Away$Total > Q3 + (1.5 * IQR))
outliervalues3 <- Away$Total[Outlier]

ggplot(Away, aes(y = Total)) + 
  geom_boxplot(outlier.color = "purple", outlier.shape = 19, outlier.size = 5) +
  theme_classic() +
  ylab("Total # of Games") + 
  ggtitle("Away Wins Outliers")

outliervalues3

For the away wins column, one outlier was identified.  The value of the outlier was determined to be 149, which came from the 2020/21 season.  This helps to explain why there were so few draws in the 2020/21 season, because there were an unusually large amount of away wins.  In fact, the 2020/21 season is on track to be the first Premier League season ever where there were more away wins than home wins.  The cause of this is likely the absence of fans, and the lack of homefield advantage created.  To highlight this, the home and away wins for the 2020/21 season are projected to be 142-149, a nearly 50/50 split.  This is the type of result you would expect if there were no homefield advantage which highlights how the lack of fans due to covid restrictions has impacted the games themselves.

### Conclusion

By analyzing the past 13 years of the English Premier League, and totaling up all the home wins, draws, and away wins, 4 outliers were discovered.  2 of the outliers belonged to the 2019/20 season, which had an unusually high amount of home wins and therefore an unusually small amount of draws.  This however, is likely due to the extraordinary preformance of a few clubs that year that set records for home wins.  Most relevant to the current question however, is the other 2 outliers which occured in the 2020/21 season.  This season, there were an unusually large amount of away wins, and low amount of draws as well.  In fact, without fans in the stadiums, the total away wins is nearly identical to the home wins.  This leads me to the conclusion that fans do in fact play a large role in hoem field advantage, and that without the fans, home field advantage appears to go away as well.  It will be incredibly interesting to see if home field advantage returns when the fans are able to sit in the stands again, or if soccer is just becoming more competitive and harder for the better teams to keep winning.
