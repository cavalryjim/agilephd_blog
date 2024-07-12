+++
title = 'Python Dictionary'
tags = ["python"]
date = 2018-03-22
+++

A **dictionary** is a collection which is unordered, changeable and indexed.

![Containers](../../dictionary-clipart.png)

### Dictionary structure
A dictionary, or dict, organizes data values by association with other data values rather than by sequential position.  A dictionary is a collection which is unordered, changeable and indexed. In Python dictionaries are written with curly brackets, and they have keys and values.  Dictionaries are initialized using curly braces **{ }**.

```python
>>> capitals = {'United States': 'Washington, DC','France': 'Paris','Italy': 'Rome'}
>>> capitals
{'United States': 'Washington, DC', 'France': 'Paris', 'Italy': 'Rome'}
>>> type(capitals)
<class 'dict'>
>>> capitals['Italy']
'Rome'
>>> capitals['Spain'] = 'Madrid'
>>> capitals
{'United States': 'Washington, DC', 'France': 'Paris', 'Italy': 'Rome',
'Spain': 'Madrid'}
>>> 'Germany' in capitals
False
>>> 'Italy' in capitals
True
>>> morecapitals = {'Germany': 'Berlin','United Kingdom': 'London'}
>>> capitals.update(morecapitals)
>>> capitals
{'United States': 'Washington, DC', 'France': 'Paris', 'Italy': 'Rome',
'Spain': 'Madrid', 'Germany': 'Berlin', 'United Kingdom': 'London'}
>>> len(capitals)
6
```

### Dictionary methods
Python has a set of built-in methods that you can use on dictionaries.

| Dictionary Method |	Results |
|---------|---------|
| dict.copy()	| Returns a copy of the dictionary |
| dict.fromkeys() | Returns a dictionary with the specified keys and values |
| dict.get() | Returns the value of the specified key |
| dict.items() | Returns a tuple for each key value pair |
| dict.keys() | Returns the dictionary's keys |
| dict.pop() | Removes the element with the specified key |
| dict.popitem() | Removes the last key-value pair |
| dict.setdefault() | Returns the value of the specified key. If the key does not exist: insert the key, with the specified value |
| dict.update() | Updates the dictionary with the specified key-value pairs |
| dict.values() | Returns a list of all the values in the dictionary |
|=======

Let's try some of these functions.

```python
>>> capitals.items()
dict_items([('United States', 'Washington, DC'), ('France', 'Paris'),
('Italy', 'Rome'), ('Spain', 'Madrid'), ('Germany', 'Berlin'),
('United Kingdom', 'London')])
>>> capitals.keys()
dict_keys(['United States', 'France', 'Italy', 'Spain', 'Germany',
'United Kingdom'])
>>> capitals.values()
dict_values(['Washington, DC', 'Paris', 'Rome', 'Madrid', 'Berlin', 'London'])
>>>
```

### Loop through a Dictionary
As an iterable object, we can loop, or iterate, through a dictionary using a **for** loop.  With the **for** loop we can execute a set of statements, once for each item in a list, tuple, set etc.

```python
>>> for key in capitals.keys():
...     print(key)
...
United States
France
Italy
Spain
Germany
United Kingdom

>>> for value in capitals.values():
...     print(value)
...
Washington, DC
Paris
Rome
Madrid
Berlin
London

>>> for key, value in capitals.items():
...     print(key, value)
...
United States Washington, DC
France Paris
Italy Rome
Spain Madrid
Germany Berlin
United Kingdom London
```