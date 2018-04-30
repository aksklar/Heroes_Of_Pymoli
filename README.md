# Heroes of Pymoli Data Analysis


```python
#Import pandas
import pandas as pd
import numpy as np
```


```python
#Create json path and read json
json_path = "purchase_data.json"
purchase_df = pd.read_json(json_path)
#purchase_df.head()
```


```python
#purchase_df.dtypes
```

## Player Count


```python
#Finding the total number of players
num_players = purchase_df['SN'].count()
total_df = pd.DataFrame({"Total Players":[num_players]})
total_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>780</td>
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
#Create purchase analysis Data Frame
purchase_analysis = pd.DataFrame({"Number of Unique Items":[unique_items],
                                 "Average Price": [avg_price],
                                 "Number of Purchases":[num_purchases],
                                 "Total Revenue":[total_rev]})
purchase_analysis["Average Price"] = purchase_analysis["Average Price"].map("${:.2f}".format)
purchase_analysis["Total Revenue"] = purchase_analysis["Total Revenue"].map("${:,.2f}".format)
organized_pa = purchase_analysis[["Number of Unique Items", "Average Price", "Number of Purchases", "Total Revenue"]]
organized_pa
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
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
gender = pd.DataFrame(purchase_df["Gender"].value_counts())
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
#Create Gender Demographics Data Frame
gender_df['Percentage of Players'] = (male_percent, female_percent, other_percent)
gender_df["Percentage of Players"] = gender_df["Percentage of Players"].map("{:.2f}".format)
organized_gender_df = gender_df[["Percentage of Players", "Total Count"]]
organized_gender_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
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
      <td>633</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>17.44</td>
      <td>136</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.41</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Gender)


```python
gender_pa = purchase_df.groupby(["Gender"])
gender_pa.head()
#Purchase Count
gender_purchase_count = gender_pa["Item Name"].count()
#Average Purchase Count
gender_avg_purchase = pd.to_numeric(gender_pa["Price"].mean())
#Total Purchase Count
gender_total_purchase = gender_pa["Price"].sum()
#Normalized Totals
gender_std = gender_pa["Price"].std()
gender_norm_totals = gender_avg_purchase/gender_std
#Create Gender Purchasing Analysis Data Frame
gender_pa_results = pd.DataFrame({"Purchase Count": gender_purchase_count, "Average Purchase Price": gender_avg_purchase,
                               "Total Purchase Value": gender_total_purchase, "Normalized Totals": gender_norm_totals})
gender_pa_results["Average Purchase Price"] = gender_pa_results["Average Purchase Price"].map("${:.2f}".format)
gender_pa_results["Total Purchase Value"] = gender_pa_results["Total Purchase Value"].map("${:.2f}".format)
gender_pa_results["Normalized Totals"] = gender_pa_results["Normalized Totals"].map("${:.2f}".format)
organized_gender_pa = gender_pa_results[["Purchase Count", "Average Purchase Price", "Total Purchase Value", "Normalized Totals"]]
organized_gender_pa
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
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
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$2.45</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>$2.95</td>
      <td>$1867.68</td>
      <td>$2.66</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$3.39</td>
    </tr>
  </tbody>
</table>
</div>



## Age Demographics


```python
#Create bins of 4 years
age_bins = [5,15,25,35,50]
age_group_labels = ["5 to 15", "15 to 25", "25 to 35", "35 to 50"]
purchase_df["Age Group"] = pd.cut(purchase_df["Age"], age_bins, labels = age_group_labels)
grouped_age_df = purchase_df.groupby("Age Group")
#Purchase count
age_price_count = grouped_age_df["Price"].count()
#Average purchase price
age_avg_price = grouped_age_df["Price"].mean()
#Total purchase value
age_total_price = grouped_age_df["Price"].sum()
#Normalized Totals
age_std_price = grouped_age_df["Price"].std()
age_norm_total = age_avg_price/age_std_price
#Create Age Demographics Data Frame
age_demo = pd.DataFrame({"Purchase Count": age_price_count, "Average Purchase Price": age_avg_price, 
                       "Total Purchase Value": age_total_price, "Normalized Totals": age_norm_total})
age_demo["Average Purchase Price"] = age_demo["Average Purchase Price"].map("${:.2f}".format)
age_demo["Total Purchase Value"] = age_demo["Total Purchase Value"].map("${:,.2f}".format)
age_demo["Normalized Totals"] = age_demo["Normalized Totals"].map("${:.2f}".format)
organized_age_demo = age_demo[["Purchase Count", "Average Purchase Price", "Total Purchase Value", "Normalized Totals"]]
organized_age_demo
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
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
      <th>5 to 15</th>
      <td>110</td>
      <td>$2.92</td>
      <td>$320.77</td>
      <td>$2.61</td>
    </tr>
    <tr>
      <th>15 to 25</th>
      <td>489</td>
      <td>$2.93</td>
      <td>$1,431.35</td>
      <td>$2.60</td>
    </tr>
    <tr>
      <th>25 to 35</th>
      <td>134</td>
      <td>$2.97</td>
      <td>$398.08</td>
      <td>$2.71</td>
    </tr>
    <tr>
      <th>35 to 50</th>
      <td>47</td>
      <td>$2.90</td>
      <td>$136.13</td>
      <td>$2.67</td>
    </tr>
  </tbody>
</table>
</div>



## Top Spenders


```python
##Top Spenders: Identify top 5 spenders by total purchase value and then list(in a table)##
#SN
grouped_sn = purchase_df.groupby("SN")
grouped_sn.head()
#Purchase Count
top_spender_count = grouped_sn["Price"].count()
#Average Purchase Price
top_spender_avg = grouped_sn["Price"].mean()
#Total Purchase Value
top_spender_total = grouped_sn["Price"].sum()
#Create Top Spender Data Frame
top_spender_results = pd.DataFrame({"Purchase Count": top_spender_count, "Average Purchase Price": top_spender_avg,
                                    "Total Purchase Value": top_spender_total})
organized_ts_results = top_spender_results[["Purchase Count", "Average Purchase Price", "Total Purchase Value"]]
top_five_spenders = organized_ts_results.sort_values("Total Purchase Value", ascending = False)
top_five_spenders["Average Purchase Price"] = top_five_spenders["Average Purchase Price"].map("${:.2f}".format)
top_five_spenders["Total Purchase Value"] = top_five_spenders["Total Purchase Value"].map("${:.2f}".format)
top_five_spenders.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
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
#Item ID
#Item Name
#Purchase Total
pop_items_total = purchase_df.groupby(["Item ID", "Item Name"]).sum()["Price"]
#Purchase Price
pop_items_price = purchase_df.groupby(["Item ID", "Item Name"]).mean()["Price"]
#Purchase Count
pop_items_count = purchase_df.groupby(["Item ID", "Item Name"]).count()["Price"]
#Create Most Popular Items Data Frame
pop_items_results = pd.DataFrame({"Purchase Count": pop_items_count, "Item Price": pop_items_price,
                                  "Total Purchase Value": pop_items_total})
pop_items_results.head()
top_five_items =pop_items_results[["Purchase Count", "Item Price", "Total Purchase Value"]]
top_five_items = top_five_items.sort_values("Purchase Count", ascending = False)
top_five_items["Item Price"] = top_five_items["Item Price"].map("${:.2f}".format)
top_five_items["Total Purchase Value"] = top_five_items["Total Purchase Value"].map("${:.2f}".format)
top_five_items.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
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
#Item Id
#Item Name
#Purchase Total
profitable_items_total = purchase_df.groupby(["Item ID", "Item Name"]).sum()["Price"]
#Item Price
profitable_items_price = purchase_df.groupby(["Item ID", "Item Name"]).mean()["Price"]
#Purchase Count
profitable_items_count = purchase_df.groupby(["Item ID", "Item Name"]).count()["Price"]
#Create Most Profitable Items Data Frame
profitable_items_results = pd.DataFrame({"Purchase Count": profitable_items_count, "Item Price": profitable_items_price,
                                         "Total Purchase Value": profitable_items_total})
profitable_items_results.head()
most_profitable_items = profitable_items_results[["Purchase Count", "Item Price", "Total Purchase Value"]]
most_profitable_items = most_profitable_items.sort_values("Total Purchase Value", ascending = False)
most_profitable_items["Item Price"] = most_profitable_items["Item Price"].map("${:.2f}".format)
most_profitable_items["Total Purchase Value"] = most_profitable_items["Total Purchase Value"].map("${:.2f}".format)
most_profitable_items.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
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


