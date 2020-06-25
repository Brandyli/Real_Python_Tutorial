# Real_Python_Tutorial

In this tutorial, you will learn how to start exploring a dataset with the Pandas Python library. 
You can access specific rows and columns to tame even the largest of datasets. 
You will employ multiple techniques to prepare and clean your data, by specifying the data type of columns, 
dealing with missing values, and more. You will create queries, aggregations, and plots based on those.

Now you can:

* Work with Series and DataFrame objects
* Subset your data with .loc, .iloc, and the indexing operator
* Answer questions with queries, grouping, and aggregation
* Handle missing, invalid, and inconsistent data
* Visualize your dataset in a Jupyter notebook

# NBA_Analysis_Using_the_Pandas_Python_Library.ipynb
## Exploring Your Dataset
```
nba["team_id"].value_counts()
nba.loc[nba["fran_id"]=="Lakers", "team_id"].value_counts()
nba.loc[nba["team_id"]=="MNL", "date_game"].min()
nba.loc[nba["team_id"]=="MNL","date_game"].max()
nba.loc[nba["team_id"] == "MNL", "date_game"].agg(("min","max"))
```
How many points the Boston Celtics have scored during all matches contained in this dataset?
```
nba.loc[nba["fran_id"]=="Celtics", "pts"].sum()
nba.loc[nba["fran_id"]=="Celtics", "pts"].agg(sum)
```
## Querying Your Dataset
```
nba.head()
nba.shape

after_2010 = nba[nba["year_id"]>2010]
after_2010.shape

ame_with_notes = nba[nba["notes"].notnull()]
game_with_notes.shape

game_with_notes2 = nba[nba["notes"].notna()]
game_with_notes2.shape
# You use .str.endswith() to filter your dataset and find all games where the home team’s name ends with "ers".
fran_end_with_ers = nba[nba["fran_id"].str.endswith("ers")]
fran_end_with_ers.shape
```
#### How many Baltimore games where both teams scored over 100 points?
```
nba[(nba["_iscopy"]==0) & (nba["pts"] > 100) & (nba["opp_pts"] > 100) & (nba["team_id"]=="BLB")]
```
#### What are teams from Los Angeles had to play a home game at another court In the spring of 1992? Both teams have an ID starting with "LA".
```
nba[(nba["_iscopy"]==0) & (nba["pts"] > 100) & (nba["opp_pts"] > 100) & (nba["team_id"]=="BLB")]

nba[
    (nba["_iscopy"]==0) &
    (nba["team_id"].str.startswith("LA")) &
    (nba["year_id"] == 1992) &
    (nba["notes"].notna())
    ]
```
