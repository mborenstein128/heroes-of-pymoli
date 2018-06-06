
# Heroes of Pymoli Data Analysis

- Of the 780 total purchases made by our 576 players to date, the majority (84%) of purchases were made by male players, compared to only 14% of total purchases that were made by female players.
- Players in the age range of 20-24 are our leading age demographic, accounting for 47% of the total purchase revenue, while 15-19 (17%) and 25-29 (12%) are the next most profitable age demographics.
- Item #178 (Oathbreaker, Last Hope of the Breaking Storm") is both the most frequently purchased item, and the most profitable.



```python
# Dependencies and Setup
import pandas as pd
import numpy as np

# Raw data file
file_to_load = "purchase_data.csv"

# Read purchasing file and store into pandas data frame
purchase_data = pd.read_csv(file_to_load)
```


```python
#Player Count
total_players=(purchase_data["SN"].unique())
player_count=len(total_players)
player_count
```




    576




```python
#Purchase Count
total_purchases=purchase_data["Purchase ID"].count()
print(total_purchases)
```

    780
    


```python
#calculations for purchasing analysis
unique_items=len(purchase_data["Item Name"].unique())
avg_price=purchase_data["Price"].mean()
total_revenue=purchase_data["Price"].sum()
```


```python
#Purchasing Analysis (Total)
summary=[("Number of Unique Items", [unique_items]), 
         ("Average Price", [avg_price]), 
         ("Number of Purchases", [total_purchases]), 
         ("Total Revenue", [total_revenue])]
df_summary=pd.DataFrame.from_items(summary)
df_summary["Average Price"]=df_summary["Average Price"].map("${:,.2f}".format)
df_summary["Total Revenue"]=df_summary["Total Revenue"].map("${:,.2f}".format)
df_summary
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
      <td>$3.05</td>
      <td>780</td>
      <td>$2,379.77</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Gender Demographics
xy=purchase_data["Gender"].value_counts()
total_gender=xy.sum()
perc_gender=xy/total_gender *100

demo_df=pd.concat([perc_gender, xy], axis=1)
demo_df.columns=["Percentage of Players", "Total Counts"]
demo_df["Percentage of Players"]=demo_df["Percentage of Players"].map("{:,.2f}%".format)
demo_df
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
      <th>Total Counts</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>83.59%</td>
      <td>652</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>14.49%</td>
      <td>113</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.92%</td>
      <td>15</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Purchasing Analysis (Gender)
grouped_df=purchase_data.groupby(["Gender"])
gender_spend=grouped_df["Price"].sum()
avg_purchase=gender_spend/xy
pag_df=pd.concat([xy, avg_purchase, gender_spend], axis=1)
pag_df.columns=["Purchase Counts", "Average Purchase Price", "Total Purchase Values"]
pag_df["Total Purchase Values"]=pag_df["Total Purchase Values"].map("${:,.2f}".format)
pag_df["Average Purchase Price"]=pag_df["Average Purchase Price"].map("${:,.2f}".format)
pag_df.index.name="Gender"
pag_df
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
      <th>Purchase Counts</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Values</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>113</td>
      <td>$3.20</td>
      <td>$361.94</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>652</td>
      <td>$3.02</td>
      <td>$1,967.64</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>15</td>
      <td>$3.35</td>
      <td>$50.19</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Age Demographics
age_bins = [0, 9.90, 14.90, 19.90, 24.90, 29.90, 34.90, 39.90, 99999]
group_names = ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"]
purchase_data["Age Group"]=pd.cut(purchase_data["Age"], age_bins, labels=group_names)
age_grouped_df=purchase_data.groupby(["Age Group"])

age_purchases=age_grouped_df["Age"].count()
age_totals=age_grouped_df["Price"].sum()
age_averages=age_totals/age_purchases
grand_total=age_totals.sum()
demo_pct=age_totals/grand_total *100
age_demo_df=pd.concat([age_purchases, age_averages, age_totals, demo_pct], axis=1)
age_demo_df.columns=["Purchase Count", "Average Purchase Price", "Total Purchase Value", "Percent of Total Value"]
age_demo_df["Average Purchase Price"]=age_demo_df["Average Purchase Price"].map("${:,.2f}".format)
age_demo_df["Total Purchase Value"]=age_demo_df["Total Purchase Value"].map("${:,.2f}".format)
age_demo_df["Percent of Total Value"]=age_demo_df["Percent of Total Value"].map("{:,.2f}%".format)
age_demo_df
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
      <th>Percent of Total Value</th>
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
      <th>&lt;10</th>
      <td>23</td>
      <td>$3.35</td>
      <td>$77.13</td>
      <td>3.24%</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>28</td>
      <td>$2.96</td>
      <td>$82.78</td>
      <td>3.48%</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>136</td>
      <td>$3.04</td>
      <td>$412.89</td>
      <td>17.35%</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>365</td>
      <td>$3.05</td>
      <td>$1,114.06</td>
      <td>46.81%</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>101</td>
      <td>$2.90</td>
      <td>$293.00</td>
      <td>12.31%</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>73</td>
      <td>$2.93</td>
      <td>$214.00</td>
      <td>8.99%</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>41</td>
      <td>$3.60</td>
      <td>$147.67</td>
      <td>6.21%</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>13</td>
      <td>$2.94</td>
      <td>$38.24</td>
      <td>1.61%</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Top Spenders
user_df=purchase_data.groupby(["SN"])
user_count=user_df["Purchase ID"].count()
user_total=user_df["Price"].sum()
user_avg=user_total/user_count
user_summary=pd.concat([user_count, user_avg, user_total], axis=1)
user_summary.columns=["Purchase Count","Average Purchase Price", "Total Purchase Value"]
user_summary=user_summary.sort_values("Total Purchase Value", ascending=False)
user_summary["Total Purchase Value"]=user_summary["Total Purchase Value"].map("${:,.2f}".format)
user_summary["Average Purchase Price"]=user_summary["Average Purchase Price"].map("${:,.2f}".format)
user_summary.head()

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
      <th>Lisosia93</th>
      <td>5</td>
      <td>$3.79</td>
      <td>$18.96</td>
    </tr>
    <tr>
      <th>Idastidru52</th>
      <td>4</td>
      <td>$3.86</td>
      <td>$15.45</td>
    </tr>
    <tr>
      <th>Chamjask73</th>
      <td>3</td>
      <td>$4.61</td>
      <td>$13.83</td>
    </tr>
    <tr>
      <th>Iral74</th>
      <td>4</td>
      <td>$3.40</td>
      <td>$13.62</td>
    </tr>
    <tr>
      <th>Iskadarya95</th>
      <td>3</td>
      <td>$4.37</td>
      <td>$13.10</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Most Popular Items
pop_items=purchase_data[["Item ID", "Item Name", "Price"]]
pop_items_grp=pop_items.groupby(["Item ID", "Item Name"])

pop_count=pop_items_grp["Item ID"].count()
pop_totals=pop_items_grp["Price"].sum()
pop_price=pop_totals/pop_count
pop_df=pd.concat([pop_count, pop_price, pop_totals], axis=1)
pop_df.columns=["Purchase Count", "Item Price", "Total Purchase Value"]
sort_df=pop_df.sort_values("Purchase Count", ascending=False)
sort_df["Item Price"]=sort_df["Item Price"].map("${:,.2f}".format)
sort_df["Total Purchase Value"]=sort_df["Total Purchase Value"].map("${:,.2f}".format)
sort_df.head()
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
      <th>178</th>
      <th>Oathbreaker, Last Hope of the Breaking Storm</th>
      <td>12</td>
      <td>$4.23</td>
      <td>$50.76</td>
    </tr>
    <tr>
      <th>145</th>
      <th>Fiery Glass Crusader</th>
      <td>9</td>
      <td>$4.58</td>
      <td>$41.22</td>
    </tr>
    <tr>
      <th>108</th>
      <th>Extraction, Quickblade Of Trembling Hands</th>
      <td>9</td>
      <td>$3.53</td>
      <td>$31.77</td>
    </tr>
    <tr>
      <th>82</th>
      <th>Nirvana</th>
      <td>9</td>
      <td>$4.90</td>
      <td>$44.10</td>
    </tr>
    <tr>
      <th>19</th>
      <th>Pursuit, Cudgel of Necromancy</th>
      <td>8</td>
      <td>$1.02</td>
      <td>$8.16</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Most Profitable Items
profit_df=pop_df.sort_values("Total Purchase Value", ascending=False)
profit_df["Item Price"]=profit_df["Item Price"].map("${:,.2f}".format)
profit_df["Total Purchase Value"]=profit_df["Total Purchase Value"].map("${:,.2f}".format)
profit_df.head()
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
      <th>178</th>
      <th>Oathbreaker, Last Hope of the Breaking Storm</th>
      <td>12</td>
      <td>$4.23</td>
      <td>$50.76</td>
    </tr>
    <tr>
      <th>82</th>
      <th>Nirvana</th>
      <td>9</td>
      <td>$4.90</td>
      <td>$44.10</td>
    </tr>
    <tr>
      <th>145</th>
      <th>Fiery Glass Crusader</th>
      <td>9</td>
      <td>$4.58</td>
      <td>$41.22</td>
    </tr>
    <tr>
      <th>92</th>
      <th>Final Critic</th>
      <td>8</td>
      <td>$4.88</td>
      <td>$39.04</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>8</td>
      <td>$4.35</td>
      <td>$34.80</td>
    </tr>
  </tbody>
</table>
</div>


