# Ecom_Data_Analysis

## Description:
The e-commerce dataset from Amazon.in contains approximately 128,976 rows and 19 columns. The columns are described as follows::
 0   index               : The index of each row in the dataset
 1   Order ID            : Unique identifier for each order.
 2   Date                : Date when the order was placed. 
 3   Status              : Current status of the order (e.g., Pending, Shipped, Delivered). 
 4   Fulfilment          : Indicates if the order was fulfilled by Amazon or the merchant.
 5   Sales Channel       : Specifies whether the order was from Amazon or a third-party seller (No-Amazon). 
 6   ship-service-level  : Indicates the shipping method selected (Standard or Expedited). 
 7   Category            : Product category or type of items in the order (e.g., Electronics, Books). 
 8   Size                : Size of the product(s) ordered (e.g., Small, Medium, Large). 
 9   Courier Status      : Current shipping status of the order (e.g., Shipped, Unshipped, On the Way). 
 10  Qty                 : Quantity of items ordered in the particular order.  
 11  currency            : Currency used for the transaction.
 12  Amount              : Total monetary value of the order.
 13  ship-city           : The city where the order will be shipped to. 
 14  ship-state          : The state or region where the order will be shipped to. 
 15  ship-postal-code    : Postal code of the shipping address.
 16  ship-country        : The country where the order will be shipped to.
 17  B2B                 : Indicates if the order is a Business-to-Business (B2B) transaction (Yes/No).  
 18  fulfilled-by        : The method of order fulfillment (e.g., Easy Ship, Merchant Fulfilled, etc.).

## Data Cleaning:
```python
amz.info()
```
output:
 -   Column              Non-Null Count   Dtype  
---  ------              --------------   -----  
 0   index               128976 non-null  int64  
 1   Order ID            128976 non-null  object 
 2   Date                128976 non-null  object 
 3   Status              128976 non-null  object 
 4   Fulfilment          128976 non-null  object 
 5   Sales Channel       128976 non-null  object 
 6   ship-service-level  128976 non-null  object 
 7   Category            128976 non-null  object 
 8   Size                128976 non-null  object 
 9   Courier Status      128976 non-null  object 
 10  Qty                 128976 non-null  int64  
 11  currency            121176 non-null  object 
 12  Amount              121176 non-null  float64
 13  ship-city           128941 non-null  object 
 14  ship-state          128941 non-null  object 
 15  ship-postal-code    128941 non-null  float64
 16  ship-country        128941 non-null  object 
 17  B2B                 128976 non-null  bool   
 18  fulfilled-by        39263 non-null   object

 As we can see the datatypes of Date, ship-postal-code, etc. are incorrect. Also, data contains Null values.

### Duplicate
Displaying the duplicate rows:
```python
amz[amz.duplicated()]
```

Finding the total number of duplicate rows:
```python
amz.duplicated().sum()
```
There are 168 duplicate rows in dataset.

Removing duplicates:
```python
display(amz.drop_duplicates(keep = False, inplace = True))
```

### Null Values
Finding null values:
```python
amz.isna().sum()
```
output:
index                     0
Order ID                  0
Date                      0
Status                    0
Fulfilment                0
Sales Channel             0
ship-service-level        0
Category                  0
Size                      0
Courier Status            0
Qty                       0
currency               7778
Amount                 7778
ship-city                31
ship-state               31
ship-postal-code         31
ship-country             31
B2B                       0
fulfilled-by          89477

Since, ship-city, ship-state, ship-postal-code, ship-country has only 31 balnks, removing it.
```python
amz.dropna(subset = 'ship-city',inplace=True)
```
Since, ship-city, ship-state, ship-postal-code, ship-country is a geographical data, deleting ship-city also delete corresponding ship-state, ship-postal-code, ship-country.

Finding unique values of currency column: 
```python
pd.unique(amz['currency'])
```
array(['INR', nan], dtype=object)

Replacing Null with INR values:
```python
amz.fillna({'currency':'INR'}, inplace = True)
```

Finding unique values of fulfilled-by column: 
```python
pd.unique(amz['currency'])
```
array(['Easy Ship', nan], dtype=object)

nan are replaced by Self ship:
```python
amz.fillna({'fulfilled-by':'Self Ship'}, inplace = True)
```
In India, there are 28 States and 8 union terretories. But, in data there are soem duplicates values, some are in lowecase, uppercase or abbreviated form.
```python
pd.unique(amz['ship-state']))
```
output:
array(['MAHARASHTRA', 'KARNATAKA', 'PUDUCHERRY', 'TAMIL NADU',
       'UTTAR PRADESH', 'ANDHRA PRADESH', 'RAJASTHAN', 'DELHI', 'HARYANA',
       'TELANGANA', 'ASSAM', 'JHARKHAND', 'CHHATTISGARH', 'ODISHA',
       'KERALA', 'MADHYA PRADESH', 'WEST BENGAL', 'NAGALAND', 'Gujarat',
       'UTTARAKHAND', 'BIHAR', 'JAMMU & KASHMIR', 'PUNJAB',
       'HIMACHAL PRADESH', 'ARUNACHAL PRADESH', 'Goa', 'MEGHALAYA', 'GOA',
       'MANIPUR', 'TRIPURA', 'LADAKH', 'DADRA AND NAGAR', 'CHANDIGARH',
       'SIKKIM', 'Delhi', 'ANDAMAN & NICOBAR', 'Punjab', 'Rajshthan',
       'Manipur', 'rajasthan', 'Odisha', 'NL', 'Bihar', 'MIZORAM',
       'punjab', 'New Delhi', 'Rajasthan', 'Punjab/Mohali/Zirakpur',
       'Puducherry', 'delhi', 'RJ', 'Chandigarh', 'orissa', 'LAKSHADWEEP',
       'goa', 'PB', 'APO', 'Arunachal Pradesh', 'AR', 'Pondicherry',
       'Sikkim', 'Arunachal pradesh', 'Nagaland', 'bihar', 'Mizoram',
       'rajsthan', 'Orissa', 'Rajsthan', 'Meghalaya'], dtype=object)

To solve this problem,
```python
region = {'ANDAMAN & NICOBAR': 'ANDAMAN & NICOBAR ISLANDS',
 'ANDHRA PRADESH': 'ANDHRA PRADESH',
 'APO': 'ANDHRA PRADESH',
 'AR': 'ARUNACHAL PRADESH',
 'ARUNACHAL PRADESH': 'ARUNACHAL PRADESH',
 'ASSAM': 'ASSAM',
 'Arunachal Pradesh': 'ARUNACHAL PRADESH',
 'Arunachal pradesh': 'ARUNACHAL PRADESH',
 'BIHAR': 'BIHAR',
 'Bihar': 'BIHAR',
 'CHANDIGARH': 'CHANDIGARH',
 'CHHATTISGARH': 'CHHATTISGARH',
 'Chandigarh': 'CHANDIGARH',
 'DELHI': 'DELHI',
 'Delhi': 'DELHI',
 'DADRA AND NAGAR': 'DADRA AND NAGAR HAVELI AND DAMAN AND DIU',
 'GOA': 'GOA',
 'Gujarat': 'GUJARAT',
 'Goa': 'GOA',
 'HARYANA': 'HARYANA',
 'HIMACHAL PRADESH': 'HIMACHAL PRADESH',
 'JAMMU & KASHMIR': 'JAMMU & KASHMIR',
 'JHARKHAND': 'JHARKHAND',
 'KARNATAKA': 'KARNATAKA',
 'KERALA': 'KERALA',
 'LAKSHADWEEP': 'LAKSHADWEEP',
 'MADHYA PRADESH': 'MADHYA PRADESH',
 'MEGHALAYA': 'MEGHALAYA',
 'MIZORAM': 'MIZORAM',
 'Manipur': 'MANIPUR',
 'Meghalaya': 'MEGHALAYA',
 'Mizoram': 'MIZORAM',
 'NAGALAND': 'NAGALAND',
 'NL': 'NAGALAND',
 'Nagaland': 'NAGALAND',
 'New Delhi': 'DELHI',
 'ODISHA': 'ODISHA',
 'Odisha': 'ODISHA',
 'Orissa': 'ODISHA',
 'PB': 'PUNJAB',
 'PUDUCHERRY': 'PUDUCHERRY',
 'PUNJAB': 'PUNJAB',
 'Pondicherry': 'PUDUCHERRY',
 'Puducherry': 'PUDUCHERRY',
 'Punjab': 'PUNJAB',
 'Punjab/Mohali/Zirakpur': 'PUNJAB',
 'RAJASTHAN': 'RAJASTHAN',
 'RJ': 'RAJASTHAN',
 'Rajasthan': 'RAJASTHAN',
 'Rajshthan': 'RAJASTHAN',
 'Rajsthan': 'RAJASTHAN',
 'SIKKIM': 'SIKKIM',
 'Sikkim': 'SIKKIM',
 'TAMIL NADU': 'TAMIL NADU',
 'TELANGANA': 'TELANGANA',
 'TRIPURA': 'TRIPURA',
 'UTTAR PRADESH': 'UTTAR PRADESH',
 'UTTARAKHAND': 'UTTARAKHAND',
 'WEST BENGAL': 'WEST BENGAL',
 'bihar': 'BIHAR',
 'delhi': 'DELHI',
 'goa': 'GOA',
 'meghalaya': 'MEGHALAYA',
 'mizoram': 'MIZORAM',
 'nagaland': 'NAGALAND',
 'orissa': 'ODISHA',
 'punjab': 'PUNJAB',
 'rajasthan': 'RAJASTHAN',
 'rajsthan': 'RAJASTHAN',
 'sikkim': 'SIKKIM'}

amz['ship-state'] = amz['ship-state'].replace(region)
amz['ship-state'].sort_values().unique()
```
output:
array(['ANDAMAN & NICOBAR ISLANDS', 'ANDHRA PRADESH', 'ARUNACHAL PRADESH',
       'ASSAM', 'BIHAR', 'CHANDIGARH', 'CHHATTISGARH',
       'DADRA AND NAGAR HAVELI AND DAMAN AND DIU', 'DELHI', 'GOA',
       'GUJARAT', 'HARYANA', 'HIMACHAL PRADESH', 'JAMMU & KASHMIR',
       'JHARKHAND', 'KARNATAKA', 'KERALA', 'LADAKH', 'LAKSHADWEEP',
       'MADHYA PRADESH', 'MAHARASHTRA', 'MANIPUR', 'MEGHALAYA', 'MIZORAM',
       'NAGALAND', 'ODISHA', 'PUDUCHERRY', 'PUNJAB', 'RAJASTHAN',
       'SIKKIM', 'TAMIL NADU', 'TELANGANA', 'TRIPURA', 'UTTAR PRADESH',
       'UTTARAKHAND', 'WEST BENGAL'], dtype=object)

Now, sinse Amount column has countinuous data, plotting boxplot:
```python
sns.boxplot(amz['Amount'])
plt.show()
```
![](boxplot.PNG)

running descriptive analysis:
```python
amz[['Amount']].describe()
```
output:
	    |Amount
count	|120833.000000
mean	|648.710886
std	  |281.273230
min	  |0.000000
25%	  |449.000000
50%	  |605.000000
75%	  |788.000000
max	  |5584.000000

Since, Amount column has outliers, it makes sense to replace null values with median value.
```python
amz.fillna(amz['Amount'].median(),inplace = True)
```
Now, our data is free from duplicates and Null values.

Changing the name of Qty column to Quantity.
```python
amz.rename(columns = {'Qty':'Quantity'}, inplace = True)
```
Changing datatype of 'Date' column:
```python
amz['Date'] = pd.to_datetime(amz['Date'], format='mixed', dayfirst=True, errors='coerce')
```
Changing datatype of 'ship-postal-code' column:
```python
amz['ship-postal-code'] = amz['ship-postal-code'].astype('int')
```
Since, Data given is of India location. Hence ship-country column has all entries as 'India'. It makes sense to remove that column.
Since, Data given is of India location. Hence courancy column has all entries as 'INR'. It makes sense to remove that column.
Since, In data there is Order ID which is unique for each order. Hence there is no need of index column. It makes sense to remove that column.
```python
amz.drop(['index', 'currency', 'ship-country'], axis=1, inplace=True)
```

Again checking information about dataset:
```python
amz.info()
```
output:
 #   Column              Non-Null Count   Dtype         
---  ------              --------------   -----         
 0   Order ID            128609 non-null  object        
 1   Date                128609 non-null  datetime64[ns]
 2   Status              128609 non-null  object        
 3   Fulfilment          128609 non-null  object        
 4   Sales Channel       128609 non-null  object        
 5   ship-service-level  128609 non-null  object        
 6   Category            128609 non-null  object        
 7   Size                128609 non-null  object        
 8   Courier Status      128609 non-null  object        
 9   Quantity            128609 non-null  int64         
 10  Amount              128609 non-null  float64       
 11  ship-city           128609 non-null  object        
 12  ship-state          128609 non-null  object        
 13  ship-postal-code    128609 non-null  int32         
 14  B2B                 128609 non-null  bool          
 15  fulfilled-by        128609 non-null  object

```python
amz.shape
```
(128609, 16)
Now, data is cleaned and ready for Exploratory Data Analysis.

# Exploratory Data Analysis (EDA):

### Distribution of Order Percentages by Months
```python
order_counts = amz.groupby(amz['Date'].dt.to_period('M')).size()
order_percentages = (order_counts / len(amz)) * 100
order_summary = pd.DataFrame({'Order Count': order_counts, 'Percentage': order_percentages})
order_summary
```
```python
st = sns.countplot(x=amz['Date'].dt.to_period('M'), order=amz['Date'].dt.to_period('M').sort_values().unique())
plt.xticks(rotation=90)
plt.grid(False)
plt.gca().get_yaxis().set_visible(False)

for p in st.patches:
    height = p.get_height()
    category = p.get_x() + p.get_width() / 2
    percentage = (height / len(amz)) * 100
    st.text(p.get_x() + p.get_width() / 2, height + 0.2, f'{percentage:.1f}%', 
            ha='center', va='bottom', fontsize=10)

plt.title('Percentage of orders in each month')
plt.show()
```
![](EDA1.PNG)

Insightes:
Top 3 months with highest orders are:
1) April
2) May
3) June

### Distribution of Order Percentages by Status
```python
order_counts = amz.groupby('Status').size()
order_percentages = (order_counts / len(amz)) * 100
order_summary = pd.DataFrame({'Order Count': order_counts, 'Percentage': order_percentages})
order_summary
```
```python
st = sns.countplot(x='Status', data=amz, order=amz['Status'].value_counts().index)
plt.xticks(rotation=90)
plt.yscale('log')
plt.grid(False)
plt.gca().get_yaxis().set_visible(False)

for p in st.patches:
    height = p.get_height()
    category = p.get_x() + p.get_width() / 2
    percentage = (height / len(amz)) * 100
    st.text(p.get_x() + p.get_width() / 2, height + 0.2, f'{percentage:.3f}%', 
            ha='center', va='bottom', fontsize=10, rotation=45)

plt.title('Percentage of status of orders')
plt.show()
```
![](EDA2.PNG)

Insights:
14.21% of the total orders are canceled, highlighting a negative aspect that could be improved.

### Distribution of Order Percentages by Fulfilment
```python
order_counts = amz.groupby('Fulfilment').size()
order_percentages = (order_counts / len(amz)) * 100
order_summary = pd.DataFrame({'Order Count': order_counts, 'Percentage': order_percentages})
order_summary
```
```python
plt.pie(amz['Fulfilment'].value_counts(), labels=amz['Fulfilment'].value_counts().index, autopct='%1.1f%%', startangle=90, colors=plt.cm.Paired.colors)
plt.title('Percentage of Orders Based on Fulfilment')
plt.show()
```
![](EDA3.PNG)

Insights:
Approximately 70% of orders are fulfilled by Amazon, while the remaining 30% are handled by the merchant.

### Distribution of Order Percentages by Sales Channel
```python
order_counts = amz.groupby('Sales Channel').size()
order_percentages = (order_counts / len(amz)) * 100
order_summary = pd.DataFrame({'Order Count': order_counts, 'Percentage': order_percentages})
order_summary
```
```python
plt.pie(amz['Sales Channel'].value_counts(), labels=amz['Sales Channel'].value_counts().index, 
        autopct='%1.1f%%', startangle=90, colors=plt.cm.Paired.colors)
plt.title('Percentage of Orders Based on Sales Channel')
plt.show()
```
![](EDA4.PNG)

Insights:
99.9% of order sales are through Amazon.

### Distribution of Order Percentages by Ship Service Level
```python
order_counts = amz.groupby('ship-service-level').size()
order_percentages = (order_counts / len(amz)) * 100
order_summary = pd.DataFrame({'Order Count': order_counts, 'Percentage': order_percentages})
order_summary
```
```python
plt.pie(amz['ship-service-level'].value_counts(), labels=amz['ship-service-level'].value_counts().index, 
        autopct='%1.1f%%', startangle=90, colors=plt.cm.Paired.colors)
plt.title('Percentage of Orders Based on ship-service-level')
plt.show()
```
![](EDA5.PNG)

Insights:
Expedited shipping accounts for 68.7% of orders, indicating a strong preference among customers for faster delivery over standard shipping options.

### Distribution of Order Percentages by Category
```python
order_counts = amz.groupby('Category').size()
order_percentages = (order_counts / len(amz)) * 100
order_summary = pd.DataFrame({'Order Count': order_counts, 'Percentage': order_percentages})
order_summary
```
```python
plt.figure(figsize=(8,5))
st = sns.countplot(y='Category', data=amz, order=amz['Category'].value_counts().index)
plt.xscale('log')
plt.grid(False)
plt.gca().get_xaxis().set_visible(False)

for p in st.patches:
    width = p.get_width()
    category = p.get_y() + p.get_height() / 2
    percentage = (width / len(amz)) * 100
    st.text(width + 0.2, category, f'{percentage:.1f}%', 
            ha='left', va='center', fontsize=10)

plt.title('Percentage of Orders Based on Category')
plt.show()
```
![](EDA6.PNG)

Insights:
The top 3 selling categories are:
1) T-shirt
2) Shirt
3) Blazzer

### Distribution of Order Percentages by Size
```python
order_counts = amz.groupby('Size').size()
order_percentages = (order_counts / len(amz)) * 100
order_summary = pd.DataFrame({'Order Count': order_counts, 'Percentage': order_percentages})
order_summary
```
```python
st = sns.countplot(x='Size', data=amz, order=['XS','S','M','L','XL','XXL','3XL','4XL','5XL','3XL','Free'])
plt.xticks(rotation=90)
plt.yscale('log')
plt.grid(False)
plt.gca().get_yaxis().set_visible(False)

for p in st.patches:
    height = p.get_height()
    category = p.get_x() + p.get_width() / 2
    percentage = (height / len(amz)) * 100
    st.text(p.get_x() + p.get_width() / 2, height + 0.2, f'{percentage:.1f}%', 
            ha='center', va='bottom', fontsize=10)

plt.title('Percentage of Orders base on Size')
plt.show()
```
![](EDA7.PNG)

Insights:
Size M is the best-selling, while size 4XL is the least popular.

### Distribution of Order Percentages by Courier Status
```python
order_counts = amz.groupby('Courier Status').size()
order_percentages = (order_counts / len(amz)) * 100
order_summary = pd.DataFrame({'Order Count': order_counts, 'Percentage': order_percentages})
order_summary
```
```python
plt.pie(amz['Courier Status'].value_counts(), labels=amz['Courier Status'].value_counts().index, 
        autopct='%1.1f%%', startangle=90, colors=plt.cm.Paired.colors, wedgeprops={'width': 0.3, 'edgecolor': 'white'},
       pctdistance=0.9,labeldistance=1.05 )
plt.title('Percentage of Orders Based on Courier Status')
plt.show()
```
![](EDA8.PNG)

Insights:
4.6% of orders are canceled, an issue that needs to be addressed. Additionally, 5.2% of orders remain unshipped, presenting an area for improvement.

### Distribution of Order Percentages by Quantity
```python
order_counts = amz.groupby('Quantity').size()
order_percentages = (order_counts / len(amz)) * 100
order_summary = pd.DataFrame({'Order Count': order_counts, 'Percentage': order_percentages})
order_summary
```
```python
plt.figure(figsize = (8,5))
st = sns.countplot(x='Quantity', data=amz)
plt.xticks(rotation=90)
plt.yscale('log')
plt.grid(False)
plt.gca().get_yaxis().set_visible(False)

for p in st.patches:
    height = p.get_height()
    category = p.get_x() + p.get_width() / 2
    percentage = (height / len(amz)) * 100
    st.text(p.get_x() + p.get_width() / 2, height + 0.2, f'{percentage:.3f}%', 
            ha='center', va='bottom', fontsize=10, rotation=45)

plt.title('Percentage of Orders based on Quantity')
plt.show()
```
![](EDA9.PNG)

Insights:
89.77% of customers place orders with a quantity of 1, indicating a strong preference for smaller purchases, which presents an opportunity to encourage larger orders.

### Distribution of Order Percentages by Amount
```python
amz['Amount'].min(),amz['Amount'].max()
```
(0.0, 5584.0)

```python
order_counts = pd.cut(amz['Amount'], bins=np.arange(0, 6001, 500), right=False).value_counts()
order_percentages = (order_counts / len(amz)) * 100
order_summary = pd.DataFrame({'Order Count': order_counts,'Percentage': order_percentages})
order_summary
```
```python
plt.figure(figsize = (8,5))
st = sns.histplot(amz['Amount'], bins=np.arange(0, 6001, 500))
for p in st.patches:
    height = p.get_height()
    category = p.get_x() + p.get_width() / 2
    percentage = (height / len(amz)) * 100
    st.text(p.get_x() + p.get_width() / 2, height + 0.2, f'{percentage:.3f}%', 
            ha='center', va='top', fontsize=8, rotation=45)
plt.yscale('log')
plt.xlim(0, 6000)
plt.gca().get_yaxis().set_visible(False)
plt.title('Percentage distribution of orders based on Amount')
plt.show()
```
![](EDA10.PNG)

Insights:
Most of the order volume falls within the INR 500-600 range, with order volume decreasing as the order amount exceeds INR 1000.

### Distribution of Order Percentages by States
```python
order_counts = amz['ship-state'].value_counts().nlargest(5)
order_percentages = (order_counts / len(amz)) * 100
top_5_summary = pd.DataFrame({'Order Count': order_counts,'Percentage': order_percentages})
top_5_summary
```
```python
order_counts = amz['ship-state'].value_counts().nsmallest(5)
order_percentages = (order_counts / len(amz)) * 100
bottom_5_summary = pd.DataFrame({'Order Count': order_counts,'Percentage': order_percentages})
bottom_5_summary
```
```python
plt.figure(figsize=(10, 8))

top_5_states = amz['ship-state'].value_counts().nlargest(5).index
plt.subplot(1, 2, 1)
st_top = sns.countplot(x='ship-state', data=amz[amz['ship-state'].isin(top_5_states)], order=top_5_states, color='skyblue')
plt.xticks(rotation=90)
plt.grid(False)
plt.gca().get_yaxis().set_visible(False)
plt.title('Top 5 Ship States by Order Count')

for p in st_top.patches:
    height = p.get_height()
    percentage = (height / len(amz)) * 100
    st_top.text(p.get_x() + p.get_width() / 2, height + 0.2, f'{percentage:.3f}%', ha='center', va='bottom', fontsize=10)

bottom_5_states = amz['ship-state'].value_counts().nsmallest(5).index
plt.subplot(1, 2, 2)
st_bottom = sns.countplot(x='ship-state', data=amz[amz['ship-state'].isin(bottom_5_states)], order=bottom_5_states, color='lightcoral')
plt.xticks(rotation=90)
plt.grid(False)
plt.gca().get_yaxis().set_visible(False)
plt.title('Bottom 5 Ship States by Order Count')

for q in st_bottom.patches:
    height = q.get_height()
    percentage = (height / len(amz)) * 100
    st_bottom.text(q.get_x() + q.get_width() / 2, height/2, f'{percentage:.6f}%', ha='center', va='center', fontsize=10)

plt.tight_layout()
plt.show()
```
![](EDA11.PNG)

Insights:
The highest order volume comes from Maharashtra, followed by Karnataka and Tamil Nadu. States with the lowest order volumes include LAKSHADWEEP, LADAKH and DADRA AND NAGAR HAVELI AND DAMAN AND DIU.

### Distribution of Order Percentages by Cities
```python
order_counts = amz['ship-city'].value_counts().nlargest(5)
order_percentages = (order_counts / len(amz)) * 100
top_5_summary = pd.DataFrame({'Order Count': order_counts,'Percentage': order_percentages})
top_5_summary
```
```python
order_counts = amz['ship-city'].value_counts().nsmallest(5)
order_percentages = (order_counts / len(amz)) * 100
bottom_5_summary = pd.DataFrame({'Order Count': order_counts,'Percentage': order_percentages})
bottom_5_summary
```
```python
plt.figure(figsize=(10, 5))

top_5_states = amz['ship-city'].value_counts().nlargest(5).index
plt.subplot(1, 2, 1)
st_top = sns.countplot(x='ship-city', data=amz[amz['ship-city'].isin(top_5_states)], order=top_5_states, color='skyblue')
plt.xticks(rotation=90)
plt.grid(False)
plt.gca().get_yaxis().set_visible(False)
plt.title('Top 5 Ship cities by Order Count')

for p in st_top.patches:
    height = p.get_height()
    percentage = (height / len(amz)) * 100
    st_top.text(p.get_x() + p.get_width() / 2, height + 0.2, f'{percentage:.3f}%', ha='center', va='bottom', fontsize=10)

bottom_5_states = amz['ship-city'].value_counts().nsmallest(5).index
plt.subplot(1, 2, 2)
st_bottom = sns.countplot(x='ship-city', data=amz[amz['ship-city'].isin(bottom_5_states)], order=bottom_5_states, color='lightcoral')
plt.xticks(rotation=90)
plt.grid(False)
plt.gca().get_yaxis().set_visible(False)
plt.title('Bottom 5 Ship cities by Order Count')

for q in st_bottom.patches:
    height = q.get_height()
    percentage = (height / len(amz)) * 100
    st_bottom.text(q.get_x() + q.get_width() / 2, height/2, f'{percentage:.6f}%', ha='center', va='center', fontsize=10)

plt.tight_layout()
plt.show()
```
![](EDA12.PNG)

Insights:
The highest order volume comes from Bengaluru, followed by Hyderabad and Mumbai. Cities with the lowest order volumes include Kasumpti SHIMLA, NALLAPADU, and Edathala.

### Distribution of Order Percentages by Postal Code
```python
order_counts = amz['ship-postal-code'].value_counts().nlargest(5)
order_percentages = (order_counts / len(amz)) * 100
top_5_summary = pd.DataFrame({'Order Count': order_counts,'Percentage': order_percentages})
top_5_summary
```
```python
order_counts = amz['ship-postal-code'].value_counts().nsmallest(5)
order_percentages = (order_counts / len(amz)) * 100
bottom_5_summary = pd.DataFrame({'Order Count': order_counts,'Percentage': order_percentages})
bottom_5_summary
```
```python
plt.figure(figsize=(10, 5))

top_5_states = amz['ship-postal-code'].value_counts().nlargest(5).index
plt.subplot(1, 2, 1)
st_top = sns.countplot(x='ship-postal-code', data=amz[amz['ship-postal-code'].isin(top_5_states)], order=top_5_states, color='skyblue')
plt.xticks(rotation=90)
plt.grid(False)
plt.gca().get_yaxis().set_visible(False)
plt.title('Top 5 ship-postal-code by Order Count')

for p in st_top.patches:
    height = p.get_height()
    percentage = (height / len(amz)) * 100
    st_top.text(p.get_x() + p.get_width() / 2, height + 0.2, f'{percentage:.3f}%', ha='center', va='bottom', fontsize=10)

bottom_5_states = amz['ship-postal-code'].value_counts().nsmallest(5).index
plt.subplot(1, 2, 2)
st_bottom = sns.countplot(x='ship-postal-code', data=amz[amz['ship-postal-code'].isin(bottom_5_states)], order=bottom_5_states, color='lightcoral')
plt.xticks(rotation=90)
plt.grid(False)
plt.gca().get_yaxis().set_visible(False)
plt.title('Bottom 5 ship-postal-code by Order Count')

for q in st_bottom.patches:
    height = q.get_height()
    percentage = (height / len(amz)) * 100
    st_bottom.text(q.get_x() + q.get_width() / 2, height/2, f'{percentage:.3f}%', ha='center', va='center', fontsize=10)

plt.tight_layout()
plt.show()
```
![](EDA13.PNG)

Insights:
The highest order volume comes from 201301, followed by 122001 and 560037. Cities with the lowest order volumes include 388160, 414609, and 799120.

### Distribution of Order Percentages by B2B
```python
order_counts = amz.groupby('B2B').size()
order_percentages = (order_counts / len(amz)) * 100
order_summary = pd.DataFrame({'Order Count': order_counts, 'Percentage': order_percentages})
order_summary
```
```python
plt.pie(amz['B2B'].value_counts(), labels=amz['B2B'].value_counts().index, autopct='%1.1f%%', startangle=90, colors=plt.cm.Paired.colors)
plt.title('B2B or not?')
plt.show()
```
![](EDA14.PNG)

Insights:
With only 0.7% of orders being B2B, there is significant potential to expand Amazon's B2B offerings.

### Distribution of Order Percentages by Fulfilled by
```python
order_counts = amz.groupby('fulfilled-by').size()
order_percentages = (order_counts / len(amz)) * 100
order_summary = pd.DataFrame({'Order Count': order_counts, 'Percentage': order_percentages})
order_summary
```
```python
plt.pie(amz['fulfilled-by'].value_counts(), labels=amz['fulfilled-by'].value_counts().index, autopct='%1.1f%%', startangle=90, colors=plt.cm.Paired.colors)
plt.title('fulfilled-by:')
plt.show()
```
![](EDA15.PNG)

Insights:
30.4% of orders are fulfilled by Easy Ship, indicating significant potential for Amazon to expand its fulfillment capabilities.

## Conclusion:
- Conducted data cleaning and preprocessing to resolve duplicates, missing values, and inconsistent data types, ensuring a reliable dataset for analysis.
- Performed exploratory data analysis (EDA) to uncover insights on top-selling categories, regional trends, and customer preferences, enabling informed decision-making.
- Identified inefficiencies, including a 14.21% cancellation rate and 5.2% unshipped orders, and recommended strategies to enhance Courier Services and reduce cancellations.
