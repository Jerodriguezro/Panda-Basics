# Panda-Basics

import pandas as pd
import numpy as np
import os
import matplotlib.pyplot as plt
%matplotlib inline 

from grader import Grader

DATA_FOLDER = '../readonly/final_project_data/'

transactions    = pd.read_csv(os.path.join(DATA_FOLDER, 'sales_train.csv.gz'))
items           = pd.read_csv(os.path.join(DATA_FOLDER, 'items.csv'))
item_categories = pd.read_csv(os.path.join(DATA_FOLDER, 'item_categories.csv'))
shops           = pd.read_csv(os.path.join(DATA_FOLDER, 'shops.csv'))

grader = Grader()

Let's start with a simple task. 

<ol start="0">
  <li><b>Print the shape of the loaded dataframes and use [`df.head`](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.head.html) function to print several rows. Examine the features you are given.</b></li>
</ol>

import pandas as pd
import numpy as np
import os
import matplotlib.pyplot as plt
%matplotlib inline 

from grader import Grader


transactions    = pd.read_csv(os.path.join(DATA_FOLDER, 'sales_train.csv.gz'))
items           = pd.read_csv(os.path.join(DATA_FOLDER, 'items.csv'))
item_categories = pd.read_csv(os.path.join(DATA_FOLDER, 'item_categories.csv'))
shops           = pd.read_csv(os.path.join(DATA_FOLDER, 'shops.csv'))

grader = Grader()
# YOUR CODE GOES HERE
transactions['date']= pd.to_datetime(transactions['date'], format='%d.%m.%Y')
print(transactions.head(150))

# YOUR CODE GOES HERE
transactions['date']= pd.to_datetime(transactions['date'], format='%d.%m.%Y')
transactions['revenue'] = transactions['item_price'] * transactions['item_cnt_day']
transactions_result = transactions.loc[(transactions['date'].dt.year == 2014) & (transactions['date'].dt.month == 9 )]

max_revenue = transactions_result.groupby('shop_id')['revenue'].sum().max()
grader.submit_tag('max_revenue', max_revenue)

# YOUR CODE GOES HERE
transactions['date']= pd.to_datetime(transactions['date'], format='%d.%m.%Y')
transactions['revenue'] = transactions['item_price'] * transactions['item_cnt_day']
transactions_r = transactions.loc[(transactions['date'].dt.year == 2014) & (transactions['date'].dt.month >= 6 ) & (transactions['date'].dt.month <= 8 )]

transaction_join = pd.merge(transactions_r, items, how='left', on='item_id')
transaction_join = transaction_join.groupby('item_category_id')['revenue'].sum().reset_index(name ='total_revenue')

item_id = transaction_join['item_category_id'].iloc[transaction_join['total_revenue'].argmax()]


category_id_with_max_revenue = item_id
grader.submit_tag('category_id_with_max_revenue', category_id_with_max_revenue)

# YOUR CODE GOES HERE
a = transactions.groupby('item_id')['item_price'].nunique()
num_items_constant_price = ((a == 1).sum())
grader.submit_tag('num_items_constant_price', num_items_constant_price)

shop_id = 25
transactions_r = transactions.loc[(transactions['date'].dt.year == 2014) & (transactions['date'].dt.month == 12 )  & (transactions['shop_id'] == 25)]
transactions_r = transactions_r.groupby('date')['item_cnt_day'].sum().reset_index(name ='total_count')


total_num_items_sold = transactions_r['total_count']
days = transactions_r['date']

# Plot it
plt.plot(days, total_num_items_sold)
plt.ylabel('Num items')
plt.xlabel('Day')
plt.title("Daily revenue for shop_id = 25")
plt.show()

total_num_items_sold_var = np.var(total_num_items_sold, ddof=1)
grader.submit_tag('total_num_items_sold_var', total_num_items_sold_var)
