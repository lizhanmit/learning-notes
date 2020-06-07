# Python 3 Note

- [Python 3 Note](#python-3-note)
  - [Install Python3.6 on Linux](#install-python36-on-linux)
  - [Sequences / Collections](#sequences--collections)
    - [String](#string)
      - [String Formatting](#string-formatting)
    - [Lists](#lists)
      - [List Comprehensions](#list-comprehensions)
    - [Tuples](#tuples)
    - [Sets](#sets)
    - [Dictionaries](#dictionaries)
    - [range()](#range)
  - [Input Validation](#input-validation)
    - [isinstance(value, data_type)](#isinstancevalue-data_type)
    - [is](#is)
  - [Control Flow](#control-flow)
    - [Loop](#loop)
      - [while ... else / for ... else](#while--else--for--else)
    - [pass](#pass)
  - [Functions](#functions)
    - [Lambda or Anonymous Functions](#lambda-or-anonymous-functions)
    - [Uncertain Function Arguments](#uncertain-function-arguments)
    - [Partial Functions](#partial-functions)
  - [File I/O](#file-io)
    - [Open](#open)
    - [Write](#write)
    - [Read](#read)
    - [Close](#close)
  - [OOP](#oop)
    - [Private Attributes](#private-attributes)
    - [Class Variables](#class-variables)
    - [Class Methods](#class-methods)
    - [Static Methods](#static-methods)
    - [Constructors](#constructors)
    - [Inheritance](#inheritance)
    - [Magic Methods](#magic-methods)
      - [`__repr(self)__`](#reprself)
      - [`__str(self)__`](#strself)
    - [Decorators](#decorators)
      - [`@property`](#property)
  - [Miscellaneous](#miscellaneous)
    - [Iterators](#iterators)
    - [Generator](#generator)
    - [Modules and Packages](#modules-and-packages)
    - [Exception Handling](#exception-handling)
    - [Regex](#regex)
    - [JSON](#json)
    - [Serialization](#serialization)
    - [Closures](#closures)
    - [Decorators](#decorators-1)

---

## Install Python3.6 on Linux 

Steps: 

1. In terminal, install the pre-requisites. 
```shell   
sudo apt-get update

sudo apt-get install build-essential libpq-dev libssl-dev openssl libffi-dev zlib1g-dev

sudo apt-get install python3-pip python3-dev
```

2. Use PPA (Personal Package Archives) to install Python3.6. 

```shell
sudo add-apt-repository ppa:jonathonf/python-3.6

sudo apt-get update

sudo apt-get install python3.6
```

3. Verify Python3 version. `python3.6 -V`
4. Make Python3.6 as the default when type `python3`. 

```shell
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.<old_version>

sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6.<new_version>

sudo update-alternatives --config python3
```

5. Verify Python3 version. `python3 -V`
6. Install virtual environment. `sudo apt-get install python3.6-venv`
7. Verify virtual environment.

```shell
mkdir test_py_venv

cd test_py_venv

python3 -m venv my_venv

source my_venv/bin/activate 

# Now you should be in your virtual environment.

# exit the virtual environment
deactivate
```
8. Upgrade pip3. `pip3 install --upgrade pip`

---

## Sequences / Collections

### String

```python
str = 'programiz'

# slicing 2nd to 5th character
print('str[1:5] = ', str[1:5]) # Output: rogr

# slicing 6th to 2nd last character
print('str[5:-2] = ', str[5:-2]) # Output: am

# get the last 5 characters of a string
my_string[-5:]

# reverse a string 
my_string[::-1]

# check if a string starts with a specific substring
if my_string.startswith(sub_string):

# check if a string ends with a specific substring
if my_string.endswith(sub_string):
```

#### String Formatting

- Any object which is not a string can be formatted using the %s operator.

```python
# two or more argument specifiers
name = "John"
age = 23
print("%s is %d years old." % (name, age))
# or 
print("{} is {} years old.".format(name, age))
```

### Lists

- Can contain any type of variable. 
- mutable

```python
my_list = []

my_list = [1,2,3]
my_list.append(4)  # [1,2,3,4]
my_list.extend([5,6])  # [1,2,3,4.5,6]

my_list[-1]  # 6

print(my_list)  # output: [1,2,3,4.5,6]

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

# zip()
x = [1, 1, 1]
y = [2, 2, 2]
list(zip(x, y))  # [(1, 2), (1, 2), (1, 2)]
[x + y for x, y in zip(x, y)]  # [3, 3, 3]

# transfer a list containing only characters to a string 
''.join(['a', 'b', 'c'])  # 'abc'
```

#### List Comprehensions

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

### Tuples

Basically, list is mutable whereas tuple is immutable.

```python
language = ("French", "German", "English", "Polish")

print(language[1]) #Output: German
print(language[3]) #Output: Polish
print(language[-1]) # Output: Polish

# delete the tuple
del language
```

### Sets

- mutable
- Cannot replace one item of a set with another as they are unordered and indexing have no meaning.

```python
my_set = {1, 2, 3}

# set of mixed datatypes
my_another_set = {1.0, "Hello", (1, 2, 3)}


# If dictionaries are passed to the update() method, the keys of the dictionaries are added to the set.
my_set.update([3, 4, 5])
print(my_set) # Output: {1, 2, 3, 4, 5}
```

![sets-functions.png](img/sets-functions.png)

```python
# transfer a list to a set 
a = set(["Jake", "John", "Eric"])
b = set(["John", "Jill"])

# get the intersection between two sets
# for the above example, the result should be {'John'}
a.intersection(b)
# or 
b.intersection(a)
# or
a & b
# or 
b & a

# get elements in either a or b but not both
a.symmetric_difference(b)
# or 
b.symmetric_difference(a)
# or
a ^ b
# or
b ^ a

# difference 
# get elements only in a but not in b 
a.difference(b)
# or 
a - b
# get elements only in b but not in a 
b.difference(a)
# 
b - a

# union 
a.union(b)
# or
b.union(a)
# or
a | b
# 
b | a
```

### Dictionaries

```python
# empty dictionary
my_dict = {}

# dictionary with integer keys
my_dict = {1: 'apple', 2: 'ball'}

# dictionary with mixed keys
my_dict = {'name': 'John', 1: [2, 4, 3]}

# get the value of an element 
dict_name["element_key"]

# iterate over dictionaries
for key in my_dict:
    print("Key of %s is %s" %(key, my_dict[key]))
# or 
for key, value in my_dict.items():
    print("Key of %s is %s" % (key, value))

# add a new key-value pair
my_dict['salary'] = 4342.4

# remove an element 
del my_dict["name"]
# or 
my_dict.pop("name")

# delete the dictionary
del my_dict
```

### range()

```python
range(6) # range(0, 6)

numbers = range(1, 6)
print(list(numbers)) # Output: [1, 2, 3, 4, 5]

numbers2 = range(1, 6, 2)
print(list(numbers2)) # Output: [1, 3, 5]

numbers3 = range(5, 0, -1)
print(list(numbers3)) # Output: [5, 4, 3, 2, 1]
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

## Control Flow

### Loop

Most programming languages use `{}` to specify the block of code. Python uses indentation. The amount of indentation is up to you, but it must be consistent throughout that block.

```python
num = -1

if num > 0:
    print("Positive number")
elif num == 0:
    print("Zero")
else:
    print("Negative number")
    
# Output: Negative number
```

#### while ... else / for ... else 

The "else" clause is a part of the loop. When the loop condition of "for" or "while" statement fails then code part in "else" is executed. If **break** statement is executed inside for loop then the "else" part is skipped. 

### pass

Use the `pass` statement to construct a body that does nothing, which you want to implement it in the future instead of now.

```python
sequence = {'p', 'a', 's', 's'}
for val in sequence:
    pass
```

---

## Functions

### Lambda or Anonymous Functions

```python
square = lambda x: x ** 2
print(square(5))

# Output: 25
```

Lambda functions are used along with built-in functions like `filter()`, `map()` etc.

```python
numbers = (1, 2, 3, 4)
result = map(lambda x: x*x, numbers)
# converting map object to set
numbersSquare = set(result)
print(numbersSquare)  # {16, 1, 4, 9}

# Passing Multiple Iterators to map() Using Lambda
num1 = [4, 5, 6]
num2 = [5, 6, 7]

result = map(lambda n1, n2: n1+n2, num1, num2)
print(list(result))  # [9, 11, 13]
```

### Uncertain Function Arguments

If the number of arguments is uncertain, use `*args_name` as the argument of the function. Then you can pass various parameters when you call the function. 

```python
def foo(first, second, third, *the_rest):
    print("First: %s" %(first))
    print("Second: %s" %(second))
    print("Third: %s" %(third))
    print("And all the rest... %s" %(list(the_rest)))

# call the function
foo(1,2,3,4,5)

# Output:
#First: 1
#Second: 2
#Third: 3
#And all the rest... [4, 5]
```

You can define a special arguments using `**args_name` which allows you to specify parameters with keyword when you call the function. 

```python
def bar(first, second, third, **options):
    if options["action"] == "sum":
        print("The sum is: %d" %(first + second + third))

    if options["number"] == "first":
        return first

result = bar(1, 2, 3, action = "sum", number = "first")
# Output: The sum is: 6
```

### Partial Functions

Derive a function with x parameters to a function with fewer parameters and fixed values set for the more limited function.

```python
from functools import partial

def multiply(x, y):
    return x * y

# create a new function that multiplies by 2
# the 2 will replace x
# y will equal 4 when p_func(4) is called
# result will be 8
p_func = partial(multiply, 2)
print(p_func(4))
```

---

## File I/O

A file operation takes place in the following order.

1. Open a file.
2. Read or write (perform operation).
3. Close the file.

### Open

You can specify the mode while opening a file. The default is reading and text mode.

```python
f = open("test.txt")    # open file in current directory
f = open("C:/Python33/README.txt")  # specifying full path

f = open("test.txt", 'w')  # Open a file for writing in text mode. Creates a new file if it does not exist or truncates the file if it exists.

f = open("img.bmp", 'r+b') # read and write in binary mode
```

### Write

In order to write into a file in Python, we need to open it in write `'w'`, append `'a'` or exclusive creation `'x'` mode.

```python
with open("test.txt", 'w', encoding = 'utf-8') as f:
   f.write("my first file\n")
   f.write("This file\n\n")
   f.write("contains three lines\n")
```

Use `with` statement to open a file. This ensures that the file is closed when the block inside with is exited.

### Read

```python
f = open("test.txt", 'r', encoding = 'utf-8')
f.read(4)    # read the first 4 characters
```

### Close

```python
try:
   f = open("test.txt", encoding = 'utf-8')
   # perform file operations
finally:
   f.close()
```

**The best way** to close a file is by using the `with` statement. (see the above)

---

## OOP

As soon as you define a class, a new class object is created with the same name. This class object allows us to access the different attributes as well as to instantiate new objects of that class.

```python
class MyClass:
  "This is my class"
  a = 10
  def func(self):
    print('Hello')

# Output: 10
print(MyClass.a)

# Output: <function myclass.func at 0x7f04d158b8b0>
print(MyClass.func)

# Output: 'This is my class'
print(MyClass.__doc__)

# create an object
obj1 = MyClass()
print(obj1.a)        # Output: 10
```

When calling the function, `obj.func()` translates into `MyClass.func(obj)`.

### Private Attributes 

When defining a private attribute in a class, the convention is add `__` in front of the attribute name. `__<priavte_attr_name>`.

### Class Variables 

All instances of a class share the same class variable `<class_name>.<variable_name>`. 

### Class Methods 

Class methods can be used to create alternative constructors. 

**When to use**: When you want to access the class rather than the instance, define this method as a class method.

When defining a class method, you need to add `@classmethod` decorator at the top of the method, and the first argument of the method is `cls`.

They should be invoked by using class name `<class_name>.<class_method_name>()`.

### Static Methods 

**When to use**: If you do not need to access the instance or class in a method, define this method as a static method. 

When defining a static method, you need to add `@staticmethod` decorator at the top of the method, and do not need to pass `self` or `cls` as argument.

They should be invoked by using class name `<class_name>.<static_method_name>()`.

### Constructors

```python
class ComplexNumber:
    def __init__(self, r = 0, i = 0):  # constructor
        self.real = r
        self.imag = i

    def getData(self):
        print("{0}+{1}j".format(self.real,self.imag))


c1 = ComplexNumber(2,3) # Create a new ComplexNumber object
c1.getData() # Output: 2+3j

c2 = ComplexNumber() # Create a new ComplexNumber object
c2.getData() # Output: 0+0j
```

### Inheritance

```python
class ElectricCar(Car):
    def __init__(self, number_of_wheels, seating_capacity, maximum_velocity):
        Car.__init__(self, number_of_wheels, seating_capacity, maximum_velocity)
        
    #  or (better)
    def __init__(self, number_of_wheels, seating_capacity, maximum_velocity):
        super().__init__(number_of_wheels, seating_capacity, maximum_velocity)
```

### Magic Methods 

#### `__repr(self)__`

Like `toString()` method in Java. 

**Best practice**: override this method with statement that is used to create this object. 

#### `__str(self)__`

Similar to `__repr(self)__`. But it is meant to be more readable for end users. 

When using `print(<object>)`, if both `__repr(self)__` and `__str(self)__` are overridden, `str(self)` will be invoked. 

Generally, we only override `__repr(self)__`.

### Decorators

#### `@property` 

Use `@property` at the top of a method to make it can be used as a property / attribute of the class when being invoked.  

```python
class Employee:

    # class variable
    raise_amount = 1.04

    # constructor
    def __init__(self, first_name, last_name, pay):
        self.first_name = first_name
        self.last_name = last_name
        self.pay = pay
        self.email = self.first_name.lower() + '.' + self.last_name.lower() + '@company.com'

    # regular method
    def full_name(self):
        return '{} {}'.format(self.first_name, self.last_name)

    # class method
    @classmethod
    def from_string(cls, emp_str):
        first_name, last_name, pay = emp_str.split('-')
        return cls(first_name, last_name, pay)

    # static method
    @staticmethod
    def is_workday(day):
        if day.weekday() == 5 or day.weekday() == 6:
            return False
        return True

    def __repr__(self):
        return 'Employee({}, {}, {})'.format(self.first_name, self.last_name, self.pay)

    def __str__(self):
        return '{} - {}'.format(self.full_name(), self.email)

class Developer(Employee):

    raise_amount = 1.10

    def __init__(self, first_name, last_name, pay, prog_lang):
        super().__init__(first_name, last_name, pay)
        self.prog_lang = prog_lang


class Manager(Employee):
    def __init__(self, first_name, last_name, pay, employees=None):
        super().__init__(first_name, last_name, pay)
        if employees is None:
            self.employees = []
        else:
            self.employees = employees

    def add_emp(self, emp):
        if emp not in self.employees:
            self.employees.append(emp)

    def remove_emp(self, emp):
        if emp in self.employees:
            self.employees.remove(emp)

    def print_emps(self):
        for emp in self.employees:
            print('--> ', emp.full_name())



# main function
if __name__ == '__main__':
    emp_1 = Employee('San', 'Zhang', 10000)
    print(emp_1.email)
    print(emp_1.full_name())
    print(emp_1.raise_amount)  # 1.04
    print(Employee.raise_amount)  # 1.04
    emp_1.raise_amount = 1.05  # this line only modifies "raise_amount" of the instance emp_1, it will not influence the value of the class variable
    print(emp_1.raise_amount)  # 1.05
    print(Employee.raise_amount)  # 1.04, not be modified

    print('------')
    emp_str_1 = 'Si-Li-20000'
    new_emp_1 = Employee.from_string(emp_str_1)  # class method should be invoked by using class name
    print(new_emp_1.email)

    print('------')
    import datetime
    my_date1 = datetime.date(2018, 10, 28)
    print(Employee.is_workday(my_date1))  # static method should be invoked by using class name
    my_date2 = datetime.date(2018, 10, 29)
    print(Employee.is_workday(my_date2))

    print('------')
    dev_1 = Developer('Wu', 'Wang', 30000, 'Python')
    print(dev_1.prog_lang)
    dev_2 = Developer('Liu', 'Zhao', 40000, 'Java')

    print('------')
    mgr_1 = Manager('Qi', 'Zhou', 100000, [dev_1])
    print(mgr_1.email)
    print(mgr_1.raise_amount)
    mgr_1.print_emps()
    mgr_1.add_emp(dev_2)
    mgr_1.print_emps()

    print(isinstance(mgr_1, Employee))
    print(issubclass(Manager, Employee))

    print('------ magic methods ------')
    print(repr(emp_1))
    print(str(emp_1))
    print(emp_1)  # will invoke __str()__ method here if both __repr()__ and __str()__ are overridden
```

---

## Miscellaneous 

### Iterators

Technically speaking, Python iterator object must implement two special methods, `__iter__()` and `__next__()`, collectively called the iterator protocol.

The `iter()` function (which in turn calls the `__iter__()` method) returns an iterator from them.

```python
my_list = [4, 7, 0, 3]

# get an iterator using iter()
my_iter = iter(my_list)

print(next(my_iter)) # Output: 4
print(next(my_iter)) # Output: 7
```

### Generator 

Python generators are a simple way of creating iterators.

Use `yield` statement to put items into a generator. Then you can iterate the generator to access each item. 

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

### Modules and Packages

Modules refer to a file containing Python statements and definitions. For example, example.py. Its module name would be `example`.

In example.py: 

```python
def add(a, b):
   return a + b
```

Then use this module:

```python
# importing example module
import example 

# accessing the function inside the module using . operator
example.add(4, 5.5) 
```

You can import specific names from a module without importing the module as a whole. For instance,

```python
from math import pi
print("The value of pi is", pi)
```

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

### Exception Handling

```python
try: 
    pass
except error_name:
    pass
```

### Regex

- `^` - start of the string.
- `.` - any non-newline character.
- `*` - repeat 0 or more times. `ab*` will match 'a', - 'ab', or 'a' followed by any number of 'b's.
- `?` - repeat 0 or 1 time. `ab?` will match either 'a' or 'ab'.

```python
import re
```

### JSON

```python
import json 

# transfer a json string to an object data structure 
json.loads(json_string) 

# transfer an object data structure to a json string 
json_string = json.dumps([1, 2, 3, "a", "b", "c"])
```

### Serialization

```python
import pickle

# deserialize
pickle.loads(pickled_string)

# serialize
pickled_string = pickle.dumps([1, 2, 3, "a", "b", "c"])
```

### Closures 

A Closure is a function object that remembers values in enclosing scopes even if they are not present in memory.

Closures can avoid use of global variables and provides some form of data hiding.

The criteria that must be met to create closure: 

- You must have a nested function (function inside a function).
- The nested function must refer to a value defined in the enclosing function.
- The enclosing function must return the nested function.


```python
def print_msg(msg): # outer enclosing function
    def printer():  # inner function
        print(msg)
    return printer 

another = print_msg("Hello") 
another() # Output: Hello


def multiplier_of(n):
    def multiplier(number):
        return number*n
    return multiplier

multiplywith5 = multiplier_of(5)
print(multiplywith5(9))
```

### Decorators

- Decorators allow you to make simple modifications to callable objects like functions, methods, or classes.
- By using decorators, you can reuse a function and do some modifications based on it instead of modifying the original function. Or you can apply a function to other functions, such as do logging, input validation and output formatting. 
- Decorator is a function itself, which can be applied to another function B. Function B is passed into the decorator as a param. 
- This is also called metaprogramming as a part of the program tries to modify another part of the program at compile time.

```python
# define the decorator 
def type_check(correct_type):
    def check(old_function):
        def new_function(arg):
            if (isinstance(arg, correct_type)):
                return old_function(arg)
            else:
                print("Bad Type")
        return new_function
    return check

# use the decorator
@type_check(int)
def times2(num):
    return num*2

print(times2(2))
times2('Not A Number')

# use the decorator
@type_check(str)
def first_letter(word):
    return word[0]

print(first_letter('Hello World'))
first_letter(['Not', 'A', 'String'])
```
