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

