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

## List Comprehensions

- Create a new list based on another list, in a single, readable line.

```python
# calculate the length of each word in the words list
sentence = "the quick brown fox jumps over the lazy dog"
words = sentence.split()
word_lengths = []
for word in words:
      if word != "the":
          word_lengths.append(len(word))
        
# use list comprehensions 
word_lengths = [len(word) for word in words if word != 'the']
```

---

## Set

![sets-functions.png](img/sets-functions.png)

```python
# transfer a list to a set 
a = set(["Jake", "John", "Eric"])
b = set(["John", "Jill"])

# get the intersection between two sets
# for the above example, the result should be {'John'}
a.intersection(b)
# the same as 
b.intersection(a)


# get elements in either a or b but not both
a.symmetric_difference(b)
# the same as 
b.symmetric_difference(a)


# difference 
# get elements only in a but not in b 
a.difference(b)
# get elements only in b but not in a 
b.difference(a)


# union 
a.union(b)
# the same as 
b.union(a)
```

---

## Dictionary

```python
# get the value of an element 
dict_name["element_key"]

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

## Generator 

- Use `yield` statement to put items into a generator. Then you can iterate the generator to access each item. 

```python
import random

# lottery() is a generator function, the type of the return value is generator
def lottery():
    # returns 6 numbers between 1 and 40
    for i in range(6):
        yield random.randint(1, 40)

    # returns a 7th number between 1 and 15
    yield random.randint(1,15)

for random_number in lottery():
       print("And the next number is... %d!" %(random_number))
```

---

## Uncertain Function Arguments

- If the number of arguments is uncertain, use `*args_name` as the argument of the function. Then you can pass various parameters when you call the function. 

```python
def foo(first, second, third, *the_rest):
    print("First: %s" %(first))
    print("Second: %s" %(second))
    print("Third: %s" %(third))
    print("And all the rest... %s" %(list(the_rest)))

# call the function
foo(1,2,3,4,5)
```

- You can define a special arguments using `**args_name` which allows you to specify parameters with keyword when you call the function. 

```python
def bar(first, second, third, **options):
    if options["action"] == "sum":
        print("The sum is: %d" %(first + second + third))

    if options["number"] == "first":
        return first

result = bar(1, 2, 3, action = "sum", number = "first")
```

---

## Regex

`^` - start of the string.

`.` - any non-newline character.

`*` - repeat 0 or more times. `ab*` will match 'a', 'ab', or 'a' followed by any number of 'b's.

`?` - un-greedy, repeat 0 or 1 time. `ab?` will match either 'a' or 'ab'.

```python
import re
```

---

## Exception Handling

```python
try: 
    pass
except error_name:
    pass
```

---

## JSON

```python
import json 

# transfer a json string to an object data structure 
json.loads(json_string) 

# transfer an object data structure to a json string 
json_string = json.dumps([1, 2, 3, "a", "b", "c"])
```

---

## Serialization

```python
import pickle

# deserialize
pickle.loads(pickled_string)

# serialize
pickled_string = pickle.dumps([1, 2, 3, "a", "b", "c"])
```

---

## Partial Functions

- Derive a function with x parameters to a function with fewer parameters and fixed values set for the more limited function.

```python
from functools import partial

def multiply(x,y):
    return x * y

# create a new function that multiplies by 2
# the 2 will replace x
# y will equal 4 when p_func(4) is called
# result will be 8
p_func = partial(multiply,2)
print(p_func(4))
```

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

# get the observation of a specific row based on index
data_frame_name.iloc[index_number]

# get the observation of a specific row based on label
data_frame_name.loc['label_value']
```

