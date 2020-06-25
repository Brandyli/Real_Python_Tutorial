# NBA_Analysis_Using_the_Pandas_Python_Library.ipynb

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
## Querying NBA Dataset
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
## Grouping and Aggregating Your Data
```
#grouping
nba.groupby("fran_id", sort=True)["pts"].sum()
```
#### Take a look at the Golden State Warriors’ 2014-15 season (year_id: 2015). How many wins and losses did they score during the regular season and the playoffs?
```
nba[
     (nba["fran_id"]=="Warriors") &
     (nba["year_id"]== 2015)
     ].groupby(["is_playoffs","game_result"])["game_id"].count()
```
## Manipulating Columns
```
#Create a copy of your original DataFrame to work with:
copy = nba.copy()
copy.shape
# You can define new columns based on the existing ones:
copy["differnce"]=copy.pts-copy.opp_pts
copy.shape
copy["differnce"].max()
```
#### How to rename column name?
```
rename = copy.rename(
    columns = {"game_result":"result","game_location":"location"}
)
rename.info()
```
#### How to delete useless columns?
```
elo_columns=["elo_i", "elo_n", "opp_elo_i", "opp_elo_n"]
copy.drop(elo_columns, inplace = True, axis=1)
copy.shape
```
#### Data Types Conversion
```
#Here, you use .to_datetime() to specify all game dates as datetime objects.
copy["date_game"] = pd.to_datetime(copy["date_game"])
# Categorical are a Pandas data type. A string variable consisting of only a few different values. Converting such a string variable to a categorical variable will save some memory. 
copy["game_location"]=pd.Categorical(copy["game_location"])
#Pandas dataframe.nunique() function return Series with number of distinct observations over requested axis

copy["game_result"].value_counts()
copy["game_result"]=pd.Categorical(copy["game_result"])
```
## Cleaning Data
#### Missing Values
```
#Have you ever wondered why .info() shows how many non-null values a column contains? The reason why is that this is vital information. Null values often indicate a problem in the data-gathering process
copy.info()
row_without_missing_data = copy.dropna()
#You can also drop problematic columns if they’re not relevant for your analysis. To do this, use .dropna() again and provide the axis=1 parameter:
column_without_missing_data = row_without_missing_data.dropna(axis=1)
column_without_missing_data.shape
```
#### Fill In Missing Values
```
#If there’s a meaningful default value for your use case, then you can also replace the missing values with that:

default_notes = nba.copy()
default_notes["notes"].fillna(
    value="no notes", 
    inplace = True
)
default_notes["notes"].describe()
```
#### Invalid Values
```
copy.describe()
#What about pts? How can the minimum be 0? Let’s have a look at those games:
copy[copy["pts"]==0]
```
#### Inconsistent Values
```
#In the NBA dataset, the values of the fields pts, opp_pts and game_result should be consistent with each other. You can check this using the .empty attribute:
copy[
    (copy["pts"] > copy["opp_pts"]) &
    (copy["game_result"]!= 'W')].empty
copy[
    (copy["pts"] < copy["opp_pts"]) &
    (copy["game_result"]!= 'L')].empty
```
## Combining Multiple Datasets
```
%matplotlib inline
```
#### Visualizing Your Pandas DataFrame
#### How many points the Knicks scored throughout the seasons?
```
copy[copy["fran_id"]=="Knicks"].groupby("year_id")["pts"].sum().plot()
```
![image](https://user-images.githubusercontent.com/46945617/85738346-f6991580-b6cd-11ea-9b8d-03577265049c.png)
```
copy["fran_id"].value_counts().head(20).plot(kind="bar")
```
![image](https://user-images.githubusercontent.com/46945617/85738352-f7ca4280-b6cd-11ea-9528-89741e8ade2e.png)
#### In 2013, the Miami Heat won the championship. Create a pie plot showing the count of their wins and losses during that season?
```
copy[
     (copy["year_id"]==2013) &
     (copy["fran_id"]=="Heat")
     ]["game_result"].value_counts().plot(kind="pie")
```
![image](https://user-images.githubusercontent.com/46945617/85738362-fa2c9c80-b6cd-11ea-976a-ef94b4fab03a.png)

