
# Answering Business Questions with SQL

This project is from the intermediate SQL course in the DataQuest data engineering certificate.

### Import Modules 


```python
import sqlite3
import pandas as pd

db = 'chinook.db'
```

### Define some Helper Functions


```python
# This function will run a sql query and return as a pandas dataframe
def run_query(q):
    with sqlite3.connect(db) as conn:
        return pd.read_sql(q, conn)
    
# Function that takes a sql command and executes using sqlite module. 
    # Note, won't return tables
def run_command(q):
    with sqlite3.connect(db) as conn:
        conn.isolation_level = None
        conn.execute(c)

# Function that calls run_query and returns a list of all tables and views in the database
def show_tables():
    q = '''
    SELECT
        name,
        type
    FROM sqlite_master
    WHERE type IN ("table","view");
    '''
    return run_query(q)

```


```python
# Show the tables
show_tables()
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
      <th>name</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>album</td>
      <td>table</td>
    </tr>
    <tr>
      <th>1</th>
      <td>artist</td>
      <td>table</td>
    </tr>
    <tr>
      <th>2</th>
      <td>customer</td>
      <td>table</td>
    </tr>
    <tr>
      <th>3</th>
      <td>employee</td>
      <td>table</td>
    </tr>
    <tr>
      <th>4</th>
      <td>genre</td>
      <td>table</td>
    </tr>
    <tr>
      <th>5</th>
      <td>invoice</td>
      <td>table</td>
    </tr>
    <tr>
      <th>6</th>
      <td>invoice_line</td>
      <td>table</td>
    </tr>
    <tr>
      <th>7</th>
      <td>media_type</td>
      <td>table</td>
    </tr>
    <tr>
      <th>8</th>
      <td>playlist</td>
      <td>table</td>
    </tr>
    <tr>
      <th>9</th>
      <td>playlist_track</td>
      <td>table</td>
    </tr>
    <tr>
      <th>10</th>
      <td>track</td>
      <td>table</td>
    </tr>
  </tbody>
</table>
</div>



### Finding Top 3 Music Genres in the USA

The first question we want to answer with the data is to find the top 3 selling music genres in the USA.

Write a query that returns each genre, with the number of tracks sold in the USA:
1. in absolute numbers
2. in percentages.


```python
top3 = '''
    WITH usa_tracks_sold AS
       (
        SELECT il.* FROM invoice_line il
        INNER JOIN invoice i on il.invoice_id = i.invoice_id
        INNER JOIN customer c on i.customer_id = c.customer_id
        WHERE c.country = "USA"
       )

    SELECT
        g.name genre,
        count(uts.invoice_line_id) tracks_sold,
        cast(count(uts.invoice_line_id) AS FLOAT) / (
            SELECT COUNT(*) from usa_tracks_sold
        ) percentage_sold
    FROM usa_tracks_sold uts
    INNER JOIN track t on t.track_id = uts.track_id
    INNER JOIN genre g on g.genre_id = t.genre_id
    GROUP BY 1
    ORDER BY 2 DESC
    LIMIT 3;
        '''
run_query(top3)
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
      <th>genre</th>
      <th>tracks_sold</th>
      <th>percentage_sold</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Rock</td>
      <td>561</td>
      <td>0.533777</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alternative &amp; Punk</td>
      <td>130</td>
      <td>0.123692</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Metal</td>
      <td>124</td>
      <td>0.117983</td>
    </tr>
  </tbody>
</table>
</div>



Therefore, the top three genres in the US are Rock, Alterinative & Punk and Metal. These genres should be the most stocked in the store.

### Top Sales by Employee

Next we want to figure out who the top sales people are.

Write a query that finds the total dollar amount of sales assigned to each sales support agent within the company.


```python
sales_query = '''
            SELECT 
                e.first_name || ' ' || e.last_name employee,
                e.title,
                e.hire_date,
                e.city,
                e.country,
                sum(i.total) total_sales
            FROM employee e
            INNER JOIN customer c ON e.employee_id = c.support_rep_id
            INNER JOIN invoice i ON c.customer_id = i.customer_id
            GROUP BY employee_id
            ORDER BY total_sales DESC
            '''
run_query(sales_query)
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
      <th>employee</th>
      <th>title</th>
      <th>hire_date</th>
      <th>city</th>
      <th>country</th>
      <th>total_sales</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Jane Peacock</td>
      <td>Sales Support Agent</td>
      <td>2017-04-01 00:00:00</td>
      <td>Calgary</td>
      <td>Canada</td>
      <td>1731.51</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Margaret Park</td>
      <td>Sales Support Agent</td>
      <td>2017-05-03 00:00:00</td>
      <td>Calgary</td>
      <td>Canada</td>
      <td>1584.00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Steve Johnson</td>
      <td>Sales Support Agent</td>
      <td>2017-10-17 00:00:00</td>
      <td>Calgary</td>
      <td>Canada</td>
      <td>1393.92</td>
    </tr>
  </tbody>
</table>
</div>



The top three sales people are all relatively similar. The person with the least sales is also the newest employee. Sales increase the longer the employee has been employeed by the company.

### Sales Data by Customer and Country

Next task is to analyze the purchases by customer and country. For each country, include:

1. total number of customers
2. total value of sales
3. average value of sales per customer
4. average order value



```python

sales_by_country = '''
WITH country_or_other AS
    (
     SELECT
       CASE
           WHEN (
                 SELECT count(*)
                 FROM customer
                 where country = c.country
                ) = 1 THEN "Other"
           ELSE c.country
       END AS country,
       c.customer_id,
       il.*
     FROM invoice_line il
     INNER JOIN invoice i ON i.invoice_id = il.invoice_id
     INNER JOIN customer c ON c.customer_id = i.customer_id
    )

SELECT
    country,
    customers,
    total_sales,
    average_order,
    customer_lifetime_value
FROM
    (
    SELECT
        country,
        count(distinct customer_id) customers,
        SUM(unit_price) total_sales,
        SUM(unit_price) / count(distinct customer_id) customer_lifetime_value,
        SUM(unit_price) / count(distinct invoice_id) average_order,
        CASE
            WHEN country = "Other" THEN 1
            ELSE 0
        END AS sort
    FROM country_or_other
    GROUP BY country
    ORDER BY sort ASC, total_sales DESC
    );
'''

run_query(sales_by_country)
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
      <th>country</th>
      <th>customers</th>
      <th>total_sales</th>
      <th>average_order</th>
      <th>customer_lifetime_value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>USA</td>
      <td>13</td>
      <td>1040.49</td>
      <td>7.942672</td>
      <td>80.037692</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Canada</td>
      <td>8</td>
      <td>535.59</td>
      <td>7.047237</td>
      <td>66.948750</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Brazil</td>
      <td>5</td>
      <td>427.68</td>
      <td>7.011148</td>
      <td>85.536000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>France</td>
      <td>5</td>
      <td>389.07</td>
      <td>7.781400</td>
      <td>77.814000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Germany</td>
      <td>4</td>
      <td>334.62</td>
      <td>8.161463</td>
      <td>83.655000</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Czech Republic</td>
      <td>2</td>
      <td>273.24</td>
      <td>9.108000</td>
      <td>136.620000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>United Kingdom</td>
      <td>3</td>
      <td>245.52</td>
      <td>8.768571</td>
      <td>81.840000</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Portugal</td>
      <td>2</td>
      <td>185.13</td>
      <td>6.383793</td>
      <td>92.565000</td>
    </tr>
    <tr>
      <th>8</th>
      <td>India</td>
      <td>2</td>
      <td>183.15</td>
      <td>8.721429</td>
      <td>91.575000</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Other</td>
      <td>15</td>
      <td>1094.94</td>
      <td>7.448571</td>
      <td>72.996000</td>
    </tr>
  </tbody>
</table>
</div>



The USA is the top selling country and has the most customers. I find that Canada's second place result is interesting. Canada has 1/10th the population of the USA, but it's sales are a little bit more than half of the USA's. It would be worthwile to study why Canada performs proportionalty better than the USA and then apply the strategy to the USA because both countries are pretty similar.

### Analyzing Full Album Sales

Finally, we want to compare if customers are buying full albums or just a few songs from each album (i.e. just the popular songs).


```python
albums_vs_tracks = '''
WITH invoice_first_track AS
    (
     SELECT
         il.invoice_id invoice_id,
         MIN(il.track_id) first_track_id
     FROM invoice_line il
     GROUP BY 1
    )

SELECT
    album_purchase,
    COUNT(invoice_id) number_of_invoices,
    CAST(count(invoice_id) AS FLOAT) / (
                                         SELECT COUNT(*) FROM invoice
                                      ) percent
FROM
    (
    SELECT
        ifs.*,
        CASE
            WHEN
                 (
                  SELECT t.track_id FROM track t
                  WHERE t.album_id = (
                                      SELECT t2.album_id FROM track t2
                                      WHERE t2.track_id = ifs.first_track_id
                                     ) 

                  EXCEPT 

                  SELECT il2.track_id FROM invoice_line il2
                  WHERE il2.invoice_id = ifs.invoice_id
                 ) IS NULL
             AND
                 (
                  SELECT il2.track_id FROM invoice_line il2
                  WHERE il2.invoice_id = ifs.invoice_id

                  EXCEPT 

                  SELECT t.track_id FROM track t
                  WHERE t.album_id = (
                                      SELECT t2.album_id FROM track t2
                                      WHERE t2.track_id = ifs.first_track_id
                                     ) 
                 ) IS NULL
             THEN "yes"
             ELSE "no"
         END AS "album_purchase"
     FROM invoice_first_track ifs
    )
GROUP BY album_purchase;
'''

run_query(albums_vs_tracks)
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
      <th>album_purchase</th>
      <th>number_of_invoices</th>
      <th>percent</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>no</td>
      <td>500</td>
      <td>0.814332</td>
    </tr>
    <tr>
      <th>1</th>
      <td>yes</td>
      <td>114</td>
      <td>0.185668</td>
    </tr>
  </tbody>
</table>
</div>



It looks like the most customers prefer to buy individual tracks from albums instead of the full album! We may be able to optimize costs by just purchasing the popular songs instead of full albums.
