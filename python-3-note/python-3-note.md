# Python 3 Note

## String Formatting

- Any object which is not a string can be formatted using the %s operator.

```python
# two or more argument specifiers
name = "John"
age = 23
print("%s is %d years old." % (name, age))
```

---

## String 

```python
# get the last 5 characters of a string
my_string[-5:]

# reverse a string 
my_string[::-1]

# check if a string starts with a specific substring
if my_string.startswith(sub_string):

# check if a string ends with a specific substring
if my_string.endswith(sub_string):
```

---

## List 

- Can contain any type of variable. 

```python
my_list = []

my_list = [1,2,3]
my_list.append(1)

print(my_list)  # output: [1,2,3]

# iterate the list
for x in my_list:
    print(x)
    
# join two lists 
list_3 = list_1 + list_2 

# repeat a list 3 times
my_list * 3

# get length of the list 
len(my_list)

# count the number of a specific element in a list 
my_list.count(my_element)

# check if a specific element is in a list
if my_element in my_list:
    
# sort the list 
sorted(name_of_list)
```

---

## Dictionary

```python
# iterate over dictionaries
for name, number in phonebook.items():
    print("Phone number of %s is %d" % (name, number))

# remove an element 
del phonebook["John"]
# the same as 
phonebook.pop("John")
```

---

## Input Validation 

### isinstance(value, data_type)

```python
if isinstance(myfloat, float) and myfloat == 10.0:
    print("Float: %f" % myfloat)
if isinstance(myint, int) and myint == 20:
    print("Integer: %d" % myint)
```
### is

The "is" operator checks if two variables are the same instance. 

```python
x = [1,2,3]
y = [1,2,3]
z = x
print(x == y) # Prints out True
print(x is y) # Prints out False
print(x is z) # Prints out True
```

---

## Loop

### while ... else / for ... else 

The "else" clause is a part of the loop. When the loop condition of "for" or "while" statement fails then code part in "else" is executed. If **break** statement is executed inside for loop then the "else" part is skipped. 

---

## Modules and Packages

```python
# extending module load path
PYTHONPATH=/foo python game.py
# the same as 
sys.path.append("/foo")

# look for which functions are implemented in each module by using the "dir" function, it will return a list of function names
dir(name_of_module)

#  read about the function more using the "help" function
help(name_of_module.name_of_function)
```

---

## NumPy

- ndarray
- Given two lists, use NumPy to do element-wise calculations. 
- Do not need to iterate the list. You can operate all elements based on array.

```python
# import module
import numpy as np

# transfer a list to a numpy array 
array_name = np.array(list_name)

# subsetting 
# get elements that are greater than 10 
subset_array_name = array_name[array_name > 10]
```

---

## Pandas 

- DataFrame 
- Store and manipulate tabular data in rows of observations and columns of variables.

```python
# import module 
import pandas as pd

country_dict = {"country": ["Brazil", "Russia", "India", "China", "South Africa"],
       "capital": ["Brasilia", "Moscow", "New Dehli", "Beijing", "Pretoria"],
       "area": [8.516, 17.10, 3.286, 9.597, 1.221],
       "population": [200.4, 143.5, 1252, 1357, 52.98] }
# transfer a dictionary to a data frame
country_data_frame = pd.DataFrame(country_dict)

# change default numerial index to customized index 
country_data_frame.index = ["BR", "RU", "IN", "CH", "SA"]


# transfer a .csv file to a data frame 
data_frame_name = pd.read_csv('file_name.csv')
```

