# Laptop Prices Project

Goal of this project is to answer some common business problems about laptop prices. We will create a class to represent the stores inventory.

Here are some questions we would like to answer:

1. Given a laptop id, find the corresponding data.
2. Given an amount of money, find whether there are two laptops whose total price is that given amount.
3. Identify all laptops whose price is falls withing a given budget.

### The Data


```python
# Load the data
import csv
with open('C:/Personal/BIDAMIA_GoogleDrive/Business Intelligence/DataQuest/4_Alogorithm Complexity/Project/laptops.csv') as file:
    laptop = list(csv.reader(file))
```


```python
# View the first 3 rows
for i in laptop[:3]:
    print(i,'\n')
```

    ['', 'Company', 'Product', 'TypeName', 'Inches', 'ScreenResolution', 'Cpu', 'Ram', 'Memory', 'Gpu', 'OpSys', 'Weight', 'Price_euros'] 
    
    ['1', 'Apple', 'MacBook Pro', 'Ultrabook', '13.3', 'IPS Panel Retina Display 2560x1600', 'Intel Core i5 2.3GHz', '8GB', '128GB SSD', 'Intel Iris Plus Graphics 640', 'macOS', '1.37kg', '1339.69'] 
    
    ['2', 'Apple', 'Macbook Air', 'Ultrabook', '13.3', '1440x900', 'Intel Core i5 1.8GHz', '8GB', '128GB Flash Storage', 'Intel HD Graphics 6000', 'macOS', '1.34kg', '898.94'] 
    
    

Below is a description of the columns in the dataset:

ID: A unique identifier for the laptop.

Company: The name of the company who produces the laptop.

Product: The name of the laptop.

TypeName: The type of laptop.

Inches: The size of the screen in inches.

ScreenResolution: The resolution of the screen.

CPU: The laptop CPU.

RAM: The amount of RAM in the laptop.

Memory: The size of the hard drive.

GPU: The graphics card name.

OpSys: The name of the operating system.

Weight: The laptop weight.

Price: The price of the laptop.


```python
# Save header to own list
header = laptop[0]

# Assign renaming rows to a variable named rows
rows = laptop[1:]
```

### Create the Inventory Class


```python
class Inventory():
    def __init__(self, csv_filename):
        with open(csv_filename) as file:
            reader = csv.reader(file)
            rows = list(reader)
        self.header = rows[0]
        self.rows = rows[1:]
        for i in self.rows:
            i[-1] = float(i[-1])
            
```


```python
# Test the class
inventory = Inventory('C:/Personal/BIDAMIA_GoogleDrive/Business Intelligence/DataQuest/4_Alogorithm Complexity/Project/laptops.csv')
```


```python
print(inventory.header)
```

    ['', 'Company', 'Product', 'TypeName', 'Inches', 'ScreenResolution', 'Cpu', 'Ram', 'Memory', 'Gpu', 'OpSys', 'Weight', 'Price_euros']
    


```python
print(len(inventory.rows))
```

    1303
    

### Create a Method to Look up Laptop from Identifier


```python
class Inventory():
    def __init__(self, csv_filename):
        with open(csv_filename) as file:
            reader = csv.reader(file)
            rows = list(reader)
        self.header = rows[0]
        self.rows = rows[1:]
        for i in self.rows:
            i[-1] = float(i[-1])
            
    def get_laptop_from_id(self, laptop_id):
        for row in self.rows:
            if row[0] == laptop_id:
                return row
        return None
    
```


```python
# Store the data file path
file_path = 'C:/Personal/BIDAMIA_GoogleDrive/Business Intelligence/DataQuest/4_Alogorithm Complexity/Project/laptops.csv'

# Test the updated class
inventory = Inventory(file_path)
print(inventory.get_laptop_from_id('2'))
print(inventory.get_laptop_from_id('3362736'))
```

    ['2', 'Apple', 'Macbook Air', 'Ultrabook', '13.3', '1440x900', 'Intel Core i5 1.8GHz', '8GB', '128GB Flash Storage', 'Intel HD Graphics 6000', 'macOS', '1.34kg', 898.94]
    None
    

The above algorithm had to loop through every row to find the laptop id or decide the id doesn't exist. For only a small dataset it is probably ok but as the success of your business grows, there will be more data. 

Below, we will preprocess the data to increase query performance.


```python
# Update the class for the new process
class Inventory():
    def __init__(self, csv_filename):
        with open(csv_filename) as file:
            reader = csv.reader(file)
            rows = list(reader)
        self.header = rows[0]
        self.rows = rows[1:]
        for i in self.rows:
            i[-1] = float(i[-1])
        self.id_to_row = {}
        for i in self.rows:
            self.id_to_row[i[0]] = i
            
    def get_laptop_from_id(self, laptop_id):
        for row in self.rows:
            if row[0] == laptop_id:
                return row
        return None
    
    def get_laptop_from_id_fast(self, laptop_id):
        if laptop_id in self.id_to_row:
            return self.id_to_row[laptop_id]
        return None
        
    
```


```python
inventory = Inventory(file_path)
print(inventory.get_laptop_from_id_fast('2'))
print('\n')
print(inventory.get_laptop_from_id_fast('3362736'))
```

    ['2', 'Apple', 'Macbook Air', 'Ultrabook', '13.3', '1440x900', 'Intel Core i5 1.8GHz', '8GB', '128GB Flash Storage', 'Intel HD Graphics 6000', 'macOS', '1.34kg', 898.94]
    
    
    None
    

Now we will compare the performance between the two algorithm. To do so, I will import the time and random module and calculate the elapsed times for each algorithm.

In general, for a given function, this is how to calculate the elapsed time: 

start = time.time()
func()
end = time.time()
elapsed = end - start


```python
# import time & random module
import time
import random

# Generate random ids
ids = [str(random.randint(1000000,9999999)) for _ in range(10000)]

inventory = Inventory(file_path)
total_time_no_dict = 0
for id in ids:
    start = time.time()
    inventory.get_laptop_from_id(id)
    end = time.time()
    total_time_no_dict += end - start
    
total_time_dict = 0
for id in ids:
    start = time.time()
    inventory.get_laptop_from_id_fast(id)
    end = time.time()
    total_time_dict += end - start
    
performance_gain = round(total_time_no_dict/total_time_dict,2)
print('Total time without preprocessing: ',round(total_time_no_dict,4))
print('\n')
print('Total time with preprocessing: ', round(total_time_dict,4))
print('\n')
print('Improvement in performance is: ',performance_gain,'times faster')
```

    Total time without preprocessing:  0.4528
    
    
    Total time with preprocessing:  0.003
    
    
    Improvement in performance is:  150.72 times faster
    

Next we want to run a gift card promotion with an x amount of dollars on it. The customer can buy a maximum of 2 laptops with it. We want the customer to be able to spend the entire amount of money on the giftcard so we want to know which combination of two laptops can use the entire amount.


```python
# Update the class for the new method
class Inventory():
    def __init__(self, csv_filename):
        with open(csv_filename) as file:
            reader = csv.reader(file)
            rows = list(reader)
        self.header = rows[0]
        self.rows = rows[1:]
        for i in self.rows:
            i[-1] = float(i[-1])
        self.id_to_row = {}
        for i in self.rows:
            self.id_to_row[i[0]] = i
            
    def get_laptop_from_id(self, laptop_id):
        for row in self.rows:
            if row[0] == laptop_id:
                return row
        return None
    
    def get_laptop_from_id_fast(self, laptop_id):
        if laptop_id in self.id_to_row:
            return self.id_to_row[laptop_id]
        return None
    
    def check_promotion_dollars(self, dollars):
        for i in self.rows:
            if i[-1] == dollars:
                return True
        for i in self.rows:
            for j in self.rows:
                if i[-1] + j[-1] == dollars:
                    return True
        return False
        
```


```python
# Test the new method
inventory = Inventory(file_path)

print(inventory.check_promotion_dollars(1000))
print(inventory.check_promotion_dollars(442))
```

    True
    False
    

### Optimize for Performance

Next we will optimize the check_promotion_dollars method for performance by preprocessing the data.


```python
# Optimize check_promotion_dollars method
class Inventory():
    def __init__(self, csv_filename):
        with open(csv_filename) as file:
            reader = csv.reader(file)
            rows = list(reader)
        self.header = rows[0]
        self.rows = rows[1:]
        for i in self.rows:
            i[-1] = float(i[-1])
        self.id_to_row = {}
        for i in self.rows:
            self.id_to_row[i[0]] = i
        # Create an empty price set to preprocess data
        self.prices = set()
        # Asign all prices to the set
        for row in self.rows:
            self.prices.add(row[-1])
            
    def get_laptop_from_id(self, laptop_id):
        for row in self.rows:
            if row[0] == laptop_id:
                return row
        return None
    
    def get_laptop_from_id_fast(self, laptop_id):
        if laptop_id in self.id_to_row:
            return self.id_to_row[laptop_id]
        return None
    
    def check_promotion_dollars(self, dollars):
        for i in self.rows:
            if i[-1] == dollars:
                return True
        for i in self.rows:
            for j in self.rows:
                if i[-1] + j[-1] == dollars:
                    return True
        return False
    
    # Create an optimized check_promotion_dolars_fast method
    def check_promotion_dollars_fast(self, dollars):
        if dollars in self.prices:
            return True
        for price in self.prices:
            if dollars - price in self.prices:
                return True
        return False
```


```python
# Check the method
inventory = Inventory(file_path)
print(inventory.check_promotion_dollars_fast(1000))
print(inventory.check_promotion_dollars_fast(442))
```

    True
    False
    

Time to compare the performance of the new method


```python
# Create some random numbers to test

prices = [random.randint(100, 5000) for _ in range(100)] 

inventory = Inventory(file_path)                     

total_time_no_dict = 0                                   
for price in prices:                                     
    start = time.time()                                  
    inventory.check_promotion_dollars(price)             
    end = time.time()                                    
    total_time_no_dict += end - start                    
    
total_time_dict = 0                                      
for price in prices:                                     
    start = time.time()                                 
    inventory.check_promotion_dollars_fast(price)        
    end = time.time()                                    
    total_time_dict += end - start                       
        
performance_gain = round(total_time_no_dict/total_time_dict,2)
print('Total time without preprocessing: ',round(total_time_no_dict,4))
print('\n')
print('Total time with preprocessing: ', round(total_time_dict,4))
print('\n')
print('Improvement in performance is: ',performance_gain,'times faster')
```

    Total time without preprocessing:  1.1579
    
    
    Total time with preprocessing:  0.001
    
    
    Improvement in performance is:  1163.85 times faster
    

### Search for all Laptops within a Budget

Next, I will create a method that returns all the laptops that have prices at or below a given budget. If we sort all of the prices, a binary search algorithm can be used.


```python
def row_price(row):
    return row[-1]

class Inventory():
    def __init__(self, csv_filename):
        with open(csv_filename) as file:
            reader = csv.reader(file)
            rows = list(reader)
        self.header = rows[0]
        self.rows = rows[1:]
        for i in self.rows:
            i[-1] = float(i[-1])
        self.id_to_row = {}
        for i in self.rows:
            self.id_to_row[i[0]] = i
        self.prices = set()
        for row in self.rows:
            self.prices.add(row[-1])
        # Sort the data
        self.rows_by_price = sorted(self.rows, key = row_price)
        
    def get_laptop_from_id(self, laptop_id):
        for row in self.rows:
            if row[0] == laptop_id:
                return row
        return None
    
    def get_laptop_from_id_fast(self, laptop_id):
        if laptop_id in self.id_to_row:
            return self.id_to_row[laptop_id]
        return None
    
    def check_promotion_dollars(self, dollars):
        for i in self.rows:
            if i[-1] == dollars:
                return True
        for i in self.rows:
            for j in self.rows:
                if i[-1] + j[-1] == dollars:
                    return True
        return False
    
    def check_promotion_dollars_fast(self, dollars):
        if dollars in self.prices:
            return True
        for price in self.prices:
            if dollars - price in self.prices:
                return True
        return False
    
    # add a binary search algorithm
    def find_laptop_with_price(self, target_price):
        range_start = 0                                   
        range_end = len(self.rows_by_price) - 1                       
        while range_start < range_end:
            range_middle = (range_end + range_start) // 2  
            value = self.rows_by_price[range_middle][-1]
            if value == target_price:                            
                return range_middle                        
            elif value < target_price:                           
                range_start = range_middle + 1             
            else:                                          
                range_end = range_middle - 1 
        if self.rows_by_price[range_start][-1] != target_price:                  
            return -1                                      
        return range_start
    
    
    def find_first_laptop_more_expensive(self, target_price): # Step 2
        range_start = 0                                   
        range_end = len(self.rows_by_price) - 1                   
        while range_start < range_end:
            range_middle = (range_end + range_start) // 2  
            price = self.rows_by_price[range_middle][-1]
            if price > target_price:
                range_end = range_middle
            else:
                range_start = range_middle + 1
        if self.rows_by_price[range_start][-1] <= target_price:                  
            return -1                                   
        return range_start
```


```python
# Test the new method
inventory = Inventory(file_path)

print(inventory.find_first_laptop_more_expensive(1000))
print(inventory.find_first_laptop_more_expensive(10000))
```

    683
    -1
    
