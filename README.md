# Using Transfer Data to Predict Soccer Team Strength, Version II
Repository holding all work for this project

[Medium Post]()

![Cover](https://miro.medium.com/max/9650/0*wPuBg7ZwWs8Fpg4V)

This is a second attempt at modeling soccer team strength, after my first attempt as a student in the [Springboard Data Science track](https://www.springboard.com/). You can view the original [Medium Post here](https://medium.com/analytics-vidhya/transfer-windows-predicting-english-premier-league-club-ratings-779b37008353).

I sourced data from: 
https://github.com/ewenme/transfers
https://github.com/fivethirtyeight/data/tree/master/soccer-spi

## Modeling Soccer Team Strength, Version II
In my first attempt modeling soccer team strength, I limited my data to Premier League teams over the last five season. It was my first project, and I was nervous about getting the code exactly right. It took me ages; there was (and still is, a bit) a good deal of imposter syndrome at play - I didn't want to think of myself as a data scientist, because I just wasn't good enough. I'm still not using that description for myself - I'm a data **something**, but I know I'm still new.

For version II of this project, I committed to going back to my original work and expanding my horizons. I did the following things:
 - I used a larger data set, encompassing nine professional leagues from across Europe going over five seasons. This increased my final dataset from 100 rows to over 700 rows.
 - I used FuzzyWuzzy to standardized team & league names. This is a meaningful improvement, in my opinion, and I'll explain more below.
 - While I explored many of the same algorithms I used in the first version, I used [Yellowbrick](https://www.scikit-yb.org/en/latest/) to get a better understanding of what the algorithms were showing.
 - I hypertuned the parameters of the best Random Forest Regressor and improved my R<sup>2</sup> score by 50% from .41 to .65
 - I used SHAP values to expalin the impact of the dataset features on the model, and determine areas of focus for teams at all levels to consider when developing their transfer strategy.

Something I'd like to quickly note: my conclusions aren't exactly revolutionary. They are probably already part and parcel of every top club's strategy. For those clubs who are trying to improve their strategy, or trying to understand where they are going wrong in their transfer windows each season, there might be a nugget or two of useful analysis here.


## Using FuzzyWuzzy to Standardize Team Names
I was dreading this part of the project when adding the other leagues to the dataset. I had two datasets, each with different naming conventions for the teams, but in this version of the project, I had nine leagues' worth of names, instead of just the Premier League. In Version I, I took each dataset individually, created a dictionary of team names from their unique values, and added a new column to the dataset with a shortened 'acronym' of the name that I used across both datasets.

Even typing out the explanation is more work that it should be.

In Version II, I used the FuzzyWuzzy library to standardize the names. I still worked with each dataset individually, but using this new-to-me library made the job significantly easier than creating individual dictionaries for each league for each dataset (or two gigantic dictionaries).

For those that are new to FuzzyWuzzy [(documentation)](https://pypi.org/project/fuzzywuzzy/) - it allows us to take two strings and match them, providing a similarity score.

For example:
```python3
Str_A = 'FuzzyWuzzy solves problems!'
Str_B = 'fuzzy wuzzy solves PROBLEMS.'

ratio = fuzz.ratio(Str_A.lower(), Str_B.lower())
print('Similarity score: {}'.format(ratio))
```
This will return a similarity score of 95.

Aside from using the `ratio()` function, FuzzyWuzzy uses functions to teokenize strings and manipulate them before running `ratio()`. [Using FuzzyWuzzy](https://github.com/tdraths/spi_transfers_global/blob/main/notebooks/full_data/Fuzzy_Wuzzy_team_names.ipynb), I created a function that compares the team names from each of my datasets, using one of them as the base name and the other as a comparison, and standardizes them based on a ratio score. It returns a dictionary that I used to replace the team names in both datasets, and was ridiculously faster than my Verion I solution to this problem!

## Using YellowBrick to visualize the algorithms
I have custom functions for visualizing data in the EDA step of each project, but I really wanted to see what the model is telling me about testing algorithms. YellowBrick has a seemingly unending series of visualizations available to the user, and I recommend checking out the project's [documentation](https://www.scikit-yb.org/en/latest/).

I kept things pretty simple and used a residuals plot and a prediction error plot to visualize each algorithm. 

![RandomForest - Yellowbrick](https://github.com/tdraths/spi_transfers_global/blob/main/Screenshot%202021-08-10%2011.45.25%20PM.png)


## Shapley Values for Explaining Predictability
I have used Shapley values before, and the [SHAP project](https://shap.readthedocs.io/en/latest/index.html#) is one of the most interesting I have explored. The good folks building out the `shap` library are brilliant, and they have developed code that uses game theory concepts to explain feature importances in a clear, meaningful way.

A few examples to show you what the shap library can do.

First, have a look at the beeswarm plot that shows the impact of the value of each feature on the model. In my project, the most impactful feature is `average_fee_spend`, the average amount a soccer team spends on transfers into the club each transfer window. What the beeswarm shows is that while the average fee spent is highly impactful, it is most positively impactful, improving a team's strength, when they have a very high average fee spent.

![SHAP-Beeswarm](https://github.com/tdraths/spi_transfers_global/blob/main/Screenshot%202021-08-10%2011.53.17%20PM.png)

**Interestingly**, if you look a few features down in the chart at `total_transfers_in` and `total_transfer_out`, you'll see that high values for those features have a more negative impact on the team's strength. Lots of transfer activity, in or out, isn't good for a club. Focusing their efforts on smaller numbers of players in and out each season has a more beneficial impact on their season strength. I can't think of a chart that shows this more clearly, and for someone who is still pretty new to machine learning and predictive analysis, the `shap` beeswarm plot is amazingly helpful.

 Here are two really cool shap visualizations that show the impact of each feature's value on a specific team's strength.
 
![SHAP-Beeswarm](https://github.com/tdraths/spi_transfers_global/blob/main/Screenshot%202021-08-11%2012.12.13%20AM.png)

**To understand this waterfall**, first have a look at the bottom right corner. That `E[f(x)]` is the baseline strength score for the dataset, according to the final RandomForest model. On the left side of the chart, you see the values of each feature for this specific team during a specific season. In this case, the extremely low average fee the club spent on transfers had a large negative impact of nearly ten points from the baseline strength score. They also didn't spend very much on any one player, and that had another large negative impact on their strength.

**Here's a stronger team.**

![SHAP-Beeswarm](https://github.com/tdraths/spi_transfers_global/blob/main/Screenshot%202021-08-11%2012.11.39%20AM.png)

It's easy to see the positive impact that spending a lot on average and a lot on a single player has on a team's overall strenght. Those two feature values alone improved this team's strength by over 17 points!


## Conclusions
 - First, reworking a previous project is pretty fun, especially when trying out new libraries, new algorithms and new techniques.
 - Second, teams can improve their strength each season by spending more on average and spending more on an individual player.
 - Third, in general, while spending money has a large increase on a team's strength, so does bringing money in by trading players away. If a club can't spend a lot, they should try to earn a lot. This is largely how Southampton has stayed in the Premier League.
 - Fourth, if I take another crack at this project (I will, because I love it), I will select out some of the less impactful features and see if I can remove some of the noise.
 

If you made it this far, thank you for reading. Check out the [entire repository](https://github.com/tdraths/spi_transfers_global), and have a look at [my website](https://tdraths.github.io/) for other projects I've worked on. Give me a shout if you have anything to add to this analysis, or notice any errors I've made. And if you own a soccer team, get ready to spend big on the summer transfer window!
[Tom's Github](https://github.com/tdraths)

