
# Hero of Pymoli Data Analysis
* Male players on average spend more money in game than female players
* Player in the age group between 20 and 24 spends the most money in game; this may be because they have the most spending power out of the age groups who video gaming company targets.
* Player in the age group between 0 and 9 spend the most per player on average.


```python
import pandas as pd
import numpy as np
import os
import json

jsonpath = os.path.join("purchase_data.json")
pyhero_df = pd.read_json(jsonpath)
pyhero_df.head()
```


```python
totalPlayers = pyhero_df["SN"].nunique()
totalItem = pyhero_df["Item Name"].nunique()
avgPrice = pyhero_df["Price"].mean()
totalRevenue = pyhero_df["Price"].sum()
totalPurchaseCt = pyhero_df["Price"].count()
print('Total Number of players ' + str(totalPlayers))
print('Total Number of Items ' + str(totalItem))
print('Average Purchase Price $'+ str(avgPrice))
print('Total Revenue $'+str(totalRevenue))
print('Total Number of Purchases '+str(totalPurchaseCt ))
```

    Total Number of players 573
    Total Number of Items 179
    Average Purchase Price $2.931192307692303
    Total Revenue $2286.33
    Total Number of Purchases 780
    


```python
#Gender Demographic
#Creating table
genderDemographic_df = pd.DataFrame(pyhero_df.groupby(['SN','Gender']).count())
genderDemographic_df = genderDemographic_df.reset_index()
genderDemographic_df = pd.DataFrame(genderDemographic_df.groupby(["Gender"]).count())
total_row = genderDemographic_df['SN'].sum()
genderDemographic_df['Percentage'] = genderDemographic_df['Price']/total_row*100
genderDemographic_df = pd.DataFrame(genderDemographic_df,columns=['SN','Percentage'])
genderDemographic_df.columns = ['Total Number of Players','Percentage']
genderDemographic_df
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
      <th>Total Number of Players</th>
      <th>Percentage</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>100</td>
      <td>17.452007</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>465</td>
      <td>81.151832</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>8</td>
      <td>1.396161</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Purchasing Analysis Gender
genderDemographic_total = pd.DataFrame(pyhero_df.groupby(['Gender']).sum())
genderDemographic_avg = pd.DataFrame(pyhero_df.groupby(['Gender']).mean())
genderDemographic_ct = pd.DataFrame(pyhero_df.groupby(['Gender']).count())
genderDemographic_total['Normalized Total'] = (genderDemographic_total['Price'] - genderDemographic_total['Price'].min())/(genderDemographic_total['Price'].max()-genderDemographic_total['Price'].min())
genderDemographic_total = genderDemographic_total.reset_index()
genderDemographic_avg = genderDemographic_avg.reset_index()
genderDemographic_ct = genderDemographic_ct.reset_index()
#Merging and compiling table for purchasing analysis by Gender
merge_table = pd.merge(genderDemographic_total,genderDemographic_avg, on = "Gender", how = "left")
merge_table = pd.merge(merge_table,genderDemographic_ct, on = 'Gender',how = "left")
GenderAnalysis_df = pd.DataFrame(merge_table, columns = ['Gender','Price_x','Price_y','Price','Normalized Total'])
GenderAnalysis_df = GenderAnalysis_df.set_index('Gender')
GenderAnalysis_df.columns = ['Total Purchase Value','Average Purchase Value','Total Purchase Count','Normalized Total']
GenderAnalysis_df.head()

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
      <th>Total Purchase Value</th>
      <th>Average Purchase Value</th>
      <th>Total Purchase Count</th>
      <th>Normalized Total</th>
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
      <td>382.91</td>
      <td>2.815515</td>
      <td>136</td>
      <td>0.189509</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>1867.68</td>
      <td>2.950521</td>
      <td>633</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>35.74</td>
      <td>3.249091</td>
      <td>11</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Age Demographics
#Creating Bins
bins=[0,10,15,20,25,30,35,40,46]
group_names = ['0 to 9 ','10 to 14', '15 to 19','20 to 24','25 to 29','30 to 34','35 to 39','40 to 45']
pyhero_df["Age Group"] = pd.cut(pyhero_df["Age"],bins,labels = group_names)
#Creating Tables for Age Group
age_total = pd.DataFrame(pyhero_df.groupby(['Age Group']).sum())
age_avg = pd.DataFrame(pyhero_df.groupby(['Age Group']).mean())
age_ct = pd.DataFrame(pyhero_df.groupby(['Age Group']).count())
age_total['Normalized Total'] = (age_total['Price'] - age_total['Price'].min())/(age_total['Price'].max()-age_total['Price'].min())
age_total = age_total.reset_index()
age_avg = age_avg.reset_index()
age_ct = age_ct.reset_index()
merge_table = pd.merge(age_total,age_avg, on = "Age Group", how = "left")
merge_table = pd.merge(merge_table,age_ct, on = "Age Group",how = "left")
AgeAnalysis_df = pd.DataFrame(merge_table, columns = ['Age Group','Price','Price_y','Price_x','Normalized Total'])
AgeAnalysis_df = AgeAnalysis_df.set_index('Age Group')
AgeAnalysis_df.columns = ['Purchase Count','Average Purchase Price','Total Purchase Value','Normalized Total']
AgeAnalysis_df.head()
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
      <th>Normalized Total</th>
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
      <th>0 to 9</th>
      <td>32</td>
      <td>3.019375</td>
      <td>96.62</td>
      <td>0.098415</td>
    </tr>
    <tr>
      <th>10 to 14</th>
      <td>78</td>
      <td>2.873718</td>
      <td>224.15</td>
      <td>0.241071</td>
    </tr>
    <tr>
      <th>15 to 19</th>
      <td>184</td>
      <td>2.873587</td>
      <td>528.74</td>
      <td>0.581787</td>
    </tr>
    <tr>
      <th>20 to 24</th>
      <td>305</td>
      <td>2.959377</td>
      <td>902.61</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>25 to 29</th>
      <td>76</td>
      <td>2.892368</td>
      <td>219.82</td>
      <td>0.236227</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Purchasing Analysis by SN
SN_total = pd.DataFrame(pyhero_df.groupby(['SN']).sum())
SN_avg = pd.DataFrame(pyhero_df.groupby(['SN']).mean())
SN_count = pd.DataFrame(pyhero_df.groupby(['SN']).count())
SN_total['Normalized Total'] = (SN_total['Price'] - SN_total['Price'].min())/(SN_total['Price'].max()-SN_total['Price'].min())
SN_total = SN_total.reset_index()
SN_avg = SN_avg.reset_index()
SN_count = SN_count.reset_index()
merge_SN = pd.merge(SN_total,SN_avg,on = 'SN', how = 'left')
merge_SN = pd.merge(merge_SN,SN_count, on = 'SN',how = 'left')
merge_SN = pd.DataFrame(merge_SN, columns = ["Price_x","Price_y","Price","Normalized Total"])
merge_SN.columns =  ['Total Purchase Value','Average Purchase Value','Total Purchase Count','Normalized Total']
merge_SN = merge_SN.sort_values(['Total Purchase Count'],ascending = False)
merge_SN.head()
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
      <th>Total Purchase Value</th>
      <th>Average Purchase Value</th>
      <th>Total Purchase Count</th>
      <th>Normalized Total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>538</th>
      <td>17.06</td>
      <td>3.4120</td>
      <td>5</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>354</th>
      <td>12.74</td>
      <td>3.1850</td>
      <td>4</td>
      <td>0.730505</td>
    </tr>
    <tr>
      <th>385</th>
      <td>9.97</td>
      <td>2.4925</td>
      <td>4</td>
      <td>0.557704</td>
    </tr>
    <tr>
      <th>428</th>
      <td>13.56</td>
      <td>3.3900</td>
      <td>4</td>
      <td>0.781659</td>
    </tr>
    <tr>
      <th>467</th>
      <td>10.24</td>
      <td>2.5600</td>
      <td>4</td>
      <td>0.574548</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Purchasing Analysis by item id AND name
Item_total = pd.DataFrame(pyhero_df.groupby(['Item Name','Item ID']).sum())
Item_avg = pd.DataFrame(pyhero_df.groupby(['Item Name','Item ID']).mean())
Item_count = pd.DataFrame(pyhero_df.groupby(['Item Name','Item ID']).count())
Item_total['Normalized Total'] = (Item_total['Price'] - Item_total['Price'].min())/(Item_total['Price'].max()-Item_total['Price'].min())
Item_total = Item_total.reset_index()
Item_avg = Item_avg.reset_index()
Item_count = Item_count.reset_index()
merge_Item = pd.merge(Item_total,Item_avg,on = 'Item Name', how = 'left')
merge_Item = pd.merge(merge_Item,Item_count, on = 'Item Name',how = 'left')
merge_Item = pd.DataFrame(merge_Item, columns = ["Item Name","Item ID_x","Price_x","Price_y","Price","Normalized Total"])
merge_Item.columns =  ['Item Name','Item ID','Total Purchase Value','Average Purchase Value','Total Purchase Count','Normalized Total']
merge_Item = merge_Item.sort_values(['Total Purchase Value'],ascending = False)
merge_Item.head()
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
      <th>Item Name</th>
      <th>Item ID</th>
      <th>Total Purchase Value</th>
      <th>Average Purchase Value</th>
      <th>Total Purchase Count</th>
      <th>Normalized Total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>133</th>
      <td>Retribution Axe</td>
      <td>34</td>
      <td>37.26</td>
      <td>4.14</td>
      <td>9</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>153</th>
      <td>Spectral Diamond Doomblade</td>
      <td>115</td>
      <td>29.75</td>
      <td>4.25</td>
      <td>7</td>
      <td>0.791967</td>
    </tr>
    <tr>
      <th>110</th>
      <td>Orenmir</td>
      <td>32</td>
      <td>29.70</td>
      <td>4.95</td>
      <td>6</td>
      <td>0.790582</td>
    </tr>
    <tr>
      <th>145</th>
      <td>Singed Scalpel</td>
      <td>103</td>
      <td>29.22</td>
      <td>4.87</td>
      <td>6</td>
      <td>0.777285</td>
    </tr>
    <tr>
      <th>155</th>
      <td>Splitter, Foe Of Subtlety</td>
      <td>107</td>
      <td>28.88</td>
      <td>3.61</td>
      <td>8</td>
      <td>0.767867</td>
    </tr>
  </tbody>
</table>
</div>




```python
merge_Item_Ct = merge_Item.sort_values(['Total Purchase Count'],ascending = False)
merge_Item_Ct.head()
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
      <th>Item Name</th>
      <th>Item ID</th>
      <th>Total Purchase Value</th>
      <th>Average Purchase Value</th>
      <th>Total Purchase Count</th>
      <th>Normalized Total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>11</th>
      <td>Betrayal, Whisper of Grieving Widows</td>
      <td>39</td>
      <td>25.85</td>
      <td>2.35</td>
      <td>11</td>
      <td>0.683934</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Arcane Gem</td>
      <td>84</td>
      <td>24.53</td>
      <td>2.23</td>
      <td>11</td>
      <td>0.647368</td>
    </tr>
    <tr>
      <th>133</th>
      <td>Retribution Axe</td>
      <td>34</td>
      <td>37.26</td>
      <td>4.14</td>
      <td>9</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>140</th>
      <td>Serenity</td>
      <td>13</td>
      <td>13.41</td>
      <td>1.49</td>
      <td>9</td>
      <td>0.339335</td>
    </tr>
    <tr>
      <th>183</th>
      <td>Trickster</td>
      <td>31</td>
      <td>18.63</td>
      <td>2.07</td>
      <td>9</td>
      <td>0.483934</td>
    </tr>
  </tbody>
</table>
</div>


