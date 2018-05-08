# Data Analysis with Python Pandas and Numpy
The premise of this project was to utilize Python's libraries (specifically Pandas and Numpy) in a real world situation and scenario.  In this project, I was tasked with creating a Python script using Pandas and Numpy in order to analyze an independent gaming company's purchasing data.


# Heroes of Pymoli


```python
# Import dependencies
import pandas as pd
import numpy as np

# Load Json file
data_json = "purchase_data.json"
# Read purchasing file and store into Pandas dataframe
purchase_df = pd.read_json(data_json, orient = "records")
```

## Player Count


```python
#Find the total number of players
player_demo = purchase_df.loc[:, ["Gender", "SN", "Age"]]
player_demo = player_demo.drop_duplicates()
num_players = player_demo.count()[0]
#Create Dataframe for total number of players
total_players = pd.DataFrame({"Total Player": [num_players]})
total_players
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Player</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Total)


```python
#Number of unique items
unique_items = len(purchase_df['Item Name'].unique())
#Average purchase price
avg_price = pd.to_numeric(purchase_df['Price'].mean())
#Total number of purchases
num_purchases = purchase_df['Price'].count()
#Total revenue
total_rev = pd.to_numeric(purchase_df['Price'].sum())
#Create Purchase Analysis Dataframe
purchase_analysis = pd.DataFrame({"Number of Unique Items":[unique_items],
                                 "Average Price": [avg_price],
                                 "Number of Purchases":[num_purchases],
                                 "Total Revenue":[total_rev]})
purchase_analysis["Average Price"] = purchase_analysis["Average Price"].map("${:.2f}".format)
purchase_analysis["Total Revenue"] = purchase_analysis["Total Revenue"].map("${:,.2f}".format)
purchase_analysis = purchase_analysis[["Number of Unique Items", "Average Price", "Number of Purchases", "Total Revenue"]]
purchase_analysis
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Unique Items</th>
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>179</td>
      <td>$2.93</td>
      <td>780</td>
      <td>$2,286.33</td>
    </tr>
  </tbody>
</table>
</div>



## Gender Demographics


```python
#Create Data Frame that shows gender value counts
gender = pd.DataFrame(player_demo["Gender"].value_counts())
#Rename gender column to total counts
gender_df = gender.rename(columns={"Gender": "Total Count"})
#Percentage and count of male players
male_count = gender_df.loc["Male", "Total Count"]
male_percent = (male_count / num_players)*100
#Percentage and count of female players
female_count = gender_df.loc["Female", "Total Count"]
female_percent = (female_count / num_players)*100 
#Percentage and count of other/non-disclosed
other_count = gender_df.loc["Other / Non-Disclosed", "Total Count"]
other_percent = (other_count / num_players)*100
#Create Gender Demographics Dataframe
gender_df['Percentage of Players'] = (male_percent, female_percent, other_percent)
gender_df["Percentage of Players"] = gender_df["Percentage of Players"].map("{:.2f}".format)
gender_df = gender_df[["Percentage of Players", "Total Count"]]
gender_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>81.15</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.40</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>



## Purchase Analysis (Gender)


```python
#Purchase Count
gender_purchase_count = purchase_df.groupby(["Gender"]).count()["Price"]
#Average Purchase Count
gender_avg_purchase = purchase_df.groupby(["Gender"]).mean()["Price"]
#Total Purchase Count
gender_total_purchase = purchase_df.groupby(["Gender"]).sum()["Price"]
#Normalized Totals
gender_norm_totals = gender_total_purchase / gender_df["Total Count"]
#Create Gender Purchasing Analysis Dataframe
gender_pa_results = pd.DataFrame({"Purchase Count": gender_purchase_count, "Average Purchase Price": gender_avg_purchase,
                               "Total Purchase Value": gender_total_purchase, "Normalized Totals": gender_norm_totals})
gender_pa_results["Average Purchase Price"] = gender_pa_results["Average Purchase Price"].map("${:.2f}".format)
gender_pa_results["Total Purchase Value"] = gender_pa_results["Total Purchase Value"].map("${:.2f}".format)
gender_pa_results["Normalized Totals"] = gender_pa_results["Normalized Totals"].map("${:.2f}".format)
gender_pa_results = gender_pa_results[["Purchase Count", "Average Purchase Price", "Total Purchase Value", "Normalized Totals"]]
gender_pa_results
```

## Age Demographics


```python
#Create bins of 4 years
age_bins = [5,15,25,35,50]
age_group_labels = ["05 to 15", "15 to 25", "25 to 35", "35 to 50"]
#Categorize existing players by using age bins
player_demo["Age Group"] = pd.cut(player_demo["Age"], age_bins, labels = age_group_labels)
#Age Demographics Totals
age_demo_totals = player_demo["Age Group"].value_counts()
#Age Demographics Percentages
age_demo_percents = age_demo_totals / num_players * 100
#Create Age Demographics Dataframe
age_demo = pd.DataFrame({"Total Count": age_demo_totals, "Percentage of Players": age_demo_percents})
age_demo = age_demo.round(2)
age_demo.sort_index()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>05 to 15</th>
      <td>13.26</td>
      <td>76</td>
    </tr>
    <tr>
      <th>15 to 25</th>
      <td>65.10</td>
      <td>373</td>
    </tr>
    <tr>
      <th>25 to 35</th>
      <td>16.75</td>
      <td>96</td>
    </tr>
    <tr>
      <th>35 to 50</th>
      <td>4.89</td>
      <td>28</td>
    </tr>
  </tbody>
</table>
</div>



## Purchase Analysis (Age)


```python
#Bin purchase_df
purchase_df["Age Group"] = pd.cut(purchase_df["Age"], age_bins, labels = age_group_labels)
#Purchase count
age_price_count = purchase_df.groupby(["Age Group"]).count()["Price"]
#Average purchase price
age_avg_price = purchase_df.groupby(["Age Group"]).mean()["Price"]
#Total purchase value
age_total_price = purchase_df.groupby(["Age Group"]).sum()["Price"]
#Normalized Totals
age_norm_total = age_total_price / age_demo["Total Count"]
#Create Age Demographics Dataframe
age_demo_pa = pd.DataFrame({"Purchase Count": age_price_count, "Average Purchase Price": age_avg_price, 
                       "Total Purchase Value": age_total_price, "Normalized Totals": age_norm_total})
age_demo_pa["Average Purchase Price"] = age_demo_pa["Average Purchase Price"].map("${:.2f}".format)
age_demo_pa["Total Purchase Value"] = age_demo_pa["Total Purchase Value"].map("${:,.2f}".format)
age_demo_pa["Normalized Totals"] = age_demo_pa["Normalized Totals"].map("${:.2f}".format)
age_demo_pa = age_demo_pa[["Purchase Count", "Average Purchase Price", "Total Purchase Value", "Normalized Totals"]]
age_demo_pa
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Age Group</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>05 to 15</th>
      <td>110</td>
      <td>$2.92</td>
      <td>$320.77</td>
      <td>$4.22</td>
    </tr>
    <tr>
      <th>15 to 25</th>
      <td>489</td>
      <td>$2.93</td>
      <td>$1,431.35</td>
      <td>$3.84</td>
    </tr>
    <tr>
      <th>25 to 35</th>
      <td>134</td>
      <td>$2.97</td>
      <td>$398.08</td>
      <td>$4.15</td>
    </tr>
    <tr>
      <th>35 to 50</th>
      <td>47</td>
      <td>$2.90</td>
      <td>$136.13</td>
      <td>$4.86</td>
    </tr>
  </tbody>
</table>
</div>



## Top Spenders


```python
##Identify top 5 spenders by total purchase value and then list(in a table)##
#Purchase Count
top_spender_count = purchase_df.groupby(["SN"]).count()["Price"]
#Average Purchase Price
top_spender_avg = purchase_df.groupby(["SN"]).mean()["Price"]
#Total Purchase Value
top_spender_total = purchase_df.groupby(["SN"]).sum()["Price"]
#Create Top Spender Dataframe
top_spender_results = pd.DataFrame({"Purchase Count": top_spender_count, "Average Purchase Price": top_spender_avg,
                                    "Total Purchase Value": top_spender_total})
top_spenders_results = top_spender_results[["Purchase Count", "Average Purchase Price", "Total Purchase Value"]]
top_spenders_results = top_spenders_results.sort_values("Total Purchase Value", ascending = False)
top_spenders_results["Average Purchase Price"] = top_spenders_results["Average Purchase Price"].map("${:.2f}".format)
top_spenders_results["Total Purchase Value"] = top_spenders_results["Total Purchase Value"].map("${:.2f}".format)
top_spenders_results.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>$3.41</td>
      <td>$17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$3.39</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$3.18</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>$4.24</td>
      <td>$12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>$3.86</td>
      <td>$11.58</td>
    </tr>
  </tbody>
</table>
</div>



## Most Popular Items


```python
##Most Popular Items: Identify the 5 most popular items by purchase count, then list (in a table)##
#Purchase Total
pop_items_total = purchase_df.groupby(["Item ID", "Item Name"]).sum()["Price"]
#Purchase Price
pop_items_price = purchase_df.groupby(["Item ID", "Item Name"]).mean()["Price"]
#Purchase Count
pop_items_count = purchase_df.groupby(["Item ID", "Item Name"]).count()["Price"]
#Create Most Popular Items Dataframe
pop_items_results = pd.DataFrame({"Purchase Count": pop_items_count, "Item Price": pop_items_price,
                                  "Total Purchase Value": pop_items_total})
pop_items_results = pop_items_results[["Purchase Count", "Item Price", "Total Purchase Value"]]
pop_items_results = pop_items_results.sort_values("Purchase Count", ascending = False)
pop_items_results["Item Price"] = pop_items_results["Item Price"].map("${:.2f}".format)
pop_items_results["Total Purchase Value"] = pop_items_results["Total Purchase Value"].map("${:.2f}".format)
pop_items_results.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>11</td>
      <td>$2.35</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>11</td>
      <td>$2.23</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>9</td>
      <td>$1.24</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>9</td>
      <td>$1.49</td>
      <td>$13.41</td>
    </tr>
  </tbody>
</table>
</div>



## Most Profitable Items


```python
##Most Profitable Items: Identify the 5 most profitable items by total purchase value, then list(in a table)##
#Purchase Total
profitable_items_total = purchase_df.groupby(["Item ID", "Item Name"]).sum()["Price"]
#Item Price
profitable_items_price = purchase_df.groupby(["Item ID", "Item Name"]).mean()["Price"]
#Purchase Count
profitable_items_count = purchase_df.groupby(["Item ID", "Item Name"]).count()["Price"]
#Create Most Profitable Items Data Frame
profitable_items_results = pd.DataFrame({"Purchase Count": profitable_items_count, "Item Price": profitable_items_price,
                                         "Total Purchase Value": profitable_items_total})
profitable_items_results = profitable_items_results[["Purchase Count", "Item Price", "Total Purchase Value"]]
profitable_items_results = profitable_items_results.sort_values("Total Purchase Value", ascending = False)
profitable_items_results["Item Price"] = profitable_items_results["Item Price"].map("${:.2f}".format)
profitable_items_results["Total Purchase Value"] = profitable_items_results["Total Purchase Value"].map("${:.2f}".format)
profitable_items_results.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>7</td>
      <td>$4.25</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>6</td>
      <td>$4.95</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>6</td>
      <td>$4.87</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>8</td>
      <td>$3.61</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>


