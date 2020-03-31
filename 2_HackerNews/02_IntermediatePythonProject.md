# Intermediate Python Project

Project is for the Dataquest intermediate python course.

## Objective

The objective is to examine two types of post and examine if either type receive more comments on average and do posts during certain times reeive more comments on average? The two types of posts are: Ask HN and Show HN. Ask HN posts are posts asking the Hacker News community a question while Show HN is when a community member posts something they want other community members to provide feedback on, such as a project, product, or some other thing of interest.

## Dataset

Dataset is a random sample of Hacker News posts. Below is a description of the columns in the dataset:

* id: The unique identifier from Hacker News for the post
* title: The title of the post
* url: The URL that the posts links to, if it the post has a URL
* num_points: The number of points the post acquired, calculated as the total * * number of upvotes minus the total number of downvotes
* num_comments: The number of comments that were made on the post
* author: The username of the person who submitted the post
* created_at: The date and time at which the post was submitted

### Import Libraries


```python
from csv import reader
import datetime as dt
```

### Import Data


```python
app_file = open("C:/Personal/BIDAMIA_GoogleDrive/Business Intelligence/DataQuest/2_IntermediatPython/Project/HN_posts.csv", encoding = "utf8")
read_file = reader(app_file)
hn = list(read_file)
app_file.close()
```

#### Print First 5 Rows


```python
for row in hn[:5]:
    print(row)
    print("\n")
```

    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']
    
    
    ['12579008', 'You have two days to comment if you want stem cells to be classified as your own', 'http://www.regulations.gov/document?D=FDA-2015-D-3719-0018', '1', '0', 'altstar', '9/26/2016 3:26']
    
    
    ['12579005', 'SQLAR  the SQLite Archiver', 'https://www.sqlite.org/sqlar/doc/trunk/README.md', '1', '0', 'blacksqr', '9/26/2016 3:24']
    
    
    ['12578997', 'What if we just printed a flatscreen television on the side of our boxes?', 'https://medium.com/vanmoof/our-secrets-out-f21c1f03fdc8#.ietxmez43', '1', '0', 'pavel_lishin', '9/26/2016 3:19']
    
    
    ['12578989', 'algorithmic music', 'http://cacm.acm.org/magazines/2011/7/109891-algorithmic-composition/fulltext', '1', '0', 'poindontcare', '9/26/2016 3:16']
    
    
    


```python
# Extract the headers
headers = hn[0]

# Remove headers from hn dataset
hn = hn[1:]

# Verify removal of header
for row in hn[:5]:
    print(row)
    print("\n")
```

    ['12579008', 'You have two days to comment if you want stem cells to be classified as your own', 'http://www.regulations.gov/document?D=FDA-2015-D-3719-0018', '1', '0', 'altstar', '9/26/2016 3:26']
    
    
    ['12579005', 'SQLAR  the SQLite Archiver', 'https://www.sqlite.org/sqlar/doc/trunk/README.md', '1', '0', 'blacksqr', '9/26/2016 3:24']
    
    
    ['12578997', 'What if we just printed a flatscreen television on the side of our boxes?', 'https://medium.com/vanmoof/our-secrets-out-f21c1f03fdc8#.ietxmez43', '1', '0', 'pavel_lishin', '9/26/2016 3:19']
    
    
    ['12578989', 'algorithmic music', 'http://cacm.acm.org/magazines/2011/7/109891-algorithmic-composition/fulltext', '1', '0', 'poindontcare', '9/26/2016 3:16']
    
    
    ['12578979', 'How the Data Vault Enables the Next-Gen Data Warehouse and Data Lake', 'https://www.talend.com/blog/2016/05/12/talend-and-Â\x93the-data-vaultÂ\x94', '1', '0', 'markgainor1', '9/26/2016 3:14']
    
    
    

#### Create Separate Lists for Ask and Show HN


```python
ask_posts = []
show_posts = []
other_posts = []

for row in hn:
    title = row[1]
    
    if title.lower().startswith('ask hn'):
        ask_posts.append(row)
    elif title.lower().startswith('show hn'):
        show_posts.append(row)
    else:
        other_posts.append(row)
```


```python
print(len(ask_posts))
print(len(show_posts))
print(len(other_posts))
print(len(hn))
```

    9139
    10158
    273822
    293119
    

#### Number of Comments for Ask Posts and Avg Number of Comments


```python
total_ask_comments = 0

for row in ask_posts:
    num_comments = int(row[4])
    
    total_ask_comments = total_ask_comments + num_comments

    
avg_ask_comments = total_ask_comments / len(ask_posts)

print(total_ask_comments)
print(round(avg_ask_comments,1))
```

    94986
    10.4
    

#### Number of Comments for Show Posts and Avg Number of Comments


```python
total_show_comments = 0

for row in show_posts:
    num_comments = int(row[4])
    total_show_comments = total_show_comments + num_comments
    
avg_show_comments = total_show_comments / len(show_posts)

print(total_show_comments)
print(round(avg_show_comments,1))
```

    49633
    4.9
    

On average, ask posts get close to double the amount of posts that show posts do and on average, 2 times the amount of comments

### Does Time of Day Matter?

Knowing that ask posts get more comments on average, I will move onto the next analysis using just ask posts. The next analysis is whether the time of day attracts more comments.

#### Amount of Ask Posts per Hour


```python
result_list = []

for row in ask_posts:
    created_at = row[6]
    num_comments = int(row[4])
    
    result_list.append([created_at, num_comments])

# View results
print(result_list[:3])
```

    [['9/26/2016 2:53', 7], ['9/26/2016 1:17', 3], ['9/25/2016 22:57', 0]]
    


```python
counts_by_hour = {}
comments_by_hour = {}

format_string = "%m/%d/%Y %H:%M"

for row in result_list:
    date_string = row[0]
    num_comments = row[1]
    datetimeObj = dt.datetime.strptime(date_string,format_string)
    hour = datetimeObj.strftime("%H")
    
    if hour not in counts_by_hour:
        counts_by_hour[hour] = 1
        comments_by_hour[hour] = num_comments
    else:
        counts_by_hour[hour] += 1
        comments_by_hour[hour] += num_comments
    
# Results
print('counts by hour: ',counts_by_hour)
print("\n")
print("comments by hour: ",comments_by_hour)
```

    counts by hour:  {'02': 269, '01': 282, '22': 383, '21': 518, '19': 552, '17': 587, '15': 646, '14': 513, '13': 444, '11': 312, '10': 282, '09': 222, '07': 226, '03': 271, '23': 343, '20': 510, '16': 579, '08': 257, '00': 301, '18': 614, '12': 342, '04': 243, '06': 234, '05': 209}
    
    
    comments by hour:  {'02': 2996, '01': 2089, '22': 3372, '21': 4500, '19': 3954, '17': 5547, '15': 18525, '14': 4972, '13': 7245, '11': 2797, '10': 3013, '09': 1477, '07': 1585, '03': 2154, '23': 2297, '20': 4462, '16': 4466, '08': 2362, '00': 2277, '18': 4877, '12': 4234, '04': 2360, '06': 1587, '05': 1838}
    


```python
# Calculate the Average Number of Comments per Posts for Posts Created
     # During Each Hour of the Day

avg_by_hour = []

for hour in comments_by_hour:
    avg_by_hour.append([hour,comments_by_hour[hour]/counts_by_hour[hour]])
    
print(avg_by_hour)
```

    [['02', 11.137546468401487], ['01', 7.407801418439717], ['22', 8.804177545691905], ['21', 8.687258687258687], ['19', 7.163043478260869], ['17', 9.449744463373083], ['15', 28.676470588235293], ['14', 9.692007797270955], ['13', 16.31756756756757], ['11', 8.96474358974359], ['10', 10.684397163120567], ['09', 6.653153153153153], ['07', 7.013274336283186], ['03', 7.948339483394834], ['23', 6.696793002915452], ['20', 8.749019607843136], ['16', 7.713298791018998], ['08', 9.190661478599221], ['00', 7.5647840531561465], ['18', 7.94299674267101], ['12', 12.380116959064328], ['04', 9.7119341563786], ['06', 6.782051282051282], ['05', 8.794258373205741]]
    


```python
# Sort the list so it is easier to read
swap_avg_by_hour = []

for hour in avg_by_hour:
    swap_avg_by_hour.append([hour[1],hour[0]])
    
print(swap_avg_by_hour)
```

    [[11.137546468401487, '02'], [7.407801418439717, '01'], [8.804177545691905, '22'], [8.687258687258687, '21'], [7.163043478260869, '19'], [9.449744463373083, '17'], [28.676470588235293, '15'], [9.692007797270955, '14'], [16.31756756756757, '13'], [8.96474358974359, '11'], [10.684397163120567, '10'], [6.653153153153153, '09'], [7.013274336283186, '07'], [7.948339483394834, '03'], [6.696793002915452, '23'], [8.749019607843136, '20'], [7.713298791018998, '16'], [9.190661478599221, '08'], [7.5647840531561465, '00'], [7.94299674267101, '18'], [12.380116959064328, '12'], [9.7119341563786, '04'], [6.782051282051282, '06'], [8.794258373205741, '05']]
    


```python
sorted_swap = sorted(swap_avg_by_hour,reverse = True)
```


```python
# Print top 5
print("Top 5 Hours for Ask Posts Comments")
print("\n")
for avg, hour in sorted_swap[:5]:
    hour_dt = hour
    hour_ob = dt.datetime.strptime(hour_dt, "%H")
    hour_sf = hour_ob.strftime("%H:%M")
    avg_comments = "{}: {:.2f} average comments per post."
    print(avg_comments.format(hour_sf, avg))
    
```

    Top 5 Hours for Ask Posts Comments
    
    
    15:00: 28.68 average comments per post.
    13:00: 16.32 average comments per post.
    12:00: 12.38 average comments per post.
    02:00: 11.14 average comments per post.
    10:00: 10.68 average comments per post.
    
