# Using Transfer Data to Predict Soccer Team Strength, Version II
Repository holding all work for this project

[Medium Post]()

![Cover](https://miro.medium.com/max/9650/0*wPuBg7ZwWs8Fpg4V)

This is a second attempt at modeling soccer team strength, after my first attempt as a student in the [Springboard Data Science track](https://www.springboard.com/). You can view the original [Medium Post here](https://medium.com/analytics-vidhya/transfer-windows-predicting-english-premier-league-club-ratings-779b37008353).

I sourced data from: 
https://github.com/ewenme/transfers
https://github.com/fivethirtyeight/data/tree/master/soccer-spi

### Modeling Soccer Team Strength, Version II
In my first attempt modeling soccer team strength, I limited my data to Premier League teams over the last five season. It was my first project, and I was nervous about getting the code exactly right. It took me ages; there was (and still is, a bit) a good deal of imposter syndrome at play - I didn't want to think of myself as a data scientist, because I just wasn't good enough. I'm still not using that description for myself - I'm a data **something**, but I know I'm still new.

For version II of this project, I committed to going back to my original work and expanding my horizons. I did the following things:
 - I used a larger data set, encompassing nine professional leagues from across Europe going over five seasons. This increased my final dataset from 100 rows to over 700 rows.
 - I used FuzzyWuzzy to standardized team & league names. This is a meaningful improvement, in my opinion, and I'll explain more below.
 - While I explored many of the same algorithms I used in the first version, I used [Yellowbrick](https://www.scikit-yb.org/en/latest/) to get a better understanding of what the algorithms were showing.
 - I used SHAP values to expalin the impact of the dataset features on the model, and determine areas of focus for teams at all levels to consider when developing their transfer strategy.

Something I'd like to quickly note: my conclusions aren't exactly revolutionary. They are probably already part and parcel of every top club's strategy. For those clubs who are trying to improve their strategy, or trying to understand where they are going wrong in their transfer windows each season, there might be a nugget or two of useful analysis here.


### Using FuzzyWuzzy to Standardize Team Names
I was dreading this part of the project when adding the other leagues to the dataset. I had two datasets, each with different naming conventions for the teams, but in this version of the project, I had nine leagues' worth of names, instead of just the Premier League. In Version I, I took each dataset individually, created a dictionary of team names from their unique values, and added a new column to the dataset with a shortened 'acronym' of the name that I used across both datasets.

Even typing out the explanation is more work that it should be.

In Version II, I used the FuzzyWuzzy library to standardize the names. I still worked with each dataset individually, but using this new-to-me library made the job significantly easier than creating individual dictionaries for each league for each dataset (or two gigantic dictionaries).

For those that are new to FuzzyWuzzy [(documentation)](https://pypi.org/project/fuzzywuzzy/) - it allows us to take two strings and match them, providing a similarity score.

For example:
```python3
Str_A = 'FuzzyWuzzy solves problems!'
Str_B = 'fuzzy wuzzy solves PROBLEMS.'

ratio = fuzz.ratio(Str_A.lower(), Str_B.lower())
print('Similarity score: {}'.format(ratio))```

This will return a similarity score of 95.
