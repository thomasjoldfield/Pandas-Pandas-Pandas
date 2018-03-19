

```python
import pandas as pd

#ok, from the top....
fileLocation = "../Resources/HeroesofPymoli/RawData.json"
print("found file")

```

    found file
    


```python
'''
Player Count
    Total Number of Players

Purchasing Analysis (Total)
    Number of Unique Items
    Average Purchase Price
    Total Number of Purchases
    Total Revenue
    
Gender Demographics
    Percentage and Count of Male Players
    Percentage and Count of Female Players
    Percentage and Count of Other / Non-Disclosed

Purchasing Analysis (Gender)
    The below each broken by gender
    Purchase Count
    Average Purchase Price
    Total Purchase Value
    Normalized Totals

Age Demographics
    The below each broken into bins of 4 years (i.e. <10, 10-14, 15-19, etc.)
    Purchase Count
    Average Purchase Price
    Total Purchase Value
    Normalized Totals

Top Spenders
    Identify the the top 5 spenders in the game by total purchase value, then list (in a table):
    SN
    Purchase Count
    Average Purchase Price
    Total Purchase Value

Most Popular Items
    Identify the 5 most popular items by purchase count, then list (in a table):
    Item ID
    Item Name
    Purchase Count
    Item Price
    Total Purchase Value

Most Profitable Items
    Identify the 5 most profitable items by total purchase value, then list (in a table):
    Item ID
    Item Name
    Purchase Count
    Item Price
    Total Purchase Value'''
```




    '\nPlayer Count\n    Total Number of Players\n\nPurchasing Analysis (Total)\n    Number of Unique Items\n    Average Purchase Price\n    Total Number of Purchases\n    Total Revenue\n    \nGender Demographics\n    Percentage and Count of Male Players\n    Percentage and Count of Female Players\n    Percentage and Count of Other / Non-Disclosed\n\nPurchasing Analysis (Gender)\n    The below each broken by gender\n    Purchase Count\n    Average Purchase Price\n    Total Purchase Value\n    Normalized Totals\n\nAge Demographics\n    The below each broken into bins of 4 years (i.e. <10, 10-14, 15-19, etc.)\n    Purchase Count\n    Average Purchase Price\n    Total Purchase Value\n    Normalized Totals\n\nTop Spenders\n    Identify the the top 5 spenders in the game by total purchase value, then list (in a table):\n    SN\n    Purchase Count\n    Average Purchase Price\n    Total Purchase Value\n\nMost Popular Items\n    Identify the 5 most popular items by purchase count, then list (in a table):\n    Item ID\n    Item Name\n    Purchase Count\n    Item Price\n    Total Purchase Value\n\nMost Profitable Items\n    Identify the 5 most profitable items by total purchase value, then list (in a table):\n    Item ID\n    Item Name\n    Purchase Count\n    Item Price\n    Total Purchase Value'




```python
#wtf is json? Looks like a list of dictionaries, let's just roll with it

pymoli = pd.read_json(fileLocation)
print("can read it")
#pymoli.head()
```

    can read it
    


```python
print("Observations on Pymoli Data\n")
print("1. The average player spent just under $4. Our “whales” averaged over $13. While bringing in more players is important- a heavier focus should be on increasing spending among existing players."\n)
print("2. Older players, 36-43, typically spent the most as individuals, though make up a very small number of players. Focusing on bring in these older players, along with their expendable income, may have greater impact that bringing in younger players who spend less."\n)
print("3. 'Final Critic' was the most popular, but also our most expensive item available. Why? Good in game stats? Does it look frickin’ sick? Figure out what’s going on there and do more of it. That item is a gold mine.")
```


```python
#calc our player count. Actually kinda underwhelming how easy this one was....
playerCount = pymoli["SN"].nunique()
print("Pymoli has " + str(playerCount) + " players.")
```

    Pymoli has 573 players.
    


```python
'''Purchasing Analysis (Total)
    Number of Unique Items
    Average Purchase Price
    Total Number of Purchases
    Total Revenue'''

uniqueItems = pymoli["Item ID"].nunique()
print("There are " + str(uniqueItems) + " unique items.")
averagePurchasePrice = pymoli["Price"].mean()
print("The average purchase costs $" + str(averagePurchasePrice) + ".")
totalPurchases = len(pymoli)
print("There have been " + str(totalPurchases) + " total purchases.")
totalRevenue = pymoli["Price"].sum()
print("Pymoli has made a total of $" + str(totalRevenue) + ".")
```

    There are 183 unique items.
    The average purchase costs $2.931192307692303.
    There have been 780 total purchases.
    Pymoli has made a total of $2286.33.
    


```python
'''Gender Demographics
    Percentage and Count of Male Players
    Percentage and Count of Female Players
    Percentage and Count of Other / Non-Disclosed'''

uniqueUsers = pymoli.drop_duplicates("SN")
len(uniqueUsers)
genderSplit = uniqueUsers.groupby("Gender")
genderCount = genderSplit.count()
totalMale = genderCount["SN"]["Male"]
totalFemale = genderCount["SN"]["Female"]
totalNon = len(uniqueUsers)-totalMale-totalFemale

print("There are " + str(totalMale) + " male users (" + str(100*totalMale/len(uniqueUsers)) +"%)")
print("There are " + str(totalFemale) + " female users (" + str(100*totalFemale/len(uniqueUsers)) +"%)")
print("There are " + str(totalNon) + " users who defied binary classification (" + str(100*totalNon/len(uniqueUsers)) +"%)")
```

    There are 465 male users (81.15183246073299%)
    There are 100 female users (17.452006980802793%)
    There are 8 users who defied binary classification (1.3961605584642234%)
    


```python
'''Purchasing Analysis (Gender)
    The below each broken by gender
    Purchase Count
    Average Purchase Price
    Total Purchase Value
    Normalized Totals'''

genderPurchases = pymoli.groupby("Gender")
genderAnalysis = genderPurchases.count()
purchaseCountMale = genderAnalysis["Price"][("Male")]
purchaseCountFemale = genderAnalysis["Price"][("Female")]
purchaseCountNon = len(pymoli)-purchaseCountMale - purchaseCountFemale
print("\nPurchase Count Breakdown by Gender")
print("Male users made " + str(purchaseCountMale) + " purchases.")
print("Female users made " + str(purchaseCountFemale) + " purchases.")
print("Other users made " + str(purchaseCountNon) + " purchases.")

#AveragePurchasePrice
genderMean = genderPurchases["Price"].mean()
print("\nAverage Price Breakdown by Gender")
print("Purchases by male users averaged $" + str(genderMean["Male"]) + ".")
print("Purchases by female users averaged $" + str(genderMean["Female"]) + ".")
print("Purchases by other users averaged $" + str(genderMean["Other / Non-Disclosed"]) + ".")

#Total Purchase Value
print("\nTotal Purchase Breakdown by Gender")
genderSums = genderPurchases["Price"].sum()
#print(genderSums)
#print(genderSums["Male"])
#print(genderSums["Female"])
#print(genderSums["Other / Non-Disclosed"])
print("Purchases by male users totaled $" + str(genderSums["Male"]) + ".")
print("Purchases by female users totaled $" + str(genderSums["Female"]) + ".")
print("Purchases by other users totaled $" + str(genderSums["Other / Non-Disclosed"]) + ".")

#Normalized Values
print("\nNormalized Purchase Breakdown by Gender")
normMaleCalc = genderSums["Male"]/totalMale
normFemaleCalc = genderSums["Female"]/totalFemale
normNonCalc = genderSums["Other / Non-Disclosed"]/totalNon
print("Male users spent a noramlized $" + str(normMaleCalc) + ".")
print("Female users spent a normalized $" + str(normFemaleCalc) + ".")
print("Other users spent a normalized $" + str(normFemaleCalc) + ".")
```

    
    Purchase Count Breakdown by Gender
    Male users made 633 purchases.
    Female users made 136 purchases.
    Other users made 11 purchases.
    
    Average Price Breakdown by Gender
    Purchases by male users averaged $2.9505213270142154.
    Purchases by female users averaged $2.815514705882352.
    Purchases by other users averaged $3.2490909090909086.
    
    Total Purchase Breakdown by Gender
    Purchases by male users totaled $1867.6799999999985.
    Purchases by female users totaled $382.90999999999985.
    Purchases by other users totaled $35.739999999999995.
    
    Normalized Purchase Breakdown by Gender
    Male users spent a noramlized $4.016516129032254.
    Female users spent a normalized $3.8290999999999986.
    Other users spent a normalized $3.8290999999999986.
    


```python
'''Age Demographics
    The below each broken into bins of 4 years (i.e. <10, 10-14, 15-19, etc.)
    Purchase Count
    Average Purchase Price
    Total Purchase Value
    Normalized Totals'''

#Let's do some binning

bins = [0, 10, 14, 19, 23, 27, 31, 35, 39, 43, 47, 51, 55, 59, 63, 100]
binLabels = ["0-10", "11-14", "15-19", "19-23", "24-27", "28-31", "32-35", "36-39", "40-43", "44-47", "48-51", "52-55", "56-59", "60-63", "64+"]
pymoli["AgeBins"] = pd.cut(pymoli["Age"], bins, labels=binLabels)
agePurchases = pymoli.groupby("AgeBins")
agePurchases.count()

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
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
    <tr>
      <th>AgeBins</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0-10</th>
      <td>32</td>
      <td>32</td>
      <td>32</td>
      <td>32</td>
      <td>32</td>
      <td>32</td>
    </tr>
    <tr>
      <th>11-14</th>
      <td>31</td>
      <td>31</td>
      <td>31</td>
      <td>31</td>
      <td>31</td>
      <td>31</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>133</td>
      <td>133</td>
      <td>133</td>
      <td>133</td>
      <td>133</td>
    </tr>
    <tr>
      <th>19-23</th>
      <td>266</td>
      <td>266</td>
      <td>266</td>
      <td>266</td>
      <td>266</td>
      <td>266</td>
    </tr>
    <tr>
      <th>24-27</th>
      <td>169</td>
      <td>169</td>
      <td>169</td>
      <td>169</td>
      <td>169</td>
      <td>169</td>
    </tr>
    <tr>
      <th>28-31</th>
      <td>60</td>
      <td>60</td>
      <td>60</td>
      <td>60</td>
      <td>60</td>
      <td>60</td>
    </tr>
    <tr>
      <th>32-35</th>
      <td>42</td>
      <td>42</td>
      <td>42</td>
      <td>42</td>
      <td>42</td>
      <td>42</td>
    </tr>
    <tr>
      <th>36-39</th>
      <td>30</td>
      <td>30</td>
      <td>30</td>
      <td>30</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>40-43</th>
      <td>16</td>
      <td>16</td>
      <td>16</td>
      <td>16</td>
      <td>16</td>
      <td>16</td>
    </tr>
    <tr>
      <th>44-47</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>48-51</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>52-55</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>56-59</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>60-63</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>64+</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
print("\nPurchase Count Breakdown by Age")
ageCount = agePurchases["Item ID"].count()
for label in binLabels:
    print("Users in the " + str(label) + " age group made " + str(ageCount.loc[label]) + " purchases.")
print("\n")

'''    
print("\n")
ageSum = agePurchases["Price"].sum()
for label in binLabels:
    print("Users in the " + str(label) + " age group made total purchases of $" + str(ageSum.loc[label]) + ".")
    
print("\n")
ageUnique = agePurchases["SN"].nunique()
for label in binLabels:
    print("Users in the " + str(label) + " age group made normalized purchases of $" + str(ageSum.loc[label]/ageUnique.loc[label]) + ".")'''
```

    
    Purchase Count Breakdown by Age
    Users in the 0-10 age group made 32 purchases.
    Users in the 11-14 age group made 31 purchases.
    Users in the 15-19 age group made 133 purchases.
    Users in the 19-23 age group made 266 purchases.
    Users in the 24-27 age group made 169 purchases.
    Users in the 28-31 age group made 60 purchases.
    Users in the 32-35 age group made 42 purchases.
    Users in the 36-39 age group made 30 purchases.
    Users in the 40-43 age group made 16 purchases.
    Users in the 44-47 age group made 1 purchases.
    Users in the 48-51 age group made 0 purchases.
    Users in the 52-55 age group made 0 purchases.
    Users in the 56-59 age group made 0 purchases.
    Users in the 60-63 age group made 0 purchases.
    Users in the 64+ age group made 0 purchases.
    
    
    




    '    \nprint("\n")\nageSum = agePurchases["Price"].sum()\nfor label in binLabels:\n    print("Users in the " + str(label) + " age group made total purchases of $" + str(ageSum.loc[label]) + ".")\n    \nprint("\n")\nageUnique = agePurchases["SN"].nunique()\nfor label in binLabels:\n    print("Users in the " + str(label) + " age group made normalized purchases of $" + str(ageSum.loc[label]/ageUnique.loc[label]) + ".")'




```python
print("\nAverage Price Breakdown by Age")
ageMean = agePurchases["Price"].mean()
for label in binLabels:
    print("Users in the " + str(label) + " age group made average purchases of $" + str(ageMean.loc[label]) + ".")
```

    
    Average Price Breakdown by Age
    Users in the 0-10 age group made average purchases of $3.0193749999999993.
    Users in the 11-14 age group made average purchases of $2.7029032258064514.
    Users in the 15-19 age group made average purchases of $2.905413533834586.
    Users in the 19-23 age group made average purchases of $2.8771052631578953.
    Users in the 24-27 age group made average purchases of $3.0178698224852085.
    Users in the 28-31 age group made average purchases of $2.956666666666666.
    Users in the 32-35 age group made average purchases of $3.11047619047619.
    Users in the 36-39 age group made average purchases of $2.746000000000001.
    Users in the 40-43 age group made average purchases of $3.189375.
    Users in the 44-47 age group made average purchases of $2.7199999999999998.
    Users in the 48-51 age group made average purchases of $nan.
    Users in the 52-55 age group made average purchases of $nan.
    Users in the 56-59 age group made average purchases of $nan.
    Users in the 60-63 age group made average purchases of $nan.
    Users in the 64+ age group made average purchases of $nan.
    


```python
print("\n")
ageSum = agePurchases["Price"].sum()
for label in binLabels:
    print("Users in the " + str(label) + " age group made total purchases of $" + str(ageSum.loc[label]) + ".")

```

    
    
    Users in the 0-10 age group made total purchases of $96.61999999999998.
    Users in the 11-14 age group made total purchases of $83.78999999999999.
    Users in the 15-19 age group made total purchases of $386.41999999999996.
    Users in the 19-23 age group made total purchases of $765.3100000000002.
    Users in the 24-27 age group made total purchases of $510.0200000000002.
    Users in the 28-31 age group made total purchases of $177.39999999999998.
    Users in the 32-35 age group made total purchases of $130.64.
    Users in the 36-39 age group made total purchases of $82.38000000000002.
    Users in the 40-43 age group made total purchases of $51.03.
    Users in the 44-47 age group made total purchases of $2.7199999999999998.
    Users in the 48-51 age group made total purchases of $0.0.
    Users in the 52-55 age group made total purchases of $0.0.
    Users in the 56-59 age group made total purchases of $0.0.
    Users in the 60-63 age group made total purchases of $0.0.
    Users in the 64+ age group made total purchases of $0.0.
    


```python
print("\nNormalized Purchase Breakdown by Age")
ageUnique = agePurchases["SN"].nunique()
for label in binLabels:
    print("Users in the " + str(label) + " age group made normalized purchases of $" + str(ageSum.loc[label]/ageUnique.loc[label]) + ".")
```

    
    Normalized Purchase Breakdown by Age
    Users in the 0-10 age group made normalized purchases of $4.391818181818181.
    Users in the 11-14 age group made normalized purchases of $4.1895.
    Users in the 15-19 age group made normalized purchases of $3.8641999999999994.
    Users in the 19-23 age group made normalized purchases of $3.6617703349282307.
    Users in the 24-27 age group made normalized purchases of $4.215041322314051.
    Users in the 28-31 age group made normalized purchases of $4.5487179487179485.
    Users in the 32-35 age group made normalized purchases of $3.84235294117647.
    Users in the 36-39 age group made normalized purchases of $4.845882352941178.
    Users in the 40-43 age group made normalized purchases of $5.103.
    Users in the 44-47 age group made normalized purchases of $2.7199999999999998.
    Users in the 48-51 age group made normalized purchases of $nan.
    Users in the 52-55 age group made normalized purchases of $nan.
    Users in the 56-59 age group made normalized purchases of $nan.
    Users in the 60-63 age group made normalized purchases of $nan.
    Users in the 64+ age group made normalized purchases of $nan.
    

    C:\Users\thoma\Anaconda3\lib\site-packages\ipykernel_launcher.py:4: RuntimeWarning: invalid value encountered in double_scalars
      after removing the cwd from sys.path.
    


```python
'''Top Spenders
    Identify the the top 5 spenders in the game by total purchase value, then list (in a table):
    SN
    Purchase Count
    Average Purchase Price
    Total Purchase Value
    
    example sort: df.sort(['A', 'B'], ascending=[1, 0])'''

#This figures out who the top 5 spenders are
spenders = pymoli.groupby("SN")
spendersPrice = spenders.sum()
spenderPrice = spendersPrice.sort_values("Price", ascending = False)
orderedSpenders = spenderPrice.index.values
#orderedSpenders 0-4 are our big spenders. We can use them as an index elsewhere.
print("The 5 biggest spenders were " + orderedSpenders[0] + ", " + orderedSpenders[1] + ", "  + orderedSpenders[2] + ", "  + orderedSpenders[3] + ", and "  + orderedSpenders[4] + ".\n")

#we need to make a table, which will be a dictionary of lists. So we need 4 lists
#list 1
top5List = [orderedSpenders[0], orderedSpenders[1], orderedSpenders[2], orderedSpenders[3], orderedSpenders[4]]
#list 2
purchaseCountList = []
#list 3
averagePriceList = []
#list 4
totalPriceList = []

spendersIndex_df = pymoli.set_index("SN")

x=0
while x < 5:
    purchaseCountList.append((len(spendersIndex_df.loc[orderedSpenders[x]])))
    averagePriceList.append(spendersIndex_df.loc[orderedSpenders[x],"Price"].mean())
    totalPriceList.append(spendersIndex_df.loc[orderedSpenders[x],"Price"].sum())
    x=x+1
#print(purchaseCountList)
#print(averagePriceList)
#print(totalPriceList)
topSpender_df = pd.DataFrame({"SN":top5List, "Purchase Count" : purchaseCountList, "Average Spent":averagePriceList, "Total Spent":totalPriceList})
topSpender_df = topSpender_df.set_index("SN")
topSpender_df
```

    The 5 biggest spenders were Undirrala66, Saedue76, Mindimnya67, Haellysu29, and Eoda93.
    
    




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
      <th>Average Spent</th>
      <th>Purchase Count</th>
      <th>Total Spent</th>
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
      <td>3.412000</td>
      <td>5</td>
      <td>17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>3.390000</td>
      <td>4</td>
      <td>13.56</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>3.185000</td>
      <td>4</td>
      <td>12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>4.243333</td>
      <td>3</td>
      <td>12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3.860000</td>
      <td>3</td>
      <td>11.58</td>
    </tr>
  </tbody>
</table>
</div>




```python
'''Most Popular Items
    Identify the 5 most popular items by purchase count, then list (in a table):
    Item ID
    Item Name
    Purchase Count
    Item Price
    Total Purchase Value'''

#This figures out what the 5 most popular items are
indexItems = pymoli.groupby("Item Name")
popItems = indexItems.count()
popItems = popItems.sort_values("Price", ascending = False)
orderedPopItems = popItems.index.values
#orderedSpenders 0-4 are our big spenders. We can use them as an index elsewhere.
print("The 5 most popular items were were " + orderedPopItems[0] + ", " + orderedPopItems[1] + ", "  + orderedPopItems[2] + " (that's actually all one item), "  + orderedPopItems[3] + ", and "  + orderedPopItems[4] + ".\n")

#we need to make a table, which will be a dictionary of lists. So we need 5 lists
#list 1
top5ItemsList = [orderedPopItems[0], orderedPopItems[1], orderedPopItems[2], orderedPopItems[3], orderedPopItems[4]]
#list 2
itemIdList = []
#list 3
itemCountList = []
#list 4
itemPriceList = []
#list 5
itemTotalPriceList = []

popItemIndex_df = pymoli.set_index("Item Name")

x=0
while x < 5:
    specificItem_df = popItemIndex_df.loc[orderedPopItems[x],["Item ID", "Price"]]
    #print(specificItem_df)
    itemIdList.append(specificItem_df.iloc[0, 0])
    itemCountList.append(len(specificItem_df))
    itemPriceList.append(specificItem_df.iloc[0, 1])
    specificTotal = specificItem_df.sum()
    #print(specificTotal)
    itemTotalPriceList.append(specificTotal["Price"])
    #print(itemTotalPriceList)
    x=x+1
#print(itemIdList)
#print(itemCountList)
#print(itemPriceList)
popItems_df = pd.DataFrame({"Item Name":top5ItemsList, "Item ID" : itemIdList, "Purchase Count":itemCountList, "Item Price":itemPriceList, "Total Purchase Price":itemTotalPriceList})
popItems_df = popItems_df.set_index("Item Name")
popItems_df
```

    The 5 most popular items were were Final Critic, Arcane Gem, Betrayal, Whisper of Grieving Widows (that's actually all one item), Stormcaller, and Woeful Adamantite Claymore.
    
    




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
      <th>Item ID</th>
      <th>Item Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Price</th>
    </tr>
    <tr>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Final Critic</th>
      <td>92.0</td>
      <td>1.36</td>
      <td>14</td>
      <td>38.60</td>
    </tr>
    <tr>
      <th>Arcane Gem</th>
      <td>84.0</td>
      <td>2.23</td>
      <td>11</td>
      <td>24.53</td>
    </tr>
    <tr>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>39.0</td>
      <td>2.35</td>
      <td>11</td>
      <td>25.85</td>
    </tr>
    <tr>
      <th>Stormcaller</th>
      <td>30.0</td>
      <td>4.15</td>
      <td>10</td>
      <td>34.65</td>
    </tr>
    <tr>
      <th>Woeful Adamantite Claymore</th>
      <td>175.0</td>
      <td>1.24</td>
      <td>9</td>
      <td>11.16</td>
    </tr>
  </tbody>
</table>
</div>




```python
'''
Most Profitable Items
    Identify the 5 most profitable items by total purchase value, then list (in a table):
    Item ID
    Item Name
    Purchase Count
    Item Price
    Total Purchase Value'''

#This figures out what the 5 priciest items are
#indexItems = pymoli.groupby("Item Name")
priceyItems = indexItems.sum()
priceyItems = priceyItems.sort_values("Price", ascending = False)
#print(priceyItems)
orderedPriceyItems = priceyItems.index.values
#orderedPriceyItems 0-4 are our priciest items. We can use them as an index elsewhere.
print("The 5 priciest items are " + orderedPriceyItems[0] + ", " + orderedPriceyItems[1] + ", "  + orderedPriceyItems[2] + " (that's actually all one item), "  + orderedPriceyItems[3] + ", and "  + orderedPriceyItems[4] + ".\n")
#we need to make a table, which will be a dictionary of lists. So we need 5 lists
#list 1
pricey5ItemsList = [orderedPriceyItems[0], orderedPriceyItems[1], orderedPriceyItems[2], orderedPriceyItems[3], orderedPriceyItems[4]]
#list 2
priceyIdList = []
#list 3
priceyCountList = []
#list 4
priceyPriceList = []
#list 5
priceyTotalPriceList = []

priceyItemIndex_df = pymoli.set_index("Item Name")

x=0
while x < 5:
    specificItem_df = priceyItemIndex_df.loc[orderedPriceyItems[x],["Item ID", "Price"]]
    #print(specificItem_df)
    priceyIdList.append(specificItem_df.iloc[0, 0])
    priceyCountList.append(len(specificItem_df))
    priceyPriceList.append(specificItem_df.iloc[0, 1])
    specificTotal = specificItem_df.sum()
    #print(specificTotal)
    priceyTotalPriceList.append(specificTotal["Price"])
    #print(itemTotalPriceList)
    x=x+1
#print(itemIdList)
#print(itemCountList)
#print(itemPriceList)
popItems_df = pd.DataFrame({"Item Name":pricey5ItemsList, "Item ID" : priceyIdList, "Purchase Count":priceyCountList, "Item Price":priceyPriceList, "Total Purchase Price":priceyTotalPriceList})
popItems_df = popItems_df.set_index("Item Name")
popItems_df


```

    The 5 priciest items are Final Critic, Retribution Axe, Stormcaller (that's actually all one item), Spectral Diamond Doomblade, and Orenmir.
    
    




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
      <th>Item ID</th>
      <th>Item Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Price</th>
    </tr>
    <tr>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Final Critic</th>
      <td>92.0</td>
      <td>1.36</td>
      <td>14</td>
      <td>38.60</td>
    </tr>
    <tr>
      <th>Retribution Axe</th>
      <td>34.0</td>
      <td>4.14</td>
      <td>9</td>
      <td>37.26</td>
    </tr>
    <tr>
      <th>Stormcaller</th>
      <td>30.0</td>
      <td>4.15</td>
      <td>10</td>
      <td>34.65</td>
    </tr>
    <tr>
      <th>Spectral Diamond Doomblade</th>
      <td>115.0</td>
      <td>4.25</td>
      <td>7</td>
      <td>29.75</td>
    </tr>
    <tr>
      <th>Orenmir</th>
      <td>32.0</td>
      <td>4.95</td>
      <td>6</td>
      <td>29.70</td>
    </tr>
  </tbody>
</table>
</div>


